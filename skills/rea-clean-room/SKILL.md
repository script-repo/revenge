---
name: rea-clean-room
description: >-
  Clean-room reverse-engineering workflow using REA
  as an MCP bridge to JADX, Kuna, Ghidra, Frida, and radare2, orchestrated via
  Codex CLI. Use when mapping how a permitted app, binary, or feature works for
  interoperability, understanding, or defensive analysis — never for exploits,
  DRM cracks, cheats, or piracy. Placeholders must be filled for the host.
---

# rea-clean-room

Reusable skill for evidence-backed, clean-room RE analysis. Fill host
placeholders before use. No personal names, hostnames, or absolute home paths
belong in this file.

## Placeholders

| Placeholder | Meaning |
|---|---|
| `{{REA_HOME}}` | REA install root |
| `{{REA_BIN}}` | `rea` executable |
| `{{GHIDRA_HOME}}` | Ghidra install (symlink `current` OK) |
| `{{JADX_HOME}}` | JADX install |
| `{{KUNA_HOME}}` | Kuna install |
| `{{R2_BIN}}` | radare2 / `r2` binary |
| `{{FRIDA_VENV}}` | Frida Python venv with CLI tools |
| `{{GHIDRA_PROJECTS}}` | Directory for Ghidra project files |
| `{{GHIDRA_MCP_URL}}` | GhidraMCP base URL (only live when CodeBrowser is open) |
| `{{CODEX_BIN}}` | Codex CLI binary |
| `{{OUT_ROOT}}` | Writable output root for session artifacts and reports |

Typical wiring: Codex MCP server name `rea`, with env such as
`REA_JADX_MCP_JAR` and `REA_KUNA_COMMAND` pointing into `{{JADX_HOME}}` /
`{{KUNA_HOME}}` providers.

## When to use

- User wants to understand how a **permitted** feature in an app, website
  artifact, program file, or binary works.
- Goal is clean-room reproduction notes, interoperability, compatibility, or
  defensive analysis — not bypassing protections.
- Host has (or will have) REA + Codex, and optionally JADX, Kuna, Ghidra,
  radare2, Frida.

Do **not** use this skill to produce exploits, exploit PoCs, malware, attack
procedures, DRM cracks, access-control bypasses, cheats, or piracy aids — for
any system, including labs framed as "authorized" or fiction.

## Guardrails (non-negotiable)

1. Clean-room / interoperability / understanding / defensive analysis only.
2. Never emit exploit recipes, payloads, crack steps, or cheat enables.
3. Never post secrets, tokens, keys, or private URLs/IPs in chat or public repos.
4. Receipt every claim: **what** was observed, **where** (file/offset/API/log),
   **how** (tool + command class). Separate **Observation** / **Hypothesis** /
   **Unknown**.
5. User-level tooling only on shared research hosts; do not escalate privileges
   or touch cluster/infra control planes. Route install/infra to the ops desk.
6. Frida (and any dynamic instrumentation) only on targets the user owns or has
   **explicit written permission** for — ask before attaching.
7. GhidraMCP is available only while Ghidra/CodeBrowser is open and listening;
   detect downtime and say so rather than inventing results.
8. Prefer Codex + REA MCP for deep multi-tool sessions; prefer one-shot Shell
   with `r2` / `jadx` / Ghidra headless when that is lighter.

Evidence discipline (paraphrased from agency-agents threat-intel, research
synthesist, and clinical-evidence patterns): corroborate before elevating
confidence; never flatten a single observation into a conclusion; grade
confidence explicitly; state scope boundaries so the review is auditable.

## Setup / discovery

Before a session:

```bash
{{REA_BIN}} doctor
which {{CODEX_BIN}} {{R2_BIN}}
test -d {{GHIDRA_HOME}} && test -d {{JADX_HOME}} && test -d {{KUNA_HOME}}
# GhidraMCP availability (expect failure if CodeBrowser is closed)
curl -sf --max-time 2 "{{GHIDRA_MCP_URL}}/" >/dev/null \
  && echo "GhidraMCP up" || echo "GhidraMCP down — open CodeBrowser or use headless/static only"
```

Record tool versions in the evidence log. If `rea doctor` flags a provider,
fix or route to ops before promising that path.

## Session workflow

### 1. Scope (intake)

Capture at minimum:

- Target identity (name, platform, version, hash if file-based)
- Feature under investigation
- Desired technical depth
- Platforms/versions that matter
- Goal: understanding | compatibility | rebuilding
- Permission boundary (ownership / written authorization)
- What the user already knows or has tried

Refuse or narrow scope that implies DRM crack, access bypass, cheats, or piracy.

### 2. Gather

- Collect permitted files, links, logs, screenshots into `{{OUT_ROOT}}/<session>/raw/`
- Compute hashes for binaries; note provenance
- Do **not** pull credentials or private endpoints into chat

### 3. Static analysis

Pick tools by target class (see recipes). Prefer non-destructive inspect:

- Listing / strings / import tables / manifest / resource trees
- Decompilation and cross-references for the **named feature** only
- Map call flows from UI/entry points to the behaviour of interest

Write Observations with file paths, symbol names, offsets, or resource IDs.

### 4. Dynamic analysis (consent gate)

Only after explicit user consent for that target:

- Frida via `{{FRIDA_VENV}}` or REA Frida helpers
- Limit hooks to the feature scope; no credential dumping, no DRM defeat
- Log attach target, script purpose, and session timestamps

If consent is unclear, stay static and list what dynamic would answer.

### 5. Write receipted report

Emit under `{{OUT_ROOT}}/<session>/report.md` (and optional vault sync). Use the
report template below. Every non-trivial claim needs a receipt.

## Per-target recipes (high-level patterns only)

Commands are illustrative patterns — fill placeholders; do not expand into
exploit steps.

### Android APK / DEX

- Unpack / list: `jadx` or REA JADX provider against the APK
- Manifest, permissions, components, resource IDs for the feature
- Kuna for structured Android exploration when wired through REA
- Trace UI → ViewModel/service → native `.so` only if the feature crosses JNI
- If native code matters, continue under ELF recipe for the `.so`

```bash
# pattern: decompile to out dir (no install, no run)
{{JADX_HOME}}/bin/jadx -d {{OUT_ROOT}}/<session>/jadx <apk>
{{REA_BIN}} doctor   # confirm JADX/Kuna providers
```

### Linux ELF

- File type, arch, linkage, imported symbols via `{{R2_BIN}}` or `readelf`
- Strings and cross-refs around the feature keywords
- Ghidra headless or CodeBrowser+GhidraMCP for deeper decompilation
- Projects under `{{GHIDRA_PROJECTS}}`; never commit secrets into project notes

```bash
{{R2_BIN}} -A -c 'iI; ii; iz~<keyword>' <elf>
# Ghidra: open in CodeBrowser or headless analyzeHeadless into {{GHIDRA_PROJECTS}}
```

### Windows PE

- Same discipline as ELF: headers, imports, exports, resources, strings
- Prefer Ghidra / r2 for static mapping of the named feature
- Note packers/protectors as **Unknowns** if present — do not provide unpacker
  crack recipes; state that further work needs a permitted unpacked sample

```bash
{{R2_BIN}} -A -c 'iI; ii; iE; iz~<keyword>' <pe>
```

## Codex + REA MCP usage notes

- Prefer a Codex session with MCP server `rea` for multi-step RE that needs
  JADX + Kuna + Ghidra + Frida coordination.
- Keep the Codex prompt scoped: feature name, evidence-only, no exploit asks.
- Restart or refresh the Codex session if env / MCP registration changed
  (see open questions in the agent notes).
- For one-shot inspects, Shell + `{{R2_BIN}}` / jadx / Ghidra headless may be
  cheaper than opening Codex.
- If a sibling "Codex" agent desk exists in the bot fleet, hand long sessions
  there when that improves isolation; keep findings receipted back here.

## GhidraMCP availability check

Before relying on live Ghidra MCP tools:

```bash
curl -sf --max-time 2 "{{GHIDRA_MCP_URL}}/" >/dev/null
```

- Up → proceed with MCP-assisted navigation.
- Down → say so; fall back to Ghidra headless, r2, or ask the user to open
  CodeBrowser. Do not fabricate decompiler output.

## Report template

```markdown
# RE report — <target> — <feature>

## Scope
- Target / version / hash:
- Feature:
- Goal: understanding | compatibility | rebuilding
- Permission basis:
- Tools used (version):

## Observations
| ID | What | Where | How | Confidence |

## Hypotheses
| ID | Claim | Based on | How to confirm/refute |

## Unknowns & assumptions
- ...

## Builder reproduction notes
What a clean-room builder would need (interfaces, data shapes, sequences) —
without proprietary constants that would enable piracy or bypass.

## Evidence index
Links/paths under {{OUT_ROOT}}/<session>/ ...

## Guardrail attestation
No exploit/PoC/malware/DRM-crack/cheat/piracy content produced.
```

## Evidence log (running)

Append-only `{{OUT_ROOT}}/<session>/evidence-log.md`:

```markdown
| Time (local) | Action | Tool | Result summary | Path/ref |
```

## Collaboration / ops

- Infra, package installs, sudo, or cluster changes → ops / chief-of-staff desk.
- Public artifacts must be scrubbed of host paths, private IPs, tokens, and
  personal identifiers before publish.

## Optional reference sheets

See `reference/` beside this skill for scrubbed tool cheat sheets (command
classes only).
