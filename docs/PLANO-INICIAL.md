# Plano inicial de desenvolvimento e laboratório

## Objetivo do laboratório

Reduzir risco técnico e custo antes de instalar equipamentos em campo. A ordem é testar software sem rádio, depois dois rádios em bancada, e só então avaliar um repetidor e autonomia energética.

## Sequência proposta

### 0. Fechar decisões de engenharia

Registrar decisões curtas em `docs/decisions/` quando necessário:

1. Faixa e parâmetros RF permitidos para o produto no Brasil, requisitos de homologação e limites de potência/antena. A referência `AU915` do rascunho deve ser tratada como hipótese, não como decisão normativa.
2. Identidade e segurança: como provisionar cada dispositivo, autenticar comandos e impedir replay/duplicação perigosa.
3. Frame canônico: tamanho máximo, versões, endianess, CRC, campos e payloads. O rascunho precisa esclarecer se `PAYLOAD_LEN` é comprimento ou índice final e como representar tipos de payload que hoje compartilham o mesmo `MSG_TYPE`.
4. Semântica de comandos: sequência, expiração, ACK, repetição idempotente, estado seguro após reinício/perda de enlace e timeout local obrigatório.
5. Metas mensuráveis de MVP: intervalo de telemetria, latência de comando, taxa mínima de entrega e autonomia desejada.

### 1. Protocolo sem hardware

- Definir codificação e decodificação do frame com vetores de referência.
- Implementar CRC e validação de comprimento/versão.
- Exercitar entradas truncadas, corrompidas e fora do limite.
- Simular perda, duplicação, reordenação, TTL esgotado e retransmissão.
- Usar um simulador serial/host para desenvolver a lógica sem placas.

**Saída:** especificação congelada para o MVP, biblioteca portável e resultados reproduzíveis em computador.

### 2. Enlace ponto a ponto

Só então usar duas placas LoRa já disponíveis ou adquirir o menor conjunto necessário. Medir RSSI/SNR, entrega, latência e consumo em distâncias e configurações controladas. Não inferir alcance de campo a partir de bancada.

**Saída:** baseline de rádio e escolha de parâmetros sustentada por medições.

### 3. Repetição controlada

Adicionar uma terceira placa apenas se o teste ponto a ponto justificar. Validar deduplicação, TTL, contenção, rotas e ACK fim a fim em topologia de 2 saltos. Medir colisões sob tráfego representativo.

**Saída:** demonstração de multi-hop repetível e limites de densidade/tráfego conhecidos.

### 4. Sensor e consumo

Integrar primeiro uma fonte de distância simulada; depois sensor real. Medir o ciclo completo, incluindo partida do sensor, rádio, tentativas e sono. Dimensionar bateria/painel com medidas e margem para clima, perdas e envelhecimento.

**Saída:** perfil de consumo medido e estimativa de autonomia documentada.

### 5. Gateway e backend mínimos

Começar em uma máquina de desenvolvimento com broker MQTT local e um serviço de ingestão. Persistir telemetria em PostgreSQL simples quando necessário; adiar Redis, TimescaleDB, Kubernetes e serviços pagos até métricas mostrarem necessidade. Usar dados sintéticos para o frontend e testes de fluxo.

**Saída:** percurso local sensor simulado → gateway simulado → MQTT → ingestão → consulta.

### 6. Comando seguro

Simular antes de acionar qualquer carga. Implementar expiração, autenticação, deduplicação/idempotência, confirmação de estado real e timeout fail-safe no próprio nó. Testar falhas de rede, reinício e comando repetido. Qualquer ensaio elétrico real deve usar interface isolada e carga de bancada segura.

**Saída:** especificação e demonstração do fluxo de comando seguro, sem depender da nuvem para desligar.

## Estratégia de economia

- Não comprar repetidores solares, antenas de alto ganho, modem celular ou infraestrutura cloud para o primeiro ciclo.
- Reutilizar placas e computador existentes; simular os papéis de nó e gateway quando possível.
- Comprar hardware em incrementos: inicialmente 2 rádios para enlace, terceiro somente para provar salto.
- Evitar Redis e TimescaleDB no MVP inicial; medir volume antes de adicionar.
- Registrar custo por nó, energia, serviço e manutenção, não só custo de aquisição.

## Riscos e pontos de revisão da arquitetura

- **Rádio:** LoRa proprietário em malha não é LoRaWAN. O projeto assume responsabilidade por acesso ao meio, endereçamento, segurança, atualizações e comportamento em congestionamento.
- **Frame:** a tabela original mistura comprimento do payload com intervalos indexados por `N`; formalizar bytes exatos e máximo total antes de criar structs `packed`.
- **Confiabilidade:** flooding com CAD e atraso aleatório não garante ausência de colisões nem entrega. Definir limites de nós/tráfego e instrumentar perda/retry.
- **Segurança:** CRC detecta erro acidental, mas não autentica origem nem protege contra replay. Comando de bomba precisa de autenticação e sequência/expiração.
- **Energia:** estimativas de autonomia dependem do ciclo de rádio real, painel, bateria, temperatura e dias sem sol. Medir antes de prometer 5–7 dias.
- **Segurança física:** relé não substitui contatora, proteção elétrica e intertravamentos. A política segura deve ser definida por equipamento e risco operacional.

## Critério para sair do laboratório

Avançar para piloto de campo apenas quando houver resultados repetíveis de rádio, consumo e fail-safe; plano de instalação/retorno; provisionamento e atualização seguros; e conformidade regulatória confirmada para o hardware e operação escolhidos.
