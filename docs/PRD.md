# Sidebar Steroids PRD

> Proposed; separately installable Pi add-on; generic versioned upstream host capability; no runtime implementation claimed.

## 1. Decision and quick path

Use a dedicated `sidebar-steroids` npm Pi package and keep the product implementation outside the upstream fork. The upstream contributes only a generic, versioned sidebar host capability. The product leaves STATUS unchanged and at its default behavior; GIT and FILES are add-on views. Capability discovery must work across separate Pi package module roots. If the host capability is absent or incompatible, the add-on fails closed with an actionable message. Removing the add-on must leave Pi and Gentle Shell healthy. Local Git and Files work offline, while GitHub integration is optional.

Quick path:

```bash
pi install npm:<package>
pi update --extensions
pi remove <source>
```

Verification must cover host compatibility, a package smoke install, regression of the original STATUS behavior, and attribution-manifest checks.

## 2. Document control

| Field | Value |
|---|---|
| Status | Proposed |
| Target release | Release 1 of the separately installable add-on. A version, publication date, and upstream compatibility range remain unset until the generic host capability, package identity, and registry and trademark reviews are complete. |
| Document owner | Sidebar Steroids product owner |
| Upstream decision owner | Gentle Shell upstream maintainer |
| Implementation owner | Sidebar Steroids add-on maintainer |
| Verification owner | Independent product and upstream reviewer |
| Publication owner | Human release operator with authority over the product repository and package registry |
| Upstream repository | `https://github.com/Gentleman-Programming/gentle-shell.git` |
| Planning snapshot | 2026-11-08 |
| Upstream base | Upstream `main` at commit `bac4c4a3ffc4e8455548691dcd820d60fd3c58f6` |
| Local provenance | This checkout contains three commits beyond the upstream base. They are not attributed to Sidebar Steroids without an explicit file-level diff. |

Owner roles are deliberately separated: the product owner controls add-on scope and release decisions; the upstream maintainer controls host-API acceptance and upstream attribution; the add-on maintainer owns implementation; the reviewer owns independent evidence; and the release operator owns all remote publication actions.

Artifact boundaries:

| Artifact or surface | Owning boundary | Contract |
|---|---|---|
| Generic sidebar host capability | Gentle Shell upstream | A small, generic, versioned capability that can host independently supplied views. It contains no Sidebar Steroids Git, Files, or agent-specific product behavior. |
| Sidebar Steroids add-on | Dedicated product repository, provisionally `andrikonbeat/sidebar-steroids` | STATUS/GIT/FILES behavior, add-on-specific adapters, package metadata, and product tests. The product repository name remains subject to registry and trademark review. |
| Product requirements and upstream evidence | This checkout | This PRD, the attribution protocol, and the machine-readable upstream manifest document the proposal and preparation boundary. Product runtime source is not part of this artifact set. |
| Upstream change record | `docs/sidebar-steroids/upstream-manifest.json` | Machine-readable identification of proposed upstream changes relative to the recorded base. It does not transfer ownership of upstream code. |

Provenance evidence:

- Human-readable protocol: [docs/sidebar-steroids/UPSTREAM_ATTRIBUTION.md](UPSTREAM_ATTRIBUTION.md)
- Machine-readable manifest: [docs/sidebar-steroids/upstream-manifest.json](upstream-manifest.json)

Remote repository creation, package-registry submission, and public release or publication are human-owned actions. They are outside this documentation task, require explicit authorization, and must not be inferred from this document.

## 3. Product definition

### Problem

Gentle Shell's existing sidebar is optimized for its original STATUS experience. It does not provide a coherent place to inspect local Git activity, repository structure, worktrees, branches, commits, or the progress of an attributed agent edit without leaving the terminal. A user who only sees a file marked as modified cannot determine which actor changed it, whether an edit is still in progress, or which file was edited most recently.

The product must add that visibility without changing the original STATUS rail, requiring a modified copy of Gentle Shell, or making local repository inspection depend on a remote service.

### Goals

- Provide one installable add-on with three explicit sidebar views: STATUS, GIT, and FILES.
- Preserve the original Gentle Shell STATUS rail unchanged and selected by default.
- Make local Git and repository-file inspection available in the terminal, including worktrees, branches, changed paths, commit relationships, and a repository tree.
- Represent attributed agent activity separately from Git working-tree state, with explicit source labels.
- Use a narrow, generic upstream host capability rather than embedding product behavior in upstream.
- Degrade safely: unsupported hosts fail closed with actionable guidance; optional GitHub integration is unavailable without network access; local Git and Files remain useful offline.
- Preserve upstream ownership, license notices, and an auditable record of proposed changes.

### Non-goals

- Replacing, redesigning, or changing the behavior of the original Gentle Shell STATUS rail.
- Patching or overwriting an installed upstream package, or requiring users to maintain a forked Gentle Shell installation.
- Treating Git working-tree state as proof that an agent performed an edit.
- Providing filesystem-write, commit, branch, merge, push, or other destructive Git actions in Release 1.
- Requiring GitHub credentials, a network connection, or a remote provider for local Git and Files use.
- Building a broad shared provider framework in upstream.
- Claiming continuous real-time filesystem observation; the current baseline does not provide a filesystem watcher.
- Creating or publishing the remote product repository or package as part of this PRD effort.

### Personas

| Persona | Need |
|---|---|
| Agent-following user | See where the agent is working, which path was edited last, and whether the visible change is attributed without leaving the terminal. |
| Repository developer | Inspect branches, worktrees, commits, changed paths, and the repository tree without losing the surrounding terminal workflow. |
| Offline or privacy-conscious user | Use local Git and Files without granting credentials or requiring a remote service. |
| Upstream maintainer | Review a narrowly scoped host capability and understand which upstream changes belong to the add-on proposal. |
| Release and support operator | Install, update, diagnose, and remove the add-on while preserving upstream health and attribution evidence. |

### User stories

- As an agent-following user, I want to distinguish an in-progress attributed edit from an ordinary modified file so that I can interpret the agent's current activity without guessing.
- As an agent-following user, I want to see the last attributed edited path and its source so that I can return to the agent's most recent work.
- As a repository developer, I want one footer control that switches among STATUS, GIT, and FILES so that related repository context remains in one stable location.
- As a repository developer, I want local Git information to include branches, worktrees, changed paths, and commit relationships so that I can understand repository state without a separate GUI.
- As a repository developer, I want a repository tree that identifies changed, new, deleted, active, last-edited, and unknown-source paths consistently with the GIT view.
- As a user working offline, I want local Git and Files to remain available without credentials so that network state does not block core inspection.
- As a user of an unsupported Gentle Shell version, I want a clear compatibility message rather than a broken sidebar so that I know what action is required.
- As an upstream maintainer, I want add-on behavior kept outside upstream and upstream changes described in the attribution manifest so that ownership remains reviewable.

### Measurable success metrics

| Metric | Release 1 target |
|---|---|
| STATUS regression | Every existing STATUS acceptance scenario produces the same content, ordering, and interactions with the add-on installed and STATUS selected as it does without the add-on. |
| Route reachability | All three views are reachable through the fixed footer control in at most two keyboard or pointer actions, with the selected route exposed textually and visually. |
| State labeling | Every path shown with repository state also has a separate Git-state value and attribution/source value; zero displayed paths imply agent attribution from Git status alone. |
| Local offline availability | All Release 1 local Git and Files inspection scenarios complete without network access or credentials; remote-only controls report unavailable rather than prompting for secrets. |
| View refresh latency | On the agreed reference repository and file fixtures, at least 95% of explicit local Git and Files refreshes complete within 1 second, excluding terminal rendering and GitHub requests. |
| Compatibility isolation | Every unsupported or incompatible-host fixture fails closed with an actionable message, and every removal fixture leaves the original upstream sidebar healthy and unchanged. |
| Attribution accuracy | In attribution fixtures with known actors, every agent-specific label matches the recorded lifecycle evidence; Git-only fixtures produce no agent attribution. |

### STATUS/GIT/FILES scope matrix

| Dimension | STATUS | GIT | FILES |
|---|---|---|---|
| Role | Preserve the original Gentle Shell rail as the default experience | Inspect local repository and attributed agent activity | Inspect the repository tree and path-level activity |
| Default selection | Selected on add-on activation and when the user has not chosen another view | Not selected by default | Not selected by default |
| Repository content | Original Gentle Shell content only; no replacement or product overlay | Local branches, worktrees, changed paths, commit relationships, diffs, and optional remote context | Local directory and file tree with path-level Git and activity annotations |
| Agent activity | Preserve existing upstream behavior; this product does not reinterpret its content | Show attributed active, last-edited, and unknown-source activity where evidence exists | Show the same activity semantics for the corresponding path |
| GitHub | No required integration | Optional, read-only enrichment; never required for local Git use | Optional path enrichment; never required for local tree use |
| Actions in Release 1 | Preserve the original rail's supported interactions | Inspection and refresh only; no write or destructive Git action | Inspection and refresh only; no file mutation |
| Offline behavior | Preserve original upstream behavior | Local Git remains available; GitHub-only data is explicitly unavailable | Local Files remains available; GitHub-only data is explicitly unavailable |

## 4. Experience contract

### Layout and navigation

The add-on presents a fixed bottom three-way control labeled **STATUS**, **GIT**, and **FILES**. The control is pinned to the bottom of the rail, remains visible while view content scrolls, and always exposes the active selection. Selecting a view changes the rail content without opening a modal or replacing the main terminal workspace.

The desktop rail has a fixed reserved width of 50 terminal columns. Its internal content, labels, borders, and footer must fit within that width without horizontal clipping.

| Terminal condition | Required behavior |
|---|---|
| Fullscreen mode and width of at least 140 columns | Show the canonical rail with the fixed 50-column reservation, the pinned three-way control, and independently scrollable view content. Exactly 140 columns satisfies the width threshold. |
| Fullscreen mode and width below 140 columns | Do not force the desktop rail into the layout. Preserve access to the three-way control through the narrow fallback. |
| Non-fullscreen mode | Do not force the desktop rail into the layout. Preserve access to the three-way control through the narrow fallback when the host exposes a suitable surface. |
| Resize across the 140-column boundary | Transition between desktop and narrow presentations without losing the active view, scroll context that remains applicable, or footer selection state. |

The narrow fallback covers small terminals and mobile-hosted terminal sessions. It keeps the same three routes and state semantics in a compact, scrollable presentation with a bounded width. The fallback must not compress or replace the main terminal work area. When a host surface is unavailable, a compact launcher may provide the control without pretending that the desktop rail is present.

### Keyboard and pointer behavior

- The three-way control is one tab stop. When focused, Left and Right or Up and Down move focus among STATUS, GIT, and FILES; Home and End select the first and last route; Enter or Space activates the focused route.
- The active route is identified by text and visual emphasis, not color alone. Route changes must not depend on hover.
- Pointer activation of STATUS, GIT, or FILES selects that route directly. Pointer targets provide visible focus feedback and a pointer cursor.
- In list or tree content, Up and Down navigate visible items, Enter opens the selected item's available detail, and Escape returns focus to the three-way control. Navigation keys operate only within the focused add-on surface and must not override unrelated terminal shortcuts.
- Keyboard and pointer paths must select the same route and expose the same content and states. No route is available only through a pointer gesture.
- Re-entering the control preserves the previously selected route. A refresh must not reset the selected route or move focus unexpectedly.

### Content states

| State | STATUS | GIT | FILES |
|---|---|---|---|
| Empty | Preserve the original Gentle Shell empty state without substitution | State whether there is no repository, no commits, or no local changes, and do not conflate those conditions | State that the repository tree is empty; a missing or unreadable root is an error, not an empty tree |
| Loading | Preserve the original Gentle Shell loading behavior | Show a labeled loading state while obtaining local Git metadata; do not present partial data as a complete snapshot | Show a labeled loading state while obtaining the tree; retain a visible loading indicator until the snapshot is committed |
| Error | Preserve the original Gentle Shell error behavior without changing its behavior | Identify the failed local operation at a category level, such as unavailable repository, unsupported Git operation, timeout, or permission denied, and offer safe recovery guidance | Identify the failed tree or path operation and offer safe recovery guidance without exposing sensitive filesystem details |
| Offline | Preserve original upstream behavior | Keep local Git information available and label GitHub-only information as unavailable because the network is offline | Keep the local tree available and label GitHub-only information as unavailable because the network is offline |

Errors are isolated to the affected add-on view. A Git failure must not replace or corrupt the FILES view, and a Files failure must not alter STATUS. A failed view may explain how to recover but must not trigger credentials prompts, remote writes, or destructive retries.

### Accessibility

- Every control and actionable item has a stable accessible name: **STATUS**, **GIT**, **FILES**, or the specific repository action.
- The active route, current Git state, agent activity state, and source attribution are available as text. Color, icons, and position are supplementary signals.
- Focus order follows the footer control and the active view's content. The footer is reachable without traversing every path in the view.
- Focus indicators remain visible against the host theme and are not conveyed by color alone.
- View changes, loading completion, and errors are announced through the host's available notification mechanism when one exists; visual state remains sufficient when it does not.
- Text and controls meet the host's terminal accessibility conventions, including readable truncation and no reliance on a minimum physical viewport beyond the defined breakpoints.
- No animation, rapid refresh, or automatic focus movement may make a view difficult to read or operate.

### Layout invariants

1. The desktop rail reserves exactly 50 terminal columns; the main workspace and host chrome are not overlaid by rail content.
2. The three-way control remains fixed at the bottom of the rail and remains visible during content scrolling.
3. Exactly one of STATUS, GIT, and FILES is active at a time, and the initial active route is STATUS.
4. View content scrolls within the rail; the footer control does not scroll out of view.
5. Long paths, branch names, commit subjects, and error messages truncate or wrap within the rail without corrupting the footer or adjacent terminal content.
6. Empty, loading, error, and offline states occupy the view content area and do not collapse the footer.
7. A resize or route change preserves state that remains valid and does not discard a newly selected route.
8. The add-on does not resize, clear, or replace the main terminal workspace.
9. STATUS retains the original Gentle Shell rail layout and interaction contract; the fixed control is an additive surface outside that rail's content.
10. Local Git, Files, and STATUS states remain independent, so one view's failure or offline condition does not obscure the others.

## 5. State semantics

Git working-tree state and attributed agent activity are separate data domains and must be rendered as separate facts. Git can establish that a path differs from its comparison point, but it cannot establish which person or agent caused the difference. Conversely, attributed agent activity can establish that an edit is in progress or was completed without proving the resulting Git state.

### Canonical states

| State | Domain | Definition | Required evidence | Interpretation limit |
|---|---|---|---|---|
| **ACTIVE/EDITING** | Attributed agent activity | A path or edit target is currently being changed by an identified agent operation that has started and has not completed, failed, or been cancelled. | Attributed edit-start evidence, an identifiable actor or session, and the target path or edit scope. | This label must not be inferred from a modified, new, or deleted Git status. A missing completion event is not by itself proof that work is still active. |
| **LAST EDITED** | Attributed agent activity | The path associated with the most recent completed attributed edit in the selected scope. | Attributed edit-completion evidence and the edited path. The completion time is used only when supplied by the source. | It means most recent in the available attributed history, not necessarily the most recent modification by any actor. It does not imply that the path is currently being edited. |
| **MODIFIED** | Git working-tree state | Git reports that a tracked path differs from the selected Git comparison point in the working tree. | Git metadata for the selected repository or worktree. | It does not identify an actor and does not prove that an agent made the change. It may also coexist with staged changes. |
| **NEW** | Git working-tree state | Git reports a path as added or untracked relative to the selected comparison point. | Git metadata for the selected repository or worktree. | It does not prove that the path was newly created by the agent; it may predate the attributed activity record or have been created by another tool. |
| **DELETED** | Git working-tree state | Git reports that a tracked path is absent from the working tree relative to the selected comparison point. | Git metadata for the selected repository or worktree. | It does not identify who removed the path or whether an agent performed the removal. |
| **UNATTRIBUTED/UNKNOWN SOURCE** | Attribution provenance | A path has a known repository state, but no sufficiently reliable evidence identifies the actor; or activity is observed without a reliable source identity. | Explicit absence or ambiguity of trustworthy attribution evidence. | Unknown source is not an error in Git metadata. It must remain explicit rather than being replaced with a guessed agent label. |

### Combined rendering

A path may carry one Git-state value and one agent-activity or attribution value at the same time.

| Git state | Activity or attribution | Required reading |
|---|---|---|
| MODIFIED | ACTIVE/EDITING | Git observes a modified tracked path, and attributed evidence independently identifies an edit in progress. |
| MODIFIED | LAST EDITED | Git observes a modified tracked path, and it is the most recent path in the completed attributed-edit history. |
| MODIFIED | UNATTRIBUTED/UNKNOWN SOURCE | Git observes a modified tracked path, with no reliable actor attribution. |
| NEW | ACTIVE/EDITING | Git observes a new or untracked path, and attributed evidence independently identifies an edit in progress. |
| NEW | UNATTRIBUTED/UNKNOWN SOURCE | Git observes a new or untracked path, but its creator is not known. |
| DELETED | UNATTRIBUTED/UNKNOWN SOURCE | Git observes a tracked path removed from the working tree, but no reliable actor attribution exists. |

### Attribution rules

- Attribution comes only from explicit, attributable lifecycle evidence. Git status, timestamps, file order, and proximity to agent activity are supporting context, not proof.
- The UI preserves Git state when attribution is absent. Unknown actor information must not erase or relabel a known MODIFIED, NEW, or DELETED state.
- ACTIVE/EDITING requires positive in-progress evidence. A modified file without such evidence is not shown as active.
- LAST EDITED refers only to completed attributed edits in the selected scope and must not be assigned from Git metadata alone.
- UNATTRIBUTED/UNKNOWN SOURCE covers both known-but-unattributed repository state and activity whose source cannot be established. The label must distinguish uncertainty from an actual source named "unknown".
- State labels are scoped to the selected repository or worktree and to the evidence window presented. Changing scope clears or re-evaluates path-level attribution rather than carrying it across roots.
- Incomplete or stale evidence is disclosed through the relevant loading, freshness, or error state; it is not converted into a positive attribution claim.

### Observation boundary

The current baseline has no filesystem watcher, and this PRD does not claim that one exists or that the UI continuously observes every filesystem write. A future data model may use agent lifecycle events, bounded polling, explicit refresh, or a combination of these mechanisms. Until a snapshot is obtained, the UI must describe its freshness or loading state accurately. Absence of an event is not evidence that no change or agent activity occurred.

## 6. Requirements

Requirement identifiers are stable within this PRD. Each functional requirement has one corresponding stable acceptance criterion (AC). Non-functional requirements state the observable threshold and the acceptance evidence that proves it.

### 6.1 Functional requirements

| Requirement | Acceptance criterion | Verification method |
|---|---|---|
| **FR-STATUS-001 — Preserve the original STATUS view.** The add-on shall not replace, overlay, or reinterpret the original Gentle Shell STATUS content or interactions. | **AC-STATUS-001:** With the add-on installed and STATUS selected, every baseline STATUS fixture has the same content, ordering, empty/loading/error behavior, and supported interactions as the same fixture without the add-on. | Differential fixture test against the upstream STATUS baseline, plus focused render and interaction regression tests. |
| **FR-STATUS-002 — Preserve the selected view and default.** STATUS shall be selected initially when the user has no saved selection; changing routes and entering or leaving fullscreen mode, including crossing the 140-column breakpoint, shall preserve the current route where the host permits it. | **AC-STATUS-002:** A fresh session starts on STATUS; after selecting GIT or FILES, route changes, fullscreen transitions, and resize transitions expose the same selected route. A session with no user selection still starts on STATUS. | Route-state and host-mode transition tests covering initial, selected, resize, and fullscreen states. |
| **FR-STATUS-003 — Provide the fixed route control.** The add-on shall provide one fixed bottom control for STATUS, GIT, and FILES. It shall remain visible while view content scrolls and shall have a compact fallback when the fullscreen rail is unavailable. | **AC-STATUS-003:** In desktop and narrow fixtures, the control exposes all three routes, stays reachable while content scrolls, selects exactly one route, and is not replaced by a pointer-only launcher. | Desktop and narrow render fixtures, scroll-layout assertions, and keyboard/pointer activation tests. |
| **FR-GIT-001 — Inspect repositories, branches, and worktrees.** The GIT view shall expose the selected repository scope, local branches, and registered worktrees without changing repository state. | **AC-GIT-001:** Multi-root and worktree fixtures list the available local branches and registered worktrees, identify the selected scope, and never create, rename, check out, or delete a branch or worktree. | Git fixture tests over multiple roots and worktrees, with command and repository-state assertions. |
| **FR-GIT-002 — Inspect changes and diffs.** The GIT view shall show changed paths and their Git working-tree states and shall permit read-only inspection of an available diff. | **AC-GIT-002:** MODIFIED, NEW, and DELETED fixtures render their Git states and path names; selecting a changed path opens the available diff read-only and leaves the working tree unchanged. | Git fixture and diff-rendering tests, including pre/post working-tree snapshots. |
| **FR-GIT-003 — Provide compact history.** The GIT view shall provide a bounded, readable commit and parent-history summary for the selected scope without claiming a graphical commit graph. | **AC-GIT-003:** A fixture with known commits shows the expected commits, parent relationships, and bounded ordering in the compact history area; truncation is explicit when the available history exceeds the display bound. | Known-history fixture test with boundary, ordering, and truncation assertions. |
| **FR-GIT-004 — Refresh through snapshots, not render commands.** Rendering a cached Git or Files snapshot shall not spawn a Git command. Git commands may run only as part of an explicit or bounded refresh outside the render path. | **AC-GIT-004:** Repeated renders of an unchanged snapshot spawn zero additional Git processes; an explicit refresh performs one bounded collection and subsequent renders consume that snapshot. | Process-spy test around render and refresh calls, plus snapshot-update assertions. |
| **FR-GIT-005 — Keep Release 1 read-only.** GIT shall not stage, commit, push, pull, merge, delete a branch, reset, check out a branch, or perform any other mutating Git operation. | **AC-GIT-005:** Across all GIT interactions in Release 1, no mutating Git command is issued, no index or working-tree content changes, and no remote mutation occurs. | Command allowlist audit, local repository-state comparison, and remote-interaction test doubles. |
| **FR-FILES-001 — Inspect a repository tree.** The FILES view shall show a bounded tree rooted at the selected repository or worktree, including directories and files. | **AC-FILES-001:** A known repository fixture renders its expected tree paths and hierarchy for the selected root; an empty tree and an unreadable or missing root produce distinct states. | Temporary repository and fixture tests for hierarchy, empty state, and root-error handling. |
| **FR-FILES-002 — Annotate path activity.** Each FILES path shall expose its Git state and attributed activity or source separately, using the same semantics as the GIT view. | **AC-FILES-002:** Combined-state fixtures render one Git-state value and one activity/source value per path, including ACTIVE/EDITING, LAST EDITED, and UNATTRIBUTED/UNKNOWN SOURCE without inferring one from the other. | Cross-view fixture comparison against the canonical state matrix. |
| **FR-ATTR-001 — Distinguish Git state from attribution.** The product shall never infer agent attribution from Git status, timestamps, file order, or proximity to activity. | **AC-ATTR-001:** Git-only fixtures produce no agent attribution; a path may show a Git state together with independently evidenced activity, and an unknown source does not erase a known Git state. | Attribution matrix tests covering every defined combined state and the Git-only case. |
| **FR-ATTR-002 — Require explicit, scoped lifecycle evidence.** ACTIVE/EDITING shall require attributable edit-start evidence; LAST EDITED shall require attributable edit-completion evidence; attribution shall be scoped to its repository or worktree and evidence window. | **AC-ATTR-002:** Start-only, completed, failed/cancelled, missing-source, and changed-scope fixtures produce only their permitted labels; changing scope clears or re-evaluates path attribution. | Lifecycle-evidence fixture tests with explicit expected state for each boundary condition. |
| **FR-INSTALL-001 — Support an independent package lifecycle.** The product shall be distributed as a separately installable Pi extension package and shall support install, update, and removal without patching the installed upstream package. | **AC-INSTALL-001:** A package smoke test installs the product, exercises STATUS/GIT/FILES, updates the package, and removes it; after removal, Pi and the original Gentle Shell sidebar remain healthy and unchanged. | Isolated package install/update/remove smoke test with pre/post host-health checks. |
| **FR-INSTALL-002 — Fail closed on host incompatibility.** Capability discovery shall work across separately loaded Pi package module roots. If the generic host capability is absent or incompatible, the add-on shall not render partial product behavior and shall show actionable compatibility guidance. | **AC-INSTALL-002:** Fixtures with a compatible host succeed; fixtures with no host capability or an incompatible host version show a specific fail-closed message and expose no STATUS, GIT, or FILES add-on route behavior. | Cross-module-root capability-discovery tests and an incompatible-host compatibility matrix. |
| **FR-INSTALL-003 — Keep local inspection offline and GitHub optional.** Local Git and Files inspection shall work without a network or credentials. Optional GitHub data shall be read-only enrichment only and shall report unavailable rather than prompt for secrets. | **AC-INSTALL-003:** With network access denied and no credentials configured, local Git and FILES remain functional; requesting GitHub-only data returns an explicit unavailable state and triggers no credential prompt or network write. | Network-denial and no-credential fixtures with local-feature assertions and request interception. |

### 6.2 Non-functional requirements

| Requirement | Acceptance evidence | Verification method |
|---|---|---|
| **NFR-PERF-001 — Local refresh latency.** On the agreed reference repository and file fixtures, at least 95% of explicit local Git and Files refreshes shall complete within 1 second, excluding terminal rendering and GitHub requests. | The benchmark records each explicit refresh duration, excludes rendering and optional remote time, and reports the measured 95th percentile against the threshold. | Repeatable instrumented benchmark over the reference fixtures. |
| **NFR-REL-001 — Isolated and truthful failure behavior.** A failure in one add-on view shall not alter another view or STATUS; stale or incomplete data shall not be presented as a complete snapshot; removal shall not impair the upstream host. | Per-view error fixtures preserve the other routes and the original STATUS; interrupted refreshes expose a loading, stale, or error state; removal smoke checks show upstream health. | Fault-injection, cross-view isolation, interrupted-refresh, and removal-health tests. |
| **NFR-SEC-001 — Security and read-only boundaries.** Release 1 shall not perform mutating Git operations, access or print credentials, interpolate untrusted values into shell commands, or expose sensitive filesystem details. Git subprocesses shall use a sanitized, bounded environment and output. | A process and output audit records no mutating Git command, credential read, secret disclosure, shell interpolation, or unbounded output; local state remains unchanged after inspection. | Security-focused command audit, environment/output assertions, secret-capture tests, and repository-state comparison. |
| **NFR-ACC-001 — Accessible operation and state.** Every route and actionable item shall have a stable accessible name; keyboard and pointer activation shall be equivalent; active route, Git state, activity, and source shall be available as text; focus shall remain visible and ordered without color-only meaning. | Automated keyboard/pointer parity fixtures record the same route and content outcomes; text snapshots contain every required state label; focus and announcement checks pass for loading, error, and route changes. | Focused keyboard, pointer, focus-order, text-rendering, and notification tests. |
| **NFR-COMP-001 — Explicit host compatibility.** The add-on shall advertise only a tested generic host-capability range and shall fail closed for absent, older, newer-incompatible, or malformed capability metadata. | The compatibility matrix records success for each supported fixture and a specific actionable failure for every unsupported fixture; no partial product view is exposed. | Version-matrix and malformed-metadata tests. |
| **NFR-OBS-001 — Observable refresh and failure lifecycle.** Route changes, refreshes, snapshot freshness, and failures shall expose stable diagnostic categories sufficient to distinguish loading, empty, stale, unavailable, permission, timeout, and incompatible-host conditions. | Diagnostic-event fixtures record the route, scope identity, refresh identity, freshness, and failure category without credentials or sensitive path disclosure. | Structured diagnostic-event assertions and sanitized-output review. |
| **NFR-RES-001 — Bounded resource use.** Git and Files collection shall bound elapsed time, output size, retained snapshot size, and concurrent work; oversized repositories shall be truncated or paged explicitly rather than consumed without limit. | Large-repository fixtures terminate within the configured time and size bounds, report explicit truncation or pagination, and leave no unbounded child process or retained snapshot. | Resource-limit tests with process, timeout, memory, and output-size measurements. |
| **NFR-PRIV-001 — Local privacy and optional remote use.** Core local Git and Files operation shall require neither credentials nor a network connection. Optional remote enrichment shall be explicitly unavailable without approved access and shall not transmit local paths unless the user has consented to that integration. | Offline fixtures complete local inspection without network or credential access; remote-disabled fixtures do not request a network; consent-enabled fixtures transmit only the explicitly approved data. | Network/credential denial tests, request inspection, and consent-state fixtures. |

## 7. Current baseline

The current repository provides terminal-owned sidebar infrastructure and several local Git and activity primitives. It does not provide the Sidebar Steroids product, a stable third-party sidebar API, or the complete inspection experience described in this PRD.

### 7.1 Existing implementation and documentation evidence

| Area | Existing repository evidence | Current capability and boundary |
|---|---|---|
| Sidebar ownership and parts | `lib/shell-sidebar.ts`, including `sidebarPart` | The terminal owns sidebar state and composes independently registered parts. `sidebarPart` is an internal extension seam, not yet a stable third-party public API. It does not by itself provide the Sidebar Steroids routes, add-on lifecycle, or compatibility contract. |
| Fullscreen layout and rendering | `lib/shell-sidebar-layout.ts`, including `ScrollView` and digest-cached sidebar rendering | The sidebar rail is a fullscreen-mode surface, with its desktop breakpoint at 140 columns. View content can use `ScrollView`, and digest caching limits repeated assembly work. These are existing host internals, not an advertised add-on API. |
| Changes and Git inspection | `lib/shell-changes.ts` | Changes infrastructure already covers per-root Git status, diffs, branch metadata, and registered worktrees. Existing branch capability is not a branch-list UI, and the existing diff capability does not by itself constitute the planned compact-history experience. |
| Change capture | `lib/session-change-capture.ts` | The session layer records explicit write/edit outcomes. These outcomes are not the proposed public edit-start/edit-finished lifecycle contract and do not establish attribution for an arbitrary Git change. |
| Child-process mutation relay | `lib/agents-runner.ts` | The runner relays successful child-process mutation outcomes. That relay is not a public start/finish contract for attributed edits and is not a filesystem watcher. |
| Worktree registry | `lib/session-worktree-registry.ts` | A session worktree registry exists and supports the existing Changes capability. It does not provide the FILES repository tree or the add-on's unified GIT/FILES scope model. |
| Visual customization policy | `lib/visual-customization-policy.ts` | A visual customization policy already governs host presentation. The current evidence does not define a stable third-party sidebar extension contract derived from that policy. |
| Recorded baseline and STATUS behavior | `odd/tasks/sidebar-steroids-prd.md` (Log L3) and `docs/readme-reference.md` (STATUS diagnostic boundaries) | The task log records the source exploration summarized here. The readme reference documents the existing opt-in STATUS diagnostic boundary; STATUS diagnostics neither invoke nor retry STATUS and are not a repository or agent-attribution API. |
| Upstream attribution and planned contribution | `docs/sidebar-steroids/UPSTREAM_ATTRIBUTION.md` and `docs/sidebar-steroids/upstream-manifest.json` | These files identify the proposed upstream boundary and attribution evidence. They do not claim that the generic host capability has been implemented or merged. |

### 7.2 Capabilities that are absent

The following product capabilities do not exist in the current baseline:

| Absent capability | Boundary clarification |
|---|---|
| Repository tree UI | The existing Changes and worktree infrastructure can identify paths and Git state, but it does not render the FILES repository tree. |
| Branch-list UI | Existing branch metadata can support Changes; there is no dedicated branch-list interface in the current sidebar. |
| Commit graph UI | There is no graphical commit graph. The product's compact history requirement is a bounded commit/parent summary and does not assert that a graph already exists. |
| Ahead/behind UI | Existing Git inspection does not expose an ahead/behind indicator in the current sidebar. |
| GitHub provider | No GitHub provider or required remote service is present; GitHub enrichment remains optional and read-only. |
| Filesystem watcher | No filesystem watcher exists. The product must not claim continuous observation of arbitrary filesystem writes. |
| Public edit-start/edit-finished contract | Existing write/edit outcomes and child mutation relay do not form a public lifecycle contract for attributed edits. |

### 7.3 Product and upstream status

The separately installable `sidebar-steroids` product and its Git/Files behavior are planned in a dedicated product repository. The upstream contribution—a small, generic, versioned sidebar host capability—is also planned. Neither the product package nor that upstream host capability is implemented by the current baseline. Consequently, `sidebarPart` remains an internal seam rather than a stable third-party API, and the add-on must fail closed until the planned host capability is available in a compatible form.

## 8. Architecture

The proposed architecture has two independent boundaries. Upstream Gentle Shell contributes a small, generic, versioned capability for hosting independently supplied sidebar views. The dedicated `sidebar-steroids` package contributes the product behavior: GIT, FILES, repository inspection, optional GitHub enrichment, and attributed activity. This section defines a conceptual contract; it does not claim that the host capability or product package currently exists.

### 8.1 Ownership boundary

| Boundary | Owns | Does not own |
|---|---|---|
| Gentle Shell upstream | Capability publication and negotiation; view registration; active-view coordination; sidebar layout, render/digest caching, focus, and pinned-footer hosting; cleanup of registrations; generic API documentation and tests | GIT or FILES product behavior, Git command policy, GitHub behavior, attribution semantics, or `sidebar-steroids` package lifecycle |
| Dedicated `sidebar-steroids` repository and package | GIT and FILES views; read-only Git queries; optional GitHub adapter; attributed-activity integration; compatibility UX; view registration and disposal; package install, update, and removal behavior | Upstream layout or cache implementation, the original STATUS behavior, arbitrary mutation of upstream modules, or a private copy of the host capability |

The two artifacts communicate only through the public, versioned capability contract. They do not share source files, internal state, or a product-specific framework.

### 8.2 Cross-package discovery and version negotiation

Pi may load the upstream host and separately installed extension packages from different module roots. A normal relative or deep import therefore cannot be the integration mechanism. The planned discovery protocol is a process-global symbol/capability registry handshake:

1. The upstream host publishes an immutable capability descriptor under a protocol-qualified global symbol key. The descriptor identifies the sidebar-host capability, the canonical host instance, and the host API versions that the host can serve. The key must be stable across separately loaded Pi package module roots.
2. The add-on resolves that exact key, rather than importing upstream source or scanning global objects. It validates the descriptor shape, capability identity, host-instance identity, and protocol version before using any host handle.
3. The add-on intersects its supported API-version set with the versions advertised by the host and selects the highest mutually supported version. Registration, rendering, switching, and disposal then use that negotiated version.
4. The host confirms the negotiated version before accepting a view registration. A malformed descriptor, an unsupported version, or a conflicting host instance is treated as incompatible rather than being downgraded to an unversioned path.

If the capability is absent, the add-on exposes no GIT or FILES route, causes no additional footer control to appear, and presents actionable compatibility guidance through the host's non-product notification surface or an add-on command. If the capability is incompatible for the same reason, the add-on fails closed in the same manner. The original STATUS view remains available, selected by default, and unchanged.

The discovery contract deliberately excludes two shortcuts:

- **Deep imports are rejected.** Importing upstream implementation files or internal sidebar modules is not a supported integration, even when the files are present in the same installation. The add-on must not use relative paths into upstream source or depend on upstream internals.
- **Duplicate dependency instances are rejected.** The add-on must not bundle or resolve a second copy of the host or its capability implementation as a bypass. Exactly one canonical host may publish the capability in a process. A fixture with multiple competing publishers, a second resolved dependency instance, or an ambiguous host identity must fail closed; the add-on must not select a winner opportunistically.

These rules make package duplication and installation topology observable failures rather than sources of divergent behavior.

### 8.3 Conceptual capability contract

The following elements define the conceptual host API. Names in this table are descriptive, not proposed exported identifiers.

| Contract element | Required behavior |
|---|---|
| Host API version | The global descriptor advertises supported versions. The add-on and host negotiate one version before registration and use only behavior defined for that version. |
| Register and unregister view | A compatible add-on can register and later unregister an additional view. Registration is atomic: a failed or rejected registration leaves no partially installed route. Unregistration removes the route and releases host-owned references to its callbacks. |
| View identity and metadata | Each view supplies a stable, package-qualified identifier, an accessible label, and an explicit order. Identifiers cannot collide with another registered view. The host controls the displayed ordering according to the negotiated contract and preserves one active route. |
| Render and digest lifecycle | The host requests view content through the registered render/digest contract, owns layout and cache invalidation, and may reuse content while the view's digest is unchanged. The view supplies product content and cache identity; it does not own host layout or cache state. |
| Active-view switching | The host owns the active-view state and switches only among the host default view and compatible registered views. A route change does not replace the main terminal workspace, and a failed switch leaves the previous valid route active. |
| Fixed pinned footer host | The host provides the fixed, bottom-pinned route-control region and keeps it separate from scrolling content. The control appears only when compatible additional views are registered; it includes the host default and the additional routes in the negotiated order, with exactly one active route. |
| Optional mouse and keyboard callbacks | A view may expose optional pointer and keyboard actions. The host owns focus, route activation, and terminal-key arbitration; callbacks run only for the applicable focused view and cannot override unrelated terminal shortcuts. |
| Disposal | Unregistration invokes a documented disposal path so the add-on can release timers, listeners, subprocess resources, and other view state. Disposal is idempotent from the host's perspective and must not damage other views or the default STATUS route. |
| Error isolation | Exceptions, rejected promises, invalid content, and failed callbacks from one view are contained by the host. They may produce a localized view error but must not corrupt layout, cache state, another view, or the original STATUS behavior. |

### 8.4 Upstream responsibilities

Upstream is responsible for the generic host and its operational invariants:

- Publish and validate the versioned capability descriptor and negotiate one API version per add-on registration.
- Keep the existing STATUS view as the default and preserve its content, ordering, empty/loading/error states, and supported interactions.
- Make the pinned route control visible only for a compatible set of additional views. Its generic hosting behavior must not encode Git, Files, GitHub, or attribution concepts.
- Own the sidebar layout, 50-column desktop reservation, narrow fallback coordination, rendering, digest cache, focus, scrolling, active-view state, and route-control presentation.
- Validate registration metadata and isolate failures from view callbacks. A rejected or failed add-on must not impair the default STATUS route.
- Remove host references and route metadata when a view is unregistered or disposed.
- Document the capability contract generically and test negotiation, registration, switching, pinned-footer behavior, cleanup, malformed metadata, duplicate publishers, and error isolation without product-specific fixtures.

### 8.5 Add-on responsibilities

The `sidebar-steroids` package is responsible for all product behavior behind the compatible host contract:

- Provide the GIT and FILES views, their stable identities, accessible labels, ordering, render/digest results, and optional interaction callbacks.
- Obtain local Git data through bounded, read-only queries; provide repository and worktree scope; and never use Git metadata as proof of agent attribution.
- Integrate explicit agent lifecycle evidence for attributed activity and render Git state separately from activity or source state.
- Provide an optional, read-only GitHub adapter as enrichment. Its absence, denial, timeout, or remote failure must not prevent local Git or FILES inspection.
- Perform compatibility negotiation and fail closed with actionable guidance before registering any product view.
- Own registration, unregistration, and disposal of its views and resources, together with the package's install, update, and removal lifecycle.

### 8.6 Process-level view

```text
Pi process / extension runtime
┌──────────────────────────────────────────────────────────────────────┐
│ Gentle Shell upstream                                                │
│                                                                      │
│  Global symbol registry                                              │
│    capability identity | host instance | supported API versions      │
│                                                                      │
│  Generic sidebar host                                                │
│    existing STATUS (default and unchanged)                           │
│    layout | render/digest cache | focus | fixed pinned footer       │
└──────────────────────────────┬───────────────────────────────────────┘
                               │ versioned handshake
                               │ register / unregister / render / dispose
┌──────────────────────────────▼───────────────────────────────────────┐
│ Separately loaded sidebar-steroids package module root                │
│                                                                      │
│  compatibility UX | GIT view | FILES view                           │
│  local Git queries | optional GitHub adapter | attributed activity   │
└──────────────────────────────────────────────────────────────────────┘
```

### 8.7 Trust and failure boundaries

| Boundary | Trusted assumption | Required containment |
|---|---|---|
| Host and capability descriptor | The upstream host is authoritative for layout and the negotiated API | Validate descriptor shape, identity, and versions; ignore unrelated globals; reject ambiguity rather than selecting an instance |
| Add-on view callbacks | The add-on is a separately installed product component, but its return values and exceptions are not allowed to compromise the host | Validate metadata and content, catch failures at callback boundaries, isolate a failed view, and keep STATUS and other routes usable |
| Git subprocess and filesystem data | Paths, branch names, commit subjects, and command output may be untrusted or malformed | Use bounded, sanitized, read-only collection and render failures only in the affected view; never interpolate untrusted values into shell commands |
| Optional GitHub response | Remote content is untrusted, optional, and potentially unavailable | Treat enrichment as unavailable on failure, redact sensitive details, and keep local views independent of remote availability |
| Package/module topology | Separate module roots are normal; upstream internals and duplicate capability instances are not valid integration paths | Use only the global handshake, reject deep imports and duplicate publishers, and fail closed with compatibility guidance |
| Lifecycle and cleanup | Registration may succeed or fail independently of product data collection | Make registration atomic, unregister before package removal, dispose resources idempotently, and preserve upstream health after removal |

A host absence, version mismatch, malformed descriptor, duplicate instance, or attempted deep-import bypass produces no partial product behavior. A product-view failure is confined to that view. A local Git or Files failure does not alter STATUS or the other add-on view, and optional GitHub failure does not disable local inspection.

### 8.8 Explicit exclusions and contribution boundaries

- **No runtime monkey-patching.** The add-on must not patch, wrap, or replace upstream functions or modules at runtime. All integration occurs through the negotiated public capability.
- **No broad shared-provider framework.** Upstream must not introduce a general dependency-injection, service-loader, or shared-provider system merely to support this add-on. The registry carries one small, capability-specific descriptor.
- **Generic upstream PR boundary.** The proposed upstream change is limited to the versioned discovery handshake, generic view registration and lifecycle, active-view coordination, pinned-footer hosting, error isolation, cleanup, and generic documentation/tests. It contains no Sidebar Steroids Git, Files, GitHub, attribution, or package implementation.
- **Dedicated-repository product boundary.** The `sidebar-steroids` source, product adapters, product fixtures, compatibility UX, attribution integration, and package lifecycle reside in the dedicated product repository. Upstream accepts only the generic capability contribution and does not own product releases.

## 9. Data and refresh model

This section defines the proposed data boundaries and refresh contract for the add-on. It does not claim that the host capability, snapshot collectors, activity-event contract, or GitHub adapter currently exists.

### 9.1 Scope identity and namespacing

Every repository-relative datum is owned by an explicit scope. A scope has the following identity:

| Identity component | Definition and use |
|---|---|
| Canonical root | The absolute, normalized, canonical path of the selected repository root or worktree root. It distinguishes two linked worktrees even though they share one Git common directory. |
| Git common-directory identity | The canonical path of the repository's Git common directory, as resolved from the selected root, together with the canonical root's identity. Together they identify a repository and its worktree root without relying on a display name. The common directory distinguishes worktrees of one repository from unrelated repositories that contain the same relative path. |
| Root-relative path | A normalized path relative to the canonical root, using the repository's path-separator conventions. The absolute path is never the cache or attribution key. |

The compound scope key is:

```text
scopeKey = canonicalRoot + gitCommonDirectoryIdentity + rootRelativePath
```

`canonicalRoot` identifies the selected worktree; `gitCommonDirectoryIdentity` identifies the repository family; and `rootRelativePath` identifies one path within that root. Therefore, `src/index.ts` in two different roots, or in two worktrees of one repository, has different scope keys. Repository-wide values use the same key with an empty path component. Branch, commit, and GitHub records use the repository key, while path state and activity use the full path key.

The implementation shall resolve the canonical root and common directory before assigning scope identity and shall reject paths that escape the selected root. It shall not use a basename, branch name, current directory alone, or a display label as an identity. A common-directory path is an identity input, not a permanent claim that a repository cannot move.

A renamed root or common directory produces a new scope key. The new scope may retain repository-wide Git data only after Git reports the same common directory under the new canonical paths; path attribution and path caches from the old key are not transferred automatically. A deleted, unreadable, or detached-from-repository root is marked missing or unavailable, its path cache is evicted, and its last committed snapshot may remain only as explicitly stale diagnostic context. It shall not be reused for a later root that happens to occupy the same path.

### 9.2 Snapshot model

Collection shall produce immutable, versioned snapshots. Rendering consumes a committed snapshot and never starts collection.

| Snapshot domain | Proposed contents | Bounds and semantics |
|---|---|---|
| Repository scope | Canonical root, common-directory identity, selected branch or detached HEAD, local branch list, registered worktrees, and the selected worktree's relationship to the repository | Branch and worktree counts are bounded and truncation is explicit. A branch is local state, not a remote-tracking claim. |
| Git working-tree state | Changed paths and their staged and unstaged Git states, including modified, new, and deleted paths | Path count and captured bytes are bounded. This domain establishes repository state only and contains no actor attribution. |
| Files tree | Bounded directory and file hierarchy rooted at the selected canonical root | Directory depth, entry count, and total retained size are bounded. Symlink traversal does not escape the selected root. |
| Commit history | Bounded recent commits, parents, and compact relationship summaries | Commit count, graph traversal depth, subject length, and retained bytes are bounded. It is a summary, not a graphical commit graph. |
| Ahead/behind | Optional counts for the selected branch and an explicit upstream reference | The field is either present with its comparison reference or absent. Missing upstream, unsupported Git operation, timeout, and offline conditions remain distinguishable. |
| Attributed activity | Explicit lifecycle records and their current or most recent completed path effects | Kept in a separate domain and bounded by evidence window, record count, and retained bytes. |
| GitHub enrichment | Optional read-only repository or pull-request context associated with a remote | Kept outside the local snapshot commit path. It never blocks or invalidates local Git and Files data. |

A snapshot commit is atomic: local Git state, Files state, and attributed activity are versioned independently but published together only after each requested collector has reached an allowed terminal state. A view may display a committed older snapshot while a refresh is in progress, but it shall label that snapshot as stale. It shall never present a partially collected snapshot as complete.

The Git snapshot and attributed-activity snapshot are related by scope and path, not by inference. A path may carry one Git-state value and one activity/source value; either domain may be absent without causing the other to be synthesized.

### 9.3 Attributed activity semantics

The proposed activity contract records lifecycle evidence, not filesystem observation. An activity record shall contain or reference:

| Field | Required meaning |
|---|---|
| Scope | The canonical scope key for the root or worktree in which the operation was reported. |
| Session | The stable identifier of the Pi session that observed or requested the operation. |
| Agent | The stable identifier of the agent or execution context responsible for the operation. |
| Path | A normalized root-relative path for the edit target. |
| Operation | A stable operation identifier plus an event sequence or lifecycle phase. |
| Evidence | The event type and source that supports start, completion, failure, or cancellation. |

Matching is exact within the same scope and path. ACTIVE/EDITING requires positive start evidence from the identified agent and session, and LAST EDITED requires positive completion evidence from that actor. Failed or cancelled operations do not establish completion. A missing completion event is not evidence that an operation remains active.

Git-observed changes without matching attributable lifecycle evidence are UNATTRIBUTED/UNKNOWN SOURCE. Timestamps, modification order, branch state, file proximity, and the existence of a modified path are not attribution evidence. Changing session, worktree, canonical root, or evidence window clears or re-evaluates path-level attribution rather than carrying it across scopes.

The activity evidence window shall be explicit. Records outside that window remain unavailable rather than being promoted to "last edited." If the source cannot identify a session or agent, the record may establish an anonymous operation but shall not identify an actor.

### 9.4 Refresh triggers and cadence

A refresh controller shall coalesce collection requests and publish snapshots outside the render path. The allowed triggers are:

| Trigger | Required behavior |
|---|---|
| Session or worktree change | Re-evaluate scope identity, select the new scope, evict nonmatching path data, and schedule one bounded local collection. A change to one scope does not refresh unrelated scopes. |
| Agent mutation or lifecycle event | Record the event in the attributed-activity domain and mark affected path state for re-evaluation. Schedule at most one coalesced local refresh for the affected scope after the event burst settles. The event does not by itself change the Git-state value. |
| Explicit refresh | Request a fresh bounded local collection for the selected scope, subject to the same concurrency, timeout, and output limits. The request may supersede a pending refresh for that scope. |
| Low-rate polling | Permit polling only while a local Git or Files view is active, its displayed data can change through means other than product events, and no fresher signal is available. The interval shall be configurable and low rate; polling stops when the view is inactive, the panel is not observable, or the data is not useful. Polling is not continuous filesystem observation. |

No render, route change, scroll, resize, focus movement, or digest comparison may spawn a Git command. A Git collection may run only from the refresh controller. Coalescing shall prevent concurrent collections for the same scope, while separate scopes may have bounded concurrency.

There is no filesystem watcher in this model. Agent lifecycle events cover only operations whose evidence source reports them. Changes made by other processes, editors, or Git commands may remain unobserved until an explicit refresh or permitted polling refresh, and the UI shall disclose that limitation through freshness state.

### 9.5 Cache, digest, invalidation, and failure states

The upstream host owns sidebar layout, render/digest caching, focus, and pinned-footer hosting. The add-on owns the data snapshot cache and the mapping from committed data to a view digest.

| Owner | Cache or digest responsibility |
|---|---|
| Upstream host | Reuse an unchanged rendered sidebar layout and coordinate active-view rendering. The host does not collect Git data. |
| Add-on snapshot cache | Retain bounded, immutable snapshots by scope key, along with collection status, collection time, and snapshot version. Cache entries are evicted by an explicit bound and least-recent-use policy. |
| Add-on view digest | Derive from the selected scope, committed snapshot version, view-local presentation state, and independently versioned GitHub and activity states. A digest match means that cached presentation may be reused; it does not authorize a Git command. |

A refresh request invalidates the affected scope's presentation digest immediately. A new snapshot is committed atomically and then produces a new digest. Activity events invalidate only the affected activity/path presentation; a GitHub response invalidates only the GitHub presentation. Route, scroll, and focus changes may alter view-local digest inputs but do not invalidate the local snapshot or start collection.

Each snapshot domain shall expose a bounded state:

| State | Meaning and presentation rule |
|---|---|
| FRESH | A complete snapshot for the current scope is committed and is within the configured freshness interval. |
| LOADING | The first snapshot is being collected; no complete snapshot is available yet. |
| STALE | A previous complete snapshot exists, but its age exceeds the freshness interval, a refresh is in progress, or evidence was invalidated. The age and last successful collection time shall be available. |
| PARTIAL | A required local sub-domain could not be collected. Only the domains actually collected are shown, and the missing domain is named. A partial result is not represented as fresh or complete. |
| ERROR | Collection failed for a reason such as unavailable repository, unsupported Git operation, permission denied, or malformed repository metadata. The failed operation is described at a safe category level. |
| TIMEOUT | Collection exceeded its elapsed-time bound and was terminated or abandoned according to the collector contract. A timed-out refresh does not erase the last complete snapshot; that snapshot remains stale. |
| UNAVAILABLE | The requested data source is intentionally absent, such as no configured GitHub provider, no upstream reference for ahead/behind, or an offline-only remote source. Local Git and Files remain usable. |

An explicit refresh clears the prior error only when a new collection begins or commits as applicable; it does not fabricate a successful state. Error and stale states are isolated to the affected domain and view.

All collection shall be bounded by configurable elapsed time, subprocess and network concurrency, per-command and total output bytes, retained snapshot bytes, path and branch counts, commit count, and tree depth. Over-limit output is truncated or rejected with an explicit diagnostic category. Collection shall not retain unbounded child processes, streams, or response bodies.

### 9.6 Local, offline, and optional GitHub behavior

Local Git and Files collection shall operate without credentials or network access. Failure of the optional GitHub adapter, a network outage, missing remote configuration, denial of consent, or GitHub rate limiting shall produce an UNAVAILABLE or ERROR state only in the GitHub domain. It shall not delay or invalidate a committed local snapshot, and it shall not suppress local Git, commit, worktree, status, or tree data.

The GitHub adapter shall be read-only and shall not be used to infer local Git state or attributed activity. Any remote request shall be separately bounded by timeout, response size, and retry limits; retries shall not amplify when the provider is unavailable. No local path shall be transmitted unless the user has explicitly enabled the relevant integration and the permitted payload excludes unnecessary sensitive paths.

### 9.7 Proposed data flow

```text
session/worktree change ─┐
agent lifecycle event ────┼─> coalesced refresh controller ─> bounded local collectors
explicit refresh ─────────┤                                  ├─> Git snapshot
permitted low-rate poll ──┘                                  ├─> Files snapshot
                                                           └─> attributed activity
                                                                      │
                                                                      v
                                                        validate and atomically commit
                                                                      │
                                                                      v
                                       scoped snapshot cache <── versioned view digest
                                              │                           │
                                              └───────────────> render <──┘
                                                                    │
                                                                    v
                                                          host layout reuse

optional GitHub request ─> independent bounded adapter ─> independent GitHub state/digest
local snapshot commit ────────────────────────────────────────X no GitHub dependency
render ─> committed cache read ────────────────────────────────X no Git command
```

| Transition | Data-flow rule |
|---|---|
| Trigger to collection | Coalesce by scope; apply concurrency, timeout, and output bounds before reading results. |
| Collector to commit | Validate domain and scope; commit complete or explicitly partial domains atomically; never mix scopes. |
| Commit to render | Render only the committed snapshot version. Cache and digest lookups do not perform I/O. |
| Render to Git command | No transition is permitted. Git collection occurs only through the refresh controller. |
| Local snapshot to GitHub | No dependency is permitted. GitHub enrichment is independently versioned and independently invalidated. |

## 10. Installability and lifecycle

### 10.1 Package boundary and manifest

Sidebar Steroids shall be released as a separate npm Pi extension package from the dedicated Sidebar Steroids repository, provisionally `andrikonbeat/sidebar-steroids`. The add-on shall not be published as, or installed by mutating, the upstream `gentle-pi` package.

The published package manifest shall declare the extension entry point through `pi.extensions`. The entry point must be resolvable from the published package and shall load only the add-on implementation and its declared runtime dependencies. Runtime libraries required by the extension shall appear in `dependencies`. Any package supplied by the Pi host, including `gentle-pi` where applicable, shall appear only in `peerDependencies`; the add-on shall not bundle, vendor, or install a second copy of a host-provided package. Development-only tooling shall remain in `devDependencies`.

The provisional repository and package identity are subject to registry, namespace, and trademark review. The name `sidebar-steroids` is not a registry reservation, and this PRD does not claim that a repository or npm package currently exists.

### 10.2 Regular Pi package commands and standalone launcher home

The regular Pi package lifecycle is the authoritative command surface:

| Action | Regular Pi command | Expected result |
|---|---|---|
| Install | `pi install npm:<package>` | The published add-on is installed into the active Pi home and is eligible for the next extension load. |
| Update | `pi update --extensions` | Installed extension packages, including Sidebar Steroids, are updated through Pi's normal package lifecycle. |
| Remove | `pi remove <source>` | The package identified by the source shown by Pi is removed from the active Pi home. |
| Inspect | `pi list` | The user can verify whether the add-on is installed and which source Pi manages. |

Here, `<package>` means the actual npm package name selected after publication review, and `<source>` means the source identifier reported by Pi for the installed package. This document does not substitute a guessed final package name for either placeholder.

The standalone Gentle Shell launcher owns a separate Pi home and uses that home for its Pi package state. Installation, update, removal, and inspection for a standalone-launcher deployment shall therefore be performed against that launcher-owned Pi home. Installing the add-on into a different Pi home must not be represented as installing it into the standalone launcher.

Only the launcher's documented forwarding behavior may be used for equivalent Pi package operations. This section defines no undocumented launcher command spelling. If the standalone launcher does not currently provide a documented, supported forwarding path for one or more install, update, remove, or list operations, launcher-specific install UX is a product requirement for the launcher maintainers; that requirement must include unambiguous selection of the launcher-owned Pi home. Until such support exists, the lifecycle operation is unsupported for that launcher surface rather than being presented as working.

The add-on itself shall never mutate, replace, patch, or copy the launcher-owned `gentle-pi` installation. It extends a compatible host through the negotiated capability contract only.

### 10.3 Compatibility handshake and fail-closed behavior

Package activation shall perform the versioned capability handshake described in Section 8.2 before registering any product view or collecting data. The add-on shall resolve the protocol-qualified global capability key across separately loaded Pi package module roots, validate the descriptor shape, capability identity, host-instance identity, and protocol version, and select the highest mutually supported host API version. A deep import, a duplicate publisher, or a second resolved host instance is not an acceptable fallback.

If the host API is absent, malformed, incompatible, or ambiguous, activation shall fail closed:

- The add-on shall register no GIT or FILES product view and shall add no product route control.
- It shall not monkey-patch, wrap, replace, or bypass the host implementation.
- It shall not crash the Pi process or interrupt startup.
- The original Gentle Shell STATUS view shall remain available, selected by default, and unchanged.
- The add-on shall emit one actionable diagnostic per incompatible activation or compatibility-state change. The diagnostic shall identify the missing or incompatible generic sidebar host capability, state the add-on's supported API range and the detected range when available, and direct the user to install or upgrade to a compatible Gentle Shell host. Repeated rendering or route activity shall not repeat the same diagnostic.
- A host upgrade shall succeed only when it advertises a mutually supported API version. A newer host with no common version remains incompatible and shall fail closed rather than using unversioned behavior.

No product data collection begins until negotiation succeeds. A failed negotiation therefore leaves the host and its default experience healthy even if the package remains installed.

### 10.4 Enable, disable, configuration, and persistence

A successfully installed add-on is enabled by default when a compatible host is present. On an incompatible or absent host, the package may remain installed for later use, but its product views remain disabled and fail closed as defined above.

Disabling the add-on shall prevent product-view registration and background collection, unregister any existing product views, and dispose the add-on's active resources. It shall be performed through a documented Pi or launcher enable/disable mechanism. If no such mechanism exists for the relevant installation surface, dedicated enable/disable UX is a product requirement; this PRD does not invent an unsupported command.

Configuration is owned by the add-on and scoped as follows:

| Setting | Scope and persistence rule |
|---|---|
| Compatibility range | Fixed by the installed add-on version and checked at every activation. It is not user-overridable to bypass negotiation. |
| Enabled state | Stored in the active Pi home when the platform supports persistent per-extension enablement. Disabling does not uninstall the package or delete user repository data. |
| Selected route | Persisted in the active Pi home when the user has made an explicit selection; otherwise STATUS is selected. An absent or invalid saved selection falls back to STATUS. |
| Refresh, polling, and display preferences | Stored in the active Pi home, not in a Git repository. They survive process restarts and compatible package updates. |
| Repository and worktree selection | Session or workspace scoped. It is not inferred from another repository's saved route or attribution state. |
| Optional GitHub consent | Explicit and local to the active Pi home. It is never inferred from repository remotes, credentials, or prior network activity. |

The standalone launcher uses the same rules with its launcher-owned Pi home. Configuration migration across an incompatible add-on version shall use explicit defaults rather than silently applying settings whose schema is not understood.

### 10.5 Update lifecycle and changelog behavior

`pi update --extensions` manages the package in the active Pi home. After an update, a fresh Pi or launcher process shall perform a new compatibility handshake and activate the updated add-on instance; an in-process hot replacement is not required. An update shall not modify the launcher-owned `gentle-pi` package or bypass host compatibility.

Package updates shall preserve compatible user configuration, invalidate caches whose schema or package version is no longer valid, and re-evaluate repository, activity, and GitHub state under their existing scope rules. They shall not preserve attribution across incompatible scope identities or carry stale Git state into a changed root.

Each published version shall include a changelog that identifies user-visible changes, compatibility requirements, configuration migrations, and any changes to removal behavior. Breaking changes to the generic host API range or configuration schema shall be called out explicitly. Package-manager output remains the source of truth for whether an update was fetched or applied; the changelog explains the resulting package behavior.

### 10.6 Removal safety

Removal through `pi remove <source>` shall leave Pi, the standalone launcher, and the original Gentle Shell sidebar healthy. Before the add-on's package code becomes unavailable, its removal lifecycle shall unregister its product views and dispose all resources it owns, including refresh timers, polling schedules, child Git processes, event listeners, host subscriptions, pending callbacks, and retained streams. Disposal shall be idempotent and shall not affect the original STATUS view or unrelated extensions.

Removal shall clean the add-on's own transient cache and other removable add-on-owned data in the active Pi home where appropriate. If the package's documented policy retains user preferences for a later reinstall, that exception shall be explicit; stale caches keyed by an incompatible package schema shall still be removed. The add-on shall not delete or modify the user's repositories, worktrees, working-tree contents, Git index, refs, commit history, remotes, credentials, or other user data. It shall not mutate the launcher-owned `gentle-pi` installation.

After removal, a fresh Pi or launcher session shall show no Sidebar Steroids route, shall retain the original STATUS behavior, and shall not retain an orphaned process, timer, subscription, or host registration. A removal smoke test shall compare repository state before and after removal and shall verify host health.

### 10.7 Release 1 behavior and installation acceptance

Release 1 remains inspection-only. Installing, enabling, updating, configuring, or disabling the add-on shall not grant permission for mutating Git operations. The GIT and FILES views perform bounded local reads; optional GitHub integration is read-only enrichment. Release 1 shall not stage, commit, checkout, merge, reset, delete a branch, push, pull, or otherwise mutate a local or remote repository.

Installation and package updates may require access to the npm registry. After installation, local Git and FILES use shall be local-first and shall work with the network unavailable and no credentials configured. GitHub-only information shall be shown as unavailable without a credential prompt, and its absence shall not block local inspection.

The release installation smoke acceptance shall use an isolated Pi home and a compatible host fixture and shall cover this sequence:

1. Confirm that the candidate is absent from the isolated home, then run `pi install npm:<package>` against that home.
2. Run `pi list` and verify that Pi manages the installed candidate by the expected source.
3. Start Pi, verify a successful compatibility handshake, confirm that STATUS is the unchanged default, and exercise the GIT and FILES routes and local-only states.
4. Run `pi update --extensions`, start a fresh process, verify the intended installed version and changelog, and repeat the route and STATUS regression checks.
5. Run `pi remove <source>`, then run `pi list` and verify that the candidate is absent.
6. Start a fresh process, verify that Pi and the original STATUS view remain healthy, verify that no add-on route or orphaned resource remains, and compare the fixture's Git state before and after the lifecycle.

The same acceptance shall be repeated against the standalone launcher's Pi home only through launcher forwarding behavior that is documented and supported. It shall be separately recorded as a product requirement for any launcher lifecycle operation that lacks that support. A package that cannot complete install, update, removal, host-health, and offline local-inspection acceptance shall not be advertised as installable.

## 11. Security and privacy

This section defines the proposed security and privacy contract for Release 1. These controls are requirements, not claims that the add-on, Git collectors, or GitHub adapter are already implemented.

### 11.1 Threat and trust boundaries

| Boundary | Trust assumption | Required containment |
|---|---|---|
| Upstream host | The upstream host is authoritative for the negotiated versioned capability, layout, and lifecycle, but is not trusted with product data collection | Validate capability identity, instance, shape, and version; reject ambiguous or incompatible hosts; isolate add-on failures from STATUS and other views |
| Dedicated add-on package | The separately installed package owns product behavior but crosses a package and process boundary | Use only the public capability contract, validate all inputs and results, confine resources to the selected scope, and dispose registrations, timers, streams, and child processes cleanly |
| Local Git repository and worktrees | Repository metadata, paths, refs, configuration, commit messages, diffs, filenames, and symlinks are untrusted input | Use a read-only command policy, sanitize the Git environment, bound and redact output, canonicalize paths within the selected root, and respect linked-worktree boundaries |
| Optional GitHub/CLI integration | Remote responses and any external CLI are optional, untrusted, and independently unavailable; an invoked CLI is not allowed to expand the add-on's authority | Require explicit enablement and least-privilege read access; bound time, output, concurrency, and retries; never prompt automatically or persist authorization material; degrade without affecting local views |
| User configuration | Configuration may be malformed, stale, or unexpectedly sensitive | Validate against a known schema, apply conservative bounds, treat paths and CLI arguments as data, never execute configured strings, and keep preferences in the active Pi home rather than a repository |

### 11.2 Required Release 1 controls

| Control | Required behavior | Acceptance evidence |
|---|---|---|
| Credentials and secrets | The add-on shall not access, enumerate, read, print, log, transmit, or persist credentials, tokens, or credential-helper material. It shall not enable a Git credential helper or trigger an automatic credential prompt. Local Git and Files inspection shall require no credentials or network access. | **NFR-SEC-001**, **NFR-PRIV-001**, **AC-INSTALL-003** |
| Git environment | Each Git child process shall receive a minimal, sanitized environment. Ambiguous Git variables that could redirect the repository, index, object store, common directory, configuration, ceiling, or execution context shall not be inherited ambiguously; repository and worktree scope shall be supplied explicitly by validated arguments. | **NFR-SEC-001**, **AC-GIT-001** |
| Process invocation | Untrusted values shall never be interpolated into a shell command or command string. Git shall be invoked through argument arrays with direct process spawning, without a shell. Every child shall have bounded output, an elapsed-time timeout, cancellation support, and deterministic cleanup; timed-out or cancelled children shall not remain running. | **NFR-SEC-001**, **NFR-RES-001** |
| Git and remote command boundary | Release 1 permits only an audited set of local inspection commands. It shall not stage, commit, push, pull, merge, fetch, update refs, create or delete branches or worktrees, reset, clean, check out, force an operation, invoke credential helpers, or perform an arbitrary remote action. Conventional fetch is excluded because it writes local Git metadata. A later proposal may define a harmless read-only fetch-like remote query only if it is explicitly approved and demonstrably writes no refs, `FETCH_HEAD`, index, worktree, configuration, or other persistent Git state; that exception is not part of Release 1. | **FR-GIT-005**, **AC-GIT-005**, **NFR-SEC-001** |
| Optional GitHub adapter | The adapter shall be read-only, explicitly enabled by the user, and request only the metadata needed for the displayed enrichment with least privilege. It shall apply timeouts, response-size and retry bounds, and specific rate-limit and error handling. It shall not automatically prompt for credentials or persist tokens. Its failure, denial, timeout, or rate limit shall affect only the GitHub domain and shall not block or invalidate local Git, Files, or attributed-activity snapshots. | **FR-INSTALL-003**, **AC-INSTALL-003**, **NFR-PRIV-001**, **NFR-REL-001** |
| Path and symlink safety | Every filesystem path shall be canonicalized and verified to remain within the selected canonical root or worktree before access. Traversal, `..`, alternate path syntax, and symlink targets shall not escape that root; symlink cycles and links into another worktree or unrelated repository shall be rejected or clearly treated as non-traversable. Files collection shall not cross a worktree boundary through a link. | **FR-FILES-001**, **AC-FILES-001**, **NFR-SEC-001** |
| Display privacy | The views shall show only metadata intended by the user for the selected scope. If derived content could contain sensitive values, it shall be redacted before display or diagnostics. Repository content shall not be persisted unless persistence is necessary for an explicitly documented feature; otherwise only bounded, session-scoped or cache-scoped derived data is retained. User preferences shall not be written into the repository. | **NFR-PRIV-001**, **NFR-OBS-001** |
| Resource and hostile-input limits | Collection shall bound per-command and total output, elapsed time, concurrent work, retained snapshot bytes, file and directory counts, directory depth, path length, branch and worktree counts, commit count, commit-graph traversal depth, and subject or diff size. Malformed output, enormous histories, deep trees, symlink loops, and other hostile repository conditions shall be truncated, rejected, or reported by category rather than consumed without limit. Cache and session storage shall have explicit eviction bounds, and cancelled or failed collection shall release processes, streams, file descriptors, and temporary resources. | **NFR-RES-001**, **NFR-REL-001** |

### 11.3 Privacy and independent degradation

Local Git and Files operations are local-first and remain usable offline without credentials. The optional GitHub/CLI adapter is an independent enrichment boundary: it may be disabled, denied, offline, rate-limited, or erroneous without affecting STATUS, local Git, Files, or attribution state. It may not infer local Git state or attributed activity, and it may not transmit local paths unless the user has explicitly enabled the integration and the request's payload excludes unnecessary path data.

Diagnostics and user-visible errors shall use stable category labels rather than raw environment values, credential material, complete command lines, remote response bodies, or unrestricted filesystem paths. Redaction shall occur before data reaches logs, notifications, caches, or the rendered view.

### 11.4 Future write-operation consent boundary

Release 1 contains no write operation. Any later local or remote write proposal requires explicit user confirmation of the concrete action, target, and scope; narrowly scoped authorization that cannot be inferred from a general read or GitHub-enablement consent; least-privilege permissions; an updated threat model and privacy analysis; and a separate release or PRD defining rollback and failure behavior. A later write release must not be treated as an implicit extension of Release 1 or its existing consent.

## 12. Verification

Verification is prospective: the generic upstream host capability and the dedicated Sidebar Steroids package are planned, not implemented by this PRD. The checks below define the evidence required for a future release; they do not assert that an implementation or test currently exists.

### 12.1 Verification strategy and artifact boundaries

Verification spans two artifacts that must be reviewed and released independently, followed by an integration release gate.

| Artifact | Verification scope | Evidence owner | Exit condition |
|---|---|---|---|
| Generic upstream host contribution | Capability publication and discovery, version negotiation, generic view registration, route coordination, pinned-footer hosting, layout, render/digest lifecycle, focus, disposal, error isolation, and preservation of the original STATUS rail | Upstream maintainer and independent upstream reviewer | Focused upstream tests pass against the pinned base and candidate change; the public capability remains product-agnostic and documented; no exact STATUS regression is observed. |
| Dedicated Sidebar Steroids add-on | GIT, FILES, repository/worktree scope, local Git and Files collection, snapshots and cache, attribution, optional GitHub degradation, add-on rendering, compatibility UX, package lifecycle, and cleanup | Add-on maintainer and independent product reviewer | Focused add-on tests pass against a supported upstream host candidate; package/install smoke passes; security, offline, compatibility, and removal evidence is complete. |
| Integrated release candidate | The built upstream host candidate and packed add-on package loaded together in an isolated Pi home, including update and removal | Release operator and verification owner | The upstream and add-on evidence bundles are individually passing, the integrated lifecycle sequence passes, and no release-gate failure is waived or hidden. |

An upstream test result is not evidence for add-on behavior, and an add-on test result is not evidence for the generic host contract. The integration run verifies that the two independently tested artifacts negotiate and operate together; it does not replace either artifact's own suite.

### 12.2 Required test layers

| Layer | Artifact | Required coverage | Evidence form |
|---|---|---|---|
| Upstream unit and contract tests | Upstream | Descriptor identity and shape, stable cross-module-root discovery, supported-version negotiation, duplicate or ambiguous publisher rejection, atomic registration/unregistration, metadata validation, callback boundaries, and idempotent disposal | Focused test output plus assertions for the negotiated version and host-instance identity |
| Upstream layout and render tests | Upstream | Exact 50-column desktop reservation, 140-column breakpoint, narrow fallback, pinned footer, scrolling, route visibility, focus order, and preservation of the active route across resize/fullscreen transitions | Layout measurements and deterministic render snapshots for desktop, narrow, scroll, resize, and mode-transition fixtures |
| Upstream digest lifecycle tests | Upstream | Digest reuse, invalidation after a changed view digest, route/scroll/focus behavior, and separation between host cache reuse and add-on data collection | Render and digest trace showing no redundant layout work for an unchanged digest |
| Upstream disposal and error-isolation tests | Upstream | Exceptions, rejected promises, invalid content, rejected registrations, failed callbacks, and unregistration/disposal cannot damage layout, cache state, another view, or the default STATUS route | Fault-injection transcripts and post-fault host-health assertions |
| Exact STATUS regression protection | Upstream and add-on | The original STATUS rail is compared fixture-for-fixture with the add-on absent and installed: content, ordering, empty/loading/error states, and supported interactions | Approved baseline fixtures and exact installed-state comparisons; no fuzzy normalization of user-visible differences |
| Add-on Git parsing unit tests | Add-on | Branch, worktree, status, diff, history, parent, ahead/behind, and malformed/unsupported Git output parsing | Parser fixtures with expected normalized values and explicit error categories |
| Add-on snapshot, cache, and namespacing unit tests | Add-on | Immutable snapshot commit, domain independence, bounded retention and eviction, invalidation, canonical-root/common-directory/root-relative scope keys, worktree separation, and stale-scope eviction | Snapshot/version transitions, cache-accounting assertions, and scope-key collision fixtures |
| Add-on attribution unit tests | Add-on | Start/completion/failure/cancellation evidence, actor/session matching, evidence-window limits, unknown source, and the complete Git-state/activity-state matrix | Named lifecycle fixtures with one expected Git-state value and one expected activity/source value per path |
| Add-on render tests | Add-on | GIT content, FILES tree and annotations, the STATUS/GIT/FILES control, active-route text, loading/empty/error/stale/partial/offline/unavailable states, truncation, and accessibility labels | Deterministic render snapshots and semantic assertions |
| Add-on integration tests | Add-on | Switching among STATUS/GIT/FILES, entering/leaving fullscreen, crossing the resize boundary, selecting repository and linked-worktree roots, and switching roots without carrying scope data | Route-transition and scope-transition transcripts with final-state assertions |
| Host capability handshake tests | Add-on and upstream together | Discovery across separate package module roots, exact v1 negotiation, malformed or ambiguous descriptors, duplicate publishers, unsupported versions, and no deep-import fallback | Handshake trace, negotiated API version, registration result, and fail-closed behavior |
| Offline and degradation tests | Add-on | No network or credentials for local Git/FILES, denied or failed optional GitHub access, absent upstream reference, unsupported Git operation, timeout, partial collection, and stale data | Request interception, credential-access spies, local-state assertions, and stable diagnostic categories |
| Security, path, and process-boundary tests | Add-on | Direct argument spawning without a shell, sanitized Git environment, read-only command allowlist, output/time bounds, cancellation, root confinement, traversal and symlink rejection, and diagnostic redaction | Command/process audit, environment capture, pre/post repository digests, path fixtures, and sanitized output review |
| Removal and cleanup tests | Add-on and integrated package | Unregister-before-removal, timer/listener/stream/process cleanup, cache and stale-scope cleanup, idempotent disposal, no orphaned route or background resource, and post-removal host health | Resource-lifecycle trace, process/timer checks, package listing, and healthy fresh Pi/Gentle Shell session |

### 12.3 Package and installation smoke

The smoke test uses the packed dedicated package, not an unpublished source checkout, and an isolated temporary Pi home that cannot affect a developer or user Pi home. The final package identity, version, registry, and launcher's supported forwarding surface remain release-time inputs.

| Step | Check | Pass condition |
|---|---|---|
| 1. Build | Build the candidate from the recorded dedicated-repository revision in a clean environment. | The build is reproducible from the recorded revision, completes within its defined limit, and produces the candidate version under test. |
| 2. Pack and inspect | Generate the npm tarball, inspect its complete file list and `package.json`, and resolve the declared extension entry point from the tarball layout. | The manifest declares a resolvable `pi.extensions` entry; runtime libraries are in `dependencies`; host-provided packages are peer dependencies only; development-only files are excluded; no duplicate host implementation, secret, credential, or unintended upstream patch is published. The file list matches the reviewed package allowlist. |
| 3. Isolated install | Install the candidate into a new temporary Pi home, then list managed packages. | `pi install npm:<package>` succeeds only in the isolated home; `pi list` reports the expected source and version; no other Pi home or installed host package is modified. |
| 4. Load and handshake | Start a fresh Pi/Gentle Shell process in the isolated home, load the extension, and exercise the supported host. | The handshake selects supported v1; the original STATUS remains the unchanged default; the add-on control, GIT, and FILES load; local inspection succeeds without network access or credentials. |
| 5. Update | Update the managed extension, start a fresh process, and repeat STATUS and route checks. | `pi update --extensions` reaches the intended version and changelog; the new process repeats a successful handshake; compatible configuration is preserved; stale schema/cache data is invalidated; STATUS remains exact and GIT/FILES remain functional. |
| 6. Remove | Remove the package through Pi's package manager and list managed packages again. | `pi remove <source>` completes; the package is absent from the isolated home's list; no add-on route remains in a fresh process; no orphaned timer, listener, stream, or child process remains. |
| 7. Post-removal health | Exercise Pi and the original Gentle Shell sidebar and compare fixture repository state before and after the lifecycle. | Pi and Gentle Shell start and render normally; the original STATUS behavior is unchanged; fixture Git state, worktree, index, refs, and files are unchanged. The same lifecycle is recorded separately for the standalone launcher's Pi home only through documented, supported forwarding behavior. |

The evidence bundle must contain the tarball file listing, manifest snapshot, Pi package listings before and after each lifecycle step, handshake and route transcripts, and pre/post repository-state digests. Package-manager output is authoritative for whether installation, update, or removal occurred.

### 12.4 Host compatibility matrix

| Host condition | Expected activation behavior | Expected user and data behavior | Pass evidence |
|---|---|---|---|
| Host API absent | Fail closed before product registration or data collection | The add-on exposes no GIT/FILES route or extra control; one actionable compatibility diagnostic identifies the missing capability and supported range; the original STATUS remains available, selected, and unchanged | Absent-capability activation fixture plus fresh-process host-health check |
| Exact supported v1 | Negotiate and select v1, then register the add-on views atomically | STATUS remains the default; the pinned STATUS/GIT/FILES control and product views are usable; all operations use only the v1 contract | Descriptor fixture advertising v1, handshake trace showing v1, registration/render/unregister results |
| Newer but incompatible version | Fail closed when there is no mutually supported version; never fall back to an unversioned path | No product view or route is partially exposed; the diagnostic reports the add-on's supported range and detected range when available; STATUS is unchanged | Host advertising only an incompatible newer version, activation and host-health fixtures |
| Future unknown version or unknown metadata shape | Treat the descriptor as incompatible and fail closed without probing or guessing behavior | No registration, collection, or partial route occurs; one actionable diagnostic identifies the unknown/incompatible contract; STATUS is unchanged | Future-version and malformed/unknown-shape descriptor fixtures, including no deep-import fallback |

A compatible host plus an incompatible add-on is the inverse failure case and follows the same fail-closed rule. A host upgrade is accepted only when a common supported version is advertised.

### 12.5 Performance, cadence, and resource checks

| Check | Method | Pass condition |
|---|---|---|
| No Git command during render | Spy on process creation while rendering an unchanged, changed, scrolled, resized, and refocused GIT/FILES view | Every render path spawns zero Git processes; collection occurs only in a refresh job |
| Bounded Git calls and cadence | Trigger route changes, lifecycle-event bursts, scope changes, explicit refreshes, and permitted polling against the refresh controller | Refreshes are coalesced by scope; a burst causes no unbounded fan-out; concurrent collection, polling frequency, and subprocess count stay within configured limits; inactive views do not poll |
| Digest-only redraw | Record host layout/render work and add-on digest inputs for unchanged and changed snapshots | An unchanged digest reuses cached presentation and causes no Git collection or redundant layout rebuild; a changed digest redraws only the affected presentation |
| Refresh latency | Run the instrumented reference-repository and file benchmark, excluding render and optional remote time | At least 95% of explicit local Git and Files refreshes complete within 1 second, with raw samples and the measured 95th percentile retained |
| Output, time, and retained-resource bounds | Exercise overlong output, deep trees, many branches/worktrees/commits/paths, large diffs, slow commands, cancellation, and hostile fixtures | Per-command and total output, elapsed time, concurrency, retained snapshot bytes, tree depth/counts, path/branch/worktree/commit counts, diff size, and subject length remain within configured bounds; over-limit data is explicitly truncated or rejected |
| No background process leak | Cancel and fail collections, deactivate views, unregister views, and remove the package while measuring child processes, streams, descriptors, timers, and listeners | Timed-out/cancelled children are terminated; failed collections release resources; inactive or removed add-ons leave no background process, timer, stream, descriptor, or subscription |

### 12.6 Security, path, and process-boundary evidence

Security checks use hostile fixtures and audit the actual process boundary, not only mock expectations:

- The command audit proves that Git is spawned directly with argument arrays and no shell, that only the approved read-only command set is possible, and that no command, index, refs, worktree, remote, or file content is mutated.
- The environment capture proves that ambiguous Git redirection variables and ambient credentials are excluded and that scope is supplied through validated arguments.
- Output, timeout, cancellation, and total-size fixtures prove that partial or oversized output cannot create an unbounded retained snapshot or leave a child process running.
- Path fixtures cover canonicalization, `..`, alternate separators, symlink targets, cycles, and links into another worktree or unrelated repository; no read or displayed path escapes the selected root.
- Credential and network spies prove that core inspection neither enumerates nor reads credential stores and that denied, offline, or unconfigured optional GitHub access neither prompts nor sends a request.
- Remote-consent fixtures prove that only explicitly enabled enrichment can make a request and that an approved request excludes unnecessary local paths.
- Diagnostic review proves that errors, logs, notifications, and caches contain stable categories and redacted paths rather than secrets, raw remote bodies, or unrestricted filesystem detail.

Any write-like command, shell interpolation, credential access, root escape, unbounded collection, or surviving child process is a release failure even if the user-visible feature appears correct.

### 12.7 Attribution-manifest validation

The upstream evidence set is validated independently of runtime behavior.

| Validation | Method | Pass condition |
|---|---|---|
| JSON parse and structure | Parse `docs/sidebar-steroids/upstream-manifest.json` and validate it against the manifest's declared structure and referenced paths | The file is valid JSON, contains no duplicate or unresolved records, and every referenced local evidence file exists |
| Planned versus implemented accuracy | Compare each manifest status with the actual candidate change and review record | A planned item is not represented as implemented; an implemented item has matching code/evidence and review status; no stale or contradictory status remains |
| Changed-file completeness | Compare the manifest's changed-file set bidirectionally with the candidate diff against pinned base `bac4c4a3ffc4e8455548691dcd820d60fd3c58f6`, accounting for the manifest's declared scope | Every in-scope changed file is declared and every declared file is either changed or explicitly classified; omissions and false claims fail validation |
| License and trademark | Review `docs/sidebar-steroids/UPSTREAM_ATTRIBUTION.md`, upstream license notices, package metadata, and registry/trademark review evidence | Upstream authorship and MIT notices are preserved, the contribution is explicitly attributable, and the provisional package/repository identity is not presented as reserved, approved, or endorsed |

The validator records both the expected and observed file sets and statuses. Manual review may explain a discrepancy but cannot convert a mismatch into a pass.

### 12.8 Evidence recording and pass/fail semantics

Each verification run records the artifact and revision, package/version or host API version, fixture identity, relevant Git version and platform, exact command or test identifier, start/end time, sanitized logs, expected result, observed result, and an explicit `PASS`, `FAIL`, or `UNAVAILABLE` status. Machine-readable test results are retained with human-readable traces for render, handshake, performance, package, and manifest checks.

Release semantics are strict:

- A requirement passes only when its mapped acceptance criterion has direct, repeatable evidence that meets the stated condition. Visual plausibility, a partial run, or a reviewer assertion is not a substitute.
- Any failing required check fails the affected artifact and the integrated release gate. A retry may diagnose a flaky test, but it does not erase the first failure; release requires a clean, recorded run after the defect is resolved.
- An unavailable, skipped, missing, blocked, or not-yet-implemented required check is `UNAVAILABLE` and is a release failure. It is never silently skipped or counted as neutral.
- Only checks formally outside the Release 1 matrix may be informational. Any exception to a required check requires an explicit release-owner decision recorded with the evidence bundle and cannot authorize publication while the required check remains unavailable.
- Performance evidence must include raw measurements and the calculated threshold result. Security, compatibility, package lifecycle, removal, and exact STATUS regression evidence cannot be waived by a functional smoke pass.
- The upstream and add-on bundles must each be passing before the integrated package/install smoke is eligible to pass. The release sign-off records the final status for every FR and NFR in the matrix below.

### 12.9 Proposed future focused checks

The following are command templates for the future implementation repositories, not commands executed by this documentation unit and not evidence of current implementation or passing behavior. The placeholders must resolve to focused test files or supported lifecycle commands when the artifacts exist.

| Purpose | Proposed future command |
|---|---|
| Upstream capability, layout, digest, disposal, and error-isolation tests | `npm test -- <upstream-focused-sidebar-host-test-files>` |
| Exact upstream STATUS regression | `npm test -- <upstream-status-regression-fixture-files>` |
| Add-on Git parsing, snapshot, cache, namespacing, and attribution units | `npm test -- <addon-focused-unit-test-files>` |
| Add-on render and integration tests | `npm test -- <addon-focused-render-integration-test-files>` |
| Attribution-manifest JSON parse | `node -e "JSON.parse(require('node:fs').readFileSync('docs/sidebar-steroids/upstream-manifest.json', 'utf8'))"` |
| Pinned-base changed-file comparison | `git diff --name-status bac4c4a3ffc4e8455548691dcd820d60fd3c58f6 -- <attribution-scope-paths>` |
| Dedicated package build and pack inspection | `<dedicated-addon-build-command> && npm pack --dry-run` |
| Isolated install, list, update, and remove | `<isolated-pi-home-prefix> pi install npm:<package>`; `<isolated-pi-home-prefix> pi list`; `<isolated-pi-home-prefix> pi update --extensions`; `<isolated-pi-home-prefix> pi remove <source>`; `<isolated-pi-home-prefix> pi list` |

A future command template is acceptable only when it maps to a recorded result and the relevant matrix row. Broad suites are not a substitute for the focused evidence identified here.

### 12.10 Requirements-to-evidence acceptance matrix

Every functional requirement retains its Section 6 acceptance-criterion ID. The matrix records test type, required evidence, and the condition that constitutes a pass without repeating the requirement prose.

#### Functional requirements

| Requirement | Acceptance criterion | Test type | Required evidence | Pass condition |
|---|---|---|---|---|
| FR-STATUS-001 | AC-STATUS-001 | Exact upstream differential plus add-on render/interaction regression | Baseline/installed fixture pairs, exact content and ordering snapshots, state and interaction transcripts | Every baseline fixture and supported interaction is identical with the add-on installed and STATUS selected |
| FR-STATUS-002 | AC-STATUS-002 | Route-state and host-mode integration | Initial, route-change, resize, and fullscreen transition traces | Fresh state starts on STATUS; an explicit selection survives each applicable transition; no saved selection falls back to STATUS |
| FR-STATUS-003 | AC-STATUS-003 | Upstream layout/render plus keyboard/pointer tests | Desktop and narrow geometry, scroll snapshots, keyboard/pointer parity traces | All three routes are reachable in both presentations, exactly one is active, and the control remains visible and non-pointer-only |
| FR-GIT-001 | AC-GIT-001 | Add-on Git unit/integration with multi-root and worktree fixtures | Branch/worktree/scope output, command trace, pre/post repository digests | Correct local scope, branches, and worktrees are shown; no repository, branch, worktree, index, or file state changes |
| FR-GIT-002 | AC-GIT-002 | Git fixture, diff render, and repository-state comparison | MODIFIED/NEW/DELETED snapshots, read-only diff output, pre/post digests | Correct states and paths render; the available diff is read-only; working-tree and index state are unchanged |
| FR-GIT-003 | AC-GIT-003 | Known-history unit/render fixture | Commit/parent ordering output and explicit boundary/truncation snapshot | Known commits and parents appear in bounded order and truncation is explicit |
| FR-GIT-004 | AC-GIT-004 | Process-spy around render and refresh | Render/refresh call trace, collection-job count, snapshot-version trace | Every repeated render spawns zero Git processes; one explicit refresh produces one bounded collection and later renders consume its snapshot |
| FR-GIT-005 | AC-GIT-005 | Command allowlist audit and local/remote state comparison | Complete Git invocation trace, repository/index/worktree/ref digests, remote double transcript | No mutating Git or remote operation occurs and all compared local and remote state is unchanged |
| FR-FILES-001 | AC-FILES-001 | Temporary-repository unit/render fixture | Expected hierarchy, empty snapshot, missing/unreadable-root diagnostics | The bounded tree matches the fixture; empty and root-error states remain distinct |
| FR-FILES-002 | AC-FILES-002 | Cross-view canonical-state render comparison | Per-path GIT and FILES state pairs for every combined fixture | Each path exposes one correct Git-state value and one correct activity/source value, with the same semantics in both views |
| FR-ATTR-001 | AC-ATTR-001 | Attribution matrix including Git-only fixtures | State-pair snapshots and attribution-event trace | Git-only paths show no agent attribution; known Git state survives unknown source; no attribution is inferred from Git metadata |
| FR-ATTR-002 | AC-ATTR-002 | Lifecycle and scope-boundary unit fixtures | Start-only, completed, failed/cancelled, missing-source, and changed-scope outputs | Only permitted labels appear, and changing scope clears or re-evaluates path attribution |
| FR-INSTALL-001 | AC-INSTALL-001 | Integrated isolated package/install smoke | Tarball, package listings, lifecycle traces, route checks, pre/post host and repository health | Pack, install, load, exercise, update, remove, and post-removal health all pass in the isolated home |
| FR-INSTALL-002 | AC-INSTALL-002 | Cross-module-root handshake and compatibility matrix | Descriptor fixtures, handshake traces, registration and visible-route assertions | Compatible v1 registers successfully; absent/incompatible/unknown hosts register nothing and show actionable fail-closed guidance |
| FR-INSTALL-003 | AC-INSTALL-003 | Network/credential denial and optional-remote degradation | Request and credential spies, local GIT/FILES output, remote-domain diagnostic | Local GIT/FILES remain functional offline; optional remote data is explicitly unavailable and no secret prompt, credential access, or network write occurs |

#### Non-functional requirements

| Requirement | Test type | Required evidence | Pass condition |
|---|---|---|---|
| NFR-PERF-001 | Instrumented reference-fixture benchmark | Raw local refresh durations, exclusions, calculated 95th percentile | At least 95% of measured local Git and Files refreshes complete within 1 second after permitted exclusions |
| NFR-REL-001 | Fault injection, cross-view isolation, interrupted refresh, and removal health | Per-view failure traces, snapshot state transitions, post-failure/removal host checks | Failure remains local, incomplete data is not shown as complete, and removal does not impair the upstream host |
| NFR-SEC-001 | Security command/process/path audit | Invocation and environment captures, credential/network spies, root-escape fixtures, pre/post digests, redacted-output review | No mutation, credential access, secret disclosure, shell interpolation, root escape, unbounded output, or surviving unauthorized process occurs |
| NFR-ACC-001 | Keyboard/pointer parity, focus-order, text-render, and notification checks | Accessible-name inventory, parity traces, focus snapshots, announcement records | Names and state labels are textual and stable; keyboard and pointer outcomes match; focus is visible and correctly ordered |
| NFR-COMP-001 | Version/metadata matrix and malformed-descriptor tests | One row per compatibility condition, negotiation and activation traces | Only the tested common version succeeds; every unsupported condition fails closed with specific guidance and no partial view |
| NFR-OBS-001 | Structured diagnostic-event assertions | Route, scope, refresh, freshness, and category event records | Stable categories distinguish all required lifecycle conditions and contain no secrets or sensitive path disclosure |
| NFR-RES-001 | Resource-limit and hostile-fixture measurements | Time, output, memory, count, depth, concurrency, process, and cache metrics | All configured bounds hold; over-limit data is explicit; no unbounded process, stream, descriptor, or snapshot remains |
| NFR-PRIV-001 | Offline, no-credential, remote-denial, and consent fixtures | Network/credential traces, local feature output, approved-payload capture | Core inspection needs neither network nor credentials; disabled remote use sends nothing; consent-enabled use sends only approved data |

The release gate is the conjunction of all rows above: every FR/AC pair and every NFR must have passing evidence, and any `FAIL` or `UNAVAILABLE` row blocks release.

## 13. Delivery plan

All phases in this section are proposed. They describe a review and release sequence; they do not claim that the upstream capability, add-on repository, package, product views, attribution integration, GitHub adapter, or release candidate already exists.

### 13.1 Delivery model and dependencies

Delivery uses two independently owned artifacts:

- The upstream repository, `https://github.com/Gentleman-Programming/gentle-shell.git`, contains only the generic, versioned sidebar host capability: discovery and negotiation, view registration and disposal, active-view coordination, pinned-footer hosting, error isolation, and generic tests and documentation. It contains no Sidebar Steroids Git, Files, GitHub, attribution, or package implementation.
- The dedicated `sidebar-steroids` repository, provisionally `andrikonbeat/sidebar-steroids`, contains all product implementation, fixtures, package metadata, compatibility UX, and product documentation. The provisional identity remains subject to registry and trademark review.

The proposed dependency order is:

| Phase | Deliverable and owning repository | Dependencies | Independent review unit |
|---|---|---|---|
| 1 | Generic host API proposal and upstream merge | Recorded upstream baseline, upstream maintainer acceptance, upstream attribution evidence | One product-agnostic upstream PR containing the candidate capability, generic tests, and generic documentation |
| 2 | Dedicated add-on repository and package skeleton | Phase 1 landed contract; product-owner approval of repository ownership and package-identity review | One add-on bootstrap change containing repository structure, manifest, build/test scaffolding, and package metadata |
| 3 | STATUS/GIT/FILES local product implementation | Phases 1 and 2 | Add-on implementation split into ordered, independently testable local-product review slices; no attribution, GitHub, or release-hardening work |
| 4 | Attribution and local activity integration | Phase 3 plus an approved explicit lifecycle-evidence source | One add-on attribution change containing event mapping, state semantics, scope handling, and its tests |
| 5 | Optional GitHub enrichment | Phases 3 and 4 plus approved provider, consent, and privacy boundaries | One add-on remote-enrichment change containing the adapter, consent controls, failure isolation, and its tests |
| 6 | Compatibility, package hardening, and release | Phases 1 through 5; release-time identity and publication inputs | One release-candidate evidence bundle; any corrective code is reviewed separately and affected gates are rerun |

Phase 2 and later work may prepare a candidate against the versioned upstream contract, but a package must not be advertised as compatible or released until the upstream contract is landed and versioned.

### 13.2 Phase definitions

#### Phase 1 — Generic upstream host API proposal and merge

- **Scope:** Propose, review, and land a small generic host contract in the upstream repository. The candidate includes the protocol-qualified capability descriptor, cross-module-root discovery, version negotiation, atomic view registration and unregistration, generic active-view coordination, the pinned route-control host, render and digest lifecycle, focus and callback boundaries, disposal, error isolation, public documentation, and generic tests. The upstream PR must not mention or implement Sidebar Steroids Git, Files, GitHub, attribution, package, or product-specific fixtures.
- **Repository and owner:** Upstream Gentle Shell repository; upstream maintainer owns the API decision and merge, with an independent upstream reviewer providing the second review.
- **Dependencies:** The recorded upstream base, the upstream attribution protocol and manifest, the existing STATUS baseline, and acceptance of the generic API scope. If attribution requires a separate generic event-transport capability, that capability must be proposed as product-agnostic upstream work under this boundary; attribution semantics remain in the dedicated repository.
- **Entry gate:** The upstream maintainer accepts the proposal scope and public contract; the proposed version and capability identity are named; the upstream diff is restricted to generic host behavior and tests; and baseline STATUS fixtures are available for exact comparison.
- **Exit gate:** The versioned contract is landed in an upstream artifact available to the add-on, not merely proposed in an open PR. Generic upstream tests, cleanup and error-isolation tests, and the upstream STATUS regression pass; the public documentation describes negotiation and lifecycle behavior without product-specific behavior; and upstream attribution records match the landed file set.
- **Evidence:** Accepted API proposal and review record; upstream diff tied to the pinned base; negotiated API-version trace; focused generic test results; exact upstream STATUS comparison; updated upstream documentation; and an attribution-manifest validation result.
- **Rollback and removal impact:** The upstream contribution is isolated so that a landed upstream PR can be reverted independently through the upstream maintainers' normal revert and release process. Reverting it removes the optional host capability and leaves earlier upstream behavior and the core product unchanged; a dependent add-on then fails closed rather than partially activating.
- **Review workload boundary:** The upstream review unit is one generic capability PR with its tests and public documentation. Product behavior, package metadata, product fixtures, and later release work are excluded and must use separate reviews.

#### Phase 2 — Dedicated add-on repository and package skeleton

- **Scope:** Establish the dedicated product repository and a minimal npm Pi extension package skeleton after human authorization of repository creation and ownership. The skeleton includes the extension entry point declared through `pi.extensions`, runtime and peer dependency separation, source/test layout, build and pack commands, package metadata, license and attribution records, and the initial compatibility shell. It does not yet claim product-view behavior or publish a package.
- **Repository and owner:** Dedicated `sidebar-steroids` repository, subject to final registry and trademark approval; add-on maintainer owns implementation, product owner owns identity and scope, and the human release operator retains publication authority.
- **Dependencies:** Phase 1 has landed and versioned the host contract. The product owner has approved a dedicated repository boundary, and registry, namespace, and trademark reviews have an owner and recorded status.
- **Entry gate:** Repository and package ownership are unambiguous; the final or explicitly provisional identity is recorded; the manifest plan distinguishes runtime dependencies from host peer dependencies; and the product implementation is confirmed to reside outside upstream.
- **Exit gate:** The dedicated repository contains a reviewable skeleton with a resolvable extension entry point, a valid manifest, build and test scaffolding, and no bundled or patched host implementation. A package dry run contains only reviewed files, and license, trademark, and attribution records identify all pending release approvals without claiming them as complete.
- **Evidence:** Repository ownership record; manifest and dependency review; extension-entry resolution test; build and pack logs; packed-file allowlist; license and trademark review status; and a check that the upstream repository contains no product implementation.
- **Rollback and removal impact:** The skeleton can be disabled or removed from Pi without affecting upstream. If repository creation is abandoned, it can be archived or left unpublished; no upstream change, user repository state, or installed host package depends on it. No package is published during this phase.
- **Review workload boundary:** The review unit is one add-on bootstrap change limited to repository structure, manifest, dependency placement, build/test scaffolding, and metadata. Product views, collection logic, attribution, GitHub, and release sign-off are excluded.

#### Phase 3 — STATUS/GIT/FILES local product implementation

- **Scope:** Implement the add-on's local product surface in the dedicated repository: preserve STATUS as the unchanged default, register the GIT and FILES views through the negotiated host contract, provide the three-way route control behavior, and implement bounded read-only inspection of repository scope, branches, worktrees, changed paths, diffs, compact history, and the repository tree. This phase includes local snapshot, cache, refresh, offline, error, accessibility, and resource behavior, but does not add attributed actor claims or remote enrichment.
- **Repository and owner:** Dedicated `sidebar-steroids` repository; add-on maintainer implements the product, product owner reviews scope, and an independent product reviewer verifies behavior against the acceptance matrix.
- **Dependencies:** Phases 1 and 2; a compatible upstream artifact with the versioned API; approved local Git and Files fixtures; and an established rule that Git-state values may render without an attribution source by showing UNATTRIBUTED/UNKNOWN SOURCE rather than inferring activity.
- **Entry gate:** The add-on skeleton loads against the compatible host; the negotiation trace selects the expected version; the local scope and resource-bound designs are approved; and exact upstream STATUS fixtures are frozen for differential testing.
- **Exit gate:** Focused unit, render, and integration checks cover the local GIT and FILES requirements; rendering performs no Git collection; all Git operations are read-only and bounded; offline use requires no credentials; failures remain isolated by view; and the local product can be unregistered and disposed without changing upstream behavior. This phase exit does not substitute for the final integrated release gates.
- **Evidence:** Parser and snapshot fixtures; render and route-transition transcripts; process-spy and command-audit records; pre/post repository digests; offline and network-denial records; resource measurements; and exact STATUS comparisons with the add-on installed.
- **Rollback and removal impact:** The add-on can unregister its views and dispose collectors, timers, and subprocesses, or be removed from Pi, while upstream and user repository state remain unchanged. An incomplete local product is not advertised or published; no destructive Git operation is required for rollback.
- **Review workload boundary:** The phase is reviewed as ordered local-product slices: route and STATUS preservation, then GIT, then FILES. Each slice must be independently testable. Attribution, GitHub, package hardening, and publication remain outside this review.

#### Phase 4 — Attribution and local activity integration

- **Scope:** Integrate explicit local agent lifecycle evidence with the local product in the dedicated repository. The implementation maps attributable start, completion, failure, and cancellation evidence to ACTIVE/EDITING, LAST EDITED, and UNATTRIBUTED/UNKNOWN SOURCE; binds activity to session, agent, repository or worktree, and root-relative path; preserves Git state as an independent fact; and exposes freshness and evidence-window behavior. No Git metadata, timestamp, path order, or filesystem proximity is treated as proof of attribution.
- **Repository and owner:** Dedicated `sidebar-steroids` repository; add-on maintainer owns the implementation and attribution semantics, the product owner owns semantic acceptance, and the independent reviewer verifies the full combined-state matrix.
- **Dependencies:** Phase 3's stable local scope and snapshot model; an identified local lifecycle-evidence source that reports actor, session, path, operation, and lifecycle phase; and an approved fixture set. A generic upstream event-transport prerequisite, if any, follows Phase 1's product-agnostic upstream boundary.
- **Entry gate:** The evidence source is approved and testable; its events are explicit rather than inferred; start, completion, failure, cancellation, missing-source, and changed-scope fixtures are available; and the attribution source cannot write repository state.
- **Exit gate:** The add-on renders only labels supported by explicit evidence, never derives attribution from Git state, clears or re-evaluates evidence when scope changes, distinguishes activity state from Git state, and handles absent or stale evidence without false positives. All relevant FR-ATTR and NFR-PRIV checks pass.
- **Evidence:** Named lifecycle-event traces; complete combined-state snapshots; Git-only negative fixtures; scope-switch and evidence-window tests; freshness diagnostics; and proof that activity integration performs no Git or filesystem mutation.
- **Rollback and removal impact:** Attribution can be disabled by removing the add-on without changing upstream, Git, or filesystem behavior. If the evidence source becomes unsuitable, the add-on can fail closed or remain installed with attribution unavailable while local Git and Files continue to work. No destructive Git operation is required.
- **Review workload boundary:** The review unit is one add-on attribution change containing evidence mapping, state semantics, scope handling, diagnostics, and tests. Changes to local Git or Files collectors, optional GitHub behavior, and release packaging are excluded.

#### Phase 5 — Optional GitHub enrichment

- **Scope:** Add optional, read-only GitHub enrichment in the dedicated repository. The adapter may provide approved repository or pull-request context, uses explicit user consent and least-privilege access, bounds timeout, response size, concurrency, and retries, reports unavailable states distinctly, and cannot block or invalidate local Git, Files, or attribution snapshots. It must not infer local state or transmit local paths unless consent and the approved payload permit it.
- **Repository and owner:** Dedicated `sidebar-steroids` repository; add-on maintainer owns the adapter, product owner owns the user-visible scope and consent model, and the independent reviewer verifies the remote trust boundary.
- **Dependencies:** Phases 3 and 4; an approved provider identity and authentication boundary; an explicit consent and payload policy; and documented provider rate-limit, error, and availability behavior. Core local functionality has no dependency on this phase's provider availability.
- **Entry gate:** The provider and required metadata are named; the consent and credential boundary is approved; request fields and permitted path disclosure are documented; remote timeout and size budgets are defined; and a disabled-provider path is specified.
- **Exit gate:** The adapter is read-only, bounded, explicitly enabled, and independently disableable. Network denial, missing consent, unavailable provider, timeout, rate limiting, and malformed responses degrade only the GitHub domain; local Git, FILES, STATUS, and attribution remain functional offline; and no secret is read, printed, stored, or automatically requested.
- **Evidence:** Request and response fixtures; network and credential spies; consent and approved-payload captures; timeout, retry, response-size, and rate-limit traces; offline local-feature assertions; and diagnostic-redaction review.
- **Rollback and removal impact:** GitHub enrichment is disabled independently through the documented add-on setting, with no upstream, package, local Git, Files, or attribution change. The add-on remains functional with the enrichment domain marked unavailable. No destructive Git operation is required.
- **Review workload boundary:** The review unit is one add-on remote-enrichment change containing provider access, consent, request limits, failure isolation, and tests. Local collectors, attribution semantics, package lifecycle, and upstream work are excluded.

#### Phase 6 — Compatibility, package hardening, and release

- **Scope:** Assemble the integrated release candidate in the dedicated repository, exercise it with the landed upstream host artifact, harden compatibility and package behavior, complete release documentation, and perform human publication. The phase covers the compatibility matrix, exact STATUS regression, package manifest and lifecycle, manifest/license/trademark/attribution validation, security and resource audits, changelog, release notes, and the complete evidence bundle. It introduces no unrelated feature work.
- **Repository and owner:** Dedicated `sidebar-steroids` repository and the selected upstream release artifact; add-on maintainer prepares the candidate, verification owner owns independent acceptance, product owner approves scope, upstream maintainer approves host compatibility, and the human release operator performs publication.
- **Dependencies:** Phases 1 through 5 have met their exit gates; final registry and trademark decisions are recorded; a supported upstream API version and artifact are available; and the release operator has an isolated Pi-home test environment.
- **Entry gate:** The candidate is built from recorded repository revisions; the packed package and upstream artifact identities are fixed; final package identity, version, license, attribution, and trademark inputs are approved; all required verification commands are available; and every upstream and add-on evidence bundle is attached to the release record.
- **Exit gate:** Every release gate in Section 13.3 is PASS with direct evidence, the integrated install/update/remove candidate is approved, release notes and changelog are ready, and the release operator has made the publication decision. Any missing, skipped, unavailable, or failing required check blocks release.
- **Evidence:** Signed release record linking candidate revisions, package tarball and manifest, upstream API version, test and regression results, compatibility matrix, install/update/remove traces, exact STATUS comparison, security and resource measurements, attribution validation, changelog, release notes, and human publication approval.
- **Rollback and removal impact:** A defective add-on can be disabled or removed without changing the upstream host or user repository state; a GitHub-specific defect can disable only optional enrichment; a generic upstream defect can be handled by reverting the landed upstream PR independently through the upstream release process. A released package defect is addressed by a corrective package or by removal. No end-user reset, clean, checkout, branch or worktree deletion, history rewrite, or other destructive Git operation is needed.
- **Review workload boundary:** The review unit is the release-candidate evidence bundle and final manifest, not a new feature implementation. Any corrective code is a separate focused change, and every affected upstream, add-on, package, compatibility, STATUS, security, resource, and documentation gate is rerun before approval.

### 13.3 Release gates

The release gate is the conjunction of the following checks. A `FAIL` or `UNAVAILABLE` result blocks publication; no functional smoke result may waive a security, compatibility, package lifecycle, removal, or exact STATUS regression failure.

| Gate | Owner | Pass condition | Required evidence |
|---|---|---|---|
| **RG-1 — Upstream API contract landed and versioned** | Upstream maintainer | The generic capability contract is present in an upstream artifact available to users, has a stable capability identity and negotiated API version, and has no product-specific behavior | Upstream landed revision and release/version record, descriptor fixture, version-negotiation trace, generic public documentation, and upstream attribution-manifest validation |
| **RG-2 — Generic upstream tests and regression pass** | Upstream maintainer and independent upstream reviewer | Required upstream unit, layout, render, digest, disposal, error-isolation, and generic regression suites pass against the release candidate | Focused and required upstream test records tied to the exact upstream revision, with no unexplained failure |
| **RG-3 — Add-on package manifest, license, trademark, and attribution checks pass** | Product owner and release operator | The packed manifest has the correct `pi.extensions` entry and dependency placement; the final package/repository identity and trademark review are approved; included and upstream license notices and attribution records are accurate and complete | Packed file list, manifest snapshot, dependency audit, registry and trademark decision, license and attribution review, and validated upstream change manifest |
| **RG-4 — Package smoke install, update, and remove pass** | Release operator and verification owner | The packed candidate installs, loads, updates, removes, and leaves no orphaned route, process, timer, listener, or stream in an isolated Pi home; post-removal Pi and upstream health remain intact | Full isolated-home lifecycle transcript, package listings before and after each action, fresh-process health checks, cleanup measurements, and pre/post repository digests |
| **RG-5 — Compatibility matrix passes** | Verification owner | Every required host condition in Section 12.4 behaves as specified, including supported negotiation and fail-closed behavior for absent, incompatible, malformed, unknown, duplicate, and ambiguous capability conditions | Version-matrix records and handshake, activation, route-registration, diagnostic, and host-health traces for every required row |
| **RG-6 — Original STATUS exact regression passes** | Independent product and upstream reviewers | With the add-on installed and STATUS selected, every baseline STATUS fixture has exact content, ordering, empty/loading/error behavior, and supported interactions equal to the add-on-absent baseline | Approved fixture set, exact differential snapshots and interaction transcripts, with no fuzzy normalization |
| **RG-7 — Security and resource checks pass** | Independent verification owner | The release performs no mutating Git or remote operation, credential access, shell interpolation, path escape, or unbounded collection; all configured time, output, concurrency, cache, path, tree, history, and child-process bounds hold | Command and environment audit, credential and network spies, hostile path fixtures, resource measurements, cancellation and leak checks, redaction review, and pre/post repository digests |
| **RG-8 — Documentation and changelog are ready** | Product owner and release operator | User and contributor documentation describes installation, compatibility, offline behavior, optional GitHub consent, limitations, removal, troubleshooting, and attribution; the changelog identifies user-visible and compatibility changes; publication and rollback instructions are complete | Reviewed docs and changelog diff, version and upstream compatibility range, release notes, removal and rollback checklist, and final publication approval |

### 13.4 Rollback and removal policy

Rollback is a property of the delivery boundaries, not a product feature that mutates a user's Git state:

1. **Independent upstream rollback:** The upstream PR contains only the generic host capability. If that capability causes an upstream regression, the upstream maintainers can revert the landed PR independently through their normal revert and release mechanism. The dedicated add-on then detects absence or incompatibility and fails closed; it does not require an upstream rewrite or a product-code change in the same rollback.
2. **Core-preserving add-on removal:** The add-on unregisters its views and disposes its resources before package removal. Removing or disabling it leaves Pi, Gentle Shell, the original STATUS behavior, repositories, worktrees, indexes, refs, and files unchanged. A package defect does not require changing the core host.
3. **Independent optional-enrichment disablement:** GitHub enrichment has a separate consent and enablement boundary. Disabling it marks only the GitHub domain unavailable and leaves STATUS, local Git, FILES, and attribution functional; removal of the whole add-on remains the fallback.
4. **No destructive Git operation:** End-user rollback requires no reset, clean, checkout, branch or worktree deletion, commit, push, history rewrite, index change, or worktree modification. The upstream maintainers' ordinary revert of a landed change is performed in the upstream repository and is not delegated to users or the add-on.
5. **Release-candidate correction:** A failed gate returns the candidate to correction. The defect is fixed in a focused change, the affected upstream and add-on evidence is regenerated, and every gate whose inputs or behavior changed is rerun. A failed or unavailable check cannot be carried forward as a pass.

## 14. Risks and decisions

The following risks are prospective and must be managed through the release gates in Section 13.3. A risk is not accepted merely because its affected feature is optional; any required mitigation without evidence remains a release blocker.

### 14.1 Risk register

| ID | Risk | Likelihood/impact or severity | Mitigation | Trigger/early signal | Owner | Release-gate relevance |
|---|---|---|---|---|---|---|
| R-01 | Upstream may not accept, prioritize, or promptly land the generic versioned sidebar host capability, preventing the add-on from activating. | Likelihood: Medium; impact: High; severity: High | Keep the upstream contribution narrowly generic and independently reviewable; maintain the dedicated add-on as a separate artifact; do not advertise compatibility until a landed, versioned host artifact exists. | Upstream proposal is rejected, deferred beyond the planned phase, or lacks an assigned maintainer and acceptance path. | Upstream maintainer | Blocks Phase 1 exit and RG-1; also blocks any package advertisement under RG-3 and RG-5. |
| R-02 | Host API evolution or an upstream upgrade may cause version incompatibility, malformed negotiation, or divergent behavior. | Likelihood: Medium; impact: High; severity: High | Negotiate the highest mutually supported API version; validate descriptor shape, capability identity, host identity, and metadata; fail closed for absent, unknown, malformed, or incompatible versions; retest the full compatibility matrix for every host upgrade. | A newer host advertises no common version, changes descriptor metadata, or produces a conflicting capability publisher. | Upstream maintainer and add-on maintainer | Blocks RG-1, RG-2, and RG-5; must be rerun for changes to the upstream artifact. |
| R-03 | The fixed 50-column desktop rail may truncate or obscure Git, Files, paths, history, state labels, and the pinned route control. | Likelihood: Medium; impact: Medium; severity: Medium | Verify exact 50-column reservation, bounded content, readable truncation or wrapping, independent scrolling, and footer visibility across representative long-path and long-history fixtures. | Render measurements show clipping, footer loss, inaccessible labels, or loss of active-route text at or below the reservation width. | Upstream maintainer and independent upstream reviewer | Covered by RG-2 and RG-6; layout evidence is required before the integrated release gate can pass. |
| R-04 | Installing or routing to the add-on may alter the original STATUS content, ordering, state behavior, or interactions. | Likelihood: Low; impact: High; severity: High | Preserve STATUS as the upstream-owned default; use exact fixture-for-fixture differential evidence; prevent product overlay or reinterpretation of STATUS content; treat any difference as a release-blocking regression. | Any add-on-installed STATUS fixture differs in content, ordering, state behavior, or interaction from the add-on-absent baseline. | Independent product and upstream reviewers | Directly blocks RG-6; any failure also requires rerunning affected upstream and integrated checks under RG-2 and RG-4. |
| R-05 | Rendering may spawn Git commands or repeat expensive collection, violating latency targets and destabilizing repository state. | Likelihood: Medium; impact: High; severity: High | Separate immutable snapshot collection from rendering; coalesce refreshes by scope; prohibit Git execution from render, route, scroll, resize, focus, or digest paths; benchmark explicit local refresh latency against the 95th-percentile threshold. | Process-spy traces show Git activity during render, repeated collectors for one refresh, or refresh latency exceeds the stated threshold. | Add-on maintainer and verification owner | Blocks RG-2, RG-7, and the performance evidence required by RG-6 integration; also informs RG-4 smoke behavior. |
| R-06 | Linked worktrees, multiple repositories, or reused root paths may be conflated, causing state or attribution to cross scope boundaries. | Likelihood: Medium; impact: High; severity: High | Key data by canonical root, Git common-directory identity, and root-relative path; resolve scope before collection; evict or re-evaluate state when scope changes; reject path escape and ambiguous roots. | Fixtures expose the same relative path in separate roots or worktrees with mixed snapshots, stale attribution, or incorrect branch and worktree selection. | Add-on maintainer and independent product reviewer | Blocks the scope and attribution evidence covered by RG-2, RG-5, and RG-7; scope failures also invalidate RG-6 integrated scenarios. |
| R-07 | Git state may be misrepresented as agent attribution, or attribution may be assigned to the wrong actor, session, path, or operation lifecycle. | Likelihood: Medium; impact: High; severity: High | Require explicit attributable start and completion evidence; keep Git state and activity state independent; require exact scope, session, agent, path, and operation matching; label absent or ambiguous evidence as UNATTRIBUTED/UNKNOWN SOURCE. | A Git-only path receives an actor label, a start-only event is treated as completion, a failed or cancelled operation is counted as completed, or scope switching retains attribution. | Add-on maintainer, product owner, and independent product reviewer | Blocks RG-2, RG-6, and RG-7; attribution-manifest evidence is also required under RG-3 and RG-4. |
| R-08 | Subagent or child-agent lifecycle events may be omitted, duplicated, reordered, or delivered without sufficient actor and scope identity. | Likelihood: Medium; impact: Medium; severity: Medium | Define a generic, product-agnostic event transport boundary where needed; require stable actor, session, operation, path, and phase fields; define ordering and deduplication behavior; treat incomplete or stale event evidence as unavailable rather than inferred. | Event traces show missing phases, ambiguous actor identity, duplicate operations, or activity associated with the wrong session or worktree. | Upstream maintainer and add-on maintainer | Blocks the attribution portion of RG-2 and the attribution evidence within RG-6; transport changes also require upstream review under RG-1. |
| R-09 | Filesystem state and Git state may disagree because changes can occur outside agent events, Git comparison semantics vary, or snapshots become stale. | Likelihood: High; impact: Medium; severity: Medium | Present Git state and attribution independently; disclose snapshot freshness and evidence limitations; permit bounded refresh and low-rate polling only where defined; never infer activity from filesystem or Git metadata; preserve explicit stale, partial, and error states. | A path changes without a corresponding lifecycle event, Git reports an unexpected state, or a rendered snapshot exceeds its freshness interval without being marked stale. | Add-on maintainer and verification owner | Blocks RG-2, RG-6, and RG-7 where the discrepancy is misrepresented; explicit degradation evidence is required for RG-4 and RG-5. |
| R-10 | A hostile repository or malformed output may exhaust time, memory, output, process, file-descriptor, or tree-traversal resources. | Likelihood: Medium; impact: High; severity: High | Enforce audited read-only commands, direct process spawning, sanitized environments, bounded output and elapsed time, cancellation, concurrency limits, retained-snapshot limits, path confinement, symlink rejection, and explicit truncation or failure categories. | Any command exceeds a configured bound, child or descriptor survives cancellation, output or tree size reaches a limit, or hostile fixtures trigger unbounded traversal or retained state. | Add-on maintainer and independent verification owner | Blocks RG-7; affected security and resource evidence must also be present for RG-2 and the integrated RG-4 lifecycle. |
| R-11 | Optional GitHub authentication, rate limits, provider outages, malformed responses, or offline use may block local inspection or prompt for secrets. | Likelihood: Medium; impact: Medium; severity: Medium | Keep GitHub enrichment optional, read-only, explicitly enabled, and independently bounded; prohibit automatic credential prompts, secret access or persistence, and unnecessary path disclosure; degrade only the GitHub domain to UNAVAILABLE or ERROR. | GitHub receives an unexpected request, local features become unavailable during provider failure, a credential prompt appears, or rate-limit handling delays the local snapshot. | Add-on maintainer and independent verification owner | Blocks the privacy and degradation evidence required by RG-7 and the offline scenarios required by RG-4 and RG-6. |
| R-12 | Separately loaded package and host module roots may prevent capability discovery, or duplicate or deep imports may create ambiguous host identities. | Likelihood: Medium; impact: High; severity: High | Use the protocol-qualified global capability handshake; validate host identity and descriptor shape; reject deep imports, duplicate dependency instances, and competing publishers; fail closed before registration or data collection with actionable guidance. | Discovery fails only under a specific module topology, more than one publisher is detected, a fallback deep import is attempted, or product routes appear without successful negotiation. | Add-on maintainer and upstream maintainer | Directly blocks RG-5; package topology and handshake evidence are also required by RG-1, RG-2, and RG-4. |
| R-13 | The standalone launcher may use a different Pi home or lack documented forwarding for package lifecycle operations, producing misleading install or removal behavior. | Likelihood: Medium; impact: Medium; severity: Medium | Bind lifecycle commands and configuration to the active Pi home; identify the launcher-owned home unambiguously; use only documented forwarding behavior; record unsupported launcher operations as a product requirement rather than inventing command syntax. | Launcher and regular Pi package listings differ, an operation affects the wrong home, or no supported forwarding path exists for a required lifecycle action. | Upstream maintainer and human release operator | Launcher-specific evidence is required for RG-4; identity and publication decisions are also governed by RG-3. |
| R-14 | The provisional repository and npm package name may conflict with registry, namespace, trademark, or prior-art requirements. | Likelihood: Medium; impact: High; severity: High | Treat `sidebar-steroids` and `andrikonbeat/sidebar-steroids` as provisional; complete registry availability, namespace, trademark, and prior-art review; select an approved identity before publication and update all package metadata consistently. | A registry conflict, trademark objection, misleading endorsement, or inconsistent name appears in manifests, documentation, or registry metadata. | Product owner and human release operator | Blocks RG-3 and therefore publication; unresolved identity also blocks RG-4 because the tested source identifier must match the release candidate. |
| R-15 | Upstream host and add-on releases may be published or updated in an order that produces an unsupported or untested combination. | Likelihood: Medium; impact: High; severity: High | Record mutually supported API ranges; require a fresh handshake after updates; publish compatibility metadata and changelogs together; test the exact combined revisions in the isolated Pi-home smoke; fail closed when no common version exists. | A release candidate or user installation negotiates outside the tested matrix, or upstream and add-on versions are advertised without a recorded compatibility result. | Release operator, upstream maintainer, and add-on maintainer | Blocks RG-1, RG-4, and RG-5; version-skew evidence must be regenerated for every upstream or add-on release change. |
| R-16 | Removal or disablement may leave registered routes, timers, listeners, streams, child processes, callbacks, caches, or other orphaned resources. | Likelihood: Medium; impact: Medium; severity: Medium | Unregister before package removal; dispose resources idempotently; cancel pending work and terminate children; release retained streams and listeners; remove incompatible-schema caches; verify a fresh process after removal; never alter repository or upstream installation state. | Resource instrumentation finds surviving activity after removal, the add-on route remains, package listing conflicts with runtime state, or post-removal host health differs from baseline. | Add-on maintainer and human release operator | Directly blocks RG-4 and the cleanup evidence required by RG-7; post-removal STATUS health also falls under RG-6. |
| R-17 | The attribution record or machine-readable upstream manifest may drift from the actual upstream change set, ownership boundary, or reviewed revision. | Likelihood: Medium; impact: High; severity: High | Validate JSON structure and referenced evidence; compare manifest status and changed-file sets bidirectionally against the pinned base and candidate diff; preserve upstream authorship, MIT notices, and explicit ownership boundaries; treat omissions or contradictory status as failures. | Manifest file sets differ from the candidate diff, a planned item is described as implemented, evidence paths are unresolved, or license and ownership notices no longer match the reviewed change. | Upstream maintainer and independent upstream reviewer | Directly blocks RG-3; stale or incomplete attribution evidence also prevents RG-1 and the upstream regression evidence required by RG-2. |

### 14.2 Open decisions

The decisions below remain open until the accountable owner records a resolution in the relevant design, review, or release record. **Unresolved upstream acceptance blocks advertising compatibility.** An unresolved host transport, version, interaction, or coexistence choice never justifies deep imports, runtime monkey-patching, wrapping, replacement, or other coupling to upstream internals; the add-on must remain fail-closed until the generic public capability is accepted, landed, and versioned.

| Decision ID | Question | Owner role | Options and trade-offs | Needed-by phase | Safe default while unresolved |
|---|---|---|---|---|---|
| OD-01 | Which transport carries the generic sidebar-host capability across separately loaded Pi package module roots? | upstream maintainer | A protocol-qualified global capability key supports cross-root discovery with little host coupling; a neutral exported module is easier to type but depends on module topology; a process service adds indirection and lifecycle complexity. All options must avoid deep imports and duplicate host instances. | Phase 1 entry | Permit no product registration or compatibility advertisement; fail closed and use no deep import, duplicate dependency, or monkey-patch fallback. |
| OD-02 | Which host API version, negotiation rules, and compatibility range become the first supported public contract? | upstream maintainer | A single stable v1 simplifies negotiation; a range from the first release supports evolution but increases matrix cost; pre-release or unversioned negotiation is easier initially but is incompatible with the fail-closed requirement. | Phase 1 entry | Advertise no tested compatibility; accept only a landed, mutually supported version and fail closed for absent, malformed, unknown, or incompatible metadata. |
| OD-03 | What exact interaction and layout contract governs the pinned STATUS/GIT/FILES switcher, including focus, route order, resize behavior, and narrow fallback? | upstream maintainer | Host-owned switching gives deterministic focus and layout; add-on-owned switching risks conflicting with host state; a shared generic callback contract adds API surface but preserves product-neutral ownership. | Phase 1 entry | Do not merge or activate the switcher until the generic contract is approved; preserve the original STATUS rail and avoid ad hoc host hooks. |
| OD-04 | How does the host preserve the original STATUS view while allowing compatible additional routes to coexist with it? | upstream maintainer | STATUS remains upstream-owned and default with an additive route-control region; a replaceable default simplifies host state but risks regression; overlay composition offers flexibility but conflicts with the no-overlay requirement. | Phase 1 entry | Keep STATUS selected by default and unchanged; require exact fixture-for-fixture regression evidence before accepting coexistence. |
| OD-05 | Which generic, product-agnostic activity-event transport and lifecycle fields are needed if upstream must supply edit evidence? | upstream maintainer | A minimal versioned event contract with actor, session, operation, path, phase, and scope is explicit but expands upstream scope; an add-on-local adapter avoids upstream work but may lack authoritative evidence; filesystem observation is not an equivalent lifecycle source. | Phase 1 entry if generic transport is required; otherwise before Phase 4 entry | Make no attributed agent claim and show UNATTRIBUTED/UNKNOWN SOURCE where necessary; do not add an upstream transport as a workaround for unresolved acceptance. |
| OD-06 | Which approved evidence source and mapping semantics produce ACTIVE/EDITING, LAST EDITED, and UNATTRIBUTED/UNKNOWN SOURCE without inferring from Git state? | add-on maintainer | Use explicit start and completion lifecycle events; use completed events only for LAST EDITED; allow operation-level events without path detail; exclude Git status, timestamps, proximity, and filesystem polling as proof. The trade-off is coverage versus precision. | Phase 4 entry | Render Git state independently and omit actor attribution; never treat a missing completion event as proof of active work. |
| OD-07 | How is optional GitHub enrichment enabled, consented to, and disabled for users? | product owner | Explicit per-user opt-in with a visible disabled state preserves privacy; repository-derived consent is convenient but unsafe; a global toggle is simple but lacks per-repository control. | Phase 5 entry | Keep GitHub enrichment disabled, issue no request, and prompt for no credentials. |
| OD-08 | What package and standalone-launcher UX is documented and supported for install, update, remove, list, enable, and disable? | release owner | Use standard Pi package commands in the active Pi home; add documented launcher forwarding to the launcher-owned Pi home; require a launcher-specific enable/disable surface. Unsupported or invented launcher spellings are not acceptable. | Phase 2 entry | Support only the regular Pi lifecycle against the active Pi home; treat undocumented launcher operations as unsupported. |
| OD-09 | What repository and npm package identity, namespace, and public metadata are approved? | product owner | Retain a provisional name for internal work; select a distinct registered npm name and repository namespace after availability, trademark, and prior-art review; align all links, package metadata, and release notes. | Phase 2 entry; final approval before Phase 6 | Use the provisional identity only for planning, publish nothing, and make no availability, ownership, trademark, or endorsement claim. |
| OD-10 | Which user preferences and selections persist, where are they stored, and how are schema migrations handled? | add-on maintainer | Persist non-sensitive preferences in the active Pi home; keep repository and attribution state session-scoped; retain route selection only after an explicit user choice; discard unknown schema versions conservatively. The trade-off is continuity versus stale state and privacy. | Phase 3 entry | Keep transient state in memory for the current session, store nothing in user repositories, and persist no credentials or authorization material. |
| OD-11 | Which GitHub resources, fields, identifiers, and local-path disclosures are permitted in optional enrichment? | security reviewer | Limit enrichment to repository and pull-request metadata that the product can use; include compare or review context if approved; exclude file contents, secrets, and local paths by default. Broader scope improves context but increases privacy and rate-limit exposure. | Phase 5 entry | Send no request and transmit no local path; keep the GitHub domain unavailable while local Git and FILES remain functional. |
| OD-12 | Under what future contract may optional write operations be considered? | security reviewer | Require a new PRD and release with action-specific confirmation, least privilege, threat and privacy analysis, rollback behavior, and separate scope; a reusable broad grant is simpler but unsafe. | After Phase 6, before any future write-oriented release | Permit no write, stage, commit, checkout, merge, reset, branch deletion, push, pull, fetch, or remote mutation in Release 1. |

### 14.3 Definition of Done

The Definition of Done is prospective. Each criterion requires recorded evidence; a planned artifact, unavailable check, or plausible demonstration does not satisfy it.

#### PRD

- The decision, product scope, experience contract, state semantics, requirements, architecture, data model, lifecycle, security and privacy contract, verification plan, delivery plan, risks, open decisions, and traceability are internally consistent and written in technical English.
- Every functional requirement has a stable identifier and one corresponding acceptance criterion, and every acceptance criterion and non-functional requirement is mapped to a repeatable evidence type and pass condition.
- The acceptance-evidence plan covers unit, render, integration, compatibility, offline, attribution, security, resource, performance, package lifecycle, cleanup, and exact STATUS regression checks without implying that any check has already run.
- The plan distinguishes Git working-tree state from attributed activity, preserves STATUS as the upstream-owned default, treats GitHub as optional enrichment, and defines fail-closed behavior for an absent or incompatible host.
- The upstream attribution protocol and machine-readable manifest match the reviewed change set, retain planned-versus-implemented status, and preserve upstream authorship, license notices, ownership boundaries, and unresolved-review labels.
- All open decisions identify an accountable owner role, needed-by phase, trade-offs, and safe default, and unresolved upstream acceptance is recorded as a compatibility-advertisement and release blocker.
- Independent product and upstream reviewers approve the PRD and record no unresolved requirement, acceptance, attribution, licensing, trademark, or scope defect.
- The PRD contains no runtime implementation claim, npm availability claim, credential assumption, private path, endorsement, or fabricated verification result.

#### Generic upstream contribution

- The contribution contains only the generic versioned sidebar-host capability: discovery and transport, version negotiation, view registration and disposal, active-view coordination, pinned-route hosting, render/digest lifecycle, focus boundaries, error isolation, and cleanup.
- The public contract and documentation are product-agnostic and do not encode Sidebar Steroids Git, FILES, GitHub, attribution, package, or product-fixture behavior.
- Acceptance evidence covers cross-module-root discovery, supported-version negotiation, absent, older, newer-incompatible, malformed, unknown, duplicate, and ambiguous capability conditions, atomic registration, unregistration, and idempotent disposal.
- Acceptance evidence covers the exact 50-column reservation, 140-column breakpoint, narrow fallback, pinned switcher, scrolling, focus order, route preservation across resize and fullscreen transitions, digest invalidation, callback failure containment, and cleanup of host references.
- The exact original STATUS regression compares every approved baseline fixture with the add-on absent and installed for content, ordering, empty/loading/error states, and supported interactions, with no fuzzy normalization.
- Focused upstream tests, public API documentation, generic examples, migration and compatibility notes, and the recorded upstream review all pass and remain product-neutral.
- The reviewed diff preserves the upstream MIT notice and other required notices, identifies upstream-authored and contributed material accurately, and matches the attribution protocol and manifest against the pinned base.

#### Dedicated add-on package

- The reviewed package manifest declares a resolvable `pi.extensions` entry, places runtime libraries in `dependencies`, places host-provided packages only in `peerDependencies`, excludes development-only material, and does not bundle, vendor, patch, or copy the host implementation.
- Acceptance evidence covers the complete GIT and FILES functional requirements, repository and worktree scope isolation, bounded refresh and snapshot behavior, no Git process during render, read-only diff and history inspection, and the complete Git-state/activity-state matrix.
- Acceptance evidence covers ACTIVE/EDITING, LAST EDITED, and UNATTRIBUTED/UNKNOWN SOURCE boundaries, explicit actor/session/path/operation evidence, changed-scope eviction, evidence-window expiry, and the negative case that Git metadata never establishes attribution.
- The compatibility matrix demonstrates supported negotiation and fail-closed behavior for absent, incompatible, malformed, unknown, duplicate, and ambiguous hosts, with one actionable diagnostic and no partial product route or data collection.
- Performance evidence shows that at least 95% of explicit local Git and Files refreshes on the agreed reference fixtures complete within one second under the stated exclusions and retains raw samples and the calculated result.
- Security evidence demonstrates direct argument spawning without a shell, a sanitized environment, an audited read-only Git command set, no credential access or automatic prompt, no secret disclosure, root confinement, symlink safety, diagnostic redaction, no remote mutation, and unchanged repository, index, refs, worktrees, and files.
- Resource evidence demonstrates the configured elapsed-time, output-size, retained-snapshot, concurrency, path-count, branch-count, worktree-count, commit-count, tree-depth, diff-size, descriptor, stream, and child-process bounds, including cancellation, timeout, hostile-input, and leak cases.
- GitHub acceptance evidence proves that disabled, unconfigured, denied, offline, timed-out, rate-limited, and failed-provider cases degrade only the GitHub domain; local Git, FILES, STATUS, and attribution remain usable; no credential is read or stored; no request occurs without consent; and approved payloads exclude unapproved local paths.
- The packed-package smoke records install, list, load and handshake, STATUS/GIT/FILES exercise, update, fresh-process restart, removal, and post-removal health in an isolated Pi home, using the documented package source and the launcher-owned home only through documented forwarding behavior.
- Cleanup acceptance evidence proves that disable and removal unregister routes, cancel timers and polling, terminate child processes, release streams and listeners, remove stale-schema cache data, leave no orphaned registration or background work, and do not alter user repositories or the installed host.
- User and contributor documentation describes installation, compatibility, offline operation, optional GitHub consent and degradation, limitations, troubleshooting, removal, and attribution; the changelog identifies user-visible, compatibility, configuration-migration, and removal-behavior changes.
- Package metadata, the final approved repository and npm identity, included license and notice files, upstream attribution, trademark status, and publication records are accurate and internally consistent before any publication request.

#### Release

- The generic host capability is accepted and landed with a stable capability identity and negotiated API version; no unresolved upstream decision or compatibility claim remains.
- The integrated release candidate combines the exact reviewed upstream artifact and packed add-on package, and the complete compatibility matrix passes for every supported and unsupported host condition.
- The exact original STATUS regression passes with the add-on installed and STATUS selected, and no upstream regression is present in the upstream and integrated bundles.
- The isolated install/update/remove smoke passes for the packed candidate, including package listings, fresh-process handshake and route checks, repository-state digests, post-removal host health, and the absence of orphaned routes or resources.
- The complete performance, security, privacy, resource, fault-isolation, and cancellation evidence bundle meets every stated threshold; no required check is `FAIL`, `UNAVAILABLE`, skipped, or waived by a functional smoke result.
- Optional GitHub degradation is demonstrated in the integrated release and cannot block or invalidate local Git, FILES, STATUS, or attribution behavior.
- Final package identity, namespace, version, registry target, license and notice files, trademark decision, upstream attribution, and changelog and release-note contents are approved and consistent across the package, documentation, registry metadata, and release record.
- The release owner records approval only after the upstream, add-on, and integrated evidence bundles are complete and passing; publication remains a human-owned action with a documented rollback or removal path.

#### Explicit non-goals

- This Definition of Done does not require implementing, publishing, or endorsing the add-on, creating a remote repository, reserving a package name, or publishing to npm.
- The generic upstream contribution does not include product-specific Git, FILES, GitHub, attribution, package, or product-test behavior.
- Completion does not permit runtime monkey-patching, deep imports, duplicate host instances, patching an installed host, or coupling to private upstream internals.
- Release 1 does not include filesystem-write, stage, commit, checkout, merge, reset, branch or worktree deletion, push, pull, fetch, or other mutating Git or remote operations.
- Release 1 does not require GitHub credentials, network access, a remote provider, continuous filesystem observation, or an attributed activity label without explicit evidence.
- The criteria do not require a graphical commit graph, a complete ahead/behind implementation, a broad provider framework, or automatic repository selection.
- Documentation completion does not substitute for implementation acceptance evidence, package lifecycle smoke, exact STATUS regression, or the independent release gate.

## 15. Traceability

This section traces the source requirements in [odd/tasks/sidebar-steroids-prd.md](../../odd/tasks/sidebar-steroids-prd.md) to the normative PRD and its prospective acceptance evidence. A documented requirement is not an implementation claim. Runtime, package, and upstream-host evidence remains unavailable until the separately reviewable artifacts in Section 13 exist; this section records neither a passing result nor a release approval.

### 15.1 Source-to-PRD traceability matrix

| Source | Documented requirement | PRD coverage | Established requirement and acceptance mapping | Required acceptance evidence | Verification method and status |
|---|---|---|---|---|---|
| **S1 — complete PRD** | The deliverable is a complete product and technical PRD for Sidebar Steroids, including the product scope, architecture, installation model, verification contract, and delivery controls. | Sections [1](PRD.md#1-decision-and-quick-path) through [14](PRD.md#14-risks-and-decisions), including the scope and experience contracts in Sections [3](PRD.md#3-product-definition) and [4](PRD.md#4-experience-contract), the complete requirement families in [6](PRD.md#6-requirements), and the prospective evidence plan in [12](PRD.md#12-verification). | All FR/AC pairs and all NFRs listed in Section 6; the complete FR/AC and NFR mapping is repeated in Section 15.2. | The Section 12.10 acceptance matrix for every FR/AC pair and NFR, plus the independent upstream, add-on, and integrated release evidence described in Sections 12.1 and 13.3. | Prospective artifact-specific verification under Sections 12.2-12.10 and release gates in Section 13.3. The documentation structure is complete; runtime evidence is not yet available. |
| **S2 — upstream attribution and provenance** | Changes made to a repository not owned by Sidebar Steroids must be attributable to their contributor. Upstream authorship, license and trademark boundaries, planned-versus-implemented status, and repository history must remain explicit and reviewable. | Sections [2](PRD.md#2-document-control), [7.1](PRD.md#71-existing-implementation-and-documentation-evidence), [8.8](PRD.md#88-explicit-exclusions-and-contribution-boundaries), [10.1](PRD.md#101-package-boundary-and-manifest), [12.7](PRD.md#127-attribution-manifest-validation), [13.1](PRD.md#131-delivery-model-and-dependencies), and [14.3](PRD.md#143-definition-of-done); the evidence artifacts are [docs/sidebar-steroids/UPSTREAM_ATTRIBUTION.md](UPSTREAM_ATTRIBUTION.md) and [docs/sidebar-steroids/upstream-manifest.json](upstream-manifest.json). | **FR-INSTALL-001 / AC-INSTALL-001** for avoiding upstream patching and preserving upstream health; **NFR-REL-001** for removal and host-health safety. Attribution, marker, license, trademark, and history checks use the dedicated Section 12.7 evidence contract rather than introducing a requirement outside the established families. | Valid JSON and structure; bidirectional changed-file comparison against base `bac4c4a3ffc4e8455548691dcd820d60fd3c58f6`; planned-versus-implemented status review; referenced-path existence; license/trademark review; and preserved upstream authorship/history record. | Section 12.7 manifest validator and independent upstream review, with release-gate evidence under Section 13.3. The protocol and manifest are linked; parse and status validation remain parent verification obligations. |
| **S3 — independently installable add-on** | Users must be able to install the product as an add-on that adds the new capabilities to Gentle Shell. Installation, update, inspection, and removal use Pi's supported package lifecycle; the standalone launcher uses its own Pi home. An absent or incompatible host fails closed. | Sections [1](PRD.md#1-decision-and-quick-path), [8](PRD.md#8-architecture), [10](PRD.md#10-installability-and-lifecycle), [12.3](PRD.md#123-package-and-installation-smoke), [12.4](PRD.md#124-host-compatibility-matrix), and [13](PRD.md#13-delivery-plan). | **FR-INSTALL-001 / AC-INSTALL-001**, **FR-INSTALL-002 / AC-INSTALL-002**, and **FR-INSTALL-003 / AC-INSTALL-003**; **NFR-COMP-001**, **NFR-REL-001**, **NFR-PRIV-001**, and **NFR-RES-001**. | Packed manifest and file allowlist; resolvable `pi.extensions` entry; isolated-home install/list/update/remove and post-removal health; launcher-home distinction; compatible-host handshake; absent, incompatible, malformed, unknown, duplicate, and ambiguous-host fail-closed evidence; and offline/no-credential behavior. | Section 12.3 package smoke, Section 12.4 compatibility matrix, and Sections 13.2-13.3 package and compatibility gates. These are future release checks; no package availability or successful installation is claimed here. |
| **S4 — STATUS/GIT/FILES and fixed three-way control** | The sidebar exposes three routes—STATUS, GIT, and FILES—through a bottom-pinned toggle. STATUS remains the original default, and the add-on does not replace the main terminal workspace. | Sections [3](PRD.md#3-product-definition), [4](PRD.md#4-experience-contract), [6.1](PRD.md#61-functional-requirements), [8.3](PRD.md#83-conceptual-capability-contract), [8.4](PRD.md#84-upstream-responsibilities), and [12.2](PRD.md#122-required-test-layers). | **FR-STATUS-001 / AC-STATUS-001**, **FR-STATUS-002 / AC-STATUS-002**, and **FR-STATUS-003 / AC-STATUS-003**; **NFR-ACC-001** and **NFR-REL-001**. | Exact add-on-absent/add-on-installed STATUS fixture pairs; route-state traces across route, resize, and fullscreen transitions; desktop and narrow geometry; pinned-footer visibility while content scrolls; and keyboard/pointer parity. | Exact STATUS differential, upstream layout/render tests, add-on route integration, and Section 12.10 evidence rows. These requirements are documented but not yet implemented or evidenced. |
| **S5 — GIT and attributed agent context** | The GIT route provides a more graphical view of local repository activity: selected scope, branches, worktrees, changed files and diffs, commits, and attributed agent context. The PRD intentionally bounds the history representation: it does not claim a graphical commit graph. | Sections [3](PRD.md#3-product-definition), [5](PRD.md#5-state-semantics), [6.1](PRD.md#61-functional-requirements), [9](PRD.md#9-data-and-refresh-model), [11](PRD.md#11-security-and-privacy), and [12](PRD.md#12-verification). | **FR-GIT-001 / AC-GIT-001** through **FR-GIT-005 / AC-GIT-005**; **FR-ATTR-001 / AC-ATTR-001** and **FR-ATTR-002 / AC-ATTR-002**; **FR-INSTALL-003 / AC-INSTALL-003**; **NFR-PERF-001**, **NFR-REL-001**, **NFR-SEC-001**, **NFR-OBS-001**, **NFR-RES-001**, and **NFR-PRIV-001**. | Multi-root/worktree branch and scope fixtures; MODIFIED/NEW/DELETED and read-only diff fixtures; bounded commit/parent ordering and truncation; render/refresh process-spy trace; mutating-command audit; complete attribution/lifecycle matrix; and optional-GitHub degradation evidence. | Add-on Git, render, integration, attribution, offline, security, and resource layers in Section 12.2, with the exact Section 12.10 rows. No current implementation or graphical commit graph is claimed. |
| **S6 — FILES tree and recent path activity** | The FILES route shows the repository tree, including the current and last-edited path, while the user remains in Gentle Shell. The experience must not overstate observation: current baseline has no filesystem watcher, so near-real-time behavior means bounded refresh or permitted low-rate polling with explicit freshness. | Sections [3](PRD.md#3-product-definition), [4](PRD.md#4-experience-contract), [5](PRD.md#5-state-semantics), [6.1](PRD.md#61-functional-requirements), [9.2](PRD.md#92-snapshot-model), [9.4](PRD.md#94-refresh-triggers-and-cadence), [9.5](PRD.md#95-cache-digest-invalidation-and-failure-states), and [12.5](PRD.md#125-performance-cadence-and-resource-checks). | **FR-FILES-001 / AC-FILES-001**, **FR-FILES-002 / AC-FILES-002**, **FR-ATTR-001 / AC-ATTR-001**, **FR-ATTR-002 / AC-ATTR-002**, and **FR-GIT-004 / AC-GIT-004**; **NFR-PERF-001**, **NFR-REL-001**, **NFR-ACC-001**, **NFR-OBS-001**, **NFR-RES-001**, and **NFR-PRIV-001**. | Temporary-repository tree, empty-root, and unreadable-root fixtures; cross-view Git-state/activity pairs; ACTIVE/EDITING and LAST EDITED lifecycle evidence; scope-change eviction; explicit refresh/cadence trace; freshness states; latency benchmark; and resource measurements. | Add-on Files, attribution, snapshot, render, integration, performance, and resource layers in Section 12.2 and Section 12.5. Continuous filesystem observation is explicitly outside the requirement set. |
| **S7 — STATUS diagnostic semantics** | STATUS diagnostics remain opt-in: enabling arms timing for the next separately authorized STATUS-bearing tool call; `show` reads the memory-only summary; `disable` clears it. Diagnostics never invoke or retry STATUS, and the Sidebar Steroids add-on does not add automatic retry behavior. | Sections [3](PRD.md#3-product-definition), [6.1](PRD.md#61-functional-requirements), [7.1](PRD.md#71-existing-implementation-and-documentation-evidence), [9.4](PRD.md#94-refresh-triggers-and-cadence), [11.3](PRD.md#113-privacy-and-independent-degradation), and [14.3](PRD.md#143-definition-of-done); the source boundary is [docs/readme-reference.md](../readme-reference.md#diagnose-status-timing-without-retrying-it). | **FR-STATUS-001 / AC-STATUS-001** for preserving STATUS behavior; **NFR-REL-001** for isolated failure behavior; **NFR-OBS-001** for stable diagnostic categorization without automatic execution. | Exact STATUS fixture/interaction differential; diagnostic-boundary review proving no STATUS invocation or retry from `enable`, `show`, or `disable`; memory-only summary and clearing behavior; and no add-on-owned automatic retry trace. | Review of the linked diagnostic contract, exact STATUS regression, and structured diagnostic-event tests in Sections 12.2 and 12.6. This is preservation of existing semantics, not a new diagnostic runtime. |
| **S8 — fullscreen installation note** | The fullscreen installation note remains separate from the Sidebar Steroids add-on contract. A recognized global installation may persist Pi's global `"tuiMode": "fullscreen"` setting as the launcher documents; a project-local installation must not cause that global fullscreen persistence. | Sections [4](PRD.md#4-experience-contract), [10.2](PRD.md#102-regular-pi-package-commands-and-standalone-launcher-home), [10.4](PRD.md#104-enable-disable-configuration-and-persistence), [10.7](PRD.md#107-release-1-behavior-and-installation-acceptance), [12.3](PRD.md#123-package-and-installation-smoke), and [13](PRD.md#13-delivery-plan). | **FR-INSTALL-001 / AC-INSTALL-001** for package operations and Pi-home boundaries; **NFR-REL-001** for preserving upstream and unrelated installation state. The fullscreen-setting distinction remains an installation-context constraint and does not create a new requirement family. | Installation evidence tied to the correct Pi home; no modification of another Pi home; pre/post host and configuration checks; and a project-local installation check showing no global `"tuiMode": "fullscreen"` mutation. | Section 12.3 isolated package smoke and Section 10.2 launcher-home checks, reviewed with the installation owner. The global-setting note is not implemented or changed by this documentation-only PRD. |

### 15.2 Requirement and acceptance-criterion coverage

#### Functional requirements and acceptance criteria

| Requirement | Acceptance criterion | Source coverage | Acceptance evidence and verification method |
|---|---|---|---|
| FR-STATUS-001 | AC-STATUS-001 | S1, S4, S7 | Exact upstream differential plus add-on render/interaction regression; every baseline fixture and supported interaction is identical with the add-on installed and STATUS selected. |
| FR-STATUS-002 | AC-STATUS-002 | S1, S4 | Route-state and host-mode integration traces prove fresh-state STATUS selection and preservation of an explicit selection across route, resize, and fullscreen transitions. |
| FR-STATUS-003 | AC-STATUS-003 | S1, S4 | Upstream layout/render and keyboard/pointer tests prove three reachable routes, one active route, and persistent visibility in desktop and narrow presentations. |
| FR-GIT-001 | AC-GIT-001 | S1, S5 | Multi-root/worktree Git fixtures and pre/post repository digests prove correct scope, branches, and worktrees without mutation. |
| FR-GIT-002 | AC-GIT-002 | S1, S5 | Git-state, diff-render, and repository-state fixtures prove MODIFIED/NEW/DELETED rendering and read-only diff inspection. |
| FR-GIT-003 | AC-GIT-003 | S1, S5 | A known-history fixture proves bounded commit/parent ordering and explicit truncation; no graphical commit graph is required. |
| FR-GIT-004 | AC-GIT-004 | S1, S5, S6 | A process spy and snapshot-version trace prove zero Git processes during render and one bounded collection for an explicit refresh. |
| FR-GIT-005 | AC-GIT-005 | S1, S5 | A command allowlist audit and local/remote state comparison prove that Release 1 issues no mutating operation. |
| FR-FILES-001 | AC-FILES-001 | S1, S6 | Temporary-repository render fixtures prove the bounded tree hierarchy and distinct empty, missing-root, and unreadable-root states. |
| FR-FILES-002 | AC-FILES-002 | S1, S5, S6 | Cross-view state-pair fixtures prove one Git-state value and one activity/source value per path with shared semantics. |
| FR-ATTR-001 | AC-ATTR-001 | S1, S5, S6 | Attribution-matrix fixtures prove that Git-only evidence produces no agent attribution and that unknown source does not erase Git state. |
| FR-ATTR-002 | AC-ATTR-002 | S1, S5, S6 | Lifecycle and scope-boundary fixtures prove permitted ACTIVE/EDITING and LAST EDITED labels and re-evaluation after scope change. |
| FR-INSTALL-001 | AC-INSTALL-001 | S1, S2, S3, S8 | An isolated packed-package smoke proves install, load, exercise, update, remove, launcher-home isolation, post-removal health, and no upstream patch. |
| FR-INSTALL-002 | AC-INSTALL-002 | S1, S3 | Cross-module-root handshake and compatibility fixtures prove successful v1 negotiation and fail-closed behavior for every unsupported host condition. |
| FR-INSTALL-003 | AC-INSTALL-003 | S1, S3, S5, S6 | Network/credential denial and optional-remote degradation fixtures prove offline local GIT/FILES use, explicit remote unavailability, and no secret prompt or network write. |

#### Non-functional requirements

| Requirement | Source coverage | Acceptance evidence and verification method |
|---|---|---|
| NFR-PERF-001 | S1, S5, S6 | An instrumented reference-fixture benchmark retains raw refresh samples and proves that at least 95% complete within one second after the permitted exclusions. |
| NFR-REL-001 | S1, S2, S3, S4, S5, S6, S7, S8 | Fault injection, cross-view isolation, interrupted-refresh, diagnostic-boundary, package-lifecycle, and removal-health evidence proves localized failures and preserved upstream health. |
| NFR-SEC-001 | S1, S5, S6 | A security command/process/path audit proves no mutation, credential access, shell interpolation, root escape, secret disclosure, unbounded output, or surviving unauthorized process. |
| NFR-ACC-001 | S1, S4, S5, S6 | Keyboard/pointer parity, accessible-name inventory, text-render, focus-order, and notification fixtures prove equivalent operation and textual state. |
| NFR-COMP-001 | S1, S3 | A version/metadata matrix proves success only for the tested common host API and actionable fail-closed behavior for every unsupported condition. |
| NFR-OBS-001 | S1, S4, S5, S6, S7 | Structured diagnostic-event assertions prove stable route, scope, refresh, freshness, and failure categories with no secrets or sensitive path disclosure. |
| NFR-RES-001 | S1, S3, S5, S6 | Time, output, memory, count, depth, concurrency, process, and cache measurements prove all configured bounds and explicit over-limit handling. |
| NFR-PRIV-001 | S1, S3, S5, S6 | Offline, no-credential, remote-denial, and consent fixtures prove credential-free local operation and only approved remote payloads. |

### 15.3 Coverage summary and evidence status

- **Requirement coverage is complete.** The three `FR-STATUS-*` requirements, five `FR-GIT-*` requirements, two `FR-FILES-*` requirements, two `FR-ATTR-*` requirements, and three `FR-INSTALL-*` requirements are represented by their fifteen corresponding `AC-*` identifiers. All nine established NFR identifiers are represented. No requirement identifier outside these Section 6 families is introduced by this traceability section.
- **Source coverage is explicit.** S1 is represented by the complete PRD and full acceptance matrix; S2 by provenance, attribution, and release-boundary evidence; S3 by package and compatibility evidence; S4 by exact STATUS and route-control evidence; S5 by GIT, history, worktree, and attribution evidence; S6 by FILES, lifecycle-evidence, freshness, and cadence evidence; S7 by the linked diagnostic boundary and exact STATUS regression; and S8 by installation-home and global-setting side-effect checks.
- **Documented requirement and implementation evidence are distinct.** Sections 3-11 and Section 14 state normative requirements, constraints, ownership, and release gates. Section 12 states the evidence required to demonstrate those requirements after implementation. In the current documentation-only state, upstream-host, add-on runtime, package lifecycle, and release-candidate evidence is `UNAVAILABLE` under Section 12.8 semantics; no trace row asserts `PASS`, and such unavailability remains a future release blocker rather than evidence that the requirement is already satisfied.
- **Known scope limitations remain traceable.** The GIT route does not claim a graphical commit graph, the FILES route does not claim a filesystem watcher or continuous observation, optional GitHub use does not block local inspection, and project-local installation does not inherit global fullscreen persistence. These are deliberate requirement boundaries, not missing implementation claims.

### 15.4 Documentation-only completion evidence checklist

The checklist below applies only to the PRD documentation artifact. It does not convert prospective runtime acceptance evidence into a completed implementation.

- [ ] **Cross-document links verified by the parent.** Confirm that the links to [odd/tasks/sidebar-steroids-prd.md](../../odd/tasks/sidebar-steroids-prd.md), [docs/sidebar-steroids/UPSTREAM_ATTRIBUTION.md](UPSTREAM_ATTRIBUTION.md), [docs/sidebar-steroids/upstream-manifest.json](upstream-manifest.json), and [docs/readme-reference.md](../readme-reference.md#diagnose-status-timing-without-retrying-it) resolve to the intended files or heading. Status in this unit: **not validated**.
- [ ] **Manifest parse and status verified by the parent.** Parse [docs/sidebar-steroids/upstream-manifest.json](upstream-manifest.json), confirm its referenced evidence paths, and compare each planned or implemented status with the candidate change set as required by Section 12.7. Status in this unit: **not validated**.
- [ ] **No unresolved drafting placeholders verified by the parent.** Confirm that no `TODO`, pending-writer marker, or unsupported completion claim remains. The named `<package>`, `<source>`, and future-command placeholders are intentional protocol inputs or release-time command templates under Sections 10, 12.9, and 13, not undocumented drafting gaps. Status in this unit: **not validated**.
- [x] **Attribution boundaries recorded.** Sections 2, 7.1, 8.8, 12.7, 13.1, and 14.3 identify upstream and product ownership, contributor attribution, planned-versus-implemented evidence, license and trademark review, and preservation of upstream history. Runtime manifest validation remains separately pending.
- [x] **Repository-boundary decision recorded.** Sections 2, 8.8, and 13.1 keep the generic host capability in the upstream preparation surface and place the product implementation, package, release lifecycle, and product tests in the dedicated, provisional `andrikonbeat/sidebar-steroids` repository, consistent with the source task's L10 decision.
- [ ] **Parent verification record completed.** The parent transaction controller records the exact section-only diff result and the outcomes of the cross-document-link, manifest, placeholder, attribution-boundary, and repository-boundary checks in the final handoff. Status in this unit: **awaiting parent verification record**.

## Provenance

See [UPSTREAM_ATTRIBUTION.md](UPSTREAM_ATTRIBUTION.md) and [upstream-manifest.json](upstream-manifest.json). Planned upstream changes remain planned.
