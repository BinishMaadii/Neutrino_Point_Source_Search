# IceCube Point-Source Likelihood Search: TXS 0506+056

An independently built, unbinned maximum-likelihood point-source search for astrophysical
neutrinos, applied to the blazar **TXS 0506+056** using IceCube's public **IceTracks-DR2**
data release. Adapts profile-likelihood methodology from collider physics (ATLAS
differential cross-section measurement) to a neutrino-telescope point-source search.

## Result

| Quantity | Value |
|---|---|
| Source | TXS 0506+056 (RA=77.3582°, Dec=+5.6931°) |
| Test statistic (TS) | 5.030 |
| Best-fit spectral index (γ) | 2.50 |
| Best-fit signal events (n_s) | 11.98 |
| Empirical p-value | 0.008 (500 background trials) |
| Approx. significance | ~2.4σ (one-sided) |

This is a **modest, non-discovery-level excess**, consistent in scale with published
IceCube time-integrated analyses of this source. It is not a replication of IceCube's
full production analysis (no time-dependence, no multi-season systematic treatment) —
see [Limitations](#limitations) below.

## Methodology

- **Spatial term:** per-event Gaussian likelihood using each event's own angular
  uncertainty (`AngErr`), following the standard point-source construction of
  Braun et al. 2008 (Astropart. Phys. 29, 299).
- **Energy term:** signal PDF built from IceCube's provided effective-area and
  smearing-matrix instrument-response tables, weighted by a hypothesized spectral
  index γ (profiled jointly with signal strength n_s).
- **Background model:** entirely data-driven (not simulation-based) — a KDE with a
  diagnosed bandwidth in well-sampled energy regions, transitioning to a fitted
  power-law extrapolation in regions with insufficient real-event statistics.
- **Significance:** derived empirically via background trials (random source RA at
  fixed declination, exploiting the detector's RA-uniform background), not from an
  asymptotic Wilks'-theorem formula — the trials distribution was found to deviate
  meaningfully from a naive multi-parameter χ² assumption, consistent with
  Chernoff's theorem for a boundary-constrained parameter (n_s ≥ 0).

## Repository structure

```
├── README.md
├── requirements.txt
├── src/
│   ├── icecube_dr2_load_and_check.py       # data loading + sanity checks
│   ├── icecube_point_source_stage1.py      # spatial-only likelihood (baseline)
│   └── icecube_point_source_stage2_v5.py   # full spatial+energy likelihood (final)
├── figures/
│   └── (saved diagnostic plots from each pipeline stage)
└── docs/
    └── analysis_notes.md                    # debugging log / methodology notes
```

## Data

This analysis uses the **IceCube Second Track Data Release (IceTracks-DR2)**:
data from 2008–2022 for neutrino source searches, released by the IceCube
Collaboration under a CC0 1.0 license.

- **Official source:** https://doi.org/10.7910/DVN/MMIIZA (Harvard Dataverse)
- **Companion paper:** arXiv:2605.19040

Raw data files are **not included in this repository** (large file sizes; the
authoritative source is the Dataverse DOI above). To reproduce this analysis:

1. Download the following from the Dataverse link above:
   - `events/IC86_*_exp.tab` (one or more seasons; this analysis used seasons I–V)
   - `irfs/IC86_effectiveArea.tab`
   - `irfs/IC86_smearing.csv`
   - `uptime/IC86_*_exp.tab` (matching seasons)
2. Arrange them locally as:
   ```
   icecube_data/
   ├── events/
   ├── irfsss/      # note: matches the folder name used in this code
   └── uptime/
   ```
3. Update `DATA_DIR` in the script to point at this folder.

### Reproducing via Kaggle

This analysis was developed and run on **Kaggle Notebooks**. To reproduce it there:

1. Create a new Kaggle Notebook.
2. Upload the four data files above as a Kaggle Dataset (or three separate
   datasets for `events`, `irfsss`, `uptime`), then add it as a notebook Input.
3. Confirm the exact input path Kaggle assigns (visible under the "Input" panel,
   typically `/kaggle/input/<dataset-name>/...`) and set `DATA_DIR` accordingly
   in the script — this path can vary depending on how the dataset was uploaded.
4. Run `src/icecube_point_source_stage2_v5.py` cell-by-cell (the script is
   structured with `# Cell #N` markers for direct copy into notebook cells).

## Requirements

```
numpy
pandas
matplotlib
scipy
tqdm
```

Install with:
```bash
pip install -r requirements.txt
```

## Limitations

- **Single pre-specified source**, time-integrated only (no flare/burst search) —
  TXS 0506+056's strongest published significance (~3.5σ) comes from a
  time-*dependent* analysis of a 2014–2015 flare, which this pipeline does not
  attempt.
- Background model built from a **single declination band** around the source;
  does not implement full all-sky background modeling.
- Uses **5 of the available IC86 seasons** (2011–2016); the public release
  includes additional seasons through 2022 not yet incorporated.
- No treatment of detector or effective-area **systematic uncertainties**.
- The "mean PSF" diagnostic plot is a Fractional_Counts-weighted average, not a
  formal median angular resolution as typically quoted in IceCube publications.

## Citation [Motivated Paper]

If referencing the underlying data, please cite:

> IceCube Collaboration, 2026, "IceCube Second Track Data Release IceTracks-DR2:
> Data from 2008-2022 for Neutrino Source Searches", Harvard Dataverse,
> https://doi.org/10.7910/DVN/MMIIZA
