Rendering Pattern

# Streaming Server-Side Rendering

We can reduce the Time To Interactive while still server rendering our application by _streaming server rendering_ the contents of our application. Instead of generating one large HTML file containing the necessary markup for the current navigation, we can split it up into smaller chunks! Node streams allow us to stream data into the response object, which means that we can continuously send data down to the client. The moment the client receives the chunks of data, it can start rendering the contents.

[![Video of the text above Click me](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056525/patterns.dev/ssr-1.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056525/patterns.dev/ssr-1.mp4)

React’s built-in `renderToNodeStream` makes it possible for us to send our application in smaller chunks. As the client can start painting the UI when it’s still receiving data, we can create a very performant first-load experience. Calling the `hydrate` method on the received DOM nodes will attach the corresponding event handlers, which makes the UI interactive!

[![Video of the text above Click me](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056525/patterns.dev/ssr-2.jpg)](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056525/patterns.dev/ssr-2.mp4)

Let’s say we have an app that shows the user thousands of cat facts in the `App` component!

```javascript
import React from "react";
import path from "path";
import express from "express";
import { renderToNodeStream } from "react-dom/server";

import App from "./src/App";

const app = express();

// app.get("/favicon.ico", (req, res) => res.end());
app.use("/client.js", (req, res) => res.redirect("/build/client.js"));

const DELAY = 500;
app.use((req, res, next) => {
  setTimeout(() => {
    next();
  }, DELAY);
});

const BEFORE = `
<!DOCTYPE html>
  <html>
    <head>
      <title>Cat Facts</title>
      <link rel="stylesheet" href="/style.css">
      <script type="module" defer src="/build/client.js"></script>
    </head>
    <body>
      <h1>Stream Rendered Cat Facts!</h1>
      <div id="approot">
`.replace(/
s*/g, "");

app.get("/", async (request, response) => {
  try {
    const stream = renderToNodeStream(<App />);
    const start = Date.now();

    stream.on("data", function handleData() {
      console.log("Render Start: ", Date.now() - start);
      stream.off("data", handleData);
      response.useChunkedEncodingByDefault = true;
      response.writeHead(200, {
        "content-type": "text/html",
        "content-transfer-encoding": "chunked",
        "x-content-type-options": "nosniff"
      });
      response.write(BEFORE);
      response.flushHeaders();
    });
    await new Promise((resolve, reject) => {
      stream.on("error", err => {
        stream.unpipe(response);
        reject(err);
      });
      stream.on("end", () => {
        console.log("Render End: ", Date.now() - start);
        response.write("</div></body></html>");
        response.end();
        resolve();
      });
      stream.pipe(
        response,
        { end: false }
      );
    });
  } catch (err) {
    response.writeHead(500, {
      "content-type": "text/pain"
    });
    response.end(String((err && err.stack) || err));
    return;
  }
});

app.use(express.static(path.resolve(__dirname, "src")));
app.use("/build", express.static(path.resolve(__dirname, "build")));

const listener = app.listen(process.env.PORT || 2048, () => {
  console.log("Your app is listening on port " + listener.address().port);
});
```

The `App` component gets stream rendered using the built-in `renderToNodeStream` method. The initial HTML gets sent to the response object alongside the chunks of data from the App component,

```javascript
<!DOCTYPE html>
<html>
  <head>
    <title>Cat Facts</title>
    <link rel="stylesheet" href="/style.css" />
    <script type="module" defer src="/build/client.js"></script>
  </head>
  <body>
    <h1>Stream Rendered Cat Facts!</h1>
    <div id="approot"></div>
  </body>
</html>
```

This data contains useful information that our app has to use in order to render the contents correctly, such as the title of the document and a stylesheet. If we were to server render the `App` component using the `renderToString` method, we would have had to wait until the application has received all data before it can start loading and processing this metadata. To speed this up, `renderToNodeStream` makes it possible for the app to start loading and processing this information as it’s still receiving the chunks of data from the App component!

> To see more examples on how to implement Progressive Hydration and Server Rendering, visit [this GitHub repo](https://github.com/GoogleChromeLabs/progressive-rendering-frameworks-samples).

> [See how styled-components use streaming rendering to optimize the delivery of stylesheets](https://medium.com/styled-components/v3-1-0-such-perf-wow-many-streams-c45c434dbd03)

- - -

## Concepts

Like progressive hydration, streaming is another rendering mechanism that can be used to improve SSR performance. As the name suggests, streaming implies chunks of HTML are streamed from the node server to the client as they are generated. As the client starts receiving “bytes” of HTML earlier even for large pages, the TTFB is reduced and relatively constant. All major browsers start parsing and rendering streamed content or the partial response earlier. As the rendering is progressive, it results in a fast FP and FCP.

Streaming responds well to network backpressure. If the network is clogged and not able to transfer any more bytes, the renderer gets a signal and stops streaming till the network is cleared up. Thus, the server uses less memory and is more responsive to I/O conditions. This enables your Node.js server to render multiple requests at the same time and prevents heavier requests from blocking lighter requests for a long time. As a result, the site stays responsive even in challenging conditions.

- - -

## React for Streaming

React introduced support for streaming in React 16 released in 2016. The following API’s were included in the ReactDOMServer to support streaming.

1.  **[`ReactDOMServer.renderToNodeStream(element)`](https://reactjs.org/docs/react-dom-server.html#rendertonodestream)**: The output HTML from this function is the same as [`ReactDOMServer.renderToString(element)`](https://reactjs.org/docs/react-dom-server.html#rendertostring) but is in a Node.js [readablestream](https://nodejs.org/api/stream.html#stream_readable_streams) format instead of a string. The function will only work on the server to render HTML as a stream. The client receiving this stream can subsequently call [ReactDOM.hydrate()](https://reactjs.org/docs/react-dom.html#hydrate) to hydrate the page and make it interactive.
    
2.  **[`ReactDOMServer.renderToStaticNodeStream(element)`](https://reactjs.org/docs/react-dom-server.html#rendertostaticnodestream)**: This corresponds to [`ReactDOMServer.renderToStaticMarkup(element)`](https://reactjs.org/docs/react-dom-server.html#rendertostaticmarkup). The HTML output is the same but in a stream format. It can be used for rendering static, non-interactive pages on the server and then streaming them to the client.
    

The readable stream output by both functions can emit bytes once you start reading from it. This can be achieved by piping the readable stream to a writable stream such as the response object. The response object progressively sends chunks of data to the client while waiting for new chunks to be rendered.

Putting it all together, let us now look at the code skeleton for this as published [here](https://mxstbr.com/thoughts/streaming-ssr/).

```javascript
import { renderToNodeStream } from 'react-dom/server';
import Frontend from '../client';

app.use('*', (request, response) => {
  // Send the start of your HTML to the browser
  response.write('<html><head><title>Page</title></head><body><div id="root">');

  // Render your frontend to a stream and pipe it to the response
  const stream = renderToNodeStream(<Frontend />);
  stream.pipe(response, { end: 'false' });
  // Tell the stream not to automatically end the response when the renderer finishes.

  // When React finishes rendering send the rest of your HTML to the browser
  stream.on('end', () => {
    response.end('</div></body></html>');
  });
});
```

A comparison between TTFB and First Meaningful Paint for normal SSR Vs Streaming is available in the following image.

![](https://res.cloudinary.com/ddxwdqwkr/image/upload/f_auto/v1616883053/patterns.dev/renderingwebap--03wnu5khnrzr.png)

Image Source: [https://mxstbr.com/thoughts/streaming-ssr/](https://mxstbr.com/thoughts/streaming-ssr/)

- - -

## Streaming SSR - Pros and Cons

Streaming aims to improve the speed of SSR with React and provides the following benefits

1.  **Performance Improvement:** As the first byte reaches the client soon after rendering starts on the server, the TTFB is better than that for SSR. it is also more consistent irrespective of the page size. Since the client can start parsing HTML as soon as it receives it, the FP and FCP are also lower.
    
2.  **Handling of Backpressure**: Streaming responds well to network backpressure or congestion and can result in responsive websites even under challenging conditions.
    
3.  **Supports SEO**: The streamed response can be read by search engine crawlers, thus allowing for SEO on the website.
    

It is important to note that streaming implementation is not a simple find-replace from `renderToString` to `renderToNodeStream()`. There are cases where the code that works with SSR may not work as-is with streaming. Following are some examples where migration may not be easy.

1.  Frameworks that use the server-render-pass to generate markup that needs to be added to the document before the SSR-ed chunk. Examples are frameworks that dynamically determine which CSS to add to the page in a preceding `<style>` tag, or frameworks that add elements to the document `<head>` while rendering. A workaround for this has been discussed [here](https://medium.com/styled-components/v3-1-0-such-perf-wow-many-streams-c45c434dbd03#:~:text=Streaming%20server%2Dside%20rendering%20was,handle%20back%2Dpressure%20more%20easily.).
2.  Code, where renderToStaticMarkup is used to generate the page template and renderToString calls are embedded to generate dynamic content. Since the string corresponding to the component is expected in these cases, it cannot be replaced by a stream. An example of such code provided [here](https://hackernoon.com/whats-new-with-server-side-rendering-in-react-16-9b0d78585d67) is as follows.

```
res.write("<!DOCTYPE html>");

res.write(renderToStaticMarkup(
 <html>
   <head>
     <title>My Page</title>
   </head>
   <body>
     <div id="content">
       { renderToString(<MyPage/>) }
     </div>
   </body>
 </html>);
```

Both Streaming and Progressive Hydration can help to bridge the gap between a pure SSR and a CSR experience. Let us now compare all the patterns that we have explored and try to understand their suitability for different situations.