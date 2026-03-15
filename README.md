# FAS-LLM: Framework for Aiding Surveys in the Age of Large Language Models

This project is a modern reimagining of the framework described in the paper **“Framework for aiding surveys by natural language processing”** by Arijan Alla, Eftim Zdravevski, and Vladimir Trajkovik ([ICT Innovations 2017](https://proceedings.ictinnovations.org/2017/paper/448/framework-for-aiding-surveys-by-natural-language-processing), PDF: https://proceedings.ictinnovations.org/attachment/paper/448/framework-for-aiding-surveys-by-natural-language-processing.pdf).

In the original framework (FAS), the pipeline relied on **custom crawlers and traditional NLP** (stemming, lemmatization, WordNet-based synonyms) to:
- Query multiple digital libraries (Google Scholar, IEEE Xplore, Springer, etc.),
- Collect metadata and abstracts,
- Filter papers by citations and duplicates,
- Search for properties/keywords in abstracts,
- And finally generate **Excel-based charts** for analysis.

In this updated version, **FAS-LLM** keeps the same core goal—**helping researchers quickly build and explore survey corpora**—but replaces most of the low-level plumbing with **LLMs and modern APIs**, focusing on:
- Easier data acquisition (via export APIs / manual CSVs instead of brittle crawlers),
- Richer semantic understanding of papers,
- More flexible and interactive analyses and visualizations.

---

## Key Improvements Over the Original Framework

Compared to the 2017 FAS architecture, this LLM-based version aims to provide:

- **No custom crawlers**  
  - Use export capabilities / APIs from Google Scholar, Semantic Scholar, OpenAlex, publisher dashboards, or manually curated CSV/JSON files as input.  
  - This removes the need to fight rate limits, CAPTCHAs, and HTML changes.

- **LLM-based semantic enrichment instead of only keyword matching**  
  - Instead of only looking for exact keywords + WordNet synonyms in abstracts, an LLM can:
    - Classify papers into topics / taxonomies (e.g., wearable sensors vs. protocols vs. cloud-based systems),
    - Extract key properties (methods, datasets, domains, findings) from abstracts or full text,
    - Summarize individual papers and groups of papers at multiple granularities.

- **Configurable taxonomies expressed in natural language**  
  - You can define “properties” and “topics” as short natural language descriptions (e.g. “Papers that use wearable sensors for connected health monitoring”) instead of manually crafting keyword lists.
  - The LLM maps each paper to one or more such properties using semantic similarity and few-shot examples.

- **Richer visualizations and analytics beyond Excel**  
  - Produce structured JSON/CSV outputs suited for:
    - Time series charts of topics/properties over years,
    - Publisher/journal breakdowns,
    - Co-occurrence maps between properties,
    - Interactive dashboards (e.g. in Streamlit, Dash, or a JS frontend).

- **Interactive exploration loop**  
  - Researchers can iteratively:
    1. Refine the taxonomy and property definitions,
    2. Rerun the LLM-based classification on the same corpus,
    3. Compare previous vs. new label distributions and visualizations.

- **Extensibility to full-text analysis**  
  - When you have access to full PDFs (not just abstracts), the same pipeline can:
    - Extract sections (methods, results, limitations),
    - Perform targeted extraction (e.g. evaluation metrics, datasets),
    - Build richer knowledge graphs of concepts and relations.

---

## Proposed High-Level Architecture (New FAS-LLM)

This repository is structured to support an implementation along these lines:

- `data/`  
  - Input corpora (e.g. CSV/JSON exports with fields like title, abstract, year, citations, publisher, DOI, URL).

- `config/`  
  - Taxonomy definitions (YAML/JSON) describing:
    - Topics (e.g. “wearable sensors”, “protocols”, “cloud-based systems”),
    - Services (e.g. “monitoring”, “detection”, “recommendation”),
    - Custom properties you want to track.

- `src/`  
  - Python modules for:
    - Loading and normalizing bibliographic data,
    - Calling an LLM API (or local model) to:
      - Classify each paper into topics/properties,
      - Summarize papers,
      - Suggest new properties and taxonomies,
    - Aggregating results per year / property / publisher,
    - Exporting datasets for charts (CSV/JSON).

- `notebooks/` (optional)  
  - Jupyter notebooks for:
    - Prototyping prompts and taxonomies,
    - Ad-hoc exploratory data analysis,
    - Generating figures for publications.

- `visualization/` (optional)  
  - Scripts or a simple app (e.g. Streamlit) to:
    - Display trends over time,
    - Drill-down to specific subsets of papers,
    - Explore co-occurrence of properties.

At this stage, this repo is a **skeleton** ready for you to implement the concrete code and wiring to your choice of LLM (OpenAI, Azure, local models, etc.).

---

## Example Workflow (Modern Usage)

1. **Prepare corpus data**  
   - Export or manually assemble a CSV/JSON with at least:  
     `title`, `abstract`, `year`, `citations`, `publisher`, `doi`, `url`, and optionally `keywords`.

2. **Define properties and taxonomy**  
   - Create/edit a config file in `config/` that describes:
     - The properties you care about (e.g. “wearable sensor”, “cloud-based system”, “monitoring”, “recommendation”),  
     - Short natural language descriptions for each property,
     - Example papers (few-shot) if needed.

3. **Run LLM-based analysis**  
   - A main script (to be implemented) would:
     - Load the corpus,
     - For each paper (or batch), call the LLM to:
       - Decide which properties apply,
       - Generate a short summary,
     - Write an enriched dataset to `data/processed/`.

4. **Generate charts and tables**  
   - Another script or notebook would:
     - Read processed data,
     - Aggregate counts per year / property / publisher,
     - Output CSV/JSON ready for visualization,
     - Optionally, render charts directly (matplotlib/Plotly/Altair).

5. **Iterate and refine**  
   - Adjust taxonomy / prompts and re-run to explore the corpus under different perspectives.

---

## Relationship to the Original 2017 Publication

This repository:

- **References the original work**  
  - “Framework for aiding surveys by natural language processing”  
    ICT Innovations 2017 Web page: https://proceedings.ictinnovations.org/2017/paper/448/framework-for-aiding-surveys-by-natural-language-processing  
    PDF: https://proceedings.ictinnovations.org/attachment/paper/448/framework-for-aiding-surveys-by-natural-language-processing.pdf

- **Preserves the core idea**  
  - Use automated processing to:
    - Collect bibliographic data for a topic,
    - Filter by relevance (citations, years),
    - Detect important properties in the corpus,
    - Provide aggregated views and charts for the researcher.

- **Modernizes the implementation strategy**  
  - Moves from:
    - Manual HTML crawling and scraping of several digital libraries,
    - Keyword + synonym matching via WordNet and simple NLP,
    - Excel-based, manually generated charts.
  - To:
    - Using export APIs / CSVs instead of fragile crawlers,
    - LLM-based classification, property detection, and summarization,
    - Programmatic generation of visualizations and dashboards.

---

## Next Steps and Possible Extensions

Suggested next steps for evolving **FAS-LLM** into a full project:

- **Implement a minimal LLM integration**  
  - Choose a provider (OpenAI, Azure OpenAI, local models, etc.).  
  - Write a `src/llm_client.py` that wraps prompt templates and calls.

- **Implement a basic end-to-end pipeline**  
  - A command-line script `analyze_corpus.py` that:
    - Reads an input CSV of papers,
    - Applies the taxonomy using the LLM,
    - Writes an enriched CSV/JSON.

- **Recreate the original use case with modern tooling**  
  - Rebuild the connected-health example from the 2017 paper using:
    - The same or similar keywords (“wearable”, “connected health”, etc.),
    - Modern data sources (Semantic Scholar / OpenAlex),
    - LLM-based classification for technologies and services.
  - Compare:
    - Topic trends over time,
    - Differences in sensitivity/precision vs. keyword-only detection.

- **Add an interactive dashboard**  
  - Build a simple UI (e.g. Streamlit, Dash, or a small web app) where a user can:
    - Upload a corpus file,
    - Pick or define a taxonomy,
    - Run analysis,
    - Explore charts and drill-down into papers.

- **Extend to full-text analysis and knowledge graphs**  
  - For corpora where full PDFs are available:
    - Extract sections (methods, datasets, limitations) and index them,  
    - Use an LLM to identify entities (tasks, methods, datasets) and relations,  
    - Visualize a knowledge graph of the research landscape.

- **Automate reproducible survey pipelines**  
  - Provide configuration + scripts so that a complete survey can be reproduced from:
    - A small taxonomy definition file,
    - A corpus export,
    - A fixed LLM prompt/version.

---

## Getting Started (Implementation Placeholder)

This repository currently includes only documentation and a proposed architecture.  
To turn it into a working system, you can:

1. Create a Python virtual environment.  
2. Install dependencies from `requirements.txt` (to be expanded).  
3. Implement the modules in `src/` following the architecture described above.  
4. Start with a small CSV of papers related to your current research topic and iterate on prompts and taxonomies.

As you build out the code, update this README to document:
- Exact commands to run the analysis,
- Configuration file formats,
- Example datasets and outputs.

