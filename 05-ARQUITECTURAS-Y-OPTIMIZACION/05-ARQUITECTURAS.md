---
title: "05. Arquitecturas y Optimización"
module: "05-ARQUITECTURAS-Y-OPTIMIZACION"
order: 5
difficulty: "intermedio-avanzado"
estimated_time: "4-5 horas"
prerequisites: ["02-PIPELINE-DE-PROCESAMIENTO-DE-LLM", "04-TECNICAS-AVANZADAS-DE-RAZONAMIENTO"]
tags: ["arquitectura", "moe", "dense", "optimizacion", "seleccion-modelo", "costos"]
version: "1.0.0"
last_updated: "2026-09-27"
learning_objectives:
  - "Entender impacto arquitectura (Densa vs MoE) en prompting y costos"
  - "Seleccionar modelo óptimo según tarea, presupuesto, latencia, privacidad"
  - "Optimizar prompts por arquitectura y ventana de contexto"
  - "Aplicar A/B testing sistemático y métricas de costo-eficiencia"
---

# 05. Arquitecturas y Optimización

## Introducción: Más Allá del Prompt Único

Entender cómo las **diferencias arquitectónicas** afectan el rendimiento de tus prompts es crucial para optimizar calidad y costo. Este capítulo enseña a seleccionar el modelo adecuado y adaptar técnicas según arquitectura subyacente.

---

## 5.1 Arquitecturas: Impacto Real en Prompting

### 5.1.1 Denso vs MoE: Implicaciones Prácticas

| Aspecto | **Denso** (GPT-4o, Claude, Llama) | **MoE** (Mixtral, DeepSeek-V3, Nemotron) |
|---------|-----------------------------------|------------------------------------------|
| **Consistencia** | Muy alta (mismo cómputo ∀ tokens) | Variable (routing estocástico) |
| **Sensibilidad formulación** | Alta - cambios pequeños afectan | Moderada - robustez por especialización |
| **Beneficio Few-shot** | Significativo | Menor (generalización intrínseca mayor) |
| **Ventaja CoT** | Alta - todos recursos disponible | Moderada - recursos limitados/token activo |
| **Sensibilidad Temperature** | Predecible | Variable (routing estocástico) |
| **Reproducibilidad** | Excelente (seed fijo = mismo output) | Buena - variaciones menores por routing |

**Recomendaciones Prácticas:**

| Caso de Uso | Arquitectura Preferida | Por Qué |
|-------------|------------------------|---------|
| Legal, médico, financiero (consistencia crítica) | Denso o MoE routing determinístico | Mismo cómputo garantizado |
| Creativo (se valora variación) | MoE | Variación interesante, robustez |
| Few-shot denso | 3-5 ejemplos óptimos | Aprende rápido de ejemplos |
| Few-shot MoE | 1-3 ejemplos suelen bastar | Generalización intrínseca mayor |

### 5.1.2 Tokenizador y Vocabulario: Impacto Real

| Tokenizador | Modelos | Impacto Prompting |
|-------------|---------|-------------------|
| **BPE byte-level** | GPT-2/3/4, Llama | Subpalabras comunes ("ing", "tion") eficientes |
| **WordPiece** | BERT, modelos antiguos | Similar BPE, distinto algoritmo entrenamiento |
| **Unigram** | T5, mT5, algunos modernos | Segmentos más lingüísticamente significativos |
| **Character-level** | Raro en LLMs grandes | Muy ineficiente inglés, bueno algunos idiomas |

**Ejemplo Práctico - "intelección":**
```
BPE (GPT):      ["in", "tel", "ección"]           → 3 tokens
WordPiece:      ["in", "tel", "ección"]           → 3 tokens  
Unigram:        ["intelección"] (si en vocab)     → 1 token
Character:      ["i","n","t","e","l","e","c","c","i","ó","n"] → 11 tokens
```

**Implicaciones:**
1. **Conteos tokens varían** significativamente entre tokenizadores
2. **Conceptos = 1 token en un modelo, varios en otro**
3. **Modismos/frases hechas** tokenizan mejor en idioma nativo
4. **Comparar costos entre modelos → SIEMPRE usa tokenizador específico del modelo**

### 5.1.3 Ventana de Contexto: Estrategias por Rango

| Ventana | Estrategia Recomendada |
|---------|------------------------|
| **< 16K** | Concisión extrema. Resumen agresivo, elimina redundantes, prioriza crítico inicio/final |
| **16K-64K** | Equilibrio. Documentos moderados OK, priorización cuidadosa |
| **64K-256K** | Múltiples docs/código extenso. Riesgo principal: "lost in the middle" severo |
| **> 256K** | Organización estructural. Desafío: cómo organizar para máxima utilidad |

**Técnicas Específicas:**

**Ventanas Pequeñas (< 16K):**
- Pirámide información: crítico en inicio/final, menos importante en medio
- Acrónimos definidos temprano
- Elimina palabras vacías
- Referencias en lugar de repetición

**Ventanas Grandes (> 64K):**
- Capas de contexto: organizar en capas prioridad con encabezados claros
- Tablas de contenido + referencias cruzadas
- "Resumen de capas" al inicio indicando dónde encontrar qué
- Monitoreo activo "lost in the middle" (repetir info clave cada ~8-10k tokens)

### 5.1.4 Modelos Especializados: Enfoque Prompting

| Tipo Modelo | Características | Prompting Recomendado |
|-------------|-----------------|----------------------|
| **Code-LLMs** (StarCoder2, CodeLlama, DeepSeek-Coder) | Entrenados código | Comentarios código como contexto, estilos programación específicos, estructuras datos específicas |
| **Math-LLMs** (DeepSeekMath, Qwen2.5-Math) | Razonamiento matemático | Notación matemática estándar, pasos demostración explícitos, formato teorema-prueba |
| **Multimodal** (GPT-4o, Claude 3.5, Gemini 1.5) | Texto + imagen | Describe claramente qué aspecto imagen relevante, coordenadas/regiones si precisión espacial, relaciona explícitamente texto-imagen |
| **Instruction-Tuned** (mayoría modernos) | Optimizados seguir instrucciones | Instrucciones claras funcionan excepcionalmente, menos few-shot |
| **Base Models** (Llama base, etc.) | Sin instruction tuning | Más few-shot + configuración contexto elaborada |

---

## 5.2 Selección de Modelos: Guía Práctica

### 5.2.1 Marco de Decisión

#### Paso 1: Requisitos No Negociables
- **Presupuesto/ejecución** → Costo máximo aceptable
- **Latencia máxima** → Tiempo respuesta tolerable
- **Privacidad** → Local vs API
- **Compliance** → Certificaciones (HIPAA, SOC2, GDPR)

#### Paso 2: Caracterizar Tarea
| Característica | Preguntas Clave |
|----------------|-----------------|
| **Tipo tarea** | Clasificación, generación, razonamiento, traducción... |
| **Complejidad razonamiento** | Pasos lógicos múltiples vs reconocimiento patrones |
| **Creatividad necesaria** | Novedad vs precisión/reproducibilidad |
| **Sensibilidad contexto** | Cuánta info fondo necesaria |
| **Formato salida** | JSON, XML, específico, texto libre |

#### Paso 3: Trade-offs Arquitectura

| Prioridad | Mejor Arquitectura | Por Qué |
|-----------|-------------------|---------|
| **Consistencia máxima** | Denso o MoE routing fijo | Mismo cómputo garantizado |
| **Costo mínimo/token** | MoE alta eficiencia (Mixtral, DeepSeek-V3) | Parámetros activos/token bajos |
| **Razonamiento complejo** | Denso alto rendimiento o MoE especializado | Más recursos/token razonamiento |
| **Tarea muy específica** | Fine-tuned dominio | Menor prompting elaborado |
| **Multimodal requerida** | Arquitecturas nativas multimodal | Mejor alineación modos entrada |
| **Ejecución local** | < 70B cuantizados (4-bit) | Caben hardware consumo (24-48GB VRAM) |

#### Paso 4: Validación Final
1. ¿Cumple **todos** requisitos no negociables?
2. ¿Posicionado óptimamente en matriz trade-offs?
3. ¿Evidencia empírica rendimiento tareas similares?
4. ¿Ecosistema herramientas/comunidad adecuado?

### 5.2.2 Guía por Caso de Uso Común

#### Bajo Costo / Alto Volumen
*Chatbots atención básica, clasificación emails*

**Modelos:** Mistral 7B / Phi-3-mini cuantizados (4-bit), modelos distilados tarea-específicos  
**Características:** < 3B params efectivos, latencia < 500ms, costo < $0.0001/1K tokens  
**Prompting:** Zero/1-shot, plantillas rígidas, validación output estricta, batch processing

#### Razonamiento Técnico Alto
*Análisis financiero, debugging código, matemáticas*

**Modelos:** DeepSeek-R1, o1-mini, GPT-4o, Claude 3.5 Sonnet  
**Características:** Benchmarks reasoning demostrados, cadenas lógicas largas  
**Prompting:** CoT estructurado obligatorio, self-consistency/ToT, formatos salida parseables, checkpoints intermedios

#### Creativo / Contenido
*Marketing, historias, brainstorming*

**Modelos:** GPT-4o, Claude 3.5 Sonnet, Gemini 1.5 Pro  
**Características:** Benchmarks creatividad controlada, adherencia restricciones formato  
**Prompting:** Zero/1-shot estilo, seeding/progresión creativa, espacio deliberado variación, validación criterios subjetivos predefinidos

#### Conocimiento Especializado
*Legal básico, médico preliminar, técnico especializado*

**Modelos:** Fine-tuned dominio O RAG base sólida + modelo amplio few-shot domain-specific  
**Características:** Precisión benchmarks dominio, bajo alucinación áreas críticas  
**Prompting:** Few-shot extensivo ejemplos representativos, plantillas formato estricto, verificaciones realidad + límites confianza, fuentes externas conocimiento

#### Multimodal Avanzado
*Imágenes médicas, gráficos financieros, catálogos productos*

**Modelos:** GPT-4o, Claude 3.5 Sonnet, Gemini 1.5 Pro  
**Características:** Alineación demostrada visión-lenguaje  
**Prompting:** Describe explícitamente qué aspecto imagen relevante, coordenadas/regiones si precisión espacial, relaciona explícitamente texto-imagen, valida integración correcta ambos modos

### 5.2.3 Herramientas Comparación

- **Benchmarks:** MMLU, GSM8K, HumanEval, MATH, AgentBench (enfócate en relevantes tu caso)
- **Comparación:** Hugging Face Leaderboard, lmsys.org Chatbot Arena
- **Costos:** Calculadoras proveedores o independientes (precios públicos)
- **Latencia:** Benchmarks inferencia hardware estándar
- **Comunidades:** Foros especializados (code LLMs, math LLMs, local LLMs)

---

## 5.3 Optimización Prompts por Arquitectura

### 5.3.1 Optimización Arquitecturas Densas

**Fortalezas:** Consistencia extrema, few-shot excelente, respuesta predecible a parámetros, alta capacidad razonamiento con contexto suficiente

**Desafíos:** Costo lineal tamaño modelo, sensibilidad ruido prompt, sobre-ajuste few-shot, alto consumo recursos tareas simples

**Técnicas Específicas:**

| Técnica | Implementación |
|---------|----------------|
| **Precisión Few-Shot** | Exactamente 3-5 ejemplos representativos, cubren variabilidad, orden menos→más complejo, casos límite |
| **Maximizar Ventana** | Crítico en principio/final, resumen extractivo medio, "recordatorios" estratégicos, dividir tareas largas en chunks con estado |
| **Aprovechar Consistencia** | Outputs verificables via reglas, self-consistency multi-ejecución, checksums/hashes output, diseñar para reproducibilidad |
| **Control Fino Parámetros** | Temp 0.1-0.9 pasos 0.1, top-p para diversidad+calidad, frequency/presence penalty anti-repetición, documenta efecto cada parámetro |

### 5.3.2 Optimización Arquitecturas MoE

**Fortalezas:** Relación capacidad/costo excelente, especialización emergente expertos, robustez variaciones prompt, escalabilidad superior

**Desafíos:** Variabilidad inherente routing, inconsistencia ejecuciones idénticas, menor beneficio few-shot, complejidad debugging

**Técnicas Específicas:**

| Técnica | Implementación |
|---------|----------------|
| **Aprovechar Especialización** | Investiga qué tareas activan qué expertos (si disponible), diseña prompts favorezcan expertos relevantes, few-shot refleje diversidad aprendida, monitorea indicadores activación |
| **Manejar Variabilidad** | Promediado si variabilidad aceptable, seeds fijos para reproducibilidad absoluta, tolerancia criterios éxito, voting multi-ejecución decisiones críticas |
| **Few-Shot Optimizado MoE** | Menos ejemplos (1-2 suelen bastar), calidad/representatividad > cantidad, ejemplos activen caminos razonamiento deseados |
| **Aprovechar Escalabilidad** | Modelos muy grandes si costo/tarea aceptable, capacidad conocimiento especializado, fine-tuning MoE para mayor especialización, escalar horizontal vs vertical |

### 5.3.3 Optimización Ventanas Contexto Grandes (100K+)

**Arquitectura Capas Información:**

```
[CAPA 1: METADATOS + NAVEGACIÓN]
- Resumen ejecutivo qué contiene cada sección
- Índice con estimación tokens/sección
- Instrucciones navegar/usar info
- Leyenda símbolos/convenciones

[CAPA 2: CRÍTICO ACCESO RÁPIDO]
- Restricciones absolutas no negociables
- Objetivos primarios + métricas éxito
- Info referencia frecuente
- Definiciones términos críticos

[CAPA 3: DETALLES TÉCNICOS + REFERENCIA]
- Documentos fuente completos
- Especificaciones técnicas detalladas
- Historiales/auditorías relevantes
- Datos fondo/contextos amplios

[CAPA 4: GUIA TRABAJO + PROCEDIMIENTOS]
- Flujos trabajo paso a paso
- Plantillas/ejemplos uso
- Checklist validación/verificación
- Troubleshooting/FAQ
```

**Técnicas Navegación:**
- Marcadores visuales claros delimitan secciones
- "Índice activado por referencia" saltar directo a secciones
- Resumen dinámico cambia según contexto consulta actual
- "Recordatorios contextuales" en puntos estratégicos

**Validación Sistemática:**
- Pruebas "punto aguja": ¿encuentra info específica enterrada?
- Mide recuperación info posiciones inicial/media/final
- Test info contradictoria intencional → ¿cómo resuelve?
- Valida atención no se desvía a irrelevante

**Optimización Costo:**
- No info "por si acaso", solo probabilidad razonable uso
- Carga diferida conceptual: menciona info disponible si se necesita
- Referencias docs externos si acceso rápido/confiable
- Divide tareas grandes en múltiples prompts menores con estado intermedio

---

## 5.4 Casos de Estudio: Optimización por Arquitectura

### Caso 1: Clasificación Emails Bajo Costo
**Req:** 5 categorías, $0.00001/email, 200ms, 85% precisión, 50k/día  
**Arquitectura:** MoE cuantizado 3B params efectivos (Phi-3-mini 4-bit)  
**Prompt:** Zero-shot, formato ultra-simple, categorías mutuamente excluyentes  
**Resultado:** $0.000008/email, 120ms, 87% precisión

### Caso 2: Análisis Financiero Complejo
**Req:** Precision muy alta, trazabilidad, analistas senior  
**Arquitectura:** Denso alto rendimiento (GPT-4o / Claude 3.5 Opus)  
**Prompt:** Few-shot 2 ejemplos, CoT guiado con checkpoints verificación, formato parsing automático  
**Resultado:** $0.04/análisis, 8s, 92%+ backtesting, alta adopción por trazabilidad

### Caso 3: Generación Código Especializado
**Req:** Corrección funcional alta, estilo/buenas prácticas, testabilidad  
**Arquitectura:** Code-LLM (StarCoder2 / CodeLlama 70B / DeepSeek-Coder-V2)  
**Prompt:** Few-shot 3 ejemplos, formato salida con docstring + tests, restricciones PEP8, complejidad especificada  
**Resultado:** 82% corrección 1er intento (91% iteración ligera), -60% tiempo review

---

## 5.5 Monitoreo, A/B Testing y Mejora Continua

### 5.5.1 Métricas Clave por Arquitectura

| Métrica | Densas | MoE | Ventanas Grandes |
|---------|--------|-----|------------------|
| Latencia respuesta | ✓ | ✓ | ✓ |
| Costo/ejecución | ✓ | ✓ | ✓ |
| Tasa éxito | ✓ | ✓ | ✓ |
| Consistencia (seed fijo) | ✓ | ✓ | - |
| Sensibilidad prompt | ✓ | ✓ | ✓ |
| Distribución activación expertos | - | ✓ | - |
| Eficiencia uso contexto | - | - | ✓ |
| Indicadores "lost in middle" | - | - | ✓ |
| Recuperación info específica | - | - | ✓ |

### 5.5.2 A/B Testing Sistemático para Prompts

**Metodología:**
1. **Hipótesis clara:** "Cambiar X mejora Y en Z%"
2. **Variantes:** Prompt A (control) vs B (variante)
3. **Tráfico:** Asignación aleatoria usuarios/ejecuciones
4. **Datos:** Métricas interés ambas variantes
5. **Análisis:** Tests estadísticos significancia
6. **Implementa ganador:** Promueve variante con mejora significativa
7. **Documenta aprendizaje:** Para futuras iteraciones

**Elementos a Testear:**
- Redacción instrucciones (distintas formas pedir lo mismo)
- Orden información (qué va primero/medio/último)
- Nivel especificidad (más/menos detalle)
- Formato ejemplos few-shot
- Incluir/excluir CoT, ToT, etc.
- Parámetros inferencia (temp, top-p)
- Longitud/estructura general

### 5.5.3 Ciclo Mensual Optimización Costo

| Semana | Actividad |
|--------|-----------|
| 1 | Recopilación/análisis datos mes anterior |
| 2 | Identificación 5 oportunidades optimización |
| 3 | Priorización impacto/esfuerzo |
| 4 | Diseño intervenciones top 2 |

**Mes 2: Implementación** → Testing validación → Preparación siguiente  
**Mes 3: Validación** → Documentación lecciones → Planificación siguiente ciclo

---

## Lo Que Viene Después

1. **[06-INGENIERIA-DE-PROMPT-AVANZADA-2026](../06-INGENIERIA-DE-PROMPT-AVANZADA-2026/06-INGENIERIA.md)** — Estado del arte REAL 2024-2026
2. **[07-COSTE-Y-TOKENS](../07-COSTE-Y-TOKENS/07-COSTE.md)** — Economía real, optimización costos producción
3. **[08-SEGURIDAD-Y-DEFENSA](../08-SEGURIDAD-Y-DEFENSA/08-SEGURIDAD.md)** — Seguridad esencial

---

### Recuerda:
- **Arquitectura = tan importante como técnicas** prompting
- **Modelo correcto** para tarea reduce dramáticamente carga prompt engineering
- **Optimización específica** por arquitectura = 20-50% mejora calidad/costo
- **Medición sistemática** esencial para mejora continua
- **Prompts son activos** que merecen mismo cuidado que código