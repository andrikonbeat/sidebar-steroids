# Sidebar Steroids PRD

This feature defines the product, technical boundary, installation contract, and upstream-attribution protocol for the Sidebar Steroids add-on. The deliverable is documentation only; no runtime implementation is authorized by this task.

## Specs

S1. "vamos con el PRD completo de sidebar-steroids"

S2. "recuerda que estos cambios los estamos haciendo sobre un repositorio que no es nuestro por ende estos cambios nuevos deben de alguna forma deben estar marcados para que el autor original sepa que fue lo que modifique yo."

S3. "y muy importante todo esto tiene que ser algo instalable el usuario lo instala y se añaden todas estas nuevas capacidades a gentle-shell"

S4. "Quiero construir una herramienta que se va a mostrar en la sidebar se Gentle-Shell y será accesible mediante un botón toggle situado en la posición inferior de la propia sidebar que va a permitir cambiar la sidebar de la visita principal a dos visitas más una será la vista git y la otra files: status/git/files."

S5. "El objetivo es que en status se mantenga la barra original por defecto de Gentle-Shell en git la sidebar cambia a una nueva sidebar que va a permitir al usuario seguir los pasos y el trabajo del agente con git y github, con grafos, worktrees, ramas, archivos tocados, comits y todos los demás conceptos de manera más gráfica la idea es que el usuario pueda ver dónde está parado el agente y seguirlo."

S6. "En la rama file la sidebar cambia mostrando el árbol de carpetas del repositorio el objetivo es que el usuario pueda ver en todo momento cuál fue el último archivo modificado, cuál está modificando, todo en tiempo real sin que el usuario tenga que salir de Gente-Shell."

S7. "STATUS diagnostics are opt-in: `/gentle:status-timing enable` arms timing for the next separately authorized STATUS-bearing tool call; `show` consults the memory-only summary and `disable` clears it. It never invokes or retries STATUS. See the [diagnostic boundaries](docs/readme-reference.md#diagnose-status-timing-without-retrying-it)."

S8. "Fullscreen installation note: a recognized global installation persists Pi's `\"tuiMode\": \"fullscreen\"` setting. Project-local and other install paths do not receive that change."

## Tasks

T1 (S1-S6, route:inline, commit:docs: map current sidebar and packaging boundary) Map the current sidebar, Changes, worktree, activity, package installation, and upstream attribution evidence. Done.

T2 (S1-S6, route:worker, commit:initial-bootstrap) Write the complete product and technical PRD with requirements, installability, compatibility, acceptance criteria, rollout, and non-goals. Done; committed in dedicated repository.

T3 (S2-S3, route:worker, commit:initial-bootstrap) Add the human-readable attribution protocol and machine-readable upstream change manifest without claiming upstream ownership. Done; committed in dedicated repository.

T4 (S1-S8, route:inline, commit:initial-bootstrap) Verify every requirement against the proposal, current implementation evidence, package model, and upstream constraints; run focused documentation checks. Done; committed in dedicated repository.

## PRD authoring contract

The PRD is written in technical English and leads with the decision, scope, and verification path. It is a product and technical contract, not a restatement of the source response and not an implementation task list.

### Required structure

1. **Decision and quick path** — state the separate-package decision, upstream host dependency, install/remove path, compatibility failure behavior, and how a reviewer verifies the proposal.
2. **Document control** — status, target release, owner roles, base version/commit, upstream and add-on artifacts, and linked provenance documents.
3. **Product definition** — problem, goals, non-goals, personas, user stories, success metrics, and a STATUS/GIT/FILES scope matrix. STATUS remains the unchanged default Gentle Shell rail.
4. **Experience contract** — fixed bottom three-way control, 50-column rail, fullscreen and 140-column breakpoint, narrow/mobile fallback, keyboard/mouse behavior, empty/loading/error/offline states, accessibility, and layout invariants.
5. **State semantics** — distinguish Git working-tree state from attributed agent activity. Define ACTIVE/EDITING, LAST EDITED, MODIFIED, NEW, DELETED, and UNATTRIBUTED/UNKNOWN SOURCE without claiming a filesystem watcher exists.
6. **Requirements** — stable IDs for `FR-STATUS-*`, `FR-GIT-*`, `FR-FILES-*`, `FR-INSTALL-*`, and `FR-ATTR-*`; every functional requirement maps to one or more acceptance criteria. Add testable non-functional requirements for performance, reliability, security, privacy, compatibility, accessibility, observability, and resource bounds.
7. **Current baseline** — cite stable paths and symbols for the terminal-owned part registry, fullscreen-only rail, 140-column breakpoint, ScrollView/digest caching, and existing Changes/worktree/diff support. Explicitly identify the absent branch list, commit graph UI, ahead/behind UI, repository tree, GitHub provider, filesystem watcher, and public edit lifecycle.
8. **Architecture** — upstream owns a small generic versioned capability host; the add-on owns Git/Files behavior. Define capability discovery that works across separately loaded Pi package module roots, registration, active-view switching, fixed pinned footer host, render/digest lifecycle, disposal, error isolation, and fail-closed compatibility. Avoid a broad shared provider framework.
9. **Data and refresh model** — root/worktree identity and namespacing, bounded snapshots, event-driven activity, polling only when useful, no Git command per render, and optional read-only GitHub degradation. Local Git/Files never require credentials.
10. **Installability and lifecycle** — separate npm Pi package with `pi.extensions`, runtime dependencies only in `dependencies`, host packages only as peer dependencies, provisional non-reserved name, `pi install npm:<package>`, `pi update --extensions`, `pi remove <source>`, standalone launcher behavior, removal safety, and no patching of the installed upstream package.
11. **Security and privacy** — no credential access or printing, sanitized Git environment, bounded output/timeouts, no shell interpolation, no write/destructive Git actions in release 1, and explicit consent for later remote actions.
12. **Verification** — unit, render, integration, package/install smoke, compatibility, offline, attribution-manifest, and exact regression tests for the original STATUS rail; include a requirements-to-evidence acceptance matrix.
13. **Delivery plan** — independently reviewable phases with entry/exit gates, dependencies, rollback/removal, and the release gate that requires the upstream host capability before compatibility is advertised.
14. **Risks and decisions** — named risks, mitigations, owners, open decisions, and Definition of Done.
15. **Traceability** — map S1-S8 to requirement IDs and verification evidence; no task checkboxes.

### Writing constraints

- Lead with the answer; use short sections, tables, and checklists where they improve reviewability.
- Do not invent implementation claims, npm availability, credentials, private paths, or endorsement.
- Keep every repository path repository-relative.
- Use the upstream base and attribution protocol in `docs/sidebar-steroids/UPSTREAM_ATTRIBUTION.md` and `docs/sidebar-steroids/upstream-manifest.json`.
- The final file must be self-contained enough for an upstream maintainer and an add-on implementer to review independently.

## Log

L1. "vamos con el PRD completo de sidebar-steroids

recuerda que estos cambios los estamos haciendo sobre un repositorio que no es nuestro por ende estos cambios nuevos deben de alguna forma deben estar marcados para que el autor original sepa que fue lo que modifique yo.

y muy importante todo esto tiene que ser algo instalable el usuario lo instala y se añaden todas estas nuevas capacidades a gentle-shell"

L2. The source proposal at `docs/sidebar-steroids/ChatGPT_Explica-flujo-GitHub_20261108-0500.md` defines the original STATUS/GIT/FILES product concept. Its proposed architecture is treated as a vision, not as proof of current implementation.

L3. Exploration on 2026-11-08 found that the current sidebar is a terminal-owned, digest-cached assembly of independently registered parts. It exists in fullscreen mode at widths of at least 140 columns. Existing Changes infrastructure already covers per-root Git status, diffs, branches, and registered worktrees, but there is no branch list, commit graph, ahead/behind UI, repository tree, GitHub provider, file watcher, or public agent edit-start/edit-finished model.

L4. Product decision: distribute the feature as a separate Pi package rather than as a modified copy of `gentle-pi`. The package must be installable with Pi's package manager and must not patch or overwrite a user's installed Gentle Shell.

L5. Architecture decision: a small, generic, backward-compatible sidebar host API in upstream Gentle Shell is the supported integration boundary. The add-on owns the Git/Files product behavior. If the host API is absent or incompatible, the add-on fails closed and explains the compatibility requirement.

L6. Provenance decision: preserve the upstream MIT notice and trademark boundaries; make upstream-facing changes explicit through a public API proposal, small marked adapter regions, a human-readable attribution document, and a machine-readable manifest tied to an upstream base commit.

L7. Planning snapshot: upstream repository `https://github.com/Gentleman-Programming/gentle-shell.git`, upstream `main` base commit `bac4c4a3ffc4e8455548691dcd820d60fd3c58f6`. The local checkout also contains three commits beyond that base; those commits are not attributed to Sidebar Steroids without an explicit file-level diff.

L8. The first PRD writer ran 20 turns and 46 tool calls but produced no file before the provider rate-limited and the task timed out. The absence of a partial artifact was confirmed. Recovery uses the compact PRD authoring contract above so the next bounded writer receives a smaller, file-local brief.

L9. Two subsequent single-writer recoveries also timed out before creating `PRD.md`: one stalled after bash and one after read. The third user-requested retry stalled after read with no artifact. Recovery now splits the PRD into four sequential, same-surface writer units: structure plus sections 1-5, requirements and baseline (6-7), architecture/install/security (8-11), and verification/delivery/traceability (12-15). Units are strictly ordered and never write concurrently.

L10. Repository-boundary decision: keep this checkout as the upstream reference and preparation surface for a generic, independently reviewable sidebar host API contribution. The installable Sidebar Steroids product, package source, release lifecycle, and product-specific tests belong in a dedicated repository with a distinct primary identity, provisionally `andrikonbeat/sidebar-steroids`, subject to registry and trademark review. Product-specific behavior must not be mixed into the generic upstream PR. Remote repository creation and publication remain separate human-owned actions and are not performed by this documentation task.

L11. PRD and attribution artifacts completed and parent-verified. Evidence: 15/15 numbered PRD sections; 15 unique FR IDs; 15 unique AC IDs with same-row FR-to-AC mappings; 8 unique NFR IDs; S1-S8 present in feature tracker and traceability; zero remaining placeholders; all 12 manifest change entries remain `planned`; pinned upstream base `bac4c4a3ffc4e8455548691dcd820d60fd3c58f6` consistent across PRD, attribution protocol, and manifest; relative Markdown links resolve; no trailing whitespace; manifest parses as valid JSON. The files remain uncommitted pending migration to the dedicated repository.

L12. Repository bootstrap: migrated PRD.md, UPSTREAM_ATTRIBUTION.md, upstream-manifest.json, and this ODD tracker into the dedicated repository `andrikonbeat/sidebar-steroids`. Removed untracked product files from the upstream checkout (`gentle-shell`), keeping the upstream fork strictly reserved for the generic sidebar host API (Phase 1). Initial bootstrap committed as `cb0697c`.

L13. Upstream API specification: added `docs/proposals/sidebar-host-api-v1.md` with the exact descriptor shape, global symbol key, semver negotiation rules, view lifecycle, error-isolation contract, and generic verification plan. Committed as `f62dc15` in the dedicated repository; the same proposal will be carried into the generic upstream branch.

L14. Tooling decisions for the add-on skeleton: use TypeScript with `tsup` for bundling and `vitest` for tests; do not declare `gentle-pi` as a dependency or peer dependency because the negotiated global-symbol handshake is the only host integration surface. These decisions apply to Phase 2 and may be revised only if the upstream contract requires a narrowly documented correction.
