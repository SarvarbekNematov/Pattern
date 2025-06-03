Design Pattern

# Provide/Inject

When managing data between parent and child components, Vue gives us the ability to use something known as **props** to pass data down from parent to child. Props can only flow in one direction, from parent components to child components (and further down). When state changes occur on parent elements, Vue will re-render components that depend on those values.

![Props](https://res.cloudinary.com/ddxwdqwkr/image/upload/v1687378352/patterns.dev/Images/vue/vue_provide_inject/props.png)

Using props works well in most cases. However, when working in large applications with a large number of components in the component tree, props can become hard to maintain since props need to be declared in _each and every component_ in the component tree.

![Props](https://res.cloudinary.com/ddxwdqwkr/image/upload/v1687392671/patterns.dev/Images/vue/vue_provide_inject/nested_props.png)

When considering how data can be managed between a large number of components, it’s often best to work towards a solution that allows the management of application-level state in a maintainable and manageable manner (e.g. creating a reusable store, using Pinia, etc.). We talk about this in more detail in the [State Management](/vue/state-management) guide.

However, Vue also provides a certain pattern to help avoid the need for complex prop drilling in a Vue application known as the provide/inject pattern.

## Provide/Inject

The `provide()` function in Vue allows us to pass data through a component tree without the need to _prop-drill_ (i.e., pass props down manually at every level). On the other hand, the `inject()` option is used in child components to access the provided data or methods from their parent or any ancestor component.

![Props](https://res.cloudinary.com/ddxwdqwkr/image/upload/v1687392149/patterns.dev/Images/vue/vue_provide_inject/provide-inject.png)

We’ll go through a simple example to illustrate how this can be done. Suppose we have a parent component called `App` that wants to share a piece of data with its child component, `ChildComponent`. Instead of passing this data as a prop, we can use `provide()` in the parent component to make the data available to all its child components.

```
<template>
  <div id="app">
    <ChildComponent />
  </div>
</template>

<script setup>
  import { provide } from "vue";
  import ChildComponent from "./components/ChildComponent";

  provide("data", "Data from parent!");
</script>
```

We can then access this provided data in the `ChildComponent` with the help of the `inject()` function.

```
<template>
  <div>
    <p>{{ data }}</p>
  </div>
</template>

<script setup>
  import { inject } from "vue";

  const data = inject("data");
</script>
```

By specifying `inject("data")` in the child component (`ChildComponent`), we directly access the provided `data` value from the parent component. We then bind `data` to the template to display its value.

```javascript
<template>
  <div id="app">
    <ChildComponent />
  </div>
</template>

<script setup>
import { provide } from "vue";
import ChildComponent from "./components/ChildComponent";

provide("data", "Data from parent!");
</script>
```

With provide/inject, we would notice the same behavior as we’ve seen above even if we had numerous child components within the component hierarchy tree. As an example, assume we had `<ChildComponent />`, `<ChildComponent2 />`, `<ChildComponent3 />`, `<ChildComponent4 />` and `<ChildComponent5 />` components where each child component is a parent to the other.

```
<!-- ChildComponent5 -->
<template>
  <div>
    <p>{{ data }}</p>
  </div>
</template>

<script setup>
  import { inject } from "vue";
  const data = inject("data");
</script>
<!--  ------------- -->

<!-- ChildComponent4 -->
<template>
  <ChildComponent5 />
</template>

<script setup>
  import ChildComponent5 from "./ChildComponent5";
</script>
<!--  ------------- -->

<!-- ChildComponent3 -->
<template>
  <ChildComponent4 />
</template>

<script setup>
  import ChildComponent4 from "./ChildComponent4";
</script>
<!--  ------------- -->

<!-- ChildComponent2 -->
<template>
  <ChildComponent3 />
</template>

<script setup>
  import ChildComponent3 from "./ChildComponent3";
</script>
<!--  ------------- -->

<!-- ChildComponent -->
<template>
  <ChildComponent2 />
</template>

<script setup>
  import ChildComponent2 from "./ChildComponent2";
</script>
<!--  ------------- -->

<!-- App -->
<template>
  <div id="app">
    <ChildComponent />
  </div>
</template>

<script setup>
  import { provide } from "vue";
  import ChildComponent from "./components/ChildComponent";

  provide("data", "Data from parent!");
</script>
<!--  ------------- -->
```

Data from the parent `<App />` component will be rendered in the `<ChildComponent5 />` component without the need to prop-drill data through every component in the tree, thanks to provide/inject!

```javascript
<template>
  <div id="app">
    <ChildComponent />
  </div>
</template>

<script setup>
import { provide } from "vue";
import ChildComponent from "./components/ChildComponent";

provide("data", "Data from parent!");
</script>
```

In addition to being able to `provide()` data from a parent component, we can lift the `provide()` up to the app level as well (i.e. where we instantiate our Vue application).

```
import { createApp } from "vue";
import App from "./App.vue";
import "./styles.css";

const app = createApp(App);

// app-level provide
app.provide("data", "Data from parent!");

app.mount("#app");
```

Since app-level provides make data available to _all_ components, they are often helpful when creating [plugins](https://vuejs.org/guide/reusability/plugins.html) — self-contained code that adds functionality to the entire Vue app.

## Props vs. provide/inject

When do we choose between props and the provide/inject pattern? Both approaches have their advantages and disadvantages.

### With props:

*   We follow a clear pattern of passing data incrementally from one level to another (advantage).
*   However, if our component hierarchy tree contains a large number of components, the process of passing props data one level at a time can become cumbersome (disadvantage).

### With provide/inject

*   Child components can directly access data from parent components located multiple levels above, eliminating the need for passing down data at each level (advantage).
*   However, when bugs arise, debugging can be more challenging with provide/inject. This challenge becomes more pronounced in large-scale applications with numerous different providers (disadvantage).

The provide/inject pattern is most suitable for application-wide client data, such as theme information, locale/language preferences, and user authentication details. These types of data are better managed with provide/inject since any component within the application may require access to them at any given time.

On the other hand, props are ideal when data needs to be isolated within a specific set of components only.

## Helpful resources

*   [Provide / Inject | Vue Documentation](https://vuejs.org/guide/components/provide-inject.html)