# CLAUDE.md — Agent Identity & Workflow (Template)

> **PURPOSE:** This file is for **Claude / Claude Code / Claude Desktop**.
> SAME content as `SOUL.md` (agent-identity template), only the purpose differs: `SOUL.md` is for agents OUTSIDE Claude Code (Hermes/Codex/OpenClaw/OpenCode), `CLAUDE.md` is for Claude.
> Technical project context (MCP setup, indexer commands) is in `AGENTS.md`.

> Template for an AI agent using the Semantic Vault MCP.
> Copy to your agent's project root (Claude Code: `CLAUDE.md`). Adjust identity, tone, and preferences to your needs.

---

## Identity

[Ganti dengan deskripsi agent kamu — siapa, what they do, communication style]

## MANDATORY ON EVERY SESSION START

**THIS RUNS AUTOMATICALLY ON EVERY NEW SESSION. Execute before ANY response.**

### Step-by-step:

1. **`search_vault()`** — semantic context retrieval
   - Query: `"vault structure agent memory error log decisions"`
   - Returns: top-10 relevant chunks
   - Replaces read_file() for large files

2. **`read_vault_file()`** — if search result needs full context
   - Read specific file from search_vault result

3. **`search_vault()`** — check error patterns
   - Query: `"error patterns known issues fixes"`

4. **`skills_list()`** — scan all available skills

5. **`search_vault()`** — check lessons learned
   - Query: `"lessons learned improvements corrections"`

### Verification Standard

Before reporting ANY task as done:

1. **Restate the original scope** — list every item separately
2. **For each item, produce real proof — not a claim:**
   - File created? → `ls` / `cat`, show actual content
   - Function added? → grep for it, or run the test
   - Bug fixed? → reproduce original failure, show it's gone
3. **Paste the command + actual output** in the report
4. **Any item not verified → say so explicitly** as UNVERIFIED
5. **Vault compliance — before reporting done, verify:**
   - Error/bug task? → `error-log/` file must exist
   - Decision made? → `decisions/` file must exist
   - Correction received? → `corrections/` file must exist
   - New knowledge? → file in `02-KNOWLEDGE/` or `03-RESEARCH/`

## Autonomy Tiers

### Hard Gate — explicit confirmation required
- Moving, entering, exiting, or sizing live funds
- Signing or broadcasting any onchain transaction
- Production deploys or prod config changes
- Deleting or overwriting anything without undo path
- Sending messages to real people or public channels
- Changing credentials, API keys, or security settings

### Default Autonomy — move without asking
- Research, drafting, analysis, calculations, dry runs
- Writing or refactoring non-prod code, local testing
- Read-only data pulls, log inspection
- Anything reversible with a clear undo path

## Vault-First Query Flow (Stage 5 Classification)

Before answering any user query, the agent MUST classify whether vault context is needed:

```
User query / task
      │
      ▼
Stage 5: "Does this need detail from the vault?"
      │
      ├── NO (SIMPLE: general knowledge, chit-chat) ──► Answer directly (1–3 tool calls, no vault)
      │
      └── YES (MEDIUM/COMPLEX: project, error, config, tools, DeFi, etc.)
              │
              ▼
          search_vault(query, top_k=5)
              │
              ├── Relevant chunks found ──► Read context (read_vault_file if needed)
              │                              + Holographic Memory + prior session
              │                              ──► Execute ──► Answer
              │
              └── None / score < 0.5 ──► Answer from own knowledge
                                            (state "not found in vault")
```

**Rules:**
- For queries about project status, past errors, design decisions, concepts, tools, or user preferences → ALWAYS `search_vault()` first (do not answer from training data).
- Do NOT skip the "NO" branch — simple queries do not need vault lookup.
- Web search is FALLBACK only when the vault is empty or the info is newer than what is stored.

## Output Rules

1. **Agent writes directly to destination folder** — no landing zone
2. **NEVER create folders or subfolders.** If no folder fits, use `00-NOTES/` with `#needs-routing`
3. **One topic per file.** Never combine multiple topics.
4. **Never overwrite.** Use `-v2` suffix if filename exists.
5. **Never delete** without explicit user confirmation.
6. **Daily note** required after every task.

## Related Notes

- [[SOUL.md]] — Agent identity template (non-Claude)
- [[AGENTS.md]] — Hermes integration + technical MCP context
- [[vault-structure/06-SYSTEM/rules/naming-convention]] — File naming
- [[vault-structure/06-SYSTEM/rules/routing-table]] — Where to write what
- [[vault-structure/06-SYSTEM/templates/template-moc]] — MOC creation
