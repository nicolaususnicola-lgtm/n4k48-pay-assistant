# N4K48 Pay Assistant

**N4K48 Pay Assistant** is an independent, experimental browser-based tool by N4K48 for inspecting transaction data from a CSV file. Its current prototype focuses on importing, validating, and summarizing transaction records. It is separate from MyZubster; MyZubster does not own or define this project.

> **Current scope:** this is a transaction-information prototype, not a bank, payment processor, accounting system, or financial adviser. It does not initiate, hold, or transfer money. Use synthetic or otherwise safe test data.

## How it works today

1. **Open the web page.** The prototype is a static HTML page served by Nginx. The current implementation processes the selected CSV in the browser; it does not upload the file to an application backend.
2. **Select a CSV file.** Use the included `sample-transactions.csv` to try the prototype.
3. **Parse and validate rows.** The importer reads the header and checks required fields, duplicate transaction IDs, positive numeric amounts, three-letter uppercase currency codes, supported directions and statuses, valid timestamps, non-negative fees, and consistency between the `recurring` flag and `recurrence_interval`.
4. **Review accepted rows and errors.** Accepted transactions appear in a table. Invalid rows are reported with row numbers and validation reasons.
5. **Review the summary.** Completed debits and credits are totaled separately and grouped by currency. Pending, failed, and refunded amounts are displayed separately. The recurring indicator counts rows whose CSV field is `recurring=true`; it does not discover recurring payments automatically.

## Transaction statuses and totals

The CSV schema includes `completed`, `pending`, `failed`, and `refunded` statuses. In the current summary logic:

- **Completed debits:** sum of rows with `status=completed` and `direction=debit`.
- **Completed credits:** sum of rows with `status=completed` and `direction=credit`.
- **Pending, failed, refunded:** each status is summarized separately and grouped by currency.
- **Currency:** totals are grouped by the row's `currency` value; different currencies are not added together.
- **Recurring:** a count of rows marked as recurring in the source CSV.

These are simple summaries of imported records, not a bank balance or reconciled ledger. A refunded row is displayed separately; the prototype does not match it to an original transaction or calculate net refund-adjusted totals. Fees are validated for negative values but are not included in the summary totals. Review statuses and source data before relying on any figures.

## Try the sample data

The included `sample-transactions.csv` contains 10 synthetic rows. The current validation and summary functions were run in an isolated Node.js 20 container against this fixture, with these results:

| Check | Expected result |
| --- | ---: |
| Rows accepted | 10 |
| Validation errors | 0 |
| Completed debits | EUR 155.58 |
| Completed credits | EUR 850.00 |
| Pending | EUR 79.00 |
| Failed | EUR 64.30 |
| Refunded | EUR 18.50 |
| Rows marked recurring | 4 |

These figures describe only the included sample fixture.

## Run with Docker

The repository includes `compose.yaml` and the static web files. The deployed VPS instance was previously confirmed to serve the page with HTTP 200 from its local port, using the `nginx:stable-alpine` container. The VPS port was bound to `127.0.0.1:8080`, so that binding alone does not make it publicly accessible.

The transaction-summary fix and this documentation were merged into `main` via [Pull Request #1](https://github.com/nicolaususnicola-lgtm/n4k48-pay-assistant/pull/1) (squash commit `071946f`). GitHub source and VPS deployment are separate: the VPS checkout/container version has not been reverified after the merge. The Docker setup previously used a bind mount, so edits to files in the mounted VPS directory can affect the served page without a container rebuild.

For a local instance, from the repository directory:

```bash
docker compose up -d
```

Check the service and published port with:

```bash
docker compose ps
```

Follow the local `compose.yaml` for the actual port mapping and deployment configuration.

## Public demo and GitHub Pages

A GitHub Actions workflow for publishing the static prototype is available at [`.github/workflows/pages.yml`](.github/workflows/pages.yml) (added in commit `22e4ae1`). In the repository **Settings → Pages**, select **GitHub Actions** as the build and deployment source. Check the [Actions runs](https://github.com/nicolaususnicola-lgtm/n4k48-pay-assistant/actions) for a successful `Deploy static demo to GitHub Pages` job and confirm the URL shown by GitHub before sharing it publicly.

Expected URL after a successful deployment: https://nicolaususnicola-lgtm.github.io/n4k48-pay-assistant/ — **publication and reachability have not yet been independently verified**.

Use the included synthetic CSV to demonstrate the prototype. Do not upload credentials or sensitive banking data. The static demo performs CSV processing in the browser and is not a live payment service.

## Who it is for

- **Freelancers and sole traders:** review imported transactions and distinguish completed, pending, and failed records.
- **Small businesses:** inspect exported CSV data as an informational overview, not as accounting or reconciliation software.
- **Individuals:** explore spending and CSV-marked recurring transactions with synthetic or non-sensitive test data.
- **Developers and early testers:** evaluate the interface, report parsing problems, and propose improvements.

These audiences are hypotheses for user testing, not evidence of established product-market fit.

## Current limitations and next steps

- The prototype is a static browser-side CSV tool; no integrated AI assistant or payment execution has been verified in the current code.
- It does not automatically detect recurring payments; it reads the CSV's recurring flag.
- Refunds are not reconciled against original transactions.
- Fees are not included in debit/credit totals.
- Totals summarize imported records and are not a verified account balance.
- Browser-based functional testing and visual testing remain separate from the CSV logic test.
- Before using real financial data, strengthen CSV parsing and validation, add explicit treatment of refunds and fees, and conduct a security/privacy review.

## Safety principles

- Do not upload banking passwords, card numbers, CVVs, access tokens, or other authentication secrets.
- Prefer synthetic data while developing and testing.
- Keep user decisions in control; this prototype does not execute payments.
- Distinguish transaction information from financial advice.

## Roadmap

1. Improve transaction-status, refund, fee, and multi-currency handling.
2. Add robust automated tests for CSV edge cases and summary calculations.
3. Validate the user experience with synthetic data.
4. Consider an AI explanation workflow only after defining privacy boundaries and testing it safely.
5. Evaluate regulated financial-data or payment-provider integrations only with appropriate security, legal review, and authorized partners.

## Project ownership

N4K48 Pay Assistant is an independent N4K48 project, with AI-assisted development support from Zorgax. It is separate from the collaboration on MyZubster.

## Disclaimer

This repository describes experimental software. It does not provide banking, payment processing, investment, legal, accounting, or financial-advisory services.
