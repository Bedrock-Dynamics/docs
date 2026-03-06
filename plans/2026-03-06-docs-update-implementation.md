# Substrate Docs Update — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Fix 5 stale reference pages, add one new `sim-mcp-server` reference page, and wire it into navigation.

**Architecture:** Pure MDX edits. No code changes. Each task is one file, independently committable. Work in `/Users/krnzt/Documents/BedrockDynamics/docs/`.

**Tech Stack:** Mintlify MDX (Mintlify component set: `<Note>`, `<Warning>`, `<Tip>`, `<Info>`, `<Accordion>`, `<AccordionGroup>`, `<Tabs>`, `<Tab>`, `<CardGroup>`, `<Card>`, `<Steps>`, `<Step>`). Navigation config in `docs.json`.

---

### Task 1: Remove MuJoCo from supported-platforms.mdx

**Files:**
- Modify: `substrate/reference/supported-platforms.mdx:21`

**Step 1: Read the current file to confirm line numbers**

Open `substrate/reference/supported-platforms.mdx`. The compatibility table ends with:
```
| MuJoCo | Simulator | Planned | Physics simulation integration (coming soon) |
```
This is the last row before the closing table. Confirm it's there.

**Step 2: Delete the MuJoCo row**

Remove this exact line:
```
| MuJoCo | Simulator | Planned | Physics simulation integration (coming soon) |
```

The table should end at `| SDF | Model Format | Full | Gazebo world and model description parsing |`.

**Step 3: Verify**

Read the file back. The table should have 9 rows (PX4 through SDF), no MuJoCo.

**Step 4: Commit**

```bash
git add substrate/reference/supported-platforms.mdx
git commit -m "docs: remove MuJoCo from compatibility matrix (native sim removed in R4)"
```

---

### Task 2: Fix rotate gizmo and spawn location in scene-authoring.mdx

**Files:**
- Modify: `substrate/features/scene-authoring.mdx`

**Step 1: Read the current file**

Open `substrate/features/scene-authoring.mdx`. Locate two items:
- The `<Accordion title="Planned Gizmo Modes">` block
- The `<Info>` callout about spawn location

**Step 2: Fix the gizmo accordion**

Replace:
```mdx
  <Accordion title="Planned Gizmo Modes">
    Rotate and scale gizmo modes are planned for a future release. The mode selection infrastructure exists but currently only translate is active.
  </Accordion>
```

With:
```mdx
  <Accordion title="Gizmo Modes">
    Two modes are available. Switch with the toolbar buttons or keyboard shortcuts:

    - **Translate** (T) — drag the axis arrows to move along X (red), Y (green), or Z (blue)
    - **Rotate** (R) — drag the torus rings to rotate around each axis

    Scale mode is planned for a future release.
  </Accordion>
```

**Step 3: Fix the spawn location Info callout**

Replace:
```mdx
<Info>
  Models are spawned at the world origin. After spawning, select the new entity and use the transform gizmo or pose editor to position it.
</Info>
```

With:
```mdx
<Info>
  Local Fuel cache models spawn at the camera's current focus point. Remote search results spawn at the world origin. After spawning, select the new entity and use the transform gizmo or pose editor to position it precisely.
</Info>
```

**Step 4: Verify**

Read the file back. Confirm:
- The accordion title is "Gizmo Modes" (not "Planned Gizmo Modes")
- The accordion mentions both Translate (T) and Rotate (R) as active
- The Info callout distinguishes local vs. remote spawn location

**Step 5: Commit**

```bash
git add substrate/features/scene-authoring.mdx
git commit -m "docs(scene-authoring): rotate gizmo is implemented; fix spawn location for local models"
```

---

### Task 3: Fix roadmap.mdx — remove MuJoCo, update gizmo description

**Files:**
- Modify: `substrate/roadmap.mdx`

**Step 1: Read the current file**

Open `substrate/roadmap.mdx`. Locate:
- The MuJoCo bullet in the **Planned** section
- The scale gizmo bullet in the **In Progress** section

**Step 2: Remove MuJoCo from Planned**

Delete this bullet entirely:
```mdx
- **MuJoCo physics integration.** Listed as "Planned" in the [Supported Platforms](/substrate/reference/supported-platforms) compatibility matrix. Will add MuJoCo as an alternative physics backend alongside Gazebo.
```

**Step 3: Fix the scale gizmo In Progress description**

Replace:
```mdx
- **Scale gizmo mode.** The `GizmoMode` enum currently defines `Translate` and `Rotate` variants. Scale is the next planned mode, requiring box-handle rendering and per-axis scale drag math. The infrastructure for mode switching already exists.
```

With:
```mdx
- **Scale gizmo mode.** The `GizmoMode` enum defines `Translate` and `Rotate` variants, both implemented and active. Scale is the next planned mode, requiring box-handle rendering and per-axis scale drag math. The mode-switching infrastructure already exists.
```

**Step 4: Verify**

Read the file back. Confirm:
- No MuJoCo bullet in the Planned section
- Scale gizmo description says Translate and Rotate are "both implemented and active"

**Step 5: Commit**

```bash
git add substrate/roadmap.mdx
git commit -m "docs(roadmap): remove MuJoCo (dropped); clarify rotate gizmo is implemented"
```

---

### Task 4: Fix tool counts in ai-tools.mdx

**Files:**
- Modify: `substrate/reference/ai-tools.mdx`

**Step 1: Read the file header**

Open `substrate/reference/ai-tools.mdx`. Check the frontmatter `description` field and the first paragraph in the body.

**Step 2: Fix the frontmatter description**

Replace:
```yaml
description: "Complete reference for all 44 tools available to the Substrate AI Assistant"
```
With:
```yaml
description: "Complete reference for all 49 built-in tools available to the Substrate AI Assistant"
```

**Step 3: Fix the intro paragraph**

Replace:
```mdx
The Substrate AI Assistant has access to **49 tools** organized into 10 categories.
```

With:
```mdx
The Substrate AI Assistant has access to **49 built-in tools** organized into 10 categories. When a simulation is running, additional tools registered by the container's native MCP server are also available — see [Simulation MCP Server](/substrate/reference/sim-mcp-server).
```

**Step 4: Fix the Code Editing section header**

Replace:
```mdx
10 tools for navigating and modifying your workspace.
```
With:
```mdx
11 tools for navigating and modifying your workspace.
```

**Step 5: Verify**

Read the file. Confirm:
- Frontmatter description says "49 built-in tools"
- Intro says "49 built-in tools" with link to sim-mcp-server
- Code Editing section says "11 tools"

**Step 6: Commit**

```bash
git add substrate/reference/ai-tools.mdx
git commit -m "docs(ai-tools): fix tool counts (49 built-in, 11 code editing); link to sim-mcp-server"
```

---

### Task 5: Create sim-mcp-server.mdx

**Files:**
- Create: `substrate/reference/sim-mcp-server.mdx`

**Step 1: Create the file with the following content**

```mdx
---
title: "Simulation MCP Server"
description: "Native MCP server running inside each simulation container, exposing MAVSDK-backed flight tools to the AI assistant."
---

Each PX4 + Gazebo simulation container starts a built-in MCP server alongside
the autopilot and bridge. The IDE proxy connects to it automatically at
simulation startup and registers its tools with the active AI agent session.

## How It Works

When the proxy connects to a running container, it:

1. Establishes a TCP connection to the container's MCP server (port 8090 by default)
2. Discovers the available tools via the MCP protocol
3. Registers them with the agent under a namespaced prefix: `sim0__` for the first container, `sim1__` for the second, and so on
4. Fetches the `substrate:workflow` prompt and injects it as a system block into the agent session

The agent then sees the container tools alongside its 49 built-in tools. There is no user configuration required.

<Note>
  The MCP port is allocated automatically. If the default port (8090) is in use,
  the proxy finds the next available port. This allows multiple simulation
  containers to run simultaneously without port conflicts.
</Note>

## Tool Reference

9 tools are exposed by the `px4-gazebo-humble` container image.

<AccordionGroup>
  <Accordion title="arm" icon="power-off">
    Arm the drone motors using MAVSDK. The vehicle must be in `ReadyToArm` state.
    Call `get_flight_state` first to verify.

    No parameters.

    **Returns:** `{"ok": true, "action": "arm"}`

    **Example prompt:** *"Arm the drone."*
  </Accordion>

  <Accordion title="disarm" icon="circle-stop">
    Disarm the drone motors. Only safe when the vehicle is landed.

    No parameters.

    **Returns:** `{"ok": true, "action": "disarm"}`

    **Example prompt:** *"Disarm after landing."*
  </Accordion>

  <Accordion title="takeoff" icon="plane-departure">
    Take off to a target altitude using the MAVSDK action API.

    | Parameter | Type | Default | Description |
    |-----------|------|---------|-------------|
    | `altitude_m` | float | 5.0 | Target altitude in metres AGL (clamped to 1–120) |

    **Returns:** `{"ok": true, "action": "takeoff", "altitude_m": 5.0}`

    **Example prompt:** *"Take off to 10 metres."*
  </Accordion>

  <Accordion title="land" icon="plane-arrival">
    Land at the current horizontal position.

    No parameters.

    **Returns:** `{"ok": true, "action": "land"}`

    **Example prompt:** *"Land the drone."*
  </Accordion>

  <Accordion title="go_to" icon="location-dot">
    Navigate to a position using MAVSDK Offboard mode. Coordinates are in
    **NED metres relative to the home position** (x = North, y = East, z = Up).

    | Parameter | Type | Default | Description |
    |-----------|------|---------|-------------|
    | `x` | float | — | North offset from home (metres) |
    | `y` | float | — | East offset from home (metres) |
    | `z` | float | — | Altitude AGL (metres, positive up) |
    | `yaw_deg` | float | 0.0 | Heading in degrees (0 = North, 90 = East) |

    The vehicle must be airborne. The tool returns immediately after sending the
    setpoint — use `wait_for_any` with a `sim_exec_background` altitude-check
    script to detect arrival.

    **Returns:** `{"ok": true, "action": "go_to", "x": 10, "y": 0, "z": 5, "yaw_deg": 0}`

    <Warning>
      `go_to` uses NED metres relative to home, not global coordinates. North is
      positive x, East is positive y, Up is positive z. This differs from the
      built-in `flight_goto` tool, which uses ENU metres.
    </Warning>

    **Example prompt:** *"Fly 20 metres north at 10 metres altitude."*
  </Accordion>

  <Accordion title="set_flight_mode" icon="sliders">
    Set the vehicle flight mode via MAVSDK.

    | Parameter | Type | Description |
    |-----------|------|-------------|
    | `mode` | string | `HOLD`, `RTL`, `LAND`, or `TAKEOFF` (case-insensitive) |

    For OFFBOARD position control, use `go_to` instead.

    **Returns:** `{"ok": true, "action": "set_flight_mode", "mode": "HOLD"}`

    **Example prompt:** *"Switch to hold mode."*
  </Accordion>

  <Accordion title="return_to_launch" icon="house">
    Return to the home position and land (RTL).

    No parameters.

    **Returns:** `{"ok": true, "action": "return_to_launch"}`

    **Example prompt:** *"Return to home."*
  </Accordion>

  <Accordion title="run_script" icon="terminal">
    Execute a Python script that already exists inside the container filesystem
    and return its output. For complex manoeuvres that exceed MCP tool granularity.

    | Parameter | Type | Default | Description |
    |-----------|------|---------|-------------|
    | `path` | string | — | Absolute path to the script inside the container |
    | `timeout_s` | float | 30.0 | Maximum execution time in seconds |

    **Returns:** `{"ok": true, "exit_code": 0, "stdout": "...", "stderr": ""}`

    <Tip>
      Combine with the built-in `write_file` + `sim_exec` tools: write a script
      to a temp path with `write_file`, copy it into the container with
      `sim_exec cp`, then run it with `run_script`.
    </Tip>

    **Example prompt:** *"Run the MAVSDK waypoint script at /tmp/mission.py."*
  </Accordion>

  <Accordion title="get_flight_state" icon="gauge">
    Read-only snapshot of the current flight state via MAVSDK telemetry.
    No side effects. Use before arming to verify the vehicle is ready.

    No parameters.

    **Returns:**
    ```json
    {
      "armed": false,
      "flight_mode": "FlightMode.HOLD",
      "altitude_m": 0.0,
      "latitude_deg": 47.3977419,
      "longitude_deg": 8.5455938,
      "battery_pct": 100.0
    }
    ```

    **Example prompt:** *"What is the drone's current altitude and battery level?"*
  </Accordion>
</AccordionGroup>

## Workflow Prompt

At connection, the proxy fetches the `substrate:workflow` prompt from the
container and injects it as a system block into the agent session. This prompt
defines the standard mission sequence and behavioural rules the agent must
follow. You do not need to repeat these rules in your prompts — the agent
already knows them.

**Standard mission sequence:**

```
1. list_simulations → start_simulation if not running
2. wait_for_simulation_ready(target_state="ReadyToArm")
3. sim0__arm → sim0__takeoff(altitude_m=5)
4. sim0__go_to(x, y, z) or sim_exec_background(script)
   → wait_for_any([task_id]) to detect arrival
5. sim0__land → sim0__disarm
```

**Behavioural rules injected into the session:**

- Never call `sleep()` — use `wait_for_any(timeout_ms=N)` loops instead
- After each `wait_for_any` timeout: call `sim0__get_flight_state`, then
  `capture_screenshot` if something looks wrong, then continue
- After 3 consecutive tool failures: stop and report to the user
- `sim0__go_to` uses NED metres relative to home (x = North, y = East, z = Up)
- Always check `sim0__get_flight_state` before arm; verify altitude after takeoff

## Relationship to Built-in Flight Tools

The container MCP tools and the built-in `flight_*` tools both control the same
vehicle. They differ in implementation:

| | Container MCP tools (`sim0__*`) | Built-in tools (`flight_*`) |
|--|--|--|
| **Implementation** | MAVSDK Python (inside container) | MAVLink UDP (proxy, on host) |
| **Coordinate frame** | NED metres relative to home | ENU metres |
| **Blocking** | Returns immediately after sending setpoint | Same |
| **Telemetry** | `sim0__get_flight_state` reads MAVSDK streams | `get_telemetry` reads proxy snapshot |

Either set works. The container tools are the recommended path for new missions
because they use the MAVSDK API directly and include the workflow prompt
guardrails.
```

**Step 2: Verify the file was written correctly**

Read `substrate/reference/sim-mcp-server.mdx`. Confirm:
- Frontmatter has `title` and `description`
- All 9 tools are documented in `<Accordion>` blocks
- The `go_to` warning about NED vs. ENU is present
- The workflow prompt section includes the mission sequence and rules
- The comparison table at the bottom is present

**Step 3: Commit**

```bash
git add substrate/reference/sim-mcp-server.mdx
git commit -m "docs: add sim-mcp-server reference page (FastMCP, MAVSDK tools, workflow prompt)"
```

---

### Task 6: Add changelog entries to changelog.mdx

**Files:**
- Modify: `substrate/changelog.mdx`

**Step 1: Read the current Latest Changes section**

Open `substrate/changelog.mdx`. The "Latest Changes" section currently has four
subsections: AI Assistant, Scene Authoring, Session Persistence, UX Improvements.

**Step 2: Add a new Simulation subsection before Scene Authoring**

Insert after the `### AI Assistant` block and before `### Scene Authoring`:

```mdx
### Simulation

- **Container MCP server**: Each PX4 + Gazebo simulation container now runs a
  native FastMCP server with MAVSDK-backed tools. When a simulation starts, the
  AI agent automatically gains `sim0__arm`, `sim0__disarm`, `sim0__takeoff`,
  `sim0__land`, `sim0__go_to`, `sim0__set_flight_mode`, `sim0__return_to_launch`,
  `sim0__run_script`, and `sim0__get_flight_state`. A `substrate:workflow` prompt
  with mission sequencing rules and anti-patterns is injected at session start.
  See [Simulation MCP Server](/substrate/reference/sim-mcp-server).
- **MCP port auto-discovery**: The proxy now finds a free TCP port for the
  container MCP server automatically. No manual configuration needed, even when
  multiple containers run simultaneously.
```

**Step 3: Update the Scene Authoring subsection**

The Scene Authoring subsection in changelog already mentions the transform gizmo
but doesn't specify that rotate mode was added. Find this bullet:

```mdx
- **Transform gizmo** with translate and rotate modes, GPU-pickable axis handles, screen-space scaling (80px), and 30 Hz update throttling.
```

It is already correct. No change needed here.

**Step 4: Verify**

Read `substrate/changelog.mdx`. Confirm the new "Simulation" subsection appears
before "Scene Authoring" and contains both bullets.

**Step 5: Commit**

```bash
git add substrate/changelog.mdx
git commit -m "docs(changelog): add container MCP server and port auto-discovery entries"
```

---

### Task 7: Add sim-mcp-server to docs.json navigation

**Files:**
- Modify: `docs.json`

**Step 1: Read the current Reference group**

Open `docs.json`. Find the Reference group in the navigation:

```json
{
  "group": "Reference",
  "pages": [
    "substrate/reference/ai-tools",
    "substrate/reference/flight-modes",
    ...
  ]
}
```

**Step 2: Insert the new page after ai-tools**

Replace:
```json
"substrate/reference/ai-tools",
"substrate/reference/flight-modes",
```

With:
```json
"substrate/reference/ai-tools",
"substrate/reference/sim-mcp-server",
"substrate/reference/flight-modes",
```

**Step 3: Verify**

Read `docs.json`. Confirm:
- `"substrate/reference/sim-mcp-server"` appears between `ai-tools` and `flight-modes`
- The JSON is valid (no trailing commas, matching brackets)

**Step 4: Commit**

```bash
git add docs.json
git commit -m "docs(nav): add sim-mcp-server to Reference group"
```

---

## Verification Checklist

After all tasks complete, do a final pass:

```bash
# Confirm no MuJoCo references remain in docs
grep -r "MuJoCo\|mujoco" substrate/ --include="*.mdx"
# Should return nothing

# Confirm sim-mcp-server page exists and is in nav
grep "sim-mcp-server" docs.json substrate/reference/sim-mcp-server.mdx

# Confirm tool count is 49 in ai-tools
grep "49" substrate/reference/ai-tools.mdx
```

All 7 commits should be in `git log`. The docs site (mintlify dev server) should
show the new sim-mcp-server page in the Reference sidebar.
