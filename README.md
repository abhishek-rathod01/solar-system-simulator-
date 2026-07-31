# Ephemeris

A real-time solar system simulator. One self-contained `index.html`, no build
step, no bundler — open it in a browser.

The planets are not animated. Their positions are solved every frame from
J2000 Keplerian orbital elements, so they are where they actually are.

## Running it

Open `index.html`. That's it. Three.js loads from a CDN via an ESM import map,
so the page needs network access on first load.

## The orbital mechanics

Elements are Standish's *Keplerian Elements for Approximate Positions of the
Major Planets* (JPL Solar System Dynamics), Table 1 — valid 1800–2050 AD, with
per-century rates. Per planet, per frame:

1. `T = (JD − 2451545.0) / 36525`
2. Mean anomaly `M = L − ϖ`, wrapped to [−180°, 180°]
3. Kepler's equation `M = E − e·sin E` by Newton–Raphson, seeded
   `E₀ = M + e·sin M`, to `|ΔE| < 1e-8` rad or 8 iterations
4. `r = a(1 − e·cos E)`,
   `ν = 2·atan2(√(1+e)·sin(E/2), √(1−e)·cos(E/2))`
5. Rotate the orbital-plane position into the ecliptic by `ω = ϖ − Ω`, then
   inclination `i`, then `Ω`

Orbital velocity in the info panel is vis-viva: `v = √(GM(2/r − 1/a))`.

### Verification

The page prints a verification gate to the console on load. Earth's
heliocentric ecliptic longitude at 2000-01-01 12:00 UTC computes to
**100.3802°** — matching `L = 100.46457°` less the small perihelion offset, and
putting the Sun's apparent geocentric longitude at 280.38°. If it were reading
~280°, the frame would be geocentric and everything would be 180° out.

Independently checked while building:

- Mercury (`e = 0.2056`, the stress case) converges in **3** Newton iterations;
  worst residual `4.4e-16` rad over 4000 samples spanning 200 years.
- Every `dL/dT` reproduces its planet's published sidereal period to better
  than 0.03%, which corroborates the transcription of the element table.
- All eight planets stay within their own perihelion/aphelion bounds across
  the whole 1800–2050 scrubber range.
- Instantaneous vis-viva speeds track published mean orbital speeds within
  normal orbital variation.

### Scale

True scale is unviewable, so distance and radius are on separate scales that
lerp independently to reality over 2.2 s when you press **1:1**.

Distances use `r^p` with `p` easing 0.62 → 1.0, anchored on Neptune's orbit so
that the outermost ring holds the same screen radius at every scale — the
collapse implodes the inner system toward the centre instead of flinging the
outer planets off-screen. Orbit paths are sampled in true AU and each sample is
pushed through the same radial map, so the Sun stays at a focus and Mercury's
eccentricity stays visible at every scale.

At true scale the Sun's disc really is sub-pixel, so it stops being a disc and
becomes what it actually is from out there: a point source, one more star.

## Controls

| | |
|---|---|
| Orbit / pan / zoom | drag, right-drag, wheel. One finger orbits, two pinch |
| Select a planet | click it, or use the rail |
| Cycle planets | ← / → |
| System view | Escape |
| Pause | Space |
| Travel | scrubber, speed slider (1 s/s → 1 yr/s), or type a date |

`prefers-reduced-motion` starts paused, damps the bloom, and skips the intro.
