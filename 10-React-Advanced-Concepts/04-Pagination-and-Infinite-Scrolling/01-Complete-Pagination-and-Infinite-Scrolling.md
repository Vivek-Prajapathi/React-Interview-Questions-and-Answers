# Pagination and Infinite Scrolling in React

## Learning Roadmap

1. Understand why long lists need pagination or progressive loading.
2. Learn page-based and cursor-based pagination.
3. Understand API request parameters and response metadata.
4. Build a page-number pagination component.
5. Fetch data when the page changes.
6. Implement infinite scrolling with the Intersection Observer API.
7. Handle loading, errors, empty states, and duplicate requests.
8. Manage race conditions, cleanup, and accessibility.
9. Compare pagination, infinite scrolling, and load-more patterns.

## 1. Why Pagination Is Needed

Rendering a very large collection at once can increase network transfer, memory usage, and rendering work. Pagination divides a collection into smaller portions and loads only the portion currently needed.

For example, an API might return 20 products per request rather than returning 2,000 products in one response.

Benefits include:
- Smaller responses and less initial data transfer.
- Faster initial display for large collections.
- Easier navigation through a known result set.
- Better control over how much data is rendered.

Pagination does not automatically solve every performance problem. Rendering thousands of already-loaded items can still be expensive; list virtualization may be needed for very large interfaces.

## 2. Page-Based Pagination

Page-based pagination uses a page number and a page size.

Example request:

```text
GET /api/products?page=2&limit=20
```

If pages are one-based, page 2 with 20 items per page represents items 21–40.

A typical response may look like:

```json
{
  "items": [{ "id": 21, "name": "Product 21" }],
  "page": 2,
  "limit": 20,
  "totalItems": 125,
  "totalPages": 7
}
```

The exact property names and page-number convention depend on the API contract.

### Calculating the offset

For a zero-based offset API:

```js
const offset = (page - 1) * pageSize;
```

For page 3 and page size 20, the offset is 40.

## 3. Cursor-Based Pagination

Cursor-based pagination uses a pointer to the position where the next batch should begin. The API might return a `nextCursor` value.

Example request:

```text
GET /api/products?limit=20&cursor=eyJpZCI6...
```

Example response:

```json
{
  "items": [{ "id": 41, "name": "Product 41" }],
  "nextCursor": "eyJpZCI6...",
  "hasMore": true
}
```

The client sends the returned cursor to fetch the next batch.

Cursor pagination is often useful for feeds where records are changing while the user browses. It can avoid some problems associated with offset pagination, but the client must follow the API's cursor contract. A cursor is usually opaque; do not construct or modify it.

## 4. Page-Based vs Cursor-Based Pagination

| Aspect | Page-based | Cursor-based |
|---|---|---|
| Request position | Page number or offset | Opaque cursor |
| Direct page navigation | Usually supported | Often not supported |
| Common UI | Page numbers and Next/Previous | Load more or infinite feed |
| Changing datasets | Offset positions can shift | Often more stable when designed well |
| Client responsibility | Track page and page size | Store and send next cursor |

Choose the approach provided by the backend API and suited to the product experience.

## 5. Basic React Page-Based Pagination

This example uses a local array to demonstrate the slicing logic:

```jsx
import { useState } from "react";

const products = Array.from({ length: 53 }, (_, index) => ({
  id: index + 1,
  name: `Product ${index + 1}`,
}));

const PAGE_SIZE = 10;

function ProductList() {
  const [page, setPage] = useState(1);
  const totalPages = Math.ceil(products.length / PAGE_SIZE);

  const startIndex = (page - 1) * PAGE_SIZE;
  const visibleProducts = products.slice(
    startIndex,
    startIndex + PAGE_SIZE
  );

  return (
    <section>
      <ul>
        {visibleProducts.map((product) => (
          <li key={product.id}>{product.name}</li>
        ))}
      </ul>

      <button
        disabled={page === 1}
        onClick={() => setPage((current) => current - 1)}
      >
        Previous
      </button>

      <span> Page {page} of {totalPages} </span>

      <button
        disabled={page === totalPages}
        onClick={() => setPage((current) => current + 1)}
      >
        Next
      </button>
    </section>
  );
}
```

The `slice` operation selects only the items for the current page. The functional state update uses the previous page value safely.

## 6. Fetching a Page from an API

When the page changes, the component can request that page from the server.

```jsx
import { useEffect, useState } from "react";

function Products() {
  const [page, setPage] = useState(1);
  const [items, setItems] = useState([]);
  const [totalPages, setTotalPages] = useState(1);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  useEffect(() => {
    const controller = new AbortController();

    async function loadProducts() {
      try {
        setLoading(true);
        setError("");

        const response = await fetch(
          `/api/products?page=${page}&limit=10`,
          { signal: controller.signal }
        );

        if (!response.ok) {
          throw new Error("Unable to load products");
        }

        const data = await response.json();
        setItems(data.items);
        setTotalPages(data.totalPages);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message || "Something went wrong");
        }
      } finally {
        if (!controller.signal.aborted) {
          setLoading(false);
        }
      }
    }

    loadProducts();

    return () => controller.abort();
  }, [page]);

  return (
    <section>
      {loading && <p>Loading products...</p>}
      {error && <p role="alert">{error}</p>}

      {!loading && !error && items.length === 0 && (
        <p>No products found.</p>
      )}

      <ul>
        {items.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>

      <button
        disabled={page <= 1 || loading}
        onClick={() => setPage((current) => current - 1)}
      >
        Previous
      </button>

      <span> Page {page} of {totalPages} </span>

      <button
        disabled={page >= totalPages || loading}
        onClick={() => setPage((current) => current + 1)}
      >
        Next
      </button>
    </section>
  );
}
```

Important points:
- The Effect depends on `page`, so a page change triggers a new request.
- `AbortController` aborts an obsolete request during cleanup.
- `response.ok` must be checked because `fetch` does not reject for HTTP error statuses such as 404 or 500.
- Loading, error, and empty states should be represented clearly.
- The UI uses stable item keys.

Adapt the response properties to the actual API. The example assumes `items` and `totalPages`.

## 7. Pagination Component as a Reusable Component

A reusable pagination control can receive the current page, total page count, and a page-change callback:

```jsx
function Pagination({ page, totalPages, onPageChange }) {
  return (
    <nav aria-label="Pagination">
      <button
        type="button"
        disabled={page <= 1}
        onClick={() => onPageChange(page - 1)}
      >
        Previous
      </button>

      <span aria-live="polite">
        Page {page} of {totalPages}
      </span>

      <button
        type="button"
        disabled={page >= totalPages}
        onClick={() => onPageChange(page + 1)}
      >
        Next
      </button>
    </nav>
  );
}
```

The parent owns the data and page state. The child presents navigation controls and reports user actions through `onPageChange`.

For a full numbered pagination bar, calculate a limited range of page numbers rather than rendering hundreds of buttons.

## 8. Infinite Scrolling

Infinite scrolling loads more data when the user approaches the end of the currently displayed list.

Typical flow:

1. Load the first batch.
2. Render the returned items.
3. Place a sentinel element after the list.
4. Observe the sentinel with `IntersectionObserver`.
5. When it enters the viewport, request the next batch.
6. Append new items to the existing list.
7. Stop when the API indicates there is no more data.

Infinite scrolling commonly uses cursor-based pagination, although it can also use page numbers.

## 9. Intersection Observer API

`IntersectionObserver` asynchronously observes whether a target element intersects a viewport or a specified ancestor.

It is useful for infinite scrolling because the application does not need to calculate scroll positions manually on every scroll event.

Basic browser example:

```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      console.log("Load the next batch");
    }
  });
});

observer.observe(sentinelElement);

// Later:
observer.disconnect();
```

Useful options include:
- `root`: the element used as the observation viewport; `null` means the browser viewport.
- `rootMargin`: expands or shrinks the root bounds, allowing earlier or later triggering.
- `threshold`: the proportion of the target that must intersect before a notification is generated.

## 10. Infinite Scrolling in React with a Sentinel

This example assumes an API that accepts a cursor and returns `items`, `nextCursor`, and `hasMore`.

```jsx
import { useCallback, useEffect, useRef, useState } from "react";

function InfiniteProducts() {
  const [items, setItems] = useState([]);
  const [cursor, setCursor] = useState(null);
  const [hasMore, setHasMore] = useState(true);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  const sentinelRef = useRef(null);
  const loadingRef = useRef(false);

  const loadMore = useCallback(async () => {
    if (loadingRef.current || !hasMore) return;

    loadingRef.current = true;
    setLoading(true);
    setError("");

    try {
      const params = new URLSearchParams({ limit: "20" });
      if (cursor) params.set("cursor", cursor);

      const response = await fetch(`/api/products?${params}`);

      if (!response.ok) {
        throw new Error("Unable to load more products");
      }

      const data = await response.json();

      setItems((current) => {
        const existingIds = new Set(current.map((item) => item.id));
        const newItems = data.items.filter(
          (item) => !existingIds.has(item.id)
        );
        return [...current, ...newItems];
      });

      setCursor(data.nextCursor ?? null);
      setHasMore(Boolean(data.hasMore));
    } catch (err) {
      setError(err.message || "Something went wrong");
    } finally {
      loadingRef.current = false;
      setLoading(false);
    }
  }, [cursor, hasMore]);

  useEffect(() => {
    loadMore();
  }, [loadMore]);

  useEffect(() => {
    const target = sentinelRef.current;
    if (!target || !hasMore) return;

    const observer = new IntersectionObserver(
      (entries) => {
        if (entries.some((entry) => entry.isIntersecting)) {
          loadMore();
        }
      },
      { root: null, rootMargin: "200px", threshold: 0 }
    );

    observer.observe(target);
    return () => observer.disconnect();
  }, [loadMore, hasMore]);

  return (
    <section>
      <ul>
        {items.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>

      {error && (
        <div role="alert">
          <p>{error}</p>
          <button type="button" onClick={loadMore}>Retry</button>
        </div>
      )}

      {loading && <p>Loading more products...</p>}
      {!hasMore && <p>You have reached the end.</p>}

      <div ref={sentinelRef} aria-hidden="true" />
      <button
        type="button"
        onClick={loadMore}
        disabled={loading || !hasMore}
      >
        Load more
      </button>
    </section>
  );
}
```

This is an instructional pattern, not a universal production-ready data-fetching solution. In production, consider request cancellation, retry behavior, cursor consistency, duplicate requests, and how errors affect the cursor.

## 11. Preventing Duplicate Requests

An observer may notify repeatedly while the sentinel remains visible. Multiple notifications can otherwise start overlapping requests.

Common safeguards:
- Track whether a request is already in progress.
- Disable or guard the load function while loading.
- Advance the cursor only from a successful response.
- Disconnect or pause observation when no more data exists.
- Deduplicate records using stable IDs when the API may overlap pages.

A React state variable updates asynchronously. A ref such as `loadingRef` can provide an immediate synchronous guard between closely spaced observer callbacks. Keep the ref and state synchronized carefully.

## 12. Handling Errors and Retry

A failed request should not silently mark the list as complete. Show an error and provide a retry action.

For cursor pagination, do not advance the cursor after a failed request. Retrying should request the same next batch.

For page-based pagination, keep the current page visible and allow the user to retry loading it.

## 13. Avoiding Race Conditions

When users move quickly between pages, an earlier request may finish after a later request. Its result must not overwrite the newer page.

Use `AbortController` in an Effect cleanup, or use a request identifier and ignore responses that are no longer current.

For infinite scrolling, prevent concurrent requests for the same cursor and ensure the next cursor is applied only after a successful response.

## 14. Infinite Scroll Cleanup

Disconnect the observer when:
- The component unmounts.
- The observed target changes.
- Observation is no longer needed because there are no more records.

If the request uses an `AbortController`, abort it during cleanup when appropriate. Observer cleanup and request cleanup are separate responsibilities.

## 15. Pagination vs Infinite Scrolling vs Load More

| Pattern | User experience | Useful when |
|---|---|---|
| Numbered pagination | User chooses a page | Search results, admin tables, catalogs |
| Infinite scrolling | More content appears automatically | Feeds and discovery experiences |
| Load More button | User explicitly requests another batch | Product lists and content collections |

Consider usability, accessibility, navigation, and whether users need to return to a specific position. Infinite scrolling can make footer access and position restoration harder.

## 16. Accessibility Best Practices

- Use a semantic `nav` element with an accessible label for pagination controls.
- Use real buttons or links, not clickable generic elements.
- Disable unavailable Previous and Next actions.
- Make loading and error feedback available to assistive technology.
- Use `aria-live` thoughtfully for status messages.
- Do not move keyboard focus unexpectedly when new items load.
- Provide a Load More button as an alternative or fallback when automatic loading is unavailable.
- Preserve meaningful headings and list semantics.

## 17. Common Mistakes

1. Rendering every record from a huge dataset without pagination or virtualization.
2. Assuming the API uses one-based pages when it uses zero-based pages.
3. Guessing response property names instead of following the API contract.
4. Replacing the list instead of appending items in infinite scrolling.
5. Forgetting stable React keys.
6. Allowing multiple observer callbacks to start duplicate requests.
7. Advancing a cursor after a failed request.
8. Failing to disconnect the observer.
9. Ignoring stale responses when users switch pages quickly.
10. Treating `fetch` HTTP error statuses as rejected Promises.
11. Providing no loading, error, empty, or end-of-list state.
12. Depending only on automatic infinite scrolling with no accessible alternative.

## 18. Performance: Pagination vs Virtualization

Pagination reduces how much data is fetched at once. Virtualization reduces how many list elements are mounted in the DOM at one time.

They can be combined:
- Pagination controls the amount of data loaded.
- Virtualization controls the number of rendered rows.

For very large lists, consider a virtualization library such as `react-window` or another maintained option suitable for the React version and project requirements.

## 19. Testing Checklist

For page-based pagination:
- First page loads correctly.
- Previous is disabled on the first page.
- Next is disabled on the last page.
- Page changes request the correct page and size.
- Loading, error, and empty states work.
- A stale request cannot overwrite the current page.

For infinite scrolling:
- Initial batch loads once.
- Intersecting the sentinel requests the next batch.
- Duplicate requests are prevented.
- New records append without duplicates.
- End-of-data stops further requests.
- Errors can be retried.
- Observer and request cleanup work.

## 20. Best Practices

- Follow the backend's pagination contract exactly.
- Prefer cursor pagination for continuously changing feeds when supported by the API.
- Use numbered pagination when users need direct navigation.
- Use `IntersectionObserver` rather than repeatedly measuring scroll position when it fits the interaction.
- Guard against duplicate requests.
- Handle errors, retries, empty states, and end-of-list states.
- Clean up observers and abort obsolete requests.
- Deduplicate records by stable identifiers when needed.
- Consider accessibility and position restoration.
- Use virtualization for extremely large rendered lists.

## Quick Revision

- Pagination divides a large dataset into smaller batches.
- Page-based pagination tracks a page number and page size.
- Cursor-based pagination uses an API-provided continuation token.
- Infinite scrolling loads more when a sentinel becomes visible.
- `IntersectionObserver` detects visibility efficiently.
- `loading` guards help prevent duplicate requests.
- `AbortController` can cancel obsolete fetches.
- Append records for infinite scrolling; replace records for page navigation.
- Virtualization reduces mounted DOM elements.
- Always handle loading, error, empty, and completion states.

## Final Interview Summary

Pagination loads a dataset in smaller portions, while infinite scrolling automatically requests the next portion as the user approaches the end of the list. Page-based pagination is useful for tables and search results where direct navigation matters. Cursor-based pagination is often suitable for feeds. In React, I manage request state, prevent duplicate requests, clean up observers, handle errors and stale responses, and consider accessibility and virtualization for large lists.
