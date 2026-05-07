# Hard-Crunchies

## High-Precision Recognition via Partial Edge Contrast & Pattern Interpolation

A research repository exploring the mathematics and computational methods for identifying objects at range when full image fidelity is unavailable — accounting for atmospheric turbulence, wavelength-dependent scattering, thermal gradients, shading, and partial edge information.

---

## Repository Structure

```
Hard-Crunchies/
├── README.md                          # This file
├── MATH_RESEARCH.md                   # Deep mathematical framework & derivations
├── visualizer/
│   └── index.html                     # Interactive browser-based visualization tool
└── notes/
    └── references.md                  # Key papers and theoretical foundations
    ```

    ---

    ## Core Problem

    When imaging objects at long range, several physical degradation sources compound:

    - **Atmospheric turbulence** (Kolmogorov/von Kármán spectrum) blurs and warps edges
    - **Wavelength-dependent scattering** (Rayleigh, Mie) attenuates contrast differently across the spectrum
    - **Thermal shimmer** introduces spatially-varying phase distortion
    - **Partial occlusion / shading** hides portions of the target silhouette
    - **Sensor noise** (shot, read, thermal) further degrades edge clarity

    The question: *given partial, degraded edge fragments, what is the object, and how certain are we?*

    ---

    ## Approach Overview

    ### 1. Partial Edge Contrast Recovery
    Using structure tensor analysis and anisotropic diffusion to extract reliable edge fragments from degraded imagery.

    ### 2. Atmospheric Transfer Functions
    Modeling the Modulation Transfer Function (MTF) degradation from turbulence using the Fried parameter *r₀* and deriving what information survives.

    ### 3. Bayesian Pattern Completion
    Probabilistic interpolation between known edge fragments using learned shape priors — treating missing edge segments as latent variables.

    ### 4. Multi-Scale Hypothesis Scoring
    Scoring candidate object templates against recovered edge fragments across wavelength bands and shading models.

    ---

    ## Quick Start: Visualization

    Open `visualizer/index.html` in any modern browser. No server required.

    The interactive tool lets you:
    - Simulate atmospheric degradation at configurable range/turbulence/wavelength
    - See raw vs. recovered edge maps
    - Watch pattern-completion hypothesis scoring in real time
    - Adjust Fried parameter, scintillation index, and thermal gradient

    ---

    ## Mathematical Foundation

    See `MATH_RESEARCH.md` for full derivations covering:
    - Kolmogorov turbulence structure functions
    - Fried coherence length and MTF rolloff
    - Anisotropic structure tensor edge detection
    - Bayesian shape prior formulation
    - Wavelength attenuation (Beer-Lambert, Rayleigh/Mie)
    - Cramer-Rao bound on range estimation

    ---

    *Repository: Hard-Crunchies | Focus: Degraded-image recognition mathematics*
