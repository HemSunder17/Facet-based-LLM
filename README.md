# Evidence-Grounded Personality Facet Retrieval and Scoring System

## Overview

This project implements an evidence-grounded pipeline for identifying and scoring personality or behavioral facets from natural language conversation.

The system does not directly assume that every semantically similar facet applies to a person. Instead, it separates the process into two important stages:

1. **Retrieval** – Find facets that may be relevant to the conversation.
2. **Evidence-based scoring** – Score a facet only when there is sufficient direct evidence in the user's statement.

This distinction helps reduce incorrect personality inference caused by semantic similarity alone.

---

## Problem Statement

Natural language statements can be semantically related to many personality traits, even when they do not directly provide evidence for those traits.

For example:

> "I enjoy taking risks and trying new things."

A semantic retrieval model may retrieve:

- Risktaking
- Creative risk-taking tendency
- Adventure-Seeking Behavior
- Creative resilience
- Fearfulness

However, semantic similarity does not mean that every retrieved facet should be scored.

Therefore, this project uses a hybrid retrieval system followed by an evidence-grounding and LLM-based scoring stage.

---

# Pipeline Architecture

The complete pipeline follows the architecture:

```text
Conversation
     │
     ▼
Conversation Segmentation
     │
     ▼
Individual Statements
     │
     ▼
Hybrid Facet Retrieval
(TF-IDF + Semantic Embeddings)
     │
     ▼
Candidate Facets
     │
     ▼
Lexical / Phrase Grounding
     │
     ▼
Evidence Validation
     │
     ▼
LLM-Based Facet Scoring
     │
     ▼
Validated Personality Facet Scores
## Installation

Clone the repository:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
```

Move into the project directory:

```bash
cd <YOUR_PROJECT_FOLDER>
```

Install all required dependencies:

```bash
pip install pandas numpy scikit-learn sentence-transformers groq nltk
```

---

## Dependencies

This project uses the following Python libraries:

- **pandas** – Data loading and manipulation
- **numpy** – Numerical operations
- **scikit-learn** – TF-IDF vectorization and cosine similarity
- **sentence-transformers** – Semantic embedding generation
- **groq** – LLM-based evidence evaluation and facet scoring
- **nltk** – Text and statement processing

---

## Groq API Setup

This project uses the Groq API for LLM-based evidence evaluation and personality facet scoring.

### Step 1: Create a Groq API Key

Create a Groq API key from the Groq platform.

### Step 2: Set the API Key

Set the API key as an environment variable.

On macOS or Linux:

```bash
export GROQ_API_KEY="your_api_key_here"
```

On Windows PowerShell:

```powershell
$env:GROQ_API_KEY="your_api_key_here"
```

### Step 3: Access the API Key in Python

```python
import os
from groq import Groq

client = Groq(
    api_key=os.environ.get("GROQ_API_KEY")
)
```

> **Important:** Never upload or commit your actual Groq API key to GitHub.

---

## Running the Project

After installing the dependencies and configuring the Groq API key:

1. Open the Jupyter Notebook or Google Colab notebook.
2. Run the cells sequentially.
3. Load the personality facet dataset.
4. Build the TF-IDF retrieval representation.
5. Load the Sentence Transformer embedding model.
6. Generate semantic embeddings for the facets.
7. Configure the Groq client.
8. Run hybrid facet retrieval.
9. Split the input conversation into individual statements.
10. Perform evidence-based facet scoring.
11. Apply grounding validation.
12. Generate the final structured JSON output.

---

## Example Python Imports

The project requires imports similar to the following:

```python
import os
import json
import re

import pandas as pd
import numpy as np

from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

from sentence_transformers import SentenceTransformer

from groq import Groq
```

---

## Security

Do not commit API keys or other secrets to the repository.

It is recommended to add the following files to `.gitignore`:

```text
.env
.env.*
```

If environment variables are loaded using a `.env` file, install:

```bash
pip install python-dotenv
```

Then add it to the project dependencies and use:

```python
from dotenv import load_dotenv
import os

load_dotenv()

GROQ_API_KEY = os.getenv("GROQ_API_KEY")
```

Example `.env` file:

```text
GROQ_API_KEY=your_actual_api_key_here
```

The `.env` file should never be committed to GitHub.