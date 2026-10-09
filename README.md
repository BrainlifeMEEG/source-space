# Surface Source Space

[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.900-blue.svg)](https://doi.org/10.25663/brainlife.app.900)

## Description

Computes a surface-based source space from a FreeSurfer cortical reconstruction, using [`mne.setup_source_space`](https://mne.tools/stable/generated/mne.setup_source_space.html). The source space defines the candidate source locations (dipoles) on the cortical surface and is a required input for forward model computation.

The app generates:
- The source space (`src.fif`)
- A QC thumbnail with coronal/axial/sagittal views of the source points overlaid on the MRI
- An interactive HTML report with an MRI slider showing the source points

## Inputs

- **`output`** (`neuro/freesurfer`): FreeSurfer subject directory produced by `recon-all` (required). On Brainlife.io this is populated automatically from the upstream FreeSurfer app. For local testing, the subject directory path can instead be set directly under the `freesurfer` key in `config.json`; its parent directory is then used as `subjects_dir` and its directory name as `subject`.

## Outputs

- **`out_dir/src.fif`** (`raw`, tag `source_space`): the computed source space, consumed by the forward-model app.
- **`out_figs/source_space_thumb.png`**: coronal/axial/sagittal thumbnail with source points overlaid on the MRI (embedded in `product.json`).
- **`out_report/report.html`**: interactive MRI slider with the source space overlaid (falls back to the thumbnail image if the interactive view cannot be built).

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `spacing` | string | `oct6` | Source density. `oct6` = 4098 sources/hemisphere (recommended), `oct5` = 1026, `oct4` = 258 (testing only), `ico5` = 10242 (high density), `all` = all vertices (~150k/hemisphere, very slow), or a numeric string (e.g. `"10"`) for an approximate mm grid spacing. Passed to `mne.setup_source_space(spacing=...)`. |
| `add_dist` | boolean \| string (enum) | `false` | Whether to add distance/patch information. `false` = fastest, no distance or patch info. `true` = full distance + patch info (needs scikit-learn). `patch` = patch info only (needs SciPy). Passed to `mne.setup_source_space(add_dist=...)`. |
| `surface` | string | `white` | Cortical surface to place sources on (e.g. `white`, `inflated`). Not currently exposed as a Brainlife.io UI parameter, so production runs always use the default `white`; can be overridden via `config.json` for local testing. |
| `subjects_dir` | string | derived from the `output`/`freesurfer` input path | Path to the FreeSurfer `SUBJECTS_DIR`. Only needed to override the value otherwise derived from the input path. |
| `subject` | string | derived from the `output`/`freesurfer` input path | FreeSurfer subject name. Only needed to override the value otherwise derived from the input path. |

## Usage

### Running on Brainlife.io

1. Select a FreeSurfer reconstruction (output of the FreeSurfer recon-all app) as the input.
2. Optionally adjust `spacing` and `add_dist`.
3. Submit the task.
4. Review `src.fif` and the interactive report (source points overlaid on the MRI) in the output viewer.

### Local Testing

```bash
# Update config.json with the FreeSurfer subject directory and parameters
python3 main.py
```

## Pipeline Position

```
app-freesurfer-v2
      │
      └──→ app-source-space-v2  (src.fif)
                  │
                  └──→ app-forward-v2
```

`app-bem-v2` and `app-coreg-v2` run in parallel with this app — all three feed into `app-forward-v2`.

## Container

`docker://brainlifemeeg/mne-freesurfer:1.12.1-7.4.1`

Contains FreeSurfer 7.4.1 and MNE-Python 1.12.1.

## Authors

- [Maximilien Chaumon](https://github.com/dnacombo)
- [obVdo](https://github.com/obVdo)

## Citations

- Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. Nat Methods 21, 809–813 (2024). https://doi.org/10.1038/s41592-024-02237-2
- Gramfort, A. et al. MEG and EEG data analysis with MNE-Python. Front. Neurosci. 7, 267 (2013). https://doi.org/10.3389/fnins.2013.00267

## Funding Acknowledgement

brainlife.io is publicly funded. We kindly ask that you acknowledge the funding below in your code and publications.

[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-ACI-1916518](https://img.shields.io/badge/NSF_ACI-1916518-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1916518)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://grantome.com/grant/NIH/R01-EB029272-01)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://grantome.com/grant/NIH/R01-EB030896-01)

## License

Copyright (c) 2026 MEEG Brainlife team. Licensed under AGPL-3.0, see [license.txt](license.txt).
