# 09 — Instruction Tuning: enseñar al modelo a seguir instrucciones

> **Módulo:** Anatomía de un LLM
> **Nivel:** Desde cero → avanzado → Maestría/PhD
> **Prerrequisitos:** Pretraining, Fine-Tuning, Transformer, inferencia, contexto y next-token prediction
> **Conceptos clave:** instruction tuning, instruction following, SFT, supervised fine-tuning, modelo base, modelo instruccional, prompt, respuesta objetivo, dataset de instrucciones, preference optimization, alignment, RLHF, DPO, evaluación y seguridad.

---

# 1. ¿Qué es Instruction Tuning?

**Instruction Tuning** es una forma de entrenamiento posterior al pretraining cuyo objetivo es adaptar un modelo para que pueda **seguir instrucciones de manera útil y consistente**.

La idea básica es:

```text
MODELO BASE
     ↓
EJEMPLOS DE INSTRUCCIONES
     ↓
ENTRENAMIENTO
     ↓
MODELO INSTRUCCIONAL
```

Por ejemplo, queremos que el modelo aprenda patrones como:

```text
"Resume este texto."
        ↓
resumen

"Traduce al inglés."
        ↓
traducción

"Clasifica este documento."
        ↓
clasificación
```

El modelo aprende que determinadas formas de entrada representan **instrucciones** y que debe producir una respuesta acorde con ellas.

---

# 2. ¿Por qué necesitamos Instruction Tuning?

Recordemos cómo se entrenó originalmente un modelo autoregresivo.

Un objetivo típico del pretraining es:

$$
P(x_t|x_{<t})
$$

Es decir:

> Predecir el siguiente token dado el contexto anterior.

Esto puede producir un modelo excelente para continuar texto.

Pero:

**continuar texto ≠ seguir instrucciones.**

---

# 3. Un ejemplo sencillo

Supongamos que tenemos un modelo base.

Le damos:

```text
Explica qué es Python.
```

Un modelo base podría continuar:

```text
Explica qué es Python. Python es un lenguaje...
```

También podría producir una continuación que no tenga la estructura conversacional que esperamos.

Después de Instruction Tuning, esperamos algo más parecido a:

```text
Python es un lenguaje de programación...
```

La diferencia no está necesariamente en que el modelo haya aprendido por primera vez qué es Python.

La diferencia está en que aprendió:

> **cómo responder ante una instrucción.**

---

# 4. Modelo base versus modelo instruccional

Podemos representarlo:

```text
                 PRETRAINING
                      ↓
                 MODELO BASE
                      ↓
              INSTRUCTION TUNING
                      ↓
            MODELO INSTRUCCIONAL
```

El modelo base tiene capacidades lingüísticas generales.

El modelo instruccional añade una capa de comportamiento orientada al seguimiento de instrucciones.

---

# 5. Una analogía

Imagina que alguien ha leído:

* millones de libros;
* manuales;
* artículos;
* código;
* conversaciones;
* documentos.

Tiene una enorme exposición al lenguaje.

Pero ahora queremos que trabaje como asistente.

Le enseñamos ejemplos:

```text
JEFE:
Resume este informe.

ASISTENTE:
[resumen]
```

Otro:

```text
JEFE:
Extrae las fechas.

ASISTENTE:
[fechas]
```

Otro:

```text
JEFE:
Clasifica el riesgo.

ASISTENTE:
[clasificación]
```

La persona ya conocía el lenguaje.

Ahora aprende una convención:

> **Cuando recibo una instrucción, debo ejecutar la tarea solicitada y responder adecuadamente.**

Esta es una analogía de Instruction Tuning.

---

# 6. Instruction Tuning y Fine-Tuning

Instruction Tuning está relacionado directamente con Fine-Tuning.

Una forma útil de verlo es:

```text
FINE-TUNING
│
└── INSTRUCTION TUNING
```

Pero no todo fine-tuning es instruction tuning.

Por ejemplo:

```text
Fine-Tuning:
clasificación especializada
```

puede no ser un sistema general de seguimiento de instrucciones.

En cambio:

```text
Instruction Tuning:
"Clasifica este texto."
"Resume este texto."
"Extrae las fechas."
"Traduce este texto."
```

está explícitamente orientado a seguir instrucciones.

---

# 7. SFT e Instruction Tuning

En muchos pipelines, Instruction Tuning se implementa mediante:

**Supervised Fine-Tuning (SFT)**.

Conceptualmente:

```text
INSTRUCCIÓN
     +
CONTEXTO
     ↓
RESPUESTA ESPERADA
     ↓
LOSS
     ↓
GRADIENTES
     ↓
ACTUALIZACIÓN
```

Por eso:

> **Instruction Tuning puede ser una aplicación de SFT.**

Pero los términos no son idénticos en todos los contextos.

---

# 8. ¿Qué contiene un dataset de instrucciones?

Un ejemplo sencillo:

```json
{
  "instruction": "Explica qué es un Transformer.",
  "input": "",
  "output": "Un Transformer es una arquitectura..."
}
```

Otro:

```json
{
  "instruction": "Resume el siguiente texto.",
  "input": "La inteligencia artificial...",
  "output": "El texto explica..."
}
```

Otro:

```json
{
  "instruction": "Clasifica el riesgo.",
  "input": "Se encontraron transacciones duplicadas.",
  "output": "ALTO"
}
```

La estructura exacta puede variar.

Lo importante es que el dataset representa:

```text
INSTRUCCIÓN
      ↓
TAREA
      ↓
RESPUESTA DESEADA
```

---

# 9. El modelo aprende una relación

Podemos representar el objetivo como:

$$
P_\theta(y|x)
$$

donde:

* \(x\) = instrucción y contexto;
* \(y\) = respuesta esperada;
* \(\theta\) = parámetros del modelo.

Durante entrenamiento buscamos aumentar la probabilidad de respuestas deseadas.

Una pérdida simplificada:

$$
L(\theta)
=
-\sum_t
\log P_\theta(y_t|x,y_{<t})
$$

Esto conecta directamente con el pretraining estudiado anteriormente.

---

# 10. ¿Qué cambia respecto al pretraining?

En pretraining:

```text
texto
 ↓
predecir siguiente token
```

En Instruction Tuning:

```text
instrucción + contexto
 ↓
generar respuesta esperada
```

Matemáticamente pueden parecerse mucho.

Lo que cambia principalmente es:

* los datos;
* la distribución;
* la estructura de los ejemplos;
* el objetivo práctico;
* la conducta que se quiere reforzar.

---

# 11. Un ejemplo de entrenamiento

Dataset:

```text
INSTRUCCIÓN:
Explica qué es un token.

RESPUESTA:
Un token es una unidad de texto...
```

El modelo produce:

```text
Un token es una palabra completa...
```

Se compara con la respuesta objetivo.

La función de pérdida mide la diferencia.

Después:

```text
LOSS
 ↓
BACKPROPAGATION
 ↓
GRADIENTES
 ↓
ACTUALIZACIÓN
```

El proceso se repite con muchos ejemplos.

---

# 12. La importancia del formato

Instruction Tuning no consiste únicamente en tener buenas respuestas.

También importa cómo representamos la interacción.

Podemos tener:

```text
### Instruction:
Resume el texto.

### Input:
[texto]

### Response:
[respuesta]
```

O:

```text
<user>
Resume el texto.
</user>

<assistant>
[respuesta]
</assistant>
```

Los formatos concretos dependen del modelo y del pipeline.

---

# 13. Chat Templates

Los modelos conversacionales suelen utilizar estructuras especiales conocidas como:

**Chat Templates**

Una conversación aparentemente sencilla:

```text
Usuario:
¿Qué es un Transformer?

Asistente:
Es una arquitectura...
```

puede convertirse internamente en una secuencia con tokens especiales que delimitan:

* roles;
* mensajes;
* turnos;
* inicio;
* fin.

Conceptualmente:

```text
SYSTEM
   ↓
USER
   ↓
ASSISTANT
```

Esto es muy importante para Ingeniería de Prompt.

---

# 14. El prompt que ves no siempre es exactamente la secuencia interna

Cuando una interfaz muestra:

```text
Usuario:
Explica attention.
```

el modelo puede recibir internamente una representación estructurada.

Por ejemplo, conceptualmente:

```text
<system>
...
</system>

<user>
Explica attention.
</user>

<assistant>
```

El formato exacto depende del modelo y plataforma.

Esto explica por qué:

> **Un prompt no debe estudiarse únicamente como texto visible; también importa cómo el sistema lo serializa para el modelo.**

---

# 15. Instruction Tuning y roles

Los modelos conversacionales suelen trabajar con roles como:

```text
SYSTEM
USER
ASSISTANT
```

Estos roles no son necesariamente propiedades universales del Transformer.

Son una convención de representación que el sistema puede convertir en tokens o estructuras que el modelo aprende a interpretar.

Por eso debemos distinguir:

```text
INTERFAZ
   ↓
CHAT STRUCTURE
   ↓
TOKENIZACIÓN
   ↓
MODELO
```

---

# 16. ¿Por qué el modelo aprende los roles?

Porque durante el entrenamiento recibe muchos ejemplos donde determinadas estructuras tienen determinados significados.

Por ejemplo:

```text
USER:
Resume.

ASSISTANT:
[resumen]
```

El modelo aprende correlaciones entre:

```text
rol USER
+
instrucción
↓
respuesta ASSISTANT
```

Con suficientes ejemplos, estas convenciones se vuelven parte del comportamiento aprendido.

---

# 17. Instruction Tuning no crea "obediencia absoluta"

Es importante evitar una interpretación incorrecta.

Instruction Tuning no significa:

> "El modelo obedecerá cualquier cosa que escriba el usuario."

El comportamiento final puede estar condicionado por:

* instrucciones del sistema;
* políticas;
* entrenamiento posterior;
* preferencias;
* restricciones;
* herramientas;
* contexto;
* seguridad.

Por eso:

```text
instruction following
≠
obediencia ciega
```

---

# 18. Jerarquía de instrucciones

En sistemas conversacionales puede existir una jerarquía conceptual:

```text
INSTRUCCIONES DEL SISTEMA
          ↓
INSTRUCCIONES DEL USUARIO
          ↓
CONTEXTO / DATOS
          ↓
RESPUESTA
```

La implementación exacta depende de la plataforma.

La idea importante para Ingeniería de Prompt es:

> **No todas las instrucciones que aparecen en el contexto tienen necesariamente la misma autoridad.**

---

# 19. Un ejemplo

Supongamos:

```text
SYSTEM:
Responde en español.

USER:
Responde en inglés.
```

El sistema puede estar diseñado para priorizar la instrucción de mayor autoridad.

Esto no significa que exista una "ley universal" del Transformer que establezca esa jerarquía.

La jerarquía es parte del diseño del sistema y de cómo el modelo fue entrenado para interpretarla.

---

# 20. Instruction Tuning y comportamiento conversacional

El modelo puede aprender patrones como:

```text
Usuario pregunta
      ↓
Asistente responde
      ↓
Usuario aclara
      ↓
Asistente continúa
```

Por eso el modelo puede parecer conversacional.

Pero debemos recordar:

> El modelo sigue realizando predicción de tokens condicionada por el contexto.

No existe necesariamente una entidad interna equivalente a un "asistente humano" consciente.

---

# 21. ¿Cómo se construye un buen dataset?

Un dataset de instrucciones debe tener ejemplos de calidad.

Podemos evaluar:

### Claridad

¿La instrucción es inequívoca?

### Corrección

¿La respuesta es correcta?

### Relevancia

¿La respuesta responde realmente a la tarea?

### Consistencia

¿Los ejemplos siguen criterios similares?

### Diversidad

¿Existen diferentes formas de expresar la misma intención?

### Cobertura

¿Se representan los casos importantes?

---

# 22. Mala instrucción

Ejemplo:

```text
Hazlo bien.
```

¿Qué significa "bien"?

La instrucción es ambigua.

---

# 23. Mejor instrucción

```text
Resume el siguiente documento en cinco puntos.
Cada punto debe tener máximo 20 palabras.
No agregues información que no aparezca en el documento.
```

Ahora tenemos:

* tarea;
* cantidad;
* límite;
* restricción factual.

El dataset puede enseñar al modelo ese comportamiento.

---

# 24. Diversidad de instrucciones

Si todos los ejemplos tienen:

```text
Resume este texto.
```

el modelo puede aprender patrones muy específicos.

Es útil incluir variaciones:

```text
Resume este texto.

Haz un resumen del siguiente documento.

Extrae las ideas principales.

Reduce el texto a cinco puntos.
```

La tarea subyacente es similar.

Esto ayuda a que el comportamiento no dependa exclusivamente de una frase.

---

# 25. Generalización

El objetivo no debería ser memorizar:

```text
"Resume este texto."
```

sino aprender el concepto:

```text
INSTRUCCIÓN DE RESUMEN
```

Así puede responder también a:

```text
"Resume el siguiente informe."
```

Esto es generalización.

---

# 26. Instruction diversity

Podemos representar:

```text
                   MISMA TAREA
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
    "Resume..."    "Sintetiza..."   "Extrae..."
        │              │              │
        └──────────────┼──────────────┘
                       ↓
                  MISMA INTENCIÓN
```

La diversidad lingüística puede ayudar a robustecer el comportamiento.

---

# 27. Multi-task Instruction Tuning

En lugar de entrenar únicamente una tarea, podemos combinar varias:

```text
RESUMEN
TRADUCCIÓN
CLASIFICACIÓN
EXTRACCIÓN
PREGUNTAS
RAZONAMIENTO
CÓDIGO
FORMATO
...
```

Visualmente:

```text
              INSTRUCTION DATASET
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
    resumen      traducción    clasificación
       ↓             ↓             ↓
       └─────────────┼─────────────┘
                     ↓
              MODELO INSTRUCCIONAL
```

Esto puede ayudar a producir un modelo de propósito más general.

---

# 28. Instruction Tuning versus entrenamiento de una sola tarea

Podemos comparar:

### Single-task fine-tuning

```text
Modelo
 ↓
clasificación
```

### Multi-task instruction tuning

```text
Modelo
 ↓
┌───────────────┐
│ instrucciones │
├───────────────┤
│ resumen       │
│ traducción    │
│ clasificación │
│ extracción    │
│ preguntas     │
│ código        │
└───────────────┘
```

El segundo busca un comportamiento más general.

---

# 29. Datos sintéticos

Los datasets de instrucciones pueden contener datos creados o ampliados mediante modelos.

Por ejemplo:

```text
MODELO TEACHER
      ↓
genera instrucciones
      ↓
genera respuestas
      ↓
filtrado / evaluación
      ↓
DATASET
      ↓
MODELO STUDENT
```

Esto se denomina frecuentemente:

**synthetic data**

o datos sintéticos.

---

# 30. ¿Los datos sintéticos son automáticamente buenos?

No.

Un modelo puede generar:

* errores;
* sesgos;
* información falsa;
* respuestas repetitivas;
* patrones artificiales.

Por ello:

```text
datos sintéticos
      ↓
filtrado
      ↓
validación
      ↓
dataset
```

es un enfoque más seguro que utilizar indiscriminadamente todo lo generado.

---

# 31. Distillation e Instruction Tuning

Un modelo grande puede generar ejemplos para ayudar a entrenar un modelo menor.

Conceptualmente:

```text
TEACHER
  ↓
instrucciones + respuestas
  ↓
dataset
  ↓
STUDENT
```

Esto conecta:

* instruction tuning;
* synthetic data;
* knowledge distillation.

No son exactamente la misma técnica, pero pueden combinarse.

---

# 32. Preference Data

Instruction Tuning mediante SFT responde a:

> ¿Cuál es una respuesta correcta o deseada?

Pero existe otra pregunta:

> ¿Cuál de dos respuestas buenas es preferible?

Por ejemplo:

```text
Respuesta A:
larga, correcta, pero poco clara

Respuesta B:
correcta, clara y concisa
```

Podemos registrar:

```text
B > A
```

Esto genera **preference data**.

---

# 33. De SFT a Preference Optimization

Podemos visualizar una evolución:

```text
PRETRAINING
     ↓
MODELO BASE
     ↓
SFT / INSTRUCTION TUNING
     ↓
MODELO QUE SIGUE INSTRUCCIONES
     ↓
PREFERENCE OPTIMIZATION
     ↓
MODELO AJUSTADO A PREFERENCIAS
```

Esta distinción será importante en el siguiente capítulo sobre **Alignment**.

---

# 34. RLHF

Uno de los enfoques más conocidos para incorporar preferencias humanas es:

**RLHF — Reinforcement Learning from Human Feedback**

Un pipeline simplificado:

```text
MODELO
   ↓
genera respuestas
   ↓
humanos comparan
   ↓
preferencias
   ↓
REWARD MODEL
   ↓
optimización
   ↓
MODELO ACTUALIZADO
```

La implementación real puede incluir varias etapas adicionales.

---

# 35. DPO

Otra técnica ampliamente estudiada es:

**DPO — Direct Preference Optimization**

En términos conceptuales:

```text
PROMPT
  │
  ├── respuesta preferida
  │
  └── respuesta rechazada
          ↓
      optimización
```

El método intenta ajustar directamente el modelo utilizando datos de preferencias.

Esto evita algunos componentes de pipelines RLHF tradicionales.

---

# 36. Instruction Tuning versus Preference Optimization

No son lo mismo.

### Instruction Tuning

Pregunta:

> ¿Qué respuesta debería producir el modelo para esta instrucción?

### Preference Optimization

Pregunta:

> Entre estas respuestas, ¿cuál comportamiento preferimos?

Visualmente:

```text
INSTRUCTION TUNING

prompt
 ↓
respuesta objetivo


PREFERENCE OPTIMIZATION

prompt
 ↓
respuesta A ←→ respuesta B
              ↓
          preferencia
```

---

# 37. Alignment

El concepto de:

**Alignment**

es más amplio.

Busca hacer que el comportamiento de un sistema de IA sea compatible con determinados objetivos, valores, restricciones o preferencias definidos por quienes diseñan y utilizan el sistema.

Puede involucrar:

* SFT;
* instruction tuning;
* preference optimization;
* RLHF;
* DPO;
* reglas;
* evaluaciones;
* filtros;
* herramientas;
* arquitectura;
* políticas de seguridad.

Por tanto:

> **Instruction Tuning puede formar parte del alignment, pero no es sinónimo de alignment.**

---

# 38. Una arquitectura conceptual completa

Podemos representarla así:

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
          PREFERENCE OPTIMIZATION
                      ↓
              MODELO POST-TRAINED
                      ↓
                  INFERENCIA
```

No todos los modelos siguen exactamente esta secuencia.

Es un modelo mental.

---

# 39. ¿Qué aprende realmente el modelo?

No aprende simplemente:

```text
"Si veo esta frase, responde exactamente esto."
```

Idealmente aprende patrones más generales:

```text
INSTRUCCIÓN
      ↓
IDENTIFICAR TAREA
      ↓
UTILIZAR CONTEXTO
      ↓
PRODUCIR RESPUESTA
```

La calidad de esa generalización depende del dataset, arquitectura y entrenamiento.

---

# 40. Instruction Following

Podemos definir **instruction following** como la capacidad del modelo de interpretar y ejecutar instrucciones expresadas en lenguaje natural.

Por ejemplo:

```text
"Extrae todas las fechas."

→ detectar fechas


"Resume el documento."

→ resumir


"Clasifica el riesgo."

→ clasificar
```

No basta con generar lenguaje gramatical.

El modelo debe producir una respuesta acorde con la tarea.

---

# 41. Instruction Following y prompt engineering

Aquí aparece una conexión fundamental para este repositorio.

Un modelo instruccional cambia la relación:

```text
ANTES

prompt
 ↓
continuación de texto
```

hacia algo más parecido a:

```text
DESPUÉS

instrucción
 ↓
interpretación de tarea
 ↓
respuesta
```

Esto hace que la Ingeniería de Prompt sea mucho más efectiva.

Pero:

> Un buen prompt no reemplaza las limitaciones del modelo.

---

# 42. Por qué dos modelos responden diferente al mismo prompt

Supongamos:

```text
PROMPT:

Resume este texto en tres puntos.
```

Modelo A:

```text
3 puntos
```

Modelo B:

```text
un párrafo largo
```

¿Por qué?

Porque:

```text
Modelo A
→ parámetros + entrenamiento + instruction tuning A

Modelo B
→ parámetros + entrenamiento + instruction tuning B
```

El prompt es el mismo.

El sistema aprendido es diferente.

Esto es fundamental para comprender por qué **prompt engineering no puede separarse completamente de la arquitectura y entrenamiento del modelo**.

---

# 43. Instruction Tuning y personalidad

Los datasets pueden reforzar determinados estilos:

```text
formal
técnico
conciso
amigable
académico
```

Pero la "personalidad" del modelo no necesariamente está almacenada en una única parte.

Puede emerger de una combinación de:

* pretraining;
* instruction tuning;
* preference optimization;
* system prompts;
* decoding;
* políticas del sistema.

---

# 44. System Prompt versus Instruction Tuning

Otra distinción importante:

### System prompt

Instrucción proporcionada durante inferencia.

```text
"Responde como un tutor de programación."
```

### Instruction tuning

Entrenamiento realizado antes de la inferencia.

```text
muchos ejemplos
→ actualización de parámetros
```

Por tanto:

```text
SYSTEM PROMPT
→ comportamiento durante esta interacción


INSTRUCTION TUNING
→ comportamiento aprendido en el modelo
```

---

# 45. ¿Cuál es más poderoso?

No existe una respuesta universal.

Depende de:

* modelo;
* tarea;
* dataset;
* calidad del prompt;
* cantidad de ejemplos;
* consistencia requerida;
* costos;
* latencia;
* infraestructura.

Una arquitectura profesional debe evaluar ambas estrategias.

---

# 46. Instruction Tuning y contexto

Un modelo instruccional puede recibir:

```text
INSTRUCCIÓN
+
CONTEXTO
+
EJEMPLOS
```

y producir:

```text
RESPUESTA
```

Esto conecta con:

**in-context learning**.

---

# 47. In-Context Learning versus Instruction Tuning

Son mecanismos diferentes.

### Instruction Tuning

El comportamiento se modifica mediante entrenamiento.

```text
datos
 ↓
loss
 ↓
parámetros
```

### In-Context Learning

El modelo recibe ejemplos durante la inferencia.

```text
prompt
 ↓
ejemplos
 ↓
modelo
 ↓
respuesta
```

Los parámetros no necesitan cambiar.

---

# 48. Few-Shot Prompting versus Instruction Tuning

Supongamos:

```text
Ejemplo 1:
Pregunta → respuesta

Ejemplo 2:
Pregunta → respuesta

Nueva pregunta:
→ ?
```

Esto es **few-shot prompting**.

Si esos ejemplos se utilizan para entrenar los parámetros:

```text
dataset
 ↓
training
 ↓
parámetros modificados
```

es fine-tuning.

La diferencia principal es:

```text
FEW-SHOT
→ información temporal en el contexto

FINE-TUNING
→ adaptación en los parámetros
```

---

# 49. Instruction Tuning y seguridad

El instruction tuning puede reforzar comportamientos seguros.

Por ejemplo, datasets pueden incluir casos donde el modelo debe:

* rechazar determinadas solicitudes;
* pedir aclaraciones;
* no inventar información;
* proteger información sensible;
* seguir restricciones.

Pero:

> Instruction Tuning por sí solo no garantiza seguridad.

La seguridad de un sistema requiere múltiples capas.

---

# 50. Defense in Depth

Una arquitectura robusta puede utilizar:

```text
               SEGURIDAD
                  │
       ┌──────────┼──────────┐
       ↓          ↓          ↓
   entrenamiento prompt   runtime
       │          │          │
       ↓          ↓          ↓
    SFT       políticas    validación
       │          │          │
       └──────────┼──────────┘
                  ↓
              MONITOREO
```

Esto se denomina conceptualmente:

**defense in depth**.

No debemos confiar en una sola capa.

---

# 51. Prompt Injection y Instruction Tuning

Supongamos que un modelo está entrenado para:

```text
seguir instrucciones del sistema
```

y recibe un documento:

```text
IGNORA TODAS LAS INSTRUCCIONES ANTERIORES...
```

El modelo puede interpretar ese texto como una instrucción si el sistema no establece correctamente los límites entre:

* instrucciones;
* datos;
* contenido recuperado;
* herramientas.

Por eso Instruction Tuning no elimina prompt injection.

La seguridad depende también de la arquitectura del sistema.

---

# 52. Instruction Tuning y datos no confiables

En sistemas RAG:

```text
DOCUMENTO EXTERNO
        ↓
RAG
        ↓
CONTEXTO
        ↓
MODELO
```

El modelo puede recibir instrucciones maliciosas dentro del documento.

Por eso una arquitectura segura debe distinguir:

```text
INSTRUCCIÓN
≠
DATOS
```

Esta distinción será fundamental cuando estudiemos Context Engineering y agentes.

---

# 53. Evaluación de Instruction Following

Podemos evaluar si el modelo realmente sigue instrucciones.

Ejemplo:

```text
Instrucción:

Devuelve exactamente 3 elementos JSON.
```

Evaluamos:

```text
¿devolvió JSON?
¿son exactamente 3?
¿son válidos?
¿cumplen el esquema?
```

Esto es más preciso que simplemente preguntar:

> "¿La respuesta parece buena?"

---

# 54. Instruction Following Benchmark

Un benchmark puede contener:

```text
Tarea A
Tarea B
Tarea C
Tarea D
...
```

y medir:

```text
cumplimiento
exactitud
formato
restricciones
robustez
```

La evaluación debe utilizar ejemplos que no hayan sido utilizados durante entrenamiento.

---

# 55. Robustez

Un modelo puede seguir una instrucción:

```text
Resume el documento.
```

pero fallar ante:

```text
Resume el siguiente documento en exactamente tres frases, sin introducir información externa y manteniendo los nombres propios.
```

Por eso necesitamos evaluar:

> ¿Sigue instrucciones simples o puede manejar restricciones múltiples?

---

# 56. Compositional Instruction Following

Las instrucciones reales suelen combinar tareas.

Por ejemplo:

```text
Lee el documento,
extrae las fechas,
ordénalas cronológicamente,
y devuelve un JSON.
```

Aquí tenemos:

```text
leer
 +
extraer
 +
ordenar
 +
estructurar
```

Esto es más complejo que una instrucción aislada.

---

# 57. Instruction Hierarchy

En sistemas complejos pueden coexistir:

```text
POLÍTICA DEL SISTEMA
        ↓
INSTRUCCIÓN DE LA APLICACIÓN
        ↓
INSTRUCCIÓN DEL USUARIO
        ↓
DATOS EXTERNOS
```

Una arquitectura segura debe definir qué información tiene autoridad para modificar el comportamiento.

Este concepto será especialmente importante en:

* agentes;
* RAG;
* herramientas;
* sistemas multiusuario.

---

# 58. El modelo no "entiende autoridad" de manera mágica

La jerarquía debe ser implementada mediante:

* formato;
* entrenamiento;
* arquitectura;
* prompts;
* políticas;
* validaciones;
* controles externos.

El modelo aprende patrones de prioridad.

Pero la seguridad crítica no debería depender exclusivamente de una inferencia probabilística.

---

# 59. Instruction Tuning y herramientas

Los modelos pueden ser entrenados con ejemplos donde una instrucción requiere una herramienta.

Por ejemplo:

```text
USER:
¿Cuánto es 125 × 843?

MODEL:
[llamada a calculadora]

TOOL:
105375

MODEL:
El resultado es 105.375.
```

El entrenamiento puede reforzar patrones de uso de herramientas.

Pero la herramienta real sigue siendo un componente externo.

---

# 60. Function Calling

Una arquitectura puede entrenar o configurar al modelo para producir estructuras como:

```json id="8y5u4k"
{
  "name": "buscar_cliente",
  "arguments": {
    "id": "12345"
  }
}
```

Después un sistema externo ejecuta:

```text
LLM
 ↓
tool call
 ↓
backend
 ↓
resultado
 ↓
LLM
```

Esto demuestra nuevamente:

> El modelo y el sistema que lo rodea no son la misma cosa.

---

# 61. Instruction Tuning no es "programar el modelo"

Una analogía peligrosa sería:

> "Le programamos reglas nuevas."

No exactamente.

Estamos entrenando una red neuronal para aumentar la probabilidad de determinados comportamientos.

No estamos necesariamente agregando:

```text
if instruction == "resume":
    ejecutar función RESUMIR()
```

El modelo aprende patrones distribuidos.

---

# 62. Una visión probabilística

Podemos expresar el comportamiento como:

$$
P_\theta(y|x)
$$

donde:

* \(x\) = instrucción/contexto;
* \(y\) = respuesta;
* \(\theta\) = parámetros.

Instruction Tuning intenta modificar:

$$
\theta
$$

para que:

$$
P_\theta(y_{desired}|x)
$$

aumente para respuestas deseadas.

Esta es una manera matemáticamente más precisa de pensar:

> "El modelo aprende a seguir instrucciones."

---

# 63. ¿Por qué un modelo puede seguir una instrucción nunca vista exactamente?

Porque no debería memorizar cada instrucción literalmente.

Supongamos entrenamiento:

```text
"Resume el texto."

"Sintetiza el documento."

"Extrae las ideas principales."
```

Después aparece:

```text
"Condensa este informe."
```

Si el modelo generaliza correctamente, reconoce la relación semántica.

Esto es posible gracias a las representaciones aprendidas.

---

# 64. El papel de los embeddings

Recordemos:

```text
texto
 ↓
tokens
 ↓
embeddings
 ↓
representaciones
```

Las palabras y expresiones relacionadas pueden producir representaciones relacionadas.

Pero el comportamiento final depende de todo el Transformer, no únicamente del embedding inicial.

Esto conecta con los capítulos anteriores.

---

# 65. Instruction Tuning y arquitectura Transformer

Podemos conectar todo:

```text
INSTRUCCIÓN
    ↓
TOKENIZACIÓN
    ↓
EMBEDDINGS
    ↓
POSICIÓN
    ↓
ATTENTION
    ↓
TRANSFORMER
    ↓
REPRESENTACIÓN
    ↓
LOGITS
    ↓
PROBABILIDADES
    ↓
RESPUESTA
```

Instruction Tuning modifica los parámetros utilizados en esta cadena.

Durante inferencia:

```text
PROMPT
 ↓
modelo instruccional
 ↓
generación
```

---

# 66. Una distinción crítica para Ingeniería de Prompt

Podemos pensar en tres niveles:

### Nivel 1 — Prompt

```text
¿Qué le digo?
```

### Nivel 2 — Modelo

```text
¿Qué aprendió?
```

### Nivel 3 — Sistema

```text
¿Qué herramientas, contexto y restricciones tiene?
```

Instruction Tuning pertenece principalmente al:

```text
NIVEL DEL MODELO
```

Mientras que Prompt Engineering trabaja principalmente con:

```text
NIVEL DE ENTRADA / CONTEXTO
```

Y la ingeniería de sistemas trabaja con:

```text
MODELO + CONTEXTO + TOOLS + DATOS + POLÍTICAS
```

---

# 67. Una visión de sistema completo

```text
                       USUARIO
                          ↓
                        PROMPT
                          ↓
                 ┌────────────────┐
                 │ MODELO         │
                 │ INSTRUCCIONAL  │
                 └───────┬────────┘
                         ↓
                 CONTEXTO / RAG
                         ↓
                     TOOLS
                         ↓
                     RESPUESTA
```

El modelo instruccional es solamente uno de los componentes.

---

# 68. Errores conceptuales frecuentes

## Error 1

> "Pretraining e Instruction Tuning son exactamente lo mismo."

No.

Pretraining busca aprender patrones generales a gran escala.

Instruction Tuning busca adaptar el comportamiento para seguir instrucciones.

---

## Error 2

> "Instruction Tuning significa que el modelo entiende cualquier instrucción."

No.

La capacidad depende del entrenamiento, datos, arquitectura y contexto.

---

## Error 3

> "El system prompt es lo mismo que Instruction Tuning."

No.

Uno ocurre durante inferencia.

El otro durante entrenamiento.

---

## Error 4

> "RAG es una forma de Instruction Tuning."

No.

RAG proporciona contexto externo durante inferencia.

---

## Error 5

> "Si el modelo sigue instrucciones, no necesita prompts."

No.

El prompt sigue siendo el mecanismo mediante el cual se especifica la tarea durante inferencia.

---

# 69. Tabla comparativa

| Concepto                | Momento                 | Modifica parámetros | Objetivo principal                   |
| ----------------------- | ----------------------- | ------------------: | ------------------------------------ |
| Pretraining             | Entrenamiento inicial   |                  Sí | Aprender patrones generales          |
| Fine-Tuning             | Entrenamiento posterior |   Sí / parcialmente | Adaptar comportamiento               |
| Instruction Tuning      | Entrenamiento posterior |   Sí / parcialmente | Seguir instrucciones                 |
| SFT                     | Método de entrenamiento |   Sí / parcialmente | Aprender de ejemplos supervisados    |
| Few-shot                | Inferencia              |                  No | Mostrar ejemplos en contexto         |
| Prompting               | Inferencia              |                  No | Condicionar la respuesta             |
| RAG                     | Inferencia              |      Normalmente no | Incorporar información externa       |
| Preference Optimization | Post-training           |                  Sí | Favorecer respuestas preferidas      |
| RLHF                    | Post-training           |                  Sí | Optimizar respecto a feedback humano |
| DPO                     | Post-training           |                  Sí | Optimizar directamente preferencias  |

---

# 70. Flujo histórico/conceptual

Una forma útil de entender la evolución es:

```text
MODELO BASE
     │
     │ "Aprende lenguaje"
     ↓
INSTRUCTION TUNING
     │
     │ "Aprende a seguir instrucciones"
     ↓
PREFERENCE OPTIMIZATION
     │
     │ "Aprende qué respuestas preferimos"
     ↓
SISTEMA DE IA
     │
     ├── Prompt
     ├── RAG
     ├── Tools
     ├── Memory
     └── Guardrails
```

Esta arquitectura explica por qué un LLM moderno es mucho más que un simple modelo de next-token prediction aislado.

---

# 71. Nivel Maestría: Instruction Tuning como distribución

Podemos formalizar el dataset de instrucciones como:

$$
D =
\{(x_i,y_i)\}_{i=1}^{N}
$$

donde:

$$
x_i =
[\text{instruction},\text{context}]
$$

y:

$$
y_i =
\text{desired response}
$$

El objetivo es:

$$
\min_\theta
\frac{1}{N}
\sum_i
-\log P_\theta(y_i|x_i)
$$

El modelo aprende una nueva distribución condicional.

Conceptualmente:

```text
ANTES

P_base(y | x)


DESPUÉS

P_instruction(y | x)
```

No estamos necesariamente creando una nueva arquitectura.

Estamos modificando los parámetros para alterar la distribución de respuestas.

---

# 72. ¿Qué significa "seguir una instrucción"?

Desde una perspectiva probabilística:

```text
instrucción
    ↓
representación
    ↓
distribución de posibles respuestas
    ↓
decodificación
    ↓
respuesta
```

Instruction Tuning modifica la distribución para aumentar la probabilidad de respuestas que cumplen la tarea.

Esto conecta directamente:

```text
PROMPT
→ CONTEXTO
→ REPRESENTACIÓN
→ DISTRIBUCIÓN
→ GENERACIÓN
```

con:

```text
TRAINING
→ CAMBIO DE PARÁMETROS
→ NUEVA DISTRIBUCIÓN
```

---

# 73. Una pregunta avanzada: ¿dónde está almacenada la instrucción?

No existe necesariamente un lugar único:

```text
"INSTRUCTION FOLLOWING"
```

almacenado como una regla.

El comportamiento está distribuido entre parámetros y representaciones.

Esto conecta con:

* distributed representations;
* superposition;
* mechanistic interpretability;
* feature learning.

---

# 74. Investigación: Mechanistic Interpretability

Una línea de investigación intenta descubrir:

> ¿Qué mecanismos internos permiten al modelo seguir instrucciones?

Los investigadores estudian:

* activaciones;
* circuitos;
* atención;
* features;
* representaciones;
* causal interventions;
* sparse autoencoders;
* circuitos especializados.

El objetivo es pasar de:

```text
"El modelo hace X."
```

a:

```text
"¿Qué mecanismos internos causan X?"
```

---

# 75. Instruction Tuning y Sparse Autoencoders

En interpretabilidad moderna se investigan técnicas como:

**Sparse Autoencoders (SAEs)**

para intentar descomponer representaciones densas en features más interpretables.

Conceptualmente:

```text
ACTIVACIÓN DEL MODELO
        ↓
Sparse Autoencoder
        ↓
FEATURES
        ↓
interpretación
```

Esto sigue siendo un área de investigación activa.

No debe interpretarse como que ya tenemos una explicación completa del funcionamiento interno de los LLM.

---

# 76. Instruction Tuning y causalidad

Una correlación entre una activación y una conducta no demuestra que esa activación cause la conducta.

Por eso la investigación avanzada utiliza intervenciones:

```text
observar activación
       ↓
modificar / intervenir
       ↓
observar resultado
```

Esto permite investigar relaciones causales dentro del modelo.

---

# 77. Instruction Tuning y generalización composicional

Una pregunta de investigación importante es:

> ¿Puede el modelo combinar instrucciones conocidas para resolver una combinación nueva?

Ejemplo:

Durante entrenamiento:

```text
resumir
clasificar
```

Después:

```text
resume y clasifica
```

La capacidad de combinar habilidades es un aspecto importante de la generalización.

---

# 78. Instruction Following no es razonamiento garantizado

Un modelo puede producir:

```text
"Primero hacemos A.
Luego B.
Finalmente C."
```

sin que eso garantice que el razonamiento interno sea equivalente al proceso descrito.

Por eso debemos distinguir:

```text
explicación generada
≠
prueba de proceso interno
```

Este punto será especialmente importante cuando estudiemos razonamiento y modelos de razonamiento.

---

# 79. Instruction Tuning y modelos de razonamiento

Los modelos modernos pueden recibir entrenamiento adicional orientado a tareas de razonamiento.

Conceptualmente:

```text
MODELO BASE
     ↓
INSTRUCTION TUNING
     ↓
REASONING / POST-TRAINING
     ↓
MODELO ESPECIALIZADO
```

La forma exacta de entrenamiento varía significativamente entre familias de modelos.

No todos los modelos de razonamiento utilizan el mismo pipeline.

---

# 80. El principio fundamental

Después de estudiar:

* pretraining;
* fine-tuning;
* instruction tuning;

podemos formular una idea central:

> **El comportamiento de un LLM es el resultado de su entrenamiento más su contexto de inferencia.**

Una formulación conceptual:

$$
Respuesta =
f(
Modelo,
Prompt,
Contexto,
Decodificación,
Herramientas
)
$$

Por tanto:

```text
MISMO PROMPT
+
MODELO DIFERENTE
=
POSIBLEMENTE RESPUESTA DIFERENTE
```

y:

```text
MISMO MODELO
+
PROMPT DIFERENTE
=
POSIBLEMENTE RESPUESTA DIFERENTE
```

---

# 81. Mapa conceptual final

```text
                         LLM
                          │
                    PRETRAINING
                          │
                          ↓
                     MODELO BASE
                          │
                 ┌────────┴────────┐
                 ↓                 ↓
          FINE-TUNING          OTROS MÉTODOS
                 │
                 ↓
        INSTRUCTION TUNING
                 │
                 ↓
       MODELO INSTRUCCIONAL
                 │
        ┌────────┴─────────┐
        ↓                  ↓
       SFT          PREFERENCE OPT.
                           │
                    ┌──────┴──────┐
                    ↓             ↓
                   DPO           RLHF
                    │             │
                    └──────┬──────┘
                           ↓
                    POST-TRAINING
                           │
                           ↓
                       INFERENCIA
                           │
              ┌────────────┼────────────┐
              ↓            ↓            ↓
            PROMPT        RAG          TOOLS
              │            │            │
              └────────────┼────────────┘
                           ↓
                       RESPUESTA
```

---

# 82. Las 12 ideas esenciales

1. **Un modelo base puede predecir lenguaje sin ser un asistente instruccional.**
2. **Instruction Tuning adapta el modelo para seguir instrucciones.**
3. **SFT es una técnica común para realizar Instruction Tuning.**
4. **El dataset contiene instrucciones, contextos y respuestas deseadas.**
5. **La calidad y diversidad del dataset son fundamentales.**
6. **Instruction Tuning modifica parámetros; un prompt normal no.**
7. **Few-shot prompting modifica el contexto, no los parámetros.**
8. **Preference Optimization es diferente de Instruction Tuning.**
9. **RLHF y DPO son métodos relacionados con aprendizaje de preferencias.**
10. **Alignment es un concepto más amplio que Instruction Tuning.**
11. **Instruction Tuning no garantiza seguridad, factualidad ni obediencia absoluta.**
12. **El comportamiento final depende del modelo, entrenamiento, prompt, contexto, decodificación y sistema que lo rodea.**

---

# 83. Conexión con Ingeniería de Prompt

Ahora podemos entender mejor una de las ideas centrales de este repositorio:

```text
PRETRAINING
    ↓
¿Qué capacidades generales aprendió?

INSTRUCTION TUNING
    ↓
¿Cómo aprendió a interpretar instrucciones?

FINE-TUNING
    ↓
¿Cómo fue especializado?

PROMPT
    ↓
¿Qué tarea le estoy solicitando ahora?

CONTEXTO
    ↓
¿Qué información tiene disponible?

DECODIFICACIÓN
    ↓
¿Cómo selecciona los tokens?

TOOLS / RAG
    ↓
¿Qué capacidades externas tiene?
```

Por eso estudiar Prompt Engineering sin conocer el modelo que recibe el prompt deja fuera una parte importante del sistema.

---

# 84. Puente hacia el siguiente capítulo

Hasta ahora tenemos:

```text
PRETRAINING
    ↓
MODELO BASE
    ↓
FINE-TUNING
    ↓
INSTRUCTION TUNING
    ↓
MODELO INSTRUCCIONAL
```

Pero todavía falta una pregunta:

> **¿Cómo conseguimos que las respuestas del modelo estén alineadas con determinadas preferencias, restricciones y objetivos humanos?**

Esto nos lleva al siguiente archivo:

# `10-Alignment.md`

Ahí estudiaremos:

* qué significa alignment;
* RLHF;
* reward models;
* preference data;
* DPO;
* otros métodos de post-training;
* seguridad;
* helpfulness;
* harmlessness;
* trade-offs;
* límites del alignment;
* problemas de specification gaming;
* reward hacking;
* Goodhart's Law;
* evaluación de alineamiento;
* interpretabilidad;
* alignment tax;
* y la diferencia entre **alinear el modelo** y **asegurar el sistema completo**.
