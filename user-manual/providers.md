---
layout: content
menu: user-manual
title: Providers
---

# Providers

Providers allow you to provide your own instance of destination properties and types prior to mapping as opposed to having ModelMapper construct them via the default constructor. They can be configured globally, for a specific TypeMap, or for specific properties.

## Creating a Provider

Providers can be implemented in two ways. The first is by extending AbstractProvider which allows for instances of a single type to be provided:

{:.prettyprint .lang-java}
	Provider<Person> personProvider = new AbstractProvider<Person>() {
	  public Person get() {
	    return new Person();
	  }
	}

The second way is by implementing the `Provider` interface which exposes a `ProvisionRequest` that contains the requested type. This allows for a generic provider to be created which can provide instances of multiple types:

{:.prettyprint .lang-java}
	Provider<Object> delegatingProvider = new Provider<Object>() {
	  public Object get(ProvisionRequest<Object> request) {
	    return SomeFactory.getInstance(request.getRequestedType());
	  }
	}

The example above delegates provisioning of the requested types to `SomeFactory`.

## Configuration

Providers can be configured for use in different contexts:

### By ModelMapper

A provider can be configured for a `ModelMapper` instance to provide instances of _all_ destination types during the mapping process. This is particularly useful for delegating object provisioning to an IoC container (See the [Spring](/user-manual/extensions#spring) and [Guice](/user-manual/extensions#guice) extensions for more on this).

{:.prettyprint .lang-java}
	modelMapper.getConfiguration().setProvider(delegatingProvider);

### By TypeMap

Providers can also be configured for specific TypeMaps:

{:.prettyprint .lang-java}
	TypeMap<PersonDTO, Person> personTypeMap = modelMapper.createTypeMap(PersonDTO.class, Person.class);
	personTypeMap.setProvider(personProvider);

In this example the `personProvider` will be used by the `personTypeMap` to provide instances of the destination type `Person`.

We can also specify a Provider to be used for providing instances of mapped properties within a TypeMap:

{:.prettyprint .lang-java}
	typeMap.setPropertyProvider(delegatingProvider);

### By Property

Finally, providers can be used to provide instances of specific properties. See the mapping API page on [providers](/user-manual/property-mapping/#providers) for more on this.

### Notes

If you don't want to or can't construct a requested type, you can always return `null` from a Provider and ModelMapper will construct the type for you.