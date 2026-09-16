# 🏛️ The Architecture of Intent
### *Where You Put Things Is the Architecture*

[![Status: Active](https://img.shields.io/badge/Status-Active%20%7C%20Part%201%20Ready-success.svg)](#series-roadmap)
[![Format: Markdown & HTML](https://img.shields.io/badge/Format-Markdown%20%7C%20HTML-blue.svg)](#repository-structure)
[![Audience: Senior Engineers](https://img.shields.io/badge/Audience-Senior%20Engineers%20(2--5%20yrs)-orange.svg)](#target-audience)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An open-source, publication-grade series on software architecture written for engineers who already know how to write good code — and are starting to wonder why their systems keep getting harder to change.

---

## 🎯 Target Audience

This series is written specifically for software engineers with **2 to 5 years of production experience**.

It assumes you have already mastered writing clean syntax, testing, and shipping features. You have inherited legacy code you didn't write, broken something three folders away that you didn't know was connected, and spent time in pull request reviews thinking *"this feels wrong, but I can't say exactly why"* — because the industry rarely provides a shared vocabulary for structural failure modes.

---

## 🧭 Repository Structure

> *"Your folder structure is a message. Most teams are sending the wrong one."*

Practicing what we preach, this repository is organized into distinct, self-documenting modules:

```text
Mediumblogs/
├── README.md                                  # Repository overview and series roadmap
├── articles/                                  # Publication-ready Markdown essays for GitHub readers
│   ├── README.md                              # Articles index and reading order
│   └── 01-your-folder-structure-is-a-message.md # Part 1: Full publication draft
├── research/                                  # Research vault: empirical data, blueprints, case studies
│   ├── README.md                              # Research overview and methodology
│   └── 01-folder-structure-research.md        # Comprehensive research file for Part 1
└── medium-export/                             # Browser-ready HTML for 1-click import into Medium
    └── 01-your-folder-structure-is-a-message.html
```

---

## 📚 Series Roadmap

| Part | Essay Title | Status | GitHub Markdown | Medium Ready |
| :---: | :--- | :---: | :---: | :---: |
| **01** | **Your Folder Structure Is a Message. Most Teams Are Sending the Wrong One.** | ✅ Published | [Read Part 1](articles/01-your-folder-structure-is-a-message.md) | [HTML Export](medium-export/01-your-folder-structure-is-a-message.html) |
| **02** | **The First Cut: How Module Boundaries Get Drawn and Why They Drift** | ⏳ Planned | Coming soon | Coming soon |
| **03** | **The Blast Radius Audit: Diagnosing Coupling Before It Causes an Outage** | ⏳ Planned | Coming soon | Coming soon |
| **04** | **Decisions That Age Well: Writing ADRs Your Team Will Actually Follow** | ⏳ Planned | Coming soon | Coming soon |
| **05** | **Architecture Without Authority: Leading Structural Alignment as a Senior Engineer** | ⏳ Planned | Coming soon | Coming soon |

---

## 🚀 How to Read & Publish

### Option 1: Read Directly on GitHub
Navigate to the [`articles/`](articles/) folder and click on any essay (e.g. [`01-your-folder-structure-is-a-message.md`](articles/01-your-folder-structure-is-a-message.md)). All citations, blockquotes, and cross-references render natively.

### Option 2: Publish to Medium (10-Second Workflow)
1. Open the file in [`medium-export/`](medium-export/) (e.g. [`01-your-folder-structure-is-a-message.html`](medium-export/01-your-folder-structure-is-a-message.html)) in any web browser (Chrome, Edge, Safari).
2. Press **`Ctrl + A`** (or `Cmd + A`) to select all, then **`Ctrl + C`** (or `Cmd + C`) to copy.
3. Open Medium's story editor and press **`Ctrl + V`** (or `Cmd + V`).
4. All Medium headings (H1/H2), inline hyperlinks, blockquotes, and horizontal dividers will paste natively with zero formatting loss.

---

## 🔬 Grounded in Primary Research

Every essay in this series avoids armchair opinions. Arguments are anchored in verified incident postmortems, academic research, and industry telemetry:

- **Conway's Law & The Inverse Conway Maneuver**: Melvin Conway's 1967 paper, verified empirically by [MIT and Harvard Business School](https://www.hbs.edu/ris/Publication%20Files/08-039_1861e507-1dc1-4602-85b8-90d71559d85b.pdf).
- **The $460M Zombie Code Disaster**: Knight Capital's 2012 catastrophe caused by dead code in an ambiguous folder ([SEC Administrative Proceeding](https://www.sec.gov/litigation/admin/2013/34-70694.pdf)).
- **The GitLab 300GB Deletion Incident**: Operational failure driven by indistinguishable directory environments ([GitLab 2017 Postmortem](https://about.gitlab.com/blog/2017/02/01/gitlab-dot-com-database-incident/)).
- **Apple Siri's 13-Year Structural Debt**: The architectural constraints that led to a $1B/year Gemini licensing agreement and leadership restructuring.
- **Twitter 2010 World Cup Outages**: The Fail Whale forcing function documented by eyewitness on-call engineers and Twitter's official engineering retrospectives.
- **The Amazon 2002 Bezos API Mandate**: How CEO-mandated interface boundaries eliminated coordination bottlenecks and inadvertently produced AWS.
- **Global Technical Debt**: [CAST Software's analysis](https://www.castsoftware.com/research-labs/technical-debt-estimation) of 10B+ lines of code across 47,000 applications.

---

## 💡 Running Example: The Fintech Engine

Starting in **Part 2**, all architectural concepts are tested against a single concrete open-source case study: a **production fintech backend service**. 
- Handles customer onboarding & authentication
- Double-entry ledger transactions
- Merchant payouts and third-party webhook dispatchers
- Demonstrates realistic domain boundaries, blast-radius containment, and refactoring techniques under pressure.

---

## 📄 License

This work is licensed under the [MIT License](LICENSE). You are free to read, share, and adapt with attribution.
