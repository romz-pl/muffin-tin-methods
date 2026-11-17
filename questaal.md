# Questaal

[**Questaal**](https://www.questaal.org/) is an open-source suite of electronic structure programs for computing the properties of solids from first principles. It is built around the **linear muffin-tin orbital (LMTO)** method, an all-electron approach descended from the Slater tradition rather than the pseudopotential approach used by many popular codes.

## Origin and licensing

The suite was developed mainly by Mark van Schilfgaarde and collaborators at King's College London, with contributors including Dimitar Pashov, Swagata Acharya, Walter R. L. Lambrecht, Jerome Jackson, Kirill D. Belashchenko, Athanasios Chantis, and Francois Jamet. The main reference is a 2020 paper in [Computer Physics Communications 249 (2020) 107065](http://dx.doi.org/10.1016/j.cpc.2019.107065) titled "**Questaal: A package of electronic structure methods based on the linear muffin-tin orbital technique**." The code is released under the GNU General Public License, version 3. Its implementation is mostly Fortran, with some C, Python, and shell scripting.

## The underlying method

Questaal's basis set consists of atom-centred functions rather than the plane waves or augmented plane waves used by many codes. The package extends the traditional "second generation" LMTO formalism with a short-range, tight-binding-like basis for full-potential calculations, and it uses an augmentation scheme that resembles PAW but can, in principle, be converged to the exact solution for the reference potential. The documentation also presents the method in both Green's function and wave-function formulations, which is useful for bulk, layered, and interface systems.

## Components

The suite is made up of several semi-independent parts:

- **Full-potential LMTO code (`lmf`)**: the flagship program. It handles density functional theory (LDA and GGA, including PBE via LibXC), LDA+U, spin-orbit coupling, non-collinear magnetism, and fully relativistic calculations. It also supports combined LMTO+APW treatments and local orbitals or added plane waves for difficult cases.
- **Atomic sphere approximation code**: a more traditional ASA implementation, useful for fast, approximate band structures and for generating basis parameters.
- **Tight-binding modules**: tools for empirical tight-binding models derived from the LMTO Hamiltonian.
- **Quasiparticle self-consistent GW (QSGW)**: the GW implementation is a major part of the package. QSGW is a self-consistent quasiparticle approach that gives substantially better band gaps than standard DFT, and Questaal is designed to support it alongside DFT.
- **Green's function tools**: functionality for surfaces, interfaces, and transport, and for coherent potential approximation (CPA) alloy theory.
- **DMFT interface**: Questaal can be coupled to different dynamical mean-field theory solvers, enabling treatments of strongly correlated materials that combine QSGW or LDA with DMFT.
- **Susceptibility calculations**: spin and charge response functions.
- **Utilities**: tools for checking input, generating basis sets, plotting band structures and densities, and analysing results.

The authors have described the code as suited to a wide range of material classes, including those with varying degrees of electronic correlation, so it is applicable to both simple semiconductors and transition-metal or f-electron systems.

## Input and workflow

Calculations are controlled by a "ctrl" file that uses a keyword-based format, with sections such as `HAM` for Hamiltonian options, `BZ` for Brillouin-zone sampling, and `ITER` for self-consistency settings. A typical workflow starts with the atomic calculation and basis generation, then runs `lmf` to reach self-consistency, followed by post-processing steps such as band plotting. The official tutorials walk through examples like SrTiO₃, covering basis setup, running the code, plotting LDA band structures, and adding local orbitals or plane waves.

## Strengths and limitations

Questaal's main strengths are its precision in an all-electron framework, its efficient atom-centred basis, its integrated GW and DMFT capabilities, and its open-source availability. Its main drawbacks are a steeper learning curve than mainstream codes such as VASP or Quantum ESPRESSO, a smaller user community, and documentation that is extensive but scattered across tutorials, manuals, and the paper. Users who want out-of-the-box plane-wave workflows or large community support may find other tools more convenient, but Questaal is attractive for researchers who need all-electron accuracy, LMTO-based insight, or self-consistent GW.

## Where to learn more

The project website at questaal.org hosts the documentation, tutorials, and downloads. The 2020 *Computer Physics Communications* paper gives the most complete technical description. I should note that I put this together from a search of published sources plus general background, so specific version numbers, repository locations, and feature details may have changed since then. The project's current documentation is the best place to confirm them before you rely on any of them.
