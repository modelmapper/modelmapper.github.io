# Downloads

## ModelMapper Core

* [modelmapper-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/modelmapper/{{site.version}}/modelmapper-{{site.version}}.jar)

### Core Maven Setup

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper</groupId>
	  <artifactId>modelmapper</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>

## ModelMapper Integrations

#### Spring

* [modelmapper-spring-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/extensions/modelmapper-spring/{{site.version}}/modelmapper-spring-{{site.version}}.jar)

#### Guice

* [modelmapper-guice-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/extensions/modelmapper-guice/{{site.version}}/modelmapper-guice-{{site.version}}.jar)

#### Dagger

* [modelmapper-dagger-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/extensions/modelmapper-dagger/{{site.version}}/modelmapper-dagger-{{site.version}}.jar)

#### jOOQ

* [modelmapper-jooq-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/extensions/modelmapper-jooq/{{site.version}}/modelmapper-jooq-{{site.version}}.jar)

#### Jackson

* [modelmapper-jackson-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/extensions/modelmapper-jackson/{{site.version}}/modelmapper-jackson-{{site.version}}.jar)

#### Gson

* [modelmapper-gson-{{site.version}}.jar](http://search.maven.org/remotecontent?filepath=org/modelmapper/extensions/modelmapper-gson/{{site.version}}/modelmapper-gson-{{site.version}}.jar)

### Integration Maven Setup

{:.prettyprint .lang-xml}
	<dependency>
	  <groupId>org.modelmapper.extensions</groupId>
	  <artifactId>modelmapper-[integration-name]</artifactId>
	  <version>{{ site.version }}</version>
	</dependency>


