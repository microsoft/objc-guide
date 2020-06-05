# Implicit Boolean Conversion

## Convention

Avoid implicit conversions to boolean.

## Rationale

[Implicit Boolean Conversion](https://clang.llvm.org/extra/clang-tidy/checks/readability-implicit-bool-conversion.html) can reduce code readability and hide bugs, especially when refactoring existing code. By making an explicit boolean comparison between a non-boolean type and a value of the same type, the context becomes less ambiguous and readers will not need to refer back to the type of the variable involved. Also note that Swift requires explicit conversions to a boolean type, so explicit checks in Objective-C increase the similarity in style between the two languages. As most types can be implicitly converted to boolean, refactoring code that uses implicit conversion may result in unexpected behavior. Explicit conversion clarifies intent, which can aid in improving the correctness of a refactor.

## Examples

```obj-c
NSObject *object = [[NSObject alloc] init];
CGFloat floatValue = 9.0;
BOOL booleanFlag = YES;

if (object) { // bad: implicitly converting pointer to boolean
    // ...
}

if (object != nil) { // good: no implicit conversion
    // ...
}

if (floatValue) { // bad: implicitly converting CGFloat to boolean
    // ...
}

if (floatValue != 0.0) { // good: no implicit conversion
    // ...
}

if (booleanFlag) { // good: no implicit conversion
    // ...
}

if (booleanFlag == YES) { // unnecessary: no implicit conversion, but overly verbose and unnecessary
    // ...
}
```