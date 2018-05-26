---
layout: content
menu: user-manual
title: Spring Boot Devtools Integration
---

# Spring Boot Devtools Integration

To prevent Spring Boot Devtools from suffering class loading issue, you must add modelmapper to the restart list in the `spring-devtools.properties` to trigger reload modelmapper.jar.

	restart.include.modelmapper=/modelmapper-(.*).jar
