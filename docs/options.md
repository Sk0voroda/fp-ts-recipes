# Working with Options

## Basics

The `Option<A>` type in **fp-ts** is a monadic container that represents the possibility of a value being present or absent. It has two states, `Some<A>` and `None`. When the value of type `A` is present, `Option<A>` is an instance of `Some<A>` and contains the value. Conversely, when the value is absent, `Option<A>` is `None`.

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

Also `fromPredicate` can be used to build `Option` based on the given predicate.

```ts
import { none, some, fromPredicate } from "fp-ts/Option";

const toOption = fromPredicate((n: number) => n >= 0);

toOption(-1); // -> none
toOption(1); // -> some(1)
```

## Extract value from `Option`

Use `toNullable`, `toUndefined` or `getOrElse` functions to extracts value out of `Option` structure, if it exist. `getOrElse` let's you define default return value like `0` for `none` case.

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

We can change the value contained within an `Option` data type in **fp-ts** without the need to extract it by utilizing functions such as `map`, `chain` and `filter`. If an `Option` data type contains `None`, the functions would not be applied.

### Map

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

temporart

```ts
import { none, some, chain } from "fp-ts/Option";

const nonZero = (x: number) => (x === 0 ? none : some(x));

chain(nonZero)(some(1)); // -> some(1)
```

### `Match`

You also can use `match` to get value from `Option`, apply some function to it and return non `Option` value.

```ts
import { none, some, match } from "fp-ts/Option";

const double = (x: number) => x * x;

match(() => 0, double)(some(2)); // -> 4
```

### Combine with `pipe`

import { none, some, filter } from "fp-ts/Option";

```ts
import { none, some, map, filter, chain } from "fp-ts/Option";
const double = (x: number) => x * x;
const nonZero = (x: number) => (x === 0 ? none : some(x));

const;
```
