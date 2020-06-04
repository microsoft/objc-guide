# Dot Notation

## Convention

Do not use dot notation to for method invocations, including property accessor methods. Instead, use the message syntax (square brackets) when calling a method.

## Rationale

[Dot notation](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW10) gives the appearance of the dot operator, which is used to access struct or union members, but is a potentially expensive method invocation. This syntactic sugar has no functional impact on the code. While the syntax may be more familiar to those coming from languages like Java, dot notation semantics (method invocation) differ significantly from the dot operator (field access).

Using dot notation can also lead to [Redundant Method Invocations](../MethodsAndImplementations/RedundantMethodInvocations.md) as it allows extra calls to the same method to hide behind the visually pleasing dot notation syntax.

We've found code readability is improved when reserving the dot operator for its historical role in C and using the message syntax for Objective-C method invocation.

For further reading, others such as [Big Nerd Ranch](https://www.bignerdranch.com/blog/dot-notation-syntax/) also advise against using dot notation.

## Examples

```Objective-C
@implementation MyView

- (void)updateFrame {

	// bad - using dot notation for the redundant [self frame] method invocations
	if (self.frame.size.width < 0) {
		// Fields cannot be set using dot notation. It's not immediately clear to the reader self.frame.size.width = 0 is invalid.
		NSRect frame = self.frame;
		frame.size.width = 0;
		self.frame = frame;
	}

	// good - using method syntax and avoiding redundant invocations
	NSRect frame = [self frame];
	if (frame.size.width < 0) {
		frame.size.width = 0;
		[self setFrame:frame];
	}
}

- (void)updateLayer {

	// bad - using dot notation for method invocations
	CALayer *layer = self.layer;
	if (self.enabled) {
		layer.backgroundColor = NSColor.greenColor.CGColor;
	}

	// good - make explicit method calls
	CALayer *layer = [self layer];
	if ([self isEnabled]) {
		[layer setBackgroundColor:[[NSColor greenColor] CGColor];
	}
}

@end
```
