# unity-component-specification
This is a WIP draft of the Unity (Single File Web Component) Specification

# Abstract
Unity Component Specification describes a new hypertext-like file format for creating Single-File Web Components (SFWC).

# Use Cases
Currently there are multiple JavaScript web frameworks that use an interoperable Single-File Web Component format. Not only are they a unifying formate between frameworks, but should aim to be a native platform implementation.

# Unity Component Module
**hello-world.ucm**
```html
<template>
    <h1>Hello {{world}}!</h1>
</template>

<style>
    h1 {
        background: "red";
    };
</style>

<script>
  export default {
    data() {
      return {
        world: "world"
      }
    }
  }
</script>
```

## Interface
This file format will support a strict subset of hypertext markup tags that will delimit and distinguish the use of JavaScript, HTML, and CSS in the single file. 

### `<template></template>` tags

#### Syntax
* Any uses of valid HTML, will be valid inside of the `<template></template>` tags.
* Always single element root? (like vdom'y stuff or not needed for native implementation)

#### Data Binding
* `{{ }}` will be the syntax. (should we even try to allow this to be overriden?)
* Any valid expression within braces
* `v-bind=` mentioned here?

#### Directives 
* Explain what the hell a directive does. Could this specification allow for the creation of JS directives, but all built ins are native for performance? 
* List of Built in Directives? 

### `<style></style>` tags

#### Syntax
* Any uses of valid CSS is permitted inside of the `<style></style>` tags.

#### Scoped? 😬

### `<script></script>` tags

#### Syntax
* Any uses of valid JS is permitted inside of the `<script></script>` tags.
* Must `export default` a object of properties describing available data bindings, props, computed properties, other registered UCM's, declarations and all other Unity Component Module options. 

#### UCM Options Object
* // TODO: Add all the Vue Component options here. 



