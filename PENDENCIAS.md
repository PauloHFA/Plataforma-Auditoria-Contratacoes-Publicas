# Pendências — Plataforma de Auditoria de Contratações Públicas (`NexusGov`)

Este documento consolida o estado técnico da plataforma, separando os requisitos homologados e verificados no piloto regional do backlog de melhorias e das dependências de produção nacional.

---

## 1. Concluído e Homologado (Engenharia & Piloto Regional PB)
- [x] **Matriz de Rastreabilidade SDD (50/50 Requisitos):** Especificação 100% implementada e coberta por testes automatizados (`spec/validate_traceability.py`).
- [x] **Salvaguardas Metodológicas e Éticas:**
  - `SG-01`: Linguagem neutra e estritamente probatória (*indício*, *exposição*, *sinal*; zero termos acusatórios) verificada por script automatizado.
  - `SG-02` / `SG-03`: Decomposição transparente dos sinais e aviso metodológico explícito per Resolução TCU nº 367/2024.
  - `SG-04`: Risco "Elevado" (> 60) condicionado à ativação de no mínimo 2 eixos analíticos independentes.
  - `SG-05`: Mitigação de homonímia com rebaixamento de confiança para pessoas físicas com mesmo nome e CPF mascarado.
  - Conformidade LGPD com perfis de acesso RBAC segregados (`AUDITOR`, `GESTOR`, `PUBLICO`).
- [x] **Motor Analítico Forense (`analytics/`):**
  - Grafos heterogêneos de coocorrência e detecção de comunidades via algoritmo de Leiden determinístico.
  - Teste de Benford em compras públicas (MAD de Nigrini, Kolmogorov-Smirnov na mantissa e correção Benjamini-Hochberg/FDR).
  - Detector de fracionamento de despesa em janelas deslizantes (30, 60 e 90 dias) conforme limites da Lei nº 14.133/2021.
  - Detecção de empresas recém-criadas (*pop-up vendors*) com capital social incompatível.
  - Cruzamento de sanções federais diretas e indiretas (CEIS, CNEP, CEPIM e TCU).
- [x] **Ingestão & Streaming Intra-Dia (`ingestion/`):**
  - Coletores de dados do PNCP, Receita Federal e IBGE com armazenamento idempotente em Parquet e hash SHA-256.
  - Sistema de streaming para alertas antecipados de editais com prazos exíguos de proposta.
- [x] **Backend REST & Dossiês (`backend/`):**
  - API Spring Boot 3.4 / Java 21, persistência Flyway (`V1` e `V2__seed_pilot_demo_data.sql`).
  - Dossiê executivo em PDF com checksum SHA-256 e verificação de assinatura digital qualificada (Gov.br / ICP-Brasil).
  - *Audit Copilot:* Minutas automáticas de instrução técnica per Resolução TCU 367/2024.
- [x] **Frontend Visualizador de Redes (`frontend/`):**
  - Interface React 18 + TypeScript com renderização interativa em Cytoscape.js e *timeline slider* temporal.
  - Mapa dos 25 municípios do piloto da Paraíba e timeline de fracionamento.
- [x] **Suíte Completa de Testes (`79/79` aprovados):**
  - 48 testes Python (analytics e ingestion).
  - 20 testes Java (Spring Boot / JUnit 5).
  - 11 testes React (Vitest).
  - Pipeline CI multi-job configurado e verde no GitHub Actions.

---

## 2. Melhorias Futuras Opcionais (Backlog de Engenharia)
- [ ] **Renderização 3D/WebGL para Megagrafos:** Implementar renderizador alternativo via WebGL (ex.: Force-Graph 3D) para redes com mais de 50.000 nós de coocorrência nacional simultâneos.
- [ ] **Orquestração com Apache Airflow:** Migrar os scripts de ingestão periódica do cron/Python direto para DAGs agendadas no Airflow caso seja implantado em cluster corporativo.

---

## 3. Dependências Externas (Bases Nacionais & Infraestrutura de Produção)
- [x] **Piloto Paraíba (PB):** Homologado com dados dos 25 maiores municípios paraibanos e cenários reais simulados via Flyway V2.
- [ ] **Carga Integral da Base CNPJ Nacional da Receita Federal:** O pipeline suporta a base completa (~100 GB descompactados); a ingestão nacional depende do download integral dos arquivos públicos da Receita.
- [ ] **Certificados Digitais A1/A3 Corporativos:** Necessários para emissão de assinaturas qualificadas reais em ambiente de produção com carimbo do tempo ICP-Brasil.
- [ ] **Infraestrutura Cloud de Produção:** Subida dos containers em cluster Kubernetes via Helm Charts disponíveis em `infra/k8s/` ou Docker Compose em `infra/compose/docker-compose.yml`.
