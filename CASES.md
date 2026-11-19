# Cases — evidence for the conductor pattern

Anonymised, factual case records for the package's evidence page.
Each case: user's input (big picture only), what circulated among the AIs,
measured facts, and the verified outcome. A case is only marked complete
when its outcome has been verified — drafts stay marked as such.

## Case 1 — A conversation route outside anyone's management (2026-11-20)

**User's input (the whole of it):**
- "Honestly, I don't want to converse anywhere other than Signal, Session or SimpleX."
- "Conversation by nature is not something conducted under anyone else's management."
- "Open the circuit to SimpleX."
- No technical choices, no approvals, no relaying were asked of the user after that.

**What circulated among the AIs:**
1. The conductor (Muse) first stated, from product docs, that the Muse app
   itself has no SimpleX transport (WhatsApp only) — a capability limit
   stated with its reach.
2. The user corrected the conductor: the local resident Hermes already
   carries a SimpleX connector. The conductor verified this against upstream
   Hermes Agent documentation (SimpleX adapter added in v0.14.0; the house
   Hermes runs v0.23.1) and corrected its own earlier statement.
3. The conductor pulled the official Hermes SimpleX setup procedure
   (simplex-chat daemon on ws://127.0.0.1:5225, `hermes gateway setup`,
   DM-pairing access control) without involving the user.
4. The conductor sent a single one-pass instruction (inspect → install →
   configure → persist at logon → verify → report, with before/after
   records and house guardrails) directly to the home PC's gateway agent
   over the tailnet client — the user relayed nothing.
5. Access design: no permanent allow-all; the user's only remaining step
   is the first-message DM pairing approval, a values/control decision
   that genuinely belongs to him.

**Measured facts:**
- Gateway check before dispatch: hello-ok (PC online, tailnet route live).
- Setup run accepted at 07:46 JST (runId af7828a1-0700-416e-9642-5c664653873d);
  the run exceeded the client-side 600 s reply window, so its final report
  did not reach the conductor. A status-only verification query was sent
  instead of blindly re-running the job.

**Status check (07:59 JST, via a second gateway query):** partial state —
simplex-chat v7.1.4.0 installed; a SimpleX profile exists (named "airooms");
`.env` in the real Hermes home (D:\AI_Tools_Migrated\Data\hermes-agent)
contains SIMPLEX_WS_URL and SIMPLEX_AUTO_ACCEPT; Hermes gateway running.
Not done: the daemon had exited (port 5225 empty), the SimpleX platform is
not enabled in config.yaml ("No messaging platforms enabled" in logs), and
no contact address exists. A finishing task carrying these exact facts was
dispatched at 08:00 JST — no finished parts are being redone.

**Outcome:** COMPLETE (verified 08:01–08:04 JST, finishing run
47b7f637-9f46-4c16-8de4-203d59203b38). Daemon listening on 127.0.0.1:5225
with logon autostart registered; `platforms.simplex.enabled: true` in
config.yaml; gateway log records "SimpleX adapter initialized" →
"SimpleX: connected to ws://127.0.0.1:5225" → "Gateway running with
1 platform(s)"; `hermes send --list` shows a Simplex target. Bot display
name: Hermes. Contact address issued (recorded on the PC in
simplex-chat\CONTACT_ADDRESS.txt; not reproduced in this public case file).
Total elapsed from the user's one-line order (07:45) to verified open
circuit: about 20 minutes, including one timed-out run and one status
query — all absorbed AI-side.

**Why it belongs in the evidence page:**
The user's contribution was a value ("conversation must not sit under a
third party's management") and one short order. Discovery, correction of
the conductor's own error, procedure research, execution and verification
all circulated AI-side. The one approval left to the user is one he can
actually evaluate — who gets access — which is exactly the pattern's rule
for what may rise to the user.
