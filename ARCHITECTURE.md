# System Architecture Diagram

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                   TRAVEL DESTINATION RECOMMENDATION SYSTEM                    ║
║                        (NLP + Recommendation System)                          ║
╚══════════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────────────────┐
│                              USER INTERFACE (app.py)                         │
│                                  Streamlit                                   │
│                                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │     🎯       │  │     🏆       │  │     📊       │  │     🔎       │  │
│  │ Recommend-   │  │   Popular    │  │ Visualiza-   │  │    Search    │  │
│  │   ations     │  │ Destinations │  │    tions     │  │              │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘  │
│                                                                              │
│  Sidebar:                                                                    │
│  ├─ Natural Language Query Input                                            │
│  ├─ Filters (Region, Budget, Rating)                                        │
│  ├─ Number of Results Slider                                                │
│  └─ Advanced Options (Weights)                                              │
└──────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ↓
┌─────────────────────────────────────────────────────────────────────────────┐
│                           BUSINESS LOGIC LAYER                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │                     NLP MODULE (nlp_module.py)                        │ │
│  │                                                                       │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 1. TEXT PREPROCESSING                                        │   │ │
│  │  │    ├─ Lowercase conversion                                   │   │ │
│  │  │    ├─ Punctuation removal                                    │   │ │
│  │  │    ├─ Stopword filtering                                     │   │ │
│  │  │    └─ Synonym expansion                                      │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  │                              ↓                                        │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 2. EMBEDDING GENERATION                                      │   │ │
│  │  │    ├─ Model: all-MiniLM-L6-v2 (BERT-based)                  │   │ │
│  │  │    ├─ Input: User query + Destination texts                 │   │ │
│  │  │    └─ Output: 384-dim semantic vectors                      │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  │                              ↓                                        │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 3. SIMILARITY COMPUTATION                                    │   │ │
│  │  │    └─ Cosine similarity between query and destinations      │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                       │                                      │
│                                       ↓                                      │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │               RECOMMENDER MODULE (recommender.py)                     │ │
│  │                                                                       │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 1. FILTER APPLICATION                                        │   │ │
│  │  │    ├─ Region filter                                          │   │ │
│  │  │    ├─ Budget filter                                          │   │ │
│  │  │    └─ Rating filter                                          │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  │                              ↓                                        │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 2. SCORE COMPUTATION                                         │   │ │
│  │  │    ├─ NLP Score (from semantic similarity)                   │   │ │
│  │  │    ├─ Content Score (activity profile matching)              │   │ │
│  │  │    └─ Popularity Score (overall rating)                      │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  │                              ↓                                        │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 3. HYBRID AGGREGATION                                        │   │ │
│  │  │    Final = α·NLP + β·Content + γ·Popularity                 │   │ │
│  │  │    (α=0.5, β=0.3, γ=0.2 by default)                         │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  │                              ↓                                        │ │
│  │  ┌──────────────────────────────────────────────────────────────┐   │ │
│  │  │ 4. RANKING & SELECTION                                       │   │ │
│  │  │    └─ Sort by final score → Return top N                    │   │ │
│  │  └──────────────────────────────────────────────────────────────┘   │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                       │                                      │
│                                       ↓                                      │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │                  UTILITIES MODULE (utils.py)                          │ │
│  │                                                                       │ │
│  │  ├─ Bar Charts (Plotly)                                              │ │
│  │  ├─ Pie Charts (Budget distribution)                                 │ │
│  │  ├─ Histograms (Activity popularity)                                 │ │
│  │  ├─ Heatmaps (Activity profiles)                                     │ │
│  │  ├─ Interactive Maps (Folium)                                        │ │
│  │  ├─ Score Breakdown Visualization                                    │ │
│  │  └─ HTML Card Formatting                                             │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────────────────┘
                                       │
                                       ↓
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DATA LAYER                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │         Worldwide-Travel-Cities-Dataset-Ratings-and-Climate.csv       │ │
│  │                                                                       │ │
│  │  Columns:                                                             │ │
│  │  ├─ Geographic: city, country, region, latitude, longitude           │ │
│  │  ├─ Descriptive: short_description                                   │ │
│  │  ├─ Metadata: budget_level, ideal_durations                          │ │
│  │  └─ Activities: culture, adventure, nature, beaches, nightlife,      │ │
│  │                 cuisine, wellness, urban, seclusion                   │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════════════════

                          DATA FLOW DIAGRAM

┌──────────────┐
│     USER     │
└──────┬───────┘
       │ 1. Enters query: "I want beaches in Asia"
       ↓
┌──────────────────────────┐
│    STREAMLIT UI          │
│    (app.py)              │
└──────┬───────────────────┘
       │ 2. Sends to NLP processor
       ↓
┌──────────────────────────┐
│   NLP PROCESSOR          │
│   (nlp_module.py)        │
│                          │
│   • Preprocess query     │
│   • Generate embedding   │
│   • Compute similarities │
└──────┬───────────────────┘
       │ 3. Returns similarity scores for all destinations
       ↓
┌──────────────────────────┐
│   RECOMMENDER            │
│   (recommender.py)       │
│                          │
│   • Apply filters        │
│   • Compute content score│
│   • Calculate hybrid     │
│   • Rank destinations    │
└──────┬───────────────────┘
       │ 4. Returns top N recommendations
       ↓
┌──────────────────────────┐
│   UTILITIES              │
│   (utils.py)             │
│                          │
│   • Format results       │
│   • Create visualizations│
│   • Generate charts      │
└──────┬───────────────────┘
       │ 5. Display results
       ↓
┌──────────────────────────┐
│   STREAMLIT UI           │
│                          │
│   • Show destination cards│
│   • Display score breakdown│
│   • Render visualizations│
└──────┬───────────────────┘
       │ 6. User sees recommendations
       ↓
┌──────────────┐
│     USER     │
└──────────────┘

═══════════════════════════════════════════════════════════════════════════════

                          TECHNOLOGY STACK

┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│  FRONTEND:                                                                   │
│  ├─ Streamlit 1.32.0          (Web framework)                               │
│  ├─ HTML/CSS                   (Custom styling)                             │
│  └─ JavaScript                 (Plotly interactive charts)                  │
│                                                                              │
│  BACKEND:                                                                    │
│  ├─ Python 3.9+                (Programming language)                       │
│  ├─ Pandas 2.1.4               (Data manipulation)                          │
│  ├─ NumPy 1.24.3               (Numerical computing)                        │
│  └─ Scikit-learn 1.3.2         (ML utilities)                               │
│                                                                              │
│  NLP:                                                                        │
│  ├─ Sentence-Transformers 2.5.1 (BERT embeddings)                           │
│  ├─ PyTorch 2.2.0              (Deep learning backend)                      │
│  └─ all-MiniLM-L6-v2           (Pre-trained model)                          │
│                                                                              │
│  VISUALIZATION:                                                              │
│  ├─ Plotly 5.18.0              (Interactive charts)                         │
│  ├─ Folium 0.15.1              (Maps)                                       │
│  ├─ Matplotlib 3.8.2           (Static plots)                               │
│  └─ Seaborn 0.13.1             (Statistical visualizations)                 │
│                                                                              │
│  PERFORMANCE:                                                                │
│  ├─ @st.cache_data             (Data caching)                               │
│  ├─ @st.cache_resource          (Model caching)                             │
│  └─ Vectorized operations       (NumPy/Pandas)                              │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════════════════

                          PROJECT STRUCTURE

rs_travel/
│
├── app.py                      # Main Streamlit application
├── nlp_module.py               # NLP processing and embeddings
├── recommender.py              # Recommendation algorithms
├── utils.py                    # Visualization utilities
│
├── data/
│   └── Worldwide-Travel-Cities-Dataset-Ratings-and-Climate.csv
│
├── requirements.txt            # Python dependencies
├── config.ini                  # Configuration settings
│
├── setup.bat                   # Windows setup script
├── run_app.bat                 # Windows run script
├── .gitignore                  # Git ignore rules
│
├── README.md                   # Project documentation
├── ACADEMIC_DOCUMENTATION.md   # Academic details
├── QUICKSTART.md               # Quick start guide
├── PROJECT_SUMMARY.md          # Delivery summary
├── ARCHITECTURE.md             # This file
│
└── LICENSE                     # MIT License

═══════════════════════════════════════════════════════════════════════════════
