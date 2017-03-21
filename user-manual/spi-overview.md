---
layout: content
menu: user-manual
title: SPI Overview
---

# SPI Overview

The ModelMapper Service Provider Interface (SPI) consists of the following types:

  * **ConditionalConverter**
    * The interface you implement to provide custom conversion from any supported object to a particular destination type
    * Registered to a ModelMapper

  * **Mapping**
    * The interface you use to see how a destination property hierarchy is mapped from a source property hierarchy or constant value
    * Retrieved from a TypeMap

  * **NamingConvention**
    * The interface you implement to determine which property names are eligible for matching
    * Set against a Configuration

  * **NameTransformer**
    * The interface you implement to transform property names prior to matching
    * Set against a Configuration

  * **NameTokenizer**
    * The interface you implement to tokenize property names prior to matching
    * Set against a Configuration

  * **[MatchingStrategy](/user-manual/configuration/#matching-strategies)**
    * The interface you implement to determine whether a source property hierarchy matches a destination property hierarchy
    * Set against a Configuration
    
  * **ValueReader**
    * The interface you implement to read values from generic data sources
    * Added to a Configuration