# Airworthiness Directive (AD) Compliance Pipeline

## Overview
This project provides an automated, production-grade pipeline for parsing Airworthiness Directives (ADs) and evaluating aircraft fleet compliance. The system uses a hybrid architecture: **Large Language Models (LLMs)** for semantic data extraction and **Deterministic Python** for logic-based compliance auditing.

## Features
- **Structured Extraction:** Uses `Pydantic` and `Gemini-3.5-flash` to convert unstructured PDF text into structured JSON.
- **Taxonomy Resolution:** Implements an **Aircraft Taxonomy Graph** to resolve naming inconsistencies between regulatory documents and internal fleet databases.
- **Robust Logic:** Employs numeric fingerprinting for service bulletins and modification codes, preventing false positives caused by varying naming conventions.
- **Auditability:** Compliance decisions are made via deterministic Python logic, separating the "reading" (AI) from the "judging" (Logic) to ensure safety and auditability.

## Installation

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/YOUR_USERNAME/Airbus-AD-Compliance-Pipeline.git](https://github.com/YOUR_USERNAME/Airbus-AD-Compliance-Pipeline.git)
   cd Airbus-AD-Compliance-Pipeline

Install dependencies:

Bash
pip install -r requirements.txt
Configure API:
Set your API key in the AD_Pipeline.ipynb notebook or as an environment variable.

Usage
The pipeline is contained within AD_Pipeline.ipynb. To run:

Ensure your PDF files are in the working directory.

Open the notebook in Jupyter or VS Code.

Execute the cells sequentially. The final cell will output a status table confirming the compliance status of your fleet.

Engineering Report
A comprehensive report covering the architectural approach, technical challenges, and trade-offs is available in REPORT.md.


