# Instance Variable Patterns

## Convention

Instance variables should be private internal implementation details of a class and defined in the class `@implementation` in descending binary size order. If an [instance variable is used to back a property](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW6), the instance variable should only be accessed in an object initializer, in the implementation of `dealloc`, and in the implementation of custom accessor methods for the property.

## Rationale

Instance variables are private implementation details. Use a property to expose information, which establishes a contract of "what" and isolates the "how" as an internal implementation detail, which reserves the ability to change the means in which the information is produced in the future without adversely affecting dependent code. Defining instance variables in the `@implementation` keeps the public interface free from implementation details and may reduce the number of `#import`s required in the header for types that are not used in the public interface, improving compilation throughput.

Ordering instance variables by descending binary size helps ensure proper [structure packing](http://www.catb.org/esr/structure-packing/) reducing [data structure bloat](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/64bitPorting/PerformanceOptimization/PerformanceOptimization.html).

When instance variables back properties, the implementation of the accessor methods may be overridden by subclasses. In this case, the instance variable may no longer be a reliable representation of the property. Therefore, always use the property to retrieve the value to ensure consistent behavior; subtle bugs may arise when part of an implementation refers to the instance variable value and another part refers to a differing property value. Similarly, when setting the default value of a property in an `init` method, set the instance variable directly to avoid calling a potential subclass's setter method.

## Examples

### Bad

```obj-c
// XYZObject.h
@interface XYZObject : NSObject { // bad: instance variables should be declared from the `@implementation`
@public // bad: instance variables should be private
    // bad: instance variables not in descending binary size order
    BOOL _firstRun;
    NSUInteger _age;
    NSString *_name;
}
@property (nonatomic) NSUInteger age;
- (NSString *)ageDescription;
@end

// XYZObject.m
@implementation

- (instancetype)initWithAge:(NSUInteger)age {
    self = [super init];
    if (self) {
        [self setAge:age]; // bad: referencing a property in an initializer/dealloc
    }
}

// ...

- (NSString *)ageDescription {
    return [NSString stringWithFormat:@"My age is %d", _age]; // bad: directly accessing instance variable backed by a property
}

// ...

@end
```

### Good

```obj-c
// XYZObject.h
@interface XYZObject : NSObject
@property (nonatomic) NSUInteger age;
- (NSString *)ageDescription;
@end

// XYZObject.m
@implementation { // good: instance variables declared from the `@implementation`
@private // good: instance variables are private
    // good: instance variables in descending binary size order
    NSString *_name;
    NSUInteger _age;
    BOOL _firstRun;
}

- (instancetype)initWithAge:(NSUInteger)age {
    self = [super init];
    if (self) {
        _age = age; // good: directly manipulating ivar in initializer
    }
}

// ...

- (NSString *)ageDescription {
    return [NSString stringWithFormat:@"My age is %d", [self age]]; // good: accessing age value property
}

// ...

@end
```
