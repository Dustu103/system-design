# The First Cut: How Module Boundaries Get Drawn and Why They Drift
*Part 2 of the Production Codebase series. New here? Start with [Part 1](https://medium.com/@arnabpramanik102018/your-folder-structure-is-a-message-most-teams-are-sending-the-wrong-one-986cce6d7e75).*

### *Software Architecture from First Principles — Part 2: Monolithic Entanglement, Coupling Mathematics, and Language-Enforced Boundaries*

---

## The Thirty-Second Test

Clone a healthy production repository, open the root directory in your editor, and inspect the file tree. Within thirty seconds, without inspecting a single function or reading a single implementation file, the operational reality of the system reveals itself. You know where domain business logic lives, where tests are segregated, which team owns the payment processor, how to spin up dependencies locally, which environment variables are mandatory, and how database migrations are sequenced. You have not sent a single message on Slack. You have not interrupted a tech lead. The physical structure of the repository answered your questions before you had to ask them.

In [the first article](https://medium.com/@arnabpramanik102018/your-folder-structure-is-a-message-most-teams-are-sending-the-wrong-one-986cce6d7e75), we established that folder structure is a system design decision — shaped by Conway's Law, [forcing functions](https://medium.com/@arnabpramanik102018/your-folder-structure-is-a-message-most-teams-are-sending-the-wrong-one-986cce6d7e75), and team communication patterns. We introduced the fintech engine and promised to open it. This is that article.

Now open the other kind of repository. Forty-three files sit strewn across the top-level directory. You see `FEATURE_240_DESCRIPTION.md`, `analytics.ts`, `fin.java`, and `IMPLEMENTATION_COMPLETE_SUMMARY.md` mixed directly alongside `package.json` and `tsconfig.json`. Three separate `docker-compose` files exist with names like `docker-compose.local.yml`, `docker-compose.dev.backup.yml`, and `docker-compose.final.yml`, none of which document which one actually works. The `.env.example` file contains stale database credentials from four years ago that reference a Postgres instance that was migrated to Amazon Aurora during the pandemic. You do not begin coding. You close your terminal, open Slack, and ask who knows how to run this project.

The difference between those two repositories has nothing to do with the algorithmic brilliance of the code inside them. It has everything to do with whether the engineers who built the system treated the root directory as an intentional architectural contract or as an unmonitored staging ground for sprint artifacts.

```text
fintech-engine/
├── .github/                    # CI workflows and templates
│   ├── workflows/              # Validation and deployment pipelines
│   └── pull_request_template.md
├── .gitignore                  # Source control boundary
├── .dockerignore               # Build context filter
├── .env.example                # Local execution contract
├── CODEOWNERS                  # Ownership and compliance control
├── LICENSE
├── README.md                   # Entry point and setup guide
├── CHANGELOG.md                # Human-readable change history
├── justfile                    # Task runner interface
├── docker-compose.yml          # Local dependency topology
├── docs/
│   ├── adr/                    # Immutable decision records
│   ├── architecture/           # System maps and diagrams
│   ├── onboarding/             # New engineer ramp-up guides
│   └── runbooks/               # Incident playbooks
├── infra/
│   ├── terraform/              # Cloud resource provisioning
│   └── k8s/                    # Container scheduling manifests
├── migrations/                 # Immutable schema scripts
├── scripts/
│   ├── local/                  # Workstation scripts
│   └── ci/                     # Pipeline scripts
├── src/                        # Application source code
└── tests/
    ├── unit/                   # Fast in-memory tests
    ├── integration/            # Database and broker tests
    └── e2e/                    # End-to-end journey tests
```

---

## The Locational Predictability Imperative

There is an unspoken assumption among junior developers that good engineers read every line of code in the systems they maintain. In a real production system containing one hundred thousand or five hundred thousand lines of code, nobody reads every line of code. Tech leads, staff architects, and on-call responders survive by reading almost none of it. They navigate systems by forming coarse mental maps of ownership, boundaries, and lifecycles, diving into precise implementation lines only when an incident strikes or a specific interface requires extension.

Empirical software engineering research confirms this reality. In their landmark study on developer activity, [*I Know What You Did Last Summer: An Investigation on How Developers Spend Their Time*](https://doi.org/10.1109/ICPC.2015.12), researchers Roberto Minelli, Andrea Mocci, and Michele Lanza tracked developers across professional tasks and discovered that programmers spend 58 to 70 percent of their working time comprehending, navigating, and inspecting codebases, while spending only 5 percent of their time actively editing or typing code. Decades earlier, Robert C. Martin observed in [*Clean Code*](https://www.pearson.com/en-us/subject-catalog/p/clean-code-a-handbook-of-agile-software-craftsmanship/P200000009187) that the ratio of time spent reading code versus writing code exceeds ten to one.

When a junior engineer submits a pull request introducing a new merchant discount rule, a senior reviewer should not have to execute a full-text search across fifty directories to determine where the logic was placed. A well-designed codebase exhibits locational predictability: given a business domain concept or a bug description, any experienced engineer should be able to deduce the exact directory, file, and interface where the modification belongs before opening the file tree. When a codebase lacks locational predictability, every code review becomes an archaeological expedition, and the seventy-percent navigation tax compounds into organizational paralysis.

---

## The Physical Geography of Repository Contracts

The root of a production repository houses the physical contracts governing how code is built, tested, and audited, beginning with `src/` to isolate application domain logic from development scaffolding. Yet treating `src/` as a universal convention overlooks other language ecosystems. In Go repositories, binaries live in `cmd/` while internal libraries reside in [`internal/`](https://go.dev/doc/go1.4#internalpackages). Introduced by [Russ Cox](https://go.dev/doc/go1.4#internalpackages) in [Go 1.4](https://go.dev/doc/go1.4#internalpackages), any package inside an `internal/` directory is mechanically restricted by the compiler: packages outside the immediate parent hierarchy cannot import it. While teams working in TypeScript or Python frequently rely on fragile lint rules that erode under sprint panic, Go turns directory placement into a compiler-enforced boundary.

Database transformations inside `migrations/` operate under a single non-negotiable law: applied migrations are immutable. Once a schema migration script is merged to the main branch and executed against any shared environment, that file must never be modified. Migration frameworks like [Flyway](https://documentation.redgate.com/fd/checksum-calculator-184127411.html) enforce this discipline by calculating [cryptographic checksums](https://documentation.redgate.com/fd/checksum-calculator-184127411.html) of every script and verifying them against a history ledger table, aborting deployments if a single byte has changed. To manage zero-downtime schema evolution safely across rolling releases without table-locking outages, teams apply the [expand-contract pattern](https://martinfowler.com/books/refactoringDatabases.html) formalized by [Pramod Sadalage and Martin Fowler](https://martinfowler.com/books/refactoringDatabases.html), decoupling schema expansions, dual-writing code deployments, data backfills, and contracting column removals into distinct, non-breaking phases.

Repository governance centers around `CODEOWNERS`, which performs three simultaneous architectural jobs. First, it routes pull requests to designated domain experts based on path matching. Second, it acts as a regulatory compliance control for Segregation of Duties (SoD) under [Section 404](https://www.sec.gov/rules/final/33-8238.htm) of the [Sarbanes-Oxley Act (SOX)](https://www.sec.gov/rules/final/33-8238.htm) and [SOC 2 Type II](https://www.aicpa-cima.com/resources/landing/system-and-organization-controls-soc-suite-of-services) [Common Criteria CC6.1](https://www.aicpa-cima.com/resources/landing/system-and-organization-controls-soc-suite-of-services), mathematically preventing authors from self-approving changes to sensitive financial calculation or authorization paths. Third, it serves as an automated [blast-radius](https://medium.com/@arnabpramanik102018/your-folder-structure-is-a-message-most-teams-are-sending-the-wrong-one-986cce6d7e75) audit: any directory lacking an explicit code owner represents an unmonitored path that can bypass mandatory security and architectural reviews.

The defensive perimeter of the repository concludes with `.env.example`, `.gitignore`, and `.dockerignore`. The `.env.example` file is an executable security contract that defines mandatory environment keys, credential origins, and safe local defaults without exposing live secrets. Behind it, `.gitignore` protects against accidental secret leakage into source control—where committing a credential requires immediate rotation and history rewriting with [`git-filter-repo`](https://github.com/newren/git-filter-repo), since git packfiles retain deleted files indefinitely. Alongside it, `.dockerignore` prevents build daemons from leaking local environment files into distributable image layers, backed by pre-commit scanners like [TruffleHog](https://github.com/trufflesecurity/trufflehog) or [Gitleaks](https://github.com/gitleaks/gitleaks) to intercept credentials before they enter git history.

---

## The AI Code Generation Paradox

This discipline matters more in 2026 than it ever has — because the next reader of your repository root is not always human. In 2026, the marginal cost of writing code has effectively dropped to zero. Any developer or autonomous agent can invoke a modern language model and generate five hundred lines of syntactically flawless implementation in seconds. Teams celebrate the illusion of velocity because tickets move across sprint boards faster than ever before.

Yet software engineering has never been governed by the speed of syntax generation; it has always been governed by the economics of maintenance. A comprehensive empirical investigation, [*Coding on Copilot: 2023 Data Shows Downward Pressure on Code Quality*](https://www.gitclear.com/coding_on_copilot_data_shows_ais_downward_pressure_on_code_quality) by GitClear, analyzed 153 million lines of code written between 2020 and 2023. Their research revealed that code churn projected to double compared to pre-AI baselines. Simultaneously, the proportion of copy-pasted code rose dramatically, while deliberate refactoring, code movement, and deduplication plummeted.

The mechanism driving this trend is structural. Large language models operate within finite context windows and optimize for local completion rather than global coherence. When an AI assistant or autonomous coding agent is asked to implement a feature in a sprawling repository with ambiguous directory boundaries, it suffers from **context poisoning and prompt pollution**: scanning the repository root ingests stale handoff documents and conflicting configuration artifacts into the agent's working memory, which the model treats as authoritative ground truth. Rather than constructing a unified domain abstraction, the agent takes the path of least resistance: it duplicates utility helpers, introduces direct cross-module couplings, or dumps ephemeral scripts into the root folder. Without rigid physical boundaries and explicit architectural constraints, AI coding assistants do not eliminate technical debt; they accelerate repository decay at ten times human speed. In the AI era, architecture is no longer merely a human convention. It is the structural fence that prevents automated agents from drowning a production system in unmaintainable sludge.

---

## The Morning After the Folder Reorganization

Now assume your team executed this root discipline flawlessly. You cleaned up root sprawl, established immutable migrations, standardized workstation automation with [`just`](https://github.com/casey/just), decoupled deployment manifests per [ArgoCD](https://argo-cd.readthedocs.io/en/stable/user-guide/best_practices/) guidelines for a [separate config repo](https://argo-cd.readthedocs.io/en/stable/user-guide/best_practices/), and created crisp domain directories inside `src/`: `/auth/`, `/transactions/`, `/ledger/`, and `/webhooks/`. The pull request merged, and the team celebrated.

Then, three weeks later, a critical merchant integration deadline loomed at 4:30 PM on a Thursday. A senior engineer needed to verify whether a customer had an active KYC flag before allowing a webhook retry. The KYC verification lived in `/auth/`. The webhook retry logic lived in `/webhooks/`. Under deadline pressure, the engineer did not design an asynchronous domain event or an anti-corruption interface. They simply typed:

```typescript
import { verifyCustomerKYC } from '../auth/service';
```

The pull request passed code review because the reviewer was rushing to cut the sprint release. It passed continuous integration because the TypeScript compiler resolved the path without issue. With a single keystroke, the architectural boundary was punctured. Six months later, `/auth/` imported `/transactions/`, `/transactions/` imported `/ledger/`, `/ledger/` imported `/webhooks/`, and `/webhooks/` imported `/auth/`. On disk, the repository appeared neatly partitioned into four domain folders. In memory, the application had collapsed into a tangled, circular distributed hairball.

![Diagram 10: Module Boundary Drift](https://raw.githubusercontent.com/Dustu103/system-design/main/01-filestructure/assets/10-module-boundary-drift.jpg)
*Diagram 10: Week 1 Intended Decoupling vs. Month 6 Tangled Hairball*

This represents the central operational dilemma of software architecture: drawing boundaries on a whiteboard is trivial, but keeping them from eroding under sprint pressure is where engineering actually occurs.

---

## The Running Case Study: The Entangled Fintech Engine

To observe how boundaries erode and how to restore them, consider a production fintech payment engine. Financial systems operate under strict regulatory and mathematical constraints, making them ideal for studying boundary decay.

```text
fintech-engine/
└── src/
    ├── auth/
    │   ├── handlers.ts
    │   └── authService.ts        # Directly queries transactions table to check customer status
    ├── transactions/
    │   ├── processor.ts          # Directly mutates ledger rows in the same shared database transaction
    │   └── gateway.ts
    ├── ledger/
    │   ├── doubleEntry.ts        # Imports shared database pool from transactions with zero isolation
    │   └── balances.ts
    ├── webhooks/
    │   ├── dispatcher.ts         # Synchronously invoked by ledger; network timeouts crash payments
    │   └── payload.ts
    └── shared/
        └── db.ts                 # Shared global connection pool exposing raw SQL execution
```

![Diagram 11: The Entangled Fintech Engine State](https://raw.githubusercontent.com/Dustu103/system-design/main/01-filestructure/assets/11-entangled-fintech-engine.jpg)
*Diagram 11: Entangled Dependencies and Shared Database Pool in the Fintech Engine*

This system must protect an inviolable architectural invariant: **the double-entry balance invariant**. Across all ledger accounts, the sum of all debits must strictly equal the sum of all credits at all times. No external service—whether an authentication handler, an external payment gateway adaptor, or a webhook dispatcher—may ever directly write to, bypass, or mutate ledger balance records.

In the unsegregated codebase, that invariant is compromised daily. Novice engineering teams routinely treat account balances as mutable state, issuing destructive updates like `UPDATE accounts SET balance = balance + 100`. In high-throughput environments, this design pattern guarantees row lock contention, deadlocks, and silent reconciliation discrepancies. In contrast, industry benchmark financial platforms—such as [Stripe's immutable ledger](https://www.architecture-weekly.com/p/building-your-own-ledger-database) infrastructure handling five billion events per day—treat money movement strictly as an append-only event stream of balanced debit and credit entries. Balances are derived projections rather than mutable database columns, completely decoupling the financial source of truth from transient application state.

The first time I saw this pattern in production, the circular dependency wasn't obvious from the code — it was obvious from the deployment. Changing the ledger required redeploying the auth service, which required redeploying the webhook dispatcher. Nobody could explain why anymore.

In our entangled fintech engine, when a payment arrives, `processor.ts` opens a database transaction, calls `gateway.ts` to charge a credit card, directly executes an SQL update against the ledger accounts table, and immediately invokes `dispatcher.ts` to send a webhook to the merchant. If the merchant's webhook endpoint times out or returns a 500 error, the entire database transaction rolls back, undoing the ledger entry even though the customer's credit card was already charged. The business logic of the ledger was held hostage by an unreliable external network call because no architectural boundary isolated their failure domains.

---

## The Mathematics of Boundaries: Coupling Metrics That End Subjective Debates

Architectural reviews frequently devolve into subjective debates where senior engineers argue personal preferences regarding clean code. To eliminate subjectivity, architects rely on the [package coupling metrics](https://en.wikipedia.org/wiki/Software_package_metrics) formalized by [Robert C. Martin](https://en.wikipedia.org/wiki/Software_package_metrics).

![Diagram 12: Afferent vs. Efferent Coupling Metrics](https://raw.githubusercontent.com/Dustu103/system-design/main/01-filestructure/assets/12-coupling-metrics-ca-ce.jpg)
*Diagram 12: Afferent Coupling (Ca) vs. Efferent Coupling (Ce) in the Fintech Engine*

[Afferent coupling](https://en.wikipedia.org/wiki/Software_package_metrics), denoted as $C_a$, measures the number of classes or modules outside a package that depend upon classes inside it. A high $C_a$ indicates high responsibility: many parts of the application rely on this module, meaning any breaking changes to its public interface will reverberate across the system. [Efferent coupling](https://en.wikipedia.org/wiki/Software_package_metrics), denoted as $C_e$, measures the number of classes outside a package that classes inside this package depend upon. A high $C_e$ indicates high dependency: the module relies on numerous external abstractions and is vulnerable to breaking whenever its dependencies change.

From these two metrics, [Robert C. Martin's instability metric](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/) detailed in [*Clean Architecture*](https://www.oreilly.com/library/view/clean-architecture-a/9780134494272/) derives the Instability Metric, denoted as $I$:

$$I = \frac{C_e}{C_a + C_e}$$

The Instability metric ranges from zero to one. When $I = 0$, the module is maximally stable: it has zero outgoing dependencies ($C_e = 0$) and many incoming dependencies ($C_a > 0$), making it difficult and expensive to change because many systems rely on it. When $I = 1$, the module is maximally unstable: it has zero incoming dependencies ($C_a = 0$) and multiple outgoing dependencies ($C_e > 0$), making it volatile, easy to change, and dependent on external stability.

Applying this formula to our fintech engine reveals exactly where boundaries must be drawn. The `ledger/core` domain should be designed with $I \approx 0$: nothing in the ledger should depend on authentication, payment gateways, or webhooks. Conversely, `webhooks/dispatcher` should operate with $I \approx 1$: it exists at the volatile edge of the system, consuming domain events and orchestrating outbound HTTP requests.

This leads directly to the **Stable Dependencies Principle (SDP)**: depend in the direction of stability. A module should only depend on modules that are more stable than itself. If a stable module like `ledger` with $I = 0.1$ imports an unstable module like `webhooks` with $I = 0.9$, the instability of the webhook package infects the ledger, destroying its stability and introducing unpredictable regression cascades.

![Diagram 13: Robert C. Martin's Package Metrics and The Main Sequence](https://raw.githubusercontent.com/Dustu103/system-design/main/01-filestructure/assets/13-main-sequence-health-map.jpg)
*Diagram 13: Robert C. Martin's Instability vs. Abstractness Health Map and The Main Sequence*

When a module is maximally stable ($I = 0$), it must also be abstract; otherwise, it becomes rigid and unmaintainable. Conversely, modules that are highly concrete should remain unstable ($I = 1$), allowing them to be modified rapidly without breaking external consumers. Calculating the distance from the Main Sequence, defined as $D = |A + I - 1|$ where $A$ represents abstractness, provides an objective mathematical index of architectural health that eliminates opinion from pull request reviews.

---

## Making Boundaries Irreversible: Language-Level and Tooling Enforcement

Conventions documented in internal wikis or discussed during architecture offsites will always collapse under the pressure of production deadlines. If the compiler, the language runtime, or the continuous integration pipeline allows an illegal cross-boundary import, an engineer working under sprint duress will eventually merge it. Sustainable architecture requires mechanical enforcement.

A landmark demonstration of mechanical boundary enforcement occurred at Shopify between 2019 and 2022. Shopify operated a massive Ruby on Rails monolith containing over 2.8 million lines of code. Over a decade of hypergrowth, coupling between disparate parts of the monolith became so severe that test suites took hours to run, boot times crippled developer workstations, and unexpected regressions occurred continuously. Instead of paying the enormous operational tax of splitting into dozens of microservices, Shopify built and [open-sourced](https://github.com/Shopify/packwerk) [Packwerk](https://shopify.engineering/enforcing-modularity-rails-apps-packwerk) in September 2020. 

Packwerk runs static analysis on Ruby abstract syntax trees during CI builds, enforcing two boundaries separately: package privacy (preventing external packages from referencing internal constants directly) and explicit dependency declarations (forbidding undeclared inter-package couplings). In their honest [retrospective](https://shopify.engineering/a-packwerk-retrospective), Shopify acknowledged that Packwerk is ["a sharp knife"](https://shopify.engineering/a-packwerk-retrospective)—at points they even evaluated removing it due to developer friction and ongoing maintenance overhead. Yet the structural value was undeniable:

> *"Packwerk has provided value in holding the line against new dependencies at the base layer of our application."*

By turning module boundaries into automated build gates, Shopify significantly reduced unexpected regressions, cut test suite times, and maintained the operational simplicity of a single deployable application — without the coordination overhead of splitting into dozens of microservices.

Modern ecosystems provide distinct mechanisms to make boundaries irreversible:

![Diagram 14: The Boundary Enforcement Hierarchy](https://raw.githubusercontent.com/Dustu103/system-design/main/01-filestructure/assets/14-boundary-enforcement-hierarchy.jpg)
*Diagram 14: The Mechanical Boundary Enforcement Hierarchy — From Compiler Gates to Linters*

In the Go toolchain, `internal/` packages enforce privacy directly through the compiler, terminating builds if external packages attempt unauthorized imports. In [Rust](https://doc.rust-lang.org/reference/visibility-and-privacy.html), [`pub(crate)`](https://doc.rust-lang.org/reference/visibility-and-privacy.html) and hierarchical module scoping provide compiler-level guarantees that private domain logic cannot be accessed beyond designated crate or module boundaries.

In the Node.js and TypeScript ecosystem, modern packages utilize the [exports map in package.json](https://nodejs.org/api/packages.html#exports) alongside subpath imports to declare public entry points while marking internal implementations unresolvable to external consumers. Furthermore, monorepos running Nx, Turborepo, or TypeScript Project References use tools like [`eslint-plugin-boundaries`](https://github.com/javierbrea/eslint-plugin-boundaries) to enforce boundary rules in CI, failing builds if presentation layers import persistence databases directly.

In the Java and Kotlin ecosystems, architects achieve equivalent mechanical enforcement using [ArchUnit](https://www.archunit.org/). ArchUnit allows engineers to write unit tests that inspect compiled Java bytecode, [verifying architectural rules in compiled bytecode](https://medium.com/@sugumar.p/enforcing-java-architecture-with-archunit-eeee32b43e98) as automated assertions:

```java
@Test
public void ledgerShouldNotDependOnWebhooks() {
    noClasses()
        .that().resideInAPackage("..ledger..")
        .should().dependOnClassesThat().resideInAPackage("..webhooks..")
        .check(importedClasses);
}
```

If a developer introduces an illegal import between `ledger` and `webhooks`, the build breaks during standard unit testing, preventing the violation from ever reaching a shared branch. Stripe implemented a parallel pattern across their monolithic Ruby codebases, developing the Sorbet static type checker to enforce strict directional dependency graphs and prevent payment processing logic from coupling to customer billing models.

These automated boundary checks represent concrete implementations of [Architectural Fitness Functions](https://evolutionaryarchitecture.com/), an engineering discipline formalized by [Neal Ford, Rebecca Parsons, and Patrick Kua](https://evolutionaryarchitecture.com/) in [*Building Evolutionary Architectures*](https://evolutionaryarchitecture.com/). Just as unit test suites continuously verify that business calculations produce correct numerical output, architectural fitness functions provide continuous, automated verification that structural invariants—such as package instability, directional dependencies, and blast-radius isolation—do not degrade under deadline pressure or unvetted AI-generated PR volume.

---

## The Architecture of Intent

Before an engineer ever traces an execution thread through a database transaction or measures the throughput of an asynchronous message queue, the architecture of the system has already communicated its intent. The macroscopic geography of the repository speaks through its root contracts: its immutable migration ledgers, its isolated infrastructure blast radii, its automated task runners, its regulatory code ownership, and its defensive pre-commit firewalls.

When you step inside `src/`, that same architectural discipline transforms into boundary enforcement. By replacing subjective debates with mathematical coupling metrics, identifying core domain invariants like the double-entry balance rule, and backing those boundaries with compiler and CI gates, teams insulate their systems against sprint fatigue, deadline shortcuts, and unchecked AI-generated code sprawl.

The boundary is drawn. The enforcement is in place. But boundaries are not static — they are stress-tested every time the team grows. In Part 3, we map the universal root of the fintech engine — every folder, every governance file, every security contract — and the precise moment each one became necessary. Before we watch the system grow, we need to understand exactly what we built.
