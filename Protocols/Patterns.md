# Protocol Patterns

This page describes general patterns for protocols. For some specialized protocol patterns, see [Delegates and Data Sources](DelegatesAndDataSources.md).

## Convention - Protocol Contents

The interface defined by a protocol should contain related methods focused around a single concept. The protocol should be conformed to by a set of objects that are inherently similar but not subclasses or parent classes of each other.

## Rationale

Protocols should be scoped to provide similar behavior in order to maximize the reusability of the protocol. Conflating multiple concepts into a single protocol may work initially but is harder to maintain and refactor.

## Convention - Unit Testing

To unit test code that relies on system APIs (e.g. `NSFileManager`), copy the portions of the interface used into a protocol, declare the original class conforms to the protocol, add an argument to accept an object conforming to the protocol if necessary, and implement the interface in the unit test code in a way that's amenable to testing.

## Rationale

Unit test code can result in better design and more maintainable code. Using protocols to mock system APIs allows more code to be unit testable. See Apple's [Testing Tips and Tricks WWDC Presentation](https://developer.apple.com/videos/play/wwdc2018/417/) for more details.

## Examples

### Separation of concerns within protocols
#### Bad
```Objective-C
// NSAppearanceCustomizationAndMouseHandling.h
@protocol NSAppearanceCustomizationAndMouseHandling <NSObject> // bad: protocol conflates multiple concepts
@required

@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;
- (void)mouseDown:(NSEvent *)theEvent;

@end

// NSView.h
@interface NSView : NSResponder <NSAppearanceCustomizationAndMouseHandling>
// ...

@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;
- (void)mouseDown:(NSEvent *)theEvent { /* ... */ }

// ...
@end

// NSApplication.h
@interface NSApplication : NSResponder <NSAppearanceCustomizationAndMouseHandling> 
// ...

@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;
- (void)mouseDown:(NSEvent *)theEvent { // bad: NSApplication must implement this to conform to the protocol but doesn't handle mouse events
    // no-op, NSApplication doesn't handle mouse events
 } 

// ...
@end
```

#### Good
```Objective-C
// NSAppearanceCustomization.h
@protocol NSAppearanceCustomization <NSObject> // good: protocol behaviors are related
@required

@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;

@end

// NSMouseEvents.h
@protocol NSMouseEvents <NSObject> // good: no unrelated behavior
@optional
- (void)mouseDown:(NSEvent *)theEvent;
@end


// NSApplication.h
@interface NSApplication : NSResponder <NSAppearanceCustomization> // good: related classes that aren't direct subclasses can conform to the protocol 
// ...
@end

// NSView.h
@interface NSView : NSResponder <NSAppearanceCustomization, NSMouseEvents> 
// ...
@end
```

### Using protocols reduces code duplication 
#### Bad
```Objective-C
// NSWindow.h
@interface NSWindow : NSResponder

@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;

// ...

@end

// NSView.h
@interface NSView : NSResponder <NSAppearanceCustomization, NSMouseEvents> 

// bad: the same appearance concept is defined twice in two spots
@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;

// ...

@end

// XYZSomeOtherCode.m
// ...
NSAppearance *appearance = // ...
id someObject = // ...
if ([someObject isKindOfClass:[NSWindow class]]) { // bad: duplication of code since there is duplication of method definitions
    [(NSWindow *)someObject setAppearance:appearance];
} else if ([someObject isKindOfClass:[NSView class]]) {
    [(NSView *)someObject setAppearance:appearance];
} // else if (...) {
// ...
```

#### Good
```Objective-C
// NSAppearanceCustomization.h
@protocol NSAppearanceCustomization <NSObject>
@required

@property (nullable, strong) NSAppearance *appearance;
@property (readonly, strong) NSAppearance *effectiveAppearance;

@end

// NSWindow.h
@interface NSWindow : NSResponder <NSAppearanceCustomization>
// ...
@end

// NSView.h
@interface NSView : NSResponder <NSAppearanceCustomization>
// ...
@end

// XYZSomeOtherCode.m
// ...
NSAppearance *appearance = // ...
id someObject = // ...
if ([someObject conformsToProtocol:@protocol(NSAppearanceCustomization)]) { // good: avoid duplication of code
    [(id<NSAppearanceCustomization>)someObject setAppearance:appearance];
}
// ...
```

### Unit Testing
#### Bad
```Objective-C
// bad: this method directly modifies the file system making it hard to unit test
- (void)deleteItemFromFileSystemWithPath:(NSString *)path { 
    NSFileManager *defaultManager = [NSFileManager defaultManager];
    if ([defaultManager removeItemAtPath:path error:nil]) {
        // do something
    } else {
        // handle the failure
    }
}
```
#### Good
```Objective-C
@protocol XYZItemRemoving <NSObject> // good: the relevant file manager API has been extracted
- (BOOL)removeItemAtPath:(NSString *)path error:(NSError *_Nullable *)error;
@end

@interface NSFileManager () <XYZItemRemoving>
@end

- (void)deleteItemFromFileSystemWithPath:(NSString *)path {
    [self deleteItemFromFileSystemWithPath:path itemRemover:[NSFileManager defaultManager]];
}

// good: the implementation of this method now takes in a generic itemRemover
- (void)deleteItemFromFileSystemWithPath:(NSString *)path itemRemover:(id<XYZItemRemoving>)itemRemover { 
    if ([itemRemover removeItemAtPath:path error:nil]) {
        // do something
    } else {
        // handle the failure
    }
}

// XYZItemDeletionUnitTests.m

@interface XYZMockItemRemover: NSObject <XYZItemRemoving>
@property (nonatomic) BOOL succeedRemovingItem;
@end

@implementation XYZMockItemRemover
- (BOOL)removeItemAtPath:(NSString *)path error:(NSError *_Nullable *)error {
    return succeedRemovingItem;
}

// good: testing the success case is simple and doesn't rely on file system state
- (void)testItemDeletionSuccess {
    // ...
    XYZMockItemRemover *itemRemover = [XYZMockItemRemover new];
    [itemRemover setSucceedRemovingItem:YES];
    [object deleteItemFromFileSystemWithPath:@"SamplePath" itemRemover:itemRemover];
    // test that results of success were handled properly
}

// good: testing the failure case is simple and doesn't rely on file system state
- (void)testItemDeletionFailure {
    // ...
    XYZMockItemRemover *itemRemover = [XYZMockItemRemover new];
    [itemRemover setSucceedRemovingItem:NO];
    [object deleteItemFromFileSystemWithPath:@"SamplePath" itemRemover:itemRemover];
    // test that results of failure were handled properly
}

@end

```