# Database A1 — MongoDB Practice Lab

A document-oriented database assignment using **Python, PyMongo, and MongoDB** to explore time-series anomaly detection methods. The notebook covers queries, aggregation, schema validation, and co-authorship graph traversal, alongside database design discussions.

Prepared for **Master IASD — DataAES (2025)**, based on the lab by **Paul Boniol**.  
Student: **Mahdi Haji Alilue Bonab**.

## Repository contents

| File | Description |
| --- | --- |
| [lab_notebook.ipynb](lab_notebook.ipynb) | Lab questions, Python solutions, explanations, and saved outputs |
| [lab_topic.pdf](lab_topic.pdf) | Assignment handout |

**The input JSON dataset is not included in this repository.** Obtain the lab dataset separately before running the data-loading cells.

## Dataset

The notebook describes a dataset of **133 anomaly detection methods** and **13 algorithmic families**, sourced from [TSADtaxonomy](https://boniolp.github.io/TSADtaxonomy/).

It uses the local MongoDB database `tsad_lab` with these collections:

- `methods`: method names, categories, authors, publication years, supervision types, dimensionality, streaming support, and optional publication/code fields.
- `families`: algorithmic family descriptions.
- `validated_methods`: documents used to demonstrate schema validation.
- `co_authors`: author adjacency lists created for graph traversal.

The source data contains capitalization and author-name variations. Several solutions normalize categories and supervision types to lowercase and reconcile author names using surname and first initial. This name-matching heuristic can merge different people who share those identifiers.

## Topics covered

### Part 1 — Getting started

- **Q1–Q4:** insertion checks, document counts, and distinct categories.
- **Q5–Q12:** filtering, projection, sorting, regular expressions, and optional fields.
- **Q13–Q18:** category, supervision, author, and publication-year summaries.
- **Q19–Q20:** document updates and `$jsonSchema` validation.
- **T1–T3:** relational schema design, sharding, and search relevance.

### Part 2 — Advanced assignment

- **H1–H7:** category rankings, author activity, citation and rating summaries, and cross-collection joins.
- **H8:** co-authorship graphs, MongoDB `$graphLookup`, and Python breadth-first search.
- **H9:** a proposed design for querying academic descendants using additional advisor–student data.

Part 2 generates **simulated** ratings (1–5) and citation counts (50–5000), deterministically seeded from the configured family name. These values are exercise data, not real bibliometric measurements.

## Running the notebook

### 1. Prepare the environment

You need Python 3, a running local MongoDB server, and Jupyter. The notebook metadata records Python **3.10.22**.

```bash
git clone https://github.com/Mahdi-Marv/database-A1.git
cd database-A1
python -m pip install pymongo jupyterlab
```

The notebook connects to:

```python
client = MongoClient("mongodb://localhost:27017/")
db = client["tsad_lab"]
```

Start MongoDB before executing the connection cell. Adjust the connection URI if your instance uses a different host, port, or authentication.

### 2. Add the lab dataset

Place the supplied JSON files in a `data/` directory beside the notebook:

```text
database-A1/
├── README.md
├── lab_notebook.ipynb
├── lab_topic.pdf
└── data/
    └── ... JSON files from the lab dataset
```

Alternatively, change `DATA_DIR = "./data"` in the loading cell. Each JSON file should contain one document: documents with a `category` field are loaded into `methods`; the others go into `families`.

### 3. Launch and execute

```bash
python -m jupyterlab lab_notebook.ipynb
```

Run cells from top to bottom:

1. Connect to MongoDB and define the result-hashing helpers.
2. Load the base dataset and work through Part 1.
3. Run the Part 2 setup to generate seeded ratings and citations.
4. Continue through the advanced questions.

Later cells depend on earlier variables and helpers, including the author-normalization functions defined in Q16. Keep the configured `Your_name` value to reproduce this submission's seeded data; changing it changes Part 2 results.

**Collection resets:** the loading and exercise cells drop and recreate `methods`, `families`, `validated_methods`, and `co_authors` at different points. Use a dedicated lab database. Part 2 reloads `methods` from the original in-memory documents, so Part 1 changes such as the `era` field are not retained.

## Checking results

The initial load should report **133 methods and 13 families** when using the intended lab dataset.

The notebook includes two SHA-256-based, 16-character result fingerprints:

- `result_hash(result)` ignores the order of the outer result list.
- `ordered_hash(result)` preserves list order for sorted or ranked results.

Both strip MongoDB `_id` fields before hashing. Matching results require the same input data, normalization, and, for Part 2, seed. Q20 also demonstrates an invalid document being rejected by the schema validator.

## Status and limitations

The notebook contains implemented query exercises and written explanations. **T3 (search-engine relevance scoring) remains an unanswered placeholder.** H9 is a design discussion rather than an implemented genealogy database.

Saved notebook outputs are included, but a fresh run requires the external JSON dataset and a MongoDB instance. No dependency lockfile or standalone automated test suite is included.
