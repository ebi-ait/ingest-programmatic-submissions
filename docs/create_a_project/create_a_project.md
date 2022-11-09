---
layout: default
title: Create a project
parent: Submission guidelines
nav_order: 2
---
<ul>
{% assign project = site.data.submission_example.project.example_project %}
</ul>

# Create a project in ingest 
{: .no_toc }


## Purpose of this document
{: .no_toc }
This document is intended to give an introduction to the HCA ingest service, specifically targeting data and metadata in
the system and how they interact in the ingest ecosystem of data.

These documents will be coupled with a set of python notebooks, which will show examples of how to interact with the data.

This is the **create a project** doc, which will explain how to create, modify and delete a project in ingest.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Step by step

In this section, a rendering of a python notebook showing the steps to create a project, step by step, can be expanded
from below. 

### Static view (HTML render)

<details markdown="block">
<summary>
Project notebook (HTML render)
</summary>
{% include create_a_project.html %}
</details>

### Interactive view (Notebook)

<a href="https://colab.research.google.com/github/ebi-ait/hca-ebi-dev-team/blob/feature%2Fdcp-834-programmatic-submissions/scripts/programmatic_submissions/programmatic_submissions.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

<a target="_blank" href="https://raw.githubusercontent.com/ebi-ait/ingest-programmatic-submissions/main/notebooks/1_create_project/programmatic_submissions.ipynb" download>Download Notebook</a>{: .btn }

## Understanding the metadata

In the example above, this is the metadata that we have initially sent:
<details >
<summary>
Project JSON metadata
</summary>
{% capture code_fence %}
```json
{
  "describedBy": "https://schema.staging.data.humancellatlas.org/type/project/17.0.0/project",
  "schema_type": "project",
  "project_core": {
    "project_short_name": "myCoolLabel",
    "project_title": "Test_project_with_minimum_information",
    "project_description": "This is a test project with minimum information for the programmatic submissions guide"
  },
  "contributors": [
    {
      "name": "Enrique,,Ventura",
      "email": "enrique@ebi.ac.uk",
      "institution": "EMBL-EBI",
      "corresponding_contributor": true,
      "project_role": {
        "text": "data curator",
        "ontology": "EFO:0009737",
        "ontology_label": "data curator"
      }
    }
  ],
  "publications": [
    {
      "authors": [
        "Lorem IP",
        "Sed UP"
      ],
      "title": "A combined approach for single-cell mRNA and intracellular protein expression analysis",
      "url": "https://www.frontiersin.org/articles/10.3389/fcell.2020.00384/full",
      "official_hca_publication": false
    }
  ],
  "funders": [
    {
      "grant_title": "a cool grant",
      "grant_id": "000000000bp1",
      "organization": "EMBL-EBI"
    }
  ]
}
```
{% endcapture %} {% assign code_fence = code_fence | markdownify %} {% include fix_linenos.html code=code_fence %}
</details>

This piece of metadata, in JSON format, is what we call an **instance** of a schema. These instances point out to the 
schema it should be validated against, in this case, the [project v17.0.0](https://schema.staging.data.humancellatlas.org/type/project/17.0.0/project) schema.

This schema validates all the necessary information, and this information is what will be submitted downstream (e.g. to
the HCA-DCP Data portal). However, the project JSON object has another type of metadata. This metadata is mainly
used by the ingest system, both to filter datasets within the system (triage operations) and to expose minimal information
about datasets that are of interest to the Human Cell Atlas ([HCA Project catalogue](https://www.ebi.ac.uk/humancellatlas/project-catalogue/))

In the following subsections, there will be an explanation on how to understand specifically the project schema and how
to expand on it.


### Schema fields

All the schema fields that are accepted can be 

- Metadata schema dictionary - Project
- Metadata schema - project.json


### Project metadata peculiarities

- Information outside of the schema
- Why is it important to be filled out
- 


## Creating your own project metadata

### The project metadata schema

## Project metadata peculiarities

