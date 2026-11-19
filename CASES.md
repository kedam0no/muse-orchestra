# Cases — evidence for the conductor pattern

Anonymised, factual case records for the package's evidence page.
Each case: user's input (big picture only), what circulated among the AIs,
measured facts, and the verified outcome. A case is only marked complete
when its outcome has been verified — drafts stay marked as such.

## Case 1 — A cloud guest works through a shared yard (2026-11-13)

**User's input (the whole of it):**
- Connect GitHub, and let the cloud AIs work through it instead of
  through the user's hands.
- No technical choices, no approvals, no relaying were asked of the user
  after that. (One exception, stated plainly: creating the repository
  itself was the user's one step — the conductor's GitHub integration
  cannot create repositories, a limit stated to the user with its reach.)

**What circulated among the AIs:**
1. The conductor posted a work request as Issue #1 in a private test
   repository (the "guest yard").
2. GitHub Copilot — a cloud AI with no key to the house — picked the
   issue up and returned the work as Pull Request #2.
3. The conductor read the PR and its diff back and verified the content
   against the request.
4. A second cloud AI (ChatGPT) independently read the same Issue and PR
   through its own GitHub connection and posted a review comment to the
   PR; the conductor read that comment back and verified its wording
   and placement (Review ID 5891955134).

**Measured facts:**
- Three AIs (conductor, Copilot, ChatGPT) exchanged work through one
  repository with zero user relay: no copy-paste, no carried files,
  no re-explaining of context by the user.
- Every claim in this case was verified by reading the actual Issue,
  PR, diff and review back from GitHub — not taken from any agent's
  report alone.

**Outcome:** COMPLETE (verified 2026-11-13). The guest-yard route became
a standing route of the house: cloud AIs receive selected work through
GitHub Issues/PRs, and results return through the same route.

**Why it belongs in the evidence page:**
The user's contribution was one direction ("let them work through
GitHub"). Discovery of the route's limits, the work request, the
execution, and the verification all circulated AI-side — and the one
step that genuinely required the user (creating the box) was named
as such, not hidden inside the flow.
