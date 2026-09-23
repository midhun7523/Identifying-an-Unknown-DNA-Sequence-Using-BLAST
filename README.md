1. Introduction to the Topic
Modern DNA sequencing produces large volumes of nucleotide data represented as strings of four bases: A, T, C, and G. On their own, these raw sequences have no biological meaning until they are compared with known, annotated sequences. To identify an unknown DNA fragment, researchers search it against large reference databases such as NCBI GenBank, which store millions of characterized genomes.
BLAST (Basic Local Alignment Search Tool) is the standard software used for this task. Instead of exhaustively comparing the query to every position in every database sequence, BLAST first looks for short exact matches called “words” and then extends only those promising regions. This heuristic makes it possible to search huge databases in seconds rather than hours.
Main concepts I studied: 
	DNA sequence and FASTA format
	Sequence databases (GenBank / NCBI nt)
2. Motivation (Chosen Application)
Application: Clinical Diagnostics and Infectious Disease Control
Context & Importance: In clinical healthcare, patients frequently present with severe bacterial or viral infections of unknown origin. Traditional culture-based pathogen identification can take days. By extracting DNA from a patient sample and running a sequence query through BLAST, clinicians can rapidly identify the exact pathogenic organism (e.g., distinguishing pathogenic Escherichia coli or Listeria from harmless flora). Rapid identification directly informs targeted antibiotic treatment, reduces hospital mortality, and helps prevent disease outbreaks.
3. Research Question
Can BLAST help identify an unknown DNA sequence by comparing it with known sequences?
4. Breaking the Problem into Five Sub-Problems
Overall Application: Clinical Diagnostics & Bacterial Pathogen Identification in Infectious Disease Control [1][2].
1.	Alignment Metric Thresholds (Chosen Focus): How do BLAST alignment metrics—specifically E-value, Percent Identity, and Query Coverage—differentiate an exact species match from random background alignment when identifying an unknown bacterial DNA sequence?[1]
2.	Sequence Length Limits & Degradation: What is the minimum nucleotide length required for a degraded DNA fragment before BLAST fails to distinguish between closely related species?[1]
3.	Mutation and Gap Penalties: How do point mutations (substitutions) and insertions/deletions (indels) impact raw alignment scores and E-values during local sequence alignment?[1]
4.	Database Scale Effects: How does searching against a massive global database (NCBI GenBank) versus a targeted database (16S rRNA gene library) affect search time and E-value significance thresholds?[1]
5.	Programmatic Parse & Automation: How can a Python script parse multi-hit BLAST alignment outputs to automate clinical diagnostic reporting?

5. Refined Sub-Problem Statement
How do BLAST alignment metrics specifically E-value, Percent Identity, and Query Coverage—differentiate an exact species match from random background alignment when identifying an unknown bacterial DNA sequence?

I started with the broad question above. It is a yes/no question, so on its own it cannot be tested. By asking clarifying questions (recorded in Document 2) I found that the interesting part is not whether BLAST returns an answer, because it always returns something, but whether that answer means anything. That gave me a sub-problem I could actually test.

To systematically explore this sub-problem, I conducted an iterative series of clarifying questions, using each finding to refine my experimental setup and understanding (THEORITICAL)
Question 1: What raw alignment values does BLAST calculate when comparing two DNA sequences?
Finding: BLAST calculates a raw alignment score (S) based on match rewards (+1 for identical bases) and penalties for mismatches (−2) and gap creations (−5). However, raw scores increase with sequence length, making them unreliable on their own for comparing searches across different database sizes.
Question 2: How does the E value translate raw alignment scores into statistical confidence?
Finding: BLAST uses the Karlin–Altschul equation:
                                              E = K · m · n · e^(−λS)
where m is the query length, n is the total database size, and K and λ are statistical scaling constants. The E value (Expect Value) estimates how many random matches with a score ≥ S would occur by chance. An E value approaching 0.0 indicates statistically significant biological homology.
Question 3: How does a single point mutation (substitution) affect Percent Identity and E value?
Finding: When an unknown sequence contains a 1 base mutation relative to a reference strain, the Percent Identity drops slightly (e.g., from 100% to 98.6%), but the E value remains extremely low (≈ 10⁻²⁰), confirming that minor genetic drift does not destroy identification confidence.
Question 4: What metrics emerge when querying an unknown sequence against completely unrelated species?
Finding: Non matching organisms yield low identity (< 30%) and massive E values (> 10⁶), demonstrating that E values greater than 1.0 reliably filter out background noise.
Question 5: Why is Query Coverage necessary alongside Percent Identity?
Finding: High identity over a tiny fraction of a sequence (e.g., 100% over 10 base pairs) is biologically meaningless. High confidence species identification requires Query Coverage > 90%, Percent Identity > 98%, and an E value < 10⁻⁵.
Practical Computational Experiments & Findings
 

 

BLAST Sensitivity Heatmap: Exponential E-value confidence declines with decreasing query length or increasing mismatch rate; green quadrant (≥ 35 bp, ≥ 98.5% identity) enables unambiguous species identification.
 
Experiment 1: Impact of Point Mutations on Identity & E value
Introduced 0 to 20 base mismatches into a 70 base pair (bp) bacterial query sequence to measure how natural mutations or sequencing errors affect BLAST metrics:
	0 mismatches (exact match) – Percent Identity: 100.0%; Raw Score: 70; E value: 3.0 × 10⁻¹⁶; Decision: Exact species identification (E. coli)
	1 mismatch – Percent Identity: 98.6%; Raw Score: 67; E value: 2.4 × 10⁻¹⁵; Decision: High similarity / minor variant (Listeria)
	3 mismatches – Percent Identity: 95.7%; Raw Score: 61; E value: 1.5 × 10⁻¹³; Decision: Genus level homolPogy
	12 mismatches – Percent Identity: 82.9%; Raw Score: 34; E value: 2.0 × 10⁻⁵; Decision: Borderline significance
	20 mismatches – Percent Identity: 71.4%; Raw Score: 10; E value: 3.4 × 10⁺²; Decision: Random background noise (rejected)
Practical Insight: A single base substitution drops identity to 98.6%, but the E value remains extremely significant (~10⁻¹⁵), proving that BLAST tolerates minor genetic variations without losing diagnostic confidence.
________________________________________
Experiment 2: Minimum Sequence Length Sensitivity
Tested query lengths from 15 bp to 200 bp to find the minimum fragment length for reliable identification:
	15 bp – Raw Score: 15; E value: 2.29 × 10⁰ (2.29); Decision: Inconclusive / random noise
	25 bp – Raw Score: 25; E value: 3.74 × 10⁻³; Decision: Weak match
	35 bp – Raw Score: 35; E value: 5.12 × 10⁻⁶; Decision: Statistically significant match (below 10⁻⁵)
	70 bp – Raw Score: 70; E value: 3.00 × 10⁻¹⁶; Decision: High confidence species match
	200 bp – Raw Score: 200; E value: 6.41 × 10⁻⁵⁵; Decision: Absolute species identification
Practical Insight: DNA fragments shorter than 25 bp generate high E values (> 10⁻³) because short overlaps occur frequently by chance. Reliable pathogen identification requires query lengths ≥ 35 bp.
________________________________________
Experiment 3: Database Scale Effects (Karlin–Altschul Equation)
Using the Karlin–Altschul formula:
E = K · m · n · e^(−λS)
Tested how searching a fixed 40 bp sequence against different database sizes (n) affects the E value:
	100 bp (micro reference set) – E value: 3.66 × 10⁻¹⁰
	50,000 bp (local bacterial 16S rRNA database) – E value: 1.83 × 10⁻⁷
	100,000,000 bp (NCBI GenBank global repository) – E value: 3.66 × 10⁻⁴
Practical Insight: As database size (n) grows, the probability of a random match increase. Searching a targeted local database yields a lower E value than searching the entire NCBI GenBank repository for the same sequence.

8. Conclusion
1. What I Learned About BLAST
•	DNA is Genetic Code: DNA is written using four chemical letters (A, T, C, and G). Raw DNA sequences have no context until they are compared against known organisms.
•	BLAST as a Search Engine: BLAST (Basic Local Alignment Search Tool) acts as a specialized search engine for genetics. You give it an unknown DNA sequence, and it searches a database of known genomes to find which species it belongs to.
2. The 3 Metrics Used to Prove a Match
To prove that an unknown DNA sample belongs to a specific bacterium, BLAST calculates three core numbers:
1.	Percent Identity: The percentage of DNA letters that match exactly (e.g., 100% for an exact match vs. 98.6% for a slight mutation).
2.	Query Coverage: How much of your unknown DNA length was aligned against the database (aiming for > 90%).
3.	E-value (Expect Value): The statistical score indicating chance occurrence. An E-value close to 0.0 proves the match is real and not a random coincidence. High E-values represent random background noise.

3. Key Insights from Practical Testing
•	Handling Mutations: A single base mutation lowers match identity slightly (from 100% to 98.6%), but the E-value remains extremely strong, proving BLAST tolerates minor genetic differences without losing accuracy.
•	Minimum Fragment Length: DNA fragments must be at least 35 base pairs long for reliable identification; shorter fragments (e.g., 15 bp) yield high E-values and cannot be trusted.
•	Database Impact: Searching a smaller, targeted database (like a bacterial 16S rRNA library) gives stronger statistical confidence than searching a massive global database.
4. Real-World Application & Impact
In Clinical Diagnostics, rapidly identifying an unknown bacterium from a patient sample using BLAST allows doctors to pinpoint pathogens (like E. coli or Listeria) in a matter of hours—enabling targeted antibiotic treatment far faster than traditional 3-day lab cultures.

 
9. References
1.	NCBI BLAST home page – https://blast.ncbi.nlm.nih.gov/Blast.cgi
2.	BLAST Help & Documentation (NCBI) – https://blast.ncbi.nlm.nih.gov/doc/blast-help/
3.	BLAST Frequently Asked Questions – https://blast.ncbi.nlm.nih.gov/doc/blast-help/FAQ.html
4.	NCBI GenBank overview – https://www.ncbi.nlm.nih.gov/genbank/
5.	NCBI Workshop: BLAST Statistics – The Expect Value – https://www.nlm.nih.gov/ncbi/workshops/2023-08_BLAST_evol/e_value.html
6.	NCBI Guide: BLAST – Compare & identify sequences – https://guides.lib.berkeley.edu/ncbi/blast
7.	SequenceServer blog: Interpreting nucleotide BLAST results – https://sequenceserver.com/blog/interpretation-of-blastn-results/
8.	VigyanLLM: How to Interpret BLAST Results — E-value, Identity, Coverage – https://www.vigyanllm.in/blog/blast-results-interpretation
9.	MetricGate: Karlin-Altschul E-value Calculator & explanation – https://metricgate.com/docs/karlin-altschul-evalue/
10.	Genomics Aotearoa: Interpreting BLAST results (E-values, coverage, identity) – https://genomicsaotearoa.github.io/hts_workshop_mpi/level1/43_blast_interpretation/
11.	BioUnfold: Reading BLAST Results: E-values, Bit Scores, and Query Coverage – https://biounfold.in/blog/blast-evalue-bitscore-interpretation
12.	PunnettSquare.org: E-value & Bit Score Calculator – BLAST Statistics – https://punnettsquare.org/evalue-calculator/
13.	DeepWiki: Karlin-Altschul Parameters and E-value Calculation – https://deepwiki.com/satoshikawato/LOSAT/4.1-karlin-altschul-parameters-and-e-value-calculation
14.	Geneious Help: Can I BLAST primers, short DNA sequences or peptides? – https://help.geneious.com/hc/en-us/articles/360044628932-Can-I-BLAST-primers-short-DNA-sequences-or-peptides
15.	EMBL-EBI IPD-MHC BLAST (notes on minimum sequence length) – https://www.ebi.ac.uk/ipd/mhc/blast/
16.	NCBI Handbook – BLAST chapter (overview of algorithms and statistics) – https://www.ncbi.nlm.nih.gov/books/NBK21097/
17.	NCBI Bookshelf: “An Introduction to Sequence Similarity (Homology) Searching” – https://pmc.ncbi.nlm.nih.gov/articles/PMC3820096/
18.	NCBI BLAST Glossary (definitions of key terms) – https://blast.ncbi.nlm.nih.gov/doc/blast-help/blastglossary.html
19.	NCBI: Nucleotide BLAST (blastn) program selection guide – https://blast.ncbi.nlm.nih.gov/doc/blast-program-selection-guide/
20.	NCBI: Understanding BLAST word size and short queries – https://blast.ncbi.nlm.nih.gov/doc/blast-help/FAQ.html#short
21.	NCBI: 16S ribosomal RNA sequences and microbial identification – https://www.ncbi.nlm.nih.gov/books/NBK544267/
22.	CDC / NIH overview: Using genomics in infectious disease surveillance – https://www.cdc.gov/genomics/ (search “infectious disease” on site)
23.	Nature Scitable: Basic concepts in sequence alignment and BLAST – https://www.nature.com/scitable/topic/sequence-alignment-101/
24.	EMBL-EBI Train Online: BLAST tutorials and exercises – https://www.ebi.ac.uk/training/online/ (search “BLAST”)
25.	Biopython Tutorial – BLAST chapter – https://biopython.org/docs/latest/Tutorial/
26.	NCBI: Microbial Genomes and Pathogen Identification (overview) – https://www.ncbi.nlm.nih.gov/genome/microbes/
27.	PubMed: Search for reviews on BLAST in clinical microbiology – https://pubmed.ncbi.nlm.nih.gov/ (query: “BLAST clinical microbiology review”)
NCBI: Introduction to the NCBI databases and resources – https://www.ncbi.nlm.nih.gov/guide/
