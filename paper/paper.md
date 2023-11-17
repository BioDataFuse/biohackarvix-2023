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
  - name: Ammar Ammar
    orcid: 0000-0002-8399-8990
    affiliation: 2
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

As part of the Biohackathon Europe 2023 in Barcelona, Spain, a dedicated team of seven participants collaborated on Project 17, titled "Extending interoperability of experimental data using modular queries across biomedical resources." This project was multifaceted, aiming to address three key objectives in order to advance the capabilities of the pyBioDatafuse package.

Firstly, part of the team set out to explore new data sources, subsequently creating annotator modules that adhered to the established structure within the pyBioDatafuse package. These annotators sought to enhance the package's capabilities by integrating diverse and valuable data sources. The second major goal of the project was to devise a robust mechanism for transforming the resulting data frame from the annotation phase into a graph data structure. This intermediary step was carefully designed to be Python-native, enabling flexible programmable filtering and manipulation of the graph. Simultaneously, the graph structure was envisioned to be serializable into other formats compatible with third-party software, specifically, Cytoscape and Neo4j. This design aimed to achieve a balance between the flexibility of Python-native manipulation and the robust visualization and analytical capabilities offered by widely-used platforms like Cytoscape [ref] and Neo4j [ref]. Furthermore, the team's third objective was to extend the capabilities of pyBioDataFuse with visualization functions, leveraging the Matplotlib [ref] Python package. 

**WRITE A BIT MORE TEXT ON** the ultimate goal of making pyBioDataFuse a comprehensive toolkit for exploring, analyzing, and visualizing biomedical data across diverse biomedical resources + interoperability + modularity.

# Results

## Wikidata annotator

A annotator for Wikidata has been developed. It support extraction of literature and cellular components information
about genes. The first method (`get_gene_literature`) returns the PubMed and Wikidata identifiers for the article that has the gene or
encoded protein as main subject. The second method (`get_gene_cellular_component`) returns the Wikidata identifier and
label of the cellular component as well as its Gene Ontology term.

Technically, the annotator runs a SPARQL query against the [Wikidata Query Service](https://query.wikidata.org/). Genes and proteins are found based
on the NBCI Gene identifier. It takes advantage of earlier work to include genes and proteins to Wikidata [@usedDataFrom:Waagmeester2020Wikidata].

## Bgee annotator

An annotator for Bgee was developed. It supports retrieving the expression level score and confidence from the [Bgee SPARQL endpoint](https://www.bgee.org/resources/sparql) for a given set of genes and anatomical entities where the genes might be expressed. The SPARQL endpoint is queried using Ensembl identifiers for genes and the anatomical entity names in plain text.

## MolMeDB annotator
An annotator for MolMeDB was developed. It supports extraction of information on transporter inhibitors.

First method (`get_gene_mol_inhibitor`) takes gene or protein identifiers as an input and outputs inhibitors. Inhibitor is described by name, SMILES, InChIKey and external identifiers (Pubchem Compound, ChEMBL Compound, ChEBI, DrugBank, MolMeDB molecule) where applicable. Part of output is DOI and PMID identifier for data source.

Second method (`get_mol_gene_inhibitor`) takes metabolite identifier as an input and outputs transporters inhibited by it. Transporter is described bu Uniprot TrEMBL id and HGNC id. Part of output is DOI and PMID identifier for data source.

Annotator uses [IDSM](https://idsm.elixir-czech.cz/) service to query MolMeDB SPARQL endpoint.

## collapse_data_sources method fix
There was an issue when in case of one-to-many mappings in BridgeDB (eg. HGCN to Uniprot TrEMBL) (`collapse_data_sources`) returned multiple rows in a dataframe for single queried identifier. After the fix the method drops rows wiht duplicate identifiers and and response body. It also drops rows with duplicate identifier and empty response body.
There is still potentiall for it to return multiple rows with duplicate identifiers and non-duplicate non-empty response body. This issue have not been seen while testing, but it will need to be solved in the future.

## TODO PubChem annotator
PubChem annotator will be implemented in the few days from BioHackathon 2023 ending.

## Knowledge graph construction

A Python module was developed to create a graph data structure from a data frame that contains information about genes and their annotations from various biomedical sources. The resulting graph is constructed using the NetworkX [ref] library in Python and can be exported for visualization in tools like Cytoscape or Neo4J.

The data, previously annotated with diverse sources like drugs, diseases, and gene ontology terms, is loaded into the script from a data frame object. The script then systematically builds a NetworkX graph, representing relationships between genes and different biomedical entities. The generated graph exhibits a rich structure (Figure \ref{fig-graph-model}), capturing relationships between genes and various biomedical entities. Nodes in the graph represent different types of biological elements, such as genes, diseases, drugs, gene ontology terms, locations, and pathways. The edges between nodes signify diverse interactions and associations, including gene-disease relationships, gene-pathway connections, and gene-drug interactions. Each node is enriched with attributes that detail its origin and provide specific information relevant to its biological context. For instance, gene nodes include details about the gene's source, identifiers, and associated attributes, while disease nodes incorporate information like disease identifiers, therapeutic areas, and semantic types. This comprehensive structure facilitates a high-level understanding of the complex interplay between different biomedical elements. 

The python-native graph data structure obtained using NetworkX allows for programmatic filtering and manipulation of the graph. At the same time, the graph serves as an intermediate step that can then be serialized into two other formats. The first format is Cytoscape compliant JSON which can be loaded into Cytoscape. The second format is GraphML which can loaded into Neo4J using the APOC extension [ref].

Cytoscape, a powerful visualization tool, can leverage the JSON export of the graph to create visually compelling representations. In Cytoscape, researchers can explore the graph interactively, allowing for the identification of key hubs, densely connected regions, and patterns within the biomedical network. Moreover, the graph's compatibility with Neo4J, a graph database, opens up possibilities for advanced analysis and querying. Exporting the graph to GraphML format allows researchers to load it into Neo4J, where nodes and relationships become elements of a property graph. Neo4J's query language, Cypher, enables researchers to perform complex queries, extract specific subgraphs, and uncover hidden patterns within the data. This functionality is particularly valuable for in-depth investigations into the relationships and attributes of genes, diseases, and other biomedical entities.

A specific module was developed to export the NetworkX graph to GraphML and load it into a live deployment of Neo4J. The related function in this module connects to the Neo4J database using specified credentials (username and password) and a provided URI (Uniform Resource Identifier). The graph is then imported into the Neo4J database, where each node is assigned a specific label based on its type. For instance, genes are labeled as "Gene," diseases as "Disease," and so on. Similarly, edge types, representing relationships between nodes, are assigned specific labels. For example, if an edge represents a gene-disease association, it is labeled as "associated_with." This step ensures that relationships are clearly defined within the Neo4J database. In the upcoming weeks after the BioHackathon, more work will be dedicated towards implementing similar functionality targeted at loading the graph into a live Cytoscape instance along with applying proper styling for enhanced visualization and better user experience.



![The pyBioDatafuse knowledge graph model \label{fig-graph-model}](./figures/pybiodatafuse-graph.png)





## Graph analysis

The data queried through the annotators is collected in a networkx MultiDiGraph, which is a graph that allows multiple edges between nodes. In the BioHackathon a few simple graph analysis methods have been explored, for example computing the gene associated with the most diseases (the max out degree node in the gene-disease subgraph).

A more generic implementation has been added to iterate through all edge types, which can be useful in order to generate e.g. specific analyses or visualizations depending on the type of connection between nodes (e.g. visualizing protein-protein interaction networks).

Finally, two simple use cases were explored for community detection on the STRING interaction subgraph and for link prediction on the gene - disease subgraph. However, the simple link prediction method used, based on [Jaccard coefficients](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.link_prediction.jaccard_coefficient.html#networkx.algorithms.link_prediction.jaccard_coefficient), requires an undirected graph, meaning it is not suitable for the gene-disease use case. In particular, the method predicts associations between different diseases, as opposed to gene - disease predictions. Therefore, as future work, a method specific to bipartite graphs should instead be investigated. An example to be further explored can be found [here](https://github.com/bi-graph/Bigraph). Futhermore, experiments with larger datasets would be required in the future.

# Discussion

...

## Acknowledgements

...

## References
