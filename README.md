# HookCheck

A webhook debugging workspace for developers and QA engineers. Create an inbox, send a webhook, and see exactly what arrived and why it passed or failed your expectations.

**Deployment:** The full application runs in a restricted Google Cloud QA environment. Public access is not available yet; the screenshots below show the working product with synthetic data. This repository is the public showcase; the application source is maintained privately.

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

## What you can do

- Temporary inboxes without account setup
- Original request bodies and metadata, including malformed JSON and binary content
- Configurable checks for HTTP methods, headers, and JSON fields
- Request history with the rules and results recorded at capture time
- Separate capture and management access
- Inbox expiry, deletion, and request/storage limits

## A typical debugging session

1. Create an inbox and save its management link.
2. Configure expectations and send a webhook to the capture endpoint.
3. Inspect the request and individual check results in the browser dashboard.
4. Correct the sender and compare the new event with the earlier failure.

Updating rules affects future requests; earlier receipts retain their original results.

## Built beyond the prototype

- **Faithful capture:** Preserves original bytes across seven HTTP methods, including malformed JSON and binary payloads. Each receipt keeps the rules and results from when it arrived, so later edits cannot rewrite history.
- **Private by design:** The endpoint that receives webhooks cannot inspect them. A separate secret link controls access; captured content is displayed as inert text or base64.
- **Consistent under load:** PostgreSQL transactions keep captured requests, rule results, and quotas together. The service acknowledges a capture only after it is committed.
- **Deployed and exercised:** A containerized application, database, HTTPS ingress, and automated release checks run in Google Cloud QA. An isolated Kubernetes lab has also passed real HTTP and browser journeys, including persistence after a VM restart.
- **Recovery tested:** Automated checks cover rollback to an earlier application image and encrypted database restore with retained synthetic requests.

**Stack:** TypeScript, Node.js, Fastify, PostgreSQL, Nunjucks, Docker, Playwright, GitHub Actions, Google Cloud, Kubernetes, Terraform.

