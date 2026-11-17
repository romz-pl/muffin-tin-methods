# WIEN2k

[WIEN2k](https://susi.theochem.tuwien.ac.at/) is a computer program for electronic-structure calculations in solids, based on density functional theory (DFT). It is among the most precise and widely used codes for computing the properties of crystalline materials, and it has been a standard reference in solid-state physics, materials science, and computational chemistry for more than two decades.

Extensive overview on WIEN2k is given in article [WIEN2k: An APW+lo program for calculating the properties of solids, J. Chem. Phys. 152, 074101 (2020)](https://doi.org/10.1063/1.5143061)


**Origins and development**

The code descends from WIEN, first released around 1990 by Peter Blaha, Karlheinz Schwarz, and collaborators at TU Wien (Vienna University of Technology). WIEN2k, a substantially rewritten successor, appeared in 2001 and has been developed continuously since, with new versions released every year or two. The group that maintains it is still centered in Vienna. WIEN2k is distributed under a license agreement rather than as open-source software, so users obtain it through the official distribution channels and agree to its terms.

**Underlying method**

WIEN2k implements the full-potential (linearized) augmented plane-wave method with local orbitals, usually abbreviated LAPW+LO. The crystal is divided into two regions. Around each atom lie non-overlapping spheres, called muffin-tins, where the wavefunctions are expanded in radial functions multiplied by spherical harmonics. The remaining space between the spheres, the interstitial region, is described by plane waves. Because the method is "full-potential," it makes no shape approximation to the effective potential or the charge density, which is why it is considered one of the most accurate approaches for DFT calculations. Local orbitals are added to describe semicore states and to improve the flexibility of the basis at the energies where it is needed.

The size of the basis is controlled mainly by a parameter called RKmax, the product of the smallest muffin-tin radius and the largest plane-wave wavevector. Convergence with respect to RKmax, together with the k-point mesh, is a central part of doing a WIEN2k calculation responsibly.

**Physical models and capabilities**

WIEN2k supports a broad range of exchange-correlation functionals, including the local density approximation (LDA), generalized gradient approximations such as PBE and PBEsol, and meta-GGA functionals such as TB-mBJ, which is widely used for improving band gaps. It also includes DFT+U corrections for strongly correlated electrons, hybrid functional options in more recent versions, and relativistic treatments. Spin-orbit coupling is included through a second-variation approach, and magnetic calculations can be spin-polarized or, in newer releases, noncollinear.

The range of properties that can be computed is wide. Standard outputs include total energies, band structures, densities of states, Fermi surfaces, charge densities, and electron density analyses. Structural optimization is possible through forces and total-energy minimization. Hyperfine properties such as electric field gradients and isomer shifts, which connect to Mössbauer and NMR experiments, are a notable strength. Optical properties, X-ray spectroscopy including near-edge spectra, and core-level spectroscopy are supported through dedicated modules. Elastic constants can be obtained by applying strains, and transport quantities are accessible through interfaces with codes such as BoltzTraP. Phonon calculations are typically carried out with finite-displacement approaches coupled to external tools.

**Workflow and structure**

A WIEN2k project is organized around a "case" name, with many files sharing that prefix. The user defines the crystal structure in a file called case.struct, initializes the calculation with an init script that generates the necessary input files, and then runs the self-consistent field cycle. That cycle is driven by a set of programs. Lapw0 computes the potential, lapw1 solves for the eigenvalues and eigenvectors using a Davidson-type diagonalization, lapw2 constructs the density, and a mixer combines old and new densities until convergence. Additional programs handle spin-orbit coupling, core states, and post-processing tasks.

A web-based interface called w2web provides a graphical front end for setting up structures, running cycles, and inspecting results, though many experienced users work largely from the command line and text input files. Visualization is commonly handled with external tools such as XCrySDen.

**Parallelization and performance**

WIEN2k can run in parallel using MPI, typically distributing work over k-points, and a hybrid MPI and OpenMP mode is available for more efficient use of modern multicore nodes. Large diagonalization steps can use ScaLAPACK. Even so, the cost of the calculation grows steeply with the number of atoms and the size of the basis, so very large supercells, complex defect studies, or extensive molecular dynamics are typically more demanding than with pseudopotential codes. Ab initio molecular dynamics is not a central feature of the package.

**Applications and reputation**

WIEN2k is used extensively for research on metals, semiconductors, insulators, oxides, intermetallics, superconductors, magnetic materials, and high-pressure phases. Its accuracy has made it a common reference in comparative benchmark studies of DFT codes, and it has been the basis for a very large body of published work. Its main strengths are precision, the completeness of its property calculations, and its treatment of all electrons, including core electrons, in a full-potential framework. Its main drawbacks are the steep computational scaling, a considerable learning curve for setting up calculations correctly, and a licensing model that differs from freely available open-source alternatives.

Because software versions change, I'd recommend checking the official WIEN2k website for the current release, its feature list, and licensing details before relying on any specific version-dependent capability.
