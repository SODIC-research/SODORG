# SODORG Example Graph (RDF, JSON-LD, SHACL, Ontology)

## What this repository is

This repository contains a small, reproducible **example knowledge graph** that demonstrates how to model **time-scoped organizational structures and thematic assignments** for German public-sector actors in the context of Open Government Data (OGD).

It accompanies the position and vision paper:

**“From Federal Heterogeneity to Reproducible Analytics: A Provenance-Aware Knowledge Graph for Cross-Portal Comparability of German Open Government Data” (KGCW 2026, ESWC 2026)**

Core idea (from the paper abstract): cross-portal analyses in Germany are difficult because **publisher organizations** and **topic taxonomies** differ per federal state and can change over time due to reorganizations. This repository provides a worked example for modeling those dynamics in a **provenance-aware** and **time-aware** way.

Developed and maintained by the [SODIC Research Group](https://www.tu-chemnitz.de/informatik/dm/forschung/sodic.php) (Semantic & Open Data Innovation Chemnitz) at TU Chemnitz.

---

## Scientific purpose

The artifacts in this repository are meant for:

- demonstrating a **reproducible modeling pattern** for organizational change and category alignment
- supporting **longitudinal queries** (who published what, under which category, in which period)
- enabling **auditable transformations** via explicit provenance and validation constraints
- serving as a minimal starting point for larger portal-scale pipelines (e.g., CKAN/DCAT snapshots)

This repository is intended for research and method development. It is not a complete production dataset.

---

## What the code does

The transformation pipeline takes a small tabular template (`organRdfFromCSV.ods`) and generates:

- an RDF **data graph** (Turtle + JSON-LD)
- a minimal **domain ontology** (Turtle + JSON-LD)
- **SHACL shapes** for validation (Turtle + JSON-LD)
- a JSON-LD **@context** to keep the JSON-LD compact and consistent

The transformation follows these modeling principles:

- **Stable organization identities** using `org:Organization`
- **Time-scoped organization versions** (`sod:OrganVersion`) per row with validity intervals
- **Explicit reorganization events** as `org:ChangeEvent` (renaming, reassignment, successor/predecessor)
- **Category assignments** (`sod:CategoryAssignment`) linked to a SKOS concept
- Optional **EU Data Theme alignment** using `skos:exactMatch` to the Publications Office authority table

Validation is provided via SHACL to enforce minimum completeness and consistency constraints.

---

## Repository structure

Typical layout:

- `input/`
  - `organRdfFromCSV.ods`  
    Tabular template containing attributes, datatypes, and example rows.
- `data/`
  - `sodorg_example_data_v2.jsonld`
- `ontology/`
  - `sodorg_example_ontology_v2.jsonld`
- `shacl/`
  - `sodorg_example_shapes_v2.jsonld`
- `context/`
  - `sodorg_context_v2.jsonld`

If your repository layout differs, adjust the paths accordingly.

---

## Data model overview

### Organizations and versions

- `org:Organization`  
  Stable organization identity with multilingual labels.
- `sod:OrganVersion`  
  Time-scoped view of an organization in a specific period.
  Carries:
  - `sod:organType` (enum, administrative level such as `federalState`)
  - location via `dct:spatial`
  - validity via `dct:temporal` → `time:Interval`

### Change events

- `org:ChangeEvent`  
  Explicit reorganization event connecting predecessor and successor:
  - `org:originalOrganization`
  - `org:resultingOrganization`
  - temporal info using PROV-O timestamps

If the direction cannot be taken verbatim from the table due to inconsistencies, the event is annotated:

- `sod:directionInferred` (boolean)
- `sod:inferenceNote` (string)

This makes the transformation auditable and avoids presenting derived assumptions as factual statements.

### Categories and EU alignment

- `skos:Concept` for categories (e.g., `Energie`)
- `sod:CategoryAssignment` linking a time-scoped org version to a category concept
- optional: `skos:exactMatch` to EU Data Theme concepts (e.g., `.../ENER`)

---

## Standards and vocabularies used

This repository reuses established W3C and EU vocabularies:

- W3C Organization Ontology (ORG)  
  https://www.w3.org/TR/vocab-org/
- PROV-O (Provenance Ontology)  
  https://www.w3.org/TR/prov-o/
- OWL-Time  
  https://www.w3.org/TR/owl-time/
- SHACL  
  https://www.w3.org/TR/shacl/
- SKOS  
  https://www.w3.org/TR/skos-reference/
- JSON-LD 1.1  
  https://www.w3.org/TR/json-ld11/
- EU Publications Office Data Theme authority table  
  https://op.europa.eu/en/web/eu-vocabularies/at-dataset/-/resource/dataset/data-theme

---

## How to use

### 1) Inspect the artifacts

- Load `data/*.jsonld` into your triple store or RDF tool (e.g., Apache Jena, RDF4J, GraphDB).
- Use the JSON-LD version if you prefer Linked Data tooling based on JSON.

### 2) Validate with SHACL

- Validate `data/sodorg_example_data_v2.jsonld` against `shacl/sodorg_example_shapes_v2.jsonld`.
- The SHACL constraints help detect missing required fields and enforce consistent modeling.

### 3) Extend to your own pipeline

This example is designed as a minimal pattern for:
- organizational time series
- successor/predecessor modeling via change events
- category alignment that remains stable and comparable over time

---

## 📖 Citation

If you use this repository in your research, please cite:

```bibtex
@misc{SODORGExample2026,
  title        = {SODORG Example Graph: Time-scoped Organizations, Change Events, and EU Theme Alignment (RDF/JSON-LD/SHACL)},
  author       = {Florian Hahn and the SODIC Research Group},
  year         = {2026},
  howpublished = {\url{https://github.com/SODIC-research/SODORG}},
  note         = {Accessed: May 2026}
}