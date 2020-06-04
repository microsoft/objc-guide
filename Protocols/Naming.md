# Protocol Naming

## Convention

Protocols primarily provide a group of behaviors unrelated to a specific class, and should be named to describe the group of behaviors. Typically protocol names are in the gerund form, ending in "ing".

If a protocol will be used in a [delegate](DelegatesAndDataSources.md) or [data source](DelegatesAndDataSources.md) pattern, append the word `Delegate` or `DataSource` to the class name of the object type that calls objects conforming to the protocol.

## Rationale

The [Code Naming Basics](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html#//apple_ref/doc/uid/20001281-BBCHBFAH) section of Apple's [Coding Guidelines for Cocoa](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html) describes how to name protocols. In general, following Apple's guidelines is recommended.

## Examples

```Objective-C
// Group of behaviors unrelated to a class
@protocol NSLock; // bad: sounds like a class name
@protocol NSLocking; // good

@protocol XYZViewHidingCoordinator; // bad: sounds like a class name
@protocol XYZViewHiding; // good

@protocol XYZHeaderProvider; // bad: sounds like a class name
@protocol XYZHeading; // good

// Delegate and DataSource patterns
@protocol UITableViewCallbackHandler; // bad: delegate patterns should append Delegate
@protocol UITableViewDelegate; // good: follows delegate pattern and appends Delegate to UITableView

@protocol UITableViewDataProvider; // bad: follows data source pattern but doesn't append DataSource
@protocol UITableViewDataSource; // good: follows data source pattern and appends DataSource
```