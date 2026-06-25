# Mini RAG Lab

Mini RAG Lab is a local-first, Azure-ready project structure for building a small Retrieval-Augmented Generation system.

The goal of this repository is to document and organize the architecture before writing application code. It is intentionally empty from an implementation perspective: the current version contains only the project structure, documentation, and placeholders for the future modules.

## Project Goal

This project is designed to become a small but complete RAG application capable of ingesting documents, splitting them into chunks, generating embeddings, storing vectors in PostgreSQL with pgvector, retrieving relevant context through similarity search, and generating grounded answers with source references.

The first implementation target is local development. The architecture should remain ready to integrate with Azure OpenAI later without rewriting the core RAG flow.

## Why This Project Exists

Large Language Models can produce confident answers even when they do not have access to the correct context. RAG reduces this risk by retrieving relevant information from a controlled knowledge base before asking the model to generate a final answer.

This repository is meant to demonstrate the engineering concerns behind a practical RAG system, including:

- document ingestion
- text normalization
- chunking strategy
- embedding generation
- vector storage
- semantic retrieval
- prompt construction
- source attribution
- hallucination mitigation
- guardrails
- audit logging
- local-first development
- Azure-ready provider abstraction

## Target Architecture

```text
Documents
   ↓
Ingestion Pipeline
   ↓
Text Cleaning and Chunking
   ↓
Embedding Provider
   ↓
PostgreSQL + pgvector
   ↓

User Question
   ↓
Question Embedding
   ↓
Vector Similarity Search
   ↓
Relevant Chunks
   ↓
Prompt Builder + Guardrails
   ↓
Chat Provider
   ↓
Grounded Answer + Sources
```

## Local-First, Azure-Ready Approach

The project should be developed locally first. PostgreSQL with pgvector can run through Docker, and AI providers should be abstracted behind interfaces.

The core application should not depend directly on Azure OpenAI. Instead, the project should define provider contracts such as:

```text
EmbeddingProvider
ChatProvider
```

Then the implementation can support multiple providers over time:

```text
EmbeddingProvider
  ├── LocalEmbeddingProvider
  └── AzureOpenAIEmbeddingProvider

ChatProvider
  ├── LocalChatProvider
  └── AzureOpenAIChatProvider
```

This keeps the RAG architecture stable while allowing the model provider to change through configuration.

## Planned Technology Stack

| Layer | Planned Choice |
|---|---|
| Language | Python |
| API | FastAPI |
| Database | PostgreSQL |
| Vector Extension | pgvector |
| Local Runtime | Docker Compose |
| Embeddings | Local provider first, Azure OpenAI later |
| Chat Model | Local provider first, Azure OpenAI later |
| Future UI | CLI, Streamlit, or simple web client |
| Future Cloud Target | Azure OpenAI, Azure Container Apps, Azure Database for PostgreSQL |

## Repository Structure

```text
mini-rag-lab/
  app/
    ai/
    rag/
    database/
  docs/
  prompts/
  sql/
  tests/
  README.md
```

### `app/ai`

Reserved for AI provider abstractions and future concrete provider implementations.

Expected future responsibilities:

- define embedding provider contracts
- define chat provider contracts
- isolate Azure OpenAI integration details
- allow local providers during development
- centralize model configuration

### `app/rag`

Reserved for the core RAG flow.

Expected future responsibilities:

- document ingestion orchestration
- chunking
- retrieval
- prompt building
- answer generation
- guardrail enforcement
- evaluation helpers

### `app/database`

Reserved for database connection and persistence concerns.

Expected future responsibilities:

- PostgreSQL connection setup
- document repository
- chunk repository
- query log repository
- vector search queries

### `docs`

Reserved for sample knowledge base documents.

The first version should use Markdown or plain text files instead of PDFs. This keeps the initial scope focused on RAG architecture instead of document parsing complexity.

Example future documents:

```text
refund-policy.md
webhooks.md
api-authentication.md
billing.md
security.md
```

### `prompts`

Reserved for system prompts and RAG prompt templates.

Expected future files:

```text
system_prompt.txt
rag_prompt.txt
```

The RAG prompt should instruct the model to answer only from retrieved context and to refuse when the context is insufficient.

### `sql`

Reserved for database setup scripts.

Expected future scripts:

```text
001_enable_pgvector.sql
002_create_documents_table.sql
003_create_document_chunks_table.sql
004_create_rag_queries_table.sql
```

### `tests`

Reserved for automated tests and evaluation fixtures.

Expected future test areas:

- chunking behavior
- retrieval quality
- source attribution
- hallucination prevention
- fallback behavior when no context is found

## Planned Database Model

The first implementation should include at least these concepts:

### Documents

Stores the original document identity.

Expected fields:

```text
id
title
source
created_at
```

### Document Chunks

Stores the chunked text and its vector representation.

Expected fields:

```text
id
document_id
chunk_index
content
content_hash
metadata
embedding
created_at
```

### RAG Queries

Stores basic audit information about user questions and generated answers.

Expected fields:

```text
id
question
answer
retrieved_chunks
model
created_at
```

## Guardrails

The first implementation should include basic application-level guardrails before relying on any cloud-specific security feature.

Planned guardrails:

- answer only from retrieved context
- refuse when context is insufficient
- require sources for grounded answers
- avoid generating unsupported policies, numbers, or claims
- treat document text as data, not as instructions
- log retrieved chunks for auditability
- avoid regenerating embeddings for unchanged chunks

## Evaluation Strategy

The project should include a small evaluation dataset with questions, expected sources, and expected answer behavior.

Example evaluation cases:

```json
[
  {
    "question": "What is the refund period?",
    "expected_source": "refund-policy.md",
    "expected_answer_contains": ["7 days"]
  },
  {
    "question": "Does the product support Bitcoin payments?",
    "expected_source": null,
    "expected_behavior": "should_not_answer"
  }
]
```

This evaluation layer should help verify whether the system retrieves the right sources, avoids hallucination, and behaves correctly when the knowledge base does not contain enough information.

## Roadmap

### Phase 1: Project Skeleton

- create repository structure
- document architecture
- define local-first and Azure-ready direction

### Phase 2: Local RAG Foundation

- add Docker Compose for PostgreSQL with pgvector
- add database schema scripts
- add sample Markdown documents
- implement ingestion pipeline
- implement chunking
- store chunks and embeddings

### Phase 3: Retrieval and Answer Generation

- generate embedding for user questions
- retrieve similar chunks from pgvector
- build RAG prompt with retrieved context
- generate grounded answer
- return source references

### Phase 4: Guardrails and Auditability

- add insufficient-context fallback
- add prompt injection resistance at the prompt/application level
- add query logging
- add embedding cache through content hashing
- add basic tests

### Phase 5: Azure Integration

- add Azure OpenAI embedding provider
- add Azure OpenAI chat provider
- configure provider selection through environment variables
- keep local provider support for development

### Phase 6: Portfolio-Ready Version

- improve README with screenshots or diagrams
- add a minimal interface
- add evaluation report
- document design decisions and trade-offs

## Definition of Done

The project should be considered complete for the first portfolio-ready version when it can:

- run locally
- ingest Markdown documents
- split content into chunks
- generate or simulate embeddings through a provider abstraction
- store chunks in PostgreSQL with pgvector
- retrieve relevant chunks for a question
- generate an answer using retrieved context
- include sources in the response
- refuse questions without enough context
- log questions, retrieved chunks, and answers
- explain the architecture clearly in this README

## Current Status

Project skeleton only. No application code has been created yet.
