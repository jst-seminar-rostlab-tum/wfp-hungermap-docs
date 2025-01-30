# How to add Query Parameters

**Author:** `Jakob Schödl`

## Introduction

Query parameters can be used to store information inside of the URL of a page. This has multiple benefits:

* If the user reloads or reopens the page (e.g. after a browser restart), the displayed content is the same
* A specific view can be bookmarked or shared with other users
* The back and forward buttons of the browser can be used more fine-grained

Following this guide, you will add a query parameter in such a way that it can be used similar to a React State value.

## 1. Specifying the Type

Think of a name and type for the query parameter you want to add. Add it to
`src/domain/entities/queryParams/QueryParams.ts`.

:::info
When storing the parameter to the URL, its `.toString()` method is called. Make sure that such a method exists, and that
it
returns a string which can easily and uniquely be converted into the original object.

`null` and `undefined` are also accepted, but parameters that are one of those types will be omitted from the URL.
:::

## 2. Specifying Parsing Behavior

Given a URL, you need to specify a way to retrieve the state value. You may do this inside of the respective `useEffect`
hook in `src/domain/contexts/QueryParamsContext.tsx`.

## 3. Using the Query Parameter

Inside of a file, you can now use the `useQueryParams` hook to access and change the query parameters.

```tsx
const {queryParams, setQueryParam, setQueryParams} = useQueryParams();

// access the new param
queryParams.myNewParam

// change the new param
setQueryParam('myNewParam', newValue)

// change all query params
setQueryParams((prevParams) => ({...prevParams, myNewParam1: "value 1", myNewParam2: "value 2"}))
```

:::danger
If you need to change multiple params at once, always use a single `setQueryParams` call! Calling `setQueryParam`
(without s!) multiple times instead can lead to undesirable results, as the first of the subsequent calls might trigger
side effects which should have happened later.
:::
