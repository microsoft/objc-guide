## Convention
A declared property effectively declares accessor methods for a property, and so conventions for naming a declared property are broadly the same as those for naming accessor methods (see Accessor Methods). If the property is expressed as a noun or a verb, the format is:

```obj-c
@property (…) type nounOrVerb;
```

If the name of a declared property is expressed as an adjective, however, the property name omits the “is” prefix but specifies the conventional name for the get accessor, for example:
```obj-c
@property (assign, getter=isEditable) BOOL editable;
```

For Apple guidelines, please refer [Declared Properties and Instance Variables](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)