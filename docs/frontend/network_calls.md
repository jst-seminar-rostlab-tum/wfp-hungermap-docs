---
sidebar_position: 3
---

# Network calls

The frontend application requests data from various APIs of WFP. These network calls fall under two categories based on how and when they're initiated.

## Server-side calls

Server-side calls are initiated from the Next.js server. We use these for data that is essential for the initial load of the application (such as the country borders, country alerts). Next.js can use the response from these calls to generate the initail page on the server (server-side rendering), though this is rarely used in this application, since most of the application (e.g. the Map component) can only be rendered on the client. The other advantage of server-side calls is that their responses can be cached and this can be shared between users. Since the data in this application rarely changes, there's no need to query the API for every request every user makes. Instead, the responses are cahced by Next by default, and when another user requests the same data, it will be served from the cache until the data is considered stale.

Server-side calls are HTTP requests that are called from the body of server components (usually `page.tsx` files that don't have `'use client'` at the top or in one of their parents), usually with `fetch`. Next has overriden the Fetch API, so by default the response of a fetch call is cahced indefinetly. To turn of caching, you need the following options to a fetch call: `{ cache: 'no-store'}`. We usually want to use caching in this app, but want to invalidate the cached data after some time. This can be done with the following options: `{ next: { revalidate: 3600 * 12 }}` - this means that the data will be considered stale after 12 hours. You can see examples of these in the root `page.tsx` and `GlobalDataRepositoryImpl.ts`

## Client-side calls

Client-side calls are initiated by the React application running in the user's browser. We don't want to fetch more data than what is required to serve the pages the user requested, so we only fetch what's needed on the server. Once the client application loads and the user request additional data (by selecting a contry, for instance), we can start calls from the client.

To manage the state of client-side calls, we use [Tanstack Query](https://tanstack.com/query/latest). This library gives us the `useQuery` hook, which wraps a network call and provides variables that track the state of the call, making handling loading states easier. It also allows client-side caching. This cache is stored in the user's browser, so this is not shared between users. However, if the users requests the same data mutiple times, we don't have to make the request to the API twice. Caching and refetching can be customized on a Query Client object. Each query can have their own settings, or they can use a global Query Client. Currently, all network calls use `cachedQueryClient` in `queryClient.ts`. This sets the caching mechanism to 1 hour TTL. If some requests don't require caching, a different query client can be used.

The Tanstack query hooks can be found in the `/src/domain/hooks` folder. The query functions are network calls (using fetch) very similar to the server-side calls, however these will be called on the client, so the default Node fetch implementation will be used, not Next's.