# The plan for ikigai-dotnet

A third polyglot face for [ikigai](https://github.com/ikigai-rs), beside
[ikigai-python](https://github.com/ikigai-rs/ikigai-python) and [ikigai-deno](https://github.com/ikigai-rs/ikigai-deno):
a client that calls a kernel, and a servable peer that a kernel mounts. This file is the plan; the README says what is
built so far.

## What a face is

Each face has the same parts, and the three are held together by shared vectors that a daily drift scan compares byte
for byte across the repositories:

| part | python | deno | .NET |
| --- | --- | --- | --- |
| postcard codec (the wire's byte format) | `postcard.py` | `postcard.ts` | `Postcard` |
| wire frames and typed errors (v9: `IssueWith`, error tags 0–13) | `wire.py` | `wire.ts` | `Wire` |
| contract digests (v1 and v2), the `ik:` Turtle mirror | `contract.py` | `contract.ts` | `Contract` |
| capability coverage, floor and denial rules | `capability.py` | in `contract.ts` | `Capability` |
| IRIs and templates | `template.py` | `iri.ts`, `template.ts` | `Iri`, `Template` |
| client over a Unix socket | `client.py`, `aio.py` | `client.ts` | `UdsClient` |
| servable peer | `serve.py` | `serve.ts` | `Peer` |
| QUIC client | `quic.py` (extra) | none | `QuicClient` (modern .NET only) |

The shared vectors live in `tests/vectors/`, copied verbatim from ikigai-core:
- `contract_vectors.json`, `capability_vectors.json`, `validation_vectors.json`, `failure_class_vectors.json`;
- pinned values beyond the files: the wire fixture's IRI and endpoint id, the v8 `Conflict` byte vector `05 08 01 78`,
  and the typed-error display form of every wire variant;
- wire v9's `IssueWith` and the new error tags, as byte vectors (below).

`.gitattributes` keeps `eol=lf` on every vector path, so a Windows checkout cannot drift. That matters more here than
in either other face.

## Two targets, one package

| target | reaches | carries |
| --- | --- | --- |
| `netstandard2.0` | .NET Framework 4.8 (and 4.6.1+), Mono, every modern .NET | codec, wire, contract, capability, IRI, template, the vectors, and the HTTP transport |
| the current LTS (`net10.0`) | modern .NET on Windows, Linux and macOS | all of the above, plus the Unix-socket client and peer, and QUIC |

Why two: .NET Framework 4.8 has neither transport a kernel's doors speak. `UnixDomainSocketEndPoint` arrived in .NET
Core 2.1, and `System.Net.Quic` is .NET 7+ and needs MsQuic (in-box on Windows 11 and Server 2022, `libmsquic` on
Linux). So the netstandard2.0 core carries everything that is pure computation, and the modern target adds the
transports.

**Framework 4.8's transport is the HTTP face.** A host's `serve --http` door and the layers' HTTPS front already exist;
on Framework 4.8 the face calls them with `HttpClient`, and HTTP statuses map back to typed errors through
`failure_class_vectors.json`'s status table read in reverse. A Framework program can read and write resources and take
part in layers. It cannot be a mounted peer, which needs a socket or QUIC door; the README says so.

## Dependencies

The ecosystem rule: do not reimplement common behavior, least of all behavior a standard defines.
- **BLAKE3** for contract digests (not in the BCL): a vetted NuGet package that targets netstandard2.0 first; else a
  managed port pinned by the official BLAKE3 test vectors and by our contract vectors. Decided at phase 2, with the
  evidence.
- **JSON**: `System.Text.Json`, which supports netstandard2.0 as a package, so one code path.
- Nothing else at runtime. Tests use xUnit.

## Layout

```
src/Ikigai/                  the library (netstandard2.0; net10.0)
tests/Ikigai.Tests/          xUnit; runs on net10.0 and, on Windows CI, net48
tests/vectors/               verbatim copies of core's vector files (eol=lf)
examples/AspNetCore/         a minimal-API app serving a resource through the face (phase 7)
.github/workflows/ci.yml     build and test (Windows, Linux, macOS), net48 on Windows, the Rust interop job
```

CI is this repository's own workflow (the repository is not Rust), under the same rules as every ikigai repo: green
before the first push, no `on.schedule` (the ecosystem's clock lives elsewhere), and an **interop job** that builds
`ikigai-cli` at a pinned version, cached by version, and runs the face against a real Rust door. A missing binary is a
FAILURE, not a skip.

## Phases, each one pull request, each green

0. **Skeleton** — done: the repository, CI, licenses, `.gitattributes`, the vector files and their digests.
1. **Codec and wire**: postcard, wire frames, the typed errors (v8's and the 0.1.108 variants with their classes), and
   wire v9's `IssueWith`. Passes the IRI and endpoint-id fixtures, the `Conflict` vector, the display forms and the
   byte vectors below.
2. **Contracts and capabilities**: v1 and v2 digests, the `ik:` Turtle mirror (`ik:mayFail` included), the capability
   coverage, floor and denial rules, and the validation checker. Passes the four vector files.
   The v2 hash includes the existence-sensitivity tail: the byte `2`, fed after the failure classes, only when an
   action's `existence_sensitive` is true (absent and `false` feed nothing); see "Existence sensitivity" in core's
   `crates/ikigai-vocab/tests/vectors/README.md` and the `sensitive/*` contracts.
3. **Unix-socket client** (modern target), with interop against the Rust door in CI.
4. **Servable peer** (modern target): a Rust kernel mounts a .NET door and calls it.
5. **QUIC client** (modern target, where MsQuic exists); it pins the server certificate, as the other faces do.
6. **HTTP transport** (both targets): the Framework 4.8 path, with typed errors from status codes, and a PowerShell
   example.
7. **Examples**: an ASP.NET Core minimal-API app; optionally the tic-tac-toe app and the template cases.
8. **NuGet readiness**: a package-content test, Source Link, deterministic builds. Publishing is the maintainer's.

Phases 1–2 are pure computation and run anywhere. Phase 6 is what a Framework 4.8 program needs first, so it may move
up to follow phase 2.

## Wire v9 byte vectors (from ikigai-cli 0.1.66)

`Call::IssueWith` is discriminant `05`: the `Request`, the `Capability`, then `IssueWith`, exactly where `IssueTraced`'s
`TraceContext` sits. `IssueWith` is three optional fields in order: `trace: Option<TraceContext>` (`00`, or `01` +
trace_id varint + `Option<u64>` parent_span), `retries: Option<u32>` (`00`, or `01` + varint), `time_left_ms:
Option<u64>` (`00`, or `01` + varint). **The deadline is time left in whole milliseconds, never an absolute instant**: a
server adds it to its own now.

- `{None, None, None}` → `00 00 00`
- `{None, Some(2), Some(1500)}` → `00 01 02 01 dc 0b`
- `{Some({7, None}), Some(2), Some(1500)}` → `01 07 00 01 02 01 dc 0b`

Typed errors (`Reply::ErrorTyped` = `05`), the five new tags:
- `DepthExceeded{65, "urn:x"}` → `05 09 41 05 75 72 6e 3a 78`
- `RetryAfter{Some(30000), "x"}` → `05 0a 01 b0 ea 01 01 78`; `RetryAfter{None, "x"}` → `05 0a 00 01 78`
- `OverBudget("x")` → `05 0b 01 78`
- `Refused("x")` → `05 0c 01 78`
- `Unsupported{Some(Sink), "x"}` → `05 0d 01 01 01 78`; `Unsupported{None, "x"}` → `05 0d 00 01 78` (the verb is the
  `Request`'s verb index: Source 0 … Meta 4)

To a peer that negotiated v7 or v8, each new tag crosses as `Endpoint(display)`, byte-identical to what a v8 server
sends today.

## How contributions work

- The face lives here. Applications built on it, such as a harness or an app, live in their own repositories and
  depend on this package.
- Fork and pull request; CI must be green; a maintainer reviews and merges. `main` is protected.
- Every behavior that leaves the process is pinned by a vector or a doctest, and "done" for a phase means the vectors
  it names pass byte for byte.
