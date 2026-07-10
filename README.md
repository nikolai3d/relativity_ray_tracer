# The Photon Ring ⚫

**Live demo: https://nikolai3d.github.io/relativity_ray_tracer/**

A general-relativistic ray tracer in a single WebGL2 file, answering the plea:

> *"People of science, hear my plea: they showed me a photograph of a glowing ring and called it a black hole. Is it true the far side of the disk arches over the shadow, and one wing burns brighter because the gas rushes toward us? Bend every ray honestly, and show me what the camera saw."*

Every pixel is a **null geodesic**, integrated live through Schwarzschild spacetime — the exact orbit equation d²u/dφ² = −u + (3/2)r<sub>s</sub>u², advanced by fourth-order Runge–Kutta, one ray per pixel per frame. The photon ring, the accretion disk's far side arching over the shadow, the Doppler-beamed wing, the Einstein-lensed star field: nothing is painted on, everything falls out of the geodesics.

## Running it

No build step, no dependencies — a single self-contained HTML file using raw WebGL 2.

**Option 1: just open the file**

```sh
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

**Option 2: serve it locally**

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

Prefers a browser with WebGL2 (any modern Chrome, Firefox, Safari, or Edge). Without WebGL2 the float64 CPU engine carries on alone — patient, but honest.

## Using the simulator

- **Drag** to orbit the camera, **scroll / pinch** to zoom.
- **Relativity, à la carte** — switch each effect off to see what it was doing:
  - *Bend light* off = Newton's straight rays: a flat saucer with a dark ball in the middle.
  - *Doppler & beaming* off = the symmetric golden disk (the choice Interstellar's artists made on purpose).
  - *Time dilation* off = no gravitational or orbital redshift.
- **Scenes**: *Edge-on furnace* (the classic lopsided portrait), *M87\* portrait* (the EHT geometry, 17° from the jet axis), *The Interstellar look* (lensing kept, beaming dropped — Hollywood's confession), *Naked lens* (no disk, just the star field smeared into arcs and an Einstein ring), *Newton's sky* (everything the real image has and this one lacks is general relativity).
- **Compute engine** — the same rays, two arithmetics:
  - **GPU · float32** — every pixel in parallel in fragment shaders, tens of millions of geodesics per second.
  - **CPU · float64** — the identical algorithm in double precision, rendered progressively as the reference.
- **The ruler is the mass** — the geometry on screen is scale-free (everything is in Schwarzschild radii); picking 10 M☉ / Sgr A\* / M87\* only sets the physical ruler and the clock. For Sgr A\* the readout gives a 52 μas photon ring and a 31-minute ISCO orbit; for M87\*, ~40 μas and 34 days — the sizes and cadences the Event Horizon Telescope actually measured.

## Honesty checks

The readout panel measures the renderer against theory, live:

- **Photon ring**: a float64 reference integrator (25,000 RK4 steps per ray, bisection on the capture boundary) measures the critical impact parameter and compares it with the exact 3√3⁄2 r<sub>s</sub> = 2.598076… (typical agreement: ~10⁻¹²).
- **Weak-field deflection**: a ray at b = 50 r<sub>s</sub> against Einstein's α = 4M/b (with the second-order term).
- **Render-grade vs reference**: the deflection error of a strongly-bent ray at your current quality slider.
- **float32's toll**: the same ray replayed with float32 rounding after every step — what the GPU's arithmetic costs.

## The science (and the honest caveats)

The disk is a thin Shakura–Sunyaev blackbody disk truncated at the ISCO (3 r<sub>s</sub>), with each pixel shifted by the exact combined factor g = √(1−3M/r) / [(1−ΩL<sub>z</sub>/E)√(1−r<sub>s</sub>/r<sub>cam</sub>)] and its intensity scaled by g⁴. The swirling brightness pattern is decoration, but it is advected at the true Keplerian angular speed at every radius. **Not modeled:** spin (this is Schwarzschild, not Kerr — no frame dragging, and real black holes rotate), radiative transfer and polarization (M87\*'s real glow is synchrotron plasma, not a blackbody), an orbiting camera (ours hovers as a static observer), and a real star catalog (the sky is procedural). The in-app "science" panel spells all of this out.
