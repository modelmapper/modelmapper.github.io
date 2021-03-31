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
class BaseSrc {
	private String firstName;
	public String getFirstName();
}

class Src extends BaseSrc {
}

class BaseDest {
	private String name;
	public void setName(String name);
}

class Dest extends BaseDest {	
}

// Create base TypeMaps with explicit mappings
TypeMap<BaseSrc, BaseDest> baseTypeMap = modelMapper.createTypeMap(BaseSrc.class, BaseDest.class)
	.addMapping(BaseSrc::getFirstName, BaseDest::setName);

TypeMap<Src, Dest> typeMap = modelMapper.createTypeMap(Src.class, Dest.class);
typeMap.includeBase(baseTypeMap);
```
