# Genogram — Architecture & ADRs (v1.0)

> Este documento define a arquitetura do sistema, as decisões técnicas (ADRs)
> e a estrutura de módulos.
> Se a IA sugerir uma tecnologia que não está aqui, a resposta é "não"
> a menos que exista um ADR que a justifique.

---

## 1. Visão Geral da Arquitetura

┌─────────────────────────────────────────────────────────────────────────┐
│ CLIENTES (Nós) │
│ │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ │
│ │ Desktop │ │ Mobile │ │ PWA │ │ NAS │ │
│ │ (Tauri 2) │ │ (Tauri 2) │ │ (Browser) │ │ (Docker) │ │
│ │ Win/Mac/ │ │ iOS/ │ │ (WASM) │ │ (Headless) │ │
│ │ Linux │ │ Android │ │ │ │ │ │
│ └──────┬──────┘ └──────┬──────┘ └──────┬──────┘ └──────┬──────┘ │
│ │ │ │ │ │
│ └─────────────────┴─────────────────┴─────────────────┘ │
│ │ │
│ ┌──────────────┴──────────────┐ │
│ │ MOTOR (Rust) — 1 código │ │
│ │ │ │
│ │ • Data model + validation │ │
│ │ • CRDT (Yrs) │ │
│ │ • E2E Encryption │ │
│ │ • RBAC Filtering │ │
│ │ • GEDCOM / FHIR │ │
│ │ • Layout Engine │ │
│ │ • Validation (Problem │ │
│ │ Spotter) │ │
│ │ • SQLite (persistence) │ │
│ └──────────────┬──────────────┘ │
│ │ │
└───────────────────────────────────┼────────────────────────────────────┘
│
WebRTC P2P (E2E)
+ Relay fallback
│
┌───────────────┴───────────────┐
│ RELAY (Dumb Pipe) │
│ • Não descifra │
│ • Não guarda │
│ • Não vê conteúdo │
│ • Só retransmite ciphertext │
└───────────────────────────────┘


### Princípios Arquiteturais

| # | Princípio | Implicação |
|---|---|---|
| A1 | **Local-first** | Todos os dados vivem no dispositivo. A rede é opcional. |
| A2 | **Um motor, muitas cascas** | O motor Rust é o mesmo em todos os targets. Só a "cascas" muda. |
| A3 | **Zero-knowledge** | Nenhum servidor/relay vê dados legíveis. |
| A4 | **Offline-first** | Todas as features funcionam sem rede. Sync é um upgrade. |
| A5 | **Open standards** | GEDCOM, FHIR, SVG, PDF. Sem lock-in. |
| A6 | **Auditable** | Código open-source (MIT). Decisões documentadas (ADRs). |

---

## 2. Stack Tecnológica

### 2.1. Motor (Rust)

| Componente | Crate | Versão | License | Função |
|---|---|---|---|---|
| CRDT | `yrs` | 0.28.x | MIT | Sincronização sem conflitos |
| CRDT (WASM) | `yrs` → `wasm32` | 0.28.x | MIT | CRDT no browser |
| Encryption | `ring` | 0.17.x | Apache-2.0 / ISC | XChaCha20-Poly1305, HKDF, HMAC |
| Storage | `rusqlite` | 0.31.x | MIT | SQLite (persistence local) |
| UUID | `uuid` | 1.x | Apache-2.0 / MIT | UUID v7 |
| Serialization | `serde` + `serde_json` | 1.x | MIT | JSON (API, GEDCOM, FHIR) |
| Dates | `chrono` | 0.4.x | MIT | Datas ISO 8601 |
| GEDCOM | Custom parser | — | MIT | Import/export GEDCOM 5.5.1 |
| FHIR | `fhircore` (ou custom) | 0.x | Apache-2.0 | Export FHIR R4 |
| PDF | `printpdf` | 0.7.x | MIT | Geração de PDF |
| DOCX | `docx-rs` | 0.4.x | MIT | Geração de DOCX |
| SVG | Custom (string builder) | — | MIT | Export SVG |
| Validation | Custom | — | MIT | Problem Spotter (23+ regras) |
| Layout | Custom (hierarchical) | — | MIT | Auto-arrange por geração |
| Logging | `tracing` + `tracing-subscriber` | 0.1.x | MIT | Audit log + debug |
| Error handling | `thiserror` + `anyhow` | 1.x | MIT | Errors typed |
| Async (NAS) | `tokio` | 1.x | MIT | Event loop no NAS (WebRTC) |
| WebRTC (NAS) | `webrtc` (strawgate) | 0.12.x | MIT | P2P no NAS |
| TLS | `rustls` | 0.23.x | Apache-2.0 / ISC | TLS para relay |

### 2.2. Frontend (Svelte)

| Componente | Biblioteca | Versão | License | Função |
|---|---|---|---|---|
| Framework | `svelte` | 5.x | MIT | UI (runes, reactivity) |
| Canvas | `konva` (via `@konvajs/html`) | 10.x | MIT | Renderização de nós e linhas |
| State | `zustand` (via `svelte-zustand`) | 5.x | MIT | State management |
| CRDT (JS) | `yjs` | 13.6.x | MIT | Sincronização no frontend |
| Yjs ↔ Svelte | `yjs` bindings | — | MIT | Reatividade CRDT → UI |
| i18n | `svelte-i18n` | 4.x | MIT | 18 idiomas |
| Forms | `sveltekit-form` (ou custom) | — | MIT | Formulários de pessoa/relação |
| Toast/Notification | `svelte-sonner` | 0.x | MIT | Feedback ao utilizador |
| Icons | `lucide-svelte` | 0.x | ISC | Ícones UI |
| Testing | `vitest` + `@testing-library/svelte` | 2.x / 4.x | MIT | Unit + integration tests |

### 2.3. Desktop/Mobile (Tauri)

| Componente | Versão | License | Função |
|---|---|---|---|
| Tauri Core | 2.12.x | MIT / Apache-2.0 | Shell desktop + mobile |
| Tauri Plugin: FS | 2.x | MIT | Acesso a ficheiros locais |
| Tauri Plugin: Shell | 2.x | MIT | Abrir apps externos |
| Tauri Plugin: Notification | 2.x | MIT | Notificações nativas |
| Tauri Plugin: Deep Link | 2.x | MIT | URLs custom (genogram://) |
| Tauri Plugin: Updater | 2.x | MIT | Auto-update (opcional) |

### 2.4. PWA (Browser)

| Componente | Versão | License | Função |
|---|---|---|---|
| Service Worker | Native (browser) | — | Offline cache, push |
| WASM | `yrs` → `wasm32-unknown-unknown` | MIT | Motor Rust no browser |
| WebCrypto | Native (browser) | — | E2E encryption no browser |
| IndexedDB | Native (browser) | — | Storage local (PWA) |
| Manifest | `manifest.json` | — | Installable PWA |

### 2.5. NAS (Docker)

| Componente | Versão | License | Função |
|---|---|---|---|
| Docker | 24.x+ | Apache-2.0 | Container runtime |
| Rust binary (headless) | — | MIT | Motor + WebRTC + API |
| SQLite | 3.4x | Public Domain | Storage no volume |
| caddy (opcional) | 2.x | Apache-2.0 | TLS termination (LAN) |

### 2.6. CI/CD

| Componente | Versão | License | Função |
|---|---|---|---|
| GitHub Actions | — | — | Pipeline CI/CD |
| `cargo` + `rustup` | stable | — | Build Rust |
| `tauri build` | 2.x | MIT | Build desktop apps |
| `wasm-pack` | 0.13.x | MIT | Build WASM |
| Docker Buildx | — | Apache-2.0 | Build image NAS |
| `schemars` | 0.8.x | MIT | Validar JSON Schema em CI |

---

## 3. Estrutura de Módulos (Repo)

genogram/
├── _docs/ # ← Os 8 ficheiros .md
│ ├── 00-vision.md
│ ├── 01-schema.md
│ ├── 02-notation.md
│ ├── 03-rbac.md
│ ├── 04-architecture.md # ← Este ficheiro
│ ├── 05-task-board.md
│ ├── 06-progress.md
│ ├── 07-conventions.md
│ └── 08-prompt-templates.md
│
├── engine/ # ← MOTOR (Rust)
│ ├── Cargo.toml
│ ├── src/
│ │ ├── main.rs # Entry point (NAS mode)
│ │ ├── lib.rs # Library (usado por Tauri + WASM)
│ │ ├── models/
│ │ │ ├── mod.rs
│ │ │ ├── genogram.rs # struct Genogram
│ │ │ ├── person.rs # struct Person + enums
│ │ │ ├── relationship.rs # struct Relationship + enums
│ │ │ ├── household.rs
│ │ │ ├── collaborator.rs
│ │ │ ├── medical.rs
│ │ │ ├── substance.rs
│ │ │ ├── genetic.rs
│ │ │ ├── criminal.rs
│ │ │ ├── life_event.rs
│ │ │ ├── custom_field.rs
│ │ │ ├── ecomap.rs
│ │ │ └── audit.rs
│ │ ├── crdt/
│ │ │ ├── mod.rs
│ │ │ ├── doc.rs # Yrs Doc wrapper
│ │ │ ├── sync.rs # Sync logic (deltas)
│ │ │ └── conflict.rs # Conflict resolution
│ │ ├── crypto/
│ │ │ ├── mod.rs
│ │ │ ├── e2e.rs # XChaCha20-Poly1305
│ │ │ ├── keys.rs # Key derivation (HKDF)
│ │ │ └── handshake.rs # E2E handshake
│ │ ├── rbac/
│ │ │ ├── mod.rs
│ │ │ ├── filter.rs # filter_genogram()
│ │ │ ├── roles.rs # Role definitions
│ │ │ └── consent.rs # Consent logic
│ │ ├── storage/
│ │ │ ├── mod.rs
│ │ │ ├── sqlite.rs # SQLite persistence
│ │ │ ├── migrations/
│ │ │ │ ├── v1.sql
│ │ │ │ └── ...
│ │ │ └── repository.rs # CRUD operations
│ │ ├── import_export/
│ │ │ ├── mod.rs
│ │ │ ├── gedcom/
│ │ │ │ ├── mod.rs
│ │ │ │ ├── parser.rs # GEDCOM → Genogram
│ │ │ │ └── writer.rs # Genogram → GEDCOM
│ │ │ ├── fhir/
│ │ │ │ ├── mod.rs
│ │ │ │ └── exporter.rs # Genogram → FHIR Bundle
│ │ │ ├── csv.rs # CSV import
│ │ │ ├── pdf.rs # PDF export
│ │ │ ├── svg.rs # SVG export
│ │ │ └── png.rs # PNG export (via resvg)
│ │ ├── layout/
│ │ │ ├── mod.rs
│ │ │ ├── hierarchical.rs # Auto-arrange por geração
│ │ │ ├── tunneling.rs # Line tunneling (hops)
│ │ │ └── force.rs # Force-directed (fallback)
│ │ ├── validation/
│ │ │ ├── mod.rs
│ │ │ ├── rules.rs # 23+ regras (Problem Spotter)
│ │ │ └── report.rs # Relatório de erros
│ │ ├── webrtc/
│ │ │ ├── mod.rs
│ │ │ ├── peer.rs # Peer connection
│ │ │ ├── data_channel.rs # Data channel (deltas)
│ │ │ └── relay.rs # Relay client (fallback)
│ │ ├── api/ # (NAS mode apenas)
│ │ │ ├── mod.rs
│ │ │ ├── routes.rs # HTTP routes (Axum)
│ │ │ ├── auth.rs # Token auth (LAN)
│ │ │ └── ws.rs # WebSocket (real-time)
│ │ └── error.rs # Error types
│ ├── tests/
│ │ ├── test_models.rs
│ │ ├── test_crdt.rs
│ │ ├── test_crypto.rs
│ │ ├── test_rbac.rs
│ │ ├── test_gedcom.rs
│ │ ├── test_layout.rs
│ │ ├── test_validation.rs
│ │ └── integration/
│ └── benches/
│ └── layout_bench.rs
│
├── frontend/ # ← FRONTEND (Svelte)
│ ├── package.json
│ ├── svelte.config.js
│ ├── vite.config.ts
│ ├── src/
│ │ ├── main.ts
│ │ ├── App.svelte
│ │ ├── lib/
│ │ │ ├── components/
│ │ │ │ ├── canvas/
│ │ │ │ │ ├── GenogramCanvas.svelte
│ │ │ │ │ ├── PersonNode.svelte
│ │ │ │ │ ├── RelationshipLine.svelte
│ │ │ │ │ ├── HouseholdBracket.svelte
│ │ │ │ │ ├── EcoMapCanvas.svelte
│ │ │ │ │ └── Legend.svelte
│ │ │ │ ├── panels/
│ │ │ │ │ ├── PersonPanel.svelte
│ │ │ │ │ ├── MedicalPanel.svelte
│ │ │ │ │ ├── RelationshipPanel.svelte
│ │ │ │ │ ├── CollaboratorPanel.svelte
│ │ │ │ │ └── SettingsPanel.svelte
│ │ │ │ ├── toolbar/
│ │ │ │ │ ├── Toolbar.svelte
│ │ │ │ │ ├── QuickAdd.svelte
│ │ │ │ │ └── ExportMenu.svelte
│ │ │ │ ├── ai/
│ │ │ │ │ ├── AIChat.svelte
│ │ │ │ │ └── AIInsights.svelte
│ │ │ │ └── common/
│ │ │ │ ├── Modal.svelte
│ │ │ │ ├── Toast.svelte
│ │ │ │ └── ContextMenu.svelte
│ │ │ ├── stores/
│ │ │ │ ├── genogram.ts # Zustand store (genogram state)
│ │ │ │ ├── crdt.ts # Yjs doc reference
│ │ │ │ ├── ui.ts # UI state (selected, zoom, etc.)
│ │ │ │ └── collab.ts # Collaboration state
│ │ │ ├── services/
│ │ │ │ ├── engine.ts # Bridge JS ↔ Rust (Tauri/WASM)
│ │ │ │ ├── sync.ts # WebRTC sync
│ │ │ │ ├── crypto.ts # WebCrypto (PWA)
│ │ │ │ └── export.ts # Export handlers
│ │ │ ├── i18n/
│ │ │ │ ├── pt.json
│ │ │ │ ├── en.json
│ │ │ │ └── ...
│ │ │ └── types/
│ │ │ └── index.ts # TS types (gerados do schema)
│ │ └── app.html
│ ├── public/
│ │ ├── manifest.json # PWA manifest
│ │ ├── sw.js # Service Worker
│ │ └── icons/
│ └── tests/
│
├── tauri/ # ← Tauri config
│ ├── tauri.conf.json
│ ├── src/
│ │ ├── main.rs # Tauri entry (desktop)
│ │ ├── lib.rs
│ │ └── commands.rs # Tauri commands (bridge JS↔Rust)
│ ├── capabilities/ # Tauri permissions
│ └── icons/
│
├── nas/ # ← NAS (Docker)
│ ├── Dockerfile
│ ├── docker-compose.yml
│ ├── entrypoint.sh
│ └── config/
│ └── default.toml
│
├── relay/ # ← Relay (dumb pipe)
│ ├── Cargo.toml
│ ├── src/
│ │ ├── main.rs
│ │ └── server.rs # WebSocket relay (não descifra)
│ └── Dockerfile
│
├── schemas/ # ← JSON Schemas
│ ├── genogram.schema.json
│ ├── person.schema.json
│ ├── relationship.schema.json
│ └── notation/
│ ├── relationships.json # 71+ tipos
│ ├── person_shapes.json # 40+ shapes
│ └── medical.json # 22 categorias
│
├── .github/
│ └── workflows/
│ ├── ci.yml # Testes + lint
│ ├── build-desktop.yml # Win/Mac/Linux
│ ├── build-mobile.yml # iOS/Android
│ ├── build-wasm.yml # WASM
│ ├── build-nas.yml # Docker image
│ └── release.yml # Tags → releases
│
├── Cargo.toml # Workspace (engine + relay)
├── rust-toolchain.toml
├── .rustfmt.toml
├── .clippy.toml
└── README.md


---

## 4. Comunicação entre Camadas

### 4.1. Desktop (Tauri)

┌──────────────────────────────────────────────────┐
│ Svelte (WebView) │
│ ┌────────────────────────────────────────────┐ │
│ │ UI Components (Canvas, Panels, Toolbar) │ │
│ └──────────────────┬─────────────────────────┘ │
│ │ │
│ ┌──────────────────┴─────────────────────────┐ │
│ │ Stores (Zustand) + Services │ │
│ └──────────────────┬─────────────────────────┘ │
│ │ │
│ ┌──────────────────┴─────────────────────────┐ │
│ │ Tauri Commands (invoke) │ │
│ │ • create_genogram(data) │ │
│ │ • add_person(genogram_id, person) │ │
│ │ • sync_delta(genogram_id, delta) │ │
│ │ • export_pdf(genogram_id, options) │ │
│ │ • import_gedcom(path) │ │
│ │ • get_validation(genogram_id) │ │
│ └──────────────────┬─────────────────────────┘ │
└─────────────────────┼────────────────────────────┘
│ (IPC — Tauri bridge)
┌─────────────────────┴────────────────────────────┐
│ Rust (Tauri process) │
│ ┌────────────────────────────────────────────┐ │
│ │ Engine (lib) │ │
│ │ • Models + Validation │ │
│ │ • CRDT (Yrs) │ │
│ │ • Crypto (ring) │ │
│ │ • RBAC Filter │ │
│ │ • Storage (SQLite) │ │
│ │ • Import/Export │ │
│ │ • Layout │ │
│ └────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────┘


### 4.2. PWA (Browser)

┌──────────────────────────────────────────────────┐
│ Browser (WebView) │
│ ┌────────────────────────────────────────────┐ │
│ │ Svelte (mesmo código do desktop) │ │
│ └──────────────────┬─────────────────────────┘ │
│ │ │
│ ┌──────────────────┴─────────────────────────┐ │
│ │ Services │ │
│ │ • engine.ts → WASM (motor Rust) │ │
│ │ • crypto.ts → WebCrypto (E2E) │ │
│ │ • sync.ts → WebRTC (nativo no browser) │ │
│ │ • storage → IndexedDB │ │
│ └──────────────────┬─────────────────────────┘ │
│ │ │
│ ┌──────────────────┴─────────────────────────┐ │
│ │ WASM Module (motor Rust compilado) │ │
│ │ • Models + Validation │ │
│ │ • CRDT (Yrs → WASM) │ │
│ │ • Layout │ │
│ │ • GEDCOM parser/writer │ │
│ │ • Validation (Problem Spotter) │ │
│ └────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────┘


### 4.3. NAS (Docker)

┌──────────────────────────────────────────────────┐
│ Docker Container │
│ ┌────────────────────────────────────────────┐ │
│ │ Rust Binary (headless) │ │
│ │ • Engine (lib) │ │
│ │ • WebRTC server (escuta :8443) │ │
│ │ • HTTP API (Axum, :8080) — LAN only │ │
│ │ • SQLite (volume /data) │ │
│ │ • Audit log (SQLite) │ │
│ └────────────────────────────────────────────┘ │
│ │
│ Ports: │
│ • 8443/TCP → WebRTC/DTLS (E2E) │
│ • 8080/TCP → HTTP (UI web, LAN only) │
└──────────────────────────────────────────────────┘


---

## 5. Fluxo de Dados (Sync)

### 5.1. Edição Local

Utilizador edita no canvas (Svelte).
Svelte → Tauri command: update_person(genogram_id, person_id, changes).
Rust: aplica mudança ao Yrs Doc (CRDT).
Rust: valida (Problem Spotter).
Rust: persiste em SQLite.
Rust: gera delta (Yrs update).
Se online → envia delta via WebRTC para peers.
Se offline → guarda delta em fila (SQLite: pending_deltas).
Svelte: UI atualiza (reactivity via Yjs binding).

### 5.2. Sincronização P2P

Device A (online) envia delta via WebRTC DataChannel.
Device B recebe delta.
Device B: aplica delta ao Yrs Doc local.
Device B: valida + persiste em SQLite.
Device B: UI atualiza.
Se RBAC aplica (B é collaborator):
→ O delta já foi filtrado no Device A (owner) antes do envio.
→ Device B nunca recebe dados que não tem permissão.

### 5.3. Sincronização via NAS

iPhone (offline) abre app → sincroniza com NAS (WebRTC).
NAS aplica delta ao Yrs Doc.
NAS persiste em SQLite (volume).
Mac (online) sincroniza com NAS → recebe deltas pendentes.
NAS aplica + persiste.
Todos os dispositivos convergem (CRDT guarantee).

### 5.4. Relay Fallback (NAT traversal)

Device A e B tentam WebRTC directo (STUN).
Se falhar (double NAT) → usam relay.
Relay: recebe ciphertext de A → retransmite ciphertext para B.
Relay NÃO descifra, NÃO guarda, NÃO vê.
A e B fazem handshake E2E (chave efémera).
A partir daqui, o tráfego é E2E (relay é "dumb pipe").

---

## 6. Segurança

### 6.1. Modelo de Ameaças

| Ameaça | Mitigação |
|---|---|
| Interceptação em trânsito | TLS 1.3 (WebRTC/DTLS) + XChaCha20-Poly1305 (payload) |
| Comprometimento de relay | Relay não descifra (zero-knowledge) |
| Comprometimento de dispositivo | E2E: dados encriptados com chave local. Sem chave = sem acesso. |
| Comprometimento de NAS | SQLite encriptado (SQLCipher). Chave no dispositivo do owner. |
| Insider (admin do hospital) | Admin define RBAC mas não vê dados (E2E). Só vê metadados. |
| Replay attack | Nonce + timestamp em cada delta. Window de 5 min. |
| Man-in-the-middle (handshake) | Key exchange via X25519 (ECDH). Fingerprints verificados. |

### 6.2. Chaves

| Chave | Geração | Storage | Uso |
|---|---|---|---|
| Master Key | `os.random(32)` no 1º arranque | SQLite (encriptado com passphrase) / Keychain (iOS) / Keystore (Android) | Deriva todas as outras |
| Device Key | HKDF(Master, device_id) | Local | Identifica o dispositivo |
| Genogram Key | HKDF(Master, genogram_id) | Local | Encripta os deltas CRDT |
| Share Key | HKDF(GenogramKey, collaborator_id + nonce) | Enviada ao colaborador (E2E) | Permite ao colaborador descifrar |
| Ephemeral Key | X25519 (handshake) | Em memória | WebRTC DTLS |

### 6.3. Passphrase (opcional)

O utilizador pode definir uma passphrase (PIN) para:

Desbloquear a Master Key no arranque.
Sem passphrase: a Master Key fica em claro no storage local
(protecção: só o owner do dispositivo tem acesso físico).
Com passphrase: a Master Key é encriptada com Argon2id(passphrase).
→ Se o dispositivo for roubado, sem passphrase = sem acesso.

---

## 7. ADRs (Architecture Decision Records)

### ADR-001: CRDT — Yrs (Yjs em Rust)

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Precisa de sincronização multi-device sem conflitos, offline-first, sem servidor central. |
| **Decisão** | Usar `yrs` (port Rust do Yjs) para o motor e `yjs` (JS) para o frontend. |
| **Alternativas** | Automerge (MIT, mais pesado, menos maduro em Rust), Loro (MIT, menos comunidade), custom CRDT (risco elevado). |
| **Consequências** | + Maturidade (10M+ downloads JS), + Multi-language, + MIT. − Yjs v14 em RC (usar v13 stable). |

### ADR-002: Framework Desktop/Mobile — Tauri 2

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Precisa de 1 código para Win/Mac/Linux/iOS/Android. Motor em Rust. UI em web tech. |
| **Decisão** | Tauri 2 (Rust + WebView). |
| **Alternativas** | Electron (sem mobile, 200 MB), Flutter (motor em Dart, FFI com Rust), React Native (sem desktop nativo). |
| **Consequências** | + 1 código → 6 plataformas, + Rust (segurança), + 3-8 MB, + Mobile. − WebView inconsistencies (QA), − Mobile Tauri mais jovem. |

### ADR-003: Frontend — Svelte 5

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Canvas interativo (drag&drop, zoom, pan). Performance crítica. Bundle size importante (PWA). |
| **Decisão** | Svelte 5 (runes, fine-grained reactivity). |
| **Alternativas** | React 19 (mais ecossistema, virtual DOM = overhead), Vue 3 (boa, mas menos fine-grained). |
| **Consequências** | + Fine-grained reactivity (sem re-render global), + Bundle pequeno, + Runes (type-safe). − Ecossistema menor que React. |

### ADR-004: Canvas — Konva.js

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Renderização de nós (pessoas) e linhas (relações). Drag&drop. Zoom/Pan. Line tunneling. |
| **Decisão** | Konva.js (HTML5 Canvas). |
| **Alternativas** | D3.js (SVG, mais pesado para interactividade), PixiJS (WebGL, overkill), SVG puro (lento com 200+ nós). |
| **Consequências** | + Performance (Canvas), + Drag&drop nativo, + Scene graph. − Não é SVG (export SVG requer conversão). |

### ADR-005: E2E Encryption — XChaCha20-Poly1305 (ring)

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | E2E encryption dos deltas CRDT. Rápida em mobile. Sem hardware crypto. |
| **Decisão** | XChaCha20-Poly1305 via crate `ring`. Key exchange: X25519. KDF: HKDF-SHA256. |
| **Alternativas** | AES-256-GCM (mais lento em software/mobile), libsodium (boa, mas `ring` tem mais adoção). |
| **Consequências** | + Rápida (software), + Auditada (RFC 8439), + Mesma do Signal. − Não tem hardware acceleration (irrelevante para deltas pequenos). |

### ADR-006: P2P — WebRTC + Relay Fallback

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Sincronização entre dispositivos. E2E by design. Funciona em browser e nativo. |
| **Decisão** | WebRTC DataChannels (nativo no browser + `webrtc-rs` em Rust). Relay (WebSocket) como fallback para NAT traversal. |
| **Alternativas** | libp2p (mais complexo, overkill para <10 peers), custom TCP (NAT traversal manual), MQTT broker (não é P2E). |
| **Consequências** | + Nativo em todos os targets, + E2E (DTLS), + STUN/ICE (NAT traversal). − Relay necessário para ~5-10% de pares. |

### ADR-007: Storage Local — SQLite

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Persistência local. Transacional. Zero-config. Funciona em desktop, mobile, NAS, WASM. |
| **Decisão** | SQLite (`rusqlite` em Rust, `sql.js` ou `wa-sqlite` em WASM, `IndexedDB` como fallback no PWA). |
| **Alternativas** | LevelDB (mais complexo), LMDB (boa, mas menos suportada em mobile), IndexedDB apenas (sem transações). |
| **Consequências** | + Zero-config, + Transacional, + Universal, + Public Domain. − Single-writer (irrelevante: 1 utilizador por dispositivo). |

### ADR-008: NAS — Docker Headless

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | NAS (Synology, QNAP) como nó persistente. Sempre ligado. |
| **Decisão** | Docker container com binary Rust headless (sem UI). WebRTC server + HTTP API (LAN). |
| **Alternativas** | Binary nativo (mais complexo de distribuir por arquitectura), Synology Package (limitado a DSM), VM (pesado). |
| **Consequências** | + Universal (qualquer NAS com Docker), + Actualizações fáceis (pull image), + Volume mount (backup). − Precisa de Docker no NAS. |

### ADR-009: PWA — WASM + Service Worker

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Acesso via browser sem instalação. Offline. Performance próxima de nativa. |
| **Decisão** | Motor Rust compilado para WASM + Service Worker (offline cache) + IndexedDB (storage). |
| **Alternativas** | Puro JS (lento para layout/crypto), Electron no browser (impossível), iframe (segurança). |
| **Consequências** | + Performance (WASM ≈ nativa), + Offline (SW), + Sem instalação. − WASM bundle (~2-5 MB), − iOS PWA limitado (sem push nativo). |

### ADR-010: RBAC — Filtering na Sincronização

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Dados sensíveis (genético, criminal, emocional) não devem chegar a dispositivos não autorizados. |
| **Decisão** | Filtrar os deltas CRDT **antes** do envio (no dispositivo do owner ou no NAS). O colaborador nunca recebe dados que não tem permissão. |
| **Alternativas** | Filtrar na UI (inseguro: dado está no dispositivo), Filtrar no relay (relay não tem chaves), Filtrar no frontend (mesmo problema). |
| **Consequências** | + Segurança máxima (dado não existe no dispositivo), + Compliance (RGPD minimização). − Complexidade no motor (filter antes de serializar). |

### ADR-011: AI — On-Device (Fase 4)

| Campo | Valor |
|---|---|
| **Status** | Accepted (deferred) |
| **Data** | 2026-11-04 |
| **Contexto** | AI para geração/análise de genogramas. Zero-knowledge: dados não saem do dispositivo. |
| **Decisão** | Inferência on-device (modelo local). Desktop: llama.cpp / MLX. Mobile: Core ML (iOS) / TFLite (Android). |
| **Alternativas** | Cloud AI (viola zero-knowledge), Híbrido (risco de leak), Sem AI (perde funcionalidade). |
| **Consequências** | + Zero-knowledge, + Funciona offline, + Privacidade total. − Modelos menores (7B-13B), − Menos accuracy que cloud, − Fase 4 (não MVP). |

### ADR-012: Estado — Zustand (via svelte-zustand)

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | State management no frontend. Simples, performático, sem boilerplate. |
| **Decisão** | Zustand (via wrapper `svelte-zustand`). |
| **Alternativas** | Svelte stores nativos (limitados para state complexo), Redux (overkill), Pinia (Vue). |
| **Consequências** | + Minimalista, + TypeScript, + DevTools, + Sem provider. − Menos ecossistema que Redux. |

### ADR-013: i18n — svelte-i18n

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | 18 idiomas. Labels de símbolos, UI, export. |
| **Decisão** | `svelte-i18n` (JSON files). |
| **Alternativas** | `paraglide-js` (gerado, mais complexo), custom (mantenção). |
| **Consequências** | + Simples, + JSON (editável), + Svelte-native. − Sem pseudo-localization (fazer à mão). |

### ADR-014: CI/CD — GitHub Actions

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Build multi-plataforma. Testes. Releases. |
| **Decisão** | GitHub Actions (matrix: Win/Mac/Linux/iOS/Android/WASM/Docker). |
| **Alternativas** | GitLab CI (boa, mas GitHub mais comum), CircleCI (pago), Buildkite (self-hosted). |
| **Consequências** | + Gratuito (public), + Matrix builds, + Caching, + Releases. − iOS build precisa de macOS runner (GitHub tem). |

### ADR-015: Licença — MIT

| Campo | Valor |
|---|---|
| **Status** | Accepted |
| **Data** | 2026-11-04 |
| **Contexto** | Código open-source. Permite uso comercial. Sem copyleft. |
| **Decisão** | MIT para todo o código do projeto. |
| **Alternativas** | Apache-2.0 (patent grant, mais longo), GPL (copyleft — impede uso em SaaS), BSD-3 (similar a MIT). |
| **Consequências** | + Máxima permissividade, + Simples, + Comercial-friendly. − Sem patent grant (mitigado: Apache-2.0 nas deps). |

---

## 8. Performance — Targets

| Métrica | Target | Medição |
|---|---|---|
| Abertura genograma (200 pessoas) | < 2s (desktop), < 4s (mobile) | `performance.now()` |
| Renderização canvas (200 nós + 500 linhas) | < 100ms | Konva `draw()` time |
| Sync delta (1 mudança) | < 2s (LAN), < 5s (internet) | WebRTC RTT |
| GEDCOM import (1000 pessoas) | < 5s | `cargo bench` |
| PDF export (3 gerações) | < 3s | `printpdf` time |
| Layout auto-arrange (200 nós) | < 500ms | `cargo bench` |
| WASM load (PWA) | < 1s (após cache) | Service Worker timing |
| Bundle size (PWA) | < 5 MB (WASM + JS) | `vite build --report` |
| App size (desktop) | < 15 MB | `tauri build` output |
| Memória (desktop, 200 pessoas) | < 150 MB | Task Manager / Activity Monitor |
| Memória (mobile, 100 pessoas) | < 100 MB | Xcode Instruments / Android Profiler |

---

## 9. Testes — Estratégia

| Nível | Ferramenta | Cobertura Target |
|---|---|---|
| Unit (Rust) | `cargo test` + `proptest` (property-based) | 90%+ (engine) |
| Unit (JS) | `vitest` | 80%+ (stores, services) |
| Integration (Rust) | `cargo test` (SQLite + CRDT + crypto) | Todos os fluxos de sync |
| Integration (JS) | `vitest` + `playwright` (canvas) | CRUD + sync |
| E2E | `playwright` (desktop) + `appium` (mobile) | Fluxos críticos |
| Property-based | `proptest` (Rust) | CRDT convergence, RBAC filtering |
| Fuzzing | `cargo fuzz` | GEDCOM parser, crypto |
| Performance | `cargo bench` + `web-vitals` | Targets do §8 |
| Security | `cargo audit` + `npm audit` + `trivy` (Docker) | 0 vulnerabilidades críticas |

### 9.1. Testes Críticos (não negociáveis)

| # | Teste | Porquê |
|---|---|---|
| T1 | CRDT: 2 dispositivos editam offline → sync → mesmo estado | Correctness do sync |
| T2 | RBAC: cardiologista NUNCA recebe dados de mental | Segurança |
| T3 | E2E: interceptar tráfego → ilegível | Privacidade |
| T4 | GEDCOM: import → export → import = idêntico | Round-trip |
| T5 | Layout: 1000 pessoas → não trava | Performance |
| T6 | WASM: motor no browser = motor em Rust (mesmos resultados) | Paridade |
| T7 | Revogação: colaborador removido → dados apagados no próximo sync | Segurança |
| T8 | Consent: `Refused` → invisível a não-owner | Compliance |

---

## 10. Deploy — Matriz de Targets

| Target | Formato | Build | Distribuição |
|---|---|---|---|
| Windows x64 | `.msi` + `.exe` (NSIS) | `tauri build --target x86_64-pc-windows-msvc` | Site + Microsoft Store |
| macOS (Apple Silicon) | `.app` + `.dmg` (universal) | `tauri build --target aarch64-apple-darwin` | Site + Mac App Store |
| macOS (Intel) | `.app` + `.dmg` | `tauri build --target x86_64-apple-darwin` | Site |
| Linux x64 | `.AppImage` + `.deb` + `.rpm` | `tauri build --target x86_64-unknown-linux-gnu` | Site + Flatpak + AUR |
| Linux ARM | `.AppImage` | `tauri build --target aarch64-unknown-linux-gnu` | Site |
| iOS | `.ipa` | `tauri build --platform ios` | App Store |
| Android | `.apk` + `.aab` | `tauri build --platform android` | Play Store + APK direto |
| PWA | HTML + JS + WASM | `vite build` + `wasm-pack build` | Site (CDN) |
| NAS | Docker image | `docker build` (multi-arch: amd64 + arm64) | Docker Hub / GHCR |
| Relay | Docker image | `docker build` | VPS / NAS / Cloud (opcional) |

### 10.1. Signing & Notarization

| Target | O que fazer |
|---|---|
| Windows | Code signing (EV cert) + SmartScreen |
| macOS | Notarization (Apple Developer) + Hardened Runtime |
| Linux | GPG signature (AppImage) |
| iOS | Apple Developer signing (auto) |
| Android | Play App Signing (Google) |
| Docker | Sigstore / cosign |

---

## 11. Regras de Implementação (para a IA)

1. **Nunca** usar uma crate/biblioteca que não está na stack (§2) sem ADR.
2. **Nunca** adicionar dependências GPL/AGPL.
3. **Sempre** usar `serde` para serialização (nunca formatar JSON à mão).
4. **Sempre** usar `thiserror` para errors typed (nunca `String` como error).
5. **Sempre** usar `tracing` para logging (nunca `println!` em produção).
6. **Nunca** fazer I/O no frontend (Svelte) — sempre via Tauri command ou WASM.
7. **Nunca** guardar dados em `localStorage` (PWA) — usar `IndexedDB`.
8. **Sempre** validar inputs na fronteira (Tauri command / WASM boundary).
9. **Nunca** enviar dados não encriptados via WebRTC (sempre E2E).
10. **Sempre** rodar `cargo clippy` + `cargo fmt` antes de commit.
11. **Sempre** rodar `vitest` antes de commit (frontend).
12. **Nunca** hardcodar strings de UI — sempre via i18n.
13. **Sempre** usar UUID v7 (nunca v4) para IDs.
14. **Nunca** usar `unwrap()` em código de produção (Rust) — usar `?` ou handle.
15. **Sempre** incluir `#[cfg(test)] mod tests` em cada ficheiro Rust com lógica.
