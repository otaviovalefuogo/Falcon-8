# Processador Falcon-8 | Arquitetura RISC em Pipeline

Processador de 8 bits baseado na arquitetura RISC, projetado com pipeline otimizado de 3 estágios para controle crítico de sistemas automotivos de frenagem regenerativa.

▶️ **[Clique aqui para ver a Demonstração em Vídeo no YouTube](https://youtu.be/4KRtq0vRjGM)**

---

## 📋 Visão Geral
O **Falcon-8** é um microprocessador de 8 bits com arquitetura RISC (Reduced Instruction Set Computer) e organização baseada na estrutura de Harvard. O projeto foi concebido para atender às rigorosas demandas de sistemas embarcados da **BYD**, especificamente no gerenciamento e controle de **sistemas de frenagem inteligente (SFI) e regenerativa**.

O objetivo principal da arquitetura é fornecer alto rendimento de processamento determinístico e baixa latência de resposta a eventos, garantindo máxima eficiência e estabilidade na atuação do controle de frenagem do veículo, superando a capacidade de resposta humana em até 300 vezes.

**Destaques do Projeto:**
*   **Arquitetura de Dados:** 8 bits com barramentos de memória de instruções e dados dedicados (Harvard).
*   **Execução em Pipeline:** 3 estágios independentes, permitindo paralelismo em nível de instrução.
*   **Foco Automotivo:** Resposta rápida para cálculo de frenagem e evasão de obstáculos.

---

##  Especificação da ISA (Instruction Set Architecture)
A ISA do Falcon-8 é composta por instruções fixas de 16 bits, decodificadas através do Opcode, e divididas em 5 formatos principais:

*   **Tipo R (Registrador):** Operações aritméticas/lógicas entre registradores (ex: `ADD`, `SUB`). O Opcode é `0000` e a operação exata é decidida pelo campo `funct`.
*   **Tipo I (Aritmético/Load/Shift):** Operações com valores constantes (`ADDI`), carga de dados da RAM (`LW`), e deslocamentos (`SLLI`).
*   **Tipo S (Store):** Armazenamento de dados do banco de registradores para a memória RAM (`SW`).
*   **Tipo B (Branches Condicionais):** Instruções de desvio de fluxo baseadas em comparações da ULA (`BEQ`, `BNE`, `BLT`).
*   **Tipo J (Jump):** Saltos incondicionais absolutos (`JAL`).

---

## Arquitetura do Datapath (Pipeline de 3 Estágios)
O caminho de dados (Datapath) do Falcon-8 foi dividido em duas paredes de registradores, permitindo o paralelismo em nível de instrução em 3 estágios:

1.  **IF (Busca / Instruction Fetch):** Recuperação da instrução na ROM de instruções e atualização do Program Counter (PC).
2.  **ID/EX (Decodificação e Execução):** Leitura do Banco de Registradores, extração da Unidade de Controle, Geração de Imediatos, cálculo pela ULA e resolução do Somador de Endereços de Salto.
3.  **WB (Gravação / Write Back):** Acesso à memória RAM de dados e escrita dos resultados de volta no Banco de Registradores.

---

## ⚡ Tratamento de Conflitos (Hazards)
O maior desafio de um pipeline é garantir a integridade dos dados. O Falcon-8 implementa três soluções avançadas em hardware:

*   **Dependências de Dados e Forwarding (Adiantamento):** Um conjunto de MUXes antes da ULA monitora o estágio de Gravação e "adianta" o dado diretamente para a entrada da ULA se houver dependência, resolvendo o *hazard* instantaneamente sem parar o processador.
*   **Load-Use Hazard e Stall (Congelamento):** Quando uma instrução depende de um `LW` (leitura de memória), a Unidade de Detecção ativa um sinal de `STALL`, congelando o PC e a Parede 1 por um ciclo, injetando uma bolha (NOP) no controle.
*   **Control Hazards e Hardware Flush (Descarte):** Em instruções de salto (`BEQ`, `JAL`), um sinal combinacional de `FLUSH` ativa um MUX na entrada da Parede 1, substituindo a instrução invasora buscada indevidamente por `0000` (Bolha).

---

## Análise de Desempenho Matemático
A transição da arquitetura monociclo clássica para a arquitetura em pipeline de 3 estágios no Falcon-8 resultou em um ganho exponencial de desempenho.

**Tempos Estimados (Caminho Crítico):**
*   **Tempo Monociclo:** ROM (200ps) + RegRead (100ps) + MUX (30ps) + ULA (200ps) + RAM (200ps) + MUX (30ps) = **760ps**.
*   **Tempo Pipeline:** Limitado pelo estágio mais lento (ID/EX) = RegRead (100ps) + MUX (30ps) + ULA (200ps) + Setup da Parede (20ps) = **350ps**.

**O Fator de Aceleração (Speedup):**
> Ganho Desempenho = Tempo Monociclo / Tempo Pipeline
> Ganho Desempenho = 760 / 350 = **2.17x**

**Conclusão:** A matemática comprova que o Falcon-8 em pipeline opera **2.17 vezes mais rápido** que sua versão monociclo. Em um cenário real de direção autônoma (SFI), essa drástica redução no tempo de ciclo garante a velocidade de reação extrema necessária para parar o veículo e salvar vidas.

---
*Projeto acadêmico desenvolvido por Otávio Campos Valefuogo para o curso de Ciência da Computação - Instituto Federal Catarinense.*