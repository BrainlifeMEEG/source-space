<h1 align="center">Surface Source Space</h1>

<p align="center">
  Computes a cortical surface source space from a FreeSurfer reconstruction with MNE-Python setup_source_space and outputs src.fif with a QC report.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.900"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.900-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-see_LICENSE-lightgrey.svg" alt="License"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Computes a cortical surface source space from a FreeSurfer reconstruction with MNE-Python setup_source_space and outputs src.fif with a QC report.**

Places candidate dipole sources on the FreeSurfer white surface at the chosen spacing (e.g. oct6, 4098 sources per hemisphere) with mne.setup_source_space, optionally adding distance and patch information, and renders the source points on the MRI. Use it to prepare the source space for an MEG/EEG forward model. It needs a FreeSurfer recon-all subject directory and a FreeSurfer license.

| | |
|---|---|
| **Inputs** | `neuro/freesurfer` |
| **Outputs** | `raw` |
| **Container** | `brainlifemeeg/mne-freesurfer:1.12.1-7.4.1` |
| **Resources** | 1 CPUs · 40 GB memory · 4 h walltime |
| **Requires** | `FREESURFER_LICENSE`, `FS_LICENSE` |
| **Run it** | [doi.org/10.25663/brainlife.app.900](https://doi.org/10.25663/brainlife.app.900) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `freesurfer` | `neuro/freesurfer` | yes | FreeSurfer subject directory produced by recon-all. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `out_dir` | `raw` | Surface source space (src.fif) for use by forward-model Apps. |

<details>
<summary><b>Parameters</b> (2)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `spacing` | string | `oct6` | Source density. Examples: oct6 = 4098 sources/hemisphere (recommended) oct5 = 1026 (faster) oct4 = 258 (testing only) ico5 = 10242 (high density) or a number like '10' for ~10mm grid spacing. |
| `add_dist` | enum | `False` | Add distance and patch information to the source space. This takes some time so precomputing it is recommended. Can also be ‘patch’ to only compute patch information. Options: `False`, `True`, `patch`. |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.900](https://doi.org/10.25663/brainlife.app.900), choose `neuro/freesurfer` from your project, set the parameters and press **Execute**. The results are saved to your project as `raw`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `brainlifemeeg/mne-freesurfer:1.12.1-7.4.1`), [`jq`](https://jqlang.github.io/jq/), `FREESURFER_LICENSE`, `FS_LICENSE`.

1. Clone this repository.
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "output": "/path/to/freesurfer/output",
       "spacing": "oct6",
       "add_dist": "False"
   }
   ```

3. Run `./main`.

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `spacing` | string | `oct6` | Source density. `oct6` = 4098 sources/hemisphere (recommended), `oct5` = 1026, `oct4` = 258 (testing only), `ico5` = 10242 (high density), `all` = all vertices (~150k/hemisphere, very slow), or a numeric string (e.g. `"10"`) for an approximate mm grid spacing. Passed to `mne.setup_source_space(spacing=...)`. |
| `add_dist` | boolean \| string (enum) | `false` | Whether to add distance/patch information. `false` = fastest, no distance or patch info. `true` = full distance + patch info (needs scikit-learn). `patch` = patch info only (needs SciPy). Passed to `mne.setup_source_space(add_dist=...)`. |
| `surface` | string | `white` | Cortical surface to place sources on (e.g. `white`, `inflated`). Not currently exposed as a Brainlife.io UI parameter, so production runs always use the default `white`; can be overridden via `config.json` for local testing. |
| `subjects_dir` | string | derived from the `output`/`freesurfer` input path | Path to the FreeSurfer `SUBJECTS_DIR`. Only needed to override the value otherwise derived from the input path. |
| `subject` | string | derived from the `output`/`freesurfer` input path | FreeSurfer subject name. Only needed to override the value otherwise derived from the input path. |

## Local Testing

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

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- Surface Source Space. brainlife.io App. [doi:10.25663/brainlife.app.900](https://doi.org/10.25663/brainlife.app.900)
- Gramfort A, et al. MEG and EEG data analysis with MNE-Python. Front Neurosci 7:267 (2013). [doi:10.3389/fnins.2013.00267](https://doi.org/10.3389/fnins.2013.00267)
- Fischl. FreeSurfer. *NeuroImage* 62:774-781 (2012). [doi:10.1016/j.neuroimage.2012.01.021](https://doi.org/10.1016/j.neuroimage.2012.01.021)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

- [Maximilien Chaumon](https://github.com/dnacombo)
- [obVdo](https://github.com/obVdo)

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io, which runs this App, is publicly funded by Wellcome, the U.S. National Institutes of Health and the U.S. National Science Foundation; acknowledging that support helps keep it running.</sub>

## License

See [LICENSE](LICENSE).
