# Alternative Designs

## Convention

Use C-style functions instead of categories to avoid name collisions and reduce Objective-C runtime overhead, unless subclasses are expected to override the category method.

## Rationale

Methods in categories are added to the class on which the category is implemented, replacing any existing methods. If the same method is implemented in multiple categories, which one is used is undefined. This is called a [name clash](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW4), which can result even when following best practices for [category naming](Naming.md). The difference between implementing a C-style function and Objective-C category method is trivial, but the static linking characteristics of C-style avoid any potential undefined behavior.

Separately, C-style functions avoid adding to the Objective-C [binding time](https://useyourloaf.com/blog/slow-app-startup-times/) when dyld loads the application executable.

## Examples
NSString+XYZExcitementAdditions.h
```Objective-C
@interface NSString (XYZExcitementAdditions)
- (NSString *)xyz_stringWithExcitement; // bad: potential name collisions
@end

NSString *XYZStringWithExcitement(NSString *string); // good: symbol safety
```
NSString+XYZExcitementAdditions.m
```Objective-C
#import "NSString+XYZExcitementAdditions.h"

@implementation NSString (XYZExcitementAdditions)
- (NSString *)xyz_stringWithExcitement { // bad: potential name collisions
    return [self stringByAppendingString:@"!"];
}
@end

NSString *XYZStringWithExcitement(NSString *string) { // good: symbol safety
    return [string stringByAppendingString:@"!"];
}
```
