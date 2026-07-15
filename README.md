# OCAI DSL ![Diagrama](docs/images/ocai_dsl_icon.png)

> **OCAI (Orchestrated Context Artificial Intelligence)** is a
> lightweight Domain Specific Language (DSL) for organizing, validating,
> and executing complex prompt-engineering workflows for Large Language
> Models (LLMs).

## Why OCAI?

Traditional prompt engineering relies on increasingly large
natural-language prompts. As projects grow, prompts become difficult to
maintain, reuse, debug, and extend.

OCAI separates **execution logic** from **context**.

Instead of one large prompt, a project is composed of reusable
**Macros**, **Pipelines**, **Runtime Rules**, and **Assets**.

## Core Concepts

### Context

Context remains the primary source of information consumed by the LLM.
OCAI organizes when and how that context is injected.

### Macro

A `$Macro` stores one executable contextual instruction.

``` ocai
$lighting = "Neutral studio lighting with soft illumination."
```

Strings assigned to a Macro are automatically converted into executable
context.

### Pipeline

A `$Pipeline[]` stores an ordered list of Macros.

``` ocai
$scene_pipe[].ADD(Preserve perspective.)
$scene_pipe[].ADD(Match ambient lighting.)
```

Executing the pipeline executes each Macro sequentially.

## Runtime Responsibilities

-   Validate project rules
-   Resolve assets
-   Check requirements
-   Execute pipelines
-   Generate the final prompt

## Asset-Oriented Design

Typical assets:

-   BODY
-   FACE
-   POSE
-   CLOTHING
-   SCENE
-   SCENE ELEMENTS

Each asset contributes only the information for which it is responsible.

## Example Workflow

``` text
BODY
 ↓
FACE
 ↓
WEARING
 ↓
POSE
 ↓
SCENE
 ↓
SCENE ELEMENTS
 ↓
FINAL PROMPT
```

## OCAI vs Context Only

  Context Only             OCAI
  ------------------------ -------------------------------
  Free-form instructions   Structured execution
  Implicit behavior        Explicit behavior
  Large prompts            Modular pipelines
  Difficult maintenance    Reusable components
  Mixed responsibilities   Clear separation of concerns
  Manual organization      Deterministic execution order

## Example

Without OCAI

``` text
Create a fashion image preserving the character, apply the clothes, place the character in the room, preserve identity...
```

With OCAI

``` ocai
$workflow().ADD({
    $wearing_pipe[]
    $scene_pipe[]
    $quality_pipe[]
})

@FRONTEND.PRINT(
    $workflow().EXECUTE()
)
```

## Advantages

-   Modular architecture
-   Reusable components
-   Deterministic execution
-   Easier debugging
-   Easier testing
-   Better scalability
-   Clear separation between control logic and prompt context

## Applications

-   Fashion AI
-   Image generation
-   Video generation
-   Digital Twins
-   CAD interpretation
-   Studio production pipelines

## Design Philosophy

OCAI does not replace prompt engineering.

It provides a deterministic execution layer that prepares, validates,
and organizes context before it reaches the language model.
