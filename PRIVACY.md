# Privacy

**Last updated: 2026-07-16 EDT**

FB Leads Scraper is an independent, user-controlled Chrome extension. This
policy describes the current release candidate in this repository; it does not
represent approval by Meta, Facebook, or the Chrome Web Store.

## Data Handled

- **Personally identifiable information:** The parsed lead name and phone number
  are handled to preview a lead, prevent duplicate sends, and deliver a live send
  to the user's selected webhook.
- **Website content:** Visible Facebook page content and manually pasted website
  text are processed temporarily in extension memory while the extension parses
  a lead. The resulting lead object includes the raw text while the content
  script, popup, and service worker process that user-started action.
- **Authentication information:** The user-configured webhook URL may contain a
  secret token in its path or query. The full URL is saved in local settings and
  used to address the webhook request. Sanitized diagnostics retain only its
  origin.
- **Local configuration metadata:** The webhook URL, destination name, source
  label, dry-run state, readable-history preference, webhook verification
  timestamp, run-delay/lead-age settings, and explicit data-handling consent
  metadata support the configured workflow. Consent is stored as
  `dataHandlingConsentVersion` and `dataHandlingConsentedAt`; it does not contain
  lead data.
- **Local duplicate metadata:** SHA-256 phone hashes prevent repeat sends.
  Normalized readable names are stored only when the user opts in; this is off by
  default. Turning the option off stops adding names but does not erase names
  already stored.
- **Local activity metadata:** A schema version, event ID, event type, timestamp,
  generic label, and fixed status message provide local workflow status and
  troubleshooting history. Activity entries exclude lead names, phone numbers,
  webhook credentials, raw text, and webhook response bodies.

## Data Sent To The Webhook

A live-send payload contains exactly `name`, `phone`, `source`, `destination`, and
`capturedAt`: the parsed name and phone number, configured source label,
configured destination name, and capture timestamp. Raw Facebook page text and
manually pasted text are excluded from the payload. A user-started webhook test
sends synthetic test values instead of a real lead.

Parsed lead data is transferred only to the user-configured HTTPS webhook after
the user starts the visible scan/send workflow or manually sends a lead, and
Chrome runtime permission exists for that webhook origin. Current explicit
data-handling consent is also required before personal lead data is parsed,
previewed, scanned, or sent. Dry run does not transfer parsed lead data. It
records only a privacy-safe local activity result.

The webhook destination is a separate processor with its own access, retention,
deletion, and use practices. Clearing local extension data cannot delete copies
already received or retained by that destination.

## Local Storage And Retention

The following data is stored in `chrome.storage.local`:

- Saved settings, including the full configured webhook URL and the current
  consent version and timestamp.
- Duplicate history, capped at 5,000 phone hashes and 5,000 optional readable
  names, with the oldest entries pruned first.
- Activity history, capped at 500 activity entries, with the oldest entries
  pruned first. Legacy entries are minimized to the current privacy-safe schema
  when read or when a new event is appended; malformed entries are discarded.

No time-based retention or expiry is implemented for these local records. The
options page provides separate clear controls for duplicate history and activity
history. Those history controls do not clear saved settings. Disabling readable
history also does not remove names already stored; use the duplicate-history
clear control to remove them.

Raw page/manual text is handled transiently during parsing and the send workflow,
but the storage layer does not save it. Duplicate and activity metadata remain
local-only. Of the configured settings, only the source and destination labels
are copied into the live/test webhook payload; the webhook URL is the request
address, not a payload field.

## Consent And User-Initiated Processing

Before the extension parses, previews, scans, or sends personal lead data, the
user must accept the explicit data-handling consent on the Options page. The
consent describes the personal data read from the Facebook Lead Center page and
the transfer to the user-configured webhook. The popup, Facebook panel, and
background service worker fail closed when the stored consent version or
timestamp is absent or invalid.

Unchecking the consent control and saving revokes consent. Revocation disables
manual parsing and sending, pauses the Facebook scanner, clears transient lead
preview and scan queues from extension memory, and blocks background delivery.
Granting exact-origin webhook access and sending the synthetic webhook test
remain available because those actions do not read or send personal lead data.

## Limited Use And Prohibited Uses

The current repository uses handled data only to run the lead preview/send
workflow, prevent duplicates, and provide local status and troubleshooting.

No handled data is sold by the project developer, and the extension has no
built-in transfer to advertisers or data brokers. The extension does not use it
for advertising profiles, credit or lending decisions, model training, or
unrelated purposes. The current repository includes no developer-operated
backend, no path for the project developer or staff to access user data, no
analytics or telemetry, and no remote code loading.

These limits describe the extension code in this repository. They do not make a
user-selected webhook destination subject to this policy; users must review that
separate processor's terms and data practices.

## User Control And Responsibility

FB Leads Scraper is not affiliated with Meta, Facebook, any CRM, or any webhook
provider. Users are responsible for obtaining any required authorization and for
complying with Facebook/Meta terms, destination policies, privacy obligations,
and applicable laws before parsing, sending, or storing lead data. Configure only
webhook destinations you own or trust, and treat webhook URLs as credentials.

The exact manifest permission justifications and field-by-field Chrome Web Store
evidence are maintained in
[`store/privacy-permissions.md`](store/privacy-permissions.md).
