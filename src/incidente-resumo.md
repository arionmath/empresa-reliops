# Resumo do Incidente - ReliOps

## Contexto operacional
- Plataforma atende clientes com monitoramento de aplicações e alertas em tempo real.
- Serviço principal depende de API de ingestão de eventos e pipeline de processamento.
- SLO contratado: disponibilidade mensal de 99,9%.

## Incidente observado
- Em um pico de eventos, o serviço de processamento entrou em saturação.
- Alertas foram disparados com atraso e parte dos dashboards ficou desatualizada.
- A mitigação inicial restaurou o serviço, mas sem registro formal do aprendizado.

## Problemas de documentação identificados
- Não existe template padrão para postmortem.
- A linha do tempo do incidente ficou incompleta em diferentes fontes.
- Ações corretivas foram distribuídas entre times sem dono e prazo definidos.

## Expectativa da liderança de operações
- Registrar postmortem com causa raiz e fatores contribuintes.
- Definir ações corretivas/preventivas com responsáveis e acompanhamento.
- Criar histórico reutilizável para incidentes futuros.
