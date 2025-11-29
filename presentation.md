# Abordagem Multiagente na combinação de ações de patrulhamento preventivo e de atendimento de chamadas policiais

**Autores:** Moacir Almeida Simões Júnior, Tobias de Abreu Kuse
**Afiliação:** Instituto de Informática, Universidade Federal do Rio Grande do Sul

---

# Introdução

## O Problema do Patrulhamento Policial
- **Prevenção:** Patrulhamento para inibir crimes.
- **Resposta:** Atendimento imediato a chamadas.
- **Limitações:** Alocação de recursos frequentemente *ad hoc*, abordagens separadas.

## Desafios
- Incerteza (chegadas aleatórias de chamadas)
- Dinâmica do ambiente (risco variável no tempo e espaço)
- Decisões sequenciais (onde patrulhar, qual viatura despachar)

## Nossa Proposta
- **Aprendizado por Reforço Multiagente (MARL)**
- Cada patrulha é um agente que aprende a escolher o próximo vértice a patrulhar.
- Despachante segue regras operacionais para envio de patrulhas.

---

# Objetivos

## Dois Objetivos Principais
1.  **Minimizar o tempo de resposta** da patrulha policial a chamadas de emergência.
2.  **Maximizar a cobertura/permanência** em áreas altamente afetadas pela criminalidade (*hotspots*).

## Otimização Simultânea com MARL
- Aprendizado por **TD Learning** com **Experience Replay** e **Deep Q-Learning**.
- Geração de uma política conjunta que tenta otimizar ambos os objetivos.

---

# Revisão da Literatura: Patrulhamento

## Patrulhamento Preventivo de Hotspots
- O crime se concentra em locais específicos (*hotspots*).
- Foco em *hotspots* é eficiente para redução do crime.
- **Teoria das Atividades Rotineiras:** Convergência de criminosos, alvos e ausência de guardiões.
- Algoritmos preditivos para identificar futuros eventos criminais.

## Patrulhamento de Resposta a Chamadas
- Despacho de patrulhas: complexidade na alocação dinâmica de recursos.
- 40-60% do tempo de patrulha dedicado a chamados.
- Resposta rápida: aumenta chances de prisão e coleta de provas.
- Sistema de patrulhas como um sistema de filas multi-servidor.
- Sistemas de prioridade: reduzem espera para alta prioridade, aumentam para baixa.

---

# Revisão da Literatura: Aprendizado por Reforço

## RL/MARL como Solução
- Abordagens tradicionais tratam prevenção e resposta de forma fragmentada.
- RL/MARL: alternativa promissora para tratamento conjunto.
- Capacidade de aprender políticas ótimas em ambientes dinâmicos, estocásticos e multi-objetivo.

## Trabalhos Relacionados
- **Joe et al. (2022):** DRL para despacho policial, equilibrando resposta e presença preventiva.
- **Repasky et al. (2024):** MARL distribuído para integração de patrulhamento e despacho.
- **Palma et al. (2025):** POMDP descentralizado para patrulhamento urbano sob observabilidade parcial.
- **Chen et al. (2013):** Abordagem puramente preventiva baseada em MDP, métrica SLF.

---

# Metodologia: Visão Geral

## Validação
- Modelo validado com dados reais de uma unidade policial urbana (Porto Alegre).

## Estrutura Espacial
- Área dividida em **grades hexagonais** (250m de lado).
- Representada como um **grafo indireto**.
- Rotas e custos de deslocamento calculados via **Bing Maps API**.

## Identificação de Hotspots
- Modelo preditivo (XGBoost) para classificar *hotspots* com base no risco de ocorrência de eventos criminais.
- Variáveis: estruturas urbanas, temporais, crimes acumulados, dados climáticos.
- Valor de probabilidade ($W_i$) atribuído a cada *hotspot* para as próximas duas horas.

---

# Metodologia: Estratégias Comparadas

## Para Avaliar o Método Proposto
1.  **Otimização por Colônia de Formigas (ACO):** Patrulhas empregadas nos pontos de maior risco com paradas determinadas.
2.  **Aleatório:** Patrulhamento randômico com paradas variáveis (1 a 15 minutos) nos *hotspots*.
3.  **MARL (Nossa Proposta):** Aprendizado por Reforço Multiagente.

---

# Metodologia: Modelo MARL Proposto

## Formulação
- Problema formulado como um **Markov Decision Process (MDP) multiagente cooperativo**.
- $\mathcal{M} = \langle \mathcal{A}, \mathcal{S}, \mathcal{U}, P, R, \gamma \rangle$

## Observação Local ($o_i$)
- Cada agente observa uma projeção local do estado global (vetor de 19 dimensões).
- Inclui: tempo, dia, filas de chamadas, ociosidade média, deslocamento médio, companhia, tipo de patrulha, posição, risco atual e risco top da companhia.

## Simulação de Eventos Discretos
- Interage com a simulação em passos de $\Delta t = 1$ minuto.
- Etapas: definição de destino, inserção de incidentes em fila, despacho, atualização de estados das patrulhas.

## Função de Recompensa Global Compartilhada ($r_t$)
- Baseada em variações (deltas) de métricas agregadas:
    - Ociosidade acumulada nos *hotspots* ($\Delta \text{idle}_t$)
    - Tempo de resposta acumulado ($\Delta \text{resp}_t$)
    - Medida ponderada de chamadas em fila ($\Delta \text{backlog}_t$)
    - Número de chamados atendidos ($\Delta \text{atendidos}_t$)

---

# Metodologia: Métricas de Desempenho

## Ociosidade de Hotspots ($\overline{I_{i}}(t)$)
- Tempo durante o qual um *hotspot* permanece descoberto (sem patrulhas).
- Indicador de presença preventiva.

## Tempo de Resposta
- Intervalo entre o registro de um incidente e a chegada da equipe policial.
- Indicador-chave de eficiência na resposta a emergências.

---

# Validação: Setup Experimental

## Dados
- Caso real: 9º Batalhão de Polícia Militar, Porto Alegre.
- Período: 2015-2021 (428.214 eventos policiais).
- Grid hexagonal: 250m de lado, 474 grades (157 com registros criminais).
- Dados de estruturas urbanas (Open Street Maps) e climáticos (INMET).

## Predição de Hotspots
- **XGBoost** para análise preditiva das ocorrências.
- Dados de treinamento e teste (80/20).

## Parâmetros de Simulação
- Duração do turno de patrulha, tempo de permanência em *hotspots*.
- Variáveis aleatórias: alocação de chamada (Poisson), intervalo entre chamadas (Poisson), prioridade, tempo de atendimento (Exponencial).
- Parâmetros ACO: $\alpha=0, \beta=1, \theta=3$.

## Modelo MARL
- Função $Q(o_i, u_i; \theta)$ aproximada por **Dueling DQN** (MLP).
- Hiperparâmetros otimizados (gamma, lr, batch_size, buffer_size, eps_decay, etc.).

---

# Resultados: Comparação Geral

## Métricas de Desempenho (Tabela 4)
| Método    | Ociosidade Média | Fila Média | Deslocamento Médio | Tempo Resposta Médio |
| :-------- | :--------------- | :--------- | :----------------- | :------------------- |
| ALEATORIO | 5104.40          | 43.33      | 7.06               | 50.39                |
| BAPS      | 4477.52          | 25.53      | 6.93               | 32.47                |
| MARL      | 4895.88          | 35.90      | 7.00               | 41.05                |

## Discussão
- **BAPS (ACO):** Menor ociosidade média, bom tempo de resposta.
- **MARL:** Desempenho competitivo, equilibrando os objetivos.
- **Aleatório:** Pior desempenho geral.

---

# Resultados: Filas por Prioridade

## Tempo Médio na Fila de Espera (Tabela 6)
| Métrica                       | Cobertura Gulosa | p-Medianas | ACO      | Aleatório |
| :---------------------------- | :--------------- | :--------- | :------- | :-------- |
| Média Fila -- Prioridade 1 (min) | 2,73             | 2,77       | 3,91     | 2,46      |
| Média Fila -- Prioridade 2 (min) | 49,28            | 58,00      | 54,17    | 63,15     |
| Média Fila -- Prioridade 3 (min) | 168,65           | 238,96     | 173,25   | 216,18    |

## Discussão
- **Prioridade 1:** Aleatório e Cobertura Gulosa com melhores tempos.
- **Prioridade 2:** Cobertura Gulosa com menor tempo.
- **Prioridade 3:** Cobertura Gulosa e ACO com melhores tempos.
- **Cobertura Gulosa:** Mais equilibrada entre os níveis de prioridade.
- **p-Medianas:** Favorece chamadas críticas, mas pior para menos urgentes.

---

# Conclusão

## Principais Achados
- **Cobertura Gulosa de Hotspots:** Desempenho mais equilibrado entre prioridades.
- **Otimização por Colônia de Formigas (ACO):** Eficaz na redução da ociosidade dos pontos críticos.
- **p-Medianas:** Estabilidade e bom desempenho em alta prioridade, mas menor responsividade em chamados menos urgentes.

## Avanço Principal do Estudo
- **Integração simultânea** de estratégias de prevenção ao crime (patrulhamento orientado por risco) com a resposta reativa a chamados emergenciais.

## Implicações
- A combinação entre prevenção e resposta exige modelos dinâmicos e adaptativos.
- Estratégias que integram coordenação entre patrulhas e atualizações contínuas do risco tendem a oferecer melhores resultados.
- A abordagem proposta é uma ferramenta promissora para apoio à tomada de decisão estratégica no policiamento urbano.
