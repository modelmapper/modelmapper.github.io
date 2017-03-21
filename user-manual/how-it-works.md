---
layout: content
menu: user-manual
title: How It Works
---

# How It Works

ModelMapper consists of two separate processes: the _matching process_, where a source and destination type's properties are matched to each other, and the _mapping process_ where matched property values are converted from a source to destination object. A further description of these processes follows.


## Matching Process

The matching process uses conventions [configured](/user-manual/configuration/) in a `ModelMapper` or `TypeMap` to determine which source and destination properties match each other. The process works by identifying eligible properties, transforming and tokenizing their names, then using those tokens to determine source to destination matches.

### Eligibility

Property eligibility is determined in two steps. First, if field matching is enabled then fields that are accessible according to the configured **AccessLevel** and that are named according to the configured **NamingConvention** are eligible. Similarly, methods are eligible based on configured AccessLevels and NamingConventions. Eligible methods take precedence over fields with the same _transformed_ property name.

Additionally, only source methods with zero parameters and a non-`void` return type are eligible, and destination methods with one parameter and a `void` return type are eligible.

### Transformation

Prior to matching, **NameTransformers** are used to transform property names to their simple _property_ name, so that source and destination properties that are named according to different conventions can be matched. For example: Consider a source object with a `getPerson` method and a destination object with a `setPerson` method. In order for these to be matched, a `NameTransformer` is used to transform the method names to `person`.

Typically `NameTransformer` implementations will transform names according to the same convention as a `NamingConvention`, and are therefore usually configured together - such as with JavaBeans.

### Tokenization

After transformation, **NameTokenizers** are used to tokenize class and property names for matching.

### Matching

[MatchingStrategies](/user-manual/configuration/#matching-strategies) are used to determine whether source and destination properties match based on their name and class name tokens. Different strategies can allow for more loose or strict matching of tokens.

### Handling Ambiguity

For matching strategies that are inexact, it is possible that multiple source properties may match the same destination property. When this occurs, the matching engine attempts to resolve the ambiguity by finding the closest match among the duplicates. 

If the ambiguity cannot be resolved, a ConfigurationException is thrown, except when the `MatchingConfiguration` is set to `ignoreAmbiguity` in which case the destination property will simply be skipped during the mapping process.

## Mapping Process

The process for mapping a source to a destination object is as follows:

 * If a `TypeMap` exists for the source and destination types, mapping will occur according to the `Mapping`s defined in the `TypeMap`.
 * Else if a `Converter` exists that is capable of converting the source object to the destination type, mapping will occur using the `Converter`.
 * Else a new `TypeMap` is created for the source and destination types, and mapping will occur according to the implicit `Mapping`s captured in the `TypeMap`.