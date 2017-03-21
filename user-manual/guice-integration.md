---
layout: content
menu: user-manual
title: Guice Integration
---

# Guice Integration

ModelMapper's Guice integration allows for the provisioning of destination objects to be delegated to a Guice Injector during the mapping process. 

## Setup

To get started, add the `modelmapper-guice` Maven dependency to your project:

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper.extensions</groupId>
	  <artifactId>modelmapper-guice</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>

## Usage

Let's obtain a Guice integrated [Provider](/user-manual/providers), which will delegate to an [Injector](https://google-guice.googlecode.com/svn/trunk/latest-javadoc/com/google/inject/Injector.html) whenever called:

{:.prettyprint .lang-java}
	Provider<?> guiceProvider = GuiceIntegration.fromGuice(injector);

Then we can configure the Provider for to be used globally for a ModelMapper:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration().setProvider(guiceProvider);

Or set the Provider to be used for a specific TypeMap:

{:.prettyprint .lang-java}
	typeMap.setProvider(guiceProvider);

The provider can also be used for individual mappings:

{:.prettyprint .lang-java}
	with(guiceProvider).map().someSetter(source.someGetter());