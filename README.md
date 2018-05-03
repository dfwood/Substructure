# Substructure CSS - SASS Features

This library is still in development. It is subject to have breaking changes without notice until the first stable release.

## Setup
It is recommended to go through all config files and ensure all values are properly set for your project.

The following NPM packages are required:
* `sass-mq`
* `normalize.css`

## Config
Detail of default config files located in the `config` directory.

### `_assetPaths.scss`
Defines paths to assets such as fonts, images, and SVGs.

### `_breakpoints.scss`
Defines breakpoints and breakpoint related settings.

### `_colors.scss`
Defines standard and custom colors.

### `_config.scss`
Main config file, this is the file that loads all the other config files.

### `_grid.scss`
Defines grid system values and settings.

### `_typography.scss`
Defines font-families as variables as well as a heading and body font-face variable. Also includes some FontAwesome specific setup variables.

## Dependencies
### FontAwesome (Optional)
[FontAwesome](http://fontawesome.io/) is included in the build through the `scss/base/_typography.scss` file. SASS settings for FontAwesome can be set in the `scss/config/_typography.scss` file.

### Normalize.css (Required)
[Normalize.css](https://necolas.github.io/normalize.css/) is included via an NPM package to fix browser inconsistencies so help have a clean starting point. This is used instead of a CSS reset to avoid having to restyle basic HTML elements.

NOTE: There is also a `scss/base/_normalize-add-ons.scss` file to include reset/normalize related global styles if additional browser issues with elements need to be resolved. Editing this file should be considered non-standard and avoided if possible.

### WordPress Classes (Included)
There is a slightly modified version of the default [WordPress Generated Classes](https://codex.wordpress.org/CSS#WordPress_Generated_Classes). These are located in `scss/base/_wp.scss`. Modifications are mostly formatting and `px` to `rem` conversions.

## SASS MQ (Required)
The [sass-mq](https://www.npmjs.com/package/sass-mq) NPM package is included to provide the `mq()` mixin for outputting media queries. All media queries should be mobile first if possible.

Example usage: `@include mq($from: $bp-md) { /* Styles go here */ }` 

### Config Options
Config values are located in the `scss/config/_breakpoints.scss` file.

`$bp-#{$breakpointName}`: These variables should exist for each breakpoint. The breakpoint name portion will be used in numerous places, so changing these names should be avoided once development starts. All values should be in pixels, the value should be the _smallest_ pixel value of this breakpoint.

`$mq-breakpoints`: A SASS map containing the breakpoint names as the key and the `$bp-#{$breakpointName}` variable as the value. NOTE: `base` as a breakpoint name may be used for a breakpoint starting at `0px`. This is used internally only.

`$mq-show-breakpoints`: A SASS list of breakpoint names to display the breakpoint name of in the top-right corner of the browser. This variable should be commented out before launch to hide the breakpoint information.

## Functions
### `leastSquaresFit($map)`
Calculates a linear regression from a provided SASS map of values. The map key should be a breakpoint size (unitless) and the value should be the value desired at that screen size (also unitless).

Example: `font-size: leastSquaresFit((576: 24, 768: 24, 992: 34));`

### `rem-calc($values)`
Converts the provided values (can be a single or multiple space separated values) from pixels into REM. The pixel unit may be omitted (e.g. `10px` and `10` are equivalent).

Examples: `margin: rem-calc(0 10 15 20)` or `font-size: rem-calc(32)`

## Mixins
### `background-image($asset)`
Outputs the styles for a background image with the correct URL. Prepends the `$imagePath` to `$asset`.

Example: `@include background-image('my-image.png');`

### `background-svg($asset)`
Outputs the styles for an svg background image with the correct URL. Prepends the `$svgPath` to `$asset`.

Example: `@include background-svg('my-image.svg');`

### `ieOnly`
Allows targeting IE 10/11 only via media query.

Example: `@include ieOnly { /* IE only styles go here */ }`

### `inputPlaceholder`
Correctly outputs all required placeholder pseudo-class and psuedo-element styles (includes all relevant browser prefixes).

Example: `@include inputPlaceholder { /* Placeholder styles go here. */ }`

## Grid System
The included grid is a flexbox based grid.

### Config Options
All grid related setting variables are defined in the `scss/config/_grid.scss` file.

#### Grid Gutter
`$gutter`: Defines the base gutter size for the grid.

`$mq-gutter`: Allows defining a different gutter size based on breakpoint. This variable is expected to be a SASS map where the key is the breakpoint name and the value is the gutter size at that breakpoint. Breakpoints should be listed in order. `base` should be the first breakpoint and be set to the value of `$gutter`. This variable may be safely commented out/removed if there is no need for breakpoint based gutter sizes.

#### Grid Column Counts
The `$maxColCount` variable is an integer defining the maximum number of columns the grid should be allowed to contain.

The `$grid-col-counts` variable is a SASS list of integers. Fractional CSS classes are generated based on this list.

#### Grid Breakpoints
The `$grid-col-gen` variable is a SASS list of breakpoint names to auto-generate CSS classes for. Passing `false` as a value will also generate non-breakpoint constrained CSS classes.

See the `Media Queries` section of this readme for additional details on breakpoint related settings.

### Grid Static CSS Classes
#### `.row`
Defines a grid row. Includes negative margin on the sizes to account for column gutter.

#### `.col`
Defines a grid column. Includes column gutter padding styles. Width of `100%`. These styles are also part of the generated `.col-#{$fraction}` classes.

### Grid Generated CSS Classes
#### `.col-#{$fraction}` and `.col-#{$fraction}@#{$breakpoint}`
These classes are generated to define the column width based on the given fractions. Fractions are generated for all column counts in the `$grid-col-counts` list. (e.g. if `$grid-col-counts: (2, 3)` then `1/2`, `2/2`, `1/3`, `2/3`, and `3/3` will be the generated fractions).

The `#{$bp}` portion of the name is the name of the breakpoint the class will first apply on. Since all generated classes are mobile first, classes generated without a breakpoint name will apply until a class name with a breakpoint is used and classes at larger breakpoints will override the smaller ones.

Example with no breakpoint: `.col-4/12`

Example with breakpoint: `.col-4/12@md`

#### `.col-offset-#{$fraction}` and `.col-offser-#{$fraction}@#{$breakpoint}`
Defines the column offset based on the fraction and breakpoint (if provided). These are generated following the same rules for the `.col-#{$fraction}` and `#{$fraction}@#{$breakpoint}` classes.

Example with no breakpoint: `.col-offset-1/12`

Example with breakpoint: `.col-offset-1/12@md`

#### `.col-order-#{$order}` and `.col-order-#{$order}@#{$breakpoint}`
Since the grid is flex-based, you can use flex ordering to rearrange the markup using just CSS classes. These classes are provided to allow this be done easily from the markup side without writing additional CSS. The are generated from `0` through the value of `$maxColCount`. These are generated following the same rules for the `.col-#{$fraction}` and `#{$fraction}@#{$breakpoint}` classes.

NOTE: The browser default for flex order is `0`, so any columns that don't have these classes will always appear before ones that do. It is recommended to either not use these classes or ensure they are applied to all columns in a row.

Example with no breakpoint: `.col-order-5`

Example with breakpoint: `.col-order-5@md`

### Grid Mixins
#### `row()`
Makes the element a grid row. Equivalent to using the `.row` class.

#### `column($breakpoint: 0, $width: $maxColCount, $maxWidth: $maxColCount)`
Makes the element a grid column with the specified width. Can be contextualized per breakpoint. Includes the grid gutter styles.

`$breakpoint`: Defines the breakpoint at which this applies. `0` will be counted as not on a breakpoint. Other values will cause the column styles to be wrapped in a media query. It is recommended to only use named breakpoints. Default is `0`.

`$width`: The number of columns this column should occupy. Must be an integer. Default is the maximum number of columns in the grid (`100%` width).

`$maxWidth`: The total number of columns to take `$width` out of. If written as a fraction, it would be `$width/$maxWidth`. Default is the maximum number of columns in the grid.

Example with no breakpoint: `@include column($width: 5);`

Example with breakpoint: `@include column($breakpoint: md, $width: 5);`

#### `offset($breakpoint: 0, $width: $maxColCount, $maxWidth: $maxColCount)`
Should be used in conjunction with the `column()` mixin or element should otherwise be a grid column.

`$breakpoint`: Defines the breakpoint at which this applies. `0` will be counted as not on a breakpoint. Other values will cause the offset styles to be wrapped in a media query. It is recommended to only use named breakpoints. Default is `0`.

`$width`: The number of columns this column should be offset by. Must be an integer. Default is the maximum number of columns in the grid (`100%` width).

`$maxWidth`: The total number of columns to take `$width` out of. If written as a fraction, it would be `$width/$maxWidth`. Default is the maximum number of columns in the grid. 

Example with no breakpoint: `@include offset($width: 2);`

Example with breakpoint: `@include offset($breakpoint: md, $width: 2);`

## Placeholders
NOTE: SASS placeholders cannot be use inside a media query unless they are defined in an identical media query (and then can _only_ be used in that media query). All placeholders listed here are not inside a media query.

### `%full-width`
Makes an element be `100vw`.

### `%clearfix`
Ensures an element self-clears any floated children.