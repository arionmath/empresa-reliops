# INC-001: Saturação do pipeline de processamento e falha em inserção de dados

- **Status:** Em análise  
- **Severidade:** SEV1  
- **Período do incidente:** 2026-03-03 21:00 até 2026-03-06 17:00  
- **Autores:** Equipe ReliOps (Grupo)  
- **Times envolvidos:** Desenvolvimento, Infraestrutura, Banco de Dados (DBA), Operações  

---

## Metadados de Governança

- **Documento:** docs/postmortem-inc-001.md  
- **Versão:** 1.0  
- **Responsável (owner):** Tech Lead de Operações  
- **Aprovador:** Head de Engenharia  
- **Última atualização:** 2026-03-28  
- **Próxima revisão:** 2026-04-15  
- **Classificação da informação:** Interna  

---

## Premissas, Lacunas e Riscos (preenchimento obrigatório)

### Premissas
- O incidente ocorreu em ambiente de produção.
- A falha de sequence impactou diretamente a API de ingestão.
- O pico de eventos agravou o problema já existente.

### Lacunas de informação
- Métricas exatas de indisponibilidade (percentual de downtime).
- Logs detalhados da falha da sequence.
- Tempo exato entre detecção e resposta inicial.

### Riscos identificados

- **Risco:** Reincidência da falha de sequence  
  - **Impacto:** Paralisação total de funcionalidades críticas  
  - **Mitigação:** Monitoramento automático de sequences + validação pós-migração  

- **Risco:** Dependência de افراد-chave (DBA)  
  - **Impacto:** Atraso na resolução de incidentes  
  - **Mitigação:** Distribuição de acesso e treinamento  

---

## 1. Resumo Executivo

- **O que aconteceu:**  
Após uma migração de dados em produção, a aplicação passou a falhar ao inserir novos registros devido a inconsistência na sequência do banco de dados, levando à saturação do pipeline de processamento e falhas na API.

- **Impacto principal:**  
Indisponibilidade parcial/total das funcionalidades de escrita, atraso em alertas e dashboards desatualizados.

- **Situação atual:**  
Serviço restaurado após correção manual da sequence e liberação de acesso ao banco.

---

## 2. Impacto no Negócio e nos Usuários

- **Serviços afetados:**  
API de ingestão de eventos, pipeline de processamento e dashboards.

- **Janela de indisponibilidade/degradação:**  
De 2026-03-04 09:00 até 2026-03-06 17:00

- **Impacto percebido pelo cliente:**
- Impossibilidade de criar novos registros  
- Atraso em alertas críticos  
- Perda de confiabilidade nos dashboards  

---

## 3. Linha do Tempo do Incidente

| Horário | Evento | Evidência/Fonte | Responsável |
|--------|-------|----------------|------------|
| 2026-03-03 15:00 | Finalização do script de migração | Commit/PR | Dev |
| 2026-03-03 21:00 | Script aplicado em produção | Log de deploy | Dev/Infra |
| 2026-03-04 09:00 | Aumento de erros 5xx | Monitoramento | Operações |
| 2026-03-04 09:30 | Reclamações de clientes | Suporte | Suporte |
| 2026-03-04 13:00 | Identificação da causa (sequence) | Dev | Desenvolvimento |
| 2026-03-04 → 05 | Tentativas de acesso ao banco via tickets | Sistema de tickets | Infra |
| 2026-03-05 | Sistema indisponível na região | Monitoramento | Operações |
| 2026-03-06 17:00 | Acesso ao banco e correção manual | DBA | PM/DBA |

---

## 4. Detecção e Resposta Inicial

- **Como o incidente foi detectado:**  
Monitoramento de erros 5xx + reclamações de clientes.

- **Tempo até início da resposta:**  
Aproximadamente 4 horas após deploy.

- **Medidas imediatas adotadas:**  
- Investigação da causa  
- Abertura de tickets para acesso ao banco  
- Tentativas de correção indireta  

---

## 5. Causa Raiz e Fatores Contribuintes

### Causa raiz
Falha na atualização da sequence do banco de dados após migração, causando conflito de chaves primárias.

### Fatores técnicos contribuintes
- Script de migração não ajustou a sequence corretamente  
- Ausência de validação pós-migração  
- Falta de monitoramento específico para inconsistência de sequence  

### Fatores processuais contribuintes
- Deploy realizado sem validação completa  
- Ausência de ambiente de homologação confiável  
- Falta de acesso ao banco em situação crítica  
- Dependência de um único DBA  

---

## 6. Mitigação e Recuperação

- **Ações que restauraram o serviço:**  
Correção manual da sequence no banco de dados.

- **Estratégia de rollback/contorno:**  
Não houve rollback; foi aplicada correção direta em produção.

- **Critério de estabilização:**  
- Redução de erros 5xx a níveis normais  
- Restabelecimento das operações de escrita  

---

## 7. Ações Corretivas e Preventivas

| Ação | Tipo | Prioridade | Responsável | Prazo | Status |
|-----|------|-----------|------------|-------|--------|
| Implementar validação automática de sequence pós-migração | Preventiva | Alta | Dev/DBA | 15 dias | Pendente |
| Criar checklist obrigatório para deploy | Preventiva | Alta | Operações | 10 dias | Pendente |
| Garantir acesso emergencial ao banco | Corretiva | Alta | Infra | 7 dias | Pendente |
| Treinamento em banco de dados para equipe | Preventiva | Média | RH/Tech Lead | 30 dias | Pendente |
| Implantar ambiente de homologação robusto | Preventiva | Alta | Infra | 30 dias | Pendente |
| Adicionar monitoramento de integridade de dados | Preventiva | Alta | SRE | 20 dias | Pendente |

---

## 8. Lições Aprendidas

### O que funcionou bem
- Identificação relativamente rápida da causa técnica  

### O que não funcionou
- Falta de acesso ao banco atrasou resolução  
- Ausência de processo estruturado de postmortem  
- Deploy sem validação adequada  

### Melhorias de processo recomendadas
- Padronização de postmortem  
- Revisão obrigatória de scripts críticos  
- Planejamento de deploy com rollback definido  

---

## 9. Acompanhamento

- **Itens pendentes:**  
Todas as ações preventivas listadas  

- **Próxima revisão:**  
2026-04-15  

- **Critério de encerramento do postmortem:**  
- Todas as ações críticas implementadas  
- Monitoramento ativo comprovando estabilidade  

---

## Anexos e Referências

- **Evidências (logs, métricas, gráficos):**  
Logs de erro 5xx, histórico de deploy, tickets de suporte  

- **Canal/ticket do incidente:**  
Sistema interno de tickets  

- **Runbooks, RFCs e ADRs relacionados:**  
(Não disponíveis – lacuna identificada)  

- **Links de PRs/issues das ações corretivas:**  
(A serem definidos)  

---

## Checklist de Qualidade (pré-entrega)

- [x] Impacto e janela do incidente quantificados  
- [x] Linha do tempo completa com evidências  
- [x] Causa raiz e fatores contribuintes descritos  
- [x] Ações corretivas/preventivas com dono e prazo  
- [x] Premissas, lacunas, riscos e acompanhamento preenchidos  
