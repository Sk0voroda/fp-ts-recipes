# Working with Options

## Basics

The `Option<A>` type in **fp-ts** is a monadic container that represents the possibility of a value being present or absent. It has two states, `Some<A>` and `None.` When the value of type `A` is present, `Option<A>` is an instance of `Some<A>` and contains the value. Conversely, when the value is absent, `Option<A>` is `None`.

## Build an Option

```ts
import { some, none, of } from "fp-ts/Option";

const someVal = some("value"); // { _tag: 'Some', value: 'value' };
const someOfVal = of("value"); // { _tag: 'Some', value: 'value' };
const noneVal = none; // { _tag: 'None' };
```

`some` and `of` constructors are equivalent for building `Option` from value.

## Build an `Option` from existing value

To convert value of type `A` to `Option<A>` we can use conversion functions such as `fromNullable`.

```ts
import { none, some, fromNullable } from "fp-ts/Option";

fromNullable(undefined); // -> none
fromNullable(null); // -> none
fromNullable(1); // -> some(1)
```

Also, `fromPredicate` can be used to build `Option` based on the given predicate.

```ts
import { none, some, fromPredicate } from "fp-ts/Option";

const toOption = fromPredicate((n: number) => n >= 0);

toOption(-1); // -> none
toOption(1); // -> some(1)
```

## Extract value from `Option`

Use `toNullable`, `toUndefined` or `getOrElse` functions to extract value out of `Option` structure, if it exist. `getOrElse` lets you define a default return value like `0` for `None` case.

```ts
import { none, some, toUndefined, toNullable, getOrElse } from "fp-ts/Option";

const noneVal = none;
const someVal = some(1);

toNullable(noneVal); // -> null
toNullable(someVal); // -> 1

toUndefined(noneVal); // -> undefined
toUndefined(someVal); // -> 1

getOrElse(() => 0)(noneVal); // -> 0
getOrElse(() => 0)(someVal); // -> 1
```

Also `match` or `fold` function can be used to perform some action based on `Option` value.

```ts
import { none, some, match } from "fp-ts/Option";
const value = some(1);

O.match(
  () => "value is none",
  (v) => `value containing ${v}`
)(value);
```

`fold` is alias for `match` function.

## Change `Option` value

In **fp-ts**, it is good practice to alter the value within an `Option` by utilizing functions such as `map`, `chain`, and `filter`, without requiring the value to be extracted. If the `Option` value is `None`, these functions won't have any effect.

### `Map`

```ts
import { none, some, map } from "fp-ts/Option";

const double = (x: number) => x * x;

const doubleOption = map(double)(some(2)); // -> some(4)
const doubleNoneOption = map(double)(none); // -> none
```

### `Filter`

```ts
import { none, some, filter } from "fp-ts/Option";

const nonZero = (x: number) => x > 0;

const isOptionNonZero = filter(nonZero)(some(2)); // -> some(2)
const isOptionNonZero = filter(nonZero)(some(-1)); // -> none
```

### `Chain`

The `Chain` allows you to chain operations that may or may not return a value. If the function returns another `Option` instance, the values combined into a single `Option` instance. If the function returns `None,` the entire chain is short-circuited and `None` is returned. It prevents us from situations when the return value is of type `Option<Option>.`

```ts
import { none, some, chain } from "fp-ts/Option";

const nonZero = (x: number) => (x === 0 ? none : some(x));

chain(nonZero)(some(1)); // -> some(1)
```

### `Match`

You also can use `match` to get value from `Option`, apply some function to it, and return non `Option` value.

```ts
import { none, some, match } from "fp-ts/Option";

const double = (x: number) => x * x;

match(() => 0, double)(some(2)); // -> 4
```

## Examples using `Option`

We can combine `chain` and `map` using `pipe` to get value out of `user` and capitalize it.

```ts
import * as O from "fp-ts/Option";
import { pipe } from "fp-ts/function";

const capitalize = (word: string) => word.charAt(0).toUpperCase() + word.slice(1)

const user = O.some({
  name: O.some("spike"),
})

const capitalizeName = pipe(
  user,
  O.chain(user => user.name)
  O.map(capitalize)
); // -> { _tag: 'Some', value: 'Spike' }
```

When working with two `Option` values inside `pipe` in **fp-ts**, the `Do` notation can be used to simplify the code and avoid nested function calls.

```ts
import * as O from "fp-ts/Option";
import { pipe } from "fp-ts/function";

const userName = O.some("jack");
const userLastname = O.some("black");

const fullName = pipe(
  O.Do,
  O.bind("name", () => userName),
  O.bind("lastname", () => userLastname),
  O.map(({ name, lastname }) => `${name} ${lastname}`)
); // -> { _tag: 'Some', value: 'jack black' }
```

When working with an array of `Option,` the `map` function from `fp-ts/Array` can be used. Here in an example, it will return a new array of double `Option<number>` values, keeping `None.`

```ts
import * as O from "fp-ts/Option";
import * as A from "fp-ts/Array";
import { pipe } from "fp-ts/function";

const double = (x: number) => x * x;

const values = [O.some(0), O.none, O.some(4), O.some(5)];

const doubleValues = pipe(values, A.map(O.map(double))); // -> [O.some(0), O.none, O.some(8), O.some(10)];
```

In the same way, `filter` can be used.

```ts
import * as O from "fp-ts/Option";
import { pipe } from "fp-ts/function";
import * as A from "fp-ts/Array";

const notNull = (x: number) => x > 0;

const values = [O.some(0), O.none, O.some(4), O.some(5)];

const doubleValues = pipe(values, A.map(O.filter(notNull))); // -> [O.none, O.none, O.some(4), O.some(5)];
```

Function `compact` from `fp-ts/Array` can be used to uwrap values from array of `Opton.` If array containes `None`, it will be removed.

```ts
import * as O from "fp-ts/Option";
import { pipe } from "fp-ts/function";
import * as A from "fp-ts/Array";

const values = [O.some(0), O.none, O.some(4), O.some(5)];

const doubleValues = pipe(values, A.compact); // -> [0, 4, 5];
```
