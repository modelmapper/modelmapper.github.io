---
layout: content
menu: user-manual
title: Validation
---

# Validation

While ModelMapper will attempt to match every destination property to a source property, there will occasionally be destination properties that it cannot find matches for. To verify that all destination properties are matched, call the `validate` method:

```
modelMapper.validate();
```

If validation fails, a `ValidationException` will be thrown with a helpful message describing any destination properties that are unmatched.

### Handling Validation Errors

To resolve a validation error resulting from an unmatched destination, you have two options:

  * Create a [mapping](/user-manual/property-mapping) that maps or ignores any unmatched properties
  * Adjust [configuration](/user-manual/configuration) to try and match any unmatched properties

Alternatively, it may be acceptable to do nothing, in which case any unmatched destination properties will simply be skipped during the mapping process.