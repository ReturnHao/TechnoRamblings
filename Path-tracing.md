# Path-tracing

Propelled by an explosion in computing power over the past decade and a half, path tracing has swept through visual media.

Path tracing nowadays is going real time, unleashing interactive, photorealistic 3D environments filled with dynamic light and shadow, reflections and refractions.

## Issue 1: "Rasterization", "Ray-tracing" & "Path-tracing"

The first, rasterization, is a technique that produces an image as seen from a single viewpoint. It’s been at the heart of GPUs from the start.

Ray tracing is a more powerful technique than rasterization. Rather than being constrained to finding out what is visible from a single point, it can determine what is visible from many different points, in many different directions.

Being able to trace all of those rays makes it possible to simulate how light scatters in the real world **much more accurately** than is possible with rasterization.

Path tracing should be attributed to Jim Kajiya in his 1986 paper, *The Rendering Equation.* It connected computer graphics with physics by way of ray tracing and introduced the path-tracing algorithm, which makes it possible to accurately represent the way light scatters throughout a scene.

Overall, one of the big differences between ray tracing and path tracing is that the former is **deterministic(Technique)**, whereas the latter is **probabilistic(Algorithm)**. Ray tracing just does a bunch of math until it arrives at the answer, but in path tracing the image quality depends on how much effort you put into it.

## Issue 2: "Recursive" & "Iterative"

**Path tracing itself also recursively tracing every ray, although it eliminates this recursion by Russian Roulette.**

Unfortunately, the current generation of GPU hardware doesn't support recursive functions. But we can usually transform recursion into iteration using a trick called [tail recursion](http://en.wikipedia.org/wiki/Tail_call).

In this situation, radiance function can be represented as:
$$
\begin{aligned}
L_0 & = L_{e0} + f_0 * L_1 \\
& = L_{e0} + f_0 * (L_{e1} + f_1 * L_2) \\
& = L_{e0} + f_0 * (L_{e1} + f_1 * (L_{e2} + f2*(L_3)) \\
& = L_{e0} + f_0 * L_{e1} + f_0 * f1 * L_{e2} + f0 * f1 *f2 * L_{e3} + f0 * f1 * f2 * f3 * L_{e4} + \cdots \\
\end{aligned}
$$
where
$$
\begin{aligned}
& n = tracing\ depth: 0\ for\ the\ screen, 1\ for\ the\ first\ object\ hit\ by\ ray... \\
& L_i = Outgoing\ radiance\ of\ object\ that\ was\ hit\ by\ ray\ at\ depth\ i \\
& L_{e_i} = Color\ of\ object\ that\ was\ hit\ by\ ray\ at\ depth\ i \\
& f_i = reflectivity\ of\ object\ that\ was\ hit\ by\ ray\ at\ depth\ i
\end{aligned}
$$
Then we can iterate calculate $L_e$ and keep track of the product of all $f_i$.

Since iterative loop does not allow ray branches, iterative path tracer no longer track all radiances as recursive one does. For instance, when dealing with dielectric surface, path tracer **randomly selects the reflection or refraction ray** for calculation (**Remember: this is the limitation of the iterative, not the path tracer itself**).
