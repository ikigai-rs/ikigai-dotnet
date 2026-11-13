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
| Last reviewed | 2026-11-13 (UTC) |

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

Every named check has a result: Pass, Fail or Blocked, with evidence. Pass covers only the
stated check, not overall accessibility. Unrun checks remain Blocked, including checks of the
documentation as rendered on GitHub or in an editor. There is no application output yet.

The two vector checks below ran on unchanged `main` commit
[`8e9c1e4`](https://github.com/ikigai-rs/ikigai-dotnet/commit/8e9c1e4d49a37e5f5f378a9d21782260ae3e8831)
on 2026-11-13 (UTC). CI repeats them in `.github/workflows/ci.yml`; neither evaluates
accessibility. No historical accessibility run is claimed as a current Pass.

| Check | Result | Evidence |
|---|---|---|
| Vector digests | Pass | `cd tests/vectors && sha256sum -c SHA256SUMS`, sha256sum (Darwin) 1.0, 2026-11-13 (UTC): all six JSON files match. Checks fixture integrity only; does not test typed-error display or documentation accessibility. |
| Vector line endings | Pass | `if grep -l $'\r' tests/vectors/*.json; then echo "CRLF in a vector file"; exit 1; fi`, Bash 3.2.57 and BSD grep 2.6.0-FreeBSD, 2026-11-13 (UTC): exit 0, no matching files. Checks carriage returns only; does not evaluate readable output. |
| Typed-error display against shared vectors | Blocked | Phase 1 is not implemented. No .NET display implementation or test exists to run against `failure_class_vectors.json`; digest verification does not check display behavior. |
| Browser rendering, contrast, zoom and reflow | Blocked | No browser evaluation was run for this review. There are no application pages; rendered documentation has not been evaluated. |
| Manual keyboard | Blocked | No person performed a keyboard pass of rendered documentation; no interactive browser session was available for this review. |
| Automated accessibility checker | Blocked | No axe or other automated accessibility checker has been run on rendered documentation; no browser checker was configured for this review. |
| Screen reader | Blocked | No screen reader was used; no screen reader session was available for this review. |
| Screen magnifier | Blocked | No screen magnifier was used; no magnifier session was available for this review. |
| Speech input | Blocked | No speech-input software was used; no speech-input session was available for this review. |
| Switch access | Blocked | No switch-access software was used; no switch-access session was available for this review. |
| Braille display | Blocked | No braille display was used; no braille display was available for this review. |

## Contributing

Read this file before adding any output people read: an error message, console text, a log line
or an example's page. Keep meaning in words, keep the typed errors' display forms equal to the
shared vectors, and add the new output and its rules to this file in the same pull request. Say in
the pull request what was tested, with what, and what was not. Give each check a row in a
Check | Result | Evidence table with Pass, Fail or Blocked and the test or command, tool and
version, or why it could not run. Keep unrun checks as Blocked. A historical run that CI does
not repeat is Blocked with its date, rather than Pass.

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
| 2026-11-13 | Record explicit check results, current vector evidence and unrun accessibility checks. |
