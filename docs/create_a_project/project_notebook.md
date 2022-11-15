# Create a project

This notebook is intended to give an insight into how to generate a submission by using the python tools available, the `hca-ingest` library. This library, amongst other utilities for interacting with the Ingest service, contains a wrapper for Ingest's API, which lets you easily create, update and delete.

This section will be focused around `Projects`

## Download example project
In order to have the files necessary for this guide, we're going to download the template file for the project metadata


```python
!wget https://raw.githubusercontent.com/ebi-ait/ingest-programmatic-submissions/main/_data/submission_example/project/example_project.json
```

    --2022-11-09 17:32:55--  https://raw.githubusercontent.com/ebi-ait/ingest-programmatic-submissions/main/_data/submission_example/project/example_project.json
    Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.108.133, 185.199.110.133, 185.199.111.133, ...
    Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.108.133|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1232 (1.2K) [text/plain]
    Saving to: ‘example_project.json’
    
    example_project.jso 100%[===================>]   1.20K  --.-KB/s    in 0s      
    
    2022-11-09 17:32:55 (34.3 MB/s) - ‘example_project.json’ saved [1232/1232]
    


## Set up  libraries and dependencies
### Install external libraries

```python
!pip install hca-ingest
```

    Looking in indexes: https://pypi.org/simple, https://us-python.pkg.dev/colab-wheels/public/simple/
    Collecting hca-ingest
      Downloading hca-ingest-2.6.0.tar.gz (57 kB)
    [K     |████████████████████████████████| 57 kB 2.7 MB/s 
    [?25hCollecting jsonref
      Downloading jsonref-1.0.1-py3-none-any.whl (9.5 kB)
    Requirement already satisfied: openpyxl in /usr/local/lib/python3.7/dist-packages (from hca-ingest) (3.0.10)
    Collecting polling
      Downloading polling-0.3.2.tar.gz (5.2 kB)
    Requirement already satisfied: PyYAML>=5.3.1 in /usr/local/lib/python3.7/dist-packages (from hca-ingest) (6.0)
    Requirement already satisfied: requests[security] in /usr/local/lib/python3.7/dist-packages (from hca-ingest) (2.23.0)
    Collecting xlsxwriter
      Downloading XlsxWriter-3.0.3-py3-none-any.whl (149 kB)
    [K     |████████████████████████████████| 149 kB 10.9 MB/s 
    [?25hCollecting mergedeep
      Downloading mergedeep-1.3.4-py3-none-any.whl (6.4 kB)
    Collecting cryptography
      Downloading cryptography-38.0.3-cp36-abi3-manylinux_2_24_x86_64.whl (4.1 MB)
    [K     |████████████████████████████████| 4.1 MB 43.8 MB/s 
    [?25hCollecting requests-cache
      Downloading requests_cache-0.9.7-py3-none-any.whl (48 kB)
    [K     |████████████████████████████████| 48 kB 4.6 MB/s 
    [?25hRequirement already satisfied: cffi>=1.12 in /usr/local/lib/python3.7/dist-packages (from cryptography->hca-ingest) (1.15.1)
    Requirement already satisfied: pycparser in /usr/local/lib/python3.7/dist-packages (from cffi>=1.12->cryptography->hca-ingest) (2.21)
    Requirement already satisfied: et-xmlfile in /usr/local/lib/python3.7/dist-packages (from openpyxl->hca-ingest) (1.1.0)
    Collecting urllib3>=1.25.5
      Downloading urllib3-1.26.12-py2.py3-none-any.whl (140 kB)
    [K     |████████████████████████████████| 140 kB 72.8 MB/s 
    [?25hRequirement already satisfied: appdirs>=1.4.4 in /usr/local/lib/python3.7/dist-packages (from requests-cache->hca-ingest) (1.4.4)
    Collecting cattrs>=22.2
      Downloading cattrs-22.2.0-py3-none-any.whl (35 kB)
    Requirement already satisfied: attrs>=21.2 in /usr/local/lib/python3.7/dist-packages (from requests-cache->hca-ingest) (22.1.0)
    Collecting url-normalize>=1.4
      Downloading url_normalize-1.4.3-py2.py3-none-any.whl (6.8 kB)
    Collecting exceptiongroup
      Downloading exceptiongroup-1.0.1-py3-none-any.whl (12 kB)
    Requirement already satisfied: typing_extensions in /usr/local/lib/python3.7/dist-packages (from cattrs>=22.2->requests-cache->hca-ingest) (4.1.1)
    Requirement already satisfied: idna<3,>=2.5 in /usr/local/lib/python3.7/dist-packages (from requests[security]->hca-ingest) (2.10)
    Requirement already satisfied: chardet<4,>=3.0.2 in /usr/local/lib/python3.7/dist-packages (from requests[security]->hca-ingest) (3.0.4)
    Collecting urllib3>=1.25.5
      Downloading urllib3-1.25.11-py2.py3-none-any.whl (127 kB)
    [K     |████████████████████████████████| 127 kB 37.2 MB/s 
    [?25hRequirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.7/dist-packages (from requests[security]->hca-ingest) (2022.9.24)
    Requirement already satisfied: six in /usr/local/lib/python3.7/dist-packages (from url-normalize>=1.4->requests-cache->hca-ingest) (1.15.0)
    Collecting pyOpenSSL>=0.14
      Downloading pyOpenSSL-22.1.0-py3-none-any.whl (57 kB)
    [K     |████████████████████████████████| 57 kB 5.0 MB/s 
    [?25hBuilding wheels for collected packages: hca-ingest, polling
      Building wheel for hca-ingest (setup.py) ... [?25l[?25hdone
      Created wheel for hca-ingest: filename=hca_ingest-2.6.0-py3-none-any.whl size=70596 sha256=114859a9f8d95b35d96ab061da4a47a702825e7a9a0015cfdcf7e06ac4300515
      Stored in directory: /root/.cache/pip/wheels/c2/8e/0c/4693dcbc7e2b550c87e3b89a41f0b2e4d98924bc7681c85846
      Building wheel for polling (setup.py) ... [?25l[?25hdone
      Created wheel for polling: filename=polling-0.3.2-py3-none-any.whl size=4129 sha256=3fb1d18a14a77ee5a6864cc4e989d673d80b8421e54621e759012f50d975858a
      Stored in directory: /root/.cache/pip/wheels/e5/3f/0c/54a03b715fce3176335c957ae94d7d0b2a918e89b1b195bace
    Successfully built hca-ingest polling
    Installing collected packages: urllib3, exceptiongroup, cryptography, url-normalize, pyOpenSSL, cattrs, xlsxwriter, requests-cache, polling, mergedeep, jsonref, hca-ingest
      Attempting uninstall: urllib3
        Found existing installation: urllib3 1.24.3
        Uninstalling urllib3-1.24.3:
          Successfully uninstalled urllib3-1.24.3
    Successfully installed cattrs-22.2.0 cryptography-38.0.3 exceptiongroup-1.0.1 hca-ingest-2.6.0 jsonref-1.0.1 mergedeep-1.3.4 polling-0.3.2 pyOpenSSL-22.1.0 requests-cache-0.9.7 url-normalize-1.4.3 urllib3-1.25.11 xlsxwriter-3.0.3


### Load libraries

```python
import requests as rq
import json
from hca_ingest.api.ingestapi import IngestApi
```

### Get a token

In order to get a token, you need to log in to the ingest UI: https://staging.contribute.data.humancellatlas.org/. For the purpose of this notebook, we will be using staging. However, that can be change to prod (by deleting the first part of the domain) or to dev (by changing `staging` to `dev`) at any point in the process.

If you are going to use any other environment, please remember to change the `environment` variable in the next section

The steps to obtain the token are detailed in this guide: [API tokens](https://ebi-ait.github.io/hca-ebi-dev-team/operations_tasks/api_token.html)


```python
token = "Bearer <paste_token_here>"
```

### Set up environment and global variables

```python
# Environment-related set-up and global variables used across the notebook
accepted_environments = {
    'develop': '.dev',
    'staging': '.staging',
    'production': ''
}

environment = 'staging'  #staging environment by default

# Set up environment value for API's URL
try:
  env_for_url = accepted_environments[environment]
except KeyError:
  print(f"Environment {environment} not recognised. Defaulting to staging")
  env_for_url = accepted_environments['staging']

base_url = f'https://api.ingest{env_for_url}.archive.data.humancellatlas.org'

# Set up API object
api = IngestApi(url=base_url)
headers = api.set_token(token=token)

```

## Create a project

This block of code will be dedicated to creating a project within ingest. The following will be assumed:
* A JSON entity is available for use as the "content"

For the purpose of this notebook, everything will be performed in the staging environment. To perform this on other environments (e.g. prod), please update the `environment` variable to any of the values accepted in `accepted_environments`


```python
# Load the project metadata entity
with open('example_project.json', 'r') as f:
  project_content = json.load(f)

ingest_project = api.create_project(submission_url='', content=project_content)

```

The returned object is the project as contained by ingest: this object contains the metadata that was submitted in the previous step, but also contains some extra, important metadata:

* uuid: Unique identifier for your project, generated randomly
* Management metadata: This metadata comprises metadata that will apply to your experiment, e.g. organs, species used, etc.

We're going to print the object and take a look


```python
ingest_project
```
    {'content': {'describedBy': 'https://schema.staging.data.humancellatlas.org/type/project/17.0.0/project',
      'schema_type': 'project',
      'project_core': {'project_short_name': 'myCoolLabel',
       'project_title': 'Test_project_with_minimum_information',
       'project_description': 'This is a test project with minimum information for the programmatic submissions guide'},
      'contributors': [{'name': 'Enrique,,Ventura',
        'email': 'enrique@ebi.ac.uk',
        'institution': 'EMBL-EBI',
        'corresponding_contributor': True,
        'project_role': {'text': 'data curator',
         'ontology': 'EFO:0009737',
         'ontology_label': 'data curator'}}],
      'publications': [{'authors': ['Lorem IP', 'Sed UP'],
        'title': 'A combined approach for single-cell mRNA and intracellular protein expression analysis',
        'url': 'https://www.frontiersin.org/articles/10.3389/fcell.2020.00384/full',
        'official_hca_publication': False}],
      'funders': [{'grant_title': 'a cool grant',
        'grant_id': '000000000bp1',
        'organization': 'EMBL-EBI'}]},
     'submissionDate': '2022-11-09T17:36:29.253947Z',
     'updateDate': '2022-11-09T17:36:29.253947Z',
     'user': '5ece3464ec0680746267e784',
     'lastModifiedUser': '5ece3464ec0680746267e784',
     'type': 'Project',
     'uuid': {'uuid': 'e838a25f-8f52-4678-9744-6d650ca65374'},
     'events': [],
     'firstDcpVersion': '2022-11-09T17:36:29.253947Z',
     'dcpVersion': '2022-11-09T17:36:29.253947Z',
     'contentLastModified': '2022-11-09T17:36:29.246070Z',
     'accession': None,
     'validationState': 'Draft',
     'validationErrors': None,
     'graphValidationErrors': None,
     'isUpdate': False,
     'releaseDate': None,
     'accessionDate': None,
     'technology': None,
     'organ': None,
     'cellCount': None,
     'dataAccess': None,
     'identifyingOrganisms': None,
     'primaryWrangler': None,
     'secondaryWrangler': None,
     'wranglingState': None,
     'wranglingPriority': None,
     'wranglingNotes': None,
     'isInCatalogue': None,
     'cataloguedDate': None,
     'publicationsInfo': None,
     'dcpReleaseNumber': None,
     'projectLabels': None,
     'hasOpenSubmission': False,
     '_links': {'self': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786'},
      'project': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786',
       'title': 'A single project'},
      'validating': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/validatingEvent'},
      'bundleManifests': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/bundleManifests',
       'title': 'Access or create bundle manifests (describing which submitted contents went into which bundle in the datastore)'},
      'auditLogs': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/auditLogs'},
      'supplementaryFiles': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/supplementaryFiles'},
      'submissionEnvelopes': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelopes',
       'title': 'Access or create new submission envelopes'},
      'submissionEnvelope': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelope',
       'title': 'A single submission envelope'}}}



Everything looks correct, so we will save the identifier for our project (called the `uuid`) and store it in case we need to retrieve the project later.


```python
# Store project uuid
ingest_project_uuid = ingest_project['uuid']['uuid']
```

### Understanding the information on the project

After printing the resulting `ingest_project`, you probably have noticed that there is much more meatadata than what was sent; for most entities, this is just system-generated and you don't need to worry about it. 

However, for `project` metadata, we load some information regarding statuses and general-level metadata for different purposes (e.g. display in the [project catalogue](https://www.ebi.ac.uk/humancellatlas/project-catalogue/)).

This project-level metadata is explained in more detail in the [create a project](https://ebi-ait.github.io/ingest-programmatic-submissions/docs/create_a_project/create_a_project.html) guideline associated with this notebook. For now, we will focus on the metadata that we should fill out:


```python
minimum_required_fields = {
    'releaseDate': None,          # Date that you want your data to be released. If the data is to be released as soon as possible, or if data has already been released (e.g. in GEO) input today's date in format: YYYY-MM-DDT00:00:00Z (e.g. 2021-11-29T00:00:00Z)
    'accessionDate': None,        # Same as above, but for accessioning in public archives.
    'technology': None,           # Library preparation technology(ies) used in the experiment, ontologised. More below.
    'organ': None,                # Organ(s) used in the experimnt, ontologised. More below
    'cellCount': None,            # Estimated number of cells generated by this project.
    'dataAccess': None,           # Type of data access, selected from a list of terms. For more detail, refer to readme.
    'identifyingOrganisms': None, # Organism that was used to generate the data, can be: Human, Mouse, or both.
    'primaryWrangler': None,      # Person that is in charge of the project/submission: associated with a user.
    'wranglingState': None,       # Status of the project. For a detailed list of accepted values, refer to readme.
    'wranglingPriority': None,    # 1, 2, or 3. 1 is highest priority and 3 is lowest. Refer to readme for more information.
    'wranglingNotes': None,       # Extra notes associated with the project; feel free to input your own notes here.
    'isInCatalogue': None,        # If the project is to be displayed in the catalogue, True, otherwise False
    }
```

### Adding minimum information

Now, we will be modifying the information on the list above, to make sure we enter the minimum amount of metadata that the project should contain. We're going to divide the fields in 2 types:
* **Ontologised**: fields that are validated against the [HCA ontology](https://ontology.archive.data.humancellatlas.org/index).
* **Other**: Fields that have are not ontologised and that are validated against other premises.

We're going to start with the ontologised fields.

#### Ontologised fields

These terms are called "ontologised" because they are validated against a set of restrictions defined both in our validation rules and enforced in the ontologies themselves; for example, `organ` validates that the term used as an input is validated as a child term, only with relationship `subclassOf`, of the term `anatomical structure`([UBERON:0000061](https://ontology.archive.data.humancellatlas.org/ontologies/hcao/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FUBERON_0000061)). Detailed information on the restrictions can be found in the readme file.

In this category, we have 2 fields:
- organ: A list of the organs used in this experiment; for this notebook, we're going to use the terms "lung"([UBERON:0002048](https://ontology.archive.data.humancellatlas.org/ontologies/hcao/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FUBERON_0002048)) and "heart"([UBERON:0000948](https://ontology.archive.data.humancellatlas.org/ontologies/hcao/terms?iri=http%3A%2F%2Fpurl.obolibrary.org%2Fobo%2FUBERON_0000948)).
- technology: A list of the library preparation technologies used in this experiment; for this notebook, we're going to use the terms `10x 3' v2`([EFO:0009899](https://ontology.archive.data.humancellatlas.org/ontologies/efo/terms?iri=http%3A%2F%2Fwww.ebi.ac.uk%2Fefo%2FEFO_0009899)) and `10x 3' v3`([EFO:0009922](https://ontology.archive.data.humancellatlas.org/ontologies/efo/terms?iri=http%3A%2F%2Fwww.ebi.ac.uk%2Fefo%2FEFO_0009922)). This field also accepts free text in case there is no ontology for the term just yet; we are also going to add an entry for this



```python
# Set up organ
organ = {
    "ontologies": [
      {
        "text": "lung",                 # Text field, free string that allows the user to introduce a more exact definition of the term if not available in the ontology
        "ontology": "UBERON:0002048",   # Unique identifier for the ontology term, in the form of <ontology>:<ID>
        "ontology_label": "lung"        # Text field, must exactly match the label provided in the ontology, case sensitive.
      },
      {
        "text": "heart",
        "ontology": "UBERON:0000948",
        "ontology_label": "heart"
      }
    ]
}

# Set up technology
technology = {
    "ontologies": [
      {
        "text": "10x 3' v2",
        "ontology": "EFO:0009899",
        "ontology_label": "10x 3' v2"
      },
      {
        "text": "10x 3' v3",
        "ontology": "EFO:0009922",
        "ontology_label": "10x 3' v3"
      }
    ],
    "others": [
        "Mysupercoollibrarypreptechnology"  # Free text field to introduce as many terms as you want that couldn't be found in the ontology
    ]
}

# pass the values to our variable
minimum_required_fields['organ'] = organ
minimum_required_fields['technology'] = technology
```

#### Other fields


```python
# Dates
# Dates must follow the following format: YYYY-MM-DDThh:mm:ssZ
minimum_required_fields['releaseDate'] = "2022-08-30T00:00:00Z"
minimum_required_fields['accessionDate'] = "2022-08-30T00:00:00Z"

# Enum values
# Set of values accepted are predetermined, depending on the field. 
# For the full list of values, please refer to the readme
minimum_required_fields['dataAccess'] = {
                                          "type": "All fully open",
                                          "notes": "Can be released publicly! :D"
                                        }
minimum_required_fields['identifyingOrganisms'] = ["Human", "Mouse", "Other"]
minimum_required_fields['wranglingPriority'] = 1 # Very important project!
minimum_required_fields['wranglingState'] = "Eligible"

# Simple values
# Set of fields that have a simple value; it may be a free string, an integer or a boolean
minimum_required_fields['cellCount'] = 17500
minimum_required_fields['primaryWrangler'] = ingest_project['user'] # User ID is required in this field.
minimum_required_fields['wranglingNotes'] = "This is an awesome project and I will finish it soon"
minimum_required_fields['isInCatalogue'] = True # We want the project to be displayed in the project catalogue
```

## Updating project with missing information

Now that we understand the metadata that we are handling, and that we have filled in the missing bits necessary for a minimum information project, we will update the project with the values that we have been gathering.

Once we have the content that we have to update, the update itself is pretty easy!


```python
# Retrieve project URL to update
ingest_project_url = ingest_project['_links']['self']['href']
response = api.patch(url=ingest_project_url, json=minimum_required_fields)

updated_ingest_project = response.json()
```

Let's print the project and check if the changes have made it through!


```python
updated_ingest_project
```




    {'content': {'describedBy': 'https://schema.staging.data.humancellatlas.org/type/project/17.0.0/project',
      'schema_type': 'project',
      'project_core': {'project_short_name': 'myCoolLabel',
       'project_title': 'Test_project_with_minimum_information',
       'project_description': 'This is a test project with minimum information for the programmatic submissions guide'},
      'contributors': [{'name': 'Enrique,,Ventura',
        'email': 'enrique@ebi.ac.uk',
        'institution': 'EMBL-EBI',
        'corresponding_contributor': True,
        'project_role': {'text': 'data curator',
         'ontology': 'EFO:0009737',
         'ontology_label': 'data curator'}}],
      'publications': [{'authors': ['Lorem IP', 'Sed UP'],
        'title': 'A combined approach for single-cell mRNA and intracellular protein expression analysis',
        'url': 'https://www.frontiersin.org/articles/10.3389/fcell.2020.00384/full',
        'official_hca_publication': False}],
      'funders': [{'grant_title': 'a cool grant',
        'grant_id': '000000000bp1',
        'organization': 'EMBL-EBI'}]},
     'submissionDate': '2022-11-09T17:36:29.253Z',
     'updateDate': '2022-11-09T17:37:57.227484Z',
     'user': '5ece3464ec0680746267e784',
     'lastModifiedUser': '5ece3464ec0680746267e784',
     'type': 'Project',
     'uuid': {'uuid': 'e838a25f-8f52-4678-9744-6d650ca65374'},
     'events': [],
     'firstDcpVersion': '2022-11-09T17:36:29.253Z',
     'dcpVersion': '2022-11-09T17:36:29.253Z',
     'contentLastModified': '2022-11-09T17:36:29.246Z',
     'accession': None,
     'validationState': 'Valid',
     'validationErrors': [],
     'graphValidationErrors': None,
     'isUpdate': False,
     'releaseDate': '2022-08-30T00:00:00Z',
     'accessionDate': '2022-08-30T00:00:00Z',
     'technology': {'ontologies': [{'text': "10x 3' v2",
        'ontology': 'EFO:0009899',
        'ontology_label': "10x 3' v2"},
       {'text': "10x 3' v3",
        'ontology': 'EFO:0009922',
        'ontology_label': "10x 3' v3"}],
      'others': ['Mysupercoollibrarypreptechnology']},
     'organ': {'ontologies': [{'text': 'lung',
        'ontology': 'UBERON:0002048',
        'ontology_label': 'lung'},
       {'text': 'heart',
        'ontology': 'UBERON:0000948',
        'ontology_label': 'heart'}]},
     'cellCount': 17500,
     'dataAccess': {'type': 'All fully open',
      'notes': 'Can be released publicly! :D'},
     'identifyingOrganisms': ['Human', 'Mouse', 'Other'],
     'primaryWrangler': '5ece3464ec0680746267e784',
     'secondaryWrangler': None,
     'wranglingState': 'Eligible',
     'wranglingPriority': 1,
     'wranglingNotes': 'This is an awesome project and I will finish it soon',
     'isInCatalogue': True,
     'cataloguedDate': '2022-11-09T17:37:57.207027Z',
     'publicationsInfo': None,
     'dcpReleaseNumber': None,
     'projectLabels': None,
     'hasOpenSubmission': False,
     '_links': {'self': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786'},
      'project': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786',
       'title': 'A single project'},
      'processing': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/processingEvent'},
      'draft': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/draftEvent'},
      'bundleManifests': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/bundleManifests',
       'title': 'Access or create bundle manifests (describing which submitted contents went into which bundle in the datastore)'},
      'auditLogs': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/auditLogs'},
      'supplementaryFiles': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/supplementaryFiles'},
      'submissionEnvelopes': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelopes',
       'title': 'Access or create new submission envelopes'},
      'submissionEnvelope': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelope',
       'title': 'A single submission envelope'}}}



And we have our project, updated, with the minimum required metadata!

## Retrieve a project

Once we have created a project with minimum information, we may want to retrieve the project to do further things with it (Add more metadata, check status, etc). In order to do this, we are going to use one of the many functions that we have available to retrieve a project:
- `IngestApi.get_project_by_uuid`: Retrieves a single project with a UUID

But there are other functions available, in case you don't have the UUID at hand or can't remember, listed below:

<details>
<summary>Functions to search for projects</summary>
<ul>
<li>.get_user_projects: Retrieve all the projects associated with your user (Requires token to be set)</li>
<li>.get_project_by_id: Retrieve a project with the MongoDB ID provided</li>
</ul>
</details>



```python
ingest_project = api.get_project_by_uuid(ingest_project_uuid)
```

Let's ensure we have retrieved our project correctly:


```python
ingest_project
```




    {'content': {'describedBy': 'https://schema.staging.data.humancellatlas.org/type/project/17.0.0/project',
      'schema_type': 'project',
      'project_core': {'project_short_name': 'myCoolLabel',
       'project_title': 'Test_project_with_minimum_information',
       'project_description': 'This is a test project with minimum information for the programmatic submissions guide'},
      'contributors': [{'name': 'Enrique,,Ventura',
        'email': 'enrique@ebi.ac.uk',
        'institution': 'EMBL-EBI',
        'corresponding_contributor': True,
        'project_role': {'text': 'data curator',
         'ontology': 'EFO:0009737',
         'ontology_label': 'data curator'}}],
      'publications': [{'authors': ['Lorem IP', 'Sed UP'],
        'title': 'A combined approach for single-cell mRNA and intracellular protein expression analysis',
        'url': 'https://www.frontiersin.org/articles/10.3389/fcell.2020.00384/full',
        'official_hca_publication': False}],
      'funders': [{'grant_title': 'a cool grant',
        'grant_id': '000000000bp1',
        'organization': 'EMBL-EBI'}]},
     'submissionDate': '2022-11-09T17:36:29.253Z',
     'updateDate': '2022-11-09T17:37:57.227Z',
     'user': '5ece3464ec0680746267e784',
     'lastModifiedUser': '5ece3464ec0680746267e784',
     'type': 'Project',
     'uuid': {'uuid': 'e838a25f-8f52-4678-9744-6d650ca65374'},
     'events': [],
     'firstDcpVersion': '2022-11-09T17:36:29.253Z',
     'dcpVersion': '2022-11-09T17:36:29.253Z',
     'contentLastModified': '2022-11-09T17:36:29.246Z',
     'accession': None,
     'validationState': 'Valid',
     'validationErrors': [],
     'graphValidationErrors': None,
     'isUpdate': False,
     'releaseDate': '2022-08-30T00:00:00Z',
     'accessionDate': '2022-08-30T00:00:00Z',
     'technology': {'ontologies': [{'text': "10x 3' v2",
        'ontology': 'EFO:0009899',
        'ontology_label': "10x 3' v2"},
       {'text': "10x 3' v3",
        'ontology': 'EFO:0009922',
        'ontology_label': "10x 3' v3"}],
      'others': ['Mysupercoollibrarypreptechnology']},
     'organ': {'ontologies': [{'text': 'lung',
        'ontology': 'UBERON:0002048',
        'ontology_label': 'lung'},
       {'text': 'heart',
        'ontology': 'UBERON:0000948',
        'ontology_label': 'heart'}]},
     'cellCount': 17500,
     'dataAccess': {'type': 'All fully open',
      'notes': 'Can be released publicly! :D'},
     'identifyingOrganisms': ['Human', 'Mouse', 'Other'],
     'primaryWrangler': '5ece3464ec0680746267e784',
     'secondaryWrangler': None,
     'wranglingState': 'Eligible',
     'wranglingPriority': 1,
     'wranglingNotes': 'This is an awesome project and I will finish it soon',
     'isInCatalogue': True,
     'cataloguedDate': '2022-11-09T17:37:57.207Z',
     'publicationsInfo': None,
     'dcpReleaseNumber': None,
     'projectLabels': None,
     'hasOpenSubmission': False,
     '_links': {'self': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786'},
      'project': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786',
       'title': 'A single project'},
      'processing': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/processingEvent'},
      'draft': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/draftEvent'},
      'bundleManifests': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/bundleManifests',
       'title': 'Access or create bundle manifests (describing which submitted contents went into which bundle in the datastore)'},
      'auditLogs': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/auditLogs'},
      'supplementaryFiles': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/supplementaryFiles'},
      'submissionEnvelopes': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelopes',
       'title': 'Access or create new submission envelopes'},
      'submissionEnvelope': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelope',
       'title': 'A single submission envelope'}}}



### Check status of a project

When a project (or any piece of metadata) is updated to ingest, it gets validated, the `content` being validated against the schema it is pointing to (on the `describedBy` field), and in the case of the project, the base fields validating against other set of rules.

The ingest service has the ability to provide with a full report of these validation events, including the status of the entity and the error messages.

On this section, we will focus on retrieving the errors (currently none) of the project we just uploaded and we will update the project to artificially produce a couple of errors. We will then retrieve the project again and check on the errors, but for a detailed explanation of each type of error, please refer to the Readme file.


```python
# Print validation errors
validation_errors = ingest_project['validationErrors']
print(f"Validation errors: {validation_errors if validation_errors else None}")

# Print validation status
validation_status = ingest_project['validationState']
print(f"Validation status: {validation_status}")
```

    Validation errors: None
    Validation status: Valid



```python
non_valid_content = ingest_project['content']
non_valid_content['estimated_cell_count'] = '17500'             # Cell count should always be an integer
non_valid_content['insdc_project_accessions'] =  [              # INSDC project accessions:
                                      'GSE7777777',   # SHOULD NOT be a GEO series accession
                                      'SRP000000',    # SHOULD follow SRPXXXXXX format
                                      '',             # SHOULD NOT be an empty string
                                      347289347       # SHOULD NOT be a numer
                                      ]

non_valid_values =  {    
                      "content" : non_valid_content   # Patching "content" field
                    } 

# Patch the non_valid content into the project content
response = api.patch(url=ingest_project_url, json=non_valid_values)
```


```python
response.json()
```




    {'content': {'describedBy': 'https://schema.staging.data.humancellatlas.org/type/project/17.0.0/project',
      'schema_type': 'project',
      'project_core': {'project_short_name': 'myCoolLabel',
       'project_title': 'Test_project_with_minimum_information',
       'project_description': 'This is a test project with minimum information for the programmatic submissions guide'},
      'contributors': [{'name': 'Enrique,,Ventura',
        'email': 'enrique@ebi.ac.uk',
        'institution': 'EMBL-EBI',
        'corresponding_contributor': True,
        'project_role': {'text': 'data curator',
         'ontology': 'EFO:0009737',
         'ontology_label': 'data curator'}}],
      'publications': [{'authors': ['Lorem IP', 'Sed UP'],
        'title': 'A combined approach for single-cell mRNA and intracellular protein expression analysis',
        'url': 'https://www.frontiersin.org/articles/10.3389/fcell.2020.00384/full',
        'official_hca_publication': False}],
      'funders': [{'grant_title': 'a cool grant',
        'grant_id': '000000000bp1',
        'organization': 'EMBL-EBI'}],
      'estimated_cell_count': '17500',
      'insdc_project_accessions': ['GSE7777777', 'SRP000000', '', 347289347]},
     'submissionDate': '2022-11-09T17:36:29.253Z',
     'updateDate': '2022-11-09T17:38:35.057769Z',
     'user': '5ece3464ec0680746267e784',
     'lastModifiedUser': '5ece3464ec0680746267e784',
     'type': 'Project',
     'uuid': {'uuid': 'e838a25f-8f52-4678-9744-6d650ca65374'},
     'events': [],
     'firstDcpVersion': '2022-11-09T17:36:29.253Z',
     'dcpVersion': '2022-11-09T17:38:35.057205Z',
     'contentLastModified': '2022-11-09T17:38:35.057205Z',
     'accession': None,
     'validationState': 'Valid',
     'validationErrors': [],
     'graphValidationErrors': None,
     'isUpdate': False,
     'releaseDate': '2022-08-30T00:00:00Z',
     'accessionDate': '2022-08-30T00:00:00Z',
     'technology': {'ontologies': [{'text': "10x 3' v2",
        'ontology': 'EFO:0009899',
        'ontology_label': "10x 3' v2"},
       {'text': "10x 3' v3",
        'ontology': 'EFO:0009922',
        'ontology_label': "10x 3' v3"}],
      'others': ['Mysupercoollibrarypreptechnology']},
     'organ': {'ontologies': [{'text': 'lung',
        'ontology': 'UBERON:0002048',
        'ontology_label': 'lung'},
       {'text': 'heart',
        'ontology': 'UBERON:0000948',
        'ontology_label': 'heart'}]},
     'cellCount': 17500,
     'dataAccess': {'type': 'All fully open',
      'notes': 'Can be released publicly! :D'},
     'identifyingOrganisms': ['Human', 'Mouse', 'Other'],
     'primaryWrangler': '5ece3464ec0680746267e784',
     'secondaryWrangler': None,
     'wranglingState': None,
     'wranglingPriority': 1,
     'wranglingNotes': 'This is an awesome project and I will finish it soon',
     'isInCatalogue': True,
     'cataloguedDate': '2022-11-09T17:37:57.207Z',
     'publicationsInfo': None,
     'dcpReleaseNumber': None,
     'projectLabels': None,
     'hasOpenSubmission': False,
     '_links': {'self': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786'},
      'project': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786',
       'title': 'A single project'},
      'processing': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/processingEvent'},
      'draft': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/draftEvent'},
      'bundleManifests': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/bundleManifests',
       'title': 'Access or create bundle manifests (describing which submitted contents went into which bundle in the datastore)'},
      'auditLogs': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/auditLogs'},
      'supplementaryFiles': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/supplementaryFiles'},
      'submissionEnvelopes': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelopes',
       'title': 'Access or create new submission envelopes'},
      'submissionEnvelope': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/636be51d897ce65cad07c786/submissionEnvelope',
       'title': 'A single submission envelope'}}}



After patching the project with invalid values, let's repeat the check we did previously.


```python
ingest_project = api.get_project_by_uuid(ingest_project_uuid)
# Print validation errors
validation_errors = ingest_project['validationErrors']
newline = '\n'
print(f"Validation errors: {validation_errors if validation_errors else None}")

# Print validation status
validation_status = ingest_project['validationState']
print(f"Validation status: {validation_status}")
```

    Validation errors: [{'errorType': 'METADATA_ERROR', 'message': 'should match pattern "^[D|E|S]RP[0-9]+$"', 'userFriendlyMessage': 'should match pattern "^[D|E|S]RP[0-9]+$" at .insdc_project_accessions[0]', 'absoluteDataPath': '.insdc_project_accessions[0]'}, {'errorType': 'METADATA_ERROR', 'message': 'should match pattern "^[D|E|S]RP[0-9]+$"', 'userFriendlyMessage': 'should match pattern "^[D|E|S]RP[0-9]+$" at .insdc_project_accessions[2]', 'absoluteDataPath': '.insdc_project_accessions[2]'}, {'errorType': 'METADATA_ERROR', 'message': 'should be string', 'userFriendlyMessage': 'should be string at .insdc_project_accessions[3]', 'absoluteDataPath': '.insdc_project_accessions[3]'}, {'errorType': 'METADATA_ERROR', 'message': 'should be integer', 'userFriendlyMessage': 'should be integer at .estimated_cell_count', 'absoluteDataPath': '.estimated_cell_count'}]
    Validation status: Invalid


As we can see, this time it has returned 2 things:
- A set of errors, comprised in a list that details the errors, from type to message.
- Validation status: invalid, indicating that the validation went wrong.

For detailed information on how to understand the errors, please proceed to the "readme.md" file.

## Delete a project

Projects in our database can be deleted. While we do not advise to delete projects once they have been published in the data portal (`uuid` identifiers are important for updates), at any point before finishing the submission (Later in the notebook), any metadata entity can be deleted, including projects.


```python
# Delete ingest project and check everything went correctly
response = api.delete(ingest_project_url)

assert response.status_code == 204
```

If the status code of the response is 204, the project has been deleted!

# Addendum
## Updating projects
### Deleting a field/Replacing all values

Deleting a field requires a slightly different sort of operation; up until now, we have used `patch` to address field modifications. However, if we want to delete a field or replace all values, we would need to delete the field from the content, and then PUT the whole content of the project entity to the project URL.

This operation will completely replace the older entry with the new one; using the old one as a template ensures critical fields (e.g. `uuid`) get preserved over this operation.

### Adding new fields

When adding new fields, considering the type of field that is going to be added is essential; nested properties and arrays can't be just modified through a `patch` operation, they need the document to be partially (or entirely) replaced

In this notebook, we are going to add 2 fields:
- A completely new field, available in the schema, `insdc_project_accessions`
- A new publication that we want associated to this project, without deleting the already existing one.


```python
# Adding the INSDC project accession
response = api.patch(url=ingest_project_url, patch={"content": {"insdc_project_accessions": ["SRP000000"]}})
assert response.status_code == 200
updated_project = api.get_project_by_uuid(ingest_project_uuid)

# Let's print the project and ensure the modification has gone through!
updated_project
```




    {'content': {'insdc_project_accessions': ['SRP000000']},
     'submissionDate': '2022-08-31T14:05:37.625Z',
     'updateDate': '2022-08-31T14:07:18.255Z',
     'user': '5ece3464ec0680746267e784',
     'lastModifiedUser': '5ece3464ec0680746267e784',
     'type': 'Project',
     'uuid': {'uuid': '019b3b05-903b-4b85-bdae-1e10589ccd06'},
     'events': [],
     'firstDcpVersion': '2022-08-31T14:05:37.625Z',
     'dcpVersion': '2022-08-31T14:07:18.252Z',
     'contentLastModified': '2022-08-31T14:07:18.252Z',
     'accession': None,
     'validationState': 'Draft',
     'validationErrors': [],
     'graphValidationErrors': None,
     'isUpdate': False,
     'releaseDate': '2022-08-30T00:00:00Z',
     'accessionDate': '2022-08-30T00:00:00Z',
     'technology': {'ontologies': [{'text': "10x 3' v2",
        'ontology': 'EFO:0009899',
        'ontology_label': "10x 3' v2"},
       {'text': "10x 3' v3",
        'ontology': 'EFO:0009922',
        'ontology_label': "10x 3' v3"}],
      'others': ['Mysupercoollibrarypreptechnology']},
     'organ': {'ontologies': [{'text': 'lung',
        'ontology': 'UBERON:0002048',
        'ontology_label': 'lung'},
       {'text': 'heart',
        'ontology': 'UBERON:0000948',
        'ontology_label': 'heart'}]},
     'cellCount': 17500,
     'dataAccess': {'type': 'All fully open',
      'notes': 'Can be released publicly! :D'},
     'identifyingOrganisms': ['Human', 'Mouse', 'Other'],
     'primaryWrangler': '5ece3464ec0680746267e784',
     'secondaryWrangler': None,
     'wranglingState': 'Eligible',
     'wranglingPriority': 1,
     'wranglingNotes': 'This is an awesome project and I will finish it soon',
     'isInCatalogue': True,
     'cataloguedDate': '2022-08-31T14:06:06.624Z',
     'publicationsInfo': None,
     'dcpReleaseNumber': None,
     'projectLabels': None,
     'hasOpenSubmission': False,
     '_links': {'self': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69'},
      'project': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69',
       'title': 'A single project'},
      'validating': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69/validatingEvent'},
      'bundleManifests': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69/bundleManifests',
       'title': 'Access or create bundle manifests (describing which submitted contents went into which bundle in the datastore)'},
      'auditLogs': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69/auditLogs'},
      'supplementaryFiles': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69/supplementaryFiles'},
      'submissionEnvelopes': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69/submissionEnvelopes',
       'title': 'Access or create new submission envelopes'},
      'submissionEnvelope': {'href': 'https://api.ingest.staging.archive.data.humancellatlas.org/projects/630f6ab106f1711fccbe4d69/submissionEnvelope',
       'title': 'A single submission envelope'}}}


