---
title: "07. Coste y Tokens: La Economía Real"
module: "07-COSTE-Y-TOKENS"
order: 7
difficulty: "intermedio"
estimated_time: "3-4 horas"
prerequisites: ["01-CONCEPTOS-FUNDAMENTALES-DE-LLM", "05-ARQUITECTURAS-Y-OPTIMIZACION"]
tags: ["costos", "tokens", "economia", "optimizacion", "produccion", "presupuestos"]
version: "1.0.0"
last_updated: "2026-09-27"
learning_objectives:
  - "Calcular costos reales con precios actualizados Septiembre 2026"
  - "Entender facturación tokens razonamiento (o1, o3, DeepSeek-R1, Gemini Thinking)"
  - "Implementar estrategias prevención bucles/sobre-razonamiento"
  - "Monitorear `reasoning_tokens` en APIs OpenAI, Anthropic, DeepSeek"
  - "Aplicar optimización costo por caso de uso con plantillas canónicas"
---

# 07. Coste y Tokens: La Economía Real

## Introducción: La Economía del Prompting

Entender y optimizar costo asociado a LLMs es esencial para aplicaciones **sostenibles y escalables**. Este capítulo enseña a medir, controlar y optimizar gasto en tokens **sin sacrificar calidad necesaria**.

---

## 7.1 Cómo se Facturan los Tokens (Realidad Sept 2026)

### 7.1.1 Tres Categorías de Costo por Llamada

| Categoría | Qué Incluye | Facturación |
|-----------|-------------|-------------|
| **Input Tokens** | Prompt instrucción + contexto + few-shot + formato + todo lo que envías | Tarifa input modelo |
| **Output Visible** | Texto generado que ves (respuesta final) | Tarifa output modelo |
| **Reasoning Tokens** (ocultos) | Chain-of-thought interno modelos reasoning (o1, o3, DeepSeek-R1, Gemini Thinking) | **Facturan como OUTPUT** aunque no los veas |

> **Clave:** En modelos reasoning, **tokens razonamiento = 50-400% del output visible** y **se facturan a tarifa output**.

### 7.1.2 Estructura Facturación por Proveedor (Sept 2026)

| Proveedor | Input | Output (incluye reasoning) | Notas |
|-----------|-------|----------------------------|-------|
| **OpenAI** | Tarifa modelo | Tarifa modelo (visible + reasoning) | Desglose `reasoning_tokens` en respuesta si solicitado |
| **Anthropic** | Tarifa modelo | Tarifa modelo | No distingue públicamente visible vs reasoning en facturación estándar |
| **Google Gemini** | Tarifa modelo | Tarifa modelo | **Gemini 2.0 Flash Thinking** distingue `thinking_tokens` vs `output_tokens` en metadata |
| **DeepSeek** | Tarifa modelo | Tarifa modelo | API expone `usage.reasoning_tokens` y `usage.completion_tokens` (solo visibles) |
| **Open Source (auto-alojado)** | $0/token | $0/token | Costo = GPU/TPU tiempo + energía + oportunidad + latencia |

### 7.1.3 Ejemplo Detallado Desglose Costo

**Escenario:** GPT-4o analiza contrato legal 8 páginas → resumen ejecutivo

| Componente | Tokens | Cálculo (Precios Sept 2026) |
|------------|--------|------------------------------|
| **Input:** Instrucción (45) + Contrato 8 pág (3,200) + 2 few-shot (120) + Formato (30) | **3,395** | 3,395 × $2.50/1M = **$0.0085** |
| **Output:** Reasoning interno (850) + Respuesta visible ~150 palabras (200) | **1,050** | 1,050 × $10.00/1M = **$0.0105** |
| **TOTAL/LLAMADA** | **4,445** | **$0.0190** |

**Desglose Atribución Costo:**
| Tipo | Tokens | % Output | Costo | % Total |
|------|--------|----------|-------|---------|
| Input útil | ~2,800 | - | $0.0070 | 37% |
| Input evitable (resumible) | ~595 | - | $0.0015 | 8% |
| **Reasoning (oculto)** | **850** | **81%** | **$0.0085** | **45%** |
| Output visible | 200 | 19% | $0.0020 | 10% |

> **Insight:** 45% del costo = tokens reasoning que **no ves**. En tareas complejas puede ser >60%.

---

## 7.2 Modelos Reasoning y Costo Oculto

### 7.2.1 Modelos Reasoning Principales (Sept 2026)

| Modelo | Tipo Reasoning | Características | Uso Típico Tokens Reasoning |
|--------|----------------|-----------------|------------------------------|
| **OpenAI o1 / o1-mini** | RL general diverse problems | 50-200% output visible |
| **OpenAI o3 / o3-mini** (preview) | RL avanzado, mayor capacidad | 75-300% output visible |
| **DeepSeek-R1 / R1-Zero** | RL math/code/logic especializado | 100-400% output visible |
| **Gemini 2.0 Flash Thinking** | Adaptativo (ajusta profundidad) | 0-150% output visible (adaptativo) |
| **Claude 3.5 Sonnet** (standard) | CoT explícito, no reasoning model nativo | 0-50% (CoT explícito si se pide) |
| **Qwen2.5-Math / QwQ** | Math reasoning especializado | 100-300% output visible |

> **Nota:** "Claude 3.5 Sonnet with Thinking", "Qwen 3.0 Max Thinking", "DeepSeek R2", "o3 series" como modelos **separados** **NO EXISTEN** como releases públicos Sept 2026. Se han removido especificaciones inventadas.

### 7.2.2 Patrones Uso Tokens Reasoning

#### Por Tipo Tarea

| Tarea | Reasoning Típico | Ejemplo |
|-------|------------------|---------|
| Hechos simples | 0-20% | "Capital Francia?" → ~0% |
| Conocimiento básico | 10-50% | "Explica fotosíntesis" |
| Matemáticas elementales | 30-80% | "Área triángulo base 10 altura 5" |
| Matemáticas avanzadas | 100-300%+ | "Demuestra suma ángulos triángulo = 180°" |
| Análisis código complejo | 150-400%+ | "Optimiza algoritmo ordenamiento casos borde" |
| Escritura creativa | 20-60% | "Cuento sci-fi viajes tiempo" |
| Análisis legal/médico | 200-500%+ | "Analiza cláusula responsabilidad" |
| Planificación estratégica | 150-350%+ | "Estrategia entrada mercado producto" |

#### Por Complejidad Razonamiento

| Tipo | Rango Reasoning | Descripción |
|------|-----------------|-------------|
| Lineal simple (A→B→C) | 20-60% | Cadena directa |
| Branching (A→{B,C}→{D,E,F}) | 60-120% | Explora alternativas |
| Backtracking (prueba A, falla, prueba B) | 100-250% | Prueba-error |
| Búsqueda árbol amplio | 200-500%+ | Exploración exhaustiva |
| Revisión iterativa | 150-300% | Mejora solución iterando |

### 7.2.3 DeepSeekMath y GRPO: Impacto Eficiencia Tokens

#### GRPO (Group Relative Policy Optimization) - DeepSeekMath
```python
# Concepto simplificado GRPO
respuestas = [model.generate(problema) for _ in range(K)]  # K candidatos
rewards = [reward_fn(r) for r in respuestas]
ventajas = [(r - mean(rewards)) / std(rewards) for r in rewards]
# Optimiza: maximizar ventaja relativa vs baseline absoluto (PPO)
```

**Ventajas eficiencia tokens:**
- Reduce sobre-razonar → enfoca mejora relativa vs perfección absoluta
- Fomenta soluciones "suficientemente buenas" vs óptimas teóricas (excesivo reasoning)
- Mejora ratio calidad/tokens reasoning
- Efectivo donde hay punto rendimientos decrecientes reasoning

#### DeepSeekMath-V2: Generador-Verificador
1. **Generador** produce intentos solución/demostración
2. **Verificador** (modelo separado, entrenado específicamente) evalúa cada paso
3. **Reforzamiento selectivo:** Solo cadenas que pasan verificación se refuerzan
4. **Feedback granular:** Aprende dónde en razonamiento falla, no solo resultado final

**Impacto tokens:** Reduce reasoning inválido, ↑ proporción tokens útiles, aprende parar cuando conclusión suficiente. Mejora eficiencia dominios reglas claras (matemáticas, lógica, código).

---

## 7.3 Estrategias Prevención Bucles y Sobre-Razonamiento

### 7.3.1 Señales Alerta

#### En Respuesta
| Señal | Patrón |
|-------|--------|
| **Repetición explícita** | `Paso 1: [análisis]` → `Paso 3: [repite Paso 1]` |
| **Expansión sin progreso** | Cada paso agrega poco/no valor nuevo |
| **Caminos muertos evidentes** | Explora opciones que no llevan a solución |
| **Incapacidad concluir** | >20 pasos sin señal acercamiento conclusión |

#### En Métricas
- **Ratio reasoning excessivo:** Reasoning/Visible > 3:1 (tareas que no requieren tanto)
- **Uso creciente tiempo:** Tokens/iteración aumenta en lugar estabilizarse
- **Desviación histórica:** Significativamente > promedio histórico tareas similares
- **Correlación inversa calidad:** Más reasoning → menor calidad/utilidad percibida

### 7.3.2 Estrategias Prevención y Control

#### 1. Límites Técnicos Tokens

| Límite | Función | Configuración Típica |
|--------|---------|----------------------|
| **max_tokens** | Techo duro tokens output (reasoning + visible) | Simples: 50-150 / Medias: 200-500 / Complejas: 600-1500 |
| **max_completion_tokens** (OpenAI) | Límite específico completion | Igual max_tokens mayoría casos |
| **max_time** | Límite tiempo (algunas APIs) | Útil cuando latencia > costo exacto |
| **Combinación** | Doble protección | max_tokens + max_time recomendado |

#### 2. Temperature y Muestreo

| Parámetro | Efecto | Rangos Control Reasoning |
|-----------|--------|--------------------------|
| **Temperature baja** | Reduce aleatoriedad → menos exploración inútil | Precisión: 0.0-0.3 / Creatividad controlada: 0.3-0.6 / Evitar >0.8 |
| **Top-p bajo** | Considera solo top-p probabilidad acumulada | 0.1-0.4 (drástico) / 0.9 (estándar con temp 0.7) |
| **Frequency Penalty** | Penaliza freq exacta → reduce repetición idéntica | 0.1-2.0 (positivo = menos repetición) |
| **Presence Penalty** | Penaliza si apareció ≥1 vez → reduce cualquier repetición | Más agresivo que frequency, útil explorar territorio nuevo |

**Combo efectivo:** `temp=0.2 + top-p=0.5` = reasoning enfocado eficiente

#### 3. Detección/Interrupción Bucles Tiempo Real

**Heurísticas Detección:**
- Repetición exacta: mismo segmento X veces en Y tokens → bucle
- Progreso estancado: métricas avance no mejoran N tokens
- Circuito cerrado: vuelve repetidamente mismos conceptos sin avanzar
- Expansión sin info nueva: cada token agrega < Z bits info nueva (entropía condicional)

**Mecanismos Interrupción:**
- **Forzar conclusión:** Añadir tokens inducen conclusión (`"En conclusión:", "Por lo tanto, la respuesta es:"`)
- **Reiniciar con guía:** Detener + reiniciar con info aprendida + orientación específica
- **Reducir temperature:** Disminuir inmediatamente para forzar determinismo
- **Aumentar penalties:** Incrementar frequency/presence penalty romper repetición
- **Divide y vencerás:** Interrumpir + dividir problema en subproblemas menores

#### 4. Técnicas Prompting Prevención Sobre-Razonamiento

**Estructuras CoT Constrained (Guiadas):**
```
RESUELVE SIGUIENDO PROTOCOLO EXACTO:

PASO 1: [acción específica y limitada]
- Solo hacer [X], no considerar [Y] ni [Z]
- Detenerse cuando [condición específica]
- Salida esperada: [formato exacto]

PASO 2: [acción específica y limitada]
- Basado SOLO en resultado PASO 1, hacer [X]
- No alternativas ni exploración no solicitada
- Salida esperada: [formato exacto]

PASO 3: [verificación final]
- Verificar [condición éxito]
- Si no cumple: indicar específicamente dónde falló
- Si cumple: respuesta final formato [ESPECÍFICO]

REGLAS:
- No agregar pasos adicionales
- No explorar alternativas no solicitadas
- Si conclusión intermedia no ajusta formato → vuelve paso anterior corrígete
- Responde ÚNICAMENTE lo solicitado cada paso
```

**Checkpoints Salida Obligatorios:**
```
DESARROLLA SOLUCIÓN SIGUIENDO PROCEDIMIENTO:

[ETAPA 1: ANÁLISIS INICIAL] - MÁX 50 TOKENS
1. [3-5 observaciones iniciales específicas]
2. [hipótesis trabajo formato específico]
SI NO LOGRAS EN 50 TOKENS → DETENTE: "FALLO ETAPA 1"

[ETAPA 2: PRUEBA HIPÓTESIS] - MÁX 100 TOKENS
1. [procedimiento prueba específico]
2. [resultados esperados formato específico]
SI NO LOGRAS EN 100 TOKENS → DETENTE: "FALLO ETAPA 2"

[ETAPA 3: CONCLUSIÓN] - MÁX 75 TOKENS
1. [conclusión específica basada resultados]
2. [recomendación acción formato específico]
SI NO LOGRAS EN 75 TOKENS → DETENTE: "FALLO ETAPA 3"

FORMATO FINAL:
ETAPA 1: [resultado o FALLO ETAPA 1]
ETAPA 2: [resultado o FALLO ETAPA 2]
ETAPA 3: [conclusión/recomendación o FALLO ETAPA 3]
```

**Presupuesto Razonamiento Explícito:**
```
TAREA: [DESCRIBIR]

RESTRICCIÓN RAZONAMIENTO:
- Máx [N] tokens pensamiento intermedio/exploración
- Máx [M] pasos intermedios antes requerir conclusión
- Máx [K] iteraciones revisión/retroalimentación auto
- Conclusión final explícita ANTES consumir token [N]º reasoning

INSTRUCCIONES:
- Distribuye razonamiento eficientemente entre aspectos tarea
- Prioriza pensar en necesario para conclusión
- Minimiza exploraciones unlikely to change conclusion
- Si acercándote límite → prioriza conclusión sobre perfección
```

---

## 7.4 Estrategias Optimización Costo por Caso Uso

### 7.4.1 Clasificación/Extracción Info
*Emails, entidades documentos, etiquetado contenido*

| Estrategia | Config Parámetros |
|------------|-------------------|
| Few-shot mínimo (0-2) | Temp: 0.0-0.2, Top-p: 0.1-0.3, Max: 50-150, FreqPen: 0.5-1.0, PresPen: 0.3-0.8 |
| Plantillas salida rígidas | Formato fijo parsing automático |
| Validación output estricta | Rechaza respuestas formato incorrecto |
| Reglas negocio como filtro | Heurísticas pre/post modelo corrigen errores |
| Batch processing inteligente | Agrupa llamadas similares → cache + reduce overhead |

**Prompt Canónico:**
```
CLASIFICA EN UNA DE 3 CATEGORÍAS:
1. SPAM - No deseado/fraudulento
2. NOTIFICACIÓN - Legítima requiere atención/acción
3. PERSONAL - Comunicación interpersonal genuina

TEXTO: [CONTENIDO]
RESPUESTA: [NÚMERO CATEGORÍA]  # Solo número
```

### 7.4.2 Generación Contenido Estructurado
*Reportes, documentación, formularios estándar*

| Config | Valores |
|--------|---------|
| Temp | 0.3-0.5 |
| Top-p | 0.5-0.7 |
| Max tokens | 300-800 |
| FreqPen | 0.2-0.5 |
| PresPen | 0.0-0.3 |

**Plantilla Canónica:**
```
ERES TÉCNICO ESPECIALISTA INFORMES MANTENIMIENTO EQUIPOS INDUSTRIALES.

TAREA: Genera informe mantenimiento basado en datos servicio:

DATOS:
- Equipo: [TIPO MODELO]
- Fecha: [FECHA]
- Técnico: [NOMBRE]
- Horas: [NÚMERO]
- Observaciones: [TEXTO]
- Partes: [LISTA CON CANTIDADES]

FORMATO OBLIGATORIO:
INFORME MANTENIMIENTO - [FECHA]
============================
Equipo: [TIPO MODELO]
Servicio #: [NÚMERO]
Fecha: [FECHA]
Técnico: [NOMBRE]
Horas: [NÚMERO]

OBSERVACIONES:
[TEXTO - MÁX 3 ORACIONES]

PARTES UTILIZADAS:
[CANTIDAD x DESCRIPCIÓN]

PRÓXIMO SERVICIO:
- Fecha: [ACTUAL + 6 MESES]
- Tipo: PREVENTIVO ESTÁNDAR

RESTRICCIONES:
- No secciones adicionales
- No modificar formato
- Datos insuficientes → "NO ESPECIFICADO"
- Máx 200 tokens salida
```

### 7.4.3 Razonamiento Técnico/Análisis Complejo
*Financiero, debugging, matemáticas, científico*

| Config | Valores |
|--------|---------|
| Temp | 0.1-0.4 |
| Top-p | 0.3-0.6 |
| Max tokens | 600-1500 |
| FreqPen | 0.0-0.2 |
| PresPen | 0.0-0.1 |

**Estrategias:** CoT guiado con checkpoints verificables, unidades/formato estándar, self-consistency controlada, herramientas externas (calculadoras, linters) vs reasoning puro.

### 7.4.4 Generación Creativa/Abierta
*Marketing, historias, brainstorming, conceptos*

| Config | Valores |
|--------|---------|
| Temp | 0.7-0.9 |
| Top-p | 0.8-0.95 |
| Max tokens | 200-600 |
| FreqPen | 0.0-0.3 |
| PresPen | 0.0-0.2 |

**Estrategias:** Iteración humana (modelo genera opciones → humano selecciona/refina), lottery muestreo (múltiples opciones alta temp → selecciona mejores), restricciones creativas (límites paradójicamente ↑ creatividad), validación humana temprana.

### 7.4.5 Trabajo con Código
*Generación, debugging, explicación algoritmos, refactor*

| Config | Valores |
|--------|---------|
| Temp | 0.1-0.3 |
| Top-p | 0.2-0.4 |
| Max tokens | 400-1000 |
| FreqPen | 0.0-0.1 |
| PresPen | 0.0 |

**Plantilla Canónica:**
```
ERES INGENIERO SOFTWARE ESPECIALISTA ALGORITMOS EFICIENTES PYTHON 3.9+.

TAREA: Implementa función resuelve: [PROBLEMA ESPECÍFICO]

RESTRICCIONES:
- Python 3.9+, solo stdlib (no numpy/pandas salvo especificado)
- Entrada: [FORMATO ENTRADA]
- Salida: [FORMATO SALIDA]
- Complejidad temporal: [O(n log n), O(n²), etc.]
- Complejidad espacial: [O(1), O(n), etc.]
- Errores: [ValueError, None, etc.]
- Legibilidad: PEP 8, excepciones documentadas
- Testable: Fácilmente unit-testable

FORMATO SALIDA:
```python
"""[Docstring Google/NumPy]"""
def funcion(parametros):
    """Docstring detallado.
    Args:
        param: tipo - descripcion
    Returns:
        tipo - descripcion
    Raises:
        Excepcion - condicion
    """
    # Comentario enfoque si no trivial
    IMPLEMENTACION
    # Notas importantes si necesario
    # Ejemplo uso:
    # [codigo ejemplo mínimo]

if __name__ == "__main__":
    # prueba básica máx 6 líneas
```

RESTRICCIONES ESTRICTAS:
- Compila/ejecuta sin errores sintaxis
- Sigue restricciones entrada/salida exactas
- Maneja casos borde (null, vacío, tipos incorrectos)
- No librerías prohibidas
- Legible, convenciones Python
- Máx 600 tokens salida
- Permite tests unitarios básicos sin modificaciones
```

---

## 7.5 Monitoreo y Control Costos Producción

### 7.5.1 Métricas Clave

#### Costo Directo
- **Costo/solicitud:** Total / n° solicitudes
- **Costo/unidad trabajo:** Total / medida producción (clasificación correcta, línea código funcional)
- **Costo tokens entrada:** Gasto atribuible solo input
- **Costo tokens salida:** Gasto output (incluye reasoning)
- **Costo tokens reasoning:** Porción output atribuible reasoning (cuando medible)

#### Eficiencia
- **Tokens entrada/unidad trabajo:** Eficiencia uso contexto
- **Tokens salida/unidad trabajo:** Productividad generación
- **Ratio reasoning:** Reasoning / Output total
- **Tokens evitados/optimización:** Reducción tokens por mejoras prompting/contexto
- **Velocidad generación:** Tokens/segundo (UX)

#### Calidad/Confiabilidad
- **Tasa éxito:** % solicitudes output útil/válido
- **Tasa error:** % fallan/output inútil
- **Tasa reintento:** % requerían reintento fallos menores
- **Latencia percibida vs real:** Diferencia tiempo real vs percibido usuario
- **Consistencia ejecuciones:** Varianza output solicitudes idénticas (seed fija)

#### Específicas Modelos Reasoning
- **Uso adaptativo reasoning:** Qué tan bien ajusta profundidad según dificultad real
- **Eficiencia verificación:** Qué tan bien detecta/corrige errores internos
- **Reasoning relevante vs irrelevante:** Proporción tokens reasoning → solución correcta
- **Punto rendimientos decrecientes:** Dónde reasoning adicional deja de mejorar calidad

### 7.5.2 Alertas Proactivas

| Tipo Alerta | Trigger | Acción |
|-------------|---------|--------|
| **Costo diario excesivo** | > umbral predefinido | Notificar equipo |
| **Costo/usuario excesivo** | Usuario específico desproporcionado | Investigar uso |
| **Costo/tipo tarea excesivo** | Categoría uso costo inesperado | Revisar prompts |
| **Degradación ratio reasoning** | Aumento significativo ratio | Posible sobre-razonamiento |
| **Aumento tokens entrada/unidad** | Más contexto para mismo resultado | Optimizar contexto |
| **Disminución tasa éxito** | % éxito cae | Revisar calidad |
| **Cambio repentino patrones** | Cambio significativo no intencional | Investigar |
| **Tokens salida inusualmente largos** | Respuestas >> esperado | Revisar prompts |
| **Patrones uso horarios no laborales** | Uso significativo fuera horas | Verificar seguridad |

**Integración Notificaciones:**
- Slack/Teams: Inmediatas equipo técnico
- Email: Formal gerencia/stakeholders  
- PagerDuty/Opsgenie: Incidentes atención inmediata
- Dashboard tiempo real: Visualización continua
- Reportes automatizados: Resúmenes diarios/semanales/mensuales

### 7.5.3 Ciclo Mensual Optimización Costo

| Mes | Actividad |
|-----|-----------|
| **1: Análisis/Plan** | Semana 1: Datos mes anterior → Semana 2: 5 oportunidades → Semana 3: Priorizar impacto/esfuerzo → Semana 4: Diseñar top 2 |
| **2: Implementación** | Semana 1: Opt #1 (plantillas contexto) → Semana 2: Opt #2 (temp/top-p) → Semana 3: Test/validación → Semana 4: Prep Opt #3 |
| **3: Validación/Cierre** | Semana 1: Opt #3 (CoT guiado análisis) → Semana 2: Test todos cambios → Semana 3: Docs lecciones + playbooks → Semana 4: Plan siguiente ciclo |

**Ejemplo Resultados 6 Meses:**
- -35% costo/unidad trabajo
- +22% tasa éxito primer intento
- -45% variabilidad costo mensual
- +18% satisfacción usuarios (encuestas)
- 3 plantillas contexto reutilizables → -60% esfuerzo creación nuevos prompts

### 7.5.4 Herramientas Gestión Costo

| Categoría | Herramientas |
|-----------|--------------|
| **Monitoreo/Visualización** | OpenTelemetry + Prometheus + Grafana, Datadog LLM Monitoring, WhyLabs, Arize AI, Custom (Metabase, Superset) |
| **Control/Limite** | API Gateways (Kong, AWS API Gateway, Azure API Mgmt), Servicios límite costo custom, Cuotas/facturación interna, Proxy LLM con control |
| **Optimización/Experimentación** | A/B testing (Optimizely, Google Optimize, custom), Frameworks experimentación, Simulación carga, Jupyter notebooks |
| **Educación** | Cursos internos regulares, Docs/playbooks vivos, Comunidades práctica, Mentoring experimentados→novatos |

---

## Lo Que Viene Después

1. **[08-SEGURIDAD-Y-DEFENSA](../08-SEGURIDAD-Y-DEFENSA/08-SEGURIDAD.md)** — Seguridad esencial
2. **[09-APLICACIONES-PROFESIONALES-Y-PLANTILLAS](../09-APLICACIONES-PROFESIONALES-Y-PLANTILLAS/09-APLICACIONES.md)** — Casos uso profesionales
3. **[10-PRUEBAS-Y-EJERCICIOS-PRACTICOS](../10-PRUEBAS-Y-EJERCICIOS-PRACTICOS/10-PRUEBAS.md)** — Ejercicios y proyectos validación

---

### Recuerda:
- **Costo es diseño:** Como latencia/escalabilidad, considerar desde inicio diseño sistema
- **Medición esencial:** No optimizas lo que no mides
- **Pequeñas mejoras se acumulan:** 5-10% en múltiples áreas = ahorros significativos escala
- **Contexto = mayor oportunidad ahorro:** Optimizar presentación/estructura info contexto = mayores retornos
- **Seguridad y costo no trade-offs:** Buen prompt engineering mejora ambas simultáneamente
- **Mide valor, no solo costo:** Objetivo = maximizar valor/unidad costo, no minimizar costo a cualquier precio