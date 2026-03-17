# Conflict Text Analysis: UNSC Framing of the Tigray Conflict

## Research Question
How do UN Security Council member states frame the Tigray conflict, and how does framing vary across geopolitical blocs and over time (November 2020 to November 2022)?

## Methods
- **BERTopic** for unsupervised topic discovery across UNSC speeches
- **LLM-as-Annotator** (Claude API) for zero-shot classification into conflict framing categories
- Validation via human coding and inter-annotator agreement metrics

## Data
UN Security Council verbatim meeting records (S/PV documents) on Ethiopia/Tigray, sourced from the [UN Digital Library](https://digitallibrary.un.org).

## Project Structure
```
conflict-text-analysis/
├── notebooks/          # Analysis notebooks
├── src/                # Reusable Python modules
├── data/
│   ├── raw/            # Original UN PDFs (not tracked)
│   └── processed/      # Cleaned text corpus (not tracked)
├── outputs/
│   ├── figures/         # Visualizations
│   └── tables/          # Summary tables
├── requirements.txt
└── README.md
```

## References
- Grimmer, J. and Stewart, B.M. (2013). Text as Data. *Political Analysis*, 21(3).
- Grootendorst, M. (2022). BERTopic: Neural Topic Modeling with a Class-Based TF-IDF Procedure.
- Tornberg, P. (2025). LLMs Outperform Expert Coders. *Social Science Computer Review*.
