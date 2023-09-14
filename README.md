# Reproduction of Svelte reactive var types

> [!NOTE]
> Reproduction code is in `src/App.svelte`.

As far as I understand, reactive statements run after other script code, which means variables declared as part of reactive statements remain `undefined` while non-reactive statements are being run. When using Typescript as below,

```ts
function assignFoo(str: string = "qux") {
  return { bar: { baz: str } };
}

$: foo = assignFoo();
console.log(foo);
```

`foo` is typed as

```
let foo: {
    bar: {
        baz: string;
    };
}
```

despite `console.log` printing `undefined`. Wouldn't it be more accurate to have foo be ` ... | undefined`? Is there an ergonomics/DX choice that was made here to not include `| undefined` in the type?

When trying to call a deep prop of `foo` in a subsequent non-reactive statement, the app crashes with `TypeError: Cannot read properties of undefined`:

```ts
function assignFoo(str: string = "qux") {
  return { bar: { baz: str } };
}

$: foo = assignFoo();

let myFavString = foo.bar.baz; // TypeError: Cannot read properties of undefined (reading 'bar')
```

Is this just how Svelte works, meaning watching out for use of variables from reactive statements in non-reactive statements is part of normal Svelte development flow?
