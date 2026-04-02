# Declarative Agents: A Paradigm for AI Workflow Engineering

**YAML-driven, prompt-first, framework-aware agent definitions for production AI systems**

*AI Systems Architecture · Engineering Paper · 2026 — Version 1.1*

---

## Abstract

Declarative agents represent an architectural shift in how AI-powered workflows are designed, governed, and deployed. Rather than embedding agent logic directly inside framework-specific Python or JavaScript code, declarative agents express core workflow intent — graph topology, prompt strategy, tool bindings, guardrails, validation rules, and human oversight checkpoints — in structured YAML specifications. A runtime engine interprets these specifications and maps them onto one or more underlying agent frameworks through adapter layers.

This paper examines the defining characteristics of declarative agents, presents a high-level architecture and execution lifecycle, and evaluates both the benefits and the practical limits of this approach. It argues that declarative agents are particularly well suited for enterprise workflows that demand auditability, portability across common execution patterns, safety-by-default controls, and faster iteration on business logic. At the same time, it recognizes clear boundaries: not all agent behavior should be declarative, and advanced or highly dynamic workloads still require programmable extensions. This paper does not advocate for a "YAML-only" approach, but a hybrid model with clear separation of concerns between reusable agent framework code abstracted by declarative paradigms and use-case-specific, custom developer-written code.

---

## 01 · Introduction: The Declarative Turn in Agent Development

The history of software engineering is, in many ways, a history of abstraction. Every major productivity leap — from assembly to C, from shell scripts to Makefiles, from Puppet DSLs to Kubernetes manifests — followed the same pattern: engineers stopped describing every procedural step and started declaring desired system state. The declarative paradigm reduces cognitive overhead, improves portability, and enables tooling ecosystems that would be difficult to build atop imperative code alone.

AI agent development in 2025–2026 remains predominantly imperative, supported by many cutting-edge agent frameworks. Teams write Python to instantiate state machines, wire tool callbacks, manage prompt templates inside function closures, define checkpoint logic, and handle retry paths through framework-specific APIs. This is flexible, but it tightly couples business workflows to a particular orchestration framework. A small business change — a new approval step, an updated validation rule, a revised safety policy — may require editing code that is deeply aware of the framework's abstractions. A framework migration often becomes a redesign. Also, there is no single best agentic framework, as each has different strengths and limitations. However, agentic frameworks are quite interoperable, allowing them to be used seamlessly with each other.

Declarative agents address this problem by separating **workflow intent** from **execution mechanism**. A declarative agent is an AI workflow whose core behavioral contract is encoded in structured YAML: graph topology, prompts, tools, guardrails, validation policies, checkpoint rules, and deployment metadata. A runtime engine interprets the YAML and orchestrates execution through a framework adapter layer, allowing business logic to remain stable even as execution substrates evolve.

This does not mean every agent behavior can or should be expressed declaratively. The strongest form of the model is not "YAML replaces code," but rather: **YAML defines the workflow contract; code implements the runtime, tools, policies, and integrations that satisfy that contract**.

---

## 02 · Core Characteristics: Twelve Defining Properties

### 01 · YAML-Defined Workflow Specification

The core workflow definition of a declarative agent lives in one or more YAML files. YAML's structure makes the specification reviewable, diff-able in version control, and accessible to stakeholders beyond the framework specialists who would otherwise implement it in code. The specification may cover graph structure, node definitions, prompt references, tool references, guardrail configurations, validation rules, checkpoint logic, and deployment metadata. Here is a sample version of what such a YAML might look like:

```yaml
# customer-support-agent.yaml
agent:
  name: "customer-support"
  version: "1.2.0"
  schema_version: "1.1"
  model_profile: "support-default"
  framework: "auto"   # runtime selects compatible adapter

guardrails:
  input:
    deny_topics: ["competitor pricing", "legal advice"]
    max_tokens: 2048
  output:
    pii_redaction: true
    confidence_threshold: 0.75

graph:
  entry: "classify_intent"
  max_cycles: 3
  nodes:
    - id: "classify_intent"
      prompt_ref: "intent_classifier:v3"
      next:
        billing: "billing_handler"
        technical: "tech_handler"

    - id: "billing_handler"
      tools: ["get_account:v2", "create_ticket:v1"]
      human_checkpoint:
        timeout_s: 120
        default: "auto_resolve"
      next: "generate_response"
```

A key practical point: declarative specifications work best when they define **workflow intent**, not arbitrary computation. YAML is powerful for structured orchestration, but not for replacing all programming logic.

### 02 · Prompt-First Design

English is the new programming language. With advancements in large language models, prompts have become the primary way to encode reasoning as well as operational tasks. Declarative agents embrace a prompt-first design, where each node references a prompt by name rather than embedding prompt text directly in YAML. The actual prompt content lives in a registry, allowing it to be versioned, reused across nodes and agents, and governed separately from workflow structure. Think of it as: instead of code versions, we will maintain prompt versions going forward.

### 03 · Business-Problem-Oriented Workflow

Declarative agents are designed from the perspective of the business problem rather than from the perspective of a framework API. A YAML file reads as a workflow — classify the request, retrieve account information, escalate if needed, generate a response — rather than as a program composed of framework objects and callback wiring.

This orientation makes workflow behavior legible to non-technical stakeholders like product managers, risk teams, compliance reviewers, and business analysts. It also creates a shared artifact around which cross-functional review can happen before deployment.

### 04 · Framework Abstraction Across Common Workflow Patterns

A declarative runtime can map a single workflow specification onto multiple underlying frameworks through adapters. For **portable core patterns** — sequential flows, branching logic, bounded cycles, tool invocation, validation, and human checkpoints — this abstraction is highly effective.

The abstraction aimed at in this design is not universal but rather focused on the most common workflow patterns. Advanced features and domain-specific logic still need to be implemented in code and plugged into the framework in an extensible way.

The abstracted design allows organizations to use their preferred frameworks for agent development while hiding underlying technical details from users of the declarative layer. The design does not express any strong opinions on which frameworks to use; that decision rests entirely with individual organizations.

This framing is more realistic than claiming universal portability for all agent behaviors.

### 05 · Cyclic Graph Support with Explicit Bounds

Declarative agents can express bounded cycles, enabling iterative patterns such as retrieve → assess → refine → retry. This is important for research agents, planning agents, code-generation agents, and other workflows where one pass is insufficient.

However, cycles are not open-ended. Maximum iteration counts, failure thresholds, and terminal conditions must be explicit. This keeps cyclical graphs operationally safe and prevents silent infinite loops.

### 06 · Pre- and Post-Processing Hooks

Nodes may define pre-processing and post-processing stages. Pre-processing can retrieve context, normalize inputs, enrich state, or apply deterministic business rules. Post-processing can parse structured outputs, update state, invoke validators, or enrich downstream context.

These hooks are declared in YAML but usually resolved to named processors implemented in code or plugins. This is a good example of the declarative/programmatic boundary: the YAML specifies *that* a transformation should occur and *which named transformer* to use, while executable logic lives in the runtime extension layer.

### 07 · Input and Output Guardrails as First-Class Policies

Guardrails are not wrappers around the workflow; they are part of the workflow contract. Input guardrails may block disallowed topics, detect prompt injection, enforce token limits, or redact sensitive data before any node is invoked. Output guardrails may enforce PII protection, content safety, confidence thresholds, formatting requirements, or policy disclosures before the response is returned.

Because these rules are declarative and centralized, they become auditable and testable. Security and compliance teams can review guardrail intent from the specification itself rather than searching for ad hoc filtering logic spread through code.

### 08 · Dynamic Prompt Construction with Controlled Boundaries

Declarative agents typically store **foundational prompts** in registries and construct full prompts dynamically at runtime. Runtime composition may include:

* current workflow state,
* retrieved documents,
* prior tool outputs,
* conversation memory within a defined scope,
* task-specific instructions inferred from graph position.

This keeps YAML concise while enabling context-rich model interactions.

A mature declarative system should therefore record:

* foundational prompt version,
* injected context sources,
* model profile,
* node-level execution trace,
* final compiled prompt hash or lineage reference.

Without this, behavior becomes difficult to reproduce even if the YAML itself is stable.

### 09 · Validation After Every Node

A declarative runtime validates outputs after each node before allowing execution to continue. Validation may include:

* required fields and types,
* regex checks,
* enum constraints,
* numerical ranges,
* custom validator references.

If validation fails, recovery strategies may include retry, route-to-error-node, escalation to checkpoint, or structured termination. This reduces the risk of malformed intermediate state silently propagating through a multi-step workflow.

### 10 · Human-in-the-Loop Checkpoints

Any node may be marked as a human checkpoint. At that point, the runtime pauses, persists state, and waits for approval, rejection, or modification. A timeout and default action are mandatory parts of the declaration, ensuring the workflow cannot block indefinitely.

This is especially important in regulated or high-stakes domains where autonomous action may be inappropriate for some decision classes.

### 11 · Standardized Containerized Deployment

A declarative agent deployment typically consists of:

1. a runtime container image,
2. a YAML workflow specification,
3. pluggable registries, secrets, and environment configuration.

This reduces the amount of custom orchestration code required to deploy common agent workflows. It does **not** eliminate all code from the system. Custom tools, validators, policy engines, and integrations still exist — but they are separated from workflow definition and packaged as reusable runtime components.

### 12 · Open Tool and Extension Model

Declarative agents rely on an open extension model. Tool calls may target framework-native tools, organization-specific internal services, or custom deterministic functions. Validator hooks, guardrail policies, and processors may similarly be implemented as pluggable extensions.

This is critical: declarative agents are not valuable because they eliminate programmability; they are valuable because they **localize programmability** to the right layers.

---

## 03 · Design Boundaries: What Should Be Declarative vs Programmable

A declarative system becomes powerful only when its boundaries are clear. Without discipline, teams risk turning YAML into a second programming language — harder to debug than code and less expressive than code.

### Best expressed declaratively

The following elements map well to YAML specifications:

* graph topology and node transitions
* entry points and terminal states
* policy declarations
* prompt references
* tool references
* checkpoint rules
* validation rules
* retry and timeout policies
* deployment metadata
* resource and runtime configuration

These are workflow concerns. They describe structure, intent, and constraints.

### Best kept programmable

The following elements are usually better implemented in code, plugins, or services:

* tool implementations
* custom business algorithms
* advanced state transformations
* adapter internals
* observability exporters
* storage backends
* authentication and integration logic
* framework-specific enhancements
* complex dynamic topology generation

These are execution concerns. They involve algorithmic logic, infrastructure behavior, or external system integration.

### Guiding principle

The YAML should declare **what the workflow is** and **what constraints govern it**. It should not become the place where developers smuggle in arbitrary procedural logic.

---

## 04 · Architecture: High-Level Design and Component Analysis

A declarative agent system consists of seven major layers: the specification layer, parsing and schema validation, the runtime engine, the framework adapter layer, the registry layer, the observability and governance layer, and the deployment layer.

```
┌──────────────────────────────────────────────────────────────────────┐
│  DEPLOYMENT ENVIRONMENT                                              │
│  container runtime · kubernetes · cloud run · ecs · on-prem         │
└───────────────────────────────┬──────────────────────────────────────┘
                                │
┌──────────────────────────────────────────────────────────────────────┐
│  DECLARATIVE AGENT RUNTIME CONTAINER                                 │
│                                                                      │
│  ┌───────────────────────────────────────────────────────────────┐   │
│  │ YAML SPECIFICATION                                            │   │
│  │ graph · prompts · tools · guardrails · validators · metadata  │   │
│  └───────────────────────────────┬───────────────────────────────┘   │
│                                  │                                   │
│  ┌───────────────────────────────▼───────────────────────────────┐   │
│  │ PARSER + SCHEMA VALIDATOR                                    │   │
│  │ schema version · graph consistency · ref resolution          │   │
│  └───────────────────────────────┬───────────────────────────────┘   │
│                                  │                                   │
│  ┌───────────────────────────────▼───────────────────────────────┐   │
│  │ RUNTIME ENGINE                                                │   │
│  │ input guardrails · prompt engine · graph engine · validators │   │
│  │ checkpoint coordinator · pre/post hooks · state manager      │   │
│  └───────────────┬───────────────────────────────┬──────────────┘   │
│                  │                               │                  │
│  ┌───────────────▼──────────────┐   ┌───────────▼────────────────┐ │
│  │ REGISTRIES / EXTENSIONS      │   │ OBSERVABILITY + GOVERNANCE │ │
│  │ prompts · tools · policies   │   │ traces · audit · lineage   │ │
│  │ validators · processors      │   │ approvals · policy checks  │ │
│  └───────────────┬──────────────┘   └───────────┬────────────────┘ │
│                  │                               │                  │
│  ┌───────────────▼───────────────────────────────────────────────┐  │
│  │ FRAMEWORK ADAPTER LAYER                                       │  │
│  │ LangGraph · Google ADK · Amazon Strands · custom runtime      │  │
│  └───────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

### Component A · YAML Specification Layer

The YAML specification is the source of truth for workflow intent. It should contain the behavior that must be reviewable, governable, and version-controlled:

* graph topology,
* node references,
* policies,
* checkpoints,
* prompt and tool references,
* deployment metadata.

It should not contain executable logic or runtime state.

### Component B · Parser and Schema Validator

The parser loads the YAML and validates it against a versioned schema. Validation includes:

* required fields,
* reference resolution,
* graph consistency,
* cycle bounds,
* policy syntax,
* tool and prompt reference validity.

A production-grade system should support **schema evolution** through explicit `schema_version` fields and migration tooling. Without versioning, declarative systems become brittle as the platform evolves.

### Component C · Runtime Engine

The runtime engine interprets the specification and executes it. It manages:

* boundary guardrails,
* dynamic prompt composition,
* graph traversal,
* state management,
* validation and retries,
* checkpoint persistence and resume behavior,
* hook execution.

The runtime is where declarative intent becomes operational behavior.

### Component D · Registry and Extension Layer

Registries resolve prompt references, tool references, validators, processors, and policy implementations. They are a key mechanism for reuse and governance. Instead of embedding large prompt bodies or implementation logic directly in YAML, the specification points to versioned assets in registries.

Examples:

* `intent_classifier:v3`
* `get_credit_profile:v2`
* `response_disclosure_regex:v1`

This makes behavior composable and easier to govern.

### Component E · Framework Adapter Layer

Adapters translate generic runtime operations into framework-specific APIs. Each adapter may implement a common interface such as:

* `compile_graph(spec)`
* `invoke_node(node, state)`
* `handle_tool_call(ref, args)`
* `persist_checkpoint(state)`
* `resume_checkpoint(id, input)`

The adapter layer is essential to reducing framework lock-in, but it is also where semantic mismatches appear. It should therefore include capability negotiation and clear feature compatibility reporting rather than pretending all frameworks behave identically.

### Component F · Observability and Governance Layer

Observability is not optional in declarative systems. Because behavior emerges from multiple layers — YAML, registries, runtime composition, adapters, and LLM outputs — the system must expose high-fidelity traces.

A production implementation should capture:

* execution traces by node,
* tool call logs,
* retry and failure events,
* validation failures,
* checkpoint lifecycle events,
* prompt/version lineage,
* latency metrics,
* active spec version,
* policy decisions and redactions.

Governance should include:

* approval workflows for spec changes,
* policy-as-code checks in CI/CD,
* prompt and tool version pinning,
* provenance for deployed specs,
* separation of duties between authors and approvers.

### Component G · Deployment Layer

The deployment model is standardized but not code-free. A typical deployment consists of:

* a runtime container,
* one or more YAML specs,
* registry connectivity,
* secrets,
* storage backends,
* extension packages,
* environment-specific configuration.

This still dramatically reduces bespoke orchestration code for common agent workflows, even though the broader system contains supporting implementations.

---

## 05 · Execution Lifecycle: Typical Flow for a Declarative Agent

Consider a **loan pre-qualification agent** for a financial services firm. It must classify an applicant request, retrieve a credit profile, evaluate eligibility, route high-risk cases for human review, generate a compliant response, and preserve full auditability.

### Step 01 · Specification Load and Version Validation

At startup, the runtime loads `loan-prequalification-agent.yaml`. The parser validates:

* schema version compatibility,
* prompt and tool reference existence,
* node graph consistency,
* cycle limits,
* checkpoint defaults and timeouts,
* validator rule syntax.

If any validation fails, startup halts with a structured schema error.

### Step 02 · Input Guardrail Inspection

The applicant submits: *"I need a $450,000 mortgage. My income is $120,000/year. I've had some late payments."*

The input guardrail layer:

* checks topic policy,
* scans for injection patterns,
* evaluates token budget,
* tags sensitive data,
* verifies input structure.

All checks pass, and the message enters execution with tagged sensitivity metadata.

### Step 03 · Intent Classification

The graph engine invokes `classify_intent`. The runtime resolves `intent_classifier:v3`, composes the node prompt from the foundational prompt plus state and session metadata, and sends it to the model.

The model returns structured output:

```json
{"intent": "mortgage_prequalification", "amount": 450000, "confidence": 0.94}
```

The validator confirms required fields and value ranges. Routing proceeds to `credit_lookup`.

### Step 04 · Credit Lookup with Tool Enrichment

A pre-processing hook calls `get_credit_profile:v2` using session identity. The tool returns a structured profile. The prompt engine enriches the node context with the profile, the applicant's declared information, and policy thresholds.

The node output is post-processed into structured assessment fields:

* eligibility band,
* risk class,
* missing disclosures,
* need for manual review.

Validation confirms the assessment shape and allowed enum values.

### Step 05 · Human Review Checkpoint

Because the case is classified as high-risk, the workflow reaches `senior_review`. The runtime:

* persists current state,
* records checkpoint lineage,
* sends a review request through the configured reviewer channel,
* waits for human input or timeout.

The compliance officer approves a conditional pre-qualification. If no response had arrived within the configured window, the declared default behavior would have been applied.

### Step 06 · Response Generation

The `generate_response` node resolves a response prompt, composing context using:

* the validated credit assessment,
* the human decision,
* required disclosures,
* policy references.

A post-processor formats the output and injects mandatory disclosure language.

### Step 07 · Output Guardrail and Delivery

Before the response exits the system, output guardrails:

* redact protected values,
* verify confidence thresholds,
* enforce disclosure formatting,
* apply content safety checks.

The response is then returned. The runtime writes a structured audit trace containing:

* spec version,
* prompt references,
* tool versions,
* node outcomes,
* validation results,
* checkpoint events,
* redaction actions.

---

## 06 · Benefits of the Declarative Agent Approach

### Productivity

Declarative agents reduce the amount of framework-specific orchestration code needed to implement common multi-step workflows. Teams can describe workflow behavior at the level of business intent rather than framework plumbing. This can significantly reduce time-to-agent for well-structured use cases.

### Portability Across Common Patterns

The strongest portability benefit is not universal framework equivalence, but rather portability for a large set of common workflow patterns. This reduces migration friction, supports comparative framework evaluation, and helps organizations avoid binding business workflows too tightly to one runtime.

### Governance and Auditability

Because workflow intent is explicit and version-controlled, declarative agents improve reviewability. Pull requests can show not just code changes, but workflow changes: a new checkpoint, a tightened guardrail, an altered routing rule, a changed prompt reference.

### Safety by Construction

Guardrails, validators, and human checkpoints are part of the contract, not optional wrappers. This shifts safety left in the development lifecycle and makes it easier to enforce minimum control requirements consistently.

### Reuse and Standardization

Prompt registries, tool registries, and policy libraries allow organizations to reuse approved building blocks across many agent workflows. This is especially valuable in large enterprises where governance consistency matters.

### Faster Iteration on Business Logic

Changes such as adding a node, adjusting a retry policy, changing a prompt reference, or inserting a review checkpoint often require spec updates rather than orchestration rewrites. This compresses the iteration loop for workflow design.

---

## 07 · Limitations and Constraints

### Expressiveness Ceiling

Not all logic belongs in YAML. Highly dynamic topologies, algorithmically complex transformations, or workflow structures that emerge only at runtime may not fit the declarative model cleanly. In these cases, forcing everything into YAML produces "configuration programming," which is often worse than code.

### Debugging Complexity

Unexpected behavior may originate in:

* the YAML specification,
* a prompt registry version,
* runtime prompt composition,
* a validator,
* a framework adapter,
* the underlying framework,
* the model itself.

Declarative systems therefore require excellent observability, tracing, and lineage reporting. Traditional debuggers alone are insufficient.

### Adapter Lag and Semantic Mismatch

Adapters inevitably trail underlying frameworks. When frameworks introduce new capabilities, the declarative platform must implement support before users can adopt them through the spec layer. Also, some features do not translate cleanly across frameworks.

### Runtime Overhead

Validation, prompt assembly, adapter translation, registry resolution, and tracing all add latency beyond raw LLM invocation. For high-throughput or latency-sensitive workloads, runtime efficiency becomes an important engineering concern.

### YAML Scalability Risks

YAML is readable at moderate size, but large specifications can become hard to understand. Risks include:

* nested complexity,
* poor modularity,
* indentation fragility,
* duplicated blocks,
* schema sprawl.

A mature platform should support composition, includes, templating boundaries, schema linting, and visual graph inspection to prevent specifications from becoming unmanageable.

### Learning Curve

Declarative systems trade framework complexity for platform complexity. Teams must learn the spec schema, registry model, capability tiers, validation semantics, and debugging tools. Adoption requires documentation, examples, and good validation feedback.

### Versioning and Compatibility Burden

Once declarative specs are widely used, schema evolution becomes a platform responsibility. Backward compatibility, migration tooling, prompt version pinning, tool contract changes, and adapter capability reporting become essential operational concerns.

---

## 08 · Failure Modes and Operational Realities

A production-oriented model should acknowledge common failure cases explicitly.

### Common failure modes

* node references a prompt version that no longer exists
* tool schema changes but YAML still points to the old contract
* validation rule is too strict and creates retry loops
* checkpoint timeout default creates unintended business outcome
* adapter implements a framework feature with slightly different semantics
* compiled runtime prompt exceeds expected token budget
* cycle bounds are legal but operationally too loose
* parallel branches produce incompatible state updates

These are not arguments against declarative agents. They are arguments for treating the declarative platform itself as a serious software product with strong validation, compatibility checks, and operational tooling.

---

## 09 · Conclusion: Looking Forward

Declarative agents are a compelling architectural model for enterprise AI systems because they separate workflow intent from execution implementation. They make it easier to review, govern, version, and evolve structured agent workflows without embedding every business change deep inside framework-specific code.

Their strength is not that they eliminate code. Their strength is that they place code in the right places:

* workflow structure in specifications,
* reusable implementations in registries and plugins,
* runtime behavior in orchestrators,
* framework specifics in adapters,
* governance in policy and observability systems.

The result is an engineering model in which:

* business logic becomes more auditable,
* workflow change becomes faster,
* safety controls become explicit,
* framework lock-in is reduced for common patterns,
* cross-functional collaboration becomes more realistic.

The limitations are real. Some workflows are too dynamic to express cleanly in declarative form. Adapter parity will always lag the frameworks underneath. YAML can become a maintenance burden if allowed to absorb too much logic. But these are design constraints, not fatal flaws.

The long-term direction of agent engineering is toward greater abstraction, better governance, stronger reproducibility, and broader accessibility. Declarative agents — when implemented with clear boundaries, versioned schemas, registry discipline, and observability-first design — are a strong step in that direction.

> The most durable abstractions do not remove complexity; they relocate it to the layers best suited to manage it. Declarative agents do exactly that: they move workflow intent into a governable specification layer while isolating execution complexity inside runtimes, registries, and adapters.
