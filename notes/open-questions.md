# Open questions (public-safe)

Track decisions that affect how operators run this skill. No hostnames, home
paths, or private IPs here.

1. **Codex session refresh** — After changing MCP env or REA providers, must a
   new Codex session be started for the agent to see the new tools, or is a
   reconnect enough?
2. **Optional disassemblers** — Hopper and IDA remain optional; IDA needs a
   licence. When (if ever) should they be wired beside Ghidra/r2?
3. **Frida device policy** — Default to ask-every-time for USB/emulator attach,
   or allow a standing written permission per named target?
4. **GhidraMCP lifecycle** — Documented expectation: MCP only while
   CodeBrowser is open. Should the skill auto-fall back to headless without
   prompting when the URL check fails?
5. **Output retention** — How long to keep `{{OUT_ROOT}}/<session>/` artifacts
   before scrubbing or archiving?
6. **Skill packaging** — Prefer embedding `rea-clean-room` only in this repo,
   or also publishing as a standalone skill package for other agents?
