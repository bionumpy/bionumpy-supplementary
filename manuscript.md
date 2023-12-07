---
title: 'Supplementary material for: Array programming for Biology'
keywords:
- markdown
- publishing
- manubot
lang: en-US
date-meta: '2023-12-07'
author-meta: []
header-includes: |
  <!--
  Manubot generated metadata rendered from header-includes-template.html.
  Suggest improvements at https://github.com/manubot/manubot/blob/main/manubot/process/header-includes-template.html
  -->
  <meta name="dc.format" content="text/html" />
  <meta property="og:type" content="article" />
  <meta name="dc.title" content="Supplementary material for: Array programming for Biology" />
  <meta name="citation_title" content="Supplementary material for: Array programming for Biology" />
  <meta property="og:title" content="Supplementary material for: Array programming for Biology" />
  <meta property="twitter:title" content="Supplementary material for: Array programming for Biology" />
  <meta name="dc.date" content="2023-12-07" />
  <meta name="citation_publication_date" content="2023-12-07" />
  <meta property="article:published_time" content="2023-12-07" />
  <meta name="dc.modified" content="2023-12-07T14:10:56+00:00" />
  <meta property="article:modified_time" content="2023-12-07T14:10:56+00:00" />
  <meta name="dc.language" content="en-US" />
  <meta name="citation_language" content="en-US" />
  <meta name="dc.relation.ispartof" content="Manubot" />
  <meta name="dc.publisher" content="Manubot" />
  <meta name="citation_journal_title" content="Manubot" />
  <meta name="citation_technical_report_institution" content="Manubot" />
  <link rel="canonical" href="https://bionumpy.github.io/bionumpy-supplementary/" />
  <meta property="og:url" content="https://bionumpy.github.io/bionumpy-supplementary/" />
  <meta property="twitter:url" content="https://bionumpy.github.io/bionumpy-supplementary/" />
  <meta name="citation_fulltext_html_url" content="https://bionumpy.github.io/bionumpy-supplementary/" />
  <meta name="citation_pdf_url" content="https://bionumpy.github.io/bionumpy-supplementary/manuscript.pdf" />
  <link rel="alternate" type="application/pdf" href="https://bionumpy.github.io/bionumpy-supplementary/manuscript.pdf" />
  <link rel="alternate" type="text/html" href="https://bionumpy.github.io/bionumpy-supplementary/v/72d881ba4c20c9c8007891aa190ccb5368cc47ed/" />
  <meta name="manubot_html_url_versioned" content="https://bionumpy.github.io/bionumpy-supplementary/v/72d881ba4c20c9c8007891aa190ccb5368cc47ed/" />
  <meta name="manubot_pdf_url_versioned" content="https://bionumpy.github.io/bionumpy-supplementary/v/72d881ba4c20c9c8007891aa190ccb5368cc47ed/manuscript.pdf" />
  <meta property="og:type" content="article" />
  <meta property="twitter:card" content="summary_large_image" />
  <link rel="icon" type="image/png" sizes="192x192" href="https://manubot.org/favicon-192x192.png" />
  <link rel="mask-icon" href="https://manubot.org/safari-pinned-tab.svg" color="#ad1457" />
  <meta name="theme-color" content="#ad1457" />
  <!-- end Manubot generated metadata -->
bibliography:
- content/manual-references.json
manubot-output-bibliography: output/references.json
manubot-output-citekeys: output/citations.tsv
manubot-requests-cache-path: ci/cache/requests-cache
manubot-clear-requests-cache: false
...












Benchmarks
---------------------

We compare the speed of BioNumPy against other existing Python packages and commonly used non-Python tools on a set of typical bioinformatics tasks. As seen in Figure @fig:benchmarks, we find that BioNumPy is generally considerably faster than vanilla Python solutions, as well as the commonly used Python packages BioPython and Biotite, which mostly rely on Python for-loops to perform operations on datasets. On problems where designated efficient bioinformatics tools are widely used (intersection of BED-files, kmer counting and VCF operations), we find that BioNumPy is close to, or as efficient as, tools written in C/C++ (BEDTools [@bedtools], Jellyfish [@jellyfish] and BCFTools [@bcftools]). A Snakemake pipeline for reproducing the results can be found at <https://github.com/bionumpy/bionumpy/tree/master/benchmarks>, along with an open invitation to expand the benchmark with additional tools and cases.

![**Benchmarking BioNumPy against other tools and methods on various typical bioinformatics tasks.**](images/benchmarks.png){#fig:benchmarks}
 
Reproducing a machine learning benchmark using BioNumPy
------------------------------------------------------------------------------------
To show how BioNumPy can be used to easily process and parse various biology data formats, we used BioNumPy to reproduce a recently published benchmark of a machine learning method [@sasse]. In the original work, the authors are using a combination of custom Python code and common bioinformatics tools (such as BCFTools [@bcftools]) to extract sequences around the transcription start sites of genes, which are used as input to a machine learning method. We have forked the original repository and replaced all this code, which consisted of X lines of Python code and X lines of shell scripts, with only a few calls to BioNumPy (in total Y lines). 

Our fork is available at <https://github.com/knutdrand/enformer_assesment_reproduction> We believe this shows that BioNumPy quite easily can be used to cleanly integrate various biology file formats.

BioNumPy Implementation details
----------------------------------------------------

BioNumPy internally stores sequence data (e.g. nucleotides or amino acids) as numeric values, allowing the use of standard NumPy arrays for data representation and processing. A key way BioNumpy achieves high performance is by storing multiple data entries in shared NumPy arrays. To illustrate the benefit of this approach, consider the example where we want to count the number of Gs and Cs in a large set of DNA sequences. Existing Python packages like BioPython and Biotite, do this by iterating over the sequences using Python for-loops, which is slow when the number of sequences is large. BioNumPy, however, stores all sequences in only one or a few shared NumPy arrays (Figure 3a), meaning that vectorized NumPy operations can be used to do the counting in a fraction of the time.

Storing multiple elements in shared arrays is trivial if the elements all have the same size, since a matrix representation can be used. However, for biological data, it is common that data elements vary in size. For instance, sequences in FASTA files are rarely all of the exact same size. BioNumPy uses the RaggedArray data structure from the npstructures package (<https://github.com/bionumpy/npstructures>, developed in tandem with BioNumPy) to tackle this problem (Figure @fig:ragged_array). The RaggedArray can be seen as a matrix where rows can have different lengths. The npstructures RaggedArray implementation is compatible with most common NumPy operations, like indexing (Figure @fig:ragged_array b), vectorized operations (Figure @fig:ragged_array c), and reductions (Figure @fig:ragged_array d). As far as possible, objects in BioNumPy follow the array interoperability protocols defined by NumPy (<https://numpy.org/doc/stable/user/basics.interoperability.html>)


![ **Overview of the RaggedArray and EncodedRaggedArray data structures**. A RaggedArray is similar to a NumPy array/matrix but can represent a matrix consisting of rows with varying lengths (a). This makes it able to represent data with varying lengths efficiently in a shared data structure. A RaggedArray supports many of the same operations as NumPy arrays, such as indexing (b), vectorization (c) and reduction (d). An EncodedRaggedArray is a RaggedArray that supports storing and operating on non-numeric data (e.g. DNA sequences) by encoding the data and keeping track of the encoding (e). An EncodedRaggedArray supports the same operations as RaggedArrays (f). This figure is an adopted and modified version of  Figure 1 in [@numpy] and is licensed under a Creative Commons Attribution 4.0 International License (<http://creativecommons.org/licenses/by/4.0/>).
](images/ragged_array_figure.png){#fig:ragged_array}





BioNumPy has been developed following the principles of continuous integration and distribution. The codebase is thoroughly and automatically tested through an extensive collection of unit tests, application tests, integration tests and property-based tests [@hypothesis]. New code changes are automatically benchmarked and tested before being automatically published, ensuring that updates can be frequent while high code quality is maintained. This workflow makes it safe and easy to allow contributions from new contributors, which is important for longevity and community adoption of the package.


[@jellyfish]: doi:10.1093/bioinformatics/btr011
[@bedtools]: doi:10.1093/bioinformatics/btq033
[@bcftools]: doi:10.1093/gigascience/giab008
[@hypothesis]: doi:10.21105/joss.01891
[@sasse]: doi:10.1038/s41588-023-01524-6


## References {.page_break_before}

<!-- Explicitly insert bibliography here -->
<div id="refs"></div>

