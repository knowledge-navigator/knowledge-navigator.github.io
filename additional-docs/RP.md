# Rendering Patterns for Web Applications
Personal research into the most common rendering patterns for web applications as of 2023. This research was done to determine which frameworks and languages I would utilize in my projects this semester. The chosen technologies would aid me in the rendering pattern I wanted to use.

## Static Websites
Web pages are defined in advance (think of `HTML` files with some `CSS` styling applied to them) and then uploaded to a storage bucket somewhere in the cloud online (E.G. `AWS`) with a domain name pointed to these files.

Some of the most popular frameworks that support programatically building static websites are:
- [Hugo](https://gohugo.io/)
- [11ty](https://www.11ty.dev/)
- [Jekyll](https://jekyllrb.com/)

The downside of static sites is that they are suboptimal for websites where the data changes often. We therefore only want to develop static websites where interactivity is low and dynamic data is not needed.

For my personal or group project, the only use case of a static site may be for reference manuals or landing pages but not for the functional website itself.

## Multi-Page Applications
The `HTML` and data is put together dynamically on a server (e.g. `Node.js`) whenever a request comes in from the browser, essentially rendering 100% of the page on a server. The appearance of the website is therefore subject to change to whatever the underlying data supplied by the server is.

Some of the most popular frameworks that support building multi-page applications are:
- [Ruby on Rails](https://rubyonrails.org/)
- [Django](https://www.djangoproject.com/)
- [Laravel](https://laravel.com/)
- [WordPress](https://wordpress.com/)

Even though this approach is great for nearly any use-case, it is rather clunky compared some of the more modern approaches (see further on this page).

For my projects, I could use this approach, but there are more fluid rendering patterns that take the same amount of development time available, thus I see this as only a worse option.

## Single-Page Applications (SPA)
All the user-interface (UI) rendering happens in the browser. Essentially a root `HTML` page acts as a shell which executes `JavaScript` to render the UI. `HTTP` requests will be made to fetch any data needed for the UI from a server. Multiple *routes* can be defined through client-side routing, which updates the pages through JavaScript in the browser instead of pointing routes to a server. This will 'simulate' a website with multiple pages, even though it is actually just a 'single-page'.

A few popular frameworks include:
- [Angular](https://angular.io/)
- [React](https://reactjs.org/)
- [Vue](https://vuejs.org/)

The advantages of this approach is huge, as it makes the website feel like a mobile-app in that it will instantaneously switch between routes and provide content. The disadvantage of this is that it requires a large JavaScript bundle, thus the initial page-load will be relatively slow. Furthermore, due to only a shell being rendered, search engines struggle to understand any of the content on the various dynamic routes. This leads to poor Search-Engine Optimization (SEO) and as such, the website will struggle to gain traction on the internet.

For my projects, the lack of SEO is not an issue, as the pages are designed for a specific group of people (select schools/hospitals), thus these web pages would never be intended to be shared or publically accessed on the web. As such, this rendering approach is a solid choice for both my personal and group project.

## Server-Side Rendering (SSR) with Hydration
The initial page-load UI and data is rendered dynamically on the server, but any data required post page-load (runtime) is hydrated on the client-side like in SPA. Frameworks using this approach, are also known as *Metaframeworks*.

Examples of frameworks which use this best-of-both-worlds approach include:
- [Next.js](https://nextjs.org/)
- [Nuxt](https://nuxt.com/)
- [SvelteKit](https://kit.svelte.dev/)

Even though this is the most popular rendering strategy at this moment, it still has the drawback of requiring a server, which can be expensive and not possible in some scenarios.

This approach is not realistic for a project with no budget, although it would be an effective approach in most scenarios.

## Static-Site Generation (SSG) with Hydration
This differs to SSR in that all HTML files are rendered in advance and uploaded to a storage bucket. These files are supplied on page-load and then hydrated with JavaScript post page-load. Sites made like this are known as *Jamstack* sites and usually built by the same frameworks that allow SSR (see above).

The advantage of this approach is that it is more simple and cost-efficient when compared to SSR with the only downside being that the site needs to be redployed whenever any data changes.

This solves the previous solution of needing an expensive server and may be a solid option for my projects.

## Incremental Static Regeneration (ISR)
You deploy a static site like SSG, but individual pages are rebuilt whenever the cache is invalidated, thus allowing you to use dynamic data without needing redeployment.

This is technically the best-of-both-worlds between SSR and SSG, but can be complex to self-host. Examples of hosts that can help set it up include:
- [Vercel](https://vercel.com/)

This pattern would be great for my project, but the complexity sets it out of the scope of this semester.

## Additional Notes
### Hydration
Hydration can make pages feel like they are still loading on the initial page-load. Partial hydration is a solution for this issue, only hydrating what is visible to the end user. Certain elements such as the footer, may be hydrated at a later stage, such as when the user scrolls down for example. [Vite](https://vitejs.dev/) is a tool that supports partial hydration.

### Islands
The islands architecture, simply involves the splitting of interactive JavaScript and static HTML components within a web page. This might even be more effective than partial hydration.[Astro](https://astro.build/) is a good example of a framework that supports islands.

### Streaming SSR (Paradigm)
This paradigm is an approach where chunks of a page are rendered concurrently rather than all at once, thus decreasing page loading times and feeling more performant to the end user.

### Resumability
This approach removes the idea of Hydration altogether:
1. Serialize all `JavaScript` to `HTML`.
2. The `JavaScript` code is broken into many tiny chunks.
3. Initial page-load is static `HTML` with no hydration needed.
4. Any interactivity requiring `JavaScript`, is lazy-loaded in the background.