# N4K48 Pay Assistant — Transaction Schema v0.1

This schema defines the synthetic transaction format used by the first MVP. It is intentionally independent from banks and payment processors and must not contain passwords, card numbers, access tokens, or other payment credentials.

## Required fields

| Field | Type | Description | Example |
| --- | --- | --- | --- |
| transaction_id | string | Unique non-sensitive identifier | txn_demo_001 |
| occurred_at | ISO 8601 datetime | Date and time with timezone | 2026-08-01T08:30:00+02:00 |
| description | string | Human-readable transaction description | CloudBox Monthly Plan |
| amount | decimal | Absolute amount with two decimals | 9.99 |
| currency | ISO 4217 string | Three-letter currency code | EUR |
| direction | enum | debit or credit | debit |
| status | enum | pending, completed, failed, or refunded | completed |
| category | enum | Normalized category | software |

## Optional fields

| Field | Type | Description |
| --- | --- | --- |
| merchant | string | Normalized merchant name |
| payment_method | enum | card, bank_transfer, wallet, cash, or other; never credentials |
| fee | decimal | Transaction fee, default 0.00 |
| recurring | boolean | Whether it belongs to a recurring series |
| recurrence_interval | enum/null | weekly, monthly, quarterly, or yearly |
| notes | string/null | User note without sensitive data |
| source | enum | sample, csv_import, or a future regulated integration |

## Validation rules

- transaction_id must be unique within a user account.
- amount must be greater than zero; direction determines incoming or outgoing.
- currency must be a supported ISO 4217 code.
- occurred_at must include an explicit timezone offset.
- recurrence_interval is required when recurring is true and empty otherwise.
- fee cannot be negative.
- Invalid imported rows must be rejected with a clear row-level error.
- Duplicate detection uses transaction_id.

## Privacy and safety boundaries

The MVP stores only the minimum information needed to explain and organize transactions. It must never store full card numbers, CVV codes, banking passwords, authentication tokens, private keys, or unredacted account identifiers. All initial development and demonstrations use synthetic data.

## Example JSON

```json
{
  "transaction_id": "txn_demo_001",
  "occurred_at": "2026-08-01T08:30:00+02:00",
  "description": "CloudBox Monthly Plan",
  "merchant": "CloudBox",
  "amount": 9.99,
  "currency": "EUR",
  "direction": "debit",
  "status": "completed",
  "category": "software",
  "payment_method": "card",
  "fee": 0.00,
  "recurring": true,
  "recurrence_interval": "monthly",
  "notes": null,
  "source": "sample"
}
```

## Definition of done for N4K-5

- [x] Required and optional fields documented
- [x] Validation rules documented
- [x] Privacy boundaries documented
- [x] JSON example included
- [x] Synthetic CSV fixture prepared
