---
layout: content
menu: user-manual
title: Property Mapping
---

# Property Mapping

For most object models, ModelMapper does a good job of intelligently mapping source and destination properties. But for certain models where property and class names are very dissimilar, a PropertyMap can be created to define **explicit** mappings between source and destination properties.

## Creating a PropertyMap

To start, extend PropertyMap, supplying type arguments to represent the source type `<S>` and destination type `<D>`, then override the `configure` method:

{:.prettyprint .lang-java}
	public class PersonMap extends PropertyMap<Person, PersonDTO>() {
	  protected void configure() {
	    map().setName(source.getFirstName());
	  }
	};

### Defining Mappings

A PropertyMap allows you to define source to destination property and value mappings using actual code. These definitions are placed in the PropertyMap's `configure` method where they are captured and evaluated later.

This example maps the destination type's `setName` method to the source type's `getFirstName` method:

{:.prettyprint .lang-java}
	map().setName(source.getFirstName());

This example maps the destination type's `setLastName` method to the source type's `surName` field:

{:.prettyprint .lang-java}	
	map().setLastName(source.surName);
	
This example maps the source type's `address` field to the destination type's `streetAddress` field:
	
	map(source.address, destination.streetAddress);

This example maps the destination type's `setEmployer` method to the constant value `"Initech"`:

{:.prettyprint .lang-java}
	map().setEmployer("Initech");
	
This example maps the constant `"Initech"` to the destination field `employer`:

{:.prettyprint .lang-java}
	map("Initech", destination.employer);
	
### Using a PropertyMap

Once a PropertyMap is defined, it is used to add mappings to a ModelMapper:

{:.prettyprint .lang-java}
	modelMapper.addMappings(new PersonMap());

Multiple PropertyMaps may be added for the same source and destination types, so long as only one mapping is defined for each destination property. 

**Explicit** mappings defined in a PropertyMap will override any **implicit** mappings for the same destination properties.

### Handling Mismatched Types

Map statements can also be written to map methods whose types do not match:

{:.prettyprint .lang-java}
	map(source.getAge()).setAgeString(null);

Similar for mapping to primitives:	

{:.prettyprint .lang-java}
	map(source.getAgeString()).setAge((short) 0);

And for mapping from constant values:

{:.prettyprint .lang-java}
	map(21).setAgeString(null);

**Note**: When a value is provided on the left-hand side of a `map()` statement, any value provided on the right-hand side in a setter is not used.

## Deep Mapping

This example maps the destination type's `setAge` method to the source type's `getCustomer().getAge()` method hierarchy, allowing deep mapping to occur between the source and destination methods: 

{:.prettyprint .lang-java}
	map().setAge(source.getCustomer().getAge());

This example maps the destination type's `getCustomer().setName()` method hierarchy to the source type's `person.getFirstName()` property hierarchy: 

{:.prettyprint .lang-java}
	map().getCustomer().setName(source.person.getFirstName());

**Note**: In order to populate the destination object, deep mapping requires the `getCustomer` method to have a corresponding mutator, such as a `setCustomer` method or an accessible `customer` field.

We can also mix field references into either the source or destination when deep mapping:

{:.prettyprint .lang-java}
    map(source.customer.age, destination);
    map().customer.setName(source.getPerson().firstName);

Deep mapping can also be performed for source properties or values whose types do not match the destination property's type:

{:.prettyprint .lang-java}
	map(source.person.getAge()).setAgeString(null);

**Note**: Since the `setAgeString` method requires a value we simply pass in `null` which is unused.

## Skipping Properties

While ModelMapper implicitly creates mappings from a source type to each property in the destination type, it may occasionally be desirable to skip the mapping of certain destination properties. 

This example specifies that the destination type's `setName` method should be skipped during the mapping process:

{:.prettyprint .lang-java}
	skip().setName(null);

**Note**: Since the `setName` method is skipped the `null` value is unused.

We can also skip the mapping of fields:

{:.prettyprint .lang-java}
	skip(source.name);

## Converters

Converters allow custom conversion to take place when mapping a source to a destination property (see the general page on [Converters](/user-manual/converters/) for more info). 

Consider this converter, which extends AbstractConverter and converts a String to an uppercase String:

{:.prettyprint .lang-java}
	Converter<String, String> toUppercase = new AbstractConverter<String, String>() {
	  protected String convert(String source) {
	    return source == null ? null : source.toUppercase();
	  }
	};

Using the `toUppercase` Converter to map from a source property to a destination property is simple:

{:.prettyprint .lang-java}
	using(toUppercase).map().setName(source.getName());
	
We can also use a Converter to map fields:

{:.prettyprint .lang-java}
	using(toUppercase).map(source.name, destination.name);

Alternatively, instead of using a converter to map a source property we can create a Converter intended to map from a source _object_ to a destination property:

{:.prettyprint .lang-java}
	Converter<Person, String> toUppercase = new AbstractConverter<Person, String>() {
	  protected String convert(Person person) {
	    return person == null ? null : person.getFirstName();
	  }
	};

When defining a mapping to use this converter, we simply pass the source _object_, which is of type `Person`, to the `map` method:

{:.prettyprint .lang-java}
	using(personToNameConverter).map(source).setName(null);

**Note**: Since a source object is given, the `null` value passed to `setName` is unused.

When using a Converter with a deep mapping, it is the last source and destination properties referenced by the mapping that will be passed to the the Converter:

{:.prettyprint .lang-java}
    // toUppercase will be called with the property types from getFirstName() and setName()
	using(toUppercase).map().customer.setName(source.getPerson().getFirstName());

## Providers

Providers allow allow you to provide your own instance of destination properties and types prior to mapping (see the general page on [Providers](/user-manual/providers/) for more info).

Consider this Provider which provides `Person` instances:

{:.prettyprint .lang-java}
	Provider<Person> personProvider = new AbstractProvider<Person>() {
	  public Person get() {
	    return new Person();
	  }
	}

Configuring `personProvider` to be used for a specific property mapping is simple:

{:.prettyprint .lang-java}
	with(personProvider).map().setPerson(source.getPerson());

When mapping takes place, `personProvider` will be called to retrieve a Person instance, which will then be mapped to the destination object's `setPerson` method.

Providers can also be used with converters:

{:.prettyprint .lang-java}
	with(personProvider)
	    .using(personConverter)
	    .map().setPerson(source.getPerson());
	    
When using a Provider with a deep mapping, it is the last destination property referenced by the mapping that will be used for the Provider:

{:.prettyprint .lang-java}
    // personProvider will be called with setPerson()'s property type as the requested type
	with(personProvider).map().getOrder().setPerson(source.getPerson());

## Conditional Mapping

Mapping for a destination property can be made conditional by supplying a `Condition` along with the mapping.

Consider this condition, which applies if the source type is not `null`:

{:.prettyprint .lang-java}
	Condition notNull = new Condition() {
	  public boolean applies(MappingContext<?, ?> context) {
	    return context.getSource() != null;
	  }
	};

We can use the `notNull` Condition to specify that mapping only take place for a property if the source is not null:

{:.prettyprint .lang-java}
	when(notNull).map().setName(source.getName());

In this example, mapping to `setName` will be _skipped_ if the source is not null, else mapping will proceed from the source object's `getName` method:

{:.prettyprint .lang-java}
	when(notNull).skip().setName(source.getName());
	
We can also conditionally skip the mapping of fields:

{:.prettyprint .lang-java}
	when(notNull).skip(source.name, destination.name);

Conditions can also be used with Providers and Converters:

{:.prettyprint .lang-java}
	when(someCondition)
	    .with(personProvider)
	    .using(personConverter)
	    .map().setPerson(source.getPerson());
	    
When using a Condition with a deep mapping, it is the last source and destination properties referenced by the mapping that will be passed to the Condition:

{:.prettyprint .lang-java}
    // isValidName will be called with the property types from getFirstName() and setName()
	when(isValidName).map().getCustomer().setName(source.person.getFirstName());

Several [built-in Conditions](/javadoc/org/modelmapper/Conditions.html) are available.

### Combining Conditions

Conditions can be combined using boolean operators with the help of the `Conditions` class:

{:.prettyprint .lang-java}
	when(Conditions.or(isNull, isEmpty)).skip().setName(source.getName());
	when(Conditions.and(txIsActive, inScope)).map().setRequest(source.getRequest());

Alternatively, `Condition` implementations can extend `AbstractCondition` which has built in support for combining conditions:

{:.prettyprint .lang-java}
	Condition isNull = new AbstractCondition() {
	  public boolean applies(MappingContext<?, ?> context) {
	    return context.getSource() == null;
	  }
	};
	
	when(isNull.or(isEmpty)).skip().setName(source.getName());
	
## String Mapping

In addition to mapping properies using getters and setters, ModelMapper supports the mapping of source property paths defined as Strings:

{:.prettyprint .lang-java}
    map().getCustomer().address.setStreet(this.<String>source("customer.street_address"));
    
Alternatively the source statement may also be used on the left-hand side of the `map()` statement:

{:.prettyprint .lang-java}
    map(source("customer.street_address")).getCustomer().address.setStreet(null);
    
This API makes it easy to define mappings from source objects that are not JavaBeans.