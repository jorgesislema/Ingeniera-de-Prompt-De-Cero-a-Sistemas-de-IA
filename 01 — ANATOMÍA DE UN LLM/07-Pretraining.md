# 07 — Pretraining: cómo aprende un LLM

> **Módulo:** Anatomía de un LLM
> **Nivel:** Desde cero → avanzado → Maestría/PhD
> **Prerrequisitos:** Tokenización, embeddings, Transformer, attention y positional information
> **Conceptos clave:** pretraining, next-token prediction, causal language modeling, loss, cross-entropy, backpropagation, gradients, optimizer, batch, step, epoch, generalización, scaling laws.

---

## 1. ¿Qué es el pretraining?

**Pretraining** significa **preentrenamiento**.

Es la etapa en la que un modelo de lenguaje aprende, a partir de enormes cantidades de datos, los patrones estadísticos necesarios para procesar y generar lenguaje.

Una simplificación útil es:

```text
DATOS
  ↓
TOKENIZACIÓN
  ↓
SECUENCIAS DE TOKENS
  ↓
MODELO
  ↓
PREDICCIÓN
  ↓
COMPARACIÓN CON EL TOKEN REAL
  ↓
ERROR (LOSS)
  ↓
BACKPROPAGATION
  ↓
GRADIENTES
  ↓
ACTUALIZACIÓN DE PARÁMETROS
  ↓
REPETIR MILLONES / MILES DE MILLONES DE VECES
  ↓
MODELO PREENTRENADO
```

El objetivo fundamental no es:

> "enseñar al modelo una lista de respuestas".

Es modificar sus parámetros para que aprenda regularidades presentes en los datos.

---

# 2. La idea fundamental

Supongamos que tenemos:

> El perro corre por el ___.

El modelo recibe:

```text
El perro corre por el
```

y debe asignar probabilidades a los posibles siguientes tokens:

```text
parque      0.42
campo       0.18
camino      0.12
bosque      0.07
...
```

Si el siguiente token real era:

```text
parque
```

el entrenamiento intenta aumentar la probabilidad asignada a `parque`.

Pero no se modifica solamente una palabra.

Se modifican pequeñas cantidades en muchos parámetros del modelo.

Después de repetir este proceso una enorme cantidad de veces, los parámetros terminan codificando regularidades estadísticas complejas.

---

# 3. Una analogía sencilla

Imagina a una persona que quiere aprender un idioma leyendo millones de páginas.

No recibe una tabla que diga:

```text
regla 1 → verbo
regla 2 → sustantivo
regla 3 → plural
```

En cambio, observa continuamente ejemplos:

```text
El perro corre.
El gato duerme.
Los perros corren.
Los gatos duermen.
```

Con suficientes ejemplos empieza a reconocer patrones.

El entrenamiento de un LLM tiene una analogía con este proceso, pero existe una diferencia fundamental:

> Un LLM no "comprende" el lenguaje exactamente como una persona.

Aprende representaciones y relaciones mediante optimización matemática sobre sus parámetros.

---

# 4. ¿Qué significa "pretraining"?

La palabra tiene dos partes:

```text
PRE + TRAINING
│      │
│      └── entrenamiento
└───────── previo
```

Se llama **pretraining** porque normalmente ocurre antes de etapas posteriores como:

* instruction tuning,
* fine-tuning,
* preference optimization,
* alignment,
* adaptación a dominios específicos,
* entrenamiento especializado.

Una representación simplificada del desarrollo de un modelo puede ser:

```text
DATOS MASIVOS
      ↓
PRETRAINING
      ↓
MODELO BASE
      ↓
INSTRUCTION TUNING
      ↓
MODELO INSTRUCCIONAL
      ↓
ALIGNMENT / PREFERENCE OPTIMIZATION
      ↓
SISTEMA UTILIZABLE
```

No todos los sistemas modernos siguen exactamente esta secuencia, pero sirve como modelo mental.

---

# 5. ¿Qué aprende durante el pretraining?

Es importante evitar una interpretación demasiado simple.

El modelo no recibe explícitamente instrucciones como:

```text
Aprende gramática.
Aprende programación.
Aprende matemáticas.
Aprende historia.
Aprende razonamiento.
```

El aprendizaje emerge de la optimización sobre los datos y el objetivo de entrenamiento.

Durante el proceso puede desarrollar representaciones relacionadas con:

* sintaxis;
* estructuras lingüísticas;
* relaciones semánticas;
* patrones discursivos;
* programación;
* formatos;
* hechos presentes en los datos;
* relaciones entre conceptos;
* estructuras matemáticas;
* diferentes estilos de escritura;
* regularidades de múltiples idiomas.

Pero esto no significa que cada capacidad esté almacenada como una regla explícita y aislada.

---

# 6. El objetivo clásico: predecir el siguiente token

En muchos LLM autoregresivos, el objetivo fundamental puede expresarse como:

> **Predecir el siguiente token dado el contexto anterior.**

Por ejemplo:

```text
Entrada:

La capital de Francia es
```

El modelo debería asignar una probabilidad elevada a:

```text
París
```

Formalmente:

$$
P(x_t \mid x_1,x_2,\ldots,x_{t-1})
$$

donde:

* \(x_t\) = token que queremos predecir;
* \(x_1,\ldots,x_{t-1}\) = tokens anteriores;
* \(P\) = probabilidad condicionada por el contexto.

---

# 7. Causal Language Modeling

Este enfoque se denomina frecuentemente:

**Causal Language Modeling (CLM)**

La palabra *causal* se refiere a que, para predecir un token, el modelo utiliza los tokens que aparecen antes de él.

Ejemplo:

```text
El gato está sobre la
```

Para predecir el siguiente token:

```text
mesa
```

el modelo utiliza:

```text
El
El gato
El gato está
El gato está sobre
El gato está sobre la
```

pero no puede utilizar durante esa predicción el token objetivo futuro como información de entrada.

---

# 8. El entrenamiento puede verse como muchos problemas pequeños

Consideremos:

```text
El gato duerme en la casa
```

Podemos convertir la secuencia en múltiples objetivos:

```text
Entrada                  Objetivo

El                       gato
El gato                  duerme
El gato duerme           en
El gato duerme en        la
El gato duerme en la     casa
```

En realidad, los modelos procesan secuencias completas de manera eficiente y utilizan máscaras causales para impedir que cada posición vea información futura.

Conceptualmente:

```text
Tokens:

El | gato | duerme | en | la | casa
 ↓     ↓      ↓      ↓    ↓     ↓
gato duerme   en     la  casa    ...
```

Cada posición contribuye al aprendizaje.

---

# 9. Input y target

Una forma sencilla de representarlo:

```text
INPUT:

El gato duerme en la

TARGET:

gato duerme en la casa
```

El modelo recibe la secuencia desplazada respecto al objetivo.

Esto suele denominarse:

**shifted inputs and labels**

o simplemente:

**input-target shifting**.

Visualmente:

```text
posición:  1     2       3      4    5
           ↓     ↓       ↓      ↓    ↓

INPUT:    El    gato   duerme   en   la

TARGET:   gato  duerme   en      la   casa
```

---

# 10. Teacher forcing

Durante el entrenamiento se utiliza normalmente una técnica conocida como:

**Teacher Forcing**

En lugar de esperar a que el modelo genere toda una secuencia y utilizar su propia predicción como entrada siguiente, durante el entrenamiento se proporcionan los tokens correctos como contexto.

Ejemplo:

```text
Entrada correcta:
El gato

Objetivo:
duerme
```

Luego:

```text
Entrada correcta:
El gato duerme

Objetivo:
en
```

Y después:

```text
El gato duerme en

Objetivo:
la
```

Esto hace que el entrenamiento sea mucho más eficiente.

---

# 11. Del token al Transformer

Ahora podemos conectar lo aprendido en los archivos anteriores.

Tenemos:

```text
Texto
 ↓
Tokenización
 ↓
Token IDs
 ↓
Embeddings
 ↓
Información posicional
 ↓
Transformer
 ↓
Representaciones contextualizadas
 ↓
Logits
 ↓
Probabilidades
```

El proceso completo de una predicción puede representarse así:

```text
"El gato duerme"

        ↓

[Tokenización]

El | gato | duerme

        ↓

[Token IDs]

17 | 843 | 2911

        ↓

[Embeddings]

vectores

        ↓

[Información posicional]

vectores + posición

        ↓

[Transformer]

Attention + capas neuronales

        ↓

[Representación final]

        ↓

[Proyección al vocabulario]

        ↓

[Logits]

        ↓

[Softmax]

        ↓

P(siguiente token)
```

---

# 12. ¿Qué son los logits?

Antes de convertir la salida en probabilidades, el modelo produce valores llamados:

**logits**.

Supongamos que el vocabulario contiene:

```text
parque
casa
calle
bosque
```

El modelo podría producir:

```text
parque    4.2
casa      2.1
calle     1.4
bosque    0.3
```

Estos valores todavía no son probabilidades.

Posteriormente se aplica una función como **softmax**:

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

donde:

* \(z_i\) = logit;
* \(P_i\) = probabilidad;
* \(e\) = número de Euler.

El resultado podría parecer:

```text
parque    0.83
casa      0.10
calle     0.05
bosque    0.02
```

---

# 13. El modelo se equivoca

Supongamos que el token correcto era:

```text
parque
```

y el modelo produjo:

```text
parque    0.20
casa      0.70
calle     0.08
bosque    0.02
```

El modelo estaba equivocado.

Necesitamos cuantificar cuánto se equivocó.

Para eso utilizamos una **función de pérdida**.

---

# 14. Loss

La palabra **loss** significa pérdida o función de pérdida.

Representa una medida del error del modelo respecto al objetivo.

En términos simples:

```text
Predicción incorrecta
        ↓
     LOSS alto

Predicción correcta
        ↓
     LOSS bajo
```

Durante el entrenamiento se intenta minimizar esta función.

---

# 15. Cross-Entropy Loss

En modelos de clasificación y lenguaje es muy común utilizar:

**Cross-Entropy Loss**

Para un único objetivo:

$$
L=-\log P(y)
$$

donde:

* \(y\) = token correcto;
* \(P(y)\) = probabilidad que el modelo asignó al token correcto.

Ejemplo conceptual:

Si:

$$
P(y)=0.9
$$

entonces:

$$
L=-\log(0.9)
$$

El error es pequeño.

Si:

$$
P(y)=0.01
$$

entonces:

$$
L=-\log(0.01)
$$

El error es mucho mayor.

La idea esencial es:

> **Cuanto menor sea la probabilidad asignada al token correcto, mayor será la penalización.**

---

# 16. Loss en una secuencia

En una secuencia no tenemos un solo objetivo.

Tenemos muchos.

Por ejemplo:

```text
El → gato
gato → duerme
duerme → en
en → la
la → casa
```

Cada predicción produce una pérdida.

Podemos obtener una pérdida promedio:

$$
L =
-\frac{1}{T}
\sum_{t=1}^{T}
\log P(x_t|x_{<t})
$$

donde:

* \(T\) = cantidad de posiciones;
* \(x_t\) = token objetivo;
* \(x_{<t}\) = tokens anteriores.

Esta ecuación es una de las ideas matemáticas fundamentales detrás del entrenamiento autoregresivo de lenguaje.

---

# 17. Perplexity

A partir de la pérdida de lenguaje se puede definir una métrica conocida como:

**Perplexity (PPL)**

Conceptualmente:

$$
PPL=e^L
$$

para una pérdida promedio \(L\) basada en logaritmos naturales.

Una interpretación intuitiva:

> La perplexity indica qué tan "sorprendido" está el modelo frente a los datos.

Una menor perplexity, bajo las mismas condiciones de evaluación, suele indicar mejor capacidad predictiva sobre ese conjunto.

Pero:

> **Una menor perplexity no significa automáticamente que un modelo sea mejor en todas las tareas.**

Por ejemplo, una métrica de lenguaje no captura por sí sola:

* seguridad;
* factualidad;
* utilidad;
* seguimiento de instrucciones;
* calidad de código;
* razonamiento;
* preferencias humanas.

---

# 18. Forward pass

Ahora podemos observar una iteración de entrenamiento.

El primer paso es el:

**Forward Pass**

El modelo recibe los datos y calcula una predicción.

```text
INPUT
  ↓
Embedding
  ↓
Positional information
  ↓
Transformer layers
  ↓
Logits
  ↓
Probabilidades
  ↓
Loss
```

Todavía no hemos modificado los parámetros.

---

# 19. Backpropagation

Después de calcular el error necesitamos determinar:

> ¿Qué parámetros contribuyeron al error y en qué dirección deberían cambiar?

Aquí aparece:

**Backpropagation**

o **retropropagación del error**.

La idea fundamental es utilizar derivadas para calcular cómo cambia la pérdida respecto a los parámetros.

Si tenemos:

$$
L = f(\theta)
$$

donde \(\theta\) representa los parámetros, queremos calcular:

$$
\nabla_\theta L
$$

Ese vector es el:

**gradiente**.

---

# 20. ¿Qué es un gradiente?

El gradiente nos proporciona información sobre la dirección en la que aumenta la función de pérdida.

Si queremos minimizarla, debemos mover los parámetros en la dirección contraria.

Conceptualmente:

```text
LOSS
 ↑
 │       •
 │      /
 │     /
 │  •
 │ /
 │•________________
        parámetros
```

El entrenamiento busca desplazarse hacia regiones donde la pérdida sea menor.

---

# 21. Actualización de parámetros

Una forma clásica de expresar una actualización mediante descenso de gradiente es:

$$
\theta_{nuevo}
=
\theta_{actual}
-
\eta\nabla_\theta L
$$

donde:

* \(\theta\) = parámetros;
* \(\eta\) = learning rate;
* \(\nabla_\theta L\) = gradiente de la pérdida.

La idea es:

```text
parámetros actuales
        ↓
calcular gradiente
        ↓
determinar dirección
        ↓
aplicar actualización
        ↓
nuevos parámetros
```

---

# 22. Learning rate

El **learning rate** determina el tamaño aproximado de los pasos de actualización.

Si es demasiado pequeño:

```text
pasos pequeños
↓
aprendizaje lento
```

Si es demasiado grande:

```text
pasos demasiado grandes
↓
inestabilidad
```

Visualmente:

```text
Learning rate pequeño:

• → • → • → • → •


Learning rate demasiado grande:

•      •
   ↘
        •
 ↗
             •
```

Por eso el aprendizaje requiere controlar cuidadosamente la dinámica de optimización.

---

# 23. Optimizers

En la práctica, los grandes modelos no suelen entrenarse simplemente con el descenso de gradiente más básico.

Se utilizan **optimizadores**.

Entre los conceptos fundamentales aparecen:

* SGD;
* Momentum;
* Adam;
* AdamW;
* variantes especializadas para entrenamiento de modelos grandes.

Por ejemplo, **Adam** incorpora información relacionada con momentos del gradiente para adaptar las actualizaciones.

**AdamW** separa de forma explícita el weight decay de la actualización adaptativa.

No es necesario memorizar las ecuaciones para comprender la arquitectura conceptual.

Lo importante es entender:

```text
LOSS
 ↓
GRADIENT
 ↓
OPTIMIZER
 ↓
PARAMETER UPDATE
```

---

# 24. ¿Qué son los parámetros durante el entrenamiento?

Los parámetros son valores numéricos que el modelo modifica durante el aprendizaje.

Por ejemplo:

```text
θ1
θ2
θ3
θ4
...
θN
```

Durante el entrenamiento:

```text
θ1 → θ1'
θ2 → θ2'
θ3 → θ3'
...
```

Una sola actualización puede modificar una enorme cantidad de parámetros.

Por eso un LLM no se "programa" escribiendo manualmente sus conocimientos.

Sus capacidades están relacionadas con los patrones aprendidos mediante millones o miles de millones de actualizaciones.

---

# 25. El ciclo completo

Podemos resumir una iteración:

```text
             DATOS
               ↓
          TOKENIZACIÓN
               ↓
            INPUT
               ↓
         FORWARD PASS
               ↓
            LOGITS
               ↓
         PROBABILIDADES
               ↓
              LOSS
               ↓
        BACKPROPAGATION
               ↓
           GRADIENTES
               ↓
          OPTIMIZADOR
               ↓
       NUEVOS PARÁMETROS
               ↓
          SIGUIENTE BATCH
```

Este ciclo se repite una enorme cantidad de veces.

---

# 26. ¿Qué es un batch?

Un **batch** es un conjunto de ejemplos procesados conjuntamente en una actualización de entrenamiento.

En lugar de hacer:

```text
ejemplo 1
→ actualización
```

podemos procesar:

```text
ejemplo 1
ejemplo 2
ejemplo 3
...
ejemplo N
```

y calcular una pérdida agregada.

Después se actualizan los parámetros.

---

# 27. ¿Qué es un step?

Un **training step** suele representar una actualización de los parámetros basada en un batch.

Conceptualmente:

```text
Batch
 ↓
Forward
 ↓
Loss
 ↓
Backward
 ↓
Optimizer
 ↓
1 step
```

Por tanto:

```text
muchos ejemplos
       ↓
     batch
       ↓
     step
       ↓
 actualización
```

---

# 28. ¿Qué es una época (epoch)?

Una **epoch** representa aproximadamente una pasada completa sobre el conjunto de entrenamiento.

Por ejemplo, si tenemos:

```text
1.000.000 ejemplos
```

y utilizamos batches de:

```text
1.000 ejemplos
```

entonces, simplificando:

$$
1000\ batches \approx 1\ epoch
$$

En datasets gigantescos utilizados para pretraining moderno, la relación entre epochs, tokens procesados y datos únicos puede ser mucho más compleja.

Por eso, en LLMs es especialmente importante hablar de:

**tokens vistos durante el entrenamiento**.

---

# 29. Tokens vistos

Una métrica muy importante es:

> ¿Cuántos tokens ha procesado el entrenamiento?

Podemos tener:

```text
tokens del dataset
×
número de pasadas
≈
tokens procesados
```

En modelos grandes, el presupuesto de entrenamiento suele describirse en términos de tokens, parámetros y cómputo.

---

# 30. Pretraining no significa simplemente "leer Internet"

Una simplificación frecuente es:

> "El modelo aprende leyendo todo Internet."

No es técnicamente precisa.

Un pipeline de datos real puede incluir etapas como:

```text
Fuentes
 ↓
recolección
 ↓
extracción
 ↓
normalización
 ↓
filtrado
 ↓
deduplicación
 ↓
clasificación
 ↓
mezcla de datasets
 ↓
tokenización
 ↓
dataset final
```

Las fuentes pueden incluir, dependiendo del modelo y sus permisos/licencias:

* páginas web;
* libros;
* documentación;
* código;
* artículos;
* conversaciones;
* datasets especializados;
* contenido sintético;
* datos licenciados;
* otras fuentes.

El dataset exacto de un modelo comercial puede no ser completamente público.

---

# 31. La calidad de los datos importa

Más datos no significa automáticamente mejor entrenamiento.

Podemos imaginar:

```text
100 TB de datos de baja calidad
```

frente a:

```text
20 TB de datos cuidadosamente filtrados
```

La segunda colección podría ser más útil para determinados objetivos.

Por eso existen procesos de:

* limpieza;
* deduplicación;
* filtrado de spam;
* detección de contenido de baja calidad;
* filtrado de código;
* clasificación de documentos;
* control de idioma;
* eliminación de información no deseada;
* balanceo de fuentes.

---

# 32. Deduplicación

Supongamos que el dataset contiene miles de copias del mismo documento.

```text
Documento A
Documento A
Documento A
Documento A
Documento B
Documento C
```

Esto puede introducir problemas.

Por ello se aplican técnicas de **deduplicación**.

La deduplicación puede ser:

* exacta;
* aproximada;
* basada en hashes;
* basada en similitud;
* aplicada a documentos completos o fragmentos.

Esto también tiene implicaciones para la evaluación.

---

# 33. Data contamination

Existe otro problema:

**data contamination**.

Ocurre cuando información utilizada para evaluar el modelo ya estaba presente en sus datos de entrenamiento o en datos estrechamente relacionados.

Ejemplo:

```text
Dataset de entrenamiento
        ↓
incluye preguntas de un benchmark
        ↓
modelo
        ↓
benchmark
        ↓
resultado aparentemente excelente
```

Pero el resultado puede estar inflado porque el modelo ya vio información relacionada.

Por eso la evaluación de modelos requiere controlar cuidadosamente la contaminación.

---

# 34. Memorización versus generalización

Una pregunta importante es:

> ¿El modelo realmente aprendió patrones generales o simplemente memorizó ejemplos?

La respuesta no es binaria.

Los modelos pueden:

* generalizar;
* memorizar ciertos ejemplos;
* combinar patrones;
* reproducir fragmentos vistos;
* aprender representaciones abstractas.

Por eso es necesario distinguir:

```text
MEMORIZACIÓN
     vs.
GENERALIZACIÓN
```

La investigación moderna estudia continuamente esta relación.

---

# 35. ¿Qué significa generalizar?

Supongamos que durante entrenamiento aparecen:

```text
2 + 2 = 4
3 + 3 = 6
4 + 4 = 8
```

Un modelo puede aprender patrones que le permitan responder ejemplos que no aparecieron exactamente de esa manera.

Eso sería una forma de generalización.

Pero no significa que cualquier capacidad nueva sea necesariamente producto de un razonamiento humano equivalente.

---

# 36. Overfitting

El **overfitting** ocurre cuando un modelo se ajusta demasiado a los datos de entrenamiento y pierde capacidad para generalizar adecuadamente.

Conceptualmente:

```text
Entrenamiento:
     excelente

Datos nuevos:
     deficiente
```

En modelos de lenguaje grandes, el análisis del sobreajuste es más complejo que en ejemplos pequeños de machine learning.

No obstante, el principio sigue siendo importante:

> El objetivo no es simplemente memorizar el dataset, sino aprender regularidades útiles que generalicen.

---

# 37. Escalamiento

Una de las ideas más importantes de la investigación de LLM es el **scaling**.

El rendimiento puede depender de múltiples recursos:

```text
PARÁMETROS
    +
DATOS
    +
CÓMPUTO
```

Podemos representarlo:

```text
             COMPUTE
                ↑
                │
DATOS ←─────── MODELO
                │
                ↓
           PARÁMETROS
```

No existe una única variable que explique por sí sola la calidad.

---

# 38. Scaling laws

Las **scaling laws** estudian cómo cambia el rendimiento o la pérdida cuando aumentan variables como:

* número de parámetros;
* cantidad de tokens;
* cómputo utilizado.

Una intuición simplificada:

```text
más cómputo
     +
más datos apropiados
     +
capacidad suficiente
     ↓
menor pérdida esperada
```

Pero existen límites, costos y rendimientos decrecientes.

La optimización del presupuesto de entrenamiento es un problema central de la ingeniería de modelos.

---

# 39. Parámetros no son inteligencia

Es importante evitar esta equivalencia:

```text
más parámetros = automáticamente mejor modelo
```

No necesariamente.

El resultado depende también de:

* arquitectura;
* calidad de datos;
* cantidad de datos;
* distribución de datos;
* objetivo de entrenamiento;
* optimización;
* regularización;
* contexto;
* post-training;
* calidad de evaluación;
* eficiencia computacional.

Por eso comparar únicamente el número de parámetros puede ser engañoso.

---

# 40. Cómputo

Entrenar modelos grandes requiere enormes cantidades de operaciones matemáticas.

A nivel conceptual:

```text
Datos
 ↓
GPU / TPU / aceleradores
 ↓
operaciones tensoriales
 ↓
forward
 ↓
backward
 ↓
actualización
```

Los sistemas modernos utilizan hardware especializado para acelerar operaciones como:

* multiplicaciones de matrices;
* convoluciones en algunos componentes;
* operaciones tensoriales;
* attention;
* comunicación entre dispositivos.

---

# 41. Entrenamiento distribuido

Un modelo grande puede no caber en un único dispositivo.

Por ello se distribuye el entrenamiento entre múltiples aceleradores.

Conceptualmente:

```text
                 MODELO
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
      GPU 1      GPU 2      GPU 3
        │          │          │
        └──────────┼──────────┘
                   ↓
             sincronización
                   ↓
             actualización
```

Existen diferentes estrategias, entre ellas:

* data parallelism;
* tensor parallelism;
* pipeline parallelism;
* sharding de estados del optimizador;
* combinaciones de estas técnicas.

La implementación real es un área avanzada de sistemas distribuidos.

---

# 42. Mixed precision

El entrenamiento moderno suele aprovechar representaciones numéricas de menor precisión cuando es seguro hacerlo.

Por ejemplo:

```text
FP32
BF16
FP16
```

La motivación es reducir:

* memoria;
* transferencia de datos;
* tiempo de cómputo.

Mientras se mantiene una estabilidad numérica suficiente.

Esto permite entrenar modelos mucho más grandes con el mismo presupuesto de hardware.

---

# 43. Gradient accumulation

Cuando un batch grande no cabe en memoria, puede utilizarse:

**gradient accumulation**.

Conceptualmente:

```text
micro-batch 1
     ↓
gradiente

micro-batch 2
     ↓
gradiente

micro-batch 3
     ↓
gradiente

micro-batch 4
     ↓
gradiente acumulado
     ↓
actualización
```

Así se puede aproximar un batch efectivo mayor sin almacenar todo simultáneamente.

---

# 44. Checkpoints

El entrenamiento puede durar mucho tiempo.

Por ello se guardan **checkpoints**.

Un checkpoint puede contener información como:

* parámetros del modelo;
* estado del optimizador;
* estado del scheduler;
* información necesaria para reanudar el entrenamiento.

Por ejemplo:

```text
Step 100.000
      ↓
checkpoint

Step 200.000
      ↓
checkpoint

Step 300.000
      ↓
checkpoint
```

Si ocurre una interrupción, el entrenamiento puede continuar desde un punto guardado.

---

# 45. Scheduler del learning rate

El learning rate no necesariamente permanece constante.

Puede utilizarse un **learning-rate schedule**.

Una estrategia conceptual puede ser:

```text
learning rate
      ↑
      │    /\
      │   /  \
      │  /    \
      │ /      \________
      └──────────────────→ tiempo
```

Al principio puede aumentar durante un período de **warmup** y posteriormente disminuir.

La forma exacta depende de la configuración de entrenamiento.

---

# 46. Pretraining versus inference

Esta distinción es fundamental.

## Pretraining

```text
DATOS
 ↓
PREDICCIÓN
 ↓
LOSS
 ↓
GRADIENTES
 ↓
ACTUALIZACIÓN DE PARÁMETROS
```

Los parámetros cambian.

## Inference

```text
PROMPT
 ↓
MODELO
 ↓
PREDICCIÓN
 ↓
TOKEN
 ↓
SIGUIENTE TOKEN
 ↓
...
```

Los parámetros normalmente permanecen congelados.

Esta diferencia explica muchas confusiones sobre los LLM.

---

# 47. Cuando escribes un prompt, no estás reentrenando el modelo

Supongamos que escribes:

> "A partir de ahora responde como auditor."

El prompt no modifica permanentemente los pesos del modelo.

No ocurre:

```text
prompt
 ↓
cambiar parámetros
 ↓
nuevo modelo
```

Ocurre:

```text
prompt
 ↓
contexto
 ↓
inferencia
 ↓
predicción
```

Por eso un prompt puede cambiar significativamente una respuesta sin modificar los parámetros permanentes del modelo.

---

# 48. Entonces, ¿cómo puede el prompt cambiar tanto la respuesta?

Porque el prompt cambia la información que recibe el modelo durante la inferencia.

Recordemos:

$$
P(x_t|x_{<t})
$$

Si modificamos el contexto:

```text
Contexto A
```

obtenemos una distribución:

```text
P_A
```

Si modificamos el contexto:

```text
Contexto B
```

obtenemos:

```text
P_B
```

Por tanto:

```text
PROMPT A
   ↓
contexto A
   ↓
distribución A


PROMPT B
   ↓
contexto B
   ↓
distribución B
```

El modelo puede ser exactamente el mismo.

---

# 49. Esto conecta directamente con la Ingeniería de Prompt

Aquí aparece uno de los principios centrales de este repositorio:

> **El prompt no programa directamente los parámetros del modelo; condiciona el proceso de inferencia.**

Podemos visualizarlo:

```text
                 MODELO
            ┌───────────────┐
            │   PARÁMETROS  │
            │   APRENDIDOS  │
            └───────┬───────┘
                    │
                    │
PROMPT ─────────────┤
                    ↓
               INFERENCIA
                    ↓
              DISTRIBUCIÓN
                    ↓
               GENERACIÓN
```

Esto permite comprender por qué:

* cambiar el prompt puede cambiar la respuesta;
* agregar contexto puede cambiar la respuesta;
* cambiar el modelo puede cambiar la respuesta;
* cambiar la temperatura puede cambiar la respuesta;
* cambiar el contexto disponible puede cambiar la respuesta.

---

# 50. Pretraining no es instruction following

Un modelo base entrenado principalmente con next-token prediction puede saber mucho sobre lenguaje y aun así no comportarse como un asistente conversacional.

Por ejemplo, ante:

> Explica qué es una base de datos.

un modelo base podría continuar:

> Explica qué es una base de datos. Una base de datos es...

Pero no necesariamente seguiría todas las convenciones esperadas de un asistente.

Para mejorar el seguimiento de instrucciones se utilizan etapas posteriores.

---

# 51. Modelo base versus modelo instruccional

Podemos conceptualizar:

```text
PRETRAINING
     ↓
BASE MODEL
     ↓
POST-TRAINING
     ↓
INSTRUCTION-FOLLOWING MODEL
```

El **base model** aprende principalmente las regularidades de los datos según su objetivo de entrenamiento.

El modelo posterior puede recibir entrenamiento adicional para:

* seguir instrucciones;
* conversar;
* utilizar herramientas;
* producir formatos específicos;
* rechazar determinadas solicitudes;
* comportarse según determinadas políticas;
* responder de manera más útil.

Esto será estudiado en los siguientes archivos.

---

# 52. Continued pretraining

Existe también el concepto de:

**continued pretraining**

o entrenamiento adicional sobre un modelo ya preentrenado.

Ejemplo conceptual:

```text
LLM general
     ↓
continued pretraining
     ↓
datos de un dominio específico
     ↓
modelo adaptado
```

Puede utilizarse para adaptar el modelo a distribuciones de datos particulares.

No debe confundirse automáticamente con fine-tuning supervisado.

---

# 53. Pretraining versus fine-tuning

Una distinción simplificada:

| Característica  | Pretraining                         | Fine-tuning                       |
| --------------- | ----------------------------------- | --------------------------------- |
| Objetivo        | Aprender representaciones generales | Adaptar comportamiento            |
| Datos           | Muy grandes y diversos              | Más específicos                   |
| Escala          | Enorme                              | Generalmente menor                |
| Costo           | Muy alto                            | Comparativamente menor            |
| Objetivo típico | Predecir tokens                     | Aprender una tarea/comportamiento |
| Resultado       | Modelo base                         | Modelo especializado              |

La frontera exacta depende de la técnica utilizada.

---

# 54. Pretraining versus RAG

Otra confusión frecuente:

> "Si agrego documentos al contexto, estoy entrenando al modelo."

No.

En RAG:

```text
Documentos
 ↓
indexación
 ↓
retrieval
 ↓
contexto
 ↓
LLM
```

Los parámetros del modelo normalmente no cambian.

En pretraining:

```text
datos
 ↓
loss
 ↓
gradientes
 ↓
actualización
 ↓
parámetros modificados
```

Esta diferencia es fundamental para diseñar sistemas empresariales.

---

# 55. Seguridad: data poisoning

El entrenamiento también tiene una dimensión de seguridad.

Un atacante podría intentar introducir datos maliciosos en fuentes utilizadas para entrenar un modelo.

Conceptualmente:

```text
Dataset legítimo
       +
datos manipulados
       ↓
pretraining
       ↓
modelo
       ↓
comportamiento afectado
```

Esto se conoce como:

**data poisoning**.

No todo dato incorrecto constituye automáticamente un ataque de poisoning. El concepto implica una manipulación intencional destinada a afectar el comportamiento o rendimiento del sistema.

---

# 56. Memorization y privacidad

Los modelos pueden memorizar ciertos fragmentos de sus datos de entrenamiento.

Esto plantea preguntas relacionadas con:

* privacidad;
* información personal;
* copyright;
* extracción de datos;
* reproducción de contenido;
* gobernanza de datasets.

Por eso el diseño del dataset es también una cuestión de seguridad y gobernanza.

---

# 57. ¿El modelo "guarda documentos"?

No debemos imaginar el modelo como:

```text
archivo.pdf
archivo.docx
web.html
```

dentro de una carpeta.

El entrenamiento modifica millones o miles de millones de parámetros.

Una representación conceptual es:

```text
DATOS
 ↓
patrones
 ↓
optimización
 ↓
parámetros
```

Por tanto:

> **Los parámetros no son una base de datos convencional de documentos.**

Sin embargo, determinados patrones o información pueden quedar reflejados en los parámetros, incluida cierta memorización.

---

# 58. ¿Dónde queda el conocimiento?

La pregunta:

> "¿Dónde está almacenado el conocimiento del modelo?"

no tiene una respuesta equivalente a:

> "Está en la posición 48.392 del archivo."

El conocimiento está distribuido en las representaciones y parámetros aprendidos.

Esto se relaciona con la idea de:

**distributed representations**.

Un concepto no tiene necesariamente un único parámetro responsable.

---

# 59. Un modelo mental más preciso

Podemos pensar:

```text
DATOS
  ↓
observaciones estadísticas
  ↓
optimización
  ↓
PARÁMETROS
  ↓
representaciones distribuidas
  ↓
INFERENCIA
  ↓
predicciones
```

No:

```text
DATOS
  ↓
copiar documentos
  ↓
base de datos
```

---

# 60. El gran ciclo del aprendizaje

Todo el pretraining puede condensarse en:

```text
┌──────────────────────────┐
│          DATOS           │
└────────────┬─────────────┘
             ↓
       TOKENIZACIÓN
             ↓
          BATCH
             ↓
       FORWARD PASS
             ↓
          LOGITS
             ↓
       PROBABILIDADES
             ↓
            LOSS
             ↓
      BACKPROPAGATION
             ↓
         GRADIENTES
             ↓
        OPTIMIZADOR
             ↓
    ACTUALIZAR PARÁMETROS
             │
             └───────────────┐
                             ↓
                       SIGUIENTE BATCH
```

Este ciclo puede repetirse una cantidad enorme de veces.

---

# 61. Desde el punto de vista matemático

Podemos representar el modelo como:

$$
f_\theta(x)
$$

donde:

* \(x\) = entrada;
* \(\theta\) = parámetros;
* \(f\) = transformación realizada por la red.

El entrenamiento busca encontrar parámetros:

$$
\theta^*
$$

que minimicen una función de pérdida:

$$
\theta^*
=
\arg\min_{\theta}
L(\theta)
$$

En lenguaje autoregresivo, una forma simplificada de la pérdida es:

$$
L(\theta)
=
-\sum_t
\log
P_\theta(x_t|x_{<t})
$$

Por tanto:

```text
datos
 ↓
Pθ(token | contexto)
 ↓
loss
 ↓
optimización
 ↓
θ*
```

El resultado es un conjunto de parámetros optimizados respecto al objetivo y datos utilizados.

---

# 62. ¿Por qué el modelo puede aprender gramática sin recibir reglas gramaticales?

Porque las regularidades gramaticales aparecen en los datos.

Si el dataset contiene millones de ejemplos:

```text
Los perros corren.
Los gatos duermen.
Las aves vuelan.
```

el modelo encuentra regularidades estadísticas.

No necesita recibir explícitamente:

```text
Artículo plural
+
sustantivo plural
+
verbo conjugado
```

como reglas escritas.

La red aprende representaciones que permiten predecir mejor los tokens.

---

# 63. ¿Eso significa que el modelo "descubre las reglas"?

No necesariamente en el sentido clásico.

El modelo no tiene que construir un libro interno de gramática con reglas explícitas.

Puede representar patrones mediante transformaciones distribuidas.

Por eso es mejor decir:

> El entrenamiento permite que el modelo aprenda representaciones y regularidades que ayudan a predecir los datos.

Esto es más preciso que afirmar simplemente:

> "El modelo aprende todas las reglas."

---

# 64. Un punto avanzado: el objetivo no es "saber"

Desde una perspectiva matemática, el entrenamiento optimiza una función.

El objetivo directo es reducir una pérdida.

No existe necesariamente una función interna llamada:

```text
SABER
```

El modelo aprende porque la optimización modifica sus parámetros de manera que las predicciones mejoran.

Esto es importante para comprender una característica fundamental de los LLM:

> **Una capacidad útil puede emerger de un objetivo de predicción aparentemente simple.**

---

# 65. ¿Cómo puede surgir algo tan complejo de un objetivo tan simple?

La respuesta completa sigue siendo objeto de investigación.

Pero conceptualmente:

```text
objetivo simple
       +
datos enormes
       +
modelo con alta capacidad
       +
optimización
       ↓
representaciones complejas
       ↓
capacidades diversas
```

El hecho de que el objetivo sea relativamente simple no significa que la función aprendida sea simple.

---

# 66. Investigación: loss landscape

A nivel de investigación podemos imaginar la función de pérdida como un paisaje matemático de alta dimensión.

```text
                 pérdida
                   ↑
                   │
        /\         │
       /  \        │
  ____/    \_______│____
        \       /
         \_____/
             ↓
        parámetros
```

En realidad, el espacio tiene una dimensionalidad enorme.

Cada parámetro representa una dimensión potencial del espacio de parámetros.

Por eso las visualizaciones bidimensionales son solamente analogías.

---

# 67. Optimización no convexa

Los problemas de entrenamiento de redes neuronales profundas son generalmente **no convexos**.

Esto significa que la función de pérdida puede contener una estructura compleja con:

* regiones de pendiente;
* valles;
* mesetas;
* puntos críticos;
* múltiples soluciones;
* simetrías;
* regiones de distinta geometría.

No existe normalmente una garantía sencilla de encontrar el mínimo global mediante una fórmula directa.

El entrenamiento utiliza métodos iterativos.

---

# 68. Representaciones internas

Durante el entrenamiento se desarrollan representaciones internas.

Podemos simplificar:

```text
tokens
 ↓
embeddings
 ↓
capas tempranas
 ↓
capas intermedias
 ↓
capas profundas
 ↓
representación para predicción
```

Las diferentes capas pueden desarrollar transformaciones con distintos niveles de abstracción.

Sin embargo:

> No debe interpretarse automáticamente que cada capa tenga una única función humana claramente separada.

La información suele estar distribuida y superpuesta.

---

# 69. Superposición y representaciones distribuidas

En investigación de interpretabilidad aparece el problema de que múltiples conceptos pueden representarse de manera superpuesta.

Una representación puede codificar información relacionada con múltiples características.

Esto dificulta responder preguntas como:

> "¿Qué parámetro representa exactamente el concepto X?"

La respuesta normalmente no es:

```text
parámetro 184293 = concepto X
```

sino una estructura distribuida.

---

# 70. ¿Qué significa "el modelo aprendió"?

Una definición técnicamente útil sería:

> **El modelo aprendió cuando la optimización produjo parámetros que permiten obtener mejores predicciones sobre la distribución objetivo de datos.**

Esto evita una interpretación antropomórfica.

No necesitamos asumir que el modelo aprende de la misma manera que un ser humano.

---

# 71. Pretraining y contexto

Ahora podemos conectar este capítulo con el anterior sobre información posicional.

Durante el entrenamiento, el modelo aprende utilizando secuencias de tokens.

Por tanto:

```text
TOKEN
 +
POSICIÓN
 +
CONTEXTO
 ↓
TRANSFORMER
 ↓
PREDICCIÓN
```

La estructura del contexto influye directamente en las representaciones que el modelo desarrolla.

Esto ayuda a explicar por qué el orden de los tokens importa.

---

# 72. Pretraining y Prompt Engineering

Podemos conectar todo lo estudiado:

```text
PRETRAINING
     ↓
aprende parámetros
     ↓
MODELO
     ↓
PROMPT
     ↓
CONTEXTO
     ↓
INFERENCIA
     ↓
PREDICCIÓN
     ↓
RESPUESTA
```

El prompt aparece **después** del pretraining.

Por eso:

> El prompt no crea el conocimiento básico del modelo desde cero.

Lo que hace es proporcionar condiciones de entrada que afectan la inferencia.

---

# 73. Un ejemplo completo

Supongamos que el modelo fue preentrenado con grandes cantidades de texto.

Después escribimos:

```text
Explica qué es un Transformer para un estudiante de ingeniería.
Utiliza un ejemplo sencillo.
```

Durante inferencia:

```text
PROMPT
  ↓
TOKENIZACIÓN
  ↓
EMBEDDINGS
  ↓
POSICIÓN
  ↓
TRANSFORMER
  ↓
ATTENTION
  ↓
LOGITS
  ↓
SAMPLING / DECODIFICACIÓN
  ↓
RESPUESTA
```

Los parámetros aprendidos durante pretraining proporcionan la capacidad base.

El prompt condiciona cómo se utiliza esa capacidad.

---

# 74. Error conceptual frecuente

### Error

> "Cada vez que hablo con ChatGPT, el modelo se vuelve a entrenar con mi prompt."

### Corrección

En una inferencia normal:

```text
prompt
 ↓
contexto
 ↓
inferencia
 ↓
respuesta
```

Los parámetros del modelo no se actualizan como parte de esa generación.

Puede existir memoria, almacenamiento de conversaciones, sistemas de aprendizaje posteriores o procesos de entrenamiento separados dependiendo del producto y configuración.

Pero eso es diferente del mecanismo de inferencia normal.

---

# 75. Otro error frecuente

### Error

> "El modelo tiene todos los documentos dentro de sus parámetros."

### Corrección

Los parámetros contienen representaciones aprendidas de patrones estadísticos.

No funcionan como un sistema tradicional de archivos o una base de datos documental.

Puede existir memorización parcial, pero no debe confundirse con almacenamiento literal de todo el dataset.

---

# 76. Otro error frecuente

### Error

> "Un modelo con más parámetros siempre es superior."

### Corrección

La capacidad depende de múltiples factores:

```text
arquitectura
+
datos
+
calidad de datos
+
cómputo
+
optimización
+
post-training
+
evaluación
+
contexto
```

Los parámetros son solamente una variable.

---

# 77. Otro error frecuente

### Error

> "Pretraining significa entrenar el modelo para responder preguntas."

### Corrección

El objetivo base puede ser mucho más general:

> predecir tokens según el contexto.

La capacidad de responder preguntas y seguir instrucciones puede desarrollarse posteriormente mediante entrenamiento adicional.

---

# 78. Resumen conceptual

El pretraining puede entenderse como:

```text
GRANDES CANTIDADES DE DATOS
            ↓
       TOKENIZACIÓN
            ↓
         SECUENCIAS
            ↓
         PREDICCIÓN
            ↓
            LOSS
            ↓
       BACKPROPAGATION
            ↓
         GRADIENTES
            ↓
         OPTIMIZADOR
            ↓
      CAMBIO DE PESOS
            ↓
      NUEVA ITERACIÓN
            ↓
          ...
            ↓
      MODELO BASE
```

---

# 79. Mapa conceptual del capítulo

```text
                         PRETRAINING
                              │
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
        DATOS              OBJETIVO           CÓMPUTO
          │                   │                   │
          ↓                   ↓                   ↓
     filtrado             next-token        GPU / TPU
     limpieza             prediction        distribuido
     deduplicación        CLM               precision
          │                   │
          └──────────┬────────┘
                     ↓
                FORWARD PASS
                     ↓
                  LOGITS
                     ↓
               PROBABILIDADES
                     ↓
                    LOSS
                     ↓
             BACKPROPAGATION
                     ↓
                 GRADIENTES
                     ↓
                OPTIMIZADOR
                     ↓
             NUEVOS PARÁMETROS
                     │
                     └───────────────┐
                                     ↓
                              SIGUIENTE BATCH
```

---

# 80. La cadena completa que debes recordar

Una de las cadenas más importantes de este módulo es:

```text
DATOS
 ↓
TOKENS
 ↓
EMBEDDINGS
 ↓
POSICIÓN
 ↓
TRANSFORMER
 ↓
LOGITS
 ↓
PROBABILIDADES
 ↓
LOSS
 ↓
GRADIENTES
 ↓
OPTIMIZACIÓN
 ↓
PARÁMETROS ACTUALIZADOS
```

Y posteriormente:

```text
PROMPT
 ↓
TOKENS
 ↓
EMBEDDINGS
 ↓
POSICIÓN
 ↓
TRANSFORMER
 ↓
LOGITS
 ↓
PROBABILIDADES
 ↓
GENERACIÓN
```

Observa la diferencia:

```text
             PRETRAINING                 INFERENCE
                  │                          │
                  ↓                          ↓
                LOSS                    RESPUESTA
                  │
                  ↓
             GRADIENTES
                  │
                  ↓
       CAMBIO DE PARÁMETROS
```

**Durante el pretraining los parámetros cambian.**

**Durante la inferencia normal, los parámetros permanecen fijos.**

---

# 81. Nivel Maestría / PhD: preguntas que abre este capítulo

A partir de aquí aparecen preguntas de investigación mucho más profundas:

### Optimización

* ¿Por qué ciertos optimizadores funcionan mejor en determinados regímenes?
* ¿Cómo afecta el learning rate a la estabilidad?
* ¿Qué papel desempeña la geometría del espacio de parámetros?
* ¿Cómo se comportan los modelos cerca de regiones de baja pérdida?

### Datos

* ¿Qué calidad de datos maximiza el aprendizaje?
* ¿Cómo afectan los datos sintéticos?
* ¿Cómo medir y evitar contaminación?
* ¿Cómo interactúan diversidad y repetición?

### Scaling

* ¿Cómo escalan loss, datos, parámetros y cómputo?
* ¿Cuál es la asignación óptima de presupuesto entre parámetros y tokens?
* ¿Cuándo deja de ser rentable aumentar el modelo?

### Representaciones

* ¿Cómo se distribuyen conceptos dentro de las representaciones?
* ¿Qué estructuras internas emergen?
* ¿Cómo se pueden interpretar?
* ¿Qué información es linealmente decodificable?

### Seguridad

* ¿Cuándo aparece memorización?
* ¿Cómo detectar data poisoning?
* ¿Cómo medir extracción de información?
* ¿Cómo reducir riesgos de privacidad?

### Capacidades

* ¿Por qué aparecen determinadas capacidades al escalar?
* ¿Son realmente "emergentes" o dependen de la métrica utilizada?
* ¿Cómo distinguir generalización, memorización y composición?

Estas preguntas pertenecen ya al área de investigación de **machine learning, deep learning, interpretabilidad y sistemas de IA**.

---

# 82. Lo esencial que debes llevarte

Si solamente recuerdas diez ideas de este capítulo, recuerda estas:

1. **Pretraining es la etapa de aprendizaje inicial de un modelo.**
2. Muchos LLM autoregresivos se entrenan mediante **next-token prediction**.
3. El modelo convierte tokens en representaciones y los procesa mediante el **Transformer**.
4. El modelo produce **logits**, que pueden transformarse en probabilidades.
5. La diferencia entre predicción y objetivo se cuantifica mediante una **loss**.
6. **Backpropagation** calcula gradientes respecto a los parámetros.
7. Un **optimizer** utiliza esos gradientes para actualizar los parámetros.
8. El proceso se repite sobre enormes cantidades de datos y pasos de entrenamiento.
9. El modelo aprende representaciones distribuidas; sus parámetros no son una base de datos convencional.
10. **El prompt no reentrena el modelo durante una inferencia normal: condiciona la inferencia.**

---

# 83. Puente hacia el siguiente capítulo

Hasta ahora sabemos:

```text
TOKEN
 ↓
EMBEDDING
 ↓
POSITION
 ↓
TRANSFORMER
 ↓
PRETRAINING
 ↓
MODELO BASE
```

Pero todavía falta responder una pregunta fundamental:

> **¿Cómo podemos tomar un modelo ya preentrenado y adaptarlo para realizar tareas o comportarse de una determinada manera?**

La respuesta nos lleva al siguiente concepto:

```text
PRETRAINING
     ↓
MODELO BASE
     ↓
FINE-TUNING
     ↓
MODELO ADAPTADO
```

El siguiente archivo estudiará:

# `08-Fine-Tuning.md`

Ahí veremos cómo se modifica un modelo preentrenado para adaptarlo a tareas, dominios o comportamientos específicos, y por qué **fine-tuning no es lo mismo que prompting, RAG o pretraining**.
