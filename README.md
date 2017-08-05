# JSExcellent-Q-A
The Questions and answers from stackoverflow!

#### [Get random item from JavaScript array](//stackoverflow.com/questions/5915096/get-random-item-from-javascript-array)

**Q:** 

How do I get random item form `items`?

    var items = Array(523,3452,334,31,...5346);

**A**:

    var item = items[Math.floor(Math.random() * items.length)];

#### [Generate random number between two numbers in JavaScript](//stackoverflow.com/questions/4959975/generate-random-number-between-two-numbers-in-javascript)

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

#### [How to print a number with commas as thousands separators in JavaScript](//stackoverflow.com/questions/2901102/how-to-print-a-number-with-commas-as-thousands-separators-in-javascript)

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

#### [Generate random string/characters in JavaScript](//stackoverflow.com/questions/1349404/generate-random-string-characters-in-javascript)

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

#### [How to redirect to another webpage in JavaScript/jQuery?](//stackoverflow.com/questions/503093/how-to-redirect-to-another-webpage-in-javascript-jquery)

**Q:**

How can i redirect the user from one page to another using JavaScript or jQuery?

**A:**

If you want to simulate someone clicking on a link, use `location.href`.

If you want to simulate an HTTP redirect, use `location.replace`.

    // similar behavior as an HTTP redirect
    window.location.replace("http://stackoverflow.com");

    // similar behavior as clicking on a link
    window.location.href = "http://stackoverflow.com";

#### [Safely turning a JSON string into an object](//stackoverflow.com/questions/45015/safely-turning-a-json-string-into-an-object)

**Q:**

Given a string of JSON data, how can you safely turn that string into a JavaScript object?

**A:**

    JSON.parse(jsonString);

Is a pure JavaScript approach so long as you can require a reasonably modern browser.

#### [Object comparison in JavaScript](//stackoverflow.com/questions/1068834/object-comparison-in-javascript)

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

#### [How to randomize (shuffle) a JavaScript array?](//stackoverflow.com/questions/2450954/how-to-randomize-shuffle-a-javascript-array)

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

#### [Create a JavaScript array containing 1…N](//stackoverflow.com/questions/3746725/create-a-javascript-array-containing-1-n)

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