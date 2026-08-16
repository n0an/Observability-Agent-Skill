<h1 align="center">Observability Agent Skill</h1>

<p align="center">
    <img src="https://img.shields.io/badge/iOS-14+-2980b9.svg" alt="iOS 14+" />
    <img src="https://img.shields.io/badge/swift-5.9+-F05138.svg" alt="Swift 5.9+" />
    <img src="https://img.shields.io/badge/WWDC-2016--2026-FF2D55.svg" alt="Covers WWDC 2016-2026" />
    <img src="https://img.shields.io/badge/license-MIT-lightgrey.svg" alt="MIT License" />
    <a href="https://agentskills.io/home">
        <img src="https://img.shields.io/badge/Agent%20Skills-Compatible-purple.svg" alt="Agent Skills Compatible" />
    </a>
</p>

An agent skill that helps AI coding agents like Claude Code, Codex, Cursor, and Gemini write correct Swift observability code on Apple platforms - unified logging that respects privacy and persistence, signpost instrumentation that shows up in Instruments, MetricKit metrics and diagnostics from production, crash reports you can actually symbolicate, and telemetry pipelines that do not lie to you.

It uses the [Agent Skills](https://agentskills.io/home) format, so it works smoothly with Claude Code, Codex, Gemini, Cursor, and more.


## What It Covers

- **Unified logging** - `Logger` (iOS 14+) and legacy `os_log`, subsystem/category design, the eight level methods and which levels persist to disk, privacy redaction (`.public`, `.private`, `.private(mask: .hash)`), formatting/alignment, lazy interpolation and why `String`-taking wrappers destroy it, multi-destination logging facades
- **Log retrieval** - Console.app filtering and saved searches, the `log` CLI (`stream`/`show`/`collect`/`config` with predicate recipes), logging configuration profiles, sysdiagnose capture, the Xcode 15+ structured console, and scripted three-tier capture for agent-assisted debugging (simulator stream-to-file, `devicectl` console launch with an env-gated print mirror, timestamped `sudo log collect` device archives that include app extensions)
- **OSLogStore** - reading your own logs at runtime, iOS scope limits, persistence and rotation caveats, building a user-initiated "Send Logs to Support" export that does not violate the privacy story
- **Signposts and tracing** - `OSSignposter` intervals/events/animation intervals, signpost IDs for concurrent operations, Points of Interest, legacy `os_signpost`, the Instruments os_signpost instrument, `xctrace` CLI capture/export, `XCTOSSignpostMetric` regression gates, `mxSignpost` field histograms and their overhead, and DTrace probes for tracing processes you cannot rebuild (macOS/simulator)
- **Activity tracing and correlation** - `os_activity` reality in Swift (C API, GCD-only propagation), `labelUserAction`, `@TaskLocal` correlation IDs that survive Swift Concurrency
- **MetricKit** - `MXMetricManager` subscription timing, payload cadence and delivery contract, the full metric payload map (launch histograms, hang time, exit reasons, scroll hitch ratio, CPU instructions, memory/disk/network), all five diagnostic types with unsymbolicated call stack trees, custom metrics, extended launch measurement, the iOS 27 Swift-native `MetricManager` (async report streams, `MetricResult`/`DiagnosticResult`, memory-exception diagnostics, StateReporting domains with the `@ReportableMetadata` macro, `trackLaunchTask`), simulate-payload testing
- **Crash, hang, and OOM observability** - what leaves a report and what does not, .ips anatomy, exception types and watchdog codes (`0x8badf00d`, `0xdead10cc`), memory-error stack signatures (malloc free-list poisoning, unrecognized selector after reuse), jetsam's missing crash reports and `MXAppExitMetric` exit reasons, the 50 MB background footprint target, memory-warning policy, the full hang tooling matrix (250 ms micro-hang threshold, Thread Performance Checker, on-device hang detection, Organizer hang reports), Organizer regressions with notifications, disk-write insights
- **Symbolication** - dSYM/UUID contract, `atos -i` recipes (inlined frames) and `--offset` for MetricKit frames, `CrashSymbolicator.py`, the ASLR slide and the three debug-info tiers (function starts, nlist, DWARF), synthetic `___lldb_unnamed_symbol` frames and why ObjC names survive stripping, Swift name demangling, the dyld shared cache and OS-frame symbol caches, MetricKit call-stack math, `mdfind` by UUID, LLDB crashlog import and `image lookup`/`image dump symtab` spot checks, CI dSYM upload for Sentry/Crashlytics/Datadog, App Store server-side symbolication
- **Production monitoring** - the App Store Connect Power and Performance API (perfPowerMetrics, smart insights, diagnostic signatures and logs), OpenTelemetry on iOS (opentelemetry-swift setup, OTLP exporters, gauge/counter/histogram semantics, TaskLocal span propagation via OpenTelemetryConcurrency, withSpan facade design, verbosity-level cost gating, MetricKit-to-OTel mapping rules), lab vs field tradeoffs, XCTest metrics with baselines, MetricKit-to-backend pipeline design, histogram bucket and unit discipline, launch-metric prewarming skew, hitch-rate and hang-rate reference bands, percentiles vs means vs 0-100 score indexes, slowdown experiments, version-comparison bias
- **Network telemetry** - request-level observability in production: why lab network conditions undersell the field (channel switching, cellular connection persistence, captive portals), the report/count/drop error taxonomy that keeps benign transport noise from burying contract breaks, non-fatal report design (readable custom errors as dashboard identity, `userInfo` context, embedded state timelines), `URLSessionTaskMetrics` phase anatomy and diagnostics (keep-alive reuse, server processing time, latency physics), aggregate monitoring via Firebase Performance or OTel histograms, targeted full-dump collection through feedback flows
- **Third-party SDKs** - what Sentry/Crashlytics/Datadog add over the native stack, the one-crash-handler rule, breadcrumb bridging with redaction, privacy manifests, sampling and cost, remote kill-switches for collection
- **Anti-patterns** - ~33 catches including print-as-logging, wrapper-forced interpolation, PII marked public, debug logs expected in production, signpost ID misuse, benchmarking with a log stream attached, simulator MetricKit testing, unit-conversion dashboard corruption, double crash reporters, watchdog testing under the debugger, OOM blindness, and transport-error report flooding


## Installing

You can install this skill into Claude Code, Codex, Gemini, Cursor, and more by using `npx`:

```bash
npx skills add https://github.com/n0an/Observability-Agent-Skill --skill observability
```

If you get the error `npx: command not found`, it means you don't currently have Node installed. You need to run this command to install Node through Homebrew:

```bash
brew install node
```

And if *that* fails it usually means you need to [install Homebrew](https://brew.sh) first.

When using `npx`, you can select exactly which agents you want to use during the installation. You can also select whether the skill should be installed just for one project, or whether it should be made available for all your projects.

### Alternative install methods

**Claude Code:**

```bash
/plugin install n0an/Observability-Agent-Skill
```

**Gemini:**

```bash
gemini extensions install https://github.com/n0an/Observability-Agent-Skill.git --consent
```

Alternatively, you can clone this whole repository and install it however you want.


## Using Observability

The skill is called Observability, and can be triggered in various ways. For example, in Claude Code you would use this:

> /observability

And in Codex you would use this:

> $observability

In both cases you can provide specific instructions if you want only a partial review. For example, `/observability Replace the print statements in SyncManager.swift with proper logging` on Claude, or `$observability Wire up MetricKit and export the launch histograms` in Codex.

You can also trigger the skill using natural language:

> Use the Observability skill to figure out why my MetricKit payloads never arrive.


## Why Use an Agent Skill for Observability?

Observability on Apple platforms is spread across half a dozen frameworks that changed substantially over the years - `Logger` replaced `os_log` in iOS 14, `OSSignposter` replaced `os_signpost` in iOS 15, `OSLogStore` opened the log store to apps, MetricKit grew immediate diagnostics in iOS 15 and was rewritten as a Swift-native async API in iOS 27, and prewarming quietly broke every hand-rolled launch measurement. Most LLM training data reflects the older shapes, so agents routinely generate code that:

- Ships `print` statements, or wraps `Logger` behind a `String`-taking function that forces interpolation on every call and strips privacy annotations
- Logs emails, tokens, and URLs as `.public` - or logs at `debug` level and expects the messages to be retrievable from a user's device later
- Measures durations with timestamped log lines instead of signposts, or reuses one signpost ID across concurrent operations so Instruments cross-matches the intervals
- Subscribes to MetricKit lazily (losing accumulated payloads), tests it in the simulator (where nothing is ever delivered), or exports histograms with a silent seconds-vs-milliseconds corruption
- Measures cold launch from process start without accounting for prewarming, producing minutes-long fake launches
- Installs a second crash reporter next to an existing one, corrupting both
- Waits for OOM crash reports that structurally cannot exist

This skill:

- **Catches anti-patterns** LLMs default to, with before/after fixes
- **Routes work to the right signal** with an explicit decision tree (log vs signpost vs field histogram vs diagnostic vs SDK)
- **Covers newer APIs** like `OSSignposter`, `OSLogStore`, mxSignpost histograms, the App Store Connect Power and Performance API, and the iOS 27 `MetricManager` with memory-exception diagnostics and state-contextualized metrics
- **Enforces the system contract** - level persistence rules, privacy redaction boundaries, MetricKit delivery cadence, dSYM/UUID matching, and the aggregation discipline that keeps production dashboards honest

The guidance is distilled from Apple documentation, WWDC session material, and established community practice.


## Contributing

Contributions are welcome - whether adding new checks, improving existing examples, or fixing typos.

- Keep Markdown concise. There is a token cost to using skills, so respect the token budgets of users.
- Do not repeat things LLMs already know. Focus on edge cases, surprises, and common mistakes.
- All work must be licensed under the MIT license.


## License

Available under the [MIT License](LICENSE), which permits commercial use, modification, distribution, and private use.
