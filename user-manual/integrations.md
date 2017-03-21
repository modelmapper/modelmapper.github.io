---
layout: content
menu: user-manual
title: Integrations
---

# Integrations

ModelMapper was designed to be easily extensible via the [API](/user-manual/api-overview/) and [SPI](/user-manual/spi-overview/) and to integrate well with existing technologies. Several integrations are described below.

## General

* [OSGi](/user-manual/osgi-integration/)

## Provider Integrations

[Providers](/user-manual/providers) allow you to provide you own instance of destination objects prior to mapping. ModelMapper has several 3rd party integrations that allow for external libraries to provide destination objects:

* [Spring](/user-manual/spring-integration)
* [Guice](/user-manual/guice-integration)
* [Dagger](/user-manual/dagger-integration)

## Value Reader Integrations

Value Readers allow you to read and map values from different types of source object, aside from typical JavaBeans. ModelMapper has several 3rd party integrations that allow for the mapping of values from various types of source objects:

* [jOOQ](/user-manual/jooq-integration)
* [Jackson](/user-manual/jackson-integration)
* [Gson](/user-manual/gson-integration)

## Native Integrations

Certain libraries natively integrate with ModelMapper without any additional depedencies. These include:

* [JDBI](/user-manual/jdbi-integration)