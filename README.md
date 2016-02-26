# myCSS

This is the way Trevan writes CSS (Sass!). 

## Architecture

This architectural guideline serves to cut down the amount of time spent figuring out where new styles should go, where existing styles are and also limits the amount of duplication. An effort must be placed on keeping CSS modular and not repetitive.

I follow the following general file structure.

```
css
├── global
│   ├── _base.scss
│   ├── _mixins.scss
│   ├── _variables.scss
│   ├── _layout.scss
│   └── etc...
│
├── component
│   ├── _button.scss
│   ├── _page-title.scss
│   └── etc...
│
├── module
│   ├── _header.scss
│   ├── _footer.scss
│   ├── _modal.scss
│   └── etc...
│
├── vendor
│   ├── _third-party.scss
```

#### Global

All "base" and "global" styles should go here. Base styles are those involving resets, `html` and `body` styles, etc. Global styles are things that are either used over and over again (layout wrappers, for example).

#### Modules

Things should be broken down into reusable components whenever possible. Examples of modules include buttons, forms, sidebars, articles, comments, etc. Modules should be reusable in a number of different locations.

#### Components

Components compose larger pieces of a UI, comprised of multiple modules. For instance, a page title module might be used in a page header component, but the page header component would contain all specific styles related to the page header (specifically layout).


## Naming

I take inspiration from the [BEM naming](https://en.bem.info/tools/bem/bem-naming/) method but don't strictly follow it.

##### 1. Strive to plut classes on **every element**.

##### 2. Naming starts at the component level (The "B" or block in BEM).

```
<div class="slider"></div> // Component
```

##### 3. Children of components inherit that component's name and are separated by **two underscores** (the "E" or elements in BEM).

```
<div class="slider__slide"></div> // Component child
```

##### 4. Grandchildren of components are written the same as children. Everything under a parent component is a "child".

```
<div class="slider__btn"> // Component grandchild
```


##### 5. State modifiers (the "M" in BEM) are denoted by appending a new class prepended with double dashes.

They are typically used for state changes or categorical changes.

```
<div class="slider__slide --active"></div> // Denotes a state change to the element
```

There are browser inconsistencies in rendering classes starting with dashes, but the mixin below solves that!

## Indentation

"Hard" tabs. I use tabs so every developer can enjoy their own preference of 2 or 4 level indentation (I prefer 2 levels).


## White space

##### 1. There should always be a blank line between selectors.

```
// Good
.example {
    display: block;

    &::before {
        content: "test";
    }
}

.other-example {
    display: block;
}

// Bad
.example {
    display: block;
    &::before {
        content: "test";
    }
}
.other-example {
    display: block;
}
```

##### 2. There should always be a space after the selector name and before the opening bracket.

```
// Good
.example {
    display: block;
}

// Bad
.example{
    display: block;
}
```

##### 3. There should always be a space between the property name and the property value.

```
// Good
display: block;

// Bad
display:block;
```

##### 4. Property declarations should always be on their own line.

```
// Good
.example {
    display: block;
    width: 50%;
}

// Bad
.example {
    display:block; width: 50%;
}
```


## Order of properties

Properties should be declared in this order:

1. Display (`display`, `visibility`)
2. Position (`position`, `float`, `top`, `right`, `margin` etc.)
3. Box model (`padding`, `width`, `height`)
4. Colors and typography
5. Other


## Property declarations

- Include a semi-colon at the end of all declarations, including the last declaration in a declaration block.
- Use shorthand syntax whenever possible (`margin: 0 0 5em` instead of `margin-bottom: 5em`)
- Shorthand syntax should follow the TRBL (top/right/bottom/left) order.
- Use lowercase hex codes for colors, or rgba() if opacity is needed.
- Shorten color values when possible (`#fff instead of #FFFFFF`).
- Use double quotes instead of single quotes (`font-family: "Helvetica"` and `content: ""`).
- Quote attribute values in selectors (`input[type="radio"]`).
- Avoid specifying units for zero-values (`margin: 0` instead of `margin: 0px`).
- A space should follow each comma in comma-separated property.


## Comments

```
/* ==========================================================================
    Section comment
========================================================================== */

.selector {
    display: block;
}

// Description comment
.selector__child {
    display: inline;
}
```


## Sass

#### Nesting

Nesting selectors should be avoided.

#### @__

It's recommended to nest child components under their parents in Sass. This makes the code easier to understand and follow.

```
.slider {
    &__slide {...}

    &__btn {...}
}
```

#### Variables

Variables should be named semantically and not be limited to certain colors or values. For instance, instead of naming a variable for the color blue `$blue`, it should be named more loosely (like $`secondary`) so that the color or value can be changed without impacting the meaning of the variable name.

#### Mixins

Mixins are awesome. Here are some I like to use a lot.

**State mixin**

The state mixin allows you to include a state class using double dashes with consistent behavior in all modern browsers.

```
@mixin state($class) {
    &[class*='--#{$class}'] {
        @content;
    }
}
```

It should be used in Sass like so:

```
.slider {
    @include state(active);
}

// Will out put .slider[class*='--active']
```

**Breakpoint mixin**

```
/* ==========================================================================
    Media queries
========================================================================== */

@mixin breakpoint($size) {
    @if $size == small {
        @media (min-width: 30em) { @content; }
    }
    @else if $size == medium {
        @media (min-width: 50em) { @content; }
    }
    @else if $size == large {
        @media (min-width: 60em) { @content; }
    }
    @else if $size == xlarge {
        @media (min-width: 75em) { @content; }
    }
    @else {
        @media (min-width: $size) { @content; }
    }
}
```

**Font-size mixin**

This mixin allows you to use the REM sizing method, but fallback to a pixel value in non REM supporting browsers. The cool thing about this mixin is that it allows you to declare your font sizes by pixel value, and not worry about calculations.

```
/* ==========================================================================
    Px to rem
========================================================================== */

@mixin font-size($pxSize: 1) {
    font-size: $pxSize + px;
    font-size: ($pxSize / 16) + rem;
}
```

**Clearfix mixin**

Since I aim to not have "classitis" in our markup, the clearfix mixin works great.

```
/* ==========================================================================
    Clearfix
========================================================================== */

@mixin clearfix {
    &:after {
        display: table;
        clear: both;
        content: "";
    }
}
```

#### @extend

I don't totally follow the OOCSS (Object Oriented CSS) methodology, but I take inspiration from the re-use of classes. However, instead of adding multiple classes to elements in the markup, I rely on Sass's `@extend` directive and placeholder selectors. For instance, a "button" module might simply contain a placeholder selector that I use to create individual buttons with `@extend`.

```
%btn {
    // base button styles
}

.btn--pri {
    @extend %btn;
    // primary button styles
}
```
