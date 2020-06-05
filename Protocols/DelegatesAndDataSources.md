# Protocol Delegates and Data Sources

[Delegates and data sources](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html) are special cased patterns of protocols. Delegates augment the behavior of an existing class by acting alongside the delegating object to achieve a given behavior. Data sources similarly act as an object delegated the responsibility of managing the data represented by the object.

## Convention

Delegates should not be required by the delegating object. Delegate protocols exclusively contain optional methods. Data sources may be required by the delegating object. Data sources may contain required methods, but that list should be kept as minimal as possible. 

Delegates and data sources should be held via weak references by the delegating object.  

Delegate and data source protocol names begin with the name of the object doing the delegating and end with either `Delegate` or `DataSource`. The methods they contain are named as follows. Begin with the name of the class doing the delegating, without the class prefix. The first word in the class name is lower case. If the method contains more arguments than just the sender, there follows a `:` indicating an argument and the first argument of the method is the object that invoked the method. If there is only the required sender parameter, the rest of the selector name and the class name should be combined into a single string and the sender argument follows the end of the selector name. For delegate methods relating to events that are about to or have just taken place, use `will` or `did` in the method name. For delegate and data source methods that are asking for a decision to be made, use `should` in the method name.

## Rationale

Delegates augment existing behavior in the delegating object, they don't provide it directly. The delegating object must be fully self-sufficient, even if there is no delegate to augment its default behavior. It follows that if the delegate itself is not a required property on the delegating object that none of the methods on the delegate should be required. The delegate can implement as many or as few delegate callbacks as it cares about based on the context of that individual delegate. Data sources however may be required or contain required methods as the delegating object may not be able perform its function without the underlying data upon which it operates. For example, a table view can operate perfectly fine without something responding when a cell is tapped (a delegate method), but can't operate if it doesn't know how many cells to display (a data source method). Where the delegating object can have reasonable defaults for unimplemented data source methods, those methods should be made optional and the defaults should be explicitly stated through documentation.

The delegating object cannot take a strong reference to a delegate or data source because it has no knowledge of the implementation details of the delegate or data source to which it keeps a reference. For example, the delegate object may have a strong reference to the delegating object and set itself as the delegate. If the delegating object took a strong reference, it would result in a [strong reference cycle](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW22) that could cause both objects to leak.

Apple's delegate and data source protocols as a rule include the word `Delegate` or `DataSource` as the end of the name of the protocol. As these are known special patterns of protocols, following their naming conventions lends readability and usability to protocols matching those patterns. The [Delegate Methods](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-1001803-BCIDAIJE) section of Apple's [Coding Guidelines for Cocoa](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html) describes how to name Delegate and Data Source methods. In general, following Apple's guidelines is recommended. Delegates and data sources require the sender argument because a single delegate object may be the delegate for multiple delegating objects and without the context of which delegate object is making the method call the delegate may not have enough information to respond appropriately.

## Examples

### Bad

```obj-c
NS_ASSUME_NONNULL_BEGIN

@protocol XYZCustomViewTapping; // bad: this protocol follows the delegate pattern and should end with Delegate
@protocol XYZCustomDataSource; // bad: doesn't fully contain the name of the delegating class 

/// A custom view that has a background and some variable lines of text provided by its data source and passes touch events on to its delegate
@interface XYZCustomView : UIView
/// The delegate of the custom view
@property (nonatomic, strong) id<XYZCustomViewTapping> delegate; // bad: delegates should be weak and nullable
/// The data source of the custom view
@property (nonatomic, strong) id<XYZCustomDataSource> dataSource;  // bad: data sources should be weak
@end

/// Delegate protocol for an XYZCustomView.
@protocol XYZCustomViewTapping <NSObject>
@required // bad: delegate protocols should not contain any required methods
/// Allows the delegate to inform the custom view if it should detect taps
///
/// @return \c YES if the custom view should detect taps, \c NO otherwise
- (BOOL)customViewShouldDetectTaps; // bad: doesn't have the delegating object as a parameter

/// Informs the delegate that the custom view was tapped
/// 
/// @param customView The XYZCustomView that was tapped
///
/// @note This only gets called if \c customViewShouldDetectTaps returns \c YES.
- (void)xyzCustomViewWasTapped:(XYZCustomView *)customView; // bad: should drop the class prefix of the delegating class

@end

@protocol XYZCustomDataSource <NSObject>
@required
/// For a given index, provide the line of string that should be drawn in the custom view
///
/// @param index The index for which to return the string
///
/// @return The string to be drawn in the custom view on the line at the provided \p index
- (NSString *)textForLineAtIndex:(NSUInteger)index // bad: missing the custom view for which this is being called

/// The number of lines of text to display in this custom view
///
/// @param customView The custom view for which to determine the number of lines of text to display
///
/// @return The number of lines of text for \p customView
- (NSUInteger)numberOfLinesOfTextInCustomView:(XYZCustomView *)customView; // bad: This method could easily be made optional by defining a default number of lines of text for a custom view

@end

NS_ASSUME_NONNULL_END
```

### Good

```obj-c
NS_ASSUME_NONNULL_BEGIN

@protocol XYZCustomViewDelegate;
@protocol XYZCustomViewDataSource;

/// A custom view that has a background and some variable lines of text provided by its data source and passes touch events on to its delegate
@interface XYZCustomView : UIView
/// The delegate of the custom view
@property (nonatomic, weak, nullable) id<XYZCustomViewDelegate> delegate;
/// The data source of the custom view
@property (nonatomic, weak) id<XYZCustomViewDataSource> dataSource;
@end

/// Delegate protocol for an XYZCustomView.
@protocol XYZCustomViewDelegate <NSObject>
@optional
/// Allows the delegate to inform the custom view if it should detect taps
///
/// @param comboBox The XYZComboBox that is about to pop up its contents
///
/// @return \c YES if the custom view should detect taps, \c NO otherwise
- (BOOL)customViewShouldDetectTaps:(XYZComboBox *)comboBox;

/// Informs the delegate that the custom view was tapped
/// 
/// @param customView The XYZCustomView that was tapped
///
/// @note This only gets called if \c customViewShouldDetectTaps: returns \c YES.
- (void)customViewWasTapped:(XYZCustomView *)customView;

@end

@protocol XYZCustomViewDataSource <NSObject>
@required
/// For a given index, provide the line of string that should be drawn in the custom view
///
/// @param customView The view for which to return the string to be drawn
/// @param index The index for which to return the string
///
/// @return The string to be drawn in the custom view on the line at the provided \p index
- (NSString *)customView:(XYZCustomView *)customView textForLineAtIndex(NSUInteger)

@optional
/// The number of lines of text to display in this custom view
/// @note If this method isn't implemented, the default number of lines of text is 1.
///
/// @param customView The custom view for which to determine the number of lines of text to display
///
/// @return The number of lines of text for \p customView
- (NSUInteger)numberOfLinesOfTextInCustomView:(XYZCustomView *)customView;

@end

NS_ASSUME_NONNULL_END
```
