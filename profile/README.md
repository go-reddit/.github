<p align="center"><img src="https://raw.githubusercontent.com/go-reddit/brand/main/social/go-reddit.png" alt="go-reddit" width="640"></p>

<h1 align="center">go-reddit</h1>
<p align="center"><strong>A pure-Go (CGO=0) Reddit client and a native macOS reader built on it — stdlib only, static binaries, no cgo.</strong></p>

<p align="center">
  🌐 <a href="https://go-reddit.github.io">Website</a> ·
  📚 <a href="https://go-reddit.github.io/docs/">Documentation</a>
</p>

<p align="center">
  <a href="https://go-reddit.github.io/docs/"><img alt="Docs" src="https://img.shields.io/badge/docs-mkdocs--material-FF4500?style=flat-square"></a>
  <a href="https://github.com/go-reddit/reddit/blob/main/LICENSE"><img alt="License: BSD-3-Clause" src="https://img.shields.io/badge/license-BSD--3--Clause-blue?style=flat-square"></a>
  <img alt="Go 1.26.4+" src="https://img.shields.io/badge/go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white">
  <img alt="Coverage 100%" src="https://img.shields.io/badge/coverage-100%25-1a7f37?style=flat-square">
</p>

---

**go-reddit** builds a small, dependency-free stack for reading Reddit from Go.
At the bottom is [`reddit`](https://github.com/go-reddit/reddit) — a
**stdlib-only, `CGO_ENABLED=0`** client for Reddit's JSON API (anonymous or
OAuth). On top of it sits [`reader`](https://github.com/go-reddit/reader) — a
**native macOS app** whose entire UI is drawn by
[go-widgets](https://github.com/go-widgets/toolkit), compiled to WebAssembly,
blitted into a `<canvas>`, and hosted in a **WKWebView opened from Go with zero
cgo** (via [purego](https://github.com/ebitengine/purego)).

Every module is pure Go, cross-compiles to all six 64-bit Go targets, and ships
as a single static binary.

## Repositories

| Repo | What it is |
|------|------------|
| [**reddit**](https://github.com/go-reddit/reddit) | the API client — anonymous or OAuth (`client_credentials` / `password`), typed listings and comments, `*APIError` status codes; stdlib-only, `CGO_ENABLED=0` |
| [**reader**](https://github.com/go-reddit/reader) | the native macOS reader app — go-widgets UI in wasm, hosted in a purego WKWebView with a private `reader://` URL-scheme transport (no socket, no port) |
| [**docs**](https://github.com/go-reddit/docs) | MkDocs Material documentation, versioned with [mike], served at [/docs/](https://go-reddit.github.io/docs/) |
| [**go-reddit.github.io**](https://github.com/go-reddit/go-reddit.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-reddit/brand) | logos and brand assets |

## Principles

- **Pure Go, zero cgo.** The client is stdlib-only; the reader drives AppKit /
  WebKit through the Objective-C runtime via purego. Both cross-compile and ship
  as a single static binary.
- **Anonymous or OAuth.** Read public listings anonymously, or authenticate with
  the app-only (`client_credentials`) or script (`password`) grant — the
  reliable path now that Reddit blocks anonymous `.json` from datacenter IPs with
  a 403.
- **Typed, honest errors.** Non-2xx responses surface as a typed `*APIError`
  carrying the status code, so callers can tell a 429 from a 403 from a 404.
- **The UI is painted, not marked up.** In the reader, cards, score badges,
  sidebar and topbar are rendered by the go-widgets painter into an RGBA buffer
  at device resolution — the WebView is just a surface.
- **100% test coverage** is the target, enforced as a CI gate.

## Status

**Client complete; reader shipping on macOS.** `reddit` covers subreddit,
front-page and comment listings across every sort and time window, with
automatic bearer-token fetch/refresh over `oauth.reddit.com`, at 100% coverage
including every error branch. `reader` renders the full feed UI (topbar sort
tabs, bookmarked-subreddit sidebar, scrollable post cards) with go-widgets,
serves the wasm and proxies `/api` in-process through a `WKURLSchemeHandler`,
and builds a double-clickable `.app` bundle — all `CGO_ENABLED=0`. Both are
`gofmt` + `go vet` clean and CI-green across the six 64-bit Go targets (amd64,
arm64, riscv64, loong64, ppc64le, s390x).

BSD-3-Clause.

[mike]: https://github.com/jimporter/mike
