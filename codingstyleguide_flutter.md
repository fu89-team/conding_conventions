# Introduction
This style guide describes the preferred style for code written as part of the Flutter project (the framework itself and all our sample code). Flutter application developers are welcome to follow this style as well, but this is by no means required. Flutter will work regardless of what style is used to author applications that use it.

# Table of Contents
- [Introduction](#Introduction)
- [Table of Contents](#Table-of-Contents)
- [Overview](#Overview)
- [Philosophy](#Philosophy)
    - [Lazy programming](#Lazy-programming)
    - [Write Test, Find Bug](#Write-Test-Find-Bug)
    - [Avoid duplicating state](#Avoid-duplicating-state)
    - [Getters feel faster than methods](#Getters-feel-faster-than-methods)
    - [No synchronous slow work](#No-synchronous-slow-work)
    - [Layers](#Layers)
    - [Avoid complecting](#Avoid-complecting)
    - [Avoid secret or global state](#Avoid-secret-or-global-state)
    - [Prefer general APIs but use dedicated APIs where there is a reason](#Prefer-general-APIs-but-use-dedicated-APIs-where-there-is-a-reason)
    - [Avoid APIs that encourage bad practices](#Avoid-apis-that-encourage-bad-practices)
    - [Avoid exposing API cliffs](#Avoid-exposing-API-cliffs)
    - [Avoid exposing API oceans](#Avoid-exposing-API-oceans)
    - [Solve real problems by literally solving a real problem](#Solve-real-problems-by-literally-solving-a-real-problem)
    - [Designing new APIs](#Designing-new-APIs)
- [Documentation](#Documentation)
    - [Answer your own questions straight away](#Answer-your-own-questions-straight-away)
    - [Avoid useless documentation](#Avoid-useless-documentation)
    - [Writing prompts for good documentation](#Writing-prompts-for-good-documentation)
    - [Avoid empty prose](#Avoid-empty-prose)
    - [Leave breadcrumbs in the comments](#Leave-breadcrumbs-in-the-comments)
    - [Refactor the code when the documentation would be incomprehensible](#Refactor-the-code-when-the-documentation-would-be-incomprehensible)
    - [Canonical terminology](#Canonical-terminology)
    - [Provide sample code](#Provide-sample-code)
    - [Provide illustrations diagrams or screenshots](#Provide-illustrations-diagrams-or-screenshots)
    - [Clearly mark deprecated APIs](#Clearly-mark-deprecated-APIs)
    - [Use comment for public-quality private documentation](#Use-comment-for-public-quality-private-documentation)
- [Coding patterns and catching bugs early](#Coding-patterns-and-catching-bugs-early)
    - [Use asserts liberally to enforce contracts and invariants](#Use-asserts-liberally-to-enforce-contracts-and-invariants)
    - [Avoid using enum values](#Avoid-using-enum-values)
    - [Prefer specialized functions, methods and constructors](#Prefer-specialized-functions-methods-and-constructors)
    - [Perform dirty checks in setters](#Perform-dirty-checks-in-setters)
    - [Minimize the visibility scope of constants](#Minimize-the-visibility-scope-of-constants)
    - [Avoid using var](#Avoid-using-var)
    - [Never check if a port is available before using it, never add timeouts, and other race conditions.](#Never-check-if-a-port-is-available)
    - [Have good hygiene when using temporary directories](#Have-good-hygiene-when-using-temporary-directories)
- [Naming](#Naming)
    - [Begin global constant names with prefix k](#Begin-global-constant-names-with-prefix-k)
    - [Naming rules for typedefs and function variables](Naming-rules-for-typedefs-and-function-variables)
    - [Spell words in identifiers and comments correctly](#Spell-words-in-identifiers-and-comments-correctly)
    - [Capitalize identifiers consistent with their spelling](#Capitalize-identifiers-consistent-with-their-spelling)
    - [Avoid double negatives in APIs](#Avoid-double-negatives-in-APIs)
    - [Prefer naming the argument to a setter value](#Prefer-naming-the-argument-to-a-setter-value)
    - [Qualify variables and methods used only for debugging](#Qualify-variables-and-methods-used-only-for-debugging)
    - [Avoid naming undocumented libraries](#Avoid-naming-undocumented-libraries)
- [Comments](#Comments)
    - [Avoid checking in commented-out code](#Avoid-checking-in-commented-out-code)
    - [Avoid checking in comments that ask questions](Avoid-checking-in-comments-that-ask-questions)
- [Formatting](#Formatting)
    - [In defense of the extra work that hand-formatting entails](#In-defense-of-the-extra-work-that-hand-formatting-entails)
    - [Constructors come first in a class](#Constructors-come-first-in-a-class)
    - [Order other class members in a way that makes sense](#Order-other-class-members-in-a-way-that-makes-sense)
    - [Constructor syntax](#Constructor-syntax)
    - [Prefer line length of 80 characters](#Prefer-line-length-of-80-characters)
    - [Indent multi-line argument and parameter lists by 2 characters](#Indent-multi-line-argument-and-parameter-lists-by-2-characters)
    - [If you have a newline after some opening punctuation match it on the closing punctuation](#If-you-have-a-newline-after-some-opening-punctuation-match-it-on-the-closing-punctuation)
    - [Use a trailing comma for arguments parameters and list items](#Use-a-trailing-comma-for-arguments-parameters-and-list-items)
    - [Prefer single quotes for strings](#Prefer-single-quotes-for-strings)
    - [Consider using arrow for short functions and methods](#Consider-using-arrow-for-short-functions-and-methods)
    - [Use arrow for inline callbacks that just return list or map literals](#Use-arrow-for-inline-callbacks-that-just-return-list-or-map-literals)
    - [Use braces for long functions and methods](#Use-braces-for-long-functions-and-methods)
    - [Separate the if expression from its statement](#Separate-the-if-expression-from-its-statement)
    - [Align expressions](#Align-expressions)
  - [Packages](#Packages)
  - [Features we expect every widget to implement](#Features-we-expect-every-widget-to-implement)
  - [Deciding where to put code](#Deciding-where-to-put-code)
 
# Overview
This document describes our approach to designing and programming Flutter, from high-level architectural principles all the way to indentation rules.

The primary goal of these style guidelines is to improve code readability so that everyone, whether reading the code for the first time or maintaining it for years, can quickly determine what the code does. Secondary goals are to design systems that are simple, to increase the likelihood of catching bugs quickly, and avoiding arguments when there are disagreements over subjective matters.

For anything not covered by this document, check the Dart style guide for more advice. That document is focused primarily on Dart-specific conventions, while this document is more about Flutter conventions.
# Philosophy
## Lazy programming
Write what you need and no more, but when you write it, do it right.

Avoid implementing features you don’t need. You can’t design a feature without knowing what the constraints are. Implementing features "for completeness" results in unused code that is expensive to maintain, learn about, document, test, etc.

When you do implement a feature, implement it the right way. Avoid workarounds. Workarounds merely kick the problem further down the road, but at a higher cost: someone will have to relearn the problem, figure out the workaround and how to dismantle it (and all the places that now use it), and implement the feature. It’s much better to take longer to fix a problem properly, than to be the one who fixes everything quickly but in a way that will require cleaning up later.

You may hear team members say "embrace the yak shave!". This is an encouragement to take on the larger effort necessary to perform a proper fix for a problem rather than just applying a band-aid.
## Write Test, Find Bug
When you fix a bug, first write a test that fails, then fix the bug and verify the test passes.

When you implement a new feature, write tests for it. See also: Running and writing tests.

Check the code coverage to make sure every line of your new code is tested. See also: Test coverage for package:flutter.

If something isn’t tested, it is very likely to regress or to get "optimized away". If you want your code to remain in the codebase, you should make sure to test it.

## Avoid duplicating state
There should be no objects that represent live state that reflect some state from another source, since they are expensive to maintain. (The Web’s HTMLCollection object is an example of such an object.) In other words, keep only one source of truth, and don’t replicate live state.
## Getters feel faster than methods
Property getters should be efficient (e.g. just returning a cached value, or an O(1) table lookup). If an operation is inefficient, it should be a method instead. (Looking at the Web again: we would have document.getForms(), not document.forms, since it walks the entire tree).

Similarly, a getter that returns a Future should not kick-off the work represented by the future, since getters appear idempotent and side-effect free. Instead, the work should be started from a method or constructor, and the getter should just return the preexisting Future.

## No synchronous slow work
There should be no APIs that require synchronously completing an expensive operation (e.g. computing a full app layout outside of the layout phase). Expensive work should be asynchronous.

## Layers
We use a layered framework design, where each layer addresses a narrowly scoped problem and is then used by the next layer to solve a bigger problem. This is true both at a high level (widgets relies on rendering relies on painting) and at the level of individual classes and methods (e.g. Text uses RichText and DefaultTextStyle).

Convenience APIs belong at the layer above the one they are simplifying.

## Avoid complecting
Each API should be self-contained and should not know about other features. Interleaving concepts leads to complexity.

For example:

Many Widgets take a child. Widgets should be entirely agnostic about the type of that child. Don’t use is or similar checks to act differently based on the type of the child.

Render objects each solve a single problem. Rather than having a render object handle both clipping and opacity, we have one render object for clipping, and one for opacity.

In general, prefer immutable objects over mutable data. Immutable objects can be passed around safely without any risk that a downstream consumer will change the data. (Sometimes, in Flutter, we pretend that some objects are immutable even when they technically are not: for example, widget child lists are often technically implemented by mutable List instances, but the framework will never modify them and in fact cannot handle the user modifying them.) Immutable data also turns out to make animations much simpler through lerping.

## Avoid secret or global state
A function should operate only on its arguments and, if it is an instance method, data stored on its object. This makes the code significantly easier to understand.

For example, when reading this code:
```kotlin
// ... imports something that defines foo and bar ...

void main() {
  foo(1);
  bar(2);
}
```
…​the reader should be confident that nothing in the call to foo could affect anything in the call to bar.

This usually means structuring APIs so that they either take all relevant inputs as arguments, or so that they are based on objects that are created with the relevant input, and can then be called to operate on those inputs.

This significantly aids in making code testable and in making code understandable and debuggable. When code operates on secret global state, it’s much harder to reason about.

# Prefer general APIs, but use dedicated APIs where there is a reason
For example, having dedicated APIs for performance reasons is fine. If one specific operation, say clipping a rounded rectangle, is expensive using the general API but could be implemented more efficiently using a dedicated API, then that is where we would create a dedicated API.

# Avoid APIs that encourage bad practices
For example, don’t provide APIs that walk entire trees, or that encourage O(N^2) algorithms, or that encourage sequential long-lived operations where the operations could be run concurrently.

In particular:

String manipulation to generate data or code that will subsequently be interpreted or parsed is a bad practice as it leads to code injection vulnerabilities.

If an operation is expensive, that expense should be represented in the API (e.g. by returning a Future or a Stream). Avoid providing APIs that hide the expense of tasks.

# Avoid exposing API cliffs
Convenience APIs that wrap some aspect of a service from one environment for exposure in another environment (for example, exposing an Android API in Dart), should expose/wrap the complete API, so that there’s no cognitive cliff when interacting with that service (where you are fine using the exposed API up to a point, but beyond that have to learn all about the underlying service).

# Avoid exposing API oceans
APIs that wrap underlying services but prevent the underlying API from being directly accessed (e.g. how dart:ui exposes Skia) should carefully expose only the best parts of the underlying API. This may require refactoring features so that they are more usable. It may mean avoiding exposing convenience features that abstract over expensive operations unless there’s a distinct performance gain from doing so. A smaller API surface is easier to understand.

For example, this is why dart:ui doesn’t expose Path.fromSVG(): we checked, and it is just as fast to do that work directly in Dart, so there is no benefit to exposing it. That way, we avoid the costs (bigger API surfaces are more expensive to maintain, document, and test, and put a compatibility burden on the underlying API).

# Solve real problems by literally solving a real problem
Where possible, especially for new features, you should partner with a real customer who wants that feature and is willing to help you test it. Only by actually using a feature in the real world can we truly be confident that a feature is ready for prime time.

Listen to their feedback, too. If your first customer is saying that your feature doesn’t actually solve their use case completely, don’t dismiss their concerns as esoteric. Often, what seems like the problem when you start a project turns out to be a trivial concern compared to the real issues faced by real developers.

# Designing new APIs
If you’re designing a new API or a new feature, consider writing a design doc, typically using Google Docs or by writing it in a GitHub issue. Then, get feedback from the relevant people, e.g. send it to flutter-dev or post it on Gitter.

# Documentation
We use "dartdoc" for our Dart documentation, and similar technologies for the documentation of our APIs in other languages, such as ObjectiveC and Java. All public members in Flutter libraries should have a documentation.

In general, follow the Dart documentation guide except where that would contradict this page.

# Answer your own questions straight away
When working on Flutter, if you find yourself asking a question about our systems, please place whatever answer you subsequently discover into the documentation in the same place where you first looked for the answer. That way, the documentation will consist of answers to real questions, where people would look to find them. Do this right away; it’s fine if your otherwise-unrelated PR has a bunch of documentation fixes in it to answer questions you had while you were working on your PR.

We try to avoid reliance on "oral tradition". It should be possible for anyone to begin contributing without having had to learn all the secrets from existing team members. To that end, all processes should be documented (typically on the wiki), code should be self-explanatory or commented, and conventions should be written down, e.g. in our style guide.

There is one exception: it’s better to not document something in our API docs than to document it poorly. This is because if you don’t document it, it still appears on our list of things to document. Feel free to remove documentation that violates our the rules below (especially the next one), so as to make it reappear on the list.

# Avoid useless documentation
If someone could have written the same documentation without knowing anything about the class other than its name, then it’s useless.

Avoid checking in such documentation, because it is no better than no documentation but will prevent us from noticing that the identifier is not actually documented.

Example (from CircleAvatar):
```kotlin
// BAD:

/// The background color.
final Color backgroundColor;

/// Half the diameter of the circle.
final double radius;


// GOOD:

/// The color with which to fill the circle. Changing the background
/// color will cause the avatar to animate to the new color.
final Color backgroundColor;

/// The size of the avatar. Changing the radius will cause the
/// avatar to animate to the new size.
final double radius;
```

# Writing prompts for good documentation
If you are having trouble coming up with useful documentation, here are some prompts that might help you write more detailed prose:

 1.If someone is looking at this documentation, it means that they have a question which they couldn’t answer by guesswork or by looking at the code. What could that question be? Try to answer all questions you can come up with.

 2.If you were telling someone about this property, what might they want to know that they couldn’t guess? For example, are there edge cases that aren’t intuitive?

 3.Consider the type of the property or arguments. Are there cases that are outside the normal range that should be discussed? e.g. negative numbers, non-integer values, transparent colors, empty arrays, infinities, NaN, null? Discuss any that are non-trivial.

 4.Does this member interact with any others? For example, can it only be non-null if another is null? Will this member only have any effect if another has a particular range of values? Will this member affect whether another member has any effect, or what effect another member has?

 5.Does this member have a similar name or purpose to another, such that we should point to that one, and from that one to this one? Use the See also: pattern.

 6.Are there timing considerations? Any potential race conditions?

 7.Are there lifecycle considerations? For example, who owns the object that this property is set to? Who should dispose() it, if that’s relevant?

 8.What is the contract for this property/method? Can it be called at any time? Are there limits on what values are valid? If it’s a final property set from a constructor, does the constructor have any limits on what the property can be set to? If this is a constructor, are any of the arguments not nullable?

 9.If there are `Future`s involved, what are the guarantees around those? Consider whether they can complete with an error, whether they can never complete at all, what happens if the underlying operation is canceled, and so forth.
   
# Avoid empty prose
It’s easy to use more words than necessary. Avoid doing so where possible, even if the result is somewhat terse.

```kotlin
// BAD:

/// Note: It is important to be aware of the fact that in the
/// absence of an explicit value, this property defaults to 2.

// GOOD:

/// Defaults to 2.
```
In particular, avoid saying "Note:". It adds nothing.

# Leave breadcrumbs in the comments
This is especially important for documentation at the level of classes.

If a class is constructed using a builder of some sort, or can be obtained via some mechanism other than merely calling the constructor, then include this information in the documentation for the class.

If a class is typically used by passing it to a particular API, then include that information in the class documentation also.

If a method is the main mechanism used to obtain a particular object, or is the main way to consume a particular object, then mention that in the method’s description.

Typedefs should mention at least one place where the signature is used.

These rules result in a chain of breadcrumbs that a reader can follow to get from any class or method that they might think is relevant to their task all the way up to the class or method they actually need.

Example:
```kotlin
// GOOD:

/// An object representing a sequence of recorded graphical operations.
///
/// To create a [Picture], use a [PictureRecorder].
///
/// A [Picture] can be placed in a [Scene] using a [SceneBuilder], via
/// the [SceneBuilder.addPicture] method. A [Picture] can also be
/// drawn into a [Canvas], using the [Canvas.drawPicture] method.
abstract class Picture ...
```
You can also use "See also" links, is in:
```kotlin
/// See also:
///
/// * [FooBar], which is another way to peel oranges.
/// * [Baz], which quuxes the wibble.
```
Each line should end with a period. Prefer "which…​" rather than parentheticals on such lines. There should be a blank line between "See also:" and the first item in the bulleted list.

# Refactor the code when the documentation would be incomprehensible
If writing the documentation proves to be difficult because the API is convoluted, then rewrite the API rather than trying to document it.

# Canonical terminology
The documentation should use consistent terminology:

 1.method - a member of a class that is a non-anonymous closure

 2.function - a callable non-anonymous closure that isn’t a member of a class

 3.parameter - a variable defined in a closure signature and possibly used in the closure body.

 4.argument - the value passed to a closure when calling it.

Prefer the term "call" to the term "invoke" when talking about jumping to a closure.

Typedef dartdocs should usually start with the phrase "Signature for…​".

# Provide sample code
Sample code helps developers learn your API quickly. Writing sample code also helps you think through how your API is going to be used by app developers.

Sample code should go in a section of the documentation that has a ## Sample code header. This will then be checked by automated tools.

For example, below is the sample code for building an infinite list of children with the ListView widget:

```kotlin
ListView.builder(
  padding: const EdgeInsets.all(8.0),
  itemExtent: 20.0,
  itemBuilder: (BuildContext context, int index) {
    return Text('entry $index');
  },
) 
```

# Provide illustrations, diagrams or screenshots
For any widget that draws pixels on the screen, showing how it looks like in its API doc helps developers decide if the widget is useful and learn how to customize it. All illustrations should be easily reproducible, e.g. by running a Flutter app or a script.

# Clearly mark deprecated APIs
According to Flutter’s Design Principles, use @deprecated with a clear recommendation of what to use instead.

In some cases, using @deprecated will turn the tree red for longer than the Flutter team can accommodate. In those cases, and when we want to give developers enough time to move to the new API, you should use this format:

```kotlin
// GOOD

/// (Deprecated, use [lib.class] instead) Original one-line statement.
///
/// A longer, one-liner that explains the context for the deprecation.
///
/// The rest of the comments
```

# Use comment for public-quality private documentation
In general, private code can and should also be documented. If that documentation is of good enough quality that we could include it verbatim when making the class public (i.e. it satisfies all the style guidelines above), then use you can use /// for those docs, even though they’re private.

Documentation of private APIs that is not of sufficient quality should only use //. That way, if we ever make the corresponding class public, those documentation comments will be flagged as missing, and we will know to examine them more carefully.

Feel free to be conservative in what you consider "sufficient quality". It’s ok to use // even if you have multiple paragraphs of documentation; that’s a sign that we should carefully rereview the documentation when making the code public.

# Coding patterns and catching bugs early
## Use asserts liberally to enforce contracts and invariants
assert() allows us to be diligent about correctness without paying a performance penalty in release mode, because Dart only evaluates asserts in debug mode.

The following example is from box.dart:

```kotlin
abstract class RenderBox extends RenderObject {
  // ...

  double getDistanceToBaseline(TextBaseline baseline, {bool onlyReal: false}) {
    // simple asserts:
    assert(!needsLayout);
    assert(!_debugDoingBaseline);
    // more complicated asserts:
    assert(() {
      final RenderObject parent = this.parent;
      if (owner.debugDoingLayout)
        return (RenderObject.debugActiveLayout == parent) &&
            parent.debugDoingThisLayout;
      if (owner.debugDoingPaint)
        return ((RenderObject.debugActivePaint == parent) &&
                parent.debugDoingThisPaint) ||
            ((RenderObject.debugActivePaint == this) && debugDoingThisPaint);
      assert(parent == this.parent);
      return false;
    });
    // ...
    return 0.0;
  }

  // ...
}
```
# Avoid using enum values
Use switch if you are examining an enum, since the analyzer will warn you if you missed any of the values when you use switch.

Avoid using if chains, ? …​ : …​, or, in general, any expressions involving enums.

# Prefer specialized functions, methods and constructors
Use the most relevant constructor or method, when there are multiple options.

Example:
```kotlin
// BAD:
const EdgeInsets.TRBL(0.0, 8.0, 0.0, 8.0);

// GOOD:
const EdgeInsets.symmetric(horizontal: 8.0);
```

# Perform dirty checks in setters
When defining mutable properties that mark a class dirty when set, use the following pattern:
```kotlin
/// Documentation here (don't wait for a later commit).
TheType get theProperty => _theProperty;
TheType _theProperty;
void set theProperty(TheType value) {
  assert(value != null);
  if (_theProperty == value)
    return;
  _theProperty = value;
  markNeedsWhatever(); // the method to mark the object dirty
}
```

The argument is called 'value' for ease of copy-and-paste reuse of this pattern. If for some reason you don’t want to use 'value', use 'newTheProperty' (where 'theProperty' is the property name).

Start the method with any asserts you need to validate the value.

# Minimize the visibility scope of constants
Prefer using a local const or a static const in a relevant class than using a global constant.

# Avoid using var
All variables and arguments are typed; avoid "dynamic" or "Object" in any case where you could figure out the actual type. Always specialize generic types where possible. Explicitly type all list and map literals.

This achieves two purposes: it verifies that the type that the compiler would infer matches the type you expect, and it makes the code self-documenting in the case where the type is not obvious (e.g. when calling anything other than a constructor).

Always avoid "var". Use "dynamic" if you are being explicit that the type is unknown, but prefer "Object" and casting, as using dynamic disables all static checking.

# Never check if a port is available before using it, never add timeouts, and other race conditions.
you look for an available port, then try to open it, it’s extremely likely that several times a week some other code will open that port between your check and when you open the port, and that will cause a failure.
 - Instead, have the code that opens the port pick an available port and return it, rather than being given a (supposedly) available port.
If you have a timeout, then it’s very likely that several times a week some other code will happen to run while your timeout is running, and your "really conservative" timeout will trigger even though it would have worked fine if the timeout was one second longer, and that will cause a failure.
 - Instead, have the code that would time out just display a message saying that things are unexpectedly taking a long time, so that someone interactively using the tool can see that something is fishy, but an automated system won’t be affected.
Race conditions like this are the primary cause of flaky tests, which waste everyone’s time.

# Have good hygiene when using temporary directories
Give the directory a unique name that starts with flutter_ and ends with a period (followed by the autogenerated random string).

For consistency, name the Directory object that points to the temporary directory tempDir, and create it with createTempSync unless you need to do it asynchronously (e.g. to show progress while it’s being created).

Always clean up the directory when it is no longer needed. In tests, use the tryToDelete convenience function to delete the directory.

# Naming
## Begin global constant names with prefix k
Examples:
```kotlin
const double kParagraphSpacing = 1.5;
const String kSaveButtonTitle = 'Save';
const Color _kBarrierColor = Colors.black54;
```
## Naming rules for typedefs and function variables
When naming callbacks, use FooCallback for the typedef, onFoo for the callback argument or property, and handleFoo for the method that is called.

If you have a callback with arguments but you want to ignore the arguments, give the type and names of the arguments anyway. That way, if someone copies and pastes your code, they will not have to look up what the arguments are.

Never call a method onFoo. If a property is called onFoo it must be a function type. (For all values of "Foo".)

## Spell words in identifiers and comments correctly
Our primary source of truth for spelling is the Material Design Specification. Our secondary source of truth is dictionaries.

Avoid "cute" spellings. For example, 'colors', not 'colorz'.

Prefer US English spellings. For example, 'colorize', not 'colourise'.

Prefer compound words over "cute" spellings to avoid conflicts with reserved words. For example, 'classIdentifier', not 'klass'.

## Capitalize identifiers consistent with their spelling
If a word is correctly spelled (according to our sources of truth as described in the previous section) as a single word, then it should not have any inner capitalization or spaces.

For examples, prefer toolbar, scrollbar, but appBar ('app bar' in documentation), tabBar ('tab bar' in documentation).

Similarly, prefer offstage rather than offStage.

Avoid starting class names with iOS since that would have to capitalize as Ios which is not how that is spelled. (Use "Cupertino" or "UiKit" instead.)

## Avoid double negatives in APIs
Name your boolean variables in positive ways, such as "enabled" or "visible", even if the default value is true.

This is because, when you have a property or argument named "disabled" or "hidden", it leads to code such as input.disabled = false or widget.hidden = false when you’re trying to enable or show the widget, which is very confusing.

## Prefer naming the argument to a setter value
Unless this would cause other problems, use value for the name of a setter’s argument. This makes it easier to copy/paste the setter later.

## Qualify variables and methods used only for debugging
If you have variables or methods (or even classes!) that are only used in debug mode, prefix their names with debug or _debug (or, for classes, _Debug).

Do not use debugging variables or methods (or classes) in production code.

## Avoid naming undocumented libraries
In other words, do not use the library keyword, unless it is a documented top-level library intended to be imported by users.

# Comments
## Avoid checking in commented-out code
It will bitrot too fast to be useful, and will confuse people maintaining the code.

## Avoid checking in comments that ask questions
Find the answers to the questions, or describe the confusion, including references to where you found answers.

If commenting on a workaround due to a bug, also leave a link to the bug and a TODO to clean it up when the bug is fixed.

Example:
```kotlin
// BAD:

// What should this be?

// This is a workaround.


// GOOD:

// According to this specification, this should be 2.0, but according to that
// specification, it should be 3.0. We split the difference and went with
// 2.5, because we didn't know what else to do.

// TODO(username): Converting color to RGB because class Color doesn't support
//                 hex yet. See http://link/to/a/bug/123
```

## Formatting
These guidelines have no technical effect, but they are still important purely for consistency and readability reasons.

We do not yet use dartfmt. Flutter code tends to use patterns that the standard Dart formatter does not handle well. We are working with the Dart team to make dartfmt aware of these patterns.

## In defense of the extra work that hand-formatting entails
Flutter code might eventually be read by hundreds of thousands of people each day. Code that is easier to read and understand saves these people time. Saving each person even a second each day translates into hours or even days of saved time each day. The extra time spent by people contributing to Flutter directly translates into real savings for our developers, which translates to real benefits to our end users as our developers learn the framework faster.

## Constructors come first in a class
The default (unnamed) constructor should come first, then the named constructors. They should come before anything else (including, e.g., constants or static methods).

This helps readers determine whether the class has a default implied constructor or not at a glance. If it was possible for a constructor to be anywhere in the class, then the reader would have to examine every line of the class to determine whether or not there was an implicit constructor or not.

## Order other class members in a way that makes sense
The methods, properties, and other members of a class should be in an order that will help readers understand how the class works.

If there’s a clear lifecycle, then the order in which methods get invoked would be useful, for example an initState method coming before dispose. This helps readers because the code is in chronological order, so they can see variables get initialized before they are used, for instance. Fields should come before the methods that manipulate them, if they are specific to a particular group of methods.
 - For example, RenderObject groups all the layout fields and layout methods together, then all the paint fields and paint methods, because layout happens before paint.
If no particular order is obvious, then the following order is suggested, with blank lines between each one:

 1.Constructors, with the default constructor first.

 2.Constants of the same type as the class.

 3.Static methods that return the same type as the class.

 4.Final fields that are set from the constructor.

 5.Other static methods.

 6.Static properties and constants.

 7.Mutable properties, each in the order getter, private field, setter, without newlines separating them.

 8.Read-only properties (other than hashCode).

 9.Operators (other than ==).

 10.Methods (other than toString and build).

 11.The build method, for Widget and State classes.

 12.operator ==, hashCode, toString, and diagnostics-related methods, in that order.

Be consistent in the order of members. If a constructor lists multiple fields, then those fields should be declared in the same order, and any code that operates on all of them should operate on them in the same order (unless the order matters).

## Constructor syntax
If you call super() in your initializer list, put a space between the constructor arguments' closing parenthesis and the colon. If there’s other things in the initializer list, align the super() call with the other arguments. Don’t call super if you have no arguments to pass up to the superclass.
```kotlin
// one-line constructor example
abstract class Foo extends StatelessWidget {
  Foo(this.bar, { Key key, this.child }) : super(key: key);
  final int bar;
  final Widget child;
  // ...
}

// fully expanded constructor example
abstract class Foo extends StatelessWidget {
  Foo(
    this.bar, {
    Key key,
    Widget childWidget,
  }) : child = childWidget,
       super(
         key: key,
       );
  final int bar;
  final Widget child;
  // ...
}
```
## Prefer line length of 80 characters
Aim for a line length of 80 characters, but prefer going over if breaking the line would make it less readable or if it would make the line less consistent with other nearby lines.
```kotlin
// BAD
final int a = 1;
final int b = 2;
final int c =
    a.very.very.very.very.very.long.expression.that.returns.three.eventually().but.is.very.long();
final int d = 4;
final int e = 5;

// BETTER
final int a = 1;
final int b = 2;
final int c = a.very.very.very.very.very.long.expression.that.returns.three.eventually().but.is.very.long();
final int d = 4;
final int e = 5;
```

## Indent multi-line argument and parameter lists by 2 characters
When breaking an argument list into multiple lines, indent the arguments two characters from the previous line.

Example:
```kotlin
Foo f = Foo(
  bar: 1.0,
  quux: 2.0,
);
```
When breaking a parameter list into multiple lines, do the same.

## If you have a newline after some opening punctuation match it on the closing punctuation
And vice versa.

Example:

```kotlin
// BAD:
  foo(
    bar, baz);
  foo(
    bar,
    baz);
  foo(bar,
    baz
  );

// GOOD:
  foo(bar, baz);
  foo(
    bar,
    baz,
  );
  foo(bar,
    baz);
```

## Use a trailing comma for arguments parameters and list items
Example:
```kotlin
List<int> myList = [
  1,
  2,
]
myList = <int>[3, 4];

foo1(
  bar,
  baz,
);
foo2(bar, baz);
```
# Prefer single quotes for strings
Use double quotes for nested strings or (optionally) for strings that contain single quotes. For all other strings, use single quotes.

Example:
```kotlin
print('Hello ${name.split(" ")[0]}');
```

## Consider using arrow for short functions and methods
But only use ⇒ when everything, including the function declaration, fits on a single line.

Example:

```kotlin
// BAD:
String capitalize(String s) =>
  '${s[0].toUpperCase()}${s.substring(1)}';

// GOOD:
String capitalize(String s) => '${s[0].toUpperCase()}${s.substring(1)}';

String capitalize(String s) {
  return '${s[0].toUpperCase()}${s.substring(1)}';
}
```

## Use arrow for inline callbacks that just return list or map literals
If your code is passing an inline closure that merely returns a list or map literal, or is merely calling another function, then if the argument is on its own line, then rather than using braces and a return statement, you can instead use the ⇒ form. When doing this, the closing ], }, or ) bracket will line up with the argument name, for named arguments, or the ( of the argument list, for positional arguments.

For example:
```kotlin
  // GOOD, but slightly more verbose than necessary since it doesn't use =>
  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (String value) { print('Selected: $value'); },
      itemBuilder: (BuildContext context) {
        return <PopupMenuItem<String>>[
          PopupMenuItem<String>(
            value: 'Friends',
            child: MenuItemWithIcon(Icons.people, 'Friends', '5 new')
          ),
          PopupMenuItem<String>(
            value: 'Events',
            child: MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
          ),
        ];
      }
    );
  }

  // GOOD, does use =>, slightly briefer
  @override
  Widget build(BuildContext context) {
    return PopupMenuButton<String>(
      onSelected: (String value) { print('Selected: $value'); },
      itemBuilder: (BuildContext context) => <PopupMenuItem<String>>[
        PopupMenuItem<String>(
          value: 'Friends',
          child: MenuItemWithIcon(Icons.people, 'Friends', '5 new')
        ),
        PopupMenuItem<String>(
          value: 'Events',
          child: MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
        ),
      ]
    );
  }
```
The important part is that the closing punctuation lines up with the start of the line that has the opening punctuation, so that you can easily determine what’s going on by just scanning the indentation on the left edge.

## Use braces for long functions and methods
Use a block (with braces) when a body would wrap onto more than one line (as opposed to using ⇒; the cases where you can use ⇒ are discussed in the previous two guidelines).

## Separate the if expression from its statement
Don’t put the statement part of an 'if' statement on the same line as the expression, even if it is short. (Doing so makes it unobvious that there is relevant code there. This is especially important for early returns.)

Example:
```kotlin
// BAD:
if (notReady) return;

// GOOD:
if (notReady)
  return;

// ALSO GOOD:
if (notReady) {
  return;
}
```

## Align expressions
Where possible, subexpressions on different lines should be aligned, to make the structure of the expression easier. When doing this with a return statement chaining || or && operators, consider putting the operators on the left hand side instead of the right hand side.

```kotlin
// BAD:
if (foo.foo.foo + bar.bar.bar * baz - foo.foo.foo * 2 +
    bar.bar.bar * 2 * baz > foo.foo.foo) {
  // ...
}

// GOOD (notice how it makes it obvious that this code can be simplified):
if (foo.foo.foo     + bar.bar.bar     * baz -
    foo.foo.foo * 2 + bar.bar.bar * 2 * baz   > foo.foo.foo) {
  // ...
}
// After simplification, it fits on one line anyway:
if (bar.bar.bar * 3 * baz > foo.foo.foo * 2) {
  // ...
}
```

```kotlin
// BAD:
return foo.x == x &&
    foo.y == y &&
    foo.z == z;

// GOOD:
return foo.x == x &&
       foo.y == y &&
       foo.z == z;

// ALSO GOOD:
return foo.x == x
    && foo.y == y
    && foo.z == z;
```

# Packages
As per normal Dart conventions, a package should have a single import that reexports all of its API.
 - For example, rendering.dart exports all of lib/src/rendering/*.dart
If a package uses, as part of its exposed API, types that it imports from a lower layer, it should reexport those types.
 - For example, material.dart reexports everything from widgets.dart. Similarly, the latter reexports many types from rendering.dart, such as BoxConstraints, that it uses in its API. On the other hand, it does not reexport, say, RenderProxyBox, since that is not part of the widgets API.

For the rendering.dart library, if you are creating new RenderObject subclasses, import the entire library. If you are only referencing specific RenderObject subclasses, then import the rendering.dart library with a show keyword explicitly listing the types you are importing. This latter approach is generally good for documenting why exactly you are importing particularly libraries and can be used more generally when importing large libraries for very narrow purposes.

By convention, dart:ui is imported using import 'dart:ui' show
…​; for common APIs (this isn’t usually necessary because a lower level will have done it for you), and as import 'dart:ui' as ui show
…​; for low-level APIs, in both cases listing all the identifiers being imported. See basic_types.dart in the painting package for details of which identifiers we import which way. Other packages are usually imported undecorated unless they have a convention of their own (e.g. path is imported as path).

As a general rule, when you have a lot of constants, wrap them in a class. For examples of this, see lib/src/material/colors.dart.

## Features we expect every widget to implement
Now that the Flutter framework is mature, we expect every new widget to implement all of the following:

 1.full accessibility, so that on both Android and iOS the widget works with the native accessibility tools.

 2.full localisation with default translations for all our default languages.

 3.full support for both right-to-left and left-to-right layouts, driven by the ambient Directionality.

 4.full support for text scaling up to at least 3.0x.

 5.documentation for every member; see the section above for writing prompts to write documentation.

 6.good performance even when used with large amounts of user data.

 7.a complete lifecycle contract with no resource leaks (documented, if it differs from usual widgets).

 8.tests for all the above as well as all the unique functionality of the widget itself.

 9.It’s the job of the programmer to provide these before submitting a PR.

 10.It’s the job of the reviewer to check that all these are present when reviewing a PR.

 # Deciding where to put code
 As a general rule, if a feature is entirely self-contained (not requiring low-level integration into the Flutter framework) and is not something with particularly wide appeal, we would encourage that that feature be provided as a package. We have two main kinds of packages that are maintained by the Flutter team, each with their own repository: plugins, which provide access to platform features and therefore include Java or ObjectiveC code as well, and regular packages, which are pure Dart. Packages can also be written and maintained by people outside the Flutter team. Packages are published to pub.