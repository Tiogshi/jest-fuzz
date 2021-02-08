# jest-fuzz
Fuzz testing for jest

# Installing

use your favorite package manager

`$ yarn add jest-fuzz`
`$ npm install jest-fuzz`

# Using

Import the test suite in your test code.

```javascript
const Fuzz = require('jest-fuzz');

...
```
## Creating a test
First of all, you need to use the `Fuzz.test` instead of Jest's `test` and what's the difference?
Basically `Fuzz.test` create a wrap to all random data that it will generate for your code. 

Now, this is how a test has to will be described.
```javascript
Fuzz.test("Name", Fuzzer, (data) => {
	//expectations
}
```
where,

- Name -> String representing the name of this suite
- Fuzzer -> A fuzzer function that you can see below
- Callback (data) -> This callback is where your tests will be, and the data param is a random data to your test.

## Fuzzers
Fuzzers are the data representation of what you need to test.

for example, if you have a function that needs a string, you can use `Fuzz.string()` also, pass the parameters for this string.

these are the built-in fuzzers

| Fuzzer      | Options                                                                                |
|-------------|----------------------------------------------------------------------------------------|
| Fuzz.string | { length: Int (default=125), sufix: String (default=''), prefix: String (default='') } |
| Fuzz.int    | { Min: Int (default=-Infinity), max: Int (default=Infinity) }                          |
| Fuzz.float  | { Min: Int (default=-Infinity), max: Int (default=Infinity) }                          |
| Fuzz.bool   |                                                                                        |
| Fuzz.array  | { type: Fuzzer (default=Int), length: Int (default=300), minLength: Int (default=1) }  |

### Creating a custom fuzzer

The custom fuzzer is a way to represent an object or other complex data.

To create you simply need to use the `Fuzz.Fuzzer`

```javascript
const exampleFuzzer = Fuzz.Fuzzer({
    id: Fuzz.string(),
    value: Fuzz.int()
});

Fuzz.test("My example", exampleFuzzer(), data => {
    // Test data;
	let Map = new Map();

	Map.set(data.id, data.value);
	expect( Map.get(data.id) ).toBe( data.value );
}
```


## Typescript

TypeScript annotations are now included in the `jest-fuzz` package. Some of the features of these annotations include...

 * Autocomplete of the `options` objects for all built-in fuzzers.
 * Type inference and checking of the data injected into your tests.
 * Inference of the type of custom fuzzer objects.
 * Statically fixed-length arrays get annotated as though they are Tuples. 

```javascript
// : Fuzz.FuzzGenerator<{id:string, value:number}>
const example1 = Fuzz.Fuzzer({
    id: Fuzz.string(),
    value: Fuzz.int()
});

// : Fuzz.FuzzGenerator<number[]>
const example2 = Fuzz.array({type: Fuzz.int()});

// : Fuzz.FuzzGenerator<[boolean, boolean][]>
const example2 = Fuzz.array({type: Fuzz.bool(), minLength:2, length:2});
Fuzz.test("Logic Ops", example2, [lhs,rhs] => {
	let expected = lhs && rhs;
	let actual = LogicalAndFunction(lhs, rhs);

	expect(actual).toBe( expected );
});
```

