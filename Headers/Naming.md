# Naming

## Convention

[Header file names](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html#//apple_ref/doc/uid/20001281-1002806-BBCFIFEH) should match the [class name](../Classes/Naming.md), [protocol name](../Protocols/Naming.md), or [category name](../Categories/Naming.md) defined in the file. Header files should contain a single class definition. If a header contians a protocols or categories in addition to the class name, name the header after the class name.

## Rationale

Header file names should describe their contents. By using the same name, the header file and the class, protocol, or category declaration are inherently linked. Limiting headers to a single class helps keep APIs clear, concise, and easily searchable.

## Examples

### Single Class

```obj-c
// Object.h 
// bad: header name doesn't match class name

// XYZObject.h
// good: header name matches class name
@interface XYZObject : NSObject
@end
```

### Multiple Classes

```obj-c
// bad: multiple classes defined in the same file
@interface XYZObject : NSObject
@end

@interface XYZRelatedObject : NSObject
@end
```

### Protocol

```obj-c
// XYZViewProtocols.h
// bad: doesn't match the protocol name

// XYZViewHidingProtocols.h
// bad: doesn't match the protocol name

// XYZViewHiding.h
// good: matches the protocol name

@protocol XYZViewHiding;
```

### Class and Related Protocol

```obj-c
// XYZObjectAndProtocols.h
// bad: name doesn't match the class name

// XYZObjectDelegate.h
// bad: name doesn't match the class name

// XYZObject.h
// good: name matches the class name

@protocol XYZObjectDelegate
// ...
@end

@interface XYZObject : NSObject
@property (nonatomic, weak) id<XYZObjectDelegate>delegate;
@end
```

### Categories and extensions

See [category naming](../Categories/Naming.md) for examples of naming headers containing categories and other extensions.
