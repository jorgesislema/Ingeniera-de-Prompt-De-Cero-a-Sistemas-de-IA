# 04 — Entrenamiento e inferencia

> **Objetivo:** comprender qué ocurre cuando un modelo de IA aprende y qué ocurre cuando posteriormente recibe una entrada y genera una respuesta.

---

# 1. La distinción más importante

Existen dos procesos que debemos separar desde el principio:

```text
ENTRENAMIENTO
    ↓
El modelo aprende
    ↓
se modifican sus parámetros


INFERENCIA
    ↓
El modelo utiliza lo aprendido
    ↓
produce una salida
```

La diferencia fundamental puede resumirse así:

> **Durante el entrenamiento se ajustan los parámetros del modelo. Durante la inferencia se utilizan esos parámetros para producir una salida.**

Esta distinción es fundamental para comprender:

* LLM;
* prompts;
* tokens;
* contexto;
* temperatura;
* RAG;
* fine-tuning;
* agentes;
* modelos de razonamiento;
* costos de API;
* hardware;
* latencia;
* memoria;
* seguridad de IA.

---

# 2. Una analogía sencilla

Imaginemos a una persona aprendiendo a tocar guitarra.

## Entrenamiento

La persona:

```text
estudia acordes
     ↓
practica
     ↓
comete errores
     ↓
corrige
     ↓
vuelve a practicar
     ↓
mejora
```

## Inferencia

Después de aprender, alguien le pide:

```text
Toca una canción.
```

La persona utiliza las habilidades adquiridas para ejecutar la canción.

Podemos representarlo:

```text
APRENDIZAJE

práctica → corrección → práctica → conocimiento


EJECUCIÓN

instrucción + conocimiento → interpretación
```

En un modelo de IA ocurre algo conceptualmente parecido, aunque el mecanismo matemático sea completamente diferente.

---

# 3. ¿Qué significa entrenar un modelo?

Entrenar un modelo significa utilizar datos y un proceso de optimización para ajustar sus parámetros.

Una representación simplificada:

```text
Datos
  ↓
Modelo
  ↓
Predicción
  ↓
Comparación con objetivo
  ↓
Error
  ↓
Actualización de parámetros
  ↓
Nueva predicción
  ↓
Repetir
```

Este proceso puede repetirse millones o miles de millones de veces.

El objetivo es encontrar valores de los parámetros que permitan al modelo realizar adecuadamente la tarea para la cual está siendo entrenado.

---

# 4. Un ejemplo extremadamente sencillo

Supongamos que queremos crear un modelo que estime el precio de una vivienda.

Tenemos:

| Metros² |  Precio |
| ------: | ------: |
|      50 |  60.000 |
|      80 |  90.000 |
|     100 | 110.000 |
|     150 | 160.000 |

El modelo comienza con parámetros iniciales.

Por ejemplo, imaginemos:

```text
peso = 500
```

Para una vivienda de 100 m²:

$$
100 \times 500 = 50.000
$$

Pero el precio real es:

```text
110.000
```

Existe un error importante.

El entrenamiento utiliza ese error para modificar los parámetros.

Después podría obtener:

```text
peso = 800
```

Ahora:

$$
100 \times 800 = 80.000
$$

Todavía existe error.

El proceso continúa:

```text
500
 ↓
800
 ↓
1000
 ↓
1050
 ↓
...
```

Hasta encontrar una configuración que permita reducir la pérdida según el objetivo del entrenamiento.

---

# 5. El ciclo de entrenamiento

Podemos representar el proceso de forma más técnica:

```text
                    ┌──────────────┐
                    │    DATOS     │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │    MODELO    │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │ PREDICCIÓN   │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │    PÉRDIDA   │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │   GRADIENTE  │
                    └──────┬───────┘
                           ↓
                 ┌────────────────────┐
                 │ ACTUALIZAR         │
                 │ PARÁMETROS         │
                 └─────────┬──────────┘
                           │
                           └──────→ repetir
```

Esta repetición es una de las bases del aprendizaje automático moderno.

---

# 6. ¿Qué es la pérdida?

La **función de pérdida** (*loss function*) cuantifica qué tan lejos está la predicción del modelo del objetivo esperado.

Conceptualmente:

$$
L(\text{predicción},\text{objetivo})
$$

Si:

```text
objetivo = 100
predicción = 90
```

existe una determinada pérdida.

Si:

```text
objetivo = 100
predicción = 99
```

la pérdida normalmente será menor, dependiendo de la función utilizada.

El entrenamiento intenta encontrar configuraciones de parámetros que minimicen la pérdida.

---

# 7. ¿Cómo se actualizan los parámetros?

Aquí aparece el concepto de **gradiente**.

Una representación simplificada es:

$$
\theta_{nuevo}
=
\theta_{anterior}
-
\eta\nabla_\theta L
$$

Donde:

* \(\theta\) = parámetros;
* \(L\) = función de pérdida;
* \(\nabla_\theta L\) = gradiente;
* \(\eta\) = tasa de aprendizaje.

La idea intuitiva es:

> El gradiente indica cómo cambia la pérdida cuando modificamos los parámetros.

El algoritmo puede utilizar esa información para ajustar los parámetros en una dirección que reduzca la pérdida.

---

# 8. Descenso de gradiente

Uno de los conceptos fundamentales del aprendizaje profundo es el **descenso de gradiente** (*gradient descent*).

Imaginemos una montaña:

```text
Pérdida
  ↑
  │       ●
  │      / \
  │     /   \
  │    /     \
  │   /       \
  │  /         \
  │ ●           ●
  │      ↓
  │    mínimo
  └────────────────→ parámetros
```

El entrenamiento intenta encontrar regiones donde la pérdida sea menor.

La analogía de la montaña es útil, pero no debemos tomarla literalmente: en modelos reales podemos tener espacios de parámetros con dimensiones enormes y superficies de pérdida extremadamente complejas.

---

# 9. ¿Qué es la tasa de aprendizaje?

La **learning rate** determina, de forma simplificada, el tamaño de los pasos utilizados durante la actualización de los parámetros.

Si es demasiado grande:

```text
paso enorme
    ↓
puede saltarse regiones útiles
```

Si es demasiado pequeña:

```text
paso diminuto
    ↓
el entrenamiento puede avanzar muy lentamente
```

Conceptualmente:

```text
learning rate alta
→ cambios grandes


learning rate baja
→ cambios pequeños
```

La elección de este valor es una parte importante de la optimización.

---

# 10. Entrenamiento por iteraciones

El modelo normalmente no procesa todo el conjunto de datos de una sola vez.

Los datos suelen dividirse en grupos denominados **batches**.

Ejemplo:

```text
1.000.000 ejemplos

        ↓

batch 1
batch 2
batch 3
...
batch N
```

Cada batch permite realizar actualizaciones.

Podemos distinguir:

### Batch

Conjunto de ejemplos procesados en una actualización.

### Iteración

Una actualización del modelo después de procesar un batch.

### Epoch

Una pasada completa por el conjunto de entrenamiento.

Por ejemplo:

```text
100.000 ejemplos
batch = 1.000

→ 100 iteraciones aproximadamente por epoch
```

---

# 11. ¿Qué es una epoch?

Una **epoch** representa una pasada completa por los datos de entrenamiento.

Si tenemos:

```text
10.000 ejemplos
```

y entrenamos durante:

```text
5 epochs
```

el sistema habrá recorrido conceptualmente el conjunto de datos cinco veces.

No significa necesariamente que cada ejemplo produzca exactamente la misma actualización en cada recorrido, ya que existen técnicas como:

* barajado;
* batching;
* regularización;
* data augmentation;
* diferentes esquemas de muestreo.

---

# 12. ¿Qué es un batch?

Supongamos que tenemos:

```text
1.000.000 documentos
```

Procesarlos todos simultáneamente puede requerir una cantidad enorme de memoria.

Podemos dividirlos:

```text
Batch 1 → documentos 1-1.000
Batch 2 → documentos 1.001-2.000
Batch 3 → documentos 2.001-3.000
...
```

Esto permite realizar el entrenamiento de manera más manejable.

En deep learning aparecen conceptos relacionados como:

* batch size;
* microbatch;
* gradient accumulation.

Estos conceptos se vuelven importantes al entrenar modelos grandes.

---

# 13. Preentrenamiento

En los LLM existe una etapa especialmente importante denominada **preentrenamiento** (*pretraining*).

Durante esta etapa, el modelo procesa cantidades enormes de datos.

Una representación simplificada:

```text
Internet
libros
documentos
código
artículos
otros datos
      ↓
preprocesamiento
      ↓
tokenización
      ↓
preentrenamiento
      ↓
modelo base
```

El objetivo exacto depende de la arquitectura y del procedimiento utilizado.

En muchos LLM autoregresivos, una tarea fundamental consiste en predecir tokens de acuerdo con el contexto disponible.

---

# 14. Ejemplo de preentrenamiento de lenguaje

Supongamos:

```text
El perro corre por el
```

El modelo intenta predecir el siguiente token.

Podría obtener:

```text
parque → alta probabilidad
campo  → probabilidad menor
bosque → probabilidad menor
...
```

Si el token objetivo es:

```text
parque
```

la función de pérdida mide qué tan adecuada fue la predicción.

El proceso se repite sobre cantidades enormes de secuencias.

---

# 15. ¿Qué aprende un LLM durante el preentrenamiento?

No aprende simplemente una lista de respuestas.

Durante el entrenamiento desarrolla representaciones que pueden capturar patrones relacionados con:

* sintaxis;
* semántica;
* relaciones entre palabras;
* estructuras de texto;
* código;
* patrones matemáticos;
* información presente en los datos;
* relaciones entre entidades;
* diferentes estilos de lenguaje.

La naturaleza exacta de las representaciones internas es un tema activo de investigación.

Por eso debemos evitar afirmar que:

> "El modelo guarda todo Internet dentro de sus parámetros."

Es una simplificación incorrecta.

El modelo aprende una representación paramétrica de patrones derivados de los datos de entrenamiento.

---

# 16. Preentrenamiento no es el final

Un LLM moderno puede atravesar varias etapas.

Una representación simplificada:

```text
Datos
  ↓
Preentrenamiento
  ↓
Modelo base
  ↓
Postentrenamiento
  ↓
Alineamiento / adaptación
  ↓
Evaluación
  ↓
Modelo desplegable
```

Dependiendo del sistema pueden aparecer técnicas como:

* supervised fine-tuning;
* preference optimization;
* RLHF;
* DPO;
* RLAIF;
* entrenamiento específico para razonamiento;
* adaptación para herramientas;
* entrenamiento multimodal.

No todos los modelos utilizan exactamente las mismas etapas.

---

# 17. Fine-tuning

El **fine-tuning** consiste en continuar el entrenamiento de un modelo utilizando datos específicos.

Por ejemplo:

```text
Modelo general
      ↓
datos jurídicos especializados
      ↓
fine-tuning
      ↓
modelo adaptado
```

Otro ejemplo:

```text
Modelo general
      ↓
10.000 ejemplos de clasificación empresarial
      ↓
fine-tuning
      ↓
modelo especializado
```

Durante el fine-tuning pueden modificarse los parámetros.

Por eso:

> **Fine-tuning es entrenamiento adicional; prompting no lo es.**

---

# 18. Prompting frente a entrenamiento

Esta diferencia debe quedar completamente clara.

## Prompting

```text
Modelo
  ↑
Prompt
```

El prompt condiciona la inferencia.

## Fine-tuning

```text
Modelo
  ↓
datos adicionales
  ↓
optimización
  ↓
parámetros modificados
  ↓
nuevo comportamiento aprendido
```

Podemos resumir:

| Característica                        | Prompting           | Fine-tuning            |
| ------------------------------------- | ------------------- | ---------------------- |
| Modifica parámetros                   | No                  | Sí                     |
| Ocurre durante inferencia             | Sí                  | No                     |
| Requiere entrenamiento                | No                  | Sí                     |
| Puede cambiar comportamiento          | Sí, contextualmente | Sí, de forma aprendida |
| Información persistente en parámetros | No                  | Puede existir          |
| Costo computacional                   | Generalmente menor  | Mayor                  |

---

# 19. ¿Qué es la inferencia?

La **inferencia** es el proceso mediante el cual utilizamos un modelo ya entrenado para producir una salida a partir de una entrada.

Conceptualmente:

```text
Entrada
  ↓
Modelo entrenado
  ↓
Procesamiento
  ↓
Salida
```

En un LLM:

```text
Prompt
  ↓
Tokenización
  ↓
Representaciones
  ↓
Modelo
  ↓
Logits
  ↓
Probabilidades
  ↓
Decodificación
  ↓
Tokens generados
  ↓
Texto
```

---

# 20. Ejemplo cotidiano de inferencia

Supongamos un modelo entrenado para reconocer imágenes.

Durante entrenamiento:

```text
miles de imágenes
       ↓
modelo
       ↓
aprendizaje
```

Después recibe una imagen nueva:

```text
imagen desconocida
       ↓
modelo entrenado
       ↓
"gato"
```

Esta segunda etapa es inferencia.

El modelo no está necesariamente aprendiendo permanentemente de esa imagen.

Está utilizando lo aprendido anteriormente.

---

# 21. Inferencia en un LLM

Supongamos que escribimos:

```text
Explica qué es Python.
```

El sistema realiza aproximadamente:

```text
"Explica qué es Python."
          ↓
     tokenización
          ↓
representaciones numéricas
          ↓
      arquitectura
          ↓
     distribución
      probabilística
          ↓
    selección de token
          ↓
      nuevo contexto
          ↓
    siguiente token
          ↓
         ...
          ↓
       respuesta
```

El proceso se repite hasta que se produce una condición de finalización.

---

# 22. Autoregresión

Muchos LLM generativos funcionan de forma **autoregresiva**.

Esto significa que los tokens generados previamente pasan a formar parte del contexto utilizado para producir los siguientes.

Ejemplo:

```text
Entrada:

"Quito es la capital de"

        ↓

"Quito es la capital de Ecuador"

        ↓

"Quito es la capital de Ecuador y"

        ↓

"Quito es la capital de Ecuador y está"

        ↓

...
```

Conceptualmente:

$$
P(x_1,x_2,\ldots,x_n)
=
\prod_{t=1}^{n}
P(x_t|x_1,\ldots,x_{t-1})
$$

Esta ecuación expresa una idea central de los modelos autoregresivos:

> La probabilidad de una secuencia puede descomponerse en probabilidades condicionales de cada token dado el contexto anterior.

---

# 23. Logits

Antes de convertir las salidas del modelo en probabilidades, suelen aparecer valores denominados **logits**.

Conceptualmente:

```text
Modelo
  ↓
logits
  ↓
softmax
  ↓
probabilidades
```

Supongamos:

```text
Token A → 4.8
Token B → 2.1
Token C → 0.5
```

Los logits no son probabilidades.

Una función como **softmax** puede transformarlos en una distribución de probabilidad.

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

donde:

* \(z_i\) = logit;
* \(P_i\) = probabilidad resultante.

---

# 24. Sampling y decodificación

Una vez calculadas las probabilidades, el sistema necesita decidir qué token producir.

Aquí aparecen técnicas de decodificación como:

* greedy decoding;
* sampling;
* temperature;
* top-k;
* top-p;
* beam search, en determinados tipos de modelos o tareas.

Por ejemplo:

```text
Modelo
 ↓
probabilidades

azul       0.70
verde      0.15
rojo       0.10
amarillo   0.05

        ↓

decodificación

        ↓

"azul"
```

La forma exacta de seleccionar el token afecta al comportamiento final.

---

# 25. Temperatura

La **temperature** modifica la distribución utilizada durante la generación.

Una forma simplificada de expresarlo:

$$
P_i =
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

donde:

$$
T = \text{temperature}
$$

De forma intuitiva:

### Temperature baja

La distribución tiende a concentrarse más.

```text
A → 0.90
B → 0.05
C → 0.05
```

### Temperature alta

La distribución puede hacerse más dispersa.

```text
A → 0.50
B → 0.30
C → 0.20
```

Los valores son únicamente ilustrativos.

Importante:

> **Cambiar la temperatura no reentrena el modelo ni cambia sus parámetros.**

Cambia la forma en que se realiza la generación durante la inferencia.

---

# 26. Top-p

**Top-p**, también llamado *nucleus sampling*, limita el conjunto de candidatos considerando los tokens cuya probabilidad acumulada alcanza determinado umbral.

Ejemplo conceptual:

```text
A → 0.50
B → 0.25
C → 0.15
D → 0.05
E → 0.05
```

Si:

```text
top-p = 0.90
```

se consideran los candidatos necesarios para alcanzar aproximadamente el 90 % acumulado.

La implementación exacta puede depender del sistema.

Top-p, como temperature, pertenece al proceso de **decodificación/inferencia**, no al aprendizaje de los parámetros.

---

# 27. Entrenamiento versus inferencia

La comparación fundamental:

| Aspecto         | Entrenamiento                      | Inferencia                          |
| --------------- | ---------------------------------- | ----------------------------------- |
| Objetivo        | Aprender patrones                  | Utilizar patrones aprendidos        |
| Parámetros      | Se actualizan                      | Normalmente permanecen fijos        |
| Datos           | Grandes conjuntos de entrenamiento | Entrada/contexto de la consulta     |
| Gradientes      | Se calculan para optimización      | Normalmente no                      |
| Backpropagation | Sí                                 | No en una inferencia estándar       |
| Costo           | Muy elevado en modelos grandes     | Generalmente menor                  |
| Duración        | Puede durar semanas o meses        | Puede durar milisegundos o segundos |
| Resultado       | Modelo entrenado                   | Predicción/generación               |
| Prompt          | No es el mecanismo principal       | Es una entrada fundamental          |

---

# 28. ¿Por qué el entrenamiento es tan caro?

Entrenar modelos grandes puede requerir enormes cantidades de:

* GPU;
* memoria;
* almacenamiento;
* energía;
* interconexión de alta velocidad;
* datos;
* tiempo;
* ingeniería.

Un entrenamiento distribuido puede utilizar grandes grupos de aceleradores trabajando simultáneamente.

Conceptualmente:

```text
GPU 1 ─┐
GPU 2 ─┤
GPU 3 ─┤
GPU 4 ─┤
GPU 5 ─┤
...    ├──→ entrenamiento
GPU N ─┘
```

El reto no consiste únicamente en tener muchas GPU.

También hay que coordinar:

* memoria;
* comunicación;
* paralelismo;
* almacenamiento;
* tolerancia a fallos;
* sincronización;
* optimización.

---

# 29. Inferencia también tiene costo

Es incorrecto pensar:

> "Una vez entrenado, utilizar el modelo es gratis."

La inferencia también consume recursos.

Por cada consulta pueden intervenir:

* CPU;
* GPU;
* memoria;
* ancho de banda;
* almacenamiento;
* energía;
* tiempo de cómputo.

En servicios comerciales, esto puede reflejarse en:

* costo por token;
* costo por solicitud;
* límites de uso;
* latencia;
* cuotas.

---

# 30. Prefill y decode

En la inferencia de LLM modernos podemos distinguir conceptualmente dos fases importantes:

### Prefill

El sistema procesa el contexto inicial.

```text
Prompt largo
     ↓
procesamiento inicial
```

### Decode

El modelo genera nuevos tokens uno tras otro.

```text
token 1
 ↓
token 2
 ↓
token 3
 ↓
...
```

Esto es importante para comprender el costo y la latencia de los LLM.

Un prompt extremadamente largo puede aumentar el trabajo de procesamiento inicial.

Una respuesta extremadamente larga aumenta el trabajo de generación.

---

# 31. KV cache

Durante la generación autoregresiva, los sistemas modernos utilizan técnicas como la **KV cache** (*Key-Value cache*) para evitar recalcular innecesariamente determinadas representaciones de tokens anteriores.

Conceptualmente:

```text
Contexto
   ↓
procesamiento
   ↓
K/V almacenados
   ↓
nuevo token
   ↓
utilizar información almacenada
```

Esto puede mejorar significativamente la eficiencia de generación.

Sin embargo, la KV cache también consume memoria, especialmente cuando:

* aumenta el contexto;
* aumenta el batch;
* aumenta el número de capas;
* aumenta el tamaño del modelo.

Este concepto será importante cuando estudiemos **context window** y costos de inferencia.

---

# 32. ¿Qué ocurre con el prompt?

Esta pregunta conecta directamente entrenamiento e inferencia.

Cuando escribimos:

```text
Analiza este documento y encuentra anomalías.
```

normalmente ocurre:

```text
Prompt
 ↓
tokenización
 ↓
contexto
 ↓
modelo entrenado
 ↓
inferencia
 ↓
respuesta
```

No ocurre:

```text
Prompt
 ↓
reentrenamiento
 ↓
modificación permanente del modelo
```

Esta segunda interpretación sería incorrecta para una inferencia normal.

---

# 33. ¿El modelo aprende de mi conversación?

Hay que distinguir varios mecanismos.

Una conversación puede utilizarse como **contexto** para producir la siguiente respuesta.

Eso no significa automáticamente que los parámetros del modelo hayan sido modificados.

Podemos tener:

```text
Mensaje 1
   ↓
Mensaje 2
   ↓
Mensaje 3
   ↓
contexto de conversación
   ↓
modelo
   ↓
respuesta
```

Esto es inferencia contextual.

Es diferente de:

```text
datos
 ↓
entrenamiento
 ↓
actualización de parámetros
```

La existencia de memoria de producto, almacenamiento de conversaciones o utilización posterior de datos depende del sistema concreto y de sus políticas. No debemos confundir esos mecanismos con el entrenamiento del modelo durante una conversación.

---

# 34. RAG ocurre durante la inferencia

RAG es especialmente útil para comprender esta diferencia.

Supongamos una empresa que tiene:

```text
manuales
contratos
políticas
procedimientos
```

En lugar de modificar necesariamente el modelo mediante fine-tuning, podemos recuperar información relevante:

```text
Pregunta
   ↓
sistema de recuperación
   ↓
documentos relevantes
   ↓
contexto
   ↓
LLM
   ↓
respuesta
```

Los documentos recuperados forman parte de la información disponible durante la inferencia.

Por eso:

> **RAG proporciona conocimiento externo al modelo durante la consulta; no equivale automáticamente a entrenar el modelo con esos documentos.**

---

# 35. Fine-tuning frente a RAG

Supongamos una empresa con 50.000 documentos internos.

## RAG

```text
documentos
   ↓
índice/vector database
   ↓
recuperación
   ↓
contexto
   ↓
LLM
```

## Fine-tuning

```text
datos preparados
   ↓
entrenamiento adicional
   ↓
parámetros modificados
   ↓
modelo adaptado
```

Son soluciones diferentes.

Una puede complementar a la otra.

---

# 36. ¿Dónde entra el prompt engineering?

Ahora podemos definir con mayor precisión qué hace la ingeniería de prompts.

El ingeniero de prompts trabaja principalmente sobre la entrada y las condiciones de inferencia:

```text
             MODELO ENTRENADO
                    ↑
                    │
       ┌────────────┼────────────┐
       │            │            │
    Prompt       Contexto    Parámetros de
                              generación
       │            │            │
       └────────────┼────────────┘
                    ↓
                INFERENCIA
                    ↓
                  salida
```

Puede diseñar:

* instrucciones;
* contexto;
* ejemplos;
* formato;
* restricciones;
* criterios;
* delimitadores;
* esquemas estructurados;
* procedimientos de verificación.

Pero no debe confundirse:

> **Ingeniería de prompts ≠ entrenamiento del modelo.**

---

# 37. Un ejemplo de ingeniería de prompts

Prompt básico:

```text
Analiza este contrato.
```

Prompt más estructurado:

```text
Analiza el contrato proporcionado.

Objetivo:
Identificar cláusulas que puedan representar
riesgos contractuales.

Procedimiento:
1. Identifica las obligaciones principales.
2. Detecta plazos.
3. Detecta penalizaciones.
4. Identifica cláusulas ambiguas.
5. Explica cada riesgo utilizando evidencia
   del documento.

Salida:
Devuelve una tabla con:
- cláusula
- riesgo
- evidencia
- impacto
- nivel de riesgo
```

El modelo sigue siendo el mismo.

Lo que cambió fue:

```text
entrada
+
estructura
+
contexto
+
criterios
```

y, por tanto, las condiciones de inferencia.

---

# 38. ¿Puede un buen prompt compensar un mal modelo?

Hasta cierto punto, pero no ilimitadamente.

Podemos pensar:

```text
capacidad del modelo
        +
calidad del contexto
        +
calidad del prompt
        +
herramientas
        +
evaluación
        ↓
resultado del sistema
```

Un prompt excelente no convierte automáticamente un modelo limitado en uno de capacidad superior.

Por ejemplo:

```text
Modelo A:
no puede procesar imágenes

Prompt:
"Analiza detalladamente esta radiografía."

```

El prompt no puede crear una capacidad multimodal que el sistema no posee.

Esto introduce una regla importante:

> **La ingeniería de prompts puede aprovechar capacidades existentes; no puede garantizar capacidades que el modelo o el sistema no posee.**

---

# 39. Entrenamiento, capacidad y comportamiento

Debemos separar tres conceptos:

### Capacidad

Lo que el modelo puede hacer gracias a su arquitectura, entrenamiento y parámetros.

### Comportamiento

Cómo responde bajo determinadas condiciones.

### Prompt

Una de las herramientas para condicionar ese comportamiento durante la inferencia.

Por ejemplo:

```text
CAPACIDAD
   ↓
puede generar JSON

PROMPT
   ↓
"Devuelve únicamente JSON válido"

COMPORTAMIENTO
   ↓
produce una respuesta estructurada
```

Pero si el modelo tiene dificultades para producir JSON válido, un prompt puede mejorar el comportamiento, pero no necesariamente resolverlo completamente.

Podemos necesitar:

* structured outputs;
* validadores;
* reintentos;
* funciones;
* esquemas;
* programación externa.

---

# 40. Modelos de razonamiento

Los modelos modernos pueden incorporar técnicas de entrenamiento y sistemas de inferencia diseñados para tareas que requieren mayor procesamiento.

De manera conceptual:

```text
problema
   ↓
modelo
   ↓
procesamiento adicional
   ↓
respuesta
```

Esto puede implicar mecanismos específicos de entrenamiento, inferencia o ambos, dependiendo del sistema.

No debemos reducir estos modelos simplemente a:

> "tienen más parámetros."

El comportamiento puede depender de:

* arquitectura;
* datos;
* postentrenamiento;
* estrategias de razonamiento;
* cómputo utilizado durante la inferencia;
* métodos de decodificación;
* herramientas.

---

# 41. Test-time compute

Un concepto importante en sistemas modernos es el **cómputo durante la inferencia** (*test-time compute*).

Tradicionalmente:

```text
modelo grande
+
poco procesamiento adicional
```

Pero algunos sistemas pueden utilizar más recursos durante la resolución de una consulta:

```text
problema complejo
      ↓
más cómputo durante inferencia
      ↓
procesamiento adicional
      ↓
respuesta
```

Esto introduce una idea importante:

> **El costo y la capacidad de un sistema moderno de IA no dependen únicamente del número de parámetros entrenados. También importa cuánto cómputo utiliza durante la inferencia.**

---

# 42. Dense frente a MoE

En una arquitectura densa, de manera simplificada:

```text
token
 ↓
gran parte de la red
 ↓
salida
```

En una arquitectura MoE:

```text
token
 ↓
router
 ↓
expertos seleccionados
 ↓
salida
```

Esto permite construir modelos con gran cantidad de parámetros totales mientras se activa solamente una parte de ellos para cada token.

Por eso podemos encontrar:

```text
parámetros totales
        ≠
parámetros activos
```

Esta distinción será importante al estudiar arquitectura y eficiencia.

---

# 43. Entrenamiento distribuido

Los modelos grandes normalmente no se entrenan en una sola computadora convencional.

Podemos distribuir el trabajo:

```text
              modelo
                │
       ┌────────┼────────┐
       ↓        ↓        ↓
      GPU      GPU      GPU
       │        │        │
       └────────┼────────┘
                ↓
        sincronización
```

Existen diferentes formas de paralelismo:

* data parallelism;
* tensor parallelism;
* pipeline parallelism;
* expert parallelism.

Estos conceptos pertenecen a una capa más avanzada de ingeniería de sistemas de IA.

---

# 44. Inferencia distribuida

La inferencia también puede distribuirse.

Por ejemplo:

```text
Usuario
   ↓
API
   ↓
router
   ↓
servidores
   ↓
GPU
   ↓
modelo
   ↓
respuesta
```

En servicios de producción pueden existir:

* balanceadores;
* múltiples réplicas;
* batching;
* cachés;
* cuantización;
* sistemas de escalado;
* colas;
* routers de modelos.

Por eso cuando utilizamos un LLM mediante una API, normalmente no estamos interactuando directamente con una GPU individual.

Estamos interactuando con un **sistema de inferencia**.

---

# 45. Modelo vs sistema de IA

Esta distinción es especialmente importante para ingeniería.

Un **modelo** puede ser:

```text
parámetros
+
arquitectura
+
configuración
```

Un **sistema de IA** puede incluir:

```text
usuario
 ↓
interfaz
 ↓
prompt
 ↓
router
 ↓
RAG
 ↓
herramientas
 ↓
modelo
 ↓
validación
 ↓
respuesta
```

Por tanto:

> **Un producto de IA no es necesariamente equivalente al modelo que utiliza.**

Por ejemplo, una aplicación empresarial puede utilizar:

```text
LLM
+
RAG
+
base de datos
+
herramientas
+
memoria
+
guardrails
+
monitorización
```

---

# 46. ¿Por qué esta distinción importa para la seguridad?

En seguridad de IA es especialmente importante distinguir:

```text
modelo
```

de:

```text
sistema
```

Un modelo puede comportarse correctamente en aislamiento y aun así existir vulnerabilidad en la aplicación que lo utiliza.

Ejemplo:

```text
Usuario
 ↓
aplicación
 ↓
RAG
 ↓
documentos externos
 ↓
LLM
```

Si un documento contiene instrucciones maliciosas, el problema puede estar en la forma en que el sistema incorpora ese documento al contexto.

Esto conecta posteriormente con:

* prompt injection;
* indirect prompt injection;
* data poisoning;
* tool abuse;
* exfiltración de información;
* control de agentes.

La seguridad de IA no puede reducirse a "el modelo es seguro".

---

# 47. La cadena completa

Podemos resumir todo el proceso:

```text
                 ENTRENAMIENTO
                      │
                      ↓
                   DATOS
                      │
                      ↓
               TOKENIZACIÓN
                      │
                      ↓
                 OPTIMIZACIÓN
                      │
                      ↓
              PARÁMETROS AJUSTADOS
                      │
                      ↓
                MODELO ENTRENADO
                      │
                      │
                      ↓
                  INFERENCIA
                      ↑
                      │
             ┌────────┴────────┐
             │                 │
           PROMPT           CONTEXTO
             │                 │
             └────────┬────────┘
                      ↓
                 TOKENIZACIÓN
                      ↓
                 PROCESAMIENTO
                      ↓
                    LOGITS
                      ↓
              DECODIFICACIÓN
                      ↓
                TOKEN GENERADO
                      ↓
              CONTEXTO ACTUALIZADO
                      ↓
                 siguiente token
                      ↓
                     ...
                      ↓
                   RESPUESTA
```

---

# 48. Errores conceptuales que debemos evitar

## Error 1

> "Cada vez que hablo con ChatGPT, estoy entrenando el modelo."

No necesariamente.

Una conversación normalmente constituye una interacción de inferencia con contexto.

---

## Error 2

> "El prompt cambia los parámetros."

En una inferencia normal, no.

El prompt condiciona la entrada.

---

## Error 3

> "RAG entrena al modelo."

No.

RAG normalmente proporciona información recuperada durante la inferencia.

---

## Error 4

> "Fine-tuning es simplemente escribir un buen prompt."

No.

Fine-tuning implica entrenamiento adicional y modificación de parámetros.

---

## Error 5

> "Más parámetros siempre significa mejor."

No necesariamente.

La capacidad depende de múltiples factores.

---

## Error 6

> "La respuesta más segura es siempre la de mayor probabilidad."

No necesariamente.

La decodificación, el objetivo del sistema y los métodos de evaluación también importan.

---

## Error 7

> "Si el modelo responde con seguridad, significa que está seguro de que es verdad."

No.

Una salida lingüísticamente convincente no garantiza exactitud factual.

---

# 49. Una comparación final

| Concepto      | Pregunta que responde                                            |
| ------------- | ---------------------------------------------------------------- |
| Entrenamiento | ¿Cómo aprende el modelo?                                         |
| Parámetros    | ¿Dónde queda representada parte de lo aprendido?                 |
| Inferencia    | ¿Cómo utiliza lo aprendido?                                      |
| Prompt        | ¿Qué instrucciones/contexto recibe?                              |
| Token         | ¿Cuál es la unidad de procesamiento del lenguaje?                |
| Contexto      | ¿Qué información tiene disponible durante la inferencia?         |
| Sampling      | ¿Cómo se seleccionan las salidas posibles?                       |
| RAG           | ¿Cómo incorporamos información externa durante la consulta?      |
| Fine-tuning   | ¿Cómo adaptamos los parámetros mediante entrenamiento adicional? |
| Arquitectura  | ¿Cómo está construido el modelo?                                 |

---

# 50. La idea fundamental para ingeniería de prompts

Llegados a este punto podemos formular una regla que acompañará todo el curso:

> **El entrenamiento determina gran parte de las capacidades y comportamiento aprendido del modelo; la inferencia determina cómo esas capacidades se utilizan ante una entrada concreta.**

Por tanto:

```text
ENTRENAMIENTO
     ↓
capacidades aprendidas
     ↓
parámetros


INFERENCIA
     ↓
prompt
+
contexto
+
configuración
+
herramientas
     ↓
comportamiento observable
     ↓
salida
```

La ingeniería de prompts trabaja principalmente en la segunda parte.

---

# 51. De cero a nivel avanzado

Para una persona que comienza:

> **Entrenar = enseñar al modelo mediante datos y ajustes.**

> **Inferir = utilizar lo aprendido para responder a una entrada.**

Para alguien con conocimientos técnicos:

$$
\text{Training}
\rightarrow
\min_\theta L(\theta)
$$

mientras que durante una inferencia estándar:

$$
y \sim p_\theta(y|x)
$$

donde:

* \(\theta\) representa los parámetros aprendidos;
* \(x\) representa la entrada;
* \(y\) representa la salida.

Para un ingeniero de IA:

```text
Training
    ↓
optimization
    ↓
parameters
    ↓
checkpoint
    ↓
deployment
    ↓
inference
    ↓
prefill
    ↓
decode
    ↓
sampling
    ↓
output
```

Y para ingeniería de prompts:

```text
modelo ya entrenado
        +
prompt
        +
contexto
        +
restricciones
        +
ejemplos
        +
configuración de generación
        ↓
      inferencia
        ↓
respuesta
```

---

# 52. Conexión con los siguientes módulos

Este capítulo prepara directamente los siguientes conceptos:

```text
04 — Entrenamiento e inferencia
          │
          ├── Parámetros
          │
          ├── Tokens
          │
          ├── Contexto
          │
          ├── Inferencia
          │
          ├── Sampling
          │
          ├── Fine-tuning
          │
          ├── RAG
          │
          └── Arquitectura de LLM
```

La progresión que debe seguir el estudiante es:

```text
¿Qué es un modelo?
        ↓
¿Cómo aprende?
        ↓
¿Qué son los parámetros?
        ↓
¿Qué es un token?
        ↓
¿Qué información recibe?
        ↓
¿Qué es el contexto?
        ↓
¿Qué ocurre durante la inferencia?
        ↓
¿Cómo genera la respuesta?
        ↓
¿Cómo puedo controlar ese comportamiento?
        ↓
INGENIERÍA DE PROMPTS
```

---

# 53. Resumen para memorizar

Si el estudiante solamente recuerda seis cosas de este capítulo, deben ser estas:

### 1. Entrenamiento

```text
datos → predicción → error → actualización
```

### 2. Parámetros

Son valores numéricos ajustados durante el entrenamiento y relacionados con el comportamiento aprendido.

### 3. Inferencia

```text
entrada → modelo entrenado → salida
```

### 4. Prompt

El prompt condiciona la inferencia; normalmente no reentrena el modelo.

### 5. RAG

Proporciona información externa durante la inferencia; no equivale automáticamente a entrenamiento.

### 6. Fine-tuning

Es entrenamiento adicional que puede modificar los parámetros del modelo.

---

## Regla de oro

> **Entrenamiento cambia lo que el modelo ha aprendido.**
>
> **Inferencia utiliza lo que el modelo ha aprendido.**
>
> **Prompting condiciona cómo se comporta durante una inferencia concreta.**
>
> **RAG le proporciona información externa durante esa inferencia.**
>
> **Fine-tuning modifica el modelo mediante entrenamiento adicional.**

Esta separación conceptual será indispensable para comprender correctamente todo lo que viene después.
