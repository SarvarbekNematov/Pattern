#### Rendering Pattern

# Animating View Transitions

**Note:** The View Transitions API for Single-Page Applications is available in Chrome 111+

### Introduction to View Transitions

The [View Transitions API](https://developer.chrome.com/docs/web-platform/view-transitions/) offers a simple way to transition any visual DOM change from one state to the next. This might include small changes such as toggling some content, or broader changes such as navigating from one page to the next. Below is a [demo](https://astro-movies.pages.dev/) of the View Transitions API in an SPA (Single-Page Application) [src](https://github.com/Charca/astro-movies):

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/v1678492898/patterns.dev/astro-movies-2023.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/v1678492898/patterns.dev/astro-movies-2023.mp4)

The JavaScript API centers around **`document.startViewTransition(callback)`**, where **`callback`** is a function that typically updates the DOM to the new state.

Let’s take toggling a **`<details>`** element as a simple example:

```javascript
if (document.startViewTransition) {
  // (check for browser support)
  document.addEventListener("click", function (event) {
    if (event.target.matches("summary")) {
      event.preventDefault(); // (we'll toggle the element ourselves)
      const details = event.target.closest("details");
      document.startViewTransition(() => details.toggleAttribute("open"));
    }
  });
}
```

**`document.startViewTransition`** takes a screenshot of the current DOM before calling the callback. Here, our callback just toggles the **`open`** attribute. Once complete, the browser can then transition between the initial screenshot and the new version.

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/v1678488008/patterns.dev/toggle-demo.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/v1678488008/patterns.dev/toggle-demo.mp4)

<div class="tab-container">
  <div class="tabs">
    <button onclick="showTab('html')" class="active">HTML</button>
    <button onclick="showTab('js')">JS</button>
    <button onclick="showTab('result')">Result</button>
  </div>

  <div id="html" class="tab-content active">
&lt;details&gt;
  &lt;summary&gt;Toggle&lt;/summary&gt;
  Lorem ipsum dolor sit amet consectetur adipisicing elit...
&lt;/details&gt;
  </div>

  <div id="js" class="tab-content">
    ```javascript
    if (document.startViewTransition) { // (check for browser support)
  document.addEventListener('click', function (event) {
    if (event.target.matches('summary')) {
      event.preventDefault() // (we’ll toggle the element ourselves)
      const details = event.target.closest('details')
      document.startViewTransition(() => details.toggleAttribute('open'))
    }
  })
}
    ```
  </div>

  <div id="result" class="tab-content">
    <details>
      <summary>Toggle</summary>
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Ipsam
      incidunt itaque accusantium, quis molestiae quia provident quas,
      rem consequuntur, fugiat placeat aperiam quo non! Aspernatur
      error et asperiores velit harum?
    </details>
  </div>
</div>

<script>
  function showTab(tabId) {
    const buttons = document.querySelectorAll('.tabs button');
    const contents = document.querySelectorAll('.tab-content');

    buttons.forEach(btn => btn.classList.remove('active'));
    contents.forEach(div => div.classList.remove('active'));

    document.querySelector(`#${tabId}`).classList.add('active');
    event.target.classList.add('active');
  }
</script>

<style>
  .tab-container {
    font-family: sans-serif;
    width: 100%;
  }
  .tabs {
    display: flex;
    margin-bottom: 8px;
  }
  .tabs button {
    flex: 1;
    padding: 8px;
    background: #333;
    color: #ccc;
    border: none;
    cursor: pointer;
  }
  .tabs button.active {
    background: #555;
    color: white;
  }
  .tab-content {
    background: #1e1e1e;
    color: white;
    padding: 12px;
    white-space: pre-wrap;
    font-family: monospace;
    display: none;
  }
  .tab-content.active {
    display: block;
  }
</style>
