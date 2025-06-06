Performance Pattern

# Prefetch

Prefetch (`<link rel="prefetch">`) is a browser optimization which allows us to fetch resources that may be needed for subsequent routes or pages before they are needed. Prefetching can be achieved in a few ways. It can be done declaratively in HTML (such as in the example below), via a HTTP Header (`Link: </js/chat-widget.js>; rel=prefetch`), [Service Workers](https://googlechrome.github.io/samples/service-worker/prefetch/) or via more custom means such as through Webpack.

```
<link rel="prefetch" href="/pages/next-page.html" />
<link rel="prefetch" href="/js/emoji-picker.js" />
```

## Prefetch

In the examples showing how we can import modules based on visibility or interaction, we saw that there was often some delay between clicking on the button in order to toggle the component, and showing the actual component on the screen. This happened, since the module still had to get requested and loaded when the user clicked on the button!

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056520/patterns.dev/prefetch.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056520/patterns.dev/prefetch.mp4)

In many cases, we know that users will request certain resources soon after the initial render of a page. Although they may not visible instantly, thus shouldn’t be included in the initial bundle, it would be great to reduce the loading time as much as possible to give a better user experience!

Components or resources that we know are likely to be used at some point in the application can be **prefetched**. We can let Webpack know that certain bundles need to be prefetched, by adding a [magic comment](https://webpack.js.org/api/module-methods/#magic-comments) to the import statement: `/* webpackPrefetch: true */`.

```
const EmojiPicker = import(/* webpackPrefetch: true */ "./EmojiPicker");
```

```javascript
import React, { Suspense, lazy } from "react";
import Send from "./icons/Send";
import Emoji from "./icons/Emoji";

const EmojiPicker = lazy(() =>
  import(/*webpackPrefetch: true,
    webpackChunkName: "emoji-picker"*/
  "./EmojiPicker")
);
const ChatInput = ({ emojiPicker, gifPicker }) => {
  const [pickerOpen, togglePicker] = React.useReducer(state => !state, false);

  return (
    <div className="chat-input-container">
      <input type="text" placeholder="Type a message..." />
      <Emoji onClick={togglePicker} />
      {pickerOpen && (
        <Suspense fallback={<p id="loading">loading</p>}>
          <EmojiPicker />
        </Suspense>
      )}
      <Send />
    </div>
  );
};

console.log("ChatInput loading", Date.now());

export default ChatInput;
```

After building the application, we can see that the `EmojiPicker` will be prefetched.

```
 Asset                             Size       Chunks                          Chunk Names
    emoji-picker.bundle.js         1.49 KiB   emoji-picker [emitted]          emoji-picker
    vendors~emoji-picker.bundle.js 171 KiB    vendors~emoji-picker [emitted]  vendors~emoji-picker
    main.bundle.js                 1.34 MiB   main  [emitted]                 main

Entrypoint main = main.bundle.js
(prefetch: vendors~emoji-picker.bundle.js emoji-picker.bundle.js)
```

The actual output is visible as a `link` tag with `rel="prefetch"` in the `head` of our document.

```
<link rel="prefetch" href="emoji-picker.bundle.js" as="script" />
<link rel="prefetch" href="vendors~emoji-picker.bundle.js" as="script" />
```

Modules that are prefetched are requested and loaded by the browser even **before the user requested the resource**. When the browser is idle and calculates that it’s got enough bandwidth, it will make a request in order to load the resource, and cache it. Having the resource cached can reduce the loading time significantly, as we don’t have to wait for the request to finish after the user has clicked the button. It can simply get the loaded resource from cache.

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056521/patterns.dev/prefetch-3.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056521/patterns.dev/prefetch-3.mp4)

Although prefetching is a great way to optimize the loading time, don’t overdo it. If the user ended up never requesting the `EmojiPicker` component, we unnecessarily loaded the resource. This could potentially cost a user money, or slow down the application. Only prefetch the necessary resources.

You may find the below resources on prefetching helpful:

*   [Preload, prefetch and priorities in Chrome](https://medium.com/reloading/preload-prefetch-and-priorities-in-chrome-776165961bbf)
*   [Faster navigations with predictive prefetching](https://web.dev/predictive-prefetching/)
*   [Prefetching heuristics](https://blog.mgechev.com/2021/02/07/prefetching-strategies-heuristics-faster-web-apps/)
*   [What not to prefetch](https://addyosmani.com/blog/what-not-to-prefetch-prerender/)