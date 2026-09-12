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

Eight waves stand between you and the last service out. Take the station back —
or switch to Endless and see how long you last.

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

**Power failures** hit from wave three onward: the mains drop to emergency red and
your helmet lamp becomes the only thing between you and whatever is still walking.

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
and a sweeping cutting beam. In Endless the Warden returns every sixth wave.

Fifteen roguelite modifications, offered three at a time after every odd wave.

### Controls

| | |
| --- | --- |
| `WASD` | Move |
| `Mouse` | Aim / fire |
| `RMB` | Focus aim |
| `Shift` | Sprint |
| `Space` | Dash (brief invulnerability) |
| `C` / `Ctrl` | Crouch — slower, steadier, and low cover actually stops rounds |
| `V` | Rifle bash — 70 damage and a hard shove, for anything in your face |
| `F` | Helmet lamp |
| `G` | Arc charge (proximity fused) |
| `R` | Reload |
| `1`–`4` / wheel | Weapons |
| `Esc` / `P` | Pause and settings |

Mouse sensitivity, field of view and volume are in the settings panel on the menu
and the pause screen, and persist between sessions. Touch controls appear
automatically on coarse pointers.

Runs are scored against a local top-eight leaderboard, with medals for things like
clearing a wave untouched, 15 headshots, or three kills with a single charge.

---

## How it renders

Six shader programs, six passes per frame:

1. **Shadow** — packed-depth orthographic map, 3×3 PCF, texel-snapped and centred
   on the player so shadows stay crisp
2. **Reflection** — mirrored camera clipped at the floor plane, fresnel-weighted
   with a ripple, for the wet platform
3. **Main** — HDR half-float target, analytic ceiling-strip lights plus the twelve
   most relevant dynamic lights, a flashlight cone, derivative-based bump mapping
   and height fog
4. **Additive** — volumetric shafts, dust, drips, particles, beams, shockwaves
5. **Bloom** — bright cut and separable blur at two mip levels
6. **Composite** — ACES tonemap, FXAA, chromatic aberration, blast-ring
   refraction, vignette, grain, low-health grade

A 2048² material atlas is painted with the 2D canvas API and its luminance becomes
a height channel the shader differentiates into surface relief.

Three quality presets (Ultra / High / Fast) toggle shadows, reflections and
resolution; the game picks one automatically and remembers your choice.

## How it sounds

Oscillators with ramped envelopes always sound synthetic, because a real gunshot
peaks in well under a millisecond and is mostly structure after that. So every
impulsive sound is **rendered sample by sample** into a buffer from a physical
sketch:

- a **Friedlander blast wave** for the muzzle — instant peak, decaying through
  zero into a negative phase
- an **N-wave** for the supersonic crack off the projectile
- **shaped noise** for the gas turbulence, its cutoff collapsing as the cloud
  expands
- **damped resonator banks** for the mechanical action, brass, impacts and every
  metallic object
- **tanh saturation**, the way a real report clips whatever records it

Twenty-six sounds in four variants each, so sustained fire never loops. Rendering
happens in small slices during idle frames, so the first shot is never late.

The reverb is an impulse response built from an image-source model of a tiled
tunnel: discrete early reflections, a flutter echo between the parallel walls about
10 m apart, and a tail that loses its top end first. Distance buys propagation
delay at the speed of sound, air absorption and a wetter send.

Reloads play real foley against their own duration — magazine out, magazine in,
bolt release — and footsteps pick up whether you are on concrete or in standing
water.

## Debugging

The published page exposes `window.LastTrain` for the browser console:

```js
LastTrain.stats()          // collider / vertex counts, capabilities
LastTrain.bank()           // rendered sound variants per name
LastTrain.quality = 0      // 0 fast, 1 high, 2 ultra
LastTrain.outage(10)       // cut the power for ten seconds
LastTrain.endless = true
LastTrain.spawnNow('heavy', 2)
LastTrain.zoneOf(x, z)     // which area a point is in
LastTrain.blockers(x, z, r) // what is obstructing a point
```

## Requirements

WebGL 1 with hardware acceleration. Extensions (`OES_standard_derivatives`,
`EXT_shader_texture_lod`, `OES_texture_half_float`, anisotropic filtering) are
used when present and degrade cleanly when not.
