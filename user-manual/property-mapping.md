---
layout: content
menu: user-manual
title: Property Mapping
---

{::options parse_block_html="true" /}

# Property Mapping

For most object models, ModelMapper does a good job of intelligently mapping source and destination properties. But for certain models where property and class names are very dissimilar, a PropertyMap can be created to define **explicit** mappings between source and destination properties.

{% include lang-tabs.html %}

<div class="tab-content">
<div class="tab-pane java6">
## Creating a PropertyMap

To start, extend PropertyMap, supplying type arguments to represent the source type `<S>` and destination type `<D>`, then override the `configure` method:

```java
public class PersonMap extends PropertyMap<Person, PersonDTO>() {
    protected void configure() {
        map().setName(source.getFirstName());
    }
};
```

### Using a PropertyMap

Once a PropertyMap is defined, it is used to add mappings to a ModelMapper:

```java
modelMapper.addMappings(new PersonMap());
```

Multiple PropertyMaps may be added for the same source and destination types, so long as only one mapping is defined for each destination property. 

**Explicit** mappings defined in a PropertyMap will override any **implicit** mappings for the same destination properties.
</div>
<div class="tab-pane active java8">
## Creating an Expression Mapping

You can simply define a property mapping by using lambda expressions to a source getter and a destination setter.

```java
typeMap.addMapping(Source::getFirstName, Destination::setName);
```

The types could be mismatched of the source getter and the destination setter.

```java
typeMap.addMapping(Source::getAge, Destination::setAgeString);
```

This example maps the destination type's `setAge` method to the source type's `getCustomer().getAge()` method hierarchy, allowing deep mapping to occur between the source and destination methods: 

```java
typeMap.addMapping(src -> src.getCustomer().getAge(), PersonDTO::setAge);
```

This example maps the destination type's `getCustomer().setName()` method hierarchy to the source type's `person.getFirstName()` property hierarchy: 

```java
typeMap.addMapping(
  src -> src.getPerson().getFirstName(),
  (dest, v) -> dest.getCustomer().setName(v));
```

## Creating an ExpressionMap

</div>
</div>

### Defining Mappings

<div class="tab-content">
<div class="tab-pane java6">
A PropertyMap allows you to define source to destination property and value mappings using actual code. These definitions are placed in the PropertyMap's `configure` method where they are captured and evaluated later.
</div>
<div class="tab-pane active java8">
An ExpressionMap allows you to define source to destination property and value mappings using lambda expression.
</div>
</div>

This example maps the destination type's `setName` method to the source type's `getFirstName` method:

<div class="tab-content">
<div class="tab-pane java6">
```java
map().setName(source.getFirstName());
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.map(Source::getFirstName, Destination::setName));
```
</div>
</div>

<div class="tab-content">
<div class="tab-pane java6">
This example maps the destination type's `setLastName` method to the source type's `surName` field:

```java
map().setLastName(source.surName);
```

This example maps the source type's `address` field to the destination type's `streetAddress` field:
	
	map(source.address, destination.streetAddress);

This example maps the destination type's `setEmployer` method to the constant value `"Initech"`:

```java
map().setEmployer("Initech");
```
	
This example maps the constant `"Initech"` to the destination field `employer`:

```java
map("Initech", destination.employer);
```
</div>
<div class="tab-pane active java8" />
</div>

### Handling Mismatched Types

Map statements can also be written to map methods whose types do not match:

<div class="tab-content">
<div class="tab-pane java6">
```java
map(source.getAge()).setAgeString(null);
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.map(Source::getAge, Destination::setAgeString));
```
</div>
</div>

<div class="tab-content">
<div class="tab-pane java6">
Similar for mapping to primitives:	

```java
map(source.getAgeString()).setAge((short) 0);
```

And for mapping from constant values:

```java
map(21).setAgeString(null);
```

**Note**: When a value is provided on the left-hand side of a `map()` statement, any value provided on the right-hand side in a setter is not used.
</div>
<div class="tab-pane active java8" />
</div>

## Deep Mapping

This example maps the destination type's `setAge` method to the source type's `getCustomer().getAge()` method hierarchy, allowing deep mapping to occur between the source and destination methods: 

<div class="tab-content">
<div class="tab-pane java6">
```java
map().setAge(source.getCustomer().getAge());
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.map(src -> src.getCustomer().getAge(), PersonDTO::setAge));
```
</div>
</div>

This example maps the destination type's `getCustomer().setName()` method hierarchy to the source type's `person.getFirstName()` property hierarchy: 

<div class="tab-content">
<div class="tab-pane java6">
```java
map().getCustomer().setName(source.person.getFirstName());
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.<String>map(
        src -> src.getPerson().getFirstName(),
        (dest, v) -> dest.getCustomer().setName(v)));
```
</div>
</div>

**Note**: In order to populate the destination object, deep mapping requires the `getCustomer` method to have a corresponding mutator, such as a `setCustomer` method or an accessible `customer` field.

<div class="tab-content">
<div class="tab-pane java6">
We can also mix field references into either the source or destination when deep mapping:

```java
map(source.customer.age, destination);
map().customer.setName(source.getPerson().firstName);
```

Deep mapping can also be performed for source properties or values whose types do not match the destination property's type:

```java
map(source.person.getAge()).setAgeString(null);
```

**Note**: Since the `setAgeString` method requires a value we simply pass in `null` which is unused.
</div>
<div class="tab-pane active java8" />
</div>

## Skipping Properties

While ModelMapper implicitly creates mappings from a source type to each property in the destination type, it may occasionally be desirable to skip the mapping of certain destination properties. 

This example specifies that the destination type's `setName` method should be skipped during the mapping process:

<div class="tab-content">
<div class="tab-pane java6">
```java
skip().setName(null);
```

**Note**: Since the `setName` method is skipped the `null` value is unused.
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.skip(Destination::setName));
```
</div>
</div>

<div class="tab-content">
<div class="tab-pane java6">
We can also skip the mapping of fields:

```java
skip(source.name);
```
</div>
<div class="tab-pane active java8" />
</div>

## Converters

Converters allow custom conversion to take place when mapping a source to a destination property (see the general page on [Converters](/user-manual/converters/) for more info). 

Consider this converter, which extends AbstractConverter and converts a String to an uppercase String:

<div class="tab-content">
<div class="tab-pane java6">
```java
Converter<String, String> toUppercase = new AbstractConverter<String, String>() {
    protected String convert(String source) {
        return source == null ? null : source.toUppercase();
    }
};
```
</div>
<div class="tab-pane active java8">
```java
Converter<String, String> toUppercase =
    ctx -> ctx.getSource() == null ? null : ctx.getSource().toUppercase();
```
</div>
</div>

<div class="tab-content">
<div class="tab-pane java6">
Using the `toUppercase` Converter to map from a source property to a destination property is simple:

```java
using(toUppercase).map().setName(source.getName());
```
</div>
<div class="tab-pane active java8">
Using the `toUppercase` Converter to map from a source property to a destination property is simple:

```java
typeMap.addMappings(
    mapper -> mapper.using(toUppercase).map(Person::getName, PersonDTO::setName));
```

Or we can also use lambda expression in `using`.

```java
typeMap.addMappings(
    mapper -> mapper
        .using(ctx -> ((String) ctx.getSource()).toUpperCase())
        .map(Person::getName, PersonDTO::setName));
```
</div>
</div>
	
<div class="tab-content">
<div class="tab-pane java6">
We can also use a Converter to map fields:

```java
using(toUppercase).map(source.name, destination.name);
```
</div>
<div class="tab-pane active java8" />
</div>

<div class="tab-content">
<div class="tab-pane java6">
Alternatively, instead of using a converter to map a source property we can create a Converter intended to map from a source _object_ to a destination property:

```java
Converter<Person, String> toUppercase = new AbstractConverter<Person, String>() {
    protected String convert(Person person) {
        return person == null ? null : person.getFirstName();
    }
};
```

When defining a mapping to use this converter, we simply pass the source _object_, which is of type `Person`, to the `map` method:

```java
using(personToNameConverter).map(source).setName(null);
```

**Note**: Since a source object is given, the `null` value passed to `setName` is unused.
</div>
<div class="tab-pane active java8" />

When using a Converter with a deep mapping, it is the last source and destination properties referenced by the mapping that will be passed to the the Converter:

<div class="tab-content">
<div class="tab-pane java6">
```java
// toUppercase will be called with the property types from getFirstName() and setName()
using(toUppercase).map().customer.setName(source.getPerson().getFirstName());
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    // toUppercase will be called with the property types from getFirstName() and setName()
    mapper -> mapper
        .using(toUppercase)
        .<String>map(src -> src.getPerson().getFirstName(), (dest, v) -> dest.getCustomer().setName(v)));
```
</div>
</div>

## Providers

Providers allow allow you to provide your own instance of destination properties and types prior to mapping (see the general page on [Providers](/user-manual/providers/) for more info).

Consider this Provider which provides `Person` instances:

<div class="tab-content">
<div class="tab-pane java6">
```java
Provider<Person> personProvider = new AbstractProvider<Person>() {
    public Person get() {
        return new Person();
    }
}
```
</div>
<div class="tab-pane active java8">
```java
Provider<Person> personProvider = req -> new Person();
```
</div>

<div class="tab-content">
<div class="tab-pane java6">
Configuring `personProvider` to be used for a specific property mapping is simple:

```java
with(personProvider).map().setPerson(source.getPerson());
```
</div>
<div class="tab-pane active java8">
Configuring `personProvider` to be used for a specific property mapping is simple:

```java
typeMap.addMappings(
    mapper -> mapper.with(personProvider).map(Source::getPerson, Destination::setPerson));
```

Or we can also use lambda expression in `with`.

```java
typeMap.addMappings(
    mapper -> mapper
        .with(req -> new Person())
        .map(Source::getPerson, Destination::setPerson));
```
</div>
</div>

<div class="tab-content">
<div class="tab-pane java6">
When mapping takes place, `personProvider` will be called to retrieve a Person instance, which will then be mapped to the destination object's `setPerson` method.

Providers can also be used with converters:
```java
with(personProvider)
    .using(personConverter)
    .map().setPerson(source.getPerson());
```

When using a Provider with a deep mapping, it is the last destination property referenced by the mapping that will be used for the Provider:

```java
// personProvider will be called with setPerson()'s property type as the requested type
with(personProvider).map().getOrder().setPerson(source.getPerson());
```
</div>
<div class="tab-pane active java8" />
</div>

## Conditional Mapping

Mapping for a destination property can be made conditional by supplying a `Condition` along with the mapping.

Consider this condition, which applies if the source type is not `null`:

<div class="tab-content">
<div class="tab-pane java6">
```java
Condition notNull = new Condition() {
    public boolean applies(MappingContext<?, ?> context) {
        return context.getSource() != null;
    }
};
```
</div>
<div class="tab-pane active java8">
```java
Condition notNull = ctx -> ctx.getSource() != null;
```
</div>
</div>

We can use the `notNull` Condition to specify that mapping only take place for a property if the source is not null:

<div class="tab-content">
<div class="tab-pane java6">
```java
when(notNull).map().setName(source.getName());
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.when(notNull).map(Person::getName, PersonDTO::setName));
```
</div>
</div>

In this example, mapping to `setName` will be _skipped_ if the source is not null, else mapping will proceed from the source object's `getName` method:

<div class="tab-content">
<div class="tab-pane java6">
```java
when(notNull).skip().setName(source.getName());
```
</div>
<div class="tab-pane active java8">
```java
typeMap.addMappings(
    mapper -> mapper.when(notNull).skip(PersonDTO::setName));
```
</div>
</div>

<div class="tab-content">
<div class="tab-pane java6">
We can also conditionally skip the mapping of fields:

```java
when(notNull).skip(source.name, destination.name);
```

Conditions can also be used with Providers and Converters:

```java
when(someCondition)
    .with(personProvider)
    .using(personConverter)
    .map().setPerson(source.getPerson());
```

When using a Condition with a deep mapping, it is the last source and destination properties referenced by the mapping that will be passed to the Condition:

```java
// isValidName will be called with the property types from getFirstName() and setName()
when(isValidName).map().getCustomer().setName(source.person.getFirstName());
```
</div>
<div class="tab-pane active java8" />
</div>

Several [built-in Conditions](/javadoc/org/modelmapper/Conditions.html) are available.

<div class="tab-content">
<div class="tab-pane java6">
### Combining Conditions

Conditions can be combined using boolean operators with the help of the `Conditions` class:

```java
when(Conditions.or(isNull, isEmpty)).skip().setName(source.getName());
when(Conditions.and(txIsActive, inScope)).map().setRequest(source.getRequest());
```

Alternatively, `Condition` implementations can extend `AbstractCondition` which has built in support for combining conditions:

```java
Condition isNull = new AbstractCondition() {
    public boolean applies(MappingContext<?, ?> context) {
        return context.getSource() == null;
    }
};

when(isNull.or(isEmpty)).skip().setName(source.getName());
```

## String Mapping

In addition to mapping properies using getters and setters, ModelMapper supports the mapping of source property paths defined as Strings:

```java
map().getCustomer().address.setStreet(this.<String>source("customer.street_address"));
```

Alternatively the source statement may also be used on the left-hand side of the `map()` statement:

```java
map(source("customer.street_address")).getCustomer().address.setStreet(null);
```
</div>
<div class="tab-pane active java8" />
</div>

This API makes it easy to define mappings from source objects that are not JavaBeans.
