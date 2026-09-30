# IA, ML, DL y LLM: cómo se relacionan

## 1. Objetivo

En Inteligencia Artificial aparecen constantemente términos como:

* Inteligencia Artificial (IA)
* Machine Learning (ML)
* Deep Learning (DL)
* modelo fundacional
* modelo de lenguaje
* Large Language Model (LLM)
* IA generativa

Aunque están relacionados, **no significan lo mismo**.

Comprender esta jerarquía es fundamental para estudiar Ingeniería de Prompt porque un prompt no interactúa con "la IA" en abstracto. Interactúa con un sistema concreto que puede utilizar un determinado tipo de modelo, arquitectura, datos, contexto y mecanismo de inferencia.

Al finalizar este capítulo deberás poder explicar:

1. Qué es Inteligencia Artificial.
2. Qué es Machine Learning.
3. Qué es Deep Learning.
4. Qué es un modelo de lenguaje.
5. Qué es un LLM.
6. Qué es un modelo fundacional.
7. Qué es IA generativa.
8. Cómo se relacionan estos conceptos.
9. Qué diferencias existen entre un modelo tradicional y un LLM.
10. Por qué estas diferencias son importantes para la Ingeniería de Prompt.

---

# 2. La relación general

Una forma simplificada de visualizar estos conceptos es:

```text
INTELIGENCIA ARTIFICIAL
│
├── Machine Learning
│   │
│   ├── Métodos tradicionales
│   │
│   └── Deep Learning
│       │
│       ├── Redes neuronales
│       │
│       ├── Modelos de visión
│       ├── Modelos de audio
│       ├── Modelos de lenguaje
│       └── Otros modelos
│
└── Otros enfoques de IA
```

Dentro de la evolución moderna aparecen los **modelos fundacionales**, algunos de los cuales son modelos de lenguaje de gran escala.

Una representación más completa sería:

```text
IA
│
└── Machine Learning
    │
    └── Deep Learning
        │
        └── Modelos fundacionales
            │
            ├── Modelos de lenguaje
            │   └── LLM
            │
            ├── Modelos de visión
            │
            ├── Modelos de audio
            │
            └── Modelos multimodales
```

Esta representación es útil, pero debe interpretarse como una **simplificación conceptual**, no como una clasificación matemática estricta. Los términos "modelo fundacional", "multimodal" y "generativo" describen propiedades o categorías que pueden solaparse.

---

# 3. ¿Qué es la Inteligencia Artificial?

La **Inteligencia Artificial (IA)** es el campo de la informática que estudia y desarrolla sistemas capaces de realizar tareas que requieren capacidades asociadas normalmente con la inteligencia.

Entre ellas encontramos:

* percepción;
* clasificación;
* predicción;
* planificación;
* razonamiento;
* reconocimiento de patrones;
* procesamiento del lenguaje;
* generación de contenido;
* toma de decisiones;
* interacción con el entorno.

Por ejemplo:

```text
Sistema de IA
     │
     ├── Detecta objetos en una imagen
     ├── Traduce un idioma
     ├── Recomienda una película
     ├── Detecta una transacción sospechosa
     ├── Genera código
     └── Responde preguntas
```

Por tanto:

> **IA es el concepto más amplio de esta clasificación.**

No toda IA es Machine Learning y no todo sistema de IA utiliza un LLM.

---

# 4. IA antes del Machine Learning moderno

Es importante comprender que la Inteligencia Artificial no comenzó con ChatGPT ni con las redes neuronales modernas.

Durante décadas se desarrollaron sistemas basados en reglas, lógica, búsqueda, planificación y representación simbólica del conocimiento.

Un ejemplo sencillo:

```text
SI
    temperatura > 38 °C
Y
    tos = verdadera
ENTONCES
    mostrar "requiere evaluación"
```

Este sistema puede considerarse un sistema de IA basado en reglas, aunque no utilice Machine Learning.

Otro ejemplo clásico es un programa que busca movimientos posibles en un juego mediante algoritmos de búsqueda.

Por tanto:

```text
IA
├── Sistemas basados en reglas
├── Búsqueda
├── Planificación
├── Lógica
├── Machine Learning
└── otros enfoques
```

Esto demuestra por qué:

```text
IA ≠ Machine Learning
```

---

# 5. ¿Qué es Machine Learning?

**Machine Learning (ML)**, o aprendizaje automático, es un subcampo de la Inteligencia Artificial que desarrolla métodos mediante los cuales los modelos aprenden patrones a partir de datos para realizar determinadas tareas.

En programación tradicional podemos representar:

```text
Entrada + reglas programadas
             ↓
           salida
```

En Machine Learning podemos representar:

```text
Datos
  ↓
Algoritmo de aprendizaje
  ↓
Modelo entrenado
  ↓
Nuevos datos
  ↓
Predicción
```

La diferencia fundamental es quién proporciona las reglas.

En un programa tradicional, el programador especifica explícitamente las reglas.

En Machine Learning, el procedimiento de aprendizaje ajusta los parámetros del modelo utilizando datos.

Esto no significa que el programador desaparezca.

El ingeniero sigue tomando decisiones sobre:

* datos;
* representación;
* arquitectura;
* objetivo;
* función de pérdida;
* entrenamiento;
* hiperparámetros;
* evaluación;
* despliegue.

---

# 6. Ejemplo sencillo de Machine Learning

Supongamos que queremos clasificar correos electrónicos como:

```text
SPAM
NO SPAM
```

Disponemos de miles de ejemplos históricos.

```text
Correo                         Etiqueta
-----------------------------------------
"Ganaste un premio"           SPAM
"Reunión mañana a las 10"     NO SPAM
"Oferta exclusiva"            SPAM
"Adjunto informe mensual"      NO SPAM
```

El sistema aprende patrones a partir de estos datos.

Después recibe:

```text
"Obtén tu premio inmediatamente"
```

y produce:

```text
SPAM
```

El modelo no necesariamente contiene una regla explícita escrita por un programador que diga:

```text
SI aparece "premio"
ENTONCES SPAM
```

Puede haber aprendido una combinación mucho más compleja de patrones.

---

# 7. Tipos principales de Machine Learning

Existen diferentes paradigmas de aprendizaje.

## 7.1. Aprendizaje supervisado

El modelo aprende utilizando ejemplos acompañados de una respuesta objetivo.

```text
Entrada → etiqueta

Imagen → gato
Imagen → perro
Correo → spam
Correo → no spam
```

Se utiliza, entre otros casos, para:

* clasificación;
* regresión;
* predicción.

---

## 7.2. Aprendizaje no supervisado

El modelo intenta encontrar estructuras o patrones sin disponer necesariamente de etiquetas proporcionadas para cada ejemplo.

Por ejemplo:

```text
Datos de clientes
      ↓
algoritmo
      ↓
grupos de clientes similares
```

Un ejemplo típico es el clustering.

---

## 7.3. Aprendizaje por refuerzo

Un agente interactúa con un entorno y recibe señales asociadas con sus acciones.

```text
Agente
  ↓
Acción
  ↓
Entorno
  ↓
Resultado
  ↓
Recompensa
  ↓
Agente
```

El sistema aprende estrategias para maximizar una señal de recompensa a lo largo del tiempo.

---

# 8. ¿Qué es Deep Learning?

**Deep Learning (DL)**, o aprendizaje profundo, es una rama de Machine Learning basada principalmente en redes neuronales con múltiples capas de representación.

Una representación muy simplificada:

```text
Entrada
   ↓
Capa 1
   ↓
Capa 2
   ↓
Capa 3
   ↓
...
   ↓
Salida
```

La palabra "profundo" hace referencia, de forma general, a la utilización de múltiples capas de procesamiento.

---

# 9. ¿Por qué Deep Learning cambió la IA?

Los modelos tradicionales de Machine Learning suelen depender en mayor medida de cómo se representan o seleccionan las características de los datos.

Las redes neuronales profundas pueden aprender representaciones internas de gran complejidad directamente a partir de grandes cantidades de datos.

Por ejemplo, en visión por computadora podemos imaginar:

```text
Imagen
 ↓
bordes
 ↓
formas
 ↓
partes
 ↓
objetos
 ↓
clasificación
```

La representación real es mucho más compleja, pero esta analogía ayuda a comprender la idea.

En lenguaje ocurre algo similar:

```text
Texto
 ↓
representaciones
 ↓
patrones lingüísticos
 ↓
relaciones contextuales
 ↓
predicción
```

---

# 10. DL no significa LLM

Esta distinción es fundamental.

Un modelo de Deep Learning puede utilizarse para:

* reconocer imágenes;
* detectar objetos;
* clasificar audio;
* procesar lenguaje;
* generar imágenes;
* generar texto;
* realizar predicciones;
* controlar sistemas.

Por tanto:

```text
Deep Learning
       ↓
muchas aplicaciones
```

Un LLM es solamente una de las familias de modelos que pueden construirse utilizando técnicas de Deep Learning.

Por tanto:

```text
DL ≠ LLM
```

---

# 11. ¿Qué es un modelo de lenguaje?

Un **modelo de lenguaje** es un modelo que aprende patrones de un lenguaje para estimar o generar secuencias de elementos lingüísticos.

Históricamente existieron modelos de lenguaje mucho más sencillos que los LLM actuales.

Una formulación simplificada es:

```text
P(siguiente token | contexto)
```

Es decir:

> El modelo estima qué continuaciones son compatibles con el contexto proporcionado.

Por ejemplo:

```text
"El Sol es una"
```

El modelo puede asignar probabilidades a diferentes continuaciones:

```text
estrella
fuente
...
```

La generación continúa de manera secuencial.

---

# 12. Del modelo de lenguaje al LLM

Un **Large Language Model (LLM)** es un modelo de lenguaje de gran escala.

La palabra "Large" puede referirse a distintos aspectos del sistema, como la escala del modelo, la cantidad de datos, la capacidad computacional utilizada durante su entrenamiento o la complejidad general del sistema.

Por eso no debemos reducir la definición a:

> "Un LLM es un modelo que tiene X parámetros."

El tamaño de parámetros es importante, pero no define por sí solo todas las capacidades de un sistema moderno.

---

# 13. ¿Qué hace un LLM?

Una simplificación útil es:

```text
Texto de entrada
      ↓
Tokenización
      ↓
Representación numérica
      ↓
Modelo
      ↓
Distribución de probabilidades
      ↓
Selección/generación
      ↓
Token siguiente
      ↓
Repetición
      ↓
Texto generado
```

Por ejemplo:

```text
Entrada:

"Python es un lenguaje de"
```

El modelo puede estimar una distribución de probabilidad para posibles continuaciones.

Una continuación probable podría ser:

```text
"programación"
```

Después vuelve a procesar el contexto actualizado y continúa generando.

Esta explicación es una simplificación pedagógica. Los LLM modernos incorporan arquitecturas, mecanismos de atención, entrenamiento y procedimientos de inferencia mucho más complejos.

---

# 14. Un LLM no es simplemente un predictor de palabras

Decir que:

> "Un LLM solamente predice la siguiente palabra"

puede ser útil como primera aproximación, pero es técnicamente insuficiente.

Primero, el modelo opera sobre **tokens**, no necesariamente sobre palabras.

Segundo, durante su entrenamiento aprende representaciones y relaciones complejas.

Tercero, durante la inferencia puede realizar tareas que parecen involucrar:

* clasificación;
* traducción;
* extracción;
* generación;
* planificación;
* transformación de información;
* resolución de problemas;
* programación;
* razonamiento.

Por tanto, una explicación más precisa es:

> **Un modelo de lenguaje genera secuencias de tokens condicionadas por el contexto utilizando representaciones aprendidas durante su entrenamiento.**

Más adelante estudiaremos cómo la arquitectura Transformer permite realizar este procesamiento.

---

# 15. ¿Qué es un modelo fundacional?

Un **modelo fundacional** es un modelo entrenado sobre grandes cantidades de datos y diseñado para servir como base para múltiples tareas y aplicaciones posteriores.

La idea importante es:

```text
Entrenamiento amplio
       ↓
Modelo base
       ↓
adaptación
       ↓
múltiples aplicaciones
```

Un modelo fundacional puede utilizar diferentes modalidades y no tiene por qué ser exclusivamente un modelo de lenguaje.

Puede servir como base para sistemas que trabajan con:

* texto;
* imágenes;
* audio;
* vídeo;
* código;
* combinaciones de estas modalidades.

---

# 16. Modelo base, modelo instruido y modelo especializado

No debemos tratar todos los modelos de lenguaje como equivalentes.

Podemos encontrar diferentes etapas o variantes.

## Modelo base

Está entrenado principalmente para aprender patrones de los datos.

```text
datos
 ↓
preentrenamiento
 ↓
modelo base
```

Puede no estar optimizado para seguir instrucciones de conversación de la manera que esperamos de un asistente.

---

## Modelo instruido

Posteriormente puede ser adaptado para seguir instrucciones.

```text
modelo base
    ↓
ajuste/alineación
    ↓
modelo instruido
```

Por ejemplo, puede aprender a responder:

```text
Usuario:
Resume este texto.

Modelo:
Aquí tienes un resumen...
```

---

## Modelo especializado

También pueden existir modelos optimizados para determinadas tareas o dominios.

Por ejemplo:

```text
modelo especializado en código
modelo especializado en matemáticas
modelo especializado en visión
modelo especializado en audio
```

Esto tiene una consecuencia importante para Prompt Engineering:

> **La estrategia de interacción debe considerar las capacidades y características del modelo concreto.**

---

# 17. ¿Qué es IA generativa?

La **IA generativa** describe sistemas capaces de generar nuevos contenidos utilizando patrones aprendidos.

Podemos tener:

```text
IA generativa
│
├── Texto
├── Código
├── Imágenes
├── Audio
├── Vídeo
└── Contenido multimodal
```

Por ejemplo:

```text
Prompt
  ↓
Modelo generativo
  ↓
Texto nuevo
```

o:

```text
Descripción
  ↓
Modelo generativo de imágenes
  ↓
Imagen
```

No toda IA es generativa.

Un modelo que clasifica una transacción como:

```text
fraude / no fraude
```

puede ser un sistema de IA y Machine Learning sin ser necesariamente un sistema generativo.

---

# 18. Generativo no significa necesariamente LLM

Otro error frecuente es:

```text
IA generativa = LLM
```

Esto es incorrecto.

Los modelos generativos pueden trabajar con diferentes modalidades.

Por ejemplo:

```text
Modelo generativo de texto
Modelo generativo de imágenes
Modelo generativo de audio
Modelo generativo de vídeo
Modelo multimodal generativo
```

Los LLM representan principalmente la familia de modelos de lenguaje de gran escala.

---

# 19. ¿Qué significa multimodal?

Un sistema multimodal puede trabajar con más de una modalidad de información.

Por ejemplo:

```text
Texto
Imagen
Audio
Vídeo
```

Un modelo puede recibir:

```text
Imagen + pregunta
```

y producir:

```text
Respuesta textual
```

Conceptualmente:

```text
Imagen ───┐
          ├──→ Modelo multimodal ──→ Texto
Texto ────┘
```

Esto amplía considerablemente el concepto de Prompt Engineering.

En un sistema puramente textual podemos tener:

```text
Prompt textual
```

En uno multimodal podemos tener:

```text
Instrucción
+
Texto
+
Imagen
+
Audio
+
otros elementos de contexto
```

Por ello, en etapas posteriores estudiaremos **Multimodal Prompt Engineering**.

---

# 20. La jerarquía completa

Ahora podemos construir una representación más precisa:

```text
INTELIGENCIA ARTIFICIAL
│
├── Enfoques simbólicos y otros
│
└── MACHINE LEARNING
    │
    ├── Métodos tradicionales
    │
    └── DEEP LEARNING
        │
        ├── Visión
        ├── Audio
        ├── Lenguaje
        ├── Código
        ├── Multimodalidad
        │
        └── MODELOS FUNDACIONALES
             │
             ├── Modelos de lenguaje
             │    └── LLM
             │
             ├── Modelos de visión
             │
             ├── Modelos de audio
             │
             └── Modelos multimodales
```

Pero debemos recordar:

> Estas categorías pueden superponerse. No constituyen una jerarquía estrictamente excluyente.

---

# 21. Una comparación sencilla

| Concepto           | Pregunta que responde                                                                       |
| ------------------ | ------------------------------------------------------------------------------------------- |
| IA                 | ¿Cómo construimos sistemas capaces de realizar tareas asociadas con inteligencia?           |
| ML                 | ¿Cómo pueden los sistemas aprender patrones a partir de datos?                              |
| DL                 | ¿Cómo podemos utilizar redes neuronales profundas para aprender representaciones complejas? |
| Modelo de lenguaje | ¿Cómo representamos y generamos lenguaje?                                                   |
| LLM                | ¿Cómo construimos modelos de lenguaje a gran escala?                                        |
| Modelo fundacional | ¿Cómo podemos entrenar modelos generales que sirvan como base para múltiples aplicaciones?  |
| IA generativa      | ¿Cómo generamos contenido nuevo a partir de modelos aprendidos?                             |

---

# 22. Ejemplo: detectar fraude

Veamos cómo pueden aparecer varios conceptos en un mismo problema.

Queremos detectar transacciones fraudulentas.

## Paso 1 — IA

El objetivo general pertenece al campo de la Inteligencia Artificial.

```text
Detectar fraude
```

## Paso 2 — Machine Learning

Utilizamos datos históricos para entrenar un modelo.

```text
Datos
 ↓
ML
 ↓
Modelo
```

## Paso 3 — Deep Learning

Podríamos utilizar una red neuronal profunda.

```text
Datos
 ↓
Red neuronal
 ↓
Predicción
```

## Paso 4 — Inferencia

Una nueva transacción llega al sistema.

```text
Nueva transacción
 ↓
Modelo entrenado
 ↓
probabilidad de fraude
```

No necesitamos un LLM.

Esto demuestra:

```text
IA
↓
ML
↓
DL
```

sin que necesariamente aparezca un LLM.

---

# 23. Ejemplo: asistente de auditoría

Ahora consideremos otro problema.

Queremos construir un asistente capaz de analizar documentos contables.

Podríamos tener:

```text
Documentos
    ↓
Extracción de texto
    ↓
Contexto
    ↓
LLM
    ↓
Análisis
    ↓
Salida estructurada
```

Aquí aparecen:

```text
IA
 ↓
Machine Learning
 ↓
Deep Learning
 ↓
Modelo de lenguaje
 ↓
LLM
 ↓
IA generativa
```

Pero el sistema completo puede incorporar además:

```text
RAG
Tools
Base de datos
Skills
Agentes
Evaluación
Seguridad
```

El LLM es solamente **un componente del sistema**.

---

# 24. El error de pensar que "el LLM es la IA"

Una aplicación moderna puede verse así:

```text
                 APLICACIÓN
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
      Datos        Tools         UI
        │            │            │
        └────────────┼────────────┘
                     ↓
                  Contexto
                     ↓
                    LLM
                     ↓
                 Respuesta
```

Por eso:

```text
LLM ≠ aplicación de IA
```

Un LLM puede ser un componente central de una aplicación, pero el sistema completo incluye otros componentes.

Esta distinción será esencial cuando lleguemos a agentes y sistemas de IA.

---

# 25. ¿Dónde está el Prompt Engineering?

Ahora podemos situarlo correctamente.

```text
IA
 ↓
ML
 ↓
DL
 ↓
LLM
 ↓
Aplicación
 ↓
Prompt Engineering
```

Pero esta representación es demasiado simple.

Una representación mejor es:

```text
                  APLICACIÓN DE IA
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
           CONTEXTO              PROMPT
              │                     │
              └──────────┬──────────┘
                         ↓
                        LLM
                         ↓
                     INFERENCIA
                         ↓
                      SALIDA
```

El Prompt Engineering actúa principalmente sobre **las instrucciones y el contexto proporcionados al modelo durante la inferencia**.

No reemplaza:

* el entrenamiento;
* la arquitectura;
* la evaluación;
* la seguridad;
* la ingeniería de software.

---

# 26. ¿Puede un prompt cambiar los parámetros del modelo?

Normalmente, no.

Si escribimos:

```text
"Actúa como un experto en contabilidad."
```

no estamos modificando los parámetros aprendidos del modelo.

Estamos proporcionando una instrucción dentro del contexto.

Podemos representarlo así:

```text
Parámetros entrenados
        │
        ↓
       LLM
        ↑
        │
Prompt + contexto
```

El prompt condiciona la inferencia, pero no equivale a entrenar nuevamente el modelo.

Por eso:

```text
Prompt Engineering
≠
Fine-Tuning
```

---

# 27. Prompt Engineering frente a Fine-Tuning

## Prompt Engineering

Modificamos principalmente:

```text
instrucciones
contexto
ejemplos
restricciones
formato
```

Sin modificar los parámetros del modelo mediante un nuevo entrenamiento.

---

## Fine-Tuning

Se utiliza un proceso de entrenamiento o ajuste para modificar los parámetros del modelo, o adaptar una parte de ellos, según el método utilizado.

Conceptualmente:

```text
Modelo existente
      ↓
Datos de ajuste
      ↓
Entrenamiento adicional
      ↓
Modelo adaptado
```

Esto puede ser útil cuando necesitamos modificar el comportamiento del modelo de una manera que no resulta suficientemente eficaz mediante instrucciones y contexto.

---

# 28. Prompt Engineering frente a RAG

RAG significa **Retrieval-Augmented Generation**.

En términos simplificados:

```text
Pregunta
   ↓
Recuperación de información
   ↓
Documentos relevantes
   ↓
Contexto
   ↓
LLM
   ↓
Respuesta
```

RAG tampoco significa modificar necesariamente los parámetros del LLM.

Se modifica el contexto que recibe durante la inferencia.

Por eso:

```text
Prompt Engineering
+
Context Engineering
+
RAG
```

pueden combinarse.

---

# 29. Prompt Engineering frente a agentes

Un agente puede utilizar:

```text
LLM
+
Prompt
+
Contexto
+
Herramientas
+
Estado
+
Memoria
+
bucle de ejecución
```

Por ejemplo:

```text
Usuario
 ↓
Agente
 ↓
LLM
 ↓
decide utilizar una herramienta
 ↓
API
 ↓
resultado
 ↓
LLM
 ↓
respuesta
```

Aquí el prompt sigue siendo importante, pero ya no es suficiente para describir todo el sistema.

Esta es una de las razones por las que el repositorio evolucionará desde:

```text
Prompt Engineering
```

hacia:

```text
Context Engineering
       ↓
Tool Engineering
       ↓
Skill Engineering
       ↓
Agent Engineering
       ↓
AI Systems Engineering
```

---

# 30. El papel de la arquitectura

Ahora podemos establecer una de las reglas fundamentales del repositorio:

> **No existe Ingeniería de Prompt independiente del modelo.**

La misma estrategia puede producir resultados diferentes según:

* arquitectura;
* entrenamiento;
* capacidad del modelo;
* contexto;
* configuración de inferencia;
* herramientas disponibles;
* modalidad de entrada;
* versión del modelo.

Por ejemplo:

```text
Prompt
  │
  ├── Modelo Dense
  │
  ├── Modelo MoE
  │
  ├── Modelo de razonamiento
  │
  ├── Modelo multimodal
  │
  └── Modelo especializado
```

No debemos asumir que todos interpretarán las instrucciones exactamente igual.

En capítulos posteriores estudiaremos experimentalmente estas diferencias.

---

# 31. El caso de MoE

Un modelo **Mixture of Experts (MoE)** utiliza múltiples "expertos" dentro de una arquitectura con mecanismos de routing.

Una representación simplificada:

```text
                 Entrada
                    ↓
                  Router
              ↙     ↓     ↘
          Experto A B     Experto C
              ↘     ↓     ↙
                  Salida
```

La idea importante para Ingeniería de Prompt es:

> El prompt no selecciona directamente un experto mediante una instrucción como "utiliza el experto 3".

El routing pertenece a la arquitectura y normalmente está gestionado internamente por el sistema.

Sin embargo, la entrada puede afectar el procesamiento interno.

Por eso no debemos afirmar:

> "Los modelos MoE necesitan prompts completamente diferentes."

Debemos formular una pregunta experimental:

> **¿Qué diferencias observables aparecen al aplicar estrategias equivalentes de prompting a modelos con arquitecturas diferentes?**

Esta diferencia entre una afirmación universal y una hipótesis comprobable será recurrente en todo el repositorio.

---

# 32. Una forma profesional de pensar

Un principiante puede preguntar:

> ¿Cuál es el mejor prompt?

Un profesional debería preguntar:

> ¿Cuál es el objetivo de la tarea?

Después:

> ¿Qué modelo estoy utilizando?

Después:

> ¿Qué arquitectura tiene?

Después:

> ¿Qué contexto recibe?

Después:

> ¿Qué salida necesito?

Después:

> ¿Cómo voy a medir la calidad?

Finalmente:

> ¿Qué modificaciones producen una mejora estadísticamente y operacionalmente relevante?

Esta evolución representa precisamente el paso de:

```text
usuario de IA
```

a:

```text
ingeniero de IA
```

---

# 33. Errores conceptuales frecuentes

## Error 1

> "IA y Machine Learning son lo mismo."

Incorrecto.

Machine Learning es un subcampo de la IA.

---

## Error 2

> "Todo Machine Learning utiliza Deep Learning."

Incorrecto.

Existen numerosos métodos de Machine Learning que no son redes neuronales profundas.

---

## Error 3

> "Todo Deep Learning es un LLM."

Incorrecto.

Deep Learning se utiliza en numerosas áreas.

---

## Error 4

> "Todo LLM es IA generativa."

La relación puede ser cierta en muchos sistemas modernos utilizados para generación, pero "LLM" y "IA generativa" no son categorías equivalentes.

Un LLM describe principalmente una familia de modelos de lenguaje de gran escala; IA generativa describe una capacidad o categoría más amplia.

---

## Error 5

> "Un prompt entrena al modelo."

Normalmente, no.

El prompt condiciona la inferencia.

---

## Error 6

> "Cambiar el prompt cambia los parámetros del modelo."

No necesariamente.

Modificar el prompt cambia la entrada o contexto utilizado durante la inferencia.

---

## Error 7

> "El LLM es toda la aplicación."

No.

Puede ser solamente uno de los componentes del sistema.

---

## Error 8

> "Más parámetros significa automáticamente mejor modelo."

No existe una regla universal de ese tipo.

La capacidad depende de múltiples factores:

* arquitectura;
* datos;
* entrenamiento;
* calidad de los datos;
* objetivos;
* postentrenamiento;
* inferencia;
* contexto;
* especialización;
* evaluación.

---

# 34. Nivel profesional: analizar un sistema

Cuando encontremos una aplicación de IA, debemos aprender a descomponerla.

Por ejemplo:

```text
Aplicación de atención al cliente
```

Preguntas:

### 1. ¿Utiliza IA?

Sí.

### 2. ¿Utiliza Machine Learning?

Probablemente, dependiendo de sus componentes.

### 3. ¿Utiliza Deep Learning?

Puede hacerlo.

### 4. ¿Utiliza un LLM?

Si procesa lenguaje mediante un modelo de lenguaje de gran escala, sí.

### 5. ¿Utiliza IA generativa?

Si genera respuestas nuevas, probablemente sí.

### 6. ¿Utiliza RAG?

Depende de su arquitectura.

### 7. ¿Utiliza herramientas?

Puede utilizar APIs, bases de datos u otros sistemas.

### 8. ¿Es un agente?

Depende de si posee características propias de un sistema agente, como capacidad de decidir acciones y ejecutar ciclos de interacción con herramientas.

La conclusión importante es:

> **No debemos clasificar una aplicación únicamente por el nombre comercial que recibe. Debemos analizar sus componentes.**

---

# 35. Nivel avanzado: separar modelo y sistema

En ingeniería debemos distinguir:

```text
MODELO
```

de:

```text
SISTEMA
```

El modelo puede ser:

```text
LLM
```

El sistema puede ser:

```text
Aplicación
+
Prompt
+
Contexto
+
RAG
+
LLM
+
Tools
+
Memory
+
Guardrails
+
Evaluation
```

Por tanto:

```text
modelo ≠ sistema
```

Esta distinción será crítica cuando estudiemos agentes.

---

# 36. Nivel de maestría: pensar en capas

Una forma más rigurosa de estudiar un sistema de IA es separarlo en capas:

```text
Capa 1 — Datos
        ↓
Capa 2 — Entrenamiento
        ↓
Capa 3 — Modelo
        ↓
Capa 4 — Inferencia
        ↓
Capa 5 — Contexto
        ↓
Capa 6 — Prompt
        ↓
Capa 7 — Herramientas
        ↓
Capa 8 — Orquestación
        ↓
Capa 9 — Evaluación
        ↓
Capa 10 — Seguridad
        ↓
Capa 11 — Aplicación
```

Un error en cualquiera de estas capas puede afectar el resultado final.

Por ejemplo:

```text
Prompt excelente
+
Contexto incorrecto
=
Respuesta incorrecta
```

o:

```text
Modelo excelente
+
Herramienta mal configurada
=
Sistema incorrecto
```

Por eso un ingeniero no debe limitarse a optimizar el prompt.

Debe comprender el sistema completo.

---

# 37. Nivel de investigación

En investigación, las fronteras entre estas categorías se vuelven menos rígidas.

Se investigan continuamente cuestiones relacionadas con:

* modelos multimodales;
* modelos de razonamiento;
* modelos con herramientas;
* modelos especializados;
* arquitecturas eficientes;
* Mixture of Experts;
* inferencia eficiente;
* contexto largo;
* memoria;
* agentes;
* entrenamiento y postentrenamiento;
* evaluación;
* seguridad.

Por ello, el estudiante avanzado debe aprender a distinguir:

```text
Concepto establecido
```

de:

```text
Resultado experimental
```

y de:

```text
Hipótesis de investigación
```

No todo lo que aparece en un artículo, publicación o demostración debe convertirse inmediatamente en una "regla de prompting".

---

# 38. Mapa conceptual final

Podemos resumir todo el capítulo así:

```text
                         INTELIGENCIA ARTIFICIAL
                                  │
              ┌───────────────────┴───────────────────┐
              │                                       │
       IA simbólica                              MACHINE LEARNING
                                                      │
                                                      ↓
                                                DEEP LEARNING
                                                      │
                                      ┌───────────────┴───────────────┐
                                      │                               │
                              Redes neuronales                 Otros enfoques
                                      │
                                      ↓
                              MODELOS FUNDACIONALES
                                      │
                    ┌─────────────────┼─────────────────┐
                    ↓                 ↓                 ↓
               Lenguaje            Visión            Multimodal
                    │
                    ↓
                   LLM
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
      Generación          Comprensión/
                           transformación
          │
          ↓
   IA GENERATIVA
          │
          ↓
     APLICACIÓN
          │
     ┌────┼────┐
     ↓    ↓    ↓
 Prompt Context Tools
     │    │    │
     └────┼────┘
          ↓
        Skills
          ↓
       Agentes
          ↓
    Sistema de IA
          │
    ┌─────┴─────┐
    ↓           ↓
Evaluación   Seguridad
```

---

# 39. Lo que debe recordar el estudiante

Si solamente recuerdas diez ideas de este capítulo, deben ser estas:

1. **IA es el concepto más amplio.**

2. **Machine Learning es un subcampo de la IA.**

3. **Deep Learning es una familia de métodos de Machine Learning basada principalmente en redes neuronales profundas.**

4. **Un LLM es un modelo de lenguaje de gran escala.**

5. **Un LLM no representa toda la Inteligencia Artificial.**

6. **IA generativa es un concepto más amplio que los LLM y puede abarcar diferentes modalidades.**

7. **Un modelo no es lo mismo que una aplicación de IA.**

8. **Un prompt normalmente condiciona la inferencia; no equivale a entrenar el modelo.**

9. **El comportamiento de un prompt depende del modelo, la arquitectura, el contexto y otras condiciones del sistema.**

10. **La Ingeniería de Prompt comienza con las instrucciones, pero evoluciona hacia la ingeniería de contexto, herramientas, skills, agentes y sistemas completos.**

---

# 40. Conexión con el siguiente capítulo

Ahora que sabemos qué son IA, ML, DL y LLM, necesitamos comprender **qué ocurre realmente cuando un modelo procesa una entrada**.

El siguiente capítulo estudiará:

```text
Texto
 ↓
Tokenización
 ↓
Tokens
 ↓
Embeddings
 ↓
Transformer
 ↓
Attention
 ↓
Representaciones
 ↓
Predicción
 ↓
Sampling
 ↓
Salida
```

Esto permitirá responder una pregunta fundamental:

> **¿Qué ocurre entre el momento en que escribimos un prompt y el momento en que el modelo genera una respuesta?**

Ese conocimiento será necesario antes de estudiar técnicas avanzadas de Prompt Engineering y, posteriormente, cómo diferentes arquitecturas —como Dense, MoE, modelos de razonamiento y multimodales— pueden modificar la forma en que debemos diseñar y evaluar nuestras interacciones con los modelos.
