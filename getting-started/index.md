---
layout: content
menu: getting-started
title: Getting Started
---

# Getting Started

This section will guide you through the setup and basic usage of ModelMapper.

## Setting Up

If you're a Maven user just add the `modelmapper` library as a dependency:

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper</groupId>
	  <artifactId>modelmapper</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>

Otherwise you can [download](/downloads) the latest ModelMapper jar and add it to your classpath.

## Mapping

Let's try mapping some objects. Consider the following source and destination [object models](https://github.com/jhalterman/modelmapper/tree/master/examples/src/main/java/org/modelmapper/gettingstarted):

{::options parse_block_html="true" /}
<div class="container">
<div class="row">
<div class="span4">  
#### Source model

{:.prettyprint .lang-java}
	// Assume getters and setters on each class
	class Order {
	  Customer customer;
	  Address billingAddress;
	}
	
	class Customer {
	  Name name;
	}
	
	class Name {
	  String firstName;
	  String lastName;
	}
	
	class Address {
	  String street;
	  String city;
	}
</div>
<div class="span4">
#### Destination Model

{:.prettyprint .lang-java}
	// Assume getters and setters
	class OrderDTO {
	  String customerFirstName;
	  String customerLastName;
	  String billingStreet;
	  String billingCity;
	}
</div>
</div>
</div>

We can use ModelMapper to implicitly map an `order` instance to a new `OrderDTO`:

{:.prettyprint .lang-java}
	ModelMapper modelMapper = new ModelMapper();
	OrderDTO orderDTO = modelMapper.map(order, OrderDTO.class);

And we can test that properties are mapped as expected:

{:.prettyprint .lang-java}
	assertEquals(order.getCustomer().getName().getFirstName(), orderDTO.getCustomerFirstName());
	assertEquals(order.getCustomer().getName().getLastName(), orderDTO.getCustomerLastName());
	assertEquals(order.getBillingAddress().getStreet(), orderDTO.getBillingStreet());
	assertEquals(order.getBillingAddress().getCity(), orderDTO.getBillingCity());

## How It Works

When the `map` method is called, the _source_ and _destination_ types are analyzed to determine which properties implicitly match according to a [matching strategy](http://modelmapper.org/user-manual/configuration/#matching-strategies) and other [configuration](/user-manual/configuration). Data is then mapped according to these matches.

Even when the _source_ and _destination_ objects and their properties are different, as in the example above, ModelMapper will do its best to determine reasonable matches between properties according to the configured [matching strategy](http://modelmapper.org/user-manual/configuration/#matching-strategies).

## Explicit Mapping

While ModelMapper will do its best to implicitly match source and destination properties for you, sometimes you may need to explicitly define mappings between properties.

ModelMapper supports a variety of mapping approaches, allowing you to use any mix of methods and field references. Let's map `Order.billingAddress.street` to `OrderDTO.billingStreet` and map `Order.billingAddress.city` to `OrderDTO.billingCity`. First we'll define a `PropertyMap` that contains these mappings:

{:.prettyprint .lang-java}
	PropertyMap<Order, OrderDTO> orderMap = new PropertyMap<Order, OrderDTO>() {
	  protected void configure() {
	    map().setBillingStreet(source.getBillingAddress().getStreet());
	    map(source.billingAddress.getCity(), destination.billingCity);
	  }
	};

Then with our mappings defined we can add them to our `ModelMapper` instance:

{:.prettyprint .lang-java}
	modelMapper.addMappings(orderMap);

## Conventional Configuration

As an alternative to manually mapping `Order.billingAddress.street` to `OrderDTO.billingStreet`, we can configure different conventions to be used when ModelMapper attempts to match these properties. The Loose [matching strategy](http://modelmapper.org/user-manual/configuration/#matching-strategies), which more loosely matches property names, will work in this case:

{:.prettyprint .lang-java}
	modelMapper.getConfiguration().setMatchingStrategy(MatchingStrategies.LOOSE);

Additional [matching strategies](http://modelmapper.org/user-manual/configuration/#matching-strategies) and other conventions can also be [configured](/user-manual/configuration) to control ModelMapper's property matching process.

## Validating Matches

Aside from writing tests to verify that your objects are being mapped as expected, ModelMapper has built-in [validation](/user-manual/validation) that will let you know if any destination properties are unmatched.

First we have to let ModelMapper know about the types we want to validate. We can do this by creating a `TypeMap`:

{:.prettyprint .lang-java}
	modelMapper.createTypeMap(Order.class, OrderDTO.class);

Or we can add mappings from a `PropertyMap` which will automatically create a `TypeMap` if one doesn't already exist for the _source_ and _destination_ types:

{:.prettyprint .lang-java}
	modelMapper.addMappings(new OrderMap());

Then we can validate our mappings:

{:.prettyprint .lang-java}
	modelMapper.validate();

If any `TypeMap` contains a destination property that is unmatched to a source property a `ValidationException` will be thrown.

## Next Steps

Congratulations! You've learned how to map objects with safety and ease. 

There's a lot more to ModelMapper, including the complete [mapping API](/user-manual/property-mapping/) and [configuration](/user-manual/configuration) options. Check out the [user manual](/user-manual/) to learn more. You can also check out some additional [examples](/examples/) of ModelMapper in action.