# source-space

Compute a surface-based source space from a FreeSurfer reconstruction.

The source space defines the candidate source locations on the cortical surface. It is a required input for the forward model computation.

## Inputs

| Datatype | Description |
|----------|-------------|
| `neuro/freesurfer` | FreeSurfer subject directory (output of recon-all) |

## Outputs

| Datatype | File | Description |
|----------|------|-------------|
| `neuro/source-space` | `src.fif` | Source space (consumed by forward app) |
| `report/html` | `report.html` | Interactive MRI slider with source points overlaid |

## Parameters

| Parameter | Default | Options | Description |
|-----------|---------|---------|-------------|
| `spacing` | `oct6` | `oct6`, `oct5`, `oct4` | Source density. `oct6` = 4098 sources/hemisphere (recommended). `oct5` = 1026. `oct4` = 258 (testing only). |
| `surface` | `white` | `white`, `inflated` | Cortical surface to place sources on. |
| `add_dist` | `false` | `false`, `patch`, `true` | Distance/patch information. `false` = fastest. `patch` = patch info only (needs SciPy). `true` = full (needs scikit-learn). |

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

`docker://brainlife/mne-freesurfer:7.3.2-1.2.1`

Contains FreeSurfer 7.3.2 and MNE-Python 1.2.1.

## Citations

Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. Nat Methods 21, 809–813 (2024). https://doi.org/10.1038/s41592-024-02237-2
