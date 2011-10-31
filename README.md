## JavaScript Style Guide - Abstract
I see a lot of new or existing JavaScript developers are not coding in the proper way. I always see things which can be improved on their codebase, but I can't help them much because I moved to the most busiest Ruby team. I understand that JavaScript is the most misunderstood language, but many people is able to catch it up at the beginner level quite easily in my office.

Two years before I left JavaScript, I was intended to do presentation about what are the most common mistakes in the my office. However, I failed to do it because of some reasons. A few months ago I see there are a [Ruby](#https://github.com/bbatsov/ruby-style-guide) and [Rails](#https://github.com/bbatsov/rails-style-guide) Style Guide. I think I should do one on JavaScript Style Guide.

There are many sources I've combined into the below documents. You could see the link for more details in each topic and in the References section. The main ones are from Google Javascript Style Guide, JavaScript Patterns from Addy Osmani, JavaScript Object-Oriented Programming from Mozilla. Here, I took most of the good parts and write up in a bit more details to clarify.

Anyway, welcome your feedbacks. (The work is in progress.)

## Table of Contents

* [JavaScript Language Rules](#language)
    * [var](#var)
    * [Constants](#constants)
    * [Semicolons](#semicolons)
    * [Nested functions](#nested)
    * [Function declarations within blocks](#function)
    * [Wrapper objects for primitive types](#wrapper)
    * [Method definitions](#method)
    * [eval()](#eval)
    * [with() {}](#with)
    * [this](#this)
    * [for-in loop](#for)
    * [Associative Arrays](#associative)
    * [Multiline string literals](#multiline)
    * [Array and Object literals](#array)
    * [Modifying prototypes of builtin objects](#modifying)
    * [Closure (working in progress)](#closure)
* [JavaScript Style Rules](#style)
    * [Naming](#naming)
    * [Code formatting](#formatting)
    * [String](#string)
    * [JavaScript tidbits](#tidbits)
* [JavaScript Object Oriented Programming](#object)
    * [Constructor](#constructor)
    * [Private Members](#private)
    * [Static Members](#static)
    * [Inheritance](#inheritance)
    * [Mixin](#mixin)
* [JavaScript Common Patterns](#patterns)
    * [Module Pattern](#module)
    * [Revealing Module Pattern](#revealing)
* [References](#references)

<a name="language"/>
# JavaScript Language Rules

<a name="var"/>
## var
* Always declare variables with var.
* If you forget to declare with var keyword, that variable will be placed in the global context. Sometimes, it's hard to debug the problems.

    ```JavaScript
    function test() {
      a = "hello";
    }
    alert(a);
    ```

<a name="constants"/>
## Constants
* Use NAMES_LIKE_THIS for constants.
* As for the const keyword, Internet Explorer doesn't parse it, so don't use it.

    ```JavaScript
    goog.example.SECONDS_IN_A_MINUTE = 60;
    ```

<a name="semicolons"/>
## Semicolons
* Always use semicolons. Always end your assignment statements with semicolons.

    ```JavaScript
    MyClass.prototype.myMethod = function () {
      return 42;
    };
    
    var x = {
      'i': 1,
      'j': 2
    };
    
    var THINGS_TO_EAT = [apples, oysters, sprayOnCheese];
    ```
<a name="nested">
## Nested functions
* Nested functions can be very useful for the task of hiding helper functions.

    ```JavaScript
    function hypotenuse(a, b) {
      function square(x) { return x*x; }
      
      return Math.sqrt(square(a) + square(b));
    }
    ```

<a name="function">
## Function declarations within blocks
* Never declare function within blocks. Do not do this:

    ```JavaScript
    if (x) {
      function foo() {}
    }
    ```

* It is not part of ECMAScript. Do this instead:

    ```JavaScript
    if (x) {
      var foo = function () {}
    }
    ```

<a name="wrapper">
## Wrapper objects for primitive types
* Don't use wrapper objects for primitive types.

    ```JavaScript
    var x = new Boolean(false);
    if (x) {
      alert('hi');  // Shows 'hi'.
    }
    ```

* However, it is very useful for casting things to number, string and boolean.

    ```JavaScript
    var x = Boolean(0);
    if (x) {
      alert('hi');  // This will never be alerted.
    }
    typeof Boolean(0) == 'boolean';
    typeof new Boolean(0) == 'object';
    ```

<a name="method">
## Method definitions
* The preferred style of declaring method definition is:

    ```JavaScript
    Foo.prototype.bar = function () {
      /* ... */
    };
    ```

<a name="eval">
## eval()
* Use eval only for parsing ajax response.

    ```JavaScript
    var userOnline = false;
    var user = 'nusrat';
    var xmlhttp = new XMLHttpRequest();
    xmlhttp.open('GET', 'http://chat.google.com/isUserOnline?user=' + user, false);
    xmlhttp.send('');
    // Server returns:
    // userOnline = true;
    if (xmlhttp.status == 200) {
      eval(xmlhttp.responseText);
    }
    ```

<a name="with">
## with() {}
* Local variables may collide with the property's object of the with statement. It would be a good idea not to use the with statement because it may change the meaning of your program.

    ```JavaScript
    with (foo) {
      var x = 3;
      return x;
    }
    ```

    Instead of this:

    ```JavaScript
    with (ooo.eee.oo.ah_ah.ting.tang.walla.walla) {
      bing = true;
      bang = true;
    }
    ```

    you could do this:

    ```JavaScript
    var o = ooo.eee.oo.ah_ah.ting.tang.walla.walla;
    o.bing = true;
    o.bang = true;
    ```

<a name="this">
## this
* The semantics of this can be tricky. At times, it refers to:
  * the global object (in most places)

    ```JavaScript
    function foo() {
      this.bar = "hello"
    };

    foo();
    alert(bar);  // Shows 'hello'.
    ```

  * the scope of the caller (in eval)

    ```JavaScript
    foo = {
      name: "hi",
      execute: function () {
        eval("alert(this.name)");
      }
    }

    foo.execute();  // Show 'hi'.
    ```

  * a node in the DOM tree

    ```JavaScript
    <div onclick="alert(this.innerHTML)">Hello World</div>

    // Show 'Hello World' when click that div.
    ```

  * a newly created object (in a constructor)

    ```JavaScript
    var Foo = function(name) {
      this.name = name;  // this refers to the new object
    };
    ```

  * some other object (if function was call()ed or apply()ed)

    ```JavaScript
    var x = 10;
    var o = { x: 15 };

    function f() {
      alert(this.x);
    }

    f();  // Show '10'
    f.call(o);  // Show '15'
    ```

* It's often to get it wrong, limit its use to those places where it is required:
  * in constructors
  * in methods of objects (including in the creation of closures)

<a name="for">
## for-in loop
* Only for iterating over keys in an object/map/hash, NOT in an Array.
* for-in does not loop from 0 to length - 1 but over all the present keys in the object and its prototype chain.

    ```JavaScript
    function printArray(arr) {
      for (var key in arr) {
        print(arr[key]);
      }
    }

    printArray([0,1,2,3]);  // This works.

    var a = new Array(10);
    printArray(a);  // This is wrong.

    a = document.getElementsByTagName('*');
    printArray(a);  // This is wrong.

    a = [0,1,2,3];
    a.buhu = 'wine';
    printArray(a);  // This is wrong again.

    a = new Array;
    a[3] = 3;
    printArray(a);  // This is wrong again.
    ```

* Always use normal for loops when using arrays.

    ```JavaScript
    function printArray(arr) {
      for (var i = 0, l = arr.length; i < l; i++) {
        print(arr[i]);
      }
    }
    ```

<a name="associative">
## Associative Arrays
* Never use Array as a map/hash/associative array (more precisely you are not allowed to use non number indexes for arrays).
* If you need a map/hash use Object instead of Array in these cases because the features that you want are actually features of Object and not of Array.

<a name="multiline">
## Multiline string literals
* Do not do this:

    ```JavaScript
    var myString = 'A rather long string of English text, an error message \
                    actually that just keeps going and going -- an error \
                    message to make the Energizer bunny blush (right through \
                    those Schwarzenegger shades)! Where was I? Oh yes, \
                    you\'ve got an error and all the extraneous whitespace is \
                    just gravy.  Have a nice day.';
    ```

* Use string concatenation instead:

    ```JavaScript
    var myString = 'A rather long string of English text, an error message ' +
        'actually that just keeps going and going -- an error ' +
        'message to make the Energizer bunny blush (right through ' +
        'those Schwarzenegger shades)! Where was I? Oh yes, ' +
        'you\'ve got an error and all the extraneous whitespace is ' +
        'just gravy.  Have a nice day.';
    ```

<a name="array">
## Array and Object literals
* Use Array and Object literals instead.
* Array constructors are error-prone due to their arguments.

    ```JavaScript
    // Length is 3.
    var a1 = new Array(x1, x2, x3);

    // Length is 2.
    var a2 = new Array(x1, x2);

    // If x1 is a number and it is a natural number the length will be x1.
    // If x1 is a number but not a natural number this will throw an exception.
    // Otherwise the array will have one element with x1 as its value.
    var a3 = new Array(x1);

    // Length is 0.
    var a4 = new Array();
    ```

* To avoid these kinds of weird cases, always use the more readable array literal.

    ```JavaScript
    var a = [x1, x2, x3];
    var a2 = [x1, x2];
    var a3 = [x1];
    var a4 = [];
    ```
* Object constructors don't have the same problems, but for readability and consistency object literals should be used.

    ```JavaScript
    var o = new Object();

    var o2 = new Object();
    o2.a = 0;
    o2.b = 1;
    o2.c = 2;
    o2['strange key'] = 3;
    ```

    Should be written as:

    ```JavaScript
    var o = {};

    var o2 = {
      a: 0,
      b: 1,
      c: 2,
      'strange key': 3
    };
    ```

<a name="modifying">
## Modifying prototypes of builtin objects
* Modifying builtins like Object.prototype and Array.prototype are strictly forbidden.
* Modifying other builtins like Function.prototype is less dangerous but still leads to hard to debug issues in production and should be avoided.

<a name="style"/>
# JavaScript Style Rules

<a name="naming"/>
## Naming
* In general, use:
  * functionNamesLikeThis,
  * variableNamesLikeThis,
  * ClassNamesLikeThis,
  * EnumNamesLikeThis,
  * methodNamesLikeThis,
  * SYMBOLIC_CONSTANTS_LIKE_THIS,
  * onEventHandlerFunction

* Properties and methods
  * Private members should be named with a trailing or prefixed underscore.
  * Like public members, protected members should be named without underscore.

    ```JavaScript
    // File 1.

    /** @constructor */
      AA_PublicClass = function () {
    };

    /** @private */
    AA_PublicClass.staticPrivateProp_ = 1;

    /** @private */
    AA_PublicClass.prototype.privateProp_ = 2;

    /** @protected */
    AA_PublicClass.staticProtectedProp = 31;

    /** @protected */
    AA_PublicClass.prototype.protectedProp = 4;

    // File 2.

    /**
     * @return {number} The number of ducks we've arranged in a row.
     */
    AA_PublicClass.prototype.method = function () {
      // Legal accesses of these two properties.
      return this.privateProp_ + AA_PublicClass.staticPrivateProp_;
    };

    // File 3.

    /**
     * @constructor
     * @extends {AA_PublicClass}
     */
    AA_SubClass = function () {
      // Legal access of a protected static property.
      AA_PublicClass.staticProtectedProp = this.method();
    };
    goog.inherits(AA_SubClass, AA_PublicClass);

    /**
     * @return {number} The number of ducks we've arranged in a row.
     */
    AA_SubClass.prototype.method = function () {
      // Legal access of a protected instance property.
      return this.protectedProp;
    };
    ```

* Namespacing
  * Use namespaces for global code to prevent collisions during code integration from two projects.
  * Always prefix with a unique namespace name.

    ```JavaScript
    var sloth = {};

    sloth.sleep = function () {
      ...
    };
    ```

  * Alias long type names to improve readability
    Use local aliases, the last part of the type, for fully-qualified types if doing so improves readability.

    ```JavaScript
    /**
     * @constructor
     */
    some.long.namespace.MyClass = function () {
    };

    /**
     * @param {some.long.namespace.MyClass} a
     */
    some.long.namespace.MyClass.staticHelper = function(a) {
      ...
    };
    ```

    Never create aliases in the global scope. Use them only in function blocks.

    ```JavaScript
    myapp.main = function () {
      var MyClass = some.long.namespace.MyClass;
      var staticHelper = some.long.namespace.MyClass.staticHelper;
      staticHelper(new MyClass());
    };
    ```

    Do not alias namespaces.

    ```JavaScript
    myapp.main = function () {
      var namespace = some.long.namespace;
      namespace.MyClass.staticHelper(new namespace.MyClass());
    };
    ```

* Filenames should be all lowercase. Filenames should end in .js, and should contain no punctuation except for - or _ (prefer - to _).

<a name="formatting">
## Code formatting
* Always use braces on if/else even though it has only statement.
* Always start your curly braces on the same line.

    ```JavaScript
    if (something) {
      // ...
    } else {
      // ...
    }
    ```

* Single-line array and object initializers are allowed when they fit on a line:

    ```JavaScript
    var arr = [1, 2, 3];  // No space around [].
    var obj = {a: 1, b: 2, c: 3};  // No space around {}.
    ```

* Multiline array initializers and object initializers are indented 2 spaces.

    ```JavaScript
    // Object initializer.
    var inset = {
      top: 10,
      right: 20,
      bottom: 15,
      left: 12
    };

    // Array initializer.
    this.rows_ = [
      '"Slartibartfast" <fjordmaster@magrathea.com>',
      '"Zaphod Beeblebrox" <theprez@universe.gov>',
      '"Ford Prefect" <ford@theguide.com>',
      '"Arthur Dent" <has.no.tea@gmail.com>',
      '"Marvin the Paranoid Android" <marv@googlemail.com>',
      'the.mice@magrathea.com'
    ];

    // Used in a method call.
    goog.dom.createDom(goog.dom.TagName.DIV, {
      id: 'foo',
      className: 'some-css-class',
      style: 'display:none'
    }, 'Hello, world!');
    ```

* Always prefer non-aligned initialization. For example:

    ```JavaScript
    CORRECT_Object.prototype = {
      a: 0,
      b: 1,
      lengthyName: 2
    };
    ```

    Not like this:

    ```JavaScript
    WRONG_Object.prototype = {
      a          : 0,
      b          : 1,
      lengthyName: 2
    };
    ```

* Whitespace
  * No tabs. Indent blocks with two spaces.
  * Spaces after commas and semicolons.
  * Separate binary operators with spaces.
  * Space after keywords (if, for, etc).
  * No trailing space on each line.
  * Spaces are NOT necessary inside brackets.

    ```JavaScript
    myFunction(a, b)
    ```
  * Use newlines to group logically related pieces of code or to seperate between block definitions.

    ```JavaScript
    doSomethingTo(x);
    doSomethingElseTo(x);
    andThen(x);

    nowDoSomethingWith(y);

    andNowWith(z);
    ```

* Indentation

    ```JavaScript
    // Four-space, wrap at 80.  Works with very long function names, survives
    // renaming without reindenting, low on space.
    goog.foo.bar.doThingThatIsVeryDifficultToExplain = function(
        veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
        tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
      // ...
    };

    // Four-space, one argument per line.  Works with long function names,
    // survives renaming, and emphasizes each argument.
    goog.foo.bar.doThingThatIsVeryDifficultToExplain = function(
        veryDescriptiveArgumentNumberOne,
        veryDescriptiveArgumentTwo,
        tableModelEventHandlerProxy,
        artichokeDescriptorAdapterIterator) {
      // ...
    };

    // Parenthesis-aligned indentation, wrap at 80.  Visually groups arguments,
    // low on space.
    function foo(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
                 tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
      // ...
    }

    // Parenthesis-aligned, one argument per line.  Visually groups and
    // emphasizes each individual argument.
    function bar(veryDescriptiveArgumentNumberOne,
                 veryDescriptiveArgumentTwo,
                 tableModelEventHandlerProxy,
                 artichokeDescriptorAdapterIterator) {
      // ...
    }
    ```

    ```JavaScript
    prefix.something.reallyLongFunctionName('whatever', function(a1, a2) {
      if (a1.equals(a2)) {
        someOtherLongFunctionName(a1);
      } else {
        andNowForSomethingCompletelyDifferent(a2.parrot);
      }
    });

    var names = prefix.something.myExcellentMapFunction(
        verboselyNamedCollectionOfItems,
        function(item) {
          return item.name;
        });
    ```

    ```JavaScript
    someWonderfulHtml = '' +
                        getEvenMoreHtml(someReallyInterestingValues, moreValues,
                                        evenMoreParams, 'a duck', true, 72,
                                        slightlyMoreMonkeys(0xfff)) +
                        '';

    thisIsAVeryLongVariableName =
        hereIsAnEvenLongerOtherFunctionNameThatWillNotFitOnPrevLine();

    thisIsAVeryLongVariableName = 'expressionPartOne' + someMethodThatIsLong() +
        thisIsAnEvenLongerOtherFunctionNameThatCannotBeIndentedMore();

    someValue = this.foo(
        shortArg,
        'Some really long string arg - this is a pretty common case, actually.',
        shorty2,
        this.bar());

    var IOService = Components.classes["@mozilla.org/network/io-service;1"]
                              .getService(Components.interfaces.nsIIOService);

    if (searchableCollection(allYourStuff).contains(theStuffYouWant) &&
        !ambientNotification.isActive() && (client.isAmbientSupported() ||
                                            client.alwaysTryAmbientAnyways())) {
      ambientNotification.activate();
    }
    ```

* Binary and Ternary Operators

    ```JavaScript
    var x = a ? b : c;  // All on one line if it will fit.

    // Indentation +4 is OK.
    var y = a ?
        longButSimpleOperandB : longButSimpleOperandC;

    // Indenting to the line position of the first operand is also OK.
    var z = a ?
            moreComplicatedB :
            moreComplicatedC;
    ```

<a name="string">
## String
* Prefer ' over "
    For consistency single-quotes (') are preferred to double-quotes ("). This is helpful when creating strings that include HTML:

    ```JavaScript
    var msg = 'This is some HTML';
    ```

<a name="tidbits">
## JavaScript tidbits
* True and False Boolean Expressions
    The following are all false in boolean expressions:

      * null
      * undefined
      * '' the empty string
      * 0 the number

      But be careful, because these are all true:

      * '0' the string
      * [] the empty array
      * {} the empty object

    Instead of this:

    ```JavaScript
    while (x != null) {
    ```

    you can write this:

    ```JavaScript
    while (x) {
    ```

    Instead of this:

    ```JavaScript
    if (y != null && y != '') {
    ```

    you can write this:

    ```JavaScript
    if (y) {
    ```

* There are some unintuitive things:

    ```JavaScript
    Boolean('0') == true
    '0' != true

    0 != null
    0 == []
    0 == false

    Boolean(null) == false
    null != true
    null != false

    Boolean(undefined) == false
    undefined != true
    undefined != false

    Boolean([]) == true
    [] != true
    [] == false

    Boolean({}) == true
    {} != true
    {} != false
    ```

* Ternary Operator

    Instead of this:

    ```JavaScript
    if (val != 0) {
      return foo();
    } else {
      return bar();
    }
    ```

    you can write this:

    ```JavaScript
    return val ? foo() : bar();
    ```

    The ternary conditional is also useful when generating HTML:

    ```JavaScript
    var html = '<input type="checkbox"' +
        (isChecked ? ' checked' : '') +
        (isEnabled ? '' : ' disabled') +
        ' name="foo">';
    ```

* && and ||
  * ||, the default operator.
    Instead of this:

    ```JavaScript
    /** @param {*=} opt_win */
    function foo(opt_win) {
      var win;
      if (opt_win) {
        win = opt_win;
      } else {
        win = window;
      }
      // ...
    }
    ```

    you can write this:

    ```JavaScript
    /** @param {*=} opt_win */
    function foo(opt_win) {
      var win = opt_win || window;
      // ...
    }
    ```

  * "&&" is also useful for shortening code.
    Instead of this:

    ```JavaScript
    if (node) {
      if (node.kids) {
        if (node.kids[index]) {
          foo(node.kids[index]);
        }
      }
    }
    ```

    you could do this:

    ```JavaScript
    if (node && node.kids && node.kids[index]) {
      foo(node.kids[index]);
    }
    ```

* Use join() to Build Strings
    It's common to see this:

    ```JavaScript
    function listHtml(items) {
      var html = '<div class="foo">';
      for (var i = 0; i < items.length; ++i) {
        if (i > 0) {
          html += ', ';
        }
        html += itemHtml(items[i]);
      }
      html += '</div>';
      return html;
    }
    ```

    but this is slow in IE, you could better do this:

    ```JavaScript
    function listHtml(items) {
      var html = [];
      for (var i = 0; i < items.length; ++i) {
        html[i] = itemHtml(items[i]);
      }
      return '<div class="foo">' + html.join(', ') + '</div>';
    }
    ```

  Assigning values to an array is faster than using push().

<a name="object">
# JavaScript Object Oriented Programming
JavaScript doesn't support the concept of classes in nature. We need some javascript patterns to emulate this behaviour. Thanks to [Douglas Crockford](http://javascript.crockford.com/private.html).

<a name="constructor">
## Constructor and Public Members
* In JavaScript, constructors are just function, constructor function. We use this to create objects from this function. Usually, we capitalize the name of constructor function and use the 'new' operator to instantiate a new object.
* Inside a constructor, the keyword 'this' references the new object that's being created. We define  properties inside constructor and define methods inside the constructor's prototype.
* With this pattern, all methods are created off of the prototype, which means there is only one copy of each in memory, no matter how many instances you create.

    ```JavaScript
    function Car(model, year, miles) {

      // public attributes
      this.model = model;
      this.year = year;
      this.miles = miles;
    }

    /*
     Note here that we are using Object.prototype.newMethod rather than 
     Object.prototype so as to avoid redefining the prototype object.
     Public methods
    */
    Car.prototype.toString = function () {
      return this.model + " has done " + this.miles + " miles";
    };

    var civic = new Car("Honda Civic", 2009, 20000);
    var mondeo = new Car("Ford Mondeo", 2010, 5000);

    console.log(civic.toString());
    ```

<a name="private">
## Private Members
* There are two usual ways to implement private members:
  * through naming convention: an underscore is added to name of members. This signifies they are for internal use only.

    ```JavaScript
    function Car(model, year, miles) {

      // private members through convention
      this._model = model;
      this._year = year;
      this._miles = miles;
    }

    Car.prototype._setAttribute(name, value) {
        this["_" + name] = value;
    };

    Car.prototype.setModel = function(model) {
        this._setAttribute('model', model);
    };

    Car.prototype.getModel = function () {
      return this._model;
    };
    ```

  * through closure: the downside is that all members are not in the constructor's prototype object. Therefore, each methods are not shared between instances.

    ```JavaScript
    function Car(model, year, miles) {

      // private attributes and methods
      var _model = model;
      var _year = year;
      var _miles = miles;
      var _setModel = function(value) {
        // do some checking
        _model = value;
      };

      // public methods
      this.getModel = function () {
        return _model;
      };

      this.setModel = function(model) {
        _setModel(model);
      };
    }
    ```

<a name="static">
## Static Members

    ```JavaScript
    var Book = (function () {
      var numOfBooks = 0; // Private static attributes. 
      function checkIsbn(isbn) { // Private static method. ... } 

      // Return the constructor. 
      return function(newIsbn, newTitle, newAuthor) { 
        var isbn; // Private attributes.
        this.getIsbn = function () { // Privileged methods. 
          return isbn;
        };

        this.setIsbn = function(newIsbn) {
          if(!checkIsbn(newIsbn)) throw new Error('Book: Invalid ISBN.');
          isbn = newIsbn;
        }; 
        numOfBooks++; // Keep track of how many Books have been created 
        this.setIsbn(newIsbn);
      }
    })();

    Book.convertToTitleCase = function(inputString) { // Public static method.
      ...
    };
    Book.prototype = { // Public, non-privileged methods.
      display: function () {
        ...
      } 
    }; 
    ```

<a name="inheritance">
## Inheritance

    ```JavaScript
    function Employee(name, dept) {
      this.name = name || "";
      this.dept = dept || "";
    }

    function Engineer(name, machine) {

      // 1. calling the superclass's constructor
      Employee.call(this, name, "engineering");
      this.machine = machine;
    }

    // 2. setup the prototype chain
    Engineer.prototype = new Employee();
    ```
<a name="mixin">
## Mixin
* This is another way to make our code reuse without inheritance. In practice, you create an object that contains your general-purpose methods, and then use it to augment/extend other classes. It is generally not instantiated or called directly, instead it exists to provide methods to other classes.
* For more detail, check this [blog post](http://javascriptweblog.wordpress.com/2011/05/31/a-fresh-look-at-javascript-mixins/).

    ```JavaScript
    var circleFns = {
      area: function () {
        return Math.PI * this.radius * this.radius;
      },
      grow: function () {
        this.radius++;
      },
      shrink: function () {
        this.radius--;
      }
    };

    /*
      The extend method will simply copy all methods from destination to source.
      Here, make sure that method belongs to, not inherited methods, the destination object.
    */
    function extend(destination, source) {
      for (var k in source) {
        if (source.hasOwnProperty(k)) {
          destination[k] = source[k];
        }
      }
      return destination;
    }

    var RoundButton = function(radius, label) {
      this.radius = radius;
      this.label = label;
    };

    extend(RoundButton.prototype, circleFns);
    extend(RoundButton.prototype, buttonFns);
    ```

<a name="patterns">
# JavaScript Common Patterns
* Thanks to Addy Osmani, the author of this [online book](#http://addyosmani.com/resources/essentialjsdesignpatterns/book/) for his excellent write-up. I've learnt quite a lot from this.

<a name="module">
## The Module Pattern
* This is the most common pattern in JavaScript. It was originally formally defined by Douglas Crockford (JavaScript: The Good Parts).
* You probably have heard about "Global variables are evil", and this pattern allows us to define public/private members which means that you avoid polluting or clobbering the global namespaces. You will have a level of shielding from external entities accessing your 'hidden' information.

    ```JavaScript
    var someModule = (function () {

      //private attributes
      var privateVar = 5;

      //private methods
      var privateMethod = function () {
        return 'Private Test';
      };

      return {

        //public attributes
        publicVar: 10,

        //public methods
        publicMethod: function () {
          return ' Followed By Public Test ';
        },

        //let's access the private members
        getData: function () {
          return privateMethod() + this.publicMethod() + privateVar;
        }
      }
    })(); //the parent here cause the anonymous function to execute and return

    someModule.getData();
    ```

<a name="revealing">
## The Revealing Module Pattern
* Here is a slightly improved version - [Christian Heilmann’s Revealing Module pattern](http://christianheilmann.com/2007/08/22/again-with-the-module-pattern-reveal-something-to-the-world/)
* The benefits of this pattern is that it makes very clear at the end which of your functions and variables may be accessed publicly. Moreover, you are able to reveal private functions with different name.

    ```JavaScript
    var myRevealingModule = (function () {
      var name = 'John Smith';
      var age = 40;

      function updatePerson() {
        name = 'John Smith Updated';
      }
      function setPerson() {
         name = 'John Smith Set';
      }
      function getPerson() {
         return name;
      }

      return {
          set: setPerson,
          get: getPerson
      };
    })();

    // Sample usage:
    myRevealingModule.get();
    ```

<a name="references">
# References
* http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml
* http://addyosmani.com/resources/essentialjsdesignpatterns/book
* http://www.adequatelygood.com/2010/3/JavaScript-Module-Pattern-In-Depth
* https://developer.mozilla.org/en/Introduction_to_Object-Oriented_JavaScript
* https://developer.mozilla.org/en/JavaScript/A_re-introduction_to_JavaScript
* http://javascript.crockford.com/private.html
* http://javascriptweblog.wordpress.com/2011/05/31/a-fresh-look-at-javascript-mixins/
* http://greasemonkey.github.com/style.html
