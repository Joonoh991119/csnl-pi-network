# CSNL PI Network

Interactive co-authorship network of principal investigators in computational neuroscience, built for the Cognitive and Systems Neuroscience Laboratory (CSNL) at Seoul National University.

## Quick Start

Open `pi_network.html` in any modern browser. No server required — all data is embedded.

## What This Is

A force-directed graph of 133 seed PIs and 49 bridge co-authors across 7 research categories relevant to CSNL's work. Co-authorship edges are derived from top-50 cited papers per PI via the OpenAlex API.

Each PI is classified into one or more CSNL-specific research categories, and assigned a relevance score (1–5) reflecting proximity to the lab's core research interests.

## CSNL Research Categories

| Code | Category | Description |
|------|----------|-------------|
| SD | Serial Dependence / History Effects | Serial bias, decision inertia, attractive/repulsive history, adaptation as sequential bias |
| VWM | Visual Working Memory | Representation, precision, efficient coding in WM, neural geometry, contraction bias |
| BDM | Bayesian Decision & Inference | Bayesian observer, efficient coding, confidence, metacognition, categorical perception |
| NN | Neural Dynamics & Bio-Plausible Networks | RNNs for cortex, CNNs for ventral stream, thalamus, attractor networks |
| fVC | fMRI & Visual Cortex | Retinotopy, pRF, visual cortex connectivity, population coding, perceptual bias ↔ neural correlates |
| CG | Categorization & Generalization | Computational models of category learning, psychological space, efficient coding of categories |
| METH | Methodology | RSA, dPCA, nMDS, retinotopy methods, VAE, topological methods, dimensionality reduction |

### What is excluded

Numerosity, biological motion, and multisensory perception are not tracked as standalone categories. PIs working in those areas are included only if their work intersects with the categories above (e.g., David Burr for serial dependence, Serge Dumoulin for fMRI methodology).

Value-based decision making, POMDP, and pure RL model-based approaches are excluded from the Bayesian Decision category. Pure motor control and neuroeconomics are similarly excluded.

## Relevance Scoring

| Score | Meaning |
|-------|---------|
| 5 | CSNL directly researches this PI's topics |
| 4 | Theory or methods directly applicable to CSNL work |
| 3 | Important intersection with CSNL research |
| 2 | Indirectly related |
| 1 | Peripheral — included for field completeness |

## UI Controls

### Category Checkboxes
Toggle entire research categories on/off. "All" and "None" buttons for quick selection.

### Relevance Slider
Filter PIs by minimum relevance score. At threshold 5, only CSNL's core PIs are shown.

### Seed PI Checkboxes
Individual on/off for each of the 133 seed PIs. Multi-colored dots next to each name indicate which categories that PI belongs to.

### Search
Type any PI name to highlight matching nodes.

### Hover
Hovering a node highlights all directly connected nodes and edges. The tooltip shows h-index, citation count, institution, categories, and relevance score.

### Zoom & Pan
Scroll to zoom, drag to pan. Nodes can be dragged to rearrange the layout.

## Multi-Category PIs

Many PIs span multiple categories. This is visualized as colored arc segments surrounding the main node circle. For example, Christopher Summerfield appears in SD + BDM + NN, shown as three colored arcs around his node.

## Data Pipeline

### 1. Source: Lab Slack Channel
The `study-paper-reading` Slack channel (637 messages, 2024-02 to 2026-03) was crawled via Slack MCP to extract each lab member's reading history and identify recurring authors.

### 2. PI Resolution: OpenAlex API
Seed PIs were resolved using the OpenAlex academic database API:
- Author name search with institution disambiguation
- ORCID-based lookup for difficult cases
- Paper-title-based reverse lookup as fallback

For each resolved PI, the top 50 most-cited papers were fetched to extract co-author relationships.

### 3. Co-authorship Edges
Edges between seed PIs represent shared publications. Edge weight = number of co-authored papers in the top-50 set.

### 4. Bridge Co-authors
Non-seed authors who co-authored 3+ papers with two or more seed PIs are identified as "bridge" co-authors. These researchers connect different subfields and are valuable for discovering cross-disciplinary work.

### 5. CSNL Taxonomy
Each PI is manually classified into 1–3 CSNL research categories and assigned a relevance score based on proximity to the lab's active research areas.

### 6. Visualization
The D3.js force-directed graph uses:
- Cluster gravity forces to group PIs by primary category
- Node size proportional to relevance score + h-index
- Arc segments for multi-category membership
- Interactive filtering via checkboxes and slider

## Data Files

| File | Description |
|------|-------------|
| `pi_network.html` | Self-contained interactive visualization |
| `data/pi_network_data.json` | Graph nodes + edges for the visualization |
| `data/master_author_graph_final.json` | Full dataset including all coauthor records and bridge data |
| `data/study_paper_reading_db.json` | Raw paper reading data from Slack channel |
| `data/researcher_reading_profiles.json` | Per-researcher analysis of reading patterns |

## Known Limitations

- Some PIs could not be resolved via OpenAlex due to name disambiguation issues (e.g., common names). These are noted in the tracked authors file.
- Co-authorship counts are based on top-50 cited papers per PI, not complete publication records. Actual collaboration strength may be higher.
- Institution affiliations from OpenAlex may be outdated.
- The CSNL category assignments and relevance scores reflect the lab's 2024–2026 research focus and should be updated as interests evolve.

## Tech Stack

- Data: OpenAlex API (academic metadata), Slack MCP (lab channel data)
- Visualization: D3.js v7 (force-directed graph)
- No build step, no dependencies beyond a browser and CDN-loaded D3
