# Genogram — Coding Conventions (v1.0)

> Regras de estilo, naming, patterns e boas práticas.
> Estas convenções são **obrigatórias**. Se a IA gerar código que as viola,
> o código é rejeitado.

---

## 1. Rust (Engine)

### 1.1. Naming

| Elemento | Convenção | Exemplo |
|---|---|---|
| Crates / pacotes | `snake_case` | `genogram_engine` |
| Módulos (`mod`) | `snake_case` | `mod medical_record` |
| Structs / Enums / Traits | `PascalCase` | `struct Person`, `enum Gender` |
| Funções / métodos | `snake_case` | `fn filter_genogram()` |
| Variáveis / parâmetros | `snake_case` | `let genogram_id: Uuid` |
| Constantes | `SCREAMING_SNAKE_CASE` | `const MAX_PERSONS: u32 = 10_000` |
| Type aliases | `PascalCase` | `type GenogramId = Uuid` |
| Generics | `PascalCase` (1 letra se simples) | `fn map<T, U>(...)` |
| Feature flags (Cargo) | `snake_case` | `#[cfg(feature = "nas-mode")]` |
| Testes | `test_` prefix + `snake_case` | `fn test_person_valid_gender()` |
| Ficheiros | `snake_case.rs` | `medical_record.rs` |
| Ficheiros de módulo | `mod.rs` (re-exports) | |

### 1.2. Módulos & Organização

Regra: 1 entidade = 1 ficheiro.
Regra: mod.rs só faz re-exports (pub use). Não tem lógica.
Regra: #[cfg(test)] mod tests no FINAL do ficheiro.
Regra: Se um ficheiro > 300 linhas → dividir.


### 1.3. Error Handling

```rust
// ✅ SEMPRE: thiserror para errors typed
#[derive(Debug, thiserror::Error)]
pub enum AppError {
    #[error("storage: {0}")]
    Storage(#[from] rusqlite::Error),

    #[error("validation: {0}")]
    Validation(#[from] ValidationError),

    #[error("crypto: {0}")]
    Crypto(#[from] CryptoError),

    #[error("gedcom: {0}")]
    Gedcom(String),

    #[error("not found: {0}")]
    NotFound(String),

    #[error("permission denied: {0}")]
    PermissionDenied(String),
}

// ✅ SEMPRE: ? para propagar
pub fn load_genogram(id: Uuid) -> Result<Genogram, AppError> {
    let conn = self.conn.lock().unwrap();
    let row = conn.query_row(...)?;  // ? propaga rusqlite::Error → AppError
    Ok(row)
}

// ❌ NUNCA: unwrap() em produção
let value = map.get(key).unwrap();  // PANIC em produção

// ✅ SEMPRE: match ou ?
let value = map.get(key).ok_or(AppError::NotFound(key.to_string()))?;

// ✅ NUNCA: expect() em produção (só em tests)
let value = map.get(key).expect("key must exist after validation");  // OK em test

1.4. Serde
// ✅ SEMPRE: derives standard
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Person { ... }

// ✅ Enums: tag = "type" (externally tagged por omissão)
// Se precisar de internally tagged:
#[serde(tag = "type", rename_all = "snake_case")]
pub enum RelationshipType { ... }

// ✅ Datas: serializar como string ISO 8601
#[serde(with = "chrono::serde::iso8601")]
pub birth_date: Option<NaiveDate>,

// ✅ UUIDs: serializar como string
#[serde(with = "uuid::serde::simple")]
pub id: Uuid,

// ❌ NUNCA: serde_json::to_string() à mão
// ✅ SEMPRE: serde::Serialize / Deserialize

1.5. Logging
// ✅ SEMPRE: tracing (nunca println! / eprintln! em produção)
use tracing::{info, warn, error, debug, trace, instrument};

#[instrument(fields(genogram_id = %genogram_id, person_id = %person_id))]
pub fn add_person(&self, genogram_id: Uuid, person: Person) -> Result<PersonId, AppError> {
    debug!(person_name = %person.name, "adding person");
    // ...
    info!(person_id = %id, "person added");
    Ok(id)
}

// ✅ Níveis:
// trace! → debug muito detalhado (só em dev)
// debug! → informação de debug (states, deltas)
// info!  → eventos importantes (create, delete, sync)
// warn!  → algo inesperado mas recuperável
// error! → falha (com contexto)

// ❌ NUNCA: logar dados sensíveis (nomes, diagnósticos) em produção
// ✅ SEMPRE: logar IDs, timestamps, contagens

1.6. Tests
// ✅ SEMPRE: #[cfg(test)] mod tests no final do ficheiro
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_person_valid_gender() {
        let person = Person {
            gender: Gender::Male,
            // ...
        };
        assert!(person.validate().is_ok());
    }

    #[test]
    fn test_person_invalid_gender() {
        // ...
    }

    // ✅ Property-based (proptest) para CRDT, crypto, validation
    proptest! {
        #[test]
        fn crdt_convergence(
            edits_a in proptest::collection::vec(any_edit(), 0..50),
            edits_b in proptest::collection::vec(any_edit(), 0..50),
        ) {
            let doc_a = CrdtDoc::new();
            let doc_b = CrdtDoc::new();
            for e in &edits_a { doc_a.apply(e); }
            for e in &edits_b { doc_b.apply(e); }
            doc_a.sync(&doc_b);
            assert_eq!(doc_a.state(), doc_b.state());
        }
    }
}

// ✅ Naming de tests: test_<entidade>_<cenário>_<esperado>
// Ex: test_rbac_cardiologist_cannot_see_mental

1.7. Clippy & Format
# .clippy.toml
msrv = "1.82"
avoid-breaking-exported-api = true

# .rustfmt.toml
edition = "2021"
max_width = 100
use_small_heuristics = "Max"

# SEMPRE antes de commit:
cargo fmt --all
cargo clippy --all -- -D warnings
cargo test --all

1.8. Segurança (Rust)
// ✅ SEMPRE: validar inputs na fronteira (Tauri command / WASM boundary)
#[tauri::command]
pub fn add_person(genogram_id: Uuid, person: Person) -> Result<PersonId, AppError> {
    person.validate()?;  // ← SEMPRE validar antes de persistir
    // ...
}

// ✅ NUNCA: confiar em dados do frontend sem validar
// ✅ SEMPRE: usar Option<T> (nunca String vazio para "não existe")
// ✅ SEMPRE: usar u8/u16/u32 com validação de range (nunca i32 para idades)
// ✅ NUNCA: usar unsafe (excepto em FFI com comment justificando)
// ✅ SEMPRE: usar Arc<Mutex<T>> ou tokio::sync para shared state (nunca global mutable)

1.9. Cargo.toml
# ✅ SEMPRE: workspace root
[workspace]
members = ["engine", "relay"]
resolver = "2"

# ✅ Engine:
[package]
name = "genogram-engine"
version = "0.1.0"
edition = "2021"
license = "MIT"

[lib]
name = "genogram_engine"
crate-type = ["lib", "cdylib", "rlib"]  # rlib (Tauri), cdylib (WASM)

[dependencies]
# Core
serde = { version = "1", features = ["derive"] }
serde_json = "1"
uuid = { version = "1", features = ["v7", "serde"] }
chrono = { version = "0.4", features = ["serde"] }
thiserror = "2"
anyhow = "1"
tracing = "0.1"

# CRDT
yrs = "0.28"

# Crypto
ring = "0.17"
argon2 = "0.5"

# Storage
rusqlite = { version = "0.31", features = ["bundled"] }

# WebRTC (NAS mode)
webrtc = { version = "0.12", optional = true }
tokio = { version = "1", features = ["full"], optional = true }
axum = { version = "0.8", optional = true }

[features]
default = []
nas-mode = ["webrtc", "tokio", "axum"]
wasm = []

[dev-dependencies]
proptest = "1"
criterion = "0.5"

2. TypeScript / Svelte (Frontend)
2.1. Naming
Elemento	Convenção	Exemplo
Ficheiros .svelte	PascalCase.svelte	PersonNode.svelte
Ficheiros .ts	camelCase.ts	useGenogramStore.ts
Componentes	PascalCase	GenogramCanvas
Funções / variáveis	camelCase	const addPerson = () => {}
Constantes	SCREAMING_SNAKE_CASE	const MAX_ZOOM = 400
Interfaces / Types	PascalCase (sem prefix I)	interface PersonData
Stores (Zustand)	use prefix	useGenogramStore
Hooks / composables	use prefix	useCrdt
Eventos (Svelte)	camelCase (com :)	on:nodeSelected
CSS classes (se necessário)	kebab-case	.person-node

2.2. Svelte 5 (Runes)
<!-- ✅ SEMPRE: usar runes (Svelte 5) -->
<script lang="ts">
  let person = $state<Person | null>(null);
  let persons = $state<Person[]>([]);
  let selectedId = $state<Uuid | null>(null);

  // ✅ Derived (em vez de $derived antigo)
  let selectedPerson = $derived(persons.find(p => p.id === selectedId) ?? null);

  // ✅ Effect (em vez de $effect antigo)
  $effect(() => {
    if (selectedPerson) {
      document.title = selectedPerson.name;
    }
  });

  // ✅ Snippet (em vez de slot)
  let layout = $props<Layout>();
</script>

<!-- ✅ Props -->
<script lang="ts">
  let { person, onEdit, onDelete }: {
    person: Person;
    onEdit: (p: Person) => void;
    onDelete: (id: Uuid) => void;
  } = $props();
</script>

<!-- ❌ NUNCA: this. (Svelte 5 não usa this) -->
<!-- ❌ NUNCA: export let (usar $props) -->

2.3. TypeScript
// ✅ SEMPRE: strict mode
// tsconfig.json: "strict": true, "noUncheckedIndexedAccess": true

// ✅ SEMPRE: types explícitos (nunca any)
// ❌ NUNCA: any
// ✅ Se necessário: unknown + type guard
function parseResponse(data: unknown): Person {
  if (!isValidPerson(data)) throw new Error("Invalid person data");
  return data; // narrowed to Person
}

// ✅ SEMPRE: import types com `import type`
import type { Person, Relationship } from './types';
import { useGenogramStore } from './stores/genogram';

// ✅ Naming de types:
// - Interfaces para objects públicos: `interface PersonData`
// - Types para unions / primitives: `type GenogramId = string`
// - Generics: `type Result<T, E = Error> = { ok: true, value: T } | { ok: false, error: E }`

2.4. Estado (Zustand)
// ✅ SEMPRE: 1 store por domínio
// useGenogramStore.ts
import { create } from 'zustand';
import type { Genogram, Person, Relationship } from '../types';

interface GenogramState {
  // Data
  genogram: Genogram | null;
  persons: Person[];
  relationships: Relationship[];
  selectedPersonId: string | null;
  selectedRelationshipId: string | null;

  // Actions
  setGenogram: (g: Genogram) => void;
  addPerson: (p: Person) => void;
  updatePerson: (id: string, changes: Partial<Person>) => void;
  deletePerson: (id: string) => void;
  addRelationship: (r: Relationship) => void;
  selectPerson: (id: string | null) => void;
  selectRelationship: (id: string | null) => void;
}

export const useGenogramStore = create<GenogramState>((set) => ({
  genogram: null,
  persons: [],
  relationships: [],
  selectedPersonId: null,
  selectedRelationshipId: null,

  setGenogram: (g) => set({ genogram: g, persons: g.persons, relationships: g.relationships }),
  addPerson: (p) => set((s) => ({ persons: [...s.persons, p] })),
  updatePerson: (id, changes) =>
    set((s) => ({
      persons: s.persons.map((p) => (p.id === id ? { ...p, ...changes } : p)),
    })),
  deletePerson: (id) =>
    set((s) => ({
      persons: s.persons.filter((p) => p.id !== id),
      relationships: s.relationships.filter((r) => r.source_id !== id && r.target_id !== id),
      selectedPersonId: s.selectedPersonId === id ? null : s.selectedPersonId,
    })),
  addRelationship: (r) => set((s) => ({ relationships: [...s.relationships, r] })),
  selectPerson: (id) => set({ selectedPersonId: id, selectedRelationshipId: null }),
  selectRelationship: (id) => set({ selectedRelationshipId: id, selectedPersonId: null }),
}));

2.5. Componentes Svelte
<!-- ✅ Estrutura padrão de um componente -->
<script lang="ts">
  // 1. Imports
  import { onMount } from 'svelte';
  import type { Person } from '../../types';

  // 2. Props
  let { person, onEdit }: { person: Person; onEdit: (p: Person) => void } = $props();

  // 3. State
  let isDragging = $state(false);

  // 4. Derived
  let displayName = $derived(person.alias ?? person.name);

  // 5. Effects
  $effect(() => { /* side effects */ });

  // 6. Functions
  function handleDragEnd(e: KonvaEventObject<DragEvent>) { /* ... */ }
</script>

<!-- 7. Template -->
<konva-rect
  :x="person.x"
  :y="person.y"
  width={40}
  height={40}
  :draggable="true"
  on:dragend={handleDragEnd}
/>

<!-- 8. Estilos (se necessário, <style> no final) -->
<style>
  :global(.person-label) { font-size: 12px; }
</style>

2.6. Services (Bridge)
// ✅ SEMPRE: 1 service por responsabilidade
// engine.ts — bridge para o motor Rust (Tauri ou WASM)
import { invoke } from '@tauri-apps/api/core';

// Detectar ambiente
const isTauri = typeof window !== 'undefined' && '__TAURI_INTERNALS__' in window;

export async function createGenogram(name: string): Promise<string> {
  if (isTauri) {
    return invoke<string>('create_genogram', { name });
  } else {
    // WASM (PWA)
    const { genogram_engine } = await import('../../public/wasm/genogram_engine.js');
    return genogram_engine.create_genogram(name);
  }
}

export async function addPerson(genogramId: string, person: Person): Promise<string> {
  if (isTauri) {
    return invoke<string>('add_person', { genogramId, person });
  } else {
    const { genogram_engine } = await import('../../public/wasm/genogram_engine.js');
    return genogram_engine.add_person(genogramId, JSON.stringify(person));
  }
}

// ✅ SEMPRE: tratar errors
export async function loadGenogram(id: string): Promise<Genogram> {
  try {
    if (isTauri) {
      return await invoke<Genogram>('load_genogram', { id });
    } else {
      const { genogram_engine } = await import('../../public/wasm/genogram_engine.js');
      const json = genogram_engine.load_genogram(id);
      return JSON.parse(json);
    }
  } catch (e) {
    throw new EngineError(`Failed to load genogram: ${e}`);
  }
}

2.7. i18n
// ✅ SEMPRE: strings de UI via i18n (nunca hardcodar)
import { t } from 'svelte-i18n';

// No componente:
<button>{t('toolbar.new_genogram')}</button>
<input placeholder={t('person.name_placeholder')} />

// ❌ NUNCA:
<button>New Genogram</button>  // ← hardcodado

// ✅ pt.json:
// { "toolbar": { "new_genogram": "Novo Genograma" } }
// ✅ en.json:
// { "toolbar": { "new_genogram": "New Genogram" } }

// ✅ Chaves: `domínio.acção` ou `domínio.elemento`
// Ex: "person.name", "medical.category_cardiovascular", "export.pdf"

2.8. CSS / Estilos
/* ✅ SEMPRE: CSS em <style> do componente (scoped) */
/* ✅ Variáveis CSS para cores (do 02-notation.md §13) */
:root {
  --color-bg: #FFFFFF;
  --color-border: #333333;
  --color-cardio: #E74C3C;
  --color-mental: #F39C12;
  --color-oncology: #27AE60;
  --color-metabolic: #2980B9;
  --color-conflict: #FF6B6B;
  --color-cutoff: #95A5A6;
  --color-close: #2ECC71;
}

/* ✅ SEMPRE: usar variáveis (nunca hardcodar cores) */
.person-node { stroke: var(--color-border); }
.medical-tl { fill: var(--color-cardio); }

/* ❌ NUNCA: !important (excepto em overrides de libs) */
/* ❌ NUNCA: IDs no CSS (usar classes) */

2.9. Testing (Frontend)
// ✅ Vitest + @testing-library/svelte
import { render, screen, fireEvent } from '@testing-library/svelte';
import { describe, it, expect, beforeEach } from 'vitest';
import PersonNode from '../lib/components/canvas/PersonNode.svelte';
import { useGenogramStore } from '../lib/stores/genogram';

describe('PersonNode', () => {
  beforeEach(() => {
    useGenogramStore.getState().setGenogram(mockGenogram);
  });

  it('renders a square for male', () => {
    render(PersonNode, { props: { person: mockMalePerson } });
    expect(screen.getByTestId('person-node')).toHaveAttribute('shape', 'rect');
  });

  it('renders a circle for female', () => {
    render(PersonNode, { props: { person: mockFemalePerson } });
    expect(screen.getByTestId('person-node')).toHaveAttribute('shape', 'circle');
  });
});

3. Git
3.1. Branches
Branch	Uso
main	Produção. Só recebe via PR. Sempre deployable.
develop	Integração. Recebe PRs de features.
feat/<tarefa-id>	Feature nova. Ex: feat/1.1.9-person-struct
fix/<tarefa-id>	Bug fix. Ex: fix/2.2.7-undo-redo
chore/<descrição>	Infra, deps, config. Ex: chore/update-yrs-0.29

3.2. Commit Messages
Formato: <type>(<scope>): <subject>

Types:
  feat:     Nova funcionalidade
  fix:      Correção de bug
  docs:     Documentação
  style:    Formatação (sem mudança de lógica)
  refactor: Refactor (sem mudança de comportamento)
  perf:     Performance
  test:     Tests
  chore:    Infra, deps, CI
  security: Correção de segurança

Exemplos:
  feat(engine): add Person struct with validation
  fix(crdt): resolve merge conflict when both delete same person
  docs: update 01-schema.md with consent_scope field
  test(rbac): add test for cardiologist cannot see mental
  chore: bump yrs from 0.28.0 to 0.28.3

3.3. Pull Requests
Título: <type>(<scope>): <resumo>
Body:
  ## O que faz
  <1-3 frases>

  ## Tarefa
  Ref: <task ID de 05-task-board.md>

  ## Testes
  - [ ] Unit tests passam
  - [ ] Integration tests passam
  - [ ] Manual test: <descrição>

  ## Screenshots (se UI)
  <anexar>

4. Ficheiros & Directórios
4.1. Naming de Ficheiros
Tipo	Convenção	Exemplo
Rust source	snake_case.rs	medical_record.rs
Rust tests	test_<módulo>.rs (em tests/)	test_crdt_merge.rs
Svelte components	PascalCase.svelte	PersonNode.svelte
TypeScript	camelCase.ts	useGenogramStore.ts
JSON (i18n)	xx.json (BCP 47)	pt.json, en.json
JSON (schema)	snake_case.schema.json	person.schema.json
SQL migrations	v<N>.sql	v1.sql, v2.sql
Config	kebab-case.toml / .json	tauri.conf.json
Docker	Dockerfile (capital D)	Dockerfile
Docs	NN-nome.md	01-schema.md
ADRs	ADR-NNN-nome.md	ADR-001-yrs.md

4.2. Estrutura de Pastas
Regra: Pastas em `snake_case` (Rust) ou `camelCase` (JS/TS).
Regra: 1 pasta por módulo/domínio.
Regra: Máx 3 níveis de profundidade (excepto `node_modules`).
Regra: `__tests__` não existe — tests ficam em `tests/` (Rust) ou ao lado (Svelte).

5. Segurança
5.1. Regras Absolutas
#	Regra
S1	Nunca hardcodar chaves, tokens, ou secrets no código.
S2	Nunca logar dados sensíveis (nomes, diagnósticos, chaves).
S3	Nunca enviar dados não encriptados via WebRTC.
S4	Nunca aceitar inputs do frontend sem validar no motor.
S5	Nunca usar eval(), new Function(), ou innerHTML com dados do utilizador.
S6	Sempre sanitizar inputs antes de usar em SQL (rusqlite faz prepared statements por omissão).
S7	Sempre validar UUIDs (formato) antes de usar como FK.
S8	Nunca expor o SQLite database em rede (só local + Docker volume).
S9	Sempre usar HTTPS (TLS) para o relay e API do NAS.
S10	Nunca guardar a Master Key em claro (sempre encriptada com passphrase ou Keychain/Keystore).

5.2. Secrets Management
# ✅ Dev: .env.local (no .gitignore)
# ✅ NAS: environment variables no docker-compose.yml (volume secret)
# ✅ CI: GitHub Secrets
# ❌ NUNCA: .env no repo, .env.example com valores reais

6. Performance
#	Regra
P1	Nunca fazer I/O em loops (batch queries).
P2	Sempre usar prepared statements (rusqlite faz por omissão).
P3	Nunca clonar Genogram inteiro para uma mudança (usar deltas CRDT).
P4	Sempre usar Vec::with_capacity() quando o tamanho é conhecido.
P5	Nunca alocar em hot paths (layout, render) — usar &str em vez de String.
P6	Sempre usar mmap para ficheiros grandes (GEDCOM > 10MB).
P7	Nunca fazer JSON.parse em hot paths (cache o resultado).
P8	Sempre lazy-load o WASM module (dynamic import).
P9	Nunca re-renderizar o canvas inteiro para 1 mudança (Konva: layer.draw() só a layer afectada).
P10	Sempre usar requestAnimationFrame para animações (nunca setInterval).

7. Acessibilidade
#	Regra
A1	Sempre aria-label em nós e linhas (ver 02-notation.md §16).
A2	Sempre navegação por teclado (Tab, Enter, Delete, Ctrl+Z).
A3	Sempre contraste WCAG AA (4.5:1) para texto.
A4	Sempre focus visible (outline não removido).
A5	Nunca usar apenas cor para transmitir informação (sempre + forma/símbolo).
A6	Sempre role="img" + aria-label no canvas.

8. Documentação de Código
// ✅ SEMPRE: doc comments em structs e funções públicas
/// Cria uma nova pessoa no genograma.
///
/// # Arguments
/// * `genogram_id` - ID do genograma de destino
/// * `person` - Dados da pessoa (já validados)
///
/// # Returns
/// * `Ok(PersonId)` - ID da pessoa criada
/// * `Err(AppError)` - Se o genograma não existir ou a validação falhar
///
/// # Errors
/// * `AppError::NotFound` - Genograma não existe
/// * `AppError::Validation` - Person não passa validação
pub fn add_person(&self, genogram_id: Uuid, person: Person) -> Result<PersonId, AppError> {
    // ...
}

// ✅ SEMPRE: JSDoc em functions públicas
/**
 * Cria um novo genograma.
 * @param name - Nome do genograma (máx 200 chars)
 * @returns ID do genograma criado
 * @throws {EngineError} Se o nome for inválido
 */
export async function createGenogram(name: string): Promise<string> {
  // ...
}

9. Regras Finais (Checklist por Commit)
Antes de cada commit, verificar:

[ ] cargo fmt --all → sem diffs
[ ] cargo clippy --all -- -D warnings → 0 warnings
[ ] cargo test --all → todos passam
[ ] npm run lint (ESLint) → 0 errors
[ ] npm test (Vitest) → todos passam
[ ] npm run build (Vite) → sem errors
[ ] Sem console.log / println! em produção
[ ] Sem any (TS) / unwrap() (Rust) em produção
[ ] Strings de UI via i18n (não hardcodadas)
[ ] Novo código tem tests
[ ] Commit message segue o formato (§3.2)
[ ] Se mudou schema → actualizar 01-schema.md
[ ] Se mudou stack → actualizar 04-architecture.md + ADR
[ ] Se mudou tarefa → actualizar 05-task-board.md + 06-progress.md
