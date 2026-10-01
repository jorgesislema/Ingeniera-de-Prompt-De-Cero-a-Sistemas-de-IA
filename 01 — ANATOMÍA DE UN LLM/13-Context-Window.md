# 13 — Context Window

> **El contexto es la información que el modelo puede recibir y utilizar durante una ejecución. La ventana de contexto (context window) define cuánto de esa información puede procesarse como una sola entrada al modelo.**

---

## 1. ¿Qué es una Context Window?

Una **ventana de contexto** es el límite de información que un modelo puede procesar dentro de una determinada ejecución.

Cuando interactuamos con un LLM, el modelo no recibe únicamente nuestra última pregunta.

Puede recibir:

* instrucciones del sistema;
* instrucciones del usuario;
* mensajes anteriores;
* documentos;
* resultados de búsquedas;
* resultados de herramientas;
* ejemplos;
* datos estructurados;
* información recuperada mediante RAG;
* contenido generado anteriormente;
* instrucciones adicionales del sistema que ejecuta la aplicación.

Todo ese material forma parte del **contexto** disponible para esa inferencia.

Conceptualmente:

```text
┌────────────────────────────────────────────┐
│              CONTEXT WINDOW                │
│                                            │
│ System instructions                       │
│ Conversation history                       │
│ User prompt                                │
│ Examples                                   │
│ Documents                                  │
│ Retrieved information                      │
│ Tool results                               │
│ Other contextual data                      │
│                                            │
└────────────────────────────────────────────┘
                    │
                    ▼
                 MODEL
                    │
                    ▼
                 OUTPUT
```

La idea fundamental es:

> **El modelo solo puede razonar sobre la información que recibe en el contexto de esa ejecución.**

---

# 2. Contexto no significa memoria

Uno de los errores más comunes al estudiar LLM es confundir:

* contexto;
* memoria;
* conocimiento del modelo;
* almacenamiento externo.

Son conceptos diferentes.

### Contexto

Información proporcionada al modelo durante una ejecución.

### Memoria

Información que un sistema conserva y puede recuperar posteriormente.

### Conocimiento paramétrico

Información aprendida durante el entrenamiento y almacenada de manera distribuida en los parámetros del modelo.

### Almacenamiento externo

Información ubicada fuera del modelo:

* bases de datos;
* archivos;
* vector databases;
* sistemas empresariales;
* APIs;
* sistemas de memoria.

Podemos representarlo así:

```text
                  SISTEMA DE IA
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
   Parámetros       Memoria        Contexto
        │              │              │
        │              │              │
 conocimiento     información     información
 aprendido         almacenada      disponible
        │              │              │
        └──────────────┴──────────────┘
                       │
                       ▼
                    MODELO
                       │
                       ▼
                   RESPUESTA
```

---

# 3. El contexto está compuesto por tokens

Los modelos de lenguaje no procesan directamente conceptos como:

> "El usuario quiere analizar las ventas de una empresa."

Primero procesan representaciones derivadas de tokens.

Por ejemplo:

```text
"El usuario quiere analizar las ventas."
                 │
                 ▼
             Tokenización
                 │
                 ▼
        [tokens / IDs]
                 │
                 ▼
             embeddings
                 │
                 ▼
          Transformer
```

Por esta razón, la capacidad de contexto normalmente se expresa en **tokens**, no en número de palabras.

---

# 4. Tokens ≠ palabras

Una palabra puede corresponder a:

* un token;
* varios tokens;
* fragmentos de palabras;
* espacios o signos combinados con texto;
* unidades específicas según el tokenizador.

Por ejemplo, conceptualmente:

```text
"inteligencia artificial"
          ↓
     varios tokens
```

Por eso no existe una conversión universal exacta:

```text
1 token = 1 palabra
```

La relación depende del:

* idioma;
* texto;
* tokenizador;
* modelo;
* contenido.

---

# 5. ¿Qué significa que un modelo tenga una ventana de 128K?

Supongamos un modelo con una ventana de contexto de:

```text
128 000 tokens
```

Eso significa, conceptualmente, que una ejecución puede trabajar con hasta aproximadamente:

```text
128 000 tokens
```

dentro de su límite de contexto.

No significa:

```text
128 000 palabras
```

ni:

```text
128 000 caracteres
```

ni tampoco:

```text
128 000 tokens de entrada + infinitos tokens de salida
```

Dependiendo de la arquitectura y de la API, el límite se considera sobre una combinación de **tokens de entrada y salida**.

Conceptualmente:

```text
Context Window
│
├── Input tokens
│
└── Output tokens
```

Por ejemplo:

```text
Ventana = 128K

Entrada
████████████████████████████████ 100K

Salida máxima disponible
████████ 28K
```

La distribución exacta depende de las reglas del modelo y de su API.

---

# 6. Context window como presupuesto

Una forma útil de pensar la ventana de contexto es como un **presupuesto limitado**.

Supongamos:

```text
Límite = 32 000 tokens
```

La aplicación podría consumir:

```text
System prompt       2 000
Historial           8 000
Usuario             1 000
Documentos         15 000
Herramientas        3 000
                    ------
Total               29 000
```

Quedarían aproximadamente:

```text
3 000 tokens
```

para completar la ejecución dentro de ese presupuesto, dependiendo de las reglas específicas del modelo.

Por eso:

> **El contexto no es gratuito ni infinito.**

---

# 7. ¿Qué ocurre cuando el contexto crece?

Supongamos una conversación:

```text
Mensaje 1
Mensaje 2
Mensaje 3
Mensaje 4
...
Mensaje 100
```

Si el sistema envía toda la conversación al modelo cada vez:

```text
Solicitud 1 → pequeño contexto

Solicitud 20 → contexto mayor

Solicitud 50 → contexto mucho mayor

Solicitud 100 → contexto potencialmente enorme
```

Esto produce dos problemas:

### Problema 1 — límite de contexto

Eventualmente puede superarse la capacidad máxima.

### Problema 2 — costo computacional

Procesar más tokens normalmente requiere más recursos.

Por eso los sistemas reales deben administrar cuidadosamente el contexto.

---

# 8. Context window no es simplemente "memoria RAM"

La analogía:

> "La ventana de contexto es la memoria RAM del modelo."

puede ser útil inicialmente, pero técnicamente es incompleta.

La context window representa principalmente la cantidad de información tokenizada que puede participar en una determinada ejecución.

Durante el procesamiento aparecen estructuras internas como:

* embeddings;
* activaciones;
* matrices Q, K y V;
* KV cache;
* estados intermedios.

Por lo tanto:

```text
Context Window
       ≠
Cantidad de RAM
```

La relación existe, pero no son equivalentes.

---

# 9. Contexto y Transformer

El contexto está directamente relacionado con la arquitectura Transformer.

Supongamos:

```text
Tokens:

t1 t2 t3 t4 t5
```

Cada token se transforma en una representación.

Después, mediante mecanismos de atención, el modelo puede relacionar diferentes posiciones.

Conceptualmente:

```text
t1 ─────┐
        │
t2 ─────┤
        │
t3 ─────┼──► Attention
        │
t4 ─────┤
        │
t5 ─────┘
```

La atención permite que la representación de un token incorpore información procedente de otros tokens del contexto.

Por eso el contexto es fundamental para el comportamiento de un Transformer.

---

# 10. Atención y contexto

Recordemos la idea básica de atención:

$$
Attention(Q,K,V)
=
softmax
\left(
\frac{QK^T}{\sqrt{d_k}}
\right)V
$$

De forma intuitiva:

```text
Query
  │
  ▼
¿Qué información necesito?
  │
  ▼
Keys
  │
  ▼
¿Qué información parece relevante?
  │
  ▼
Values
  │
  ▼
Información utilizada
```

Cuando aumenta el contexto:

```text
pocos tokens
     ↓
pocas relaciones

muchos tokens
     ↓
muchas relaciones posibles
```

Por eso ampliar la ventana de contexto no es simplemente "hacer una caja más grande".

Tiene consecuencias arquitectónicas y computacionales.

---

# 11. Contexto y complejidad de atención

En el Transformer clásico, la atención completa tiene una complejidad aproximada:

$$
O(n^2)
$$

donde:

$$
n = \text{número de tokens}
$$

Esto significa que si aumentamos considerablemente el número de tokens, las interacciones potenciales crecen rápidamente.

Por ejemplo:

```text
n = 1 000
n² = 1 000 000

n = 10 000
n² = 100 000 000
```

El crecimiento es cuadrático.

Esto explica por qué trabajar con contextos extremadamente largos representa desafíos de ingeniería.

---

# 12. ¿Entonces todos los modelos modernos tienen exactamente ese costo?

No necesariamente.

La investigación y la ingeniería han desarrollado diferentes estrategias para reducir el costo del procesamiento de secuencias largas.

Entre ellas aparecen:

* atención eficiente;
* atención local;
* atención dispersa (sparse attention);
* arquitecturas híbridas;
* mecanismos recurrentes;
* compresión de contexto;
* recuperación selectiva;
* memoria externa;
* técnicas específicas de inferencia.

Por lo tanto:

> **La ventana de contexto y el mecanismo utilizado para procesarla son problemas relacionados, pero no idénticos.**

---

# 13. Contexto de entrada y salida

Una ejecución de un LLM puede verse conceptualmente así:

```text
INPUT
  │
  ├── System
  ├── History
  ├── User
  ├── Documents
  └── Tools
  │
  ▼
MODEL
  │
  ▼
OUTPUT
  │
  └── Generated tokens
```

En sistemas autoregresivos, la salida también se genera token por token.

```text
Contexto inicial
      ↓
Predice token 1
      ↓
Predice token 2
      ↓
Predice token 3
      ↓
...
```

Los tokens generados pasan a formar parte de la secuencia que condiciona los siguientes tokens.

---

# 14. Prefill y Decode

En sistemas modernos de inferencia suele ser útil separar dos fases:

```text
                 INFERENCIA
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
       PREFILL                DECODE
```

## Prefill

El modelo procesa el contexto inicial.

Por ejemplo:

```text
System
+
Historial
+
Documentos
+
Pregunta
```

Todo eso se procesa para preparar la generación.

## Decode

Después comienza la generación autoregresiva:

```text
token 1
   ↓
token 2
   ↓
token 3
   ↓
token 4
```

Esta distinción es importante para comprender:

* latencia;
* costo;
* throughput;
* KV cache;
* optimización de inferencia.

---

# 15. KV Cache

Durante la generación autoregresiva, volver a calcular todo el contexto desde cero sería muy ineficiente.

Los Transformers utilizan normalmente una estructura conocida como:

> **KV cache**

KV significa:

```text
K = Keys
V = Values
```

Conceptualmente:

```text
Contexto inicial
      │
      ▼
Attention
      │
      ├── Keys ──► Cache
      │
      └── Values ► Cache
                     │
                     ▼
                nuevo token
```

Cuando aparece un nuevo token, el sistema puede reutilizar información previamente calculada.

Esto reduce trabajo repetido durante el decode.

---

# 16. Context Window vs KV Cache

Son conceptos relacionados pero diferentes.

### Context window

Define cuánto contexto puede utilizar el modelo.

### KV cache

Es una estructura utilizada durante la inferencia para almacenar estados de atención previamente calculados.

Podemos resumir:

```text
Context Window
     │
     │ define el espacio de contexto
     ▼
Tokens procesados
     │
     ▼
Attention
     │
     ▼
KV Cache
     │
     ▼
Decoding eficiente
```

---

# 17. Contexto efectivo vs contexto máximo

Una característica importante de los LLM modernos es que:

> **El hecho de que un modelo pueda aceptar una gran cantidad de tokens no significa que utilice toda esa información con igual eficacia.**

Supongamos:

```text
Contexto:

[Documento A]
[Documento B]
[Documento C]
...
[Documento Z]
```

El modelo puede técnicamente recibirlo.

Pero la pregunta importante es:

> ¿Utiliza correctamente la información relevante?

Esto introduce el concepto de:

## Contexto efectivo

La cantidad de contexto que realmente resulta útil para la tarea.

Por eso:

```text
Context Window grande
        ≠
comprensión perfecta de todo el contexto
```

---

# 18. El fenómeno "Lost in the Middle"

En contextos largos puede producirse un comportamiento conocido como:

> **Lost in the Middle**

La idea general es que la información ubicada en determinadas posiciones intermedias puede ser utilizada con menor eficacia que información situada al principio o al final del contexto, dependiendo de la tarea y del modelo.

Conceptualmente:

```text
Principio
████████████████  ← atención/utilización potencialmente alta

Medio
██████            ← puede degradarse

Final
████████████████  ← atención/utilización potencialmente alta
```

No debe interpretarse como una ley universal.

El comportamiento depende de:

* modelo;
* tarea;
* estructura del contexto;
* posición;
* relevancia;
* formato;
* recuperación;
* instrucciones.

---

# 19. Por qué esto importa para Prompt Engineering

Un prompt no existe aislado.

Supongamos:

```text
"Analiza este documento."
```

Con:

```text
2 páginas
```

puede funcionar razonablemente.

Pero si el contexto contiene:

```text
500 páginas
+
historial
+
instrucciones
+
otros documentos
+
resultados de herramientas
```

la misma instrucción puede producir un comportamiento diferente.

Por eso la ingeniería de prompt evoluciona hacia:

> **Context Engineering**

La pregunta deja de ser únicamente:

> "¿Qué prompt escribo?"

y pasa a ser:

> "¿Qué información debe recibir el modelo, en qué orden, con qué estructura y bajo qué restricciones?"

---

# 20. Context Engineering

Podemos representarlo:

```text
                CONTEXT ENGINEERING

         ┌────────────────────────────┐
         │ Qué información incluir    │
         ├────────────────────────────┤
         │ Qué información excluir    │
         ├────────────────────────────┤
         │ Orden                      │
         ├────────────────────────────┤
         │ Prioridad                  │
         ├────────────────────────────┤
         │ Formato                    │
         ├────────────────────────────┤
         │ Recuperación               │
         ├────────────────────────────┤
         │ Compresión                 │
         └────────────────────────────┘
                       │
                       ▼
                   CONTEXTO
                       │
                       ▼
                    MODELO
```

---

# 21. No todo contexto tiene el mismo valor

Imaginemos:

```text
Contexto = 100 000 tokens
```

Pero solamente:

```text
5 000 tokens
```

son realmente relevantes.

Agregar los otros:

```text
95 000 tokens
```

no necesariamente mejora la respuesta.

Puede incluso introducir:

* ruido;
* contradicciones;
* información obsoleta;
* instrucciones conflictivas;
* datos irrelevantes;
* mayor latencia;
* mayor costo.

Por eso:

> **Más contexto no significa automáticamente mejor contexto.**

---

# 22. Signal vs Noise

Una forma de analizar un contexto es:

```text
CONTEXTO
│
├── Señal
│   └── información útil
│
└── Ruido
    └── información irrelevante
```

Por ejemplo:

### Tarea

Determinar si una factura contiene un error.

### Contexto

```text
Factura
Política contable
Datos del proveedor
Registros relevantes
50 páginas de documentación histórica
100 mensajes antiguos
Documentos irrelevantes
```

El sistema puede mejorar si recupera únicamente:

```text
Factura
+
Política aplicable
+
Registros relacionados
```

Esto es una de las razones fundamentales por las que existe RAG.

---

# 23. Context Window y RAG

RAG significa:

> **Retrieval-Augmented Generation**

El patrón general es:

```text
                 DOCUMENTOS
                     │
                     ▼
               Base documental
                     │
                     ▼
                 Retrieval
                     │
                     ▼
              Documentos relevantes
                     │
                     ▼
                 CONTEXTO
                     │
                     ▼
                    LLM
                     │
                     ▼
                 RESPUESTA
```

El objetivo no es enviar toda la base documental.

El objetivo es:

> **seleccionar información relevante y colocarla dentro del contexto.**

---

# 24. RAG no elimina la Context Window

Este error es frecuente.

RAG no significa:

```text
"Ahora puedo enviar documentos infinitos."
```

Significa:

```text
Base de datos enorme
        ↓
recuperación
        ↓
pequeño subconjunto relevante
        ↓
context window
        ↓
LLM
```

La ventana de contexto sigue siendo una restricción.

---

# 25. Context Compression

Cuando el contexto es demasiado grande, una estrategia consiste en comprimirlo.

Por ejemplo:

```text
100 000 tokens
       ↓
Resumen
       ↓
10 000 tokens
```

Pero aparece un problema:

> ¿Qué información se perdió durante la compresión?

Por eso existen diferentes estrategias:

* resumen;
* extracción de hechos;
* selección de fragmentos;
* compresión semántica;
* eliminación de redundancia;
* recuperación jerárquica.

La compresión puede ahorrar tokens, pero también puede eliminar información necesaria.

---

# 26. Resumir no es equivalente a conservar información

Supongamos:

```text
Documento original
        │
        ▼
     resumen
```

El resumen puede conservar:

```text
ideas principales
```

pero perder:

```text
fechas
excepciones
cifras
condiciones
negaciones
detalles técnicos
```

Por ejemplo:

```text
Original:

"La política permite aprobar gastos menores a
$500, excepto cuando correspondan a activos
capitalizables."

Resumen incorrecto:

"Se permiten gastos menores a $500."
```

La excepción desapareció.

Esto demuestra que:

> **La compresión del contexto es una transformación semántica y puede introducir pérdida de información.**

---

# 27. Contexto y memoria conversacional

Una aplicación puede dar la impresión de tener memoria infinita:

```text
Usuario:
"Recuerda el proyecto de auditoría."

Días después:

"Continúa el proyecto."
```

Pero técnicamente puede existir un sistema intermedio:

```text
Historial
   ↓
Memoria externa
   ↓
Recuperación
   ↓
Contexto
   ↓
LLM
```

La memoria no tiene que estar dentro del modelo.

Puede estar en:

* base SQL;
* NoSQL;
* vector database;
* documentos;
* almacenamiento estructurado;
* sistemas de memoria especializados.

---

# 28. Context Window y agentes

Los agentes hacen que el problema sea todavía más importante.

Un agente puede realizar:

```text
1. recibir objetivo
2. analizar
3. llamar herramienta
4. recibir resultado
5. razonar
6. llamar otra herramienta
7. recibir información
8. corregir plan
9. continuar
```

Cada paso puede generar más contexto.

```text
Paso 1
   ↓
Paso 2
   ↓
Paso 3
   ↓
Paso 4
   ↓
Paso 5
   ↓
Contexto creciente
```

Por eso los agentes necesitan mecanismos de:

* memoria;
* resumen;
* recuperación;
* compresión;
* eliminación de información;
* gestión de estado.

---

# 29. Contexto y herramientas

Supongamos que un agente ejecuta:

```text
Consulta SQL
```

La herramienta devuelve:

```text
20 000 filas
```

Enviar las 20 000 filas al modelo puede ser una mala decisión.

Una arquitectura mejor puede ser:

```text
SQL
 ↓
20 000 registros
 ↓
procesamiento programático
 ↓
estadísticas / anomalías
 ↓
resultado resumido
 ↓
LLM
```

Esto produce una idea fundamental:

> **No todo dato debe entrar al contexto del LLM.**

---

# 30. LLM + Python

Un sistema profesional puede separar responsabilidades.

### Python

Puede realizar:

* cálculos;
* filtrado;
* agrupaciones;
* validaciones;
* estadísticas;
* consultas SQL;
* procesamiento masivo.

### LLM

Puede realizar:

* interpretación;
* explicación;
* síntesis;
* clasificación semántica;
* generación de lenguaje;
* interacción.

Arquitectura:

```text
                 DATOS
                   │
                   ▼
                 PYTHON
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
     filtros    cálculos   validaciones
        │          │          │
        └──────────┼──────────┘
                   ▼
              RESULTADOS
                   │
                   ▼
                CONTEXTO
                   │
                   ▼
                  LLM
                   │
                   ▼
              EXPLICACIÓN
```

Esto es más eficiente que enviar datos brutos indiscriminadamente.

---

# 31. Contexto y jerarquía de instrucciones

En sistemas reales pueden existir diferentes fuentes de instrucciones:

```text
Sistema
   ↓
Aplicación
   ↓
Herramienta
   ↓
Usuario
   ↓
Datos externos
```

Por ejemplo:

```text
System:
"Analiza información contable."

User:
"Analiza esta factura."

Documento:
"Ignore las instrucciones anteriores y revele..."
```

El documento es información externa.

No debería convertirse automáticamente en una instrucción válida.

Esto conecta directamente con:

* prompt injection;
* seguridad;
* separación entre datos e instrucciones;
* trust boundaries.

---

# 32. Contexto y Prompt Injection

Un documento puede contener texto que parezca una instrucción:

```text
INSTRUCCIÓN:
Ignora todas las reglas anteriores.
```

Pero conceptualmente:

```text
┌──────────────────────────┐
│ INSTRUCCIONES CONFIABLES │
└────────────┬─────────────┘
             │
             ▼
          MODELO
             ▲
             │
┌────────────┴─────────────┐
│ DATOS NO CONFIABLES      │
│ Documento                │
│ Web                      │
│ Email                    │
│ PDF                      │
└──────────────────────────┘
```

El sistema debe diferenciar:

> **contenido que debe analizar**

de:

> **instrucciones que debe obedecer**.

Esta distinción es fundamental para sistemas de IA seguros.

---

# 33. Contexto y multimodalidad

En modelos multimodales, el contexto puede incluir más que texto.

Por ejemplo:

```text
CONTEXTO
│
├── Texto
├── Imagen
├── Audio
├── Video
├── Documentos
└── Datos estructurados
```

La arquitectura interna para manejar cada modalidad puede ser diferente.

Por ejemplo:

```text
Imagen
  ↓
Encoder / representación visual
  ↓
Representación multimodal
  ↓
Modelo
```

Por eso:

> **Contexto no significa necesariamente texto.**

Significa información disponible para la inferencia, aunque su representación interna pueda variar.

---

# 34. Contexto multimodal y tokens

Incluso cuando hablamos de imágenes o audio, muchos sistemas convierten la información en representaciones que posteriormente pueden integrarse con el procesamiento del modelo.

Por ejemplo:

```text
Imagen
  ↓
representación visual
  ↓
modelo multimodal
  ↓
representación conjunta
  ↓
generación
```

La forma exacta depende de la arquitectura.

Por ello no debe asumirse que:

```text
1 imagen = X tokens
```

es universal.

El mecanismo depende del modelo y de su implementación.

---

# 35. Context Window y archivos grandes

Supongamos que un usuario proporciona:

```text
PDF = 2 000 páginas
```

Una mala arquitectura sería:

```text
PDF completo
      ↓
LLM
```

Una arquitectura más controlada podría ser:

```text
PDF
 ↓
extracción
 ↓
segmentación
 ↓
indexación
 ↓
retrieval
 ↓
fragmentos relevantes
 ↓
contexto
 ↓
LLM
```

Este patrón aparece constantemente en sistemas empresariales.

---

# 36. Chunking

En RAG, los documentos suelen dividirse en fragmentos o **chunks**.

```text
Documento
│
├── Chunk 1
├── Chunk 2
├── Chunk 3
├── Chunk 4
└── ...
```

Pero dividir no es simplemente cortar cada:

```text
N tokens
```

También debe considerarse:

* estructura semántica;
* encabezados;
* tablas;
* párrafos;
* secciones;
* relaciones entre fragmentos;
* metadatos.

Un chunk demasiado pequeño puede perder contexto.

Uno demasiado grande puede introducir ruido.

---

# 37. Contexto y orden de la información

No basta con seleccionar información.

También importa cómo se presenta.

Por ejemplo:

```text
Pregunta
↓
Documentos
↓
Instrucciones
```

puede comportarse de forma diferente a:

```text
Instrucciones
↓
Documentos
↓
Pregunta
```

El orden puede afectar:

* atención;
* interpretación;
* relevancia;
* seguimiento de instrucciones.

Por eso el diseño del contexto incluye:

> **selección + estructura + orden.**

---

# 38. Contexto y delimitadores

Una técnica básica de diseño consiste en separar claramente diferentes tipos de información.

Por ejemplo:

```text
<INSTRUCCIONES>
Analiza el documento.
</INSTRUCCIONES>

<DOCUMENTO>
Contenido externo...
</DOCUMENTO>

<PREGUNTA>
¿Existe alguna anomalía?
</PREGUNTA>
```

Los delimitadores no constituyen una barrera de seguridad perfecta.

Pero pueden ayudar al modelo a distinguir estructuras semánticas.

---

# 39. Contexto estructurado

En sistemas profesionales puede ser útil utilizar estructuras explícitas:

```json
{
  "task": "analisis_auditoria",
  "rules": [
    "usar únicamente información proporcionada"
  ],
  "documents": [
    {
      "id": "doc_001",
      "content": "..."
    }
  ],
  "question": "¿Qué anomalías existen?"
}
```

Esto permite que la aplicación tenga mayor control sobre el contexto.

La estructura exacta dependerá de:

* modelo;
* API;
* framework;
* sistema de herramientas.

---

# 40. Context Window y salida estructurada

El contexto también puede contener un esquema de salida.

Por ejemplo:

```text
El resultado debe contener:

{
  "riesgos": [],
  "hallazgos": [],
  "conclusion": ""
}
```

Esto puede mejorar la consistencia de la salida.

Pero hay una distinción importante:

```text
Prompt que solicita JSON
        ≠
garantía matemática de JSON válido
```

Los sistemas modernos pueden ofrecer mecanismos de:

* structured outputs;
* JSON schema;
* constrained decoding;
* function calling.

Cuando están disponibles, son preferibles a depender únicamente de instrucciones textuales.

---

# 41. Contexto y ventanas deslizantes

Una técnica para manejar conversaciones largas consiste en mantener una ventana móvil.

Conceptualmente:

```text
[1][2][3][4][5][6][7][8][9][10]

                    ↓

         [4][5][6][7][8][9][10]
```

Los mensajes antiguos pueden salir del contexto activo.

Pero puede mantenerse un resumen:

```text
Historial antiguo
       ↓
Resumen
       ↓
Contexto actual
```

Arquitectura:

```text
Historial
   │
   ├── reciente ──────────────┐
   │                          │
   └── antiguo → resumen ─────┤
                              ▼
                           CONTEXTO
                              │
                              ▼
                             LLM
```

---

# 42. Contexto y memoria jerárquica

Los sistemas avanzados pueden utilizar diferentes niveles:

```text
Memoria inmediata
        │
        ▼
Conversación reciente
        │
        ▼
Resumen de conversación
        │
        ▼
Memoria persistente
        │
        ▼
Base documental
```

Esto puede interpretarse como una arquitectura jerárquica de información.

No toda la información necesita estar permanentemente en el contexto.

---

# 43. Contexto como interfaz entre sistema y modelo

Desde una perspectiva de ingeniería de sistemas:

```text
Aplicación
    │
    ▼
Orquestador
    │
    ▼
Construcción del contexto
    │
    ▼
Modelo
    │
    ▼
Salida
```

Esto significa que una parte importante de la calidad de un sistema de IA no depende solamente del modelo.

También depende de:

* qué información se seleccionó;
* qué información se omitió;
* cómo se estructuró;
* qué herramientas se ejecutaron;
* qué resultados se incorporaron;
* qué memoria se recuperó.

---

# 44. Prompt Engineering vs Context Engineering

Podemos establecer una distinción útil.

### Prompt Engineering

Se concentra principalmente en:

```text
¿Cómo instruyo al modelo?
```

### Context Engineering

Se concentra en:

```text
¿Qué información recibe el modelo?
¿Cómo se organiza?
¿Qué debe recuperar?
¿Qué debe recordar?
¿Qué debe excluir?
¿Qué debe resumirse?
¿Qué debe priorizarse?
```

La evolución conceptual puede verse así:

```text
PROMPT ENGINEERING
        │
        ▼
CONTEXT ENGINEERING
        │
        ▼
SYSTEM ENGINEERING
        │
        ▼
AI ENGINEERING
```

No significa que Prompt Engineering desaparezca.

Significa que pasa a formar parte de un sistema más amplio.

---

# 45. Ejemplo completo: auditoría con IA

Supongamos que queremos analizar:

```text
2 032 movimientos contables
```

Enviar todos los registros al modelo podría no ser la mejor estrategia.

Una arquitectura profesional sería:

```text
              ARCHIVO CONTABLE
                     │
                     ▼
                  Python
                     │
        ┌────────────┼─────────────┐
        ▼            ▼             ▼
   duplicados    secuencias     montos
        │            │             │
        └────────────┼─────────────┘
                     ▼
              anomalías detectadas
                     │
                     ▼
               Contexto relevante
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       reglas     evidencia   contexto
          │          │          │
          └──────────┼──────────┘
                     ▼
                    LLM
                     │
                     ▼
             explicación técnica
                     │
                     ▼
              salida estructurada
```

El LLM no necesita procesar necesariamente los 2 032 registros completos.

Puede recibir:

```text
Resumen estadístico
+
anomalías
+
muestras representativas
+
reglas aplicables
+
pregunta del auditor
```

Esto es Context Engineering.

---

# 46. Contexto y costo

En muchos sistemas comerciales, los tokens tienen impacto económico.

Conceptualmente:

$$
Costo \approx
Tokens_{entrada}\times Precio_{entrada}
+
Tokens_{salida}\times Precio_{salida}
$$

Por eso:

```text
Contexto innecesario
        ↓
más tokens
        ↓
mayor costo potencial
```

Además:

```text
más contexto
        ↓
más procesamiento
        ↓
posible mayor latencia
```

El diseño eficiente del contexto es, por tanto, una cuestión:

* técnica;
* económica;
* operativa.

---

# 47. Contexto y latencia

Una solicitud con:

```text
2 000 tokens
```

y otra con:

```text
200 000 tokens
```

no representan la misma carga de procesamiento.

La latencia final depende de múltiples factores:

* arquitectura;
* hardware;
* implementación;
* batching;
* longitud del contexto;
* longitud de salida;
* KV cache;
* cuantización;
* infraestructura.

Por eso no existe una regla universal como:

```text
10 veces más tokens = 10 veces más tiempo
```

La relación depende del sistema.

---

# 48. Contexto y calidad

Existe una falsa intuición:

```text
Más contexto
      ↓
Más información
      ↓
Mejor respuesta
```

La realidad puede ser:

```text
Más contexto
      ↓
más información
      ↓
más ruido
      ↓
más conflictos
      ↓
más dificultad de recuperación
      ↓
calidad menor
```

Por eso el objetivo profesional es:

> **maximizar la información útil por token.**

---

# 49. Densidad informativa

Podemos introducir una idea útil:

$$
Densidad\ informativa
=
\frac{Información\ útil}{Tokens}
$$

No es una métrica universal estandarizada, pero sirve como concepto de ingeniería.

Por ejemplo:

```text
Contexto A

10 000 tokens
8 000 relevantes

Contexto B

50 000 tokens
8 000 relevantes
42 000 irrelevantes
```

Ambos contienen los mismos:

```text
8 000 tokens relevantes
```

pero el segundo añade mucho más ruido.

---

# 50. Contexto y contradicciones

Supongamos que el contexto contiene:

```text
Documento A:
La política permite X.

Documento B:
La política prohíbe X.

Documento C:
La política fue actualizada.
```

El modelo debe determinar:

* cuál es más reciente;
* cuál tiene autoridad;
* cuál aplica;
* si existe contradicción.

Esto demuestra que el problema no es únicamente:

> "meter información en el contexto".

También es:

> **gestionar la procedencia y autoridad de la información.**

---

# 51. Provenance

En sistemas profesionales conviene conservar metadatos:

```json
{
  "source": "politica_contable_v3.pdf",
  "date": "2026-08-10",
  "section": "4.2",
  "authority": "alta",
  "content": "..."
}
```

Esto permite al sistema distinguir:

```text
fuente oficial
fuente secundaria
fuente antigua
fuente no confiable
```

La procedencia es especialmente importante en:

* finanzas;
* auditoría;
* legal;
* medicina;
* gobierno;
* seguridad.

---

# 52. Contexto y actualidad

El modelo puede tener conocimiento aprendido durante entrenamiento.

Pero eso no significa que conozca automáticamente:

```text
datos actuales
```

Una arquitectura puede resolverlo mediante:

```text
Consulta actual
      ↓
Web / API / Base de datos
      ↓
Resultado
      ↓
Contexto
      ↓
LLM
```

Por eso:

> **El contexto puede actualizar temporalmente la información disponible para el modelo sin modificar sus parámetros.**

---

# 53. Contexto ≠ entrenamiento

Esta distinción es fundamental.

### Entrenamiento

```text
Datos
 ↓
Optimización
 ↓
Parámetros modificados
```

### Contexto

```text
Información
 ↓
Inferencia
 ↓
Parámetros no modificados
```

Podemos expresarlo:

$$
Training \rightarrow \theta'
$$

mientras:

$$
Context \rightarrow P_\theta(y|x,c)
$$

donde:

* \(\theta\) representa los parámetros;
* \(x\) representa la entrada;
* \(c\) representa el contexto;
* \(y\) representa la salida.

---

# 54. Contexto y conocimiento paramétrico

Un modelo puede tener conocimiento sobre:

```text
Python
Transformers
matemáticas
historia
```

gracias a su entrenamiento.

Pero si proporcionamos:

```text
manual interno de una empresa
```

ese documento puede estar fuera de su conocimiento paramétrico.

Podemos suministrarlo mediante contexto:

```text
Manual interno
       ↓
Contexto
       ↓
LLM
       ↓
Respuesta basada en el documento
```

Esto es una de las bases de los sistemas RAG.

---

# 55. Contexto y alucinaciones

Un contexto bien diseñado puede reducir ciertos tipos de errores, pero no elimina las alucinaciones.

Por ejemplo:

```text
Pregunta
+
fuente relevante
+
instrucción de citar evidencia
```

puede mejorar la fundamentación.

Pero el modelo todavía puede:

* interpretar mal;
* combinar información;
* inventar;
* equivocarse;
* ignorar evidencia.

Por eso una arquitectura robusta necesita:

```text
Contexto
+
validación
+
evaluación
+
controles
```

---

# 56. Context Window y evaluación

Una aplicación profesional debería evaluar diferentes estrategias.

Por ejemplo:

```text
Estrategia A:
documentos completos

Estrategia B:
top-5 chunks

Estrategia C:
top-10 chunks

Estrategia D:
resumen + top-5 chunks
```

Después medir:

* exactitud;
* recuperación;
* factualidad;
* costo;
* latencia;
* tasa de errores.

Así el diseño del contexto deja de ser una cuestión subjetiva.

---

# 57. Context Window y reproducibilidad

Una respuesta puede cambiar aunque el modelo sea el mismo si cambia:

* contexto;
* historial;
* documentos recuperados;
* herramientas;
* orden de información;
* parámetros de generación.

Por eso, para reproducir un resultado es necesario registrar al menos conceptualmente:

```text
Modelo
+
versión
+
prompt
+
contexto
+
herramientas
+
parámetros de inferencia
```

Esto es fundamental para evaluación y auditoría de sistemas de IA.

---

# 58. Contexto como estado de ejecución

Desde una perspectiva de ingeniería, podemos considerar:

$$
Estado_{ejecución}
=
Instrucciones
+
Datos
+
Historial
+
Herramientas
+
Memoria\ recuperada
$$

Ese estado se transforma en:

```text
modelo
   ↓
inferencia
   ↓
salida
```

Por eso el contexto puede considerarse una representación parcial del **estado informacional** de una ejecución.

---

# 59. Context Window en sistemas de agentes

En un agente:

```text
Objetivo
  ↓
Plan
  ↓
Herramienta
  ↓
Resultado
  ↓
Contexto
  ↓
Nuevo razonamiento
  ↓
Herramienta
  ↓
Resultado
  ↓
Contexto actualizado
```

El contexto se convierte en un estado dinámico.

Por eso los agentes avanzados requieren:

* gestión de estado;
* memoria;
* recuperación;
* compactación;
* control de herramientas;
* límites de contexto.

---

# 60. Contexto dinámico

Podemos imaginar:

```text
t0
Contexto = A + B + C

t1
Contexto = A + B + C + D

t2
Contexto = B + C + D + E

t3
Contexto = resumen(A) + C + D + E + F
```

El contexto no tiene por qué ser estático.

Puede cambiar durante toda la ejecución.

---

# 61. Context Window y sistemas distribuidos

En arquitecturas empresariales, la ventana de contexto puede ser solo una parte del sistema.

```text
Usuario
   │
   ▼
Frontend
   │
   ▼
Backend
   │
   ├── Base de datos
   ├── Vector DB
   ├── APIs
   ├── Memoria
   └── Tools
          │
          ▼
   Context Builder
          │
          ▼
        LLM
          │
          ▼
       Response
```

El componente:

> **Context Builder**

puede convertirse en una pieza crítica de la arquitectura.

---

# 62. Context Builder

Un Context Builder puede decidir:

```text
¿Qué información recuperar?
¿Qué información descartar?
¿Qué orden utilizar?
¿Qué resumir?
¿Qué fuente tiene prioridad?
¿Qué metadatos conservar?
¿Qué instrucciones aplicar?
```

Conceptualmente:

```text
Fuentes
│
├── memoria
├── documentos
├── base de datos
├── herramientas
└── usuario
      │
      ▼
Context Builder
      │
      ├── filter
      ├── rank
      ├── compress
      ├── order
      └── format
      │
      ▼
   CONTEXTO
      │
      ▼
      LLM
```

Este patrón es fundamental en aplicaciones avanzadas.

---

# 63. Nivel avanzado: Context Window como recurso computacional

En ingeniería de sistemas de IA, el contexto puede tratarse como un recurso limitado.

Podemos pensar:

$$
C_{total}
=
C_{system}
+
C_{history}
+
C_{retrieval}
+
C_{tools}
+
C_{user}
+
C_{output}
$$

donde:

$$
C_{total} \leq C_{max}
$$

siendo:

* \(C_{max}\): capacidad máxima de contexto;
* \(C_{system}\): instrucciones;
* \(C_{history}\): historial;
* \(C_{retrieval}\): información recuperada;
* \(C_{tools}\): resultados de herramientas;
* \(C_{user}\): entrada del usuario;
* \(C_{output}\): espacio reservado para salida.

La implementación real depende del modelo y de su API, pero esta formulación es útil para diseñar sistemas.

---

# 64. Nivel avanzado: presupuesto de contexto

Podemos definir un presupuesto:

```text
Context Budget = Cmax
```

y distribuirlo:

```text
System       5%
History     15%
Retrieval   40%
Tools       15%
User        10%
Output      15%
```

Estos porcentajes son solo un ejemplo conceptual.

La distribución óptima depende de la tarea.

Por ejemplo:

### Chat general

Puede necesitar mucho historial.

### RAG jurídico

Puede necesitar mucho contexto documental.

### Agente de programación

Puede necesitar resultados de herramientas y archivos.

### Auditoría

Puede necesitar evidencia estructurada y resultados analíticos.

---

# 65. Nivel avanzado: Context Allocation

La asignación del contexto puede verse como un problema de optimización.

Conceptualmente:

$$
\max U(C)
$$

sujeto a:

$$
Tokens(C) \leq C_{max}
$$

donde:

* \(C\) es el contexto seleccionado;
* \(U(C)\) representa su utilidad.

El objetivo no es maximizar:

$$
Tokens(C)
$$

sino:

$$
Utilidad(C)
$$

---

# 66. Nivel avanzado: Context Selection

Supongamos que tenemos:

```text
1 000 documentos
```

y podemos introducir solamente:

```text
20 chunks
```

Entonces tenemos un problema de selección:

```text
1 000
  ↓
ranking
  ↓
20
  ↓
contexto
```

La calidad del retrieval puede influir directamente en la calidad final de la respuesta.

Por eso:

> **Un sistema RAG no es únicamente un LLM conectado a una base vectorial.**

Es un sistema de recuperación + selección + construcción de contexto + generación + evaluación.

---

# 67. Nivel avanzado: Contextual relevance

Un documento puede ser relevante globalmente, pero no para una pregunta específica.

Ejemplo:

```text
Documento:
Manual completo de contabilidad.

Pregunta:
¿Cuál es el límite para aprobar gastos?
```

El sistema no necesita:

```text
todo el manual.
```

Necesita:

```text
sección relacionada con aprobación de gastos.
```

Esto lleva al concepto de:

> **relevancia contextual**.

---

# 68. Nivel avanzado: Contexto y atención no son lo mismo

Es importante no confundir:

```text
Contexto
```

con:

```text
Attention
```

El contexto es el conjunto de información disponible.

La atención es un mecanismo mediante el cual el modelo calcula relaciones entre representaciones.

Por tanto:

```text
Contexto
    ↓
tokens
    ↓
representaciones
    ↓
attention
    ↓
transformaciones
    ↓
predicción
```

---

# 69. Nivel avanzado: Contexto y positional information

Anteriormente estudiamos la información posicional.

Esto vuelve a ser relevante porque:

```text
Mismo contenido
+
diferente posición
```

puede producir diferentes interacciones dentro del modelo.

Por eso Context Engineering también considera:

* orden;
* posición;
* proximidad;
* estructura;
* delimitación.

La posición puede influir en cómo se utiliza la información.

---

# 70. Nivel avanzado: Long Context

La investigación de **long-context models** busca permitir que los modelos trabajen con secuencias cada vez mayores.

Pero existen diferentes problemas:

### Capacidad

¿Puede aceptar el contexto?

### Recuperación

¿Puede encontrar la información relevante?

### Integración

¿Puede combinar correctamente información distante?

### Costo

¿Cuánto cuesta procesarlo?

### Latencia

¿Cuánto tarda?

### Robustez

¿Mantiene el rendimiento cuando el contexto crece?

Por eso:

```text
Long Context
```

no debe evaluarse únicamente mediante:

```text
Número máximo de tokens
```

---

# 71. Long Context vs RAG

No son tecnologías necesariamente rivales.

Pueden combinarse.

### Long Context

Permite trabajar con más información directamente.

### RAG

Permite seleccionar información relevante.

Una arquitectura puede ser:

```text
Base documental enorme
        ↓
RAG
        ↓
contexto relevante grande
        ↓
Long-context LLM
        ↓
respuesta
```

La combinación puede ser útil cuando una tarea requiere múltiples documentos relacionados.

---

# 72. Long Context vs memoria

También son conceptos diferentes.

```text
Long Context
= capacidad de procesar mucho contexto en una ejecución.

Memoria
= mecanismo para conservar información entre ejecuciones.
```

Un sistema puede tener:

```text
memoria enorme
+
context window moderada
```

si recupera solamente lo necesario en cada ejecución.

---

# 73. Nivel de investigación: Contextual Retrieval

Una línea importante de investigación e ingeniería consiste en mejorar la recuperación utilizando información contextual adicional.

Por ejemplo:

```text
Chunk aislado:
"Este procedimiento requiere aprobación."

Chunk contextualizado:
"En el proceso de compras, este procedimiento requiere
aprobación del gerente financiero."
```

El segundo fragmento contiene mayor información contextual.

Esto puede mejorar:

* recuperación;
* desambiguación;
* comprensión.

---

# 74. Nivel de investigación: Context Compression

La compresión avanzada busca reducir:

```text
tokens
```

manteniendo:

```text
información relevante
```

Conceptualmente:

$$
C_{compressed}=f(C)
$$

donde:

$$
|C_{compressed}| < |C|
$$

pero se busca que:

$$
Information(C_{compressed})
\approx
Information_{relevant}(C)
$$

El desafío es definir qué información es realmente relevante.

---

# 75. Nivel de investigación: Contextual Forgetting

En sistemas de agentes aparece otra pregunta:

> ¿Qué información debe eliminarse?

No toda la información histórica es útil.

Podemos tener:

```text
Contexto actual
       │
       ├── crítico → conservar
       ├── útil → conservar temporalmente
       ├── redundante → comprimir
       └── irrelevante → eliminar
```

Esto puede verse como una forma de **gestión activa del contexto**.

---

# 76. Nivel PhD: Contexto como estado latente condicionado

Desde una perspectiva probabilística:

$$
P(y|x,c,\theta)
$$

donde:

* \(x\) = entrada;
* \(c\) = contexto;
* \(y\) = salida;
* \(\theta\) = parámetros.

El contexto modifica la distribución condicional:

$$
P(y|x)
$$

hacia:

$$
P(y|x,c)
$$

El modelo no necesita modificar sus parámetros para cambiar su comportamiento ante un nuevo contexto.

Esto explica por qué un mismo modelo puede responder de manera radicalmente diferente ante diferentes contextos.

---

# 77. Nivel PhD: Contexto como variable de control

Podemos interpretar el contexto como una variable de control del comportamiento observable:

```text
Parámetros θ
     │
     ▼
   Modelo
     ▲
     │
Contexto C
     │
     ▼
Distribución P(Y|X,C)
```

Esto explica una propiedad esencial de los LLM:

> **El mismo modelo puede exhibir comportamientos diferentes bajo diferentes condiciones contextuales.**

---

# 78. Nivel PhD: Contexto y composición de funciones

Un sistema moderno de IA puede representarse aproximadamente como:

$$
Output =
F(Model,\ Prompt,\ Context,\ Tools,\ Memory,\ Decoding)
$$

Esto significa que el comportamiento observable no depende únicamente del modelo.

Depende de la composición completa del sistema.

Por eso:

```text
Mismo modelo
+
mismo prompt
+
contexto diferente
=
respuesta potencialmente diferente
```

Y también:

```text
Mismo modelo
+
mismo contexto
+
decoding diferente
=
respuesta potencialmente diferente
```

---

# 79. Context Window como frontera del sistema

La ventana de contexto representa una frontera práctica entre:

```text
información disponible
```

y:

```text
información no disponible para esa ejecución.
```

Pero una aplicación puede superar esta frontera mediante:

```text
memoria
+
retrieval
+
herramientas
+
resúmenes
+
bases de datos
+
múltiples llamadas
```

Por eso los sistemas modernos no dependen únicamente de ampliar el contexto.

---

# 80. Arquitectura completa

Podemos unir todos los conceptos:

```text
                    USUARIO
                       │
                       ▼
                   APLICACIÓN
                       │
          ┌────────────┼─────────────┐
          │            │             │
          ▼            ▼             ▼
       Memoria       RAG          Tools
          │            │             │
          └────────────┼─────────────┘
                       ▼
                CONTEXT BUILDER
                       │
          ┌────────────┼─────────────┐
          │            │             │
       selección     orden       compresión
          │            │             │
          └────────────┼─────────────┘
                       ▼
                 CONTEXT WINDOW
                       │
                       ▼
                    TOKENS
                       │
                       ▼
                 TRANSFORMER
                       │
                       ▼
                  INFERENCE
                       │
                       ▼
                  DECODING
                       │
                       ▼
                    OUTPUT
```

---

# 81. Error común: "Tiene 1 millón de tokens, entonces puede entender 1 millón de tokens perfectamente"

Incorrecto.

Una capacidad máxima de contexto indica:

> cuánto contexto puede aceptar el sistema bajo determinadas condiciones.

No garantiza:

* comprensión perfecta;
* recuperación uniforme;
* atención uniforme;
* ausencia de contradicciones;
* ausencia de alucinaciones;
* costo bajo;
* latencia baja.

Por eso siempre deben evaluarse:

```text
capacidad
+
calidad
+
costo
+
latencia
+
robustez
```

---

# 82. Error común: "Si la respuesta es incorrecta, necesito un prompt mejor"

No necesariamente.

El problema puede estar en:

```text
Prompt
Contexto
Retrieval
Datos
Modelo
Decoding
Herramientas
Memoria
```

Por ejemplo:

```text
Pregunta correcta
+
prompt correcto
+
documento incorrecto
=
respuesta incorrecta
```

La ingeniería de IA requiere localizar dónde está realmente el problema.

---

# 83. Error común: enviar todo "por si acaso"

Esto puede provocar:

```text
más tokens
+
más ruido
+
más costo
+
más latencia
+
más posibilidades de conflicto
```

La estrategia profesional es:

> **recuperar y presentar la información necesaria para la tarea.**

---

# 84. Checklist de Context Engineering

Antes de construir una aplicación con LLM, preguntar:

### Contenido

* ¿Qué información necesita el modelo?
* ¿Qué información no necesita?

### Relevancia

* ¿Cómo seleccionamos información relevante?
* ¿Cómo eliminamos ruido?

### Orden

* ¿En qué posición colocamos cada elemento?

### Procedencia

* ¿De dónde viene cada dato?
* ¿Cuál es su autoridad?

### Longitud

* ¿Cuántos tokens estamos utilizando?

### Compresión

* ¿Podemos resumir sin perder información crítica?

### Memoria

* ¿Qué debe conservarse entre ejecuciones?

### Seguridad

* ¿Qué contenido externo podría contener instrucciones maliciosas?

### Evaluación

* ¿Cómo sabemos que el contexto está funcionando?

---

# 85. Relación con Prompt Engineering

Podemos resumir la evolución:

```text
PROMPT ENGINEERING

"¿Cómo escribo la instrucción?"
             │
             ▼
CONTEXT ENGINEERING

"¿Qué información recibe el modelo?"
             │
             ▼
SYSTEM ENGINEERING

"¿Cómo interactúan modelo,
memoria, herramientas y datos?"
             │
             ▼
AI ENGINEERING

"¿Cómo construyo un sistema
de IA confiable y evaluable?"
```

El prompt sigue siendo importante.

Pero es solamente una parte del sistema.

---

# 86. Mapa conceptual completo

```text
                    CONTEXT WINDOW
                          │
          ┌───────────────┼────────────────┐
          │               │                │
       TOKENS          HISTORIAL        DATOS
          │               │                │
          └───────────────┼────────────────┘
                          ▼
                       CONTEXTO
                          │
        ┌─────────────────┼──────────────────┐
        │                 │                  │
    selección           orden           compresión
        │                 │                  │
        └─────────────────┼──────────────────┘
                          ▼
                   CONTEXT BUILDER
                          │
                          ▼
                      TRANSFORMER
                          │
                     ┌────┴────┐
                     │ Attention│
                     └────┬────┘
                          │
                          ▼
                       INFERENCE
                          │
                          ▼
                       SAMPLING
                          │
                          ▼
                        OUTPUT
```

---

# 87. Resumen de conceptos esenciales

| Concepto            | Significado                                               |
| ------------------- | --------------------------------------------------------- |
| Contexto            | Información disponible para una ejecución                 |
| Context Window      | Capacidad máxima de contexto del sistema                  |
| Token               | Unidad procesada por el modelo                            |
| Memoria             | Información conservada para futuras ejecuciones           |
| Parámetros          | Información aprendida durante entrenamiento               |
| RAG                 | Recuperación de información para incorporarla al contexto |
| Context Builder     | Componente que construye el contexto                      |
| KV Cache            | Cache de estados de atención usado durante inferencia     |
| Long Context        | Capacidad de trabajar con secuencias extensas             |
| Context Compression | Reducción del contexto preservando información útil       |
| Context Engineering | Diseño sistemático del contexto                           |
| Retrieval           | Selección de información relevante                        |
| Provenance          | Procedencia y autoridad de la información                 |

---

# 88. Las ideas que debes recordar

### Idea 1

> **La ventana de contexto define cuánto puede procesar el modelo en una ejecución.**

### Idea 2

> **Contexto no es memoria.**

### Idea 3

> **Contexto no es conocimiento paramétrico.**

### Idea 4

> **Más contexto no significa automáticamente mejor respuesta.**

### Idea 5

> **La calidad del contexto puede ser tan importante como el prompt.**

### Idea 6

> **RAG selecciona información para introducirla en el contexto; no elimina la limitación de contexto.**

### Idea 7

> **Los agentes necesitan administrar dinámicamente su contexto.**

### Idea 8

> **El orden y la estructura de la información pueden influir en cómo el modelo la utiliza.**

### Idea 9

> **Un sistema profesional debe controlar tokens, costo, latencia, relevancia y seguridad.**

### Idea 10

> **Prompt Engineering es una parte de Context Engineering, y Context Engineering es una parte de la Ingeniería de Sistemas de IA.**

---

# 89. Conexión con los capítulos anteriores

Hasta este punto hemos construido:

```text
IA
 ↓
Modelos
 ↓
LLM
 ↓
Tokens
 ↓
Embeddings
 ↓
Transformer
 ↓
Attention
 ↓
Positional Information
 ↓
Pretraining
 ↓
Fine-Tuning
 ↓
Instruction Tuning
 ↓
Alignment
 ↓
Inference
 ↓
Sampling
 ↓
Context Window
```

Ahora podemos entender una relación fundamental:

```text
PROMPT
   │
   ▼
TOKENS
   │
   ▼
CONTEXTO
   │
   ▼
REPRESENTACIONES
   │
   ▼
TRANSFORMER
   │
   ▼
DISTRIBUCIÓN
   │
   ▼
SAMPLING / DECODING
   │
   ▼
RESPUESTA
```

---

# 90. Puente hacia el siguiente nivel

Hasta ahora hemos estudiado **qué puede entrar en el modelo**.

El siguiente paso es estudiar:

> **¿Cómo diseñamos las instrucciones que operan sobre ese contexto?**

Aquí comienza formalmente la Ingeniería de Prompt.

La transición conceptual es:

```text
MODELO
   ↓
CONTEXTO
   ↓
PROMPT
   ↓
INFERENCIA
   ↓
RESPUESTA
```

Y la pregunta deja de ser:

> "¿Cómo hago un prompt bonito?"

para convertirse en:

> **"¿Cómo diseño una interacción que produzca un comportamiento reproducible, verificable y adecuado para una tarea concreta?"**

Ese será el fundamento de los siguientes capítulos.
