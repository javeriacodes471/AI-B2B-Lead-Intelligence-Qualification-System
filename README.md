# AI B2B Lead Intelligence & Qualification System

**Category:** Lead Enrichment & Sales Automation
**Stack:** n8n, HTTP Request (Company Enrichment API), Code (JS), Twenty CRM API, Slack

## The Problem

B2B sales teams waste hours manually researching every inbound lead — checking company size, budget fit, and project scope — before deciding whether it's worth a follow-up. By the time research is done, the lead has gone cold, and low-quality leads still eat up rep time because there's no consistent scoring logic.

## The Solution

An end-to-end webhook-triggered pipeline that captures a lead the moment a form is submitted, enriches it with real company data, scores it against fit/budget/project criteria, and routes only qualified leads into the CRM with an instant Slack alert to the sales team — unqualified leads are logged and discarded automatically.

## Workflow Steps

- **Webhook** receives the raw lead payload from the website form
- **Normalize Lead** — Code node reshapes and cleans incoming fields (dot-notation mapping, trims/defaults missing values)
- **Company Enrichment** — HTTP Request call to a company data API using the lead's domain/email
- **Merge Enriched Lead** — combines original form data with enrichment response into one flat object
- **Qualify & Score Lead** — Code node applies scoring rules (company fit, project size, budget threshold) and outputs a numeric score + qualified boolean
- **IF Qualified** — branches the flow:
  - **Qualified path:** search Twenty CRM by email → if found, update record; if not, create new CRM person → format sales alert → notify team on Slack
  - **Not qualified path:** routed to a No-Op node (logged, no further action — keeps CRM clean)

## Why This Design

- Enrichment happens **before** scoring, so the qualification logic works on real company data, not just form input
- CRM existence check prevents duplicate contact records
- Scoring logic lives in a single Code node — easy to tune thresholds without touching the rest of the graph

## Potential Extensions

- Add retry/error-branch on the enrichment API call (rate limits / timeouts)
- Log qualification scores to a sheet for later model tuning
