# Foil

[![Foil header](https://raw.githubusercontent.com/Zabadam/foil/master/doc/foil.gif 'Get started! import \'package:foil/foil.dart\'')](https://pub.dev/packages/foil)

Wrap a widget with `Foil`, providing a rainbow shimmer
that twinkles as the accelerometer moves.

Consider [holographic Pokémon trading cards](https://github.com/Zabadam/foil/blob/master/example/lib/main.dart#L228 'Foil Demo app has a foil-wrapped card (that itself rotates with help of \'package:xl\')'),
some credit cards, or an [oil slick](https://google.com/search?q=oil+slick&tbm=isch 'Google image search for "oil slick"')
on the road.

## 📚 Table of Contents
- [Foil](#foil)
  - [Getting Started](#getting-started)
    - [Accelerometer](#accelerometer)
    - [Transitioning](#transitioning)
  - [Advanced Usage](#advanced-usage)
    - [`Roll`s](#rolls)
    - [`Crinkle`s](#crinkles)
    - [`TransformGradient`](#transformgradient)
  - [Extra Goodies](#extra-goodies)
    - [`Steps`](#steps)
    - [`GradientUtils` Extensions](#gradientutils-extensions)
    - [`Foils`](#foils)
  - [Reference](#reference)
    - [📖 API Documentation](#-api-documentation)
    - [📲 `Foil` Demo](#-foil-demo)
    - [🛣️ Roadmap](#️-roadmap)
    - [🐞 Bugs](#-bugs)

## Getting Started
Wrap a widget by providing it as `Foil.child`.

Use any [`gradient`](#foils '`Foils` is a selection of pre-rolled `Gradient`s')
of choice and determine the `blendMode` to mask the gradient \
onto the child in different ways. Default is `BlendMode.srcATop` which will \
reveal the child through the gradient if the gradient has opacity or if \
`Foil.opacity` is provided a non-`1.0` value. 
- Use `BlendMode.srcIn` to paint *only* the gradient and use the child as a mask.

Flag `isUnwrapped` toggles this `Foil`'s invisibility. Default is `false.` \
When this value is changed, the gradient will smoothly animate to a disabled \
state, leaving only the `child` behind.

```dart
class Example extends StatelessWidget {
  const Example({Key? key}): super(key: key);

  @override
  Widget build(BuildContext context) => FittedBox(
    child: Foil(
      child: const Text(
        'FOIL',
        style: TextStyle(fontWeight: FontWeight.w900)
      ),
    ),
  );
}
```
> ### Quickstart
> [![Foil header](https://raw.githubusercontent.com/Zabadam/foil/master/doc/foil_small.gif 'Get started! import \'package:foil/foil.dart\'')](https://pub.dev/packages/foil) \
> Plop an instance of this `const Example()` in a running `ListView` 
> you've got open in a code editor. \
> Run terminal command `flutter pub add foil`. \
> `import 'package:foil/foil.dart'`


### Accelerometer
Disable this `Foil`'s reaction to accelerometer sensor motion by \
`useSensor: false`. Default is `true`.
- In this case, you may want to employ a [`Roll` with `Crinkle`s](#crinkles).

Influence the intensity of this `Foil`'s reaction to accelerometer motion \
by providing a custom `Scalar` property `Foil.scalar`. 
- Default is `Scalar.identity` which has both a `horizontal` and `vertical` \
multiplier of `+1.0`.

> [![foily, oscillating Gyarados card with package:xl](https://raw.githubusercontent.com/Zabadam/foil/master/doc/gyarados.gif 'the card rotates with the help of package:xl')](https://github.com/Zabadam/foil/blob/master/example/lib/main.dart#L228) \
> This trading card is wrapped in *two* `Foil` widgets offering different gradients!

### Transitioning
Control how rapidly this `Foil` transforms its gradient with `Foil.speed` \
and define the animation `curve`. Defaults are `150ms` and `Curves.ease`.

| [![animated unwrapping](https://raw.githubusercontent.com/Zabadam/foil/master/doc/isUnwrapped.gif 'Changing gradients or disabling the Foil both smoothly animate')](https://raw.githubusercontent.com/Zabadam/foil/master/doc/isUnwrapped.gif) | Furthermore, provide `Foil.duration` to dictate how long intrinsic animations of gradient will take. `Foil.duration` is also used if `isUnwrapped` is made `true` as the duration over which `Foil.gradient` will lerp to an appropriately-Typed transparent gradient for tweening. There is hard-coded recognition for linear, radial, and sweep gradients, as well as the additional [`Steps`](#steps) variants this package provides. Falls back to a transparent `LinearGradient` if `Type` cannot be matched. |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
> Upon completion of any tween to a new `gradient`, this `Foil` will call 
> `onGradientTweenEnd`, an optional void callback.

## Advanced Usage
### `Roll`s
Wrap any `Foil` (or many of them!) in a `Roll` higher in the widget tree \
to provide some inherited properties.

A gradient provided by an ancestral `Roll` may be used by as a `Foil.gradient` \
if one is not explicitly perscribed in the `Foil`.
- If neither an ancestral `Roll` nor a `Foil` dictates its own `gradient`, 
  then the default is `Foils.rainbow`.
- A descendent that provides its own `Foil.gradient` will override the `Roll.gradient`.

> One day a `Roll` will also provide the option to "cut" any descendent `Foil`
> from a single shared gradient sheet that covers a space the size of the `Roll`.

`Roll`s can also serve to provide animation properties to a descendent `Foil`, \
regardless of whether its serving its gradient. 

### `Crinkle`s
These animations are in addition to any accelerometer sensors data \
already animating the `Foil` unless `Foil.useSensors: false`.


| [![animated by \`Roll.crinkle\`](https://raw.githubusercontent.com/Zabadam/foil/master/doc/crinkle_small.gif 'animated by \`Roll.crinkle\`')](https://raw.githubusercontent.com/Zabadam/foil/master/doc/crinkle.gif) | The `Roll.crinkle` parameter defaults to `Crinkle.smooth` which is not animated (although each `Roll` has its own `AnimationController` for toggling purposes). A `Crinkle` dictates speed, intensity, and directionality of animation. |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
> - `Crinkle.twinkling` is a very slow moving preset
> - `Crinkle.vivacious` is a highly-animated preset
> - Build your own or opt to `Crinkle.copyWith` a preset
>   - `isAnimated`, `shouldReverse` flags
>   - Use `period` to determine the animation loop `Duration` 
>   - The `scalar` property can be used to invert, scale, or negate axes \
>   (this is a `Scalar` object like a `Foil` uses to scale its sensors data)

### `TransformGradient`
This is the definition of a function that takes in a scaled `double x` and `double y` \
and returns a `GradientTransform` object. That object is used when a `Foil`'s \
gradient shader is drawn by calling its `GradientTransform.transform()` method.

To provide a custom transformation as `Crinkle.transform`, extend that class \
and override `transform()`, returning some `Matrix4`, such as the default \
for this package:

## Extra Goodies
This package also comes with some other offerings. \
Some expand on `Gradient` functionality, and others make them simpler to use.

Up first is named [`Foil.sheet`](https://pub.dev/documentation/foil/latest/foil/Foil/Foil.sheet.html) which functions like a self-contained \
`Foil` + `AnimatedContainer`. Stylized by a [`Sheet`](https://pub.dev/documentation/foil/latest/foil/Sheet-class.html).


### `Steps`
The basic premise of a gradient is to gradate between colors. \
Consider new `Steps` to be extensions of `Gradient`s, 
one each for linear, radial, and sweep, \
where the colors do not gradate but instead hard-transition. Like steps! \
`RadialSteps` are great for making rainbows. \
> ![three new gradients called \`Steps\`](https://raw.githubusercontent.com/Zabadam/foil/master/doc/Foils.stepBow.gif) \
> | `SweepSteps` | `RadialSteps` | `LinearSteps` |

### `GradientUtils` Extensions
Along with the three new variety of `Gradient`, this package \
provides 📋 `copyWith()` methods for each existing Flutter type.

> ```dart
> final copy = RadialGradient(
>   colors: [. . .],
>   stops: [. . .],
>   center: const Alignment(0, 0),
> ).copyWith(center: const Alignment(-2, -2));
> ```
> Even the superclass `Gradient` may be copied, provided `this` is a standard
> linear, radial, or sweep gradient, or one of the three new steps gradients
> (falls back to a standard linear gradient). Provides every potential
> copyable parameter from the varieties. Most useful in a scenario where a
> common parameter like `colors` or `transform` is to be applied to
> some arbitrary `Gradient`.

This makes it a cinch to pick out pre-rolled `Foils`  \
yet tweak them for specific scenarios--such as changing \
start and end points while maintaining the list of colors.

What are `Foils`?

### `Foils`
Finally, this package is bundled with a selection of pre-defined gradients. \
One such pre-rolled `Foils.oilslick` has opacity within each `Color`.
- But consider that the `Foil` constructor accepts an overriding \
`opacity` parameter for semi-transparent appearance even without \
`Gradient.colors` that have opacity.

The default `Foil.gradient` is `Foils.linearLooping`,
a nice 'n' simple `LinearGradient`. \
Reverse the order of colors with `Foils.linearReversed`. \
To only cycle through the rainbow once see `Foils.linearRainbow`. \
![four varieties of linear rainbow gradient](https://raw.githubusercontent.com/Zabadam/foil/master/doc/Foils.linear.gif)

| <!--  | ![four varieties of linear rainbow gradient](https://raw.githubusercontent.com/Zabadam/foil/master/doc/Foils.linear.gif) |
| :---: |

&nbsp; -->

Find other options like `Foils.gymClassParachute` and `Foils.sitAndSpin`, \
then tweak them to your liking with the 📋 `copyWith()` methods. 
| ![\`sitAndSpin\`, and \`gymClassParachute\`](https://raw.githubusercontent.com/Zabadam/foil/master/doc/Foils.gymClass.gif) | ![\`oilslick\`](https://raw.githubusercontent.com/Zabadam/foil/master/doc/Foils.oilslick.gif) |
| :------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------------------------------------------------------: |

> [![\`Foils.rainbow\`](https://raw.githubusercontent.com/Zabadam/foil/master/doc/Foils.rainbow.gif)](https://pub.dev/documentation/foil/latest/foil/Foils-class.html) \
> There is even a literal rainbow decal gradient made with the new `RadialSteps` type.

## Reference
### 📖 [API Documentation](https://pub.dev/documentation/foil/latest)
### 📲 [`Foil` Demo](https://github.com/Zabadam/foil/tree/main/example/lib) 

### 🛣️ Roadmap
0. Make available a `Roll` of `Foil` feature such that a developer could \
deploy a single gradient sheet in a region and mask that single gradient to any \
child `Foil` widgets.
   - `Roll`s are implemented now, but only offer to pass a `Gradient` to \
   descendent `Foil`s--not to mask them from the same gradient.
1. If this package's `Gradient` support expands further,
much of that functionality could be forked to an independent package.
2. Custom `GradientTransform`s will be investigated, 
for example:  to deform `Foils.oilslick`.

### 🐞 Bugs
0. Potential optimizations for `GradientTween`ing within `lerp()`,
especially with the new `Steps` style gradients.
1. Because this package's [`copyWith()`](#gradientutils-extensions) method
for the superclass `Gradient` is used when applying `Crinkle` transformation,
the returned gradient is limited by the hard-coded return types of the
`copyWith` method; that is to say: the three Flutter `Gradient`s and the
three [`Steps` gradients](#steps) added by this package.
   - If a bespoke `Gradient` type is used for some `Foil` that is wrapped in 
   a `Roll` whose `Crinkle` provides animation, that gradient will become a 
   `LinearGradient` with the appropriate colors at least.
   - In this scenario, until this bug is patched, consider transforming the 
   gradient prior to providing it to your `Crinkle`-animated `Foil` in order 
   to maintain the bespoke `Gradient`.
