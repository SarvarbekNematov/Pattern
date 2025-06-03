Performance Pattern

# Dynamic Import

In our chat application, we have four key components: `UserInfo`, `ChatList`, `ChatInput` and `EmojiPicker`. However, only _three_ of these components are used instantly on the initial page load: `UserInfo`, `ChatList` and `ChatInput`. The `EmojiPicker` isn’t directly visible, and may not even be rendered at all if the user won’t even click on the `Emoji` in order to toggle the `EmojiPicker`. This would mean that we unnecessarily added the `EmojiPicker` module to our initial bundle, which potentially increased the loading time!

In order to solve this, we can _dynamically import_ the `EmojiPicker` component. Instead of statically importing it, we’ll only import it when we want to show the `EmojiPicker`. An easy way to dynamically import components in React is by using [**React Suspense**](https://reactjs.org/docs/concurrent-mode-suspense.html). The `React.Suspense` component receives the component that should be dynamically loaded, which makes it possible for the `App` component can render its contents faster by suspending the import of the `EmojiPicker` module! When the user clicks on the emoji, the `EmojiPicker` component gets rendered for the first time. The `EmojiPicker` component renders a `Suspense` component, which receives the lazily imported module: the `EmojiPicker` in this case. The `Suspense` component accepts a `fallback` prop, which receives the component that should get rendered while the suspended component is still loading!

Instead of unnecessarily adding `EmojiPicker` to the initial bundle, we can split it up into its own bundle and reduce the size of the initial bundle!

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244202/patterns.dev/heheh_shqtb6.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244202/patterns.dev/heheh_shqtb6.mp4)

A smaller initial bundle size means a faster initial load: the user doesn’t have to stare at a blank loading screen for as long. The `fallback` component lets the user know that our application hasn’t frozen: they simply need to wait a little while for the module to be processed and executed.

```
Asset                             Size         Chunks            Chunk Names
emoji-picker.bundle.js           1.48 KiB      1    [emitted]    emoji-picker
main.bundle.js                   1.33 MiB      main [emitted]    main
vendors~emoji-picker.bundle.js   171 KiB       2    [emitted]    vendors~emoji-picker
```

Whereas previously the initial bundle was `1.5MiB`, we’ve been able to reduce it to `1.33 MiB` by suspending the import of the `EmojiPicker`!

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056514/patterns.dev/bundle-splitting-2.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056514/patterns.dev/bundle-splitting-2.mp4)

In the console, you can see that the `EmojiPicker` doesn’t get executed until we’ve toggled the `EmojiPicker`!

```javascript
import React, { Suspense, lazy } from "react";
  // import Send from "./icons/Send";
  // import Emoji from "./icons/Emoji";
  const Send = lazy(() =>
    import(/*webpackChunkName: "send-icon" */ "./icons/Send")
  );
  const Emoji = lazy(() =>
    import(/*webpackChunkName: "emoji-icon" */ "./icons/Emoji")
  );
  // Lazy load EmojiPicker  when <EmojiPicker /> renders
  const Picker = lazy(() =>
    import(/*webpackChunkName: "emoji-picker" */ "./EmojiPicker")
  );

  const ChatInput = () => {
    const [pickerOpen, togglePicker] = React.useReducer(state => !state, false);

    return (
      <Suspense fallback={<p id="loading">Loading...</p>}>
        <div className="chat-input-container">
          <input type="text" placeholder="Type a message..." />
          <Emoji onClick={togglePicker} />
          {pickerOpen && <Picker />}
          <Send />
        </div>
      </Suspense>
    );
  };

  console.log("ChatInput loaded", Date.now());

  export default ChatInput;
```

When building the application, we can see the different bundles that Webpack created.

By dynamically importing the `EmojiPicker` component, we managed to reduce the initial bundle size from `1.5MiB` to `1.33 MiB`! Although the user may still have to wait a while until the `EmojiPicker` has been fully loaded, we have improved the user experience by making sure the application is rendered and interactive while the user waits for the component to load.

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056515/patterns.dev/dynamic-import.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056515/patterns.dev/dynamic-import.mp4)

- - -

## Loadable Components

Server-side rendering doesn’t support React Suspense (yet). A good alternative to React Suspense is the [`loadable-components`](https://loadable-components.com/docs/getting-started/) library, which can be used in SSR applications.

```javascript
import React from "react";
import loadable from "@loadable/component";

import Send from "./icons/Send";
import Emoji from "./icons/Emoji";

const EmojiPicker = loadable(() => import("./EmojiPicker"), {
  fallback: <div id="loading">Loading...</div>
});

const ChatInput = () => {
  const [pickerOpen, togglePicker] = React.useReducer(state => !state, false);

  return (
    <div className="chat-input-container">
      <input type="text" placeholder="Type a message..." />
      <Emoji onClick={togglePicker} />
      {pickerOpen && <EmojiPicker />}
      <Send />
    </div>
  );
};

export default ChatInput;
```

Similar to React Suspense, we can pass the lazily imported module to the `loadable`, which will only import the module once the `EmojiPicker` module is being requested! While the module is being loaded, we can render a `fallback` component.

Although loadable components are a great alternative to React Suspense for SSR applications, they’re also useful in CSR applications in order to suspend the import of modules.

```javascript
import React from "react";
  import Send from "./icons/Send";
  import Emoji from "./icons/Emoji";
  import loadable from "@loadable/component";

  const EmojiPicker = loadable(() => import("./components/EmojiPicker"), {
    fallback: <p id="loading">Loading...</p>
  });

  const ChatInput = () => {
    const [pickerOpen, togglePicker] = React.useReducer(state => !state, false);

    return (
      <div className="chat-input-container">
        <input type="text" placeholder="Type a message..." />
        <Emoji onClick={togglePicker} />
        {pickerOpen && <EmojiPicker />}
        <Send />
      </div>
    );
  };

  console.log("ChatInput loaded", Date.now());

  export default ChatInput;
```
