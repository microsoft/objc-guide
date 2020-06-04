# Generics

Lightweight generics were added to Objective-C in [Xcode 7](https://developer.apple.com/library/archive/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html#//apple_ref/doc/uid/TP40001051-CH1-SW326), which allow specification of type information for collection classes. While generic types are [erased](https://en.wikipedia.org/wiki/Type_erasure) at runtime, they provide more information to both the developer and compiler.

## Convention

Use generics when declaring variables of types that support them. Add generic type constraints to custom object types if relevant.

## Rationale

Generics provide compile-time type checking to verify that collections, such as `NSSet`, `NSArray`, or `NSDictionary`, contain the correct object type when the generic constraint is present. They clarify and document assumptions about the contents of collections. Generics are a helpful way to write self-documenting code and improve code maintainability, as it's easier to read and understand the intent of collections that explicitly declare the contained type, which can be verified at compile time. Proper use of generics also improves integration with Swift.

## Examples

### No Generics

Maintaining type consistency is more difficult without generics. Here is a for-each loop, which mistakenly declares the type of each item in the array as `NSString *`. `NSString` also provides an implementation of `doubleValue`, so the compiler does not emit any errors because there are no type checking errors after the implicit `id` to `NSString *` conversion.

```Objective-C
- (NSNumber *)average:(NSArray *)ages {
    double average = 0; 
    NSUInteger count = [ages count];
    if (count > 0) {
        double total = 0;
        for (NSString *age in ages) { // This is not caught by the compiler
            NSAssert([age isKindOfClass:[NSNumber class]], @"Expect NSNumbers in the input array");
            // As -[NSString doubleValue] also exists, this does not cause a compiler error
            total += [age doubleValue];
        }
        average = total / count;
    }
    
    return @(average);
}
```

### Generics 

However, with generics, this code will fail to compile due to the type mismatch between generic type provided for the array (`NSNumber *`) and `NSString *` type used in the for-each loop.

```Objective-C
- (NSNumber *)average:(NSArray<NSNumber *> *)ages {
    double average = 0; 
    NSUInteger count = [ages count];
    if (count > 0) {
        double total = 0;
        for (NSString *age in ages) { // compile error as objects in the ages array are NSNumbers
            NSAssert([age isKindOfClass:[NSNumber class]], @"Expect NSNumbers in the input array");
            total += [(NSNumber *)age doubleValue];
        }
        average = total / count;
    }
    
    return @(average);
}
```