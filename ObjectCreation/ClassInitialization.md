## Convention
Use the instancetype keyword as the return type of methods that return an instance of the class they are called on (or a subclass of that class). These methods include alloc, init, and class factory methods. Using `instancetype` instead of `id` in appropriate places improves type safety in your Objective-C code.

In Objective-C, object initialization is based on the notion of a designated initializer, an initializer method that is responsible for calling one of its superclass’s initializers and then initializing its own instance variables. The designated initializer pattern helps ensure that inherited initializers properly initialize all instance variables. When using `NS_DESIGNATED_INITIALIZER`, mark "init" `NS_UNAVAILABLE`.

For Apple guidelines, please refer [Object Initialization](https://developer.apple.com/library/archive/releasenotes/ObjectiveC/ModernizationObjC/AdoptingModernObjective-C/AdoptingModernObjective-C.html)


## Examples

```obj-c
/// bad : in header file
@interface Object : NSObject
- (id)initWithObject:(Foo *)foo; // bad : use of id and not declaring designated initializer if object foo is critical
@end

/// good : in header file
@interface Object : NSObject
- (instancetype)initWithObject:(Foo *)foo NS_DESIGNATED_INITIALIZER; // good: explicit designated initializer
- (instancetype)init NS_UNAVAILABLE; // good make default NSObject init unavailable
@end

/// in .m or .mm file
@implementation Object : 
- (instancetype)initWithObject:(Foo *)foo {
    self = [super init];
    if (self != nil)
    {
        // custom initialization with object foo
    }
    return self
}
@end
```