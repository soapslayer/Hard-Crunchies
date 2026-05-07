# References & Theoretical Foundations

## Hard-Crunchies Research Notes

---

## Atmospheric Optics & Turbulence

### Kolmogorov Turbulence Theory
- Kolmogorov, A.N. (1941). "The local structure of turbulence in incompressible viscous fluid for very large Reynolds numbers."
  - Original formulation of the 2/3 power law structure function
  - D_n(r) = C_n^2 * r^(2/3)

- Tatarski, V.I. (1961). "Wave Propagation in a Turbulent Medium." McGraw-Hill.
  - Extension to optical wave propagation through turbulent atmosphere

### Fried Parameter
- Fried, D.L. (1966). "Optical resolution through a randomly inhomogeneous medium for very long and very short exposures."
  J. Opt. Soc. Am., 56(10), 1372-1379.
  - Introduced r_0 (Fried parameter / coherence length)
  - r_0 = 0.185 * (lambda^2 / integral C_n^2 dz)^(3/5)

- Fried, D.L. (1965). "Statistics of a Geometric Representation of Wavefront Distortion."
  J. Opt. Soc. Am., 55, 1427.

### MTF and Long-Exposure Imaging
- Hufnagel, R.E. & Stanley, N.R. (1964). "Modulation Transfer Function Associated with Image Transmission through Turbulent Media."
  J. Opt. Soc. Am., 54, 52-61.
  - MTF_turb(f) = exp(-3.44*(lambda*f*R/r_0)^(5/3)) for long exposure

- Roggemann, M.C. & Welsh, B. (1996). "Imaging Through Turbulence." CRC Press.

---

## Edge Detection & Structure Tensor

### Forstner/Harris Operator
- Forstner, W. & Gulch, E. (1987). "A fast operator for detection and precise location of distinct points, corners and centres of circular features."
  ISPRS Intercommission Conference on Fast Processing of Photogrammetric Data.

- Harris, C. & Stephens, M. (1988). "A combined corner and edge detector."
  Proc. 4th Alvey Vision Conference, 147-151.

### Structure Tensor for Orientation
- Bigun, J. & Granlund, G. (1987). "Optimal orientation detection of linear symmetry."
  Proc. IEEE First International Conference on Computer Vision, 433-438.

- Weickert, J. (1999). "Coherence-Enhancing Diffusion Filtering."
  International Journal of Computer Vision, 31(2-3), 111-127.
  - Defines the CED approach: diffusion tensor aligned with local image structure

### Scale-Space Theory
- Lindeberg, T. (1994). "Scale-Space Theory in Computer Vision." Kluwer Academic Publishers.
  - Gaussian scale-space, multi-scale edge detection
  - Normalized derivatives for scale-invariant feature detection

---

## Anisotropic Diffusion

### Perona-Malik Diffusion
- Perona, P. & Malik, J. (1990). "Scale-space and edge detection using anisotropic diffusion."
  IEEE Trans. Pattern Analysis and Machine Intelligence, 12(7), 629-639.
  - dI/dt = div(c(|grad I|) * grad I)
  - Exponential and rational diffusion coefficient functions

### Coherence-Enhancing Diffusion
- Weickert, J. (1997). "A Review of Nonlinear Diffusion Filtering."
  Scale-Space Theory in Computer Vision, LNCS 1252, 3-28.

---

## Bayesian Pattern Completion & Partial Matching

### Elastica / Euler Spiral Completion
- Horn, B.K.P. (1983). "The curve of least energy." ACM Trans. Math. Software, 9(4), 441-460.

- Mumford, D. (1994). "Elastica and computer vision."
  Algebraic Geometry and its Applications, 491-506.
  - Euler-Bernoulli elastica minimization: integral(kappa^2 + lambda)ds

- Kimia, B., Frankel, I. & Popescu, A. (2003). "Euler Spiral for Shape Completion."
  International Journal of Computer Vision, 54(1-3), 159-182.

### Shape Prior Models
- Cootes, T.F., Taylor, C.J., Cooper, D.H. & Graham, J. (1995). "Active Shape Models - Their Training and Application."
  Computer Vision and Image Understanding, 61(1), 38-59.

- Blanz, V. & Vetter, T. (1999). "A Morphable Model for the Synthesis of 3D Faces."
  Proc. SIGGRAPH 1999, 187-194.

### Chamfer Distance
- Barrow, H.G., Tenenbaum, J.M., Bolles, R.C. & Wolf, H.C. (1977).
  "Parametric Correspondence and Chamfer Matching: Two New Techniques for Image Matching."
  Proc. IJCAI, 659-663.

---

## Wavelength Attenuation

### Rayleigh Scattering
- Rayleigh, Lord (1871). "On the scattering of light by small particles."
  Philosophical Magazine, 41, 447-454.
  - beta ~ lambda^(-4) for molecular scattering

### Mie Scattering
- Mie, G. (1908). "Beitraege zur Optik trueber Medien, speziell kolloidaler Metalloesungen."
  Annalen der Physik, 25(3), 377-445.

- van de Hulst, H.C. (1957). "Light Scattering by Small Particles." Wiley.

### Angstrom Exponent
- Angstrom, A. (1929). "On the atmospheric transmission of sun radiation and on dust in the air."
  Geografiska Annaler, 11, 156-166.
  - beta_Mie(lambda) = beta_0 * (lambda_0/lambda)^alpha_A

---

## Geometric Hashing & Template Matching

### Geometric Hashing
- Lamdan, Y. & Wolfson, H.J. (1988). "Geometric Hashing: A General and Efficient Model-Based Recognition Scheme."
  Proc. International Conference on Computer Vision, 238-249.

### Hausdorff Distance for Shape Matching
- Huttenlocher, D.P., Klanderman, G.A. & Rucklidge, W.J. (1993).
  "Comparing Images Using the Hausdorff Distance."
  IEEE Trans. Pattern Analysis and Machine Intelligence, 15(9), 850-863.

### Track-Before-Detect
- Barniv, Y. (1985). "Dynamic programming solution for detecting dim moving targets."
  IEEE Trans. Aerospace and Electronic Systems, 21(1), 144-156.

---

## Information Theory & Cramer-Rao Bounds

### Fisher Information and Image Resolution
- Shahram, M. & Milanfar, P. (2004). "Imaging below the diffraction limit: A statistical analysis."
  IEEE Trans. Image Processing, 13(5), 677-689.

- Ram, S., Ward, E.S. & Ober, R.J. (2006). "Beyond Rayleigh's criterion: A resolution measure with application to single-molecule microscopy."
  Proc. National Academy of Sciences, 103(12), 4457-4462.

### Information Theory Applied to Recognition
- Cover, T.M. & Thomas, J.A. (2006). "Elements of Information Theory." 2nd ed. Wiley.

---

## Practical Systems / Review Articles

- Roggemann, M.C., Welsh, B.M. & Hunt, B.R. (1996). "Imaging Through Turbulence." CRC Press.
  - Comprehensive treatment of MTF, Fried parameter, adaptive optics

- Driggers, R.G., Cox, P. & Edwards, T. (1999). "Introduction to Infrared and Electro-Optical Systems." Artech House.
  - System MTF, NEC, noise models

- Goodman, J.W. (2005). "Introduction to Fourier Optics." 3rd ed. Roberts & Company.
  - Fourier treatment of coherent and incoherent imaging, PSF, OTF

- Andrews, L.C. & Phillips, R.L. (1998). "Laser Beam Propagation Through Random Media." SPIE Press.
  - Scintillation theory, Rytov approximation, sigma_I formula

---

## Key Equations Collected

| Name | Equation |
|------|----------|
| Fried r_0 | r_0 = 0.185*(lambda^2/(C_n^2*R))^(3/5) |
| Turbulence MTF | exp(-3.44*(lambda*f*R/r_0)^(5/3)) |
| Scintillation | sigma_I^2 = 1.23*C_n^2*k^(7/6)*R^(11/6) |
| Rayleigh scatter | beta_R = 0.00879*lambda^(-4.09) km^-1 |
| Beer-Lambert | T = exp(-beta*R) |
| Strehl ratio | S = exp(-sigma_phi^2) or (r_0/D)^2 |
| Edge CR bound | delta_x >= sigma_turb/SNR |
| Euler elastica | min integral(kappa^2 + lambda)ds |
| Chamfer score | exp(-alpha*d_C)*P(T_j) |
| Fisher info edge | I(x_0) ~ SNR^2/sigma_turb^2 |

---

*Hard-Crunchies Research Notes | Compiled references for partial edge recognition at range*
