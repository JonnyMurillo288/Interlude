# Interlude
### Graph-Based Modeling of Artist Collaborations and Synthetic Track Features

---

## Overview

**Interlude** is an end-to-end data engineering and applied machine learning project that models artist collaboration networks and generates synthetic track-level audio features for collaborations that do not yet exist.

The system ingests and normalizes large-scale music metadata from MusicBrainz, AcousticBrainz, and Spotify-derived sources, constructs an artist collaboration graph, and applies interpretable link prediction models to identify high-probability but nonexistent collaborations. These candidate collaborations are then used to condition a generative model that produces structured track feature vectors constrained by artist similarity, graph structure, and popularity signals.

The project emphasizes scalability, interpretability, and production realism over black-box recommendation systems.

---

## System Architecture

### Data Sources
- **MusicBrainz**: artists, recordings, collaboration relationships
- **AcousticBrainz**: high-level acoustic descriptors
- **Spotify-derived metadata**: popularity and exposure signals

### Pipeline

[![Pipeline]([image-url](https://github.com/JonnyMurillo288/Interlude/blob/main/counterpoint_pipeline.svg))]

---

## Data Engineering

- Ingested and normalized **millions of records** across heterogeneous music datasets
- Designed relational schemas for:
  - artists
  - recordings
  - artist collaborations
  - aggregated acoustic feature tables
- Implemented high-throughput batch ingestion with conflict handling
- Resolved production-scale issues including:
  - deadlocks during parallel writes
  - identity column constraints
  - large table drops and schema evolution
- Optimized ingestion and query performance to support iterative modeling

**Technologies:** Python, PostgreSQL, scikit-learn, tensorflow

---

## Graph Modeling & Link Prediction

- Constructed an artist collaboration graph from normalized relational data
- Engineered graph-aware features including:
  - cosine similarity of artist embeddings
  - shared neighbor counts
  - Adamic–Adar scores
  - preferential attachment metrics
- Trained an interpretable logistic regression model for link prediction
- Selected probability thresholds to prioritize:
  - **100% recall** of known collaborations
  - **80–90% precision** for candidate collaboration generation

This framing treats link prediction as a **candidate filtering problem**, rather than a final recommender.

---

## Synthetic Feature Generation

- Used predicted artist pairs as conditioning context
- Trained a **Conditional Variational Autoencoder (CVAE)** to generate:
  - acoustic descriptors
  - genre principal components
  - mood and timbral attributes
- Conditioning variables include:
  - artist embeddings
  - graph proximity
  - popularity signals

The model generates **structured feature vectors**, not audio, enabling downstream use in ranking, simulation, or music-AI model conditioning.

---

## Evaluation & Validation

- **Link Prediction**
  - precision–recall tradeoff analysis
  - held-out edge validation
- **CVAE**
  - reconstruction loss vs KL divergence monitoring
  - latent collapse checks
  - distributional alignment with real track features
- **Sanity Checks**
  - feature bounds
  - genre coherence
  - monotonic relationships with popularity signals

---

## Repository Structure

├── etl/
│   ├── ingest_musicbrainz.py
│   ├── ingest_acousticbrainz.py
│   └── normalize_features.py
│
├── schemas/
│   ├── artists.sql
│   ├── recordings.sql
│   ├── artist_collaborations.sql
│   └── acoustic_features.sql
│
├── graph/
│   ├── build_graph.py
│   ├── compute_embeddings.py
│   └── graph_features.py
│
├── models/
│   ├── link_prediction.py
│   ├── cvae.py
│   └── train_cvae.py
│
├── notebooks/
│   ├── 01_data_validation.ipynb
│   ├── 02_link_prediction_analysis.ipynb
│   └── 03_synthetic_feature_generation.ipynb
│
├── docs/
│   └── architecture.md
│
└── README.md


---

## Future Work

- Replace logistic regression with GNN-based link prediction
- Joint training of graph embeddings and generative models
- Human-in-the-loop evaluation of generated features
- Licensing synthetic feature datasets for music AI model training
- Moving from High-Level data prediction to low level, or exact song generation

---

## Notes

This project prioritizes:
- scalable data engineering
- interpretable modeling
- production-oriented workflows

It intentionally avoids black-box recommender systems or end-user playlist generation.

