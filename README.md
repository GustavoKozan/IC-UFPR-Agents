# Integração de Sistemas Multiagentes baseados em LLMs ao Ciclo de Vida de Desenvolvimento de Software

Repositório destinado à documentação, implementação e análise experimental das atividades desenvolvidas no âmbito da pesquisa de **Iniciação Científica (IC)** sobre a aplicação de **Sistemas Multiagentes (MAS)** e Modelos de Linguagem (LLMs e SLMs) às etapas do **Ciclo de Vida de Desenvolvimento de Software (SDLC)**.

---

## 📌 Contexto e Objetivos

O avanço de modelos de linguagem generativos abriu novos paradigmas para a automação na Engenharia de Software. No entanto, o uso isolado de modelos monolíticos frequentemente enfrenta desafios como alucinações sintáticas/semânticas, falta de contexto do projeto e baixa aderência a requisitos complexos.

Esta pesquisa investiga como arquiteturas multiagentes autônomas e colaborativas podem ser integradas ao SDLC para atuar de ponta a ponta — desde a análise de requisitos até a implementação, validação e revisão de código.

---

## 🔬 Fase 1: Protótipo de um Agente para Geração de Código

A etapa inicial concentra-se no desenvolvimento de um agente individual para geração de código e na condução de um estudo empírico comparativo entre diferentes categorias de modelos e técnicas de engenharia de prompt[cite: 1].

### 1. Modelos em Avaliação
A análise engloba tanto modelos de pequeno porte para inferência local (*SLMs - Small Language Models*) quanto modelos proprietários em larga escala (*LLMs - Large Language Models*)[cite: 1]:
* **SLMs (Execução Local / Ollama):** LLaMA 7B, LLaMA 13B, Code Llama, Qwen2.5-Coder 7B, Gemma, entre outros[cite: 1].
* **LLMs (Serviços em Nuvem / API):** Família ChatGPT (OpenAI), Gemini (Google), Claude (Anthropic), DeepSeek, Kimi K3, Nemotron, entre outros[cite: 1].

### 2. Framework e Benchmark
* **Construção do Agente:** Desenvolvimento do protótipo com **LangChain** para padronização de cadeias de chamada e execução[cite: 1].
* **Benchmark de Avaliação:** **HumanEval** (OpenAI), composto por 164 desafios de programação em Python validados por suítes formais de testes unitários[cite: 1].

### 3. Estratégias de Prompting em Estudo
Para mensurar a sensibilidade e a robustez dos modelos, são avaliadas as seguintes abordagens[cite: 1]:
* **Zero-Shot:** Envio estrito apenas da assinatura da função e de sua *docstring*[cite: 1].
* **One-Shot:** Envio da tarefa acompanhada de 1 exemplo resolvido[cite: 1].
* **Few-Shot (2, 5 e 10 exemplos):** Envio da tarefa com múltiplos exemplos resolvidos[cite: 1]. Todos os modelos recebem rigorosamente os mesmos exemplos e na mesma ordem[cite: 1].
* **Chain-of-Thought (CoT):** Inclusão de instruções explícitas para raciocínio analítico antes da geração do código[cite: 1]:
  * **Possibilidade A (Raciocínio Interno):**
    1. *Reason carefully about the problem internally before producing the solution.*[cite: 1]
    2. *Return only the final Python code.*[cite: 1]
  * **Possibilidade B (Raciocínio Estruturado Passo a Passo):**
    1. *Analyze the problem step by step before writing the code:*[cite: 1]
    2. *Identify the requirements.*[cite: 1]
    3. *Consider edge cases.*[cite: 1]
    4. *Develop an algorithm.*[cite: 1]
    5. *Write the Python implementation.*[cite: 1]
    6. *Verify the implementation against the specification.*[cite: 1]
    7. *Then provide the final code.*[cite: 1]
* **Few-Shot + CoT:** Combinação de exemplos práticos contendo o raciocínio intermediário passo a passo antes da solução final[cite: 1].

---

## 📊 Resultados Preliminares: Etapa Zero-Shot

Os testes iniciais foram consolidados a partir da execução integral dos 164 problemas do *HumanEval* sob a estratégia **Zero-Shot** (apenas função + *docstring*).

### Tabela Comparativa de Desempenho

| Modelo | Categoria | Pass@1 (%) | Sucesso | Falha | Latência Média (s) | Tokens Médios / Resposta | Complexidade Ciclomática (CC) | Índice de Manutenibilidade (MI) |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Nemotron 3 Ultra 550B** | Cloud / API | **90,85%** | 149 / 164 | 15 / 164 | 15,06 s | 877,4 | 3,76 | 82,00 |
| **Gemini 3.5 Flash Lite** | Cloud / API | **88,41%** | 145 / 164 | 19 / 164 | **1,24 s** | 138,7 | 3,55 | 82,38 |
| **Qwen2.5-Coder 7B** | Local (Ollama) | **86,59%** | 142 / 164 | 22 / 164 | 4,70 s | 198,8 | 3,33 | **87,96** |
| **GPT OSS 120B** | Cloud / API | **84,76%** | 139 / 164 | 25 / 164 | 1,09 s | 669,6 | 3,65 | 77,88 |
| **Kimi K3** | Cloud / API | **81,71%** | 134 / 164 | 30 / 164 | 20,50 s | 591,4 | 2,89 | 70,38 |
| **Qwen2.5 7B** | Local (Ollama) | **74,39%** | 122 / 164 | 42 / 164 | 5,66 s | 274,1 | 3,52 | 85,90 |
| **Gemma 3 4B** | Local (Ollama) | **68,90%** | 113 / 164 | 51 / 164 | 4,17 s | 228,2 | 4,04 | 87,50 |
| **Llama 3.1 8B** | Local (Ollama) | **68,90%** | 113 / 164 | 51 / 164 | 6,63 s | 325,7 | 3,41 | 80,39 |

### Destaques dos Primeiros Testes:
1. **Viabilidade dos SLMs Especializados:** O `Qwen2.5-Coder 7B` local alcançou 86,59% de $Pass@1$, superando modelos gerais em nuvem de porte significativamente superior e apresentando o melhor Índice de Manutenibilidade Médio (87,96).
2. **Trade-off Latência vs. Eficácia:** Modelos como o `Gemini 3.5 Flash Lite` combinam alta taxa de assertividade (88,41%) com tempos de resposta expressivamente baixos (~1,24 s), perfil ideal para nós de iteração rápida em agentes de desenvolvimento.
3. **Complexidade Estrutural:** Todos os modelos mantiveram a Complexidade Ciclomática média em níveis saudáveis ($< 5$), refletindo implementações concisas e diretas.

---

## 📁 Organização do Repositório

```plaintext
.
├── data/
│   ├── raw/                               # Saídas brutas de inferência dos modelos
│   └── Resultados_do_Benchmark_HumanEval_ZeroShot(LLM-SLM).xlsx  # Dados consolidados
├── src/
│   ├── agent.py                           # Definição do agente com LangChain
│   ├── prompt_templates.py                # Templates (Zero-shot, Few-shot, CoT)
│   └── evaluator.py                       # Integração com o avaliador HumanEval
├── requirements.txt                       # Dependências do projeto
└── README.md
