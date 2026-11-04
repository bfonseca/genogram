# Genogram — RBAC & Permissions (v1.0)

> Este documento define quem vê o quê, quem pode fazer o quê,
> e como o filtering é aplicado durante a sincronização.
> O princípio fundamental: o dado restrito nunca chega ao dispositivo não autorizado.

---

## 1. Princípios

| # | Princípio | Implicação técnica |
|---|---|---|
| P1 | Mínimo necessário | Por omissão, um colaborador vê nada. O admin concede explicitamente. |
| P2 | Filtering na sincronização | O motor aplica o filtro antes de serializar os deltas CRDT para o dispositivo do colaborador. O dado filtrado não existe no dispositivo. |
| P3 | Separação dono/colaborador | O dono (owner) vê tudo, sempre. Não é filtrado. |
| P4 | Reversibilidade | Remover um colaborador revoga o acesso. Os dados já presentes no dispositivo são apagados no próximo sync. |
| P5 | Auditabilidade | Todo acesso (leitura, edição, exportação) é registado no NAS. |
| P6 | Configurabilidade | O admin do hospital define roles próprios. O sistema tem defaults, mas não é rígido. |
| P7 | Consentimento individual | Dados genéticos de terceiros exigem consentimento explícito do titular (DL 131/2014). |

---

## 2. Roles (defaults)

### 2.1. Role: `owner`

| Permissão | Valor |
|---|---|
| Ver | Tudo |
| Editar | Tudo |
| Adicionar pessoa/relação | ✓ |
| Apagar | ✓ |
| Convidar colaborador | ✓ |
| Revogar colaborador | ✓ |
| Exportar (PNG/PDF/SVG/GEDCOM/FHIR) | ✓ |
| Partilhar | ✓ |
| Apagar genograma | ✓ |
| Alterar permissões | ✓ |
| Ver audit log | ✓ |

### 2.2. Role: `editor`

| Permissão | Valor |
|---|---|
| Ver | Tudo |
| Editar | Tudo (exceto permissões e gestão de colaboradores) |
| Adicionar pessoa/relação | ✓ |
| Apagar | ✗ |
| Convidar colaborador | ✗ |
| Revogar colaborador | ✗ |
| Exportar | ✓ |
| Partilhar | ✗ |
| Apagar genograma | ✗ |
| Alterar permissões | ✗ |
| Ver audit log | ✗ |

### 2.3. Role: `viewer`

| Permissão | Valor |
|---|---|
| Ver | Tudo (read-only) |
| Editar | ✗ |
| Adicionar pessoa/relação | ✗ |
| Apagar | ✗ |
| Convidar colaborador | ✗ |
| Revogar colaborador | ✗ |
| Exportar | ✓ (PNG/PDF/SVG apenas; não GEDCOM/FHIR) |
| Partilhar | ✗ |
| Apagar genograma | ✗ |
| Alterar permissões | ✗ |
| Ver audit log | ✗ |

### 2.4. Role: `specialist` (configurável)

| Permissão | Valor |
|---|---|
| Ver | Filtrado (ver §3) |
| Editar | Filtrado (apenas nos campos visíveis) |
| Adicionar pessoa/relação | Configurável (default: ✗) |
| Apagar | ✗ |
| Convidar colaborador | ✗ |
| Revogar colaborador | ✗ |
| Exportar | Configurável (default: ✓ PNG/PDF apenas) |
| Partilhar | ✗ |
| Apagar genograma | ✗ |
| Alterar permissões | ✗ |
| Ver audit log | ✗ |

### 2.5. Role: `auditor`

| Permissão | Valor |
|---|---|
| Ver | Metadados apenas (quem acessou, quando, o quê; nunca conteúdo clínico) |
| Editar | ✗ |
| Adicionar pessoa/relação | ✗ |
| Apagar | ✗ |
| Convidar colaborador | ✗ |
| Revogar colaborador | ✗ |
| Exportar | ✓ (audit log apenas) |
| Partilhar | ✗ |
| Apagar genograma | ✗ |
| Alterar permissões | ✗ |
| Ver audit log | ✓ |

---

## 3. PermissionSet (para `specialist`)

```rust
use std::collections::HashMap;

pub struct PermissionSet {
    // ─── O que este colaborador PODE VER ───

    /// Categorias médicas visíveis.
    /// [] (vazio) = TUDO (equivalente a "all").
    /// [Cardiovascular, Metabolic] = só essas.
    pub medical_categories: Vec<MedicalCategory>,

    /// Ver dinâmicas emocionais (relações da categoria Emotional)?
    pub show_emotional: bool,

    /// Ver dados sociais (ocupação, educação, residência, socioeconomic)?
    pub show_social: bool,

    /// Ver herança cultural?
    pub show_cultural: bool,

    /// Ver religião?
    pub show_religion: bool,

    /// Ver histórico criminal?
    pub show_criminal: bool,

    /// Ver testes genéticos?
    pub show_genetic: bool,

    /// Ver substâncias (álcool, tabaco, drogas)?
    pub show_substance: bool,

    /// Ver campos custom?
    pub show_custom_fields: bool,

    /// Ver notas livres (person.notes, relationship.notes)?
    pub show_notes: bool,

    /// Ver eventos de vida (casamento, divórcio, imigração)?
    pub show_life_events: bool,

    /// Ver households (agregados familiares)?
    pub show_households: bool,

    /// Ver eco-map?
    pub show_ecomap: bool,

    /// Ver fotos?
    pub show_photos: bool,

    // ─── O que este colaborador PODE FAZER ───
    pub can_edit: bool,
    pub can_add_person: bool,
    pub can_add_relationship: bool,
    pub can_delete: bool,
    pub can_invite: bool,
    pub can_export: bool,
    pub can_share: bool,
}
```

### 3.1. Lógica de filtering (no motor Rust)

```rust
/// Aplica o PermissionSet ao genograma ANTES de serializar para o dispositivo.
/// O resultado é um Genogram "filtrado": os dados restritos simplesmente não existem.
pub fn filter_genogram(genogram: &Genogram, perms: &PermissionSet) -> Genogram {
    let mut filtered = genogram.clone();

    // 1. Filtrar medical_records em cada pessoa
    for person in &mut filtered.persons {
        if !perms.medical_categories.is_empty() {
            person
                .medical_records
                .retain(|mr| perms.medical_categories.contains(&mr.category));
        }

        // 2. Filtrar substâncias
        if !perms.show_substance {
            person.substance_records.clear();
        }

        // 3. Filtrar genético
        if !perms.show_genetic {
            person.genetic_tests.clear();
        }

        // 4. Filtrar criminal
        if !perms.show_criminal {
            person.criminal_records.clear();
        }

        // 5. Filtrar cultural
        if !perms.show_cultural {
            person.cultural_heritage.clear();
        }

        // 6. Filtrar religião
        if !perms.show_religion {
            person.religion = None;
        }

        // 7. Filtrar social
        if !perms.show_social {
            person.occupation = None;
            person.education = None;
            person.residence = None;
            person.socioeconomic = None;
        }

        // 8. Filtrar notas
        if !perms.show_notes {
            person.notes = None;
        }

        // 9. Filtrar custom fields
        if !perms.show_custom_fields {
            person.custom_fields.clear();
        }

        // 10. Filtrar life events
        if !perms.show_life_events {
            person.life_events.clear();
        }

        // 11. Filtrar fotos
        if !perms.show_photos {
            person.photo_ref = None;
        }
    }

    // 12. Filtrar relationships
    if !perms.show_emotional {
        filtered
            .relationships
            .retain(|r| r.category != RelationshipCategory::Emotional);
    }
    if !perms.show_notes {
        for rel in &mut filtered.relationships {
            rel.notes = None;
        }
    }

    // 13. Filtrar households
    if !perms.show_households {
        filtered.households.clear();
    }

    // 14. Filtrar eco-map
    if !perms.show_ecomap {
        // filtered.eco_maps.clear();
    }

    filtered
}
```

Ponto crucial: esta função é chamada no servidor/NAS (ou no dispositivo do owner) antes de enviar os deltas CRDT para o dispositivo do colaborador. O colaborador nunca recebe os dados filtrados.

---

## 4. RBAC matrix (resumo visual)

| Campo | Owner | Editor | Viewer | Specialist (ex.: cardio) | Auditor |
|---|---|---|---|---|---|
| Demographics (nome, idade, género) | ✓ | ✓ | ✓ | ✓ | ✗ |
| Medical — Cardiovascular | ✓ | ✓ | ✓ | ✓ | ✗ |
| Medical — Mental | ✓ | ✓ | ✓ | ✗ | ✗ |
| Medical — Oncológico | ✓ | ✓ | ✓ | ✗ | ✗ |
| Medical — Metabólico | ✓ | ✓ | ✓ | ✓ | ✗ |
| Substâncias | ✓ | ✓ | ✓ | ✓ (config.) | ✗ |
| Genético | ✓ | ✓ | ✓ | ✗ (config.) | ✗ |
| Criminal | ✓ | ✓ | ✓ | ✗ | ✗ |
| Dinâmicas emocionais | ✓ | ✓ | ✓ | ✗ | ✗ |
| Social (ocupação, educação) | ✓ | ✓ | ✓ | ✗ | ✗ |
| Cultural | ✓ | ✓ | ✓ | ✗ | ✗ |
| Religião | ✓ | ✓ | ✓ | ✗ | ✗ |
| Life events | ✓ | ✓ | ✓ | ✗ | ✗ |
| Households | ✓ | ✓ | ✓ | ✗ | ✗ |
| Eco-map | ✓ | ✓ | ✓ | ✗ | ✗ |
| Notas | ✓ | ✓ | ✓ | ✗ | ✗ |
| Fotos | ✓ | ✓ | ✓ | ✗ | ✗ |
| Custom fields | ✓ | ✓ | ✓ | ✗ | ✗ |
| Editar | ✓ | ✓ | ✗ | Config. | ✗ |
| Apagar | ✓ | ✗ | ✗ | ✗ | ✗ |
| Convidar | ✓ | ✗ | ✗ | ✗ | ✗ |
| Exportar | ✓ | ✓ | ✓ (limitado) | Config. | ✓ (audit) |
| Audit log | ✓ | ✗ | ✗ | ✗ | ✓ |

---

## 5. Consentimento individual (dados genéticos)

### 5.1. Regra

| Dado | Regra | Base legal |
|---|---|---|
| Teste genético do doente (pessoa índice) | Consentimento do doente | RGPD Art. 9.2.a |
| Teste genético de familiar (ex.: irmão) | Consentimento do familiar | DL 131/2014, Art. 14 |
| Variante genética herdada (inferida) | Consentimento do titular da variante | DL 131/2014, Art. 14.2 |

### 5.2. Implementação no schema

```rust
pub struct GeneticTest {
    // ... (ver 01-schema.md §3.5)
    pub consent_given: bool,
    pub consent_date: Option<NaiveDate>,
    pub consent_scope: ConsentScope, // Quem autorizou o quê
}

pub enum ConsentScope {
    /// O doente autorizou que o seu teste seja visível a TODOS os colaboradores.
    AllCollaborators,

    /// O doente autorizou que o seu teste seja visível apenas a roles específicos.
    SpecificRoles(Vec<CollaboratorRole>),

    /// O doente autorizou que o seu teste seja visível apenas a utilizadores específicos.
    SpecificUsers(Vec<Uuid>),

    /// O doente NÃO autorizou. O teste é visível apenas ao owner.
    OwnerOnly,
}
```

### 5.3. Lógica de filtering genético

Quando filtrar `genetic_tests` para um colaborador:

1. Se `consent_scope == AllCollaborators` → visível a todos, se `show_genetic == true`.
2. Se `consent_scope == SpecificRoles` → visível se o role do colaborador estiver na lista.
3. Se `consent_scope == SpecificUsers` → visível se o `user_id` do colaborador estiver na lista.
4. Se `consent_scope == OwnerOnly` → visível apenas ao owner.
5. Se `consent_given == false` → visível apenas ao owner.

---

## 6. Consentimento de terceiros (pessoas no genograma que não são doentes)

### 6.1. Problema

Um genograma inclui familiares (pais, irmãos, filhos) que não são doentes e que não deram consentimento explícito para o tratamento dos seus dados.

### 6.2. Regra

| Pessoa no genograma | Consentimento necessário | Visibilidade |
|---|---|---|
| Pessoa índice (doente) | Consentimento do doente | Consoante RBAC |
| Familiar (ex.: pai do doente) | Nenhum (dado incidental, necessário para o contexto clínico) | Consoante RBAC, mas nunca exportado para FHIR sem anonimização |
| Familiar com dados genéticos | Consentimento do familiar | Restrito |
| Familiar com dados criminais | Consentimento do familiar ou relevância clínica documentada | Restrito (só owner + specialist com `show_criminal`) |

### 6.3. Implementação

```rust
pub struct Person {
    // ... (ver 01-schema.md §3)
    pub consent_status: ConsentStatus,
}

pub enum ConsentStatus {
    /// Pessoa índice (doente) — deu consentimento.
    Patient,

    /// Familiar — dado incidental. Não deu consentimento explícito,
    /// mas é necessário para o contexto clínico.
    Incidental,

    /// Familiar — deu consentimento explícito para aparecer no genograma.
    Explicit,

    /// Familiar — NÃO consentiu. Os seus dados são visíveis apenas ao owner.
    /// (Ex.: irmão que não quer ser incluído.)
    Refused,
}
```

### 6.4. Lógica de filtering por `ConsentStatus`

Quando filtrar uma `Person` para um colaborador que não é owner:

1. Se `consent_status == Patient` → visível, consoante RBAC normal.
2. Se `consent_status == Incidental` → visível, mas:
   - sem `genetic_tests`
   - sem `criminal_records`
   - sem `custom_fields`
   - export FHIR: apenas nome + relação (anónimo)
3. Se `consent_status == Explicit` → visível, consoante RBAC normal.
4. Se `consent_status == Refused` → invisível, exceto para owner.
   - O nó aparece no canvas como `█████` (mascarado).
   - As relações que a ligam continuam visíveis, mas o nome e os dados estão ocultos.

---

## 7. Audit Log

### 7.1. Schema

```rust
pub struct AuditEntry {
    pub id: Uuid,
    pub genogram_id: Uuid,
    pub user_id: Uuid,
    pub action: AuditAction,
    pub target_type: AuditTarget, // "person", "relationship", "genogram", "collaborator"
    pub target_id: Uuid,
    pub timestamp: i64,
    pub device_fingerprint: String,
    pub ip_address: Option<String>, // Se via NAS
    pub details: Option<String>,   // JSON com detalhes (ex.: "export PNG")
}

pub enum AuditAction {
    View,
    Edit,
    Add,
    Delete,
    Export,
    Share,
    Revoke,
    Login,
    Logout,
    Sync,
    PermissionChange,
}

pub enum AuditTarget {
    Genogram,
    Person,
    Relationship,
    Collaborator,
    GenogramSettings,
}
```

### 7.2. Regras

| Regra | Detalhe |
|---|---|
| Imutabilidade | O audit log não pode ser editado nem apagado; apenas append. |
| Retenção | Mínimo 5 anos (RGPD Art. 30). Configuração pelo admin. |
| Acesso | Apenas owner e auditor. |
| Export | CSV ou JSON. Nunca inclui conteúdo clínico; apenas metadados. |
| Storage | No NAS (SQLite, tabela separada). E2E encryptado. |

---

## 8. Gestão de convites

### 8.1. Fluxo

1. O owner clica em “Convidar” e introduz o email/ID do colaborador.
2. O motor gera:
   a. um `Collaborator` record (role + permissions)
   b. uma chave de partilha, derivada da master key do owner + `user_id` do colaborador + um nonce
3. A chave é enviada ao colaborador por email, QR code ou link.
   - O email/link contém apenas a chave cifrada.
   - O servidor de email não vê o conteúdo do genograma.
4. O colaborador abre a app, introduz a chave e realiza o handshake E2E.
5. A partir daqui, os deltas CRDT são sincronizados, filtrados por RBAC.

### 8.2. Revogação

1. Owner clica “Remover colaborador”.
2. O `Collaborator.revoked_at` é definido.
3. No próximo sync, o dispositivo do colaborador:
   a. recebe um “revoke delta” (CRDT)
   b. apaga todos os dados do genograma do armazenamento local
   c. invalida a chave de partilha
4. O audit log regista a revogação.

### 8.3. Expiração

O admin pode definir:

- `expires_at: Option<i64>` em `Collaborator`
- se `expires_at < now`, o acesso é revogado automaticamente
- default: `null` (sem expiração)

---

## 9. Configuração por hospital (admin)

### 9.1. O que o admin define

| Configuração | Exemplo |
|---|---|
| Roles custom | Criar role “cardiologista” com `medical_categories: [Cardiovascular, Metabolic]` |
| Permissões por role | Definir o que cada role vê/faz |
| Exceções pontuais | “O Dr. X pode ver o genograma da Doente Y com `show_criminal: true`” |
| Retenção audit log | 5 anos, 10 anos, etc. |
| Expiração default | Convites expiram em 90 dias |
| Export restrictions | “Nenhum colaborador pode exportar GEDCOM” |
| Consent defaults | “Dados genéticos: OwnerOnly por omissão” |

### 9.2. Storage da configuração

```rust
pub struct HospitalConfig {
    pub hospital_id: Uuid,
    pub roles: Vec<RoleDefinition>,      // Roles custom
    pub default_permissions: PermissionSet,
    pub audit_retention_years: u8,
    pub invite_expiry_days: Option<u16>,
    pub export_restrictions: ExportRestrictions,
    pub consent_defaults: ConsentDefaults,
}

pub struct RoleDefinition {
    pub id: Uuid,
    pub name: String,
    pub description: Option<String>,
    pub permissions: PermissionSet,
    pub is_system: bool, // true = role built-in (owner, editor, viewer)
}

pub struct ExportRestrictions {
    pub allow_png: bool,
    pub allow_pdf: bool,
    pub allow_svg: bool,
    pub allow_gedcom: bool,
    pub allow_fhir: bool,
    pub allow_by_role: HashMap<CollaboratorRole, Vec<String>>, // Role → formatos permitidos
}

pub struct ConsentDefaults {
    pub genetic_default: ConsentScope, // Default: OwnerOnly
    pub criminal_default: bool,        // Default: false (não visível)
    pub emotional_default: bool,      // Default: false
}
```

---

## 10. Edge cases

| # | Cenário | Comportamento |
|---|---|---|
| E01 | Colaborador `specialist` com `medical_categories: []` | `[]` significa “tudo”. Se o admin quer “nada”, usar uma flag explícita como `show_all_medical: false`. |
| E02 | Owner revoga a si próprio | Não é possível. O owner não pode ser removido. Para transferir ownership, usar “Transfer ownership”. |
| E03 | Dois colaboradores com roles diferentes editam o mesmo campo | O CRDT resolve (last-writer-wins). O campo é visível para ambos, se ambos tiverem permissão. |
| E04 | Colaborador `viewer` tenta exportar GEDCOM | Bloqueado na UI e validado no motor. O audit log regista a tentativa. |
| E05 | Pessoa com `consent_status: Refused` tem uma relação com a pessoa índice | A relação é visível; o colaborador sabe que existe um familiar, mas o nome e os dados estão mascarados. |
| E06 | Colaborador `specialist` com `show_emotional: false` vê um genograma com 50 relações emocionais | As 50 relações emocionais não existem no seu dispositivo; o canvas mostra apenas as restantes. |
| E07 | Owner muda as permissões de um colaborador | No próximo sync, o dispositivo do colaborador atualiza (adiciona/remove dados). Se o dado foi removido, é apagado localmente. |
| E08 | Colaborador `auditor` tenta ver o genograma | Vê apenas metadados: “Genograma X, 45 pessoas, 120 relações, último acesso: 2026-11-04 por Dr. Y”. Não vê nomes, condições nem conteúdo clínico. |
| E09 | Dois hospitais partilham um genograma | O hospital A (owner) partilha com o hospital B. O hospital B recebe uma cópia filtrada. Não pode re-partilhar sem autorização. |
| E10 | Colaborador perde o dispositivo | O owner revoga o acesso. O audit log regista. Se houver backup E2E do colaborador, os dados ficam no backup (controlado pelo colaborador). |

---

## 11. Regras de implementação (para a IA)

- Nunca aplicar RBAC na UI (frontend). O filtering é sempre no motor (Rust). A UI apenas mostra o que recebeu.
- Nunca enviar dados filtrados para o dispositivo e “esconder” na UI. Se o colaborador não tem permissão, o dado não existe no dispositivo.
- Sempre validar no motor: se o colaborador tentar aceder a um campo que não tem permissão, devolver `403 Forbidden` e registar no audit.
- Sempre registar no audit: `View`, `Export`, `Edit`, `Share`, `Revoke`.
- Nunca permitir que um `specialist` veja `genetic_tests` sem `consent_given: true` e `consent_scope` que o inclua.
- Sempre mascarar (não apagar) pessoas com `consent_status: Refused`. O nó existe, mas os dados estão ocultos.
- Nunca permitir export FHIR de: `EcoMap`, `CriminalRecord`, `Relationship` da categoria `Emotional`, `GeneticTest` sem consentimento.
- Sempre aplicar `HospitalConfig.export_restrictions` antes de qualquer exportação.
- Nunca permitir que um `viewer` ou `specialist` altere `Collaborator.permissions`.
- Sempre verificar `expires_at` no handshake. Se expirado, recusar e registar no audit.

---

## 12. Notas sobre este documento

| Aspeto | Detalhe |
|---|---|
| Tamanho | Documento central para RBAC e permissões. |
| Atualização | Só mudar com ADR ou decisão documentada. |
| Validação | O motor deve aplicar o filtro antes de qualquer serialização e antes de qualquer export. |
| Para a IA | Colar apenas a secção relevante (ex.: §3) no prompt para reduzir tokens. |
| Para clínicos | A parte mais sensível é a consent/filtragem genética. |
