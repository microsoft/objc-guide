# Header Factoring

## Convention

Headers can be factored through the use of modifiers, with the name of the class followed by "+" and the modifier.

For a header that provides methods explicitly for subclassing purposes, use the "Protected" modifier. By convention, the methods in this header should only be overridden and never called directly. 

For a header that provides methods only to be used internally, but not publicly consumed outside of the module, use the "Package" modifier. By convention, this header must not be included or made available outside of its module.

For a header that provides access to internal implementation details for unit testing, use the "Private" modifier. By convention this header must not be included outside of unit tests and the implementation file of the class itself.

### Include chain for header modifiers

 * Protected
   * Protected header `#import`s base header
   * Implementation file `#import`s protected header
 * Package
   * Package header `#import`s base header
   * Implementation file `#import`s package header
 * Private
   * Private header `#import`s base header
   * Implementation file `#import`s private header
 * Protected and Package
   * Protected header `#import`s base header
   * Package header `#import`s base header
   * Implementation file `#import`s both package and protected headers
 * Protected and Private
   * Protected header `#import`s base header
   * Private header `#import`s protected header
   * Implementation file `#import`s private header
 * Package and Private
   * Package header `#import`s base header
   * Private header `#import`s package header
   * Implementation file `#import`s private header
 * Protected, Package, and Private
   * Protected header `#import`s base header
   * Package header `#import`s base header
   * Private header `#import`s both package and protected headers
   * Implementation file `#import`s private header

## Rationale

Factoring headers through the use of modifiers allows for better code organization, better unit testing, and a minimal public API.

Protected headers allow for subclassing of a class without exposing unnecessary implementation details.

Package headers enable internal classes to enjoy improved access for more complex scenarios while keeping the public interface as small and simple as possible.

Private headers allow unit testing of internal methods while establishing the convention they should not be `#import`ed outside of unit tests or the implementation.

## Examples

```obj-c
// XYZObject.h
@interface XYZObject : NSObject
// ...
@end

// XYZObject+Protected.h
@interface XYZObject ()
// ...
@end

// XYZObject+Package.h
void XYZObjectPerformPackageFunction(XYZObject *object);
@interface XYZObject ()
// ...
@end

// XYZObject+Private.h
void XYZObjectPerformInternalFunction(XYZObject *object);
@interface XYZObject ()
// ...
@end
```