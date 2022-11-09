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

### Static view

<details markdown="block">
<summary>
Project notebook (Markdown render)
</summary>
  {% include_relative project_notebook.md %}
</details>

### Interactive view (Notebook)

<a href="https://colab.research.google.com/github/ebi-ait/hca-ebi-dev-team/blob/feature%2Fdcp-834-programmatic-submissions/scripts/programmatic_submissions/programmatic_submissions.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

<a target="_blank" href="https://raw.githubusercontent.com/ebi-ait/ingest-programmatic-submissions/main/notebooks/create_project/programmatic_submissions.ipynb" download>Download Notebook</a>{: .btn }

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

All the schema fields that are accepted can be found under the [project type schema](https://github.com/HumanCellAtlas/metadata-schema/blob/master/json_schema/type/project/project.json).
Each field is self-describing, meaning that by looking at the schema, you should be able to figure out what it is and 
how to fill out the information for that field.

A very human-readable webpage is also built from the schemas and can be accessed here: [metadata dictionary](https://data.humancellatlas.org/metadata/dictionary/project/project)


### Project metadata peculiarities (Non-schema fields)

As stated previously, the project metadata schema captures some extra information with the goal to:
- Help the system understand and show the ownership/provenance/status of the metadata (`System`)
- Help prioritisation of the work for the HCA wranglers (`Prioritisation`)
- Understand the process the current project may go through (`Triage`)
- Display current landscape of HCA data quickly into the HCA DCP Project Catalogue (`Triage`)

There are many fields available outside of the `content` field; here, we will go one by one, explaining them in detail.

#### System fields

These fields will be **filled automatically**. You do not need to worry about them. Most of the fields are shared amongst
all entities in ingest; for more information, you can go back to [System-specific fields](../introduction/introduction.html#system-specific-fields).
Nonetheless, the project has 4 exclusive system fields that are unique to the project and showcase provenance 
information for the project metadata:

- `cataloguedDate`: Date at which the project was first exposed in the HCA DCP Project Catalogue.
- `publicationsInfo`: Information about the publication related to this dataset. This information is surfaced automatically from the publication-related schema fields.
- `hasOpenSubmission`: Boolean value, indicating if the project is linked to an open submission; more on submission statuses on the submissionEnvelopes document.
- `accession`: UI-focused field; for programmatic submissions, it is not necessary.

#### Prioritisation fields

As a user, you do not need to worry about these fields. Feel free to skip filling this information; this will be
used by the wranglers

- `primaryWrangler`: ID of the user that is mainly responsible for wrangling this data.
- `secondaryWrangler`: ID of another user related to this project; not necessarily the main person of contact.
- `wranglingState`: Wrangling status, which may be one of the following values:
   - New
   - Eligible
   - Not eligible
   - In progress
   - Stalled
   - Submitted
   - Published in DCP
   - Deleted
   - New suggestion
- `wranglingPriority`: Priority assigned to the dataset, from 1 (More important) to 3. 
- `wranglingNotes`: Free text string to include notes about the project.


#### Triage fields

- `dcpReleaseNumber`: Number of DCP release this has been released to.
- `projectLabels`: Label of the project. This field helps wranglers assess the route the data will go through.
- `isInCatalogue`: Boolean value, indicating if this project is to be surfaced in the catalogue. If `true`, the information will appear immediately in the project catalogue.
- `releaseDate`: Date of release of the data, in YYYY-MM-DDThh:mm:ssZ format
- `accessionDate`: Date by which the data wants to be archived, in YYYY-MM-DDThh:mm:ssZ format
- `dataAccess`: Type of access for the data from this dataset. Currently, we only accept raw data for full open access
datasets or count matrices/processed data for datasets that require controlled access. Its value is an object, that contains:
  - `type`: Value extracted from the following enum:
    - `All fully open`
    - `All managed access`
    - `A mixture of open and managed`
    - `it's complicated`
  - `notes`: a free string value for the user to put the data access restrictions in context.


The next set of fields are especially important, since they get **surfaced in the project catalogue**:
- `cellCount`: Number of cells that were newly generated in this dataset
- `identifyingOrganisms`: Array of strings, that can accept any of these values:
  - `Human`
  - `Mouse`
  - `Other`
- `technology`: Array of library preparation technologies that were used to generate the data for this project. 
It is an ontologised field, that only accepts terms under [library preparation](https://ontology.archive.data.humancellatlas.org/ontologies/efo/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FOBI_0000711)
. However, since it's outside of the schema, it is encapsulated with another option. The way to fill it:
  - `ontologies`: Array of objects, each following the [ontologies format](../introduction/introduction.html#ontologised-fields)
  - `others`: Array of free strings, in case the ontology value is not available for the technology
  Example:
```json
{"technology": 
  {"ontologies": [
    {
      "text": "10x 3' v2",
      "ontology": "EFO:0009899",
      "ontology_label": "10x 3' v2"
    }
  ]}, 
  "others": [
      "CoolTech"
  ]
}
```
- `organ`: Very similar to technologies, but only allowing the `ontologies` field. The ontology value is evaluated to be
a term under [anatomical entity](https://ontology.archive.data.humancellatlas.org/ontologies/hcao/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FUBERON_0001062).

## Creating your own project metadata

In the previous section, you have learnt about the project metadata schema, its allowed properties and how they are
supposed to be filled. With this knowledge in mind, you can create your own entity, that could be used to create a 
project.

There are no limits to how many projects you can create, so try to add and experiment! (But don't forget to delete them
afterwards!).

The only thing that you need to remember is to add the out-of-schema information afterwards. The easiest way to do that
is by retrieving the project (If not already retrieved) and modifying the information according to the guidelines of the
[project metadata peculiarities](#project-metadata-peculiarities) section.

## API utilities

### Searching for/Retrieving your project

If you have generated a project in ingest, but you have forgotten its UUID, there are a couple ways you can retrieve it
back:

- Using the IngestApi function: `IngestApi.get_user_projects`. This requires you to have set up your token beforehand, and
it will retrieve all the projects that you have created.
- Going to the `/user/projects/` endpoint in the API. If you send a GET request to this endpoint, with the token set-up,
you will be able to retrieve all the projects that are assigned to yourself.

If you remember the project UUID, you can also retrieve the project by calling `IngestApi.get_project_by_uuid(uuid)`

### Updating your project

Once you have the project again, updating the project is very simple with the IngestApi object:
- Updating metadata schema fields: Send a PATCH request with `{'content': {<fields + values you want to update>}}`, e.g.
`api.patch(url=ingest_project_url, json={"content": {"insdc_project_accessions": ["SRP000000"]}})`
- Updating non-metadata schema fields: Send a PUT request with an updated version of your project

When updating using the IngestApi.put/patch functions, remember to give the `project._links.self.href` url as the `url`
parameter and the content as the `json` parameter.

The response, if successful, will be the updated version of the project. You can inspect it to ensure the patch has
gone through.

The most common mistake is to not have your token set up; if you are getting 400 error responses, please check that
the token has not expired

### Deleting your project

If you want to delete your project, you just need to send a DELETE request to the URL of your project. Example: 
`api.delete(ingest_project_url)`.

The returning value will always be a response object; to ensure that it has been deleted successfully, you can assert
that it's a 204 response code by checking the object.

### Accessing other entities from the project

From the project's `_links` field, you can access a number of other entities related to this project:

- `self`: link to itself
- `project`: link to the project; equivalent to `self`
- `draft`: link to put the project in Draft status; requires an empty POST request.
- `supplementaryFiles`: Supplementary files associated with this project
- `submissionEnvelopes`: Submissions associated with this project
- `submissionEnvelope`: <span style="color:red">**_OUTDATED_**</span>, please ignore
- `bundleManifests`: <span style="color:red">**_OUTDATED_**</span>, please ignore
- `auditLogs`: <span style="color:red">**_OUTDATED_**</span>, please ignore


