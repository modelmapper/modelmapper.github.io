---
layout: content
menu: user-manual
title: Frequently Asked Questions
---

# FAQ

#### Is ModelMapper threadsafe?

Yes

#### Should I reuse my ModelMapper instance?

Unless you need different mappings between the same types, then it's best to re-use the same `ModelMapper` instance. If you use a dependency injection container, you can accomplish this by configuring `ModelMapper` as a singleton.

#### Should I validate my mappings?

Always write tests to ensure that mapping between two types works as expected. ModelMapper's built-in [validation](/user-manual/validation/) can help with this.