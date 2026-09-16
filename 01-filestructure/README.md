# 📁 Topic 01: File Structure & Codebase Architecture
### *The Architecture of Intent — Where You Put Things Is the Architecture*

[![Status: Active](https://img.shields.io/badge/Status-Part%201%20Ready-success.svg)](#-articles-roadmap)
[![Audience: Senior Engineers](https://img.shields.io/badge/Audience-Senior%20Engineers%20(2--5%20yrs)-orange.svg)](#-about-this-topic)

This topic explores the physical organization of production codebases: why folder layouts create default communication patterns (Conway's Law), how technical debt accumulates silently in unowned directories, and how to define crisp module boundaries before external forcing functions break your system.

---

## 🗂️ Topic Structure

```text
01-filestructure/
├── README.md                                  # Topic overview and article roadmap
└── articles/                                  # 📖 Publication-ready Markdown essays
    ├── README.md                              # Articles index and reading sequence
    └── 01-your-folder-structure-is-a-message.md # Part 1: Full publication essay
```

---

## 📚 Articles Roadmap

| Part | Title | Status | Reading Time | Markdown Article |
| :---: | :--- | :---: | :---: | :---: |
| **01** | **Your Folder Structure Is a Message. Most Teams Are Sending the Wrong One.** | ✅ Ready | ~12 min | [Read Part 1](articles/01-your-folder-structure-is-a-message.md) |
| **02** | **The First Cut: How Module Boundaries Get Drawn and Why They Drift** | ⏳ Planned | ~12 min | Coming soon |
| **03** | **The Blast Radius Audit: Diagnosing Coupling Before It Causes an Outage** | ⏳ Planned | ~12 min | Coming soon |
| **04** | **Decisions That Age Well: Writing ADRs Your Team Will Actually Follow** | ⏳ Planned | ~12 min | Coming soon |
| **05** | **Architecture Without Authority: Leading Structural Alignment as a Senior Engineer** | ⏳ Planned | ~12 min | Coming soon |

---

## 🔬 Core Case Studies & Empirical Data in Part 1

Every claim in this series is backed by verified incident postmortems, academic research, and engineering telemetry:

- **Conway's Law & The Inverse Conway Maneuver**: Empirical verification by [MIT and Harvard Business School](https://www.hbs.edu/ris/Publication%20Files/08-039_1861e507-1dc1-4602-85b8-90d71559d85b.pdf).
- **The $460M Dead Code Loss**: Knight Capital's 2012 catastrophe documented in the [SEC Administrative Proceeding](https://www.sec.gov/litigation/admin/2013/34-70694.pdf).
- **GitLab 300GB Database Deletion**: How indistinguishable directory naming contributed to live data loss ([GitLab 2017 Postmortem](https://about.gitlab.com/blog/2017/02/01/gitlab-dot-com-database-incident/)).
- **Apple Siri's 13-Year Structural Debt**: Craig Federighi's confirmation of the failed hybrid attempt and the subsequent $1B/year Gemini licensing deal.
- **Twitter 2010 World Cup Fail Whale**: Eyewitness on-call account and Twitter Engineering's retrospective on migrating off the Monorail to Scala/JVM.
- **Amazon 2002 Bezos Mandate**: How CEO-mandated interface boundaries eliminated coordination friction and accidentally gave birth to AWS.
- **Global Technical Debt**: [CAST Software's analysis](https://www.castsoftware.com/research-labs/technical-debt-estimation) of 10B+ lines of code across 47,000 applications.

---

## 🧭 Navigation
- [← Back to Master System Design Index](../README.md)
