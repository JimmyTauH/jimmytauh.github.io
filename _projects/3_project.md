---
layout: page
title: SpaDC
description: Sequence-based integrative analysis and regulatory inference for spatial chromatin accessibility data
img: assets/img/publication_preview/spadc.png
importance: 2
category: work
related_publications: true
sitemap: false
robots: noindex, follow
---

## SpaDC: Spatial Chromatin Accessibility Data Analysis

**SpaDC** is a graph-regularized convolutional neural network for spatial epigenomics. It jointly incorporates DNA sequence, chromatin accessibility, and spatial location to improve the analysis of spatial chromatin accessibility data.

### Overview

SpaDC learns sequence-aware representations for denoising and integrating spatial epigenomic datasets. It supports spatial domain identification and regulatory inference, including the discovery of spatial domain-specific cis-regulatory elements and gene regulatory networks.

### Key Features

- **Sequence-aware modeling:** uses DNA sequence together with chromatin accessibility signals
- **Spatially informed learning:** incorporates spatial-neighbor constraints for robust spatial domain identification
- **Integrative analysis:** aligns multiple spatial datasets and mitigates batch effects
- **Regulatory inference:** identifies cis-regulatory elements and infers transcription-factor gene regulatory networks

### Publication & Code

This work was published in *Communications Biology* (2026).

- [Read the paper](https://doi.org/10.1038/s42003-026-10462-y)
- [View the code](https://github.com/mcllllllll/SpaDC)

---

{% cite ma2026spadc %}
