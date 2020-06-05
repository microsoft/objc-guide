# Import and Include

`#import` and `#include` are preprocessor directives for bringing in the contents of a header to a file. `#include` is replaced by the contents of the header directly, while `#import` is only replaced by the contents of the header the first time that header is imported.

## Convention

Objective-C headers should be brought in via the `#import` preprocessor directive. Usage of `#include` should be reserved for C and C++ headers which, by convention, have [`#include` guards](https://en.wikipedia.org/wiki/Include_guard). C and C++ headers may affect or rely on the preprocessor state, such as the use of `#pragma push_macro`, `#pragma pop_macro`, and [x macros](https://en.wikipedia.org/wiki/X_Macro), which are not patterns conventionally used in Objective-C. Avoid `#pragma once` in Objective-C code as it is a non-standard extension and redundant with `#import`.

## Rationale

Using `#import` eliminates the need for `#include` guards, which are not used by convention in Objective-C headers. `#include` is the conventional directive for C and C++ headers and should be used so the headers are preprocessed as intended by the author.

## Examples

```obj-c
#include "XYZObjcClass.h" // bad: possible duplicate inclusion of this header
#import "XYZObjcClass.h" // good: clang determines if this header has been included already and avoids duplicate inclusion
#import "XYZPreprocessorModifier.h" // bad: may not be inlined here to affect the preprocessor state
#include "XYZPreprocessorModifier.h" // good: preprocessor modifications reliably copied where needed
#import "XyzCppHeader.h" // bad: using Objective-C convention for C and C++ headers
#include "XyzCppHeader.h" // good: use proper convention for C and C++ headers
```