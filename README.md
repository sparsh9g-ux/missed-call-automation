# Spark Automation — Lead Response Workflows

Three n8n workflows that catch a local service business's leads at three different moments — a missed call, a new form submission, a completed job — and respond automatically before the lead goes cold or the review request gets forgotten.

Built for the pressure-washing case study on [Spark Automation](https://spark-automation-site.vercel.app), and live-tested against real phone numbers and real form submissions, not mocks.

![Workflow diagram](./workflow-diagram.png)
*(diagram shown is the original missed-call-text-back workflow)*

## The three workflows

**`missed-call-text-back.json`** — A missed call triggers a webhook, waits 30 seconds for a real callback window, dedupes repeat callers, looks up or creates the GoHighLevel contact, sends the right message to a new lead vs. a returning customer, creates/updates the pipeline opportunity, and logs failures for manual follow-up instead of failing silently. 24 nodes end to end.

**`instant-lead-response.json`** — Fires the moment a lead submits a quote form on the client's actual site (posted directly via a webhook call from the site itself, deliberately skipping GHL's own form product to avoid an unremovable bot-check and an unnecessary extra hop). Same dedupe/lookup/branch pattern as the missed-call workflow, but with no wait — the whole point is instant.

**`review-request.json`** — Fires when an opportunity moves to "won" in the pipeline. Waits 7 days, then texts a Google review link, with a 30-day dedup window so a contact can't get double-asked.

## How this was built

Built through a genuine back-and-forth with Claude — ideas for direction, tone, and what to lead with were proposed and then tested and validated against what would actually land with a real business owner, not accepted on a first pass. The live deploy was checked before it went anywhere near an actual sales call.

The missed-call workflow went through two real failures before being called done: a wrong field path silently rejected every real call, and later a dead webhook tunnel did the same. Both were only caught by tracing a live phone call through n8n's execution history and GHL's execution logs — not by trusting that "no visible errors" meant it worked. The other two workflows inherited and fixed the same class of bug (GHL's contact search API doesn't reliably filter server-side) before it ever reached a real lead, because it had already been found once.

## About the export

Account-specific identifiers (GHL location ID, pipeline ID, stage ID, credential reference, and stored phone numbers) have been replaced with placeholders across all three JSON files. The real workflows run on a private n8n instance against a live GoHighLevel account — these files are for showing the logic and structure, not for re-importing and running as-is.

To run it for real, you'd need your own GHL account, your own credential set up in n8n, and your own pipeline/stage IDs swapped back in.

## Contributors

- Sparsh Gupta
- Yoda — Sparsh's Claude-based personal agent
