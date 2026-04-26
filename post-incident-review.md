# Post-Incident Review: Monday Staging Incident

## Section 1: Incident Summary
During Nia's Monday investor demonstration, the staging payment service was unavailable for 48 seconds. The release process was started with the wrong destination, which interrupted the live demonstration flow. Service was restored quickly after the team identified the issue and returned traffic to the previous healthy version.

## Section 2: Timeline
**Reconstructed from purely the template's content from Thursday's Page 4 work  but from the instructions we have been given we have been told to use also the *logs from  Week 5 Mondays Incident* but in week 5 there was no such scenario so i take that as a mistake**  

- **09:15:40** - Investor demonstration is in progress on staging.
- **09:16:05** - Release command is launched with the wrong destination value.
- **09:16:12** - New process restarts in the wrong place; staging begins returning unavailable responses.
- **09:16:18** - Nia alerts the operations lead that the board-facing screen is failing.
- **09:16:28** - Operations lead confirms the destination mismatch from command history.
- **09:16:41** - Team starts rollback to the prior known-good staging state.
- **09:17:00** - Staging returns successful responses and remains stable.
- **Impact duration:** **48 seconds** of user-visible staging unavailability (09:16:12 to 09:17:00).

## Section 3: Root Cause
The release pipeline accepted a manually entered destination at runtime and did not enforce a branch-to-environment lock. Because no policy check validated destination against release context before execution, a valid release action could run against the wrong environment.

## Section 4: Contributing Factors
1. **Live-demo pressure on manual execution:** The command was entered during an active investor session, increasing the chance of selecting the wrong destination.
2. **Shared release path across environments:** The same release entry point could target multiple environments without a hard safety boundary.
3. **Missing preflight policy gate:** There was no mandatory stop that compared branch, destination, and approval state before runtime.

## Section 5: What Went Well
Escalation and coordination were fast. Nia reported the problem immediately, command history made diagnosis quick, and the team restored service within 48 seconds by returning to a known-good state.

## Section 6: Action Items

1. **Owner role:** DevOps Engineer  
    **Description:** Remove manual destination arguments from release commands; derive destination only from branch mapping in pipeline configuration.  
    **Target completion timeframe:** Within 3 days.

2. **Owner role:** Platform Engineer  
    **Description:** Enforce environment-scoped credentials and runner permissions so staging credentials cannot execute production releases and production credentials cannot execute staging releases.  
    **Target completion timeframe:** Within 5  days.

3. **Owner role:** Site Reliability Engineer  
    **Description:** Add a mandatory preflight policy check that blocks execution when branch, destination, release tag, and approval state do not match policy.  
    **Target completion timeframe:** Within 1 week.

4. **Owner role:** Release Manager  
    **Description:** Introduce a protected approval step for non-staging releases requiring one independent reviewer before release execution.  
    **Target completion timeframe:** By end of next sprint.