# Genogram — Task Board (v1.0)

> Lista de tarefas hierárquica para desenvolvimento.
> Cada tarefa: 1 ficheiro + 1 teste. Máx ~30min de trabalho.
> 
> **Estados:** ⬜ Todo | 🔶 In Progress | ✅ Done | ⛔ Blocked
> 
> **Regra:** Não começar uma tarefa com estado ⬜ se as dependências não estão ✅.

---

## Fase 0 — Fundação (2-3 semanas)

### 0.1. Schema & Especificação

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 0.1.1 | Escrever JSON Schema para `Genogram` (raiz) | `schemas/genogram.schema.json` | ⬜ | — |
| 0.1.2 | Escrever JSON Schema para `Person` + enums | `schemas/person.schema.json` | ⬜ | 0.1.1 |
| 0.1.3 | Escrever JSON Schema para `Relationship` + 71 tipos | `schemas/relationship.schema.json` | ⬜ | 0.1.1 |
| 0.1.4 | Escrever JSON Schema para `Household` | `schemas/household.schema.json` | ⬜ | 0.1.1 |
| 0.1.5 | Escrever JSON Schema para `Collaborator` + `PermissionSet` | `schemas/collaborator.schema.json` | ⬜ | 0.1.1 |
| 0.1.6 | Escrever JSON Schema para `MedicalRecord` + enums | `schemas/medical.schema.json` | ⬜ | 0.1.2 |
| 0.1.7 | Escrever JSON Schema para `SubstanceRecord` | `schemas/substance.schema.json` | ⬜ | 0.1.2 |
| 0.1.8 | Escrever JSON Schema para `GeneticTest` + `GeneticVariant` | `schemas/genetic.schema.json` | ⬜ | 0.1.2 |
| 0.1.9 | Escrever JSON Schema para `CriminalRecord` | `schemas/criminal.schema.json` | ⬜ | 0.1.2 |
| 0.1.10 | Escrever JSON Schema para `LifeEvent` | `schemas/life_event.schema.json` | ⬜ | 0.1.2 |
| 0.1.11 | Escrever JSON Schema para `EcoMap` + nodes + connections | `schemas/ecomap.schema.json` | ⬜ | 0.1.1 |
| 0.1.12 | Escrever JSON Schema para `AuditEntry` | `schemas/audit.schema.json` | ⬜ | 0.1.1 |
| 0.1.13 | Escrever JSON Schema para `HospitalConfig` + `RoleDefinition` | `schemas/hospital_config.schema.json` | ⬜ | 0.1.5 |
| 0.1.14 | Escrever JSON para 71+ relationship types (notation) | `schemas/notation/relationships.json` | ⬜ | 0.1.3 |
| 0.1.15 | Escrever JSON para 40+ person shapes (notation) | `schemas/notation/person_shapes.json` | ⬜ | 0.1.2 |
| 0.1.16 | Escrever JSON para 22 medical categories (notation) | `schemas/notation/medical.json` | ⬜ | 0.1.6 |
| 0.1.17 | Escrever JSON para 11 cultural patterns | `schemas/notation/cultural.json` | ⬜ | 0.1.2 |
| 0.1.18 | Escrever JSON para 13 religion icons | `schemas/notation/religion.json` | ⬜ | 0.1.2 |
| 0.1.19 | Escrever JSON para 5 socioeconomic levels | `schemas/notation/socioeconomic.json` | ⬜ | 0.1.2 |
| 0.1.20 | Validar todos os schemas com `ajv` (JS) | `tests/schema_validation.test.js` | ⬜ | 0.1.1-0.1.19 |

### 0.2. ADRs & Documentação

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 0.2.1 | Escrever ADR-001 (Yrs) | `_docs/adr/ADR-001.md` | ⬜ | — |
| 0.2.2 | Escrever ADR-002 (Tauri 2) | `_docs/adr/ADR-002.md` | ⬜ | — |
| 0.2.3 | Escrever ADR-003 (Svelte 5) | `_docs/adr/ADR-003.md` | ⬜ | — |
| 0.2.4 | Escrever ADR-004 (Konva.js) | `_docs/adr/ADR-004.md` | ⬜ | — |
| 0.2.5 | Escrever ADR-005 (XChaCha20) | `_docs/adr/ADR-005.md` | ⬜ | — |
| 0.2.6 | Escrever ADR-006 (WebRTC) | `_docs/adr/ADR-006.md` | ⬜ | — |
| 0.2.7 | Escrever ADR-007 (SQLite) | `_docs/adr/ADR-007.md` | ⬜ | — |
| 0.2.8 | Escrever ADR-008 (Docker NAS) | `_docs/adr/ADR-008.md` | ⬜ | — |
| 0.2.9 | Escrever ADR-009 (WASM PWA) | `_docs/adr/ADR-009.md` | ⬜ | — |
| 0.2.10 | Escrever ADR-010 (RBAC filtering) | `_docs/adr/ADR-010.md` | ⬜ | — |
| 0.2.11 | Escrever ADR-011 (AI on-device) | `_docs/adr/ADR-011.md` | ⬜ | — |
| 0.2.12 | Escrever ADR-012 (Zustand) | `_docs/adr/ADR-012.md` | ⬜ | — |
| 0.2.13 | Escrever ADR-013 (svelte-i18n) | `_docs/adr/ADR-013.md` | ⬜ | — |
| 0.2.14 | Escrever ADR-014 (GitHub Actions) | `_docs/adr/ADR-014.md` | ⬜ | — |
| 0.2.15 | Escrever ADR-015 (MIT) | `_docs/adr/ADR-015.md` | ⬜ | — |

### 0.3. Setup do Projecto

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 0.3.1 | Criar repo Git + `.gitignore` | repo root | ⬜ | — |
| 0.3.2 | Criar Cargo workspace (`engine/`, `relay/`) | `Cargo.toml` | ⬜ | 0.3.1 |
| 0.3.3 | Criar `engine/Cargo.toml` com todas as deps | `engine/Cargo.toml` | ⬜ | 0.3.2 |
| 0.3.4 | Criar `engine/src/lib.rs` (entry point) | `engine/src/lib.rs` | ⬜ | 0.3.3 |
| 0.3.5 | Criar `engine/src/error.rs` (AppError enum) | `engine/src/error.rs` | ⬜ | 0.3.4 |
| 0.3.6 | Criar `frontend/` com Vite + Svelte 5 | `frontend/` | ⬜ | 0.3.1 |
| 0.3.7 | Criar `tauri/` com config Tauri 2 | `tauri/` | ⬜ | 0.3.6 |
| 0.3.8 | Configurar `.rustfmt.toml` + `.clippy.toml` | repo root | ⬜ | 0.3.2 |
| 0.3.9 | Configurar CI básico (GitHub Actions: cargo test + vitest) | `.github/workflows/ci.yml` | ⬜ | 0.3.3, 0.3.6 |
| 0.3.10 | Criar `nas/Dockerfile` (base, sem código ainda) | `nas/Dockerfile` | ⬜ | 0.3.2 |
| 0.3.11 | Criar `nas/docker-compose.yml` (template) | `nas/docker-compose.yml` | ⬜ | 0.3.10 |
| 0.3.12 | Criar `schemas/` + validar com `schemars` em CI | `schemas/`, CI | ⬜ | 0.1.20 |

**Aceitação Fase 0:**
- [ ] `cargo test` passa (engine)
- [ ] `npm test` passa (frontend)
- [ ] `cargo clippy` sem warnings
- [ ] Todos os schemas validam com `ajv` e `schemars`
- [ ] `tauri dev` abre janela vazia
- [ ] CI verde no GitHub Actions

---

## Fase 1 — Motor de Dados (3-4 semanas)

### 1.1. Models (Rust)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.1.1 | `Gender` enum + `EducationLevel` + `SocioeconomicLevel` + `Religion` | `engine/src/models/enums.rs` | ⬜ | 0.3.5 |
| 1.1.2 | `CulturalPattern` struct + `CulturalPatternType` enum | `engine/src/models/cultural.rs` | ⬜ | 1.1.1 |
| 1.1.3 | `MedicalRecord` struct + `MedicalCategory` + `MedicalStatus` + `Quadrant` | `engine/src/models/medical.rs` | ⬜ | 1.1.1 |
| 1.1.4 | `SubstanceRecord` struct + enums | `engine/src/models/substance.rs` | ⬜ | 1.1.1 |
| 1.1.5 | `GeneticTest` struct + `GeneticVariant` + `ConsentScope` | `engine/src/models/genetic.rs` | ⬜ | 1.1.1 |
| 1.1.6 | `CriminalRecord` struct + `CriminalStatus` | `engine/src/models/criminal.rs` | ⬜ | 1.1.1 |
| 1.1.7 | `LifeEvent` struct + `LifeEventType` | `engine/src/models/life_event.rs` | ⬜ | 1.1.1 |
| 1.1.8 | `CustomField` struct + `CustomFieldType` | `engine/src/models/custom_field.rs` | ⬜ | 1.1.1 |
| 1.1.9 | `Person` struct (completo, com todos os campos) | `engine/src/models/person.rs` | ⬜ | 1.1.2-1.1.8 |
| 1.1.10 | `RelationshipType` enum (71+ variantes) + `RelationshipCategory` | `engine/src/models/relationship_types.rs` | ⬜ | 1.1.1 |
| 1.1.11 | `Relationship` struct + `RelationshipVisual` + `LineStyle` + `LineMarker` | `engine/src/models/relationship.rs` | ⬜ | 1.1.10 |
| 1.1.12 | `Household` struct | `engine/src/models/household.rs` | ⬜ | 1.1.1 |
| 1.1.13 | `CollaboratorRole` + `PermissionSet` + `Collaborator` struct | `engine/src/models/collaborator.rs` | ⬜ | 1.1.1 |
| 1.1.14 | `EcoMap` + `EcoMapNode` + `EcoMapConnection` + enums | `engine/src/models/ecomap.rs` | ⬜ | 1.1.1 |
| 1.1.15 | `AuditEntry` + `AuditAction` + `AuditTarget` | `engine/src/models/audit.rs` | ⬜ | 1.1.1 |
| 1.1.16 | `GenogramMetadata` + `GenogramSettings` + `ColorScheme` | `engine/src/models/metadata.rs` | ⬜ | 1.1.1 |
| 1.1.17 | `Genogram` struct (raiz, com todos os campos) | `engine/src/models/genogram.rs` | ⬜ | 1.1.9, 1.1.11, 1.1.12, 1.1.13, 1.1.16 |
| 1.1.18 | `mod.rs` (re-exports) | `engine/src/models/mod.rs` | ⬜ | 1.1.17 |
| 1.1.19 | Unit tests: `Person` (valid, invalid gender, edge cases) | `engine/src/models/person.rs` (#[cfg(test)]) | ⬜ | 1.1.9 |
| 1.1.20 | Unit tests: `Relationship` (self-loop, valid, invalid) | `engine/src/models/relationship.rs` (#[cfg(test)]) | ⬜ | 1.1.11 |
| 1.1.21 | Unit tests: `Genogram` (CRUD in-memory, serialization round-trip) | `engine/src/models/genogram.rs` (#[cfg(test)]) | ⬜ | 1.1.17 |
| 1.1.22 | Unit tests: `MedicalRecord` (quadrant, status, dates) | `engine/src/models/medical.rs` (#[cfg(test)]) | ⬜ | 1.1.3 |

### 1.2. Storage (SQLite)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.2.1 | Migration v1: criar tabelas (person, relationship, medical, etc.) | `engine/src/storage/migrations/v1.sql` | ⬜ | 1.1.18 |
| 1.2.2 | `SqliteStorage` struct (connect, migrate) | `engine/src/storage/sqlite.rs` | ⬜ | 1.2.1 |
| 1.2.3 | `Repository` trait (CRUD genérico) | `engine/src/storage/repository.rs` | ⬜ | 1.2.2 |
| 1.2.4 | `GenogramRepository` (save, load, delete, list) | `engine/src/storage/genogram_repo.rs` | ⬜ | 1.2.3 |
| 1.2.5 | `PersonRepository` (save, load, list_by_genogram) | `engine/src/storage/person_repo.rs` | ⬜ | 1.2.3 |
| 1.2.6 | `RelationshipRepository` (save, load, list_by_genogram) | `engine/src/storage/rel_repo.rs` | ⬜ | 1.2.3 |
| 1.2.7 | `AuditRepository` (append, list) | `engine/src/storage/audit_repo.rs` | ⬜ | 1.2.3 |
| 1.2.8 | `mod.rs` (re-exports) | `engine/src/storage/mod.rs` | ⬜ | 1.2.7 |
| 1.2.9 | Integration test: criar genograma → persistir → reload → igual | `engine/tests/test_storage.rs` | ⬜ | 1.2.8 |
| 1.2.10 | Integration test: 1000 pessoas → persistir → reload < 2s | `engine/tests/test_storage_perf.rs` | ⬜ | 1.2.8 |

### 1.3. CRDT (Yrs)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.3.1 | `CrdtDoc` wrapper (criar Yrs Doc, mapear Genogram → Yrs types) | `engine/src/crdt/doc.rs` | ⬜ | 1.1.18 |
| 1.3.2 | `PersonMap` (Yrs Map para cada Person) | `engine/src/crdt/person_map.rs` | ⬜ | 1.3.1 |
| 1.3.3 | `RelationshipMap` (Yrs Map para cada Relationship) | `engine/src/crdt/rel_map.rs` | ⬜ | 1.3.1 |
| 1.3.4 | `SyncEngine` (gerar deltas, aplicar deltas) | `engine/src/crdt/sync.rs` | ⬜ | 1.3.2, 1.3.3 |
| 1.3.5 | `PendingDeltaQueue` (fila de deltas offline em SQLite) | `engine/src/crdt/pending.rs` | ⬜ | 1.3.4, 1.2.2 |
| 1.3.6 | `mod.rs` | `engine/src/crdt/mod.rs` | ⬜ | 1.3.5 |
| 1.3.7 | Integration test: 2 Docs editam offline → merge → mesmo estado | `engine/tests/test_crdt_merge.rs` | ⬜ | 1.3.6 |
| 1.3.8 | Integration test: 10 edições concorrentes → convergem | `engine/tests/test_crdt_concurrent.rs` | ⬜ | 1.3.6 |
| 1.3.9 | Property test: CRDT convergence (proptest, 1000 casos) | `engine/tests/test_crdt_property.rs` | ⬜ | 1.3.6 |

### 1.4. Encryption (E2E)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.4.1 | `KeyManager` (gerar Master Key, derivar Device/Genogram/Share keys) | `engine/src/crypto/keys.rs` | ⬜ | 0.3.5 |
| 1.4.2 | `E2E` (encrypt/decrypt com XChaCha20-Poly1305) | `engine/src/crypto/e2e.rs` | ⬜ | 1.4.1 |
| 1.4.3 | `Handshake` (X25519 key exchange, fingerprint) | `engine/src/crypto/handshake.rs` | ⬜ | 1.4.1 |
| 1.4.4 | `PassphraseGuard` (Argon2id, lock/unlock Master Key) | `engine/src/crypto/passphrase.rs` | ⬜ | 1.4.1 |
| 1.4.5 | `mod.rs` | `engine/src/crypto/mod.rs` | ⬜ | 1.4.4 |
| 1.4.6 | Unit test: encrypt → decrypt = original | `engine/src/crypto/e2e.rs` (#[cfg(test)]) | ⬜ | 1.4.2 |
| 1.4.7 | Unit test: decrypt sem chave correcta → error | `engine/src/crypto/e2e.rs` (#[cfg(test)]) | ⬜ | 1.4.2 |
| 1.4.8 | Integration test: handshake A↔B → E2E funciona | `engine/tests/test_handshake.rs` | ⬜ | 1.4.3 |

### 1.5. RBAC Filtering

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.5.1 | `filter_genogram(genogram, permissions) -> Genogram` | `engine/src/rbac/filter.rs` | ⬜ | 1.1.18 |
| 1.5.2 | `filter_delta(delta, permissions) -> Delta` (filtra antes de sync) | `engine/src/rbac/filter_delta.rs` | ⬜ | 1.5.1, 1.3.4 |
| 1.5.3 | `ConsentChecker` (valida consent_status + consent_scope) | `engine/src/rbac/consent.rs` | ⬜ | 1.5.1 |
| 1.5.4 | `RoleRegistry` (carregar roles do HospitalConfig) | `engine/src/rbac/roles.rs` | ⬜ | 1.5.1 |
| 1.5.5 | `mod.rs` | `engine/src/rbac/mod.rs` | ⬜ | 1.5.4 |
| 1.5.6 | Unit test: cardiologista não recebe mental | `engine/src/rbac/filter.rs` (#[cfg(test)]) | ⬜ | 1.5.1 |
| 1.5.7 | Unit test: `consent_status: Refused` → invisível | `engine/src/rbac/consent.rs` (#[cfg(test)]) | ⬜ | 1.5.3 |
| 1.5.8 | Integration test: owner edita → delta filtrado → collaborator recebe só o permitido | `engine/tests/test_rbac_sync.rs` | ⬜ | 1.5.2, 1.3.6 |

### 1.6. Validation (Problem Spotter)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.6.1 | `ValidationRule` trait + `ValidationResult` | `engine/src/validation/mod.rs` | ⬜ | 1.1.18 |
| 1.6.2 | Regras V01-V05 (generation, dates, married same gen) | `engine/src/validation/rules_01_05.rs` | ⬜ | 1.6.1 |
| 1.6.3 | Regras V06-V10 (parent-child, medical dates, pregnancy, self-loop) | `engine/src/validation/rules_06_10.rs` | ⬜ | 1.6.1 |
| 1.6.4 | Regras V11-V15 (duplicates, orphan, genetic consent, criminal year, household) | `engine/src/validation/rules_11_15.rs` | ⬜ | 1.6.1 |
| 1.6.5 | Regras V16-V23 (RBAC info, age consistency, until<since, twins, pet, empty, name, cancer) | `engine/src/validation/rules_16_23.rs` | ⬜ | 1.6.1 |
| 1.6.6 | `ValidationReport` (agregar resultados, severity) | `engine/src/validation/report.rs` | ⬜ | 1.6.2-1.6.5 |
| 1.6.7 | Unit tests: cada regra (1 teste por regra, 23 testes) | `engine/src/validation/rules_*.rs` (#[cfg(test)]) | ⬜ | 1.6.6 |

### 1.7. Bridge JS ↔ Rust (Tauri Commands)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 1.7.1 | `tauri/src/commands.rs` — `create_genogram(name) -> GenogramId` | `tauri/src/commands.rs` | ⬜ | 1.1.18, 1.2.8 |
| 1.7.2 | `tauri/src/commands.rs` — `load_genogram(id) -> Genogram` | `tauri/src/commands.rs` | ⬜ | 1.7.1 |
| 1.7.3 | `tauri/src/commands.rs` — `add_person(genogram_id, person) -> PersonId` | `tauri/src/commands.rs` | ⬜ | 1.7.1 |
| 1.7.4 | `tauri/src/commands.rs` — `update_person(genogram_id, person_id, changes)` | `tauri/src/commands.rs` | ⬜ | 1.7.3 |
| 1.7.5 | `tauri/src/commands.rs` — `add_relationship(genogram_id, rel) -> RelId` | `tauri/src/commands.rs` | ⬜ | 1.7.1 |
| 1.7.6 | `tauri/src/commands.rs` — `delete_person(genogram_id, person_id)` | `tauri/src/commands.rs` | ⬜ | 1.7.3 |
| 1.7.7 | `tauri/src/commands.rs` — `get_validation(genogram_id) -> ValidationReport` | `tauri/src/commands.rs` | ⬜ | 1.6.6 |
| 1.7.8 | `tauri/src/commands.rs` — `sync_delta(genogram_id, delta) -> SyncResult` | `tauri/src/commands.rs` | ⬜ | 1.3.4 |
| 1.7.9 | Integration test: Tauri command → engine → SQLite (round-trip) | `tauri/tests/test_commands.rs` | ⬜ | 1.7.8 |

**Aceitação Fase 1:**
- [ ] `cargo test` passa (todos os módulos)
- [ ] CRDT: 2 devices offline → sync → convergem
- [ ] E2E: encrypt → intercept → ilegível
- [ ] RBAC: cardiologista nunca recebe mental
- [ ] Validation: 23 regras testadas
- [ ] Storage: 1000 pessoas < 2s
- [ ] `cargo clippy` sem warnings

---

## Fase 2 — Canvas Interativo (4-5 semanas)

### 2.1. Canvas Base

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 2.1.1 | `GenogramCanvas.svelte` (Konva Stage, pan/zoom) | `frontend/src/lib/components/canvas/GenogramCanvas.svelte` | ⬜ | 0.3.6 |
| 2.1.2 | `PersonNode.svelte` (quadrado/círculo, drag, select) | `frontend/src/lib/components/canvas/PersonNode.svelte` | ⬜ | 2.1.1 |
| 2.1.3 | `RelationshipLine.svelte` (linha entre 2 nós, estilos) | `frontend/src/lib/components/canvas/RelationshipLine.svelte` | ⬜ | 2.1.1 |
| 2.1.4 | `useGenogramStore.ts` (Zustand: persons, relationships, selected) | `frontend/src/lib/stores/genogram.ts` | ⬜ | 0.3.6 |
| 2.1.5 | `useCrdtStore.ts` (Yjs doc ref, subscribe changes) | `frontend/src/lib/stores/crdt.ts` | ⬜ | 2.1.4 |
| 2.1.6 | `engine.ts` (bridge: Tauri invoke / WASM call) | `frontend/src/lib/services/engine.ts` | ⬜ | 1.7.9 |
| 2.1.7 | Unit test: store (add person, add relationship, select) | `frontend/tests/stores.test.ts` | ⬜ | 2.1.4 |
| 2.1.8 | Integration test: canvas renderiza 1 quadrado + 1 círculo + 1 linha | `frontend/tests/canvas.test.ts` | ⬜ | 2.1.3 |

### 2.2. Interacção

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 2.2.1 | Drag & drop de nós (com snap a grid) | `PersonNode.svelte` (drag logic) | ⬜ | 2.1.2 |
| 2.2.2 | `QuickAdd.svelte` (teclas 1-5: filho, pai, mãe, parceiro, irmão) | `frontend/src/lib/components/toolbar/QuickAdd.svelte` | ⬜ | 2.1.2, 2.1.4 |
| 2.2.3 | Context menu (clicar direito em nó: editar, apagar, adicionar relação) | `frontend/src/lib/components/common/ContextMenu.svelte` | ⬜ | 2.1.2 |
| 2.2.4 | `PersonPanel.svelte` (form lateral: nome, género, datas, ocupação) | `frontend/src/lib/components/panels/PersonPanel.svelte` | ⬜ | 2.2.3 |
| 2.2.5 | `RelationshipPanel.svelte` (form: tipo, datas, notas, intensidade) | `frontend/src/lib/components/panels/RelationshipPanel.svelte` | ⬜ | 2.2.3 |
| 2.2.6 | `MedicalPanel.svelte` (form: categoria, condição, status, datas, ICD-10) | `frontend/src/lib/components/panels/MedicalPanel.svelte` | ⬜ | 2.2.4 |
| 2.2.7 | Undo/Redo (Ctrl+Z / Ctrl+Y, stack de 50) | `frontend/src/lib/stores/undo.ts` | ⬜ | 2.1.4 |
| 2.2.8 | Apagar pessoa (com confirmação + cascade de relações) | `PersonPanel.svelte` (delete logic) | ⬜ | 2.2.4 |
| 2.2.9 | Apagar relação (clicar na linha + Delete) | `RelationshipLine.svelte` (delete logic) | ⬜ | 2.1.3 |
| 2.2.10 | Integration test: quick-add filho → aparece no canvas | `frontend/tests/quick_add.test.ts` | ⬜ | 2.2.2 |
| 2.2.11 | Integration test: undo/redo (add → undo → redo) | `frontend/tests/undo_redo.test.ts` | ⬜ | 2.2.7 |

### 2.3. Auto-Layout & Line Tunneling

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 2.3.1 | `hierarchical.rs` — layout por geração (x por position, y por generation) | `engine/src/layout/hierarchical.rs` | ⬜ | 1.1.18 |
| 2.3.2 | `tunneling.rs` — detectar cruzamentos + gerar hops (arcos 8px) | `engine/src/layout/tunneling.rs` | ⬜ | 2.3.1 |
| 2.3.3 | `force.rs` — force-directed fallback (para layouts complexos) | `engine/src/layout/force.rs` | ⬜ | 2.3.1 |
| 2.3.4 | Tauri command: `auto_layout(genogram_id) -> Vec<(person_id, x, y)>` | `tauri/src/commands.rs` | ⬜ | 2.3.1 |
| 2.3.5 | UI: botão "Auto-arrange" → chama layout → anima transição | `Toolbar.svelte` + `GenogramCanvas.svelte` | ⬜ | 2.3.4 |
| 2.3.6 | Unit test: 3 gerações → y correcto, x por position | `engine/src/layout/hierarchical.rs` (#[cfg(test)]) | ⬜ | 2.3.1 |
| 2.3.7 | Unit test: 2 linhas cruzam → hop gerado | `engine/src/layout/tunneling.rs` (#[cfg(test)]) | ⬜ | 2.3.2 |
| 2.3.8 | Bench: 200 pessoas → layout < 500ms | `engine/benches/layout_bench.rs` | ⬜ | 2.3.1 |

### 2.4. View Layers

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 2.4.1 | `useLayerStore.ts` (layers: medical, cultural, social, religion, emotional — on/off) | `frontend/src/lib/stores/layers.ts` | ⬜ | 2.1.4 |
| 2.4.2 | `PersonNode.svelte` — renderizar quadrantes médicos (se layer on) | `PersonNode.svelte` | ⬜ | 2.4.1 |
| 2.4.3 | `PersonNode.svelte` — renderizar cultural pattern (se layer on) | `PersonNode.svelte` | ⬜ | 2.4.1 |
| 2.4.4 | `PersonNode.svelte` — renderizar religion icon (se layer on) | `PersonNode.svelte` | ⬜ | 2.4.1 |
| 2.4.5 | `RelationshipLine.svelte` — renderizar emocional (se layer on) | `RelationshipLine.svelte` | ⬜ | 2.4.1 |
| 2.4.6 | `LayerToggle.svelte` (toolbar: toggles on/off) | `frontend/src/lib/components/toolbar/LayerToggle.svelte` | ⬜ | 2.4.1 |
| 2.4.7 | Integration test: toggle medical off → quadrantes desaparecem | `frontend/tests/layers.test.ts` | ⬜ | 2.4.6 |

### 2.5. Toolbar & Export UI

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 2.5.1 | `Toolbar.svelte` (novo, abrir, guardar, auto-arrange, export, validação) | `frontend/src/lib/components/toolbar/Toolbar.svelte` | ⬜ | 2.1.1 |
| 2.5.2 | `ExportMenu.svelte` (PNG, PDF, SVG, GEDCOM, CSV) | `frontend/src/lib/components/toolbar/ExportMenu.svelte` | ⬜ | 2.5.1 |
| 2.5.3 | `SettingsPanel.svelte` (idioma, color scheme, grid, max generations) | `frontend/src/lib/components/panels/SettingsPanel.svelte` | ⬜ | 2.5.1 |
| 2.5.4 | `CollaboratorPanel.svelte` (lista, convidar, revogar, permissões) | `frontend/src/lib/components/panels/CollaboratorPanel.svelte` | ⬜ | 2.5.1 |

**Aceitação Fase 2:**
- [ ] Canvas: criar genograma com 3 gerações, 10 pessoas, 15 relações
- [ ] Drag & drop funciona
- [ ] Quick-add (teclas 1-5) funciona
- [ ] Auto-layout organiza por geração
- [ ] Line tunneling: cruzamentos fazem hop
- [ ] View layers: toggle on/off
- [ ] Undo/Redo funciona
- [ ] Pan & zoom (25%-400%)
- [ ] `vitest` passa

---

## Fase 3 — Import/Export (2-3 semanas)

### 3.1. GEDCOM

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 3.1.1 | GEDCOM parser: tokeniser (linhas, níveis, tags) | `engine/src/import_export/gedcom/parser.rs` | ⬜ | 1.1.18 |
| 3.1.2 | GEDCOM parser: INDIVIDUAL records → `Person` | `engine/src/import_export/gedcom/parser.rs` | ⬜ | 3.1.1 |
| 3.1.3 | GEDCOM parser: FAMILY records → `Relationship` | `engine/src/import_export/gedcom/parser.rs` | ⬜ | 3.1.2 |
| 3.1.4 | GEDCOM parser: HSP/NOTE → `MedicalRecord` / `notes` | `engine/src/import_export/gedcom/parser.rs` | ⬜ | 3.1.2 |
| 3.1.5 | GEDCOM writer: `Genogram` → GEDCOM 5.5.1 string | `engine/src/import_export/gedcom/writer.rs` | ⬜ | 1.1.18 |
| 3.1.6 | Tauri command: `import_gedcom(path) -> Genogram` | `tauri/src/commands.rs` | ⬜ | 3.1.4 |
| 3.1.7 | Tauri command: `export_gedcom(genogram_id, path)` | `tauri/src/commands.rs` | ⬜ | 3.1.5 |
| 3.1.8 | Integration test: GEDCOM → Genogram → GEDCOM = idêntico (round-trip) | `engine/tests/test_gedcom_roundtrip.rs` | ⬜ | 3.1.5, 3.1.4 |
| 3.1.9 | Fuzz test: GEDCOM parser (inputs aleatórios não crasham) | `engine/fuzz/gedcom_parser/` | ⬜ | 3.1.1 |

### 3.2. CSV

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 3.2.1 | CSV parser: header → `Person` (nome, género, nascimento, ocupação) | `engine/src/import_export/csv.rs` | ⬜ | 1.1.18 |
| 3.2.2 | Tauri command: `import_csv(path) -> Genogram` | `tauri/src/commands.rs` | ⬜ | 3.2.1 |

### 3.3. Export Visual

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 3.3.1 | SVG export: Genogram → SVG string (nós + linhas + legenda) | `engine/src/import_export/svg.rs` | ⬜ | 1.1.18 |
| 3.3.2 | PNG export: SVG → PNG (via `resvg`) | `engine/src/import_export/png.rs` | ⬜ | 3.3.1 |
| 3.3.3 | PDF export: Genogram → PDF (via `printpdf`, com legenda) | `engine/src/import_export/pdf.rs` | ⬜ | 1.1.18 |
| 3.3.4 | `Legend` generator (símbolos usados → tabela) | `engine/src/import_export/legend.rs` | ⬜ | 3.3.3 |
| 3.3.5 | Tauri commands: `export_svg`, `export_png`, `export_pdf` | `tauri/src/commands.rs` | ⬜ | 3.3.1-3.3.3 |
| 3.3.6 | UI: `ExportMenu` → chama Tauri → save dialog | `ExportMenu.svelte` | ⬜ | 3.3.5 |
| 3.3.7 | Integration test: export SVG → parse → elementos correctos | `engine/tests/test_svg_export.rs` | ⬜ | 3.3.1 |
| 3.3.8 | Integration test: export PDF → tem página + legenda | `engine/tests/test_pdf_export.rs` | ⬜ | 3.3.3 |

### 3.4. FHIR (Fase 9, mas stub agora)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 3.4.1 | FHIR exporter: `Genogram` → `Bundle` (Patient, RelatedPerson, FamilyMemberHistory, Condition) | `engine/src/import_export/fhir/exporter.rs` | ⬜ | 1.1.18 |
| 3.4.2 | FHIR: aplicar RBAC (só exportar o que o role permite) | `engine/src/import_export/fhir/exporter.rs` | ⬜ | 3.4.1, 1.5.1 |
| 3.4.3 | Tauri command: `export_fhir(genogram_id, role, path)` | `tauri/src/commands.rs` | ⬜ | 3.4.2 |
| 3.4.4 | Integration test: export FHIR → valida com FHIR validator | `engine/tests/test_fhir_export.rs` | ⬜ | 3.4.1 |

**Aceitação Fase 3:**
- [ ] GEDCOM round-trip lossless
- [ ] CSV import funciona
- [ ] PNG/PDF/SVG export com legenda
- [ ] FHIR export válido (R4)
- [ ] FHIR: não exporta emocional/criminal/ecomap

---

## Fase 4 — PWA + Multi-Device (3-4 semanas)

### 4.1. WASM

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 4.1.1 | Compilar `engine` para `wasm32-unknown-unknown` | `engine/Cargo.toml` (target) | ⬜ | 1.1.18 |
| 4.1.2 | `wasm-bindgen` exports (create_genogram, add_person, etc.) | `engine/src/wasm.rs` | ⬜ | 4.1.1 |
| 4.1.3 | `wasm-pack build` → `frontend/public/wasm/` | CI | ⬜ | 4.1.2 |
| 4.1.4 | `engine.ts` (PWA mode): chamar WASM em vez de Tauri | `frontend/src/lib/services/engine.ts` | ⬜ | 4.1.3 |
| 4.1.5 | Integration test: WASM engine = Rust engine (mesmos resultados) | `engine/tests/test_wasm_parity.rs` | ⬜ | 4.1.2 |

### 4.2. PWA

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 4.2.1 | `manifest.json` (nome, ícones, theme_color, display: standalone) | `frontend/public/manifest.json` | ⬜ | 0.3.6 |
| 4.2.2 | `sw.js` (Service Worker: cache-first para assets, network-first para API) | `frontend/public/sw.js` | ⬜ | 4.2.1 |
| 4.2.3 | Register Service Worker em `main.ts` | `frontend/src/main.ts` | ⬜ | 4.2.2 |
| 4.2.4 | IndexedDB wrapper (storage local PWA) | `frontend/src/lib/services/idb.ts` | ⬜ | 4.2.1 |
| 4.2.5 | Offline: genograma sobrevive a refresh (IndexedDB) | `frontend/src/lib/stores/genogram.ts` | ⬜ | 4.2.4 |
| 4.2.6 | "Adicionar ao ecrã principal" (iOS/Android) | `manifest.json` + meta tags | ⬜ | 4.2.1 |
| 4.2.7 | Integration test: offline → app funciona (Playwright, network off) | `frontend/tests/offline.test.ts` | ⬜ | 4.2.5 |

### 4.3. Sync Multi-Device (WebRTC)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 4.3.1 | `webrtc/peer.rs` — PeerConnection (offer/answer/ICE) | `engine/src/webrtc/peer.rs` | ⬜ | 1.3.4 |
| 4.3.2 | `webrtc/data_channel.rs` — enviar/receber deltas CRDT | `engine/src/webrtc/data_channel.rs` | ⬜ | 4.3.1 |
| 4.3.3 | `webrtc/relay.rs` — client relay (WebSocket fallback) | `engine/src/webrtc/relay.rs` | ⬜ | 4.3.1 |
| 4.3.4 | `relay/` — server (WebSocket, retransmite ciphertext, não descifra) | `relay/src/server.rs` | ⬜ | 4.3.3 |
| 4.3.5 | `sync.ts` (frontend): WebRTC P2P (browser nativo) | `frontend/src/lib/services/sync.ts` | ⬜ | 4.3.1 |
| 4.3.6 | E2E no PWA: WebCrypto (XChaCha20-Poly1305) | `frontend/src/lib/services/crypto.ts` | ⬜ | 4.3.5 |
| 4.3.7 | Handshake UI: "Conectar a outro dispositivo" (QR code / code) | `frontend/src/lib/components/panels/SyncPanel.svelte` | ⬜ | 4.3.5 |
| 4.3.8 | Integration test: 2 browsers → sync < 2s (mesma rede) | `frontend/tests/sync.test.ts` | ⬜ | 4.3.5 |
| 4.3.9 | Integration test: 2 devices em redes diferentes → relay → sync < 5s | `engine/tests/test_relay_sync.rs` | ⬜ | 4.3.4 |
| 4.3.10 | Security test: interceptar relay → ciphertext ilegível | `engine/tests/test_relay_security.rs` | ⬜ | 4.3.4 |

**Aceitação Fase 4:**
- [ ] PWA: instalar no browser → funciona offline
- [ ] WASM: motor no browser = motor Rust
- [ ] Sync: 2 dispositivos → < 2s (LAN), < 5s (internet)
- [ ] E2E: relay não vê dados legíveis
- [ ] Offline: fechar internet → app continua

---

## Fase 5 — NAS + Colaboração (3-4 semanas)

### 5.1. NAS (Docker)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 5.1.1 | `engine/src/main.rs` — NAS mode (WebRTC server + HTTP API) | `engine/src/main.rs` | ⬜ | 4.3.1 |
| 5.1.2 | `engine/src/api/routes.rs` — Axum (GET/POST genograms, sync) | `engine/src/api/routes.rs` | ⬜ | 5.1.1 |
| 5.1.3 | `engine/src/api/auth.rs` — token auth (LAN, simples) | `engine/src/api/auth.rs` | ⬜ | 5.1.2 |
| 5.1.4 | `engine/src/api/ws.rs` — WebSocket (real-time deltas) | `engine/src/api/ws.rs` | ⬜ | 5.1.2 |
| 5.1.5 | `nas/Dockerfile` (multi-stage: build Rust → slim runtime) | `nas/Dockerfile` | ⬜ | 5.1.1 |
| 5.1.6 | `nas/docker-compose.yml` (volumes, ports, env) | `nas/docker-compose.yml` | ⬜ | 5.1.5 |
| 5.1.7 | `nas/entrypoint.sh` (migrations, init) | `nas/entrypoint.sh` | ⬜ | 5.1.5 |
| 5.1.8 | `nas/config/default.toml` (portas, paths, log level) | `nas/config/default.toml` | ⬜ | 5.1.5 |
| 5.1.9 | Integration test: `docker run` → curl → genograma criado | `nas/tests/test_api.sh` | ⬜ | 5.1.5 |
| 5.1.10 | Test: iPhone → NAS (WebRTC) → sync funciona | manual / `engine/tests/` | ⬜ | 5.1.1 |

### 5.2. Colaboração Multi-Utilizador

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 5.2.1 | `invite_genogram(genogram_id, user_id, role, permissions)` | `engine/src/collab/invite.rs` | ⬜ | 1.5.1 |
| 5.2.2 | `revoke_collaborator(genogram_id, collaborator_id)` | `engine/src/collab/revoke.rs` | ⬜ | 5.2.1 |
| 5.2.3 | `generate_share_key(genogram_key, collaborator_id, nonce) -> ShareKey` | `engine/src/crypto/keys.rs` | ⬜ | 1.4.1 |
| 5.2.4 | `accept_invite(share_key) -> GenogramId` (no dispositivo do colaborador) | `engine/src/collab/accept.rs` | ⬜ | 5.2.3 |
| 5.2.5 | RBAC filter na sync (deltas filtrados antes de enviar) | `engine/src/crdt/sync.rs` | ⬜ | 1.5.2, 5.2.1 |
| 5.2.6 | `CollaboratorPanel.svelte` — UI (convidar, revogar, ver permissões) | `frontend/src/lib/components/panels/CollaboratorPanel.svelte` | ⬜ | 5.2.1 |
| 5.2.7 | `AcceptInvite.svelte` — UI (introduzir chave, handshake) | `frontend/src/lib/components/panels/AcceptInvite.svelte` | ⬜ | 5.2.4 |
| 5.2.8 | Integration test: owner convida → collaborator aceita → vê filtrado | `engine/tests/test_collab.rs` | ⬜ | 5.2.5 |
| 5.2.9 | Integration test: revogar → próximo sync → dados apagados | `engine/tests/test_revoke.rs` | ⬜ | 5.2.2 |
| 5.2.10 | Audit log: registar View, Edit, Export, Share, Revoke | `engine/src/collab/audit.rs` | ⬜ | 1.1.15 |

**Aceitação Fase 5:**
- [ ] `docker compose up` no NAS → API responde
- [ ] iPhone sincroniza com NAS
- [ ] Colaboração: owner + 2 colaboradores (roles diferentes)
- [ ] RBAC: dados filtrados nunca chegam ao dispositivo
- [ ] Revogação: dados apagados no próximo sync
- [ ] Audit log: todas as acções registadas

---

## Fase 6 — Polish + Release (2-3 semanas)

### 6.1. Templates

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 6.1.1 | Template: 2 gerações (casal + 2 filhos) | `frontend/src/lib/templates/2gen.ts` | ⬜ | 2.1.4 |
| 6.1.2 | Template: 3 gerações (avós + pais + filhos) | `frontend/src/lib/templates/3gen.ts` | ⬜ | 2.1.4 |
| 6.1.3 | Template: blended family (2 casamentos, filhos de cada) | `frontend/src/lib/templates/blended.ts` | ⬜ | 2.1.4 |
| 6.1.4 | Template: couples (só 2 pessoas + relações) | `frontend/src/lib/templates/couples.ts` | ⬜ | 2.1.4 |
| 6.1.5 | UI: "Novo genograma" → escolher template | `frontend/src/lib/components/panels/NewGenogram.svelte` | ⬜ | 6.1.1-6.1.4 |

### 6.2. i18n

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 6.2.1 | `pt.json` (Português — todos os labels) | `frontend/src/i18n/pt.json` | ⬜ | 0.3.6 |
| 6.2.2 | `en.json` (English) | `frontend/src/i18n/en.json` | ⬜ | 6.2.1 |
| 6.2.3 | `es.json` (Spanish) | `frontend/src/i18n/es.json` | ⬜ | 6.2.1 |
| 6.2.4 | `fr.json` (French) | `frontend/src/i18n/fr.json` | ⬜ | 6.2.1 |
| 6.2.5 | `de.json` (German) | `frontend/src/i18n/de.json` | ⬜ | 6.2.1 |
| 6.2.6 | `it.json` (Italian) | `frontend/src/i18n/it.json` | ⬜ | 6.2.1 |
| 6.2.7 | Restantes 12 idiomas (ja, zh, ko, ar, hi, ru, nl, pl, sv, tr, pt-BR, nl) | `frontend/src/i18n/*.json` | ⬜ | 6.2.1 |
| 6.2.8 | `svelte-i18n` setup + selector na UI | `frontend/src/main.ts` + `SettingsPanel` | ⬜ | 6.2.1 |
| 6.2.9 | Test: mudar idioma → todos os labels actualizam | `frontend/tests/i18n.test.ts` | ⬜ | 6.2.8 |

### 6.3. Problem Spotter UI

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 6.3.1 | `ValidationPanel.svelte` (lista de erros/warnings, click → navega para nó) | `frontend/src/lib/components/panels/ValidationPanel.svelte` | ⬜ | 1.6.6 |
| 6.3.2 | Toolbar: botão "Validar" → abre panel | `Toolbar.svelte` | ⬜ | 6.3.1 |
| 6.3.3 | Badge no canvas: número de erros (vermelho) / warnings (amarelo) | `GenogramCanvas.svelte` | ⬜ | 6.3.1 |

### 6.4. Onboarding

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 6.4.1 | `Onboarding.svelte` (3 passos: bem-vindo, criar genograma, quick-add) | `frontend/src/lib/components/Onboarding.svelte` | ⬜ | 2.2.2 |
| 6.4.2 | Trigger: primeiro arranque → onboarding | `App.svelte` | ⬜ | 6.4.1 |

### 6.5. CI/CD Completo

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 6.5.1 | `ci.yml` (testes + lint + schema validation) | `.github/workflows/ci.yml` | ⬜ | 0.3.9 |
| 6.5.2 | `build-desktop.yml` (Win x64, Mac ARM, Mac Intel, Linux x64) | `.github/workflows/build-desktop.yml` | ⬜ | 6.5.1 |
| 6.5.3 | `build-wasm.yml` (WASM + PWA) | `.github/workflows/build-wasm.yml` | ⬜ | 6.5.1 |
| 6.5.4 | `build-nas.yml` (Docker multi-arch: amd64 + arm64) | `.github/workflows/build-nas.yml` | ⬜ | 6.5.1 |
| 6.5.5 | `release.yml` (tag → GitHub Release + Docker Hub) | `.github/workflows/release.yml` | ⬜ | 6.5.2-6.5.4 |
| 6.5.6 | `cargo audit` + `npm audit` + `trivy` em CI | `.github/workflows/ci.yml` | ⬜ | 6.5.1 |

### 6.6. Performance & QA

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 6.6.1 | Bench: 200 pessoas → abrir < 2s | `engine/benches/` | ⬜ | 2.3.1 |
| 6.6.2 | Bench: 1000 pessoas → GEDCOM import < 5s | `engine/benches/` | ⬜ | 3.1.4 |
| 6.6.3 | Bench: layout 200 nós < 500ms | `engine/benches/layout_bench.rs` | ⬜ | 2.3.1 |
| 6.6.4 | E2E test: fluxo completo (criar → editar → export → import) | `frontend/tests/e2e_flow.test.ts` | ⬜ | 3.3.5 |
| 6.6.5 | Memory check: 200 pessoas < 150 MB (desktop) | manual / CI | ⬜ | 6.6.1 |

**Aceitação Fase 6 (v1.0):**
- [ ] Todos os targets do §8 (04-architecture.md) cumpridos
- [ ] 18 idiomas funcionais
- [ ] 4 templates
- [ ] Problem Spotter na UI
- [ ] Onboarding no primeiro arranque
- [ ] CI/CD: push → build → release
- [ ] 0 vulnerabilidades críticas (cargo audit, npm audit, trivy)
- [ ] E2E: fluxo completo sem bugs

---

## Fase 7 — Mobile (4-5 semanas)

### 7.1. Tauri Mobile

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 7.1.1 | Tauri iOS: config + build (`.ipa`) | `tauri/tauri.conf.json` (ios) | ⬜ | 2.5.1 |
| 7.1.2 | Tauri Android: config + build (`.aab`) | `tauri/tauri.conf.json` (android) | ⬜ | 2.5.1 |
| 7.1.3 | iOS: Keychain (guardar Master Key) | `tauri/src/mobile/ios_keychain.rs` | ⬜ | 7.1.1 |
| 7.1.4 | Android: Keystore (guardar Master Key) | `tauri/src/mobile/android_keystore.rs` | ⬜ | 7.1.2 |
| 7.1.5 | iOS: notificações (sync pendente) | Tauri plugin notification | ⬜ | 7.1.1 |
| 7.1.6 | Android: notificações (sync pendente) | Tauri plugin notification | ⬜ | 7.1.2 |
| 7.1.7 | Mobile: canvas touch (pinch zoom, swipe pan) | `GenogramCanvas.svelte` (touch events) | ⬜ | 2.1.1 |
| 7.1.8 | Mobile: quick-add (botões em vez de teclas) | `QuickAdd.svelte` (mobile variant) | ⬜ | 2.2.2 |
| 7.1.9 | Mobile: panels (bottom sheet em vez de lateral) | `PersonPanel.svelte` (mobile) | ⬜ | 2.2.4 |
| 7.1.10 | Test: iPhone 15 → app funciona offline | manual | ⬜ | 7.1.1 |
| 7.1.11 | Test: Android 13 → app funciona offline | manual | ⬜ | 7.1.2 |

### 7.2. App Store / Play Store

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 7.2.1 | Privacy manifest (iOS): "Data Not Collected" | `tauri/ios/PrivacyInfo.xcprivacy` | ⬜ | 7.1.1 |
| 7.2.2 | App Store: screenshots, description, privacy policy | assets | ⬜ | 7.2.1 |
| 7.2.3 | App Store: submissão (Xcode + App Store Connect) | — | ⬜ | 7.2.2 |
| 7.2.4 | Play Store: screenshots, description, privacy policy | assets | ⬜ | 7.1.2 |
| 7.2.5 | Play Store: submissão (Play Console) | — | ⬜ | 7.2.4 |
| 7.2.6 | Play Store: Play App Signing | — | ⬜ | 7.2.5 |

**Aceitação Fase 7 (v1.1):**
- [ ] iOS: app na App Store, funciona offline
- [ ] Android: app na Play Store, funciona offline
- [ ] Touch: pinch zoom, swipe pan, tap select
- [ ] Keychain/Keystore: chave guardada com segurança
- [ ] Sync: mobile ↔ desktop < 5s

---

## Fase 8 — AI (4-6 semanas)

### 8.1. AI On-Device (Desktop)

| ID | Tarefa | Ficheiro | Estado | Deps |
|---|---|---|:---:|---|
| 8.1.1 | `llama.cpp` integration (Rust: `llama-cpp-rs`) | `engine/Cargo.toml` | ⬜ | 1.1.18 |
| 8.1.2 | `ai/generate.rs` — text-to-genogram (prompt → JSON schema) | `engine/src/ai/generate.rs`   
