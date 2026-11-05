# Genogram — Prompt Templates (v1.0)

> Templates reutilizáveis para trabalhar com IA no desenvolvimento.
> 
> **Como usar:**
> 1. Escolher o template correcto (§2-§6).
> 2. Colar o task card de `05-task-board.md` (a secção "O que fazer" + "Aceitação").
> 3. Colar a secção relevante de `01-schema.md` (só a entidade em causa).
> 4. Enviar à IA.
> 
> **Regra de ouro:** Nunca enviar o `01-schema.md` inteiro. Só a secção relevante.
> Salva tokens e reduz alucinação.

---

## 1. Regras Gerais (incluir SEMPRE no contexto)

PROJECT CONTEXT:

Project: Genogram (local-first, cross-platform genogram tool)
Vision: _docs/00-vision.md
Stack: Rust (engine) + Svelte 5 (frontend) + Tauri 2 (desktop/mobile)
CRDT: Yrs (Rust) + Yjs (JS)
Encryption: XChaCha20-Poly1305 (ring crate)
Storage: SQLite (rusqlite)
License: MIT
Conventions: _docs/07-conventions.md (OBRIGATÓRIO)
Architecture: _docs/04-architecture.md
ABSOLUTE RULES:

NEVER create a field that is not in the schema.
NEVER use a library not in the stack (04-architecture.md §2).
NEVER use unwrap() in production Rust code.
NEVER use any in TypeScript.
ALWAYS include #[cfg(test)] mod tests with at least 3 test cases.
ALWAYS use thiserror for error types.
ALWAYS use tracing for logging (never println!).
ALWAYS validate inputs at the boundary.
If the schema is ambiguous, ASK before assuming.
Do NOT add features not in the task.

---

## 2. Template: Implementar Tarefa (Rust — Engine)

[COLEAR §1 — Regras Gerais]

TASK:
<colar o task card de 05-task-board.md>

SCHEMA (relevante):
<colar a secção de 01-schema.md — ex: "§3.3 MedicalRecord">

CONVENTIONS (relevantes):
<colar a secção de 07-conventions.md — ex: "§1.3 Error Handling">

INSTRUCTIONS:

Create the file:
Implement exactly what the task says. Nothing more.
Include #[cfg(test)] mod tests with:
At least 3 test cases (happy path, edge case, error case)
Test names: test_
Use thiserror for any new error variants needed.
Use tracing for logging.
If you need a helper function that doesn't exist yet, define it in the same file.
Do NOT create other files unless explicitly stated in the task.
DELIVERABLE:

Complete file content (ready to paste)
List of any new dependencies needed in Cargo.toml
List of any assumptions made (if schema was ambiguous)

### Exemplo concreto (Task 1.1.3 — MedicalRecord)

[COLEAR §1 — Regras Gerais]

TASK:
ID: 1.1.3
File: engine/src/models/medical.rs
What: MedicalRecord struct + MedicalCategory + MedicalStatus + Quadrant
Acceptance: cargo test passes, 5 unit tests

SCHEMA:
pub struct MedicalRecord {
pub id: Uuid,
pub person_id: Uuid,
pub category: MedicalCategory,
pub condition: String,
pub icd10_code: Option,
pub snomed_ct: Option,
pub status: MedicalStatus,
pub onset_date: Option,
pub resolution_date: Option,
pub quadrant: Quadrant,
pub severity: Option,
pub notes: Option,
pub source: Option,
pub created_at: i64,
pub updated_at: i64,
}

pub enum MedicalCategory {
Cardiovascular, Hypertension, Stroke, Arrhythmia, HeartFailure,
PeripheralVascular, Depression, Anxiety, Bipolar, Schizophrenia,
Psychosis, Dementia, Alzheimer, Parkinson, Epilepsy, Adhd, Autism,
StrokeNeuro, OtherNeuro, CancerBreast, CancerColon, CancerLung,
CancerProstate, CancerOvary, CancerLeukemia, CancerLymphoma,
CancerSkin, CancerOther, DiabetesType1, DiabetesType2,
DiabetesGestational, Thyroid, Obesity, Asthma, Allergy, Autoimmune,
Respiratory, Musculoskeletal, Renal, Hepatic, Infectious, Genetic, Other,
}

pub enum MedicalStatus {
Active, Suspected, Remission, Recovered, Deceased,
}

pub enum Quadrant {
TopLeft, TopRight, BottomLeft, BottomRight, Center,
}

CONVENTIONS:

Serde: #[derive(Debug, Clone, Serialize, Deserialize)]
Datas: #[serde(with = "chrono::serde::iso8601")]
UUIDs: #[serde(with = "uuid::serde::simple")]
Enums: #[serde(rename_all = "snake_case")]
Tests: #[cfg(test)] mod tests at end of file
INSTRUCTIONS:

Create engine/src/models/medical.rs
Implement the 3 enums + 1 struct as specified
Add a validate() method on MedicalRecord that checks:
condition is not empty (max 300 chars)
severity is 1-5 if Some
resolution_date >= onset_date (if both Some)
onset_date >= person birth_date (pass birth_date as param)
Include 5 unit tests:
test_medical_valid_basic
test_medical_invalid_empty_condition
test_medical_invalid_severity_range
test_medical_invalid_resolution_before_onset
test_medical_serialization_roundtrip
DELIVERABLE:

Complete file content
New Cargo.toml deps (if any)
Assumptions (if any)

---

## 3. Template: Implementar Tarefa (Svelte — Frontend)

[COLEAR §1 — Regras Gerais]

TASK:
<colar o task card de 05-task-board.md>

SCHEMA (relevante):
<colar a secção de 01-schema.md>

NOTATION (relevante):
<colar a secção de 02-notation.md — ex: "§2 Relationship Lines — Partner">

CONVENTIONS (relevantes):
<colar a secção de 07-conventions.md — ex: "§2.2 Svelte 5 Runes">

EXISTING CODE (contexto):
<colar o conteúdo dos ficheiros que este componente vai usar>
Ex: "useGenogramStore.ts" (o store que o componente vai ler/escrever)

INSTRUCTIONS:

Create the file:
Use Svelte 5 runes ($state, $derived, $effect, $props)
Use TypeScript (lang="ts")
Import types from '../types' (not redefine them)
Use the store (useGenogramStore) for state — do NOT create local state for shared data
Use i18n (t('...')) for ALL user-facing strings
Include ARIA labels for accessibility
Do NOT use Konva directly in this component unless specified (use the canvas wrapper)
Keep the component under 200 lines. If larger, split into sub-components.
DELIVERABLE:

Complete .svelte file content
Any new TypeScript types needed (in src/lib/types/index.ts)
Any new store actions needed
Assumptions (if any)

### Exemplo concreto (Task 2.1.2 — PersonNode)

[COLEAR §1 — Regras Gerais]

TASK:
ID: 2.1.2
File: frontend/src/lib/components/canvas/PersonNode.svelte
What: PersonNode (quadrado/círculo, drag, select)
Acceptance: renders a square for male, circle for female; draggable; selectable

SCHEMA:
Person struct (see 01-schema.md §3) — relevant fields:

id, name, gender, generation, position, x, y
is_deceased, is_index, is_pregnant
medical_records (for quadrant rendering — Phase 2.4)
NOTATION:

person_male: square, 40×40px, stroke #333333
person_female: circle, 40px diameter, stroke #333333
person_deceased: X through symbol
person_index: double outline
person_pregnant: triangle inside circle
CONVENTIONS:

Svelte 5 runes
TypeScript strict
Konva for rendering (konva-rect, konva-circle)
ARIA labels
EXISTING CODE:
// useGenogramStore.ts (relevant parts)
export const useGenogramStore = create((set) => ({
persons: [],
selectedPersonId: null,
selectPerson: (id) => set({ selectedPersonId: id }),
// ...
}));

// types/index.ts
export interface Person {
id: string;
name: string;
gender: 'male' | 'female' | 'non_binary' | 'intersex' | 'unknown';
x: number;
y: number;
is_deceased: boolean;
is_index: boolean;
is_pregnant: boolean;
// ...
}

INSTRUCTIONS:

Create PersonNode.svelte
Props: person: Person
Render:
Male → konva-rect (40×40)
Female → konva-circle (r=20)
Non-binary → konva-path (dome shape)
Unknown → konva-rect rotated 45° (diamond)
If is_deceased: draw X (2 lines) through the symbol
If is_index: double stroke (2 rects/circles, 2px apart)
If is_pregnant: small triangle inside (for female)
Drag: konva draggable, on dragend → update x,y in store
Select: on click → store.selectPerson(person.id)
Visual feedback: if selected → stroke #2980B9, width 3
ARIA: role="img", aria-label=", , "
Label below: person.name (12px, centered)
DELIVERABLE:

Complete PersonNode.svelte
Any new types needed
Assumptions

---

## 4. Template: Rever Código

[COLEAR §1 — Regras Gerais]

REVIEW REQUEST:
I wrote the following code for task . Please review it.

CODE:
<colar o código>

CHECK AGAINST:

Schema (01-schema.md §): Are all fields correct? Any missing? Any extra?
Conventions (07-conventions.md): Naming, error handling, logging, tests?
Architecture (04-architecture.md): Does it follow the ADRs?
Task acceptance criteria: <colar "Aceitação" do task card>
RULES:

Do NOT rewrite the code. Only list problems.
For each problem: line number, what's wrong, what it should be.
Severity: [BLOCKER] (breaks functionality), [WARNING] (convention violation), [INFO] (suggestion).
If the code is correct, say "APPROVED" and list any minor suggestions.

---

## 5. Template: Depurar

[COLEAR §1 — Regras Gerais]

BUG REPORT:
Task:
File:
Error:
<colar o erro exacto (cargo test output, browser console, etc.)>

CONTEXT:

What I was doing: <descrição>
What I expected:
What happened:
Schema relevant: <colar secção de 01-schema.md>
Conventions relevant: <colar secção de 07-conventions.md>
RULES:

Explain the root cause (1-3 sentences).
Propose the MINIMAL fix (not a rewrite).
Show the exact code to change (before → after).
Add a regression test that would have caught this bug.
If the bug is in the schema/spec (not the code), say so and propose the spec change.

---

## 6. Template: Decidir / Comparar

[COLEAR §1 — Regras Gerais]

DECISION NEEDED:
Problem: <descrição do problema>
Context: <colar o contexto técnico — ADRs relevantes, constraints>
Criteria: <listar os critérios de decisão — ex: performance, complexidade, manutenção, dependências>
Constraints: <limitações — ex: "tem de ser MIT", "tem de correr em WASM", "máx 5MB bundle">

RULES:
1. Present a comparison table (option | pros | cons | risk).
2. Give ONE recommendation with a 2-sentence justification.
3. If the decision affects the schema or architecture, flag it: "⚠️ Requires ADR update".
4. Do NOT implement. Only decide.

DELIVERABLE:
- Comparison table
- Recommendation + justification
- ADR draft (if needed)
- Impact on existing tasks (if any)

7. Template: Gerar Testes
[COLEAR §1 — Regras Gerais]

TASK: Generate tests for an existing module.

MODULE:
<colar o conteúdo do ficheiro a testar>

SCHEMA (relevante):
<colar a secção de 01-schema.md>

EXISTING TESTS (se houver):
<colar os testes que já existem>

INSTRUCTIONS:
- Generate tests that cover:
  1. Happy path (input válido → output correcto)
  2. Edge cases (boundaries: 0, 1, max, empty, null)
  3. Error cases (input inválido → error correcto)
  4. Integration (se aplicável: 2 entidades a interactuar)
  5. Property-based (se aplicável: CRDT, crypto, validation)
- Test naming: test_<entity>_<scenario>_<expected>
- Use proptest for property-based tests (Rust) or fast-check (TS)
- Target: 90%+ line coverage for the module
- Do NOT modify the module being tested. Only write the test file.
- If a test reveals a bug in the module, mark it with #[ignore] + comment "BUG: <descrição>"

DELIVERABLE:
- Complete test file content
- List of bugs found (if any)
- Coverage estimate (%)

Exemplo concreto (Tests para filter_genogram)
[COLEAR §1 — Regras Gerais]

TASK: Generate tests for engine/src/rbac/filter.rs

MODULE:
pub fn filter_genogram(genogram: &Genogram, perms: &PermissionSet) -> Genogram {
    let mut filtered = genogram.clone();
    for person in &mut filtered.persons {
        if !perms.medical_categories.is_empty() {
            person.medical_records.retain(|mr| perms.medical_categories.contains(&mr.category));
        }
        if !perms.show_substance { person.substance_records.clear(); }
        if !perms.show_genetic { person.genetic_tests.clear(); }
        if !perms.show_criminal { person.criminal_records.clear(); }
        if !perms.show_cultural { person.cultural_heritage.clear(); }
        if !perms.show_religion { person.religion = None; }
        if !perms.show_social {
            person.occupation = None;
            person.education = None;
            person.residence = None;
            person.socioeconomic = None;
        }
        if !perms.show_notes { person.notes = None; }
        if !perms.show_custom_fields { person.custom_fields.clear(); }
        if !perms.show_life_events { person.life_events.clear(); }
        if !perms.show_photos { person.photo_ref = None; }
    }
    if !perms.show_emotional {
        filtered.relationships.retain(|r| r.category != RelationshipCategory::Emotional);
    }
    if !perms.show_notes {
        for rel in &mut filtered.relationships { rel.notes = None; }
    }
    if !perms.show_households { filtered.households.clear(); }
    filtered
}

SCHEMA:
PermissionSet (see 03-rbac.md §3)

INSTRUCTIONS:
- Generate 10 tests:
  1. test_filter_cardiologist_no_mental — cardiologist perms → no mental records
  2. test_filter_cardiologist_keeps_cardio — cardiologist perms → cardio records kept
  3. test_filter_empty_categories_means_all — medical_categories: [] → all kept
  4. test_filter_no_criminal — show_criminal: false → criminal_records empty
  5. test_filter_no_emotional — show_emotional: false → emotional relationships removed
  6. test_filter_no_social — show_social: false → occupation/education/residence = None
  7. test_filter_no_notes — show_notes: false → all notes = None
  8. test_filter_no_households — show_households: false → households empty
  9. test_filter_all_true_nothing_removed — all perms true → genogram unchanged
  10. test_filter_all_false_only_identity — all false → only name/gender/birth/death remain
- Use a helper fn: fn make_test_genogram() -> Genogram (with 2 persons, 1 medical each category, 1 emotional rel, 1 household)
- Property test: for any PermissionSet, filtered.persons.len() == original.persons.len() (filtering removes fields, not persons)

DELIVERABLE:
- Complete test file: engine/tests/test_rbac_filter.rs
- Helper functions
- Coverage estimate

8. Template: Refactor
[COLEAR §1 — Regras Gerais]

REFACTOR REQUEST:
File(s): <path(s)>
Reason: <porquê — ex: "ficheiro > 300 linhas", "código duplicado", "naming inconsistente">
Constraint: O comportamento NÃO pode mudar. Todos os testes existentes têm de continuar a passar.

CURRENT CODE:
<colar o código actual>

EXISTING TESTS:
<colar os testes existentes>

INSTRUCTIONS:
1. Identify the problem (1-2 sentences).
2. Propose the refactor (what changes, what stays).
3. Show the refactored code (complete file).
4. Show any new files created (if splitting).
5. Confirm: "All existing tests pass without modification" (or list which tests need updating and why).
6. Do NOT add new features. Do NOT change the public API (unless the reason justifies it).

DELIVERABLE:
- Refactored file(s)
- New files (if any)
- Tests that need updating (if any) + updated version
- Before/after: line count, cyclomatic complexity

9. Template: GEDCOM / FHIR Mapping
[COLEAR §1 — Regras Gerais]

TASK: Implement GEDCOM/FHIR import or export.

DIRECTION: [import | export]
FORMAT: [GEDCOM 5.5.1 | FHIR R4]
TASK CARD:
<colar o task card>

SCHEMA:
<colar a secção de 01-schema.md>

MAPPING TABLE:
<colar a secção de 01-schema.md §8 (GEDCOM) ou §9 (FHIR)>

SAMPLE INPUT (se import):
<colar um extract de GEDCOM/FHIR real (anónimo)>

SAMPLE OUTPUT (se export):
<colar o esperado>

INSTRUCTIONS:
- Implement the mapping exactly as specified in the mapping table.
- Fields without a mapping: export as NOTE with prefix [Genogram] (GEDCOM) or omit (FHIR).
- Handle missing data gracefully (Option → skip, don't error).
- Include 3 integration tests:
  1. Round-trip (import → export → import = idêntico) [GEDCOM only]
  2. Edge case: GEDCOM with 0 families (only individuals)
  3. Edge case: FHIR with missing birthDate
- Fuzz test (GEDCOM parser): 100 random inputs → no crash, no panic.

DELIVERABLE:
- Complete file content
- New Cargo.toml deps (if any)
- Mapping gaps found (fields in schema without GEDCOM/FHIR equivalent)

10. Template: WebRTC / Sync
[COLEAR §1 — Regras Gerais]

TASK: Implement WebRTC/sync functionality.

TASK CARD:
<colar o task card>

CRDT CONTEXT:
<colar a secção de 01-schema.md §11 (Versioning) + 04-architecture.md §5 (Fluxo de Dados)>

CRYPTO CONTEXT:
<colar a secção de 04-architecture.md §6 (Segurança)>

INSTRUCTIONS:
- Implement exactly what the task says.
- E2E: ALL data sent via WebRTC MUST be encrypted with XChaCha20-Poly1305.
- The relay MUST NOT be able to decrypt. (Test: intercept relay traffic → assert ciphertext.)
- Handle: connection lost → retry (exponential backoff, max 5 retries).
- Handle: peer offline → queue deltas locally (PendingDeltaQueue).
- Include 3 integration tests:
  1. Two peers connect → send delta → receive in < 2s
  2. Peer disconnects → reconnect → pending deltas synced
  3. Relay intercept → data is ciphertext (not readable)
- Use tokio for async (NAS mode) or native browser WebRTC (PWA mode).

DELIVERABLE:
- Complete file content
- New Cargo.toml deps
- Network diagram (ASCII) of the flow
- Assumptions

11. Regras de Personalização
11.1. Consoante o Tipo de Tarefa
Tipo de tarefa	O que adicionar ao prompt	O que remover
Model (struct/enum)	Schema completo da entidade + 2-3 exemplos JSON	Notation, Conventions de UI
Crypto	ADR-005 + RFC 8439 (XChaCha20) + ADR-006 (X25519)	Schema, Notation
CRDT	ADR-001 + Yrs docs + exemplo de Yrs Map	Notation, Conventions de UI
RBAC	03-rbac.md completo + ADR-010	Notation
Canvas (Svelte)	02-notation.md (secção visual) + Konva docs + store existente	Schema (só os campos renderizados)
Layout (Rust)	02-notation.md §15 (Regras de Renderização) + exemplo de genograma	Crypto, RBAC
GEDCOM/FHIR	01-schema.md §8/§9 (mapping) + sample input	Notation, Canvas
WebRTC	04-architecture.md §5 + §6 + ADR-006	Schema (só o tipo de delta)
UI (panels)	01-schema.md (campos do form) + i18n keys + store	Notation (só se renderiza símbolos)
Docker/NAS	04-architecture.md §2.5 + docker-compose example	Schema, Notation, Canvas

11.2. Consoante a Complexidade
Complexidade	Ajustes no prompt
Simples (< 30 min, 1 struct, 1 enum)	Template básico. Sem "existing code".
Média (30 min - 2h, 1 módulo, 2-3 ficheiros)	Adicionar "EXISTING CODE" (os ficheiros que vai tocar).
Complexa (> 2h, multi-ficheiro, integração)	Adicionar "DEPENDENCIES" (o que tem de estar pronto) + "INTEGRATION POINTS" (como se liga ao resto). Pedir à IA para listar os ficheiros que vai criar antes de os escrever.

11.3. Consoante a Fase
Fase	Ajustes
Fase 0 (Schema)	Foco em JSON Schema validation. Pedir ajv tests. Sem Rust.
Fase 1 (Motor)	Foco em Rust + tests. Sem UI. Pedir cargo test + cargo clippy.
Fase 2 (Canvas)	Foco em Svelte + Konva. Incluir store existente. Pedir vitest.
Fase 3 (Import/Export)	Foco em parsing + round-trip. Incluir samples. Pedir fuzz tests.
Fase 4 (PWA/Sync)	Foco em WASM + WebRTC. Incluir crypto context. Pedir security tests.
Fase 5 (NAS/Collab)	Foco em Docker + RBAC. Incluir docker-compose. Pedir integration tests.
Fase 6+ (Polish)	Foco em UX + i18n + performance. Incluir performance targets.

12. Exemplos Completos por Fase
12.1. Fase 0 — Exemplo: JSON Schema para Person
[COLEAR §1 — Regras Gerais]

TASK:
ID: 0.1.2
File: schemas/person.schema.json
What: JSON Schema (draft 2020-12) for the Person entity
Acceptance: Valid JSON Schema. Validates the example in 01-schema.md §14.

SCHEMA:
<colar 01-schema.md §3 completo (Person + todos os enums)>

RULES:
- Use JSON Schema draft 2020-12
- All required fields in "required" array
- Enums as "enum" with snake_case values
- Dates as "type": "string", "format": "date"
- UUIDs as "type": "string", "format": "uuid"
- $ref for nested entities (MedicalRecord, etc.)
- "additionalProperties": false (strict)
- Include "examples" with 1 valid example

DELIVERABLE:
- Complete schemas/person.schema.json
- 3 validation test cases (valid, invalid gender, missing required)

12.2. Fase 1 — Exemplo: CRDT Merge Test
[COLEAR §1 — Regras Gerais]

TASK:
ID: 1.3.7
File: engine/tests/test_crdt_merge.rs
What: Integration test — 2 Docs edit offline → merge → same state
Acceptance: cargo test passes. Both docs converge after sync.

SCHEMA:
Genogram struct (01-schema.md §2) — only the fields that are CRDT-tracked:
- persons (Yrs Map)
- relationships (Yrs Map)
- Each person: name, gender, x, y, medical_records (Yrs Array)

CRDT CONTEXT:
- Yrs Doc per genogram
- Each Person is a Yrs Map (key = person_id)
- Each Relationship is a Yrs Map (key = rel_id)
- Sync: exchange Yrs updates (binary deltas)

INSTRUCTIONS:
- Test scenario:
  1. Create Doc A and Doc B (same initial state: 2 persons, 1 relationship)
  2. On Doc A: change person 1 name to "Maria", add medical record
  3. On Doc B: change person 2 gender to Female, add new relationship
  4. Sync A → B (exchange updates)
  5. Assert: A.state() == B.state()
  6. Assert: person 1 name == "Maria" in both
  7. Assert: person 2 gender == Female in both
  8. Assert: medical record exists in both
  9. Assert: new relationship exists in both

- Also test: concurrent edit of SAME field (both change person 1 name)
  → LWW (last-writer-wins) based on timestamp
  → Both converge to the same value

DELIVERABLE:
- Complete test file
- Helper: fn make_initial_doc() -> CrdtDoc
- 2 test functions (different fields, same field)

12.3. Fase 2 — Exemplo: QuickAdd
[COLEAR §1 — Regras Gerais]

TASK:
ID: 2.2.2
File: frontend/src/lib/components/toolbar/QuickAdd.svelte
What: Quick-add via keyboard (keys 1-5)
Acceptance: Pressing 1-5 adds a person with the correct relationship to the selected person

KEY MAPPING:
- 1: Add child (below selected, next position)
- 2: Add father (above selected, left of partner)
- 3: Add mother (above selected, right of partner)
- 4: Add partner (same generation, next to selected)
- 5: Add sibling (same generation, next position)

SCHEMA:
Person (01-schema.md §3) — generation, position
Relationship (01-schema.md §4) — type: biological (1,2,3,5), married (4)

EXISTING CODE:
// useGenogramStore.ts
export const useGenogramStore = create<GenogramState>((set) => ({
  persons: [],
  relationships: [],
  selectedPersonId: null,
  addPerson: (p) => set((s) => ({ persons: [...s.persons, p] })),
  addRelationship: (r) => set((s) => ({ relationships: [...s.relationships, r] })),
  selectPerson: (id) => set({ selectedPersonId: id }),
}));

INSTRUCTIONS:
- Listen for keydown (1-5) when canvas is focused (not when typing in input)
- On key press:
  1. Get selected person from store
  2. If none selected → show toast "Select a person first"
  3. Calculate new person's generation and position based on key
  4. Create Person (with UUID v7, default values)
  5. Create Relationship (biological or married)
  6. Add both to store
  7. Select the new person
  8. Animate: new node fades in (CSS transition, 200ms)
- Show a small tooltip near the toolbar: "1: Filho  2: Pai  3: Mãe  4: Parceiro  5: Irmão"
- i18n: all labels via t('quickadd.child'), t('quickadd.father'), etc.
- Do NOT handle edge cases (e.g., adding father when father already exists) — that's Phase 2.2.3 (context menu)

DELIVERABLE:
- Complete QuickAdd.svelte
- New i18n keys (pt + en)
- New store actions needed (if any)

12.4. Fase 3 — Exemplo: GEDCOM Parser
[COLEAR §1 — Regras Gerais]

TASK:
ID: 3.1.2
File: engine/src/import_export/gedcom/parser.rs
What: Parse GEDCOM INDIVIDUAL records → Vec<Person>
Acceptance: Parses a sample GEDCOM with 5 individuals → 5 Person structs (correct fields)

MAPPING:
- NAME → Person.name
- SEX → Person.gender (M=male, F=female, U=unknown)
- BIRT/DATE → Person.birth_date
- DEAT/DATE → Person.death_date
- DEAT/CAUS → Person.death_cause
- OCCU → Person.occupation
- RESI/PLAC → Person.residence
- NATI → Person.nationality
- NOTE → Person.notes (append)
- HSP → MedicalRecord (category: "other", condition: text)

SAMPLE INPUT:
0 HEAD
1 SOUR GenoPro
2 NAME GenoPro 7.0
0 @I1@ INDI
1 NAME Silva / João /
1 SEX M
1 BIRT
2 DATE 12 MAR 1945
1 DEAT
2 DATE 22 AUG 2010
2 CAUS Infarto agudo do miocárdio
1 OCCU Mecânico
1 RESI
2 PLAC Leiria, Portugal
1 NOTE Pessoa índice
0 @I2@ INDI
1 NAME Silva / Maria /
1 SEX F
1 BIRT
2 DATE 5 JUN 1948

INSTRUCTIONS:
- Parse line by line (GEDCOM is line-based, level-indented)
- Level 0: new record (INDI, FAM, HEAD)
- Level 1: field of current record
- Level 2: sub-field (e.g., DATE under BIRT)
- Handle: multi-line values (continuation lines start with "+")
- Handle: missing fields (→ None)
- Handle: unknown tags (→ skip, don't error)
- Date format: "12 MAR 1945" → NaiveDate (use chrono with custom format "%d %b %Y")
- Name format: "Silva / João /" → "João Silva" (surname / given / suffix)
- Return: Vec<Person> (without relationships — those come from FAM records, task 3.1.3)
- Include 5 unit tests:
  1. test_parse_basic_individual (the sample above)
  2. test_parse_missing_death (no DEAT → death_date = None)
  3. test_parse_unknown_tag (skip, don't error)
  4. test_parse_multi_line_note (continuation with +)
  5. test_parse_invalid_date (→ error, not panic)

DELIVERABLE:
- Complete parser.rs (INDIVIDUAL section only)
- Helper: fn parse_gedcom_date(s: &str) -> Result<NaiveDate, AppError>
- 5 unit tests

12.5. Fase 4 — Exemplo: WASM Parity Test
[COLEAR §1 — Regras Gerais]

TASK:
ID: 4.1.5
File: engine/tests/test_wasm_parity.rs
What: Verify WASM engine produces identical results to native Rust engine
Acceptance: For 100 random operations, WASM output == native output

INSTRUCTIONS:
- This test runs in 2 modes:
  - Native: calls engine functions directly
  - WASM: calls the same functions via wasm-bindgen (in a WASM test harness)
- Operations to test (100 random):
  1. Create genogram with N persons (N = 1..50)
  2. Add M relationships (M = 0..30)
  3. Add K medical records (K = 0..10)
  4. Apply RBAC filter (random PermissionSet)
  5. Run validation (Problem Spotter)
  6. Export to JSON
- Assert: native_result == wasm_result (byte-for-byte for JSON)
- Use proptest to generate random inputs
- Run in CI with: wasm32-unknown-unknown target
- Note: This test is SLOW (compiles WASM). Mark with #[ignore] for local dev. Run in CI only.

DELIVERABLE:
- Complete test file
- Helper: fn random_genogram(seed: u64) -> Genogram
- CI config: run only on push to main (not on every PR)

13. Checklist Pré-Prompt
Antes de enviar qualquer prompt à IA, verificar:

[ ] O task card está completo (O que fazer + Aceitação + Dependências)?
[ ] As dependências estão ✅ no 06-progress.md?
[ ] Colei a secção correcta do schema (não o documento inteiro)?
[ ] Colei as convenções relevantes (não o documento inteiro)?
[ ] O prompt tem "ABSOLUTE RULES" (§1)?
[ ] O prompt tem "DELIVERABLE" (o que esperar da IA)?
[ ] O prompt tem "Do NOT add features not in the task"?
[ ] Se é UI: colei o store existente?
[ ] Se é Rust: colei o error type (AppError)?
[ ] Se é crypto: colei o ADR-005?
[ ] O task é pequeno o suficiente (< 30 min)? Se não → dividir.
14. Erros Comuns da IA + Correção
#	Erro típico	Causa	Correção no prompt
E1	IA inventa campos que não existem no schema	Prompt sem schema	Sempre colar a secção do schema. Adicionar: "If you need a field not in the schema, use custom_fields. Do NOT add new fields."
E2	IA usa React quando o projecto é Svelte	IA "sabe" React melhor	Adicionar: "This project uses Svelte 5 with runes. Do NOT use React, Vue, or any other framework."
E3	IA usa Electron quando o projecto é Tauri	IA confunde	Adicionar: "Desktop framework: Tauri 2 (NOT Electron). Mobile: Tauri mobile (NOT React Native, NOT Flutter)."
E4	IA usa unwrap() em Rust	Hábito da IA	Adicionar: "NEVER use unwrap() or expect() in production code. Use ? or match."
E5	IA adiciona features não pedidas	Prompt vago	Adicionar: "Implement EXACTLY what the task says. If you think something else is needed, list it under 'SUGGESTIONS' at the end. Do NOT implement it."
E6	IA escreve código sem tests	Prompt sem "DELIVERABLE" claro	Adicionar: "The deliverable MUST include #[cfg(test)] mod tests with at least 3 test cases."
E7	IA usa any em TypeScript	Hábito da IA	Adicionar: "NEVER use any. Use unknown + type guard, or define a proper interface."
E8	IA hardcode strings de UI	Não sabe que existe i18n	Adicionar: "ALL user-facing strings MUST use i18n: t('key'). Do NOT hardcode text."
E9	IA cria ficheiros que não foram pedidos	Prompt sem limite	Adicionar: "Create ONLY the file(s) specified in the task. Do NOT create other files."
E10	IA ignora o RBAC	Prompt sem contexto de segurança	Adicionar: "Remember: RBAC filtering happens in the engine (Rust), NOT in the UI. The UI only renders what it receives."
E11	IA usa AES-256-GCM em vez de XChaCha20	IA "sabe" AES melhor	Adicionar: "Encryption: XChaCha20-Poly1305 (NOT AES-256-GCM). Key exchange: X25519. KDF: HKDF-SHA256. See ADR-005."
E12	IA guarda dados em localStorage (PWA)	Hábito web	Adicionar: "Storage in PWA: IndexedDB (NOT localStorage). See ADR-007."
E13	IA cria um "server" central	Não entende local-first	Adicionar: "This is a LOCAL-FIRST app. There is NO central server. The 'server' (NAS) is a peer node owned by the user. See 00-vision.md."
E14	IA ignora o CRDT e usa "last write wins" manual	Não entende Yrs	Adicionar: "State synchronization uses Yrs (CRDT). Do NOT implement manual conflict resolution. The CRDT handles it."
E15	IA exporta dados sensíveis para FHIR	Não sabe as restrições	Adicionar: "FHIR export: NEVER export EcoMap, CriminalRecord, Emotional relationships, or GeneticTest without consent. See 01-schema.md §9."

15. Fluxo de Sessão (Resumo)
1. Abrir 05-task-board.md → escolher próxima tarefa (⬜, deps ✅)
2. Marcar 🔶 no 05-task-board.md e 06-progress.md
3. Escolher template correcto (§2-§10)
4. Preencher: task card + schema + conventions + existing code
5. Verificar checklist (§13)
6. Enviar à IA
7. Rever output (template §4 — "Rever Código")
8. Rodar tests (cargo test / vitest)
9. Se passa → marcar ✅ + actualizar 06-progress.md
10. Se falha → template §5 ("Depurar") → corrigir → repetir
11. Commit (formato 07-conventions.md §3.2)
