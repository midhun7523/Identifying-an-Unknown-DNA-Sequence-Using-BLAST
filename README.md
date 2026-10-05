# Identifying an Unknown DNA Sequence Using BLAST

<div align="center">

  <p><strong>A bioinformatics pipeline and workflow for querying, aligning, and identifying unknown DNA sequences using NCBI BLAST (Basic Local Alignment Search Tool).</strong></p>

  <p>
    <img src="https://img.shields.io/badge/Language-Python-blue.svg" alt="Python">
    <img src="https://img.shields.io/badge/Bioinformatics-BLAST-green.svg" alt="BLAST">
    <img src="https://img.shields.io/badge/License-MIT-yellow.svg" alt="License">
  </p>

</div>

---

## 🧬 Overview

In computational biology and genomics, determining the organism of origin or functional identity for an uncharacterized genetic sample is a fundamental challenge. This project implements a streamlined workflow using **BLAST (Basic Local Alignment Search Tool)** to compare an unknown nucleotide sequence against established biological databases (such as NCBI's GenBank nucleotide collection).

By analyzing local alignments, statistical significance scores (E-values), and percentage identities, this project demonstrates how to accurately deduce the biological identity of an unknown DNA sequence.

---

## 🚀 Key Features

* **Sequence Analysis:** Handles FASTA-formatted or raw nucleotide queries ($A, T, G, C$).
* **Database Alignment:** Interfaces with nucleotide databases to execute similarity searches (`blastn`).
* **Statistical Filtering:** Evaluates alignment outputs based on Expect values ($E$-values), Bit Scores, and Query Coverage.
* **Reproducible Workflow:** Designed for clarity, making it easy to adapt for wet-lab sequencing validation, metagenomics snippets, or academic assignments.

---

## 🛠️ Prerequisites & Tools

To run or replicate this analysis, ensure you have the following installed:

* **Python 3.x** (if utilizing custom parsing scripts)
* **NCBI BLAST+ Command Line Applications** (Optional, if running locally via CLI)
* Alternatively, this workflow can be executed via the [NCBI BLAST Web Server](https://blast.ncbi.nlm.nih.gov/).

---

## 📁 Repository Structure

```text
├── data/               # Contains input FASTA sequences (unknown queries)
├── results/            # BLAST alignment output files / reports
├── scripts/            # Helper scripts for sequence parsing or formatting (if applicable)
└── README.md           # Project documentation
