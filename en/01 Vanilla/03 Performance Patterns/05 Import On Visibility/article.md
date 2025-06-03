Performance Pattern

# Import On Visibility

Besides user interaction, we often have components that aren’t visible on the initial page. A good example of this is lazy loading images that aren’t directly visible in the viewport, but only get loaded once the user scrolls down.

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244229/patterns.dev/pat5_r1bjcp.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244229/patterns.dev/pat5_r1bjcp.mp4)

As we’re not requesting all images instantly, we can reduce the initial loading time. We can do the same with components! In order to know whether components are currently in our viewport, we can use the [`IntersectionObserver` API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API), or use libraries such as `react-lazyload` or `react-loadable-visibility` to quickly add import on visibility to our application.

```javascript
import React from "react";
import Send from "./icons/Send";
import Emoji from "./icons/Emoji";
import LoadableVisibility from "react-loadable-visibility/react-loadable";

const EmojiPicker = LoadableVisibility({
  loader: () => import("./EmojiPicker"),
  loading: <p id="loading">Loading</p>
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

console.log("ChatInput loading", Date.now());

export default ChatInput;
```

Whenever the `EmojiPicker` is rendered to the screen, after the user clicks on the Gif button, `react-loadable-visibility` detects that the `EmojiPicker` element should be visible on the screen. Only then, it will start importing the module while the user sees a loading component being rendered.

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056516/patterns.dev/import-on-visibility.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056516/patterns.dev/import-on-visibility.mp4)

This fallback component to let the user know that our application hasn’t frozen: they simply need to wait a short while for the module to be loaded, parsed, compiled, and executed!