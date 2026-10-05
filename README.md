# Integração de Sistemas Multiagentes baseados em LLMs ao Ciclo de Vida de Desenvolvimento de Software

Repositório institucional e técnico dedicado à documentação, implementação e validação experimental das atividades desenvolvidas no âmbito da investigação de **Iniciação Científica (IC)** sobre a aplicação de **Sistemas Multiagentes (MAS - Multi-Agent Systems)** baseados em Modelos de Linguagem (*Large and Small Language Models* - LLMs/SLMs) no **Ciclo de Vida de Desenvolvimento de Software (SDLC - Software Development Life Cycle)**.

---

## 📌 Contextualização e Objetivos

A introdução de modelos de linguagem generativos estabeleceu novos paradigmas de automatização na Engenharia de Software. Não obstante o potencial demonstrado, a utilização isolada de modelos monolíticos apresenta limitações críticas, tais como a emergência de alucinações sintáticas e semânticas, a perda de contexto arquitetural alargado e a vulnerabilidade na adesão a requisitos funcionais complexos.

O objetivo central desta investigação consiste em avaliar sistematicamente como arquiteturas colaborativas multiagentes podem mitigar tais limitações, integrando-se de forma coordenada e modular às diversas etapas do SDLC — desde a elicitação e especificação de requisitos até à síntese algorítmica, verificação formal por testes unitários e auditoria de código.

---

## 🔬 Fase 1: Avaliação Empírica de Agentes Unimodais para Geração de Código

A fase preliminar da investigação concentra-se na conceção de um agente prototípico e na condução de um estudo empírico comparativo entre distintas famílias de modelos e estratégias formais de engenharia de *prompting*.

### 1. Modelos Submetidos a Avaliação
A amostragem engloba modelos compactos destinados a inferência local (*SLMs - Small Language Models*) e modelos proprietários de larga escala operados em nuvem (*LLMs - Large Language Models*):
* **SLMs (Inferência Local via Ollama):** LLaMA 7B, LLaMA 13B, Code Llama, Qwen2.5-Coder 7B, Gemma 3 4B, entre outros (priorizando a privacidade de dados, soberania da informação e ausência de custos transacionais por *token*).
* **LLMs (Serviços Gerenciados em Nuvem / API):** Ecossistemas OpenAI (ChatGPT/GPT OSS), Google (Gemini), Anthropic (Claude), DeepSeek, Kimi K3 e Nemotron.

### 2. Quadro Metodológico e Protocolo de Avaliação
* **Arquitetura do Agente:** Implementação modular baseada no ecossistema **LangChain**, assegurando a rastreabilidade e padronização dos fluxos de chamada e execução determinística.
* **Benchmark de Referência:** **HumanEval** (OpenAI), constituído por 164 problemas algorítmicos em Python, validados por asserções e suítes formais de testes funcionais.

### 3. Estratégias de *Prompting* Investigadas
Com o intuito de caracterizar a sensibilidade e a capacidade de inferência dos modelos, são avaliadas as seguintes diretivas experimentais:
* **Zero-Shot:** Disponibilização estrita da assinatura da função acompanhada da respetiva especificação formal (*docstring*).
* **One-Shot:** Inclusão do enunciado da tarefa acompanhado de um caso exemplar com resolução válida.
* **Few-Shot ($k \in \{2, 5, 10\}$):** Inclusão de múltiplos exemplos resolvidos contextualmente antes da tarefa alvo[cite: 1]. Todos os modelos recebem rigorosamente o mesmo conjunto e ordem de exemplos, garantindo a reprodutibilidade do ensaio.
* **Cadeia de Raciocínio (Chain-of-Thought - CoT):** Instruções explícitas destinadas a induzir reflexão analítica prévia à emissão do artefacto final[cite: 1]:
  * **Variante A (Deliberação Interna):**
    1. *Reason carefully about the problem internally before producing the solution.*
    2. *Return only the final Python code.*
  * **Variante B (Decomposição Analítica Estruturada):**
    1. *Analyze the problem step by step before writing the code:*
    2. *Identify the requirements.*
    3. *Consider edge cases.*
    4. *Develop an algorithm.*
    5. *Write the Python implementation.*
    6. *Verify the implementation against the specification.*
    7. *Then provide the final code.*
* **Few-Shot combinada com CoT:** Associação de casos exemplares com o mapeamento explícito do raciocínio analítico passo a passo que antecede a solução de código.

---

## 📊 Resultados Preliminares: Protocolo Experimental *Zero-Shot*

Os dados a seguir compilam a execução exaustiva das 164 tarefas do *benchmark* **HumanEval** sob o protocolo **Zero-Shot** (avaliação direta sem indução prévia).

### Tabela Comparativa de Desempenho

| Modelo Avaliado | Ambiente / Categoria | Pass@1 (%) | Sucesso (Aprovadas) | Falhas | Latência Média (s) | Tokens Médios / Resposta | Complexidade Ciclomática (CC) | Índice de Manutenibilidade (MI) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Nemotron 3 Ultra 550B** | Cloud / API | **90,85%** | 149 / 164 | 15 / 164 | 15,06 s | 877,4 | 3,76 | 82,00 |
| **Gemini 3.5 Flash Lite** | Cloud / API | **88,41%** | 145 / 164 | 19 / 164 | **1,24 s** | 138,7 | 3,55 | 82,38 |
| **Qwen2.5-Coder 7B** | Local (Ollama) | **86,59%** | 142 / 164 | 22 / 164 | 4,70 s | 198,8 | 3,33 | **87,96** |
| **GPT OSS 120B** | Cloud / API | **84,76%** | 139 / 164 | 25 / 164 | 1,09 s | 669,6 | 3,65 | 77,88 |
| **Kimi K3** | Cloud / API | **81,71%** | 134 / 164 | 30 / 164 | 20,50 s | 591,4 | 2,89 | 70,38 |
| **Qwen2.5 7B** | Local (Ollama) | **74,39%** | 122 / 164 | 42 / 164 | 5,66 s | 274,1 | 3,52 | 85,90 |
| **Gemma 3 4B** | Local (Ollama) | **68,90%** | 113 / 164 | 51 / 164 | 4,17 s | 228,2 | 4,04 | 87,50 |
| **Llama 3.1 8B** | Local (Ollama) | **68,90%** | 113 / 164 | 51 / 164 | 6,63 s | 325,7 | 3,41 | 80,39 |

### Análise e Discussão dos Resultados
1. **Eficácia de SLMs Especializados:** O modelo local `Qwen2.5-Coder 7B` atingiu uma taxa de aprovação direta ($Pass@1$) de **86,59%**, superando modelos proprietários de maior escala parametral e registando o maior Índice de Manutenibilidade Médio (**87,96**). Isto evidencia que a especialização de domínio compensa eficazmente a redução na contagem de parâmetros.
2. **Compromisso entre Latência e Assertividade:** O `Gemini 3.5 Flash Lite` demonstrou um perfil altamente favorável, associando reduzida latência de inferência (1,24 s) a um $Pass@1$ competitivo (88,41%), o que o qualifica como forte candidato para agentes que demandam validações cíclicas e rápidas no pipeline.
3. **Métricas de Qualidade de Software:** Todos os modelos avaliados mantiveram a Complexidade Ciclomática média em níveis recomendáveis ($CC < 5$), indicando a geração de soluções algorítmicas concisas, modulares e de baixa densidade de desvios condicionais.

---

## 📁 Estrutura do Repositório

```plaintext
.
├── data/
│   ├── raw/                                                      # Registo bruto das respostas das inferências
│   └── Resultados_do_Benchmark_HumanEval_ZeroShot(LLM-SLM).xlsx  # Dados analíticos e consolidados
├── src/
│   ├── agent.py                                                  # Instanciação do agente e cadeias de chamada
│   ├── prompt_templates.py                                       # Especificação dos templates de inferência
│   └── evaluator.py                                              # Módulo de submissão e validação no HumanEval
├── requirements.txt                                              # Dependências do ecossistema de testes
└── README.md
