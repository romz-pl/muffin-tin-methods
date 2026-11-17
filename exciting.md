# The exciting FP-LAPW Code

## 1. What exciting is

[exciting](https://github.com/exciting/exciting) is an open-source, all-electron, full-potential electronic structure package for first-principles calculations based on the (linearized) augmented plane-wave plus local-orbital, (L)APW+lo, method. It implements density-functional theory (DFT) in its Kohn-Sham form and a set of many-body and excited-state methods built on top of the ground state. The code is distributed under the GNU General Public License (GPL), is developed by an international academic team (the "exciting team") and is written mainly in Fortran.

The project grew out of the EXCITING EU Research and Training Network, in which Dewhurst, Sharma, Ambrosch-Draxl and collaborators in Graz were central. Its sibling code Elk, written at Karl-Franzens-Universität Graz as a deliberately simplified implementation of the same method, shares much of the same heritage. exciting is the larger, more feature-rich of the two and is aimed at production work on advanced electronic properties.

Release names have followed chemical elements. The first official release was called *hydrogen*, followed by *beryllium* (installed on several HPC systems in 2014) and later *neon*, whose documentation carries copyright notices from 2002 to 2023. Because the code is actively developed, the current release and its feature list should always be checked on the official site, exciting-code.org.

## 2. The method: (L)APW+lo

### 2.1 Partitioning the unit cell

The LAPW approach divides the crystal into two regions. Around each atomic nucleus lies a non-overlapping muffin-tin (MT) sphere of radius $R_{\mathrm{MT}}$. The remaining volume is the interstitial region. Wavefunctions are expanded differently in each region, and the expansions are matched continuously at the MT boundary.

### 2.2 Basis functions

Inside an MT sphere the basis consists of radial functions multiplied by spherical harmonics,

$$
\psi_{\mathbf{k}+\mathbf{G}}(\mathbf{r}) = \sum_{lm} \left[ A_{lm}^{\alpha}(\mathbf{k}+\mathbf{G})\, u_{l}^{\alpha}(r, E_{l}) + B_{lm}^{\alpha}(\mathbf{k}+\mathbf{G})\, \dot{u}_{l}^{\alpha}(r, E_{l}) \right] Y_{lm}(\hat{\mathbf{r}}),
$$

where $u_l$ is the solution of the radial scalar-relativistic Schrödinger-like equation at a fixed linearization energy $E_l$, and $\dot u_l$ is its energy derivative. The linearization

$$
u_{l}(r,E) \approx u_{l}(r,E_{l}) + (E - E_{l})\,\dot{u}_{l}(r,E_{l})
$$

is what gives the method its name and makes the problem tractable with a single diagonalization per k-point.

In the interstitial region the basis is a set of plane waves,

$$
\phi_{\mathbf{k}+\mathbf{G}}(\mathbf{r}) = \frac{1}{\sqrt{\Omega}}\, e^{i(\mathbf{k}+\mathbf{G})\cdot\mathbf{r}},
$$

with $\Omega$ the cell volume. The plane-wave cutoff is controlled by the product $R_{\mathrm{MT}}^{\min} K_{\max}$ (the parameter `rgkmax` in the input), which is the single most important convergence parameter for basis-set completeness.

### 2.3 Local orbitals

Standard LAPW can struggle when a state lies too far from the linearization energy, which is typical of semicore states such as 3s and 3p in transition metals or 4f states in lanthanides. Local orbitals (lo) fix this. A local orbital is a radial function confined inside one MT sphere, vanishing at the boundary, and built from a combination of a state at one energy and its derivative, or at two different energies. Adding local orbitals makes it possible to treat semicore states variationally in the same calculation as valence states, and also to extend the basis systematically. This is the "+lo" part of (L)APW+lo.

### 2.4 Why all-electron matters

Because the wavefunctions are represented explicitly in the core region, exciting does not require pseudopotentials. Core states are present explicitly and treated with a fully radial solution, which means that:

- the code applies equally well to any element in the periodic table, including heavy elements where relativistic effects dominate;
- properties that depend on the core, such as hyperfine fields, isomer shifts, electric field gradients, and core-level spectroscopy, are computed without approximating the nuclear region;
- there is no transferability error inherent in a pseudopotential.

Core electrons are treated fully relativistically, while valence electrons are handled with a scalar-relativistic approximation, with spin-orbit coupling included as an option where needed.

## 3. The potential and the Kohn-Sham problem

"Full-potential" means the effective potential and charge density are not restricted to a spherical or otherwise simplified shape. The density and potential are expanded in lattice harmonics inside the MT spheres and in Fourier series in the interstitial, and the Hartree potential is obtained by solving Poisson's equation in both regions. The exchange-correlation potential is evaluated on a real-space grid.

The Kohn-Sham equations take the generalized eigenvalue form

$$
\hat{H}(\mathbf{k})\, \mathbf{c}_{n\mathbf{k}} = \varepsilon_{n\mathbf{k}}\, \hat{O}(\mathbf{k})\, \mathbf{c}_{n\mathbf{k}},
$$

where $\hat{H}$ and $\hat{O}$ are the Hamiltonian and overlap matrices in the LAPW+lo basis. The self-consistent field cycle iterates the density until the total energy and charge density converge. Brillouin-zone integration uses tetrahedron or Gaussian-type smearing schemes on Monkhorst-Pack or user-defined k-meshes, with symmetry reduction exploiting the crystal's space group.

## 4. Feature set

### 4.1 Ground-state DFT

exciting supports a range of exchange-correlation functionals through the Libxc library, including local density approximation (LDA) and generalized gradient approximation (GGA) forms such as PBE and PBEsol, along with meta-GGA and hybrid functional capabilities depending on the release. Spin-polarized calculations are supported in the most general way, and noncollinear magnetism with spin-orbit coupling is available. External magnetic fields and constraints can be imposed.

Structural optimization is supported through calculation of Hellmann-Feynman forces and stresses, allowing relaxation of atomic positions and lattice parameters. Phonon and linear-response capabilities are available in the code base and in related development branches; the exact status should be checked against the release documentation.

### 4.2 Excited states and spectroscopy

This is where exciting distinguishes itself from many DFT codes. Its ground-state machinery is extended to:

- **Time-dependent DFT (TDDFT)**, including an all-electron real-time formulation, which allows the simulation of electron dynamics and optical response beyond linear response;
- **The Bethe-Salpeter equation (BSE)** for both valence excitations and core excitations, enabling calculation of optical absorption spectra with excitonic effects and X-ray absorption spectra (XAS) at core edges;
- **GW-based quasiparticle corrections** for band gaps and band structures beyond DFT;
- **Dielectric functions, loss functions and optical constants** computed from the linear response of the system;
- **Core-level spectroscopy** such as X-ray emission and X-ray absorption, where the all-electron nature of the method is a decisive advantage.

Because the core is treated explicitly, core-excitation spectra are natural to compute, which is hard for pseudopotential codes.

### 4.3 Properties and post-processing

Typical outputs include band structures, densities of states (total and projected onto MT spheres and angular momenta), charge densities and potentials, magnetic moments, electric field gradients and hyperfine parameters, and Wannier-function interfaces for downstream tight-binding work.

## 5. Inputs, outputs and workflow

exciting uses an XML-based input file, conventionally `input.xml`. The structure of the input is organized into blocks: the crystal structure (lattice vectors, species, atomic positions, optionally from a CIF-like conversion), the ground-state block (XC functional, k-mesh, smearing, convergence thresholds, basis parameters), and property blocks for the excited-state calculations. The XML schema is documented in the input reference, and the project maintains a template market with worked examples for common tasks.

Output is written as a mixture of XML and plain-text files that record the total energy, eigenvalues, forces, and convergence history. Because the XML is structured, exciting outputs are amenable to scripting. The ecosystem includes Python tools such as `excitingtools` and `excitingscripts` for generating inputs, parsing outputs, and running workflows.

A typical ground-state workflow is:

1. Build the structure and choose MT radii that do not overlap.
2. Choose `rgkmax` and check its convergence against total energy and forces.
3. Choose the k-mesh and smearing, and check convergence with respect to both.
4. Run the self-consistent calculation.
5. Perform a property or excited-state calculation using the converged density.

## 6. Computational aspects

exciting is parallelized with MPI for distributed memory, OpenMP for shared memory, and hybrid MPI+OpenMP for large systems on HPC clusters. Dense linear algebra relies on BLAS, LAPACK and ScaLAPACK. Community forks have added GPU acceleration: for example, exciting-plus-gpu, a fork of the Exciting-Plus branch, enables computation on GPUs through CUDA and/or OpenACC.

The computational cost of all-electron LAPW is significantly higher than that of plane-wave pseudopotential codes for the same system, because the basis must describe the rapidly varying core region and because the matrices are larger. The cost scales roughly as the cube of the number of basis functions for diagonalization, which for heavy elements can make large systems expensive. In return, the method delivers reference-quality results against which pseudopotential and other approximate methods can be benchmarked.

## 7. Strengths and limitations

**Strengths**

- Reference-quality all-electron accuracy across the periodic table.
- Natural treatment of core states and core-level spectroscopy.
- A broad and integrated suite of excited-state methods, including TDDFT, BSE, and GW, within one code.
- Open-source (GPL) with transparent source and active community development.
- Clear, documented XML input and a growing set of scripting tools.

**Limitations**

- High computational cost relative to pseudopotential plane-wave codes for large or heavy-element systems.
- Results depend on basis-set parameters (`rgkmax`, MT radii, `lmaxapw`) that must be converged carefully. Users who skip these checks can obtain misleading numbers.
- The learning curve is steeper than for simpler codes, particularly for excited-state workflows.
- As with any actively developed code, individual features vary in maturity across releases. The developers advise checking the consistency of production results carefully, and this advice is worth following.

## 8. Relation to other all-electron codes

exciting belongs to the family of LAPW codes that includes WIEN2k, FLEUR, Elk and others. WIEN2k is a commercial-licensed code with a long track record and a large user base; FLEUR is a noted all-electron code with particular strengths in magnetism and thin films; Elk shares the exciting lineage but is more minimal. exciting's distinguishing characteristics are its open license, its integrated excited-state spectroscopy suite, and its strong emphasis on core-level and optical properties.

## 9. Practical advice for new users

- Start with the tutorials and template examples before attempting excited-state calculations.
- Always converge `rgkmax` and the k-mesh for the property of interest, not only for total energy.
- Check MT radii for overlap, since overlapping spheres cause numerical problems.
- For heavy elements, consider the role of spin-orbit coupling and verify the relativistic treatment.
- Keep input and output XML under version control, and script repeated calculations rather than editing files by hand.

## 10. Key references

- A. Gulans, S. Kontur, C. Meisenbichler, D. Nabok, P. Pavone, S. Rigamonti, S. Sagmeister, U. Werner, C. Draxl, "exciting: a full-potential all-electron package implementing density-functional theory and many-body perturbation theory," *J. Phys.: Condens. Matter* **26**, 363202 (2014).
- C. Meisenbichler et al., and co-workers on all-electron real-time TDDFT, "All-electron full-potential implementation of real-time TDDFT in exciting," *Electron. Struct.* **3**, 037001 (2021).
- The exciting project website and documentation: http://exciting-code.org

## 11. Summary

exciting is a full-featured, open-source, all-electron DFT package built on the (L)APW+lo method. Its defining traits are the absence of pseudopotentials, a full-potential treatment of the density and potential, a systematic basis with local orbitals for semicore states, and an unusually integrated suite of excited-state and spectroscopic methods. It is best suited to users who need reference-quality results, especially for core-level spectroscopy, optical response, or heavy-element systems, and who are prepared to invest in careful convergence and a relatively demanding computational budget.
