# Naming

## Convention

[Class names](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html#//apple_ref/doc/uid/20001281-1002242-BBCIJGDB) should contain a noun that signifies what the class represents or what functionality it provides. The class name should have an appropriate three letter [prefix](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html#//apple_ref/doc/uid/20001281-1002226-BBCJECED).

## Rationale

Using nouns helps identify the specific information and functionality provided by a given class.

Class names in Objective-C share a process global namepsace. Three letter prefixes are a mechanism for namepsacing classes to reduce the chance of name collisions, resulting in undefined behavior. Do not use two letter prefixes, as they are [reserved for system use](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Conventions/Conventions.html#//apple_ref/doc/uid/TP40011210-CH10-SW2).

## Examples

```Objective-C
@interface Object : NSObject // bad: no three letter prefix
@end

@interface XYZObject : NSObject // good: XYZ three letter prefix
@nd

@interface XYZColorful : NSObject // bad: colorful is not a noun
@end

@interface XYZColorfulPalette : NSObject // good: palette is a noun
@end
```