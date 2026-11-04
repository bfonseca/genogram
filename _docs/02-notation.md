# Genogram — Notation Specification (v1.0)

> Este documento define cada símbolo usado no genograma.
> É a "gramática" que o motor interpreta e o renderer desenha.
> Cada entrada tem: `id`, `label` (PT/EN), `visual`, `gedcom`, `fhir`, `rbac_default`.

---

## 1. Person Shapes (40+)

### 1.1. Formas Base

| ID | Forma | Label PT | Label EN | Descrição Visual |
|---|---|---|---|---|
| `person_male` | Quadrado | Homem | Male | Quadrado, contorno preto, 40×40px |
| `person_female` | Círculo | Mulher | Female | Círculo, contorno preto, 40px diâmetro |
| `person_nonbinary` | Dome | Não-binário | Non-binary | Base quadrada, topo circular |
| `person_intersex` | Quadrado + Círculo | Intersexo | Intersex | Quadrado com círculo sobreposto |
| `person_unknown` | Losango | Desconhecido | Unknown | Losango, contorno preto |
| `person_pet` | Hexágono | Animal de estimação | Pet | Hexágono, 30×30px |
| `person_coparent` | Retângulo arredondado | Coparente | Coparent | Retângulo arredondado, 50×30px |

### 1.2. Variantes de Género

| ID | Base | Modificador | Label PT | Label EN |
|---|---|---|---|---|
| `person_male_trans` | Quadrado | Círculo interior | Homem trans | Trans man |
| `person_female_trans` | Círculo | Quadrado interior | Mulher trans | Trans woman |
| `person_male_gay` | Quadrado | Triângulo invertido interior | Gay | Gay male |
| `person_female_lesbian` | Círculo | Triângulo invertido interior | Lésbica | Lesbian female |
| `person_bisexual` | Quadrado/Círculo | Triângulo invertido pontilhado | Bissexual | Bisexual |
| `person_index` | Qualquer | Contorno duplo | Pessoa índice | Index person (proband) |
| `person_deceased` | Qualquer | X através do símbolo | Falecido | Deceased |
| `person_pregnant` | Círculo | Triângulo interior | Gravidez | Pregnant |
| `person_misriage` | Círculo | Triângulo + X | Aborto espontâneo | Miscarriage |
| `person_induced_abortion` | Círculo | Triângulo + X + linha horizontal | Aborto induzido | Induced abortion |
| `person_stillbirth` | Quadrado/Círculo reduzido | X + "SB" | Natimorto | Stillbirth |
| `person_childless` | Qualquer | Barra diagonal interior | Sem filhos | Childless |

### 1.3. Preenchimento / Shading

| ID | Padrão | Label PT | Label EN | Uso |
|---|---|---|---|---|
| `fill_solid` | Preenchido a preto | Preenchido | Solid fill | Falecido (alternativa ao X) |
| `fill_half_left` | Metade esquerda | Metade esquerda | Half left | Doença física/mental |
| `fill_half_bottom` | Metade inferior | Metade inferior | Half bottom | Álcool/substâncias |
| `fill_half_bottom_hatched` | Metade inferior hachurada | Metade inferior hachurada | Half bottom hatched | Suspeita de substâncias |
| `fill_quadrant_tl` | Quadrante sup. esquerdo | Quadrante TL | Top-left quadrant | Cardiovascular |
| `fill_quadrant_tr` | Quadrante sup. direito | Quadrante TR | Top-right quadrant | Mental/Neurológico |
| `fill_quadrant_bl` | Quadrante inf. esquerdo | Quadrante BL | Bottom-left quadrant | Oncológico |
| `fill_quadrant_br` | Quadrante inf. direito | Quadrante BR | Bottom-right quadrant | Metabólico/Endócrino |
| `fill_hatched` | Hachurado (diagonal) | Hachurado | Hatched | Suspeita (não confirmada) |
| `fill_stripped` | Listrado (horizontal) | Listrado | Striped | Em recuperação/remissão |
| `fill_partial` | Parcial (metade) | Parcial | Partial | Em recuperação |

### 1.4. Anotações no Símbolo

| ID | Posição | Conteúdo | Exemplo |
|---|---|---|---|
| `annotation_age` | Interior, centro | Idade atual ou à morte | "45" ou "d. 72" |
| `annotation_birth_year` | Superior esquerdo | Ano de nascimento | "1945" |
| `annotation_death_year` | Inferior direito | Ano de morte | "2010" |
| `annotation_occupation` | Inferior, centro | Ocupação (abreviada) | "Mec." |
| `annotation_religion` | Superior direito | Ícone religioso | "✝" |
| `annotation_cultural` | Preenchimento do símbolo | Padrão cultural | (ver §5) |

---

## 2. Relationship Lines — Partner (18)

| ID | Label PT | Label EN | Linha | Marcadores | GEDCOM | FHIR | RBAC Default |
|---|---|---|---|---|---|---|---|
| `rel_married` | Casado | Married | Sólida (1) | — | `HUSB`/`WIFE` | `RelatedPerson.relationship: married` | All |
| `rel_civil_union` | União de facto | Civil union | Sólida (1) | Anel (○) | `HUSB`/`WIFE` + NOTE | `RelatedPerson.relationship: civil_union` | All |
| `rel_cohabiting` | Cohabitação | Cohabiting | Tracejada (1) | — | NOTE "Cohabiting" | `RelatedPerson.relationship: cohabiting` | All |
| `rel_dating` | Namoro | Dating | Pontilhada (1) | — | — | — | All |
| `rel_engaged` | Noivado | Engaged | Pontilhada (1) | Anel (○) | — | — | All |
| `rel_separated` | Separado | Separated | Sólida (1) | Barra simples (|) | `DIV` (pending) | `RelatedPerson.relationship: separated` | All |
| `rel_divorced` | Divorciado | Divorced | Sólida (1) | Dupla barra (||) | `DIV/DATE` | `RelatedPerson.relationship: divorced` | All |
| `rel_widowed` | Viúvo/a | Widowed | (derivado) | — | `DEAT` no parceiro | — | All |
| `rel_affair` | Caso extraconjugal | Affair | Pontilhada (1) | Cruz (×) | NOTE "Affair" | — | Clinical only |
| `rel_on_off` | Relação intermitente | On-off | Ondulada (1) | — | — | — | Clinical only |
| `rel_long_distance` | Relação à distância | Long distance | Tracejada (1) | Pontos (···) | — | — | All |
| `rel_polyamorous` | Poliamor | Polyamorous | Sólida (1) | 3+ conexões | — | — | Clinical only |
| `rel_common_law` | União estável | Common law | Sólida (1) | — | `HUSB`/`WIFE` | `RelatedPerson.relationship: common_law` | All |
| `rel_domestic_partnership` | Parceria doméstica | Domestic partnership | Sólida (1) | Coração (♥) | — | — | All |
| `rel_separated_reconciled` | Separado → reconciliado | Separated → reconciled | Sólida (1) | Seta reconexão (↩) | — | — | Clinical only |
| `rel_divorced_reconciled` | Divorciado → reconciliado | Divorced → reconciled | Sólida (1) | Seta reconexão (↩) | — | — | Clinical only |
| `rel_partner_unknown` | Parceiro desconhecido | Partner unknown | Pontilhada (1) | "?" | — | — | All |
| `rel_partner_deceased` | Parceiro falecido | Partner deceased | (derivado) | X no símbolo | `DEAT` | — | All |

---

## 3. Relationship Lines — Parent-Child (7)

| ID | Label PT | Label EN | Linha | Marcadores | GEDCOM | FHIR | RBAC Default |
|---|---|---|---|---|---|---|---|
| `rel_biological` | Filho biológico | Biological child | Sólida (1) | — | `CHIL` (no `FAM`) | `RelatedPerson.relationship: parent` | All |
| `rel_adopted` | Filho adotado | Adopted child | Tracejada (1) | — | `CHIL` + `ADOP` | `RelatedPerson.relationship: adopted_parent` | All |
| `rel_foster` | Filho em acolhimento | Foster child | Pontilhada (1) | — | NOTE "Foster" | `RelatedPerson.relationship: foster_parent` | Clinical only |
| `rel_step` | Filho de passo | Step child | Sólida + pontilhada | — | NOTE "Step" | `RelatedPerson.relationship: step_parent` | All |
| `rel_twin_identical` | Gémeos idênticos | Identical twins | 2 linhas + barra topo | — | `TWIN` + NOTE "identical" | — | All |
| `rel_twin_fraternal` | Gémeos fraternos | Fraternal twins | 2 linhas | — | `TWIN` | — | All |
| `rel_coparent` | Coparente | Coparent | Retângulo arredondado | — | — | — | All |

---

## 4. Relationship Lines — Emotional (39)

### 4.1. Próximo / Harmonioso

| ID | Label PT | Label EN | Linha | Marcadores | RBAC Default |
|---|---|---|---|---|---|
| `rel_close` | Próximo | Close | 2 linhas paralelas | — | Clinical |
| `rel_very_close` | Muito próximo | Very close | 3 linhas paralelas | — | Clinical |
| `rel_fused` | Fuso / emaranhado | Fused / enmeshed | 3 linhas (denso) | — | Clinical |
| `rel_enmeshed` | Emaranhado | Enmeshed | 3 linhas + ondas | — | Clinical |
| `rel_harmonious` | Harmonioso | Harmonious | 2 linhas | Coração (♥) | Clinical |

### 4.2. Distante / Frio

| ID | Label PT | Label EN | Linha | Marcadores | RBAC Default |
|---|---|---|---|---|---|
| `rel_distant` | Distante | Distant | Tracejada (1) | — | Clinical |
| `rel_very_distant` | Muito distante | Very distant | 2 tracejadas | — | Clinical |
| `rel_cold` | Frio | Cold | Tracejada (1) | "X" | Clinical |
| `rel_estranged` | Estranhado | Estranged | 2 tracejadas | "X" | Clinical |
| `rel_disconnected` | Desconectado | Disconnected | Linha cortada | — | Clinical |

### 4.3. Conflito

| ID | Label PT | Label EN | Linha | Marcadores | RBAC Default |
|---|---|---|---|---|---|
| `rel_conflict` | Conflito | Conflict | 2 tracejadas (zigzag) | — | Clinical |
| `rel_severe_conflict` | Conflito grave | Severe conflict | 2 zigzag (denso) | — | Clinical |
| `rel_hostile` | Hostil | Hostile | Zigzag (1) | — | Clinical |
| `rel_very_hostile` | Muito hostil | Very hostile | 2 zigzag (denso) | — | Clinical |
| `rel_abusive` | Abusivo | Abusive | Zigzag (1) | "!" | Clinical |
| `rel_verbal_abuse` | Abuso verbal | Verbal abuse | Zigzag (1) | "V" | Clinical |
| `rel_physical_abuse` | Abuso físico | Physical abuse | Zigzag (1) | "P" | Clinical |
| `rel_sexual_abuse` | Abuso sexual | Sexual abuse | Zigzag (1) | "S" | Clinical |
| `rel_emotional_abuse` | Abuso emocional | Emotional abuse | Zigzag (1) | "E" | Clinical |
| `rel_neglect` | Negligência | Neglect | Linha cortada | "N" | Clinical |

### 4.4. Cutoff / Ruptura

| ID | Label PT | Label EN | Linha | Marcadores | RBAC Default |
|---|---|---|---|---|---|
| `rel_cutoff` | Cutoff (ruptura) | Cutoff | 2 barras (||) | — | Clinical |
| `rel_cutoff_repaired` | Cutoff reparado | Cutoff repaired | 2 barras + círculo | ○ | Clinical |
| `rel_ambivalent` | Ambivalente | Ambivalent | 2 linhas + zigzag | — | Clinical |
| `rel_conflictual` | Conflitual (próximo e hostil) | Conflictual | 2 linhas + zigzag | — | Clinical |
| `rel_fused_hostile` | Fuso e hostil | Fused and hostile | 3 linhas + zigzag | — | Clinical |

### 4.5. Cuidado / Dependência

| ID | Label PT | Label EN | Linha | Marcadores | RBAC Default |
|---|---|---|---|---|---|
| `rel_caregiver` | Cuidador | Caregiver | 2 setas abertas | ↔ | Clinical |
| `rel_dependent` | Dependente | Dependent | Seta preenchida | → | Clinical |
| `rel_codependent` | Codependente | Codependent | 2 setas + ondas | ↔ | Clinical |
| `rel_rescuer` | Resgatador | Rescuer | Seta preenchida | "R" | Clinical |
| `rel_martyr` | Mártir | Martyr | Seta preenchida | "M" | Clinical |

### 4.6. Outros Emocionais

| ID | Label PT | Label EN | Linha | Marcadores | RBAC Default |
|---|---|---|---|---|---|
| `rel_secret` | Segredo | Secret | Triângulo pequeno preenchido | △ | Clinical |
| `rel_gossip` | Fofoca | Gossip | Linhas curtas | "G" | Clinical |
| `rel_triangulation` | Triangulação | Triangulation | 3 linhas (triângulo) | — | Clinical |
| `rel_scapegoat` | Bode expiatório | Scapegoat | Seta preenchida | "X" | Clinical |
| `rel_golden_child` | Filho dourado | Golden child | Seta preenchida | ★ | Clinical |
| `rel_enabler` | Facilitador | Enabler | Seta preenchida | "E" | Clinical |
| `rel_spiritual` | Vínculo espiritual | Spiritual bond | Ondulada | "✝" (ou símbolo) | Clinical |
| `rel_mentor` | Mentor | Mentor | Seta preenchida | "M" | Clinical |
| `rel_rival` | Rival | Rival | 2 setas opostas | ↔ (cruzadas) | Clinical |
| `rel_jealous` | Ciúme | Jealous | Seta preenchida | "J" | Clinical |

---

## 5. Cultural Patterns (11)

| ID | Padrão Visual | Label PT | Label EN | Descrição |
|---|---|---|---|---|
| `cult_solid` | Preenchido a preto | Preenchido | Solid | Herança 100% |
| `cult_horizontal` | Linhas horizontais | Horizontal | Horizontal lines | Herança 75% |
| `cult_vertical` | Linhas verticais | Vertical | Vertical lines | Herança 50% |
| `cult_diagonal_right` | Diagonais ↗ | Diagonal ↗ | Diagonal right | Herança 25% |
| `cult_diagonal_left` | Diagonais ↘ | Diagonal ↘ | Diagonal left | Herança 12.5% |
| `cult_grid` | Grade (cruzamento) | Grade | Grid | Mista |
| `cult_dots` | Pontos | Pontos | Dots | Mista (leve) |
| `cult_waves` | Ondas | Ondas | Waves | Mista (fluida) |
| `cult_braid` | Trança | Trança | Braid | Múltipla herança |
| `cult_crosshatch` | Hachura cruzada | Hachura cruzada | Crosshatch | Mista (densa) |
| `cult_checkerboard` | Xadrez | Xadrez | Checkerboard | Mista (alternada) |

**Nota:** Uma pessoa pode ter múltiplos `cultural_heritage` (ex.: metade horizontal + metade vertical = 50% + 50%).

---

## 6. Religion Icons (13)

| ID | Ícone | Label PT | Label EN |
|---|---|---|---|
| `relig_christianity` | ✝ | Cristianismo | Christianity |
| `relig_catholicism` | ✝ (cruz latina) | Catolicismo | Catholicism |
| `relig_protestantism` | ✝ (cruz simples) | Protestantismo | Protestantism |
| `relig_orthodoxy` | ✝ (cruz ortodoxa) | Ortodoxia | Orthodoxy |
| `relig_islam` | ☪ | Islão | Islam |
| `relig_judaism` | ✡ | Judaísmo | Judaism |
| `relig_hinduism` | ॐ | Hinduísmo | Hinduism |
| `relig_buddhism` | ☸ | Budismo | Buddhism |
| `relig_sikhism` | ☬ | Sikismo | Sikhism |
| `relig_taoism` | ☯ | Taoísmo | Taoism |
| `relig_shinto` | ⛩ | Xintoísmo | Shinto |
| `relig_other` | ? | Outro | Other |
| `relig_none` | — | Ateu/Agnóstico | None / Atheist |

---

## 7. Socioeconomic Levels (5)

| ID | Símbolo | Label PT | Label EN |
|---|---|---|---|
| `soc_poverty` | $ | Pobreza | Poverty |
| `soc_working` | $$ | Classe operária | Working class |
| `soc_middle` | $$$ | Classe média | Middle class |
| `soc_upper_middle` | $$$$ | Classe média-alta | Upper-middle class |
| `soc_upper` | $$$$$ | Classe alta | Upper class |

---

## 8. Medical Quadrants (4 + 1)

| Posição | Categoria | Cor | Condições |
|---|---|---|---|
| Superior Esquerdo (TL) | Cardiovascular | 🔴 Vermelho | Hipertensão, IAM, AVC, insuficiência cardíaca, arritmia, DVP |
| Superior Direito (TR) | Mental/Neurológico | 🟡 Amarelo | Depressão, ansiedade, bipolar, esquizofrenia, demência, Alzheimer, Parkinson, epilepsia, TDAH, autismo |
| Inferior Esquerdo (BL) | Oncológico | 🟢 Verde | Mama, cólon, pulmão, próstata, ovário, leucemia, linfoma, pele, outro |
| Inferior Direito (BR) | Metabólico/Endócrino | 🔵 Azul | Diabetes (T1/T2/gestacional), tireoide, obesidade, asma, alergia, autoimune |
| Centro | Outro | ⚫ Preto | Respiratório, musculoesquelético, renal, hepático, infeccioso, genético |

### 8.1. Estados da Condição (fill)

| Estado | Preenchimento | Label PT | Label EN |
|---|---|---|---|
| `active` | Sólido (cor cheia) | Ativo | Active |
| `suspected` | Hachurado (diagonal) | Suspeita | Suspected |
| `remission` | Listrado (horizontal) | Remissão | Remission |
| `recovered` | Parcial (metade) | Recuperado | Recovered |
| `deceased` | Sólido + X | Faleceu desta condição | Deceased of condition |

---

## 9. Substance Indicators

| ID | Posição | Preenchimento | Label PT | Label EN |
|---|---|---|---|---|
| `sub_alcohol_active` | Metade inferior | Sólido (castanho) | Álcool (ativo) | Alcohol (active) |
| `sub_alcohol_former` | Metade inferior | Listrado | Álcool (ex) | Alcohol (former) |
| `sub_tobacco_active` | Metade inferior | Sólido (cinza) | Tabaco (ativo) | Tobacco (active) |
| `sub_tobacco_former` | Metade inferior | Listrado | Tabaco (ex) | Tobacco (former) |
| `sub_drugs_active` | Metade inferior | Sólido (roxo) | Drogas (ativo) | Drugs (active) |
| `sub_drugs_former` | Metade inferior | Listrado | Drogas (ex) | Drugs (former) |
| `sub_drugs_suspected` | Metade inferior | Hachurado | Drogas (suspeita) | Drugs (suspected) |

---

## 10. Household / Residence

| ID | Visual | Label PT | Label EN |
|---|---|---|---|
| `household_bracket` | Bracket (parenteses) ao redor dos membros | Agregado familiar | Household |
| `household_line` | Linha horizontal sob os membros | Morada | Residence line |
| `household_label` | Texto sob a linha | Nome da morada | Address label |

---

## 11. Eco-Map Symbols

| ID | Visual | Label PT | Label EN |
|---|---|---|---|
| `eco_center` | Círculo grande (80px) | Centro (pessoa) | Center (person) |
| `eco_node_family` | Círculo médio, borda azul | Família | Family |
| `eco_node_friends` | Círculo médio, borda verde | Amigos | Friends |
| `eco_node_work` | Quadrado médio, borda castanha | Trabalho | Work |
| `eco_node_school` | Quadrado médio, borda roxa | Escola | School |
| `eco_node_health` | Círculo médio, borda vermelha | Saúde | Health |
| `eco_node_community` | Hexágono, borda laranja | Comunidade | Community |
| `eco_node_religion` | Círculo médio, borda dourada | Religião | Religion |
| `eco_node_legal` | Quadrado médio, borda preta | Legal | Legal |
| `eco_conn_strong` | 3 linhas paralelas | Forte | Strong |
| `eco_conn_moderate` | 2 linhas paralelas | Moderado | Moderate |
| `eco_conn_weak` | 1 linha | Fraco | Weak |
| `eco_conn_negative` | Zigzag | Negativo | Negative |
| `eco_conn_none` | Linha cortada | Ausente | None |
| `eco_dir_bidirectional` | ↔ | Bidirecional | Bidirectional |
| `eco_dir_outward` | → | Para fora | Outward |
| `eco_dir_inward` | ← | Para dentro | Inward |

---

## 12. Line Styles (referência para o renderer)

| Estilo | Definição SVG | Exemplo |
|---|---|---|
| `solid` | `stroke-dasharray: none` | — |
| `dashed` | `stroke-dasharray: 8,4` | - - - |
| `dotted` | `stroke-dasharray: 2,4` | · · · |
| `dash_dot` | `stroke-dasharray: 8,4,2,4` | -·-·- |
| `wavy` | Caminho SVG com `q` (quadratic bezier) | ~~~ |
| `zigzag` | Caminho SVG com segmentos alternados | ⚡ |

### 12.1. Line Markers (símbolos na linha)

| Marker | Definição SVG | Uso |
|---|---|---|
| `cross` | 2 linhas a 45° (X) | Falecido, estranhado |
| `single_bar` | 1 linha perpendicular (|) | Separação |
| `double_bar` | 2 linhas perpendiculares (||) | Divórcio, cutoff |
| `arrow_filled` | Triângulo preenchido (→) | Dependência, cuidado |
| `arrow_open` | V aberto (→) | Cuidador |
| `heart` | Coração (♥) | Harmonioso, parceria |
| `circle_small` | Círculo pequeno (○) | Cutoff reparado |
| `triangle_filled` | Triângulo preenchido (△) | Segredo, gravidez |
| `exclamation` | "!" | Abuso |
| `question` | "?" | Desconhecido |
| `star` | Estrela (★) | Golden child |
| `letter_X` | "X" | Scapegoat |
| `letter_V` | "V" | Abuso verbal |
| `letter_P` | "P" | Abuso físico |
| `letter_S` | "S" | Abuso sexual |
| `letter_E` | "E" | Abuso emocional / Enabler |
| `letter_R` | "R" | Rescuer |
| `letter_M` | "M" | Martyr / Mentor |
| `letter_G` | "G" | Gossip |
| `letter_N` | "N" | Neglect / Neighbor |
| `letter_J` | "J" | Jealous |
| `letter_F` | "F" | Friendship |

---

## 13. Cores Padrão (Color Scheme "Medical")

| Elemento | Cor (Hex) |
|---|---|
| Fundo | `#FFFFFF` |
| Contorno padrão | `#333333` |
| Cardiovascular (TL) | `#E74C3C` |
| Mental/Neurológico (TR) | `#F39C12` |
| Oncológico (BL) | `#27AE60` |
| Metabólico/Endócrino (BR) | `#2980B9` |
| Outro (Centro) | `#7F8C8D` |
| Substâncias (metade inferior) | `#8B4513` |
| Relação conflito | `#FF6B6B` |
| Relação cutoff | `#95A5A6` |
| Relação próxima | `#2ECC71` |
| Household bracket | `#3498DB` |
| Eco-map center | `#34495E` |
| Eco-map connection | `#7F8C8D` |

### 13.1. Color Scheme "High Contrast"

| Elemento | Cor (Hex) |
|---|---|
| Fundo | `#000000` |
| Contorno padrão | `#FFFFFF` |
| Todas as condições | `#FFFF00` (amarelo) |
| Relações | `#FFFFFF` |
| Conflito | `#FF0000` |

---

## 14. Legend (automática no export)

O export (PDF/PNG/SVG) inclui automaticamente uma legenda com:

1. **Person shapes** usados no genograma (só os que existem)
2. **Relationship lines** usadas (só as que existem)
3. **Medical quadrants** presentes
4. **Cultural patterns** presentes
5. **Religion icons** presentes
6. **Socioeconomic** presentes
7. **Substance indicators** presentes

**Formato da legenda:** Tabela 2 colunas (símbolo | descrição), font 10pt, máx 2 colunas de símbolos.

---

## 15. Regras de Renderização

| Regra | Detalhe |
|---|---|
| Ordem de desenho | 1. Linhas (relações) → 2. Nós (pessoas) → 3. Anotações → 4. Households → 5. Eco-map |
| Z-index | Linhas atrás dos nós. Nós atrás das anotações. |
| Overlap | Se 2 linhas cruzam, a que está "por cima" faz um "hop" (arco) de 8px. |
| Mínimo distância | 60px entre centros de nós adjacentes. |
| Geração spacing | 120px vertical entre gerações. |
| Posição spacing | 80px horizontal entre irmãos. |
| Zoom | 25% a 400% (snap em 25%). |
| Pan | Ilimitado (infinite canvas). |
| Grid | Opcional. 20px. Snap em 10px. |
| Mínimo canvas | 1000×1000px. Máximo: 10000×10000px. |

---

## 16. Acessibilidade

| Regra | Detalhe |
|---|---|
| `aria-label` | Cada nó: "João Silva, homem, 65 anos, falecido em 2010, hipertensão" |
| `aria-label` linhas | "Relação: Casado, de João Silva para Maria Silva, desde 1970" |
| Contraste | Mínimo WCAG AA (4.5:1) para texto. |
| High contrast | Color scheme alternativo (ver §13.1). |
| Screen reader | Navegação por tabs: nós → relações → anotações. |
| Font | Mínimo 12pt para anotações. 10pt para legenda. |   
