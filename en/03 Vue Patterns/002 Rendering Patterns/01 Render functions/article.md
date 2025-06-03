Rendering Pattern

# Render functions

Vue recommends for us to use templates (i.e. the `<template></template>` syntax) to construct the markup of our Vue components. However, we’re also given the opportunity to directly use something known as **render functions** to build the markup of our components as well.

Vue, at build time, takes the templates we create for our components and compiles them to render functions. It’s at these compiled render functions, where Vue builds a virtual representation of nodes that make up the virtual DOM.

> If you’re interested, the [Rendering Mechanism](https://vuejs.org/guide/extras/rendering-mechanism.html) section of the Vue documentation goes into more detail on the concept of the virtual DOM and Vue’s internal rendering mechanism.

By using render functions, we skip the compile step that Vue takes to compile our templates, and are able to construct our component templates with the help of programmatic JavaScript.

## But why?

Render functions come into play when we require a higher level of customization and flexibility that’s not easily achievable with the standard template syntax. This can sound a bit counterintuitive at first, especially given Vue’s emphasis on the simplicity and readability of its template system. In a nutshell, you may prefer to use render functions:

*   When you need to dynamically render components or elements based on complex logic that can be cumbersome to express within a template.
*   You want to have a direct hand on the Virtual DOM for advanced manipulations.
*   You want to use JSX for building the template of your components.

Outside of these unique cases, Vue’s template syntax should remain the go-to method for constructing component markup. However, for unique situations, it can be important to grasp how render functions operate. So, in this article, we’ll delve into render functions and explore how to use them for building a basic component.

## Render functions

Assume we had the following component that contains a `<div>` element encompassing a `<header>` element. The text content of the `<header>` element simply displays the value of a `message` prop.

```
<template>
  <div class="render-card">
    <header class="card-header card-header-title">{{ message }}</header>
  </div>
</template>

<script setup>
  const { message } = defineProps(["message"]);
</script>
```

We’ll recreate the markup of the component step by step with the help of the render function — i.e. the `h()` function.

```
<script setup>
  import { h } from "vue";

  const { message } = defineProps(["message"]);
</script>
```

`h` is short for **hyperscript** which is a term often used in virtual DOM implementations to denote JavaScript syntax that produces HTML. In simple terms, the `h()` function is the render function that allows us to create the “virtual” representation of the DOM nodes that Vue uses to track and subsequently render on the page.

The `h()` function takes three arguments of its own:

1.  An HTML tag name or a component definition.
2.  The props/attributes to be passed onto the element (event listeners, class attributes, etc.).
3.  Child nodes of the parent node.

The HTML tag name for the parent node we want to construct is a `<div>` element. We’ll assign the result of the `h()` function to a constant labeled `render` and pass in a string of value `'div'` as the first argument:

```
<script setup>
  import { h } from "vue";

  const { message } = defineProps(["message"]);

  const render = () => {
    return h("div");
  };
</script>
```

We’ll be interested in applying a `.render-card` CSS class to the parent `<div>` element. To do this, we’ll declare the data object in the second argument of the `h()` function to have a `class` property that has a string value of ‘render-card’:

```
<script setup>
  import { h } from "vue";

  const { message } = defineProps(["message"]);

  const render = () => {
    return h("div", {
      class: "render-card",
    });
  };
</script>
```

> Though we won’t be doing much more for this example, there are numerous different ways of defining attributes with the second argument data object. If you’re interested, be sure to check out the [Vue documentation](https://vuejs.org/guide/extras/render-function.html#creating-vnodes) for a good summary.

We’ll want the parent `<div>` element to have a child `<header>` element of its own. In the third argument of the `h()` function, we’re able to either specify a simple string to render text or an array to render more virtual nodes (i.e. more elements).

Since we’ll be rendering another generated element as the child, we’ll declare the `h()` function within the child nodes array and give it a string value of ‘header’:

```
<script setup>
  import { h } from "vue";

  const { message } = defineProps(["message"]);

  const render = () => {
    return h(
      "div",
      {
        class: "render-card",
      },
      [h("header")]
    );
  };
</script>
```

The header child element is to have classes of its own so we’ll pass in an attributes object in the nested `h()` function to declare the classes the header element should have:

```
<script setup>
  import { h } from "vue";

  const { message } = defineProps(["message"]);

  const render = () => {
    return h(
      "div",
      {
        class: "render-card",
      },
      [
        h("header", {
          class: "card-header card-header-title",
        }),
      ]
    );
  };
</script>
```

The child header element is to contain no child elements of its own and instead is to simply display the value of the `message` prop. To have the header element display the `message` prop as its child content we’ll declare the value of `message` in the third argument of the nested `h()` function.

```
<script setup>
  import { h } from "vue";

  const { message } = defineProps(["message"]);

  const render = () => {
    return h(
      "div",
      {
        class: "render-card",
      },
      [
        h(
          "header",
          {
            class: "card-header card-header-title",
          },
          message
        ),
      ]
    );
  };
</script>
```

And that’s it! The last thing left for us to do is to place the `render` virtual node element we’ve created in the template section of the component.

```
<template>
  <render />
</template>

<script setup>
  import { h } from "vue";

  /* eslint-disable-next-line no-undef, no-unused-vars */
  const { message } = defineProps(["message"]);

  /* eslint-disable-next-line no-unused-vars */
  const render = () => {
    return h(
      "div",
      {
        class: "render-card",
      },
      [
        h(
          "header",
          {
            class: "card-header card-header-title",
          },
          message
        ),
      ]
    );
  };
</script>
```

We can now go ahead render the above component in the parent `App.vue` instance and pass a value of `"Hello World!"` to the `message` prop.

```
<template>
  <RenderComponent message="Hello world!" />
</template>

<script setup>
  import RenderComponent from "./components/RenderComponent.vue";
</script>
```

When saving these changes, we’ll be presented with the \`“Hello World!” message in the UI which tells us we’ve appropriately rendered the child component.

![Badge component](https://res.cloudinary.com/ddxwdqwkr/image/upload/v1687828417/patterns.dev/Images/vue/render-functions/render-function.png)

Whew. If you’re feeling confused here, no need to worry. Though render functions give us more power in how we’d want to tailor the markup of our components, using standard templates is usually a _lot easier_ the vast majority of the time. Only in unique cases where complex dynamic rendering or customization is required, would one opt for render functions.

```javascript
<template>
  <render />
</template>

<script setup>
import { h } from "vue";

/* eslint-disable-next-line no-undef, no-unused-vars */
const { message } = defineProps(["message"]);

/* eslint-disable-next-line no-unused-vars */
const render = () => {
  return h(
    "div",
    {
      class: "render-card",
    },
    [
      h(
        "header",
        {
          class: "card-header card-header-title",
        },
        message
      ),
    ]
  );
};
</script>
```

## Render functions and JSX

A large reason why the implementation we’ve done above might be seen as somewhat painful was due to us writing the render function with raw native JavaScript. To help make writing render functions a lot easier, Vue gives us the ability to write render functions with JSX with the help of an appropriate [Babel plugin](https://github.com/vuejs/babel-plugin-jsx)!

> If you come from a React background, JSX might already be a familiar topic. Simply put, JavaScript XML (or more commonly known as JSX) is an extension that allows us to write JavaScript that looks like HTML (i.e. write XML-like syntax in JavaScript).

JSX can help recreate our render implementation in a way that is a lot easier to read since we can safely write HTML in the render function:

```
<template>
  <render />
</template>

<script setup lang="jsx">
  const { message } = defineProps(["message"]);

  const render = (
    <div class="render-card">
      <header class="card-header card-header-title">{message}</header>
    </div>
  );
</script>
```

With JSX, our render function doesn’t look too difficult! It’s important to keep in mind that JSX is a development tool that always needs to be transpiled with the help of a Babel package (like [babel-plugin-jsx](https://github.com/vuejs/babel-plugin-jsx)) to standard JavaScript. [create-vue](https://github.com/vuejs/create-vue) and [Vue CLI](https://cli.vuejs.org/) both have options for scaffolding projects with pre-configured JSX support.

```javascript
<template>
  <render />
</template>

<script setup lang="jsx">
const { message } = defineProps(["message"]);

const render = <div class="render-card"><header class="card-header card-header-title">{message}</header></div>
</script>
```

## Functional components

Functional components, a type of render function, provide a way to define components **using plain functions**. Functional components are a distinct type of component that lacks an internal state. They resemble pure functions, accepting props as input and producing virtual nodes as output.

To construct a functional component, we employ a simple function instead of an options object. This function essentially serves as the render function responsible for generating the component’s output.

```
function RenderComponent(props, { slots, emit, attrs }) {
  // ...
}

export default RenderComponent;
```

We can use the `h()` function to create the template of our component as we’ve seen earlier.

```
import { h } from "vue";

function RenderComponent(props) {
  return h(
    "div",
    {
      class: "render-card",
    },
    [
      h(
        "header",
        {
          class: "card-header card-header-title",
        },
        props.message
      ),
    ]
  );
}

export default RenderComponent;
```

Additionally, we can also use JSX to render the template of the component in an easier-to-read manner.

```
function RenderComponent(props) {
  return (
    <div class="render-card">
      <header class="card-header card-header-title">{props.message}</header>
    </div>
  );
}

export default RenderComponent;
```

With this functional component setting, our component will render the same “Hello World!” in the UI.

```javascript
function RenderComponent(props) {
    return (
      <div class="render-card">
        <header class="card-header card-header-title">{props.message}</header>
      </div>
    );
  }
  
  export default RenderComponent;
```

## Wrap Up

Render functions provide a powerful way to programmatically build the markup of Vue components using JavaScript. They allow us to create virtual representations of the DOM nodes that Vue uses to track and render on the page.

While render functions offer flexibility and customization, they can be more complex compared to using standard templates. If you feel like you haven’t fully grasped the information in this article - **that is totally okay**. Vue recommends we use standard templates whenever we can since render functions are harder to grasp and implement in an application. However, render functions can be useful for unique scenarios where more power and flexibility are needed in customizing the markup of components.

## Helpful resources

*   [Render Functions & JSX | Vue Documentation](https://vuejs.org/guide/extras/render-function.html)
*   [Rendering Mechanism | Vue Documentation](https://vuejs.org/guide/extras/rendering-mechanism.html)