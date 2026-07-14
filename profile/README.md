# Theia

Reverend Bayes is taking his semi autonomous vehicle (a horse and carriage) to church.
 - semi-autonomous-vehicle concept


**An AUTOSAR-Adaptive-Platform-style framework** — Functional Clusters running
as supervised processes on a custom C++ actor runtime, with the whole system
modeled in a small declarative DSL and built with Bazel.

You describe your system in `.art` — nodes (threads with typed ports),
compositions (processes), clusters (deploy units) — and the toolchain generates
the C++ scaffolds, wire types, deploy manifests, and supervision tree.

```scala
node atomic Counter {
    tipc type=0x80020001 instance=0
    ports {
        receiver ticks_in requires TickStream     // inbound stream
        server   ctl      provides ModeCtl        // request/reply surface
    }
}

composition DemoProc {            // a process
    prototype Counter counter
    prototype Driver  driver
    connect driver.ticks_out to counter.ticks_in
}
```

## Repositories

| Repo | What | Docs |
| --- | --- | --- |
| **[theia](https://github.com/perotheia/theia)** | The framework: the C++ actor runtime (`GenServer`/`GenStateM`/`GenRunnable`), the OTP-style supervisor, the ARA service Functional Clusters, and the Bazel build + `.deb` packaging. | [Wiki](https://github.com/perotheia/theia/wiki) |
| **[artheia](https://github.com/perotheia/artheia)** | The `.art` system-description DSL + code generators (C++ scaffolds, `.proto`, manifests, netgraphs) and the LSP that powers editor support. | [Wiki](https://github.com/perotheia/artheia/wiki) |
| **[rf-theia](https://github.com/perotheia/rf-theia)** | The Robot Framework testing harness — drives the live supervisor, reads the trace feed, and asserts end-to-end signal flow across clusters. | [Wiki](https://github.com/perotheia/rf-theia/wiki) |
| **[colony](https://github.com/perotheia/colony)** | The deployment adapter — agentless (Ansible) provisioning + orchestration of rigs from the per-rig bundle `theia` emits, plus the nightly OTA end-to-end integration gate (build → S3 → provision → Mender OTA, in containers). | [Wiki](https://github.com/perotheia/colony/wiki) |
| **[ground-station](https://github.com/perotheia/ground-station)** | The cloud Ground Station — the operator surface for day-2 fleet operations: enroll devices, plan + push software campaigns (base → colony, app → Mender), watch rollouts, and aggregate device health. A web UI + API over a Mender OTA backend. | [Wiki](https://github.com/perotheia/ground-station/wiki) |

📖 Start with the **[Theia wiki](https://github.com/perotheia/theia/wiki)** — architecture,
the deployment/manifest pipeline, a step-by-step tutorial, and the MCP/skills integration.

## Packages

Reusable **packages** — a node + protocol + impl, built once as a linkable lib and
`import`ed into many workspaces (the ROS `runtime / libraries / workspace` split) —
live in the separate **[perotheia-packages](https://github.com/perotheia-packages)**
org. Scaffold your own with `theia init --kind package`; consume one as an
`@<name>//` bazel-module git submodule under `packages/`. The worked examples:
[`v2v`](https://github.com/perotheia-packages/v2v) (relative-topology SLAM +
cooperative-alert consensus), [`meshtastic`](https://github.com/perotheia-packages/meshtastic)
(a swappable mesh transport that feeds v2v), and
[`connectivity-ws`](https://github.com/perotheia-packages/connectivity-ws) — an
**integration workspace** that submodules related connectivity packages and composes
them into deployable apps with port-connected pipelines (v2v + meshtastic today). See
the tutorial's [Advanced deployment › Packages](https://github.com/perotheia/theia/wiki)
chapter.

## Getting started

Install the framework, scaffold your own workspace, generate + build an app:

```sh
pip install artheia                      # the DSL + generators (artheia, artheia-lsp)
theia init                               # scaffold a consuming workspace
artheia gen-app --kind fc app.art --out apps --proto-out platform/proto
bazel build //apps/...                   # compiles against the Theia runtime
```

See each repo's README for details. Licensed under **Apache-2.0**.
