# Week 7 

This repository contains the written deliverables and evidence for a blue-green deployment and rollback assignment for kk-payments.

## What Is In This Repo

- [evidence.md](evidence.md) - run evidence for the deployment, fault injection, automated rollback, timestamps, and post-rollback validation.
- [kk-payments-slo.md](kk-payments-slo.md) - SLI and SLO definitions, rollback thresholds, and excluded conditions.
- [post-incident-review.md](post-incident-review.md) - six-section review of the Monday staging incident.
- [demo-script.md](demo-script.md) - short board-facing script for Nia to read during the live demonstration.
- [switch-cycle.log](switch-cycle.log) - command output for the switch-to-green cycle.
- [evidence-screenshots/](evidence-screenshots/) - screenshots used to support the written evidence.

## What Is Happening

The project documents a controlled deployment flow with three main moments:

1. The system starts in a clean blue state.
2. Traffic is switched to green and validated.
3. A fault is introduced, the monitor detects repeated failures, and traffic is rolled back to blue.

The evidence records the exact timestamps for the fault, the automated rollback trigger, and the rollback confirmation. It also shows that the final health check returns version `v1.3.0` after rollback, confirming that blue is active again.

## Key Outcomes

- Clean starting state is documented.
- Automated rollback completes within the required time window.
- The SLI and SLO document defines measurable targets and justified thresholds.
- The post-incident review explains the Monday incident and the corrective actions.
- The demo script is written for a non-technical audience and stays within the word limit.

