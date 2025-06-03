Design Pattern

# Container/Presentational Pattern

In 2015, Dan Abramov wrote an article titled [“Presentational and Container Components”](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0) that changed the way many developers thought about component architecture in React. He introduced a pattern that separated components into two categories:

1.  **Presentational Components (or Dumb Components)**: These are concerned with how things look. They don’t specify how the data is loaded or mutated but rather receive data and callbacks exclusively via props.
2.  **Container Components (or Smart Components)**: These are concerned with how things work. They provide the data and behavior to presentational or other container components.

While this pattern was mainly associated with React, its fundamental principle was adopted and adapted in various forms across other libraries and frameworks.

Dan’s distinction offered a clearer and more scalable way to structure JavaScript applications. By clearly defining the responsibilities of different types of components, developers could ensure better reusability of the UI components (presentational) and logic (containers). The idea was that if we were to change how something looked (like a button’s design), we could do so without touching the logic of the app. Conversely, if we needed to change how data flowed or was processed, the presentational components would remain untouched, ensuring that the UI remained consistent.

However, with the emergence of [hooks](https://react.dev/learn/reusing-logic-with-custom-hooks) in React and the [Composition API](https://vuejs.org/guide/extras/composition-api-faq.html) in Vue 3, the clear boundary between presentational and container components began to blur. Hooks and the Composition API began allowing developers to encapsulate and reuse state and logic without necessarily being confined to a class-based container component or the Options API. As a result, the container/presentational pattern isn’t as rigidly adhered to as it once was. With that being said, we’ll spend some time in this article discussing the pattern since it can still be helpful at certain times.

- - -

Let’s say we want to create an application that fetches 6 dog images, and renders these images on the screen.

![Browse dogs application](https://res.cloudinary.com/ddxwdqwkr/image/upload/v1692062597/patterns.dev/Images/vue/container-presentational/browse-dogs.png)

To follow the container/presentational pattern, we want to enforce the separation of concerns by separating this process into two parts:

1.  **Presentational Components**: Components that care about _**how**_ data is shown to the user. In this example, that’s the rendering of the list of dog images.
2.  **Container Components**: Components that care about _**what**_ data is shown to the user. In this example, that’s fetching the dog images.

- - -

[![Video of the text above Click me](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056518/patterns.dev/jspat-40_af2vga.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056518/patterns.dev/jspat-40_af2vga.mp4)

Fetching the dog images deals with **application logic**, whereas displaying the images only deals with the **view**.

- - -

## Presentational Component

A presentational component receives its data through `props`. Its primary function is to simply **display the data it receives** the way we want them to, including styles, _without modifying_ that data.

Let’s take a look at the example that displays the dog images. When rendering the dog images, we simply want to map over each dog image that was fetched from the API and render those images. To do so, we can create a `DogImages` component that receives the data through props and renders the data it receives.

```
<!-- DogImages.vue -->

<template>
  <img v-for="(dog, index) in dogs" :src="dog" :key="index" alt="Dog" />
</template>

<script setup>
  import { defineProps } from "vue";
  const { dogs } = defineProps(["dogs"]);
</script>
```

The `DogImages` component can be regarded to be a presentational component. Presentational components are usually stateless: they do not contain their own component state, unless they need a state for UI purposes. The data they receive is not altered by the presentational components themselves.

Presentational components receive their data from **container components**.

- - -

## Container Components

The primary function of container components is to **pass data** to presentational components, which they contain. Container components themselves usually don’t render any other components besides the presentational components that care about their data. Since they don’t render anything themselves, they usually do not contain any styling either.

In our example, we want to pass dog images to the `DogsImages` presentational component. Before being able to do so, we need to fetch the images from an external API. We need to create a **container component** that fetches this data, and passes this data to the presentational component `DogImages` to display it on the screen. We’ll call this container component `DogImagesContainer`.

```
<!-- DogImagesContainer.vue -->

<template>
  <DogImages :dogs="dogs" />
</template>

<script setup>
  import { ref, onMounted } from "vue";
  import DogImages from "./DogImages.vue";

  const dogs = ref([]);

  onMounted(async () => {
    const response = await fetch(
      "https://dog.ceo/api/breed/labrador/images/random/6"
    );
    const { message } = await response.json();
    dogs.value = message;
  });
</script>
```

Combining these two components together makes it possible to separate handling application logic from the view.

[![Video of the text above Click me](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056519/patterns.dev/jspat-45_budnfb.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056519/patterns.dev/jspat-45_budnfb.mp4)

This in a nutshell is the container/presentational pattern. When integrating with state management solutions like [Pinia](https://pinia.vuejs.org/), the container components can be leveraged to interact directly with the store, fetching or mutating the state as needed. This allows the presentational components to remain pure and unaware of the broader application logic, focusing only on rendering the UI based on the props they receive.

```javascript
<template>
  <DogImages :dogs="dogs" />
</template>

<script setup>
import { ref, onMounted } from "vue";
/* eslint-disable-next-line no-unused-vars */
import DogImages from "./DogImages.vue";

const dogs = ref([]);

onMounted(async () => {
  const response = await fetch(
    "https://dog.ceo/api/breed/labrador/images/random/6"
  );
  const { message } = await response.json();
  dogs.value = message;
});
</script>
```

- - -

## Composables

> Do read the [Composables](/vue/composables) guide for a deep-dive into understanding composables.

In many cases, the container/presentational pattern can be replaced with composables. The introduction of composables made it easy for developers to add statefulness **without needing a container component to provide that state**.

Instead of having the data fetching logic in the `DogImagesContainer` component, we can create a composable that fetches the images, and returns the array of dogs.

```
import { ref, onMounted } from "vue";

export default function useDogImages() {
  const dogs = ref([]);

  onMounted(async () => {
    const response = await fetch(
      "https://dog.ceo/api/breed/labrador/images/random/6"
    );
    const { message } = await response.json();
    dogs.value = message;
  });

  return { dogs };
}
```

By using this hook, we no longer need the wrapping `DogImagesContainer` container component to fetch the data and send this to the presentational `DogImages` component. Instead, we can use this hook directly in our presentational `DogImages` component!

```
<template>
  <img v-for="(dog, index) in dogs" :src="dog" :key="index" alt="Dog" />
</template>

<script setup>
  import useDogImages from "../composables/useDogImages";

  /* eslint-disable-next-line no-unused-vars */
  const { dogs } = useDogImages();
</script>
```

By using the `useDogImages()` hook, we still separated the application logic from the view. We’re simply using the returned data from the `useDogImages` hook, without modifying that data within the `DogImages` component.

[![Video of the text above Click me](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056518/patterns.dev/jspat-46_evhhpd.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056518/patterns.dev/jspat-46_evhhpd.mp4)

With all the changes we’ve made, our app can be seen as below.

```javascript
import { ref, onMounted } from 'vue';

export default function useDogImages() {
  const dogs = ref([]);

  onMounted(async () => {
    const response = await fetch("https://dog.ceo/api/breed/labrador/images/random/6");
    const { message } = await response.json();
    dogs.value = message;
  });

  return { dogs };
}
```

Composables make it easy to separate logic and view in a component, just like the Container/Presentational pattern. It saves us the extra layer that was necessary to wrap the presentational component within the container component.

## Helpful Resources

*   [Vue Composables | Patterns.dev](/vue/composables)