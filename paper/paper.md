---
title: 'BioHackEU23 report: Extending interoperability of experimental data using modular queries across biomedical resources'
title_short: 'BioHackEU23 #17: BioDataFuse'
tags:
  - Knowledge Graph
  - Modular query
  - Biomedical data source
authors:
  - name: Yojana Gadiya
    orchid: 0000-0002-7683-0452
    affiliation: 1
  - name: Ammar Ammar
    orcid: 0000-0002-8399-8990
    affiliation: 2
  - name: Egon Willighagen
    orcid: 0000-0001-7542-0286
    affiliation: 2
  - name: Dominik Martinat
    orcid: 0000-0001-6611-7883
    affiliation: 3
  - name: Ana Claudia Sima
    orcid: 0000-0003-3213-4495
    affiliation: 4
  - name: Hasan Balci
    orcid: 0000-0001-8319-7758
    affiliation: 5
  - name: Tooba Abbassi-Daloii
    orcid: 0000-0002-4904-3269
    affiliation: 2
affiliations:
  - name: Fraunhofer Institute for Translational Medicine and Pharmacology, Germany
    index: 1
  - name: Dept of Bioinformatics - BiGCaT, NUTRIM, FHML, Maastricht University
    index: 2
  - name: Dept of Physical Chemistry, Faculty of Science, Palacky University Olomouc, CZ
    index: 3
  - name: SIB Swiss Institute of Bioinformatics
    index: 4
  - name: Maastricht Centre for Systems Biology (MaCSBio), Maastricht University, The Netherlands
    index: 5
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
authors_short: Abbassi-Daloii, T., Gadiya, Y. \emph{et al.}
---


# Introduction

As part of Biohackathon Europe 2023 in Barcelona, Spain, a dedicated team of seven participants collaborated on Project 17, titled *"Extending interoperability of experimental data using modular queries across biomedical resources"*.

During the biohackathon, we developed a tool named **BioDataFuse** to seamlessly integrate data resources on the fly, thus establishing a modular framework for data wrangling. By following the what-you-see-is-what-you-get (WYSIWYG) convention, BioDataFuse enables users to choose "how" the graph is built based on strategic decisions regarding the data sources of interest. Thus, it creates context-specific knowledge graphs around the user data.

The tool consists of a backend and a front-end framework. The backend comprises a Python package, namely [pyBioDataFuse](https://pypi.org/project/pyBiodatafuse/), which assists in supporting various tool functionalities, whereas the user interface acts as the front-end, enhancing the tool's utility for non-programers.

# Goals for the biohackathon

This project was multifaceted, and our objectives were to address the following four key aspects to advance the capabilities of *BioDataFuse*.

1. **Extending data resources**: Part of the team set out to explore new data sources, subsequently creating annotator modules that adhered to the established structure within the *pyBioDataFuse* package. These annotators sought to enhance the package's capabilities by integrating diverse and valuable biomedical data sources.
2. **Generation of knowledge graphs**: The second major goal of the project was to devise a robust mechanism for transforming the collected data from the annotation phase into a graph data structure. This intermediary step was carefully designed to be Python-native, enabling flexible programmable filtering and manipulation of the graph. This design aimed to achieve a balance between the flexibility of Python-native manipulation and the robust visualization and analytical capabilities offered by widely-used platforms like [Cytoscape](https://cytoscape.org/) [@usesMethodIn:Shannon2003] and [Neo4j](https://neo4j.com/) [@usesMethodIn:noauthororeditorneo4j].
3. **Data visualization**: Acknowledging the ability of graphical representation of data, we wanted to extend the capabilities of *pyBioDataFuse* with visualization functions for a quick summary of the underlying graph data.
4. **Use case based pipeline**: We wanted to build an end-to-end pipeline to test and support more types of input for the tool, including, but not limited to, metabolites, and the output table of the differential expression analysis.

The ultimate goal of the *BioDataFuse* project is to evolve into a comprehensive toolkit that empowers users to explore, interpret, and visualize biomedical data seamlessly across a myriad of diverse biomedical resources. By fostering interoperability, *BioDataFuse* ensures that users can effortlessly navigate and extract meaningful insights from their context-specific biomedical graph.

# BioDataFuse framework

Based on our goals described in the previous section, we built *BioDataFuse* on four main components (Figure \ref{fig-framework}):

1. Data Harmonizer
2. Data Annotators
3. Graph Generator
4. Graph Analyzer 

![The BioDataFuse framework \label{fig-framework}](./figures/pyBiodatafuse-pipeline.png)

## Data Harmonizer

Adhering to the FAIR principles, the tool emphasizes the necessity of unique persistent identifiers for diverse biomedical modalities such as genes, chemicals, diseases, etc. Consistent mapping of these entities is achieved through the utilization of the [BridgeDb framework](https://www.bridgedb.org/) [@usesMethodIn:vanIersel2010BridgeDb].

Moreover, we expanded the capabilities *pyBioDataFuse* to support not only a list of genes as input but also a list of metabolites and the output table of the differential expression analysis. 

The harmonizer step works as the basis for biological entity grounding and mapping and servers as input for the data annotators, as shown in Figure \ref{fig-framework}.

## Data Annotators

We added seven biomedical data annotators that allow for performing modular queries simultaneously across these resources. The initial selection of these data sources was guided by their content, providing users with the ability to extend the graph based on diseases, compounds or metabolites, biological processes, protein interactions, and scientific publications.

To concurrently query these resources, **S**PARQL **P**rotocol **A**nd **R**DF **Q**uery **L**anguage ([SPARQL](https://www.w3.org/TR/sparql11-query/)).

In the following subsections, we provide a detailed overview of the integrated data annotators along with their capabilities in *BioDataFuse*:

### Wikidata annotator

[Wikidata](https://www.wikidata.org/) is a structured data resource benefited by cross sourced curation by individuals and experts. In the context of biology, it supports extraction of literature and cellular components information about genes [@usesDataFrom:Waagmeester2020Wikidata].

Technically, we execute a SPARQL query against the [Wikidata Query Service](https://query.wikidata.org/) using the [NCBI Gene](https://www.ncbi.nlm.nih.gov/gene) identifier for genes and proteins of interest. Two methods have been added for data retrieval: the first, `get_gene_literature()`, provides the [PubMed](https://pubmed.ncbi.nlm.nih.gov/) and Wikidata identifiers for the article that has the gene or encoded protein as the main subject. The second, `get_gene_cellular_component()`, returns the Wikidata identifier and label of the cellular component along with its corresponding [Gene Ontology (GO)](https://geneontology.org/) term.

Thus, this annotator allows us to gather scientific publications and information on the cellular location of the genes or proteins of interest.

### Bgee annotator

[Brain Gene Expression Evolution (Bgee)](https://www.bgee.org/) is a gene expression data resource that captures and collects data from multiple organisms, with a focus on vertebrates [@usesDataFrom:Bastian2020]. All data collected in the resource are curated by experts, adding confidence to the underlying data.

The annotator supports the retrieval of expression level scores and confidence (via the `get_gene_expression()` function) from the [Bgee SPARQL endpoint](https://www.bgee.org/resources/sparql) for a given set of genes and anatomical entities where the genes might be expressed. To perform the SPARQL query, [Ensembl](https://www.ensembl.org/index.html) identifiers for genes and anatomical entities are used.

Through this annotator, we extract the expression profile of genes in multiple organisms, especially mice, rats, and humans. This allows users to compare expression data across laboratory organism species that are prominently used in safety and efficacy monitoring of drugs [@usesDataFrom:euclinicaltrials].

### MolMeDB annotator

[Molecules on Membranes Database (MolMeDB)](https://molmedb.upol.cz/) is an extensive compound-membrane interactome database that is manually curated and regularly updated with scientific literature [@usesDataFrom:Juracka2019MolMeDB]. 

The annotator for this resource supports the extraction of information on transporter protein inhibitors around compounds or metabolites of interest. It utilizes the [IDSM](https://idsm.elixir-czech.cz/) service, the SPARQL endpoint of MolMeDB, for querying. MolMeDB has a two-way annotation profile that allows capturing molecule inhibitors for genes or vice versa, i.e., genes inhibited by molecules. Hence, we developed two methods, namely `get_gene_mol_inhibitor()` and `get_mol_gene_inhibitor()`, to maximize the utility of MolMeDB. 

For the first method, [UniProt](https://www.uniprot.org/) TrEMBL and/or [HGNC](https://www.genenames.org/) identifiers are used as input, and metabolite/compound inhibitors are described by their name, SMILES, InChIKey, and external identifiers found in [PubChem](https://pubchem.ncbi.nlm.nih.gov/), [ChEMBL](https://www.ebi.ac.uk/chembl/), [ChEBI](https://www.ebi.ac.uk/chebi/), [DrugBank](https://go.drugbank.com/), and MolMeDB. Additionally, the literature reference (in the form of DOI or PubMed identifiers) from which the data was captured is also provided. For the second method, metabolites or compounds are searched based on InChIKey, as SMILES are not unique for compounds.

Thus, this resource enables the capturing of membrane interacting molecules, allowing for a deeper dive into the adsorption profile of compounds of interest.

### Open Targets annotator

[Open Targets](https://www.opentargets.org/) is a public-private partnership project that focuses on drug target identification and prioritization to advance drug discovery and development [@usesDataFrom:Ochoa2022]. It achieves this by integrating large-scale omics data, such as functional genomics and chemogenomics, with drug and pathway-based information.

The annotator is built on the [GraphQL API](https://platform-docs.opentargets.org/data-access/graphql-api) of the [Open Targets Platform](https://platform.opentargets.org/). Given the different dimensions of data that can be extracted, we created a large number of functions to extract relevant gene information, including its tractability (`get_gene_tractability()`), its involvement in different biological pathways (from [Reactome](https://reactome.org/) and GO with `get_gene_reactome_pathways()` and `get_gene_go_process()` respectively), the diseases it is prominently correlated with (`get_gene_disease_associations()`), as well as the drugs that target the gene (`get_gene_drug_interactions()`). For each of the mentioned queries, the Ensembl identifier for the gene is used.

Overall, this annotator serves as the basis for the enrichment of gene-related metadata from different modalities. Furthermore, since the data in Open Targets is integrated from more than ten resources, it allows us to query one consolidated resource instead of ten.

### DisGeNET annotator

[DisGeNET](https://www.disgenet.org/) is a knowledge platform that integrates information on the genetic basis of human diseases, providing valuable data on the relationships between genes and diseases and facilitating the exploration of molecular mechanisms underlying various disorders [@usesDataFrom:QueraltRosinach2016DisGeNETRDF]. 

Using the [DisGeNET API](https://www.disgenet.org/api/#/gene), we developed the annotator. The `get_gene_disease()` method, designed for a given set of Entrez/NCBI Gene identifiers, extracts diseases related to the gene of interest. Alongside disease information, the annotator retrieves metadata about the edge connection, including the source from which the data was extracted, year, and the DisGeNET score for the connection. 

This resource enables the annotation of genetically related diseases for genes, and the inclusion of selection thresholds allows for filtering diseases based on metadata.

### WikiPathways annotator

In addition to Reactome and GO databases, another prominent resource for pathways is [WikiPathways](https://www.wikipathways.org/). This open, collaborative platform follows a community-driven approach to curate and disseminate biological pathways [@usesDataFrom:Agrawal2023WikiPathways].

The Wikipathways annotator (`get_gene_wikipathway()`) features a main function that queries the WikiPathways SPARQL endpoint for pathways related to a set of input genes (Ensembl/NCBI gene identifiers). It prepares the gene list, constructs SPARQL queries using predefined templates, and executes them in batches for efficiency. The results are collected in a list of dataframes, later merged to create a comprehensive output dataframe. The query returns pathway ID, label, and the number of genes in the pathway. 

This resource enables the annotation of genes with related pathway information and allows for filtering pathways based on their size.

### STRING annotator

[Search Tool for the Retrieval of Interacting Genes/Proteins (STRING)](https://string-db.org/) is a bioinformatics database and web resource focusing on the exploration and analysis of protein-protein interactions (PPIs) [@usesDataFrom:Szklarczyk2023stringdb]. It collects this information based on functional associations between proteins, complementing genetic associations.

The core of this annotator lies in the `get_ppi()` function. It starts by preparing the API call based on the input list of genes. Subsequently, it leverages the STRING API to obtain STRING identifiers corresponding to the input genes. These identifiers are then used to fetch the protein-protein interaction network from the API. To format the obtained interaction data appropriately, the `_format_data()` helper function is employed. It organizes the interaction details for each gene, ensuring a clear representation in the resulting dataframe. The annotated data, now enriched with protein-protein interaction information, is returned along with metadata. The metadata includes details such as the size of the queried gene list, query execution time, and the date of the query. Notably, it also includes the version information of the STRING API obtained through the `get_version_stringdb()` function.

## Graph generator

The graph generator plays a fundamental role in creating the knowledge graph (KG) from the data captured by annotators. This process allows for the visualization of complex connections within a dataset. The KG is comprised of nodes representing entities or data points and edges indicating relationships or interactions. This clear and structured representation reveals patterns, clusters, or trends within interconnected datasets, providing valuable insights for researchers, analysts, and decision-makers. 

### Schema of the graph

We developed a Python module in *pyBioDataFuse* (`class pyBiodatafuse.graph()`) to construct a graph data structure from the aggregate data (Figure \ref{fig-graph-model}). This module, leveraging the [NetworkX](https://networkx.org/) [@usesMethodIn:SciPyProceedings_11] library, generates the graph and seamlessly exports it to Cytoscape and Neo4j for visualization. Within the KG, nodes represent distinct biological entities such as genes, diseases, drugs, and pathways. Each node is enriched with attributes (metadata), providing details about its origin. For example, gene nodes include information about the gene's source, identifiers, and associated attributes. The edges connecting the nodes signify diverse interactions and associations, encompassing gene-disease relationships, gene-pathway connections, and gene-drug interactions. Edge nomenclature adheres to the RDF format, ensuring a structured representation for a comprehensive understanding of the complex interplay between different biomedical elements.

![The pyBioDataFuse graph schema \label{fig-graph-model}](./figures/pybiodatafuse-graph.png)

### Input-output (IO) dynamics for the graph

The python native graph data structure, obtained using NetworkX, enables programmatic filtering and manipulation of the graph. Simultaneously, the graph serves as an intermediary stage, ready for serialization into two alternative formats. The first format adheres to Cytoscape's specifications in JSON, facilitating seamless integration with Cytoscape. The second format is GraphML, compatible with Neo4j, and can be effortlessly loaded using the APOC extension [@usesMethodIn:neo4japoc].

A dedicated module was developed to export the KG to GraphML and load it into a live deployment of Neo4j (`graph.neo4j_exporter.export()`). This function establishes a connection to the Neo4j database through the Neo4j Python driver, using user specified credentials (i.e., username and password) and a provided URI (Uniform Resource Identifier) pointing to the active/running database instance. Subsequently, the graph is imported into the Neo4j database via the import functionality of the Neo4j APOC plugin, with each node being assigned a specific label based on its type. 

## Graph Analyzer

The graph analyzer provides a quick summary of the data generated by the *BioDataFuse* pipeline. To achieve this, we leverage Python packages such as [Matplotlib](https://matplotlib.org/) [@usesMethodIn:Hunter2007], [Seaborn](https://seaborn.pydata.org/) [@usesMethodIn:Waskom2021], and [Plotly](https://plotly.com/). Using these tools, we built basic plots, including a pie chart (`plot_pie_chart()` and `plotly_pie_chart()`) and bar plots(`plot_hbarplot_chart()` and `plotly_barplot_chart()`).

Additionally, a more generic network-specific summary was also incorporated. This was achieved by iterating through all node and edge types, providing a useful approach for generating data type specific summaries. For instance, it allows visualizing protein-protein interaction networks and obtaining node statistics.

## Pipeline testing

To ensure the smooth operation of all functions outlined in the previous sections and to detect any functional failures, we implemented test scripts for each resource. These test scripts guarantee a stable connection between resources for query. Additionally, testing has been integrated into our [GitHub Actions](https://docs.github.com/en/actions/using-workflows), enabling automated connectivity tests with all annotators each time a push is made to the main repository. This provides an efficient way to identify and address any potential connection issues.

During the initial testing of the resources, we identified cases of one-to-many mappings for genes in BridgeDb (e.g., from HGNC to UniProt TrEMBL). We promptly raised this issue with the BridgeDB developers, who are also co-authors of this paper/project, and it is currently being investigated for resolution.

## User interface

In the front end, a user interface developed using [Streamlit](https://streamlit.io/). Users can input a list of genes, specify the type of identifier for those genes, and then select data sources to annotate the input list. After querying the chosen databases, users can download a TSV file with columns associated with each data source and a metadata file containing information about the version of the queried data sources, input and query details, and the time taken to complete each query, all in a JSON format. If the Cytoscape app is running on the user's end, the generated graph will be imported there (usning  library) and can also be visualized in Cytoscape.

However, during the hackathon, a decision was made to transition away from Streamlit and explore other frameworks such as [Shiny](https://shiny.posit.co/). Consequently, updates and developments in pyBioDataFuse, utilized in the backend, have not been implemented in the Streamlit app.

## Future work

The following are the planned future directions of the work:

- Supporting more types of input data (e.g., gene co-expression data).
- Addition of annotators from drug databases like [PubChem](https://pubchem.ncbi.nlm.nih.gov/) and [ChEMBL](https://www.ebi.ac.uk/chembl/).
- Connection to larger data repositories like those hosted in the [Database Center for Life Science (DBCLS)](https://dbcls.rois.ac.jp/index-en.html) into *BioDataFuse*.
- Continuous updating and versioning of [*pyBioDataFuse*](https://pypi.org/project/pyBiodatafuse/) at PyPi with the above-described annotators.
- The current user interface is based on Streamlit, but to enhance its functionality, we would migrate towards [Shiny](https://shiny.posit.co/) or [Dash](https://plotly.com/dash/) frameworks.
- Adapt the live loading of graph onto Cytoscape similar to the approach used in Neo4j.

# Discussion

During the BioHackathon, our team accomplished significant milestones with *BioDataFuse*. The tool now supports additional input types and incorporates a more extensive range of biomedical data sources. The creation of context-specific knowledge graphs is a notable achievement, and these graphs can be visualized in both Cytoscape and Neo4j, enhancing accessibility and usability.

However, in terms of the graph analyzer component, there are areas that still require attention. We acknowledge the importance of refining and expanding this aspect of the tool. Through successful collaborations initiated during the Biohackathon, we aim to improve not only the graph analyzer but also other components by implementing the workflow on specific use cases. This collaborative effort is pivotal in addressing remaining tasks and ensuring the comprehensive functionality of BioDataFuse.

Furthermore, the user interface requires additional refinement. We plan to transition away from Streamlit and explore alternative tools such as Dash to improve functionality and enhance the user experience.

# Contributions to the package

We warmly welcome community contributions to enhance the package. Interested contributors can participate by parsing and adding relevant annotators, conducting visual analysis on the graph or data, or providing advice on the development of a stable web interface for *BioDataFuse*. If you find this interesting, please feel free to open an issue on our [GitHub repo](https://github.com/BioDataFuse/pyBiodatafuse), and we would be delighted to discuss future prospects with you. 

## Acknowledgements

This work was supported by ELIXIR, the research infrastructure for life-science data. We thank the organizers of the BioHackathon Europe 2023 for providing travel support for the project leads.

## References
