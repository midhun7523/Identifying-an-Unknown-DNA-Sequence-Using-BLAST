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

🔍 Step-by-Step Workflow
Input Acquisition: Obtain the unknown nucleotide sequence in standard FASTA format.

Database Selection: Choose an appropriate reference database (e.g., nt for standard nucleotide or specific organism-restricted libraries).

Execution (blastn): Run the search algorithm to find high-scoring segment pairs (HSPs) between the query and database sequences.

Interpretation:

Max Score / Bit Score: Measures the overall quality of the alignment.

E-value (Expect Value): Represents the probability of finding matches purely by chance (lower values indicate higher significance).

Identity %: Indicates the exact match percentage between the query and the subject sequence.

💡 Example Usage
If executing via NCBI BLAST+ CLI:

Bash
blastn -query data/unknown_sequence.fasta -db nt -remote -out results/blast_output.txt -max_target_seqs 5 -outfmt 0
🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check out the issues page.

📝 License
This project is distributed under the MIT License. Feel free to use, modify, and build upon this work for academic or research purposes.
