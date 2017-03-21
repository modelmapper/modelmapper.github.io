---
layout: content
menu: user-manual
title: OSGi Integration
---

# OSGi Integration

To integrate ModelMapper with your OSGi application, add the following to the `Import-Package` section of your bundle's configuration:

	org.modelmapper.internal.cglib.proxy,
	org.modelmapper.internal.cglib.core,
	org.modelmapper.internal.cglib.transform
