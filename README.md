# blit-workflows
Re-useable workflows for deploying parts of my infra.

## `macos-notarise`

Composite action that signs Mach-O binaries in place with a Developer ID and notarises them. Unsigned binaries downloaded through a browser are quarantined and Gatekeeper refuses to run them; after this they run without `xattr`. Run it between `cargo build` and packaging so the archive carries the signed bytes. A bare binary cannot hold a stapled ticket, so Gatekeeper checks the ticket online the first time it runs.

```yaml
- if: runner.os == 'macOS'
  uses: radiosilence/blit-workflows/macos-notarise@<sha>
  with:
    binaries: |
      target/${{ matrix.target }}/release/mybin
    certificate-p12: ${{ secrets.MACOS_CERTIFICATE_P12 }}
    certificate-password: ${{ secrets.MACOS_CERTIFICATE_PASSWORD }}
    api-key-p8: ${{ secrets.APPLE_API_KEY_P8 }}
    api-key-id: ${{ secrets.APPLE_API_KEY_ID }}
    api-issuer-id: ${{ secrets.APPLE_API_ISSUER_ID }}
```

It fails when a secret is empty rather than shipping an unsigned binary, so gate the step to events that have the secrets (pushes to the default branch, not pull requests from forks).
