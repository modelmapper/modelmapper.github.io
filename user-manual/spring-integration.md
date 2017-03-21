---
layout: content
menu: user-manual
title: Spring Integration
---

# Spring Integration

ModelMapper's Spring integration allows for the provisioning of destination objects to be delegated to a Spring BeanFactory during the mapping process. 

## Setup

To get started, add the `modelmapper-spring` Maven dependency to your project:

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper.extensions</groupId>
	  <artifactId>modelmapper-spring</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>
	
## Usage

Let's obtain a Spring integrated [Provider](/user-manual/providers), which will delegate to a [BeanFactory](http://static.springsource.org/spring/docs/3.0.x/javadoc-api/org/springframework/beans/factory/BeanFactory.html) whenever called:

{:.prettyprint .lang-java}
	Provider<?> springProvider = SpringIntegration.fromSpring(beanFactory);

Then we can configure the Provider for to be used globally for a ModelMapper:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration().setProvider(springProvider);

Or set the Provider to be used for a specific TypeMap:

{:.prettyprint .lang-java}
	typeMap.setProvider(springProvider);

The provider can also be used for individual mappings:

{:.prettyprint .lang-java}
	with(springProvider).map().someSetter(source.someGetter());