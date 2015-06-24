# Variable Scope

## Objectives

1. Know the purpose and value of scope in applications.
2. Distinguish scope from context, and know how to identify both.
3. Recognize context inclusion and exclusion.
4. Avoid the pitfall of unintentional "shadowing".
5. Realize the value of passing variables through method arguments.

## Scope: What and Why?

*By "variable scope" we mean "the scope of variables," not "a scope that varies."*

Imagine if every part of your code knew about every variable in every other part of your code. Since you've only written code in a single file so far, this may be difficult to visualize. But speculate for a moment: what if you worked on an application that contained hundreds or even thousands of files? You'd have to take great care to not accidentally reuse variable names in multiple places and to not overlap your variable names with those of other developers working on different features of the same application. What if, for example, the variable `point` was defined as an "x-y" coordinate in one place, but also as a "latitude-longitude" variable somewhere else? You'd have to keep everything in your mind all at once. What a mess!

Fortunately, the "visibility" of classes, methods, and variables is controlled by what is termed "**scope**". It's a sort of topography to code—the higher the scope of a variable, the more places it's visible, while the lower the scope, the fewer places it's visible. The variables, methods, etc. that a particular place in your code can "see" are referred to as being "in scope" or "in context", while those that it cannot see are "out of scope" or "out of context". It's a scale of general-to-specific, or in programming terms, "global" to "local". 

A "**context**" refers to the lexicon of variables, methods, and classes available at a given location in the program. Autocomplete, our handy tool, references the current context when supplying options—it can only offer the options that it sees in the current context. (*To distinguish "scope" from "context"—an individual code element has a scope, while the context contains many elements.*)

The particular nature of scope varies dramatically from language to language. Fortunately for us, Objective-C has a pretty straightforward take on it.

## Metaphor: Levels of Governance

You can think of scope through the metaphor of different levels of governance. The Flatiron School's campus is located in the city of New York, in the county of Manhattan, in the state of New York, in the nation of the United States of America. Each of these levels represents a different "scope" of governance.

The laws and regulations of each of these levels of jurisdiction apply simultaneously, but at different levels of localization. Crossing the East River into Astoria, New York (state), for example, would put us into Queens county. We would still be within the jurisdiction of the state of New York and within the jurisdiction of the United States of America, but we would have changed locations within the jurisdiction of counties.

Similarly, if we were to cross the Hudson River to the west, we would find ourselves in the state of New Jersey (and no longer governed by the 8.875% New York sales tax). We would, however, still be in the United States of America. It would take travelling northward into the province (state equivalent) of Ontario to put us into the jurisdiction of the nation of Canada. But even then, we'd still be within the scope of the North American continent (though this only a geographic locality and not a level of governance).

It's this sense of scope that allows Astoria, New York and Astoria, Washington to share the same city name; they are, however, unique within their local counties. And on the county level, there is an Orange County, California and an Orange County, Florida; a Marion County, Tennessee and a Marion County, Florida. Again, names that are unique within the jurisdiction (read "scope") of their local states, but which mutually submit to the jurisdiction of the national government.

Local state, county, and city governments have the authority to pass laws upon their constituencies in order to govern themselves—but in doing so they cannot (legally) contradict the laws of any of their predominant jurisdictions. Traffic laws regarding "right turn on red" is a great example of this. It's illegal in Manhattan to make a right turn at a red light, however, it's permissible in most of the rest of the state of New York. ([NY.gov](http://www.safeny.ny.gov/roadrule.htm))

So how does this image apply to programming?

#### The Levels of Scope

The "levels of governance" for scope begins at the level of an entire application. This is called "global" scope and refers to variables accessible from anywhere in your code. This is that "national" level of your code—the context that belongs to everything.

**Note:** *We'll discuss declaring and using global variables in more detail later, but you should avoid using them until much later in the course: while they do have some uses they are a common source of problems.*

Within your application, each of your class files in the project navigator represents a separate context. These class files can be seen as the states or provinces to your code. At this level, classes can contain variables in the form of properties (which we'll explain to you later) and method declarations & definitions.

Within your class files, methods can be seen as the counties in our metaphor. Arguments are variables passed into the context of the method from the context in which the method is being called. They can then access those argument variables from within their local context. Method bodies employ curly braces (`{``}`) to mark their enclosures.

Inside of method bodies, conditionals and loops also employ curly braces (`{``}`) to enclose their contexts. These contexts can be nested without any hard limits similar to how a city can be further divided into districts, neighborhoods, street blocks, and even individual lots.

## Context Inclusion

In a similar way that national laws are applicable at the state, county, and city levels, a given context includes everything in the contexts in a direct line above it. However, once a context ends, the variables declared within it are no longer accessible (in most cases they are actually dereferenced and released from memory).

In a similar way, variables from a given context are available inside all of the more localized contexts within it. An example of this inclusiveness can look like this:

```objc
NSInteger x = 5
NSLog(@"x: %li", x);

if (x < 10 ) {
	x = 7;             // 'x' is in context so we can access it 
	NSLog(@"x: %li, x);
}
NSLog(@"x: %li", x);  // 'x' remains altered
```
This will print: 

```
x: 5
x: 7
x: 7
```
Our variable `x` remains altered because it was declared in the wider context. The redefinition of `x` belongs to a more localized context which will only run if the condition is met.

**Advanced:** *Classes gain knowledge of other classes through using* `#import` *to read the header files of another class and add its properties and method definitions to its context lexicon.*

## Context Exclusion

However, a more general context will not include variables declared in a localized contexts which it contains. Let's declare a new variable `y` inside the `if` statement, then try printing it after the `if` statement closes:

```objc
NSInteger x = 5
NSLog(@"x: %li", x);

if (x < 10 ) {
	x = 7;
	NSInteger y = 3;   // a new variable inside the localized context
	
	NSLog(@"x: %li, y: %li", x, y);
}
NSLog(@"x: %li, y: %li", x, y);  // 'y' is now "out of context"
```
This will generate a compiler error: 

```
Use of undeclared identifier 'y'.
```

![ExampleError_use_of_undeclared_identifier](https://curriculum-content.s3.amazonaws.com/reading-ios-variable-scope/reading-ios-variable-scope/ExampleError_use_of_undeclared_identifier.png)

This error message appears because `y` has been released and does not exist in the current context.

## Shadowing

"**Shadowing**" or "variable masking" is permitted in Objective-C but it can lead to unexpected behavior if done unintentionally. Shadowing is the action of redeclaring a variable within a more localized context than its original declaration. As you should be aware, attempting to redeclare a variable in the same context generates a compiler error:

```objc
NSInteger x = 5;

NSInteger x = 7;   // Error: "Redefinition of 'x'"
```

![ExampleError_redefinition](https://curriculum-content.s3.amazonaws.com/reading-ios-variable-scope/reading-ios-variable-scope/ExampleError_redefinition.png)

**Note:** *This is a case of a minor semantic difference between Xcode's terminology and the terminology of these lessons. If it had been up to him, this author would have written the error code above to read* `Redeclaration of 'x'`.

However, if we redeclared `x` within an `if` statement, for example, the change will only affect `x` within the localized context. Once we return to the wider context, however, `x` will again hold its value from when we were last in the wider context:

**~ Do NOT follow this example in your work! ~**

```objc
NSInteger x = 5;
NSLog(@"x: %li", x);

if (x < 10) {
    NSInteger x = 7;  // shadowing of 'x'
    NSLog(@"x: %li", x);
}

NSLog(@"x: %li", x);  // 'x' is 'unmasked'
```
This will print:

```
x: 5
x: 7
x: 5
```
Shadowing is something that you will want to avoid in most cases. There are few situations in which you will find it useful. We're explaining it here so that you can recognize it in order to avoid mistakes that it can cause.

Avoiding unintentional shadowing is one reason to keep your variables names descriptive, even with integers. **The wider the scope of a variable, the more specific its name should be.** In Objective-C, single-letter variable names are only regarded as acceptable for highly-localized integers with short lifespans such as `NSUInteger i` with `for` loops. After the initial declaration of the variable, you'll be able to rely on autocomplete to finish the names for you, so let your variable and method names be as verbose as needed!

## Arguments: Passing In Values

As mentioned above, methods can accept a value from outside their current context heirarchy in the form of an argument. What's worth remarking about arguments is that they are a form of passing values between separated contexts.

In the following example, we've created a `customMethod` that calls a "helper method" `addInteger:toArray:` in order to outsource some of its functionality. (*Notice that it sends the method call to* `self` *since it is calling a method within the same class file.*)

It requires two arguments, an `NSInteger` named `i` and a mutable array named `array`. Via the method call, the values are passed from the context of `customMethod` into the separate context of `addInteger:toArray:`.

```objc
-(void)customMethod {
   NSMutableArray *myArray = [[NSMutableArray alloc] init];
   [self addInteger:5 toArray:myArray];
   NSLog(@"myArray now has %lu element(s)", myArray.count);
}

-(void)addInteger:(NSInteger)i toArray:(NSMutableArray *)array {
   [array addObject:@(i)];
}
```
This will print: `myArray now has 1 element(s)`, which in this case will be an `NSNumber` holding the value `5`.

This functionality plays heavily into the "object" nature of Objective-C (which we'll discuss in more detail later) that makes it such a powerful language.


