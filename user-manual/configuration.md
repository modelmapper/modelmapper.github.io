---
layout: content
menu: user-manual
title: Configuration
---

# Configuration

ModelMapper uses a set of conventions and configuration to determine which source and destination properties match each other. Available configuration, along with default values, is described below:

Setting|Description|Default Value
-------|-----------|-------------
Access level|Determines which methods and fields are eligible for matching based on accessibility|public
Field matching|Indicates whether fields are eligible for matching|disabled
Naming convention|Determines which methods and fields are eligible for matching based on name|JavaBeans
Name transformer|Transforms eligible property and class names prior to tokenization|JavaBeans
Name tokenizer|Tokenizes source and destination property names prior to matching|Camel Case
[Matching strategy](#matching-strategies)|Determines how source and destination tokens are matched|Standard

You can read about how this configuration is used during the [matching process](/user-manual/how-it-works/#matching-process).

## Default Configuration

Default configuration uses the *Standard* matching strategy to match only *public* source and destination *methods* that are named according to the *JavaBeans* convention. 

## Configuration Examples

Adjusting configuration for certain matching requirements is simple. This example configures a `ModelMapper` to allow `protected` methods to be matched:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration()
	  .setMethodAccessLevel(AccessLevel.PROTECTED);

This example configures a `ModelMapper` to allow private fields to be matched:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration()
	  .setFieldMatchingEnabled(true)
	  .setFieldAccessLevel(AccessLevel.PRIVATE);

This example configures a `ModelMapper` to use the `Loose` [MatchingStrategies matching strategy]:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration()
	  .setMatchingStrategy(MatchingStrategies.LOOSE);

This example configures a `ModelMapper` to allow any source and destination property names to be eligible for matching:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration()
	  .setSourceNamingConvention(NamingConventions.NONE);
	  .setDestinationNamingConvention(NamingConventions.NONE);

This example configures a `ModelMapper` to use the `Underscore` name tokenizer for source and destination properties:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration();
	  .setSourceNameTokenizer(NameTokenizers.UNDERSCORE);
	  .setDestinationNameTokenizer(NameTokenizers.UNDERSCORE);

## Available Conventions

ModelMapper includes several pre-defined conventions for handling different property matching requirements:

Convention|Description
----------|-----------
`NamingConventions.NONE`| Represents no naming convention, which applies to all property names
`NamingConventions.JAVABEANS_ACCESSOR`|Finds eligible accessors according to JavaBeans convention
`NamingConventions.JAVABEANS_MUTATOR`|Finds eligible mutators according to JavaBeans convention
`NameTransformers.JAVABEANS_ACCESSOR`|Transforms accessor names according to JavaBeans convention
`NameTransformers.JAVABEANS_MUTATOR`|Transforms mutators names according to JavaBeans convention
`NameTokenizers.CAMEL_CASE`|Tokenizes property and class names according to Camel Case convention
`NameTokenizers.UNDERSCORE`|Tokenizes property and class names by underscores
`MatchingStrategies.STANDARD`|Intelligently matches source and destination properties
`MatchingStrategies.LOOSE`|Loosely matches source and destination properties
`MatchingStrategies.STRICT`|Strictly matches source and destination properties

## Matching Strategies

Matching strategies are used during the [matching process](/user-manual/how-it-works/#matching-process) to match source and destination properties to each other. Below is a description of each strategy.

### Standard

The Standard matching strategy allows for source properties to be intelligently matched to destination properties, requiring that _all_ destination properties be matched and all source property names have at least one token matched. The following rules apply: 

  * Tokens can be matched in _any_ order 
  * _All_ destination property name tokens must be matched 
  * _All_ source property names must have at least _one_ token matched

The standard matching strategy is configured by default, and while it is not exact, it is ideal to use in most scenarios.

### Loose

The Loose matching strategy allows for source properties to be loosely matched to destination properties by requiring that _only_ the last destination property in a hierarchy be matched. The following rules apply: 

  * Tokens can be matched in _any_ order 
  * The last destination property name must have _all_ tokens matched 
  * The last source property name must have at least _one_ token matched

The loose matching strategy is ideal to use for source and destination object models with property hierarchies that are very dissimilar. It may result in a higher level of ambiguous matches being detected, but for well-known object models it can be a quick alternative to defining [mappings](/user-manual/property-mapping).

### Strict

The strict matching strategy allows for source properties to be strictly matched to destination properties. This strategy allows for complete matching accuracy, ensuring that no mismatches or ambiguity occurs. But it requires that property name tokens on the source and destination side match each other precisely. The following rules apply: 

  * Tokens are matched in _strict_ order 
  * _All_ destination property name tokens must be matched 
  * _All_ source property names must have _all_ tokens matched 

The strict matching strategy is ideal to use when you want to ensure that no ambiguity or unexpected mapping occurs without having to inspect a `TypeMap`. The drawback is that the strictness may result in some destination properties remaining unmatched.