# Setup — Self-Improving Agent

## First-Time Setup

**Important:** Before running setup, the skill first checks for `{baseDir}/../self-improving/.self-improving.json`. If it exists and `dataPath` is valid, setup is skipped.

### 1. Check Existing Configuration

```bash
# Try to read config file
cat {baseDir}/../self-improving/.self-improving.json
```

If the file exists and contains a valid `dataPath` pointing to an existing directory with core files (memory.md, corrections.md, etc.), skip to step 6.

### 2. Determine Data Path

Ask the user where to store self-improving data:

```
Self-Improving 需要一个数据存储位置。

默认路径：{workspace}/self-improving/
（即：{baseDir}/../self-improving/）

您可以：
1. 直接回复"好"或"确认"使用默认路径
2. 或指定其他路径

如果您有多个 agent，建议每个 agent 使用各自的默认路径。
```

### 3. Confirm Path

If user confirms default: `dataPath = {baseDir}/../self-improving/`

If user specifies custom path: `dataPath = {user-specified-path}`

### 4. Create Config File

```bash
# Create directory for config
mkdir -p {baseDir}/../self-improving/

# Write config
echo '{ "dataPath": "{dataPath}", "version": "1.0", "initializedAt": "{timestamp}" }' > {baseDir}/../self-improving/.self-improving.json
```

### 5. Create Data Directory Structure

```bash
mkdir -p {dataPath}/{projects,domains,archive}
touch {dataPath}/memory.md
touch {dataPath}/index.md
touch {dataPath}/corrections.md
touch {dataPath}/heartbeat-state.md
```

Initialize core files:

**memory.md:**
```markdown
# Memory (HOT Tier)

## Preferences

## Patterns

## Rules
```

**corrections.md:**
```markdown
# Corrections Log

| Date | What I Got Wrong | Correct Answer | Status |
|------|-----------------|----------------|--------|
```

**index.md:**
```markdown
# Memory Index

| File | Lines | Last Updated |
|------|-------|--------------|
| memory.md | 0 | — |
| corrections.md | 0 | — |
```

**heartbeat-state.md:**
```markdown
# Self-Improving Heartbeat State

last_heartbeat_started_at: never
last_reviewed_change_at: never
last_heartbeat_result: never

## Last actions
- none yet
```

### 6. Choose Operating Mode

Add to your AGENTS.md or workspace config:

```markdown
## Self-Improving Mode

Current mode: Passive

Available modes:
- Passive: Only learn from explicit corrections
- Active: Suggest patterns after 3x repetition
- Strict: Require confirmation for every entry
```

### 7. Add SOUL.md Steering

Add this section to your `SOUL.md`:

```markdown
**Self-Improving**
Compounding execution quality is part of the job.
Before non-trivial work, load `{dataPath}/memory.md` and only the smallest relevant domain or project files.
After corrections, failed attempts, or reusable lessons, write one concise entry to the correct self-improving file immediately.
Prefer learned rules when relevant, but keep self-inferred rules revisable.
Do not skip retrieval just because the task feels familiar.
```

### 8. Add HEARTBEAT.md Steering

Add this section to your `HEARTBEAT.md`:

```markdown
## Self-Improving Check

- Read `./skills/self-improving/heartbeat-rules.md`
- Use `{dataPath}/heartbeat-state.md` for last-run markers and action notes
- If no file inside `{dataPath}/` changed since the last reviewed change, return `HEARTBEAT_OK`
```

---

## Verification

Run "memory stats" to confirm setup:

```
📊 Self-Improving Memory

🔥 HOT (always loaded):
   memory.md: 0 entries

🌡️ WARM (load on demand):
   projects/: 0 files
   domains/: 0 files

❄️ COLD (archived):
   archive/: 0 files

⚙️ Mode: Passive
```

## Migration from Old Path

If migrating from the old hardcoded `~/self-improving/` path:

1. Old data is typically at `~/self-improving/` or `~/.self-improving/`
2. Copy the entire directory to the new `{dataPath}/`
3. Update `{baseDir}/../self-improving/.self-improving.json` with the new path
4. Verify all files are present in the new location
