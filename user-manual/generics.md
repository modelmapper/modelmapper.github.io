---
layout: content
menu: user-manual
title: Generics
---

# Generics

ModelMapper uses [TypeTokens](http://code.google.com/p/guava-libraries/wiki/ReflectionExplained) to allow mapping of generic parameterized types. 

## Example

Let's say you want to map a List of Integers to a List of Strings:

{:.prettyprint .lang-java}
    List<Integer> numbers = buildIntegers();
    List<String> characters = new ArrayList<String>();
    
    modelMapper.map(numbers, characters);

Unfortunately, `characters` is populated with **`Integer`** instances, not `String` instances as expected. The reason is ModelMapper doesn't know that `characters` is supposed to contain `String` instances since that type information is erased at runtime. So it creates another List of Integers. The workaround? Use TypeTokens:

{:.prettyprint .lang-java}
    Type listType = new TypeToken<List<String>>() {}.getType();
    List<String> characters = modelMapper.map(numbers, listType);

Here we create an anonymous subclass of the `TypeToken` class, passing `List<String>` as the type parameter. This allows the resulting `listType` to contain the captured type information that is otherwise erased at runtime so that ModelMapper knows we mean to map `numbers` to a List of Strings.
