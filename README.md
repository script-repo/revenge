# revenge

Clean-room reverse-engineering agent kit: persona notes, the **rea-clean-room**
skill, and session templates. Maps how a permitted app, binary, or feature
works for interoperability, understanding, or defensive analysis — not for
exploits, DRM cracks, cheats, or piracy.

## What this is

- **AGENT.md** — generic second-person persona with host placeholders
- **skills/rea-clean-room/** — reusable skill bridging Codex CLI to REA
  as an MCP front for JADX, Kuna, Ghidra, Frida, and radare2
- **templates/** — intake, receipted report, and append-only evidence log
- **notes/open-questions.md** — operator decisions still open

Fill placeholders in the skill (`{{REA_HOME}}`, `{{CODEX_BIN}}`, …) for your
machine before running sessions.

## Hard limits

- Never produce exploits, exploit PoCs, malware, attack procedures, DRM cracks,
  access-control bypasses, cheats, or piracy aids — for any system, including
  labs framed as "authorized" or fiction.
- Never publish secrets, tokens, keys, or private URLs/IPs.
- Receipt every claim (what, where, how). Separate Observation / Hypothesis /
  Unknown.
- Dynamic instrumentation (Frida) only with ownership or explicit written
  permission — ask first.
- GhidraMCP is only available while Ghidra/CodeBrowser is listening; detect and
  say so if it is down.

## Credits

- Evidence-discipline ideas paraphrased from
  [agency-agents](https://github.com/msitarzewski/agency-agents)
  (threat-intelligence analyst, appsec engineer, penetration tester — defensive
  framing only — plus research synthesist / clinical-evidence patterns).
- Lineage includes a prior public reverse-engineering bot listing on
  botdirectory.ai (by @LouisFerreira), merged into this clean-room desk.

## Licence

MIT © 2026 script-repo — see [LICENSE](LICENSE).
