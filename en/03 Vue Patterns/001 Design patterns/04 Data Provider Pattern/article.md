Design Pattern

# Data Provider Pattern

In a previous [article](/vue/renderless-components), we’ve come to learn how renderless components help separate the logic of a component from its presentation. This becomes useful when we need to create reusable logic that can be applied to different UI implementations.

Renderless components also allow us to leverage another helpful pattern known as the **data provider pattern**.

## Data Provider Pattern

The data provider pattern is a design pattern that complements the renderless component pattern in Vue by focusing on providing data and state management capabilities to components _without being concerned about how the data is rendered or displayed_.

In the data provider pattern, a data provider component encapsulates the logic for fetching, managing, and exposing data to its child components. The child components can then consume this data and use it in their own rendering or behavior.

![Data provider pattern](https://res.cloudinary.com/ddxwdqwkr/image/upload/v1688485822/patterns.dev/Images/vue/data-provider/data-provider-pattern.png)

This pattern promotes separation of concerns, as the data provider component takes care of data-related tasks, while the child components can focus on presentation and interaction.

Let’s illustrate the data provider pattern with an example. Consider a simple application that displays the setup of a funny joke followed by its punchline. To help us show different jokes randomly, we’ll use the free public API endpoint [https://official-joke-api.appspot.com/random\_joke](https://official-joke-api.appspot.com/random_joke) that returns a random joke in JSON format.

```
# https://official-joke-api.appspot.com/random_joke

{
  "type": "general",
  "setup": "How good are you at Power Point?",
  "punchline": "I Excel at it.",
  "id": 129
}
```

We’ll first create a data provider component called `DataProvider` that will hold the responsibility of fetching the joke from the API. In the `<script>` section of the component, we’ll import the `ref()` and `reactive()` functions from the Vue library, assign the endpoint URL value to a constant, and set up `data` and `loading` reactive properties to capture the data and loading status of our API request.

```
<script setup>
  import { ref, reactive } from "vue";

  const API_ENDPOINT_URL = "https://official-joke-api.appspot.com/random_joke";

  const data = reactive({
    setup: null,
    punchline: null,
  });
  const loading = ref(false);
</script>
```

We’ll then create an asynchronous function called `fetchJoke()` responsible for fetching a joke from the specified API endpoint. The function will:

*   Start by setting the `loading` reactive value to `true`, indicating that the joke is being fetched.
*   Use the native browser [fetch()](https://developer.mozilla.org/en-US/docs/Web/API/fetch) function to send a GET request to the API endpoint.
*   Convert the response from the API to JSON format using the `response.json()` method.
*   Extract the `setup` and `punchline` values from the obtained request data and assign them to the respective properties in the `data` object.
*   Finally, set the `loading` value back to `false`, indicating that the joke has been fetched.

With these changes, our `fetchJoke()` function will look like the following:

```
<script setup>
  import { ref, reactive } from "vue";

  const API_ENDPOINT_URL = "https://official-joke-api.appspot.com/random_joke";

  const data = reactive({
    setup: null,
    punchline: null,
  });
  const loading = ref(false);

  const fetchJoke = async () => {
    loading.value = true;

    const response = await fetch(API_ENDPOINT_URL);
    const responseData = await response.json();

    data.setup = responseData.setup;
    data.punchline = responseData.punchline;
    loading.value = false;
  };

  fetchJoke();
</script>
```

Notice we trigger the `fetchJoke()` function at the end of the `<script>` section? This ensures that the joke is fetched immediately when the `DataProvider` component is rendered.

The last thing left for us to do is to make the `data` and `loading` properties available in the consumer of the `DataProvider` component. To do this, we can pass these properties to a `<slot>` element we’ll place in the `<template>` section.

```
<template>
  <slot :checkbox="checkbox" :toggleCheckbox="toggleCheckbox"></slot>
</template>

<script setup>
  import { ref, reactive } from "vue";

  const API_ENDPOINT_URL = "https://official-joke-api.appspot.com/random_joke";

  const data = reactive({
    setup: null,
    punchline: null,
  });
  const loading = ref(false);

  const fetchJoke = async () => {
    loading.value = true;

    const response = await fetch(API_ENDPOINT_URL);
    const responseData = await response.json();

    data.setup = responseData.setup;
    data.punchline = responseData.punchline;
    loading.value = false;
  };

  fetchJoke();
</script>
```

With our renderless data provider component complete, we can now utilize it in our application. In the parent app component, we’ll import the `DataProvider` component and place it in the template.

```
<template>
  <DataProvider v-slot="{ data, loading }">
    <!-- ... -->
  </DataProvider>
</template>

<script setup>
  import DataProvider from "./components/DataProvider.vue";
</script>
```

By simply rendering the `<DataProvider>` component, we make a request to the endpoint to fetch a joke and can access the `data` and `loading` values of the request with the help of the `v-slot` directive.

Within the `<DataProvider>` component declaration, we can create the UI that would show a loading message if the request is in the loading state or display the joke setup and punchline when the data is available.

```
<template>
  <DataProvider v-slot="{ data, loading }">
    <div class="joke-section">
      <p v-if="loading">Joke is loading...</p>
      <p v-if="!loading">{{ data.setup }}</p>
      <p v-if="!loading">{{ data.punchline }}</p>
    </div>
  </DataProvider>
</template>

<script setup>
  import DataProvider from "./components/DataProvider.vue";
</script>
```

When saving our changes, we’ll be presented with a brief loading message followed by a random joke.

![Data provider example](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1688485819/patterns.dev/Images/vue/data-provider/data-provider-example.gif)

If we need to render another instance of a joke setup and punchline, perhaps even with a different template, we can simply reuse the `<DataProvider>` component and create the new child elements we’d like to show.

```
<template>
  <DataProvider v-slot="{ data, loading }">
    <div class="joke-section">
      <p v-if="loading">Joke is loading...</p>
      <p v-if="!loading">{{ data.setup }}</p>
      <p v-if="!loading">{{ data.punchline }}</p>
    </div>
  </DataProvider>

  <DataProvider v-slot="{ data, loading }">
    <p v-if="loading">Hold on one sec...</p>
    <div v-else class="joke-section">
      <details>
        <summary>{{ data.setup }}</summary>
        <p>{{ data.punchline }}</p>
      </details>
    </div>
  </DataProvider>
</template>

<script setup>
  import DataProvider from "./components/DataProvider.vue";
</script>
```

In our newly rendered UI, we’re now placing the punchline of the joke within a disclosure element with the help of the HTML `<details>` and `<summary>` elements.

![Data provider example](https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900,f_auto/v1688486269/patterns.dev/Images/vue/data-provider/data-provider-example-2.gif)

With the data provider pattern, we’re able to manage and provide data to different elements/components in a decoupled and reusable manner. By abstracting the API fetch logic into a renderless component, we can reuse the request of API data in various contexts without duplicating code.

```javascript
<template>
  <slot :data="data" :loading="loading"></slot>
</template>

<script setup>
import { ref, reactive } from "vue";

const API_ENDPOINT_URL = "https://official-joke-api.appspot.com/random_joke";

const data = reactive({
  setup: null,
  punchline: null,
});
const loading = ref(false);

const fetchJoke = async () => {
  loading.value = true;

  const response = await fetch(API_ENDPOINT_URL);
  const responseData = await response.json();

  data.setup = responseData.setup;
  data.punchline = responseData.punchline;
  loading.value = false;
};

fetchJoke();
</script>
```

## Could we instead use Composables?

Yes! Instead of using the data provider pattern, we could just leverage composables to extract the fetching logic into a reusable function.

```
import { ref, reactive } from "vue";

const API_ENDPOINT_URL = "https://official-joke-api.appspot.com/random_joke";

export function useGetJoke() {
  const data = reactive({
    setup: null,
    punchline: null,
  });
  const loading = ref(false);

  const fetchJoke = async () => {
    loading.value = true;

    const response = await fetch(API_ENDPOINT_URL);
    const responseData = await response.json();

    data.setup = responseData.setup;
    data.punchline = responseData.punchline;
    loading.value = false;
  };

  fetchJoke();

  return { data, loading };
}
```

In our component instances, we can then import and use the composable function to get the `data` and `loading` status of a certain request.

```
<template>
  <div class="joke-section">
    <p v-if="loading">Joke is loading...</p>
    <p v-if="!loading">{{ data.setup }}</p>
    <p v-if="!loading">{{ data.punchline }}</p>
  </div>
</template>

<script setup>
  import { useGetJoke } from "./composables/useGetJoke";

  const { data, loading } = useGetJoke();
</script>
```

Our application will now behave just as it did before with our data provider example.

```javascript
<template>
  <div class="joke-section">
    <p v-if="loading">Joke is loading...</p>
    <p v-if="!loading">{{ data.setup }}</p>
    <p v-if="!loading">{{ data.punchline }}</p>
  </div>
</template>

<script setup>
import { useGetJoke } from "./composables/useGetJoke";

const { data, loading } = useGetJoke();
</script>
```

The data provider pattern helps separate the logic of a component from its presentation by having the parent component take care of rendering the appropriate UI based on the exposed data and behavior of the renderless component. However, with the ability to create reusable composable functions in Vue 3, composables can just as well be used for the majority of cases where the data provider pattern could be used.

When considering between employing the data provider pattern or instead using composable functions, we recommend using composable functions whenever possible since it avoids the need to render a component instance every time data fetching needs to be done (which can cause a [performance overhead](https://vuejs.org/guide/reusability/composables.html#vs-renderless-components)).

Additionally, if you’re using a state management tool like [Pinia](https://pinia.vuejs.org/) to manage how data is provided to components, you would most likely have your API requests be made in the [actions()](https://pinia.vuejs.org/core-concepts/actions.html#actions) of your store. With this state management pattern already in place, the need to use the data provider component pattern becomes less important.

## Helpful Resources

*   [Renderless Components | Vue Documentation](https://vuejs.org/guide/components/slots.html#scoped-slots)