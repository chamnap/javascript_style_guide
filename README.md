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
    MyClass.prototype.myMethod = function() {
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
      var foo = function() {}
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
    Foo.prototype.bar = function() {
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
      execute: function() {
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
    ```JavaScript

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

<a name="modify">
## Modifying prototypes of builtin objects
* Modifying builtins like Object.prototype and Array.prototype are strictly forbidden.
* Modifying other builtins like Function.prototype is less dangerous but still leads to hard to debug issues in production and should be avoided.
