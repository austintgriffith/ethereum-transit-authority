# For agents (and humans) editing this map

Everything is `index.html` — but it is **generated**: a build script compiles per-request route data into the `CITIES` objects below, and `render()` draws them at page load. You never write SVG — you write data. Edits to this file are overwritten on the next build; propose changes as issues with receipts.

## The data model

A **city** (one map) is:

```js
const M9={
 lanes:[                       // horizontal lines, top to bottom = one data item each
  {id:'ip', name:'Your IP + device', v:'--l-ip'},          // v = CSS color token
  {id:'signed', name:'THE SIGNED TX', v:'--l-signed', startsAt:'river', bold:true},
  {id:'rcpt', name:'Recipient', v:'--l-rcpt', opensAt:'EN1'}, // line born at a station
 ],
 riverAfter:3,                 // river (the signing) after this slot; null = no river on this map
 eth:{slot:6.2, lane:'signed'},// Ethereum terminus; omit if the map doesn't reach the chain
 zones:[[1,3,'DISCOVER']],     // labeled brackets across slot ranges
 houses:[{from:1,to:3.1,label:'YOUR MACHINE'}], // green region = user-run stations
 noservice:['jwt'],            // lanes drawn as dashed stubs: "no service on this route"
 calls:[                       // stations, west to east = chronological
  {slot:1, code:'XX1', name:'Station name', host:'full.real.hostname.com',
   carries:['ip','rcpt'],      // which lanes get a ring here
   blocks:1,                   // ✕ chip: can censor (breaks the CROPS line)
   lies:1,                     // ≈ chip: can return wrong data you'd act on
   txin:1,                     // ✎ chip: its answer becomes part of what you sign
   tunnel:1,                   // hatched collar: TLS terminates at Cloudflare
   self:1,                     // green rings: the user runs this station
   opt:1,                      // dashed: conditional / not every trip
   rep:'↻ 20 s',               // repeats on a timer
   tip:'hover tooltip — the receipt for this station'},
 ]};
```

Then: `render('map-m9', M9, 'M9 · N calls');` a `<section class="view" id="view-m9">` with `<figure id="map-m9">` and a `<div class="legend-slot">`, a nav pill, a home-page city row, and `'m9'` in the router's `views` array.

The 🌾 CROPS throughline, glyph row, device terminal, lane terminus caps, station labels, and interchange bars are all drawn automatically from the data.

## Rules (non-negotiable)

1. **No fake data.** Full real hostnames from source code or a network capture — never truncated, never "e.g.", never guessed. Unknown vendor → write `unknown` and note what research would resolve it. Every station should be traceable to a file:line in the client that makes the call.
2. **No prose.** No explanatory paragraphs, callout boxes, footers, or editorial sentences. Section intros are fragments ("MetaMask extension · mainnet · defaults"). Captions are counts ("M1 · 12 calls · 7 companies"). Facts live in the map, the tooltips, and the tables.
3. **Semantics:** a ring means *receives*; receiving implies the company can leak or retain it, so there is no separate "leak" marker. `blocks`/`lies` are the CROPS breaks. `self` stations don't break the line.
4. **Verify before shipping.** Render headless and look at it:
   `chrome --headless=new --screenshot=out.png --window-size=1500,2600 "file:///path/index.html#/m9"`
   Check: labels don't collide, lanes a station carries are ringed, the CROPS line and river draw, both themes work (`<script>document.documentElement.dataset.theme="dark"</script>` prepended for dark).

## Layout knobs

`PADL` (left pad), `SLOT` (px per slot), `laneGap`, `topPad` (rotated-label headroom — full hostnames need ~250). Non-integer slots space stations; keep labels ~1 slot apart or they overlap.
