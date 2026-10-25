# Assignment 3: Yehia Meets Maya

## Mission 1: Predictions and what I learned

These predictions were recorded during a walkthrough after the initial starter implementation had already been added. They are preserved as my original answers from that discussion; this was not a pre-implementation commit.

### Where each request runs

- **Employee profile:** I predicted the request runs on the server while the page renders.
- **Recent-posts preview:** I first predicted it would run in the browser after a button click. I learned that the brief asks for this request to run in an async Server Component inside its own Suspense boundary.
- **Explore all posts:** I correctly predicted this request starts only after the user clicks “Load posts”; it runs in the browser.

### What remains visible during the server preview

I predicted the dashboard title, profile, and other independent content remain visible while only the preview shows its fallback. A focused Suspense boundary is intended to provide that behavior.

### Does Suspense handle a click-handler fetch?

My prediction was that Suspense automatically tracks any pending fetch. I learned that a request started by a click handler does not automatically activate a Suspense fallback; the client component needs explicit loading state.

### Does fetch throw for HTTP 503?

My prediction was that fetch throws automatically for an HTTP 503 response. I learned that fetch can resolve with a `Response` for an HTTP error, so the code must check `response.ok` and handle unsuccessful responses itself.

## Mission 2: Employee profile

Implemented: the server-rendered profile requests employee 1 from `GET https://jsonplaceholder.typicode.com/users/1` and displays `name`, `email`, and `company.name`. Its own Suspense boundary provides a profile loading state, and request failures show a profile-specific message without replacing other sections.

## Mission 3: Recent-posts preview

Implemented: an async Server Component requests posts for employee 1 from `GET https://jsonplaceholder.typicode.com/posts?userId=1`, displays up to three titles, and is wrapped in a focused Suspense boundary. The request-time work includes the assignment's intentional two-second delay using `connection()` before the delay. The fallback is local to the preview, so the dashboard shell and independently loaded profile can remain visible.

The delay is a learning simulation, not API latency. A direct reload showed the preview fallback while the page title and profile remained visible; this was recorded in `docs/TESTING.md`.

## Mission 4: Explore all posts in the browser

Implemented: the browser section starts idle and makes no request until the user selects **Load posts**. It tracks explicit idle, loading, success, empty, and error states; prevents duplicate clicks while loading; displays post titles and bodies; and offers retry after a failure. This click-handler request does not rely on Suspense for its loading UI. Browser verification observed zero posts requests before the click and one request returning ten posts afterward.

## Mission 5: Failures and demo scenarios

Implemented: `ApiError` records an unsuccessful HTTP status and is used by the shared response-checking path. The client distinguishes that HTTP error from other failures, including network errors. The labelled scenario selector applies to the next Load or Retry action: Success uses the real API, Slow waits two seconds before the real API, HTTP error creates a local 503 `Response`, and Empty creates a local successful JSON `Response` containing an empty array. The simulated responses pass through the same `response.ok` and JSON parsing code as real responses.

## Mission 9: Interview explanations

These explanations were developed in a guided walkthrough and reflect what I understood and verified.

1. **Where does each request run, and how can I verify it?** The profile and recent-post preview requests run on the server in Server Components. The full-post request runs in the browser after I click **Load posts**. I verified the client request in the browser Network panel: no posts request appeared before the click, and one appeared afterward. The server-side request locations are visible in the Server Component code or can be checked in server logs.
2. **Why use server fetching for the profile and preview, and browser fetching for the button?** The profile and preview are initial dashboard content, so fetching them on the server lets them be prepared as part of the page response. The full list is optional and user-triggered, so it makes sense to wait until the user asks for it.
3. **What activates the preview fallback, and why does the click request not use it automatically?** The async preview component suspends while it waits for its server request, and it is inside a focused Suspense boundary, so that boundary shows its fallback. A fetch started in a normal click handler does not suspend React rendering; the client component tracks its loading state explicitly instead.
4. **What if the Suspense boundary wrapped the whole dashboard?** The fallback could replace the whole dashboard while the preview waits, hiding the page shell and other independent content. The focused boundary limits the fallback to the preview.
5. **What do `ApiError` and `super(message)` do?** `ApiError` adds an HTTP status to the normal error message, providing structured context for an unsuccessful HTTP response. `super(message)` initializes the inherited `Error` message. Network failures are not `ApiError`s in this implementation.
6. **What is the difference between HTTP failure, network failure, and empty result?** An HTTP failure has a response with a non-OK status, such as 503. A network failure means the request rejects without a usable HTTP response. An empty result is a successful response whose JSON is an empty array; it is not an error.
7. **What changes if employee ID 1 becomes 2 in all three requests?** The profile and both post sections should show the records for user 2. The server/client fetching patterns and loading/error behavior remain the same.
8. **Which decision did I verify personally?** I checked that there was no client posts request before clicking **Load posts**, then observed one request after clicking it.

## Walkthrough notes

- Server-side work is completed before its rendered result is sent to the browser; a route or Suspense fallback can be shown while server work is pending.
- Browser-side work starts after the client component runs. In this assignment, the full-post request should start only on a button click, and its loading, success, empty, and error states should be represented explicitly.
- A browser request failing should not remove independent server-rendered sections.
- The browser-side section now uses the click-to-load posts explorer with explicit states and selectable request scenarios.

## AI use and verification

An AI-generated walkthrough initially described the local mock-data implementation as if it matched the assignment. I checked the actual assignment brief and learned that the required profile and posts come from JSONPlaceholder, and that the preview needs its own Suspense boundary. I will use the brief as the source of truth and verify behavior in the running app rather than treating a description as test evidence.
