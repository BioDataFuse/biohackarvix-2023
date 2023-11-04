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
  - name: Dominik Martinat
    orcid: 0000-0001-6611-7883
    affiliation: 4
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
  - name: Dept of Physical Chemistry, Faculty of Science, Palacky University Olomouc, CZ
    undex: 4
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

# Methods

BridgeDb ... [@usesMethodIn:Willighagen2022BridgeDb] ...

# Annotators

## Wikidata annotator

A annotator for Wikidata has been developed. It support extraction of literature and cellular components information
about genes. The first method (`get_gene_literature`) returns the PubMed and Wikidata identifiers for the article that has the gene or
encoded protein as main subject. The second method (`get_gene_cellular_component`) returns the Wikidata identifier and
label of the cellular component as well as its Gene Ontology term.

Technically, the annotator runs a SPARQL query against the [Wikidata Query Service](https://query.wikidata.org/). Genes and proteins are found based
on the NBCI Gene identifier. It takes advantage of earlier work to include genes and proteins to Wikidata [@usesDataFrom:Waagmeester2020Wikidata].

## Bgee annotator

An annotator for Bgee was developed. It supports retrieving the expression level score and confidence from the [Bgee SPARQL endpoint](https://www.bgee.org/resources/sparql) for a given set of genes and anatomical entities where the genes might be expressed. The SPARQL endpoint is queried using Ensembl identifiers for genes and the anatomical entity names in plain text.

## MolMeDB annotator
An annotator for MolMeDB was developed [@usesDataFrom:Juracka2019MolMeDB]. It supports extraction of information on transporter inhibitors.

First method (`get_gene_mol_inhibitor`) takes gene or protein identifiers as an input and outputs inhibitors. Inhibitor is described by name, SMILES, InChIKey and external identifiers (Pubchem Compound, ChEMBL Compound, ChEBI, DrugBank, MolMeDB molecule) where applicable. Part of output is DOI and PMID identifier for data source.

Second method (`get_mol_gene_inhibitor`) takes metabolite identifier as an input and outputs transporters inhibited by it. Transporter is described bu Uniprot TrEMBL id and HGNC id. Part of output is DOI and PMID identifier for data source.

Annotator uses [IDSM](https://idsm.elixir-czech.cz/) service to query MolMeDB SPARQL endpoint.

## collapse_data_sources method fix
There was an issue when in case of one-to-many mappings in BridgeDb (eg. HGCN to UniProt TrEMBL) (`collapse_data_sources`) returned multiple rows in a dataframe for single queried identifier. After the fix the method drops rows wiht duplicate identifiers and and response body. It also drops rows with duplicate identifier and empty response body.
There is still potentiall for it to return multiple rows with duplicate identifiers and non-duplicate non-empty response body. This issue have not been seen while testing, but it will need to be solved in the future.

## TODO PubChem annotator
PubChem annotator will be implemented in the few days from BioHackathon 2023 ending.

# Graph analysis
The data queried through the annotators is collected in a networkx MultiDiGraph, which is a graph that allows multiple edges between nodes. In the BioHackathon a few simple graph analysis methods have been explored, for example computing the gene associated with the most diseases (the max out degree node in the gene-disease subgraph).

A more generic implementation has been added to iterate through all edge types, which can be useful in order to generate e.g. specific analyses or visualizations depending on the type of connection between nodes (e.g. visualizing protein-protein interaction networks).

Finally, two simple use cases were explored for community detection on the STRING interaction subgraph and for link prediction on the gene - disease subgraph. However, the simple link prediction method used, based on [Jaccard coefficients](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.link_prediction.jaccard_coefficient.html#networkx.algorithms.link_prediction.jaccard_coefficient), requires an undirected graph, meaning it is not suitable for the gene-disease use case. In particular, the method predicts associations between different diseases, as opposed to gene - disease predictions. Therefore, as future work, a method specific to bipartite graphs should instead be investigated. An example to be further explored can be found [here](https://github.com/bi-graph/Bigraph). Futhermore, experiments with larger datasets would be required in the future.

# Discussion

...

## Acknowledgements

This work was supported by ELIXIR, the research infrastructure for life-science data.

## References
