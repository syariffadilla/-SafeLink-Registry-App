# SafeLink Registry — App

The web app for the SafeLink Registry Intelligent Contract on GenLayer
Studionet (chain 61999). It calls the deployed contract — nothing is mocked.

- **Live demo:** https://safe-link-on-gen-layer.vercel.app/
- **Contract:** `0x2920A2340F47226490Ac645bDCE64C390Fac40DA`
  ([Studio explorer](https://explorer-studio.genlayer.com/address/0x2920A2340F47226490Ac645bDCE64C390Fac40DA))
- **Contract source (in this repo):** [`contracts/safelink.py`](contracts/safelink.py)
  + [`contracts/safelink_logic.py`](contracts/safelink_logic.py) — the exact
  source deployed at the address above. Full history + tests:
  [SafeLink-On-GenLayer](https://github.com/syariffadilla/SafeLink-On-GenLayer)
- **Intelligent Contract:** accepted, 100 Builder pts, 13 Nov 2026.
- **Design:** warm paper / ink, flat verdict colours, serif + grotesque + mono.
  No gradients, no glassmorphism, no marketing hero — the check form and the
  live registry data are the page.

## Smart contract

The Intelligent Contract this app calls lives in this repository under
`contracts/` — `safelink.py` (the contract) and `safelink_logic.py` (the
pure logic it is assembled from, byte-identical). It is the exact source
deployed at `0x2920A2340F47226490Ac645bDCE64C390Fac40DA` on GenLayer
Studionet (chain 61999). The app's `src/lib/safelink.js` calls `check_url`,
`get_verdict`, `is_safe`, `list_entries`, `get_stats`, `get_brands`,
`dispute_url` and `request_recheck` on that deployed contract.

## What it does (contract method in brackets)

- Connect wallet on Studionet, auto add/switch network (`wallet_addEthereumChain`).
- **Check a URL** — on-chain consensus round [`check_url`], full tx lifecycle
  (submitted → finalized), tx hash + explorer link, verdict + all signals.
  Undetermined rounds render as *unchecked*, never as a crash.
- **Look up** — read-only stored verdict [`get_verdict`, `is_safe`], no wallet.
- **Recent entries** [`list_entries`, newest first] and **registry stats**
  [`get_stats`], brands [`get_brands`], blocked hosts.
- **Dispute** [`dispute_url`] and **re-check** [`request_recheck`] on any entry.
- Admin functions (`add/remove_brand`, `block/unblock_host`, `set_ttl_checks`)
  are intentionally NOT in the app: the admin is the Studio deployer account
  (`0x01ED7Ae5A842895C7E05a3CE5587d8DC67a6102F`), not the visitor.

## Run locally

```sh
npm ci
npm run dev        # vite dev server
npm run build      # production build -> dist/
```

## Verify against the live contract (no key needed for reads)

```sh
node scripts/read.mjs
# e2e write path (burner key in /tmp/safelink-e2e-key, Studio faucet, test GEN only):
node scripts/e2e.mjs faucet
node scripts/e2e.mjs check https://example.net/
node scripts/e2e.mjs wait <txhash>
```

Verified 2026-11-13: `check_url("https://example.net/")` →
tx `0x951e0259d680624915ccad3d57c3f91b43fda3bba57ee63d0a0bebce298c6725`,
FINALIZED, MAJORITY_AGREE, verdict **safe** (checked_seq 3).
Registry at that point: 3 entries (example.com safe, example.org malicious,
example.net safe), check_count 3.

## Deploy

Vercel, static Vite build (see `vercel.json`). Live at
https://safe-link-on-gen-layer.vercel.app/ since 13 Nov 2026.

## Testnet only

GenLayer Studionet is a testnet. Test GEN only; nothing here has real value.
Never enter a seed phrase anywhere in this app — it only ever asks the
injected wallet to sign.
