# Pagination and Infinite Scrolling — Interview Questions and Answers

## Table of Contents
1. [What is pagination?](#q1-what-is-pagination)
2. [Client-side vs server-side pagination?](#q2-client-side-vs-server-side-pagination)
3. [What is cursor-based pagination?](#q3-what-is-cursor-based-pagination)
4. [How do you calculate the offset for a page?](#q4-how-do-you-calculate-the-offset-for-a-page)
5. [Why reset the page when filters change?](#q5-why-reset-the-page-when-filters-change)
6. [How does infinite scrolling work?](#q6-how-does-infinite-scrolling-work)
7. [Why use IntersectionObserver?](#q7-why-use-intersectionobserver)
8. [How do you prevent duplicate infinite-scroll requests?](#q8-how-do-you-prevent-duplicate-infinite-scroll-requests)
9. [How do you handle out-of-order page responses?](#q9-how-do-you-handle-out-of-order-page-responses)
10. [Pagination vs infinite scrolling: when do you choose each?](#q10-pagination-vs-infinite-scrolling-when-do-you-choose-each)

## Q1. What is pagination?
**Interview answer:** Pagination divides a large dataset into smaller chunks. The UI requests or displays one chunk at a time. It improves usability and can reduce network transfer and rendering work.

## Q2. Client-side vs server-side pagination?
**Interview answer:** With client-side pagination, the application already has the full dataset and slices it locally. With server-side pagination, the client requests only the required page from the backend. I choose server-side pagination for large datasets because downloading everything is inefficient.

## Q3. What is cursor-based pagination?
**Interview answer:** Cursor pagination uses a marker that represents where the next batch should begin. The API returns a next cursor rather than relying only on a page number. It can behave more consistently than offsets when records are inserted while the user is browsing.

## Q4. How do you calculate the offset for a page?
**Interview answer:** For one-based page numbering, the offset is `(page - 1) * pageSize`. For example, page 3 with 10 records per page starts at offset 20.

## Q5. Why reset the page when filters change?
**Interview answer:** A filter can reduce the number of available pages. If the user is on page 8 and the filtered result has only 2 pages, the current page becomes invalid. I reset to page 1 when the filter or sort criteria changes.

## Q6. How does infinite scrolling work?
**Interview answer:** I render the records already loaded and place a sentinel element near the end. When that sentinel becomes visible, I request the next page or cursor and append the results. I track loading and whether more records exist.

## Q7. Why use IntersectionObserver?
**Interview answer:** `IntersectionObserver` asynchronously observes whether an element intersects a viewport or root container. It avoids continuously calculating scroll position in a scroll handler and is a clean way to detect when a sentinel approaches view.

## Q8. How do you prevent duplicate infinite-scroll requests?
**Interview answer:** I maintain a loading guard, check `hasMore`, and ensure only one request for the next cursor is active. I also account for repeated observer callbacks and deduplicate records by ID if the API can overlap pages.

## Q9. How do you handle out-of-order page responses?
**Interview answer:** I cancel obsolete requests with `AbortController` where possible or use a request ID to ignore stale responses. That prevents an older request from overwriting the state associated with the latest page or filter.

## Q10. Pagination vs infinite scrolling: when do you choose each?
**Interview answer:** I choose pagination for tables, admin dashboards, and workflows where users need page numbers or predictable navigation. I choose infinite scrolling for discovery feeds. I also consider accessibility, bookmarking, scroll restoration, and whether a Load More button gives users better control.
