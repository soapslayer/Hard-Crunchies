# MATH_RESEARCH.md
# Mathematical Framework: High-Precision Recognition via Partial Edge Contrast

## Hard-Crunchies Research Series

---

## Table of Contents

1. Problem Formulation
2. Atmospheric Propagation Mathematics
3. Modulation Transfer Function (MTF) Framework
4. Partial Edge Detection: Structure Tensor Theory
5. Anisotropic Diffusion for Edge Recovery
6. Bayesian Pattern Completion
7. Wavelength-Dependent Attenuation
8. Thermal & Turbulence Effects
9. Multi-Hypothesis Scoring
10. Cramer-Rao Bounds on Identification Confidence
11. Algorithm Summary

---

## 1. Problem Formulation

### 1.1 The Degraded Observation Model

An observed image I_obs at range R is related to the true scene radiance I_true by:

    I_obs(x,y) = [I_true * h_atm * h_sensor](x,y) + n(x,y)

Where:
- h_atm = atmospheric point spread function (turbulence + scattering)
- h_sensor = sensor optics PSF
- n(x,y) = additive noise (Gaussian + Poisson shot noise)
- * = 2D convolution

The goal: given I_obs, extract sufficient edge information to classify the target object.

### 1.2 Information Survival Condition

Not all spatial frequencies survive propagation. The cutoff frequency f_c in cycles/meter at the image plane is:

    f_c = r_0 / (lambda * R)

Where:
- r_0 = Fried coherence length (meters)
- lambda = wavelength (meters)
- R = range (meters)

Features smaller than 1/f_c are destroyed by turbulence. Edge recovery operates only on surviving frequencies.

### 1.3 Signal-to-Noise Ratio at Range

The photon-limited SNR for a contrast feature C at range R:

    SNR(R) = C * sqrt(N_photons) / (1 + sigma_speckle)

Where N_photons = (L * A_lens * tau * eta) / (4 * F^2 * R^2)

- L = scene radiance (W/sr/m^2)
- A_lens = aperture area
- tau = integration time
- eta = quantum efficiency
- F = f-number

At long range, SNR ~ 1/R^2, making partial edge recovery essential.

---

## 2. Atmospheric Propagation Mathematics

### 2.1 Kolmogorov Turbulence Structure Function

The atmospheric refractive index structure function D_n(r) describes how turbulence varies with separation r:

    D_n(r) = C_n^2 * r^(2/3)    for l_0 << r << L_0

Where:
- C_n^2 = refractive index structure parameter (m^(-2/3)), typically 10^-17 to 10^-12
- l_0 = inner scale (~1 mm near ground)
- L_0 = outer scale (~10-100 m)

### 2.2 Fried Coherence Length r_0

The Fried parameter r_0 defines the aperture size at which turbulence becomes significant:

    r_0 = 0.185 * (lambda^2 / int_0^R C_n^2(z) dz)^(3/5)

For a horizontal path with uniform C_n^2:

    r_0 = 0.185 * (lambda^2 / (C_n^2 * R))^(3/5)

Typical values:
- Excellent seeing: r_0 = 20 cm
- Moderate: r_0 = 5 cm
- Poor: r_0 = 1 cm

Key insight: as R increases, r_0 decreases as R^(-3/5), rapidly blurring edges.

### 2.3 Isoplanatic Angle

The angle theta_0 over which turbulence is correlated:

    theta_0 = 0.314 * (r_0 / R)

Features separated by more than theta_0 experience independent blur realizations.

### 2.4 Scintillation Index

The normalized variance of intensity fluctuations:

    sigma_I^2 = 1.23 * C_n^2 * k^(7/6) * R^(11/6)

Where k = 2*pi/lambda is the wavenumber.

For sigma_I^2 > 1 (strong scintillation), intensity dropouts cause edge fragmentation.

---

## 3. Modulation Transfer Function (MTF) Framework

### 3.1 Long-Exposure Atmospheric MTF

The ensemble-averaged MTF due to turbulence (long exposure):

    MTF_turb(f) = exp(-3.44 * (lambda * f * R / r_0)^(5/3))

Where f is spatial frequency in cycles/meter at the object plane.

This is the critical equation: it gives the fraction of edge contrast surviving at spatial frequency f.

### 3.2 Combined System MTF

Total system MTF is the product of component MTFs:

    MTF_total(f) = MTF_optics(f) * MTF_turb(f) * MTF_sensor(f) * MTF_motion(f)

Where:
- MTF_optics(f) = 2/pi * (arccos(f/f_cutoff) - (f/f_cutoff)*sqrt(1-(f/f_cutoff)^2))  [diffraction-limited]
- MTF_sensor(f) = sinc(f * p)  [pixel pitch p]
- MTF_motion(f) = sinc(f * v * tau)  [image motion v during integration tau]

### 3.3 Noise Equivalent Contrast (NEC)

The minimum detectable contrast given noise:

    NEC = sigma_noise / (MTF_total(f) * mean_signal)

Edge fragments with local contrast < NEC are undetectable.

### 3.4 Recoverable Edge Band

Define the recoverable band [f_min, f_max] where:

    f_min = 1 / (object_angular_size * R)   [gross shape]
    f_max = r_0 / (lambda * R)              [turbulence cutoff]

Partial edge recovery works within this band.

---

## 4. Partial Edge Detection: Structure Tensor Theory

### 4.1 The Structure Tensor

For image I(x,y), the structure tensor J at scale sigma is:

    J = G_sigma * [[I_x^2,  I_x*I_y],
                   [I_x*I_y, I_y^2 ]]

Where:
- I_x, I_y = partial derivatives of I
- G_sigma = Gaussian smoothing kernel of width sigma

The tensor encodes local orientation and edge strength.

### 4.2 Eigenvalue Analysis

Eigenvalues lambda_1 >= lambda_2 of J:

    lambda_1,2 = ((I_x^2 + I_y^2) +/- sqrt((I_x^2 - I_y^2)^2 + 4*I_x^2*I_y^2)) / 2

Edge measures:
- Edge strength: sqrt(lambda_1)
- Edge coherence: (lambda_1 - lambda_2) / (lambda_1 + lambda_2)
- Edge orientation: theta = 0.5 * atan2(2*I_x*I_y, I_x^2 - I_y^2)

### 4.3 Scale-Space Edge Detection

Edges are detected across scales sigma_k = sigma_0 * 2^(k/s):

    E(x, y, sigma) = sigma^gamma * sqrt(lambda_1(x, y, sigma))

where gamma = 1 for edge detection, gamma = 2 for blob detection.

True edges show consistent response across scales; noise peaks disappear.

### 4.4 Non-Maximum Suppression in Partial-Edge Context

Standard NMS: suppress pixels not at local maxima along gradient direction.

Modified for partial edges (turbulence gaps):
1. Compute edge response E(x,y)
2. For gaps < gap_threshold pixels: interpolate linearly along dominant orientation
3. Mark interpolated segments with confidence weight w = exp(-gap_length / lambda_gap)

    lambda_gap = coherence_length / pixel_size

---

## 5. Anisotropic Diffusion for Edge Recovery

### 5.1 Perona-Malik Diffusion

The Perona-Malik equation enhances edges while smoothing noise:

    dI/dt = div(c(|grad I|) * grad I)

Where the diffusion coefficient c controls smoothing:

    c(s) = exp(-(s/K)^2)      [exponential - sharp edge preservation]
    c(s) = 1 / (1 + (s/K)^2)  [rational - more stable]

K = contrast parameter (set near noise level).

### 5.2 Coherence-Enhancing Diffusion (CED)

For degraded edge recovery, CED follows the dominant edge orientation:

    dI/dt = div(D * grad I)

Where diffusion tensor D is:

    D = lambda_+ * v_+ v_+^T + lambda_- * v_- v_-^T

    lambda_+ = alpha                              [along edge: minimal diffusion]
    lambda_- = alpha + (1-alpha)*exp(-C/(lambda_1-lambda_2)^2m)  [across edge]

v_+ and v_- are eigenvectors of the structure tensor.

This propagates edge information along its own direction, bridging turbulence gaps.

### 5.3 Discretization

On a grid with spacing h, time step dt:

    I^(n+1)_ij = I^n_ij + dt/h^2 * sum_{neighbors k} c_ik * (I^n_k - I^n_ij)

Stability condition: dt < h^2 / (4 * max(c))

---

## 6. Bayesian Pattern Completion

### 6.1 The Completion Problem

Given observed edge fragments E_obs = {e_1, e_2, ..., e_N} and a library of shape templates T = {T_1, T_2, ..., T_M}, find:

    P(T_j | E_obs) proportional to P(E_obs | T_j) * P(T_j)

### 6.2 Likelihood Model

The likelihood P(E_obs | T_j) models edge fragment compatibility:

    P(E_obs | T_j) = product_i P(e_i | T_j)

For each fragment e_i:

    P(e_i | T_j) = sum_{t in T_j} G(e_i; t, Sigma_turb)

Where G is a Gaussian kernel in (position, orientation, scale) space:

    G(e; t, Sigma) = exp(-0.5 * (e-t)^T * Sigma^-1 * (e-t)) / sqrt(|2*pi*Sigma|)

Sigma_turb encodes positional uncertainty from turbulence:
    sigma_position = lambda * R / r_0   [position blur]
    sigma_orientation = sigma_I * 0.3   [orientation noise from scintillation]

### 6.3 Shape Prior P(T_j)

The prior encodes object frequency in expected environments:

    P(T_j) = n_j / sum_k n_k

Where n_j is the expected occurrence rate of object class j.

In a Bayesian update framework, previous observations update the prior:

    P(T_j | E_1,...,E_t) = P(E_t | T_j) * P(T_j | E_1,...,E_(t-1)) / Z

### 6.4 Missing Segment Imputation

Treat missing edge segments as latent variables Z:

    P(T_j | E_obs) = sum_Z P(T_j | E_obs, Z) * P(Z | E_obs)

P(Z | E_obs) is estimated from the edge structure prior:

    P(Z | E_obs) proportional to exp(-E_elastica(Z, E_obs))

Where E_elastica is the Euler-Bernoulli elastic energy:

    E_elastica = int (kappa^2 + lambda) ds

kappa = curvature of the completion curve, lambda = length penalty.

The minimum energy completion is the Euler spiral (clothoid) connecting fragment endpoints.

### 6.5 Chamfer Distance Scoring

Fast approximation using Chamfer distance between observed fragments and template:

    d_C(E_obs, T_j) = (1/|E_obs|) * sum_{e in E_obs} min_{t in T_j} ||e - t||

Score function:

    Score(T_j | E_obs) = exp(-alpha * d_C(E_obs, T_j)) * P(T_j)

Normalized to get posterior probabilities.

---

## 7. Wavelength-Dependent Attenuation

### 7.1 Beer-Lambert Law

Transmission T(lambda, R) through atmosphere:

    T(lambda, R) = exp(-int_0^R beta_ext(lambda, z) dz)

Where beta_ext = beta_scatter + beta_absorb is the extinction coefficient.

### 7.2 Rayleigh Scattering (molecular)

Dominant below 1 micron:

    beta_Rayleigh(lambda) = (8*pi^3 / 3) * (n^2 - 1)^2 / (N * lambda^4)

Scales as lambda^(-4) — violet light scatters ~10x more than red.

Practical formula for standard atmosphere:

    beta_R(lambda) = 0.00879 * lambda^(-4.09)   [km^-1, lambda in microns]

### 7.3 Mie Scattering (aerosol)

For particles with radius a ~ lambda:

    beta_Mie(lambda) = pi * a^2 * Q_ext * N_aerosol

Q_ext from Mie theory, approximately:

    Q_ext ≈ 2 for large particles (a >> lambda/2*pi)
    Q_ext ~ (2*pi*a/lambda)^4 for small particles

For haze/fog (Angstrom formula):

    beta_Mie(lambda) = beta_0 * (lambda_0/lambda)^alpha_A

alpha_A = Angstrom exponent: 0 for large droplets (fog), ~1.5 for fine aerosol.

### 7.4 Optimal Wavelength Selection

Maximize contrast-to-noise ratio across wavelength bands:

    CNR(lambda) = C_scene(lambda) * T(lambda, R) * MTF_turb(lambda) * sqrt(QE(lambda) * N_photons(lambda))

Optimal band lambda* = argmax CNR(lambda)

For edge detection specifically, shorter wavelengths provide finer spatial resolution (lambda * R / r_0 smaller) but suffer greater Rayleigh scattering. Optimal is typically near-IR (0.8-1.5 micron) for moderate ranges.

---

## 8. Thermal and Turbulence Effects

### 8.1 Thermal Gradient Effects

Near-surface thermal gradients cause refractive bending. The gradient of refractive index:

    dn/dz = -(79e-6 * P / T^2) * dT/dz

For dT/dz = 1 K/m (moderate thermal gradient):
    dn/dz ≈ 3e-8 /m  (at sea level)

This produces angular ray bending:

    d^2y/dz^2 = -(1/n) * dn/dy ≈ -(dn/dy)

Integrated over path R, vertical image displacement:

    Delta_y = -(1/2) * (dn/dy) * R^2

### 8.2 Thermal Shimmer as Phase Screen

Model thermal turbulence as a thin phase screen at distance z:

    phi(x,y) = k * int_0^R delta_n(x,y,z) dz

Phase structure function:

    D_phi(r) = 6.88 * (r/r_0)^(5/3)

This gives the Strehl ratio S (peak intensity relative to perfect optics):

    S = exp(-sigma_phi^2)   for sigma_phi << 1 (weak turbulence)
    S ≈ (D/r_0)^(-2)        for D >> r_0 (strong turbulence, D = aperture)

### 8.3 Temporal Evolution of Turbulence (Taylor Frozen Flow)

Turbulence evolves as wind transports frozen eddies across the beam:

    temporal_correlation_time = r_0 / v_wind

For v_wind = 5 m/s and r_0 = 5 cm:
    tau_coherence ≈ 10 ms

Integration times << tau provide speckle images (sharp but randomly blurred).
Integration times >> tau provide long-exposure images (average blur).

Short-exposure lucky imaging: capture frames during momentarily reduced turbulence (sigma_I low), then stack the best frames.

### 8.4 Turbulence-Aware Edge Model

Turbulence blurs the ideal edge profile E_ideal(x) into:

    E_obs(x) = E_ideal(x) * h_turb(x) + noise

Where h_turb is a Gaussian with sigma_turb = lambda * R / (2*pi*r_0).

The gradient of a blurred step edge:

    dE_obs/dx = (C / (sigma_turb * sqrt(2*pi))) * exp(-x^2 / (2*sigma_turb^2))

Edge localization uncertainty (1-sigma):

    delta_x ≈ sigma_turb / sqrt(SNR)

---

## 9. Multi-Hypothesis Scoring

### 9.1 Hypothesis Space

For each candidate object class j, generate hypotheses:
- H_j = {pose, scale, partial visibility fraction, illumination model}

Total hypothesis count: O(N_classes * N_poses * N_scales) per frame.

Efficient search via hierarchical coarse-to-fine:
1. Low-resolution gross shape match (5 candidates)
2. Medium resolution: verify edge topology (2 candidates)
3. Full-resolution: fine edge scoring (1 winner)

### 9.2 Weighted Hausdorff Distance

Robust to outliers and partial visibility:

    d_H(E_obs, T_j) = max(d_fwd, d_bwd)

    d_fwd = (1/|E_obs|) * sum_{e in E_obs} min_{t in T_j} w(e,t) * ||e-t||
    d_bwd = (1/|T_j|) * sum_{t in T_j} min_{e in E_obs} w(e,t) * ||e-t||

Weight w(e,t) based on orientation agreement:

    w(e,t) = exp(-|theta_e - theta_t|^2 / (2*sigma_theta^2))

### 9.3 Geometric Hashing for Rapid Template Matching

For each pair of edge fragments (e_i, e_j), compute invariant descriptor:

    phi = [d_ij, theta_i_rel, theta_j_rel, kappa_i, kappa_j]

Where d_ij = distance, theta_rel = relative orientations, kappa = curvature.

Hash phi into table indexed by quantized descriptor bins.
Template lookup in O(1) vs O(N_templates) brute force.

### 9.4 Temporal Integration (Track-Before-Detect)

Accumulate evidence across frames t = 1..T:

    Posterior_t(j) = P(E_1,...,E_t | T_j) * P(T_j)

Log-likelihood update:

    log L_t(j) = log L_{t-1}(j) + log P(E_t | T_j) - log Z_t

Declare detection when: log L_t(j*) > threshold AND margin over second-best > delta.

---

## 10. Cramer-Rao Bounds on Identification Confidence

### 10.1 Fisher Information for Edge Position

For a Gaussian edge profile with known sigma_turb, the Fisher information for edge position x_0:

    I(x_0) = integral [(d/dx_0 log p(I_obs | x_0))^2] p(I_obs) dx

    I(x_0) = SNR^2 / sigma_turb^2  (approximately)

Cramer-Rao bound on position estimation:

    Var(x_hat_0) >= 1 / I(x_0) = sigma_turb^2 / SNR^2

### 10.2 Identification Confidence

Define confidence as mutual information between observation and class:

    C_I = I(T; E_obs) = H(T) - H(T | E_obs)

    H(T | E_obs) = -sum_j P(T_j | E_obs) * log P(T_j | E_obs)

Identification is reliable when H(T | E_obs) < threshold (e.g., 0.5 bits).

### 10.3 Required Edge Fragment Coverage

Minimum fraction of template edges needed for reliable identification (at SNR and range R):

    f_min = N_features_required / N_features_total

    N_features_required ≈ log2(N_classes) / log2(1 / P_fa)

Where P_fa = false alarm probability.

For 100 classes, P_fa = 0.01: N_features_required ≈ 10 edge fragments.

### 10.4 Range Limit for Classification

Maximum range R_max at which classification remains reliable:

    R_max = r_0(C_n^2) / (lambda * f_min_detectable)

    f_min_detectable = f : MTF_turb(f) * SNR(R) = NEC_threshold

Solving numerically given system parameters gives the operational envelope.

---

## 11. Algorithm Summary

### Complete Pipeline

    INPUT: I_obs (degraded image), lambda (wavelength), R (range estimate), C_n^2

    STEP 1: PREPROCESSING
        Estimate r_0 from wavefront sensor or stellar scintillometer
        Compute expected MTF_turb(f) = exp(-3.44*(lambda*f*R/r_0)^(5/3))
        Apply Wiener deconvolution: I_restored = FFT^-1[ FFT(I_obs) / MTF_total ]

    STEP 2: EDGE EXTRACTION
        Compute structure tensor J at scales [sigma_1, sigma_2, sigma_3]
        Extract edge candidates with E > NEC threshold
        Tag each fragment with: position, orientation, scale, confidence

    STEP 3: EDGE COMPLETION
        For each gap in edge chain < gap_max:
            Compute minimum-energy Euler spiral completion
            Assign weight w = exp(-gap/lambda_gap)
        Add completed segments to fragment set

    STEP 4: HYPOTHESIS GENERATION
        Hash observed fragments into geometric descriptor table
        Retrieve top K candidate templates (K = 5-20)

    STEP 5: SCORING
        For each candidate T_j:
            Compute weighted Hausdorff distance d_H(E_obs, T_j)
            Score(j) = exp(-alpha * d_H) * P(T_j)
        Normalize scores to posterior probabilities

    STEP 6: TEMPORAL INTEGRATION
        Update log-likelihood: log L_t(j) += log Score_t(j)
        Compute posterior H(T | E_1..t)

    STEP 7: DECISION
        If H < H_threshold AND max P(T_j|E) > P_threshold:
            DECLARE: T_j* with confidence P(T_j*|E)
        Else:
            CONTINUE COLLECTING DATA

    OUTPUT: Object class, posterior probability, positional uncertainty

---

## Key Equations Reference

| Quantity | Formula | Notes |
|---------|---------|-------|
| Fried length r_0 | 0.185*(lambda^2/(C_n^2*R))^(3/5) | Smaller = worse |
| Turbulence MTF | exp(-3.44*(lambda*f*R/r_0)^(5/3)) | Long exposure |
| SNR at range | C*sqrt(N_ph) / sigma_speckle | Photon-limited |
| Edge position noise | sigma_turb / sqrt(SNR) | CR bound |
| Rayleigh scatter | 0.00879 * lambda^(-4.09) km^-1 | lambda in microns |
| Scintillation index | 1.23 * C_n^2 * k^(7/6) * R^(11/6) | Rytov approx |
| Completion curve | min integral(kappa^2 + lambda)ds | Euler spiral |
| Chamfer score | exp(-alpha * d_chamfer) * P(T_j) | Fast scoring |

---

*Hard-Crunchies Research | Partial Edge Contrast & Pattern Recognition at Range*
