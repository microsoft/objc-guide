# Basic

Use `Nulllable` or `Nonnull` annotation which also helps interop with Swift.
```obj-c
- (nullable AAPLListItem *)itemWithName:(nonnull NSString *)name;
- (NSInteger)indexOfItem:(nonnull AAPLListItem *)item;
```

To ease adoption of the new annotations, you can mark certain regions of your Objective-C header files as audited for nullability with `NS_ASSUME_NONNULL_BEGIN` and `NS_ASSUME_NONNULL_END`. Within these regions, any simple pointer type will be assumed to be nonnull. This collapses our earlier example down into something much simpler.
```obj-c
NS_ASSUME_NONNULL_BEGIN
@interface AAPLList : NSObject <NSCoding, NSCopying>
// ...
- (nullable AAPLListItem *)itemWithName:(NSString *)name;
- (NSInteger)indexOfItem:(AAPLListItem *)item;

@property (copy, nullable) NSString *name;
@property (copy, readonly) NSArray<AAPLListItem *> *allItems;
// ...
@end
NS_ASSUME_NONNULL_END
```

For Apple guidelines, please refer [Nullability and Objective-C](https://developer.apple.com/swift/blog/?id=25)