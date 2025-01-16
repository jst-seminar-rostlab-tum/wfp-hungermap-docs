---
sidebar_position: 1
---

# How to add a new network call

If you need to fetch additional data in the application, you'll likely need to create a new Tanstack Query, unless the data is needed for the initial page. Make sure to read the docs on [Network calls](/docs/frontend/network_calls) first to understand the differences between client- and server-side calls.

First, you'll need to extend one of the repository interfaces (`domain/repositories`) with the method signature, then you need to create the implementation of that method in one of the repository implementations (`infrastructure/repositories`). Use `fetch` to send the HTTP request to the server. If this will be a server-side call, make sure to set the Next caching options as neededa and call it from a server component.

If it's a client-side call, you'll need to call the method from the query function of a `useQuery()` hook. Create (a new file and) a new hook in `domain/hooks`. The `queryKey` has to be unique throughout the app, queries with the same key will share their cached data. It can also include parameters, such as IDs. The query function should point to method of a repository resolved using the `container.tsx`. If the defualt caching mechanism is fine, use the `cachedQueryClient` as a query client, otherwise you can customize the options.

When you include a useQuery hook in your component, the query function will run when that component renders. If multiple components need access to the state of a query, but only some of them should trigger, you can add an enabled parameter to the query generator function. You can see exmples for this in `alertHooks.ts`, `ConflictLayer.tsx` and `AlertsMenu.tsx`. Both components subscribe to the state of the network call that fetches the conflicts. However, the `AlertsMenu` is rendered by default, but the call only have to be intiated once the user has selected the Conflict alert. So in the `AlertsMenu` component, the `useHazardQuery` is called with `false`, which prevents the query from being run when that component renders. However, `ConflictLayer` calls the hook with `true`, so the call is made to the server (or the cache) once the layer is rendered.

```typescript
export const useDataQuery = (enabled: boolean) =>
  useQuery<DataType[]>(
    {
      queryKey: ['unique_key'],
      queryFn: repository.method,
      enabled,
    },
    cachedQueryClient
  );
```