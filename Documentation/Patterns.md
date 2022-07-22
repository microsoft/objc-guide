# Documentation Patterns

## Convention

All functions, interfaces, protocols, properties, and methods in headers should be documented with the [Doxygen](http://www.doxygen.org) style comments. All parameters and return values should be fully described. It is strongly recommended to document functions, interfaces, protocols, properties, and methods declared in implementation files as well. Code should be documented only once where it is declared. Documentation should be commented with three forward slashes (`///`).

## Rationale

Code documentation helps define an explicit contract for an API, which can improve testability and maintainability of code. In cases where documentation seems trivial, the cost of adding it is low and the value of adding it is high for consistency and maintainability.

Doxygen documentation is an industry standard documentation format. Additionally it is a format that Xcode, the de facto Objective-C code editor, auto generates and parses for presentation in the UI.

Place documentation only on an entity's declaration. If it has no declaration (e.g. a static function or method in an `@implementation`), then place the documentation on the definition. One canonical location for documentation improves maintainability, as copying the documentation from the declaration to definition produces duplicate and potentially conflicting documentation.

Using the three forward slash style of documentation distinguishes documentation comments from other non-documentation comments. This matches the [Swift recommended documentation style](https://swift.org/documentation/api-design-guidelines/), enabling better documentation block consistency across languages.

Xcode can automatically add these for you. Put your cursor in the method/enum/etc. and then Editor menu -> Structure submenu -> add documentation or "Option + command + forward slash (“/“)"

## Examples

### Bad

```obj-c
@class XYZPerson;

NSString *XYZPersonGetInitials(XYZPerson *person); // bad: return value ambiguous regarding locale awareness

@interface XYZPerson : NSObject
@property (nonatomic, copy) NSString *firstName;
@property (nonatomic, copy) NSString *lastName;

- (NSString *)fullName; // bad: return value ambiguous regarding locale awareness
- (NSString *)truncatedName:(NSUInteger)numberOfCharacters; // bad: unclear what name is being truncated and in what way

@end
```

### Good

```obj-c
@class XYZPerson;

/// Gets the initials of a person for an abbreviated representation in the UI.
///
/// @param person The person from which to return the initials.
/// @return The first character of the \c firstName and \c lastName in a locale aware order.
NSString *XYZPersonGetInitials(XYZPerson *person);

/// Uniquely identifies a person and provides access to their information.
@interface XYZPerson : NSObject

/// The first or given name of the person.
@property (nonatomic, copy) NSString *firstName;

/// The last or family name of the person.
@property (nonatomic, copy) NSString *lastName;

/// The full name of the person.
///
/// @return A locale-aware concatenation of the \c firstName and \c lastName.
- (NSString *)fullName;

/// Truncates the person's full name to a given number of characters.
///
/// @param numberOfCharacters The number of characters to which the name should be truncated.
/// @return The \c fullName of this person truncated to \p numberOfCharacters characters using end truncation.
- (NSString *)truncatedName:(NSUInteger)numberOfCharacters;

@end
```
