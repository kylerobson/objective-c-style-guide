# Objective-C Style Guide

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn’t mentioned here, it’s probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Naming](#naming)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Bitmasks](#bitmasks)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Imports](#imports)
* [Xcode Project](#xcode-project)

## Dot Notation Syntax

Dot notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**For example:**
```objc
if (user.isHappy) {
    // Do something
}
else {
    // Do something else
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

Use the operators `?` and `?:`, but only when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into named variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error Handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (`-` or `+` symbol). There should be a space between the method parameter segments. There should be a space before the opening curly brace in the method implementation. There should be no other spaces in the method signature.

**For Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except as simple counter variables in `for` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants (`NSString * const RBConstantString`).

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface RBSection : NSObject

@property (nonatomic, copy) NSString *headline;

@end
```

**Not:**

```objc
@interface RBSection : NSObject {
    NSString *headline;
}
```

#### Variable Qualifiers

When it comes to the variable qualifiers [introduced with ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifier (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) should be placed between the asterisks and the variable name, e.g., `NSString * __weak text`. 

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Descriptive method and variable names are good, but don't be overly wordy.

**For example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *setBut;
```

A two letter prefix (e.g., `RB`) should always be used for class names as well as constants, enums, options, variables, and functions that are exposed outside of an interface in a header file. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

**For example:**

```objc
static const NSTimeInterval RBArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Constants available only within an implementation file should be named prefixed with a k

**For example:**

static const int kMaximumItemCount = 50;

**Not:**

static int MAXIMUM_ITEM_COUNT = 50;

Properties and local variables should be camel-case with the leading word being lowercase.

Instance variables should be camel-case with the leading word being lowercase, and should be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.** Synthesize is appropriate for readonly properties that have the getter implemented where having an ivar is desired, or where a readwrite property has the getter and setter implemented.

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

## Types

Use Foundation types such as NSInteger, NSUInteger, CGFloat, NSTimeInterval, etc in most cases over their C type counterparts. Exceptions such as using float when dealing with UIProgressView's float progress property. Use instancetype over id and code accordingly.

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted. Comments should not be used as an alternative to writing readable code.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## File organization

Imports at the top.

Constants below the imports.

Class extension below constants.

Implementation below class extension.

Methods exposed in the interface of this class should be at the top of the implementation. Below that any methods inherited from super. Below that methods for protocols specified in the interface. Below that any private methods for this implementation. Below that any protocol methods specified from this implementation file (using class extension).

`@synthesize` and `@dynamic` statements at the very bottom.

`init` methods should be structured like this. Built in code snippets can be edited to clean them up (they're totally inconsistent and often left out of date by the Xcode team):

```objc
- (instancetype)init {
    if (self = [super init]) { // or call the designated initializer
    	
    }
    return self;
}
```

Use NS_DESIGNATED_INITIALIZER when possible. Not all Apple classes use this correctly so calling NS_DESIGNATED_INITIALIZER may not always be possible. If implementing an init method, call the designated initializer if it is specified. When implementing multiple init methods, put common init work into one method called by all of them (e.g. commonInit).

Don't start off method names with "init" if it does not return instancetype.

## Pragma marks

Use pragma marks to order code. Use an empty line before and after pragmas. The following common ones should be used on every page where applicable:

```objc

#pragma mark - public

#pragma mark - public super

#pragma mark - public UITableViewDataSource // if it is indicated in .h

#pragma mark - private

#pragma mark - private UITextViewDelegate // if it is indicated in .m

```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## Mutable vs. non-mutable

Interfaces should not expose mutable data structures. When working with objects such as NSArray/NSMutableArray use copy/mutableCopy to switch between the two.


## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro. Use a struct constant to organize constants together when possible.

**For example:**

```objc
static NSString * const kAboutViewControllerCompanyName = @"RedBrick"; // in .m

static const CGFloat RBImageThumbnailHeight = 50.0; // in .h
```

**Not:**

```objc
#define CompanyName @"RedBrick"

#define thumbnailHeight 2
```

## Enumerated Types

When using `enum`s, use the new fixed underlying type specification, which provides stronger type checking and code completion. The SDK includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`.

**Example:**

```objc
typedef NS_ENUM(NSInteger, RBAdRequestState) {
    RBAdRequestStateInactive,
    RBAdRequestStateLoading
};
```

## Bitmasks

When working with bitmasks, use the `NS_OPTIONS` macro.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, RBAdCategory) {
  RBAdCategoryAutos      = 1 << 0,
  RBAdCategoryJobs       = 1 << 1,
  RBAdCategoryRealState  = 1 << 2,
  RBAdCategoryTechnology = 1 << 3
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `RBPrivate` or `private`) should never be used unless extending another class.

**For example:**

```objc
@interface RBAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Image Naming

Image names should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` and `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` and `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

Images that are used for a similar purpose should be grouped in respective groups in an Images folder or Asset Catalog. Use Asset Catalogs when possible.

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (!someNumber.boolValue)
```

**Not:**

```objc
if (isAwesome == YES) // Never do this.
if (someNumber.boolValue == NO)
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Blocks

When working with blocks within the current class a weak reference to self is not necessary if the block is not being kept as a property. For blocks that are kept as a property, or for blocks being passed to methods on other classes/instances, use a weak refence to self inside the block. Inside the block, making a strong reference to self from the weak reference to self may be necessary so that self is not deallocated before/during the callback. Use @strongify(self) and @weakify(self) to help.

**For Example:**

```objc
@weakify(self);
void (^addComponents)() = ^{
    @strongify(self);
    [self fooBar];
};
[RBSomeOtherClass passRetainedBlock:addComponents];

// or

@weakify(self);
void (^addComponents)() = ^{
    [self fooBar]; // if self is deallocated, this method will not be called. If that is desired, do not use strongify.
};
[RBSomeOtherClass passRetainedBlock:addComponents];

// or

void (^addComponents)() = ^{
    [self fooBar];
};
addComponents();
    
```

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[[self class] alloc] init];
   });

   return sharedInstance;
}
```
This will prevent [possible and sometimes frequent crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Imports

Put each import on its own line at the top of the file. There should be no empty lines or comments on imports.

Note: For modules use the [@import](http://clang.llvm.org/docs/Modules.html#using-modules) syntax.

```objc
@import QuartzCore;
#import "RBUser.h"
#import "RBButton.h"
#import "RBUserView.h"
```

## File Naming

Views should be suffixed with "View", ViewControllers should be suffixed with "ViewController." All obj-c files should be named after the main interface defined within the file if possible. All code files, storyboards, nibs should be prefixed with the class prefix (RB).

Sometimes Apple does not follow a consistent naming pattern for views. If subclassing one of these views such as UITableViewCell, a name such as RBUserTableViewCell is acceptable.

** For Example:**

RBUser.h
RBUserView.h
RBUserViewController.h
RBUserController.h // this is not a view controller, this is an NSObject

**Not:**

RBUserObject.h // It's obvious that it's an object
RBUserScreen.h // For a UIView subclass, this is bad
RBUserController.h // For a UIViewController subclass, this is bad

## Storyboards/Nibs

Use storyboards and nibs when possible over purely using code. Use of storyboards should be combined with subclassing and categories for code reuse and maintainability. All view controllers that use Interface Builder should be defined in a storyboard. Multiple storyboards should be used. Storyboards should either contain one view controller per storyboard or contain one section/user story per storyboard. Nibs should be used for UIView subclasses. Storyboards should be named after the initial view controller. Nibs should be named after the view class.

## Autolayout

Use auto layout when possible. Manual layout should be used as a last resort when auto layout is not well suited to a task (for example, collection view layout subclass is manual layout). When doing auto layout in code, use PureLayout over the built-in verbose ways of doing auto layout.

## ARC

Use ARC.

## Xcode project

When possible, always turn on “Treat Warnings as Errors” in the target’s Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang’s pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

If ours doesn’t fit your tastes, have a look at some other style guides:

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/2011/06/28/my-objective-c-style-guide/)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
* [Wikimedia](https://www.mediawiki.org/wiki/Wikimedia_Apps/Team/iOS/ObjectiveCStyleGuide)

