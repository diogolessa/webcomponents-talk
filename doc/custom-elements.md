# Custom Elements

Allows web developers to create new HTML tags, beef-up existing HTML tags, or extend the components other developers have authored.

## Two distinct types:

1. An **autonomous custom element**, which is defined with no extends option. These types of custom elements have a local name equal to their defined name.

2. A **customized built-in element**, which is defined with an extends option. These types of custom elements have a local name equal to the value passed in their extends option, and their defined name is used as the value of the is attribute, which therefore must be a valid custom element name.

## Defining a custom element

```js
class Toast extends HTMLElement {...}
window.customElements.define('custom-toast', Toast);
```

## Rules on creating custom elements

1. The name of a custom element must contain a dash (-). This requirement is so the HTML parser can distinguish custom elements from regular elements.
2. You can't register the same tag more than once. Attempting to do so will throw a DOMException.
3. Custom elements cannot be self-closing because HTML only allows a few elements to be self-closing..

### `this` keyword

Notice that `this` inside a class definition refers to the DOM element itself i.e. the instance of the class.

## Properties and attributes

Reflecting a property is useful anywhere you want to **keep the element's DOM representation in sync with its JavaScript state.** One reason you might want to reflect a property is so user-defined styling applies when JS state changes.

```js
get disabled() {
  return this.hasAttribute('disabled');
}

set disabled(val) {
  // Reflect the value of `disabled` as an attribute.
  if (val) {
    this.setAttribute('disabled', '');
  } else {
    this.removeAttribute('disabled');
  }
  this.toggleDrawer();
}
```

## Custom element reactions

| name | Called when |
| ---- | ----------- |
| Constructor | An instance of the element is created or upgraded. Useful for initializing state, setting up event listeners, or creating a shadow dom. See the spec for restrictions on what you can do in the constructor. |
| connectedCallback | Called every time the element is inserted into the DOM. Useful for running setup code, such as fetching resources or rendering. Generally, you should try to delay work until this time. |
| disconnectedCallback | Called every time the element is removed from the DOM. Useful for running clean up code. |
| attributeChangedCallback(attrName, oldVal, newVal) | Called when an observed attribute has been added, removed, updated, or replaced. Also called for initial values when an element is created by the parser, or upgraded. Note: only attributes listed in the observedAttributes property will receive this callback. |
| adoptedCallback | The custom element has been moved into a new document (e.g. someone called document.adoptNode(el)). |

## Progressively enhanced HTML

Custom elements can be used before their definition is registered.

```js
customElements.whenDefined('app-drawer').then(() => {
  console.log('app-drawer defined');
});
```

It is possible to query for not defined custom elements using `':not(:defined)'` selector:

```css
foo-element:not(:defined) {
  display: inline-block;
  height: 100vh;
  opacity: 0;
  transition: opacity 0.3s ease-in-out;
}
```

## Creating elements that uses Shadow DOM and templates

Shadow DOM provides a way for an element to own, render, and style a chunk of DOM that's separate from the rest of the page.

Templates are an ideal placeholder for declaring the structure of a custom element.

```html
<template id="foo-template">
  <style>
    p { color: green; }
  </style>
  <p>I'm in Shadow DOM. My markup was stamped from a <template>.</p>
</template>

<script>
  let tmpl = document.querySelector('#foo-template');
  // If your code is inside of an HTML Import you'll need to change the above line to:
  // let tmpl = document.currentScript.ownerDocument.querySelector('#foo-template');

  customElements.define('foo-element', class extends HTMLElement {
    constructor() {
      super(); // always call super() first in the constructor.
      let shadowRoot = this.attachShadow({mode: 'open'});
      shadowRoot.appendChild(tmpl.content.cloneNode(true));
    }
    ...
  });
</script>
```

## Extending a custom element

```js
class FancyDrawer extends AppDrawer { ... }
customElements.define('fancy-app-drawer', FancyDrawer);
```

```html
<script>
  class FancyButton extends HTMLButtonElement { ... }
  customElements.define('fancy-button', FancyButton, {extends: 'button'});
</script>

<button is="fancy-button" disabled>Fancy button!</button>
```
