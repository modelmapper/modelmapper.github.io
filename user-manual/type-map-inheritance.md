---
layout: content
menu: user-manual
title: Type Map Inheritance
---

# Type Map Inheritance

TypeMap inheritance provides methods that you can create a derived TypeMap from the base TypeMap with include.

{:.prettyprint .lang-java}
	// Creates a base TypeMap with explicit mappings
	TypeMap<BaseSrc, BaseDest> typeMap = modelMapper.addMapping(new PropertyMap<BaseSrc, BaseDest>() {
		public void configure() {
			map().setName(source.getFirstName());
		}
	});
	typeMap.include(SrcA.class, DestA.class)
		.include(SrcB.class, DestB.class)
		.include(SrcC.class, DestC.class);

We can also include a base TypeMap with includeBase.

{:.prettyprint .lang-java}
	typeMap.includeBase(BaseSrcA.class, BaseDestA.class)
		.includeBase(BaseSrcB.class, BaseDestB.class);
