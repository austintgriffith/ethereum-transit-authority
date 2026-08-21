# Ethereum Transit Authority

**Live map: https://austintgriffith.github.io/ethereum-transit-authority/**

A subway map of every company your data passes through when you transact on Ethereum.

## How to read it

- **Each colored line is a piece of your data** — your IP, your address, the recipient, the tx intent, the signed tx, your account list, the tx hash. One line per data item (Vignelli's rule), even when several ride the same request.
- **Each station is a company that receives it.** A ring = that station receives that line. Chained rings = one company receives several lines at once — it can join them.
- **The river is the signing.** Everything west of it left your machine before you agreed to anything.
- **Hatched collars** = stations inside the Cloudflare tunnel (TLS terminates at Cloudflare; it sees the plaintext).
- **Green house regions** = the user's own machine. Lines that end inside the wall never crossed it.
- **🌾 The CROPS line** — a green throughline under each map. Every ✕ (can block) or ≈ (can lie) chip sitting on it is a break: a place the flow is not Censorship-resistant, Open, Private, or Secure. The goal of the whole project is to find and fix those breaks.
- **✎ chips** = stations whose answer becomes part of what you sign — a nonce, a gas price, an ENS resolution, a simulation verdict.
- **Cities** = transaction types: a normie MetaMask send, a Rabby send, an ENS send, a mountain-man self-sovereign send, an Aave deposit, background wallet polling. Same stations appear across cities — the Interchanges page ranks them; the Fixes page ranks the interventions that would retire them.

## Where the data comes from

Every station is sourced, not guessed:

- **M1 / M2 / Night** — traced from `MetaMask/metamask-extension` + `MetaMask/core` source (payload-level: what each request carries, which controller sends it) plus response-header fingerprinting of the hosts.
- **R1** — traced from `RabbyHub/Rabby` source: every construct/sign/broadcast call goes through `api.rabby.io`.
- **M3** — traced from the `aave/interface` source: Next.js API proxies (`/api/rpc-proxy` → Alchemy, `/api/preflight-compliance` → an unnamed screening vendor, `/api/prices-proxy` → Family), Amplitude `setUserId(walletAddress)`, Merit APIs.
- Unknown values are labeled **unknown** — never invented. Truncated hostnames are treated as fake data and are not allowed.

Wrong? Open an issue with the receipt (file + line of the client that makes the call).

## How it's built

One file: `index.html`, generated from a route data model — one JSON step per network request, carrying what it sends, who receives it, what a lie there costs, and which fix retires it. A build script compiles the steps into the maps, the Interchanges and Fixes rankings, and the CROPS scores; a ~200-line renderer draws each city from the compiled data, no dependencies, no framework. See [AGENTS.md](AGENTS.md) for the data model.
