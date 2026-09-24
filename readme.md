This is a library that contains helpful features and code that I have extracted while working on my other projects.

# Library Feature Documentation
## Custom Testing Framework
### Creating Units and Tests
For basic usage, named tests can be created from a function:

```javascript
testing.addUnit("My unit", {
	"test case 1": () => {
		// Test code here...
	},
	"test case 2": () => {
		// Test code here...
	}
});
```

It is also possible to generate table-driven tests using an array of inputs and expected outputs:

```javascript
testing.addUnit("My function", myFunction, [
	["input 1 argument 1", "input 1 argument 2", "expected output 1"],
	["input 2", "expected output 2"]
]);
```

This will auto-generate names for the tests, formatted like `f(1) should return 2`. However, you can also provide custom names by passing in an object instead of an array:

```javascript
testing.addUnit("My function", myFunction, {
	"test case 1": ["input 1 argument 1", "input 1 argument 2", "expected output 1"],
	"test case 2": ["input 2", "expected output 2"]
});
```

A warning is logged if a test suite is created with no tests.

### Assertions via `expect`
Assertions can be implemented in natural language using the `expect` syntax. The following assertions are supported:
- Equality checks:
	- `expect(foo).toEqual(bar)`: asserts that the arguments are equal. Equality is checked using the `===` operator, but with the following exceptions:
		- If both values are `NaN`, they are considered equal to each other.
		- If the first value is an object, the two objects are compared using a deep equality algorithm. Custom equality logic can be provided by overriding the `equals` method.
		- If both values are `number`s or `bigint`s, their numerical value is compared (so `number`s and `bigint`s can be considered equal).
	- `expect(foo).toStrictlyEqual(bar)`: same as the above, but with none of the special cases except for `NaN == NaN`.
	- `expect(foo).toNotEqual(bar)`: asserts that the arguments are not equal in the sense used by `toEqual` (see above).
	- `expect(foo).toNotStrictlyEqual(bar)`: asserts that the arguments are not equal in the sense used by `toStrictlyEqual` (see above).
- Truth and falsity:
	- `expect(foo).toBeTrue()`: asserts that the argument is true, using strict (`===`) comparison.
	- `expect(foo).toBeFalse()`: asserts that the argument is false, using strict (`===`) comparison.
	- `expect(foo).toBeTruthy()`: asserts that the argument is [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy).
	- `expect(foo).toBeFalsy()`: asserts that the argument is [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy).
- Type checking:
	- `expect(foo).toBeNumeric()`: asserts that the argument is a `number` or `bigint`, and is not `NaN`.
	- `expect(foo).toBeAnObject()`: asserts that the argument is an object (and not `null`).
	- `expect(foo).toBeAnArray()`: asserts that the argument is an array.
- Numbers:
	- `expect(foo).toBePositive()`: asserts that the argument is a `number` or `bigint` that is strictly greater than zero.
	- `expect(foo).toBeNegative()`: asserts that the argument is a `number` or `bigint` that is strictly less than zero.
	- `expect(foo).toBeAnInteger()`: asserts that the argument is a `number` or `bigint` that is an integer.
	- `expect(foo).toBeBetween(x, y)`: asserts that the argument `foo` is greater than or equal to `x` and less than or equal to `y`.
	- `expect(foo).toBeStrictlyBetween(x, y)`: asserts that the argument `foo` is greater than `x` and less than `y`.
	- `expect(foo).toApproximatelyEqual(bar, tolerance)`: asserts that `foo` is within `tolerance` of `bar`. If not provided, `tolerance` defaults to $10^{-15}$.
	- `expect(foo).toBeGreaterThan(bar)`: asserts that `foo` is strictly greater than `bar`.
	- `expect(foo).toBeLessThan(bar)`: asserts that `foo` is strictly less than `bar`.
- Objects:
	- `expect(foo).toHaveProperties("bar", "qux")`: asserts that the given properties are [own properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Enumerability_and_ownership_of_properties) of `foo`.
	- `expect(foo).toOnlyHaveProperties("bar", "qux")`: asserts that asserts that the given properties are the only [own properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Enumerability_and_ownership_of_properties) of `foo`.
	- `expect(foo).toInstantiate(Foo)`: asserts that `foo` is an instance of the class or constructor function `Foo`, possibly with other subclasses in between.
	- `expect(foo).toDirectlyInstantiate(Foo)`: asserts that `foo` is an instance of the class or constructor function `Foo`, with no subclasses in between.
- Other:
	- `expect(foo).toThrow()`: asserts that the argument is a function that throws an error when called.
	- `expect(foo).toMatch(regex)`: asserts that the argument is a string that matches the given regular expression.

### Assertions via `assert`
If you don't want to use the natural language syntax, the library also provides helper methods to perform assertions directly:
- `testing.assert(foo)`: asserts that `foo` is truthy. The second argument can be used to provide a custom error message.
- `testing.refute(foo)`: asserts that `foo` is falsy.
- `testing.assertEqual(foo, bar)`: asserts that `foo` and `bar` are strictly equal (`===`).
- `testing.assertEqualApprox(foo, bar, tolerance)`: asserts that `foo` and `bar` are within `tolerance` of each other. If not provided, `tolerance` defaults to $10^{-12}$.
- `testing.assertEquivalent(foo, bar)`: asserts that the given values are strictly equal (`===`). However, if both inputs are objects, a deep equality algorithm is used. Custom equality logic can be provided by overriding the `equals` method.
- `testing.assertThrows(foo, expectedMsg)`: asserts that `foo` is a function that throws an error when called. If the second argument `expectedMsg` is provided, it also asserts that the error message thrown by `foo` equals `expectedMsg`. A regular expression can also be provided for `expectedMsg`; if so, it asserts that `foo` matches the regular expression.

### Running the Test Suite
Tests can be run via `testing.run()`. It supports the following arguments:
- When called with no arguments, all the tests are run.
- If the input is a string that matches the name of a unit, all the tests in that unit will be run.
- If the input is a string that matches the name of a test, that test will be run.
- If the input is a string that matches the name of a test formatted as in `Test name - unit name`, that test will be run.
- If the input is a `Test` or `TestUnit` object, the test or unit will be run.

The test output can be viewed in the JavaScript console. By default it only logs tests that failed (or "All Tests Passed" if all tests passed), plus the total time taken. For a more verbose output, you can call `testing.logTests()`, which logs all tests to the console, grouped into units.

After running the tests, statistics are available via the properties `testing.assertionsRun`, `testing.assertionsPassed`, and `testing.assertionsFailed`.

<!-- Tests can also be run via `testing.testAll()`, which supports additional options.
- The first (boolean) parameter `fastOnly` can be used to only run tests that are not tagged as slow. See [Running Only Fast Tests](#running-only-fast-tests).
- The second (boolean) parameter `debugFailed` runs all of the failed tests 1 additional time after logging the result to the console. This can be used to step into breakpoints after confirming that a test still fails. -->

## Data Structures

### `Sequence`
`Sequence` represents a finite or infinite sequence of values. Any value type is allowed, but the class is designed around storing sequences of numbers, and many methods are designed specifically for numeric values. Sequences can be constructed using the following formats:
- A generator function that yields each term of the sequence in order
- A function that takes in an integer $n$ and outputs the $n$th term of the sequence

Sequences can be marked as monotonic (only-increasing or only-decreasing) by passing in an optional second argument of `{ isMonotonic: true }`. Some of the `Sequence` methods below can only be called on sequences that have been marked as monotonic, and will throw an error otherwise.

Sequences can be iterated through using a standard `for`-`of` loop. When retrieving values from a sequence via this iteration or any of the methods below, the values in the sequence are cached to prevent duplicate computation.

Sequences have the following methods:
- `nthTerm(index)`: returns the term at the given zero-based index.
- `nextTerm(term)`: returns the next term after a known value in the sequence.
- Array-like methods:
	- `indexOf(searchTarget)`: finds the first matching index. If the sequence is marked as monotonic, `indexOf` will return `-1` after reaching a value larger than `searchTarget`. If the sequence is not marked as monotonic and does not contain `searchTarget`, then `indexOf` will not terminate.
	- `find(callback)`: returns the first value matching the predicate. The callback should take in up to three arguments: the current value, the current index, and the sequence being iterated over.
	- `filter(callback)`: returns a new `Sequence` consisting of all the values for which `callback` returns true. The callback can take in arguments as in the `find` method.
	- `map(callback)`: returns a new `Sequence` obtained by applying `callback` to each value in the sequence. The callback can take in arguments as in the `find` method.
	- `slice(minIndex, maxIndex)`: returns an array consisting of all elements between the given pair of indices, including `minIndex` but excluding `maxIndex`. The `maxIndex` can be `Infinity`, in which case it returns a `Sequence` instead of an array.
- `isIncreasing()` and `isDecreasing()`: returns whether the sequence is increasing or decreasing. Returns `null` if the sequence is not marked as monotonic.
- `termsBelow(maximum, inclusive)`: returns all terms below a given maximum for increasing sequences
- `entries()`: yields `[value, index]` pairs for iteration

The class also includes the following static properties and functions:
- `Sequence.POSITIVE_INTEGERS` - the sequence of all positive integers (not including 0).
- `Sequence.INTEGERS` - the sequence of all integers, in the order `0, 1, -1, 2, -2, ...`
- `Sequence.PRIMES` - the sequence of all prime numbers, calculated efficiently using the [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes).
- `Sequence.union(...sequences)`: merges monotonic sequences while preserving order.

### `Grid`
`Grid` is a lightweight wrapper for a 2D array. The constructor supports the following formats:
- `new Grid(width, height, defaultValue)`: creates a grid of the given dimensions with each entry having the given `defaultValue`
- `new Grid(rows)`: creates a grid from a 2D array. Throws an error if the rows are not all the same length.
- `new Grid(str)`: creates a grid from a multiline string, where each line in the string becomes a row in the grid.

Grids support the following methods:
- Basic functionality:
	- `get(x, y)` / `get({ x, y })`: reads a cell value
	- `set(x, y, value)` / `set({ x, y }, value)`: writes a cell value
	- `width()` and `height()`: returns the grid dimensions
- Array-like methods - for each of these, the callback takes can take in up to 4 arguments: the value, the x-position, the y-position, and the grid being iterated over.
	- `forEach(callback)`: calls the callback once for each element in the grid.
	- `some(callback)`: returns whether the callback returns true for some value in the grid.
	- `every(callback)`: returns whether the callback returns true for all values in the grid.
	- `find(callback)`: returns the first value for which the callback returns true, when searching left-to-right and then top-down.
	- `findPosition(callback)`: returns a `Vector` representing the first position for which the callback returns true.
	- `findPositions(callback)`: returns an array containing all `Vector`s for which the callback returned true.
	- `includes(obj)`: returns whether `obj` is present in the grid.
	- `map(callback)`: creates a new grid by applying the callback to each entry.
- Other methods:
	- `rotate(angle)`: rotates by multiples of 90 degrees, returning a new grid
	- `columns()`: returns the columns as arrays
	- `containsGrid(grid)`: checks whether `grid` is contained within the given grid as a contiguous sub-grid.
	- `removeRow(rowIndex)` and `removeColumn(columnIndex)`: return a modified grid without the given row or column
	- `entries()`: yields each value paired with its `Vector` position

### `Graph`
Undirected graphs can be constructed from:
- an array of nodes and connections, like `[[value, [connectedValues]], ...]`
- a list of values, optionally paired with a callback to build edges automatically
- another `Graph`
- a `Grid` (where adjacent cells are connected)

Graphs support the following methods:
- Nodes:
	- `size()`: returns the number of nodes
	- `values()`: returns an array containing the node values
	- `has(value)`: returns whether the value is in the graph
	- `add(value)`: inserts a node
	- `remove(value)`: deletes a node
- Edges:
	- `connections()`: returns the array of edges of the graph, represented as arrays with 2 elements
	- `areConnected(value1, value2)`: returns whether the two values are joined
	- `connect(value1, value2)`, `disconnect(value1, value2)`: add or remove edges
	- `setConnection(value1, value2, connected)`: adds or removes the edge between `value1` and `value2` based on whether `connected` is true or false.
	- `setConnections(callback)`: modifies all edges in the graph based on a callback function. The callback should take in two parameters (node values) and return whether or not they should be connected.
	- `toggleConnection(value1, value2)`: toggles the edge between the given two nodes.
- Connected components:
	- `componentContaining(value)`: returns a `Set` containing all nodes in the connected component containing the target value
	- `components()`: returns the set of all connected components (as `Set`s)

## Graphics and Geometry
### `Vector`
`Vector` is a 2D vector class used for positions, directions, and geometry calculations.

Vectors can be constructed using the following argument formats:
- `new Vector(x, y)` to create a vector from its components.
- `new Vector({ x, y })` to create a vector from an object.
- `new Vector({ angle, magnitude })` to create a vector from a magnitude and angle in degrees.
- `new Vector([x, y])` to create a vector from an array with 2 entries.
- `new Vector("left")`, `new Vector("right")`, `new Vector("up")`, or `new Vector("down")` to create a unit vector, where negative y values are considered to go upwards.
- `new Vector("(-1, 2)")` to parse a vector from a string.

Vectors support the following operations:
- Basic vector arithmetic:
	- `add()`: adds the entries of two vectors. Arguments can be given as two numbers `x` and `y`, or as an object with properties `x` and `y` (e.g. another `Vector`).
	- `subtract()`: subtracts the entries of two vectors. Arguments can be given as two numbers `x` and `y`, or as an object with properties `x` and `y` (e.g. another `Vector`).
	- `multiply(scalar)`: multiplies the entries in a vector by a scalar.
	- `divide(scalar)`: divide the entries in a vector by a scalar.
- Angle and magnitude:
	- `normalize()`: returns a unit vector in the same direction.
	- `distanceFrom()`: returns the distance to another vector or point.
- Dot products and projection:
	- `dotProduct(vector)`: computes the dot product $x_1 y_1 + x_2 y_2$.
	- `projection(vector)`: projects one vector orthogonally onto another.
	- `scalarProjection(vector)`: returns the magnitude of `projection(vector)`.
	- `rotateAbout(point, angle)` / `rotateAbout(x, y, angle)`: rotates the vector a given number of degrees counterclockwise around the provided point.
- `isAdjacentTo(vector)`: checks whether two vectors are adjacent with distance 1 between them (not including diagonals).

### `CanvasIO`
`CanvasIO` is a lightweight wrapper around an HTML canvas element that tracks mouse and keyboard state. It supports the following properties and methods:
- `activate()`: attaches the required mouse and keyboard listeners and, in `fill-parent` mode, sizes the canvas to its parent element.
- `canvas`: the DOM canvas element.
- `ctx`: the canvas rendering context.
- `mouse`: a `Vector` representing the current mouse location, with additional properties:
- `pressed` (boolean): whether either mouse button is currently pressed.
- `button` (`"right"` or `"left"`): which mouse button is currently pressed.
- `keys`: a map of pressed keyboard keys by `KeyboardEvent.code`.

## Extensions to Native JavaScript Objects
### `Math`
The library extends the `Math` object with the following functions:
- `logBase(base, num)`: computes the logarithm of `num` with base `base`.
- `divisors(num)`: computes an array containing all the divisors of `num` in increasing order. Runs in $O(\sqrt n)$ time.
- `factorize(num, mode)`: computes the prime factorization of the integer `num`. Supports both `number`s and `bigint`s, with the return value's type matching the input's type. Supports three different formats for the return value:
	- `factors-list` (default): returns an array containing all the prime factors in increasing order, possibly with duplicates.
	- `prime-exponents`: returns a map where the keys are the prime factors and the values are the exponent.
	- `exponents-list`: returns a list where the $i$th entry is the exponent on the $i$th prime in the factorization (or $0$ if that prime is not a prime factor).
- `isPrime(num)`: returns whether `num` is a prime number. Runs in $O(\sqrt n)$ time.
- `gcd(x1, x2, ...)`: computes the greatest common divisor of the given numbers using the [Euclidean algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm).
- `areCoprime(a, b)`: returns whether `a` and `b` have no prime factors in common.
- `map(value, min1, max1, min2, max2)`: linearly interpolates `value` from the interval `[min1, max1]` to the interval `[min2, max2]`.
- `dist`: returns the distance between the given numbers or vectors.
	- When called with 2 arguments, it returns the distance between them.
	- When called with 4 arguments `x1`, `y1`, `x2`, `y2`, it returns the distance between the vectors `(x1, y1)` and `(x2, y2)`.
- `rotate(x, y, degrees)`: returns the `Vector` obtained by rotating `(x, y)` by `degrees` degrees about the origin.

### `CanvasRenderingContext2D`
The library extends the `CanvasRenderingContext2D` prototype with the following functions:
- `polygon`: adds a polygon to the current fill path. Supports the following input formats:
	- When objects with properties `x` and `y` are passed in as arguments, it uses those objects as the vertices.
	- When numbers are passed in as arguments, it uses the first two to determine the first vertex, the next two to determine the next vertex, and so on.
	- Either of the above can be used by passing in an array or by passing in the inputs as the arguments directly.
- `fillPoly`: draws a solid polygon. Arguments are formatted the same as for `polygon`.
- `strokePoly`: draws an outline of a polygon. Arguments are formatted the same as for `polygon`.
- `fillCircle(x, y, radius)`: draws a solid circle with the given center and radius.
- `strokeCircle(x, y, radius)`: draws an outline of a circle with the given center and radius.
- `fillCanvas(color)`: fills the canvas with the given color, or the current `fillStyle` if no color is provided.
- `strokeLine`: draws a line between two points. The arguments can be provided as four numbers (`x1`, `y1`, `x2`, `y2`), or as two objects with properties `x` and `y`.

### `Function`
The library extends the `Function` prototype with the following functions:
- `myFunc.memoize`: returns a memoized/cached version of the function `myFunc`. The memoized functions name will be of the form `myFunc (memoized)`, to allow for easier debugging. The following options are supported:
	- `stringifyKeys` (defaults to false): if enabled, the arguments will be converted to a string by calling the `toString` method before being used to check the cache. If stringification is not enabled, every key in the cache will be checked using a deep equality algorithm.
	- `cloneOutput` (defaults to false): if enabled, the result will be deeply copied before being returned to the original caller. This can matter if the return value is altered afterwards.

### `Array`
The library extends the `Array` prototype with the following functions:
- `repeat(numTimes)`: concatenates the array with itself repeatedly `numTimes` times.
- `subArrays()`: returns an array containing every contiguous sub-array of the original array.
- `partitions()`: returns an array containing all partitions of the array into contiguous sub-arrays.
- `partitionGenerator()`: same as `partitions()`, but as a generator function.
- `sum(func?, thisArg?)`: computes the sum of the array elements. If a callback `func` is provided, it will be applied to each of the arguments before summing. The argument `thisArg` will be passed in as the `this` property of the callback.
- `product(func?, thisArg?)`: computes the product of the array elements. If a callback `func` is provided, it will be applied to each of the arguments before taking the product. The argument `thisArg` will be passed in as the `this` property of the callback.
- `min(func, thisArg, resultType)`: finds the minimum of an array. If a callback `func` is provided, it is applied to each of the values before taking the minimum. The following output formats are supported (determined by the `resultType` argument):
	- `object` (default): returns the value in the array that minimizes the callback.
	- `index`: returns the index of the value that minimizes the callback.
	- `value`: returns the smallest output of the callback on all the elements.
	- `all`: returns an array containing the index of the value that minimizes the callback, the value, and the output of the callback on that value.
- `max(func, thisArg, resultType)`: same as `min`, but for the largest value instead.
- `count(value)` / `count(callback)`: counts how many times a value appears, or how many elements satisfy a callback.
- `permutations()`: returns an array containing every permutation of the array.
- `isSorted(callback)`: returns whether the array is sorted. The `callback` should return positive, negative, or zero, like when using [Array.sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort). If no comparison callback is provided, it returns whether the values are sorted in ascending order numerically.
- `deduplicate()`: removes all but the first occurrence of each element.
- `group(...callbacks)`: partitions the array into groups based on the first callback that returns true for each item, and places any leftover items into the final group.
- `Array.SORT_ASCENDING` and `Array.SORT_DESCENDING`: predefined comparator functions for sorting arrays of numbers.

### `Map`
The library extends the `Map` prototype with the following functions:
- `equals(map)`: compares two maps using deep equality.
- `clone()`: creates a deep copy of the map.

### `Set`
The library extends the `Set` prototype the following functions:
- `equals(set)`: returns whether the two sets are equal, using deep equality.
- `clone()`: creates a deep copy of the set.
- `intersection(set)`: returns the set of all elements present in both sets.
- `union(set)`: returns the set of all elements present in either set.
- `difference(set)`: returns the set of elements in the first set that are not in the second.
- `subsets()`: returns the power set of the current set.
- `cartesianPower(power)`: returns the set of all tuples of length `power` formed from its elements.
- `map(callback)`, `every(callback)`, `some(callback)`, `filter(callback)`, and `find(callback)`: set versions of the standard array methods.
- `onlyItem()`: returns the only item in the set if the set has exactly one item, and throws an error otherwise.

### `Number`
The library extends the `Number` prototype with the following functions:
- `digits()`: returns an array of the digits of the number. Throws an error for non-integer values.

### `Object`
The library extends the `Object` prototype with the following functions:
- `clone()`: deeply clones the object. Supports cyclic objects.
- `equals(obj)`: returns whether the two objects are equal using deep equality.
- `set(key, value)`: assigns the property and returns the object.
- `watch(key, callback)`: wraps a property with a getter/setter that invokes `callback` whenever the value changes.
- `Object.typeof(value)` (static method): returns `typeof value`, with the following special cases:
	- If the value is `NaN`, it returns `"NaN"` (instead of `"number"`)
	- If the value is `null`, it returns `"null"` (instead of `"object"`)
	- If the value is an array, it returns `"array"` (instead of `"array"`)
	- If the value is an instance of a custom object, it returns `"instance"` (instead of `"object"`)

## Other Functions
- `utils.binarySearch(min, max, callback, whichOne)`: uses binary search to find an integer value in the given range for which the increasing callback returns 0. Supports both `number`s and `bigint`s. The `whichOne` parameter controls the behavior if there are multiple solutions or no solution:
	- If there are multiple integer values for which the callback returns 0, the first value is returned if `whichOne` is `"first"`, and the last value is returned if `whichOne` is `"last"`.
	- If there are no integer values for which the callback returns 0, the last negative value is returned if `whichOne` is `"first"`, and the first positive value is returned if `whichOne` is `"last"`.
- `utils.binaryInsert(array, element, compareFn)`: inserts `element` into the sorted array `array` while preserving the ordering. A custom ordering function `compareFn` can be provided; it should follow the same specification as for [Array.sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort).
- `utils.toString(obj, maxLength)`: converts the object to a string, attempting to reduce the length to below `maxLength` if possible. If not provided, `maxLength` defaults to `Infinity`. Includes readable formatting for the following objects:
	- Arrays, formatted as `[foo, bar, baz]` or `[object Array]` if too long.
	- Sets, formatted as `{foo, bar, baz}` or `[object Set]` if too long.
	- Strings, formatted as `"foo"` with additional quotes.
	- Other objects, formatted via calling the default `toString` method, or `[object Foo]` if too long.
- `utils.createElement(selector)`: builds an HTML element from a selector-like string. The first word in the string is the element name. IDs are indicated by `#` and classes are indicated by `.`, so for example `"div#foo.bar.qux"` creates a `div` with an ID of `foo` and classes of `bar` and `qux`.
