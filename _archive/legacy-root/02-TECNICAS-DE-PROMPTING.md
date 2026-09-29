# Técnicas de Prompting
### Fuente Universal de Consulta en Ingeniería de Prompts

---

## 1. El Pipeline de Procesamiento de un LLM: Del Texto a la Respuesta

Los Modelos de Lenguaje Grande (LLM) procesan el texto através de una serie de etapas bien definidas antes de generar una respuesta. Entender este pipeline es fundamental para efectuar una ingeniería de prompts precisa.

### 1.1 Explicación Paso a Paso del Flujo

1. **Normalización**: El texto de entrada se lleva a un formato estándar (por ejemplo, convirtiendo a minúsculas, eliminando caracteres especiales innecesarios según el tokenizador).
2. **Tokenización**: El texto se divide en unidades llamadas tokens usando algoritmos como BPE (Byte Pair Encoding) o WordPiece.
3. **Conversión a IDs**: Cada token se mapea a un ID entero correspondiente en el vocabulario del modelo.
4. **Embeddings Estáticos**: Cada ID se convierte en un vector denso (embedding) desde una tabla de lookup.
5. **Codificación Posicional**: Se añaden vectores que codifican la posición de cada token en la secuencia, ya que el mecanismo de atención es inherentemente no ordenado.
6. **Capas Transformer**: El embedding resultante pasa por múltiples capas idénticas, cada una compuesta por:
   - **Mecanismo de Self-Atención**: Permite que cada token pese la relevancia de todos los otros tokens en la secuencia.
   - **Red Feed-Forward (FFN) o MoE**: Aplica transformación no lineal a cada posición de forma independiente.
7. **Logits**: La salida final de la última capa Transformer es un vector de puntuaciones (logits) para cada token en el vocabulario.
8. **Softmax**: Los logits se convierten en probabilidades mediante la función softmax.
9. **Sampling**: Se selecciona el siguiente token basado en estas probabilidades (usando estrategias como greedy, beam search, o sampling con temperature).
10. **Generación Autoregresiva**: El token seleccionado se anexa a la secuencia de entrada y el proceso se repite hasta alcanzar un token de fin o un límite de longitud.

### 1.2 Diagrama de Flujo Visual

```mermaid
graph TD
    A[Texto de Entrada: "El gato duerme"] --> B[Normalización]
    B --> C[Tokenización: ["El", " gato", " duerme"]]
    C --> D[Conversión a IDs: [452, 1203, 3891]]
    D --> E[Embeddings Estáticos: Lookup en tabla]
    E --> F[Codificación Posicional]
    F --> G[Capa Transformer 1: Self-Attention + FFN/MoE]
    G --> H[Capa Transformer 2: Self-Attention + FFN/MoE]
    H --> I[...]
    I --> J[Capa Transformer N: Self-Attention + FFN/MoE]
    J --> K[Logits: Vector de puntuaciones para vocabulario]
    K --> L[Softmax: Convertir a probabilidades]
    L --> M[Sampling: Seleccionar próximo token]
    M --> N[Anexar token a la secuencia]
    N --> O{¿Token de fin o límite?}
    O -->|No| J
    O -->|Sí| P[Texto de Salida: "El gato duerme pacíficamente"]
```

### 1.3 Ejemplo Conductor: "El gato duerme"

Veamos cómo se procesa la frase "El gato duerme":

- **Tokenización** (usando un tokenizador típico de BPE):
  - "El" → ["El"] (ID: 452)
  - " gato" → [" gato"] (Nota el espacio inicial) (ID: 1203)
  - " duerme" → [" duerme"] (ID: 3891)
  - Resultado final: ["El", " gato", " duerme"] → [452, 1203, 3891]

- **Embeddings Estáticos**: Cada ID se mapea a un vector de, por ejemplo, 4096 dimensiones desde la tabla de embeddings del modelo.

Este proceso se repite para cada capa Transformer, donde los embeddings se vuelven contextuales a través del mecanismo de atención.

---

## 2. Tokenización en Profundidad: El Mito de la Limpieza

### 2.1 Desmontando el Mito

Un error común es pensar que los LLMs "limpian" el texto (quitando tildes, stopwords, etc.). En realidad, los LLMs trabajan exactamente con los tokens que producen sus tokenizadores, sin ninguna limpieza lingüística explícita. El tokenizador opera a nivel subpalabra y trata todo el texto como una secuencia de caracteres que debe segmentarse de manera óptima para la compresión y la generalización.

### 2.2 Algoritmo BPE con Ejemplo Práctico

**Byte Pair Encoding (BPE)** comienza con caracteres individuales y fusiona iterativamente las parejas de bytes más frecuentes hasta alcanzar un tamaño de vocabulario predefinido.

**Ejemplo práctico con "tascas" y "¿Cuántas":**

Supongamos que después de entrenamiento, nuestro vocabulario BPE contiene:
- Caracteres básicos: t, a, s, c, ¿, ñ, u, etc.
- Algunas fusiones comunes: "as", "ta", "sc", "ña", etc.

Proceso para "tascas":
1. Comienza como: t a s c a s
2. Busca la pareja más frecuente: Supongamos que "as" es muy frecuente
3. Fusiona "as": t a s c [as]
4. Repite: Ahora vemos "sc" y "s[as]" - si "sc" es frecuente: t a [sc] [as]
5. Repite: [t a] [sc] [as] - si "ta" es frecuente: [ta] [sc] [as]
6. Resultado final: ["ta", "sc", "as"] → IDs correspondientes

Proceso para "¿Cuántas":
1. Comienza como: ¿ C u ñ a s
2. ¿Qué parejas son frecuentes? Supongamos que "Cu" y "ánt" y "as"
3. Primero fusionamos "¿C" → [¿C] u ñ a s (si es frecuente)
4. Luego "uñ" → [¿C] [uñ] a s
5. Luego "añ" → [¿C] [uñ] [añ] s
6. Finalmente "as" → [¿C] [uñ] [añ] [as]
7. Pero si nuestro vocabulario aprendió "¿Cu" y "ánt" y "as":
   - ["¿Cu", "ánt", "as"] → IDs correspondientes

**Importante**: El mismo token (como "as") siempre tiene el mismo ID en el vocabulario, pero su **embedding contextual** cambia según los tokens vecinos debido al mecanismo de atención.

---

## 3. Embeddings: Estáticos vs. Contextuales

### 3.1 Diferencia Fundamental

- **Embedding Estático**: Vector fijo obtenido directamente de la tabla de lookup usando el ID del token. Es independiente del contexto.
- **Embedding Contextual**: Resultado de pasar el embedding estático por las capas Transformer. Este vector incorpora información de todos los otros tokens en la secuencia mediante el mecanismo de atención.

### 3.2 Ejemplo: "gato" en Tres Contextos Distintos

Consideremos el token "gato" (ID: 1203) en tres oraciones diferentes:

| Oración | Tokens | ID de "gato" | Embedding Contextual (valores ficticios, primeros 5 dims) |
|---------|--------|--------------|----------------------------------------------------------|
| El gato duerme | ["El", " gato", " duerme"] | 1203 | [0.2, -0.1, 0.8, 0.3, -0.5] |
| El gato hidráulico levanta el coche | ["El", " gato", " hidráulico", " levanta", " el", " coche"] | 1203 | [0.1, 0.9, -0.2, 0.7, 0.4] |
| El gato está bajo el carro | ["El", " gato", " está", " bajo", " el", " carro"] | 1203 | [-0.3, 0.2, 0.6, -0.1, 0.9] |

**Interpretación**:
- En "El gato duerme", el vector refleja más aspectos relacionados con animales y sueño.
- En "El gato hidráulico", el vector se inclina hacia herramientas y mecánica (asociado con "hidráulico", "levanta", "coche").
- En "El gato está bajo el carro", el vector mantiene la relación animal pero con fuertes componentes espaciales (asociado con "bajo", "debajo", "carro").

Aunque el ID es idéntico (1203) y por tanto el embedding estático es el mismo, el embedding contextual varía significativamente según el contexto lingüístico.

### 3.3 Tabla Comparativa de Valores Ficticios

| Dimensión | Embedding Estático (ID 1203) | "El gato duerme" | "El gato hidráulico" | "El gato está bajo el carro" |
|-----------|------------------------------|------------------|----------------------|------------------------------|
| 1 | 0.5 | 0.2 | 0.1 | -0.3 |
| 2 | -0.3 | -0.1 | 0.9 | 0.2 |
| 3 | 0.7 | 0.8 | -0.2 | 0.6 |
| 4 | 0.1 | 0.3 | 0.7 | -0.1 |
| 5 | -0.6 | -0.5 | 0.4 | 0.9 |
| ... | ... | ... | ... | ... |
| 4096 | 0.2 | 0.1 | -0.3 | 0.5 |

---

## 4. Mecanismo de Atención (Self-Attention)

### 4.1 Fórmula de Atención

La atención escalada por punto producto se define como:

\[
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right) V
\]

Donde:
- \( Q \) (Query): Matriz de consultas, representa qué está buscando cada token
- \( K \) (Key): Matriz de claves, representa qué ofrece cada token
- \( V \) (Value): Matriz de valores, representa el contenido actual de cada token
- \( K^T \): Transpuesta de la matriz de claves
- \( d_k \): Dimensión de las vectors de clave (usada para escalado estable)
- \( \text{softmax} \): Función que convierte puntuaciones en probabilidades que suman 1

### 4.2 Desglose de Cada Componente

1. **QK^T**: Calcula la similitud entre cada consulta y todas las claves. Una alta puntuación indica que la query es relevante para esa key.
2. **División por \(\sqrt{d_k}\)**: Escala las puntuaciones para evitar gradientes muy pequeños cuando \(d_k\) es grande (estabiliza el entrenamiento).
3. **Softmax**: Convierte las puntuaciones escaladas en una distribución de probabilidad. Cada fila suma 1, representando cuánta atención presta cada token a todos los otros tokens.
4. **Multiplicación por V**: Combina los valores ponderados por las probabilidades de atención. El resultado es una nueva representación de cada token que incorpora información de los demás tokens según su relevancia.

### 4.3 Ejemplo: Matriz de Atención para "El gato duerme bajo el carro"

Consideremos la oración: ["El", " gato", " duerme", " bajo", " el", " carro"]

Supongamos una matriz de atención simplificada (valores ficticios, filas=query, columnas=key):

| Query\Key | El | gato | duerme | bajo | el | carro |
|-----------|----|------|--------|------|----|-------|
| **El** | 0.1 | 0.2 | 0.1 | 0.1 | 0.3 | 0.2 |
| **gato** | 0.1 | 0.1 | 0.5 | 0.1 | 0.1 | 0.1 |
| **duerme** | 0.1 | 0.2 | 0.1 | 0.4 | 0.1 | 0.1 |
| **bajo** | 0.1 | 0.1 | 0.1 | 0.1 | 0.2 | 0.4 |
| **el** | 0.3 | 0.1 | 0.1 | 0.1 | 0.2 | 0.2 |
| **carro** | 0.2 | 0.1 | 0.1 | 0.3 | 0.1 | 0.2 |

**Interpretación de qué token mira a quién**:
- **"gato"** presta más atención a "duerme" (0.5) - tiene sentido ya que describe la acción del gato.
- **"duerme"** mira más a "bajo" (0.4) - posiblemente asociando la acción de dormir con estar bajo algo.
- **"bajo"** enfoca fuertemente en "carro" (0.4) - formando la frase "bajo el carro".
- **"El"** (primera) y **"el"** (quinta) muestran patrones diferentes: la primera "El" mira más al segundo "el" (0.3), mientras que la quinta "el" distribuye su atención más uniformemente.

### 4.4 Heatmap Visual (ASCII)

```
Atención: El gato duerme bajo el carro
          El  gato duerme bajo  el  caro
El      ░░░▒▒░░░░░▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
gato    ░░░░░▒▒▒▒▒▒▒▒▒▒▒░░░░░░░░░░░░
duerme  ░░░▒▒░░░▒▒▒▒▒▒▒▒░░░░░░░░░░░░
bajo    ░░░░░░░░░░░░░░░░░░▒▒▒▒▒▒▒▒▒▒
el      ▒▒▒░░░░░░░░░░░░░░░▒▒▒▒▒▒▒▒▒▒
carro   ▒▒░░░░░░░░░░▒▒▒▒▒░░░░░░▒▒▒▒▒
```
*Donde ░ representa baja atención y ▒ representa alta atención*

---

## 5. Arquitecturas: Denso vs. MoE (Mixture of Experts)

### 5.1 Diferencia Fundamental

- **Arquitectura Densa**: Todos los parámetros del modelo se utilizan para procesar cada token. Cada capa FFN procesa completamente cada posición.
- **Arquitectura MoE (Mixture of Experts)**: En lugar de una FFN única por capa, hay múltiples "expertos" (redes FFN paralelas) y un mecanismo de "router" que selecciona solo los top-k expertos más relevantes para cada token. Esto permite aumentar el número total de parámetros sin incrementar proporcionalmente el cómputo por token.

### 5.2 ¿Qué es un "Experto"?

Un experto en una capa MoE es una sub-red feed-forward neural network (FFN) estándar, típicamente compuesta por dos transformaciones lineales con una función de activación no lineal (como GeLU o ReLU) en el medio. A diferencia de lo que podría sugerir el nombre, los expertos **no tienen etiquetas semánticas humanas** (no hay un "experto en medicina" o "experto en literatura"); son especialidades emergentes aprendidas durante el entrenamiento que se especializan en ciertos patrones de datos.

### 5.3 Cómo Decide el Router

El router es una pequeña red neuronal que, para cada token de entrada, produce una puntuación para cada experto disponible. Luego selecciona los **top-k expertos** (usualmente k=2) con las puntuaciones más altas para procesar ese token específico. Esta decisión se toma **por token**, no por prompt completo, lo que permite una computación altamente eficiente y especializada.

### 5.4 Tabla Comparativa de Modelos Actuales (2026)

| Modelo | Arquitectura | Parámetros Totales | Parámetros Activos/Token | Ventana de Contexto | Licencia |
|--------|--------------|-------------------|--------------------------|---------------------|----------|
| **Kimi K2.6** | MoE | 36B | 2B | 32K | Propietaria |
| **MiMo V2.5 Pro** | MoE | 45B | 3.5B | 64K | Apache 2.0 |
| **DeepSeek V4 Pro** | MoE | 236B | 21B | 128K | MIT |
| **Qwen 3.5** | Denso | 72B | 72B | 128K | Apache 2.0 |
| **Mixtral 8x7B** | MoE | 56B | 12B | 32K | Apache 2.0 |

**Notas**:
- Parámetros Activos/Token: Número de parámetros que realmente se utilizan para procesar un solo token (relevante para costo de cómputo).
- Para modelos MoE: Parámetros Activos ≈ (número de expertos activos) × (parámetros por experto) + parámetros compartidos (atención, embeddings, etc.)

### 5.5 Últimos Avances en MoE para 2026

1. **Photonic MoE**: Utiliza fotónica en lugar de electrónica para las interconexiones entre expertos, reduciendo drásticamente la latencia y el consumo energético.
2. **Evolving Sparse Spiking MoE (S²-MoE)**: Combina principios de redes neuronales esparsas con pulsos (spiking) para una activación aún más selectiva de expertos.
3. **Layer-Scoped Expert-Budget Expansion**: Permite que diferentes capas tengan diferentes presupuestos de expertos, optimizando la asignación de recursos según la profundidad en la red.
4. **Cluster-aware Upcycling**: Mejora la reutilización de expertos menos utilizados mediante técnicas de clustering en el espacio de representación.
5. **ConceptMoE**: Los expertos se organizan alrededor de conceptos latentes descubiertos automáticamente, mejorando la interpretabilidad.
6. **LatentMoE**: Trabaja en un espacio latente comprimido antes de la selección de expertos, aumentando la eficiencia del routing.

---

## 6. Razonamiento en LLMs: ¿Cómo "Piensan"?

### 6.1 Diferencia entre Memorizar y Razonar

- **Memorización**: El modelo recupera patrones exactos o muy similares vistos durante el entrenamiento.
- **Razonamiento**: El modelo combina y transforma conocimientos aprendidos para llegar a conclusiones nuevas que no estaban explícitamente en los datos de entrenamiento. Los LLMs aprenden patrones de razonamiento (como las cadenas de pensamiento en los datos de entrenamiento) y los aplican de manera generalizada a problemas nuevos.

### 6.2 Chain-of-Thought (CoT) y sus Mecanismos

El Chain-of-Thought activa más cómputo mediante:
1. **Descomposición explícita**: Obliga al modelo a dividir problemas complejos en pasos intermedios.
2. **Asignación de recursos**: Cada paso de razonamiento consume capacidad computacional adicional.
3. **Auto-corrección**: Al hacer explícito el proceso, es más fácil detectar y corregir errores intermedios.
4. **Exploración de espacios de solución**: Permite al modelo "probar" diferentes caminos de razonamiento antes de comprometerse con una respuesta final.

### 6.3 DeepSeekMath y GRPO (Group Relative Policy Optimization)

**DeepSeekMath** introdujo un avance fundamental en el entrenamiento de modelos de razonamiento matemático:
- Utilizó un gran corpus de datos matemáticos con soluciones paso a paso.
- **GRPO (Group Relative Policy Optimization)** es una técnica de aprendizaje por refuerzo que:
  1. Agrupa varias soluciones candidatas para el mismo problema.
  2. Calcula ventajas relativas entre estas soluciones en lugar de usar un baseline absoluto.
  3. Optimiza la política del modelo para generar soluciones que sean mejores respecto al grupo.
  4. Se convirtió en el estándar para entrenar modelos de razonamiento como **DeepSeek-R1**, permitiendo una mejora significativa en el rendimiento en razonamiento matemático y lógico respecto a métodos anteriores como PPO (Proximal Policy Optimization).

**DeepSeekMath-V2** extendió esto con un sistema generador-verificador:
- Un modelo generador produce intentos de demostración.
- Un modelo verificador (entrenado por separado) evalúa la validez lógica de cada paso.
- Solo se refuerzan las demostraciones que pasan la verificación, mejorando significativamente la precisión en tareas de demostración formal.

---

## 7. Ingeniería de Prompt Avanzada (Estado del Arte 2026)

### 7.1 Técnicas de vanguardia (actualizado a 27-09-2026)

| Técnica | Descripción | Estado en 2026 |
|---------|-------------|----------------|
| **Chain-of-Thought (CoT)** | Razona paso a paso | Estándar, base para técnicas avanzadas |
| **Self-Consistency** | Genera múltiples cadenas de pensamiento y toma la respuesta más frecuente | Mejora robustez en razonamiento |
| **Tree-of-Thoughts (ToT)** | Explora múltiples caminos de razonamiento en estructura de árbol | Adoptado para decisiones complejas |
| **Graph-of-Thought** | Generaliza ToT a grafos arbitrarios, permitiendo conexiones no jerárquicas | Investigación activa |
| **Thread-of-Thought** | Mantiene hilos de razonamiento separados para aspectos diferentes de un problema | Emergente en 2026 |
| **Instruction Hierarchy** | Jerarquiza instrucciones por importancia (sistema > desarrollador > usuario) | Implementado en modelos de frontier |
| **Parallel/Batch Prompting** | Ejecuta múltiples prompts similares en paralelo para eficiencia | Usado en APIs para reducir latencia percibida |
| **Prompt Ensembling** | Combina resultados de múltiples prompts ligeramente variados | Mejora precisión en tareas subjetivas |
| **Soft Prompting** | Aprende vectores continuos (prompts) en lugar de tokens discretos | Principalmente en investigación |
| **Confidence Calibration** | Ajusta la salida para que las probabilidades reflejen verdadera incertidumbre | Crítico para aplicaciones de alto riesgo |
| **Adversarial Chain-of-Thought (Adv-CoP)** | Entrena modelos para generar cadenas de pensamiento resistentes a manipulaciones | Defensa avanzada contra jailbreaks |
| **Prompt Repetition** | Repite instrucciones críticas en posiciones estratégicas | Mitiga efectos como recency bias |
| **Defensas contra Jailbreak** | Técnicas como perplexity filtering, delimiter randomization, etc. | Área de investigación activa |

### 7.2 Tendencia "Less Prompt Beats More"

Investigaciones recientes muestran que **menos prompt puede ser más efectivo**:
- **Anthropic** eliminó aproximadamente el 80% del system prompt de Claude Code y mantuvo o mejoró el rendimiento en tareas de programación.
- **OpenAI** midió mejoras del 10-15% en ciertos benchmarks al reducir el system prompt en un 41-66% de tokens.
- **Explicación**: Los prompts excesivamente largos pueden:
  - Introducir ruido que distrae al modelo
  - Activar caminos de atención no productivos
  - Aumentar la probabilidad de que el modelo se enfoque en aspectos irrelevantes
  - Consumir recursos de ventana de contexto que podrían usarse mejor para el contenido real

### 7.3 Context Engineering como Disciplina Superior

El **Context Engineering** se está posicionando como más crítico que el Prompt Engineering tradicional para aplicaciones de producción:
- **Definición**: Disciplina que estructura, filtra y optimiza toda la información que entra en la ventana de contexto de un LLM, no solo el prompt explícito.
- **Componentes**:
  - Selección y priorización de fuentes de información
  - Compresión y resumen inteligente de contexto
  - Gestión de dependencias y relaciones entre elementos de contexto
  - Validación y verificación de información antes de inyectarla al contexto
  - Optimización del orden de presentación para maximizar la atención relevante
- **Por qué es más importante**: En aplicaciones reales, la calidad y organización del contexto suele tener mayor impacto en el resultado que las sutilezas del prompt de instrucción.

### 7.4 Frameworks de Optimización Automática

**AgentGrad** representa el estado del arte en optimización automática de prompts:
- Utiliza gradientes estimados a través de aproximaciones finitas o métodos de pérdida directa.
- Puede optimizar tanto prompts discretos como continuos (soft prompts).
- En benchmarks estándar, ha demostrado superar la edición manual de prompts en hasta un **31%** en métricas de precisión y relevancia.
- Funciona mediante:
  1. Muestreo de variaciones alrededor del prompt actual
  2. Evaluación de cada variación en un conjunto de validación
  3. Estimación del gradiente de rendimiento respecto a los tokens del prompt
  4. Actualización iterativa del prompt en dirección de mejora

---

## 8. Coste y Tokens: La Letra Pequeña

### 8.1 Cómo se Facturan los Tokens

Los proveedores de LLM facturan basado en el consumo de tokens:
- **Tokens de Entrada**: Todo lo que usted envía al modelo (prompt + contexto).
- **Tokens de Salida Visible**: El texto que el modelo genera y que usted ve.
- **Tokens de Razonamiento Intermedios**: En modelos de razonamiento (como o1, o3, DeepSeek R1), el modelo genera una cadena de pensamiento interna antes de producir la respuesta final. **Estos tokens se facturan como salida**, incluso si no se muestran al usuario.

### 8.2 Modelos de Razonamiento y Coste Oculto

Modelos como:
- **OpenAI o1/o3 series**
- **DeepSeek R1/R2 series**
- **Gemini 2.5 Flash Thinking Mode**

Generan una cantidad significativa de tokens de razonamiento que:
- No son visibles en la interfaz de chat estándar
- Se facturan a la misma tasa que los tokens de salida visible
- Pueden representar del 50% al 200%+ de los tokens de salida total dependiendo de la complejidad del problema

**Ejemplo**: Un problema matemático complejo podría generar:
- 100 tokens de razonamiento (no visibles, facturados como salida)
- 50 tokens de respuesta final (visibles)
- Total facturado como salida: 150 tokens

### 8.3 Estrategias para Evitar Bucles de Razonamiento

Para controlar costes en modelos de razonamiento:
1. **max_tokens**: Límite absoluto en tokens de salida (incluye razonamiento + respuesta visible).
2. **budget_tokens** (propietario de algunos proveedores): Límite suave que frena la generación antes del límite duro.
3. **timeouts**: Límite de tiempo para evitar bucles infinitos.
4. **Detección de heurísticas**: Identificar patrones que indican bucle (repetición de frases, progreso estancado en métricas internos).
5. **Temperature baja**: Reduce la aleatoriedad, haciendo menos probable que el modelo explore caminos inútiles.
6. **Stop sequences específicas**: Definir secuencias que indiquen el fin del razonamiento (ej. "\n\nRespuesta final:").

### 8.4 Monitoreo de `reasoning_tokens` en APIs

- **OpenAI API**: Los tokens de razonamiento se incluyen en el campo `usage.completion_tokens`. Para distinguirlos, algunos proveedores ofrecen campos adicionales como `reasoning_tokens` en respuestas especiales o a través de headers.
- **DeepSeek API**: Proporciona explícitamente `usage.reasoning_tokens` y `usage.completion_tokens` (donde completion incluye solo tokens visibles).
- **Estrategia de monitoreo**: 
  - Registrar `prompt_tokens`, `reasoning_tokens` (si está disponible), y `completion_tokens`.
  - Calcular el ratio de razonamiento: `reasoning_tokens / (reasoning_tokens + completion_tokens visible)`.
  - Alertar cuando este ratio exceda umbrales predefinidos (ej. >70% indica posible sobre-razonamiento).

---

## 9. Recursos para el Ingeniero de Prompt

### 9.1 Para Programadores

| Recurso | Descripción | Enlace/Referencia |
|---------|-------------|-------------------|
| **Hugging Face LLM Course** | Curso gratuito y práctico que cubre desde fundamentos hasta fine-tuning y despliegue de LLMs. | https://huggingface.co/learn/llm-course |
| **Repositorio `mlabonne/llm-course`** | Materiales complementarios al curso de Hugging Face, incluyendo notebooks y ejercicios. | https://github.com/mlabonne/llm-course |
| **Documentación de `tiktoken`** | Tokenizador oficial de OpenAI, esencial para contar y estimar costos con precisión. | https://github.com/openai/tiktoken |
| **Documentación de `transformers`** | Biblioteca de Hugging Face para trabajar con LLMs: carga, inferencia, fine-tuning. | https://huggingface.co/docs/transformers/index |
| **Guía de IBM sobre Prompt Engineering 2026** | Actualización anual con mejores prácticas, estudios de caso y tendencias emergentes. | https://www.ibm.com/think/topics/prompt-engineering |

### 9.2 Para No Programadores

| Recurso | Descripción | Enlace/Referencia |
|---------|-------------|-------------------|
| **Guía de Prompt Engineering de DAIR.AI** | Recurso accesible con explicaciones claras, ejemplos visuales y ejercicios prácticos. | https://www.dair.ai/prompt-engineering |
| **Curso "ChatGPT Prompt Engineering for Developers" de DeepLearning.AI** | Aunque dirigido a desarrolladores, su enfoque conceptual es valioso para todos. | https://www.deeplearning.ai/courses/chatgpt-prompt-engineering/ |
| **Guía de Anthropic para Prompts con Claude** | Recomendaciones específicas para obtener el mejor rendimiento de la familia de modelos Claude. | https://www.anthropic.com/claude/prompt-engineering |

### 9.3 Libros Recomendados

| Título | Autores | Año | Comentario |
|--------|---------|-----|------------|
| "Prompt Engineering for Generative AI" | James Phoenix & Mike Taylor | 2024 | Enfoque práctico con casos de uso en múltiples dominios. |
| "Prompt Engineering for LLMs" | John Berryman & Albert Ziegler | 2023 | Fundamentos teóricos y aplicaciones avanzadas. |
| "Co-Intelligence" | Ethan Mollick | 2024 | Perspectiva sobre cómo la IA generativa transforma el trabajo y la creatividad. |

---

## 10. Glosario de Términos Clave

| Término | Definición |
|---------|------------|
| **Token** | Unidad mínima de texto que el LLM procesa. Puede ser una palabra, parte de palabra, o incluso un carácter. |
| **Tokenizador** | Algoritmo que convierte texto crudo en una secuencia de tokens (ej. BPE, WordPiece). |
| **ID** | Número entero que representa un token específico en el vocabulario del modelo. |
| **Embedding** | Vector denso que representa el significado de un token. Puede ser estático (de tabla lookup) o contextual (después de atención). |
| **Self-Atención** | Mecanismo que permite a cada token ponderar la relevancia de todos los otros tokens en la secuencia. |
| **MoE (Mixture of Experts)** | Arquitectura que usa múltiples redes especializadas (expertos) y un router para seleccionar cuáles usar por token. |
| **Router** | Red neuronal pequeña que decide qué expertos activar para cada token de entrada en una capa MoE. |
| **Experto** | Sub-red FFN en una capa MoE que se especializa en ciertos patrones de datos durante el entrenamiento. |
| **KV Cache** | Mecanismo que almacena claves y valores calculados previamente para acelerar la generación autoregresiva. |
| **Logits** | Vector de puntuaciones sin normalizar que el modelo produce antes de aplicar softmax. |
| **Softmax** | Función que convierte logits en probabilidades que suman 1. |
| **Temperature** | Parámetro que controla la aleatoriedad en el sampling: valores bajos → más determinista, altos → más creativo. |
| **Alucinación / Camino Rojo** | Generación de contenido factualmente incorrecto pero presentado con alta confianza por el modelo. |
| **GRPO** | Group Relative Policy Optimization, técnica de RL que optimiza políticas comparando grupos de respuestas. |
| **CoT (Chain-of-Thought)** | Técnica de prompting que induce al modelo a generar cadenas de razonamiento explícitas. |
| **ToT (Tree-of-Thought)** | Extensión de CoT que explora múltiples caminos de razonamiento en estructura de árbol. |

---

## 11. Preguntas para Práctica y Entrevistas de Trabajo

### 11.1 Preguntas de Opción Múltiple

1. **¿Qué ocurre con el embedding estático del token "gato" cuando aparece en diferentes contextos?**
   - A) Cambia según las palabras adyacentes
   - B) Siempre permanece igual
   - C) Se convierte en cero en contextos abstractos
   - D) Se duplica en longitud cuando está compuesto
   **Respuesta correcta: B**

2. **En la fórmula de atención `Attention(Q, K, V) = softmax(QK^T / √d_k) V`, ¿qué representa `√d_k`?**
   - A) La dimensión del espacio de valores
   - B) La raíz cuadrada de la dimensión de las claves para escalado estable
   - C) El número de cabezas de atención
   - D) La longitud de la secuencia de entrada
   **Respuesta correcta: B**

3. **¿Cuál es la ventaja principal de una arquitectura MoE frente a una densa en términos de cómputo por token?**
   - A) Menor consumo de memoria siempre
   - B) Los mismos parámetros totales pero menor cómputo activo por token
   - C) Mejor rendimiento en todas las tareas sin excepciones
   - D) Eliminación completa de la necesidad de GPUs
   - **Respuesta correcta: B**

4. **¿Qué técnica fue fundamental en el entrenamiento de DeepSeekMath para mejorar el razonamiento matemático?**
   - A) Reinforcement Learning from Human Feedback (RLHF)
   - B) Proximal Policy Optimization (PPO)
   - C) Group Relative Policy Optimization (GRPO)
   - D) Direct Preference Optimization (DPO)
   - **Respuesta correcta: C**

5. **Según la tendencia "Less Prompt Beats More" observada en 2026, ¿qué hicieron Anthropic y OpenAI con sus system prompts?**
   - A) Los aumentaron significativamente para mejorar el rendimiento
   - B) Los mantuvieron igual pero cambiaron el formato
   - C) Los redujeron sustancialmente (40-80%) manteniendo o mejorando el rendimiento
   - D) Los eliminaron por completo confiando en el few-shot prompting
   - **Respuesta correcta: C**

### 11.2 Preguntas Abiertas

1. **Explique con sus propias palabras por qué el mismo token (por ejemplo, "para") puede tener diferentes efectos en la salida del modelo dependiendo del contexto, haciendo referencia a embeddings estáticos vs. contextuales.**

2. **Desühle cómo funcionaría el mecanismo de self-attention para la frase "El banco de confianza está cerca del río", prestando especial atención a qué tokens probablemente tendría altas puntuaciones de atención mutua entre "banco" y "río" y por qué.**

3. **Compare y contraste las arquitecturas de un modelo denso (como Qwen 3.5) y un modelo MoE (como DeepSeek V4 Pro) en términos de: parámetros totales, parámetros activos por token, flexibilidad para aumentar capacidad, y desafíos de implementación.**

4. **Un modelo de razonamiento como DeepSeek R1 genera tokens de razonamiento que no son visibles pero se facturan. Designe tres estrategias concretas que un ingeniero de prompts podría usar para controlar estos costes ocultos sin sacrificar la calidad de razonamiento necesario.**

5. **Describa brevemente tres técnicas de ingeniería de prompt de vanguardia (publicadas o establecidas como estándar a septiembre de 2026) que no eran ampliamente conocidas o utilizadas a principios de 2024, explicando el problema específico que cada una busca resolver.**

### 11.3 Respuestas

**Preguntas de Opción Múltiple:**
1-B, 2-B, 3-B, 4-C, 5-C

**Preguntas Abiertas (puntos clave esperados):**

1. **Embeddings estáticos vs. contextuales**: 
   - El embedding estático es fijo por ID de token (misma representación vectorial en tabla lookup)
   - El embedding contextual resulta de pasar por capas Transformer, donde la atención modifica la representación basado en los tokens vecinos
   - Ejemplo: "para" en "para comer" (preposición) vs "el para de un paraguas" (sustantivo) tendrá mismos ID/embedding estático pero embeddings contextuales diferentes debido a atención con palabras adyacentes

2. **Self-attention en "El banco de confianza está cerca del río"**:
   - "banco" probablemente tenga alta atención con "confianza" (forma compuesto semántico) y "cerca" (relación espacial)
   - "río" probablemente tenga alta atención con "cerca" (relación espacial) y posiblemente "del" (preposición)
   - La atención mutua alta entre "banco" y "río" sería baja directamente, pero mediada por "cerca" formando "banco ... cerca del río"

3. **Comparación Denso vs MoE**:
   - Parámetros totales: MoE suele tener más (ej. DeepSeek V4 Pro 236B vs Qwen 3.5 72B)
   - Parámetros activos/token: MoE mucho menos (ej. DeepSeek 21B activos vs Qwen 72B activos)
   - Flexibilidad: MoE permite escalar capacidad total sin aumentar linealemente el cómputo por token
   - Desafíos: MoE requiere routing eficiente, balanceo de carga entre expertos, y puede tener complejidad de inferencia mayor

4. **Estrategias para controlar costes de razonamiento**:
   - Establecer límites razonables de `max_tokens` en llamadas a API
   - Usar temperature baja (ej. 0.1-0.3) para reducir exploración inútil en razonamiento
   - Implementar detección de patrones de bucle (repetición, falta de progreso en métricas internos)
   - Para casos específicos, usar prompt engineering que guíe hacia razonamiento más eficiente (ej. estructuras de CoT constrainidas)

5. **Técnicas de vanguardia 2024-2026**:
   - **Thread-of-Thought** (2025): Separa hilos de razonamiento para aspectos diferentes de problemas complejos, evitando interferencia entre líneas de pensamiento.
   - **Instruction Hierarchy** (2025): Formaliza el orden de precedence entre instrucciones de sistema, desarrollador y usuario, mejorando robustez contra inyecciones.
   - **AgentGrad** (2026): Framework de optimización automática de gradientes que supera edición manual en hasta 31%, usando estimación de gradientes a través de muestreados de variaciones de prompt.
   - **Context Engineering** (2025-2026): Paradigma que enfoca en optimizar todo el contexto de entrada, no solo el prompt explícito, reconocido como más crítico que prompt engineering tradicional para producción.
   - **Confidence Calibration avanzada** (2026): Técnicas para que las salidas de probabilidad de LLMs reflejen verdadera incertidumbre, esencial para toma de decisiones en aplicaciones de alto riesgo.