#### Design Pattern

# Static Import

The **`import`** keyword allows us to import code that has been exported by another module. By default, all modules we’re *statically importing* get added to the initial bundle. A module that is imported by using the default ES2015 import syntax, **`import module from 'module'`**, is statically imported.

[![Image alt text](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244287/patterns.dev/saticimport1_cgh9h6.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244287/patterns.dev/saticimport1_cgh9h6.mp4)

Let’s look at an example! A simple chat app contains a **`Chat`** component, in which we’re statically importing and rendering three components: **`UserProfile`**, a **`ChatList`**, and a **`ChatInput`** to type and send messages! Within the **`ChatInput`** module, we’re statically importing an **`EmojiPicker`** component to show be able to show the user the emoji picker when the user toggles the emoji.

```javascript
import React from "react";

// Statically import Chatlist, ChatInput and UserInfo
import UserInfo from "./components/UserInfo";
import ChatList from "./components/ChatList";
import ChatInput from "./components/ChatInput";

import "./styles.css";

console.log("App loading", Date.now());

const App = () => (
  <div className="App">
    <UserInfo />
    <ChatList />
    <ChatInput />
  </div>
);
```

The modules get executed as soon as the engine reaches the line on which we import them. When you open the console, you can see the order in which the modules have been loaded!

```javascript
import React from "react";

// Statically import Chatlist, ChatInput and UserInfo
import UserInfo from "./components/UserInfo";
import ChatList from "./components/ChatList";
import ChatInput from "./components/ChatInput";

import "./styles.css";

console.log("App loading", Date.now());

const App = () => (
  <div className="App">
    <UserInfo />
    <ChatList />
    <ChatInput />
  </div>
);

export default App;
```

Since the components were statically imported, Webpack bundled the modules into the initial bundle. We can see the bundle that Webpack creates after building the application:

```javascript
Asset           Size      Chunks            Chunk Names
main.bundle.js  1.5 MiB    main  [emitted]  main
```

Our chat application’s source code gets bundled into one bundle: **`main.bundle.js`**. A large bundle size can affect the loading time of our application significantly depending on the user’s device and network connection. Before the **`App`** component is able to render its contents to the user’s screen, it first has to load and parse all modules.

Luckily, there are many ways to speed up the loading time! We don’t always have to import all modules at once: maybe there are some modules that should only get rendered based on user interaction, like the **`EmojiPicker`** in this case, or rendered further down the page. Instead of importing all component statically, we can *dynamically* import the modules after the **`App`** component has rendered its contents and the user is able to interact with our application.