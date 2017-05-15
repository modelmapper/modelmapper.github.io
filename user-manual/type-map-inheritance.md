---
layout: content
menu: user-manual
title: Type Map Inheritance
---

# Type Map Inheritance

TypeMap inheritance provides methods that you can create a derived TypeMap from the base TypeMap with include.

```java
// Creates a base TypeMap with explicit mappings
TypeMap<BaseSrc, BaseDest> typeMap = modelMapper.createTypeMap(BaseSrc.class, BaseDest.class)
	.addMapping(BaseSrc::getFirstName, BaseDest::setName);

typeMap.include(SrcA.class, DestA.class)
	.include(SrcB.class, DestB.class)
	.include(SrcC.class, DestC.class);
```

We can also include a base TypeMap with includeBase.

```java
// Create base TypeMaps with explicit mappings
TypeMap<BaseSrcA, BaseDestA> typeMap = modelMapper.createTypeMap(BaseSrcA.class, BaseDestA.class)
	.addMapping(BaseSrcA::getFirstName, BaseDestA::setName);
TypeMap<BaseSrcB, BaseDestB> typeMap = modelMapper.createTypeMap(BaseSrcB.class, BaseDestB.class)
	.addMapping(BaseSrcB::getAgeString, BaseDestB::setAge);

typeMap.includeBase(BaseSrcA.class, BaseDestA.class)
	.includeBase(BaseSrcB.class, BaseDestB.class);
```
