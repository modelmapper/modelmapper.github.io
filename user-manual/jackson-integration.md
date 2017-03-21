---
layout: content
menu: user-manual
title: Jackson Integration
---

# Jackson Integration

ModelMapper's Jackson integration allows you to map a Jackson [JsonNode](http://jackson.codehaus.org/1.9.4/javadoc/org/codehaus/jackson/JsonNode.html) to a JavaBean.

## Setup

To get started, add the `modelmapper-jackson` Maven dependency to your project:

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper.extensions</groupId>
	  <artifactId>modelmapper-jackson</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>
	
Next, configure ModelMapper to support the JsonNodeValueReader, which allows for values to be read and mapped from a [JsonNode](http://jackson.codehaus.org/1.9.4/javadoc/org/codehaus/jackson/JsonNode.html):

{:.prettyprint .lang-java}
    modelMapper.getConfiguration().addValueReader(new JsonNodeValueReader());
	
## Usage Example

Consider the following JSON representing an order:

{:.prettyprint .lang-json}
    {
      "id": 456,
      "customer": {
        "id": 789,
        "street_address": "123 Main Street", 
        "address_city": "SF"
      }
    }

We may need to map this to a different object model:

{:.prettyprint .lang-java}
	// Assume getters and setters are present

    public class Order {
      private int id;
      private Customer customer;
    }

    public class Customer {
      private int id;        
	  private Address address;
    }

    public class Address {
      private String street;
	  private String city;
    }

Since the order JSON in this example uses an underscore naming convention, we'll need to configure ModelMapper to tokenize source property names by underscore:

{:.prettyprint .lang-java}
    modelMapper.getConfiguration().setSourceNameTokenizer(NameTokenizers.UNDERSCORE);

With that set, mapping a JsonNode for the order JSON to an Order object is simple:

{:.prettyprint .lang-java}
    JsonNode orderNode = new ObjectMapper().readTree(orderJson);
	Order order = modelMapper.map(orderNode, Order.class);
	
And we can assert that values are mapped as expected:

{:.prettyprint .lang-java}
    assertEquals(order.getId(), 456);
    assertEquals(order.getCustomer().getId(), 789);
    assertEquals(order.getCustomer().getAddress().getStreet(), "123 Main Street");
    assertEquals(order.getCustomer().getAddress().getCity(), "SF");
    
## Explicit Mapping

While ModelMapper will do its best to **implicitly** match JsonNode values to destination properties, sometimes you may need to **explicitly** define how one property maps to another. A [PropertyMap](/user-manual/property-mapping/) allows us to do this.

Let's define how a `JsonNode` maps to an `Order` by creating a PropertyMap. Our PropertyMap will include a `map()` statement that maps a source JsonNode's `customer.street_address` field hierarchy to a destination Order's `getCustomer().getAddress().setStreet()` method hierarchy:

{:.prettyprint .lang-java}
    PropertyMap<JsonNode, Order> orderMap = new PropertyMap<JsonNode Order>() {
      protected void configure() {
        map().getCustomer().getAddress().setStreet(this.<String>source("customer.street_address"));
      }
    };

To use our PropertyMap, we'll create a TypeMap for our order JsonNode and add our PropertyMap to it:

{:.prettyprint .lang-java}
	modelMapper.createTypeMap(orderNode, Order.class).addMappings(orderMap)

We can then map JsonNodes to Orders as usual, with properties being mapped according to the PropertyMap that we defined:
	
{:.prettyprint .lang-java}
	Order order = modelMapper.map(orderNode, Order.class);
    
## Things to Note

ModelMapper maintains a [TypeMap](http://modelmapper.org/javadoc/org/modelmapper/TypeMap.html) for each source and destination type, containing the mappings bewteen the two types. For "generic" types such as JsonNode this can be problematic since the structure of a JsonNode can vary. In order to distinguish structurally different JsonNodes that map to the same destination type, we can provide a _type map name_ to ModelMapper.

Continuing with the example above, let's map another order JSON, this one with a different structure, to the same Order class:

{:.prettyprint .lang-json}
    {
      "id": 222,
      "customer_id": 333,
      "customer_street_address": "444 Main Street",
      "customer_address_city": "LA"
    }
    
Mapping this JSON to an order is simple, but we'll need to provide a _type map name_ to distinguish this JsonNode to Order mapping from the previous unnamed mapping:

{:.prettyprint .lang-java}
    JsonNode orderNode = new ObjectMapper().readTree(flatJson);
	Order order = modelMapper.map(orderNode, Order.class, "flat");