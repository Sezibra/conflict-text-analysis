# Conflict Text Analysis: UNSC Framing of the Tigray Conflict

## Research Question

How do UN Security Council member states frame the Tigray conflict, and how does framing vary across geopolitical blocs and over time (November 2020 to November 2022)?

## Background

The Tigray conflict erupted in November 2020 when fighting broke out between the Ethiopian federal government and the Tigray People's Liberation Front (TPLF). The conflict drew in Eritrean forces, Amhara regional forces, and multiple militia groups, producing large-scale civilian casualties, displacement, and a severe humanitarian crisis. The UN Security Council held multiple meetings on the situation between 2020 and 2022, though geopolitical divisions limited formal Council action. China and Russia repeatedly blocked resolutions, while Western members pushed for humanitarian access and accountability.

This project analyzes the language Council members used when discussing Tigray, applying two complementary text analysis methods to identify how different states framed the conflict.

## Data

The corpus consists of 88 text units drawn from publicly available UN documents:

- **4 UNSC verbatim records** (S/PV.8812, S/PV.8843, S/PV.8875, S/PV.8899): Open meeting transcripts from July, August, October, and November 2021. Each record was parsed into individual speeches by Council members, briefers, and invited states, yielding 75 speech-level text units.
- **10 Secretary-General press releases** (February 2021 to November 2022): Short official statements on key conflict developments.
- **1 Security Council press statement** (SC/14501, April 2021): A collective Council position on Ethiopia.
- **2 letters from Eritrea** (S/2021/378, S/2021/510): Formal responses to Council discussions.

All source documents were downloaded from the [UN Digital Library](https://digitallibrary.un.org). The EHRC/OHCHR Joint Investigation Report on Tigray is stored in `data/raw/` as a reference document but was not included in the analytical corpus due to its different institutional register.

Total corpus size: 88 text units, 51,371 words, 33 unique speakers, 20 countries/entities.

## Methods

### Track A: BERTopic (Unsupervised Topic Discovery)

BERTopic (Grootendorst, 2022) replaces LDA as the standard topic modeling approach in computational social science. It works in four stages:

1. **Embedding**: Each text is converted into a 384-dimensional vector using the `all-MiniLM-L6-v2` sentence transformer, capturing semantic meaning.
2. **Dimensionality reduction**: UMAP projects embeddings into a lower-dimensional space suitable for clustering.
3. **Clustering**: HDBSCAN groups semantically similar texts without requiring a pre-specified number of topics.
4. **Topic representation**: c-TF-IDF extracts the most distinctive words per cluster.

Parameters were adjusted for the small corpus (88 documents): `n_neighbors=10`, `min_cluster_size=3`, `min_samples=2`.

### Track B: LLM-as-Annotator (Zero-Shot Classification)

Following Tornberg (2025), the Claude API (Haiku model) classified each text into five predefined conflict framing categories with confidence scores (0.0 to 1.0):

1. **Humanitarian**: Aid access, civilian suffering, food insecurity, displacement.
2. **Security**: Military operations, ceasefire, armed groups, territorial control.
3. **Accountability**: Human rights violations, war crimes, investigations, international law.
4. **Sovereignty**: Non-interference, internal affairs, territorial integrity.
5. **Political process**: Negotiations, dialogue, AU mediation, political settlement.

Each text received a confidence score for all five frames (not mutually exclusive) plus a primary frame assignment.

### Validation

A stratified random sample of 26 documents was hand-coded by the researcher and compared against LLM annotations using Cohen's kappa and a confusion matrix.

## Key Findings

### Finding 1: Humanitarian framing dominates Council discourse

Both methods confirm this. BERTopic assigned 18 of 65 substantive documents to "Humanitarian crisis and access," the largest cluster. The LLM classified 53 of 88 texts with humanitarian as the primary frame. The average humanitarian confidence score across all Council member speeches was 0.66.

### Finding 2: Geopolitical blocs frame the conflict differently

The LLM annotation heatmap reveals clear patterns in average frame scores by country:

| Country group | Humanitarian | Accountability | Sovereignty |
|---|---|---|---|
| Ireland, Norway, Estonia | 0.85-0.88 | 0.55-0.72 | 0.28-0.33 |
| Russia | 0.55 | 0.21 | 0.68 |
| China | 0.59 | 0.06 | 0.61 |

- **Western/Nordic states** (Ireland, Norway, Estonia, UK) scored highest on humanitarian (0.82-0.88) and accountability (0.48-0.72), reflecting their push for humanitarian access and human rights investigations.
- **Russia** scored highest on sovereignty (0.68) and lowest on accountability (0.21), consistent with its position that the Council should not interfere in Ethiopia's internal affairs.
- **China** scored lowest of all countries on accountability (0.06) and high on sovereignty (0.61), aligning with its systematic opposition to international human rights mechanisms.
- **Kenya and India** scored high on political process (0.55 and 0.49), reflecting their emphasis on AU-led mediation and regional solutions.

### Finding 3: Framing shifted over time

The BERTopic topics-over-time analysis shows that the July 2021 meeting (the first open session) had the most diverse topic mix. By October 2021, "UN operations and staff safety" dominated, corresponding to the killing of humanitarian workers during that period. "Peace, security and AU mediation" grew across all four meetings as international pressure for ceasefire negotiations increased.

### Finding 4: BERTopic detects genre alongside theme

A robustness check revealed that BERTopic clusters partly by document type (press releases vs. speeches) rather than purely by theme. When re-run on speeches only with k-means clustering (k=6), four substantive themes emerged that were consistent with the full model: UN humanitarian operations, humanitarian crisis and civilians, peace/security/AU mediation, and conflict/human rights/accountability.

### Finding 5: Validation reveals systematic coding ambiguity

Cohen's kappa between human and LLM coding was 0.239 (fair agreement). The confusion matrix identified two main disagreement patterns:

- **Security vs. Political process**: Speeches discussing ceasefire alongside military dynamics were coded as "security" by the human but "political_process" by the LLM. This reflects genuine ambiguity in UNSC discourse where security and diplomatic frames overlap.
- **Humanitarian vs. Sovereignty**: Speeches by China and Russia that discuss humanitarian conditions but frame responses through sovereignty were coded differently by human and LLM.

This result is consistent with Baumann et al. (2025), who warned that variability across prompt design and category definitions introduces systematic biases in LLM classification.

## Project Structure

```
conflict-text-analysis/
├── notebooks/
│   ├── 01_data_collection.ipynb      # Text extraction and corpus construction
│   ├── 02_bertopic_analysis.ipynb    # Unsupervised topic modeling
│   ├── 03_llm_annotation.ipynb       # Zero-shot classification via Claude API
│   └── 04_validation.ipynb           # Human coding and agreement metrics
├── src/                               # Reusable Python modules
├── data/
│   ├── raw/                           # Original UN PDFs and DOCX files (not tracked)
│   └── processed/                     # Cleaned corpus and annotations (not tracked)
├── outputs/
│   └── figures/                       # All visualizations
│       ├── topic_distribution.png
│       ├── topics_by_country.png
│       ├── topics_over_time.png
│       ├── primary_frame_by_country.png
│       ├── frame_heatmap_by_country.png
│       └── confusion_matrix.png
├── .env                               # API key (not tracked)
├── .gitignore
├── requirements.txt
└── README.md
```

## Reproducing the Analysis

### Requirements

```
Python 3.11 (Anaconda)
bertopic
sentence-transformers
umap-learn
hdbscan
scikit-learn
pdfplumber
anthropic
python-dotenv
pandas
matplotlib
seaborn
```

### Setup

```bash
conda create -n conflict-text python=3.11 -y
conda activate conflict-text
pip install -r requirements.txt
```

### Data

Download the following documents from the [UN Digital Library](https://digitallibrary.un.org) and place them in `data/raw/`:

- S/PV.8812 (2 July 2021)
- S/PV.8843 (26 August 2021)
- S/PV.8875 (6 October 2021)
- S/PV.8899 (8 November 2021)
- S/2021/378 (Letter from Eritrea, 16 April 2021)
- S/2021/510 (Letter from Eritrea, 27 May 2021)
- SC/14501 (Security Council Press Statement, 22 April 2021)
- 10 Secretary-General press releases (SG/SM/20563 through SG/SM/21566)
- EHRC/OHCHR Joint Investigation Report (November 2021, reference only)

### API Key

For Notebook 03 (LLM annotation), create a `.env` file in the project root:

```
ANTHROPIC_API_KEY=your_key_here
```

Obtain an API key from [console.anthropic.com](https://console.anthropic.com). Estimated API cost for the full corpus: under $0.50.

## References

- Baumann, M. et al. (2025). LLM Hacking: Variability in LLM Classification. Working paper.
- Grimmer, J. and Stewart, B.M. (2013). Text as Data: The Promise and Pitfalls of Automatic Content Analysis Methods for Political Texts. *Political Analysis*, 21(3), 267-297.
- Grootendorst, M. (2022). BERTopic: Neural Topic Modeling with a Class-Based TF-IDF Procedure.
- Maerz, S.F. and Puschmann, C. (2020). Text as Data for Conflict Research. In Deutschmann et al. (eds.), *Computational Conflict Research*. Springer.
- Tornberg, P. (2025). LLMs Outperform Expert Coders at Annotating Political Social Media Messages. *Social Science Computer Review*.

## Author

Part of a six-project Computational Social Science portfolio applying quantitative methods to conflict research. Project 1: [Conflict Event Data Analysis](https://github.com/Sezibra/conflict-event-analysis).
