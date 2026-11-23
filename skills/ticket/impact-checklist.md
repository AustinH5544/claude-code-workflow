# Impact checklist

For each item, say whether it applies. If it does, say what you checked.

- **Callers and contracts:** every consumer of a changed function, type, prop, route, API response, webhook, public API or event. Does it still work for clients that are already running: old browser tabs, API integrations, queued jobs, cached responses?
- **Data:** schema changes; migrations (can they be undone? are they safe on large tables? do they take locks?); existing rows that break the new assumptions; nulls; cascades and deletes.
- **Auth and tenancy:** who can call this? Is permission checked on the server against the database, and scoped to the right tenant (organization, school, workspace or account)? Could the UI offer an action the server will reject?
- **Security:** input validation, injection, XSS, SSRF, open redirects, secrets or personal data in code or logs, rate limiting.
- **Deployment modes and config:** different deployment targets (e.g. cloud vs self-hosted, web vs mobile), feature flags, environment variables (what happens when one is missing?).
- **Time and locale:** time zones, daylight saving, all-day dates, translated strings, plurals, long translations.
- **Concurrency and state:** race conditions, double submits, retries and idempotency, caching and cache invalidation, stale state in the browser.
- **Performance:** N+1 queries, missing indexes, queries with no limit, bundle size, anything that blocks server rendering.
- **Failure modes:** what happens when the database, a third-party service or the network fails? Are errors handled and shown, not swallowed?
- **UX and accessibility:** loading, empty and error states; keyboard and screen reader use; mobile.
- **Observability:** if this breaks in production, will we find out (logs, error tracking, analytics)?
- **Dependencies:** any new package or version bump needs my explicit approval.
- **Tests and docs:** tests, docs, translations and changelog updated wherever the repo expects them.
