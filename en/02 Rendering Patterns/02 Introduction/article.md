#### Rendering Pattern

# Introduction

When you start architecting a new web app, one of the foundational decisions you make is - “How and where do I want to render content?“. Should it be rendered on the web server, build server, on the Edge, or directly on the client? Should it be rendered all at once, partially, or progressively?

The answers to these critical decisions largely depend on the use case.” Choosing the most suitable rendering pattern can make a world of difference to the Developer Experience (DX) you create for the engineering team and the User Experience(UX) you design for your end users.

Choosing the correct pattern could lead to faster builds and excellent loading performance at low processing costs. On the other hand, a wrong choice of pattern can kill an app that could have brought to life a great business idea. So you must ensure that every revolutionary idea you have goes into development with the appropriate rendering pattern.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/01.webp" alt="Rendering Pattern" width="100%">

### Importance of Rendering Patterns

To create great UX, we usually try to optimize our apps for user-centric metrics, such as the [Core Web Vitals (CWV)](https://web.dev/articles/vitals). The CWV metrics measure parameters most relevant to user experience. Optimizing the CWV can help ensure a great user experience and optimal SEO for our apps.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/02.webp" alt="Core Web Vitals" width="100%">

To create a great DX for our product/engineering teams, we have to optimize our development environments by ensuring faster build times, easy rollbacks, scalable infrastructure, and many other features that help developers succeed.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/03.webp" alt="Developer Experience" width="100%">

Setting up a development environment based on these principles enables our development teams to build a great product efficiently.

Summarizing our expectations, we have now built quite a long list. But, if you choose the correct rendering pattern, you can get most of these benefits out of the box.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/04.webp" alt="Rendering Pattern Benefits" width="100%">

### Choosing a Pattern

Rendering patterns have come a long way, from Server Side Rendering (SSR) and Client Side Rendering (CSR) to highly nuanced patterns discussed and judged today on different forums. While this can get overwhelming, it’s important to remember that every pattern was designed to address specific use cases. A pattern characteristic beneficial for one use case can be detrimental in the case of another. It is also quite likely that different types of pages require different rendering patterns on the same website.

The Chrome team [has encouraged](https://web.dev/articles/rendering-on-the-web) developers to consider static or server-side rendering over a full rehydration approach. Over time, progressive loading and rendering techniques, by default, may help strike a good balance of performance and feature delivery when using a modern framework.

The following chapters cover different patterns - old and new in detail. But first, we’ll briefly introduce some of them to help you understand where they work best.

### Static Rendering

Static rendering is a simple yet powerful pattern you can use to build fast websites with nearly instant page loads.

With static rendering, the HTML for the entire page gets generated at build time and does not change till the next build. The HTML content is static and easily cacheable on a CDN or an Edge network. CDNs can quickly serve the pre-rendered cached HTML to clients when they request a specific page. This considerably cuts down the time it would otherwise take to process the request, render HTML content and respond to a request in a typical server-side rendering set-up.

The process described above is most suitable for pages that do not change often and display the same data no matter who requests them. Since we consume a lot of dynamic, customized data on the web today, we have variations of static rendering to cater to different use cases.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/05.webp" alt="Static Rendering" width="100%">

### Basic/Plain Static Rendering

Since there are many variations of static rendering, let’s call the primary technique we discussed earlier Plain Static Rendering. You can use it for pages with little to no dynamic content.

The page in the following real-estate website demo always displays the same content to everyone across the globe. It does not contain any dynamic data or personalized content.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_10.18.37_AM_bhybvb.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_10.10.58_AM_umtlyl.webm)

When the site is deployed and built (For example, on Vercel), the corresponding HTML is generated and saved on static storage on the server.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_10.18.37_AM_bhybvb.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_10.18.37_AM_bhybvb.webm)

When a user requests the page, the server sends the pre-generated HTML to the client. This response is also cached to the edge location closest to the user. The browser then renders the HTML and employs a JavaScript bundle to hydrate the page.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/06.webp" alt="Plain Static Rendering" width="100%">

Plain static rendering is excellent for performance because it results in an extremely **quick TTFB** since HTML is already available on the server. The browser receives a faster response and can render it quickly, resulting in a **fast FCP and LCP**. Since the content is static, there is **no layout shift** while rendering it.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/07.webp" alt="Plain Static Rendering Benefits" width="100%">

Thus, Plain Static Rendering, especially using a CDN for caching, helps to achieve great Core Web Vitals. However, most websites have at least some dynamic content or user interaction.

### Static Rendering with Client-Side **`fetch`**

Let’s say we wanted to enhance our real-estate demo to display the most recent property listings. We’d have to use a data provider to get these listings.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/08.webp" alt="Static Rendering with Client-Side Fetch" width="100%">

We can use **Static Rendering with Client-Side** **`fetch`** in this case. This pattern works great when you want to update data on every request.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/09.webp" alt="Static Rendering with Client-Side Fetch Benefits" width="100%">

You can still use static rendering for the website to render the UI with a **skeleton component** where you want to place the dynamic listing data. Then, after the page has loaded, we can fetch the data (using SWR, for example) on the client.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/10.webp" alt="Static Rendering with Client-Side Fetch Example" width="100%">

A custom API route is used to fetch the data from the CMS and return this data.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.49.59_PM_deygni.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_2.55.30_PM_r0jvez.webm)

The pre-generated HTML file is sent to the client when the user requests the page. The user initially sees the skeleton UI without any data. The client fetches the data from the API route, receives the response, and shows the listings. (the hydration call is not included in the example)es the response and shows the listings. (the hydration call is not included in the example)

<img src="../../../imgs/02RenderingPatterns/02 Introduction/11.webp" alt="Static Rendering with Client-Side Fetch Example" width="100%">

While **Static Rendering with Client-Side** fetch gives us a **good TTFB and FCP**, the **LCP is sub-optimal** since “largest content” can only be displayed after we get the listings data from the API route.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/12.webp" alt="Static Rendering with Client-Side Fetch Benefits" width="100%">

There is also a strong **possibility of layout shifts**, especially if the size of the skeleton UI doesn’t match the content rendered eventually.

Another disadvantage is that this approach could result in **higher server costs** since we call the API route once per page request.

Next.js offers some solutions, as discussed in the following sections, to improve your app’s performance when working with dynamic data.

### Static with **`getStaticProps`**

<img src="../../../imgs/02RenderingPatterns/02 Introduction/13.webp" alt="Static with getStaticProps" width="100%">

This method allows you to access the data provider and fetch data at build time on the server. It can be a good solution if you know that the dynamic data on a static page will always be available at build time.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/14.webp" alt="Static with getStaticProps Benefits" width="100%">

The **`getStaticProps`** method allows us to generate the HTML with the data on the server. Thus, we can avoid creating API routes to fetch the data on the client. Similarly, a skeleton component is not required while the data loads, as the page, will be rendered with the data.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.02.04_PM_avmzuy.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.02.04_PM_avmzuy.webm)

When we build the project, the data provider is called, and the returned data is piped to the generated HTML.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.06.26_PM_djvt57.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.06.26_PM_djvt57.webm)

When a user requests the page, the process is similar to plain static rendering. The response is cached and rendered to the screen, and the browser fetches the JavaScript bundles required to hydrate the page.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/15.webp" alt="Static with getStaticProps Example" width="100%">

From a client perspective, the network and main thread work are identical to plain static rendering, so we get a similarly **superior performance**.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/16.webp" alt="Static with getStaticProps Benefits" width="100%">

As the site grows, the DX, when using this method, may not be so good.

For sites with hundreds of pages built statically(for example, blog sites), calling the **`getStaticProps`** method repeatedly can result in **long build times**. If you’re using an external API, you might hit the request limit or run a **large usage bill**.

The method is also suitable only when we can get away with renewing data infrequently at build time. Frequent updates to data would mean often having to rebuild and redeploy the site.

### Incremental Static Regeneration

<img src="../../../imgs/02RenderingPatterns/02 Introduction/17.webp" alt="Incremental Static Regeneration" width="100%">

We can use incremental static regeneration to solve the build time, and dynamic data issues discussed earlier.

ISR is hybrid in the sense that it allows us to pre-render only certain static pages and render the dynamic pages on-demand when the user requests them. This results in shorter build times and allows automatic invalidation of the cache and regeneration of the page after a specific interval.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/18.webp" alt="Incremental Static Regeneration Benefits" width="100%">

Let’s assume we now want to show individual property details to enhance our previous demo. We can pre-render these new pages so that they load quickly when a user clicks on a listing.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/19.webp" alt="Incremental Static Regeneration Example" width="100%">

Next.js helps us achieve this by using the getStaticPaths method to generate dynamic paths. We can tell Next.js which pages to pre-generate based on their query parameter.

For our demo, let’s fetch all listings and pre-generate the pages for each of them. Note that this would take very long if there were thousands of listings. In that case, we will have to tell Next to only pre-generate a subset of all the pages and render a fallback when the remaining listing pages are generated on-demand (when the user requests).

<img src="../../../imgs/02RenderingPatterns/02 Introduction/20.webp" alt="Incremental Static Regeneration Example" width="100%">

The pre-rendered and generated on-demand pages are delivered similarly. If a user requests a page that hasn’t been generated yet, it gets generated on-demand and cached by the Edge. Thus, only the first user is likely to have a poorer experience for pages that are not pre-rendered. Everyone else will benefit from the fast, cached response.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.49.59_PM_deygni.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_3.49.59_PM_deygni.webm)

This addresses the long build-time problem of the previous methods. But we still have the landing page, which needs to be redeployed every time we have a new listing.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/21.webp" alt="Incremental Static Regeneration Example" width="100%">

To enable a refresh of the landing page, we can automatically invalidate the cache and regenerate the page in the background at a specific interval. We can use this by adding a revalidate field to the returned object.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/updated_jvhqnv.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/updated_jvhqnv.webm)

If a user requests a page that has been in the cache for longer than the specified number of seconds, the user will initially see the stale page. The page regeneration is triggered simultaneously. Once the page is regenerated in the background, the cache is invalidated and updated with the recently regenerated page.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/22.webp" alt="Incremental Static Regeneration Example" width="100%">

With Incremental Static Regeneration, we can show dynamic content by automatically revalidating the page every few seconds.

Although this is already a vast improvement from what we had before, there are a few drawbacks. Our content likely doesn’t update as often as the interval we have defined. This would result in unnecessary page regeneration and invalidation of the cache. Each time this happens, we invoke our serverless functions, which could result in higher server costs.

### On-demand Incremental Static Regeneration

<img src="../../../imgs/02RenderingPatterns/02 Introduction/23.webp" alt="On-demand Incremental Static Regeneration" width="100%">

To solve the last drawback mentioned above we have **On-demand Incremental Static Regeneration** which allows us to use ISR, but the regeneration occurs on certain events rather than at fixed intervals.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/24.webp" alt="On-demand Incremental Static Regeneration Benefits" width="100%">

Instead of using a revalidate field, we **`revalidate`** based on new data in API routes.

For example, we can listen to an incoming webhook event that tells us when new data has been added to our data provider. When we invoke the revalidate method, the page at the specified path regenerates automatically.

With regular ISR, the updated page is cached only at the edge nodes which have handled user requests for the page. On-demand ISR regenerates and redistributes the page across the edge network so that users worldwide will automatically see the most recent version of the page from the edge cache without seeing stale content. We also avoid unnecessary regenerations and serverless function calls, reducing operational costs compared to regular ISR.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/25.webp" alt="On-demand Incremental Static Regeneration Example" width="100%">

Thus On-demand ISR gives us performance benefits and a great DX.

Overall Static generation is a fantastic pattern, and its variations, especially ISR, can cover a variety of use cases.

It allows us to have fast and dynamic websites that are always online at a reasonable cost. However, there are use cases where static isn’t the best option, for example, for highly dynamic, personalized pages that are different for every user. Let’s see what pattern works best for these.

### Server-Side Rendering

<img src="../../../imgs/02RenderingPatterns/02 Introduction/26.webp" alt="Server-Side Rendering" width="100%">

With server-side rendering, we generate the HTML for every request. This approach is most suitable for pages containing highly personalized data, for example, data based on the user cookie or generally any data obtained from the user’s request. It’s also suitable for pages that should be render-blocking, perhaps based on authentication state.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/27.webp" alt="Server-Side Rendering Benefits" width="100%">

A personalized dashboard is an excellent example of highly dynamic content on a page. Most of the content is based on the user’s identity or authorization level that may be contained in a user cookie. This dashboard only shows when a user is authenticated and possibly shows user-specific sensitive data that should not be visible to others.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/28.webp" alt="Server-Side Rendering Example" width="100%">

Next.js allows us to render the page on the server using the **`getServerSideProps`** method. This method runs on the server for every request and eventually passes the returned data to the page to generate the HTML.

When a user requests the page, the **`getServerSideProps`** method runs, returns the data used to generate the page, and sends the response to the client. The client then renders this HTML and may send another request to fetch the JavaScript bundle that hydrates the elements.

The generated HTML content is unique to every request and should not be cached by the CDN.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/30.webp" alt="Server-Side Rendering Example" width="100%">

The network and main thread for the client are very similar for static and server-side rendering. The FCP is almost equal to the LCP, and we can easily avoid layout shifts as there is no dynamic content loading after the initial page load.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.36.49_PM_kkuxv3.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.36.49_PM_kkuxv3.webm)

However, the TTFB for server-rendered pages is significantly longer than static rendering as the page gets generated from scratch at every request.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/31.webp" alt="Server-Side Rendering Benefits" width="100%">

Although server-rendering is an excellent method when you want to render highly personalized data, there are some things to consider to achieve a great user experience and reduce server costs. These are likely to be high because you invoke serverless functions at every request.

1.**Execution time of** **`getServerSideProps`** <br>

The page generation does not start until the data from **`getServerSideProps`** is available. Hence, we must ensure that the **`getServerSideProps`** method doesn’t run too long.

2. **Deploy databases in the same region as your serverless function** <br>

If the data comes from a database, we must reduce the time taken to query the database. In addition to query optimization, you should also consider the location of the database.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.38.55_PM_uwecig.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.38.55_PM_uwecig.webm)

If your serverless function is deployed in San Francisco, but your database is in Tokyo, establishing a connection and getting the data can take a while. Instead, consider moving your database to the same region as your serverless function to ensure your database queries return data faster.

3. **Add** **`Cache-control`** **headers to responses** <br>

Another step to improve SSR performance is adding Cache-Control headers to the responses.

4. **Upgrade server hardware** <br>

Upgrading the server hardware can help to process individual requests quickly and get faster responses.

Vercel uses serverless functions to server-render your pages.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/32.webp" alt="Server-Side Rendering Example" width="100%">

Although serverless functions have many benefits, such as only having to pay for what you use, there are a few limitations. The time it takes to start up the lambda, known as the long cold boot, is a common issue with serverless functions. Also, connections to databases can be slow. You should also not call a serverless function located on one side of the planet from the other.

### Edge SSR + HTTP Streaming

<img src="../../../imgs/02RenderingPatterns/02 Introduction/33.webp" alt="Edge SSR + HTTP Streaming" width="100%">

Vercel is currently exploring **Edge Server-Side Rendering**, which will enable users to **server-render from all regions** and experience a **near-zero cold boot**. Another benefit of **Edge SSR** is that the edge runtime allows **HTTP streaming**.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.47.07_PM_usqexj.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.47.07_PM_usqexj.webm)

With serverless functions, we generate the entire page server-side and wait for the whole bundle to be loaded and parsed on the client before hydration can begin.

[![Video of the text above Click me](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.48.20_PM_auurip.webm)](https://res.cloudinary.com/dq8xfyhu4/video/upload/l_logo_pke9dv,o_52,x_-1510,y_-900/ac_none/v1609691928/CS%20Visualized/Screen_Recording_2022-05-05_at_5.48.20_PM_auurip.webm)

With Edge SSR, we can stream parts of the document as soon as they’re ready and hydrate these components granularly. This reduces the waiting time for users as they can see components as they stream in one by one.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/34.webp" alt="Edge SSR + HTTP Streaming Example" width="100%">

**Streaming SSR** also enables **React Server Components**. The combination of **Edge SSR with React Server Components** can allow us to have a beautiful **hybrid between static and server rendering**.

**React Server Components** allow us to partially render React components on the server, which is useful for components that require large dependencies that need not be downloaded to the client.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/35.webp" alt="Edge SSR + HTTP Streaming Benefits" width="100%">

Going back to the real-estate website example, if we wanted to show the landing page again and include region-specific listings for the user. The vast majority of the page only contains static data; it’s just the listings that require request-based data.

Instead of having to server-render the entire page, we can now choose only to render the listings component server-side and the rest client-side. Whereas we initially had to server-render the whole page to achieve this behavior, we can now get the **excellent performance of Static Rendering with the dynamic benefits of Server-Side Rendering**.

### Conclusion

<img src="../../../imgs/02RenderingPatterns/02 Introduction/36.webp" alt="Rendering Patterns Conclusion" width="100%">

We have now introduced many patterns that render content on the server. [Client Side Rendering](https://www.patterns.dev/react/client-side-rendering/)(CSR) with complete rehydration is still recommended for very dynamic websites where every component on the screen may change based on user interaction.

Depending on the type of the application or the page type, some of the patterns may be more suitable than the others. The following chart compares the highlights of different patterns and provides use cases for each.

<img src="../../../imgs/02RenderingPatterns/02 Introduction/37.webp" alt="Rendering Patterns Comparison" width="100%">

The following table from [Patterns for Building JavaScript Websites in 2022](https://dev.to/this-is-learning/patterns-for-building-javascript-websites-in-2022-5a93) offers another view pivoted by key application characteristics. It should be helpful for anyone looking for a suitable pattern for common [application holotypes](https://jasonformat.com/application-holotypes/).

|                      | **PORTFOLIO**    | **CONTENT**          | **STOREFRONT**              | **SOCIAL NETWORK**        | **IMMERSIVE**          |
|----------------------|------------------|-----------------------|-----------------------------|---------------------------|------------------------|
| **Holotype**         | Personal Blog    | CNN                   | Amazon                      | Facebook                  | Figma                 |
| **Interactivity**    | Minimal          | Linked Articles       | Purchase                    | Multi-Point, Real-time    | Everything            |
| **Session Depth**    | Shallow          | Shallow               | Shallow - Medium            | Extended                  | Deep                  |
| **Values**           | Simplicity       | Discover-ability      | Load Performance            | Dynamicism                | Immersiveness         |
| **Routing**          | Server           | Server, Hybrid        | Hybrid, Transitional        | Transitional, Client      | Client                |
| **Rendering**        | Static           | Static, SSR           | Static, SSR                 | SSR                       | CSR                   |
| **Hydration**        | None             | Progressive, Partial  | Partial, Resumable          | Any                       | None (CSR)            |
| **Example Framework**| 11ty             | Astro, Elder          | Marko, Qwik, Hydrogen       | Next, Remix               | Create React App      |
