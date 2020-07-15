# Shadow DOM

 If custom elements are the way to create a new HTML (with a JS API), shadow DOM is the way you provide its HTML and CSS. The two APIs combine to make a component with self-contained HTML, CSS, and JavaScript.

Shadow DOM introduces scoped styles to the web platform.

## Some elements can't host a shadow tree.

- The browser already hosts its own internal shadow DOM for the element (`<textarea>, <input>`).
- It doesn't make sense for the element to host a shadow DOM (`<img>`).

## The `<slot>` element

Shadow DOM composes different DOM trees together using the `<slot>` element. Slots are placeholders inside your component that users can fill with their own markup. 

Slots don't physically move DOM; they render it at another location inside the shadow DOM.

A component can define zero or more slots in its shadow DOM. Slots can be empty or provide fallback content.

```html
<!-- Default slot. If there's more than one default slot, the first is used. -->
<slot></slot>
<slot>fallback content</slot> <!-- default slot with fallback content -->
```

It is also possible to have named slots:

```html
#shadow-root
  <div id="tabs">
    <slot id="tabsSlot" name="title"></slot> <!-- named slot -->
  </div>
  <div id="panels">
    <slot id="panelsSlot"></slot>
  </div>
```

and declare like so:

```html
<fancy-tabs>
  <button slot="title">Title</button>
  <button slot="title" selected>Title 2</button>
  <button slot="title">Title 3</button>
  <section>content panel 1</section>
  <section>content panel 2</section>
  <section>content panel 3</section>
</fancy-tabs>
```

## Component-defined styles

The most useful feature of shadow DOM is scoped CSS:

- CSS selectors from the outer page don't apply inside your component.
- Styles defined inside don't bleed out. They're scoped to the host element.

```html
<style>
  #heading {
    font-size: 32px;
  }
</style>
<h2 id="heading">...</h2>
```

This also works:

```html
<link rel="stylesheet" href="styles.css">
<h2 id="heading">...</h2>
```

## Styling

Self styling:
```html
<style>
  :host {
    display: block; /* by default, custom elements are display: inline */
    contain: content;
  }
</style>
```

Functional form `:host(<selector>)`:
```html
<style>
:host(:hover) {
  opacity: 1;
}
:host([disabled]) { /* style when host has disabled attribute. */
  background: grey;
  pointer-events: none;
  opacity: 0.4;
}
:host(.blue) {
  color: blue; /* color host when it has class="blue" */
}
:host(.pink) > #tabs {
  color: pink; /* color internal #tabs node when host has class="pink". */
}
</style>
```

Styling based on context: `:host-context(<selector>)`

```html
<body class="darktheme">
  <fancy-tabs>
    ...
  </fancy-tabs>
</body>
```

```js
:host-context(.darktheme) {
  color: white;
  background: black;
}
```

Styling distributed nodes: `::slotted(<compound-selector>)`

```html
<name-badge>
  <h2>Eric Bidelman</h2>
  <span class="title">
    Digital Jedi, <span class="company">Google</span>
  </span>
</name-badge>
```

```html
<style>
::slotted(h2) {
  margin: 0;
  font-weight: 300;
  color: red;
}
::slotted(.title) {
   color: orange;
}
/* DOESN'T WORK (can only select top-level nodes).
::slotted(.company) { ... }
*/
</style>
<slot></slot>
```

## Styling hooks

This allows users to change internal component css from outside.

Outside component:
```html
<!-- main page -->
<style>
  fancy-tabs {
    margin-bottom: 32px;
    --fancy-tabs-bg: black;
  }
</style>
<fancy-tabs background>...</fancy-tabs>
```

Inside Shadow DOM:
```css
:host([background]) {
  background: var(--fancy-tabs-bg, #9E9E9E);
  border-radius: 10px;
  padding: 10px;
}
```