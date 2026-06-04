````md
# Vision Moderation Rollback Runbook

## When to Roll Back

Rollback immediately if ANY of the following persist for 5 minutes:

- 5xx error rate > 1%
- P99 latency > 1s
- Canary success rate below baseline by 2%
- Drift alert firing after deployment
- ModelVersionMismatch alert firing
- Smoke tests failing

---

## How to Roll Back

### GitHub Actions

1. Open Actions → `deploy-model`
2. Select last successful production deployment
3. Run:

```bash
./scripts/rollback.sh production
````

Or redeploy previous SHA:

```bash
./scripts/deploy.sh production ghcr.io/<repo>:<previous-sha>
```

---

## What to Verify

All must return GREEN:

* Availability SLO dashboard
* P99 latency dashboard
* Error-rate dashboard
* Canary verification checks
* PagerDuty alerts resolved
* Correct `model_version` header returned

Wait 15 minutes after rollback before closing incident.

---

## Who to Notify

* PagerDuty incident channel
* #ml-platform Slack
* On-call SRE
* ML service owner
* Product support lead if customer impact occurred

---

## What NOT to Do

* Do NOT roll forward before root cause is identified
* Do NOT deploy unrelated fixes during incident
* Do NOT ignore drift or version mismatch alerts
* Do NOT close incident before metrics stabilize

---

## When to Roll Forward

Only redeploy when:

* Root cause documented
* Fix reviewed and merged
* Staging smoke tests pass
* Canary metrics stable for 30 minutes
* Incident commander approves rollout

```
```
