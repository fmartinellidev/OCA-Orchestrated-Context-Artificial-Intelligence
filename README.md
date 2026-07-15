![Diagrama](docs/images/ocai_dsl_icon.png)

> **OCAI (Orchestrated Context Artificial Intelligence)** é uma linguagem específica de domínio (DSL) criada para organizar, validar e orquestrar fluxos de contexto destinados a Large Language Models (LLMs).

O objetivo do OCAI não é substituir Prompt Engineering, mas fornecer uma camada de execução estruturada capaz de tornar projetos complexos mais organizados, reutilizáveis e previsíveis.

---

# Por que o OCAI?

À medida que projetos de IA crescem, os prompts acabam se tornando enormes blocos de texto contendo:

- regras
- exceções
- validações
- fluxos condicionais
- reutilização de instruções

Embora LLMs sejam excelentes interpretando linguagem natural, projetos grandes acabam sofrendo com:

- duplicação de instruções;
- dificuldade de manutenção;
- pouca reutilização;
- ordem de execução implícita;
- comportamento imprevisível.

O OCAI resolve esse problema adicionando uma pequena linguagem declarativa para organizar todo esse contexto.

---

# Filosofia

O OCAI parte de um conceito simples:

> **Contexto continua sendo o que a IA executa. A DSL apenas organiza esse contexto.**

Em vez de escrever um único prompt gigantesco, o projeto passa a ser composto por pequenos blocos reutilizáveis.

```
Contexto
        +
DSL
        +
Runtime
        =
Prompt Final
```

---

# Como funciona

O Runtime interpreta a DSL e prepara o contexto antes que ele seja enviado ao modelo.

```
Usuário
      │
      ▼
 FRONTEND
      │
      ▼
 Runtime
      │
      ├── Assets
      ├── Macros
      ├── Pipelines
      ├── Validações
      ▼
 Workflow
      ▼
 Prompt Final
      ▼
 LLM
```

---

# Principais conceitos

## Macro

Uma Macro representa a menor unidade executável da linguagem.

Ela armazena exatamente uma instrução contextual.

```ocai
$lighting = "Neutral studio lighting."
```

ou

```ocai
$lighting = {
    Use neutral studio lighting.
}
```

Strings atribuídas a uma Macro são automaticamente convertidas pelo Runtime para uma Macro temporária.

---

## Pipeline

Um Pipeline é uma sequência ordenada de Macros.

```ocai
$scene_pipe[].ADD(
    Match ambient lighting.
)

$scene_pipe[].ADD(
    Preserve perspective.
)
```

Ao executar o Pipeline:

```ocai
$scene_pipe[].EXECUTE()
```

cada Macro é executada na ordem em que foi adicionada.

---

## Workflow

O Workflow representa a composição dos diversos Pipelines necessários para executar uma tarefa.

```ocai
$workflow().ADD({
    $wearing_pipe[]
    $scene_pipe[]
    $quality_pipe[]
})

$workflow().EXECUTE()
```

---

# Runtime

O Runtime é responsável por:

- validar regras
- validar Assets
- resolver dependências
- converter Strings em Macros
- executar Pipelines
- montar o Prompt Final

---

# Assets

OCAI foi desenvolvido pensando em fluxos profissionais de IA.

Exemplo:

```
BODY
FACE
POSE
CLOTHING
SCENE
SCENE ELEMENTS
```

Cada Asset possui uma responsabilidade específica.

Exemplo:

BODY

- identidade corporal
- proporções
- anatomia

FACE

- identidade facial
- pele
- olhos
- cabelo

CLOTHING

- roupas
- tecidos
- construção
- materiais

SCENE

- ambiente

SCENE ELEMENTS

- localização espacial
- máscaras
- posicionamento

---

# Exemplo de execução

```
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
Prompt Final
```

Cada etapa adiciona contexto sem substituir a anterior.

---

# Comparação

## Prompt tradicional

```
Crie uma fotografia profissional usando essa personagem.
Preserve sua identidade.
Vista as roupas anexadas.
Aplique essa pose.
Coloque-a na sala.
Preserve iluminação.
Preserve tecidos.
...
```

Tudo está misturado.

---

## Com OCAI

```ocai
$workflow().ADD({
    $wearing_pipe[]
    $scene_pipe[]
    $quality_pipe[]
})

@FRONTEND.PRINT(
    $workflow().EXECUTE()
)
```

O Runtime organiza automaticamente o contexto.

---

# Vantagens

✅ Organização

Separação entre contexto e lógica de execução.

---

✅ Reutilização

Pipelines podem ser reutilizados em vários projetos.

---

✅ Modularidade

Cada módulo possui apenas uma responsabilidade.

---

✅ Manutenção

Alterações ficam localizadas.

---

✅ Escalabilidade

Projetos podem crescer sem transformar prompts em blocos gigantes.

---

✅ Determinismo

A ordem de execução passa a ser explícita.

---

✅ Testabilidade

Cada Pipeline pode ser testado isoladamente.

---

# Casos de uso

O OCAI pode ser utilizado em projetos envolvendo:

- geração de imagens
- geração de vídeos
- moda
- fotografia
- publicidade
- digital twins
- CAD
- arquitetura
- engenharia
- agentes especializados
- automação de prompts

---

# OCAI não substitui Prompt Engineering

O Prompt continua sendo o produto final entregue ao modelo.

O OCAI apenas fornece uma camada de organização, validação e execução capaz de preparar esse Prompt de maneira muito mais estruturada.

---

# Status do projeto

🚧 Em desenvolvimento.

O OCAI está sendo utilizado como base para diversos projetos especializados de IA, incluindo o **Fashion Builder**, voltado para produção profissional de imagens de moda utilizando modelos como GPT Image e Nano Banana.

---

# Licença

Este projeto é disponibilizado como uma especificação aberta para construção de fluxos estruturados de contexto para Large Language Models.

Contribuições são bem-vindas.


---

# In English


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
