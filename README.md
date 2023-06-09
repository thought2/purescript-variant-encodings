# variant-encodings

Alternative runtime encodings for Variant types.

## Description

Variant types are great to FFI JavaScript tagged union types. But the encoding
on the JavaScript side does not always match exactly the one from Variant (`{
type: ..., value: ...}`)

This library provides types that describe variants with custom encodings: Tag
and value keys can be configured. Also a flat encoding is available.

The types are merely meant as an intermediate step. There will be no utility
functions provided. However, there are functions available that convert to and from `Variant`.

## Installation

```
spago install variant-encodings
```

## Example

### Flat encoding

On the JS side you have the following flat encoded tagged union types:

```js
export const valSamples = [
  { kind: "loading", progress: 0, id: "abc" },
  { kind: "loading", progress: 20, id: "abc" },
  { kind: "loading", progress: 71, id: "abc" },
  { kind: "loading", progress: 98, id: "abc" },
  { kind: "success", result: "xyz" },
];
```

On the PureScript side you can FFI it like so:

```hs
import Data.Variant.Encodings.Flat (normalizeEncodingFlat, VariantEncodedFlat)

type SampleVarEnc =
  VariantEncodedFlat "kind"
    ( loading :: { progress :: Int, id :: String }
    , success :: { result :: String }
    )

foreign import valSamples :: Array SampleVarEnc
```

And then convert it into a more usable Variant type:

```hs
type SampleVar = Variant
  ( loading :: { progress :: Int, id :: String }
  , success :: { result :: String }
  )

valSamplesVariant :: Array SampleVar
valSamplesVariant = map normalizeEncodingFlat valSamples
```

### Nested encoding

On the JS side you have the following nested encoded tagged union types:

```js
export const valSamples = [
  { kind: "loading", payload: 0 },
  { kind: "loading", payload: 20 },
  { kind: "loading", payload: 71 },
  { kind: "loading", payload: 98 } },
  { kind: "success", payload: "done!" },
];
```

On the PureScript side you can FFI it like so:

```hs
import Data.Variant.Encodings.Nested (VariantEncodedNested, normalizeEncodingNested)

type SampleVarEnc =
  VariantEncodedNested "kind" "payload"
    ( loading :: Int
    , success :: String
    )

foreign import valSamples :: Array SampleVarEnc
```

And then convert it into a more usable Variant type:

```hs
type SampleVar = Variant
  ( loading :: Int
  , success :: String
  )

valSamplesVariant :: Array SampleVar
valSamplesVariant = map normalizeEncodingNested valSamples
```
