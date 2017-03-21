---
layout: content
menu: user-manual
title: jOOQ Integrations
---

# jOOQ Integration

ModelMapper's jOOQ integration allows you to map a jOOQ [Record](http://www.jooq.org/javadoc/latest/org/jooq/Record.html) to a JavaBean. 

## Setup

To get started, add the `modelmapper-jooq` Maven dependency to your project:

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper.extensions</groupId>
	  <artifactId>modelmapper-jooq</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>

Next, configure ModelMapper to support the RecordValueReader, which allows for values to be read and mapped from a jOOQ [Record](http://www.jooq.org/javadoc/latest/org/jooq/Record.html):

{:.prettyprint .lang-java}
    modelMapper.getConfiguration().addValueReader(new RecordValueReader());

## Example Mapping

Now let's see an example mapping of a jOOQ record to a JavaBean. Consider the following record representing an order:

order_id|customer_id|customer_street_address|customer_address_city
-------|-----------
345|678|123 Main Street|SF

We may need to map this to a more complex object model:

{:.prettyprint .lang-java}
	// Assume getters and setters are present

    public class Order {
      private int id;
      private Customer customer;
    }

    public class Customer {
	  private Address address;
    }

    public class Address {
      private String street;
	  private String city;
    }

Since the source Record's fields in this example uses an underscore naming convention, we'll need to configure ModelMapper to tokenize source property names by underscore:

{:.prettyprint .lang-java}
    modelMapper.getConfiguration().setSourceNameTokenizer(NameTokenizers.UNDERSCORE);

With that set, mapping an order Record to an Order object is simple:

{:.prettyprint .lang-java}
	Order order = modelMapper.map(orderRecord, Order.class);
	
And we can assert that values are mapped as expected:

{:.prettyprint .lang-java}
    assertEquals(order.getId(), 456);
    assertEquals(order.getCustomer().getId(), 789);
    assertEquals(order.getCustomer().getAddress().getStreet(), "123 Main Street");
    assertEquals(order.getCustomer().getAddress().getCity(), "SF");
    
## Explicit Mapping

While ModelMapper will do its best to **implicitly** match Record values to destination properties, sometimes you may need to **explicitly** define how one property maps to another. A [PropertyMap](/user-manual/property-mapping/) allows us to do this.

Let's define how a `Record` maps to an `Order` by creating a PropertyMap. Our PropertyMap will include a `map()` statement that maps a source Record's `customer_street_address` field to a destination Order's `getCustomer().getAddress().setStreet()` method hierarchy:

{:.prettyprint .lang-java}
    PropertyMap<Record, Order> orderMap = new PropertyMap<Record, Order>() {
      protected void configure() {
        map().getCustomer().getAddress().setStreet(this.<String>source("customer_street_address"));
      }
    };

To use our PropertyMap, we'll create a TypeMap for our order Record and add our PropertyMap to it:

{:.prettyprint .lang-java}
	modelMapper.createTypeMap(orderRecord, Order.class).addMappings(orderMap)

We can then map Records to Orders as usual, with properties being mapped according to the PropertyMap that we defined:
	
{:.prettyprint .lang-java}
	Order order = modelMapper.map(orderRecord, Order.class);
    
## Things to Note

ModelMapper maintains a [TypeMap](http://modelmapper.org/javadoc/org/modelmapper/TypeMap.html) for each source and destination type, containing the mappings bewteen the two types. For "generic" types such as Record this can be problematic since the structure of a Record can vary. In order to distinguish structurally different Records that map to the same destination type, we can provide a _type map name_ to ModelMapper.

Continuing with the example above, let's map another order Record, this one with a different structure, to the same Order class:

order_id|order_customer_id|order_customer_street_address|order_customer_address_city
-------|-----------
444|777|123 Main Street|LA
    
Mapping this Record to an order is simple, but we'll need to provide a _type map name_ to distinguish this Record to Order mapping from the previous unnamed mapping:

{:.prettyprint .lang-java}
	Order order = modelMapper.map(longOrderRecord, Order.class, "long");