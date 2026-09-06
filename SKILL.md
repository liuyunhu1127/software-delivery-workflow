---
name: software-delivery-workflow
description: Run a resumable, gated software-engineering workflow for requirements, design, development, testing, optional release, and final expert review. Use when the user asks to start, resume, inspect, supplement, or run a numbered phase of this workflow, or requests its standard artifacts and stage-level expert scoring. Do not invoke for an ordinary one-off coding task unless the user requests this workflow.
---

# Software Delivery Workflow

Run the selected workflow mode and persist enough state to resume after chat, process, or authentication interruption.

## Recognize the request

Map the request to one action:

- `start-full`: run 01 → 02 → 03 → 04 → 05 when selected → 06;
- `start-stage`: run only one selected phase from 01 through 06;
- `resume`: continue an existing workflow from its latest checkpoint;
- `supplement`: inspect existing artifacts and repair only missing, stale, or inadequate parts;
- `status`: report current phase, state, artifacts, score, open issues, and next safe action;
- `review`: run the current phase expert review or the 06 final review.

Read [shared/trigger-protocol.md](shared/trigger-protocol.md) to parse invocation wording and initialize or locate a run.

## Execute a run

1. Resolve the project, workflow identifier, requested action, selected phase, source inputs, and whether phase 05 is enabled. Ask only for missing information that changes scope or safety.
2. Read [shared/workflow-spec.md](shared/workflow-spec.md), [shared/document-control.md](shared/document-control.md), and [shared/scoring-rules.md](shared/scoring-rules.md).
3. Create or load one requirement workspace at `<project-root>/docs/<english-requirement-slug>/`. Store all workflow, session, checkpoint, issue, and review-history state in its single `工作流状态.md`. Do not create a separate run directory, issue register, applicability file, or checkpoint file.
4. Create only the active phase directory. Never pre-create later phase directories. Phase 05 and its report do not exist until the user enables phase 05.
5. For the active phase, read only that phase's document templates and `expert-review-scorecard.md`. Use the scorecard as the structure for one review-conclusion file under the requirement-level `输出报告/`; do not output a separate scorecard file.
6. Determine each artifact's applicability and record the applicability section inside `工作流状态.md`. Do not treat an unfilled artifact as “not applicable”; record the reason for every exclusion.
7. Generate or revise only the standard business documents defined for that phase. Documents receive quality findings but no numeric document score.
   - In phase 01, every user story must use the exact card labels `【AS】`、`【I want】`、`【so that】`, followed by acceptance criteria using `【Given】`、`【When】`、`【Then】` in that order, and a numbered `业务规则` list. Repeat the Given/When/Then group for multiple scenarios. Then decompose the story into identifiable Tasks, declare upstream/downstream story relationships and execution waves, and provide role-based person-day estimates with scope and uncertainty. Do not replace these card labels with a prose sentence or Gherkin code block, and do not substitute story points for person-days when the user requests effort.
   - In phase 02, detailed design must contain the minimum diagrams needed to remove implementation ambiguity. Select diagrams by design risk rather than decoration; for a stateful business system this normally includes a module/package dependency view, domain or class model, sequence diagrams for critical flows, and state diagrams for lifecycle-heavy aggregates. Every diagram must be consistent with the surrounding text, API, database, and UX artifacts.
   - In phase 02, when an HTTP API is applicable, create one machine-readable OpenAPI 3.x contract (`openapi.yaml` or `openapi.json`) beside the human-readable API design. It is a companion artifact of the API design, not a separately scored document. Keep paths, schemas, security, errors, and examples synchronized and ensure Swagger tooling can consume it.
8. After all applicable phase artifacts are ready, conduct one expert review and calculate one 0—100 phase score. For phase 06, calculate one independent 0—100 overall score; do not average historical phase scores.
9. Save the phase review, score, findings, and re-review history in the phase's one review-conclusion file under `输出报告/`. Phase 06 writes only `输出报告/06-总体专家评价报告.md` and has no phase directory.
10. Before the gate, show a numbered `待用户确认和评估的问题` list. Include every key decision awaiting confirmation, unresolved finding or risk, deferred item with its planned closing phase, and the effect of passing. If there is nothing to confirm, explicitly state `无待确认问题`. Then present the score, evidence, findings, red-line risks, and recommendation, and ask exactly: `本阶段已完成执行和专家评审。请回复“通过”，或回复“不通过：问题说明”。` A plain `通过` confirms the listed decisions and accepts the stated deferrals; a response may approve with exceptions.
11. On `通过`, baseline the artifacts and close the phase. In a full run, continue to the next enabled phase; in a single-phase run, stop.
12. On `不通过`, record the user's findings inside `工作流状态.md`, update every affected artifact and trace link, update the same review-conclusion file, show score change, and return to the same gate. Do not create an additional issue or re-review file.

## Persist and recover

Use the `工作流状态.md` structure defined in [shared/workflow-spec.md](shared/workflow-spec.md).

- Save the single state file after each user message, artifact revision, external action, review, and user gate.
- Treat timeout, disconnect, or process loss as paused, never as completed or failed.
- Before retrying a deployment, database change, publication, or notification, query its recorded result and honor its idempotency key.
- On resume, show the recovered workflow identifier, active phase, last completed action, open issues, and next safe action before proceeding.
- Keep only business documents, `工作流状态.md`, and one review-conclusion file per completed phase visible in the requirement workspace.

Do not claim that an in-memory chat alone provides persistence. Persistence exists only when the run state and artifacts have been written to durable storage available in the current environment.
