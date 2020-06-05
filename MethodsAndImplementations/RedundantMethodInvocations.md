# Redundant Method Invocations

## Convention

When a value is used repeatedly, save it to a local variable on the stack instead of retrieving it through redundant method invocations at each use site.

## Rationale

[Copied and pasted](https://en.wikipedia.org/wiki/Copy_and_paste_programming) code is generally considered an anti-pattern and redundant method invocations fall in this category. Unnecessary, repeated invocations of the same method increase binary size and run time, so judicious use of method invocations can provide two important performance wins. Copied and pasted code increases the maintenance burden, creating more work when refactoring an interface.

The caller of a method (including property accessors) shouldn't make assumptions about its performance, as its implementation is subject to change over time. The implementation may potentially be a trivial accessor, a complex computation, or a proxy to another object.

We experienced a real world performance regression where a property was repeatedly accessed in a loop when the receiver became an `NSProxy` subclass in order to work around a system bug. By accessing the property once before the loop began, the runtime of the loop decreased from over a second to negligible. While this scenario is perhaps a rare occurrence, we've found reducing run time (and binary size) has always served us well.

## Examples

```obj-c

@implementation MyView

- (void)updateButtonAppearance {
	// bad: unnecessarily call several methods multiple times ([self anObject], [anObject button], and [button titleLabel])
	[[[[self anObject] button] titleLabel] setFont:[UIFont systemFontOfSize:12]];
	[[[[self anObject] button] titleLabel] setLineBreakMode:NSLineBreakByTruncatingTail];
	
	// good: call [[[self anotherObject] button] titleLabel] once using a local variable to invoke its methods
	UILabel *buttonLabel = [[[self anotherObject] button] titleLabel];
	[buttonLabel setFont:[UIFont systemFontOfSize:12]];
	[buttonLabel setLineBreakMode:NSLineBreakByTruncatingTail];
}

- (void)updateSubviewBackgrounds {
	// bad: make frequent calls to a potentially expensive backgroundColors method call
	[[self subviews] enumerateObjectsUsingBlock:^(UIView *subview, NSUInteger index, __unused BOOL *stop) {
		[[subview layer] setBackgroundColor:[[self backgroundColors] objectAtIndex:(index % [[self backgroundColors] count])]];
	}

	// good: only call backgroundColors once and use the local variable to access values
	NSArray<UIColor *> *backgroundColors = [self backgroundColors];
	NSUInteger backgroundColorsCount = [backgroundColors count];
	[[self subviews] enumerateObjectsUsingBlock:^(UIView *subview, NSUInteger index, __unused BOOL *stop) {
		[[subview layer] setBackgroundColor:[backgroundColors objectAtIndex:(index % backgroundColorsCount)]];
	}
}

@end
```
