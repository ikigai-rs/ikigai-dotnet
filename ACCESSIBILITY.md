# Accessibility

What `ikigai-dotnet` emits that people read (today, nothing: the repository holds the plan and the
shared vectors, and no code), the rules its output is planned to follow, how it is tested, and how
to report a barrier. It is not a conformance claim. Adapted from the
[ACCESSIBILITY.md template](https://github.com/mgifford/ACCESSIBILITY.md) (MIT).

| Field | Value |
|---|---|
| Target | WCAG 2.2 Level AA, for any page that presents this output; this repository writes no HTML or SVG, and today no output at all |
| Status | Evaluation in progress. No conformance claim. |
| Owner | the maintainers of this repository |
| Report a barrier | [a GitHub issue](https://github.com/ikigai-rs/ikigai-dotnet/issues/new?labels=accessibility) with the `accessibility` label; anything security-sensitive through [private vulnerability reporting](https://github.com/ikigai-rs/ikigai-dotnet/security/advisories/new) |
| Last reviewed | 2026-10-31 |

## What it emits that people read

Nothing yet. The repository is at phase 0 of `PLAN.md`: the shared vectors under `tests/vectors/`
and a CI workflow that checks their digests and line endings. The people who read anything here
read the documentation (`README.md`, `PLAN.md`, this file).

What `PLAN.md` says the face will emit, once it is built:

- **Typed errors** whose display form is the Rust kernel's, held to `failure_class_vectors.json`
  and the display forms the other faces pin.
- **Examples**: a PowerShell example of the HTTP transport (phase 6) and an ASP.NET Core
  minimal-API app (phase 7). If an example writes HTML, this file gains that page and its rules
  in the same pull request.

## The rules that output follows

None is in force yet, because there is no output. The plan's one rule that people read is that a
typed error reads as the Rust kernel prints it, the same words in every face.

## How it is tested

- CI (`.github/workflows/ci.yml`) checks that the vector files match their recorded digests and
  hold no carriage returns. No test reads any output people read, because there is none.
- **No automated accessibility checker, browser or assistive technology has been used on this
  output.**

## Contributing

Read this file before adding any output people read: an error message, console text, a log line
or an example's page. Keep meaning in words, keep the typed errors' display forms equal to the
shared vectors, and add the new output and its rules to this file in the same pull request. Say in
the pull request what was tested, with what, and what was not.

## Reporting a barrier

[Open an issue](https://github.com/ikigai-rs/ikigai-dotnet/issues/new?labels=accessibility) with
the `accessibility` label (the link sets it when GitHub lets you add labels; otherwise a maintainer
adds it): what you were doing, what happened, and your tools and any assistive technology, if you
are comfortable sharing them. Anything security-sensitive goes through
[private vulnerability reporting](https://github.com/ikigai-rs/ikigai-dotnet/security/advisories/new).
**We acknowledge a report within 7 days**, and promise nothing further.

## Change history

| Date | Change |
|---|---|
| 2026-10-31 | First version. |
