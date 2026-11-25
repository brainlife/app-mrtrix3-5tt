<h1 align="center">Tissue-type segmentation (MRtrix3 5ttgen)</h1>

<p align="center">
  A five-tissue-type (5TT) image and binary gray matter, white matter and CSF masks for anatomically-constrained tractography, from FreeSurfer output or a T1-weighted image.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.720"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.720-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-lightgrey.svg" alt="MIT license"></a>
</p>

## 🧠 At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Generates a five-tissue-type (5TT) image from FreeSurfer output with MRtrix3 5ttgen hsvs (hybrid surface and volume segmentation), plus binarized gray matter, white matter and CSF masks, for anatomically-constrained tractography.**

Runs 5ttgen hsvs on the FreeSurfer subject directory, optionally using a T1w image as the template that defines the output voxel grid, with options for hippocampus and thalamus segmentation methods, brainstem as white matter, cropping and amygdala/hippocampus as subcortical GM. Writes the 5TT image and binarized cortical GM, WM and CSF masks. Requires a FreeSurfer run; use the fsl-based App when only a T1 image is available.

| | |
|---|---|
| 📥 **Inputs** | `neuro/freesurfer` · `neuro/anat/t1w` (optional) |
| 📤 **Outputs** | `neuro/mask` |
| 🐳 **Container** | `brainlife/mrtrix3:3.0.3-fsr` |
| ⚙️ **Resources** | 4 CPUs · 3 h walltime |
| 🔗 **Run it** | [doi.org/10.25663/brainlife.app.720](https://doi.org/10.25663/brainlife.app.720) |

#### 📥 Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `freesurfer` | `neuro/freesurfer` | yes | FreeSurfer recon-all output directory used by 5ttgen hsvs. |
| `input` | `neuro/anat/t1w` | no | Optional T1-weighted image passed as -template to 5ttgen hsvs so the 5TT image is written on its voxel grid. |

#### 📤 Outputs

| Output | Datatype | Description |
|---|---|---|
| `mask` | `neuro/mask` | 5TT image (mask.nii.gz) plus binarized gray matter (gm.nii.gz), white matter (wm.nii.gz) and CSF (csf.nii.gz) masks extracted from its volumes. |

<details>
<summary><b>⚙️ Parameters</b> (5)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `hippocampal` | enum | `subfields` | Hippocampus segmentation method for 5ttgen hsvs -hippocampi: subfields, first or aseg. Options: `subfields`, `first`, `aseg`. |
| `thalami` | enum | `nuclei` | Thalamus segmentation method for 5ttgen hsvs -thalami: nuclei, first or aseg. Options: `nuclei`, `first`, `aseg`. |
| `white_stem` | boolean | `false` | classify the brainstem as white matter (true) |
| `nocrop` | boolean | `false` | do not crop resulting 5tt image to reduce size |
| `sgm_amyg_hipp` | boolean | `false` | represent the amygdalae and hippocampi as sub-cortical grey matter in the 5tt image |

</details>

<!-- brainlife-app:end -->

## 🏷️ Versions on brainlife.io

This repository hosts two Apps, each built from its own branch. They use different `5ttgen` algorithms and are both maintained.

| Version | Algorithm | MRtrix3 | Branch | Run it | Status |
|---|---|---|---|---|---|
| Tissue-type segmentation with hsvs | `5ttgen hsvs` on FreeSurfer output | 3.0.3 | [`hsvs-binarize-v1.0`](https://github.com/brainlife/app-mrtrix3-5tt/tree/hsvs-binarize-v1.0) | [doi:10.25663/brainlife.app.720](https://doi.org/10.25663/brainlife.app.720) | current |
| Tissue-type segmentation | `5ttgen fsl` on a T1-weighted image | 3.0_RC3 | [`binarize-v1.0`](https://github.com/brainlife/app-mrtrix3-5tt/tree/binarize-v1.0) | [doi:10.25663/brainlife.app.239](https://doi.org/10.25663/brainlife.app.239) | available |

## 🚀 Running the App

### ☁️ On brainlife.io

**With hsvs** ([doi.org/10.25663/brainlife.app.720](https://doi.org/10.25663/brainlife.app.720)): choose a `neuro/freesurfer` dataset and, optionally, a T1-weighted image whose voxel grid the 5TT image should use (`-template`). Set the hippocampus method (`hippocampal`: `subfields`, `first` or `aseg`) and thalamus method (`thalami`: `nuclei`, `first` or `aseg`), and optionally `white_stem` (brainstem as white matter), `nocrop` and `sgm_amyg_hipp` (amygdalae and hippocampi as subcortical gray matter).

**With FSL** ([doi.org/10.25663/brainlife.app.239](https://doi.org/10.25663/brainlife.app.239)): choose a T1-weighted image (`neuro/anat/t1w`). The App runs `5ttgen fsl` (FSL BET, FAST and FIRST) with `-nocrop -sgm_amyg_hipp`. If the T1 is already skull-stripped, set `premask` to `true` (passes `-premasked`); otherwise you risk losing significant portions of the brain.

Both Apps archive a `neuro/mask` dataset tagged `5tt` and `5tt_masks` that MRtrix3 tracking Apps can use for ACT. The code was adapted from app-mrtrix3-act by Brent McPherson.

### 💻 Locally

You need [Singularity/Apptainer](https://apptainer.org/) and [`jq`](https://jqlang.github.io/jq/). The hsvs App runs `brainlife/mrtrix3:3.0.3-fsr`; the FSL App runs `brainlife/mrtrix3:3.0_RC3`.

1. Clone this repository and check out the branch of the App you want (`git checkout hsvs-binarize-v1.0` or `git checkout binarize-v1.0`).
2. Create `config.json`. For hsvs:

   ```json
   {
       "freesurfer": "input/freesurfer/output",
       "input": "input/t1/t1.nii.gz",
       "hippocampal": "subfields",
       "thalami": "nuclei",
       "white_stem": false,
       "nocrop": false,
       "sgm_amyg_hipp": false
   }
   ```

   For the FSL version:

   ```json
   {
       "anat": "input/t1/t1.nii.gz",
       "premask": false
   }
   ```

3. Run the App:

   ```bash
   ./main
   ```

### 🧪 Sample data

Download a T1-weighted sample from brainlife.io with the [brainlife CLI](https://github.com/brainlife/cli):

```bash
npm install -g brainlife
bl login
mkdir input
bl dataset download 5b96bbbf059cf900271924f2 && mv 5b96bbbf059cf900271924f2 input/t1
```

## 📦 Outputs

```text
mask/
├── mask.nii.gz   4D 5TT image (cortical GM, subcortical GM, WM, CSF, pathological tissue)
├── gm.nii.gz     binarized cortical gray matter (volume 0)
├── wm.nii.gz     binarized white matter (volume 2)
└── csf.nii.gz    binarized CSF (volume 3)
```

## 🩺 Troubleshooting

- **`mask generation failed`** (hsvs): the CSF mask was not produced, so `5ttgen hsvs` or `mrconvert` failed. Check the log for the `5ttgen` error, for example FreeSurfer files missing for the chosen hippocampus or thalamus method.
- **`mask generation failed`** (FSL): check the log for the `5ttgen fsl` error; for a skull-stripped T1 set `premask` to `true`.

## 📚 Citing

> [!TIP]
> If you use this App in your work, please cite the App, the software it builds on and brainlife.io (below). GitHub's **Cite this repository** button gives the App citation in APA and BibTeX.

- Smith RE, Tournier J-D, Calamante F, Connelly A. Anatomically-constrained tractography: Improved diffusion MRI streamlines tractography through effective use of anatomical information. *NeuroImage* 62:1924–1938 (2012).
- Tournier J-D, Smith RE, Raffelt D, Tabbara R, Dhollander T, Pietsch M, Christiaens D, Jeurissen B, Yeh C-H, Connelly A. MRtrix3: A fast, flexible and open software framework for medical image processing and visualisation. *NeuroImage* 202:116137 (2019).
- Smith SM. Fast robust automated brain extraction. *Human Brain Mapping* 17:143–155 (2002).
- Zhang Y, Brady M, Smith S. Segmentation of brain MR images through a hidden Markov random field model and the expectation-maximization algorithm. *IEEE Transactions on Medical Imaging* 20:45–57 (2001).
- Patenaude B, Smith SM, Kennedy DN, Jenkinson M. A Bayesian model of shape and appearance for subcortical brain segmentation. *NeuroImage* 56:907–922 (2011).
- Smith SM, Jenkinson M, Woolrich MW, Beckmann CF, Behrens TE, Johansen-Berg H, et al. Advances in functional and structural MR image analysis and implementation as FSL. *NeuroImage* 23:S208–S219 (2004).
- Tissue-type segmentation with hsvs. brainlife.io App. [doi:10.25663/brainlife.app.720](https://doi.org/10.25663/brainlife.app.720)
- Smith RE, Tournier J-D, Calamante F, Connelly A. Anatomically-constrained tractography: Improved diffusion MRI streamlines tractography through effective use of anatomical information. NeuroImage 62:1924-1938 (2012).
- Fischl. FreeSurfer. *NeuroImage* 62:774-781 (2012). [doi:10.1016/j.neuroimage.2012.01.021](https://doi.org/10.1016/j.neuroimage.2012.01.021)
- Jenkinson et al. FSL. *NeuroImage* 62:782-790 (2012). [doi:10.1016/j.neuroimage.2011.09.015](https://doi.org/10.1016/j.neuroimage.2011.09.015)
- Tournier et al. MRtrix3: A fast, flexible and open software framework for medical image processing and visualisation. *NeuroImage* 202:116137 (2019). [doi:10.1016/j.neuroimage.2019.116137](https://doi.org/10.1016/j.neuroimage.2019.116137)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## 👥 Authors

- Brad Caron
- Brent McPherson

**Contributors:** Soichi Hayashi

## 💰 Funding

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

<sub>brainlife.io is publicly funded, and acknowledging that support helps keep it running. This App was developed with support from Wellcome, the U.S. National Institutes of Health (including the BRAIN Initiative BRAIN CONNECTS program: The Axonal Projectome EXchange, U24NS140384, and the Center for Mesoscale Connectomics, UM1NS132207) and the U.S. National Science Foundation.</sub>

## ⚖️ License

MIT; see the [`LICENSE`](https://github.com/brainlife/app-mrtrix3-5tt/blob/hsvs-binarize-v1.0/LICENSE) file on the App branches. MRtrix3, FSL and FreeSurfer are distributed under their own licenses.
