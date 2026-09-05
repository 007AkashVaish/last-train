# Last Train

Two single-file WebGL shooters set on the last northbound platform. No assets, no
libraries, no network — every texture, sound, mesh and system is generated at
runtime. Open either `.html` file directly in a browser.

| File | What it is |
| --- | --- |
| [`last-train.html`](last-train.html) | The original. 167 lines, 5 waves, one platform. |
| [`last-train-ultra.html`](last-train-ultra.html) | **Terminus** — the full version below. |

---

## Last Train: Terminus

Eight waves stand between you and the last service out. Take the station back.

### The station

Eight connected areas forming a loop, with real elevation — the concourse sits a
storey above the running lines.

```
   UPPER CONCOURSE ── ramp ── NORTH HALL ──┐
         │                                 │
   cross passage                    PLATFORM 04 ── MAINTENANCE BAY
         │                                 │
   PLATFORM 03 ───── SOUTH LINK ───────────┘
```

A ticket hall with turnstiles and a departure board, a stepped access ramp, a wet
cross-passage, a second platform with a derailed and sparking train, and a service
corridor closing the loop. Enemies spawn across every zone and path door to door
using a precomputed route graph, so waves arrive from all sides.

**Supply caches** drop in random distant areas each wave — marked on the radar,
with a beacon visible through the ceiling.

### Combat

Four weapons, each a full assembly whose bolt rides back on every shot and whose
magazine physically leaves the well on a reload:

| | Weapon | Character |
| --- | --- | --- |
| `1` | **AR-04** | Full-auto service rifle. Climbs about 2° under sustained fire. |
| `2` | **SG-12** | Ten-pellet breaching shotgun. Devastating inside 24 m. |
| `3` | **RAIL-9** | Rail lance. 155 damage, punches through six bodies. |
| `4` | **ARC-3** | Arc projector. Chains to three nearby targets. |

Six enemy archetypes — Sentry, Rusher, Drone, Enforcer (frontal armour), Marksman
(laser telegraph), and the three-phase **Warden** boss with three breakable cores
and a sweeping cutting beam.

Fifteen roguelite modifications, offered three at a time after waves 1, 3, 5 and 7.

### Controls

| | |
| --- | --- |
| `WASD` | Move |
| `Mouse` | Aim / fire |
| `RMB` | Focus aim |
| `Shift` | Sprint |
| `Space` | Dash (brief invulnerability) |
| `G` | Arc charge (proximity fused) |
| `R` | Reload |
| `1`–`4` / wheel | Weapons |
| `Esc` / `P` | Pause |

Touch controls appear automatically on coarse pointers.

---

## How it renders

Six shader programs, six passes per frame:

1. **Shadow** — packed-depth orthographic map, 3×3 PCF, texel-snapped and centred
   on the player so shadows stay crisp
2. **Reflection** — mirrored camera clipped at the floor plane, fresnel-weighted
   with a ripple, for the wet platform
3. **Main** — HDR half-float target, analytic ceiling-strip lights plus the twelve
   most relevant dynamic lights, derivative-based bump mapping, height fog
4. **Additive** — volumetric shafts, dust, drips, particles, beams, shockwaves
5. **Bloom** — bright cut and separable blur at two mip levels
6. **Composite** — ACES tonemap, FXAA, chromatic aberration, blast-ring
   refraction, vignette, grain, low-health grade

A 2048² material atlas is painted with the 2D canvas API and its luminance
becomes a height channel the shader differentiates into surface relief.

Three quality presets (Ultra / High / Fast) toggle shadows, reflections and
resolution; the game picks one automatically and remembers your choice.

## How it sounds

Every sound is synthesised through the Web Audio API. Gunfire is four layers
stacked tight — a 12 ms bright transient crack, a pink-noise body sweeping down as
gas expands, a low sine thump, and the mechanical action a beat later — with a
distinct profile per weapon. Surface-aware impacts, supersonic crack as rounds
pass your head, distance-based air absorption, and a convolution reverb built from
a decaying noise burst give the platform its tunnel tail.

## Debugging

The published page exposes `window.LastTrain` for the browser console:

```js
LastTrain.stats()          // collider / vertex counts, capabilities
LastTrain.quality = 0      // 0 fast, 1 high, 2 ultra
LastTrain.spawnNow('heavy', 2)
LastTrain.zoneOf(x, z)     // which area a point is in
LastTrain.blockers(x, z, r) // what is obstructing a point
```

## Requirements

WebGL 1 with hardware acceleration. Extensions (`OES_standard_derivatives`,
`EXT_shader_texture_lod`, `OES_texture_half_float`, anisotropic filtering) are
used when present and degrade cleanly when not.
