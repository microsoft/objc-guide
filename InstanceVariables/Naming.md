# Instance Variable Naming

## Convention

Instance variable names begin with an underscore (`_`) followed by a [camel case](https://en.wikipedia.org/wiki/Camel_case) alphanumeric string. The first character following the underscore must be a lowercase letter; numerals are not permitted.

## Rationale

The underscore prefix is recommended in the [Naming Properties and Data Types](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html) section of Apple's [Coding Guidelines for Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html). For implicit (default) property synthesis, clang will [prefix an underscore](https://reviews.llvm.org/source/clang/browse/cfe/trunk/lib/AST/DeclObjC.cpp;326860$222) to the property name to name the corresponding instance variable. The underscore prefix maintains consistency with Apple's recommendation and the compiler's behavior.

Secondarily, the name prefixed with an underscore is the preferred patten for accessing instance variables via [Key Value Coding](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html) as described in step 5 of [Accessor Search Patterns: Search Pattern for the Basic Getter](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/SearchImplementation.html#//apple_ref/doc/uid/20000955-138234) of the Key-Value Coding Programming Guide. While production code generally shouldn't depend on accessing instance variables via key-value coding, the KVC family of methods (e.g. `-[NSObject valueForKey:]`) may be useful for examining object state in the debugger.

A lowercase letter after the underscore followed by additional alphanumeric characters in camel case corresponds with the [property naming convention](../Properties/Naming.md), allowing the instance variable to be made available in the future via a property by the same name (after dropping the underscore prefix). Properties (and methods) cannot begin with a number, hence the prohibition on using a numeral after the underscore despite being a valid identifier.

## Examples

```obj-c
@implementation MyObject {
@private
	NSString *_name; // good
	id _observer;    // good
	NSUInteger age_; // bad: no underscore prefix, non-alphanumerics in name
	int _;           // bad: no characters following underscore
	BOOL _1stRun;    // bad: character following underscore is not a lower case letter
	BOOL enabled;    // bad: no underscore prefix
	BOOL m_visible;  // bad: no underscore prefix, non-alphanumerics in name
}
@end
```