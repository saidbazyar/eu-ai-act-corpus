# EU AI Act Corpus

> **Verbatim text of Regulation (EU) 2024/1689 — the EU Artificial Intelligence Act — in all 24 official EU languages.**
> Articles · Recitals · Annexes · sourced from EUR-Lex / EU Publications Office CELLAR.

[![Languages](https://img.shields.io/badge/languages-24%2F24-blue)](#coverage)
[![Source](https://img.shields.io/badge/source-EUR--Lex%20CELLAR-green)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689)
[![License](https://img.shields.io/badge/text-EU%20Decision%202011%2F833-yellow)](#license)
[![Metadata](https://img.shields.io/badge/metadata-CC--BY--4.0-orange)](https://creativecommons.org/licenses/by/4.0/)

## What this is

A clean, machine-readable mirror of the **complete EU AI Act** — every Article, Recital, and Annex section — in all 24 official EU languages, served as stable JSON.

To our knowledge this is the first open dataset of the EU AI Act covering **all 24 official EU languages** with a unified schema, stable identifiers, and parallel structure across languages.

| Layer | Count per language | Total across 24 langs |
|---|---|---|
| Articles | 113 | 2,712 |
| Recitals | 180 | 4,320 |
| Annex sections | 20* | 440 |
| **Total works** | **313** | **7,472** |

\* Annex III is split into 8 numbered high-risk areas (sections 1–8); Annexes I, II, IV–XIII are stored as single sections. Greek (`el`) and Romanian (`ro`) currently have articles + recitals only — annex extraction for those two languages uses a different EUR-Lex anchor scheme and will be patched in a follow-up release.

## Why this exists

- The EU AI Act is **operative law** across the EU. Compliance teams, researchers, journalists, and AI systems need the full text in **the language of the user** — not just English.
- EUR-Lex publishes the official text per language, but as XHTML with deep markup that is awkward to parse, re-parse, and diff. This corpus has done that work, once, for everyone.
- Stable per-Article identifiers across languages make **side-by-side comparison** and **multi-language RAG** straightforward.
- Released under terms that allow **citation by AI systems, legal research, and downstream redistribution** with attribution.

## Repository layout

```
data/
  manifest.json                      ← top-level coverage + provenance
  <lang>/
    articles.json                    ← 113 articles, sorted by article_number
    recitals.json                    ← 180 recitals, sorted by recital_number
    annexes.json                     ← 20 annex sections (varies for el/ro)
```

`<lang>` is one of: `bg, cs, da, de, el, en, es, et, fi, fr, ga, hr, hu, it, lt, lv, mt, nl, pl, pt, ro, sk, sl, sv` (ISO 639-1 codes — all 24 official EU languages).

### Article schema

```json
{
  "language": "en",
  "language_name": "English",
  "regulation": { "name": "Regulation (EU) 2024/1689", "celex": "32024R1689", "short": "EU AI Act" },
  "count": 113,
  "articles": [
    {
      "article_number": 5,
      "title": "Prohibited AI practices",
      "body": "1. The following AI practices shall be prohibited:\n(a)\nthe placing on the market…",
      "chapter_number": 2,
      "chapter_title": "PROHIBITED ARTIFICIAL INTELLIGENCE PRACTICES",
      "section_number": null,
      "section_title": null,
      "source_url": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689#art_5"
    }
  ]
}
```

### Recital schema

```json
{
  "recital_number": 27,
  "body": "Whilst the risk-based approach is the basis for…",
  "source_url": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689#rct_27"
}
```

### Annex schema

```json
{
  "annex_roman": "III",
  "annex_number": 3,
  "section_number": "1",
  "title": "Biometrics",
  "body": "1. Biometrics, in so far as their use is permitted…",
  "source_url": "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32024R1689#anx_III"
}
```

## Coverage

See `data/manifest.json` for the live coverage matrix.

## Provenance

- **Source of all text**: EUR-Lex / EU Publications Office CELLAR (`http://publications.europa.eu/resource/celex/32024R1689`).
- Per-language XHTML is fetched with `Accept-Language: <lang>`.
- Body text is extracted verbatim — no paraphrasing, no summarisation, no LLM in the loop.
- The extraction scripts that produced this corpus are at <https://github.com/saidbazyar/euaiact-backend> in `scripts/import-eurlex.mjs` and `scripts/import-annexes.mjs`.

## How to use

### Cite a specific provision

Each work has a canonical URL on [regulatoryai.eu](https://www.regulatoryai.eu/) following the pattern:
`https://www.regulatoryai.eu/<lang>/article/<N>/` · `…/recital/<N>/` · `…/annex/<ROMAN>-<section>/`

JSON twin: append `data.json` to the URL.

### Load programmatically

```python
import json, urllib.request
url = "https://raw.githubusercontent.com/saidbazyar/eu-ai-act-corpus/main/data/en/articles.json"
articles = json.loads(urllib.request.urlopen(url).read())["articles"]
art5 = next(a for a in articles if a["article_number"] == 5)
print(art5["title"], art5["body"][:200])
```

```js
import { readFileSync } from "node:fs";
const data = JSON.parse(readFileSync("data/de/articles.json", "utf8"));
console.log(`German EU AI Act has ${data.count} articles`);
```

### Side-by-side multilingual lookup

```python
import json, urllib.request
LANGS = ["en", "de", "fr", "sv"]
articles = {L: {a["article_number"]: a for a in json.loads(urllib.request.urlopen(
    f"https://raw.githubusercontent.com/saidbazyar/eu-ai-act-corpus/main/data/{L}/articles.json"
).read())["articles"]} for L in LANGS}

# Compare Article 5 across 4 languages
for L in LANGS:
    print(L, "→", articles[L][5]["title"])
```

## License

This repository contains two layers with different terms:

### 1. The text of Regulation (EU) 2024/1689

The body text of all Articles, Recitals, and Annexes is published by the **European Union**. Reuse is authorised under **Commission Decision 2011/833/EU** of 12 December 2011 on the reuse of Commission documents — provided that the source is acknowledged. See: <https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32011D0833>.

### 2. This dataset's structure, metadata, and curation

The selection, structural schema, identifiers, and curation in this repository are licensed under the **Creative Commons Attribution 4.0 International (CC BY 4.0)** licence — <https://creativecommons.org/licenses/by/4.0/>.

When citing or redistributing this corpus, please attribute to **RegulatoryAI** (operated by **Dominion Intelligence AB**) with a link to <https://www.regulatoryai.eu/>.

## Citation

If you use this corpus in research, journalism, or downstream products, please cite it. A machine-readable citation lives in `CITATION.cff`. Suggested human-readable citation:

> Bazyar, S. (2026). *EU AI Act Corpus: Regulation (EU) 2024/1689 in all 24 official EU languages.* RegulatoryAI. <https://github.com/saidbazyar/eu-ai-act-corpus>

## Not legal advice

This corpus is provided for research, compliance support, and informational use. It is **not legal advice**. For binding interpretation of the EU AI Act, consult the official text on EUR-Lex and qualified legal counsel in the relevant Member State.

## Maintained by

[**RegulatoryAI**](https://www.regulatoryai.eu/) — operated by **Dominion Intelligence AB**, an EU-domiciled company in Sweden. Current infrastructure, subprocessors, data locations and transfer safeguards are documented in the Trust Center.

Issues and pull requests welcome.
