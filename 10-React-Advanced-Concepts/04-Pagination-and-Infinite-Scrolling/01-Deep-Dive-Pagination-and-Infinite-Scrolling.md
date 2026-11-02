# Pagination and Infinite Scrolling in React

## 1. What pagination solves

Pagination divides a large collection into smaller portions. It reduces the amount of data rendered and often reduces the amount transferred in one request. Pagination is a UI and data-fetching pattern; React does not provide a built-in pagination component.

Common models:
- **Page-number pagination:** request page 1, 2, 3, and so on.
- **Offset/limit pagination:** request `limit` records after an `offset`.
- **Cursor pagination:** request records after a cursor representing a stable position in the dataset.

Cursor pagination is often preferable for frequently changing datasets because offset-based pages can shift when records are inserted or deleted.

## 2. Client-side vs server-side pagination

**Client-side:** fetch the full dataset, then display a slice. This is simple for small collections but can be expensive for large datasets.

```jsx
const pageSize = 10;
const start = (page - 1) * pageSize;
const pageItems = items.slice(start, start + pageSize);
```

**Server-side:** send page/limit or cursor parameters to the backend. The server returns only the requested portion and usually metadata such as total count or next cursor. This is better for large datasets.

## 3. Page-number implementation

```jsx
function Pagination({ currentPage, totalPages, onPageChange }) {
  return (
    <nav aria-label="Pagination">
      <button
        disabled={currentPage === 1}
        onClick={() => onPageChange(currentPage - 1)}
      >
        Previous
      </button>
      <span>Page {currentPage} of {totalPages}</span>
      <button
        disabled={currentPage === totalPages}
        onClick={() => onPageChange(currentPage + 1)}
      >
        Next
      </button>
    </nav>
  );
}
```

When filters or sorting change, reset the page to 1; otherwise the current page may be beyond the new result count.

## 4. Server-side data fetching

Track the page, result data, loading state, and error state. When the page changes, fetch that page. Consider cancelling the previous request or ignoring its result so a slower earlier request cannot replace newer data.

```jsx
useEffect(() => {
  const controller = new AbortController();

  async function loadPage() {
    setLoading(true);
    setError("");
    try {
      const response = await fetch(`/api/products?page=${page}&limit=10`, {
        signal: controller.signal
      });
      if (!response.ok) throw new Error("Request failed");
      const result = await response.json();
      setProducts(result.items);
      setTotalPages(result.totalPages);
    } catch (error) {
      if (error.name !== "AbortError") setError(error.message);
    } finally {
      if (!controller.signal.aborted) setLoading(false);
    }
  }

  loadPage();
  return () => controller.abort();
}, [page]);
```

The exact response structure depends on the API contract.

## 5. Infinite scrolling

Infinite scrolling loads the next batch as the user approaches the end of the current list. It is often implemented with `IntersectionObserver`, which observes a sentinel element near the bottom.

```jsx
const sentinelRef = useRef(null);

useEffect(() => {
  const node = sentinelRef.current;
  if (!node || !hasMore || loading) return;

  const observer = new IntersectionObserver(entries => {
    if (entries[0].isIntersecting) {
      loadNextPage();
    }
  });

  observer.observe(node);
  return () => observer.disconnect();
}, [hasMore, loading, loadNextPage]);
```

In a complete implementation, ensure `loadNextPage` has stable identity or structure the observer effect carefully. Also prevent duplicate concurrent requests.

## 6. Infinite-scroll state model

Typical state:
- `items`: accumulated records.
- `cursor` or `page`: next position.
- `hasMore`: whether more data exists.
- `loading`: whether a request is in progress.
- `error`: request failure.
- Optional `seenIds`: deduplication if the API can return overlapping pages.

Append new records immutably:

```jsx
setItems(previous => [...previous, ...newItems]);
```

Do not append the same page repeatedly. Guard against duplicate observer notifications and stale requests.

## 7. Accessibility and UX

- Provide a visible loading indicator and an error/retry path.
- Keep a normal pagination alternative where appropriate.
- Use a labelled navigation region for page controls.
- Announce result updates when useful with an appropriate live region.
- Preserve scroll position intentionally when changing pages.
- Avoid trapping keyboard and screen-reader users in an endless list.
- Consider a “Load more” button as a predictable accessible alternative.

## 8. Choosing a strategy

Use numbered pagination when users need to jump to a specific page, compare pages, or return to a known location. Use infinite scrolling for discovery feeds where continuous browsing is natural. Use “Load more” when you want incremental loading with explicit user control.

## 9. Common mistakes

- Fetching the entire dataset when server pagination is available.
- Forgetting to reset page after filters change.
- Allowing older responses to overwrite newer data.
- Triggering multiple next-page requests from repeated observer callbacks.
- Failing to handle the last page or empty results.
- Not providing loading, error, and retry states.
