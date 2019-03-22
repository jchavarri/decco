# ppx_decco

## What is it?

Bucklescript PPX which generates JSON serializers and deserializers for user-defined types.

Example:

```reason
/* Define types */
[@decco] type variant('a) = A | B(int) | C(int, 'a);
type dict = Js.Dict.t(string);
[@decco] type mytype = {
    s: string,
    i: int,
    o: option(int),
    complex: array(option(list(variant(string)))),
    [@decco.default 1.0] f: float,
    [@decco.key "other_key"] otherKey: string,
    magic: [@decco.codec Decco.Codecs.magic] dict,
};

/* Use <typename>_encode to encode */
let encoded = mytype_encode({
    s: "hello",
    i: 12,
    o: None,
    complex: [| Some([ C(25, "bullseye") ]) |],
    f: 13.,
    otherKey: "other",
    magic: Js.Dict.fromArray([|("key","value")|]),
});

Js.log(Js.Json.stringifyWithSpace(encoded, 2));
/* {
     "s": "hello",
     "i": 12,
     "o": null,
     "complex": [ [ ["C", 25, "bullseye"] ] ],
     "f": 13,
     "other_key": "other",
     "magic": { "key": "value" }
  } */

/* Use <typename>_decode to decode */
let { s, i, o, complex, f, otherKey, magic } =
    mytype_decode(encoded)
    |> Belt.Result.getExn;
```

## What state is it in?
I've been using it for personal projects for a while, but it's never been used for anything important, so YMMV.

## How do I install it?
1. Install package
```
npm i @ryb73/decco
```

2. Update your `bsconfig.json`
```json
{
  ...,
  "bs-dev-dependencies": [ "@ryb73/decco" ],
  "ppx-flags": [ "ppx_decco" ],
  ...
}
```

## How do I use it?

See [`__tests__/test.re`](__tests__/test.re) for some examples.
