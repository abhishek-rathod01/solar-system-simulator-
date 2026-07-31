# Ephemeris

A real-time, physically accurate solar system simulator. Single self-contained
`index.html`, no build step. Open it in a browser.

Planet positions are computed every frame from J2000 Keplerian orbital elements
(JPL / Standish, *Approximate Positions of the Major Planets*) — Kepler's
equation solved by Newton–Raphson. Nothing is animated at an arbitrary speed;
the planets are where they actually are.
