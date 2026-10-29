# torii's fork of instant-acme 0.9.0

This is the `torii` branch: instant-acme's `main` at 1cbc35c (0.9.0, unreleased;
https://github.com/djc/instant-acme) plus the changes below, for
[torii](https://github.com/Dirbaio/torii)'s ACME client. torii's own changes are marked
`torii fork` in the source. The goal: torii honours every Retry-After an ACME server
sends, through instant-acme's own API.

| Change | Where | Upstream behaviour |
| --- | --- | --- |
| A 429's Retry-After is kept, in `Error::RateLimited { problem, retry_after }` (djc/instant-acme#355, by helix-a, cherry-picked) | `src/types.rs`, `src/lib.rs` | `Error::Api(problem)`: the Retry-After was dropped |
| A Retry-After too far ahead to represent is no Retry-After (same PR) | `src/lib.rs` | `SystemTime` overflow panicked |
| `finalize_csr()` and `Account::order()` keep the response's Retry-After, and `poll_ready()` / `poll_certificate()` wait for it before their first poll | `src/order.rs`, `src/account.rs` | dropped, and cleared when polling began: the first poll came after the policy's initial delay, before the time the server asked for after finalization (RFC 8555 §7.4) |
