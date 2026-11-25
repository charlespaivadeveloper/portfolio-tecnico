# Decisões de Arquitetura

Visão consolidada das decisões arquiteturais dos projetos publicados. Cada projeto mantém seus ADRs no repositório correspondente — esta página serve de índice e destaca padrões comuns.

---

## Padrões comuns

### Clean Architecture (Hexagonal / Ports & Adapters)

Todos os três projetos aplicam Clean Architecture com variações de nomenclatura:

| Círculo | enterprise-rag-java | spring-ai-mcp-clean | nomad-tax-calculation-engine |
|---|---|---|---|
| Domínio | `entity/` | `entities/` | `tax-domain` (Maven module) |
| Aplicação | `usecase/` | `usecases/` | `tax-usecases` |
| Interface Adapters | `interfaceadapter/` | `interfaceadapters/` | `tax-interface-adapters` |
| Frameworks | `framework/` | `frameworks/` | `tax-frameworks` |

**Consequência:** em todos os projetos, trocar Spring AI por outro provider, ou PostgreSQL por outro banco, não exige modificar use cases ou entities — apenas o adapter correspondente.

### AI providers como adapters substituíveis

Tanto o `enterprise-rag-java` (ADR-002) quanto o `spring-ai-mcp-clean` tratam modelos de linguagem e SDKs de AI como adapters. As abstrações (`EmbeddingPort`, `GenerationPort` no RAG; `ToolCallbackProvider` no MCP) vivem na camada de aplicação, não na de infraestrutura.

### PostgreSQL como banco único no MVP

Ambos `enterprise-rag-java` (ADR-001) e `nomad-tax-calculation-engine` (ADR-004) escolhem PostgreSQL como única camada de persistência no MVP, adiando a adoção de bancos especializados (OpenSearch, Redis) até que necessidade medida justifique.

---

## ADRs por repositório

### enterprise-rag-java

| ADR | Título | Status | Link |
|---|---|---|---|
| ADR-001 | PostgreSQL/pgvector em vez de OpenSearch | Aceita | [ver no GitHub](https://github.com/charlespaivadeveloper/enterprise-rag-java/blob/main/docs/ADR-001-PGVECTOR.md) |
| ADR-002 | AI providers como adapters substituíveis | Aceita | [ver no GitHub](https://github.com/charlespaivadeveloper/enterprise-rag-java/blob/main/docs/ADR-002-CLEAN-BOUNDARIES.md) |

### spring-ai-mcp-clean

<!-- TODO: verificar se existem ADRs formais no repositório spring-ai-mcp-clean; a arquitetura está documentada no README -->

### nomad-tax-calculation-engine

| ADR | Título | Status |
|---|---|---|
| ADR-001 | Monólito modular | Proposta (revisão em G2) |
| ADR-002 | BigDecimal/decimal engine | Proposta (revisão em G2) |
| ADR-003 | Regras versionadas | Proposta |
| ADR-004 | PostgreSQL | Proposta |
| ADR-005 | Transactional Outbox | Proposta |
| ADR-006 | REST síncrono | Proposta |
| ADR-007 | Boundary sintético | Proposta |
| ADR-008 | Clean Architecture | Proposta |

ADRs completos: [docs/architecture/adr/](https://github.com/charlespaivadeveloper/nomad-tax-calculation-engine/tree/main/docs/architecture/adr)

---

## Threat model

O projeto `enterprise-rag-java` mantém um [threat model](https://github.com/charlespaivadeveloper/enterprise-rag-java/blob/main/docs/THREAT-MODEL.md) com seis ameaças documentadas:

1. **Cross-tenant retrieval** — predicado de tenant obrigatório + RLS planejado
2. **Prompt injection** — validação de entrada + delimiter de conteúdo não confiável
3. **Instruções ocultas em documentos** — delimitadores especiais na ingestão + triagem de conteúdo
4. **Citações fabricadas** — referências limitadas ao material recuperado + entailment checks
5. **Exfiltração via model provider** — redação em boundary do adapter + modelos privados (roadmap)
6. **Abuso de recursos** — caps de requisição, processamento assíncrono, rate throttling (roadmap)

Princípio central: "A autorização de retrieval acontece antes de o conteúdo entrar no prompt."
