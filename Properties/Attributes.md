# Basics
If declaring any class which supports [NSCopying](https://developer.apple.com/documentation/foundation/nscopying)--such as NSString, NSDate, NSArray etc--as a property in ARC environement, use `copy` instead of `strong`.

Under the hood, `copy` for immutable objects just does a strong reference (it doesn't actually copy the objects). However, if the object is mutable (i.e. NSMutableString, for example), then it does do a copy (and that copy is fast, but more about that later).

This prevents somebody from doing something like this contrived example:
```obj-c
@interface Person : NSObject 
// note that these are strong, not copy 
@property (readwrite, strong, nonatomic) NSString *firstName; 
@property (readwrite, strong, nonatomic) NSString *fullName; 
@end 

@implementation Person
@end
  
// caller code in a different file
Person *person = [[Person alloc] init]; 
NSMutableString *string = [[NSMutableString alloc] initWithString:@"Grace"]; 
[person setFirstName:string]; 
[string appendString:@" Hopper"]; 
[person setFullName:string];
```
At this point, if we were to print out firstName, we’d get "Grace Hopper”, and fullName is “Grace Hopper”. 
If we change the code to do copy instead of strong:
  
```obj-c
@interface Person : NSObject 
@property (readwrite, copy, nonatomic) NSString *firstName;
@property (readwrite, copy, nonatomic) NSString *fullName; 
@end
```

And run the code again, we’ll get the expected behavior. 

Copy can be used for anything implementing NSCopying (NSArray, NSDictionary, NSString, NSSet, etc.). As mentioned earlier that copy is fast for mutable objects - and that’s mostly true. It delays the actual copy until the next write. 

If we consider the original code:
```obj-c
NSMutableString *string = [[NSMutableString alloc] initWithString:@"Grace"]; 
[person setFirstName:string];
```
At this point, string has been done a copy. So internally, it’s created a new NSString object, and pointed it at the internal bytes that we’re pointing at (which stores @“Grace”) and some flag is set to keep track that we’ve done this.So then when we get to this line: 
```obj-c
[string appendString:@" Hopper"];
```
Internally to appendString: (or any API which modifies the string) is where the copy is actually done. Since we set that flag earlier, we know that we can’t just modify the bytes that we were pointing to. Instead, we need to copy them to a new place and then write out @“ Hopper" to those bytes. 

Why does it delay the copy? Well, considering the use case where you might construct a mutable object and then hand it off as immutable, the system gets a huge perf win. Another example:
```obj-c
NSSet<NSNumber *> *GetLotteryPicks() { 
    NSMutableSet<NSNumber *> *numbers = [[NSMutableSet alloc] init]; 
    while ([numbers count]  < 6) {
        [numbers addObject:@(rand() % 60)];  }  
        return [numbers copy]; 
    }
```
Since we create the mutable set just to build it and return it, we never end up doing any real copy of all of the objects in the set.
