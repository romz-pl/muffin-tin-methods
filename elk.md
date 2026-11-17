# Elk Code

## Overview

[Elk](https://elk.sourceforge.io/) is an all-electron, full-potential linearised augmented-plane-wave (FP-LAPW) code for determining the properties of crystalline solids within density functional theory (DFT). Its developers describe it as highly accurate and as designed to be as developer-friendly as possible, so that new ideas in DFT can be added quickly and reliably. It is released under the GNU General Public License (GPL), which makes the source freely available for inspection, modification and redistribution. The latest version listed on the project website is 11.0.2.

The code was originally written at Karl-Franzens-Universität Graz as a milestone of the EXCITING EU Research and Training Network, which is the origin of its name's lineage and of much of its design. It is now developed mainly by the Theory Department of the Max Planck Institute of Microstructure Physics in Halle, where it serves as a testbed for new methods. The principal developers named on the institute's pages are K. Dewhurst and S. Sharma, with contributions from many others over roughly fifteen years of development.

## The Scientific Problem Elk Addresses

Kohn-Sham DFT has made electronic-structure calculations of complex materials routine, and it is widely used for materials search and design before expensive experiments are carried out. The reliability of such predictions, however, depends on the numerical accuracy of the underlying code. Many electronic-structure codes rely on pseudopotentials, which replace the strongly bound core electrons with an effective potential. Others treat core and valence electrons differently. Elk belongs to the comparatively small family of all-electron codes, which treat every electron in the solid on the same footing. This matters most for properties that are sensitive to the core region, such as hyperfine fields, core-level spectroscopy, relativistic effects in heavy elements and certain magnetic properties.

The Kohn-Sham equations are solved self-consistently,

$$
\left[-\frac{1}{2}\nabla^{2} + v_{\text{eff}}(\mathbf{r})\right]\psi_{i\mathbf{k}}(\mathbf{r}) = \varepsilon_{i\mathbf{k}}\,\psi_{i\mathbf{k}}(\mathbf{r}),
$$

where the effective potential $v_{\text{eff}}$ contains the external, Hartree and exchange-correlation contributions. In Elk the Kohn-Sham equations are solved in a two-step procedure. In the first-variational step, a Hamiltonian containing only the scalar potential and the external electric field is constructed and diagonalised. The second-variational step then incorporates the spin-orbit coupling and the magnetic fields in a reduced basis built from the first-variational eigenstates.

## The LAPW Method and Elk's Basis

In the LAPW method, space is partitioned into non-overlapping spheres centred on the atomic nuclei, called muffin-tins, and the remaining interstitial region. Inside each muffin-tin, the wavefunctions are expanded in radial functions multiplied by spherical harmonics, with the radial functions chosen to match smoothly to plane waves in the interstitial region. The basis functions take the general form

$$
\phi_{\mathbf{k}+\mathbf{G}}(\mathbf{r}) =
\begin{cases}
\displaystyle\sum_{\ell m}\left[A_{\ell m}^{\alpha,\mathbf{k}+\mathbf{G}}\,u_{\ell}^{\alpha}(r_\alpha,E_\ell) + B_{\ell m}^{\alpha,\mathbf{k}+\mathbf{G}}\,\dot{u}_{\ell}^{\alpha}(r_\alpha,E_\ell)\right] Y_{\ell m}(\hat{\mathbf{r}}_\alpha), & \mathbf{r} \in \text{muffin-tin } \alpha,\\[1ex]
\dfrac{1}{\sqrt{\Omega}}\,e^{i(\mathbf{k}+\mathbf{G})\cdot\mathbf{r}}, & \mathbf{r} \in \text{interstitial},
\end{cases}
$$

where $u_\ell^\alpha$ are radial solutions at energy $E_\ell$, $\dot{u}_\ell^\alpha$ is their energy derivative, and the coefficients $A$ and $B$ are fixed by matching at the muffin-tin surface. Elk's distinguishing feature within this family of methods is the breadth of its basis options, which go well beyond the basic construction:

- **APW radial derivative matching.** The radial derivative matching at the muffin-tin surface can be extended to arbitrary order, which the developers refer to as super-LAPW or related schemes. This allows the basis to be tailored to the accuracy needed.
- **Local orbitals.** Elk supports an arbitrary number of local orbitals. These are additional radial functions confined to a single muffin-tin, used to describe semicore or high-lying states accurately. A practical consequence is that all core states can be made valence if the calculation requires it.
- **Flexible basis construction.** Because APW functions, local orbitals and their combinations are all available, the user can trade basis size against accuracy for the particular system at hand.

## Symmetry, k-Points and Density Treatment

Elk performs full symmetrisation of the density and magnetisation and of their conjugate fields, so that the crystal symmetry is enforced throughout the self-consistent cycle. The code also determines and reduces the k-point set automatically from the symmetry of the structure. Both features reduce the computational cost and help keep results consistent with the space-group symmetry of the crystal.

The Hartree potential is obtained by solving Poisson's equation, which in the LAPW setting requires treating the charge density inside and outside the muffin-tins. In Elk, the same machinery is used with complex charge densities to compute Coulomb matrix elements of the form $\langle \psi_i \psi_j \,|\, 1/|\mathbf{r}-\mathbf{r}'| \,|\, \psi_k \psi_l \rangle$. These matrix elements are needed for several of the more advanced features discussed below, including Hartree-Fock, the optimised effective potential method and reduced density matrix functional theory.

## Feature Set

Elk offers an unusually wide range of capabilities, and a distinctive aspect is that many of them can be combined with each other. The developers emphasise that almost all features can be used together, which makes the code both powerful and robust across different types of calculation.

### Ground-State Properties

Standard DFT ground-state calculations are the core of the code. The available exchange-correlation functionals include the standard local and gradient-corrected approximations, and the code can make use of the external libxc library when it is available, which greatly expands the range of functionals. Magnetic calculations, including the treatment of spin-polarised and magnetically ordered systems and magnetic anisotropy, are supported. Elk can also compute magnetic anisotropy energies, which the release notes show as an actively maintained part of the code.

### Lattice Dynamics and Electron-Phonon Coupling

Elk computes phonon dispersions using density functional perturbation theory (DFPT). Phonon calculations can be distributed across networked computers, which is useful for large q-point grids. The code also provides electron-phonon coupling matrices and phonon linewidths. From these it derives the Eliashberg function $\alpha^2 F(\omega)$ and the electron-phonon coupling constant $\lambda$, and it can estimate the superconducting critical temperature $T_c$ using the McMillan-Allen-Dynes formula,

$$
T_c = \frac{\omega_{\log}}{1.2}\exp\!\left[-\frac{1.04\,(1+\lambda)}{\lambda - \mu^{*}(1+0.62\lambda)}\right],
$$

where $\omega_{\log}$ is the logarithmic average of the phonon frequency and $\mu^{*}$ is the Coulomb pseudopotential. This makes Elk a practical tool for superconductivity studies in addition to structural and magnetic work.

### Beyond Standard DFT: Many-Body and Advanced Functionals

Elk implements several methods that go beyond the standard Kohn-Sham scheme:

- **Hybrid and exact-exchange methods.** Hartree-Fock and the optimised effective potential (OEP) method are available, using the Coulomb matrix elements described above.
- **Reduced density matrix functional theory (RDMFT).** Elk implements RDMFT for solids. The developers devised and parameterised a simple functional by fitting to the exchange-correlation energy of the electron gas. Elk is described as the only solid-state code able to compute ground-state properties and spectra using one-body reduced density matrix functional theory.
- **GW approximation.** An all-electron implementation of the GW approximation is included, along with extensions that incorporate higher-order terms in the Hedin equations.
- **Bethe-Salpeter equation (BSE).** Optical and excitonic spectra can be obtained by solving the BSE, using the exchange-correlation kernels that are available in the code.

### Time-Dependent and Excited-State Methods

Elk offers linear-response time-dependent density functional theory (TDDFT), which gives access to optical excitation spectra and related response properties. The project literature also describes the code as capable of real-time propagation for extended systems and of real-time dynamics of spin and charge under strong electromagnetic fields, a combination that the developers identify as distinctive among solid-state codes. Time-dependent output such as excited charge and magnetisation densities is written over the full Brillouin zone.

## Parallelism and Performance

Three forms of parallelism are implemented, and they can be combined. Elk uses OpenMP for shared-memory threading, which is heavily nested in many parts of the code, and MPI for distributing work across nodes. The code also distributes independent tasks such as different k-points, which are computed in parallel. The optimal configuration depends on the crystal structure, the computer architecture and the task. Documentation from high-performance computing centres reports that Elk can run efficiently with a small number of MPI processes per node and many OpenMP threads, and that on NUMA architectures, using moderate thread counts per process can yield better performance than using all cores in a single process. As a practical rule, the number of MPI tasks is often chosen to match or divide the number of k-points, since k-points are the natural unit of parallel work.

## Software Engineering and Design Philosophy

Elk is written in Fortran, and the developers state that it complies strictly with the Fortran 2008 standard and uses only one language, which simplifies building and maintaining the code. Its input is a free-form file named elk.in, organised into blocks of parameters, and its output files are written in uppercase with the extension .OUT. Every subroutine includes full LaTeX documentation, and the manual accompanies the code, which is part of why the developers regard it as accessible to both users and developers.

Pseudo-atomic data for each element are supplied as species files, which by default are read from a species directory installed with the code. Users can override this and point to custom species files through the sppath setting in elk.in. Elk can be compiled with OpenMP and MPI enabled, and the build system supports optional OpenMP-specific optimisations and the libxc library.

The developers' overarching philosophy is that the code should be easy to extend. Because of its modular, single-language structure and comprehensive internal documentation, new theoretical ideas can be implemented and tested with relatively little overhead. This is the reason the Max Planck group describes Elk as a testbed for new methods, and it is also why several research groups have used it as a platform for their own developments.

## Licensing and Availability

Elk is freely available under the GNU General Public License. Source code, the manual, and downloads are distributed through the project's SourceForge site (elk.sourceforge.io), and the code is widely packaged for Linux distributions, the BSD family and academic software environments. Many university and national computing centres also provide the code as installed modules. Because the code is open, its results can be checked independently, which the developers explicitly encourage.

## Where Elk Fits Among Electronic-Structure Codes

Elk occupies a specific niche. Pseudopotential plane-wave codes are generally faster for large, routine calculations of total energies and structures, and they are the standard choice for high-throughput screening. Elk is aimed at problems where all-electron accuracy matters, where core-region information is needed, or where the user wants access to methods that are less common in mainstream codes, such as RDMFT, real-time TDDFT for extended systems, or a fully integrated set of electron-phonon tools. Its strength is breadth of method combined with precision, not raw throughput on very large systems.

Users should be aware that a code of this depth has a learning curve, and that results from any DFT code depend on the choice of functional, basis convergence, k-point sampling and the treatment of semicore states. The Elk documentation and the developers' own guidance recommend checking results against known benchmarks for the system under study before relying on them for production work.

## Summary

Elk is a mature, GPL-licensed, all-electron FP-LAPW code developed over roughly fifteen years, with its main development now at the Max Planck Institute of Microstructure Physics in Halle. Its defining characteristics are a flexible LAPW basis with arbitrary local orbitals and matching orders, full symmetry treatment, a broad set of ground-state, lattice-dynamics and superconductivity tools, an unusually wide suite of beyond-DFT methods including RDMFT, GW, BSE, OEP and TDDFT, and a developer-friendly, single-language Fortran 2008 design. It is best suited to research where all-electron accuracy and methodological breadth are more important than high-throughput speed.
