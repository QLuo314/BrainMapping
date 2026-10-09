# Biologically inspired alignment reveals distributed and directionally asymmetric correspondence between rat and human cortices

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **BrainMapping** learns distributed, bidirectional functional correspondence between rat and human cortices from resting-state fMRI.

## Overview

Rodent models enable causal investigation of brain circuits, but translating their systems-level organization to humans requires correspondence beyond one-to-one anatomical homology.

**BrainMapping** is a biologically inspired framework that combines shared rs-fMRI representations with graph alignment and optimal transport to infer **probabilistic, many-to-many cortical correspondences**. It represents each region of interest (ROI) using voxel-wise time series and relates regions through their intrinsic activity and distributed functional organization, without imposing predefined rat-human ROI matches.

The study asks whether these mappings preserve functional organization in held-out subjects and whether preservation depends on mapping direction. The resulting maps provide a framework for expressing rat functional organization in human functional-network coordinates and generating testable cross-species hypotheses.

## Motivation

- Anatomical reference points alone may not capture how functional organization is distributed across species.
- Differences in cortical scale, parcellation and evolutionary organization make strict one-to-one correspondence restrictive.
- Direct rat-human ROI correspondence labels are unavailable, motivating evaluation through preservation of functional connectivity, cortical gradients and graph topology.
- Human-to-rat and rat-to-human mappings need not preserve organization to the same extent.

## Framework

BrainMapping comprises three stages.

### Stage 1: Cross-Species Representation Pretraining (CRP)

A **shared-weight Transformer encoder** processes voxel-wise ROI time series from both species and learns ROI-level features in a common latent space. Angular-margin supervision encourages separation of within-species ROI representations.

### Stage 2: Biology-Informed Representation Refinement (BIR)

The pretrained Transformer encoder is frozen, and ROI representations are refined through two objectives:

- **Masked-view self-supervision:** a BYOL-style online-target architecture matches representations of two masked views of the same ROI. This objective promotes representation agreement rather than reconstructing masked voxel intensities.
- **Functional segregation-integration (FSI) supervision:** a cross-entropy classification objective uses predefined within-species network or cortical-system labels to regularize the features.

These labels encode within-species organization; they do not supply rat-human ROI correspondence pairs.

### Stage 3: Bidirectional Graph Matching via Optimal Transport (BGM-OT)

Species-specific **K-nearest-neighbor feature graphs** are processed by a shared-weight **graph convolutional network (GCN)**. Entropy-regularized **optimal transport**, solved with Sinkhorn iterations, estimates a distributed cross-species transport plan.

Training combines feature alignment, graph-Laplacian spectral alignment and cycle consistency. Feature and topology alignment are defined in the human-to-rat direction, with a human-to-rat-to-human feature cycle. The resulting mapping supports transformations in both directions, although the training objectives are not directionally symmetric.

## Data and Atlas Definitions

| | Human | Rat |
|---|---|---|
| Dataset | WU-Minn HCP 1200 Subjects; 3T preprocessed `rfMRI-REST1` fMRIVolume data | 7T rs-fMRI from male Sprague-Dawley rats under anesthesia |
| Subjects | 72: 57 training, 15 held-out | 54: 43 training, 11 held-out |
| Atlas | Schaefer 100-parcel, 7-network atlas | SIGMA atlas |
| Within-species labels | Seven functional networks | Four predefined cortical systems: frontal, parietal, occipital and temporal |

Supplementary Tables S1 and S2 provide ROI identities, hemisphere labels and network/system assignments. Table S3 lists the human subject IDs and demographic information.

## Validation Strategy

The final map is estimated **using training subjects only**. Cross-species ROI cosine-similarity matrices are averaged over human-rat training-subject pairs, converted into a transport cost, and used to estimate a single mapping plan. That plan is **fixed before application to held-out subjects**.

Evaluation examines complementary aspects of target-space organization:

- **Functional connectivity:** network-level similarity between mapped and native within-network FC matrices.
- **Functional gradients:** preservation of macroscale cortical organization.
- **Graph topology:** agreement between mapped and native topological profiles.
- **Representation stability:** inter-subject similarity and comparisons of training and held-out representations.

These tests assess preservation of functional organization, not direct ROI correspondence accuracy.

## Main Findings

- **Distributed correspondence:** the learned maps express rat-human relationships as many-to-many functional associations.
- **Held-out preservation:** fixed, training-derived maps preserve complementary aspects of FC, cortical gradients and graph topology.
- **Directional asymmetry:** human-to-rat mapping preserves organization more uniformly across rat cortical systems. Rat-to-human FC preservation is stronger in default-mode, dorsal-attention and salience/ventral-attention networks than in visual and somatomotor networks.
- **Biological hypotheses:** the maps recover literature-consistent default-mode and salience-system relationships and generate non-canonical candidate correspondences for further testing.

Directional differences must be interpreted in the context of acquisition conditions, atlas granularity, target-system definitions and the asymmetric training objectives. Individual correspondences are functional hypotheses, not confirmed anatomical or evolutionary homologues. A compression-expansion interpretation remains a hypothesis rather than an established mechanism.

## Interactive Exploration

The manuscript and supplementary information describe the [BrainMapping visualization platform](http://www.brmapping.com) for exploring cross-species ROI similarity profiles.

Supplementary Figs. S1-S4 illustrate human-to-rat exploration from human FrOperIns 1 and OFC 1, and rat-to-human exploration from rat orbitofrontal and agranular insular regions. Supplementary Tables S4 and S5 list the top five cross-species targets for selected seeds; their reported scores are cosine similarities.

## Repository Status

This repository accompanies the **BrainMapping** manuscript. It currently contains project documentation; code, pretrained models and detailed usage instructions have not yet been added. Their release remains planned for after publication.

## Planned Contents

- Data preprocessing pipeline
- Model training and inference code
- Cross-species alignment and evaluation scripts
- Pretrained checkpoints
- Interactive mapping demo

## Citation

**Biologically inspired alignment reveals distributed and directionally asymmetric correspondence between rat and human cortices**

Xingbo Dong, Qiang Luo, Aoling Cai, Zhe Jin, Chaoxu Mu, Fei Wang and Hongming Shan.

Publication details and a formatted citation will be added upon publication.

## License

This project is released under the MIT License.
