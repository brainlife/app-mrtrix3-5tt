[![Abcdspec-compliant](https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg)](https://github.com/brain-life/abcd-spec)
[![Run on Brainlife.io](https://img.shields.io/badge/Brainlife-bl.app.239-blue.svg)](https://doi.org/10.25663/brainlife.app.239)

# app-mrtrix3-5tt

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

**Generates a five-tissue-type (5TT) image from FreeSurfer output with MRtrix3 5ttgen hsvs (hybrid surface and volume segmentation), plus binarized gray matter, white matter and CSF masks, for anatomically-constrained tractography.**

Runs 5ttgen hsvs on the FreeSurfer subject directory, optionally using a T1w image as the template that defines the output voxel grid, with options for hippocampus and thalamus segmentation methods, brainstem as white matter, cropping and amygdala/hippocampus as subcortical GM. Writes the 5TT image and binarized cortical GM, WM and CSF masks. Requires a FreeSurfer run; use the fsl-based App when only a T1 image is available.

Run it on brainlife.io: [https://doi.org/10.25663/brainlife.app.720](https://doi.org/10.25663/brainlife.app.720)

| Input | Datatype | Required | Description |
|---|---|---|---|
| `freesurfer` | `neuro/freesurfer` | yes | FreeSurfer recon-all output directory used by 5ttgen hsvs. |
| `input` | `neuro/anat/t1w` | no | Optional T1-weighted image passed as -template to 5ttgen hsvs so the 5TT image is written on its voxel grid. |

| Output | Datatype | Description |
|---|---|---|
| `mask` | `neuro/mask` | 5TT image (mask.nii.gz) plus binarized gray matter (gm.nii.gz), white matter (wm.nii.gz) and CSF (csf.nii.gz) masks extracted from its volumes. |

| Parameter | Type | Default | Description |
|---|---|---|---|
| `hippocampal` | enum | `subfields` | Hippocampus segmentation method for 5ttgen hsvs -hippocampi: subfields, first or aseg. Options: `subfields`, `first`, `aseg`. |
| `thalami` | enum | `nuclei` | Thalamus segmentation method for 5ttgen hsvs -thalami: nuclei, first or aseg. Options: `nuclei`, `first`, `aseg`. |
| `white_stem` | boolean | `false` | classify the brainstem as white matter (true) |
| `nocrop` | boolean | `false` | do not crop resulting 5tt image to reduce size |
| `sgm_amyg_hipp` | boolean | `false` | represent the amygdalae and hippocampi as sub-cortical grey matter in the 5tt image |

Runtime: container `brainlife/mrtrix3:3.0.3-fsr`; 4 CPUs; 3 h walltime.

Please cite:
- Smith RE, Tournier J-D, Calamante F, Connelly A. Anatomically-constrained tractography: Improved diffusion MRI streamlines tractography through effective use of anatomical information. NeuroImage 62:1924-1938 (2012).

<!-- brainlife-app:end -->

This app will generate a 5-tissue type mask (5tt) from a T1 anatomical image using mrtrix3's 5ttgen. This code was adapted from app-mrtrix3-act (https://brainlife.io/app/5aac2437f0b5260027e24ae1), written by Brent McPherson (bcmcpher@iu.edu).

### Authors
- Brent McPherson (bcmcpher@iu.edu)
- Brad Caron (bacaron@iu.edu)

### Contributors
- Soichi Hayashi (hayashi@iu.edu)

### Funding
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-1636893](https://img.shields.io/badge/NSF_BCS-1636893-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1636893)

### Citations 

Please cite the following articles when publishing papers that used data, code or other resources created by the brainlife.io community. 

1. Smith, R. E.; Tournier, J.-D.; Calamante, F. & Connelly, A. Anatomically-constrained tractography: Improved diffusion MRI streamlines tractography through effective use of anatomical information. NeuroImage, 2012, 62, 1924-1938.

1. Smith, S. M. Fast robust automated brain extraction. Human Brain Mapping, 2002, 17, 143-155

1. Zhang, Y.; Brady, M. & Smith, S. Segmentation of brain MR images through a hidden Markov random field model and the expectation-maximization algorithm. IEEE Transactions on Medical Imaging, 2001, 20, 45-57

1. Patenaude, B.; Smith, S. M.; Kennedy, D. N. & Jenkinson, M. A Bayesian model of shape and appearance for subcortical brain segmentation. NeuroImage, 2011, 56, 907-922

1. Smith, S. M.; Jenkinson, M.; Woolrich, M. W.; Beckmann, C. F.; Behrens, T. E.; Johansen-Berg, H.; Bannister, P. R.; De Luca, M.; Drobnjak, I.; Flitney, D. E.; Niazy, R. K.; Saunders, J.; Vickers, J.; Zhang, Y.; De Stefano, N.; Brady, J. M. & Matthews, P. M. Advances in functional and structural MR image analysis and implementation as FSL. NeuroImage, 2004, 23, S208-S219

1. Tournier, J.-D.; Smith, R. E.; Raffelt, D.; Tabbara, R.; Dhollander, T.; Pietsch, M.; Christiaens, D.; Jeurissen, B.; Yeh, C.-H. & Connelly, A. MRtrix3: A fast, flexible and open software framework for medical image processing and visualisation. NeuroImage, 2019, 202, 116137

## Running the App 

### On Brainlife.io

You can submit this App online at [https://doi.org/10.25663/brainlife.app.239](https://doi.org/10.25663/brainlife.app.239) via the "Execute" tab.

### Running Locally (on your machine)

1. git clone this repo.
2. Inside the cloned directory, create `config.json` with something like the following content with paths to your input files.

```json
{
  "anat": "test/data/t1/t1.nii.gz"
}

```

### Sample Datasets

You can download sample datasets from Brainlife using [Brainlife CLI](https://github.com/brain-life/cli).

```
npm install -g brainlife
bl login
mkdir input
bl dataset download 5b96bbbf059cf900271924f2 && mv 5b96bbbf059cf900271924f2 input/t1
```


3. Launch the App by executing `main`

```bash
./main
```

## Output

The main output of this App is a mask datatype containing the 5tt mask.

#### Product.json
The secondary output of this app is `product.json`. This file allows web interfaces, DB and API calls on the results of the processing. 

### Dependencies

This App requires the following libraries when run locally.

  - singularity: https://singularity.lbl.gov/
  - Mrtrix3: https://hub.docker.com/r/brainlife/mrtrix3:3.0_RC3
