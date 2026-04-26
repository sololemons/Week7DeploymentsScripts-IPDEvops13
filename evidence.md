## Evidence Summary

This evidence shows a complete automated health-check and rollback sequence. The monitor started before the fault, captured the service failure, and triggered rollback without human intervention.

`

## Before Fault was Introduced ACTIVESERVICE = Green

![Pre-fault state](evidence-screenshots/Requirement1.png)

## Fault Injection

![Fault evidence](evidence-screenshots/StoppedTimestamp.png)

## Rollback Triggered after experiencing 3 consecutive health-check failures.

![Rollback evidence](evidence-screenshots/Health-check-full.png)

## Version Confirmation 

![Version Evidence](evidence-screenshots/VersionShowing.png)

## Post-Switch Proof

![Post Switch Proof](evidence-screenshots/Post-switch-proof.png)







## Verified Timeline

| Marker | Timestamp | Event |
| --- | --- | --- |
| T0 | 08:16:52 | Fault introduced (`date -u +%H:%M:%S && sudo systemctl stop kk-api-green.service`) |
| T1 | 08:17:03 | Monitor logged rollback trigger (`[MONITOR FAIL] ROLLBACK TRIGGERED`) |
| T2 | 08:17:05 | Rollback confirmed (`Post-switch confirmation passed: proxy is routing to blue`) |

## Duration

- Total duration from **T0 to T2: 13 seconds**
- Duration **from T1 to T2: 2 seconds**
- Result: the rollback finished well under the **90-second limit**

## What The Log Shows

- The monitor was already running before the fault was introduced.
- There was no human action between fault introduction and rollback confirmation.
- The rollback was triggered automatically after 3 consecutive health-check failures.
- The full sequence is captured in the monitor log, including the failed polls and the successful switch back to blue.

## Key Log Events

- `08:16:43` - monitor started with 60-second confidence window
- `08:16:48` - poll 2 returned HTTP 200
- `08:16:52` - fault introduced (green service stopped)
- `08:16:53` - poll 3 returned HTTP 000 and failure count began
- `08:16:58` - poll 4 returned HTTP 000
- `08:17:03` - poll 5 returned HTTP 000, rollback triggered automatically
- `08:17:03` - rollback process switched traffic back to blue
- `08:17:05` - post-switch confirmation passed and rollback completed successfully

## Final State

- Active environment: blue
- Previous environment: green
- Health check response after rollback: `{"status":"ok","version":"v1.3.0","port":3000}`





