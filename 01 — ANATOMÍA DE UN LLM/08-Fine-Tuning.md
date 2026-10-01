# 08 — Fine-Tuning: adaptar un modelo preentrenado

> **Módulo:** Anatomía de un LLM
> **Nivel:** Desde cero → avanzado → Maestría/PhD
> **Prerequisitos:** Pretraining, Transformer, embeddings, attention, inferencia y contexto
> **Conceptos clave:** fine-tuning, pretraining, post-training, supervised fine-tuning, SFT, instruction tuning, dataset supervisado, labels, loss, catastrophic forgetting, PEFT, LoRA, adapters, QLoRA, RLHF, preference optimization, DPO, evaluación y seguridad.

---

# 1. ¿Qué es Fine-Tuning?

**Fine-tuning** significa **ajuste fino**.

Es el proceso de tomar un modelo que ya fue preentrenado y continuar su entrenamiento utilizando un conjunto de datos más específico para modificar o especializar su comportamiento.

La idea fundamental es:

```text
MODELO PREENTRENADO
        ↓
   DATOS ESPECÍFICOS
        ↓
   ENTRENAMIENTO
        ↓
PARÁMETROS AJUSTADOS
        ↓
MODELO ADAPTADO
```

El modelo no comienza desde cero.

Ya posee parámetros aprendidos durante el pretraining.

El fine-tuning intenta modificar esos parámetros —o una parte de ellos— para conseguir un comportamiento determinado.

---

# 2. La diferencia fundamental con el pretraining

En el capítulo anterior vimos:

```text
PRETRAINING

datos masivos
     ↓
modelo inicialmente sin conocimiento lingüístico suficiente
     ↓
next-token prediction
     ↓
optimización
     ↓
modelo base
```

En fine-tuning:

```text
FINE-TUNING

modelo preentrenado
     ↓
datos especializados
     ↓
objetivo específico
     ↓
optimización
     ↓
modelo adaptado
```

La diferencia fundamental es el **punto de partida**.

---

# 3. Una analogía

Imagina una persona que ya domina:

* lectura;
* escritura;
* gramática;
* matemáticas básicas;
* programación;
* comprensión de documentos.

Ahora quieres convertirla en especialista en auditoría financiera.

No necesitas enseñarle nuevamente a leer.

Puedes darle:

```text
informes financieros
normas
casos de auditoría
ejemplos de hallazgos
formatos de informes
```

y entrenarla específicamente en ese dominio.

Eso es una analogía de fine-tuning.

```text
conocimiento general
        ↓
especialización
        ↓
comportamiento adaptado
```

---

# 4. Fine-Tuning no significa "agregar información al contexto"

Esta distinción es fundamental.

Si hacemos:

```text
PROMPT
+
DOCUMENTOS
↓
LLM
```

estamos trabajando con contexto.

Si hacemos:

```text
DATOS DE ENTRENAMIENTO
↓
LOSS
↓
GRADIENTES
↓
ACTUALIZACIÓN DE PARÁMETROS
```

estamos entrenando el modelo.

Por tanto:

```text
PROMPT / CONTEXTO
→ cambia la entrada

FINE-TUNING
→ puede cambiar los parámetros
```

---

# 5. Cuatro mecanismos que no debemos confundir

Podemos resumirlos así:

| Técnica     |        ¿Modifica parámetros? | ¿Dónde está la información principal? |
| ----------- | ---------------------------: | ------------------------------------- |
| Prompting   |                           No | Prompt/contexto                       |
| RAG         |               Normalmente no | Base documental + contexto            |
| Fine-tuning | Sí, directa o indirectamente | Parámetros adaptados                  |
| Pretraining |                           Sí | Parámetros aprendidos                 |

Visualmente:

```text
PROMPTING
Prompt
  ↓
Modelo fijo
  ↓
Respuesta


RAG
Documentos
  ↓
Retrieval
  ↓
Contexto
  ↓
Modelo fijo
  ↓
Respuesta


FINE-TUNING
Datos especializados
  ↓
Entrenamiento
  ↓
Parámetros ajustados
  ↓
Modelo adaptado


PRETRAINING
Datos masivos
  ↓
Entrenamiento inicial
  ↓
Modelo base
```

---

# 6. ¿Qué queremos conseguir con Fine-Tuning?

El fine-tuning puede utilizarse para adaptar un modelo a diferentes objetivos.

Por ejemplo:

### Estilo

```text
responder con un estilo determinado
```

### Formato

```text
generar siempre una estructura específica
```

### Tarea

```text
clasificar documentos
```

### Dominio

```text
lenguaje médico
lenguaje jurídico
lenguaje financiero
```

### Comportamiento

```text
seguir instrucciones específicas
```

### Herramientas

En determinados sistemas, puede utilizarse entrenamiento para mejorar patrones relacionados con el uso de herramientas.

Pero es importante recordar:

> No existe una única clase de fine-tuning.

---

# 7. El punto de partida

Supongamos que tenemos:

```text
Modelo Base
```

Este modelo ya contiene parámetros:

$$
\theta
$$

Después tenemos un dataset especializado:

$$
D
$$

El fine-tuning busca encontrar nuevos parámetros:

$$
\theta'
$$

que produzcan un comportamiento más adecuado para el objetivo específico.

Conceptualmente:

$$
\theta
\rightarrow
\theta'
$$

No estamos construyendo:

$$
\theta'
$$

desde cero.

Estamos comenzando desde:

$$
\theta
$$

---

# 8. El dataset de Fine-Tuning

Aquí aparece una diferencia importante respecto al pretraining.

En pretraining podemos tener enormes colecciones de datos.

En fine-tuning normalmente tenemos un dataset más específico.

Por ejemplo:

```text
Entrada:
"¿Qué es una factura?"

Salida:
"Una factura es un documento comercial..."
```

Otro ejemplo:

```text
Entrada:
"Clasifica el siguiente texto como riesgo alto, medio o bajo."

Texto:
"Se detectaron transacciones duplicadas..."

Salida:
"ALTO"
```

El dataset define qué comportamiento queremos reforzar.

---

# 9. Dataset supervisado

Cuando proporcionamos ejemplos de entrada y salida esperada hablamos de:

**Supervised Fine-Tuning (SFT)**

o:

**Supervised Fine-Tuning / SFT**

La estructura básica es:

```text
INPUT
  ↓
MODELO
  ↓
PREDICCIÓN
  ↓
COMPARAR CON TARGET
  ↓
LOSS
  ↓
GRADIENTES
  ↓
ACTUALIZACIÓN
```

Esto conecta directamente con el capítulo de pretraining.

---

# 10. Ejemplo sencillo de SFT

Dataset:

```text
Entrada:
"Define inteligencia artificial."

Respuesta esperada:
"La inteligencia artificial es el campo..."
```

Otro:

```text
Entrada:
"Define machine learning."

Respuesta esperada:
"Machine learning es..."
```

Otro:

```text
Entrada:
"Define deep learning."

Respuesta esperada:
"Deep learning es..."
```

El modelo observa muchos ejemplos de este tipo.

La optimización intenta aumentar la probabilidad de producir las salidas esperadas.

---

# 11. El entrenamiento vuelve a utilizar Loss

Este punto es fundamental:

> Fine-tuning sigue siendo entrenamiento.

Por tanto, volvemos a tener:

```text
INPUT
 ↓
FORWARD PASS
 ↓
PREDICTION
 ↓
LOSS
 ↓
BACKPROPAGATION
 ↓
GRADIENTS
 ↓
OPTIMIZER
 ↓
PARAMETERS
```

La diferencia está principalmente en:

* modelo inicial;
* dataset;
* objetivo;
* escala;
* configuración del entrenamiento.

---

# 12. Fine-Tuning y next-token prediction

En un modelo de lenguaje autoregresivo, incluso durante SFT puede mantenerse una pérdida basada en predicción de tokens.

Por ejemplo:

```text
Entrada:
Explica qué es un Transformer.

Objetivo:
Un Transformer es una arquitectura de redes neuronales...
```

El modelo aprende a aumentar la probabilidad de la secuencia objetivo.

Conceptualmente:

$$
L =
-\sum_t
\log P_\theta(y_t|x,y_{<t})
$$

donde:

* \(x\) = entrada;
* \(y\) = respuesta objetivo;
* \(y_{<t}\) = tokens anteriores de la respuesta.

---

# 13. Instruction Tuning

Una forma muy importante de SFT es:

**Instruction Tuning**

El objetivo es entrenar al modelo para seguir instrucciones.

Por ejemplo:

```text
INSTRUCCIÓN:
Resume este documento.

CONTEXTO:
[documento]

RESPUESTA:
[resumen]
```

Otro:

```text
INSTRUCCIÓN:
Extrae las fechas.

TEXTO:
[documento]

RESPUESTA:
[fechas]
```

Otro:

```text
INSTRUCCIÓN:
Clasifica el siguiente texto.

TEXTO:
[texto]

RESPUESTA:
[clasificación]
```

El modelo aprende patrones de:

```text
instrucción
→ tarea
→ respuesta
```

---

# 14. Base Model versus Instruction Model

Podemos visualizar:

```text
                    PRETRAINING
                         ↓
                   BASE MODEL
                         ↓
                  INSTRUCTION TUNING
                         ↓
                INSTRUCTION MODEL
```

Un modelo base puede ser muy competente en predicción de lenguaje.

Pero un modelo instruccional está adicionalmente adaptado para seguir instrucciones.

---

# 15. ¿Qué ocurre con los parámetros?

Supongamos que el modelo tiene:

$$
\theta
$$

Durante fine-tuning podemos obtener:

$$
\theta'
$$

Conceptualmente:

```text
Modelo Base
θ
 ↓
Fine-Tuning
 ↓
θ'
```

Algunas configuraciones actualizan prácticamente todos los parámetros.

Otras actualizan solamente una pequeña parte.

Esto nos lleva a una distinción muy importante.

---

# 16. Full Fine-Tuning

En **full fine-tuning** se actualizan los parámetros del modelo de manera amplia.

Conceptualmente:

```text
Modelo
│
├── Parámetros 1 → actualizados
├── Parámetros 2 → actualizados
├── Parámetros 3 → actualizados
├── ...
└── Parámetros N → actualizados
```

Ventajas potenciales:

* máxima libertad de adaptación;
* puede conseguir cambios profundos;
* permite modificar ampliamente el comportamiento.

Desventajas:

* alto consumo de memoria;
* alto costo computacional;
* almacenamiento considerable;
* mayor complejidad;
* riesgo de degradar capacidades anteriores.

Para modelos muy grandes puede ser extremadamente costoso.

---

# 17. PEFT

Para reducir el costo aparece una familia de técnicas llamada:

**Parameter-Efficient Fine-Tuning (PEFT)**

o:

**Fine-Tuning eficiente en parámetros**.

La idea:

> No necesitamos modificar todos los parámetros del modelo para adaptarlo.

Podemos modificar solamente una pequeña cantidad.

```text
Modelo base
████████████████████████████
      ↓
pequeña parte entrenable
████
```

El resto permanece congelado.

---

# 18. Modelo congelado

Cuando decimos que un parámetro está **frozen**:

```text
frozen = congelado
```

significa que no se actualiza durante ese entrenamiento.

Podemos tener:

```text
MODELO
│
├── 99% congelado
│
└── 1% entrenable
```

La proporción exacta depende de la técnica y configuración.

---

# 19. Adapters

Una estrategia consiste en insertar pequeños módulos entrenables dentro del modelo.

Conceptualmente:

```text
Transformer Layer
       ↓
   Adapter
       ↓
Transformer Layer
       ↓
   Adapter
       ↓
...
```

El modelo principal permanece mayormente congelado.

Los adapters contienen muchos menos parámetros que el modelo completo.

---

# 20. LoRA

Una de las técnicas PEFT más importantes es:

**LoRA — Low-Rank Adaptation**

La idea matemática básica consiste en representar la actualización de una matriz mediante una factorización de bajo rango.

Supongamos que tenemos una matriz:

$$
W
$$

En lugar de modificar directamente:

$$
W
$$

podemos representar una actualización:

$$
W' = W + \Delta W
$$

y aproximar:

$$
\Delta W = BA
$$

donde:

* \(A\) y \(B\) son matrices pequeñas;
* el rango es mucho menor que las dimensiones completas de \(W\).

Por tanto:

```text
W
+
pequeña actualización de bajo rango
=
W'
```

---

# 21. Intuición de LoRA

Supongamos que tenemos una enorme matriz:

```text
████████████████████████
████████████████████████
████████████████████████
████████████████████████
```

Modificarla completamente puede ser costoso.

LoRA intenta aprender:

```text
ΔW
```

de una forma mucho más compacta.

Visualmente:

```text
MATRIZ ORIGINAL
      W
      │
      ├───────────────┐
      │               │
      ↓               ↓
    BAJO          ACTUALIZACIÓN
   RANGO             LoRA
      │               │
      └───────┬───────┘
              ↓
         W + ΔW
```

---

# 22. ¿Por qué funciona una actualización de bajo rango?

La hipótesis práctica es que muchas adaptaciones específicas no requieren modificar completamente el espacio de parámetros.

Es posible representar parte de la adaptación mediante una actualización de menor dimensionalidad.

No significa que:

> "Toda inteligencia está en unas pocas dimensiones."

Significa que, para determinadas tareas y configuraciones, una adaptación útil puede aproximarse mediante una estructura de bajo rango.

---

# 23. QLoRA

Una extensión conocida es:

**QLoRA**

La idea combina:

* cuantización del modelo base;
* adaptación mediante LoRA.

Conceptualmente:

```text
MODELO BASE CUANTIZADO
          ↓
       LoRA
          ↓
     FINE-TUNING
```

La cuantización reduce los requisitos de memoria.

LoRA reduce la cantidad de parámetros entrenables.

La combinación puede hacer viable adaptar modelos que serían mucho más costosos mediante full fine-tuning.

---

# 24. LoRA no crea un modelo completamente independiente

Una adaptación LoRA normalmente puede representarse conceptualmente como:

```text
MODELO BASE
     +
ADAPTADOR LoRA
     ↓
MODELO ADAPTADO
```

Esto tiene una consecuencia importante:

> El adapter depende del modelo base compatible.

Por ello es útil pensar en:

```text
Base Model
   +
Adapter
```

en lugar de imaginar necesariamente dos modelos completamente independientes.

---

# 25. Fine-Tuning como modificación de comportamiento

Supongamos un modelo general:

```text
Modelo General
```

y queremos especializarlo en:

```text
Auditoría financiera
```

Podríamos utilizar ejemplos:

```text
Documento
 ↓
análisis
 ↓
hallazgos
 ↓
riesgos
 ↓
conclusión
```

El fine-tuning puede ayudar a que el modelo aprenda patrones de respuesta relacionados con ese dominio y formato.

Pero no significa automáticamente que el modelo tendrá acceso a:

> las normas o datos más recientes.

Para información cambiante, RAG o acceso a herramientas puede ser más apropiado.

---

# 26. Fine-Tuning no reemplaza RAG

Esta distinción es especialmente importante en sistemas empresariales.

Supongamos:

> Una empresa modifica su política interna cada semana.

Podríamos intentar hacer fine-tuning con cada nueva versión.

Eso sería poco práctico.

Una arquitectura más apropiada podría ser:

```text
DOCUMENTOS ACTUALIZADOS
        ↓
       RAG
        ↓
CONTEXTO ACTUAL
        ↓
       LLM
```

El fine-tuning puede utilizarse para enseñar:

```text
cómo responder
```

mientras RAG puede proporcionar:

```text
qué información actual utilizar
```

Una simplificación útil es:

> **Fine-tuning puede adaptar comportamiento; RAG puede proporcionar conocimiento externo actualizado durante la inferencia.**

No es una regla absoluta, pero es un excelente modelo mental inicial.

---

# 27. Ejemplo: chatbot empresarial

Supongamos una empresa inmobiliaria.

Queremos que el modelo:

* clasifique leads;
* haga preguntas específicas;
* identifique intención;
* produzca una estructura JSON;
* utilice un tono profesional.

Podemos dividir el problema:

```text
COMPORTAMIENTO
     ↓
Fine-Tuning / prompting

CONOCIMIENTO ACTUAL
     ↓
RAG

DATOS DEL CLIENTE
     ↓
CRM / herramientas

ACCIONES
     ↓
Function calling / tools
```

Esto es mucho más potente que intentar resolver todo mediante fine-tuning.

---

# 28. Fine-Tuning y formato

Supongamos que queremos:

```json
{
  "riesgo": "ALTO",
  "monto": 12500,
  "descripcion": "..."
}
```

Podemos entrenar ejemplos con ese formato.

El modelo puede aprender una mayor tendencia a producir estructuras similares.

Pero esto no garantiza una salida siempre válida.

En sistemas de producción puede ser necesario combinar:

```text
Prompt
+
Structured Outputs
+
Validación
+
Fine-Tuning
```

El fine-tuning no reemplaza los mecanismos de validación.

---

# 29. Catastrophic Forgetting

Uno de los problemas clásicos del fine-tuning es:

**Catastrophic Forgetting**

o **olvido catastrófico**.

Un modelo puede adaptarse a una tarea nueva y degradar parte de capacidades previamente aprendidas.

Conceptualmente:

```text
ANTES

capacidad general ██████████


DESPUÉS DE ADAPTACIÓN

tarea específica █████████████
capacidad general ██████
```

La magnitud depende de:

* dataset;
* cantidad de entrenamiento;
* learning rate;
* arquitectura;
* técnica utilizada;
* distribución de datos.

---

# 30. ¿Por qué ocurre?

Imagina que un modelo tiene representaciones útiles para muchas tareas.

Si realizamos un entrenamiento agresivo sobre una distribución muy limitada:

```text
datos especializados
      ↓
actualizaciones repetidas
      ↓
parámetros
      ↓
representaciones anteriores alteradas
```

algunas capacidades pueden degradarse.

Esto es una de las razones por las que el diseño del dataset y la estrategia de entrenamiento son importantes.

---

# 31. Dataset pequeño no significa necesariamente dataset malo

Un dataset de fine-tuning puede ser mucho menor que un dataset de pretraining.

Pero:

> La calidad de los ejemplos puede ser más importante que simplemente aumentar su cantidad.

Ejemplo:

```text
100.000 ejemplos inconsistentes
```

pueden ser menos útiles que:

```text
10.000 ejemplos cuidadosamente diseñados
```

para un objetivo específico.

No existe una proporción universal.

---

# 32. Garbage In, Garbage Out

El principio clásico:

> **Garbage In, Garbage Out**

también se aplica al fine-tuning.

Si entrenamos con:

```text
respuestas incorrectas
+
contradicciones
+
formatos inconsistentes
+
etiquetas erróneas
```

podemos reforzar esos errores.

Por tanto:

```text
CALIDAD DEL DATASET
        ↓
CALIDAD DEL ENTRENAMIENTO
        ↓
CALIDAD DEL COMPORTAMIENTO
```

---

# 33. Consistencia de las etiquetas

Supongamos que queremos clasificar riesgos:

```text
ALTO
MEDIO
BAJO
```

Pero nuestro dataset contiene:

```text
Caso A → ALTO
Caso B → CRÍTICO
Caso C → Alto
Caso D → riesgo alto
```

Tenemos inconsistencias.

El modelo debe inferir qué significa cada etiqueta.

Es preferible normalizar:

```text
ALTO
MEDIO
BAJO
```

cuando ese sea realmente el esquema deseado.

---

# 34. Dataset representativo

Otro problema es el sesgo de cobertura.

Supongamos que entrenamos un chatbot únicamente con:

```text
usuarios jóvenes
```

y después esperamos que funcione igualmente bien con:

```text
usuarios de todas las edades
```

Puede existir una diferencia de distribución.

La composición del dataset debe representar razonablemente los escenarios objetivo.

---

# 35. Data distribution

Podemos pensar:

```text
Distribución de entrenamiento
            P_train(x)

Distribución real
            P_real(x)
```

Si:

$$
P_{train}(x)
$$

es muy diferente de:

$$
P_{real}(x)
$$

puede producirse una degradación del rendimiento fuera de la distribución de entrenamiento.

Esto se relaciona con:

**distribution shift**.

---

# 36. Evaluación antes del Fine-Tuning

Nunca deberíamos comenzar con:

```text
entrenar
↓
esperar que funcione
```

Es mejor:

```text
Modelo base
 ↓
Benchmark inicial
 ↓
Fine-tuning
 ↓
Benchmark posterior
 ↓
Comparación
```

Así podemos determinar si realmente conseguimos una mejora.

---

# 37. El baseline

El modelo original funciona como:

**baseline**

o línea base.

Supongamos:

```text
Modelo base
85% clasificación correcta
```

Después:

```text
Fine-tuned
91%
```

Tenemos evidencia de mejora en esa métrica y conjunto de prueba.

Pero debemos preguntar:

* ¿qué ocurrió con otras tareas?
* ¿aumentaron las alucinaciones?
* ¿se perdió capacidad general?
* ¿mejoró realmente el caso de negocio?
* ¿qué ocurrió con seguridad?
* ¿qué ocurrió fuera del dataset?

---

# 38. Train / validation / test

Un esquema clásico es:

```text
DATASET
   │
   ├── TRAIN
   │
   ├── VALIDATION
   │
   └── TEST
```

### Train

Utilizado para actualizar parámetros.

### Validation

Utilizado para tomar decisiones de configuración y monitorear generalización.

### Test

Reservado para evaluación final.

El conjunto de test no debería convertirse accidentalmente en parte del entrenamiento.

---

# 39. Data leakage

Existe otro riesgo:

**data leakage**.

Ocurre cuando información que debería estar aislada termina influyendo en el entrenamiento o selección del modelo.

Ejemplo:

```text
TRAIN
 ↓
contiene información del TEST
 ↓
resultado
 ↓
evaluación artificialmente optimista
```

Por eso la separación de datos es crítica.

---

# 40. Fine-Tuning y temperatura

Otra confusión:

> "Si hago fine-tuning, ya no necesito controlar la temperatura."

Incorrecto.

Fine-tuning y sampling son mecanismos diferentes.

```text
Fine-Tuning
→ modifica/adapta parámetros

Temperature
→ modifica la distribución utilizada durante generación
```

Podemos utilizar ambos.

---

# 41. Fine-Tuning y prompt

También pueden combinarse.

```text
                    MODELO
                      │
              Fine-Tuning
                      ↓
              comportamiento
                      ↓
                   PROMPT
                      ↓
                  contexto
                      ↓
                INFERENCIA
```

El fine-tuning no elimina la necesidad de prompt engineering.

En sistemas complejos, ambas capas pueden complementarse.

---

# 42. Fine-Tuning y herramientas

Supongamos que queremos que un agente utilice:

```text
CRM
base de datos
calculadora
API
```

No necesariamente necesitamos enseñar todos esos datos mediante fine-tuning.

Podemos utilizar herramientas:

```text
LLM
 ↓
decide acción
 ↓
tool call
 ↓
resultado
 ↓
LLM
```

El entrenamiento puede ayudar al modelo a producir patrones de llamadas correctos, pero la ejecución real ocurre fuera del modelo.

---

# 43. Preference Optimization

Después del SFT pueden existir etapas orientadas a preferencias.

La idea general:

```text
Respuesta A
Respuesta B
```

y un evaluador humano o automático determina cuál es preferible según determinados criterios.

Entonces se entrena al modelo para favorecer respuestas preferidas.

Esto pertenece a una familia más amplia de:

**preference optimization**.

---

# 44. RLHF

Uno de los enfoques históricos más conocidos es:

**RLHF — Reinforcement Learning from Human Feedback**

Una representación simplificada:

```text
MODELO
  ↓
genera respuestas
  ↓
humanos comparan / puntúan
  ↓
datos de preferencia
  ↓
modelo de recompensa
  ↓
optimización
  ↓
modelo adaptado
```

El objetivo es alinear el comportamiento del modelo con determinadas preferencias humanas.

La implementación real puede ser considerablemente más compleja.

---

# 45. DPO

Otra técnica conocida es:

**DPO — Direct Preference Optimization**

En términos conceptuales, utiliza pares de preferencias:

```text
prompt

respuesta preferida
respuesta no preferida
```

y optimiza directamente una función relacionada con esas preferencias, evitando la necesidad de entrenar explícitamente un reward model de la misma manera que en pipelines RLHF clásicos.

Una forma conceptual:

```text
Prompt
  │
  ├── respuesta preferida ✓
  │
  └── respuesta rechazada ✗
          ↓
      optimización
```

DPO es parte de una familia más amplia de métodos de optimización basada en preferencias.

---

# 46. SFT + Preference Optimization

Un pipeline conceptual puede ser:

```text
PRETRAINING
     ↓
MODELO BASE
     ↓
SFT
     ↓
MODELO INSTRUCCIONAL
     ↓
PREFERENCE OPTIMIZATION
     ↓
MODELO POST-TRAINED
```

No todos los modelos siguen exactamente este pipeline.

Pero sirve para comprender la evolución:

```text
aprender lenguaje
       ↓
aprender instrucciones
       ↓
ajustar preferencias / comportamiento
```

---

# 47. Fine-Tuning y alignment

El término:

**alignment**

puede referirse a esfuerzos para hacer que el comportamiento del modelo sea compatible con determinados objetivos, instrucciones, preferencias o restricciones.

Fine-tuning puede formar parte de un pipeline de alignment.

Pero:

> Fine-tuning y alignment no son sinónimos.

Alignment es un concepto más amplio.

---

# 48. Fine-Tuning de dominio

Podemos adaptar un modelo a un lenguaje especializado.

Ejemplos:

```text
modelo general
      ↓
datos jurídicos
      ↓
modelo adaptado al dominio jurídico
```

o:

```text
modelo general
      ↓
documentación médica
      ↓
modelo adaptado al dominio
```

Esto puede mejorar determinados patrones lingüísticos del dominio.

Pero no implica automáticamente que el modelo se convierta en una autoridad confiable en ese dominio.

---

# 49. Fine-Tuning no garantiza factualidad

Supongamos que entrenamos un modelo con información incorrecta.

El modelo puede aprender a producir respuestas incorrectas de manera más consistente.

Por tanto:

```text
fine-tuning
≠
garantía de verdad
```

La factualidad depende de:

* calidad de datos;
* arquitectura del sistema;
* recuperación de información;
* herramientas;
* evaluación;
* diseño de prompts;
* mecanismos de verificación.

---

# 50. Fine-Tuning no convierte al modelo en una base de datos

Este error es especialmente importante.

Supongamos que entrenamos:

```text
100.000 documentos
```

No debemos asumir:

```text
modelo = buscador perfecto de esos 100.000 documentos
```

Si necesitamos recuperar información exacta:

```text
RAG
+
búsqueda
+
base de datos
```

puede ser mucho más apropiado.

Fine-tuning es principalmente una técnica de **adaptación del modelo**, no una base documental convencional.

---

# 51. Fine-Tuning para estilo

Un uso relativamente intuitivo es adaptar el estilo.

Dataset:

```text
Pregunta
→
Respuesta escrita en estilo deseado
```

Con suficientes ejemplos, el modelo puede aprender patrones estilísticos.

Sin embargo:

> Si solamente necesitamos un cambio sencillo de estilo, hacer fine-tuning puede ser innecesario.

Primero debemos evaluar:

```text
Prompt
 ↓
¿es suficiente?
```

Si no:

```text
Few-shot
 ↓
¿es suficiente?
```

Si no:

```text
RAG / tools / fine-tuning
```

según el problema.

---

# 52. ¿Cuándo tiene sentido considerar Fine-Tuning?

Una decisión conceptual puede ser:

```text
¿Necesito conocimiento actualizado?
        │
        ├── Sí → RAG / tools
        │
        └── No
             ↓
¿Necesito un comportamiento altamente consistente?
        │
        ├── No → Prompt / few-shot
        │
        └── Sí
             ↓
¿Hay suficientes ejemplos de calidad?
        │
        ├── No → mejorar dataset
        │
        └── Sí
             ↓
       evaluar Fine-Tuning
```

No es una regla universal, pero ayuda a estructurar la decisión.

---

# 53. Fine-Tuning como problema de optimización

A nivel avanzado podemos verlo así:

Tenemos:

$$
D=\{(x_i,y_i)\}_{i=1}^{N}
$$

y un modelo:

$$
f_\theta
$$

Queremos minimizar:

$$
L(\theta)
=
\frac{1}{N}
\sum_{i=1}^{N}
\ell(f_\theta(x_i),y_i)
$$

Partimos de:

$$
\theta_0
$$

y buscamos:

$$
\theta^*
$$

mediante actualizaciones:

$$
\theta_{t+1}
=
\theta_t
-
\eta_t
\nabla_\theta L
$$

La diferencia esencial respecto al entrenamiento desde cero es:

$$
\theta_0
$$

ya contiene información aprendida durante pretraining.

---

# 54. Fine-Tuning como transferencia de aprendizaje

Esto conecta con una idea clásica de machine learning:

**Transfer Learning**

La idea:

```text
aprendizaje anterior
       ↓
transferencia
       ↓
nueva tarea
```

En LLMs:

```text
pretraining
       ↓
representaciones generales
       ↓
fine-tuning
       ↓
tarea / dominio específico
```

El modelo no comienza nuevamente desde parámetros aleatorios.

---

# 55. ¿Por qué es tan importante partir de un modelo preentrenado?

Entrenar desde cero requiere aprender:

* lenguaje;
* sintaxis;
* semántica;
* patrones;
* estructuras;
* relaciones;
* programación;
* múltiples regularidades.

Fine-tuning permite aprovechar ese conocimiento previo.

Podemos representarlo:

```text
ENTRENAMIENTO DESDE CERO

aleatorio
   ↓
lenguaje
   ↓
representaciones
   ↓
capacidades
   ↓
especialización


FINE-TUNING

modelo preentrenado
   ↓
especialización
```

Esto reduce drásticamente el trabajo necesario para determinadas adaptaciones.

---

# 56. Un problema: distribution shift

Supongamos que el modelo fue preentrenado con:

```text
lenguaje general
```

y lo ajustamos con:

```text
documentos financieros extremadamente especializados
```

La nueva distribución puede ser muy diferente.

Podemos tener:

$$
P_{general}(x)
\neq
P_{domain}(x)
$$

El fine-tuning intenta adaptar el modelo a esa nueva distribución.

---

# 57. Fine-Tuning y estabilidad

Un problema importante es encontrar un equilibrio entre:

```text
adaptación
```

y:

```text
preservación
```

Si el ajuste es insuficiente:

```text
modelo no aprende suficientemente la nueva tarea
```

Si es excesivo:

```text
puede degradar capacidades anteriores
```

Por eso el fine-tuning es una cuestión de optimización y evaluación, no simplemente de "entrenar más".

---

# 58. Hiperparámetros importantes

Algunos hiperparámetros relevantes pueden incluir:

* learning rate;
* batch size;
* número de epochs;
* warmup;
* weight decay;
* secuencia máxima;
* número de pasos;
* LoRA rank;
* LoRA alpha;
* dropout;
* scheduler;
* selección de capas entrenables.

No todos aplican de la misma manera a todas las técnicas.

---

# 59. LoRA rank

En LoRA aparece un hiperparámetro importante:

$$
r
$$

el **rank** de la actualización.

Conceptualmente:

```text
rank pequeño
→ menos parámetros
→ menor capacidad de adaptación

rank mayor
→ más parámetros
→ mayor capacidad potencial
```

Pero:

> Mayor rank no garantiza automáticamente mejor resultado.

Debe evaluarse experimentalmente.

---

# 60. Fine-Tuning y cuantización

Podemos tener:

```text
modelo FP16 / BF16
```

o una versión cuantizada:

```text
INT8
INT4
...
```

La cuantización reduce precisión numérica para ahorrar memoria y, dependiendo del hardware y método, acelerar ciertas operaciones.

Con técnicas como QLoRA:

```text
modelo cuantizado
+
adaptadores LoRA
```

se puede realizar entrenamiento eficiente en memoria.

---

# 61. Merge de LoRA

Después del entrenamiento podemos mantener:

```text
Base Model
+
LoRA Adapter
```

o, dependiendo de la infraestructura y necesidades, combinar el adapter con determinados pesos del modelo base mediante un proceso de **merge**.

Conceptualmente:

```text
Base
  +
LoRA
  ↓
modelo combinado
```

Esto tiene implicaciones para:

* despliegue;
* almacenamiento;
* compatibilidad;
* capacidad de cambiar adapters;
* gestión de versiones.

---

# 62. Arquitectura modular con adapters

Una ventaja interesante de los adapters es la posibilidad conceptual de tener:

```text
             MODELO BASE
                  │
        ┌─────────┼─────────┐
        ↓         ↓         ↓
      LoRA A    LoRA B    LoRA C
        │         │         │
     Finanzas   Legal    Código
```

Un mismo modelo base puede utilizar distintos adaptadores especializados.

Esto abre posibilidades para arquitecturas modulares.

---

# 63. Fine-Tuning y sistemas multiempresa

Supongamos una plataforma que atiende:

```text
Empresa A → sector financiero
Empresa B → inmobiliaria
Empresa C → educación
```

Podría existir:

```text
              MODELO BASE
                  │
        ┌─────────┼─────────┐
        ↓         ↓         ↓
      Adapter A Adapter B Adapter C
```

Sin embargo, esto requiere estudiar cuidadosamente:

* aislamiento de datos;
* privacidad;
* gobernanza;
* compatibilidad;
* seguridad;
* costos;
* versionado.

---

# 64. Riesgo de contaminación entre clientes

En sistemas empresariales, entrenar conjuntamente datos de diferentes clientes puede crear riesgos.

Supongamos:

```text
Cliente A
+
Cliente B
↓
dataset
↓
fine-tuning
```

Existe una pregunta crítica:

> ¿Podría el modelo reproducir información asociada a A cuando interactúa con B?

Este problema debe tratarse como una cuestión de arquitectura, privacidad y gobernanza.

No basta con asumir que los datos quedarán perfectamente aislados dentro de los parámetros.

---

# 65. Fine-Tuning y seguridad

Un dataset de entrenamiento también puede introducir comportamientos no deseados.

Ejemplo:

```text
datos maliciosos
      ↓
fine-tuning
      ↓
comportamiento alterado
```

Por eso deben existir controles sobre:

* origen de datos;
* integridad;
* permisos;
* etiquetado;
* revisión;
* evaluación;
* detección de ataques;
* pruebas adversariales.

---

# 66. Fine-Tuning y prompt injection

Prompt injection ocurre durante la interacción con un sistema.

Fine-tuning ocurre durante entrenamiento.

Son problemas diferentes:

```text
PROMPT INJECTION
→ ataque al comportamiento durante inferencia


DATA POISONING
→ manipulación de datos de entrenamiento


FINE-TUNING
→ mecanismo legítimo de adaptación
```

Sin embargo, pueden interactuar en sistemas reales.

---

# 67. Fine-Tuning y gobernanza

En producción deberíamos poder responder:

* ¿Qué modelo base utilizamos?
* ¿Qué dataset se utilizó?
* ¿Quién autorizó los datos?
* ¿Qué versión del dataset?
* ¿Qué hiperparámetros?
* ¿Qué evaluación se realizó?
* ¿Qué riesgos fueron detectados?
* ¿Qué versión del adapter está desplegada?
* ¿Cómo podemos revertirlo?

Esto convierte el fine-tuning en un problema de **MLOps y gobernanza**, no únicamente de machine learning.

---

# 68. Versionado

Un sistema profesional debería poder identificar:

```text
Base Model:
model-v1

Dataset:
dataset-v7

Fine-Tuning:
run-2026-09-30

Adapter:
lora-finanzas-v3

Evaluation:
eval-v5
```

Esto permite reproducibilidad y auditoría.

---

# 69. Experimento reproducible

Un pipeline conceptual:

```text
Dataset v1
     ↓
Training Config v1
     ↓
Base Model v2
     ↓
Fine-Tuning Run #17
     ↓
Adapter v3
     ↓
Evaluation v5
     ↓
Deployment
```

Cada componente debe poder rastrearse.

---

# 70. Evaluación de un Fine-Tuning

No basta con observar algunas respuestas y concluir:

> "Funciona mejor."

Debemos utilizar evaluaciones.

Podemos medir:

### Calidad

* accuracy;
* F1;
* exact match;
* BLEU/ROUGE en determinados escenarios;
* métricas específicas de tarea.

### Generación

* factualidad;
* relevancia;
* consistencia;
* formato.

### Seguridad

* jailbreak resistance;
* toxicidad;
* fuga de información;
* cumplimiento de políticas.

### Negocio

* tasa de conversión;
* tiempo de respuesta;
* costo;
* errores;
* intervención humana.

---

# 71. Evaluación humana

Para muchas tareas generativas, las métricas automáticas no son suficientes.

Podemos utilizar:

```text
respuesta A
respuesta B
     ↓
evaluador
     ↓
preferencia
```

Los evaluadores pueden considerar:

* exactitud;
* utilidad;
* claridad;
* relevancia;
* seguridad;
* estilo.

La evaluación humana también debe diseñarse cuidadosamente para reducir sesgos.

---

# 72. Fine-Tuning no es una bala de plata

Una mala arquitectura no se arregla automáticamente entrenando un modelo.

Si el problema real es:

```text
información desactualizada
```

probablemente necesitamos revisar:

```text
RAG / herramientas / fuentes
```

Si el problema es:

```text
salida inválida
```

podemos necesitar:

```text
structured outputs / validación
```

Si el problema es:

```text
comportamiento inconsistente
```

podemos evaluar:

```text
prompt engineering
few-shot
fine-tuning
```

La técnica debe corresponder al problema.

---

# 73. Árbol de decisión conceptual

```text
                   PROBLEMA
                      │
          ┌───────────┼────────────┐
          ↓           ↓            ↓
     conocimiento  comportamiento  acción
      externo       específico     externa
          │           │            │
          ↓           ↓            ↓
         RAG       Prompt/SFT     Tools
                       │
                       ↓
                  Fine-Tuning
```

En sistemas reales estas técnicas pueden combinarse.

---

# 74. Arquitectura híbrida

Una aplicación moderna puede tener:

```text
                  USUARIO
                     ↓
                   PROMPT
                     ↓
              MODELO ADAPTADO
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
       RAG          TOOLS       MEMORIA
        │            │            │
        └────────────┼────────────┘
                     ↓
                  RESPUESTA
```

El modelo puede haber sido:

```text
Pretrained
   ↓
Fine-Tuned
```

y posteriormente utilizar:

```text
Prompt
+
RAG
+
Tools
+
Memory
```

durante inferencia.

Esta es una de las ideas centrales de la ingeniería moderna de sistemas de IA:

> **No todo problema debe resolverse dentro de los parámetros del modelo.**

---

# 75. Nivel avanzado: full fine-tuning versus PEFT

| Característica           |       Full Fine-Tuning |           PEFT |
| ------------------------ | ---------------------: | -------------: |
| Parámetros actualizados  |         Muchos / todos |          Pocos |
| Memoria                  |                   Alta |          Menor |
| Costo                    |                   Alto |          Menor |
| Adaptación               |                 Amplia |     Específica |
| Almacenamiento por tarea |                   Alto |           Bajo |
| Modularidad              |                  Menor |          Mayor |
| Ejemplos                 | actualización completa | LoRA, adapters |

La elección depende del objetivo y de las restricciones del sistema.

---

# 76. Nivel Maestría: transferencia y regularización

A nivel avanzado aparece una pregunta:

> ¿Cómo adaptar el modelo sin destruir las representaciones útiles que ya posee?

Esto conecta fine-tuning con:

* regularización;
* learning rate;
* weight decay;
* freezing;
* adapters;
* parameter-efficient learning;
* distillation;
* continual learning.

El objetivo puede verse como un equilibrio:

$$
L_{total}
=
L_{task}
+
\lambda L_{regularization}
$$

donde el segundo término puede ayudar a limitar determinadas modificaciones.

La formulación concreta depende del método utilizado.

---

# 77. Continual Learning

Supongamos que queremos adaptar continuamente un modelo:

```text
Tarea A
 ↓
Tarea B
 ↓
Tarea C
 ↓
Tarea D
```

Aparece el problema:

> ¿Cómo incorporar aprendizaje nuevo sin destruir lo aprendido anteriormente?

Esto se estudia como:

**Continual Learning**

o aprendizaje continuo.

Está relacionado con el catastrophic forgetting.

---

# 78. Knowledge Editing

Existe también una línea de investigación llamada:

**Knowledge Editing**

Busca modificar aspectos específicos del comportamiento o conocimiento de un modelo sin realizar necesariamente un fine-tuning completo.

Conceptualmente:

```text
Modelo
 ↓
identificar conocimiento
 ↓
editar comportamiento
 ↓
modelo modificado
```

Es un área de investigación activa y tiene desafíos importantes relacionados con:

* generalización;
* consistencia;
* efectos secundarios;
* permanencia;
* localización del conocimiento.

No debe confundirse con editar una base de datos externa.

---

# 79. Distillation

Otra técnica relacionada es:

**Knowledge Distillation**

Un modelo grande, denominado frecuentemente **teacher**, puede utilizarse para ayudar a entrenar un modelo más pequeño, denominado **student**.

```text
TEACHER
   ↓
predicciones / señales
   ↓
STUDENT
```

Puede utilizarse para obtener modelos más pequeños o especializados.

Esto amplía la idea de transferencia de conocimiento más allá del fine-tuning convencional.

---

# 80. Investigación: ¿qué cambia realmente durante Fine-Tuning?

Una pregunta de investigación profunda es:

> ¿Qué representaciones internas cambian cuando hacemos fine-tuning?

Podemos imaginar:

```text
Modelo base
     ↓
representaciones
     ↓
fine-tuning
     ↓
representaciones modificadas
```

Pero el cambio no necesariamente ocurre de manera uniforme en todas las capas.

Los investigadores estudian:

* activaciones;
* pesos;
* atención;
* representaciones;
* subespacios;
* direcciones semánticas;
* cambios por capa.

---

# 81. ¿Todas las capas aprenden lo mismo?

No.

En un Transformer profundo, diferentes capas pueden desempeñar diferentes roles funcionales.

Pero sería incorrecto afirmar que:

```text
capa 1 = gramática
capa 2 = semántica
capa 3 = razonamiento
```

como una división universal.

La realidad es más distribuida y dependiente de la arquitectura y tarea.

---

# 82. Representaciones y subespacios

En investigación avanzada se estudia si una tarea determinada puede asociarse con cambios en ciertos subespacios de representación.

Conceptualmente:

```text
espacio de representación
┌─────────────────────────┐
│                         │
│     subespacio A        │
│                         │
│             subespacio B│
│                         │
└─────────────────────────┘
```

LoRA, adapters y otras técnicas pueden interpretarse parcialmente desde esta perspectiva.

Pero estas visualizaciones son simplificaciones.

---

# 83. Fine-Tuning y emergent capabilities

Una cuestión de investigación es si determinadas capacidades aparecen de manera abrupta al aumentar escala o si algunos fenómenos aparentemente abruptos son consecuencia de las métricas utilizadas.

Por eso debemos evitar afirmaciones simplistas como:

> "A partir de cierto número de parámetros aparece mágicamente el razonamiento."

La relación entre:

* escala;
* entrenamiento;
* arquitectura;
* datos;
* evaluación;
* capacidades;

es mucho más compleja.

---

# 84. Un ejemplo completo de extremo a extremo

Supongamos que queremos crear un modelo especializado en análisis de auditoría.

### Etapa 1 — Modelo base

```text
LLM general
```

### Etapa 2 — Dataset

```text
casos de auditoría
+
preguntas
+
respuestas expertas
+
clasificaciones
+
formatos estructurados
```

### Etapa 3 — SFT

```text
entrada
↓
respuesta esperada
↓
loss
↓
actualización
```

### Etapa 4 — Evaluación

```text
casos no vistos
↓
métricas
↓
evaluación humana
```

### Etapa 5 — RAG

```text
normativas actualizadas
↓
retrieval
↓
contexto
```

### Etapa 6 — Tools

```text
ERP
base de datos
calculadora
```

### Arquitectura final

```text
                   USUARIO
                      ↓
                    PROMPT
                      ↓
              MODELO ESPECIALIZADO
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
         RAG         TOOLS      CONTEXTO
          │           │           │
          └───────────┼───────────┘
                      ↓
                  RESPUESTA
```

Observa que fine-tuning es solamente una parte del sistema.

---

# 85. El error de "meter conocimiento" mediante Fine-Tuning

Supongamos que una empresa tiene:

```text
50.000 documentos
```

y quiere que el modelo pueda responder preguntas sobre ellos.

La primera pregunta no debería ser:

> "¿Cómo hago fine-tuning con los 50.000 documentos?"

Debería ser:

> "¿El problema requiere que el modelo aprenda un comportamiento o que pueda recuperar información documental?"

Si la respuesta es:

```text
recuperar información
```

RAG puede ser más apropiado.

Si la respuesta es:

```text
adoptar un comportamiento
```

fine-tuning puede ser candidato.

---

# 86. Una regla mental extremadamente útil

Puedes recordar:

```text
PROMPT
= dime cómo responder ahora


RAG
= aquí tienes información externa relevante


FINE-TUNING
= aprende este comportamiento


PRETRAINING
= aprende patrones generales del mundo lingüístico
```

Es una simplificación, pero ayuda a construir una arquitectura mental correcta.

---

# 87. Pretraining → Fine-Tuning → Inference

La secuencia completa:

```text
                 PRETRAINING
                      ↓
               MODELO BASE
                      ↓
                FINE-TUNING
                      ↓
             MODELO ADAPTADO
                      ↓
                  PROMPT
                      ↓
                 CONTEXTO
                      ↓
                  INFERENCE
                      ↓
                 RESPUESTA
```

Y en sistemas modernos:

```text
                     MODELO
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
       RAG            TOOLS         MEMORY
        │              │              │
        └──────────────┼──────────────┘
                       ↓
                   RESPUESTA
```

---

# 88. Mapa conceptual final

```text
                         FINE-TUNING
                              │
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
      MODELO BASE           DATOS              OBJETIVO
          │                   │                   │
          ↓                   ↓                   ↓
    preentrenado          especializados       tarea
          │                   │                 estilo
          │                   │                 dominio
          │                   │                 formato
          └────────────┬──────┴─────────────────┘
                       ↓
                    SFT
                       ↓
                    LOSS
                       ↓
                BACKPROPAGATION
                       ↓
                  OPTIMIZACIÓN
                       ↓
             PARÁMETROS ADAPTADOS
                       │
              ┌────────┴────────┐
              ↓                 ↓
        FULL FINE-TUNING       PEFT
                                │
                     ┌──────────┼──────────┐
                     ↓          ↓          ↓
                   LoRA      Adapters    QLoRA
```

---

# 89. Las diez ideas esenciales

Si solamente recuerdas diez conceptos de este capítulo:

1. **Fine-tuning adapta un modelo previamente entrenado.**
2. **No parte desde parámetros aleatorios.**
3. **SFT utiliza ejemplos de entrada y salida esperada.**
4. **Instruction tuning es una forma importante de adaptación para seguir instrucciones.**
5. **Full fine-tuning puede actualizar una gran parte o la totalidad de los parámetros.**
6. **PEFT permite adaptar modelos modificando muchos menos parámetros.**
7. **LoRA representa la actualización mediante matrices de bajo rango.**
8. **QLoRA combina cuantización con adaptación tipo LoRA.**
9. **Fine-tuning no sustituye automáticamente a RAG, prompting ni herramientas.**
10. **La calidad del dataset y la evaluación son tan importantes como el proceso de entrenamiento.**

---

# 90. La pregunta que debe quedar abierta

Ahora ya podemos comprender:

```text
PRETRAINING
    ↓
aprende patrones generales

FINE-TUNING
    ↓
adapta comportamiento

PROMPT
    ↓
condiciona la inferencia

RAG
    ↓
proporciona contexto externo

TOOLS
    ↓
permiten actuar sobre sistemas externos
```

Pero todavía falta una pieza fundamental.

Un modelo puede ser capaz de seguir instrucciones y aun así no comportarse como queremos.

Entonces surge la pregunta:

> **¿Cómo hacemos que el modelo se comporte de acuerdo con preferencias, restricciones y objetivos humanos?**

Eso nos lleva al siguiente capítulo:

# `09-Instruction-Tuning.md`

Ahí estudiaremos con mayor profundidad cómo los modelos pasan de ser **modelos base capaces de predecir texto** a sistemas capaces de **seguir instrucciones**, y cómo se relacionan SFT, instruction tuning, preference optimization y alignment.
