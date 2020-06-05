# Early Returns

## Convention

Each method that returns a value should only return once within the method body. That return should be the last line of code in the method body.

> This convention, while not necessarily specific to Objective-C, has helped us improve the factoring and organization of our code. Although adopted to different degrees by different teams, we felt this was worth including to document rationale for teams that find the convention useful.

## Rationale

Methods with multiple return statements may become hard to maintain over time. When adding new code to an existing method with multiple returns, it can be difficult to reason about code flow and ensure the new code is always executed in the correct circumstances. For example, adding post-processing to a value before returning it may require substantially refactoring the implementation to ensure all return paths fall through to execute the new code. This can be particularly problematic when adding calls that need to be paired, such as [Core Foundation](https://developer.apple.com/documentation/corefoundation?language=objc) `Create` and `Release` calls, leading to code duplication and thus lower maintainability.

## Examples

### Early Returns

```obj-c
- (CFIndex)indexOfFirstTokenFromString:(CFStringRef)string withLength:(NSInteger)length {
    CFLocaleRef locale = CFLocaleCopyCurrent();
	CFStringTokenizerRef tokenizer = CFStringTokenizerCreate(kCFAllocatorDefault,
		                                                     string,
		                                                     CFRangeMake(0, CFStringGetLength(string)),
		                                                     kCFStringTokenizerUnitWord,
		                                                     locale);
    
    while (CFStringTokenizerAdvanceToNextToken() == kCFStringTokenizerTokenNormal) {
        CFRange range = CFStringTokenizerGetCurrentTokenRange(tokenizer);
        if (range.length == length) {
            // Cleanup and returning needs to be handled multiple times
            CFRelease(tokenizer);
            CFRelease(locale);
            return range.location;
        }
    }
    
    // Cleanup and returning needs to be handled multiple times
    CFRelease(tokenizer);
    CFRelease(locale);
    return NSNotFound;
}
```

### No Early Returns

```obj-c
- (CFIndex)indexOfFirstTokenFromString:(CFStringRef)string withLength:(NSInteger)length {
    CFIndex index = NSNotFound;
    CFLocaleRef locale = CFLocaleCopyCurrent();
	CFStringTokenizerRef tokenizer = CFStringTokenizerCreate(kCFAllocatorDefault,
		                                                     string,
		                                                     CFRangeMake(0, CFStringGetLength(string)),
		                                                     kCFStringTokenizerUnitWord,
		                                                     locale);
    
    while (CFStringTokenizerAdvanceToNextToken() == kCFStringTokenizerTokenNormal) {
        CFRange range = CFStringTokenizerGetCurrentTokenRange(tokenizer);
        if (range.length == length) {
            index = range.location;
            break;
        }
    }

    // Cleanup and returning can be handled once
    CFRelease(tokenizer);
    CFRelease(locale);
    return index;
}
```