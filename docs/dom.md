---
layout: default
id: dom
title: DOM utilities
prev: architecture
next: element
---

BunnyJS provides helper functions to work with Document Object Model (DOM).

```javascript
import { ... } from 'bunnyjs/src/utils/DOM';
```

or

```html
<script src="https://unpkg.com/bunnyjs/dist/utils-dom.min.js"></script>
```

## New global constants

DOM utils registers new global constants which can be used in type events. Now you don't need to remember all the `keyCode`'s.

- KEY_BACKSPACE
- KEY_TAB
- KEY_ENTER
- KEY_SHIFT
- KEY_CTRL
- KEY_ALT
- KEY_ESCAPE
- KEY_SPACE
- KEY_PAGE_UP
- KEY_PAGE_DOWN
- KEY_ARROW_LEFT
- KEY_ARROW_UP
- KEY_ARROW_RIGHT
- KEY_ARROW_DOWN
- KEY_DELETE

Example:

```javascript
customBtn.addEventListener('keydown', e => {
  if (e.keyCode === KEY_SPACE) {
    // ...
  }
})
```

## General helper functions

### ready(callback)

**ready()** function registers a callback function which will be called on `DOMContentLoaded` event (when DOM is ready and all the DOM functions like `document.getElementById()` can be used or components - initiated)

If `DOMContentLoaded` event was already fired, callback will be executed immediately.

```javascript
ready(() => {
  document.getElementById('login').addEventListener('click', () => {
    console.log('Login button clicked');
  });
});
```

### htmlToNode(html) : Node

Generates DOM Node from an HTML string

```javascript
document.body.appendChild(htmlToNode('<div>Hello, BunnyJS!</div>'))
```

### appendHtml(parent, html)

Append an HTML to parent element.

```javascript
appendHtml(document.body, '<div>Hello, BunnyJS!</div>');
```

### isElementInside(parentElement, childElement) : Boolean

Need to check if your element is inside another? Here you can!

### makeAccessible(element, tabIndex = 0, role = 'button')

Have a custom component, button, which needs to be accessible? Do it!

makeAccessible() adds a tabindex, role and simulates a click event when user presses Enter or Space.

## Event helper functions

### addEvent(element, eventName, callback) : Number

Adds event listener to element and stores a function in this element's custom property and **returns** unique eventIndex which can be used to remove event listener later - even anonymous functions, component methods, functions with arguments.

Ever wanted to define an event listener as a separate object method which you could pass to `addEventListener()` without a headache, for example `Component.onBodyClicked`? Now you can!

Simple example with object

```javascript
const Component = {
  docBodyClickEventId: null,
  anonymousEventId: null,

  init(param1, param2) {
    this.docBodyClickEventId = addEvent(document.body, 'click', this.onBodyClicked.bind(this, param1, param2));

    this.anonymousEventId = addEvent(document.body, 'click', e => {
        console.log(e)
  });
  },

  destroy() {
    this.docBodyClickEventId = removeEvent(document.body, 'click', this.docBodyClickEventId);

    this.anonymousEventId = removeEvent(document.body, 'click', this.anonymousEventId)'
  },

  onBodyClicked(param1, param2) {
    console.log(this.internalAction(param1, param2));
  },

  internalAction(param1, param2) {
    return param1 + param2;
  }
}
```

### removeEvent(element, eventName, eventIndex)

Removed event listener by an eventIndex returned by an addEvent()

### addEventOnce(element, eventName, callback, [delay = 500]) : Number

Call event listener only once after "delay" ms.

Useful for scroll, keydown and other events when the actions must be done only once or when user stopped typing or scrolling, for example.

Also **returns** eventIndex.

### isEventCursorInside(event, element) : Boolean

Checks if event cursor is inside an element.

Below is an example from a good accessible widget. It is not recommended to use only CSS to show/hide dropdowns and similar widgets because user by an error may move cursor outside and element will instantly close. To prevent this give a bit of time for a user. This is also how Bunny Dropdown may work.

```javascript
addEventOnce(element, 'mouseout', e => {
  if (isEventCursorInside(e)) {
    // user returned cursor back to the element, do nothing
  } else {
    // close the menu
  }
})
```

### onClickOutside(element, callback) : Handler

Registers an event listener which will be called when user clicked outside of Node passed in 1st argument.

Useful for cases when you need to close a dropdown on click outside, for example.

**Returns** a handler function which may be used to remove this event later.

### removeClickOutside(element, handler)

Removes onClickOutside handler

### addEventKeyNavigation(element, items, itemSelectCallback, itemSwitchCallback = null) : Handler

Adds up, down, esc, enter keypress event on 'element' to traverse though 'items'.

Have a custom menu which needs accessibility? Add it!

**Returns** a handler which can be used to remove this event later.

### removeEventKeyNavigation(element, handler)

Removes key navigation event.

## Experimental `<template>` helpers

<div class="alert alert-info">Please join this discussion ([WhatWG/HTML proposal #2254](https://github.com/whatwg/html/issues/2254)) to make `<template>` tag even better and more powerful! Standardize the web!</div>

<div class="alert alert-danger">Those helper functions are experimental and may change more often</div>

### parseTemplate(templateId, dataObject) : Node

Parses `<template>` contents and replaces all {{ var }} inside

Second argument should be an object of var keys => var values

If second argument is an Array of Objects, returns DocumentFragment, else - Node.

### registerTemplateHandlers(templateId, handlers)

Registers callbacks to be called when template is parsed and Node generated via `parseTemplate()`.

Should be called before parsing a template.

Second argument is an object of callbacks. This object keys should be equal with the `handler="key"` attribute value. This attribute should be added to any element in template.

Callback receives only one argument - element to which handler is attached.

```html
<template id="item">
  <item>
    <div>{{ id }}</div>
    <div>{{ title }}</div>
    <div>
      <button handler="edit">Edit</button>
    </div>
  </item>
</template>
```

```javascript
registerTemplateHandlers('item', {
  edit(btn) {
    btn.addEventListener('click', () => {
      // ...
    });
  }
})

document.body.appendChild(parseTemplate('item', {
  id: 42,
  title: 'Hello, World!'
}));
```

