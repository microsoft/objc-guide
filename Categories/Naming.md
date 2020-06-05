# Naming

## Convention

Category names should begin with the project's three letter prefix followed by a descriptive name of functionality the extension provides. Avoid generic category names such as "Extensions, "Utilities", or "Additions".

Files extending the functionality of existing classes, whether through categories or [alternative designs](AlternativeDesigns.md), should be named with the name of the class being extended followed by "+", followed by the name of the category.

Methods in categories, including those declared as properties, should be prefixed with the three letter prefix and an underscore.

## Rationale

Categories can modify existing classes from other frameworks and libraries. Using the three letter prefix of the framework or library defining the category helps provide a unique category name and quickly indicates which library added the functionality when viewing the header to symbols in a backtrace. 

Avoid generic category names for better code organization and to improve discoverability from header file names. Good names prevent a category from becoming a dumping of unrelated functionality.

Methods must be prefixed in order to help avoid potential [naming clashes](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW4).

See [Apple's guidelines](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Category.html#//apple_ref/doc/uid/TP40008195-CH5-SW2) for more detail and rationale on declaring categories.

## Examples

```obj-c
// StringUtilities.h 
// bad: doesn't use the full name of the original class, doesn't have a "+", no three letter prefix, and an overly-generic category name.

// NSString+XYZExcitementAdditions.h 
// good: follows the pattern of (original class)+(descriptive category name with prefix).h

@interface NSString (Utilities) // bad: no three-letter prefix present, not a descriptive category name
- (NSString *)stringWithExcitement; // bad: three letter prefix reduces likelihood of name collision
@property (nonatomic, copy) NSNumber *excitementLevel; // bad: no prefix to property name
@end

@interface NSString (XYZExcitementAdditions) // good: three-letter prefix present, descriptive category name
- (NSString *)xyz_stringWithExcitement; // good: potential name collisions
@property (nonatomic, copy, setter=xyz_setExcitementLevel:) NSNumber *xyz_excitementLevel; // good: property name prefixed, setter name maintains prefix
@end

```
