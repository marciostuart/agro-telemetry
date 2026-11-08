# AgroTelemetry

Plataforma de telemetria e automação rural com nós LoRa, gateway IP e serviços SaaS multi-tenant. O projeto começa com validação em laboratório, priorizando baixo custo de hardware e operação.

## Estado atual

Estamos no planejamento do MVP. O documento de arquitetura de trabalho (`ARCHITECTURE.md`) e as memórias locais ficam fora do Git por conterem material de trabalho privado. As decisões compartilháveis e evolutivas ficam em `docs/`.

## Princípios para o MVP

- Validar primeiro com simulação e ferramentas já disponíveis; comprar placas apenas para testar o que a simulação não prova.
- Separar o protocolo de rádio do backend e da interface.
- Medir consumo, alcance, taxa de entrega e latência antes de dimensionar energia ou prometer cobertura.
- Manter acionamentos seguros localmente, mesmo sem nuvem ou rádio.
- Adiar serviços e componentes de infraestrutura até existir necessidade medida.

## Próximo passo

Seguir o plano de laboratório em [`docs/PLANO-INICIAL.md`](docs/PLANO-INICIAL.md). A primeira entrega técnica deve ser uma especificação do frame e um simulador/validador de protocolo antes de implementar a malha em hardware.
