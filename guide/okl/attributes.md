# Attributes

!>
    OKL is still transitioning from **v0.2** &nbsp;&rarr;&nbsp; **v1.0** (ETA is end of May '18)
    <br/>
    Examples are being written for **v1.0** which won't work at the moment, but linked source code will work
    <br/>
    [Check the v0.2 &nbsp;&rarr;&nbsp; v1.0 guide to see how to make the v1.0 examples work in the current OCCA setup](/guide/okl/v1)
    <br/>
    **Sorry for the inconvenience!**

Attributes, such as `@dim`, are used to programatically transform code, similar to `#pragma` directives

Currently there are a few core attributes but a public API for defining and registering custom attributes will be released in v2

## @dim

`@dim` can be used to easily work with multi-indexed arrays

?> The array bounds don't have to be defined when declaring the variable, but should exist when accessing it

```okl
int *xy @dim(X, Y);
xy(1, 2) = 0;
```

<md-icon class="transform-arrow">arrow_downward</md-icon>

```cpp
int *xy;
xy[1 + (2 * X)] = 0;
```

Additionally, the `@dim` attribute works with `typedef`'d types

```okl
typedef int* mat3 @dim(3, 3);
mat3 xy;
xy(1, 2) = 0;
```

<md-icon class="transform-arrow">arrow_downward</md-icon>

```cpp
typedef int* mat3;
mat3 xy;
xy[1 + (2 * 3)] = 0;
```

## @dimOrder

`@dimOrder` is used to reorder `@dim` indices, taking the ordering as its arguments

```okl
typedef int* mat23 @dim(2, 3);
mat23 xy;
mat23 yx @dimOrder(1, 0);
xy(1, 2) = 0;
yx(1, 2) = 0;
```

<md-icon class="transform-arrow">arrow_downward</md-icon>

```cpp
typedef int* mat23;
mat23 xy;
mat23 yx;
xy[1 + (2 * 2)] = 0;
yx[2 + (1 * 3)] = 0;
```

## @tile

`@tile` is used for auto-tiling loops

!>
    Tiling can cause the iterator to go **out of bounds**.
    To automatically add a check, use `@safeTile`.

```okl
for (int i = 0; i < N; ++i; @tile(16)) {
  // work
}
```

<md-icon class="transform-arrow">arrow_downward</md-icon>

```cpp
for (int iTile = 0; iTile < N; iTile += 16) {
  for (int i = iTile; i < (iTile + 16); ++i) {
    // work
  }
}
```

Attributes to split loops can be passed as additional arguments

```okl
for (int i = 0; i < N; ++i; @tile(16, @outer, @inner)) {
  // work
}
```

<md-icon class="transform-arrow">arrow_downward</md-icon>

```okl
for (int iTile = 0; iTile < N; iTile += 16; @outer) {
  for (int i = iTile; i < (iTile + 16); ++i; @inner) {
    // work
  }
}
```

## @safeTile

`@safeTile` is the same as `@tile` but adds a check in the inner loop to prevent **out-of-bounds** accesses

```okl
for (int i = 0; i < N; ++i; @safeTile(16)) {
  // work
}
```

<md-icon class="transform-arrow">arrow_downward</md-icon>

```cpp
for (int iTile = 0; iTile < N; iTile += 16) {
  for (int i = iTile; i < (iTile + 16); ++i) {
    if (i < N) {
      // work
    }
  }
}
```
