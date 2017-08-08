# JSExcellent-Q-A
The Questions and answers from stackoverflow!

### [Get random item from JavaScript array](//stackoverflow.com/questions/5915096/get-random-item-from-javascript-array)

**Q:** 

How do I get random item form `items`?

    var items = Array(523,3452,334,31,...5346);

**A**:

    var item = items[Math.floor(Math.random() * items.length)];

### [Generate random number between two numbers in JavaScript](//stackoverflow.com/questions/4959975/generate-random-number-between-two-numbers-in-javascript)

**Q:**

Is there a way to generate a random number in a specified range (e.g. from `1` to `6`: `1`, `2`, `3`, `4`, `5`, or `6`) in JavaScript?

**A:**

If you wanted to get between `1` and `6`, you would calculate:

    Math.floor(Math.random() * 6) + 1  

Where:

- `1` is the start number
- `6` is the number of possible results `(1 + start (6) - end (1))`

What it does "extra" is it allows random intervals that do not start with `1`. So you can get a random number from `10` to `15` for example. Flexibility.

    function randomIntFromInterval(min,max){
        return Math.floor(Math.random() * (max - min + 1) + min);
    }

### [How to print a number with commas as thousands separators in JavaScript](//stackoverflow.com/questions/2901102/how-to-print-a-number-with-commas-as-thousands-separators-in-javascript)

**Q:**

I am trying to print an integer in JavaScript with commas as thousands separators. For example, I want to show the number `1234567` as `1,234,567`. How would I go about doing this?

**A:**

    function numberWithCommas(x) {
        return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")
    }

@neu-rah mentioned that this function adds commas in undesirable places if there are more than 3 digits after the decimal point. If this is a problem, you can use this function:

    function numberWithCommas(x) {
        var parts = x.toString().split(".");
        parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        return parts.join(".");
    }

### [Generate random string/characters in JavaScript](//stackoverflow.com/questions/1349404/generate-random-string-characters-in-javascript)

**Q:**

I want a 5 character string composed of characters picked randomly from the set `[a-zA-Z0-9]`. What's the best way to do this with JavaScript?

**A:**

    function makeid() {
        var text = "";
        var possible = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

        for (var i = 0; i < 5; i++)
            text += possible.charAt(Math.floor(Math.random() * possible.length));

            return text;
        }
    }

or:
    Math.random().toString(36).substring(7);

or: 
    Math.random().toString(36).substr(2, 5);

### [How to redirect to another webpage in JavaScript/jQuery?](//stackoverflow.com/questions/503093/how-to-redirect-to-another-webpage-in-javascript-jquery)

**Q:**

How can i redirect the user from one page to another using JavaScript or jQuery?

**A:**

If you want to simulate someone clicking on a link, use `location.href`.

If you want to simulate an HTTP redirect, use `location.replace`.

    // similar behavior as an HTTP redirect
    window.location.replace("http://stackoverflow.com");

    // similar behavior as clicking on a link
    window.location.href = "http://stackoverflow.com";

### [Safely turning a JSON string into an object](//stackoverflow.com/questions/45015/safely-turning-a-json-string-into-an-object)

**Q:**

Given a string of JSON data, how can you safely turn that string into a JavaScript object?

**A:**

    JSON.parse(jsonString);

Is a pure JavaScript approach so long as you can require a reasonably modern browser.

### [Object comparison in JavaScript](//stackoverflow.com/questions/1068834/object-comparison-in-javascript)

**Q:**

What is the best way to compare objects in JavaScript?

Example:

    var user1 = {name : "nerd", org: "dev"};
    var user2 = {name : "nerd", org: "dev"};
    var eq = user1 == user2;
    alert(eq); // gives false

I know that **two objects are equal if they refer to the exact same object**, but is there a way to check if they have the same attributes' values?

**A:**

Unfortunately there is no perfect way, unless you use `_proto_` recursively and access all non-enumerable properties, but this works in Firefox only.

So the best I can do is to guess usage scenarios.

**1) Fast and limited.**

Works when you have simple JSON-style objects without methods and DOM nodes inside:

    JSON.stringify(obj1) === JSON.stringify(obj2) 

The ORDER of the properties IS IMPORTANT, so this method will return false for following objects:

    x = {a: 1, b: 2};
    y = {b: 2, a: 1};

**2) Slow and more generic.**

Compares objects without digging into prototypes, then compares properties' projections recursively, and also compares constructors.

This is almost correct algorithm:

    function deepCompare () {
        var i, l, leftChain, rightChain;

        function compare2Objects (x, y) {
            var p;

            // remember that NaN === NaN returns false
            // and isNaN(undefined) returns true
            if (isNaN(x) && isNaN(y) && typeof x === 'number' && typeof y === 'number'){
                return true;
            }

            // Compare primitives and functions.     
            // Check if both arguments link to the same object.
            // Especially useful on the step where we compare prototypes
            if (x === y) {
                return true;
            }

            // Works in case when functions are created in constructor.
            // Comparing dates is a common scenario. Another built-ins?
            // We can even handle functions passed across iframes
            if ((typeof x === 'function' && typeof y === 'function') ||
            (x instanceof Date && y instanceof Date) ||
            (x instanceof RegExp && y instanceof RegExp) ||
            (x instanceof String && y instanceof String) ||
            (x instanceof Number && y instanceof Number)) {
                return x.toString() === y.toString();
            }

            // At last checking prototypes as good as we can
            if (!(x instanceof Object && y instanceof Object)) {
                return false;
            }

            if (x.isPrototypeOf(y) || y.isPrototypeOf(x)) {
                return false;
            }

            if (x.constructor !== y.constructor) {
                return false;
            }

            if (x.prototype !== y.prototype) {
                return false;
            }

            // Check for infinitive linking loops
            if (leftChain.indexOf(x) > -1 || rightChain.indexOf(y) > -1) {
                return false;
            }

            // Quick checking of one object being a subset of another.
            // todo: cache the structure of arguments[0] for performance
            for (p in y) {
                if (y.hasOwnProperty(p) !== x.hasOwnProperty(p)) {
                    return false;
                }
                else if (typeof y[p] !== typeof x[p]) {
                    return false;
                }
            }

            for (p in x) {
                if (y.hasOwnProperty(p) !== x.hasOwnProperty(p)) {
                    return false;
                }
                else if (typeof y[p] !== typeof x[p]) {
                    return false;
                }

                switch (typeof (x[p])) {
                    case 'object':
                    case 'function':

                        leftChain.push(x);
                        rightChain.push(y);

                        if (!compare2Objects (x[p], y[p])) {
                            return false;
                        }

                        leftChain.pop();
                        rightChain.pop();
                        break;

                    default:
                        if (x[p] !== y[p]) {
                            return false;
                        }
                        break;
                }
            }

            return true;
        }

        if (arguments.length < 1) {
            return true; //Die silently? Don't know how to handle such case, please help...
            // throw "Need two or more arguments to compare";
        }

        for (i = 1, l = arguments.length; i < l; i++) {

            leftChain = []; //Todo: this can be cached
            rightChain = [];

            if (!compare2Objects(arguments[0], arguments[i])) {
                return false;
            }
        }

        return true;
    }

Known issues (well, they have very low priority, probably you'll never notice them):

- objects with different prototype structure but same projection
- functions may have identical text but refer to different closures

Tests: passes tests are from [How to determine equality for two JavaScript objects?](//stackoverflow.com/a/16788517/139361).

### [How to randomize (shuffle) a JavaScript array?](//stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array)

**Q:**

I have one array like this:

    var arr1 = ["a", "b", "c", "d"];

How can I randomize / shuffle it?

**A:**

The de-facto unbiased shuffle algorithm is the Fisher-Yates (aka Knuth) Shuffle. See [https://github.com/coolaj86/knuth-shuffle](//github.com/coolaj86/knuth-shuffle)

You can see a [great visualization here](//bost.ocks.org/mike/shuffle/) (and the original post [linked to this](//sedition.com/perl/javascript-fy.html))

    function shuffle(array) {
        var currentIndex = array.length, temporaryValue, randomIndex;

        // While there remain elements to shuffle...
        while (0 !== currentIndex) {

            // Pick a remaining element...
            randomIndex = Math.floor(Math.random() * currentIndex);
            currentIndex -= 1;

            // And swap it with the current element.
            temporaryValue = array[currentIndex];
            array[currentIndex] = array[randomIndex];
            array[randomIndex] = temporaryValue;
        }

        return array;
    }

    // Used like so
    var arr = [2, 11, 37, 42];
    arr = shuffle(arr);
    console.log(arr);

Some more info [about the algorithm](//en.wikipedia.org/wiki/Fisher-Yates_shuffle) used.

### [Create a JavaScript array containing 1…N](//stackoverflow.com/questions/3746725/create-a-javascript-array-containing-1-n)

**Q:**

I'm looking for any alternatives to the below for creating a JavaScript array containing `1` through to `N` where `N` is only known at runtime.

    var foo = [];

    for (var i = 1; i <= N; i++) {
        foo.push(i);
    }

To me it feels like there should be a way of doing this without the loop.

**A:**

You can do so:

    var N = 10; 
    Array.apply(null, {length: N}).map(Number.call, Number)

> result: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

or with random values:

    Array.apply(null, {length: N}).map(Function.call, Math.random)

> result: [0.7082694901619107, 0.9572225909214467, 0.8586748542729765, 0.8653848143294454, 0.008339877473190427, 0.9911756622605026, 0.8133423360995948, 0.8377588465809822, 0.5577575915958732, 0.16363654541783035]

**Explanation**

First, note that `Number.call(undefined, N)` is equivalent to `Number(N)`, which just returns `N`. We'll use that fact later.

`Array.apply(null, [undefined, undefined, undefined])` is equivalent to `Array(undefined, undefined, undefined)`, which produces a three-element array and assigns `undefined` to each element.

How can you generalize that to N elements? Consider how [`Array()`](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) works, which goes something like this:

    function Array() {
        if ( arguments.length == 1 &&
            'number' === typeof arguments[0] &&
            arguments[0] >= 0 && arguments &&
            arguments[0] < 1 << 32 ) {
            return [ … ];  // array of length arguments[0], generated by native code
        }
        var a = [];
        for (var i = 0; i < arguments.length; i++) {
            a.push(arguments[i]);
        }
        return a;
    }

[Since ECMAScript 5](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply), `Function.prototype.apply(thisArg, argsArray)` also accepts a duck-typed array-like object as its second parameter. If we invoke `Array.apply(null, { length: N })`, then it will execute

    function Array() {
        var a = [];
        for (var i = 0; i < /* arguments.length = */ N; i++) {
            a.push(/* arguments[i] = */ undefined);
        }
        return a;
    }

Now we have an N-element array, with each element set to `undefined`. When we call [`.map(callback, thisArg)`](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) on it, each element will be set to the result of `callback.call(thisArg, element, index, array)`. Therefore, `[undefined, undefined, …, undefined].map(Number.call, Number)` would map each element to `(Number.call).call(Number, undefined, index, array)`, which is the same as `Number.call(undefined, index, array)`, which, as we observed earlier, evaluates to `index`. That completes the array whose elements are the same as their index.

Why go through the trouble of `Array.apply(null, {length: N})` instead of just `Array(N)`? After all, both expressions would result an an N-element array of undefined elements. The difference is that in the former expression, each element is explicitly set to undefined, whereas in the latter, each element was never set. According to the documentation of [`.map()`](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map):

> `callback` is invoked only for indexes of the array which have assigned values; it is not invoked for indexes which have been deleted or which have never been assigned values.

Therefore, `Array(N)` is insufficient; `Array(N).map(Number.call, Number)` would result in an uninitialized array of length N.

**Compatibility**

Since this technique relies on behaviour of `Function.prototype.apply()` specified in ECMAScript 5, it will [not work](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply#Description) in pre-ECMAScript 5 browsers such as Chrome 14 and Internet Explorer 9.

In ES6 using Array [`from()`](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) and [`keys()`](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/keys) methods.

    Array.from(Array(10).keys())
    //=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

Shorter version using [spread operator](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator).

    [...Array(10).keys()]
    //=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

In ES6 you can do:

    Array(N).fill().map((e,i)=>i+1);

> Changed `Array(45)` to `Array(N)` since you've updated the question.

### [Javascript add leading zeroes to date](https://stackoverflow.com/questions/3605214/javascript-add-leading-zeroes-to-date)

**Q:**

I've created this script to calculate the data for `10` days in advance in the formate of `dd/mm/yyyy`:

    var MyDate = new Date();
    var MyDateString = new Date();
    MyDate.setDate(MyDate.getDate() + 10);
    MyDateString = MyDate.getDate() + '/' + (MyDate.getMonth() + 1) + '/' + MyDate.getFullYear();

I need to have the date appear with leading zeroes on the day and month component by way of adding these rules to the script. I can't seem to get it to work.

    if (MyDate.getMonth > 10) getMonth = '0' + getMonth;

and

    if (MyDate.getDate < 10) get.Date = '0' + getDate;

If someone could show me where to insert these into the script I would be really appreciative.

**A:**

[Try this](//jsfiddle.net/xA5B7/):

    var MyDate = new Date();
    var MyDateString;

    MyDate.setDate(MyDate.getDate() + 20);
    MyDateString = ('0' + MyDate.getDate()).slice(-2) + '/' + ('0' + (MyDate.getMonth() + 1)).slice(-2) + '/' + MyDate.getFullYear();

**EDIT:**

To explain, `.slice(-2)` gives us the last two characters of the string.

So no matter what, we can add `"0"` to the day or month, and just ask for the last two since those are always the two we want.

So if the `MyDate.getMonth()` returns `9`, it will be:

    ("0" + "9") // Giving us "09"

so adding `.slice(-2)` on that gives us the last two characters which is:

    ("0" + "9").slice(-2);
    // "09"

But if `MyDate.getMonth()` returns `10`, it will be:

    ("0" + "10") // Giving us "010"

so adding `.slice(-2)` gives us the last two characters, or:

    ("0" + "10").slice(-2)
    // "10"

### [Why does parseInt(1/0, 19) return 18?](//stackoverflow.com/questions/11340673/why-does-parseint1-0-19-return-18)

**Q:**

I've an annoying problem in JavaScript.

    > parseInt(1 / 0, 19)
    > 18

Why does `parseInt` return `18`?

**A:**

The result of `1/0` is [`Infinity`](//developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Infinity).

[`parseInt`](//developer.mozilla.org/en/JavaScript/Reference/Global_Objects/parseInt) treats its first argument as a string which means first of all `Infinity.toString()` is called, producing the string `"Infinity"`. So it works the same as if you asked it to convert `"Infinity"` in base `19` to decimal.

Here are the digits in base `19` along with their decimal values:

    Base 19   Base 10 (decimal)
    ---------------------------
    0            0
    1            1
    2            2
    3            3
    4            4
    5            5
    6            6
    7            7
    8            8
    9            9
    a            10
    b            11
    c            12
    d            13
    e            14
    f            15
    g            16
    h            17
    i            18

What happens next is that `parseInt` scans the input `"Infinity"` to find which part of it can be parsed and stops after accepting the first `I` (because `n` is not a valid digit in base `19`).

Therefore it behaves as if you called `parseInt("I", 19)`, which converts to decimal `18` by the table above.

### [Is object empty?](//stackoverflow.com/questions/4994201/is-object-empty)

**Q:**

What is the fastest way to check if an object is empty or not?

Is there a faster and better way than this:

    function count_obj(obj){
        var i = 0;
        for(var key in obj){
            ++i;
        }

        return i;
    }

**A:**

I'm assuming that by empty you mean "has no properties of its own".

    // Speed up calls to hasOwnProperty
    var hasOwnProperty = Object.prototype.hasOwnProperty;

    function isEmpty(obj) {

        // null and undefined are "empty"
        if (obj == null) return true;

        // Assume if it has a length property with a non-zero value
        // that that property is correct.
        if (obj.length > 0)    return false;
        if (obj.length === 0)  return true;

        // If it isn't an object at this point
        // it is empty, but it can't be anything *but* empty
        // Is it empty?  Depends on your application.
        if (typeof obj !== "object") return true;

        // Otherwise, does it have any properties of its own?
        // Note that this doesn't handle
        // toString and valueOf enumeration bugs in IE < 9
        for (var key in obj) {
            if (hasOwnProperty.call(obj, key)) return false;
        }

        return true;
    }

Examples:

    isEmpty(""), // true
    isEmpty(33), // true (arguably could be a TypeError)
    isEmpty([]), // true
    isEmpty({}), // true
    isEmpty({length: 0, custom_property: []}), // true

    isEmpty("Hello"), // false
    isEmpty([1,2,3]), // false
    isEmpty({test: 1}), // false
    isEmpty({length: 3, custom_property: [1,2,3]}) // false

If you only need to handle ECMAScript5 browsers, you can use `Object.getOwnPropertyNames` instead of the `hasOwnProperty` loop:

    if (Object.getOwnPropertyNames(obj).length > 0) return false;

This will ensure that even if the object only has non-enumerable properties `isEmpty` will still give you the correct results.

[ECMA 5+:](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys#Browser_compatibility)

    // because Object.keys(new Date()).length === 0;
    // we have to do some additional check
    Object.keys(obj).length === 0 && obj.constructor === Object

Pre-ECMA 5:

    function isEmpty(obj) {
        for(var prop in obj) {
            if(obj.hasOwnProperty(prop))
                return false;
        }

        return JSON.stringify(obj) === JSON.stringify({});
    }

There's no easy way to do this. You'll have to loop over the properties explicitly:

    function isEmpty(obj) {
        for(var prop in obj) {
            if(obj.hasOwnProperty(prop))
                return false;
        }

        return true;
    }

If [ECMAScript 5 support](//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys#Browser_compatibility) is available, you can use `Object.keys()` instead:

    function isEmpty(obj) {
        return Object.keys(obj).length === 0;
    }

### [Unique values in an array](//stackoverflow.com/questions/1960473/unique-values-in-an-array)

**Q:**

I have an array of numbers that I need to make sure are unique. I found the code snippet below on the internet and it works great until the array has a zero in it. I found [this other script](//stackoverflow.com/questions/1890203/unique-for-arrays-in-javascript) here on SO that looks almost exactly like it, but it doesn't fail.

So for the sake of helping me learn, can someone help me determine where the prototype script is going wrong?

    Array.prototype.getUnique = function() {
        var o = {}, a = [], i, e;
        for (i = 0; e = this[i]; i++) {o[e] = 1};
        for (e in o) {a.push (e)};
        return a;
    }

**A:**

With JavaScript 1.6 / ECMAScript 5 you can use the native `filter` method of an Array in the following way to get an array with unique values:

    function onlyUnique(value, index, self) { 
        return self.indexOf(value) === index;
    }

    // usage example:
    var a = ['a', 1, 'a', 2, '1'];
    var unique = a.filter( onlyUnique ); // returns ['a', 1, 2, '1']

The native method `filter` will loop through the array and leave only those entries that pass the given callback function `onlyUnique`.

`onlyUnique` checks, if the given value is the first occurring. If not, it must be a duplicate and will not be copied.

This solution works without any extra library like jQuery or prototype.js.

It works for arrays with mixed value types too.

For old Browsers (`<ie9`), that do not support the native methods `filter` and `indexOf` you can find work arounds in the MDN documentation for `filter` and `indexOf`.

If you want to keep the last occurrence of a value, simple replace `indexOf` by `lastIndexOf`.

With ES6 it could be shorten to this:

    // usage example:
    var myArray = ['a', 1, 'a', 2, '1'];
    var unique = myArray.filter((v, i, a) => a.indexOf(v) === i); 

    // unique is ['a', 1, 2, '1']

Thanks to [Camilo Martin](//stackoverflow.com/users/124119/camilo-martin) for hint in comment.

ES6 has a native object `Set` to store unique values. To get an array with unique values you could do now this:

    var myArray = ['a', 1, 'a', 2, '1'];

    let unique = [...new Set(myArray)]; 

    // unique is ['a', 1, 2, '1']

The constructor of `Set` takes an iterable object, like Array, and the spread operator `...` transform the set back into an Array. Thanks to Lukas Liese for hint in comment.

### [RGB to Hex and Hex to RGB](//stackoverflow.com/questions/5623838/rgb-to-hex-and-hex-to-rgb)

**Q:**

How to convert colors in RGB format to Hex format and vice versa? For example, convert `'#0080C0'` to `(0, 128, 192)`.

**A:**

The following will do to the RGB to hex conversion and add any required zero padding:

    function componentToHex(c) {
        var hex = c.toString(16);
        return hex.length == 1 ? "0" + hex : hex;
    }

    function rgbToHex(r, g, b) {
        return "#" + componentToHex(r) + componentToHex(g) + componentToHex(b);
    }

    alert( rgbToHex(0, 51, 255) ); // #0033ff

Converting the other way:

    function hexToRgb(hex) {
        var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
        return result ? {
            r: parseInt(result[1], 16),
            g: parseInt(result[2], 16),
            b: parseInt(result[3], 16)
        } : null;
    }

    alert( hexToRgb("#0033ff").g ); // "51";

Finally, an alternative version of `rgbToHex()`, as discussed in @casablanca's answer and suggested in the comments by @cwolves:

    function rgbToHex(r, g, b) {
        return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
    }

**Update 3 December 2012:**

Here's a version of `hexToRgb()` that also parses a shorthand hex triplet such as `"#03F"`:

    function hexToRgb(hex) {
        // Expand shorthand form (e.g. "03F") to full form (e.g. "0033FF")
        var shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i;
        hex = hex.replace(shorthandRegex, function(m, r, g, b) {
            return r + r + g + g + b + b;
        });

        var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
        return result ? {
            r: parseInt(result[1], 16),
            g: parseInt(result[2], 16),
            b: parseInt(result[3], 16)
        } : null;
    }

    alert( hexToRgb("#0033ff").g ); // "51";
    alert( hexToRgb("#03f").g ); // "51";

### [Javascript reduce on array of objects](//stackoverflow.com/questions/5732043/javascript-reduce-on-array-of-objects)

**Q:**

Say I want to sum `a.x` for each element in `arr`.

    arr = [{x:1},{x:2},{x:4}]
    arr.reduce(function(a,b){return a.x + b.x})
    >> NaN

I have cause to believe that `a.x` is `undefined` at some point.

The following works fine

    arr = [1,2,4]
    arr.reduce(function(a,b){return a + b})
    >> 7

What am I doing wrong in the first example?

**A:**

After the first iteration your're returning a number and then trying to get property `x` of it to add to the next object which is `undefined` and maths involving `undefined` results in `NaN`.

try returning an object contain an `x` property with the sum of the x properties of the parameters:

    var arr = [{x:1},{x:2},{x:4}];

    arr.reduce(function (a, b) {
    return {x: a.x + b.x}; // returns object with property x
    })

    // ES6
    arr.reduce((a, b) => ({x: a.x + b.x}));

    // -> {x: 7}

Explanation added from comments:

The return value of each iteration of `[].reduce` used as the a variable in the next iteration.

Iteration 1: `a = {x:1}`, `b = {x:2}`, `{x: 3}` assigned to `a` in Iteration 2

Iteration 2: `a = {x:3}`, `b = {x:4}`.

The problem with your example is that you're returning a number literal.

    function (a, b) {
    return a.x + b.x; // returns number literal
    }

Iteration 1: `a = {x:1}`, `b = {x:2}`, `// returns 3` as `a` in next iteration

Iteration 2: `a = 3`, `b = {x:2}` returns `NaN`

A number literal `3` does not (typically) have a property called `x` so it's `undefined` and  `undefined + b.x` returns `NaN` and `NaN + <anything>` is always `NaN`

> Clarification: I prefer my method over the other top answer in this thread as I disagree with the idea that passing an option parameter to reduce with a magic number to get out a number primitive is cleaner. It may result in fewer lines written but imo it is less readable.

