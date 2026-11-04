# Genogram — Vision

## O que é

Uma ferramenta **local-first, cross-platform** para criação, edição, análise e
colaboração de **genogramas** e **eco-maps** em contextos clínicos,
genealógicos e sociais.

## Para quem

| Perfil | Uso principal |
|---|---|
| Médico de família / geral | Genograma clínico completo (medical, social, cultural) |
| Psicólogo / Psiquiatra | Dinâmicas familiares, eco-maps, padrões multigeracionais |
| Enfermeiro | Plano de cuidados, condições ativas |
| Geneticista | Padrões hereditários, variantes por linhagem |
| Assistente social | Contexto familiar, violência, dependências |
| Genealogista | Árvore genealógica enriquecida |
| Hospital / ULS | Deploy centralizado (NAS) com RBAC por especialidade |

## Problema que resolve

Nenhuma ferramenta existente oferece **simultaneamente**:
- Profundidade de dados (medical history temporal, criminal, cultural)
- AI end-to-end (geração, edição, análise de padrões)
- Colaboração em tempo real com RBAC
- **Zero dados na cloud** (E2E, local-first, P2P)
- Todas as plataformas (Win, Mac, Linux, iOS, Android, NAS, browser)

## O que NÃO é

- Não é um **sistema de registo clínico** (não substitui SClínico, Epic, Cerner).
- Não é um **sistema genealógico** (não substitui Gramps, RootsMagic — mas importa GEDCOM).
- Não é uma **plataforma de diagramação geral** (não substitui Creately, draw.io).
- Não é um **servidor central de dados** (os dados vivem no dispositivo do utilizador).
- Não é um **sistema de gestão hospitalar** (não trata admissões, faturação, agendamento).

## Princípios inegociáveis

1. **Privacidade primeiro:** Nenhum dado legível sai do dispositivo sem a chave do utilizador.
2. **Local-first:** Funciona 100% offline. A rede é um *upgrade*, não um requisito.
3. **Open standards:** GEDCOM, FHIR, SVG, PDF. Sem lock-in.
4. **Transparência:** O código é auditável. As decisões estão documentadas (ADRs).
5. **Portabilidade:** O utilizador pode levar os seus dados a qualquer altura (export).

## Stack (resumo)

| Camada | Tecnologia |
|---|---|
| Motor | Rust (Yrs, SQLite, libsodium) |
| Frontend | Svelte 5 + Konva.js |
| Desktop/Mobile | Tauri 2 |
| Web/PWA | WASM (motor compilado) + Service Worker |
| NAS | Docker (headless Rust binary) |
| Sync | CRDT (Yjs/Yrs) + WebRTC P2P + E2E (XChaCha20-Poly1305) |
| Licença | MIT (todo o código) |

## Métricas de sucesso (v1.0)

- [ ] Genograma com 200 pessoas abre em < 2s (desktop)
- [ ] Sync entre 2 dispositivos: < 2s (mesma rede), < 5s (internet)
- [ ] Tamanho do app desktop: < 15 MB
- [ ] Funciona offline: 100% das features (exceto AI cloud, que é opcional)
- [ ] GEDCOM import/export: round-trip lossless
- [ ] RBAC: dados restritos nunca chegam ao dispositivo não autorizado
- [ ] 0 dependências com license GPL/AGPL   
