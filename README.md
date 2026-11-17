# Muffin-Tin Methods

## 1. Overview

Muffin-tin methods are a family of techniques in computational solid-state physics and quantum chemistry for solving the one-electron Schrödinger (or Kohn-Sham) equation in crystals. They rest on a geometric and physical idealisation of the crystal potential, the **muffin-tin approximation (MTA)**, and on the construction of basis functions adapted to that idealisation. The name comes from the resemblance of the potential's shape, a set of spherical wells surrounded by a flat plateau, to the cavities of a muffin tin baking pan.

Muffin-tin methods were the workhorse of band-structure calculations from the 1930s through the 1980s and remain widely used today, in refined forms, in codes such as:

+ [WIEN2k](./wien2k.md),
+ [Elk](./elk.md),
+ [exciting](./exciting.md),
+ [FLEUR](./fleur.md),
+ Questaal,
+ and the KKR codes (Akai-KKR, SPR-KKR).

## 2. The Muffin-Tin Approximation

Consider a periodic crystal with lattice vectors $\mathbf{R}$ and a one-electron potential $V(\mathbf{r})$ that is periodic: $V(\mathbf{r} + \mathbf{R}) = V(\mathbf{r})$. The exact potential is a superposition of sharply peaked, nearly Coulombic structures near each nucleus and a comparatively smooth variation in the region between atoms.

The muffin-tin approximation partitions real space into two regions:

1. **Muffin-tin spheres.** Non-overlapping spheres of radius $R_\alpha$ centred on each atomic site $\alpha$. Inside each sphere the potential is taken to be spherically symmetric:
   
$$
V(\mathbf{r}) \approx V_\alpha(r), \qquad r = |\mathbf{r} - \boldsymbol{\tau}_\alpha| < R_\alpha.
$$

2. **The interstitial region.** The space outside all spheres, where the potential is taken to be constant:
   
$$
V(\mathbf{r}) \approx V_0.
$$

Conventionally $V_0$ is set to zero (or to a constant that absorbs the average interstitial potential), so that the interstitial region is effectively free-electron-like.

### 2.1 Physical Motivation

The approximation is justified by a separation of physical scales:

- Near a nucleus, wavefunctions are strongly varying, with many nodes, and the potential is steep and deep. These features are nearly spherical, because the nucleus dominates locally.
- Between atoms, the potential is shallow and smooth, and the wavefunctions are nearly plane-wave-like.

Choosing a spherical form inside the spheres and a constant outside captures the dominant physics while making the mathematics tractable: the radial problem can be solved exactly, and the interstitial problem is free-particle-like.

### 2.2 Limitations

The approximation is least accurate where the true charge density or potential departs from spherical symmetry:

- **Covalent and directional bonding**, such as in diamond-structure semiconductors, where charge accumulates between atoms.
- **Open structures** with large interstitial voids, where the constant-potential assumption breaks down.
- **Surfaces and interfaces**, where symmetry is reduced.
- **Low-symmetry crystals and molecules**, where the crystal field is strongly non-spherical.

These limitations motivated the development of **full-potential** methods, discussed in Section 5.

## 3. Mathematical Structure

### 3.1 The Radial Schrödinger Equation

Inside a muffin-tin sphere, write the wavefunction as a radial function times a spherical harmonic. In atomic units with the convention $\hbar^2/2m = 1$ (Rydberg-like units), the radial equation for angular momentum $l$ is

$$
\left[ -\frac{d^2}{dr^2} + \frac{l(l+1)}{r^2} + V_\alpha(r) - E \right] u_l(r, E) = 0,
$$

where $u_l(r,E) = r R_l(r,E)$ and $R_l$ is the radial part of the wavefunction. The solutions regular at the origin, $u_l(r,E)$, are the **radial functions** and are computed numerically, typically by integrating outward from the nucleus with a sufficiently fine logarithmic mesh.

The energy derivative of the radial function, $\dot u_l(r,E) = \partial u_l/\partial E$, is also needed for the linearised methods discussed below. It satisfies an inhomogeneous equation obtained by differentiating the radial equation with respect to $E$.

### 3.2 Bloch's Theorem

Because the potential is periodic, Bloch's theorem guarantees that eigenfunctions take the form

$$
\psi_{\mathbf{k}}(\mathbf{r}) = e^{i\mathbf{k}\cdot\mathbf{r}}\, u_{\mathbf{k}}(\mathbf{r}), \qquad u_{\mathbf{k}}(\mathbf{r} + \mathbf{R}) = u_{\mathbf{k}}(\mathbf{r}).
$$

The calculation therefore reduces to a single unit cell and a set of $\mathbf{k}$-points in the Brillouin zone.

### 3.3 Plane-Wave Expansion of the Interstitial Region

In the interstitial region, the natural basis is a set of plane waves $e^{i(\mathbf{k}+\mathbf{K})\cdot\mathbf{r}}$, where $\mathbf{K}$ runs over reciprocal lattice vectors. Any function that is smooth there can be expanded in such waves. The difficulty is that these waves do not match the oscillatory radial solutions inside the spheres without additional construction. This is the central problem that the augmented methods solve.

## 4. Augmented Basis Methods

### 4.1 The Augmented Plane Wave (APW) Method

Introduced by Slater in 1937, the APW method constructs basis functions that are plane waves outside the spheres and radial solutions inside, glued continuously at the sphere boundary. For a given $\mathbf{k}$-point and reciprocal vector $\mathbf{K}$, with $\mathbf{q} = \mathbf{k} + \mathbf{K}$:

$$
\phi_{\mathbf{q}}(\mathbf{r}) =
\begin{cases}
\displaystyle \frac{1}{\sqrt{\Omega}}\, e^{i\mathbf{q}\cdot\mathbf{r}}, & \mathbf{r} \in \text{interstitial}, \\[1.5ex]
\displaystyle \sum_{lm} A_{lm}^{\alpha}(\mathbf{q})\, u_l(r_\alpha, E)\, Y_{lm}(\hat{\mathbf{r}}_\alpha), & r_\alpha < R_\alpha.
\end{cases}
$$

Here $\Omega$ is the unit-cell volume, $\mathbf{r} _ \alpha = \mathbf{r} - \boldsymbol{\tau} _ \alpha$, and $Y _ {lm}$ are real or complex spherical harmonics.

The coefficients $A_{lm}$ are fixed by requiring continuity of the wavefunction at $r_\alpha = R_\alpha$. Using the Rayleigh plane-wave expansion

$$
e^{i\mathbf{q}\cdot\mathbf{r}} = 4\pi \sum_{lm} i^l\, j_l(qr)\, Y^*_{lm}(\hat{\mathbf{q}})\, Y_{lm}(\hat{\mathbf{r}}),
$$ 

one finds

$$
A_{lm}(\mathbf{q}) = \frac{4\pi\, i^l\, Y^*_{lm}(\hat{\mathbf{q}})\, j_l(qR)}{u_l(R, E)}\, \cdot \frac{1}{\sqrt{\Omega}}.
$$

Here $j_l$ is the spherical Bessel function.

**The energy problem.** The basis functions depend explicitly on the energy $E$ through $u_l(R,E)$. The Hamiltonian and overlap matrices therefore depend on $E$, and the eigenvalue problem takes the nonlinear form

$$
\det\big[\, H(E) - E\, S(E) \,\big] = 0.
$$

Solutions are found by root-finding in $E$ for each band and each $\mathbf{k}$-point. This is cumbersome, and near the energies where $u_l(R,E)$ vanishes the basis becomes ill-conditioned, producing spurious "ghost" states and a variational instability known as the *asymptote problem*.

### 4.2 Linearised Augmented Plane Wave (LAPW)

Andersen (1975) and related work by Koelling and Arbman (1975) addressed the energy-dependence problem by linearising the radial functions around a fixed reference energy $E_l$ for each angular momentum:

$$
u_l(r, E) \approx u_l(r, E_l) + (E - E_l)\, \dot u_l(r, E_l).
$$

The LAPW basis functions inside the sphere become

$$
\sum_{lm} \Big[ A_{lm}\, u_l(r_\alpha, E_l) + B_{lm}\, \dot u_l(r_\alpha, E_l) \Big] Y_{lm}(\hat{\mathbf{r}}_\alpha),
$$

with coefficients $A_{lm}$ and $B_{lm}$ fixed by matching both the value and the derivative of the radial function at $R_\alpha$ to the plane wave. Since the basis no longer depends on $E$, the problem becomes a **generalised linear eigenvalue problem**

$$
H\mathbf{c} = E\, S\mathbf{c},
$$

solvable with standard linear-algebra routines in a single pass.

The LAPW method is accurate for energies within a window around $E_l$, with errors of order $(E - E_l)^2$, and is the foundation of the most widely used all-electron electronic structure method.

### 4.3 Local Orbitals (LO) and APW+lo

Some semi-core and high-lying states cannot be described accurately by a single linearisation. Local orbitals (Singh, 1991) add radial functions, typically with a second energy $E_l'$ or a radial-derivative combination, that vanish at the sphere boundary and are confined inside the sphere. The resulting LAPW+LO scheme is accurate for a wider energy range and for semi-core states.

The APW+lo method combines the energy-dependent APW for valence states with local orbitals for the linearisation problem, avoiding the variational difficulties of the original APW while retaining its flexibility.

### 4.4 Convergence Parameters

LAPW convergence is controlled primarily by the dimensionless parameter

$$
R_{\mathrm{MT}} K_{\max},
$$

where $K_{\max}$ is the largest plane-wave wavevector included. Typical production values lie between 7 and 9. Additional parameters include the maximum angular momentum $l_{\max}$ inside the spheres, the number of $\mathbf{k}$-points, and the radial mesh density.

## 5. Full-Potential Extensions

The muffin-tin approximation is removed in **full-potential** methods. The potential and charge density are expanded in lattice harmonics inside the spheres and in plane waves in the interstitial:

$$
V(\mathbf{r}) = \sum_{lm} V_{lm}(r)\, Y_{lm}(\hat{\mathbf{r}} _ \alpha) \quad (r_\alpha < R_\alpha), \qquad V(\mathbf{r}) = \sum_{\mathbf{G}} V_{\mathbf{G}}\, e^{i\mathbf{G}\cdot\mathbf{r}} \quad (\text{interstitial}).
$$

The full-potential LAPW (FLAPW) method, implemented in WIEN2k, FLEUR, and exciting, is considered the most accurate general-purpose all-electron approach for solids. Its cost is higher, and the non-spherical terms require careful treatment of the potential matching at the sphere boundary.

Full-potential treatments are essential for:
- Accurate total energies and forces, needed for structural relaxation.
- Systems with strongly non-spherical charge distributions.
- Surfaces, interfaces, and low-symmetry materials.

## 6. Other Muffin-Tin-Based Formalisms

### 6.1 Linear Muffin-Tin Orbital (LMTO) Method

Developed by Andersen (1975), the LMTO method uses energy-independent muffin-tin orbitals built from solutions of the Schrödinger equation at a fixed energy and their energy derivatives, together with Hankel or Neumann functions that decay outside the spheres. The result is a compact, tight-binding-like basis with a small number of orbitals per atom. The **atomic sphere approximation (ASA)** variant takes spheres that fill the space (touching or overlapping), with the potential taken as spherical inside each atomic sphere. This makes the method very fast and well suited to chemical analysis and to large systems, at the cost of reduced accuracy for open or low-symmetry structures. Questaal implements such variants.

### 6.2 Korringa-Kohn-Rostoker (KKR) Method

The KKR method, formulated by Korringa (1947) and by Kohn and Rostoker (1954), is a multiple-scattering approach. Each muffin-tin sphere acts as a scatterer characterised by its phase shifts or $t$-matrix, derived from the spherical potential. The electronic structure is obtained from the **scattering path operator** $\tau(E)$ or from the condition that the multiple-scattering matrix has a nontrivial null space:

$$
\det\big[\, \mathbf{M}(E, \mathbf{k}) \,\big] = 0,
$$

where $\mathbf{M}$ combines the single-site $t$-matrices with the free-electron propagator between sites. A key advantage of KKR is that the Green's function $G(E)$ is computed directly for complex energies, which makes it natural for:

- Computing densities of states and the Fermi surface without explicit band calculations.
- Treating substitutional disorder through the coherent potential approximation (CPA), as in the Akai-KKR and SPR-KKR codes.
- Calculating spectroscopies such as photoemission and magnetic linear dichroism.
- Treating impurities, alloys, and surfaces in a Green's function framework.

The KKR approach is connected to the muffin-tin framework because the single-site scattering problem is solved exactly for a spherical potential, and the full crystal is built by multiple scattering between such sites. Full-potential KKR variants have been developed, but the muffin-tin version remains the most common.

### 6.3 Muffin-Tin Scattering in Diffraction and Spectroscopy

The muffin-tin potential is also used outside band theory. In low-energy electron diffraction (LEED) and photoelectron diffraction, the scattering of an electron from an atomic site is described by phase shifts computed from a muffin-tin potential. In these contexts the approximation is often justified because the scattering is dominated by the spherical part of each atomic potential.

## 7. Practical Considerations

### 7.1 Choosing the Muffin-Tin Radii

Muffin-tin radii should be large enough to contain the core and valence charge but small enough that spheres do not overlap significantly. Typical choices are 90 to 95 percent of half the nearest-neighbour distance for close-packed metals, adjusted for chemical species and the desired $R_{\mathrm{MT}} K_{\max}$. Overlap is not allowed in the standard MT formulation, but it is permitted in the ASA.

### 7.2 Core and Valence Treatment

Core states are typically fully contained inside the spheres and treated fully relativistically or semi-relativistically. Valence states are treated by the basis functions described above. Semi-core states often require local orbitals.

### 7.3 Relativistic Effects

Scalar-relativistic approximations are standard. Spin-orbit coupling is included through second-variational treatments or fully relativistic Dirac-based radial solvers inside the spheres. Relativistic corrections are particularly important for heavy elements.

### 7.4 Computational Cost and Scaling

All-electron LAPW calculations scale with the cube of the number of basis functions for diagonalisation, which limits system size. LMTO and KKR calculations are often faster for large systems. Modern codes use parallel MPI and OpenMP implementations and exploit symmetry extensively.

## 8. Summary

Muffin-tin methods approximate the crystal potential as spherically symmetric inside non-overlapping atomic spheres and constant in the interstitial region. This idealisation makes the problem tractable and enables highly accurate basis constructions:

| Method | Key idea | Energy dependence | Typical use |
|---|---|---|---|
| APW | Plane waves matched to radial functions | Yes (nonlinear) | Historical; foundation |
| LAPW | Linearised radial functions plus derivatives | No (generalised eigenproblem) | All-electron DFT, FP-LAPW |
| APW+lo / LAPW+LO | Adds local orbitals | Reduced | Semi-core states, accuracy |
| LMTO | Energy-independent muffin-tin orbitals | No | Fast, tight-binding-like, ASA |
| KKR | Multiple scattering via $t$-matrices and Green's functions | Via complex energy | Disorder (CPA), spectroscopy |

The muffin-tin approximation is physically motivated but has known limitations, which led to full-potential extensions. Today it remains important both as a computational technique in its own right and as a conceptual framework in solid-state physics.

## References for Further Reading

- J. C. Slater, "Wave Functions in a Periodic Potential," *Physical Review* 51, 846 (1937).
- O. K. Andersen, "Linear Methods in Band Theory," *Physical Review B* 12, 3060 (1975).
- D. D. Koelling and G. O. Arbman, "Use of Energy Derivative of the Radial Solution in an Augmented Plane Wave Method," *Journal of Physics F* 5, 2041 (1975).
- E. Sjöstedt, L. Nordström, and D. J. Singh, "An Alternative Way of Linearizing the Augmented Plane-Wave Method," *Solid State Communications* 114, 15 (2000).
- P. Blaha, K. Schwarz, F. Tran, R. Laskowski, G. K. H. Madsen, and L. D. Marks, "WIEN2k: An APW+lo Program for Calculating the Properties of Solids," *Journal of Chemical Physics* 152, 074101 (2020).
- W. Kohn and N. Rostoker, "Solution of the Schrödinger Equation in Periodic Lattices with an Application to Metallic Lithium," *Physical Review* 94, 1111 (1954).
- J. Korringa, "On the Calculation of the Energy of a Bloch Wave in a Metal," *Physica* 13, 392 (1947).
