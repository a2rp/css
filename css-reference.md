# CSS Reference

A practical reference to CSS syntax, selectors, the cascade, values, at-rules, common properties, layout, responsive design, and accessibility. It is organized by purpose so related features are easy to find.

CSS evolves through modules. Newer features can have different support across browsers, so check the target browsers before relying on them.

## Contents

- [CSS rule structure](#css-rule-structure)
- [Selectors](#selectors)
- [The cascade](#the-cascade)
- [Values, units, and functions](#values-units-and-functions)
- [At-rules](#at-rules)
- [Properties by purpose](#properties-by-purpose)
- [Responsive design](#responsive-design)
- [Accessibility](#accessibility)
- [Glossary](#glossary)

## CSS rule structure

A style rule has a selector and a declaration block. Each declaration pairs a property with a value.

```css
.card {
  background-color: white;
  border: 1px solid #d0d7de;
  border-radius: 0.75rem;
  padding: 1rem;
}
```

- `.card` is the selector.
- `background-color`, `border`, `border-radius`, and `padding` are properties.
- The text after each colon is a value.
- A semicolon ends each declaration. The final semicolon is optional, but keeping it helps when adding declarations.
- A declaration that the browser does not recognize or cannot parse is ignored.

Use comments to explain intent or mark sections:

```css
/* Main content card */
.card {
  padding: 1rem;
}
```

### Connecting a stylesheet

The usual way to apply a reusable stylesheet is with a `link` element in the document head:

```html
<link rel="stylesheet" href="styles.css">
```

The path in `url()` values is resolved relative to the CSS file. A `style` element can hold page-specific CSS. A `style` attribute applies declarations to one element and is harder to maintain, so prefer a stylesheet for reusable rules.

## Selectors

Selectors choose which elements a rule matches.

| Selector | Matches |
| --- | --- |
| `*` | Every element. |
| `p` | Every `p` element. |
| `.notice` | Elements whose class list contains `notice`. |
| `#main` | The element whose ID is `main`. IDs should be unique in a document. |
| `[disabled]` | Elements with a `disabled` attribute. |
| `[type="email"]` | Elements whose `type` attribute equals `email`. |
| `h1, h2` | Both `h1` and `h2` elements. |
| `:root` | The document root element. Commonly used to declare global custom properties. |

### Combinators

| Pattern | Relationship |
| --- | --- |
| `A B` | A `B` descendant at any depth inside `A`. |
| `A > B` | A `B` direct child of `A`. |
| `A + B` | A `B` immediately following `A` as its next sibling. |
| `A ~ B` | A `B` following `A` among its siblings. |

### Attribute selectors

| Pattern | Meaning |
| --- | --- |
| `[name]` | Attribute exists. |
| `[name="value"]` | Exact value. |
| `[name~="word"]` | A whitespace-separated value contains `word`. |
| `[name|="en"]` | Value is `en` or begins with `en-`. |
| `[name^="prefix"]` | Value begins with `prefix`. |
| `[name$="suffix"]` | Value ends with `suffix`. |
| `[name*="part"]` | Value contains `part`. |
| `[name="value" i]` | Compare ASCII letters without case sensitivity. |

### Common pseudo-classes

Pseudo-classes match an element based on its state, position, or relationship.

| Group | Selectors | Typical use |
| --- | --- | --- |
| Relationships | `:root`, `:empty`, `:has()`, `:is()`, `:where()`, `:not()` | Match the root, empty elements, or elements based on related elements. |
| Child positions | `:first-child`, `:last-child`, `:only-child`, `:nth-child()`, `:nth-last-child()` | Match an element by its position among siblings. |
| Type positions | `:first-of-type`, `:last-of-type`, `:only-of-type`, `:nth-of-type()`, `:nth-last-of-type()` | Match an element by position among siblings of the same type. |
| Links | `:link`, `:visited`, `:any-link`, `:target` | Match links or the current URL fragment target. Browsers restrict styles on visited links for privacy. |
| User interaction | `:hover`, `:active`, `:focus`, `:focus-visible`, `:focus-within` | Match pointer, activation, or keyboard focus states. Do not rely on hover alone for important information. |
| Forms | `:enabled`, `:disabled`, `:checked`, `:indeterminate`, `:required`, `:optional` | Match common form-control states. |
| Form validity | `:valid`, `:invalid`, `:in-range`, `:out-of-range`, `:read-only`, `:read-write`, `:placeholder-shown` | Match controls based on value and editing state. |
| Language and direction | `:lang()`, `:dir()` | Match content language or direction. |
| Disclosure and dialog | `:open`, `:modal`, `:popover-open` | Match supported open dialog, disclosure, or popover states. |

The `An+B` pattern used by `:nth-child()` selects positions. For example, `li:nth-child(2n)` matches every even-positioned list item. The `of` form can limit the count to matching siblings, such as `:nth-child(odd of .item)`.

### Common pseudo-elements

Pseudo-elements target a particular part of an element or create a styling box.

| Pseudo-element | Purpose |
| --- | --- |
| `::before`, `::after` | Create a generated box before or after an element's content. Set `content` for the box to appear. Do not use generated text for essential information. |
| `::first-letter`, `::first-line` | Style the first letter or first formatted line of text. |
| `::marker` | Style a list item's marker. |
| `::selection` | Style selected text. |
| `::placeholder` | Style placeholder text in a form control. Keep it readable and do not use it as the only label. |
| `::backdrop` | Style the backdrop behind a modal dialog or other top-layer element. |
| `::file-selector-button` | Style the button portion of a file input. |
| `::part()` | Select an exposed part of a web component. |
| `::slotted()` | Select a slotted element from a shadow tree stylesheet. |

## The cascade

The cascade decides which declaration supplies a property's value when several declarations match the same element. A useful simplified order is:

1. Compare origin and importance, including browser defaults, user styles, author styles, and transitions.
2. Compare cascade layer order where layers apply. Normal declarations outside layers outrank normal layered declarations. For important declarations, layer precedence is reversed.
3. Compare selector specificity.
4. Compare scoping proximity when scoped rules apply.
5. If still tied, the declaration that appears later wins.

The exact cascade has additional rules for important declarations, encapsulation, animations, and transitions. Avoid using `!important` to patch unclear selector conflicts. Fix the source of the conflict or use a deliberate layer.

### Specificity

Specificity is compared as three groups, often written `(IDs, classes, types)`:

- ID selectors add to the first group: `#menu` is `(1, 0, 0)`.
- Class, attribute, and pseudo-class selectors add to the second group: `.menu`, `[open]`, and `:focus` are `(0, 1, 0)`.
- Type and pseudo-element selectors add to the third group: `button` and `::before` are `(0, 0, 1)`.
- The universal selector and combinators add no specificity.
- `:where()` always has zero specificity.
- `:is()`, `:not()`, and `:has()` take the specificity of their most specific argument.

Compare groups from left to right. A larger ID group beats any number of class or type selectors. Inline style declarations have their own high precedence in the author origin. Important declarations are sorted by importance before normal specificity comparison.

### Inheritance and CSS-wide keywords

| Keyword | Effect |
| --- | --- |
| `inherit` | Use the computed value from the parent. |
| `initial` | Use the property's initial value from its definition. |
| `unset` | Behave as `inherit` for inherited properties and `initial` for other properties. |
| `revert` | Roll the value back to a lower cascade origin. |
| `revert-layer` | Roll the value back to a lower-precedence cascade layer. |

Inherited properties commonly include text and font properties. Box layout properties such as `margin` and `padding` generally do not inherit.

### Cascade layers

Layers make precedence between groups of rules explicit. Declare their order near the start of a stylesheet.

```css
@layer reset, base, components, utilities;
@layer base {
  body {
    font-family: system-ui, sans-serif;
  }
}
@layer components {
  .card {
    padding: 1rem;
  }
}
```

For normal declarations, later named layers outrank earlier named layers, and unlayered normal declarations outrank layered ones. Important declarations reverse the layer order.

## Values, units, and functions

### Common value types

| Type | Examples |
| --- | --- |
| Keywords | `auto`, `none`, `normal`, `inherit`, `initial`, `unset`, `revert` |
| Numbers | `0`, `1.5`, `-2` |
| Percentages | `50%`, resolved according to the property's rules and containing context. |
| Lengths | `1rem`, `24px`, `2vw` |
| Colors | `rebeccapurple`, `#336699`, `rgb(20 40 60)` |
| Images | `url("image.png")`, `linear-gradient(white, gray)` |
| Strings | `"Open menu"`, mainly used by generated content and font names. |
| Lists | Several values separated by spaces or commas, depending on the property. |

### Units

| Unit group | Units | Notes |
| --- | --- | --- |
| Absolute length | `px`, `cm`, `mm`, `Q`, `in`, `pt`, `pc` | CSS pixels are reference units and do not always equal one physical screen pixel. |
| Font-relative length | `em`, `rem`, `ex`, `ch`, `cap`, `ic`, `lh`, `rlh` | Relative to a font metric or line height. `rem` uses the root element's font size. |
| Viewport length | `vw`, `vh`, `vi`, `vb`, `vmin`, `vmax` | Relative to viewport dimensions or logical axes. `sv*`, `lv*`, and `dv*` variants represent small, large, and dynamic viewports. |
| Container length | `cqw`, `cqh`, `cqi`, `cqb`, `cqmin`, `cqmax` | Relative to a query container's dimensions. |
| Angle | `deg`, `rad`, `grad`, `turn` | Used by rotation and gradient directions. |
| Time | `s`, `ms` | Used by transitions and animations. |
| Frequency | `Hz`, `kHz` | Available for speech-related media values. |
| Resolution | `dpi`, `dpcm`, `dppx` | Common in resolution media queries. |
| Grid fraction | `fr` | Shares available space in CSS Grid tracks. |

Use `rem` for many page-level sizes, `em` when a size should scale with the current element's font, and percentages or container units when sizing should follow a containing box. Choose based on the relationship you want.

### Common CSS functions

| Function | Purpose |
| --- | --- |
| `var(--name, fallback)` | Read a custom property, optionally using a fallback. |
| `calc()` | Calculate a value from compatible units. |
| `min()`, `max()`, `clamp()` | Choose a minimum, maximum, or bounded fluid value. |
| `rgb()`, `hsl()`, `hwb()`, `lab()`, `lch()`, `oklab()`, `oklch()`, `color()` | Express colors in different color models. |
| `color-mix()` | Mix two colors. |
| `url()` | Refer to an external resource such as a background image or font file. |
| `linear-gradient()`, `radial-gradient()`, `conic-gradient()` | Generate gradients as images. |
| `translate()`, `rotate()`, `scale()`, `matrix()` | Transform an element. |
| `cubic-bezier()`, `steps()`, `linear()` | Define animation or transition timing. |
| `counter()`, `counters()` | Read CSS counters for generated content. |
| `repeat()`, `minmax()`, `fit-content()` | Define flexible grid tracks. |
| `env()` | Read a user-agent environment variable, such as a safe-area inset. |

Example of fluid sizing:

```css
.page-title {
  font-size: clamp(2rem, 1.2rem + 3vw, 4rem);
}
```

### Custom properties

Custom properties hold reusable values and participate in the cascade.

```css
:root {
  --color-brand: #1257a6;
  --space-md: 1rem;
}
.button {
  background-color: var(--color-brand);
  padding: var(--space-md);
}
```

Names begin with `--` and are read with `var()`. They inherit by default and are not automatically type-checked. `@property` can register a custom property with a syntax, initial value, and inheritance behavior.

## At-rules

At-rules begin with `@` and provide stylesheet-level instructions or conditional groups.

| At-rule | Purpose |
| --- | --- |
| `@charset` | Declares stylesheet encoding when required. It must appear first. UTF-8 is the usual encoding. |
| `@import` | Imports another stylesheet. Place imports before ordinary style rules. |
| `@media` | Applies rules when a media condition matches, such as viewport width or user preference. |
| `@supports` | Applies rules when the browser supports a CSS declaration or selector. |
| `@container` | Applies rules based on a query container's size or style. |
| `@layer` | Creates and orders cascade layers. |
| `@scope` | Limits where selectors in a group of rules apply. |
| `@font-face` | Defines a downloadable or locally available font face. |
| `@font-feature-values` | Defines names for selected OpenType font features. |
| `@font-palette-values` | Defines a named palette for a font that supports palettes. |
| `@keyframes` | Defines the stages of a CSS animation. |
| `@property` | Registers a typed custom property and defines its initial and inheritance behavior. |
| `@counter-style` | Defines a custom counter or list-marker style. |
| `@page` | Defines page-box settings for paged media, such as printing. |
| `@starting-style` | Provides a starting style for elements entering or leaving a rendered state. |
| `@view-transition` | Configures a document view transition where supported. |
| `@position-try` | Defines fallback position options for anchor-positioned elements where supported. |

The set and support of at-rules evolves. Use conditional rules when a newer feature needs a fallback.

## Properties by purpose

The tables list widely used properties and related groups. A property can have many values and detailed rules. A shorthand such as `margin` sets several related longhand properties at once.

### Box sizing and spacing

| Properties | Purpose |
| --- | --- |
| `box-sizing` | Choose whether declared width and height include padding and border. `border-box` is a common page-wide choice. |
| `width`, `min-width`, `max-width`, `height`, `min-height`, `max-height` | Set physical dimensions and their limits. |
| `inline-size`, `min-inline-size`, `max-inline-size`, `block-size`, `min-block-size`, `max-block-size` | Set dimensions on logical inline and block axes. |
| `aspect-ratio` | Suggest a preferred width-to-height ratio. |
| `margin`, `margin-block`, `margin-inline`, `margin-top`, `margin-right`, `margin-bottom`, `margin-left` | Control outer spacing. |
| `padding`, `padding-block`, `padding-inline`, `padding-top`, `padding-right`, `padding-bottom`, `padding-left` | Control inner spacing. |
| `border`, `border-block`, `border-inline`, `border-top`, `border-right`, `border-bottom`, `border-left` | Set border width, style, and color. |
| `border-width`, `border-style`, `border-color`, `border-radius` | Set border details and rounded corners. |
| `outline`, `outline-width`, `outline-style`, `outline-color`, `outline-offset` | Draw an outline outside the border, often for focus indication. |
| `box-shadow` | Draw one or more shadows around a box. |
| `box-decoration-break` | Control how a box's decoration is split across fragments or lines. |

### Display, flow, and positioning

| Properties | Purpose |
| --- | --- |
| `display` | Select the box and layout behavior, such as `block`, `inline`, `flex`, `grid`, or `none`. |
| `position`, `top`, `right`, `bottom`, `left`, `inset`, `inset-block`, `inset-inline` | Position an element relative to its normal place, containing block, or viewport. |
| `z-index` | Set stack order within the applicable stacking context. |
| `float`, `clear` | Wrap text around floated content and control where following content may sit. |
| `visibility` | Hide an element while keeping its layout box. |
| `isolation` | Create a new stacking context with `isolate`. |
| `contain`, `container`, `container-name`, `container-type` | Control containment and define query containers. |
| `content-visibility` | Let the browser skip rendering work for content that is not currently needed. |
| `columns`, `column-count`, `column-width`, `column-gap`, `column-rule`, `column-span` | Create and control multi-column text flow. |

### Flexbox

| Properties | Purpose |
| --- | --- |
| `flex-direction` | Choose row or column direction and its reverse. |
| `flex-wrap` | Allow items to wrap onto additional lines. |
| `flex-flow` | Shorthand for direction and wrapping. |
| `justify-content` | Distribute items along the main axis. |
| `align-items`, `align-self` | Align items or one item on the cross axis. |
| `align-content` | Distribute multiple flex lines when extra cross-axis space exists. |
| `flex` | Shorthand for grow, shrink, and basis. |
| `flex-grow`, `flex-shrink`, `flex-basis` | Control how an item sizes within the flex container. |
| `order` | Change visual order. Keep source order meaningful for reading and keyboard navigation. |
| `gap`, `row-gap`, `column-gap` | Add space between flex items or lines. |

### CSS Grid

| Properties | Purpose |
| --- | --- |
| `grid-template-columns`, `grid-template-rows`, `grid-template-areas` | Define explicit grid tracks and named areas. |
| `grid-auto-columns`, `grid-auto-rows`, `grid-auto-flow` | Control automatically created tracks and placement. |
| `grid-column`, `grid-row`, `grid-area` | Place an item across grid lines or into an area. |
| `grid-template`, `grid` | Shorthands for grid definitions. |
| `gap`, `row-gap`, `column-gap` | Set spacing between tracks. |
| `subgrid` | Let a nested grid use tracks from its parent grid where supported. |

Grid track functions and keywords include `fr`, `repeat()`, `minmax()`, `min-content`, `max-content`, `fit-content()`, and `subgrid`.

### Alignment

| Properties | Purpose |
| --- | --- |
| `justify-content`, `align-content`, `place-content` | Align or distribute content along container axes. |
| `justify-items`, `align-items`, `place-items` | Align items within their grid areas or container. |
| `justify-self`, `align-self`, `place-self` | Align one item. |
| `vertical-align` | Align inline-level content or table-cell content. It does not generally align block elements vertically. |

Alignment values include `start`, `end`, `center`, `stretch`, `space-between`, `space-around`, and `space-evenly`, where supported by the property.

### Typography and text

| Properties | Purpose |
| --- | --- |
| `font`, `font-family`, `font-size`, `font-style`, `font-weight`, `font-stretch` | Select and configure fonts. |
| `font-variant`, `font-feature-settings`, `font-variation-settings`, `font-optical-sizing` | Control font variants and advanced font features. Prefer higher-level properties when available. |
| `line-height` | Set line-box height. Unitless values scale with the element's font size. |
| `letter-spacing`, `word-spacing` | Adjust spacing between letters or words. |
| `text-align`, `text-align-last`, `text-indent` | Align and indent text. |
| `text-transform`, `text-decoration`, `text-decoration-line`, `text-decoration-style`, `text-decoration-color`, `text-decoration-thickness`, `text-underline-offset` | Control text transformation and decoration. |
| `text-overflow`, `overflow-wrap`, `word-break`, `white-space`, `hyphens` | Control wrapping and overflow of text. |
| `text-shadow` | Draw a shadow behind text. |
| `text-wrap`, `text-wrap-mode`, `text-wrap-style` | Control line wrapping behavior where supported. |
| `vertical-align`, `direction`, `unicode-bidi`, `writing-mode`, `text-orientation` | Control inline alignment, direction, and writing mode. |

### Color, backgrounds, and borders

| Properties | Purpose |
| --- | --- |
| `color` | Set the foreground text color. |
| `background`, `background-color`, `background-image` | Set a background color or one or more background images. |
| `background-position`, `background-size`, `background-repeat`, `background-origin`, `background-clip`, `background-attachment` | Position, size, repeat, and constrain background images. |
| `background-blend-mode` | Blend background layers. |
| `opacity` | Set whole-element opacity. Values below `1` can create a stacking context. |
| `border-color`, `border-style`, `border-width`, `border-radius` | Style borders and corners. |
| `box-shadow`, `outline`, `outline-offset` | Add shadows or an outline outside a box. |
| `accent-color`, `color-scheme` | Set an accent color for supported controls and declare supported light or dark schemes. |

### Lists, tables, and generated content

| Properties | Purpose |
| --- | --- |
| `list-style`, `list-style-type`, `list-style-position`, `list-style-image` | Style list markers. |
| `border-collapse`, `border-spacing`, `caption-side`, `table-layout`, `empty-cells` | Control table borders, sizing, captions, and empty cells. |
| `content` | Set generated content for supported pseudo-elements. It is not a replacement for meaningful document content. |
| `counter-reset`, `counter-increment`, `counter-set` | Create and update CSS counters. |

### Images and other replaced elements

| Properties | Purpose |
| --- | --- |
| `object-fit`, `object-position` | Fit and position replaced content such as an image or video inside its box. |
| `aspect-ratio` | Preserve a preferred ratio when sizing a box. |
| `image-rendering` | Give the browser a hint about image scaling. |
| `fill`, `stroke`, `stroke-width`, `stroke-linecap`, `stroke-linejoin` | Style SVG graphics where the property applies. |

### Overflow and scrolling

| Properties | Purpose |
| --- | --- |
| `overflow`, `overflow-x`, `overflow-y`, `overflow-block`, `overflow-inline` | Control content that exceeds a box. |
| `overscroll-behavior`, `overscroll-behavior-x`, `overscroll-behavior-y` | Control scroll chaining at a scroll boundary. |
| `scroll-behavior`, `scroll-snap-type`, `scroll-snap-align`, `scroll-snap-stop` | Control smooth scrolling and scroll snapping. Respect reduced-motion preferences. |
| `scroll-margin`, `scroll-padding` | Adjust target and snap positions within a scroll container. |
| `scrollbar-color`, `scrollbar-width` | Style scrollbars where supported. Keep them usable and visible. |

### Interaction and user interface

| Properties | Purpose |
| --- | --- |
| `cursor` | Choose the pointer cursor. |
| `pointer-events` | Control whether an element can be a pointer-event target. |
| `user-select` | Control selection behavior. Avoid preventing selection of useful content. |
| `resize` | Allow resizing of supported elements such as text areas. |
| `appearance` | Control native control appearance. Preserve clear affordances and focus indication. |
| `caret-color`, `accent-color` | Set text-caret and supported control accent colors. |
| `touch-action` | Control which touch gestures the browser may handle. |
| `user-modify` | Non-standard legacy property. Do not use for new work. |

### Transforms, transitions, and animation

| Properties | Purpose |
| --- | --- |
| `transform`, `transform-origin`, `transform-box` | Translate, rotate, scale, or skew an element without changing normal flow. |
| `translate`, `rotate`, `scale` | Individual transform properties. |
| `transition`, `transition-property`, `transition-duration`, `transition-timing-function`, `transition-delay`, `transition-behavior` | Animate a change between property values. |
| `animation`, `animation-name`, `animation-duration`, `animation-timing-function`, `animation-delay`, `animation-iteration-count`, `animation-direction`, `animation-fill-mode`, `animation-play-state`, `animation-composition` | Configure keyframe animations. |
| `animation-timeline`, `animation-range` | Connect animations to timelines, including scroll-driven timelines where supported. |
| `offset-path`, `offset-distance`, `offset-rotate`, `offset-anchor`, `offset-position` | Move an element along a motion path. |
| `will-change` | Hint at an upcoming change. Use sparingly and remove when it is no longer needed. |

### Filters, masking, and effects

| Properties | Purpose |
| --- | --- |
| `filter`, `backdrop-filter` | Apply visual filters to an element or the content behind it. |
| `mix-blend-mode`, `background-blend-mode` | Blend a rendered element or background layers with content beneath. |
| `clip-path`, `clip-rule` | Clip an element to a shape. |
| `mask`, `mask-image`, `mask-mode`, `mask-position`, `mask-size`, `mask-repeat` | Use an image or shape as a mask. |
| `shape-outside`, `shape-margin`, `shape-image-threshold` | Shape text wrapping around floated elements. |

### Anchor positioning and view transitions

These newer features are evolving. Check support and provide a usable fallback.

| Properties or selectors | Purpose |
| --- | --- |
| `anchor-name`, `position-anchor`, `position-area`, `position-try`, `position-try-fallbacks` | Connect a positioned element to an anchor and select a placement. |
| `view-transition-name`, `view-transition-class` | Name elements participating in a view transition. |
| `::view-transition`, `::view-transition-group()`, `::view-transition-image-pair()`, `::view-transition-old()`, `::view-transition-new()` | Select the generated parts of a view transition. |

## Responsive design

Use flexible layout and intrinsic sizing first. Add breakpoints when the content needs them, rather than choosing breakpoints only for a particular device model.

```css
.page {
  width: min(100% - 2rem, 72rem);
  margin-inline: auto;
}
.card-list {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 16rem), 1fr));
  gap: 1rem;
}
```

### Media queries

`@media` can query viewport dimensions, media type, resolution, orientation, color scheme, contrast preference, and motion preference.

```css
@media (width >= 48rem) {
  .layout {
    grid-template-columns: 16rem minmax(0, 1fr);
  }
}
@media (prefers-reduced-motion: reduce) {
  .animated-element {
    animation-duration: 0.01ms;
    animation-iteration-count: 1;
    scroll-behavior: auto;
    transition-duration: 0.01ms;
  }
}
```

Range syntax is a modern media query form. Use a conventional query form when a project's target browsers require it.

### Container queries

A container must be established before descendants can query its size.

```css
.card-list {
  container-type: inline-size;
}
@container (width >= 36rem) {
  .card {
    display: grid;
    grid-template-columns: 12rem minmax(0, 1fr);
  }
}
```

Container queries help a component respond to the space it receives, independent of the viewport size.

## Accessibility

- Preserve visible keyboard focus. `:focus-visible` can style focus without removing the browser's keyboard affordance.
- Keep text and controls readable with sufficient contrast in every supported theme.
- Do not communicate meaning through color alone.
- Do not hide essential content with `display: none`, `visibility: hidden`, clipping, or generated content.
- Respect `prefers-reduced-motion`, `prefers-color-scheme`, `forced-colors`, and contrast preferences when they apply.
- Keep focus order and reading order meaningful. CSS visual reordering does not change the underlying document order.
- Ensure hover-only interactions also work with keyboard and touch input.
- Avoid fixed dimensions that make content unusable when text is zoomed or enlarged.
- Do not remove outlines unless an equally visible focus style replaces them.

Example focus style:

```css
:focus-visible {
  outline: 3px solid currentColor;
  outline-offset: 3px;
}
```

## Browser support and fallbacks

- Start with a usable baseline, then add enhancements.
- Use `@supports` to guard features when a fallback is needed.
- Use standard property names. Prefixes are usually handled by build tools when a project's browser targets need them.
- Test layout at narrow widths, zoomed text, keyboard use, and more than one browser engine.
- New syntax can be ignored by older browsers. Put a valid fallback declaration before an enhanced one when appropriate.

```css
.panel {
  padding: 1rem;
  padding-inline: clamp(1rem, 4vw, 2rem);
}
@supports (selector(:has(*))) {
  .field:has(input:invalid) {
    border-color: #b42318;
  }
}
```

## Glossary

| Term | Meaning |
| --- | --- |
| Declaration | One property and value pair, such as `color: navy`. |
| Declaration block | The braces and declarations belonging to a style rule. |
| Selector | A pattern that matches elements. |
| Specificity | A selector's weight when otherwise comparable declarations compete. |
| Cascade | The process that chooses a value from applicable declarations. |
| Inheritance | The passing of selected computed values from a parent to its descendants. |
| Computed value | A value after the cascade and inheritance have been resolved. |
| Containing block | The reference box used by some sizing and positioning calculations. |
| Formatting context | A layout environment that determines how boxes interact. |
| Stacking context | An isolated group of boxes ordered together along the z-axis. |
| Replaced element | An element whose rendered content comes from an external resource or native widget, such as an image or form control. |
| Custom property | An author-defined property, normally named with a `--` prefix. |
| Cascade layer | A named or anonymous group of rules whose precedence can be ordered. |
| Media query | A conditional rule based on the output medium or environment. |
| Container query | A conditional rule based on a query container rather than only the viewport. |
