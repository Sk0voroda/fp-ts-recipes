# Working with Options

## Basics

### Build an Option

`Option<A>` is a container for an optional value of type `A`. It can have two states `Some<A>` and `None`. If the value of type `A` present `Option<A>` is instance of `Some<A>`, containing the value. If the value absent, the `Option<A>` is `None`.

```ts
type Option<A> = None | Some<A>; // type definition of Option;
```

To define `Option<A>` value that exists we can use `Some<A>` constructor.

```ts
import { some } from "fp-ts/Option";

const value = some("value"); // { _tag: 'Some', value: 'value' };
```

To init `Option<A>` that is absent we can use `None` constructor.

```ts
import { none } from "fp-ts/Option";

const value = none; // { _tag: 'None' };
```

### Build an Option from existing value

To convert value of type `A` to `Option<A>` we can use conversion functions such as `fromNullable`.

```ts
import { none, some, fromNullable } from "fp-ts/Option";

fromNullable(undefined); // -> none
fromNullable(null); // -> none
fromNullable(1); // -> some(1)
```
