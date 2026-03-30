# Memory Template

Copy this structure to `{dataPath}/memory.md` on first use.

```markdown
# Self-Improving Memory

## Confirmed Preferences
<!-- Patterns confirmed by user, never decay -->

## Active Patterns
<!-- Patterns observed 3+ times, subject to decay -->

## Recent (last 7 days)
<!-- New corrections pending confirmation -->
```

## Initial Directory Structure

Create on first activation:

```bash
mkdir -p {dataPath}/{projects,domains,archive}
touch {dataPath}/{memory.md,index.md,corrections.md,heartbeat-state.md}
```

## Index Template

For `{dataPath}/index.md`:

```markdown
# Memory Index

## HOT
- memory.md: 0 lines

## WARM
- (no namespaces yet)

## COLD
- (no archives yet)

Last compaction: never
```

## Corrections Log Template

For `{dataPath}/corrections.md`:

```markdown
# Corrections Log

<!-- Format:
## YYYY-MM-DD
- [HH:MM] Changed X → Y
  Type: format|technical|communication|project
  Context: where correction happened
  Confirmed: pending (N/3) | yes | no
-->
```

## Heartbeat State Template

For `{dataPath}/heartbeat-state.md`:

```markdown
# Self-Improving Heartbeat State

last_heartbeat_started_at: never
last_reviewed_change_at: never
last_heartbeat_result: never

## Last actions
- none yet
```
