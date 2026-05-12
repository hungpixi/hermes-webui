---
name: dokploy-domain-dns
description: Router for Dokploy domain workflows. Use when user needs DNS/domain setup or DNS/domain troubleshooting and route to the correct specialized DNS skill.
---

# Dokploy Domain DNS Router

## Use When
- Request involves DNS, domain mapping, SSL, or public route checks for Dokploy app.

## Do Not Use When
- Primary issue is app build/runtime with no DNS symptoms.

## Routing Table
| Condition | Lane |
|---|---|
| First-time domain/subdomain setup | Setup lane below; formerly `dokploy-dns-setup` |
| Existing domain suddenly failing | Troubleshoot lane below; formerly `dokploy-dns-troubleshoot` |
| NS/A record/Dokploy mapping clean but `404/502/timeout` persists | Escalate to `dokploy-deploy-troubleshooter` |

## Inputs
- Domain/subdomain state (new vs existing).
- Target domain/subdomain and Dokploy server IP.
- App identifier and internal app port.
- Expected target app/IP/port.
- Current Dokploy deployment state.
- Symptom list (`ENOTFOUND`, 404/502, TLS errors, HTTPS errors, etc.).
- Access to DNS provider and Dokploy API key.

## Workflow
1. Classify as first-time setup or existing-domain incident.
2. Run exactly one lane: setup or troubleshoot.
3. Validate public DNS, Dokploy domain attachment, HTTPS endpoint, and runtime escalation status.
4. Return route decision, evidence, fixes, and post-check status.

## Setup Lane
Use when new subdomain/domain must be connected to Dokploy app.

1. Verify authoritative nameserver delegation.
2. Create DNS-only A record to Dokploy IP.
3. Attach domain in Dokploy and redeploy app.
4. Validate HTTPS endpoint, preferably `/healthz`.

Setup output:
- DNS record and NS state summary.
- Domain attachment status in Dokploy.
- HTTPS check result.

Setup failure modes:
- Nameserver points to wrong provider.
- DNS propagation incomplete.
- Domain attached but wrong target port.

Setup quality gate:
- Public DNS resolves to expected IP.
- Domain attached and deploy complete.
- HTTPS health endpoint returns expected status.

## Troubleshoot Lane
Use when domain used to work or was already configured but now fails.

1. Classify failure layer: DNS delegation, DNS record, Dokploy mapping, runtime port.
2. Verify NS, A-record, and propagation.
3. Verify domain attachment and app port mapping.
4. Re-test HTTPS endpoint and document outcome.
5. Escalate to app runtime repair if DNS is clean but service still fails.

Short-circuit rule:
- If NS, A-record, and Dokploy domain attachment are all confirmed correct, and endpoint still fails (`404/502/timeout`), immediately escalate to `dokploy-deploy-troubleshooter` without repeating DNS setup steps.

Troubleshoot output:
- Root-cause layer and evidence.
- Fixes applied and retest status.
- Escalation direction if unresolved.

Troubleshoot failure modes:
- Cached resolver misleading diagnosis.
- Multiple simultaneous faults (DNS + runtime).
- Incomplete visibility into provider/Dokploy config.

Troubleshoot quality gate:
- Root cause identified with reproducible checks.
- Post-fix validation confirms expected endpoint behavior.

## Outputs
- Selected DNS lane and reason.
- DNS result summary with evidence.
- Domain attachment status.
- HTTPS retest status.
- Escalation path if unresolved.

## Failure Modes
- Misclassified setup vs incident.
- Mixed DNS + runtime failures requiring multi-skill escalation.

## Quality Gate
- Exactly one DNS lane selected.
- Output includes route decision and post-check status.
