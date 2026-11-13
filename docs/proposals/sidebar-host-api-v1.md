# RFC / Proposal: Gentle Shell Sidebar Host API v1 (`gentle.sidebar.host.v1`)

- **Author**: Andrik (`andrikonbeat`)
- **Status**: Draft / Proposed for Upstream
- **Target Upstream**: `Gentleman-Programming/gentle-shell`
- **Base Commit**: `bac4c4a3ffc4e8455548691dcd820d60fd3c58f6`
- **Capability Identifier**: `gentle.sidebar.host`
- **Protocol Key**: `Symbol.for("gentle.sidebar.host.v1")`

---

## 1. Summary & Motivation

Gentle Shell provides a rich 50-column fullscreen sidebar rail (at terminal widths $\ge 140$ columns) that currently hardcodes its internal sections (Todos, Agents, Changes, Jobs, Review, etc.) into a single monolithic "STATUS" rail.

Third-party extensions and advanced developer tools (such as visual Git graphs, interactive file explorers, or domain-specific project monitors) need the ability to contribute alternative sidebar views without:
1. Monkey-patching `tui.terminal` or upstream private modules at runtime.
2. Relying on fragile relative or deep imports across separately loaded extension package roots.
3. Recreating the desktop layout engine, 140-column breakpoint detection, ScrollView mechanics, or terminal theme integration.

This specification defines a lightweight, backward-compatible, generic capability host for Gentle Shell. It enables dynamic registration, switching, rendering, and lifecycle disposal of custom sidebar views through a well-known global Symbol handshake.

### Key Principles
- **Generic & Unopinionated**: The host API contains zero product concepts. It knows nothing about Git, Files, GitHub, attribution, or any specific add-on.
- **Fail-Closed & Isolated**: An absent host, a version mismatch, or an uncaught runtime error in a custom view never crashes the terminal or corrupts the default STATUS view.
- **STATUS Invariant**: The default Gentle Shell STATUS rail remains 100% untouched and active by default. When no extension views are registered, the host renders identically to vanilla Gentle Shell, with zero visual diff.

---

## 2. Discovery Protocol & Publication Key

### 2.1 Well-Known Global Symbol
Pi extensions and Gentle Shell may be resolved and loaded from distinct package roots or isolated module loaders within the same Node process. A standard ECMAScript package import cannot bridge independently loaded extension trees.

The discovery protocol relies on a well-known process-global Symbol:

```typescript
const SIDEBAR_HOST_SYMBOL = Symbol.for("gentle.sidebar.host.v1");
```

### 2.2 Host Publication
During TUI initialization in `gentle-shell` (when the fullscreen layout is attached), the host publishes an immutable, frozen capability descriptor to `globalThis[SIDEBAR_HOST_SYMBOL]`:

```typescript
if (!(SIDEBAR_HOST_SYMBOL in globalThis)) {
  Object.defineProperty(globalThis, SIDEBAR_HOST_SYMBOL, {
    value: Object.freeze(createSidebarHostDescriptor(tui)),
    writable: false,
    configurable: true,
    enumerable: false,
  });
}
```

When the layout is detached or disposed, the property is cleanly deleted.

---

## 3. Capability Descriptor & Version Negotiation

The capability descriptor acts as the entry gate for add-ons. It establishes capability identity, canonical host instance identity, and supported API versions.

### 3.1 Descriptor TypeScript Definitions

```typescript
export interface SidebarHostDescriptorV1 {
  /** Fixed capability identifier */
  readonly capability: "gentle.sidebar.host";

  /** Unique canonical host instance identifier for detecting duplicate publishers */
  readonly hostInstanceId: string;

  /** SemVer version of the host implementation */
  readonly hostVersion: string;

  /** List of supported API contract versions, e.g. ["1.0.0"] */
  readonly supportedApiVersions: readonly string[];

  /**
   * Negotiate an API session with the host.
   * Returns a versioned host client handle if compatible, or an error reason if incompatible.
   */
  negotiate(request: SidebarHostNegotiateRequest): SidebarHostNegotiateResult;
}

export interface SidebarHostNegotiateRequest {
  /** Package or extension identifier initiating negotiation */
  readonly clientName: string;

  /** SemVer version of the requesting client */
  readonly clientVersion: string;

  /** API versions supported by the client, in preference order */
  readonly requestedApiVersions: readonly string[];
}

export type SidebarHostNegotiateResult =
  | {
      readonly success: true;
      readonly apiVersion: string;
      readonly host: SidebarHostApiV1;
    }
  | {
      readonly success: false;
      readonly reason: string;
      readonly supportedVersions: readonly string[];
    };
```

### 3.2 Negotiation Invariants
1. **SemVer Major Compatibility**: For `v1`, negotiation succeeds only if both parties agree on major version `1` (e.g. `^1.0.0`).
2. **Deterministic Selection**: The host selects the highest mutually supported version.
3. **Duplicate Publisher Guard**: If multiple objects attempt to publish under `Symbol.for("gentle.sidebar.host.v1")` with mismatched `hostInstanceId`s, clients fail closed and refuse registration.
4. **Passive Fallback**: If negotiation returns `success: false` or if `globalThis[Symbol.for("gentle.sidebar.host.v1")]` is absent, the extension must remain passive and present diagnostic guidance without altering the terminal.

---

## 4. Host Client API (`SidebarHostApiV1`)

Upon successful negotiation, the extension receives a scoped client interface bound to the canonical host:

```typescript
export interface SidebarHostApiV1 {
  /** The agreed API version (e.g. "1.0.0") */
  readonly version: string;

  /**
   * Register a custom sidebar view.
   * Registration is atomic: failure leaves no partial state.
   */
  registerView(view: SidebarViewDescriptorV1): SidebarViewRegistrationV1;

  /**
   * Unregister a previously registered view by id. Idempotent.
   */
  unregisterView(viewId: string): void;

  /**
   * Programmatically switch the active rail view.
   * Returns true if switched, false if viewId was unknown.
   */
  switchView(viewId: string): boolean;

  /**
   * Return the identifier of the currently active view.
   * Defaults to "status".
   */
  getActiveView(): string;

  /**
   * List all registered views, including the built-in default "status" view.
   */
  listViews(): readonly SidebarViewSummaryV1[];

  /**
   * Request an invalidation and re-render of the sidebar rail.
   */
  invalidate(): void;
}

export interface SidebarViewRegistrationV1 {
  /** The registered view identifier */
  readonly viewId: string;

  /** Unregister and dispose this view registration */
  dispose(): void;
}

export interface SidebarViewSummaryV1 {
  readonly id: string;
  readonly label: string;
  readonly isDefault: boolean;
}
```

---

## 5. View Descriptor Contract (`SidebarViewDescriptorV1`)

A custom view implements the following descriptor:

```typescript
export interface SidebarViewDescriptorV1 {
  /**
   * Package-qualified unique view identifier.
   * Must not be "status" (reserved for built-in rail).
   * Example: "sidebar-steroids:git"
   */
  readonly id: string;

  /**
   * Short, accessible label displayed in the switcher footer.
   * Recommended length: 3 to 8 characters (e.g. "GIT", "FILES").
   */
  readonly label: string;

  /**
   * Optional display ordering priority in the switcher (lowest number first).
   * Default ordering: insertion order after built-in "status".
   */
  readonly order?: number;

  /**
   * Optional accessible description for tooltips / accessibility.
   */
  readonly description?: string;

  /**
   * Render callback producing terminal lines for the view.
   *
   * @param context Render parameters (viewport width, theme).
   * @returns Array of formatted terminal strings (ANSI styled).
   */
  render(context: SidebarViewRenderContext): readonly string[];

  /**
   * Optional cheap digest function.
   * If provided, the host memoizes rendered lines and skips calling `render()`
   * as long as `digest()` returns the identical string.
   */
  digest?(): string;

  /**
   * Optional mouse event handler for pointer interactions inside the view.
   */
  handleMouse?(event: SidebarViewMouseEvent): SidebarViewMouseResult | void;

  /**
   * Lifecycle notification called when this view becomes the active rail view.
   */
  onActivate?(): void;

  /**
   * Lifecycle notification called when this view is deactivated.
   */
  onDeactivate?(): void;

  /**
   * Cleanup callback invoked when the view is unregistered or host shuts down.
   * Must be idempotent.
   */
  dispose?(): void;
}

export interface SidebarViewRenderContext {
  /** Usable width for content lines (excluding rail padding/borders) */
  readonly width: number;

  /** Theme color helpers from the active Gentle Shell theme */
  readonly theme: {
    fg(role: string, text: string): string;
    bg(role: string, text: string): string;
    bold(text: string): string;
  };
}

export interface SidebarViewMouseEvent {
  readonly type: "up" | "down" | "move" | "wheel";
  readonly button: "left" | "right" | "middle" | "none";
  readonly x: number;
  readonly y: number;
  readonly wheelDelta?: number;
}

export interface SidebarViewMouseResult {
  readonly handled: boolean;
  readonly render?: boolean;
}
```

---

## 6. View Coordination & Pinned Footer Switcher

### 6.1 State Machine
1. **Initial Active Route**: The host initializes with active route set to `"status"`.
2. **Immutable Default**: The `"status"` view is managed internally by Gentle Shell and cannot be unregistered or replaced.
3. **Switching Logic**:
   - Switching to an unregistered view ID is a no-op returning `false`.
   - When switching from view $A$ to view $B$, the host safely calls $A$`onDeactivate?()`, updates the active view pointer, and calls $B$`onActivate?()`.
   - If an active custom view is unregistered, the host immediately deactivates it and falls back to `"status"`.

### 6.2 Bottom-Pinned Route Switcher
- The switcher is positioned at the bottom of the rail, below the `ScrollView` viewport.
- **Zero-View Invariant**: If only `"status"` exists (no custom views registered), the footer switcher is **omitted entirely**.
- **Multi-View Rendering**: When $\ge 1$ custom views are registered, the switcher displays as a compact horizontal segmented control:
  ```text
  ── [ STATUS ] ─ [ GIT ] ─ [ FILES ] ──
  ```
  - The active view is highlighted using theme accent colors and reverse video.
  - Clicking any tab triggers `switchView(id)`.
  - Keyboard shortcut or command (`/gentle:sidebar-next`) allows cycling between available views.

---

## 7. Error Isolation & Fault Tolerance

Custom view code is untrusted relative to host stability. The host enforces strict containment:

1. **Callback Guarding**: All invocations of `render()`, `digest()`, `handleMouse()`, `onActivate()`, `onDeactivate()`, and `dispose()` are wrapped in `try ... catch` blocks.
2. **Digest Errors**: If `digest()` throws, the host disables memoization for that frame, logs an internal diagnostic warning, and proceeds to call `render()`.
3. **Render Errors**: If `render()` throws:
   - The host catches the exception and prevents it from bubbling to the TUI event loop.
   - The host renders a localized 3-line error container in the rail:
     ```text
     ┌ Error: view "GIT" failed ────────┐
     │ Render error occurred. Check logs│
     └──────────────────────────────────┘
     ```
   - The footer switcher remains fully operational, enabling the user to immediately switch back to `"status"`.
4. **Disposal Errors**: Exceptions thrown during `dispose()` are caught and swallowed/logged so they cannot interrupt host cleanup or other registrations.

---

## 8. Verification & Upstream Test Plan

The upstream implementation of this specification in `Gentleman-Programming/gentle-shell` must be verified by dedicated, generic automated tests under `tests/sidebar-host-api.test.ts`:

1. **Publication Verification**: Verify `globalThis[Symbol.for("gentle.sidebar.host.v1")]` exists and is frozen when layout is active, and removed on layout disposal.
2. **Negotiation Matrix**:
   - Valid request with compatible SemVer $\to$ returns `{ success: true, apiVersion: "1.0.0", host: ... }`.
   - Incompatible major version (e.g. requested `2.0.0`) $\to$ returns `{ success: false, reason: "..." }`.
   - Invalid or empty request $\to$ fails gracefully.
3. **Registration & Switch Lifecycle**:
   - Register view $\to$ appears in `listViews()`.
   - Switch active view $\to$ calls `onDeactivate()` on previous view and `onActivate()` on new view.
   - Render rail $\to$ paints content of the active custom view instead of status sections.
4. **Digest Cache Memoization**:
   - Return static digest $\to$ verify `render()` is only called on initial paint, not on subsequent rail invalidations with unchanged digest.
   - Change digest $\to$ verify `render()` is called.
5. **Fault Isolation**:
   - Register view whose `render()` throws $\to$ layout remains intact, localized error box paints, no unhandled rejection.
   - User clicks `"status"` in footer switcher $\to$ rail returns to normal status display.
6. **Unregistration & Fallback**:
   - Unregister active custom view $\to$ active view reverts cleanly to `"status"`.
   - Unregister last custom view $\to$ footer switcher is removed from layout.
