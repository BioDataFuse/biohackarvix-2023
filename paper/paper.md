---
title: 'BioHackEU23 report: Extending interoperability of experimental data using modular queries across biomedical resources'
title_short: 'BioHackEU23 #17: unknown chemical substances'
tags:
  - Knowledge Graph
  - Modular query
  - Biomedical database
authors:
  - name: First Author
    affiliation: 1
  - name: Egon Willighagen
    orcid: 0000-0001-7542-0286
    affiliation: 2
  - name: Ana Claudia Sima
    orcid: 0000-0003-3213-4495
    affiliation: 3
  - name: Last Author
    orcid: 0000-0000-0000-0000
    affiliation: 3
affiliations:
  - name: First Affiliation
    index: 1
  - name: Dept of Bioinformatics - BiGCaT, NUTRIM, FHML, Maastricht University
    index: 2
  - name: SIB Swiss Institute of Bioinformatics
    index: 3
date: 2 November 2023
cito-bibliography: paper.bib
event: BH23EU
biohackathon_name: "BioHackathon Europe 2023"
biohackathon_url:   "https://biohackathon-europe.org/"
biohackathon_location: "Barcelona, Spain, 2023"
group: Project 17
# URL to project git repo --- should contain the actual paper.md:
git_url: https://github.com/BioDataFuse/biohackarvix-2023/
# This is the short authors description that is used at the
# bottom of the generated paper (typically the first two authors):
authors_short: First Author \emph{et al.}
---


# Introduction

As part of the BioHackathon Europe 2023, we here report...

# Results

## Wikidata annotator

A annotator for Wikidata has been developed. It support extraction of literature and cellular components information
about genes. The first method (`get_gene_literature`) returns the PubMed and Wikidata identifiers for the article that has the gene or
encoded protein as main subject. The second method (`get_gene_cellular_component`) returns the Wikidata identifier and
label of the cellular component as well as its Gene Ontology term.

Technically, the annotator runs a SPARQL query against the Wikidata Query Service. Genes and proteins are found based
on the NBCI Gene identifier.

## Bgee annotator

An annotator for Bgee was developed. It supports retrieving the expression level score and confidence from the Bgee SPARQL endpoint for a given set of genes and anatomical entities where the genes might be expressed. The SPARQL endpoint is queried using Ensembl identifiers for genes and the anatomical entity names in plain text.

# Discussion

...

## Acknowledgements

...

## References
