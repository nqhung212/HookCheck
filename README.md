# HookCheck

A webhook inspection tool for developers and QA engineers. Create a temporary inbox, send an event, and inspect the original request alongside PASS/FAIL checks for the HTTP method, headers, and JSON fields.

**Status (23 September 2026):** The product is in private QA. There is no public hosted demo yet. This portfolio repository presents the interface and verified engineering work; the application source is maintained separately.

## Interface

Screenshots from a local session using synthetic events. Access tokens are hidden.

### Home

![HookCheck home page](assets/home.png)

### Validation rules

![Rule editor requiring the JSON field /order/id](assets/expectations.png)

### Request inspection

| Missing required field | Valid request |
| --- | --- |
| ![Failed check for a missing order ID](assets/request-fail.png) | ![Passing check with both requests preserved in history](assets/request-pass.png) |

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

`TypeScript` `Node.js` `Fastify` `PostgreSQL` `Nunjucks` `JavaScript` `Docker` `Playwright` `GitHub Actions` `Google Cloud` `Kubernetes` `Terraform`

## Engineering highlights

- A bounded raw-stream reader preserves request bytes across seven HTTP methods while management APIs use normal JSON parsing.
- PostgreSQL transactions keep receipt storage, rule results, and quota accounting consistent. Successful capture responses follow commit.
- Separate capabilities control sending events and inspecting or managing inbox data.
- Captured content is displayed as inert text or base64; inbox expiry is checked after acquiring locks.
- Local recovery checks exercise backup restoration and rollback between two application images with retained synthetic data.
- An isolated cloud QA environment has exercised the API, browser journeys, exact-byte capture across seven methods, and persistence after VM restart.

## Verification

The latest recorded full local gate passed **71/71 automated checks with no skips** using real PostgreSQL and Chromium. Specification validation, typecheck, build, local container, restore, and rollback checks passed at that checkpoint. The raw-stream investigation covered 140 method, content-type, and framing combinations.

The private cloud QA run also passed seven-method exact-byte transport checks, API acceptance, and three browser journeys. A later browser refresh was rate-limited after repeated QA runs; it was not counted as a fresh pass. These are engineering checks with synthetic data, not evidence of a public release or user adoption. Public availability and real user validation remain pending.

