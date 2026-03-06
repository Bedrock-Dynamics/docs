# Design: Substrate Docs Update — 2026-03-06

## Goal

Bring the Substrate reference docs up to date with the latest codebase changes.
Fix incorrect information in existing pages (rotate gizmo, spawn location,
MuJoCo, tool count) and add authoritative reference content for the new
simulation container MCP server.

## Scope

7 files touched: 5 existing pages corrected, 1 new reference page, 1 nav file.

---

## File-by-File Changes

### 1. `substrate/reference/supported-platforms.mdx`

**Remove MuJoCo row.**

MuJoCo was listed as "Planned" but the native simulation code was removed in the
Phase 11 cleanup (R4). Keeping it in the matrix is misleading.

Change: delete the `| MuJoCo | Simulator | Planned | ... |` row.

---

### 2. `substrate/features/scene-authoring.mdx`

**Two corrections:**

**a) Rotate gizmo — was listed as Planned, is now implemented.**

The "Planned Gizmo Modes" accordion currently reads:
> "Rotate and scale gizmo modes are planned for a future release."

Phase 10C implemented rotate. Only scale remains planned. Change accordion
title to "Rotate and Scale Gizmo Modes" and update body to:
> "Rotate mode is active — select an entity and press R (or use the toolbar) to
> switch. Drag the torus rings to rotate around X (red), Y (green), or Z
> (blue). Scale mode is planned for a future release."

**b) Model spawn location — local models spawn at camera focus, not world origin.**

The Info callout currently reads:
> "Models are spawned at the world origin."

Phase 10D implemented spawn-at-camera-focus for local Fuel cache models.
Change to:
> "Local Fuel cache models spawn at the camera's current focus point. Remote
> search results spawn at the world origin until remote thumbnails and
> camera-focus spawning are implemented."

---

### 3. `substrate/roadmap.mdx`

**Two corrections:**

**a) Remove MuJoCo from Planned.**
Delete the MuJoCo bullet from the Planned section.

**b) Fix scale gizmo description in In Progress.**
Currently: "The `GizmoMode` enum currently defines `Translate` and `Rotate`
variants. Scale is the next planned mode..."

Rotate is implemented. Change to:
> "The `GizmoMode` enum defines `Translate` and `Rotate` variants, both
> implemented. Scale is the next planned mode, requiring box-handle rendering
> and per-axis scale drag math."

---

### 4. `substrate/reference/ai-tools.mdx`

**Three corrections:**

**a) Meta description:** `"44 tools"` → `"49 built-in tools, plus simulation container tools"`

**b) Intro paragraph:** Change:
> "The Substrate AI Assistant has access to **49 tools**..."

To:
> "The Substrate AI Assistant has access to **49 built-in tools** organized
> into 10 categories. When a simulation is running, additional tools registered
> by the container's MCP server are available under a `sim0__` prefix (e.g.
> `sim0__arm`, `sim0__takeoff`). See [Simulation MCP Server](/substrate/reference/sim-mcp-server)."

**c) Code Editing section header:** `"10 tools"` → `"11 tools"` (the `task`
sub-agent tool is listed in this section but excluded from the count).

---

### 5. NEW: `substrate/reference/sim-mcp-server.mdx`

New authoritative reference page for the container-side MCP server.

**Sections:**
- **What it is**: A FastMCP server (`mcp_server.py`) auto-started inside every
  PX4+Gazebo simulation container. Runs on port 8090 (configurable via
  `MCP_PORT`). Exposes MAVSDK-native tools and a workflow prompt.
- **How it connects**: The IDE proxy discovers the server at startup via TCP
  port-forward. Tools are registered with the active AI agent session under a
  `sim0__` namespace (or `sim1__` for multi-vehicle). The agent sees them
  alongside its built-in tools.
- **Tool reference table**: arm, disarm, takeoff, land, go_to, set_flight_mode,
  return_to_launch, run_script, get_flight_state — each with parameters and
  return type.
- **Workflow prompt**: The `substrate:workflow` prompt is fetched once at
  connection and injected as a system block into the agent session. Documents
  the standard mission sequence and rules (no sleep(), 3-strike error protocol,
  NED frame for go_to, etc.).
- **Relationship to built-in tools**: Note that the sim tools (arm, takeoff,
  etc.) coexist with the built-in flight_arm, flight_takeoff tools. The native
  MCP tools use MAVSDK; the built-in tools use the proxy's MAVLink
  implementation. Both work.

---

### 6. `substrate/changelog.mdx`

Add new entries to "Latest Changes":

**Simulation:**
- **Container MCP server**: Each PX4+Gazebo simulation container now runs a
  native FastMCP server with MAVSDK-backed tools. The AI agent automatically
  gains `sim0__arm`, `sim0__takeoff`, `sim0__land`, `sim0__go_to`,
  `sim0__get_flight_state`, and more when a simulation starts. A
  `substrate:workflow` prompt with mission sequencing rules is injected at
  session start.
- **MCP port auto-discovery**: The proxy now finds a free TCP port for the
  container MCP server automatically. No manual port configuration needed even
  when multiple containers run simultaneously.

**Scene Authoring:**
- **Rotate gizmo mode**: The transform gizmo now supports rotate mode in
  addition to translate. Drag the torus rings around each axis. Switch modes
  with R (rotate) / T (translate).

---

### 7. `docs.json`

Add `"substrate/reference/sim-mcp-server"` to the Reference group in
navigation, after `"substrate/reference/ai-tools"`.

---

## Navigation Update

```json
{
  "group": "Reference",
  "pages": [
    "substrate/reference/ai-tools",
    "substrate/reference/sim-mcp-server",   // NEW
    "substrate/reference/flight-modes",
    "substrate/reference/coordinate-frames",
    "substrate/reference/docker-image",
    "substrate/reference/keyboard-shortcuts",
    "substrate/reference/supported-platforms",
    "substrate/reference/architecture"
  ]
}
```

---

## What Is NOT Changed

- `features/ai-assistant.mdx` — already accurate
- `features/simulation.mdx` — already accurate
- `features/3d-viewer.mdx` — already accurate
- `installation.mdx`, `quickstart.mdx` — unaffected
- `guides/*` — unaffected
- `reference/docker-image.mdx` — may need separate pass for container version
  bump; out of scope here
