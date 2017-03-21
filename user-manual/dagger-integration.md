---
layout: content
menu: user-manual
title: Dagger Integration
---

# Dagger Integration

ModelMapper's Dagger integration allows for the provisioning of destination objects to be delegated to a Dagger ObjectGraph during the mapping process. 

## Setup

To get started, add the `modelmapper-dagger` Maven dependency to your project:

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper.extensions</groupId>
	  <artifactId>modelmapper-dagger</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>
	
## Usage

Let's obtain a Dagger integrated [Provider](/user-manual/providers), which will delegate to an [ObjectGraph](http://square.github.io/dagger/javadoc/dagger/ObjectGraph.html) whenever called:

{:.prettyprint .lang-java}
	Provider<?> daggerProvider = DaggerIntegration.fromDagger(objectGraph);

Then we can configure the Provider for to be used globally for a ModelMapper:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration().setProvider(daggerProvider);

Or set the Provider to be used for a specific TypeMap:

{:.prettyprint .lang-java}
	typeMap.setProvider(daggerProvider);

The provider can also be used for individual mappings:

{:.prettyprint .lang-java}
	with(daggerProvider).map().someSetter(source.someGetter());