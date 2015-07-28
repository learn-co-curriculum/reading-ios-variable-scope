# Variable Scope

## Objectives

1. Know the purpose and value of scope in applications.
3. Recognize when a code element is in scope or out of scope.
4. Avoid the pitfall of unintentional "shadowing".
5. Realize the value of passing variables through method arguments.

## Scope: What and Why?

*By "variable scope" we mean "the scope of variables," not "a scope that varies."*

Imagine if every part of your code knew about every variable in every other part of your code. Since you've only written code in a single file so far, this may be difficult to visualize. But speculate for a moment: what if you worked on an application that contained hundreds or even thousands of files? You'd have to take great care to not accidentally reuse variable names in multiple places and to not overlap your variable names with those of other developers working on different features of the same application. What if, for example, the variable `point` was defined as an "x-y" coordinate in one place, but also as a "latitude-longitude" variable somewhere else? You'd have to keep everything in your mind all at once. What a mess!

Fortunately, the "visibility" of classes, methods, and variables is controlled by what is termed "**scope**". Scope is the breadth of visibility of any given element of your code. The variables, methods, etc. that a particular place in your code can "see" are referred to as being "in scope", while those that it cannot see are referred to as being "out of scope". Elements that are widely visible to the entire application have what is called "global" scope, while elements with limited visibility are said to be "local" to a certain context.

## Metaphor: Levels of Governance

You can think of scope through the metaphor of different levels of governance. The Flatiron School's campus is located in the city of New York, in the county of Manhattan, in the state of New York, in the nation of the United States of America. Each of these levels represents a different "scope" of governance.

The laws and regulations of each of these levels of jurisdiction apply simultaneously but with different scopes. Crossing the East River into Astoria, New York (state), for example, would put us into Queens county. We would still be within the jurisdiction of the state of New York and within the jurisdiction of the United States of America, but we would have changed locations within the jurisdiction of counties.

Similarly, if we were to cross the Hudson River to the west, we would find ourselves in the state of New Jersey (and no longer governed by the 8.875% New York sales tax). We would, however, still be in the United States of America. It would take travelling northward into the province (state equivalent) of Ontario to put us into the jurisdiction of the nation of Canada. But even then, we'd still be within the scope of the North American continent (though this only a geographic locality and not a level of governance).

It's this sense of scope that allows Astoria, New York and Astoria, Washington to share the same city name; they are, however, unique within their local counties. And on the county level, there is an Orange County, California and an Orange County, Florida; a Marion County, Tennessee and a Marion County, Florida. Again, names that are unique within the jurisdiction (read "scope") of their local states, but which mutually submit to the jurisdiction of the national government.

Local state, county, and city governments have the authority to pass laws upon their constituencies in order to govern themselves—but in doing so they cannot (legally) contradict the laws of any of their predominant jurisdictions. Traffic laws regarding "right turn on red" is a great example of this. It's illegal in Manhattan to make a right turn at a red light, however, it's permissible in most of the rest of the state of New York. ([NY.gov](http://www.safeny.ny.gov/roadrule.htm))

So how does this image apply to programming?

## Scope in Objective-C

The particular nature of scope varies dramatically from language to language. Fortunately for us, Objective-C has a pretty straightforward take on it.

In a similar way that there are different tiers of government, each with their own jurisdictions, there are different levels of scope within an application. As mentioned before, variables accessible across the whole application are "global". Class files have a local nature to the properties and methods that they declare. And the methods themselves have a scope internal to their bodies.

Method bodies understand their boundaries by the opening and closing of curly braces `{``}` in their definitions. Within the methods bodies themselves, `if` conditionals and loops also employ curly braces (`{``}`) to denote their local scopes. These "control flow" statements can be nested without any hard limits similar to how a city can be further divided into districts, neighborhoods, street blocks, and even individual lots; however, anything declared within a given local scope will not be visible outside of that scope.

## The Mechanics of Visibility

You've already been writing code that utilizes scope. Let's look at some familiar examples through this new lens.

Variables declared outside of a local scope are availabile within the local scope.

```objc
NSInteger x = 5;
NSLog(@"x: %li", x);

if (x < 10) {
	x = 7;             // 'x' is in scope so we can access it 
	NSLog(@"x: %li", x);
}
NSLog(@"x: %li", x);  // 'x' remains altered
```
This will print: 

```
x: 5
x: 7
x: 7
```
Our variable `x` remains altered because it was declared in the wider scope. The setting of `x` to `7` within the `if` statement does **not** limit the variable to that local scope.

**Advanced:** *Classes gain knowledge of definitions of other classes by using* `#import` *to read other header files. Doing this allows creating instances of the other class and using the methods and properties in its header.*

Just as the specific laws of New York City do not apply to New York State at large, wider scopes cannot access variables declared in more local scopes. Let's declare a new variable `y` inside the `if` statement, then try printing it after the `if` statement closes:

```objc
NSInteger x = 5;
NSLog(@"x: %li", x);

if (x < 10) {
	x = 7;
	NSInteger y = 3;   // a new variable with local scope
	
	NSLog(@"x: %li, y: %li", x, y);
}
NSLog(@"x: %li, y: %li", x, y);  // 'y' is now "out of scope"
```
This will generate a compiler error: 

```
Use of undeclared identifier 'y'.
```

![ExampleError_use_of_undeclared_identifier](https://curriculum-content.s3.amazonaws.com/reading-ios-variable-scope/reading-ios-variable-scope/ExampleError_use_of_undeclared_identifier.png)

This error message appears because `y` is not available outside of the scope defined by the `if` statement.

## Shadowing
**Shadowing** is the action of redefining a variable within a more local scope than its original definition. While shadowing is permitted in Objective-C, it can lead to unexpected behavior so you should avoid implementing it. As you should be aware, attempting to redeclare a variable within the same scope generates a compiler error. 

```objc
NSInteger x = 5;

NSInteger x = 7;   // Error: "Redefinition of 'x'"
```

![ExampleError_redefinition](https://curriculum-content.s3.amazonaws.com/reading-ios-variable-scope/reading-ios-variable-scope/ExampleError_redefinition.png)

However, if we redefined `x` within an `if` statement, for example, the change will only affect `x` within the local scope of the `if` statement. Once we return to the wider scope, however, `x` will again hold its value from when we were last in the wider scope:

**~ Do NOT follow this example in your work! ~**

```objc
NSInteger x = 5;
NSLog(@"x: %li", x);

if (x < 10) {
    NSInteger x = 7;  // shadowing of 'x'
    NSLog(@"x: %li", x);
}

NSLog(@"x: %li", x);  // original value of 'x'
```
This will print:

```
x: 5
x: 7
x: 5
```
As mentioned above, shadowing is something that you will want to avoid in most cases. There are few situations in which you will find it useful. We're explaining it here so that you can recognize it in order to avoid mistakes that it can cause.

Avoiding unintentional shadowing is one reason to keep your variables names descriptive, even with integers. **The wider the scope of a variable, the more specific its name should be.** In Objective-C, single-letter variable names are only regarded as acceptable for very locally-scoped integers with short lifespans, such as `NSUInteger i` with `for` loops. After the initial declaration of the variable, you'll be able to rely on autocomplete to finish the names for you, so let your variable and method names be as verbose as needed!

## Arguments: Passing In Values

As mentioned above, methods can accept a value from outside their scope in the form of an argument. What's worth remarking about arguments is that they are a form of passing values between separated scopes.

In the following example, we've created a "helper method" `addInteger:toMutableArray:` that we're calling within the `application:didFinishLaunchingWithOptions:` method. **Notice that the method call is sent to** `self` **since the recipient of the method call is itself.** 

This `addInteger:toMutableArray:` helper method requires two arguments, an `NSInteger` named `integer` and a mutable array named `mutableArray`. Via the method call, the argument values are passed from the local scope of `application:didFinishLaunchingWithOptions:` method into the separate local scope of the `addInteger:toMutableArray:` method.

```objc
-(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    NSMutableArray *mArray = [[NSMutableArray alloc] init];
    
    [self addInteger:5 toMutableArray:mArray];  // calling the helper method
    
    NSLog(@"The array now has %lu element(s).", mArray.count);

    return YES;
}

-(void)addInteger:(NSInteger)integer toMutableArray:(NSMutableArray *)mutableArray {
   [mutableArray addObject:@(integer)];
}
```
This will print: `mArray now has 1 element(s)`, which in this case will be an `NSNumber` holding the value `5`.

Arguments in method calls are one of the easiest ways to pass values around in your application.


