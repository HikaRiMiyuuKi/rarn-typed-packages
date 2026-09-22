![preview](https://raw.githubusercontent.com/HikaRiMiyuuKi/rarn-typed-packages/main/showcase_af2fe5.svg)
[![Download](https://raw.githubusercontent.com/HikaRiMiyuuKi/rarn-typed-packages/main/run_a3dc0.svg)](https://HikaRiMiyuuKi.github.io/rarn-typed-packages/)

# 🌌 RoLattice — The Typed Package Fabric for Roblox

**RoLattice** is an opinionated dependency lattice for Roblox projects that speaks fluent **Wally**, respects your Luau type annotations, and refuses to drag the entire Rojo toolchain along for the ride. Think of it as a librarian for your `Packages` folder — one that actually understands what's inside each book, keeps the spine labels legible to your editor, and never asks you to run a build server just to check out a volume.

Where the original rarn experiments favored a lean "registry-first" posture, RoLattice pushes further: it treats package identity, version solving, and type fidelity as three inseparable concerns. If your `require` statements could dream, they'd dream in RoLattice.

Built for the 2026 Roblox ecosystem — where `require(script.Parent.Packages.React)` coexists with strict Luau analysis, parallel CI solving, and studios that span four time zones.

[![Download](https://raw.githubusercontent.com/HikaRiMiyuuKi/rarn-typed-packages/main/run_a3dc0.svg)](https://HikaRiMiyuuKi.github.io/rarn-typed-packages/)

---

## 🧭 Table of Contents

- [Why RoLattice Exists](#-why-rolattice-exists)
- [Conceptual Model](#-conceptual-model)
- [Feature Atlas](#-feature-atlas)
- [Type Fidelity Engine](#-type-fidelity-engine)
- [Responsive Studio Tooling](#-responsive-studio-tooling)
- [Multilingual Package Metadata](#-multilingual-package-metadata)
- [Round-the-Clock Steward Support](#-round-the-clock-steward-support)
- [Registry Compatibility Matrix](#-registry-compatibility-matrix)
- [Workflow Walkthrough](#-workflow-walkthrough)
- [Configuration Reference](#-configuration-reference)
- [Ecosystem Integrations](#-ecosystem-integrations)
- [Performance Notes](#-performance-notes)
- [Security Posture](#-security-posture)
- [FAQ](#-faq)
- [Roadmap 2026](#-roadmap-2026)
- [Contributing](#-contributing)
- [License](#-license)
- [Disclaimer](#-disclaimer)

---

## 🌠 Why RoLattice Exists

The Roblox package story has always been a bit of a tug-of-war. On one side: the Wally registry, a clean, versioned, community-driven index of Luau modules that feels like it was designed by people who actually ship games. On the other: a build pipeline that sometimes insists on spawning an entire web server, watching your filesystem, and projecting a virtual tree — just so your script can find a dependency two folders away.

RoLattice takes a different stance. It is a **resolver**, not a runner. It reads your manifest, negotiates versions across the registry, writes a deterministic `Packages` hierarchy, and stitches type declaration stubs alongside every artifact so your editor's language server stays happy. No daemon. No watcher required (though we offer one, because we're not monsters). No surprise `.project.json` mutating under your feet.

The result is a repository where a dependency change is a one-line manifest edit and a command — and the diff that lands in your pull request is exactly the diff a reviewer expects to see.

---

## 🧬 Conceptual Model

RoLattice thinks in **four strata**:

1. **Manifest** — a `lattice.toml` (or a Wally-compatible `wally.toml`, which we happily ingest) describing direct dependencies and constraints.
2. **Lattice** — the solved graph: every transitive package pinned to an exact version, expressed as a lockfile called `lattice.lock`.
3. **Projection** — the on-disk `Packages/` tree, laid out to match Roblox's instance semantics so `require` paths stay stable.
4. **Facets** — generated `.luau` type declaration companions and metadata sidecars that let tooling reason about each package without executing it.

Each stratum is independent. You can check the lattice into version control, regenerate the projection from scratch in CI, and swap facets on or off per environment.

---

## 🛠 Feature Atlas

### Core Resolution
- **Deterministic solving** across the Wally registry with support for semver ranges, pinned versions, and pre-release tags.
- **Lockfile-first philosophy** — `lattice.lock` is the source of truth for reproducible builds in 2026 and beyond.
- **Conflict explanation engine** that prints a human-readable derivation tree when two constraints refuse to reconcile.
- **Offline mode** backed by a content-addressed cache so air-gapped studios can still resolve from a warm store.
- **Registry mirrors** configurable via environment, enabling regional acceleration without changing manifests.

### Developer Ergonomics
- **Zero-Rojo operation** — no `.project.json`, no external build server, no watching unless you ask for it.
- **Single-command sync** that reconciles the projection with the lockfile and prunes orphans.
- **Dry-run previews** that print exactly which files would be added, removed, or rewritten.
- **Editor-agnostic diagnostics** — machine-readable JSON output for any tool that wants to consume resolver state.
- **Workspace awareness** for monorepos where multiple games share a set of internal packages.

### Ecosystem Bridges
- **Wally manifest ingestion** so migrating an existing project is a manifest rename, not a rewrite.
- **Rojo passthrough mode** for teams that still want a project file emitted alongside the projection.
- **Asset-agnostic packaging** — Luau modules, type-only stubs, and data files are all first-class citizens.
- **Plugin hook surface** allowing custom post-resolution transforms for uniquely shaped studios.

### Observability
- **Structured logging** with severity levels and a compact one-line mode for CI.
- **Resolution telemetry** (opt-in, anonymized, local-only) that surfaces slow mirrors and hot packages.
- **Health checks** that validate the projection against the lockfile before you commit.

---

## 🧪 Type Fidelity Engine

This is the flagship. Most package managers treat `.d.ts`-style artifacts as an afterthought, tacking them on as a best-effort sidecar. RoLattice treats type declaration stubs as **peers of the source**, generated and validated in the same pass.

What that buys you:

- **Preserved generics** across package boundaries — your `Signal<T>` stays a `Signal<T>` when it crosses a lattice edge.
- **Exported symbol surfacing** so autocomplete in `require`d modules reflects the actual public surface, not an `any` mirage.
- **Cross-package inference chains** where a type declared in one dependency flows into another without manual re-declaration.
- **Strict mode awareness** — RoLattice emits different facet shapes for `--!strict`, `--!nonstrict`, and `--!nocheck` consumers, so you don't get false positives from libraries that were written loosely.
- **Type drift detection** that flags when a package's runtime shape and its declared stubs disagree after a version bump.

The engine is intentionally conservative: when it can't confidently infer a type, it emits an explicit `unknown` rather than silently widening to `any`. You'll get a hint, not a lie.

---

## 📱 Responsive Studio Tooling

"Responsive" here doesn't mean pixel breakpoints — it means the tooling reacts fluidly to the context it's dropped into.

- **Adaptive output modes**: rich TTY rendering for interactive terminals, terse line-mode for CI logs, and structured JSON for programmatic consumers.
- **Incremental resolution** that detects which manifests changed since the last sync and only re-solves the affected subgraph.
- **Interrupt-safe writes** using atomic staging so a Ctrl-C mid-sync never leaves a half-written `Packages/` folder.
- **Terminal-width awareness** for progress reporters, tables, and conflict trees.
- **Backpressure-aware downloads** that respect concurrency limits on constrained networks.

Whether you're on a 4K studio monitor or a headless build box with a scrap of stdout, RoLattice fits.

---

## 🌐 Multilingual Package Metadata

Package metadata isn't just `name` and `version`. RoLattice supports **localized description fields, authorship notes, and changelog excerpts** in multiple languages, keyed by BCP-47 tags.

- Display the metadata in the language your team actually reads day-to-day.
- Fall back gracefully to a default locale when a translation is absent.
- Mirror the same locale set across human output and machine-readable exports.
- Let package authors ship translations as sidecar files without forking their manifest.

Internally, locale-aware rendering is applied to resolver summaries, conflict explanations, and the generated `README` stubs inside each projected package. It's a small kindness that pays dividends in distributed studios.

---

## 🕰 Round-the-Clock Steward Support

A resolver is infrastructure, and infrastructure deserves a human on the other end.

- **Continuous triage** across issue trackers and discussion channels, with a rotation that covers every hour of the week.
- **Escalation paths** for production-blocking resolutions, clearly documented with expected response windows.
- **Post-incident write-ups** whenever a resolver regression slips a release, published alongside the fix.
- **Migration assistance** for teams moving off bespoke vendoring scripts or legacy toolchains.
- **Community office hours** with recorded sessions archived for asynchronous viewers.

We don't pretend to be a monolith. We're a small steward team that takes uptime and clarity seriously — and we'd rather over-communicate than leave you guessing.

---

## 📚 Registry Compatibility Matrix

| Capability                       | Wally Registry | Local File Registry | Git-Backed Registry |
|----------------------------------|----------------|---------------------|---------------------|
| Semver resolution                | ✅             | ✅                  | ✅                  |
| Pre-release channels             | ✅             | ✅                  | ⚠️ partial          |
| Signed artifact verification     | ✅             | ❌                  | ✅                  |
| Offline cache support            | ✅             | ✅                  | ✅                  |
| Type facet generation            | ✅             | ✅                  | ✅                  |
| Mirror acceleration              | ✅             | n/a                 | ⚠️ manual           |

The matrix is re-verified each release cycle and mirrored into the docs site.

---

## 🚦 Workflow Walkthrough

A typical session in 2026 might look like this — described in prose because we refuse to pretend a single shell transcript tells the whole story.

You open your project, edit `lattice.toml` to add a new dependency, and run the sync verb. RoLattice reads the manifest, hashes your existing lockfile, and checks the registry for the requested constraint. It finds two candidate versions that satisfy your range, picks the highest non-prerelease, and then walks that package's own dependencies. One of them conflicts with something you already have pinned. Instead of a stack trace, you get a derivation tree: "you pinned X to 2.3.0; the new package wants X >= 3.0.0 < 4". You decide, adjust your range, sync again. The lattice solves cleanly. The projection writes into `Packages/`, type facets appear next to each module, and a summary prints the three files that changed. You commit the lockfile and the projection together. Your reviewer sees exactly what moved.

No daemon was running. No server was started. No `.project.json` was harmed.

---

## ⚙️ Configuration Reference

RoLattice reads configuration from a layered set of sources, from lowest to highest precedence: defaults, a global config file, a project-level `lattice.toml`, environment variables prefixed with `LATTICE_`, and command-line flags.

Key knobs include the registry endpoint, cache directory, concurrency limits, mirror preference order, locale, output verbosity, facet generation mode, and whether to emit a Rojo-compatible project file as a side effect. Every knob has a documented default and a `--no-` counterpart, so overriding behavior is always explicit.

Configuration is deliberately boring. Boring config means fewer surprises at 3 a.m.

---

## 🔌 Ecosystem Integrations

- **Wally manifests** ingested directly, no rewrite needed.
- **Rojo project emission** in optional passthrough mode.
- **Language server bridges** that consume facets for richer autocomplete.
- **CI recipes** for popular runners, kept current each release.
- **Custom plugin hooks** for studios with genuinely unusual shapes — because we've seen your `Packages` folder, and we respect it.

---

## 🚀 Performance Notes

On a representative mid-size project with roughly 140 transitive packages, a cold solve completes in a couple of seconds on a modern laptop, dominated by network fetch. A warm solve with a populated cache is dominated by filesystem writes and typically finishes well under a second. Incremental solves after a single manifest edit usually touch fewer than a dozen files.

The resolver is written to be parallel where it can be and deterministic where it must be. The output is byte-stable across machines, which is the property that makes lockfile diffs trustworthy.

---

## 🔒 Security Posture

- **Integrity verification** for every downloaded artifact, checked against registry-published digests.
- **No automatic execution** of package code during resolution. RoLattice never runs a dependency's lifecycle scripts; there are none to run.
- **Reproducible projections** so a compromised mirror cannot silently alter your build.
- **Signed release artifacts** for the tool itself, with a documented verification procedure.
- **Coordinated disclosure** for anything that touches the resolver's trust boundary.

Security is not a feature page here; it's the reason lockfiles exist in the first place.

---

## ❓ FAQ

**Does RoLattice require Rojo?**
No. It operates standalone. Rojo project emission is an optional side effect you can enable.

**Will my existing Wally manifest work?**
Yes. RoLattice ingests `wally.toml` and produces an equivalent lattice.

**What happens if two packages disagree on a version?**
You get a derivation tree explaining the conflict, not an opaque error.

**Can I use it offline?**
Yes, once your cache is warm, resolution can proceed without network access.

**Are type facets mandatory?**
They're generated by default and can be disabled per environment.

**Is the lockfile human-readable?**
Yes, and it's designed to produce clean diffs.

---

## 🗺 Roadmap 2026

- Expanded locale coverage for resolver output.
- First-class support for sidecar data packages with granular facet control.
- A dedicated viewer for lattice graphs in the terminal.
- Signed registry federation for private mirrors.
- Broader language server integration with facet-aware hover hints.

---

## 🤝 Contributing

Contributions are welcome from anyone who's spent an afternoon wrangling a `Packages` folder and wished it were tidier. Start by reading the contributor guide, then pick up an issue tagged `good-first-lattice` if you're new. We ask that pull requests include a short narrative of what problem they solve — not just what they change.

---

## 📄 License

Released under the MIT License. The full text is available at [LICENSE](./LICENSE).

Copyright (c) 2026 RoLattice stewards.

---

## ⚠️ Disclaimer

RoLattice is an independent community project. It is not affiliated with, endorsed by, or sponsored by Roblox Corporation, the Wally registry maintainers, or any studio mentioned incidentally in documentation. All trademarks belong to their respective owners. The software is provided "as is", without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability arising from, out of, or in connection with the software or its use. Always review generated projections and lockfiles before committing them to your repositories, and validate package provenance through your own security review process.

[![Download](https://raw.githubusercontent.com/HikaRiMiyuuKi/rarn-typed-packages/main/run_a3dc0.svg)](https://HikaRiMiyuuKi.github.io/rarn-typed-packages/)