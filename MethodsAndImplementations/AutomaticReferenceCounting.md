# Automatic Reference Counting

[Automatic Reference Counting](https://clang.llvm.org/docs/AutomaticReferenceCounting.html) (ARC) is a memory management option for Objective-C provided by the [Clang](https://clang.llvm.org) compiler. When compiling Objective-C code with ARC enabled, the compiler will effectively retain, release, or autorelease where appropriate to ensure the object's lifetime extends through, at least, its last use.

## Convention

All Objective-C and Objective-C++ code should be compiled with ARC enabled.

## Rationale

ARC emits deterministic memory management code, which leads to fewer leaks or over-releases when compared with manual retain/release. ARC can provide retain/release/autorelease optimizations not available to manually maintained code, improving overall performance. Finally, ARC simplifies writing new code and decreases the burden of maintaining code over time.

## Examples
Analyzing object lifetime can be complex. With ARC, the compiler ensures correctness of an object's lifetime, simplifying the maintenance burden. In the following examples, a developer adds a call to scan a newly introduced value with an existing scanner. The update to the non-ARC code yields undefined behavior, while the update to the ARC code behaves correctly.

### Code compiled without ARC (`-fno-objc-arc`)

```Objective-C
NSScanner *scanner = [[NSScanner alloc] initWithString:string];
[scanner scanInt:&value1];
[scanner release]; // bad: premature release of object
...
[scanner scanInt:&value2]; // undefined behavior; potential crash
```

### Code compiled with ARC (`-f-objc-arc`)

```Objective-C
NSScanner *scanner = [[NSScanner alloc] initWithString:string];
[scanner scanInt:&value1];
...
// ARC guarantees the object is retained for as long as it's used
[scanner scanInt:&value2];
```
