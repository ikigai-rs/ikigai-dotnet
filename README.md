# ikigai-dotnet

The [ikigai](https://github.com/ikigai-rs) wire protocol for .NET: a client that calls a kernel and a servable peer
that a kernel mounts, alongside [ikigai-python](https://github.com/ikigai-rs/ikigai-python) and
[ikigai-deno](https://github.com/ikigai-rs/ikigai-deno).

**Status: phase 0.** The repository holds the shared vectors, copied verbatim from ikigai-core
(`tests/vectors/`, recorded in `SHA256SUMS`, from core at the commit in `FROM-CORE`), and CI checks them. There is no
code yet.

## The plan

- **Two targets in one package:**
  - `netstandard2.0` reaches .NET Framework 4.8 and every modern .NET. It carries the codec, wire, contracts,
    capabilities, IRIs and templates, plus an HTTP transport to a host's HTTP face.
  - The current LTS (`net10.0`) adds the Unix-socket client and peer, and QUIC (through MsQuic).
- **Framework 4.8 uses the HTTP face,** because it has neither Unix domain sockets nor QUIC. It can call and take part
  in layers, but it cannot be a mounted peer.
- **Held to the same shared vectors as the other faces:**
  - contract, capability, validation and failure-class vectors, byte for byte;
  - the wire fixtures and the typed-error display forms.
- **Contributions** come by fork and pull request.

Applications built on this face live in their own repositories and depend on its package; they are not part of this
one.

## Accessibility

[ACCESSIBILITY.md](ACCESSIBILITY.md) says what this face emits that people read (nothing yet, at phase 0), the rules
its output is planned to follow, what is tested, and how to report a barrier.

## License

MIT OR Apache-2.0, like the other faces.
