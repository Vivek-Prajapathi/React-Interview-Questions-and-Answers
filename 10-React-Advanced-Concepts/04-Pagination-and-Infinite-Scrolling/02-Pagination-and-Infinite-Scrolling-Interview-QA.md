# Pagination and Infinite Scrolling – Interview Questions and Answers

## Table of Contents

1. [What is pagination?](#1-what-is-pagination)
2. [Why is pagination needed?](#2-why-is-pagination-needed)
3. [What is page-based pagination?](#3-what-is-page-based-pagination)
4. [What is cursor-based pagination?](#4-what-is-cursor-based-pagination)
5. [What is the difference between page and cursor pagination?](#5-what-is-the-difference-between-page-and-cursor-pagination)
6. [What is page size?](#6-what-is-page-size)
7. [How do you calculate an offset?](#7-how-do-you-calculate-an-offset)
8. [What is infinite scrolling?](#8-what-is-infinite-scrolling)
9. [What is the typical infinite-scroll flow?](#9-what-is-the-typical-infinite-scroll-flow)
10. [What is the Intersection Observer API?](#10-what-is-the-intersection-observer-api)
11. [What is a sentinel element?](#11-what-is-a-sentinel-element)
12. [What does root mean in IntersectionObserver?](#12-what-does-root-mean-in-intersectionobserver)
13. [What is rootMargin?](#13-what-is-rootmargin)
14. [What is threshold?](#14-what-is-threshold)
15. [Why use IntersectionObserver instead of a scroll listener?](#15-why-use-intersectionobserver-instead-of-a-scroll-listener)
16. [How do you implement page-based pagination in React?](#16-how-do-you-implement-page-based-pagination-in-react)
17. [How do you calculate total pages?](#17-how-do-you-calculate-total-pages)
18. [How do you calculate the starting index?](#18-how-do-you-calculate-the-starting-index)
19. [How do you fetch data when the page changes?](#19-how-do-you-fetch-data-when-the-page-changes)
20. [Why check response.ok when using fetch?](#20-why-check-responseok-when-using-fetch)
21. [How do you handle loading and error states?](#21-how-do-you-handle-loading-and-error-states)
22. [How do you handle an empty result?](#22-how-do-you-handle-an-empty-result)
23. [Why use functional state updates?](#23-why-use-functional-state-updates)
24. [How do you build a reusable Pagination component?](#24-how-do-you-build-a-reusable-pagination-component)
25. [How do you implement infinite scrolling in React?](#25-how-do-you-implement-infinite-scrolling-in-react)
26. [Why append items instead of replacing them?](#26-why-append-items-instead-of-replacing-them)
27. [How do you prevent duplicate requests?](#27-how-do-you-prevent-duplicate-requests)
28. [Why can a ref help with a loading guard?](#28-why-can-a-ref-help-with-a-loading-guard)
29. [How do you prevent duplicate records?](#29-how-do-you-prevent-duplicate-records)
30. [How do you detect the end of the list?](#30-how-do-you-detect-the-end-of-the-list)
31. [How do you handle errors in infinite scrolling?](#31-how-do-you-handle-errors-in-infinite-scrolling)
32. [How do you implement retry?](#32-how-do-you-implement-retry)
33. [What is a race condition in pagination?](#33-what-is-a-race-condition-in-pagination)
34. [How do you prevent stale page responses?](#34-how-do-you-prevent-stale-page-responses)
35. [What is AbortController?](#35-what-is-abortcontroller)
36. [How do you clean up an IntersectionObserver?](#36-how-do-you-clean-up-an-intersectionobserver)
37. [Does disconnecting an observer cancel a fetch?](#37-does-disconnecting-an-observer-cancel-a-fetch)
38. [What is the difference between pagination and infinite scrolling?](#38-what-is-the-difference-between-pagination-and-infinite-scrolling)
39. [What is a Load More pattern?](#39-what-is-a-load-more-pattern)
40. [What is list virtualization?](#40-what-is-list-virtualization)
41. [How is virtualization different from pagination?](#41-how-is-virtualization-different-from-pagination)
42. [Can pagination and virtualization be combined?](#42-can-pagination-and-virtualization-be-combined)
43. [What are the accessibility concerns with infinite scrolling?](#43-what-are-the-accessibility-concerns-with-infinite-scrolling)
44. [Why should pagination use semantic navigation?](#44-why-should-pagination-use-semantic-navigation)
45. [How do you handle changing page size?](#45-how-do-you-handle-changing-page-size)
46. [What happens if the total page count changes?](#46-what-happens-if-the-total-page-count-changes)
47. [What is offset pagination's weakness with changing data?](#47-what-is-offset-paginations-weakness-with-changing-data)
48. [Why should a cursor be treated as opaque?](#48-why-should-a-cursor-be-treated-as-opaque)
49. [What should happen when the first page loads?](#49-what-should-happen-when-the-first-page-loads)
50. [How do you test page-based pagination?](#50-how-do-you-test-page-based-pagination)
51. [How do you test infinite scrolling?](#51-how-do-you-test-infinite-scrolling)
52. [What is wrong with this pagination calculation?](#52-what-is-wrong-with-this-pagination-calculation)
53. [What is wrong with this infinite-scroll implementation?](#53-what-is-wrong-with-this-infinite-scroll-implementation)
54. [Why might an observer trigger more than once?](#54-why-might-an-observer-trigger-more-than-once)
55. [How do you handle a very large list?](#55-how-do-you-handle-a-very-large-list)
56. [When would you choose cursor pagination?](#56-when-would-you-choose-cursor-pagination)
57. [When would you choose numbered pagination?](#57-when-would-you-choose-numbered-pagination)
58. [What are the common pagination mistakes?](#58-what-are-the-common-pagination-mistakes)
59. [What are the best practices in React?](#59-what-are-the-best-practices-in-react)
60. [Give a concise interview summary.](#60-give-a-concise-interview-summary)

---

## 1. What is pagination?

Pagination is a technique for dividing a large dataset into smaller portions and loading one portion at a time. Instead of requesting every record at once, the client requests a page or batch. It improves initial loading and makes large collections easier to navigate.

## 2. Why is pagination needed?

It reduces the amount of data transferred and processed in one request. It can improve initial display time and reduce memory and rendering work. For extremely large rendered lists, I may also use virtualization because pagination alone does not limit how many already-loaded elements are in the DOM.

## 3. What is page-based pagination?

Page-based pagination uses a page number and a page size. For example, `page=2&limit=20` requests the second batch of 20 records, assuming the API uses one-based page numbering.

## 4. What is cursor-based pagination?

Cursor-based pagination uses a token or pointer returned by the API to request the next batch. The client stores the `nextCursor` and sends it with the next request. I treat the cursor as opaque and follow the backend contract.

## 5. What is the difference between page and cursor pagination?

Page pagination identifies a position using a page number or offset and often supports direct page navigation. Cursor pagination uses a continuation token and is commonly used for feeds and infinite scrolling. Cursor pagination can behave more consistently when records change, depending on the backend implementation.

## 6. What is page size?

Page size is the maximum number of records requested in one page or batch. For example, a page size of 20 means the client requests up to 20 records at a time.

## 7. How do you calculate an offset?

For a zero-based offset API, I use `(page - 1) * pageSize`. For example, page 3 with a page size of 20 has an offset of 40. I first confirm whether the backend expects page numbers or offsets.

## 8. What is infinite scrolling?

Infinite scrolling loads additional records automatically when the user approaches the end of the currently displayed content. It is commonly used for social feeds, discovery pages, and long content lists.

## 9. What is the typical infinite-scroll flow?

I load the initial batch, render it, and place a sentinel after the list. I observe that sentinel with `IntersectionObserver`. When it intersects the viewport, I request the next batch, append the records, and stop observing or loading when the API indicates there is no more data.

## 10. What is the Intersection Observer API?

It is a browser API that asynchronously observes the intersection of a target element with a viewport or ancestor element. In React, I use it to detect when a sentinel approaches the visible area and trigger the next data request.

## 11. What is a sentinel element?

A sentinel is a small DOM element placed near the end of a list. The application observes it. When it becomes visible, the application can load more records.

## 12. What does root mean in IntersectionObserver?

`root` defines the element used as the observation viewport. If it is `null`, the browser viewport is used. If I provide a scrollable container, the observer measures intersection relative to that container.

## 13. What is rootMargin?

`rootMargin` expands or shrinks the root's bounding area. For example, `rootMargin: "200px"` can cause the observer to trigger before the sentinel actually reaches the visible viewport, giving the next batch time to load.

## 14. What is threshold?

`threshold` defines the proportion of the target that must intersect the root before the observer reports the threshold crossing. A threshold of `0` means the callback can be notified when any part begins intersecting.

## 15. Why use IntersectionObserver instead of a scroll listener?

It provides an asynchronous, browser-managed way to detect element visibility. It avoids manually calculating scroll positions on every scroll event and is a natural fit for sentinel-based loading.

## 16. How do you implement page-based pagination in React?

I keep the current page in state. When the page changes, I either slice a local array or fetch that page from the API. I render the records and provide Previous and Next controls, disabling them at the boundaries.

## 17. How do you calculate total pages?

I use `Math.ceil(totalItems / pageSize)`. For example, 125 records with a page size of 20 require 7 pages.

## 18. How do you calculate the starting index?

For one-based pages, I use `(page - 1) * pageSize`. I use that index as the start position when slicing a local array.

## 19. How do you fetch data when the page changes?

I store the page in state and use an Effect that depends on that page. Inside the Effect, I request the corresponding page. I clean up obsolete requests and handle loading, errors, and empty results.

## 20. Why check response.ok when using fetch?

`fetch` rejects for network-level failures, but an HTTP response such as 404 or 500 still resolves to a Response object. I check `response.ok` and throw or handle an error when it is false.

## 21. How do you handle loading and error states?

I keep separate loading and error state. I show a loading indicator while the request is pending, display an accessible error message if it fails, and provide a retry action where appropriate.

## 22. How do you handle an empty result?

I check whether the returned items array is empty and display a clear empty state, such as "No products found." I distinguish an empty successful response from a failed request.

## 23. Why use functional state updates?

When the next state depends on the previous state, I use the functional form, such as `setPage(current => current + 1)`. It avoids relying on a potentially stale state value captured by a callback.

## 24. How do you build a reusable Pagination component?

I pass the current page, total page count, and an `onPageChange` callback as props. The component renders the controls and reports user actions. The parent owns the page state and data fetching.

## 25. How do you implement infinite scrolling in React?

I use a sentinel element with a ref and observe it in an Effect. When it intersects, I call a function that requests the next batch. I append the new records, guard against duplicate requests, and disconnect the observer in cleanup.

## 26. Why append items instead of replacing them?

Infinite scrolling is intended to keep previously loaded records visible. Therefore, I append the next batch to the existing list. In ordinary page navigation, I generally replace the visible page's records instead.

## 27. How do you prevent duplicate requests?

I check whether a request is already in progress before starting another one. I also check whether more records exist. A synchronous ref guard can prevent closely spaced observer callbacks from starting duplicate requests.

## 28. Why can a ref help with a loading guard?

Updating React state schedules a render and is not an immediate synchronous lock. A ref can be updated immediately and read by another callback before a render occurs. I keep the ref synchronized with the loading lifecycle.

## 29. How do you prevent duplicate records?

I use a stable unique identifier, such as an item ID, to check whether a record is already present before appending it. I also verify that the backend's cursor or page contract does not return unintended overlaps.

## 30. How do you detect the end of the list?

I follow the API response. It may provide `hasMore`, `nextCursor`, or a total count. When the API indicates there is no next batch, I stop requesting more records and display an end-of-list message if useful.

## 31. How do you handle errors in infinite scrolling?

I show an error message and keep the current records visible. I do not advance the cursor after a failed request. I provide a retry action that attempts to load the same next batch again.

## 32. How do you implement retry?

I keep the cursor unchanged when a request fails and expose a retry button that calls the same load function. I make sure the loading guard is reset in a `finally` block so retry is possible.

## 33. What is a race condition in pagination?

It can happen when multiple page requests are in flight and responses arrive in a different order from the requests. An older response might overwrite data for the newer page if I do not handle it.

## 34. How do you prevent stale page responses?

I can abort the previous request with `AbortController` when the page changes. Another option is to use a request ID and ignore a response if it does not belong to the latest request.

## 35. What is AbortController?

`AbortController` is a browser API that can signal cancellation to supported asynchronous operations, including `fetch`. I pass its signal to the request and call `abort()` when the request becomes obsolete or the component no longer needs it.

## 36. How do you clean up an IntersectionObserver?

I call `observer.disconnect()` in the cleanup function of the Effect that created it. This stops the observer from watching its targets.

## 37. Does disconnecting an observer cancel a fetch?

No. Disconnecting stops future intersection notifications. It does not cancel a request that has already started. I use an `AbortController` separately when I need to cancel a fetch.

## 38. What is the difference between pagination and infinite scrolling?

Pagination usually presents explicit page controls and lets the user navigate between pages. Infinite scrolling automatically loads additional content as the user approaches the end of the list. Pagination is often better for tables and search results; infinite scrolling is common for feeds.

## 39. What is a Load More pattern?

It displays a button that the user activates to request another batch. It provides more control than automatic infinite scrolling and can be a useful accessible alternative.

## 40. What is list virtualization?

Virtualization renders only the list items currently visible, plus a small buffer, instead of mounting every item in a huge list. It reduces DOM work and can improve scrolling performance.

## 41. How is virtualization different from pagination?

Pagination limits how much data is fetched or displayed at a time. Virtualization limits how many list elements are mounted in the DOM. They address different performance costs.

## 42. Can pagination and virtualization be combined?

Yes. Pagination controls how much data is loaded from the server, while virtualization controls how many of the loaded records are rendered at once. This is useful for very large datasets.

## 43. What are the accessibility concerns with infinite scrolling?

Users need to understand when more content is loading and when the list has ended. I provide accessible status messages, preserve keyboard focus, use semantic list markup, and consider a Load More button so users are not dependent only on automatic loading.

## 44. Why should pagination use semantic navigation?

A `nav` element with an accessible label communicates that the controls are navigation. Real buttons or links provide expected keyboard behavior and make the interface easier for assistive technology.

## 45. How do you handle changing page size?

I update the page size and usually reset the page to the first page, or otherwise calculate a valid page based on the new size. I then fetch the data using the new page-size parameters.

## 46. What happens if the total page count changes?

The current page may become invalid, for example if records are deleted and the last page disappears. I should validate the current page against the updated total and move to a valid page when needed.

## 47. What is offset pagination's weakness with changing data?

If records are inserted or deleted before the current offset, items can shift between pages. This can cause skipped or repeated records. Cursor pagination can reduce some of these problems when the backend designs its cursor and ordering appropriately.

## 48. Why should a cursor be treated as opaque?

The cursor is defined by the backend. The client should store it and send it back unchanged rather than trying to interpret or construct it. Its internal format may change without notice.

## 49. What should happen when the first page loads?

I show an initial loading state, request the first page or batch, then render the returned records. I handle an empty result, errors, and whether more data exists.

## 50. How do you test page-based pagination?

I test the first and last page boundaries, correct request parameters, page changes, loading and error states, empty results, and whether stale requests can overwrite the current page. I also verify that the correct records appear for each page.

## 51. How do you test infinite scrolling?

I test initial loading, sentinel intersection, next-batch requests, duplicate-request prevention, record appending, duplicate removal, end-of-data behavior, retry, and observer cleanup. In component tests, I may mock `IntersectionObserver`.

## 52. What is wrong with this pagination calculation?

```js
const startIndex = page * pageSize;
```

If pages are one-based, this skips the first page's range. The correct calculation is `(page - 1) * pageSize`. I would also confirm the backend's page-number convention.

## 53. What is wrong with this infinite-scroll implementation?

```js
const observer = new IntersectionObserver(() => {
  fetchNextPage();
});
observer.observe(element);
```

It does not show cleanup, loading guards, an end-of-data check, or error handling. The observer may trigger repeatedly and start duplicate requests. I would disconnect it in cleanup and guard the load function.

## 54. Why might an observer trigger more than once?

The callback can run as the target enters or changes intersection thresholds, and the sentinel may remain visible after new items are appended. I prevent duplicate work with a loading guard and stop observing when there is no more data.

## 55. How do you handle a very large list?

I choose an appropriate pagination strategy to limit fetched data. If many loaded records still need to be displayed, I use virtualization. I also avoid unnecessary re-renders and keep item keys stable.

## 56. When would you choose cursor pagination?

I would choose it for a continuously changing feed or an API designed around continuation tokens. It is useful when direct navigation to arbitrary page numbers is not required and the backend provides reliable cursor semantics.

## 57. When would you choose numbered pagination?

I would choose it for search results, admin tables, and catalogs where users need to know their position or jump to a particular page. It also makes navigation and returning to a specific result set more explicit.

## 58. What are the common pagination mistakes?

Common mistakes include using the wrong page-number convention, guessing API response fields, forgetting loading and error states, allowing stale responses to overwrite current data, failing to guard duplicate requests, and not cleaning up observers.

## 59. What are the best practices in React?

I follow the API contract, keep request state explicit, use stable keys, prevent duplicate requests, cancel obsolete requests, disconnect observers, handle errors and empty states, and consider accessibility. For very large lists, I evaluate virtualization.

## 60. Give a concise interview summary.

Pagination divides a large dataset into smaller batches. Page-based pagination is useful when users need direct page navigation, while cursor-based pagination is often useful for feeds. Infinite scrolling uses `IntersectionObserver` to request more records as a sentinel becomes visible. In React, I manage loading and errors, prevent duplicate requests, clean up observers, handle stale responses, and consider accessibility and virtualization.
