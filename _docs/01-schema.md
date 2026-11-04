# Genogram — Data Schema (v1.0)

> **Este é o documento mais importante do projeto.**
> Todo o código (Rust, JS, AI prompts, GEDCOM mapping, FHIR mapping)
> deriva deste schema. Se um campo não está aqui, NÃO existe.
> Se a IA vai "inventar" um campo, este documento é a referência.

---

## 1. Convenções Gerais

| Regra | Detalhe |
|---|---|
| IDs | UUID v7 (temporais, ordenáveis). Nunca auto-increment. |
| Datas | ISO 8601 (`YYYY-MM-DD`). `null` = desconhecido. Nunca `0000-00-00`. |
| Timestamps | Unix epoch (segundos, `i64`). |
| Strings | `String` em Rust, `string` em JSON. Máx 500 chars (a menos que indicado). |
| Enums | `snake_case` em Rust e JSON. Nunca inteiros. |
| Opcionais | `Option<T>` em Rust, `nullable` em JSON Schema. `null` ≠ vazio. |
| Arrays | `Vec<T>` em Rust, `array` em JSON. `[]` = vazio (diferente de `null`). |
| Versioning | Cada entidade tem `schema_version: u32`. Migrações em `src/migrations/`. |
| Serde | Todas as structs: `#[derive(Serialize, Deserialize, Clone, Debug)]` |

---

## 2. Entidade Raiz: `Genogram`

```rust
pub struct Genogram {
    pub id: Uuid,                    // UUID v7
    pub schema_version: u32,         // = 1
    pub name: String,                // "Família Silva"
    pub description: Option<String>, // "Genograma clínico — ULS de Leiria"
    pub owner_id: Uuid,              // Quem criou (pessoa no sistema)
    pub created_at: i64,             // Unix timestamp
    pub updated_at: i64,             // Unix timestamp
    pub revision: u64,               // Incrementa a cada mudança (optimistic locking)

    pub persons: Vec<Person>,        // 1..N
    pub relationships: Vec<Relationship>,  // 0..N
    pub households: Vec<Household>,  // 0..N
    pub collaborators: Vec<Collaborator>,  // 0..N

    pub metadata: GenogramMetadata,
    pub settings: GenogramSettings,
}

pub struct GenogramMetadata {
    pub language: String,            // "pt-PT", "en-GB", "es-ES" (BCP 47)
    pub template: Option<String>,    // "3gen", "couples", "blended_family"
    pub tags: Vec<String>,           // ["oncologia", "urgência"]
    pub source: Option<String>,      // "manual", "gedcom_import", "ai_generated"
    pub source_ref: Option<String>,  // Ficheiro original (se import)
    pub notes: Option<String>,       // Notas gerais do genograma
}

pub struct GenogramSettings {
    pub default_generation_count: u8,  // 3 (padrão)
    pub max_generation_count: u8,      // 10
    pub show_households: bool,         // true
    pub show_ecomap: bool,             // false
    pub color_scheme: ColorScheme,     // "default", "medical", "high_contrast"
}

pub enum ColorScheme {
    Default,
    Medical,
    HighContrast,
}

JSON Schema (resumo):

{
  "type": "object",
  "required": ["id", "schema_version", "name", "owner_id", "created_at", "updated_at", "revision", "persons", "relationships", "metadata", "settings"],
  "properties": {
    "id": { "type": "string", "format": "uuid" },
    "schema_version": { "type": "integer", "minimum": 1 },
    "name": { "type": "string", "maxLength": 200 },
    "description": { "type": ["string", "null"], "maxLength": 1000 },
    "owner_id": { "type": "string", "format": "uuid" },
    "created_at": { "type": "integer" },
    "updated_at": { "type": "integer" },
    "revision": { "type": "integer", "minimum": 0 },
    "persons": { "type": "array", "minItems": 1, "items": { "$ref": "#/definitions/Person" } },
    "relationships": { "type": "array", "items": { "$ref": "#/definitions/Relationship" } },
    "households": { "type": "array", "items": { "$ref": "#/definitions/Household" } },
    "collaborators": { "type": "array", "items": { "$ref": "#/definitions/Collaborator" } },
    "metadata": { "$ref": "#/definitions/GenogramMetadata" },
    "settings": { "$ref": "#/definitions/GenogramSettings" }
  }
}

3. Entidade: Person
pub struct Person {
    pub id: Uuid,
    pub genogram_id: Uuid,           // FK → Genogram.id
    pub schema_version: u32,

    // ─── Identidade ───
    pub name: String,                // "Maria Silva"
    pub alias: Option<String>,       // "Marina" (apelido)
    pub gender: Gender,
    pub birth_date: Option<NaiveDate>,
    pub death_date: Option<NaiveDate>,
    pub death_cause: Option<String>, // "Neoplasia pulmonar"
    pub age_at_death: Option<u8>,    // 72
    pub is_index: bool,              // true = pessoa índice (proband)
    pub is_pet: bool,                // true = animal de estimação
    pub is_coparent: bool,           // true = coparente (fora do par)
    pub is_deceased: bool,           // Derived: death_date.is_some()
    pub is_pregnant: bool,           // Gravidez atual
    pub pregnancy_weeks: Option<u8>, // 28 (se is_pregnant)

    // ─── Posição no genograma ───
    pub generation: u8,              // 1 = mais antigo, 2 = seguinte, etc.
    pub position: u8,                // Ordem dentro da geração (1 = esquerda)
    pub x: f64,                      // Posição no canvas (0-1000, normalizado)
    pub y: f64,                      // Posição no canvas (0-1000, normalizado)

    // ─── Demographics ───
    pub nationality: Option<String>,
    pub residence: Option<String>,
    pub education: Option<EducationLevel>,
    pub occupation: Option<String>,
    pub socioeconomic: Option<SocioeconomicLevel>,

    // ─── Cultura & Religião ───
    pub cultural_heritage: Vec<CulturalPattern>,  // 0..N (múltiplas heranças)
    pub religion: Option<Religion>,

    // ─── Saúde ───
    pub medical_records: Vec<MedicalRecord>,   // 0..N
    pub substance_records: Vec<SubstanceRecord>, // 0..N
    pub genetic_tests: Vec<GeneticTest>,       // 0..N

    // ─── Criminal ───
    pub criminal_records: Vec<CriminalRecord>, // 0..N

    // ─── Eventos de vida ───
    pub life_events: Vec<LifeEvent>,           // 0..N

    // ─── Campos custom ───
    pub custom_fields: Vec<CustomField>,       // 0..N

    // ─── Meta ───
    pub notes: Option<String>,                 // Notas livres (máx 5000)
    pub photo_ref: Option<String>,             // Path/ID da foto (se exist
ir)
    pub created_at: i64,
    pub updated_at: i64,
}

3.1. Enums de Person
pub enum Gender {
    Male,
    Female,
    NonBinary,
    Intersex,
    Unknown,
}

pub enum EducationLevel {
    None,
    Primary,
    Secondary,
    HighSchool,
    Vocational,
    Bachelor,
    Master,
    Doctorate,
    PostDoc,
}

pub enum SocioeconomicLevel {
    Poverty,        // $
    Working,        // $$
    Middle,         // $$$
    UpperMiddle,    // $$$$
    Upper,          // $$$$$
}

pub enum Religion {
    Christianity,
    Catholicism,
    Protestantism,
    Orthodoxy,
    Islam,
    Judaism,
    Hinduism,
    Buddhism,
    Sikhism,
    Taoism,
    Shinto,
    Other,          // + field `religion_other: Option<String>`
    None,           // Ateu / agnóstico
    Unknown,
}

3.2. CulturalPattern
pub struct CulturalPattern {
    pub pattern: CulturalPatternType,  // O padrão visual
    pub label: Option<String>,         // "Portuguesa", "Brasileira"
}

pub enum CulturalPatternType {
    Solid,           // Preenchido a preto
    Horizontal,      // Linhas horizontais
    Vertical,        // Linhas verticais
    DiagonalRight,   // Linhas diagonais ↗
    DiagonalLeft,    // Linhas diagonais ↘
    Grid,            // Grade (cruzamento)
    Dots,            // Pontos
    Waves,           // Ondas
    Braid,           // Trança
    Crosshatch,      // Hachura cruzada
    Checkerboard,    // Xadrez
}

3.3. MedicalRecord
pub struct MedicalRecord {
    pub id: Uuid,
    pub person_id: Uuid,               // FK → Person.id
    pub category: MedicalCategory,
    pub condition: String,             // "Hipertensão arterial"
    pub icd10_code: Option<String>,    // "I10"
    pub snomed_ct: Option<String>,     // "38341003"
    pub status: MedicalStatus,
    pub onset_date: Option<NaiveDate>,
    pub resolution_date: Option<NaiveDate>,
    pub quadrant: Quadrant,            // Posição no símbolo
    pub severity: Option<u8>,          // 1-5 (se aplicável)
    pub notes: Option<String>,         // "Diagnosticada em 2019"
    pub source: Option<String>,        // "anamnese", "histórico", "teste"
    pub created_at: i64,
    pub updated_at: i64,
}

pub enum MedicalCategory {
    // Cardiovascular (quadrante superior esquerdo)
    Cardiovascular,
    Hypertension,
    Stroke,
    Arrhythmia,
    HeartFailure,
    PeripheralVascular,

    // Mental / Neurológico (quadrante superior direito)
    Depression,
    Anxiety,
    Bipolar,
    Schizophrenia,
    Psychosis,
    Dementia,
    Alzheimer,
    Parkinson,
    Epilepsy,
    Adhd,
    Autism,
    StrokeNeuro,  // AVC (neurológico)
    OtherNeuro,

    // Oncológico (quadrante inferior esquerdo)
    CancerBreast,
    CancerColon,
    CancerLung,
    CancerProstate,
    CancerOvary,
    CancerLeukemia,
    CancerLymphoma,
    CancerSkin,
    CancerOther,

    // Metabólico / Endócrino (quadrante inferior direito)
    DiabetesType1,
    DiabetesType2,
    DiabetesGestational,
    Thyroid,
    Obesity,
    Asthma,
    Allergy,
    Autoimmune,

    // Outros
    Respiratory,
    Musculoskeletal,
    Renal,
    Hepatic,
    Infectious,
    Genetic,          // Doença genética específica
    Other,
}

pub enum MedicalStatus {
    Active,           // Condição atual
    Suspected,        // Suspeita (não confirmada)
    Remission,        // Em remissão
    Recovered,        // Recuperado
    Deceased,         // Faleceu desta condição
}

pub enum Quadrant {
    TopLeft,          // Cardiovascular
    TopRight,         // Mental/Neurológico
    BottomLeft,       // Oncológico
    BottomRight,      // Metabólico/Endócrino
    Center,           // Múltiplo / Outro
}

3.4. SubstanceRecord
pub struct SubstanceRecord {
    pub id: Uuid,
    pub person_id: Uuid,
    pub substance: SubstanceType,
    pub status: SubstanceStatus,
    pub since: Option<NaiveDate>,
    pub until: Option<NaiveDate>,      // Se "former"
    pub frequency: Option<SubstanceFrequency>,
    pub notes: Option<String>,
    pub created_at: i64,
    pub updated_at: i64,
}

pub enum SubstanceType {
    Alcohol,
    Tobacco,
    Cannabis,
    Cocaine,
    Opioids,
    Stimulants,
    Sedatives,
    Hallucinogens,
    Other,        // + `substance_other: Option<String>`
}

pub enum SubstanceStatus {
    Active,
    Former,
    Suspected,
    InTreatment,
}

pub enum SubstanceFrequency {
    Daily,
    Weekly,
    Monthly,
    Occasional,
    Unknown,
}

3.5. GeneticTest
pub struct GeneticTest {
    pub id: Uuid,
    pub person_id: Uuid,
    pub test_name: String,             // "BRCA1/2", "Panel hereditário"
    pub lab: Option<String>,           // "Laboratório X"
    pub date: Option<NaiveDate>,
    pub result: GeneticTestResult,
    pub variants: Vec<GeneticVariant>, // 0..N
    pub consent_given: bool,           // O doente consentiu?
    pub consent_date: Option<NaiveDate>,
    pub notes: Option<String>,
    pub created_at: i64,
    pub updated_at: i64,
}

pub enum GeneticTestResult {
    Positive,
    Negative,
    VUS,            // Variant of Uncertain Significance
    Inconclusive,
    Pending,
}

pub struct GeneticVariant {
    pub gene: String,                  // "BRCA1"
    pub variant: String,               // "c.68_69delAG"
    pub classification: VariantClassification,
    pub inherited_from: Option<Uuid>,  // FK → Person.id (de quem herdou)
}

pub enum VariantClassification {
    Pathogenic,
    LikelyPathogenic,
    VUS,
    LikelyBenign,
    Benign,
}

3.6. CriminalRecord
pub struct CriminalRecord {
    pub id: Uuid,
    pub person_id: Uuid,
    pub offense: String,               // "Homicídio"
    pub year: Option<i16>,
    pub status: CriminalStatus,
    pub notes: Option<String>,
    pub created_at: i64,
    pub updated_at: i64,
}

pub enum CriminalStatus {
    Convicted,
    Acquitted,
    Pending,
    Pardon,
}

3.7. LifeEvent
pub struct LifeEvent {
    pub id: Uuid,
    pub person_id: Uuid,
    pub event_type: LifeEventType,
    pub date: Option<NaiveDate>,
    pub related_person_id: Option<Uuid>, // FK → Person.id (se aplicável)
    pub notes: Option<String>,
    pub created_at: i64,
}

pub enum LifeEventType {
    Birth,
    Marriage,
    Divorce,
    Separation,
    CohabitationStart,
    Adoption,
    FosterPlacement,
    FosterDisruption,
    Immigration,
    Emigration,
    JobChange,
    EducationStart,
    EducationEnd,
    MilitaryService,
    Incarceration,
    Death,
    Other,  // + `event_type_other: Option<String>`
}

3.8. CustomField
pub struct CustomField {
    pub key: String,                   // "almofada_social"
    pub value: String,                 // "Rede forte"
    pub field_type: CustomFieldType,
    pub group: Option<String>,         // "Social", "Ocupacional"
    pub order: u8,                     // Ordem de exibição
}

pub enum CustomFieldType {
    Text,
    Number,
    Date,
    Boolean,
    Enum,        // + `enum_options: Vec<String>`
}

4. Entidade: Relationship
pub struct Relationship {
    pub id: Uuid,
    pub genogram_id: Uuid,
    pub schema_version: u32,

    pub source_id: Uuid,               // FK → Person.id
    pub target_id: Uuid,               // FK → Person.id
    pub type: RelationshipType,        // 71+ tipos (ver §4.1)
    pub category: RelationshipCategory,

    // ─── Temporal ───
    pub since: Option<NaiveDate>,
    pub until: Option<NaiveDate>,
    pub is_active: bool,               // Derived: until.is_none()

    // ─── Atributos ───
    pub intensity: Option<u8>,         // 1-5 (para emocionais)
    pub reconciled: Option<bool>,      // Se "cutoff" — foi reparado?
    pub notes: Option<String>,

    // ─── Meta ───
    pub created_at: i64,
    pub updated_at: i64,
}

pub enum RelationshipCategory {
    Partner,        // 18 tipos
    ParentChild,    // 7 tipos
    Emotional,      // 39 tipos
    Other,          // 7 tipos (abuse, spiritual, etc.)
}

4.1. RelationshipType (71+ tipos)
pub enum RelationshipType {
    // ─── PARTNER (18) ───
    Married,                  // Sólida
    CivilUnion,               // Sólida + anel
    Cohabiting,               // Tracejada
    Dating,                   // Pontilhada
    Engaged,                  // Pontilhada + anel
    Separated,                // Sólida + 1 barra
    Divorced,                 // Sólida + 2 barras
    Widowed,                  // (derivado: parceiro falecido)
    Affair,                   // Pontilhada + cruz
    OnOff,                    // Ondulada
    LongDistance,             // Tracejada + pontos
    Polyamorous,              // Sólida + 3+
    CommonLaw,                // Sólida (variação)
    DomesticPartnership,      // Sólida + coração
    SeparatedReconciled,      // Sólida + seta reconexão
    DivorcedReconciled,       // Sólida + seta reconexão
    PartnerUnknown,           // Pontilhada + "?"
    PartnerDeceased,          // (derivado)

    // ─── PARENT-CHILD (7) ───
    Biological,               // Sólida
    Adopted,                  // Tracejada
    Foster,                   // Pontilhada
    Step,                     // Sólida + pontilhada
    TwinIdentical,            // 2 linhas + barra topo
    TwinFraternal,            // 2 linhas
    Coparent,                 // Retângulo arredondado

    // ─── EMOTIONAL (39) ───
    // Próximo / Harmonioso
    Close,                    // 2 linhas paralelas
    VeryClose,                // 3 linhas paralelas
    Fused,                    // 3 linhas (enmeshed)
    Enmeshed,                 // 3 linhas + ondas
    Harmonious,               // 2 linhas + coração

    // Distante / Frio
    Distant,                  // Tracejada
    VeryDistant,              // 2 tracejadas
    Cold,                     // Tracejada + "X"
    Estranged,                // 2 tracejadas + "X"
    Disconnected,             // Linha cortada

    // Conflito
    Conflict,                 // 2 tracejadas (zigzag)
    SevereConflict,           // 2 zigzag
    Hostile,                  // Zigzag
    VeryHostile,              // 2 zigzag (denso)
    Abusive,                  // Zigzag + "!"
    VerbalAbuse,              // Zigzag + "V"
    PhysicalAbuse,            // Zigzag + "P"
    SexualAbuse,              // Zigzag + "S"
    EmotionalAbuse,           // Zigzag + "E"
    Neglect,                  // Linha cortada + "N"

    // Cutoff / Ruptura
    Cutoff,                   // 2 barras (sem contacto)
    CutoffRepaired,           // 2 barras + círculo
    Ambivalent,               // 2 linhas + zigzag
    Conflictual,              // 2 linhas + zigzag (próximo E hostil)
    FusedHostile,             // 3 linhas + zigzag

    // Cuidado / Depender
    Caregiver,                // 2 setas abertas
    Dependent,                // Seta preenchida (depende de)
    Codependent,              // 2 setas + ondas
    Rescuer,                  // Seta + "R"
    Martyr,                   // Seta + "M"

    // Outros emocionais
    Secret,                   // Triângulo pequeno preenchido
    Gossip,                   // Linhas curtas + "G"
    Triangulation,            // 3 linhas formando triângulo
    Scapegoat,                // Seta + "X"
    GoldenChild,              // Seta + estrela
    Enabler,                  // Seta + "E" (diferente de abuse)
    Spiritual,                // Linha ondulada + "✝"
    Mentor,                   // Seta + "M"
    Rival,                    // 2 setas opostas
    Jealous,                  // Seta + "J"

    // ─── OUTROS (7) ───
    AbuseUnknown,             // Zigzag + "?"
    SpiritualBond,            // Ondulada + símbolo religioso
    Friendship,               // Sólida + "F"
    Professional,             // Sólida + "P"
    Neighbor,                 // Pontilhada + "N"
    Other,                    // Custom
    Unknown,                  // "?"
}

4.2. Mapeamento Visual (para o renderer)
Cada RelationshipType tem uma definição visual:

pub struct RelationshipVisual {
    pub line_style: LineStyle,
    pub line_count: u8,
    pub color: Option<String>,       // Hex (se null = preto)
    pub markers: Vec<LineMarker>,    // Símbolos na linha
    pub thickness: f32,              // 1.0 = normal
}

pub enum LineStyle {
    Solid,
    Dashed,
    Dotted,
    DashDot,
    Wavy,
    ZigZag,
}

pub enum LineMarker {
    Cross,          // X
    DoubleBar,      // ||
    SingleBar,      // |
    Arrow,          // →
    OpenArrow,      // → (aberta)
    Heart,          // ♥
    Circle,         // ○
    Triangle,       // △
    Exclamation,    // !
    QuestionMark,   // ?
    Star,           // ★
    Letter(String), // V, P, S, E, R, M, G, N, F, J
}

5. Entidade: Household
pub struct Household {
    pub id: Uuid,
    pub genogram_id: Uuid,
    pub name: Option<String>,        // "Morada A"
    pub members: Vec<Uuid>,          // FK → Person.id (1..N)
    pub location: Option<String>,    // "Lisboa"
    pub since: Option<NaiveDate>,
    pub until: Option<NaiveDate>,    // Se o agregado terminou
    pub notes: Option<String>,
    pub created_at: i64,
    pub updated_at: i64,
}

6. Entidade: Collaborator
pub struct Collaborator {
    pub id: Uuid,
    pub genogram_id: Uuid,
    pub user_id: Uuid,               // ID do utilizador no sistema
    pub role: CollaboratorRole,
    pub permissions: PermissionSet,  // O que este colaborador vê
    pub added_by: Uuid,              // FK → Person.id (quem convidou)
    pub added_at: i64,
    pub revoked_at: Option<i64>,     // Se foi removido
    pub last_accessed_at: Option<i64>,
    pub device_fingerprint: Option<String>, // Para audit
}

pub enum CollaboratorRole {
    Owner,      // Tudo (criador)
    Editor,     // Tudo exceto delete/invite
    Viewer,     // Read-only
    Specialist, // Filtrado (ver PermissionSet)
    Auditor,    // Metadados apenas (quem acessou, quando)
}

pub struct PermissionSet {
    // O que este colaborador PODE VER:
    pub medical_categories: Vec<MedicalCategory>,  // [] = tudo, [Cardiovascular] = só cardio
    pub show_emotional: bool,
    pub show_social: bool,
    pub show_cultural: bool,
    pub show_religion: bool,
    pub show_criminal: bool,
    pub show_genetic: bool,
    pub show_substance: bool,
    pub show_custom_fields: bool,
    pub show_notes: bool,

    // O que este colaborador PODE FAZER:
    pub can_edit: bool,
    pub can_add_person: bool,
    pub can_add_relationship: bool,
    pub can_delete: bool,
    pub can_invite: bool,
    pub can_export: bool,
    pub can_share: bool,
}

6.1. RBAC Filtering Logic (no motor)
QUANDO sincronizar com um Collaborator:

1. Se role == Owner → enviar TUDO
2. Se role == Editor → enviar TUDO (mas can_delete = false na UI)
3. Se role == Viewer → enviar TUDO (read-only na UI)
4. Se role == Specialist:
   a. medical_records: filtrar por medical_categories
   b. Se show_emotional == false → remover relationships da categoria Emotional
   c. Se show_criminal == false → remover criminal_records
   d. Se show_genetic == false → remover genetic_tests
   e. Se show_substance == false → remover substance_records
   f. (idem para os restantes flags)
   → O dado filtrado NUNCA é enviado para o dispositivo.

7. Eco-Map (Fase 9 — mas já no schema)
pub struct EcoMap {
    pub id: Uuid,
    pub genogram_id: Uuid,
    pub center_person_id: Uuid,      // A pessoa no centro
    pub nodes: Vec<EcoMapNode>,
    pub connections: Vec<EcoMapConnection>,
    pub created_at: i64,
    pub updated_at: i64,
}

pub struct EcoMapNode {
    pub id: Uuid,
    pub label: String,               // "Trabalho", "Escola", "Amigos"
    pub category: EcoMapCategory,
    pub x: f64,
    pub y: f64,
    pub notes: Option<String>,
}

pub enum EcoMapCategory {
    Family,
    Friends,
    Work,
    School,
    Health,
    Community,
    Religion,
    Legal,
    Other,
}

pub struct EcoMapConnection {
    pub id: Uuid,
    pub source_node_id: Uuid,
    pub target_node_id: Uuid,
    pub strength: EcoMapStrength,
    pub direction: EcoMapDirection,
    pub notes: Option<String>,
}

pub enum EcoMapStrength {
    Strong,       // 3 linhas
    Moderate,     // 2 linhas
    Weak,         // 1 linha
    Negative,     // Zigzag
    None,         // Linha cortada
}

pub enum EcoMapDirection {
    Bidirectional,  // ↔
    Outward,        // → (dai para o nó)
    Inward,         // ← (do nó para o centro)
    None,           // Sem direção
}

8. GEDCOM Mapping
Campo Genogram	Tag GEDCOM	Notas
Person.name	NAME	
Person.gender	SEX	M/F/U
Person.birth_date	BIRT/DATE	
Person.death_date	DEAT/DATE	
Person.death_cause	DEAT/CAUS	
Person.occupation	OCCU	
Person.residence	RESI/PLAC	
Person.nationality	NATI	
Relationship.Married	HUSB/WIFE (no FAM)	
Relationship.Biological	CHIL (no FAM)	
Relationship.Adopted	CHIL + ADOP	
MedicalRecord	HSP (Health Status Problem)	Extension GEDCOM 5.5.1
LifeEvent.Marriage	MARR/DATE	
LifeEvent.Divorce	DIV/DATE	
LifeEvent.Immigration	IMMI/DATE	
Household	RESI (por família)	
GeneticTest	Sem equivalente	Exportar como NOTE
EcoMap	Sem equivalente	Não exportar para GEDCOM
CriminalRecord	Sem equivalente	Exportar como NOTE
CustomField	_GENO_KEY (custom tag)	Prefixo _ = custom

Regra: O que não tem equivalente GEDCOM é exportado como NOTE com prefixo [Genogram].

9. FHIR Mapping (para SNS/SClínico)
Campo Genogram	FHIR Resource	Field
Person (doente)	Patient	name, gender, birthDate
Person (familiar)	RelatedPerson	name, gender, birthDate
Relationship (familiar)	RelatedPerson	relationship
MedicalRecord	Condition	code (ICD-10/SNOMED), onsetDateTime, clinicalStatus
MedicalRecord (familiar)	FamilyMemberHistory	condition.code, condition.onset
GeneticTest	Observation	code (LOINC), valueCodeableConcept
GeneticVariant	MolecularSequence	elementReference, type
SubstanceRecord	Condition	code (ICD-10: F10-F19)
LifeEvent	Observation	code (LOINC), effectiveDateTime

Regra: Só exportar para FHIR o que é necessário (princípio da minimização). Eco-maps, dinâmicas emocionais, criminal → nunca exportar para FHIR.

10. Validação (Problem Spotter)
O motor valida o genograma contra estas regras:

#	Regra	Severidade
V01	Pessoa sem generation definido	Error
V02	Pessoa com death_date < birth_date	Error
V03	Relação Married entre pessoas da mesma geração (sem flag consanguineous)	Warning
V04	Pessoa com mais de 2 parceiros simultâneos (sem flag polyamorous)	Warning
V05	Filho com generation = generation do pai/mãe	Error
V06	Relação ParentChild entre pessoas da mesma geração	Error
V07	MedicalRecord com onset_date < birth_date	Warning
V08	MedicalRecord com resolution_date < onset_date	Error
V09	Person com is_pregnant = true e gender = Male	Warning
V10	Relationship com source_id == target_id (self-loop)	Error
V11	Relationship duplicada (mesmo source, target, type, período)	Warning
V12	Person com generation = 1 sem pais (OK, mas flag)	Info
V13	GeneticTest com consent_given = false e result = Positive	Error
V14	CriminalRecord com year > ano atual	Warning
V15	Household com 0 membros	Error
V16	Collaborator com role = Specialist e medical_categories = [] (vazio = tudo)	Info
V17	Person com age_at_death inconsistente com birth_date/death_date	Warning
V18	Relationship com until < since	Error
V19	TwinIdentical/TwinFraternal com birth_date diferente	Warning
V20	Person com is_pet = true e medical_records não vazios	Info
V21	Genogram com 0 Relationships (só pessoas soltas)	Warning
V22	Person com name vazio	Error
V23	MedicalRecord com category = Cancer* e status = Active sem onset_date	Warning

11. Versioning e Migrações
Regra	Detalhe
schema_version	Cada entidade tem o seu. O Genogram tem o global.
Migração	src/migrations/v1_to_v2.rs (Rust). Cada migração é uma função: fn migrate(data: &mut Genogram) -> Result<()>
Compatibilidade	Sempre backward-compatible. Um genograma v1 abre numa app v2 sem migração (os campos novos ficam null).
Breaking changes	Só em major version (v2.0). A app deteta a versão e aplica migrações em cascata.
Locking	revision incrementa a cada mudança. Se 2 dispositivos editam o mesmo campo, o CRDT resolve (last-writer-wins para scalars, merge para sets).

12. Limites e Constraints
Campo	Mín	Máx
Genogram.name	1	200 chars
Person.name	1	200 chars
Person.notes	0	5000 chars
MedicalRecord.condition	1	300 chars
MedicalRecord.notes	0	1000 chars
Relationship.notes	0	500 chars
Genogram.persons	1	10000
Genogram.relationships	0	50000
Person.medical_records	0	100
Person.life_events	0	50
Person.custom_fields	0	20
Genogram.collaborators	0	50
Household.members	1	50
Person.generation	1	10
Person.position	1	100
MedicalRecord.severity	1	5
Relationship.intensity	1	5
Genogram.settings.max_generation_count	1	10

13. Índices (SQLite)
CREATE INDEX idx_person_genogram ON person(genogram_id);
CREATE INDEX idx_person_generation ON person(genogram_id, generation, position);
CREATE INDEX idx_rel_genogram ON relationship(genogram_id);
CREATE INDEX idx_rel_source ON relationship(source_id);
CREATE INDEX idx_rel_target ON relationship(target_id);
CREATE INDEX idx_rel_type ON relationship(genogram_id, type);
CREATE INDEX idx_medical_person ON medical_record(person_id);
CREATE INDEX idx_medical_category ON medical_record(person_id, category);
CREATE INDEX idx_household_genogram ON household(genogram_id);
CREATE INDEX idx_collab_genogram ON collaborator(genogram_id);
CREATE INDEX idx_collab_user ON collaborator(user_id);

14. Exemplo JSON (mini genograma)
{
  "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b6",
  "schema_version": 1,
  "name": "Família Silva",
  "description": "Genograma clínico — 3 gerações",
  "owner_id": "0192a000000000000000000000000001",
  "created_at": 1730649600,
  "updated_at": 1730736000,
  "revision": 42,
  "persons": [
    {
      "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
      "genogram_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b6",
      "schema_version": 1,
      "name": "João Silva",
      "alias": null,
      "gender": "male",
      "birth_date": "1945-03-12",
      "death_date": "2010-08-22",
      "death_cause": "Infarto agudo do miocárdio",
      "age_at_death": 65,
      "is_index": false,
      "is_pet": false,
      "is_coparent": false,
      "is_deceased": true,
      "is_pregnant": false,
      "pregnancy_weeks": null,
      "generation": 1,
      "position": 1,
      "x": 200.0,
      "y": 100.0,
      "nationality": "Portugal",
      "residence": "Leiria",
      "education": "secondary",
      "occupation": "Mecânico",
      "socioeconomic": "working",
      "cultural_heritage": [{"pattern": "solid", "label": "Portuguesa"}],
      "religion": "catholicism",
      "medical_records": [
        {
          "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5c1",
          "person_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
          "category": "cardiovascular",
          "condition": "Hipertensão arterial",
          "icd10_code": "I10",
          "snomed_ct": "38341003",
          "status": "active",
          "onset_date": "1985-01-01",
          "resolution_date": null,
          "quadrant": "top_left",
          "severity": 3,
          "notes": "Diagnosticada aos 40 anos",
          "source": "anamnese",
          "created_at": 1730649600,
          "updated_at": 1730649600
        },
        {
          "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5c2",
          "person_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
          "category": "cardiovascular",
          "condition": "Infarto agudo do miocárdio",
          "icd10_code": "I21.9",
          "snomed_ct": null,
          "status": "deceased",
          "onset_date": "2010-08-22",
          "resolution_date": "2010-08-22",
          "quadrant": "top_left",
          "severity": 5,
          "notes": null,
          "source": "histórico",
          "created_at": 1730649600,
          "updated_at": 1730649600
        }
      ],
      "substance_records": [
        {
          "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5d1",
          "person_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
          "substance": "tobacco",
          "status": "former",
          "since": "1965-01-01",
          "until": "2005-01-01",
          "frequency": "daily",
          "notes": "20 cigarros/dia durante 40 anos",
          "created_at": 1730649600,
          "updated_at": 1730649600
        }
      ],
      "genetic_tests": [],
      "criminal_records": [],
      "life_events": [
        {
          "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5e1",
          "person_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
          "event_type": "marriage",
          "date": "1970-06-15",
          "related_person_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b2",
          "notes": null,
          "created_at": 1730649600
        }
      ],
      "custom_fields": [],
      "notes": null,
      "photo_ref": null,
      "created_at": 1730649600,
      "updated_at": 1730649600
    }
  ],
  "relationships": [
    {
      "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a6b1",
      "genogram_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b6",
      "schema_version": 1,
      "source_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
      "target_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b2",
      "type": "married",
      "category": "partner",
      "since": "1970-06-15",
      "until": "2010-08-22",
      "is_active": false,
      "intensity": null,
      "reconciled": null,
      "notes": "40 anos de casamento",
      "created_at": 1730649600,
      "updated_at": 1730649600
    }
  ],
  "households": [
    {
      "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a7b1",
      "genogram_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b6",
      "name": "Casa de Leiria",
      "members": [
        "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b1",
        "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b2"
      ],
      "location": "Leiria",
      "since": "1970-06-15",
      "until": "2010-08-22",
      "notes": null,
      "created_at": 1730649600,
      "updated_at": 1730649600
    }
  ],
  "collaborators": [
    {
      "id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a8b1",
      "genogram_id": "0192a3b4c5d6e7f8a9b0c1d2e3f4a5b6",
      "user_id": "0192a000000000000000000000000002",
      "role": "specialist",
      "permissions": {
        "medical_categories": ["cardiovascular", "metabolic"],
        "show_emotional": false,
        "show_social": false,
        "show_cultural": false,
        "show_religion": false,
        "show_criminal": false,
        "show_genetic": false,
        "show_substance": true,
        "show_custom_fields": false,
        "show_notes": false,
        "can_edit": false,
        "can_add_person": false,
        "can_add_relationship": false,
        "can_delete": false,
        "can_invite": false,
        "can_export": true,
        "can_share": false
      },
      "added_by": "0192a000000000000000000000000001",
      "added_at": 1730649600,
      "revoked_at": null,
      "last_accessed_at": 1730736000,
      "device_fingerprint": "a3f8b2c1"
    }
  ],
  "metadata": {
    "language": "pt-PT",
    "template": "3gen",
    "tags": ["cardiologia", "familiar"],
    "source": "manual",
    "source_ref": null,
    "notes": null
  },
  "settings": {
    "default_generation_count": 3,
    "max_generation_count": 5,
    "show_households": true,
    "show_ecomap": false,
    "color_scheme": "medical"
  }
}

15. Notas para a IA (regras de implementação)
Nunca criar um campo que não está neste schema. Se precisa de algo extra, usar custom_fields.
Nunca usar i32 para IDs — sempre Uuid (UUID v7).
Nunca usar Option<Option<T>> — usar Option<T>.
Sempre incluir created_at e updated_at em entidades com id.
Sempre incluir schema_version: u32 em entidades que podem evoluir.
Nunca armazenar dados em formato denormalizado (ex.: não guardar o nome do pai dentro do filho — usar Relationship).
Sempre validar source_id != target_id em Relationship.
Sempre validar generation do filho = generation do pai/mãe + 1.
Nunca exportar EcoMap, CriminalRecord, ou Relationship (categoria Emotional) para FHIR.
Sempre aplicar RBAC filtering antes de serializar para o dispositivo do colaborador.

---

### Notas sobre este documento

| Aspecto | Detalhe |
|---|---|
| **Tamanho** | ~1500 linhas. É o documento mais extenso. |
| **Atualização** | Só mudar com ADR (decisão documentada). Nunca "no ar". |
| **Validação** | O schema JSON deve ser validado com `ajv` (JS) e `schemars` (Rust) em CI. |
| **Para a IA** | Colar a secção relevante (ex.: "secção 3") no prompt, não o documento inteiro (salva tokens). |
| **Para clínicos** | A secção 3 (Person) e 4 (Relationship) são as que os clínicos devem validar. O resto é técnico. |

---
