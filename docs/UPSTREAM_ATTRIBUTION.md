# Sidebar Steroids upstream attribution protocol

This document defines a low-noise contribution path for a Sidebar Steroids feature that depends on upstream Gentle Shell. It separates a possible generic upstream API contribution from the separately distributed Sidebar Steroids add-on and from local product documentation.

> **Planning status:** Every upstream change in `upstream-manifest.json` is `planned`. No upstream runtime code has been changed by this artifact. The three local commits beyond the pinned upstream base are not attributed to Sidebar Steroids without an explicit file-level diff.

## Decision summary

| Concern | Decision |
| --- | --- |
| Upstream relationship | Treat Gentle Shell as a third-party repository owned by its upstream maintainers. Preserve its MIT notice, original authorship, Git history, and trademarks. |
| Upstream PR | If accepted, submit only a generic, backward-compatible sidebar extension seam with focused tests and generic documentation. It must be independently reviewable without knowledge of Sidebar Steroids. |
| Product delivery | Distribute Sidebar Steroids as a separate Pi npm package. Do not patch, fork, rebrand, or present it as an official Gentle Shell or `gentle-pi` distribution. |
| Attribution | Use normal Git authorship and history, narrowly scoped `sidebar-extension-api` source markers, this document, and the machine-readable manifest as complementary evidence. |
| Current evidence | The manifest records plans only. Actual files and hunks must be refreshed from a real candidate diff before submission. |

## Ownership and scope boundaries

The following boundaries must remain visible in issues, pull requests, package metadata, and release notes.

| Area | Owner and treatment |
| --- | --- |
| Existing upstream Gentle Shell code | Owned and maintained upstream. A contributor may propose changes but may not remove upstream notices, rewrite published history, or claim the project. |
| Generic sidebar extension API | A **planned** upstream contribution. It exposes a small host seam and contains no Sidebar Steroids product behavior. |
| Sidebar Steroids add-on | A separate package maintained by the contributor. It owns the STATUS/GIT/FILES product behavior, Git and file views, compatibility checks, and its own licensing. |
| Local product and tracker documents | Local-only planning and provenance records. They are not upstream API changes and need not be included in a generic upstream PR unless maintainers request them. |
| Upstream names and logos | Governed by `TRADEMARKS.md`, independently of the MIT software license. |

A fork or modified distribution must retain the upstream MIT notice and original copyright attribution, preserve upstream Git history, and use a distinct primary name and branding. It must never be described as official, endorsed, sponsored, certified, or partnered. Truthful references such as “compatible with Gentle Shell” are acceptable only when they do not imply a relationship that does not exist.

## Low-noise contribution process

### 1. Establish a reviewable base

1. Start from the upstream repository and its `main` branch, not from an unrelated product branch.
2. Record the exact base commit in the manifest. The current planning base is:

   ```text
   bac4c4a3ffc4e8455548691dcd820d60fd3c58f6
   ```

3. Create a focused candidate branch from that base. If upstream `main` advances, either rebase the candidate onto the pinned base or deliberately refresh the recorded base and repeat the complete diff review.
4. Preserve upstream commits and authorship. Do not squash, rewrite, or replace published upstream history.

### 2. Keep the upstream PR generic

The upstream PR should answer one question: “Can an optional extension contribute a sidebar part through a stable, generic contract?” It should not contain the Sidebar Steroids product, package, branding, Git dashboard, file explorer, or add-on implementation.

A defensible upstream PR contains only:

- a small public contract module;
- minimal host wiring at existing extension seams;
- focused contract, lifecycle, cleanup, and default-behavior tests;
- generic documentation and an example that is not tied to Sidebar Steroids.

The contract should be additive and opt-in. Existing Status behavior must remain unchanged when no extension is installed, and an incompatible or missing contract must not require monkey-patching upstream internals.

A separate issue may introduce the product proposal and ask whether the generic seam is acceptable. The issue may link to Sidebar Steroids as context, but the implementation PR must remain understandable and reviewable without it.

### 3. Preserve notices, authorship, and marks

- Keep the upstream `LICENSE` unchanged unless the maintainer separately approves a license change.
- Preserve `Copyright (c) 2025 Mario Zechner` and the complete MIT notice in copies or substantial portions of upstream software.
- Preserve upstream Git history, original authorship, and accurate contributor metadata.
- Follow `TRADEMARKS.md` for the `gentle-shell` and `gentle-pi` names, logos, package identifiers, and compatibility wording.
- Use a distinct primary name for any fork or modified distribution. The add-on must not be presented as upstream `gentle-pi`.
- Do not use upstream logos as the add-on’s primary branding or make an official-status claim.

### 4. Add attribution only where it helps review

Use ordinary Git diffs, commit authorship, and the manifest for the normal attribution trail. Add a source marker only at an extension seam where a reviewer may otherwise be unable to distinguish an intentional upstream API adapter from incidental code.

Do not put a contributor marker on every changed line. Source comments are review aids, not a replacement for Git history or legal notices.

### 5. Refresh the manifest from the candidate diff

Before opening the PR, compare the candidate head with the pinned base and update the manifest with the actual changed files and hunks. Follow the procedure in [Refresh the actual change set](#refresh-the-actual-change-set).

### 6. Run the contribution checks

Run the focused contract and regression tests, the manifest completeness check, and the release checks. A changed upstream file that is missing from the manifest is a blocking CI failure.

### 7. Submit with the attribution template

Use the [PR or issue attribution template](#pr-or-issue-attribution-template). State that the upstream change is generic, list the exact base and candidate commits, and link the add-on only as optional product context.

## What each attribution mechanism proves

These mechanisms are complementary. None substitutes for all the others.

| Mechanism | What it proves | What it does not prove |
| --- | --- | --- |
| `LICENSE` | The MIT permission and conditions, the required inclusion of the copyright and permission notice, and the original copyright notice. | Trademark permission, endorsement, or the authorship of every later modification. |
| `TRADEMARKS.md` | The permitted use of upstream names, logos, package identifiers, compatibility references, and the need for permission for official claims or confusingly similar names. | Copyright ownership, the MIT grant, or which commits a contributor authored. |
| Git attribution | Repository provenance through commit identity, commit and committer metadata, ancestry, branch history, and optional trailers such as `Co-authored-by`. | Retention of a license notice in a distributed artifact, trademark permission, or completeness of a proposed file manifest. |
| Package metadata | The identity of a published artifact: package name, version, repository, declared license, and related metadata. It helps consumers identify what they installed. | A substitute for retaining the full license text and notices, a trademark grant, or a complete record of upstream commits. |
| Source markers | The location and intended ownership of a non-obvious extension seam, including the contributor handle and contract version. | Legal provenance, a complete changed-file inventory, official status, or authorship of surrounding lines. |
| `upstream-manifest.json` | Machine-readable provenance, planned scope, classifications, actual diff evidence after refresh, expected checks, and release gates. | Evidence that a change was implemented, accepted, or merged; a license grant; or trademark permission. |

## Source marker convention

Use this exact identifier in TypeScript source comments at an extension seam:

```ts
// sidebar-extension-api: v1 | contributor:andrikonbeat | reason:<short reason>
```

Rules:

1. **One marker per coherent seam.** Place it immediately before or inside the registration, adapter, initialization, render, or disposal boundary that represents the upstream API seam.
2. **Use the same grammar everywhere.** The marker must contain `sidebar-extension-api`, a contract version such as `v1`, `contributor:<handle>`, and a short lowercase reason.
3. **Do not litter the diff.** Do not mark every changed line, ordinary implementation logic, tests, or prose.
4. **Do not use a marker as a license or ownership statement.** The marker says where to look; Git and the manifest carry provenance.
5. **Keep the add-on generic.** A separate add-on may use its own normal package headers, but it must not describe itself as upstream Gentle Shell code.

Example at a narrow adapter boundary:

```ts
// sidebar-extension-api: v1 | contributor:andrikonbeat | reason:generic sidebar registration seam
export function registerSidebarExtension(/* ... */) {
  // Upstream-owned host wiring.
}
```

The exact API shape is a plan. The marker convention is stable regardless of the final signature.

## Provenance record

The machine-readable record is [`upstream-manifest.json`](./upstream-manifest.json). It currently records:

- the upstream repository URL, `main` branch, pinned base commit, MIT license, and trademark-policy path;
- contributor handle `andrikonbeat`, role, scope, and `planned` status;
- the separate-package delivery model, provisional package name, installation mechanism, and compatibility strategy;
- file-level planned upstream changes with purpose, change type, public-contract impact, and marker strategy;
- local-only documentation and feature-tracker files that are explicitly not upstream API changes;
- verification commands, expected evidence, release gates, and current gaps.

Each future upstream implementation entry should also contain refreshed evidence:

```json
{
  "path": "lib/example-extension-seam.ts",
  "status": "implemented",
  "actualChangedFiles": ["lib/example-extension-seam.ts"],
  "actualChangedHunks": [
    {
      "hunk": "@@ -10,2 +10,4 @@",
      "symbol": "registerExampleExtension",
      "classification": "upstream-extension-seam",
      "marker": "sidebar-extension-api: v1 | contributor:andrikonbeat | reason:generic registration seam"
    }
  ]
}
```

The example is a target schema, not a statement that the file or hunk exists. Until a candidate diff exists, entries remain `planned` and the actual file/hunk lists remain empty.

## Refresh the actual change set

The manifest must be based on a diff, not on commit messages, branch names, or the three local commits beyond the pinned base.

### 1. Select the candidate head

Work from a committed candidate branch so new files are included in the comparison.

```bash
BASE=bac4c4a3ffc4e8455548691dcd820d60fd3c58f6
HEAD=$(git rev-parse HEAD)
git cat-file -e "${BASE}^{commit}"
git merge-base --is-ancestor "$BASE" "$HEAD"
```

Expected evidence:

- `git cat-file -e` exits successfully, proving the pinned base is available locally.
- `git merge-base --is-ancestor` exits successfully, proving the candidate descends from the pinned base.
- If ancestry fails, stop. Rebase or cherry-pick the candidate onto the pinned base, or explicitly update the manifest’s base after reviewing the new diff. Do not silently change the comparison point.

### 2. Extract changed files and hunks

```bash
git diff --name-status "$BASE" "$HEAD"
git diff --unified=0 "$BASE" "$HEAD"
git diff --check "$BASE" "$HEAD"
git log --format='%H%x09%an%x09%ae%x09%s' "$BASE..HEAD"
```

Expected evidence:

- the name-status output is the complete changed-file set for the candidate;
- the zero-context diff exposes exact hunk headers for the manifest;
- `git diff --check` exits successfully;
- the log output corroborates commit authorship and history.

### 3. Classify every changed path

Classify each changed path exactly once:

| Classification | Meaning | Manifest treatment |
| --- | --- | --- |
| `upstream-extension-api` | A generic public contract or host seam. | Must match a `plannedUpstreamChanges` entry. |
| `upstream-api-test` | A test that protects the generic contract. | Must match a `plannedUpstreamChanges` entry. |
| `upstream-api-documentation` | Generic documentation for the contract. | Must match a `plannedUpstreamChanges` entry. |
| `upstream-build-or-metadata` | A build, dependency, or package-metadata change needed by the generic API. | Must be explicitly justified in a manifest entry; do not add it implicitly. |
| `product-specific-upstream` | Sidebar Steroids behavior, branding, or add-on delivery placed upstream. | Not allowed in the generic upstream PR; remove it or split it into an independently justified upstream proposal. |
| `local-only` | Sidebar Steroids documentation, tracker, or add-on code outside the upstream PR. | Must match a `localOnlyChanges` entry and must not appear in the upstream candidate diff. |

If a path appears in both upstream and local-only scopes, the candidate is invalid until the classification is corrected.

### 4. Record the evidence

For every matching `plannedUpstreamChanges` entry:

1. Record the actual changed file path or paths.
2. Record each changed hunk header and the relevant symbol or seam.
3. Record the classification, rationale, and marker strategy.
4. Record whether the hunk has one `sidebar-extension-api` marker or deliberately has none because it is not an extension seam.
5. Update the top-level accounting block with the refreshed file and hunk evidence.

A planned path absent from the candidate diff is stale. Remove it or record an explicit reason before submission. A changed path absent from the manifest is a blocking error.

## CI completeness check

The contribution CI must compute the changed upstream paths between the pinned base and the candidate head, then compare that set with the manifest.

The planned checker command is:

```bash
node scripts/verify-upstream-manifest.mjs \
  --base bac4c4a3ffc4e8455548691dcd820d60fd3c58f6 \
  --head "$(git rev-parse HEAD)" \
  --manifest docs/sidebar-steroids/upstream-manifest.json
```

This checker is specified by this documentation task but is not implemented by it. It must be added when implementation begins.

The checker must fail with a non-zero exit status and list every missing path when:

- a changed upstream file is missing from `plannedUpstreamChanges`;
- a changed path is listed as both upstream and local-only;
- an implemented upstream entry lacks refreshed file or hunk evidence;
- a non-planned entry claims implementation without a matching candidate diff; or
- the manifest’s pinned base differs from the base supplied to the checker.

The current documentation-only state intentionally has no upstream implementation diff. In that state, a planning-mode check confirms that all entries are still `planned`; it does not claim that the planned API exists.

## Release checks

All checks below are blocking gates for the relevant release. A release record should link the command output or reviewer decision as evidence.

### License and notice retention

- [ ] The upstream `LICENSE` remains the MIT license with `Copyright (c) 2025 Mario Zechner`.
- [ ] Copies or substantial portions of upstream code contain the required MIT copyright and permission notice.
- [ ] Any modified upstream distribution retains the upstream notice and is distributed under a distinct primary name and branding.
- [ ] Third-party notices for copied or bundled upstream code are present in the add-on artifact.

### Trademark wording

- [ ] User-facing text makes only truthful, nominative compatibility statements.
- [ ] No text, logo, package identifier, screenshot, or metadata implies official status, sponsorship, endorsement, certification, or partnership.
- [ ] The add-on uses a distinct primary name; `pi-sidebar-steroids` remains provisional until registry and trademark review are complete.
- [ ] The upstream names and logos are not used as the add-on’s primary branding.
- [ ] Any proposed confusingly similar name or official-style use has prior written permission from the trademark owner.

### Contributor attribution

- [ ] Git author and committer metadata accurately identify the contributor.
- [ ] A `Co-authored-by` trailer is used only when accurate and appropriate.
- [ ] The manifest identifies `andrikonbeat`, the exact base, changed files, hunks, and classifications.
- [ ] The PR identifies the generic upstream scope and does not imply ownership of upstream history.

### Independent add-on licensing

- [ ] The separate add-on has its own explicit `LICENSE` and unambiguous package metadata.
- [ ] The add-on is not described or packaged as a modified distribution of `gentle-pi`.
- [ ] The add-on does not patch or overwrite the installed upstream package.
- [ ] If upstream code is copied, the required upstream MIT notice is included and the package does not imply that upstream endorses the add-on.

### API and package compatibility

- [ ] The upstream API is generic, opt-in, backward-compatible, and independently tested.
- [ ] Existing Status rendering remains unchanged when no extension is registered.
- [ ] Extension disposal and failure paths are covered by tests.
- [ ] The add-on detects an absent or incompatible host API, fails closed with a clear message, and leaves existing behavior intact.
- [ ] The add-on’s package metadata and installation instructions describe the separate package model accurately.

### Provenance and completeness

- [ ] The actual candidate diff was refreshed against the pinned base.
- [ ] Every changed upstream file is represented in the manifest.
- [ ] CI passes the manifest completeness check.
- [ ] The attribution and release evidence is attached to the PR or release record.

## PR or issue attribution template

Use the relevant sections without implying approval or implementation that has not occurred.

```markdown
## Summary

<!-- One generic upstream outcome. Do not describe an unrelated product implementation. -->

## Scope

- Proposed upstream change: <files and generic behavior>
- Explicitly out of scope: <product-specific behavior, branding, and package delivery>
- Upstream base commit: `<full SHA>`
- Candidate commit: `<full SHA>`

## Provenance

- Contributor: `andrikonbeat`
- Changed files and classifications: <link to refreshed upstream-manifest.json>
- Source-marker convention: `sidebar-extension-api: <version> | contributor:<handle> | reason:<reason>`
- Git history: <branch or commit links>

## Licensing and attribution

- Upstream MIT notice retained: yes
- Existing upstream copyright notice retained: yes
- Separate add-on licensing and notices: <link or N/A>
- Trademark wording review: <result; do not claim official status>

## Compatibility and risk

- Existing behavior without an extension: <expected unchanged behavior>
- Failure behavior for absent or incompatible API: <expected fail-closed behavior>
- Migration or breaking changes: <none or details>

## Verification

- <focused test commands and results>
- <manifest completeness command and result>
- <license, notice, and trademark release checks and result>

## Context (optional)

<!-- Link Sidebar Steroids only as non-normative product context. -->
```

## Current gaps and next evidence

The following evidence does not yet exist and must not be implied by the current artifacts:

- an upstream implementation branch or diff;
- actual changed-file and hunk records against the pinned base;
- the planned `scripts/verify-upstream-manifest.mjs` CI checker;
- upstream maintainer acceptance of the generic API;
- final trademark review and package-registry availability for the provisional package name;
- an independently licensed, installable Sidebar Steroids add-on.

Until those gaps close, the correct statement is: **a separate add-on is planned against a possible generic upstream API; no upstream code change has been completed or attributed by this document.**
