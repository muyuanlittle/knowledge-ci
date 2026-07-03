# Knowledge CI

Knowledge CI is a knowledge lifecycle management proposal for the AI Coding era.

It explores how software project knowledge — specs, ADRs, design docs, README files, memories, commit messages, pull requests, reviews, and historical decisions — can evolve together with code, instead of drifting away from the real implementation.

> In the AI Coding era, code changes faster than project knowledge. Knowledge CI asks: can knowledge be continuously captured, validated, updated, preserved, and injected back into AI-assisted development workflows?

## Background

AI Coding tools such as Claude Code, Cursor, OpenCode, and other agentic development systems are changing how software is built.

Development is gradually moving from:

```text
Human writes code line by line
to:


Human defines intent
↓
AI Agent implements
↓
Human reviews and governs
This shift significantly increases code production speed. But it also amplifies an old software engineering problem:

Project knowledge often evolves much slower than code.

In traditional projects, knowledge is scattered across:

README
ADR
Design Doc
Product Spec
Technical Note
Engineering Note
Commit Message
Pull Request
Review Comment
Incident Review
Team Memory
These assets explain why a system behaves the way it does. But when code changes frequently, especially under AI-assisted workflows, those knowledge assets can easily become outdated, incomplete, or lost.

The Problem
Knowledge CI focuses on three related problems.

Knowledge Drift
Knowledge still exists, but no longer accurately describes the current code.

Examples:

Documentation says an alert is checked every 60 seconds, but the code now checks every 5 minutes.
An ADR says Redis is used for a sliding window, but the implementation has moved to PostgreSQL aggregation.
A memory records a classification threshold as 0.6, but the code has changed it to 0.65.
Knowledge Drift is dangerous because humans and AI agents may rely on outdated context and make incorrect changes.

Knowledge Debt
Code contains special decisions that require explanation, but the explanation is missing.

Examples:

A magic number may encode a business threshold or experiment result.
A long function may exist because transaction order must be preserved.
A global singleton may manage connection pooling, cache lifecycle, or resource ownership.
An adapter or mapper may preserve compatibility with an external system.
Knowledge Debt is different from technical debt.

Technical debt asks:

Is this code clean?

Knowledge debt asks:

Can we still understand why this code exists?

Knowledge Loss
The historical reason behind a decision can no longer be recovered.

Examples:

Why is the risk threshold 80?
Why does this customer have special compatibility logic?
Why can this bridge module not be removed?
Why is this path hard-coded?
Once this context is lost, teams can only recover it through guesswork, interviews, archaeology, or production incidents.

Core Idea: Knowledge As State
Traditional documentation systems treat knowledge as files:


Document As File
For example:


README.md
docs/payment.md
adr/001-risk-control.md
Knowledge CI explores a different model:


Knowledge As State
That means project knowledge should be managed as an evolving state that can be:

located
verified
tracked
updated
versioned
audited
injected into AI coding workflows
Instead of regenerating documents from scratch, Knowledge CI focuses on how knowledge can continuously evolve with code.

Conceptual Model
Knowledge CI uses a gradual modeling approach.


Source Anchor
↓
Knowledge Claim
↓
Directory Knowledge Unit
↓
Knowledge State
Source Anchor
A Source Anchor is the smallest traceable origin of a piece of knowledge.

It can be:

a file
a directory
a code symbol
a document section
a pull request
an issue
a commit message
a review comment
It answers:

Where does this knowledge come from?

Knowledge Claim
A Knowledge Claim is a verifiable statement about the system.

Examples:

Payment failure is retried at most 3 times.
Risk score above 80 rejects the order.
RefundService depends on the final state of PaymentService.
A compatibility branch exists for a legacy customer.
A temporary workaround was introduced after a production incident.
A Knowledge Claim should be linked to evidence and source anchors.

Directory Knowledge Unit
Instead of requiring perfect business-domain modeling from day one, Knowledge CI starts from repository structure.

A directory can become a first-class knowledge unit:


src/payment/
↓
Payment Directory Knowledge Unit
This makes the system easier to adopt because directory boundaries usually already reflect module ownership, code organization, and incremental scanning boundaries.

Knowledge State
Knowledge State is the continuously evolving collection of:

Knowledge Claims
Source Anchors
Evidence
Decision Context
Risk Context
Version History
Audit Trail
The goal is not to extract a perfect knowledge graph immediately, but to make project knowledge traceable, verifiable, and evolvable over time.

What Knowledge CI Is
Knowledge CI is intended to be a system for:

detecting knowledge drift between code and documentation
identifying knowledge debt in high-risk or high-context code
preserving decision context from Git history, PRs, issues, ADRs, and reviews
generating structured knowledge updates after code changes
validating whether knowledge is still safe to use
injecting relevant knowledge before AI agents modify code
helping teams govern project memory in AI-assisted development
What Knowledge CI Is Not
Knowledge CI is not:

a README generator
a generic documentation generator
a traditional RAG system
a wiki replacement
a code quality scanner
a linter
a static analysis tool focused only on code style
It does not ask only:

Is the code good?

It asks:

Is the knowledge behind the code still accurate, understandable, and usable?

High-Level Workflow
A future Knowledge CI workflow can be described as:


Knowledge Capture
↓
Knowledge Graph
↓
Impact Analysis
↓
Knowledge Evolution
↓
Knowledge Validation
↓
Knowledge Preservation
↓
Knowledge Injection
Before code changes
When a developer or AI Agent is about to modify code, relevant knowledge can be injected:


Code Context
+
Historical Context
+
Decision Context
+
Risk Context
This helps reduce incorrect changes caused by missing background knowledge.

After code changes
When code is merged, Knowledge CI can analyze the change and decide whether related knowledge needs to be updated, marked stale, reviewed, or preserved as historical context.


Code Change
↓
Impact Analysis
↓
Affected Knowledge Claims
↓
Knowledge Patch
↓
Validation / Review
↓
Updated Knowledge State
Design Documents
This repository currently contains design documents and exploratory materials for Knowledge CI.

Recommended reading order:

knowledge-ci.md

Whitepaper-style overview of the background, problem definition, product positioning, core concepts, architecture, and long-term vision.

knowledge-ci-design.md

More implementation-oriented design notes covering Knowledge Drift, Knowledge Debt, Knowledge Loss, Source Anchor, Knowledge Claim, special design detection, freshness gate, and integration with AI Coding workflows.

knowledge-ci-injection-incremental.md

Design notes for Knowledge Injection and incremental knowledge updates, including when knowledge should be injected and when formal knowledge state should be updated.

knowledge-ci-protocol.md

Draft protocol and state-machine design, including PatchRequest, KnowledgePatch, InjectionRequest, InjectionPacket, claim status, patch status, risk scoring, and security boundaries.

knowledge-ci.html

Rendered HTML version of the Knowledge CI whitepaper for easier reading.

Current Status
Knowledge CI is currently in the design and exploration stage.

The repository is focused on:

defining the problem space
clarifying the conceptual model
designing the knowledge lifecycle
exploring protocol boundaries
identifying integration points with AI Coding tools
preparing for future implementation
The implementation is not yet positioned as a stable public product.

Project Direction
Future work may include:

repository scanning
source anchor extraction
knowledge claim modeling
drift detection
special design detection
knowledge patch generation
validation and review workflows
AI Coding context injection
knowledge graph construction
audit and governance mechanisms
One-Sentence Definition
Knowledge CI is a knowledge lifecycle management system for AI-assisted software development: it helps project knowledge continuously evolve with code, remain verifiable, preserve historical decision context, and provide reliable context to AI agents before they modify software.


