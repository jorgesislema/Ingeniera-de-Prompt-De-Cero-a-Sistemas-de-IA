---
title: "11. Recursos y Bibliografía"
module: "11-RECURSOS-Y-BIBLIOGRAFIA"
order: 11
difficulty: "referencia"
estimated_time: "referencia continua"
prerequisites: []
tags: ["recursos", "bibliografia", "comunidades", "herramientas", "certificaciones", "planes-aprendizaje", "papers", "benchmarks"]
version: "1.0.0"
last_updated: "2026-09-27"
learning_objectives:
  - "Acceder a 100+ recursos curados con anotación de dificultad y tiempo"
  - "Identificar comunidades activas y canales de aprendizaje continuo"
  - "Evaluar certificaciones y cursos por ROI real"
  - "Diseñar plan de aprendizaje personalizado 30/60/90 días"
  - "Mantenerse actualizado con fuentes primarias y secundarias confiables"
---

# 11. Recursos y Bibliografía

> **Principio:** "No necesitas leer todo. Necesitas leer LO CORRECTO, en el ORDEN CORRECTO, con PROPÓSITO CLARO."

Esta es tu **biblioteca de referencia viva**. No es para leer de corrido; es para consultar cuando necesitas: "¿qué paper leer sobre X?", "¿qué herramienta usar para Y?", "¿qué comunidad preguntar sobre Z?".

---

## 11.1 Papers Fundamentales (Lectura Obligatoria)

### Arquitectura y Fundamentos

| Paper | Año | Por Qué Importa | Dificultad | Tiempo |
|-------|-----|-----------------|------------|--------|
| **Attention Is All You Need** (Vaswani et al.) | 2017 | Arquitectura Transformer original. Base de todo LLM moderno. | Alta | 2-3h |
| **BERT: Pre-training of Deep Bidirectional Transformers** (Devlin et al.) | 2019 | Bidireccionalidad, pre-training objectives. Base de embedding models. | Alta | 2h |
| **Language Models are Few-Shot Learners** (Brown et al., GPT-3) | 2020 | Scaling laws, in-context learning, few-shot emergence. | Media | 1.5h |
| **Training Compute-Optimal Large Language Models** (Hoffmann et al., Chinchilla) | 2022 | Leyes de escalado óptimas: data vs params. Define frontier actual. | Media | 1h |
| **Scaling Laws for Neural Language Models** (Kaplan et al.) | 2020 | Leyes de poder: loss ~ compute^(-0.07). Fundamento económico LLM. | Alta | 1.5h |

### Tokenización y Representación

| Paper | Año | Tema Clave |
|-------|-----|------------|
| **BPE (Sennrich et al.)** | 2016 | Subword tokenization estándar |
| **SentencePiece (Kudo & Richardson)** | 2018 | Tokenization unsupervised, multilingüe |
| **Token-Free Language Models** (Clark et al.) | 2024 | Byte-level, character-level alternatives |

### Atención y Mecanismos

| Paper | Año | Aporte |
|-------|-----|--------|
| **FlashAttention** (Dao et al.) | 2022 | IO-aware attention, 2-4x speedup, memory efficient |
| **FlashAttention-2** (Dao) | 2023 | Better parallelism, head dimensions |
| **Ring Attention** (Liu et al.) | 2023 | Context scaling across GPUs |
| **Mamba** (Gu & Dao) | 2023 | SSM alternative to attention, linear scaling |

### MoE y Arquitecturas Eficientes

| Paper | Año | Aporte |
|-------|-----|--------|
| **Switch Transformers** (Fedus et al.) | 2021 | MoE scaling to trillion params |
| **GLaM** (Du et al.) | 2022 | Generalist Language Model, MoE efficient |
| **DeepSeekMoE** (DeepSeek-AI) | 2024 | Fine-grained experts, shared experts, load balancing |
| **Mixtral 8x7B** (Jiang et al.) | 2024 | Sparse MoE open source, strong performance |

### Razonamiento y Técnicas Avanzadas

| Paper | Año | Técnica |
|-------|-----|---------|
| **Chain-of-Thought** (Wei et al.) | 2022 | CoT prompting emergence |
| **Self-Consistency** (Wang et al.) | 2022 | Majority voting over CoT paths |
| **Tree of Thoughts** (Yao et al.) | 2023 | Deliberate search over reasoning trees |
| **ReAct** (Yao et al.) | 2023 | Reasoning + Acting loop |
| **Reflexion** (Shinn et al.) | 2023 | Self-reflection + memory |
| **DeepSeekMath / GRPO** (DeepSeek-AI) | 2024 | Math reasoning + Group Relative Policy Optimization |
| **AgentBench** (Liu et al.) | 2023 | Evaluating LLM agents |
| **ToolBench** (Xu et al.) | 2023 | Tool use evaluation |

### RAG y Contexto

| Paper | Año | Aporte |
|-------|-----|--------|
| **Retrieval-Augmented Generation** (Lewis et al.) | 2020 | RAG original |
| **REALM** (Guu et al.) | 2020 | Retrieval-augmented LM pre-training |
| **LongLoRA** (Chen et al.) | 2023 | Efficient long-context fine-tuning |
| **LongLoRA / YaRN** (Peng et al.) | 2023 | Context extension via rope scaling |
| **Lost in the Middle** (Liu et al.) | 2023 | Context position bias quantification |

### Seguridad y Alineamiento

| Paper | Año | Tema |
|-------|-----|------|
| **Constitutional AI** (Bai et al.) | 2022 | Self-supervision for harmlessness |
| **RLHF / InstructGPT** (Ouyang et al.) | 2022 | RLHF pipeline |
| **DPO** (Rafailov et al.) | 2023 | Direct Preference Optimization |
| **Universal Adversarial Prompts** (Wallace et al.) | 2019 | Adversarial prompt discovery |
| **Prompt Injection** (Greshake et al.) | 2023 | Indirect prompt injection taxonomy |

---

## 11.2 Benchmarks y Evaluación (Referencia Rápida)

### Benchmarks Clásicos

| Benchmark | Qué Mide | Estado 2026 | Modelos Top (Sep 2026) |
|-----------|----------|-------------|------------------------|
| **MMLU** | Knowledge + reasoning multi-task | Saturado (>90% top models) | GPT-4o ~92%, Claude 3.5 Opus ~91% |
| **GPQA** | Graduate-level science reasoning | Activo, desafiante | GPT-4o ~65%, Claude 3.5 ~68% |
| **HumanEval / MBPP** | Code generation | Saturando | GPT-4o ~92%, DeepSeek-Coder-V2 ~94% |
| **GSM8K / MATH** | Mathematical reasoning | Activo | GPT-4o ~95% GSM8K, ~60% MATH |
| **HELM** | Holistic evaluation (robustez, fairness, efficiency) | Referencia continua | Stanford CRFM mantiene leaderboard |

### Benchmarks de Agentes y Herramientas

| Benchmark | Qué Mide | Estado |
|-----------|----------|--------|
| **AgentBench** | Web, OS, DB, KG, digital assistant | Referencia agentes |
| **ToolBench** | 16K+ APIs, tool use + planning | Referencia tool use |
| **APIBench / API-Bank** | API calling accuracy | Activo |
| **WebShop / WebArena** | Web navigation + purchase | Web agents |
| **SWE-bench** | Real GitHub issue resolution | Software engineering agents |
| **τ-bench** | Dynamic tool use + user simulation | Customer service agents |

### Métricas de Evaluación

| Métrica | Qué Mide | Cuándo Usar |
|---------|----------|-------------|
| **Exact Match / F1** | QA extractivo, classification | Ground truth claro |
| **ROUGE / BLEU / BERTScore** | Generation quality vs reference | Summarization, translation |
| **Semantic Similarity (SBERT)** | Meaning preservation | Paraphrase, open-ended |
| **Hallucination Rate** | % claims no supported by context | RAG, factual QA |
| **Citation Accuracy** | % citations correct + relevant | RAG with citations |
| **Faithfulness / Consistency** | Output aligned with context | RAG, summarization |
| **Calibration (ECE, Brier)** | Probabilities reflect true uncertainty | High-stakes decisions |
| **Latency (p50/p95/p99)** | User experience | Production |
| **Cost per Quality Unit** | $ / (quality metric) | Budget optimization |

---

## 11.3 Cursos y Certificaciones (Evaluados por ROI)

### Tier 1: Alto ROI / Reconocimiento Industrial

| Programa | Proveedor | Duración | Costo | Valor Real | Para Quién |
|----------|-----------|----------|-------|------------|------------|
| **LLM Course** | Hugging Face | 6-8 semanas | Gratis | ⭐⭐⭐⭐⭐ | Todos - mejor punto partida |
| **Prompt Engineering for Developers** | DeepLearning.AI (Andrew Ng) | 2-4 semanas | ~$50 | ⭐⭐⭐⭐ | Devs que quieren base sólida |
| **Advanced Prompt Engineering** | DAIR.AI | 4-6 semanas | ~$100 | ⭐⭐⭐⭐ | Intermedio→Avanzado |
| **LLM Engineering** | Databricks / MosaicML | 6-8 semanas | ~$200 | ⭐⭐⭐⭐ | ML Engineers → LLM Engineers |
| **Generative AI with LLMs** | AWS / DeepLearning.AI | 3 semanas | ~$50 | ⭐⭐⭐ | Cloud practitioners |

### Tier 2: Especialización / Nicho

| Programa | Enfoque | Valor |
|-----------|---------|-------|
| **LangChain/LangGraph Certification** | Framework orchestration | ⭐⭐⭐ si usas LangChain |
| **RAGAS / RAG Evaluation** | RAG evaluation rigoroso | ⭐⭐⭐⭐ para RAG engineers |
| **DSPy / Optimizing LM Programs** | Programmatic prompting | ⭐⭐⭐⭐ investigadores |
| **AI Safety / Alignment** | Anthropic, Redwood, CAIS | ⭐⭐⭐ safety-critical |

### Qué EVITAR (Bajo ROI)
- Certificaciones genéricas "AI Expert" de plataformas genéricas
- Cursos >$500 sin proyectos reales + evaluación
- "Masterclasses" de influencers sin curriculum público + outcomes medibles

---

## 11.4 Herramientas por Categoría (Producción 2026)

### Orquestación y Frameworks

| Herramienta | Tipo | Mejor Para | Madurez 2026 |
|-------------|------|------------|--------------|
| **LangGraph** | Graph-based orchestration | Multi-agent, cycles, human-in-loop | ⭐⭐⭐⭐⭐ Production-ready |
| **LangChain** | Chains, agents, memory | Legacy apps, migración | ⭐⭐⭐ Mantenido pero legacy |
| **LlamaIndex** | RAG, data connectors | Enterprise RAG | ⭐⭐⭐⭐ Enterprise focus |
| **Haystack** | RAG, pipelines | Enterprise, EU compliance | ⭐⭐⭐⭐ Strong EU adoption |
| **DSPy** | Programmatic prompting, optimization | Research, systematic optimization | ⭐⭐⭐⭐ Cutting edge |
| **Guidance / Outlines** | Structured generation, constraints | Constrained decoding | ⭐⭐⭐⭐ Niche but powerful |
| **Instructor / Pydantic-AI** | Structured output validation | Type-safe LLM outputs | ⭐⭐⭐⭐⭐ Pythonic, growing |

### Vector Databases

| DB | Tipo | Mejor Para | Costo/Operación |
|----|------|------------|-----------------|
| **Pinecone** | Managed | Zero-ops, scale | $$$ |
| **Weaviate** | Hybrid (vector + keyword) | Hybrid search, multi-tenant | $$ |
| **Qdrant** | Open source, Rust | Performance, filtering | $ (self-host) |
| **Chroma** | Simple, local-first | Dev, prototyping | Free |
| **Milvus** | Scale, distributed | Billions vectors | $$ (managed) |
| **pgvector** | Postgres extension | Already on Postgres | $ |

### Evaluation & Observability

| Herramienta | Qué Hace | Para Quién |
|-------------|----------|------------|
| **RAGAS** | RAG evaluation (faithfulness, answer relevancy, context precision) | RAG engineers |
| **DeepEval** | Unit tests for LLM outputs | Dev teams |
| **LangSmith / LangFuse** | Tracing, evaluation, datasets | LangChain/LangGraph users |
| **Weights & Biases** | Experiment tracking, model comparison | ML teams |
| **WhyLabs / Arize** | Production monitoring, drift detection | Production ML |
| **PromptLayer / Helicone** | Prompt versioning, analytics, cost tracking | Prompt engineers |

### Model Serving (Local/Cloud)

| Solución | Tipo | Para Qué |
|----------|------|----------|
| **vLLM** | High-throughput serving | Production self-host |
| **Ollama** | Local dev, easy model mgmt | Dev, privacy |
| **TGI (Text Generation Inference)** | HF optimized serving | Production HF models |
| **SGLang** | Structured generation + fast serving | Structured output heavy |
| **LM Studio** | GUI local models | Non-technical users |

### Cost Optimization

| Herramienta | Función |
|-------------|---------|
| **LiteLLM** | Unified API 100+ providers, cost tracking, fallbacks, load balancing |
| **Portkey** | Gateway: caching, fallbacks, load balancing, analytics |
| **Helicone** | Open-source proxy: logging, caching, eval, cost tracking |
| **OpenRouter** | Single API 100+ models, comparison, fallback |

---

## 11.5 Comunidades Activas 2026 (Dónde Preguntar y Aprender)

### Discord (Tiempo Real, Alta Actividad)

| Comunidad | Miembros | Enfoque | Calidad Respuestas |
|-----------|----------|---------|-------------------|
| **Prompt Engineering** | 25K+ | General prompting, techniques | ⭐⭐⭐⭐ |
| **AI Engineering** | 30K+ | LLM apps, architecture, prod | ⭐⭐⭐⭐⭐ |
| **LocalLLaMA** | 40K+ | Local models, quantization, hardware | ⭐⭐⭐⭐ |
| **LangChain/LangGraph** | 20K+ | Framework-specific | ⭐⭐⭐ |
| **DSPy** | 5K+ | Programmatic prompting | ⭐⭐⭐⭐ |
| **RAGAS / RAG Evaluation** | 3K+ | RAG evaluation | ⭐⭐⭐⭐ |

### Reddit (Async, Archivable)

| Subreddit | Actividad | Calidad |
|-----------|-----------|---------|
| r/PromptEngineering | Alta | ⭐⭐⭐ |
| r/LocalLLaMA | Muy alta | ⭐⭐⭐⭐ |
| r/MachineLearning | Media (papers) | ⭐⭐⭐⭐⭐ |
| r/LangChain | Media | ⭐⭐⭐ |
| r/AI_Agents | Creciente | ⭐⭐⭐ |

### Newsletters (Curated, Semanales)

| Newsletter | Frecuencia | Enfoque | Valor |
|------------|------------|---------|-------|
| **The Prompt Engineer** | Semanal | Techniques, cases, tools | ⭐⭐⭐⭐ |
| **Context Quarterly** | Trimestral | Context engineering deep-dives | ⭐⭐⭐⭐ |
| **Import AI** (Jack Clark) | Semanal | Policy, safety, frontier | ⭐⭐⭐⭐⭐ |
| **The Batch** (Andrew Ng) | Semanal | Industry, research, business | ⭐⭐⭐⭐ |
| **TLDR AI** | Diario | Headlines only | ⭐⭐⭐ |
| **Ben's Bites** | Diario | Products, tools, launches | ⭐⭐⭐ |

### Twitter/X (Tiempo Real, Ruido Alto → Curar Lista)

**Cuentas Esenciales (Alta Señal/Ruido):**
- **Investigadores:** @karpathy, @ylecun, @sebastianruder, @sebastian_raschka, @omarsar0
- **Ingenieros/Builders:** @simonw, @jerryjliu0, @hwchase17, @lorenzokerr, @logan_kilpatrick
- **Seguridad/Ética:** @catherineroberts, @catherinemm, @KirstenLum
- **Product/Industria:** @logankilpatrick, @mattturck, @mattshumer_, @yoheinakajima
- **Académicos/Teoría:** @sebastianborgeaud, @jasonwei_ai, @william_fedus

---

## 11.6 Planes de Aprendizaje Personalizados

### Plantilla: Plan 30/60/90 Días (Personalizable)

```markdown
# MI PLAN PERSONALIZADO [FECHA INICIO]

## MI PERFIL
- Rol actual: [ej. Backend Dev / PM / Data Scientist / Student]
- Objetivo 90d: [ej. "Staff Prompt Engineer en fintech" / "Liderar equipo RAG"]
- Horas/semana disponibles: [10-20h]
- Presupuesto cursos/herramientas: $[X]
- Mentor/Accountability: [Nombre / "Buscar en Discord"]

## FORTALEZAS ACTUALES (✓)
- [ ] Python / [ ] TypeScript / [ ] SQL / [ ] ML basics / [ ] Cloud (AWS/GCP/Azure)
- [ ] Prompting básico / [ ] RAG / [ ] Agents / [ ] Eval / [ ] Security / [ ] Cost optimization

## BRECHAS CRÍTICAS (Prioridad 1-5)
1. [Brecha] → Acción concreta + recurso + deadline
2. [Brecha] → Acción concreta + recurso + deadline
...

## PLAN 30 DÍAS (Fundamentos)
| Semana | Módulos | Ejercicios | Entregable | Check |
|--------|---------|------------|------------|-------|
| 1 | 00, 01, 02 | 1.1, 1.2, 1.3 | Autoevaluación + Plan | [ ] |
| 2 | 03 | 2.1, 2.2 | Tabla comparación técnicas | [ ] |
| 3 | 04 | 3.1, 3.2 | ToT/ReAct en problema real | [ ] |
| 4 | 05, 07 | 4.1, 4.2 | Calculadora costos + selector modelo | [ ] |

## PLAN 60 DÍAS (Competencia)
| Mes | Foco | Capstone | Entregable Portfolio |
|-----|------|----------|---------------------|
| 2 | 04, 06, 08 | Capstone 1 (RAG) | Repo + eval + postmortem |

## PLAN 90 DÍAS (Listo para Liderar)
| Mes | Foco | Capstones | Portfolio Final |
|-----|------|-----------|-----------------|
| 3 | 10, 11, 12 | 2, 3 | 3 capstones + 3 postmortems + 1 video |

## RECURSOS ASIGNADOS
- Curso principal: [Hugging Face LLM Course / DeepLearning.AI / etc]
- Comunidad accountability: [Discord "AI Engineering" / Pareja estudio]
- Mentor: [Nombre / "Buscar en AI Engineering Discord semana 1"]
- Presupuesto: $[X] → [Curso X] + [Herramienta Y] + [Cloud credits Z]

## MÉTRICAS SEMANALES (Dashboard)
- Horas estudio: ___ / objetivo
- Ejercicios completados: ___ / objetivo
- Capstone progress: ___%
- Portfolio artifacts: ___ nuevos
- Networking: ___ conexiones nuevas
```

---

## 11.7 Fuentes Primarias para Mantenerse Actualizado 2026

### Dónde Leer Papers Primero (Antes que Blogs)

1. **arXiv cs.CL / cs.LG / cs.AI** (diario) — RSS: `https://arxiv.org/rss/cs.CL`
2. **Papers with Code** — Tasks, leaderboards, code links
3. **Hugging Face Papers** — Trending, tasks, datasets
4. **Google Research Blog / OpenAI Research / Anthropic Research** — Primary sources
5. **Distill.pub** — Visual explanations (cuando publican)

### Conferencias 2026 (Deadlines y Fechas)

| Conferencia | Deadline | Conferencia | Enfoque |
|-------------|----------|-------------|---------|
| **ICLR 2026** | Sep 2025 | Abr 2026 | Representation learning |
| **NeurIPS 2026** | May 2026 | Dic 2026 | ML general |
| **ICML 2026** | Jan 2026 | Jul 2026 | ML theory + applications |
| **ACL 2026** | Dec 2025 | Aug 2026 | NLP principal |
| **EMNLP 2026** | Apr 2026 | Nov 2026 | NLP aplicado |
| **NAACL 2026** | Dec 2025 | Jun 2026 | NLP Americas |
| **COLM** | Mar 2026 | Oct 2026 | Language modeling focus |

### Repositorios GitHub para Seguir (Stars > 5K, Activos 2026)

| Repo | Stars | Qué Ofrece |
|------|-------|------------|
| `huggingface/transformers` | 120K+ | Model hub, trainers, pipelines |
| `langchain-ai/langgraph` | 15K+ | Graph orchestration |
| `microsoft/autogen` | 25K+ | Multi-agent framework |
| `microsoft/guidance` | 20K+ | Structured generation |
| `dspy-ai/dspy` | 15K+ | Programmatic prompting |
| `run-llama/llama_index` | 30K+ | RAG framework |
| `vllm-project/vllm` | 35K+ | Fast inference |
| `modelscope/agentscope` | 10K+ | Multi-agent framework |
| `berkeley-nest/foa` | 5K+ | Function calling orchestration |

---

## 11.8 Bibliografía de Referencia Rápida (Libros)

| Libro | Autor | Año | Para Qué Sirve | Nivel |
|-------|-------|-----|----------------|-------|
| **Prompt Engineering for Generative AI** | Phoenix & Taylor | 2024 | Casos prácticos multi-dominio | Intermedio |
| **Prompt Engineering for LLMs** | Berryman & Ziegler | 2023 | Fundamentos teóricos + apps | Intermedio-Avanzado |
| **Co-Intelligence** | Ethan Mollick | 2024 | Trabajo + creatividad + IA | Todos |
| **Designing Machine Learning Systems** | Chip Huyen | 2022 | ML systems engineering | Avanzado |
| **Building LLM Applications** | Valentina Alto | 2024 | End-to-end apps | Intermedio |
| **Natural Language Processing with Transformers** | Tunstall et al. | 2022 | HF transformers deep-dive | Avanzado |
| **Machine Learning Engineering** | Andriy Burkov | 2023 | ML systems end-to-end | Avanzado |

---

## 11.9 Checklist de "Mantenerse Actualizado" (Mensual)

```markdown
## CHECKLIST MENSUAL [MES/AÑO]

### 📚 Papers (Objetivo: 3-5 papers profundos/mes)
- [ ] Paper 1: [Título] → [1 insight accionable] → [Aplicado en: proyecto X]
- [ ] Paper 2: [Título] → [1 insight accionable] → [Aplicado en: proyecto Y]
- [ ] Paper 3: [Título] → [1 insight accionable] → [Compartido en: Discord/equipo]

### 🛠 Herramientas (Objetivo: 1 nueva evaluada/mes)
- [ ] Herramienta: [Nombre] → [Veredicto: Adoptar/Piloto/Descartar] → [Próximo paso]

### 💬 Comunidad (Objetivo: 1 contribución/mes)
- [ ] Pregunta respondida / Post escrito / Code review / Bug report / PR

### 💰 Costo/Producción (Revisión mensual)
- [ ] Costo total: $[X] vs budget $[Y] → [Variación: Z%]
- [ ] Cost/query trend: [Subiendo/Bajando/Estable] → [Acción si >10% subida]
- [ ] Nuevo modelo evaluado: [Nombre] → [Veredicto para nuestro caso]

### 📈 Métricas Clave (Dashboard)
- [ ] Latency p95: [X]ms (target < [Y]ms)
- [ ] Error rate: [X]% (target < [Y]%)
- [ ] Hallucination rate: [X]% (target < 2%)
- [ ] Cost/query: $[X] (target < $[Y])
- [ ] User satisfaction: [X]/5 (target > 4.5)

### 🎯 Próximo Mes: Top 3 Prioridades
1. [Prioridad 1]
2. [Prioridad 2]
3. [Prioridad 3]
```

---

## 11.10 Glosario de Siglas (Quick Reference)

| Sigla | Significado |
|-------|-------------|
| **LLM** | Large Language Model |
| **RAG** | Retrieval-Augmented Generation |
| **CoT** | Chain-of-Thought |
| **ToT** | Tree-of-Thought |
| **ReAct** | Reasoning + Acting |
| **MoE** | Mixture of Experts |
| **RLHF** | Reinforcement Learning from Human Feedback |
| **DPO** | Direct Preference Optimization |
| **GRPO** | Group Relative Policy Optimization |
| **SFT** | Supervised Fine-Tuning |
| **PEFT** | Parameter-Efficient Fine-Tuning (LoRA, Adapters) |
| **KV Cache** | Key-Value Cache (attention optimization) |
| **RoPE** | Rotary Positional Embedding |
| **FlashAttention** | IO-aware attention kernel |
| **RAGAS** | RAG Assessment Framework |
| **DSPy** | Declarative Self-improving Python (programmatic prompting) |
| **SGLang / vLLM / TGI** | Model serving engines |
| **KV Cache** | Key-Value attention cache |
| **Context Window** | Max tokens model can process at once |
| **Temperature / Top-p** | Sampling hyperparameters |

---

## Lo Que Viene Después

**Módulo 12: Investigación de Vanguardia** — Papers 2024-2026 resumidos, benchmarks actualizados, técnicas emergentes, roadmap futuro, preguntas abiertas.

**Módulo 13: Glosario Unificado** — 300+ términos canónicos, hipervínculos bidireccionales, pronunciación, "confusibles", glosario visual.

> *"El conocimiento tiene vida media. Lo que sabes hoy será obsoleto en 6-18 meses. El hábito de aprender continuamente es tu único activo permanente."*

---

## Apéndice: Plantilla de Ficha de Paper (Para Tu Biblioteca Personal)

```markdown
# [TÍTULO PAPER] ([AÑO]) — [AUTORES PRINCIPALES]

**Link:** [arXiv / PDF / Conference]
**Task:** [Qué problema resuelve]
**Key Insight (1 frase):** [Tu resumen en tus palabras]

## Método (3-5 bullets)
- [Punto clave 1]
- [Punto clave 2]
- ...

## Resultados Clave (Métricas)
| Benchmark | Baseline | Este Paper | Delta |
|-----------|----------|------------|-------|
| [Benchmark] | [Score] | [Score] | [%] |

## Aplicabilidad a Mi Trabajo
- [ ] Directamente aplicable a: [proyecto/tarea]
- [ ] Inspiración para: [idea nueva]
- [ ] No aplicable ahora, pero: [por qué guardarlo]

## Implementación / Código
- [ ] Official repo: [link]
- [ ] HF Space / Demo: [link]
- [ ] Mi implementación: [link a mi fork/notebook]

## Crítica / Limitaciones
- [Limitación 1]
- [Limitación 2]

## Preguntas Abiertas / Ideas Futuras
- [Pregunta 1]
- [Idea para experimento]

---
*Fecha lectura: [FECHA] | Tiempo: [MIN] | Valor: [1-5] | Releer: [SÍ/NO]*
```

> **Hábito:** Una ficha por paper leído en profundidad. En 1 año = 50+ fichas = tu base de conocimiento personal curada.