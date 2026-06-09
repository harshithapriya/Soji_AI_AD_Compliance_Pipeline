# Airworthiness Directive Pipeline: Engineering Report

## 1. Approach
To solve the AD applicability problem, I designed a **Hybrid Architecture** that separates the "reading" task from the "decision" task:

* **Extraction:** I used `PyMuPDF` (`fitz`) to extract raw text from the PDFs.
* **Structuring:** I passed this text to an LLM (`gemini-3.5-flash`) constrained by a strict Pydantic schema to extract the `affected_models` and `excluded_modifications`.
* **Evaluation:** The structured JSON was passed to a deterministic, object-oriented Python engine. This engine resolves aircraft through an **Aircraft Taxonomy Graph**—a master data dictionary that maps specific fleet models to their regulatory aliases.

**Why this method?** Traditional Regex or rule-based parsers fail on regulatory documents because the language varies wildly between authorities. Having previously developed a **Non-Conformity (NC) NLP Pipeline** for parsing fuzzy NC descriptions at Airbus, I applied that same methodology here: using an LLM to normalize unstructured data while keeping the final decision-making logic deterministic. This leverages the LLM for what it does best (reading fuzzy text) while keeping the evaluation process strict, auditable, and safe from LLM hallucinations in boolean logic.

## 2. Challenges & Handling Ambiguity
Bridging the gap between "fuzzy" regulatory language and rigid fleet database records was the primary technical hurdle.

* **Challenge 1: Model Naming Inconsistencies:** Regulatory documents often include manufacturer prefixes (e.g., "Airbus A320-214") while internal fleet data often omits them and could be named simply "A320-214". 
    * **Solution:** I implemented an **Aircraft Taxonomy Graph**. By mapping specific fleet models to their broader parent families and regulatory aliases, the pipeline resolves the aircraft correctly regardless of naming conventions, rather than relying on brittle string matching.

* **Challenge 2: Modification Ambiguity:** Regulators use terms like "Airbus modification (mod) 24591", while fleet databases use "mod 24591 (production)".
    * **Solution:** I implemented a numeric ID extraction filter (`filter(str.isdigit, mod)`). By isolating the core numeric ID as a unique "fingerprint," the pipeline handles exclusionary rules consistently, regardless of how the text is described around the modification number.

## 3. Limitations & Future Work
While highly accurate for the provided test cases, this approach has limitations:
* **Scalability of the Taxonomy:** Currently, the taxonomy is manually curated. To scale this to hundreds of ADs, I would automate the creation of the Taxonomy Graph by using an LLM to generate the mapping from official EASA/FAA type certificates during the initial ingestion phase.

## 4. Trade-offs
* **LLM vs. Pure Code:** I chose an LLM for extraction because writing dedicated parsers for every global aviation authority is not scalable. The LLM handles the unstructured variance perfectly, while Python handles the logic.
* **VLMs vs. Text Extraction:** I opted for standard PDF text extraction (`PyMuPDF`) fed into an LLM. Since the source text layer was intact, using a Vision-Language Model (VLM) would have been an unnecessary cost and latency penalty. However, if future ADs rely on complex scanned tables or visual diagrams, I would integrate a VLM as an additional ingestion node.
* **Speed vs. Accuracy:** By stripping the evaluation logic out of the LLM prompt and placing it in deterministic Python, the pipeline runs near-instantaneously once the rules are parsed. The LLM is only called once per document, not per aircraft, ensuring the system can process large fleets at scale.
