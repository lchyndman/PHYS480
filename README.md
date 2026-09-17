# PHYS480 Honours Project – Modelling and Analysis of Multi-Messenger Blazar Spectra

Luke Hyndman · Supervisor: Dr Ankur Sharma

An ongoing Honours-level project in astroparticle physics, using machine learning to recover the physical parameters of blazars (a class of active galactic nuclei) from their simulated photon spectra, and to assess which sources IceCube could realistically detect as neutrino emitters.

## Background

The origin of the diffuse high-energy neutrino flux detected by the IceCube Neutrino Observatory is still an open question, with blazars (AGN whose relativistic jets point close to our line of sight) among the leading candidate source populations. A blazar's relativistic protons can produce both hadronic gamma-rays and high-energy neutrinos via the same photopion interaction process, meaning a source's observable photon spectrum should, in principle, carry an imprint of whether it's also a significant neutrino emitter. This project asks: **given a blazar's simulated spectral energy distribution (SED), can a model learn to recover the physical parameters that produced it — and can that be extended to more realistic, incomplete observations?**

## Method

- **Simulation:** blazar photon and neutrino SEDs are generated using [AM3](https://doi.org/10.3847/1538-4365/ad725c), a time-dependent, self-consistent numerical model that evolves the coupled particle cascade (protons, electrons, photons, pions, muons, neutrinos) in a one-zone jet model, sampling physical parameters (redshift, bulk Lorentz factor, magnetic field strength, injection luminosities and energy cutoffs, etc.) from literature-motivated distributions. The dataset currently comprises **70,000+ simulated SEDs**, each tagged with its generating parameters, run through redshift/EBL attenuation to observer-frame spectra.
- **Parameter regression:** a Scikit-learn gradient-boosted histogram regressor is trained to recover physical parameters directly from photon SEDs (interpolated onto a common energy grid, mean-subtraction normalised). Regression is tested on unmasked photon SEDs, randomly masked photon SEDs (simulating incomplete real-world observations), and concatenated photon+neutrino SEDs (as an unrealistic but informative upper bound).
- **Subclass classification:** sources are additionally labelled along two independent binary axes (significant hadronic contribution vs. not; significant external photon field vs. not), and a 4-class gradient-boosted classifier is trained to recover these labels from the SED.
- **IceCube detectability filtering:** for each simulated source, a dimensionless detectability statistic is computed by comparing its expected IceCube signal (integrated over declination-dependent effective area) against IceCube's published point-source sensitivity flux, allowing the simulated population to be ranked by whether it would be observationally detectable — independent of the source's actual sky position.

## Current Results

- **Unmasked photon-SED regression** recovers several parameters well (redshift: R² = 0.999; bulk Lorentz factor: R² = 0.997; magnetic field: R² = 0.87), with weaker performance on maximum hadronic energy and no meaningful recovery of maximum leptonic (electron) energy.
- **Masking** photon SEDs by up to 20% causes a sharp drop in reconstruction quality for the harder-to-constrain parameters.
- **Adding the (unrealistic) neutrino SED as a feature** substantially improves regression across almost all parameters — highlighting how much extra constraining power the neutrino channel carries in principle, even though it's not directly observable for individual sources from Earth.
- **IceCube detectability filtering** shows the most detectable simulated sources are, as expected, those with low redshift, high Doppler boosting (bulk Lorentz factor), and high hadronic luminosity/maximum hadronic energy — consistent with physical intuition about what makes a nearby, strongly-boosted, hadronically active source visible to a neutrino telescope.

## Open Problems / Current Focus

- Deciding how to sample the simulation's parameter space in a way that preserves physically realistic correlations between parameters, rather than treating them as independent — currently a key limitation of the dataset.
- A subset of generated SEDs show only a single spectral peak rather than the expected two-hump (synchrotron + inverse-Compton) structure; whether this is a genuine physical regime or a numerical artefact (insufficient solver evolution time) is still being isolated.
- The IceCube detectability statistic currently assumes a fixed power-law spectral index for the published sensitivity flux, which doesn't match the shape of the simulated spectra — refining this will require interpolating between sensitivity curves at different spectral indices.

## Contents

- `Progress_Report.pdf` — full written progress report with methodology, figures, and references
- `Progress_Report_Presentation.pptx` — accompanying presentation slides

## Key References

Klinger et al. 2024 (AM3), Keivani 2018 & Aartsen et al. 2017 (TXS 0506+056 / diffuse flux constraints), Rodrigues et al. 2024 (leptohadronic blazar population modelling), Abbasi et al. 2026 (IceCube point-source sensitivity), Pedregosa et al. 2011 (Scikit-learn). Full reference list in the progress report.
