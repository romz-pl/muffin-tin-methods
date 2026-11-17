# FLEUR

[FLEUR (the Full-potential Linearized augmented plane-wave code)](https://www.flapw.de) is an open-source electronic-structure code for density functional theory (DFT) calculations in solids, surfaces, thin films, and molecules. It is developed mainly at Forschungszentrum Jülich, with contributions from RWTH Aachen and other academic groups, and is part of the JuDFT (Jülich DFT) project. It implements the FLAPW method, which descends from the work of Wimmer, Krakauer, Weinert, and Freeman in 1981. It is written in Fortran and parallelized with MPI and OpenMP.

## The Underlying Method

FLEUR is an **all-electron, full-potential** code. Each atom has a muffin-tin sphere, where the wavefunctions are expanded in radial solutions of the Schrödinger (or Dirac) equation multiplied by spherical harmonics. Between the spheres, the wavefunctions are expanded in plane waves. The potential and charge density are not approximated by a spherical or atomic-sphere shape, so no pseudopotentials are needed. Core electrons are treated explicitly, and relativistic effects are included: scalar-relativistic corrections as standard, with spin-orbit coupling added through the second-variation method.

The practical consequence is high accuracy for properties that depend on the full electron density near nuclei, such as hyperfine fields, magnetic moments, core-level shifts, and the electric field gradient. Pseudopotential plane-wave codes often struggle with these properties.

The cost is computational. All-electron methods are heavier than plane-wave pseudopotential codes like Quantum ESPRESSO or VASP for the same system size, so FLEUR is typically used where accuracy on specific quantities justifies the expense.

## Input and Workflow

FLEUR's input is driven by an XML file, `inp.xml`, which validates against a published schema. This makes input files self-describing and machine-readable, which is useful for automation and for tools that generate or parse inputs. A typical workflow has three stages:

1. **Structure generation** with `inpgen`, which takes a crystal structure, lattice parameters, and atomic positions and produces the muffin-tin radii, basis-set parameters, and an initial `inp.xml`.
2. **Self-consistent field calculation** with the main `fleur` executable, which iterates the density and potential to convergence, with mixing schemes (Broyden-type) and convergence criteria the user sets.
3. **Post-processing** with auxiliary tools for band structures, density of states, charge densities, equation-of-state fits, and other analysis.

Key convergence parameters include the plane-wave cutoff (`Rkmax`), the k-point mesh, the muffin-tin radii, and the number of spin-density iterations. These need careful testing for each system, as with any DFT code.

## Physical Capabilities

FLEUR covers a broad range of properties and methods:

- **Exchange-correlation functionals**: LDA and GGA families, including PBE, with extensions for hybrid-type approaches under development.
- **Magnetism**: collinear and noncollinear magnetism, spin spirals, magnetic anisotropy energy, and Dzyaloshinskii–Moriya interaction (DMI), which is important for skyrmions and chiral magnets.
- **Spin-orbit coupling** in the second-variation formalism, relevant for topological materials, Rashba systems, and heavy-element compounds.
- **Strongly correlated electrons**: DFT+U corrections for localized d and f states.
- **Exchange interactions**: calculation of Heisenberg exchange constants J_ij for spin-model parameterization.
- **Structural relaxation**: Hellmann–Feynman forces and total-energy minimization for geometry optimization.
- **Electronic and spectroscopic properties**: band structures, densities of states, Fermi surfaces, work functions, and Berry-phase quantities.
- **Hyperfine and core-level properties**: hyperfine fields and other quantities that benefit from all-electron treatment.
- **Interfaces to other methods**: Green's-function-based embedding and transport, Wannier-function interfaces, and a companion code (SPEX) for GW quasiparticle calculations.

## Parallelization and Performance

FLEUR parallelizes over k-points and bands with MPI, and uses OpenMP within nodes. Good scaling depends on system size and on how the k-point mesh and band distribution are set up. Large calculations typically run on HPC clusters. Memory use and runtime grow steeply with the number of atoms and the basis size, so the computational budget often dictates the system size that can be studied.

## Ecosystem and Tooling

FLEUR has a supporting ecosystem:

- **AiiDA-FLEUR**, a plugin for the AiiDA workflow and provenance framework. It supports automated, reproducible workflows such as convergence tests, relaxations, and high-throughput screening.
- **masci-tools**, a Python library for parsing FLEUR inputs and outputs and for building custom analysis scripts.
- **Documentation and tutorials**, with examples covering standard tasks, and a test suite that helps verify installations and regressions.
- **Open-source distribution**, with the code hosted on GitHub under the JuDFT organization. Check the license terms in the repository before use.

## Where FLEUR Fits

FLEUR is one of several all-electron LAPW codes, alongside WIEN2k (commercial and widely used), Elk (open source, in Fortran), and exciting. Its distinguishing features are the XML-based input, a strong focus on noncollinear magnetism and spin-related properties, and the surrounding Jülich ecosystem for workflows and spectroscopy.

Its typical users are researchers in condensed-matter physics, materials science, and spintronics who need all-electron accuracy for magnetic, relativistic, or core-sensitive properties. It is less suited to routine large-scale screening, where pseudopotential plane-wave codes are usually faster, and it has a steeper learning curve than many modern codes with graphical front ends.

## Caveats

My knowledge of FLEUR's current release, feature set, and repository details may be out of date, and development is ongoing. For the latest version, supported features, and installation instructions, check the official site (flapw.de) and the JuDFT GitHub repositories. I can search for current release information if you want me to verify specific details.
