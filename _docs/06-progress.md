# Genogram — Progress Tracker (v1.0)

> Registo do estado actual do projecto.
> Actualizar a cada tarefa concluída.
> 
> **Formato:** `✅` Done | `🔶` In Progress | `⬜` Pending | `⛔` Blocked
> 
> **Regra:** Não marcar `✅` sem testes a passar. Não marcar `🔶` em mais de 2 tarefas simultaneamente.

---

## Estado Global

| Métrica | Valor | Actualizado |
|---|---|---|
| Fase actual | Fase 0 — Fundação | 2026-11-04 |
| Tarefas completas | 0 / 280 | 2026-11-04 |
| Tarefas em curso | 0 | 2026-11-04 |
| Tarefas bloqueadas | 0 | 2026-11-04 |
| % global | 0% | 2026-11-04 |
| Último commit | — | — |
| CI status | — | — |
| Build status | — | — |

---

## Fase 0 — Fundação

### 0.1. Schema & Especificação

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 0.1.1 | JSON Schema `Genogram` (raiz) | ⬜ | — | |
| 0.1.2 | JSON Schema `Person` + enums | ⬜ | — | |
| 0.1.3 | JSON Schema `Relationship` + 71 tipos | ⬜ | — | |
| 0.1.4 | JSON Schema `Household` | ⬜ | — | |
| 0.1.5 | JSON Schema `Collaborator` + `PermissionSet` | ⬜ | — | |
| 0.1.6 | JSON Schema `MedicalRecord` + enums | ⬜ | — | |
| 0.1.7 | JSON Schema `SubstanceRecord` | ⬜ | — | |
| 0.1.8 | JSON Schema `GeneticTest` + `GeneticVariant` | ⬜ | — | |
| 0.1.9 | JSON Schema `CriminalRecord` | ⬜ | — | |
| 0.1.10 | JSON Schema `LifeEvent` | ⬜ | — | |
| 0.1.11 | JSON Schema `EcoMap` + nodes + connections | ⬜ | — | |
| 0.1.12 | JSON Schema `AuditEntry` | ⬜ | — | |
| 0.1.13 | JSON Schema `HospitalConfig` + `RoleDefinition` | ⬜ | — | |
| 0.1.14 | JSON: 71+ relationship types (notation) | ⬜ | — | |
| 0.1.15 | JSON: 40+ person shapes (notation) | ⬜ | — | |
| 0.1.16 | JSON: 22 medical categories (notation) | ⬜ | — | |
| 0.1.17 | JSON: 11 cultural patterns | ⬜ | — | |
| 0.1.18 | JSON: 13 religion icons | ⬜ | — | |
| 0.1.19 | JSON: 5 socioeconomic levels | ⬜ | — | |
| 0.1.20 | Validar schemas com `ajv` | ⬜ | — | |

### 0.2. ADRs

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 0.2.1 | ADR-001 (Yrs) | ⬜ | — | |
| 0.2.2 | ADR-002 (Tauri 2) | ⬜ | — | |
| 0.2.3 | ADR-003 (Svelte 5) | ⬜ | — | |
| 0.2.4 | ADR-004 (Konva.js) | ⬜ | — | |
| 0.2.5 | ADR-005 (XChaCha20) | ⬜ | — | |
| 0.2.6 | ADR-006 (WebRTC) | ⬜ | — | |
| 0.2.7 | ADR-007 (SQLite) | ⬜ | — | |
| 0.2.8 | ADR-008 (Docker NAS) | ⬜ | — | |
| 0.2.9 | ADR-009 (WASM PWA) | ⬜ | — | |
| 0.2.10 | ADR-010 (RBAC filtering) | ⬜ | — | |
| 0.2.11 | ADR-011 (AI on-device) | ⬜ | — | |
| 0.2.12 | ADR-012 (Zustand) | ⬜ | — | |
| 0.2.13 | ADR-013 (svelte-i18n) | ⬜ | — | |
| 0.2.14 | ADR-014 (GitHub Actions) | ⬜ | — | |
| 0.2.15 | ADR-015 (MIT) | ⬜ | — | |

### 0.3. Setup do Projecto

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 0.3.1 | Repo Git + `.gitignore` | ⬜ | — | |
| 0.3.2 | Cargo workspace | ⬜ | — | |
| 0.3.3 | `engine/Cargo.toml` (deps) | ⬜ | — | |
| 0.3.4 | `engine/src/lib.rs` | ⬜ | — | |
| 0.3.5 | `engine/src/error.rs` | ⬜ | — | |
| 0.3.6 | `frontend/` (Vite + Svelte 5) | ⬜ | — | |
| 0.3.7 | `tauri/` (config Tauri 2) | ⬜ | — | |
| 0.3.8 | `.rustfmt.toml` + `.clippy.toml` | ⬜ | — | |
| 0.3.9 | CI básico (GitHub Actions) | ⬜ | — | |
| 0.3.10 | `nas/Dockerfile` (base) | ⬜ | — | |
| 0.3.11 | `nas/docker-compose.yml` (template) | ⬜ | — | |
| 0.3.12 | `schemas/` + `schemars` em CI | ⬜ | — | |

### ✅ Aceitação Fase 0

| Critério | Estado |
|---|:---:|
| `cargo test` passa | ⬜ |
| `npm test` passa | ⬜ |
| `cargo clippy` sem warnings | ⬜ |
| Schemas validam (`ajv` + `schemars`) | ⬜ |
| `tauri dev` abre janela vazia | ⬜ |
| CI verde | ⬜ |

---

## Fase 1 — Motor de Dados

### 1.1. Models

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.1.1 | Enums base (Gender, Education, Socio, Religion) | ⬜ | — | |
| 1.1.2 | `CulturalPattern` | ⬜ | — | |
| 1.1.3 | `MedicalRecord` + enums | ⬜ | — | |
| 1.1.4 | `SubstanceRecord` + enums | ⬜ | — | |
| 1.1.5 | `GeneticTest` + `GeneticVariant` + `ConsentScope` | ⬜ | — | |
| 1.1.6 | `CriminalRecord` | ⬜ | — | |
| 1.1.7 | `LifeEvent` + `LifeEventType` | ⬜ | — | |
| 1.1.8 | `CustomField` | ⬜ | — | |
| 1.1.9 | `Person` struct (completo) | ⬜ | — | |
| 1.1.10 | `RelationshipType` enum (71+) | ⬜ | — | |
| 1.1.11 | `Relationship` struct + visual | ⬜ | — | |
| 1.1.12 | `Household` struct | ⬜ | — | |
| 1.1.13 | `Collaborator` + `PermissionSet` | ⬜ | — | |
| 1.1.14 | `EcoMap` + nodes + connections | ⬜ | — | |
| 1.1.15 | `AuditEntry` + enums | ⬜ | — | |
| 1.1.16 | `GenogramMetadata` + `Settings` | ⬜ | — | |
| 1.1.17 | `Genogram` struct (raiz) | ⬜ | — | |
| 1.1.18 | `mod.rs` (re-exports) | ⬜ | — | |
| 1.1.19 | Tests: `Person` | ⬜ | — | |
| 1.1.20 | Tests: `Relationship` | ⬜ | — | |
| 1.1.21 | Tests: `Genogram` (round-trip) | ⬜ | — | |
| 1.1.22 | Tests: `MedicalRecord` | ⬜ | — | |

### 1.2. Storage

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.2.1 | Migration v1 (SQL) | ⬜ | — | |
| 1.2.2 | `SqliteStorage` | ⬜ | — | |
| 1.2.3 | `Repository` trait | ⬜ | — | |
| 1.2.4 | `GenogramRepository` | ⬜ | — | |
| 1.2.5 | `PersonRepository` | ⬜ | — | |
| 1.2.6 | `RelationshipRepository` | ⬜ | — | |
| 1.2.7 | `AuditRepository` | ⬜ | — | |
| 1.2.8 | `mod.rs` | ⬜ | — | |
| 1.2.9 | Test: round-trip (save → load) | ⬜ | — | |
| 1.2.10 | Test: 1000 pessoas < 2s | ⬜ | — | |

### 1.3. CRDT

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.3.1 | `CrdtDoc` (Yrs wrapper) | ⬜ | — | |
| 1.3.2 | `PersonMap` | ⬜ | — | |
| 1.3.3 | `RelationshipMap` | ⬜ | — | |
| 1.3.4 | `SyncEngine` (deltas) | ⬜ | — | |
| 1.3.5 | `PendingDeltaQueue` | ⬜ | — | |
| 1.3.6 | `mod.rs` | ⬜ | — | |
| 1.3.7 | Test: 2 docs offline → merge | ⬜ | — | |
| 1.3.8 | Test: 10 edições concorrentes | ⬜ | — | |
| 1.3.9 | Property test: convergence (1000 casos) | ⬜ | — | |

### 1.4. Encryption

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.4.1 | `KeyManager` | ⬜ | — | |
| 1.4.2 | `E2E` (XChaCha20-Poly1305) | ⬜ | — | |
| 1.4.3 | `Handshake` (X25519) | ⬜ | — | |
| 1.4.4 | `PassphraseGuard` (Argon2id) | ⬜ | — | |
| 1.4.5 | `mod.rs` | ⬜ | — | |
| 1.4.6 | Test: encrypt → decrypt | ⬜ | — | |
| 1.4.7 | Test: decrypt sem chave → error | ⬜ | — | |
| 1.4.8 | Test: handshake A↔B | ⬜ | — | |

### 1.5. RBAC

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.5.1 | `filter_genogram()` | ⬜ | — | |
| 1.5.2 | `filter_delta()` | ⬜ | — | |
| 1.5.3 | `ConsentChecker` | ⬜ | — | |
| 1.5.4 | `RoleRegistry` | ⬜ | — | |
| 1.5.5 | `mod.rs` | ⬜ | — | |
| 1.5.6 | Test: cardiologista não recebe mental | ⬜ | — | |
| 1.5.7 | Test: `Refused` → invisível | ⬜ | — | |
| 1.5.8 | Test: owner edita → delta filtrado | ⬜ | — | |

### 1.6. Validation

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.6.1 | `ValidationRule` trait | ⬜ | — | |
| 1.6.2 | Regras V01-V05 | ⬜ | — | |
| 1.6.3 | Regras V06-V10 | ⬜ | — | |
| 1.6.4 | Regras V11-V15 | ⬜ | — | |
| 1.6.5 | Regras V16-V23 | ⬜ | — | |
| 1.6.6 | `ValidationReport` | ⬜ | — | |
| 1.6.7 | Tests: 23 regras | ⬜ | — | |

### 1.7. Bridge (Tauri Commands)

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 1.7.1 | `create_genogram` | ⬜ | — | |
| 1.7.2 | `load_genogram` | ⬜ | — | |
| 1.7.3 | `add_person` | ⬜ | — | |
| 1.7.4 | `update_person` | ⬜ | — | |
| 1.7.5 | `add_relationship` | ⬜ | — | |
| 1.7.6 | `delete_person` | ⬜ | — | |
| 1.7.7 | `get_validation` | ⬜ | — | |
| 1.7.8 | `sync_delta` | ⬜ | — | |
| 1.7.9 | Test: round-trip (command → SQLite) | ⬜ | — | |

### ✅ Aceitação Fase 1

| Critério | Estado |
|---|:---:|
| `cargo test` passa (todos os módulos) | ⬜ |
| CRDT: 2 devices offline → convergem | ⬜ |
| E2E: intercept → ilegível | ⬜ |
| RBAC: cardiologista nunca recebe mental | ⬜ |
| Validation: 23 regras testadas | ⬜ |
| Storage: 1000 pessoas < 2s | ⬜ |
| `cargo clippy` sem warnings | ⬜ |

---

## Fase 2 — Canvas Interativo

### 2.1. Canvas Base

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 2.1.1 | `GenogramCanvas.svelte` (Konva Stage) | ⬜ | — | |
| 2.1.2 | `PersonNode.svelte` | ⬜ | — | |
| 2.1.3 | `RelationshipLine.svelte` | ⬜ | — | |
| 2.1.4 | `useGenogramStore.ts` | ⬜ | — | |
| 2.1.5 | `useCrdtStore.ts` | ⬜ | — | |
| 2.1.6 | `engine.ts` (bridge) | ⬜ | — | |
| 2.1.7 | Test: store (CRUD) | ⬜ | — | |
| 2.1.8 | Test: canvas renderiza | ⬜ | — | |

### 2.2. Interacção

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 2.2.1 | Drag & drop (snap grid) | ⬜ | — | |
| 2.2.2 | `QuickAdd.svelte` (teclas 1-5) | ⬜ | — | |
| 2.2.3 | `ContextMenu.svelte` | ⬜ | — | |
| 2.2.4 | `PersonPanel.svelte` | ⬜ | — | |
| 2.2.5 | `RelationshipPanel.svelte` | ⬜ | — | |
| 2.2.6 | `MedicalPanel.svelte` | ⬜ | — | |
| 2.2.7 | Undo/Redo | ⬜ | — | |
| 2.2.8 | Apagar pessoa (cascade) | ⬜ | — | |
| 2.2.9 | Apagar relação | ⬜ | — | |
| 2.2.10 | Test: quick-add | ⬜ | — | |
| 2.2.11 | Test: undo/redo | ⬜ | — | |

### 2.3. Auto-Layout

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 2.3.1 | `hierarchical.rs` | ⬜ | — | |
| 2.3.2 | `tunneling.rs` | ⬜ | — | |
| 2.3.3 | `force.rs` (fallback) | ⬜ | — | |
| 2.3.4 | Tauri command: `auto_layout` | ⬜ | — | |
| 2.3.5 | UI: botão "Auto-arrange" | ⬜ | — | |
| 2.3.6 | Test: 3 gerações | ⬜ | — | |
| 2.3.7 | Test: cruzamentos → hops | ⬜ | — | |
| 2.3.8 | Bench: 200 nós < 500ms | ⬜ | — | |

### 2.4. View Layers

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 2.4.1 | `useLayerStore.ts` | ⬜ | — | |
| 2.4.2 | Quadrantes médicos (canvas) | ⬜ | — | |
| 2.4.3 | Cultural pattern (canvas) | ⬜ | — | |
| 2.4.4 | Religion icon (canvas) | ⬜ | — | |
| 2.4.5 | Emocional (linhas) | ⬜ | — | |
| 2.4.6 | `LayerToggle.svelte` | ⬜ | — | |
| 2.4.7 | Test: toggle on/off | ⬜ | — | |

### 2.5. Toolbar & Panels

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 2.5.1 | `Toolbar.svelte` | ⬜ | — | |
| 2.5.2 | `ExportMenu.svelte` | ⬜ | — | |
| 2.5.3 | `SettingsPanel.svelte` | ⬜ | — | |
| 2.5.4 | `CollaboratorPanel.svelte` | ⬜ | — | |

### ✅ Aceitação Fase 2

| Critério | Estado |
|---|:---:|
| Genograma 3 gerações, 10 pessoas, 15 relações | ⬜ |
| Drag & drop | ⬜ |
| Quick-add (teclas 1-5) | ⬜ |
| Auto-layout | ⬜ |
| Line tunneling | ⬜ |
| View layers | ⬜ |
| Undo/Redo | ⬜ |
| Pan & zoom (25%-400%) | ⬜ |
| `vitest` passa | ⬜ |

---

## Fase 3 — Import/Export

### 3.1. GEDCOM

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 3.1.1 | Parser: tokeniser | ⬜ | — | |
| 3.1.2 | Parser: INDIVIDUAL → Person | ⬜ | — | |
| 3.1.3 | Parser: FAMILY → Relationship | ⬜ | — | |
| 3.1.4 | Parser: HSP/NOTE → Medical | ⬜ | — | |
| 3.1.5 | Writer: Genogram → GEDCOM | ⬜ | — | |
| 3.1.6 | Command: `import_gedcom` | ⬜ | — | |
| 3.1.7 | Command: `export_gedcom` | ⬜ | — | |
| 3.1.8 | Test: round-trip lossless | ⬜ | — | |
| 3.1.9 | Fuzz: parser | ⬜ | — | |

### 3.2. CSV

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 3.2.1 | CSV parser | ⬜ | — | |
| 3.2.2 | Command: `import_csv` | ⬜ | — | |

### 3.3. Export Visual

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 3.3.1 | SVG export | ⬜ | — | |
| 3.3.2 | PNG export (resvg) | ⬜ | — | |
| 3.3.3 | PDF export (printpdf) | ⬜ | — | |
| 3.3.4 | Legend generator | ⬜ | — | |
| 3.3.5 | Commands: `export_svg/png/pdf` | ⬜ | — | |
| 3.3.6 | UI: ExportMenu → save dialog | ⬜ | — | |
| 3.3.7 | Test: SVG | ⬜ | — | |
| 3.3.8 | Test: PDF | ⬜ | — | |

### 3.4. FHIR

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 3.4.1 | FHIR exporter (Bundle) | ⬜ | — | |
| 3.4.2 | FHIR + RBAC | ⬜ | — | |
| 3.4.3 | Command: `export_fhir` | ⬜ | — | |
| 3.4.4 | Test: FHIR válido | ⬜ | — | |

### ✅ Aceitação Fase 3

| Critério | Estado |
|---|:---:|
| GEDCOM round-trip lossless | ⬜ |
| CSV import | ⬜ |
| PNG/PDF/SVG + legenda | ⬜ |
| FHIR válido (R4) | ⬜ |
| FHIR: não exporta emocional/criminal | ⬜ |

---

## Fase 4 — PWA + Multi-Device

### 4.1. WASM

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 4.1.1 | Compilar engine → wasm32 | ⬜ | — | |
| 4.1.2 | `wasm-bindgen` exports | ⬜ | — | |
| 4.1.3 | `wasm-pack build` (CI) | ⬜ | — | |
| 4.1.4 | `engine.ts` (PWA mode) | ⬜ | — | |
| 4.1.5 | Test: WASM = Rust (paridade) | ⬜ | — | |

### 4.2. PWA

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 4.2.1 | `manifest.json` | ⬜ | — | |
| 4.2.2 | `sw.js` (Service Worker) | ⬜ | — | |
| 4.2.3 | Register SW | ⬜ | — | |
| 4.2.4 | IndexedDB wrapper | ⬜ | — | |
| 4.2.5 | Offline: genograma sobrevive | ⬜ | — | |
| 4.2.6 | "Adicionar ao ecrã" | ⬜ | — | |
| 4.2.7 | Test: offline (Playwright) | ⬜ | — | |

### 4.3. Sync (WebRTC)

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 4.3.1 | `webrtc/peer.rs` | ⬜ | — | |
| 4.3.2 | `webrtc/data_channel.rs` | ⬜ | — | |
| 4.3.3 | `webrtc/relay.rs` (client) | ⬜ | — | |
| 4.3.4 | `relay/` (server) | ⬜ | — | |
| 4.3.5 | `sync.ts` (frontend) | ⬜ | — | |
| 4.3.6 | E2E no PWA (WebCrypto) | ⬜ | — | |
| 4.3.7 | Handshake UI (QR/code) | ⬜ | — | |
| 4.3.8 | Test: 2 browsers < 2s | ⬜ | — | |
| 4.3.9 | Test: relay < 5s | ⬜ | — | |
| 4.3.10 | Test: relay = ciphertext | ⬜ | — | |

### ✅ Aceitação Fase 4

| Critério | Estado |
|---|:---:|
| PWA: instalar → offline | ⬜ |
| WASM = Rust (paridade) | ⬜ |
| Sync: < 2s (LAN), < 5s (internet) | ⬜ |
| E2E: relay não vê dados | ⬜ |
| Offline: app funciona sem rede | ⬜ |

---

## Fase 5 — NAS + Colaboração

### 5.1. NAS

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 5.1.1 | `main.rs` (NAS mode) | ⬜ | — | |
| 5.1.2 | API routes (Axum) | ⬜ | — | |
| 5.1.3 | Auth (token, LAN) | ⬜ | — | |
| 5.1.4 | WebSocket (deltas) | ⬜ | — | |
| 5.1.5 | `Dockerfile` (multi-stage) | ⬜ | — | |
| 5.1.6 | `docker-compose.yml` | ⬜ | — | |
| 5.1.7 | `entrypoint.sh` | ⬜ | — | |
| 5.1.8 | `default.toml` | ⬜ | — | |
| 5.1.9 | Test: docker run → curl | ⬜ | — | |
| 5.1.10 | Test: iPhone → NAS sync | ⬜ | — | |

### 5.2. Colaboração

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 5.2.1 | `invite_genogram()` | ⬜ | — | |
| 5.2.2 | `revoke_collaborator()` | ⬜ | — | |
| 5.2.3 | `generate_share_key()` | ⬜ | — | |
| 5.2.4 | `accept_invite()` | ⬜ | — | |
| 5.2.5 | RBAC filter na sync | ⬜ | — | |
| 5.2.6 | `CollaboratorPanel.svelte` | ⬜ | — | |
| 5.2.7 | `AcceptInvite.svelte` | ⬜ | — | |
| 5.2.8 | Test: convidar → ver filtrado | ⬜ | — | |
| 5.2.9 | Test: revogar → dados apagados | ⬜ | — | |
| 5.2.10 | Audit log (todas as acções) | ⬜ | — | |

### ✅ Aceitação Fase 5

| Critério | Estado |
|---|:---:|
| `docker compose up` → API responde | ⬜ |
| iPhone ↔ NAS sync | ⬜ |
| 3 colaboradores (roles diferentes) | ⬜ |
| RBAC: dados não chegam | ⬜ |
| Revogação: dados apagados | ⬜ |
| Audit log completo | ⬜ |

---

## Fase 6 — Polish + Release

### 6.1. Templates

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 6.1.1 | Template: 2 gerações | ⬜ | — | |
| 6.1.2 | Template: 3 gerações | ⬜ | — | |
| 6.1.3 | Template: blended family | ⬜ | — | |
| 6.1.4 | Template: couples | ⬜ | — | |
| 6.1.5 | UI: "Novo genograma" | ⬜ | — | |

### 6.2. i18n

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 6.2.1 | `pt.json` | ⬜ | — | |
| 6.2.2 | `en.json` | ⬜ | — | |
| 6.2.3 | `es.json` | ⬜ | — | |
| 6.2.4 | `fr.json` | ⬜ | — | |
| 6.2.5 | `de.json` | ⬜ | — | |
| 6.2.6 | `it.json` | ⬜ | — | |
| 6.2.7 | Restantes 12 idiomas | ⬜ | — | |
| 6.2.8 | `svelte-i18n` setup | ⬜ | — | |
| 6.2.9 | Test: mudar idioma | ⬜ | — | |

### 6.3. Problem Spotter UI

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 6.3.1 | `ValidationPanel.svelte` | ⬜ | — | |
| 6.3.2 | Toolbar: "Validar" | ⬜ | — | |
| 6.3.3 | Badge no canvas | ⬜ | — | |

### 6.4. Onboarding

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 6.4.1 | `Onboarding.svelte` (3 passos) | ⬜ | — | |
| 6.4.2 | Trigger: primeiro arranque | ⬜ | — | |

### 6.5. CI/CD

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 6.5.1 | `ci.yml` (testes + lint) | ⬜ | — | |
| 6.5.2 | `build-desktop.yml` | ⬜ | — | |
| 6.5.3 | `build-wasm.yml` | ⬜ | — | |
| 6.5.4 | `build-nas.yml` | ⬜ | — | |
| 6.5.5 | `release.yml` | ⬜ | — | |
| 6.5.6 | Security audit em CI | ⬜ | — | |

### 6.6. Performance & QA

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 6.6.1 | Bench: 200 pessoas < 2s | ⬜ | — | |
| 6.6.2 | Bench: GEDCOM 1000 < 5s | ⬜ | — | |
| 6.6.3 | Bench: layout < 500ms | ⬜ | — | |
| 6.6.4 | E2E: fluxo completo | ⬜ | — | |
| 6.6.5 | Memory: 200 pessoas < 150 MB | ⬜ | — | |

### ✅ Aceitação Fase 6 (v1.0)

| Critério | Estado |
|---|:---:|
| Performance targets cumpridos | ⬜ |
| 18 idiomas | ⬜ |
| 4 templates | ⬜ |
| Problem Spotter na UI | ⬜ |
| Onboarding | ⬜ |
| CI/CD: push → build → release | ⬜ |
| 0 vulnerabilidades críticas | ⬜ |
| E2E: fluxo completo sem bugs | ⬜ |

---

## Fase 7 — Mobile

### 7.1. Tauri Mobile

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 7.1.1 | iOS: config + build | ⬜ | — | |
| 7.1.2 | Android: config + build | ⬜ | — | |
| 7.1.3 | iOS: Keychain | ⬜ | — | |
| 7.1.4 | Android: Keystore | ⬜ | — | |
| 7.1.5 | iOS: notificações | ⬜ | — | |
| 7.1.6 | Android: notificações | ⬜ | — | |
| 7.1.7 | Canvas: touch (pinch, swipe) | ⬜ | — | |
| 7.1.8 | Quick-add: botões (mobile) | ⬜ | — | |
| 7.1.9 | Panels: bottom sheet (mobile) | ⬜ | — | |
| 7.1.10 | Test: iPhone offline | ⬜ | — | |
| 7.1.11 | Test: Android offline | ⬜ | — | |

### 7.2. Stores

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 7.2.1 | Privacy manifest (iOS) | ⬜ | — | |
| 7.2.2 | App Store: assets | ⬜ | — | |
| 7.2.3 | App Store: submissão | ⬜ | — | |
| 7.2.4 | Play Store: assets | ⬜ | — | |
| 7.2.5 | Play Store: submissão | ⬜ | — | |
| 7.2.6 | Play App Signing | ⬜ | — | |

### ✅ Aceitação Fase 7 (v1.1)

| Critério | Estado |
|---|:---:|
| iOS: App Store, offline | ⬜ |
| Android: Play Store, offline | ⬜ |
| Touch: pinch, swipe, tap | ⬜ |
| Keychain/Keystore | ⬜ |
| Sync: mobile ↔ desktop < 5s | ⬜ |

---

## Fase 8 — AI

### 8.1. AI On-Device (Desktop)

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 8.1.1 | `llama-cpp-rs` integration | ⬜ | — | |
| 8.1.2 | `ai/generate.rs` (text-to-genogram) | ⬜ | — | |
| 8.1.3 | `ai/edit.rs` (chat editing) | ⬜ | — | |
| 8.1.4 | `ai/insights.rs` (pattern analysis) | ⬜ | — | |
| 8.1.5 | `ai/image.rs` (image-to-genogram, OCR) | ⬜ | — | |
| 8.1.6 | Model download (7B-13B, GGUF) | ⬜ | — | |
| 8.1.7 | UI: `AIChat.svelte` | ⬜ | — | |
| 8.1.8 | UI: `AIInsights.svelte` | ⬜ | — | |
| 8.1.9 | Test: text-to-genogram → schema válido | ⬜ | — | |
| 8.1.10 | Test: insights → padrões correctos | ⬜ | — | |

### 8.2. AI Mobile (Core ML / TFLite)

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 8.2.1 | Core ML model (iOS) | ⬜ | — | |
| 8.2.2 | TFLite model (Android) | ⬜ | — | |
| 8.2.3 | Test: mobile AI → schema válido | ⬜ | — | |

### ✅ Aceitação Fase 8 (v2.0)

| Critério | Estado |
|---|:---:|
| Text-to-genogram (desktop) | ⬜ |
| Chat editing | ⬜ |
| AI Insights (padrões) | ⬜ |
| Image-to-genogram | ⬜ |
| Mobile AI (básico) | ⬜ |
| Zero-knowledge: dados não saem | ⬜ |

---

## Fase 9 — Eco-Maps + FHIR

### 9.1. Eco-Maps

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 9.1.1 | `EcoMapCanvas.svelte` | ⬜ | — | |
| 9.1.2 | `EcoMapNode.svelte` | ⬜ | — | |
| 9.1.3 | `EcoMapConnection.svelte` | ⬜ | — | |
| 9.1.4 | UI: criar/editar eco-map | ⬜ | — | |
| 9.1.5 | Test: eco-map CRUD | ⬜ | — | |

### 9.2. FHIR (Completo)

| ID | Tarefa | Estado | Data | Notas |
|---|---|:---:|---|---|
| 9.2.1 | FHIR: Patient + RelatedPerson | ⬜ | — | |
| 9.2.2 | FHIR: FamilyMemberHistory | ⬜ | — | |
| 9.2.3 | FHIR: Condition (ICD-10 + SNOMED) | ⬜ | — | |
| 9.2.4 | FHIR: Observation (genético) | ⬜ | — | |
| 9.2.5 | FHIR: Bundle assembly | ⬜ | — | |
| 9.2.6 | FHIR: consent check antes de export | ⬜ | — | |
| 9.2.7 | Test: FHIR Bundle válido (validator) | ⬜ | — | |
| 9.2.8 | Test: FHIR + RBAC (só o permitido) | ⬜ | — | |

### ✅ Aceitação Fase 9 (v2.1)

| Critério | Estado |
|---|:---:|
| Eco-map: criar, editar, exportar | ⬜ |
| FHIR: Bundle válido (R4) | ⬜ |
| FHIR: RBAC aplicado | ⬜ |
| FHIR: consent check | ⬜ |
| Interop: SClínico (FHIR API) | ⬜ |

---

## Log de Decisões em Runtime

> Registo de decisões tomadas durante o desenvolvimento que **não** estão nos ADRs.
> Formato: `Data | Decisão | Razão | Impacto`

| Data | Decisão | Razão | Impacto |
|---|---|---|---|
| — | — | — | — |

---

## Bloqueios Atuais

| ID | Bloqueio | Desde | Causa | Mitigação |
|---|---|---|---|---|
| — | — | — | — | — |

---

## Notas de Sessão

> Notas livres por sessão de trabalho. Útil para a IA saber o contexto.

| Data | Sessão | Resumo |
|---|---|---|
| 2026-11-04 | Planeamento | Criação dos 8 documentos de especificação. Fase 0 iniciada. |   
