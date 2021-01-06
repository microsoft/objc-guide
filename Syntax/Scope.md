# Scope

## Convention
Avoid implicit single-line scope. Instead use curly braces (`{}`) to explicitly define scope.

> This convention, while not necessarily specific to Objective-C, has helped us improve the factoring and organization of our code. Although adopted to different degrees by different teams, we felt this was worth including to document rationale for teams that find the convention useful.

## Rationale
Adding curly braces for single line logic statements prevents errors in maintenance and refactoring over time. These types of refactoring issues around single-line if statements being refactored for multiple lines have caused real-world regressions, including a [high-profile security issue](https://www.imperialviolet.org/2014/02/22/applebug.html) in Apple's TLS/SSL stack.

## Examples

```obj-c
// bad: without brackets, the scope of the if statement is implicitly just the next statement which can be missed in refactoring
if (booleanStatement)
    [self executeConditionalLogic]
[self executeUnconditionalLogic]

// bad: whitespace can exacerbate this issue
if (booleanStatement)
    [self executeConditionalLogic]
    [self executeUnconditionalLogic]

// good: the curly braces explicitly define the scope of the logical statement and which logic will execute unconditionally
if (booleanStatement) {
    [self executeLogic]
}
[self executeUnconditionalLogic]

// bad: this single line statement relies on implicit scope and whether the nonLoopLogic executes as part of the loop could be clearer
while ([self shouldLoop]) [self executeLoopLogic]
[self executeNonLoopLogic]

// bad: whitespace can exacerbate this issue
while ([self shouldLoop]) [self executeLoopLogic]
    [self executeNonLoopLogic]

// good: the explicit curly braces makes it obvious that the non-loop logic executes outside of the while loop
while ([self shouldLoop]) { [self executeLoopLogic]; }
[self executeNonLoopLogic]
```