# Last Train

Two single-file WebGL shooters set on the last northbound platform. No assets, no
libraries, no network — every texture, sound, mesh and system is generated at
runtime. Open either `.html` file directly in a browser, on desktop or phone.

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
corridor closing the loop. Hostiles spawn across every zone and path door to door
using a precomputed route graph, so waves arrive from all sides.

**Supply caches** drop in random distant areas each wave — marked on the radar,
with a beacon visible through the ceiling.

**Power failures** hit from wave three onward: the mains drop to emergency red and
your helmet lamp becomes the only thing between you and whatever is still moving.

### Who you are fighting

A black-clad tactical unit. Every figure is built from tapered cylinders and
ellipsoids with smooth normals — so limbs, torsos and heads actually read as round
— and articulated the way a body is: hips drive thighs, thighs drive shins, shins
drive boots, the torso counter-rotates against the stride and the head tracks you.
Knee pads, shin guards, elbow pads and moulded chest plates sit over charcoal
fatigues; the only colour on them is the eyewear. Skin tone and dye are picked per
unit so a squad is never one man copied six times. When they go down they collapse
under their own weight and stay where they fell.

| Unit | Notes |
| --- | --- |
| **Patrol** | Helmet, twin red goggle lenses, plate carrier, rifle. |
| **Runner** | Hooded, goggles, no armour — closes fast with a blade. |
| **Breacher** | Respirator under an orange visor, pauldrons, riot shield. Hit the flanks. |
| **Marksman** | Cap and scrim, magenta lenses, scoped DMR. Laser tell before the shot. |
| **Drone** | The one machine — a ducted quadcopter spotter. |
| **Warden** | Boss. Sealed helmet with a cyan visor, powered suit, three exposed cells. |

Figures beyond 12 m and 24 m drop mesh detail automatically, so a full squad does
not cost a full frame.

### Weapons

Seven, each a full assembly whose bolt rides back on every shot and whose magazine
physically leaves the well on a reload. All seven are in the locker from the first
wave — press `1`–`7` or roll the wheel:

| | Weapon | Character |
| --- | --- | --- |
| `1` | **AR-04** | Full-auto service rifle. Climbs about 2° under sustained fire. |
| `2` | **SG-12** | Ten-pellet breaching shotgun. Devastating inside 24 m. |
| `3` | **RAIL-9** | Rail lance. 155 damage, punches through six bodies. |
| `4` | **ARC-3** | Arc projector. Chains to three nearby targets. |
| `5` | **MP-9** | Machine pistol. 36 rounds, very fast, all crack and no body. |
| `6` | **GAU-2** | Rotary cannon. Winds up under the trigger, slows you while it spins. |
| `7` | **RPG-7** | Rocket launcher. A simulated projectile — dodgeable, and it hits whatever is in the way. |

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
| `V` | Rifle bash — 70 damage and a hard shove |
| `F` | Helmet lamp |
| `G` | Arc charge (proximity fused) |
| `R` | Reload |
| `1`–`7` / wheel | Weapons |
| `Esc` / `P` | Pause and settings |

Mouse sensitivity, field of view, aim assist and volume are in the settings panel
on the menu and the pause screen, and persist between sessions. Runs are scored
against a local top-eight leaderboard, with medals for things like clearing a wave
untouched or three kills with a single charge.

### On a phone

Touch controls appear automatically. The left stick moves, dragging the right side
aims, and holding the trigger fires every weapon rather than only the automatics.
Crouch, bash, lamp, dash, charge, reload and weapon swap all have thumb buttons,
laid out inside the safe area so nothing hides under a notch or a home bar.

- **Aim assist** is on by default on touch and adjustable in settings
- Entering a run asks for **fullscreen and a landscape lock**, and a portrait
  notice pauses the game if you turn the phone upright
- The backing store is **capped below native resolution** and reflections are off
  by default on handhelds
- **Haptics** on taking a hit and on landing one

---

## How it renders

Six shader programs, six passes per frame:

1. **Shadow** — packed-depth orthographic map, 3×3 PCF, texel-snapped and centred
   on the player so shadows stay crisp
2. **Reflection** — mirrored camera clipped at the floor plane, fresnel-weighted
   with a ripple, for the wet platform
3. **Main** — HDR half-float target, analytic ceiling-strip lights plus the twelve
   most relevant dynamic lights, a flashlight cone, derivative-based bump mapping,
   a motivated rim on character materials, and height fog
4. **Additive** — volumetric shafts, dust, drips, particles, beams, shockwaves
5. **Bloom** — bright cut and separable blur at two mip levels
6. **Composite** — ACES tonemap, FXAA, chromatic aberration, blast-ring
   refraction, vignette, grain, low-health grade

A 2048² atlas of 64 materials is painted with the 2D canvas API — concrete, tiling,
livery, ripstop uniform, skin, scuffed leather, armour cordura, webbing, weapon
polymer — and its luminance becomes a height channel the shader differentiates
into surface relief.

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
- **damped resonator banks** for the mechanical action, brass and impacts
- **tanh saturation**, the way a real report clips whatever records it

Twenty-nine sounds in up to four variants each, so sustained fire never loops.
Rendering happens in small slices during idle frames, so the first shot is never
late. The rotary cannon carries a live whine that tracks its spin.

Every shot is followed by its own **tunnel slap** — a darker, wider copy arriving a
beat later off the far end of the platform. The reverb underneath it is an impulse
response built from an image-source model of a tiled tunnel: discrete early
reflections, a flutter echo between the parallel walls about 10 m apart, and a tail
that loses its top end first. Distance buys propagation delay at the speed of
sound, air absorption and a wetter send.

Reloads play real foley against their own duration, and footsteps pick up whether
you are on concrete or in standing water.

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
