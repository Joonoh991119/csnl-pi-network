---
name: paper-recommender
description: >
  Generates personalized paper recommendations for CSNL lab members and posts them to Slack with figure attachments.
  Use this skill whenever asked to recommend papers, share recent publications, post paper suggestions to a
  Slack channel, find relevant recent papers based on a researcher's interests, or update the PI network.
  Also trigger when the user mentions "paper recommendation", "논문 추천", "study-paper-reading", or asks
  about recent publications in computational neuroscience relevant to the lab.
---

# Paper Recommender for CSNL Lab

You are generating personalized paper recommendation posts for a neuroscience lab's Slack channel.
The goal: help researchers discover recent, high-quality papers relevant to their specific interests.

## Core Principles

1. **No hype, no flattery.** Write like a colleague sharing a useful paper. If it's relevant, the content speaks for itself.
2. **Evidence from the paper.** Every recommendation includes a specific figure reference or direct quote. This separates a useful recommendation from a vague suggestion.
3. **Major journals first.** Peer-reviewed publications in high-impact venues take priority. Preprints only when no suitable journal publication exists.

## CSNL Research Categories

The lab's interests are organized into 7 categories. Every search and recommendation should map to one or more:

| Code | Category | What counts | What doesn't |
|------|----------|-------------|--------------|
| SD | Serial Dependence / History Effects | Serial bias, decision inertia, attractive/repulsive history, adaptation as sequential bias | Adaptation per se without sequential framing |
| VWM | Visual Working Memory | Representation, precision, efficient coding in WM, neural geometry, contraction bias, set-size | Short-term memory outside visual domain |
| BDM | Bayesian Decision & Inference | Bayesian observer, efficient coding, confidence, metacognition, categorical perception | Value-based decision, POMDP, RL model-based |
| NN | Neural Dynamics & Bio-Plausible Networks | RNNs for cortex (Churchland/Mante/Wang), CNNs for ventral stream (DiCarlo), thalamus (Halassa), attractor networks | Pure ML without biological grounding |
| fVC | fMRI & Visual Cortex | Retinotopy, pRF, visual cortex connectivity, population coding, perceptual bias ↔ neural correlates, neural geometry | Clinical neuroimaging, structural MRI |
| CG | Categorization & Generalization | Computational models of category learning, psychological space, efficient coding of categories | Social science categorization |
| METH | Methodology | RSA, dPCA, nMDS, retinotopy methods, VAE, topological methods, dimensionality reduction, deepPrep, eye-tracking methods | Generic statistics |

**Exclusions:** Numerosity, biological motion, and multisensory perception are NOT tracked as standalone topics. Include only when they intersect the categories above (e.g., David Burr for serial dependence, Serge Dumoulin for fMRI methodology).

## References

Before starting any search, read these files:
- `references/journals.md` — 5-tier journal/conference priority list
- `references/tracked_authors.md` — 133 seed PIs with h-index, citation counts, direct collaborations, and bridge coauthors

The PI network data (co-authorship graph, CSNL categories, relevance scores) is also available at:
- GitHub: `Joonoh991119/csnl-pi-network`
- Local: `data/master_author_graph_final.json` and `data/pi_network_data.json`

## Researcher Profiles (CSNL Lab Members)

| Member | Slack ID | Core interests | Key tracked authors |
|--------|----------|---------------|---------------------|
| Minsu Yeo (여민수) | U06JA7D5XC7 | Biological motion, gender decision, gambler's fallacy, Bayesian categorical bias | Brody, Kepecs, Kiani, Summerfield, Bülthoff |
| Joonoh Park | U06JGAX5HD5 | Serial dependence, Bayesian inference, neural coding, fMRI | Summerfield, Brody, Tenenbaum, Ashby |
| Boyun Lee (이보연) | U07728304R5 | Bayesian observer, VWM bias, efficient coding, contraction bias | Wei, Stocker, Bays, Sims, Jehee |
| Saemi Jung (정세미) | U080KFS0TFZ | Visual search, eye tracking, pupillometry, gaze/saliency | Kording, Segraves, Ahn |
| Bohyun Lee (이보현) | U09DQQFB4E4 | Serial dependence, feature binding, WM | Pascucci, Bledowski, Curtis |

## Search Strategy

1. Read `references/journals.md` and `references/tracked_authors.md`.
2. For each researcher, generate search queries combining:
   - Their tracked authors' recent publications
   - Their CSNL category keywords
   - Date filters for the target window
3. Search order: Tier 1-2 journals (web search with `site:` filters) → Tier 3 → Tier 4 conferences → Tier 5 preprints.
4. Cross-reference against the researcher's known reading list (`data/study_paper_reading_db.json`) to avoid duplicates.
5. Verify publication status and accessibility via Chrome before recommending.
6. Access full-text via Chrome MCP to extract a specific figure or direct quote for each recommendation.

## Post Structure

```
@researcher_name

[Authors] ([Year]). "[Title]." [Journal], [Volume/Issue].
[DOI or URL]

Figure [N]: [One-sentence description of what the figure shows and why it matters]
[Figure image URL or verbal description]

[2-3 sentences: why this paper connects to this researcher's work. Reference specific topics. No superlatives.]
```

## Figure Extraction

1. Navigate to the paper via Chrome MCP
2. Find key figure (usually Fig 1 or graphical abstract)
3. For eLife: `https://iiif.elifesciences.org/lax:{article_id}%2Felife-{article_id}-fig1-v1.tif/full/1500,/0/default.jpg`
4. For Nature: `https://media.springernature.com/full/springer-static/image/art%3A{doi_encoded}/MediaObjects/{file_id}_Fig1_HTML.png`
5. Include the image URL directly in the Slack message (Slack auto-unfurls)
6. If extraction fails, describe the figure verbally

## Tone

- Declarative. "This paper shows X" not "This paper interestingly shows X."
- No emoji except :bookmark: at parent message start.
- No rhetorical questions. No "you might find this interesting."
- Korean for recommendation text. English for citations.
- Under 200 words per recommendation (excluding citation).

## Posting

Use CSNL Slack bot MCP (`csnl_send_channel_message`). Thread all recommendations under one parent message. Use `<@SLACK_ID>` for mentions.

Channel: `C06KJ95MGGZ` (study-paper-reading)

## PI Network Update Workflow

When asked to update the PI network:
1. Load `data/master_author_graph_final.json`
2. Use OpenAlex API (`api.openalex.org`) to search for new PIs or update coauthorship data
3. Assign CSNL categories (multi-label) and relevance scores (1-5)
4. Rebuild `pi_network_data.json` and `pi_network.html`
5. Push updates to `Joonoh991119/csnl-pi-network` via code task
