# Theia

**An AUTOSAR-Adaptive-Platform-style framework** — Functional Clusters running
as supervised processes on a custom C++ actor runtime, with the whole system
modeled in a small declarative DSL and built with Bazel.

You describe your system in `.art` — nodes (threads with typed ports),
compositions (processes), clusters (deploy units) — and the toolchain generates
the C++ scaffolds, wire types, deploy manifests, and supervision tree.

```art
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

| Repo | What |
| --- | --- |
| **[theia](https://github.com/perotheia/theia)** | The framework: the C++ actor runtime (`GenServer`/`GenStateM`/`GenRunnable`), the OTP-style supervisor, the ARA service Functional Clusters, and the Bazel build + `.deb` packaging. |
| **[artheia](https://github.com/perotheia/artheia)** | The `.art` system-description DSL + code generators (C++ scaffolds, `.proto`, manifests, netgraphs) and the LSP that powers editor support. |
| **[rf-theia](https://github.com/perotheia/rf-theia)** | The Robot Framework testing harness — drives the live supervisor, reads the trace feed, and asserts end-to-end signal flow across clusters. |

## Getting started

Install the framework, scaffold your own workspace, generate + build an app:

```sh
pip install artheia                      # the DSL + generators (artheia, artheia-lsp)
theia init                               # scaffold a consuming workspace
artheia gen-app --kind fc app.art --out apps --proto-out platform/proto
bazel build //apps/...                   # compiles against the Theia runtime
```

See each repo's README for details. Licensed under **Apache-2.0**.
