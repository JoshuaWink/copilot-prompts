---
applyTo: '**'
---
# ModuLink-Py Cheatsheet

## Core Concepts

- **Context (`ctx`)**: Dictionary passed through all links in a chain.
- **Link**: Pure async function: `async def my_link(ctx: Context) -> Context: ...`
- **Chain**: Sequence of links: `chain = Chain(link1, link2)`
- **Middleware**: Observability, logging, metrics: `chain.use(Logging())`
- **Branching**: Use `.connect()` with conditions for custom flows.

## Quick Start

```shell
install modulink-py
```

```python
from modulink import Chain, Context
from modulink.middleware import Logging, Timing

async def validate(ctx: Context) -> Context:
    if "email" not in ctx:
        ctx["error"] = "Missing email"
    return ctx

async def welcome(ctx: Context) -> Context:
    print(f"Welcome sent to {ctx['email']}")
    return ctx

signup = Chain(validate, welcome)
signup.use(Logging())
signup.use(Timing())

result = await signup.run({"email": "alice@example.com"})
```

## Branching Example

```python
signup.connect(
    source=validate,
    target=handle_error,
    condition=lambda ctx: "error" in ctx
)
```

## Middleware Example

```python
from modulink.middleware import Logging

signup.use(Logging())
```

## CLI Tools

- **Visualize a Chain**:
  ```sh
  python -m modulink.cli_visualize <path_to_chain_file>
  ```
- **Documentation Browser**:
  ```sh
  python -m modulink.modulink-doc <topic>
  ```


## Best Practices

- **Taxonomic Naming:** Name all components from most generic to most specific (e.g., `core.validate.validate_email`).
- **Strict Folder Discipline:** Each folder represents a category or subcategory; never mix unrelated links or chains.
- **Manifest as Source of Truth:** Maintain a central registry (e.g., `manifest.json`) listing all links, chains, and middleware with their intent and file path.
- **Docstrings and Metadata:** Every link, chain, and middleware must declare its purpose and usage in a docstring or metadata.
- **Review and Refactor:** Regularly audit the registry and folder structure to merge duplicates, clarify intent, and prune unused components.
- **Automated Tooling:** Use scripts to validate the manifest, check for orphaned files, and visualize the architecture.
- **Keep links pure and stateless.**
- **Use context for all data flow.**
- **Attach middleware for logging and metrics.**
- **Use explicit branching for error handling.**
- **Organize chains and links in separate files for clarity.**

### Example: Large-Scale Project Structure

```
modulink_project/
  links/
    core/
      validate/
      transform/
      utility/
    domain/
      user/
      item/
      order/
      payment/
      shipping/
      feedback/
  chains/
    onboarding/
    transaction/
    search/
    support/
  middleware/
    logging/
    error/
    security/
    performance/
  registry/
    manifest.json
  config/
  tests/
  docs/
  scripts/
  README.md
```

### Example Manifest Entry
```json
{
  "links": [
    {"name": "validate_email", "category": "core.validate", "intent": "email validation", "file": "links/core/validate/validate_email.py"}
  ],
  "chains": [
    {"name": "transaction_buy", "intent": "buy item workflow", "file": "chains/transaction/transaction_buy.py"}
  ],
  "middleware": [
    {"name": "logging_metrics", "intent": "metrics logging", "file": "middleware/logging/logging_metrics.py"}
  ]
}
```

This approach ensures even the largest ModuLink-py projects remain organized, discoverable, and maintainable.

---

## Core API Reference

- **`Context`**: Alias for `dict`, used for passing data between links.
- **`Chain`**: Main class for composing and running pipelines of links.
  - `.run(ctx)`: Execute the chain with a given context.
  - `.use(middleware)`: Attach middleware for logging, timing, etc.
  - `.connect(source, target, condition)`: Add custom branching.
  - `.add_link(link)`: Add a new link to the chain.
  - `.inspect()`: Get a dictionary representation of the chain structure.
- **`Link`**: Any async function with signature `(ctx: Context) -> Context`.
- **`Middleware`**: Class or function with `before` and `after` hooks for observability.
  - Example: `Logging`, `Timing` (from `modulink.middleware`)
- **`Listener`**: For triggers (HTTP, TCP, etc.), see `modulink.listeners`.
- **`ConditionExpr`**: Boolean or callable for branching logic.

This section gives your team a quick reference to the most important building blocks in modulink-py.

# SocraticStateChain: modulink-py Core Library State

prior:
- Summary: ModuLink-py was created to enable modular, composable, and observable async function orchestration in Python.
- Meta:
    - Key insight: Pure function chains, context-driven data flow, and middleware for observability.
    - Patterns: Chain-based pipelines, error routing, and flexible triggers.
    - Unnecessary complexity is avoided; focus is on clarity and testability.

last:
- Summary: The library’s documentation demonstrates how to define links, compose chains, attach middleware, and use CLI tools for visualization and documentation.
- Meta:
    - Impact: Users can build, observe, and debug complex pipelines with minimal boilerplate.
    - Alignment: Documentation and code are kept in sync through dynamic docstrings and examples.

current:
- Summary: The team is integrating modulink-py into Bird-Bone AI, leveraging its context-driven chains, middleware, and triggers for neural network compression workflows.
- Meta:
    - Bottleneck: Ensuring all contributors understand the core API and best practices.
    - Momentum: Using cheatsheets, code samples, and statechains to accelerate onboarding.

next:
- Summary: Continue to expand usage by:
    - Defining pure async links for each pipeline step.
    - Composing chains with explicit branching and error handling.
    - Attaching middleware for logging, timing, and metrics.
    - Using CLI tools for chain visualization and documentation.
- Meta:
    - Assumptions: All contributors follow the context-first, pure function approach.
    - Alternatives: If advanced needs arise, extend with custom listeners or middleware.

future:
- Path 1 (if path continues):
    - Prediction: The project will maintain high modularity, observability, and testability, with rapid onboarding and robust error handling.
    - Meta:
        - Conditions: Team adheres to modulink-py’s design patterns and updates documentation as chains evolve.
        - Entropy: Low—core concepts remain stable, new features are additive.
- Path 2 (if deviation happens):
    - Prediction: If contributors bypass core patterns (e.g., impure links, hidden state), pipelines become harder to debug and maintain.
    - Meta:
        - Deviation trigger: Ignoring context flow, middleware, or branching best practices.
        - Urgency: High—requires refactoring and retraining to restore clarity.

# Anchors
- Core Types: `Context` (dict), `Link` (async function), `Chain` (pipeline), `Middleware` (observability), `Listener` (trigger).
- Core Functions: `Chain.run`, `Chain.use`, `Chain.connect`, `Chain.add_link`, `Chain.inspect`.
- CLI Tools: `python -m modulink.cli_visualize`, `python -m modulink.modulink-doc`.
- Best Practices: Pure links, context flow, explicit branching, middleware for cross-cutting concerns, dynamic docstrings for documentation.
- **Incremental TDD**: Each link and chain is a pure function, making it easy to write unit tests for every step. Example:
    ```python
    import pytest
    from modulink import Chain

    async def test_link(ctx): return {**ctx, "tested": True}
    chain = Chain(test_link)

    @pytest.mark.asyncio
    async def test_chain_behavior():
        result = await chain.run({"input": 1})
        assert result["tested"] is True
    ```
    - ModuLink’s design encourages testing each link and chain in isolation, supporting incremental, test-driven development as the pipeline grows.

# End Signal
- This statechain is a living reference for all modulink-py usage and documentation in the project. Update as the library or project evolves.


# Refresher: Table of Context (Quick Reverse Lookup)

## All Key Names, Concepts, and Terms

- Manifest
- Registry
- Taxonomic Naming
- Folder Discipline
- Docstrings
- Metadata
- Review and Refactor
- Automated Tooling
- Core API Reference
- ConditionExpr
- CLI Tools
- Logging
- Timing
- Error Handling
- Security
- Performance
- Utility
- Validate
- Transform
- Domain
- User
- Item
- Order
- Payment
- Shipping
- Feedback
- Onboarding
- Transaction
- Search
- Support
- Logging Metrics
- Error Handler
- Security Auth
- Security Audit
- Performance Timer
- Performance Cache
- config_env.py
- config_db.py
- config_api.py
- db_migrate.py
- data_seed.py
- manifest.json
- README.md
- architecture.md
- api_reference.md
- tests/
- scripts/
- docs/
- core types:
    - Context (`ctx`)
    - Link
        - testable unit
    - Chain
    - Chain of chains
    - Middleware
    - Listener
    - Branching / Connections