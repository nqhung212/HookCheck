# HookCheck

A webhook inspection tool for developers and QA engineers. Create a temporary inbox, send an event, and inspect the original request alongside PASS/FAIL checks for the HTTP method, headers, and JSON fields.

## Product scope

- Temporary inboxes without account setup
- Original request bodies and metadata, including malformed JSON and binary content
- Configurable checks for HTTP methods, headers, and JSON fields
- Request history with the rules and results recorded at capture time
- Separate capture and management access
- Inbox expiry, deletion, and request/storage limits

## User workflow

1. Create an inbox and save its management link.
2. Configure expectations and send a webhook to the capture endpoint.
3. Inspect the request and individual check results in the browser dashboard.
4. Correct the sender and compare the new event with the earlier failure.

Updating rules affects future requests; earlier receipts retain their original results.

## Technology

`TypeScript` `Node.js` `Fastify` `PostgreSQL` `Nunjucks` `JavaScript` `Docker` `Playwright` `GitHub Actions`

## Engineering highlights

- A bounded raw-stream reader preserves request bytes across seven HTTP methods while management APIs use normal JSON parsing.
- PostgreSQL transactions keep receipt storage, rule results, and quota accounting consistent. Successful capture responses follow commit.
- Separate capabilities control sending events and inspecting or managing inbox data.
- Captured content is displayed as inert text or base64; inbox expiry is checked after acquiring locks.
- Local recovery checks exercise backup restoration and rollback between two application images with retained synthetic data.

## Verification

The recorded 14 September 2026 hardening checkpoint passed 26 automated tests with no skips using real PostgreSQL and Chromium. Build, specification validation, raw HTTP probes, and local container, restore, and rollback checks also passed. The raw-stream investigation covered 140 method, content-type, and framing combinations.

These results describe a local development checkpoint. External transport verification, monitoring, off-host backups, and real user validation remain pending.

## Availability

Local MVP implemented. No public demo yet.

[Portfolio](https://github.com/nqhung212/career-portfolio)