# Newcomer Codebase Guide

This guide is a practical map of the repository for engineers who are joining the project and need to quickly understand:
- where code lives,
- what each important file does,
- which functions are core,
- and how to safely maintain/extend the system.

---

## 1) High-level architecture

- **Frontend (`frontend/`)**: React + Vite + TypeScript UI for source upload, graph generation, graph inspection, and chat.
- **Backend (`backend/`)**: FastAPI service that ingests sources, builds graph data, runs post-processing, and serves Q&A APIs.
- **Database**: Neo4j stores source metadata, chunks, entities, relationships, vector indexes, and community summaries.
- **Docs (`docs/`)**: AsciiDoc + images for user/developer documentation.
- **Experiments (`experiments/`)**: notebooks and one-off analysis artifacts.

Typical flow:
1. User adds sources from UI.
2. Backend scans source metadata (`/url/scan`).
3. Backend extracts graph from selected sources (`/extract`).
4. UI polls status and renders file/graph state.
5. Chat endpoints query vector/graph/fulltext context and return answers + provenance.

---

## 2) Top-level files and directories

- `README.md`: product-level setup and environment configuration.
- `LLMGraphBuilder.md`: additional project notes and background.
- `docker-compose.yml`: local multi-service orchestration.
- `example.env`: baseline environment variable template.
- `data/`: model comparison/supporting datasets.
- `docs/`: deeper backend/frontend/project docs and screenshots.
- `POC_Documents/`: archived PoC documents.
- `experiments/`: notebooks and evaluation data used during prototyping.

---

## 3) Backend map (`backend/`)

### Runtime and packaging
- `backend/score.py`: **FastAPI application entrypoint**. Declares middleware, security headers, and all HTTP endpoints.
- `backend/Dockerfile`: backend container build.
- `backend/requirements.txt`: backend Python dependencies.
- `backend/example.env`: backend runtime env template.
- `backend/README.md`: backend-specific run instructions.

### Tests and scripts
- `backend/test_integrationqa.py`: integration-style Q&A validation.
- `backend/test_commutiesqa.py`: community-focused Q&A test flow.
- `backend/Performance_test.py`, `backend/locustperf.py`: performance/load testing helpers.
- `backend/dbtest.py`: connectivity/DB checks.
- `backend/ragas_eval.py`: quality metric generation from RAGAS perspective.

### Core source files (`backend/src`)

#### API orchestration and response helpers
- `src/main.py`: orchestrates source-node creation and extraction jobs by source type.
- `src/api_response.py`: normalizes API response payload format.
- `src/logger.py`: structured logging wrapper.

#### Graph ingestion and extraction
- `src/llm.py`: selects configured LLM and turns chunks into graph documents.
- `src/create_chunks.py`: document chunking utility class.
- `src/diffbot_transformer.py`: Diffbot-specific graph extraction path.
- `src/make_relationships.py`: creates/merges relationships and chunk-linking edges.

#### Source adapters
- `src/document_sources/local_file.py`: load local uploads into Documents.
- `src/document_sources/s3_bucket.py`: list/read S3 source files.
- `src/document_sources/gcs_bucket.py`: list/read/upload/merge/delete GCS files.
- `src/document_sources/web_pages.py`: scrape/process generic web pages.
- `src/document_sources/wikipedia.py`: ingest Wikipedia content.
- `src/document_sources/youtube.py`: transcript retrieval/chunking + timestamp utilities.

#### Querying and retrieval
- `src/graph_query.py`: graph read queries for visualizations and chunk text fetch.
- `src/QA_integration.py`: retrieval chain setup, session history, and chat answer pipeline.
- `src/neighbours.py`: neighboring-node retrieval for graph exploration.

#### Post-processing and quality
- `src/post_processing.py`: vector/fulltext index creation + embedding updates.
- `src/chunkid_entities.py`: map chunks to entities/communities and deduplicate.
- `src/communities.py`: GDS community creation, summaries, and community embeddings.
- `src/ragas_eval.py`: API-level RAGAS metrics.

#### Shared infra and entities
- `src/graphDB_dataAccess.py`: data access layer around Neo4j operations.
- `src/shared/common_fn.py`: shared helpers for DB connection, persistence, cleanup, and formatting.
- `src/shared/constants.py`: constants referenced across backend modules.
- `src/shared/schema_extraction.py`: schema-from-text model and extraction helper.
- `src/entities/source_node.py`: source node data model.
- `src/entities/user_credential.py`: credential model.

### Important backend functions to know
- In `score.py`:
  - `/url/scan`: create source nodes for chosen source type.
  - `/extract`: perform full extraction into graph/chunks/entities.
  - chat/graph utility endpoints for Q&A, duplicate/orphan checks, chunk/entity info.
- In `main.py`:
  - `create_source_node_graph_*` functions (source-specific source-node creation).
  - `extract_graph_from_file_*` functions (source-specific extraction pipelines).
- In `QA_integration.py`:
  - `get_rag_chain`, `retrieve_documents`, `format_documents`, `initialize_neo4j_vector`.
- In `communities.py`:
  - `create_community_graph_projection`, `write_communities`, `create_community_summaries`.
- In `post_processing.py`:
  - `create_vector_fulltext_indexes`, `create_entity_embedding`.

---

## 4) Frontend map (`frontend/`)

### Runtime and build files
- `frontend/package.json`: scripts/dependencies.
- `frontend/vite.config.ts`: Vite build/dev config.
- `frontend/tsconfig*.json`: TS compiler configs.
- `frontend/example.env`: client env template.
- `frontend/README.md`: frontend setup notes.

### App bootstrap and routing
- `src/main.tsx`: React app bootstrap.
- `src/router.tsx`: route definitions (including chat-only route).
- `src/App.tsx`: main composition shell.
- `src/types.ts`: shared TypeScript domain types.

### API layer
- `src/API/Index.ts`: aggregated API exports.
- `src/services/*.ts`: backend endpoint wrappers by feature.
  - `ConnectAPI.ts`, `HealthStatus.ts`: connectivity checks.
  - `URLScan.ts`, `GetFiles.ts`, `DeleteFiles.ts`: source lifecycle.
  - `QnaAPI.ts`, `GraphQuery.ts`, `getChunkText.ts`: chat and graph reads.
  - `PostProcessing.ts`, `MergeDuplicateEntities.ts`, `DeleteOrphanNodes.ts`: cleanup workflows.
  - `GetDuplicateNodes.ts`, `GetOrphanNodes.ts`, `ChunkEntitiesInfo.ts`: analysis/detail views.
  - `PollingAPI.ts`, `ServerSideStatusUpdateAPI.ts`, `CancelAPI.ts`, `retry.ts`: long-run execution control.
  - `SchemaFromTextAPI.ts`, `vectorIndexCreation.ts`, `GetRagasMetric.ts`: advanced settings and metrics.

### State/context
- `src/context/UserCredentials.tsx`: Neo4j/session credential state.
- `src/context/UsersFiles.tsx`: uploaded source/file state.
- `src/context/UserMessages.tsx`: chat message context.
- `src/context/Alert.tsx`: app-wide alert state.
- `src/context/ThemeWrapper.tsx`: theme mode + wrapper.

### Hooks
- `src/hooks/useSse.tsx`: server-sent events handling.
- `src/hooks/useSpeech.tsx`: speech interactions.
- `src/hooks/useSourceInput.tsx`: reusable source-input behavior.

### UI components (by feature)
- `src/components/Layout/*`: app shell (header, side nav, drawers).
- `src/components/DataSources/*`: local/S3/GCS source input and modals.
- `src/components/WebSources/*`: web/Wikipedia/YouTube source forms.
- `src/components/FileTable.tsx`: file state table + actions.
- `src/components/Graph/*`: graph result modal, legends, properties, selection.
- `src/components/ChatBot/*`: chat panel, mode switches, metadata tabs.
- `src/components/Popups/*`: settings/connection/retry/processing dialogs.
- `src/components/UI/*`: reusable low-level UI primitives.
- `src/components/Content.tsx`, `QuickStarter.tsx`, `Dropdown.tsx`: page-level composition helpers.

### Assets and styling
- `src/assets/images/*`: iconography and imagery.
- `src/assets/schemas.json`: predefined schema options.
- `src/assets/ChatbotMessages.json`: chat UX canned messages.
- `src/index.css`, `src/App.css`, `src/styling/info.css`: global and feature styles.

---

## 5) How to maintain this codebase

### A) When adding a new ingestion source
1. Add backend adapter in `backend/src/document_sources/`.
2. Add source-node creation/extraction orchestration in `backend/src/main.py`.
3. Expose API behavior in `backend/score.py` (if new endpoint/fields needed).
4. Add frontend source component under `frontend/src/components/DataSources` or `WebSources`.
5. Add/update client service wrapper in `frontend/src/services/`.
6. Update env docs (`README.md`, `example.env` files) and add test coverage.

### B) When adding a new chat/retrieval mode
1. Backend: update retrieval logic in `QA_integration.py` + query helpers.
2. Backend: ensure needed indexes/embeddings exist in `post_processing.py`.
3. Frontend: add mode toggle/UI in `components/ChatBot/*` and constants.
4. Validate latency + answer traceability with chunk/source metadata.

### C) Safe change checklist
- Keep API response shape stable (`api_response.py`) to avoid UI regressions.
- Update TypeScript types when backend payload fields change.
- Prefer adding a service wrapper rather than inlining `fetch` in components.
- Preserve source-type string contracts used across backend and frontend.
- For long jobs, keep polling/cancel paths consistent.

### D) Operational recommendations
- Run backend and frontend separately during development for quicker iteration.
- Verify Neo4j credentials + database selection before debugging extraction errors.
- Watch logs from `CustomLogger` for API timings and source-level failures.
- Run integration/perf scripts before large pipeline changes.

---

## 6) Suggested onboarding path (first 2 days)

1. Read `README.md` and run app locally.
2. Trace one full path: Local file upload -> `/url/scan` -> `/extract` -> graph view -> chat answer.
3. Review these files in order:
   - `backend/score.py`
   - `backend/src/main.py`
   - `backend/src/llm.py`
   - `backend/src/QA_integration.py`
   - `frontend/src/components/Content.tsx`
   - `frontend/src/components/FileTable.tsx`
   - `frontend/src/components/ChatBot/Chatbot.tsx`
   - `frontend/src/services/*.ts` used by above components
4. Make a small non-breaking change (e.g., add a UI tooltip or log field) and run tests.

---

## 7) Ownership conventions (recommended)

- Keep backend business logic in `src/` and endpoint wiring in `score.py`.
- Keep frontend side effects in `services/` or hooks, not deeply inside presentational components.
- Use context only for true cross-app state; keep feature-local state inside components.
- Any new environment variable should be documented in both root and service-level docs.

