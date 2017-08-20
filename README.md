# unity-component-specification
This is a WIP draft of the Unity (Single File Web Component) Specification

# Abstract
Unity Component Specification describes a new hypertext-like file format for creating Single-File Web Components (SFWC).

## Goals
* rosetta stone-like capabilities component interop between frameworks.
  * provides a promise for users, companies, lib authors to have a single "meta" description of how their "ui component" should function, meanwhile using modern tooling, and small compiler chains, could compile this "meta" information into the framework's actual representation. 

* allow users to ship way less JavaScript and CSS to the browser

* 60fps scroll lists, fast dom changes, fast paints. "Does it beat the Ken Wheeler Test" (long term platform goal)

## Scenario:
Giant company (A) is silo'd and has a variety of different frameworks that they use in house, and they all love the workflows that they have, however there is massive amounts of overlap because they cannot write components that both teams can use. 

With "unity components" (Single File Components) this could be possible: Team A using `Preact` _(Figure 1)_, could use this component in the same way that Team B using 'Vue' _(Figure 2)_ does. 

**Figure 1: Using `Button.sfc` in `.js` file and consumed with preact `render()` fn**
```js
import {Button} from "material-unity" // returns a SFC "module" .sfc or .vue, etc whatever

render(
    <Button someProp="whatever" />,
    document.body
);
```

**Figure 2: Using same `Button.sfc` inside of a Vue single file component**
```html
<template>
    <div>
        <button some-prop="whatever"></button>
    </div>
</template>
<script>
import {Button} from "material-unity" // returns a SFC "module" .sfc or .vue, etc whatever
export default {
  components: {
    Button
  }
}
</script>
<style>
  /* ... */
</style>
```

The point is that the framework-specific implementation details of how this "meta information" represented in the Single File Component are hidden through the compiler toolchains.

So although you might see `data(){}` function that returns state for the component, it may instead compile to `state` in react/preact, or `@tracked` in GlimmerJS. 

## How this relates to the platform (the long term idea if frameworks buy in  @first)
Although this statement may be opinionated, it is observed that the number one problem with web components currently is that it is a write only target, not something a framework could consume and then use (like their own systems components) without potentially substantial changes to their respective api's, renders, etc. This poses a huge challenge for adoption. 

In addition, until there is cross-platform adoption for Web Components completely, there is really no interest for a majority of users or framework teams to start integrating with this system.

## Abstraction not only for Frameworks, but also the Platform
The magic behind what makes Vue's single file component structure so flexible and powerful, is that everything inside of their respective tags, is only a "likeness" to some extent.

For example: 
```html
<template>
  <div>I look and smell like HTML, but I'm not HTML. I'm HTML-like</div>
</template>
<style>
.div {
  likeness: to-css;
}
</style>
<script>
export default {
  data() {
    maybe: "Interpreted through the context of the component, but technically this could _not_ really be JavaScript, just a state machine's instructions"
  }
}
</script>
```

In the same way that:
```jsx
render(
   <JSX>I look and smell like HTML, but I'm not html, I'm JSX, I'm html-like</JSX>,
   document.body
)
```

So in the same way that a loader, compiler, etc. statically analyzes a `.vue` file to create a JavaScript implementation of that components Dom API functionality (via VDOM), we can apply the same principle for the platform to accomplish the same thing. 

*In a real index.html file*

```html
<head>
<module type="unity" name="unity-button" src="./unity-components/button.sfc" />
<head>

<body>
  <unity-button some-prop="whatever">Click Me</unity-button>
</body>
```

We have fed a single component to the browser, which consumes the SFC module, and instead of creating render functions, vdom, JSX, javascript, or anything else, it is interpreted and compiled to _native instructions_ for the lifecycle, state, and the visual representation and behavior for that element on the DOM. 

## Side Notes:
Here's some things I want to clarify before they get mentioned

### Template "Directives" 
A huge motivation for the use of these universal directives, is the same reason why they are great in VueJs. Although at any time, in a single-file `.vue` component, a user can drop `<template></template>` and instead opt-in to using `render(<JSX/>, document.body)` instead. 

But in the end (for vue): 
```html
<template>
  <div v-for="item of 1000000items">{{item}}</div>
</template>
```
is just an abstraction that compiles to VDOM/Render functions to perform that ginormous scrolling list. The benefit of the platform consuming this, is that it can read that directive as an instruction to perform the same results, but in a far more, Ken Wheeler-defying-90fps-fashion (because its not DOM, or JS, its native instructions interpreted from the file).

### JSX inside of `<script>`
In the same way that `<template></template>` can be statically analyzed and compiled, you can take this a step further and say the use of JSX inside of `<script></script>` could have a similar outcome. 

### Pros: 
* Native calls are fast, no JavaScript DOM API's are called, the render, layout engines, etc. now can compile and manage these just like another implementation detail as direct "assembly like instructions" for how things must be viewed, laid out, painted et al.
* There is no need to Custom Elements, it is native instructions, there needs to be no extension of existing elements or behaviors. 
* What might compile to a couple hundred lines of code, and be shipped inside of JS, CSS, could now be shipped in a single file, in a non blocking fashion. 
* (Maybe? I'm not an expert in the browser to understand this piece yet) patterns like `<style scoped>` provides a css-like scoping solution that doesn't have to rely on shadow dom, etc.
* It doesn't rely on pieces of the existing web component specification, and browsers, (as long as its implemented), could implement this however they would like for their respective platforms, etc.
* Using in the browser outside of the FW, could be as simple as just dropping the component into a `<tag>` and using. And this could potentially create a Rosetta Stone-like api for interop between a variety of frameworks (if not all). 
* webpack users could still leverage their own styling and template tools/preprocessors of choice, and webpack will (if this spec is pushed, or at the least 3+ frameworks can adopt it), create an output target for `.sfc`. That way loaders can still be used to allow one to customize, extend etc while still compiling to `.sfc` in browserland.

### Cons:
* Silver bullet syndrome: things that become abstracted, can lose flexibility, or have features removed to create interop. This should be carefully balanced and thought out. 

* Not all frameworks can statically compile these formats? (If not what is the MVP amount of changes that we could make to ensure that not only VDOM-frameworks, but also observer, watcher, and bind based libraries can leverage as well. 

* Can dilute the framework capabilities of a specific component type? What may work with a full rich featureset in `.vue`, may need to have limited capabilities in GlimmerJS, or Polymer. (Or am I wrong. I don't know enough about them to make this assumption, but would like feedback on this.)

* Pushback in browserland vs WC v2.: Although this could arise, I'm hopeful that because the `.sfc` format doesn't mandate creation of API's that affect DOM (besides a new module/script type), that this could be easily worked around, and potentially alongside WC v2.

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



