# 01 — Modelos Base

> **Nivel 02 — Arquitecturas de IA y Modelos**

---

## 1. Objetivo del capítulo

Antes de estudiar arquitecturas como **Transformers densos, Mixture of Experts (MoE), modelos de razonamiento o modelos multimodales**, debemos comprender qué significa realmente que un modelo sea un **modelo base** (*Base Model*).

El término puede parecer sencillo, pero es fundamental para comprender por qué diferentes modelos responden de manera distinta ante el mismo prompt.

La idea central de este capítulo es:

> **Un modelo base es un modelo que ha aprendido patrones a partir de grandes cantidades de datos, pero que todavía no necesariamente ha sido entrenado para seguir instrucciones humanas de la forma en que esperamos de un asistente conversacional.**

Esto tiene una consecuencia importante para la Ingeniería de Prompt:

```text
                 MISMO PROMPT
                      │
          ┌───────────┴───────────┐
          │                       │
     MODELO BASE          MODELO INSTRUCTION
          │                       │
          ↓                       ↓
   Continuación de texto     Seguimiento de instrucciones
```

Por lo tanto:

> **El prompt no puede estudiarse completamente separado del modelo que lo recibe.**

---

# 2. ¿Qué es un modelo base?

Un **modelo base** es un modelo de inteligencia artificial que ha sido entrenado principalmente para aprender las regularidades estadísticas presentes en grandes conjuntos de datos.

En un LLM, una de las tareas fundamentales durante el entrenamiento consiste en aprender a predecir qué token puede aparecer a continuación.

Por ejemplo:

```text
El cielo es de color ______
```

El modelo puede aprender que algunas continuaciones probables son:

```text
azul
```

o:

```text
azul durante un día despejado
```

Pero el modelo no está simplemente almacenando una lista de frases.

Durante el entrenamiento aprende representaciones y relaciones entre:

* palabras;
* tokens;
* conceptos;
* estructuras lingüísticas;
* relaciones semánticas;
* patrones sintácticos;
* código;
* información presente en los datos;
* diferentes formas de expresión.

Una simplificación conceptual sería:

```text
                 DATOS
                   │
                   ↓
              ENTRENAMIENTO
                   │
                   ↓
          ┌──────────────────┐
          │   MODELO BASE    │
          │                  │
          │ Patrones         │
          │ Representaciones │
          │ Relaciones       │
          │ Regularidades    │
          └──────────────────┘
                   │
                   ↓
                INFERENCIA
                   │
                   ↓
             PREDICCIÓN
```

---

# 3. Modelo base no significa "modelo incompleto"

Es importante evitar una confusión frecuente.

Un modelo base **no necesariamente es un modelo malo, pequeño o poco capaz**.

"Base" describe principalmente **su etapa y objetivo de entrenamiento**, no una puntuación de calidad.

Podemos tener modelos base extremadamente grandes y técnicamente sofisticados.

Por ejemplo, conceptualmente:

```text
Modelo pequeño
     │
     ├── puede ser base
     │
     └── puede estar ajustado para instrucciones


Modelo grande
     │
     ├── puede ser base
     │
     └── puede estar ajustado para instrucciones
```

Por tanto:

> **Tamaño y tipo de entrenamiento son dimensiones diferentes.**

---

# 4. Modelo base frente a modelo instruccional

Esta diferencia será fundamental durante todo el repositorio.

Podemos imaginar dos modelos:

```text
MODELO A
Modelo Base

MODELO B
Modelo ajustado para seguir instrucciones
```

Supongamos que escribimos:

```text
Explica qué es Python.
```

Un modelo instruccional puede interpretar esto como una orden:

```text
USUARIO
   │
   ↓
"Explica qué es Python"
   │
   ↓
Interpretar intención
   │
   ↓
Generar explicación
```

Un modelo base puede comportarse de una manera diferente.

Su entrenamiento puede hacer que interprete la entrada principalmente como texto que debe continuar.

Por ejemplo:

```text
Explica qué es Python.

Python es un lenguaje de programación...
```

Pero también podría continuar de otras maneras:

```text
Explica qué es Python.

Python fue creado por Guido van Rossum...
```

o incluso producir una estructura que parezca el comienzo de un documento.

La diferencia fundamental es:

```text
MODELO BASE

entrada → continuación probable


MODELO INSTRUCTION

entrada → interpretación de una instrucción → respuesta
```

Esta diferencia será especialmente importante cuando estudiemos **prompt engineering**.

---

# 5. ¿Por qué existe un modelo base?

El modelo base constituye una etapa fundamental de la construcción de muchos sistemas modernos de IA.

Un esquema simplificado puede ser:

```text
                  DATOS
                    │
                    ↓
              PREENTRENAMIENTO
                    │
                    ↓
             ┌───────────────┐
             │ MODELO BASE   │
             └───────────────┘
                    │
                    ↓
          AJUSTES POSTERIORES
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
   Instruction Tuning      Otros ajustes
          │
          ↓
       ALINEACIÓN
          │
          ↓
    MODELO PARA USO
```

No todos los sistemas siguen exactamente esta secuencia ni utilizan las mismas técnicas.

Pero como modelo mental inicial resulta muy útil.

---

# 6. Preentrenamiento

El **preentrenamiento** (*pretraining*) es una de las etapas fundamentales para construir un modelo base.

Durante esta etapa, el modelo procesa enormes cantidades de datos y modifica sus parámetros para mejorar su capacidad de predecir los datos de entrenamiento.

Una representación simplificada:

```text
Texto
  ↓
Tokenización
  ↓
Tokens
  ↓
Modelo
  ↓
Predicción
  ↓
Comparación con el objetivo
  ↓
Error
  ↓
Actualización de parámetros
  ↓
Nueva predicción
```

Este proceso se repite una enorme cantidad de veces.

---

# 7. ¿Qué aprende un modelo base?

Aquí aparece una de las cuestiones más importantes.

Un modelo base no recibe necesariamente una tabla explícita como:

```text
Python → lenguaje de programación

Madrid → capital de España

2 + 2 → 4
```

Su aprendizaje ocurre mediante la optimización de millones, miles de millones o incluso más parámetros, dependiendo del modelo.

Durante el entrenamiento puede desarrollar representaciones internas capaces de capturar relaciones complejas.

Podemos representarlo conceptualmente:

```text
                 DATOS
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
    lenguaje      código     matemáticas
       │           │           │
       └───────────┼───────────┘
                   ↓
             REPRESENTACIONES
                   │
                   ↓
                MODELO
```

Esto no significa que el modelo "entienda" los datos exactamente como una persona.

La palabra **comprensión** debe utilizarse con cuidado.

En términos técnicos, estamos hablando de representaciones internas y transformaciones aprendidas mediante optimización.

---

# 8. Parámetros

Los parámetros son valores numéricos aprendidos durante el entrenamiento.

Podemos imaginar un modelo como una enorme función matemática:

```text
                    MODELO

       entrada ───────────────→ salida
                    │
                    │
              parámetros
                    │
             θ₁ θ₂ θ₃ ... θₙ
```

Durante el entrenamiento se modifican esos parámetros para reducir el error de las predicciones.

Una formulación simplificada sería:

$$
\theta_{nuevo} =
\theta_{actual} - \eta \nabla L(\theta)
$$

donde:

* \(\theta\) representa los parámetros;
* \(\eta\) representa la tasa de aprendizaje;
* \(L\) representa la función de pérdida;
* \(\nabla L\) representa el gradiente.

No es necesario dominar todavía el cálculo diferencial para comprender Ingeniería de Prompt.

Sin embargo, sí es importante conocer la idea:

> **El prompt no cambia directamente los parámetros del modelo durante una conversación normal.**

El prompt modifica la **entrada y el contexto de inferencia**.

---

# 9. Modelo base y conocimiento

Existe otra confusión frecuente:

> "El modelo base tiene una base de datos interna con todo lo que sabe."

Esta explicación es demasiado simplificada.

Un modelo neuronal no funciona normalmente como una base de datos tradicional en la que cada conocimiento está almacenado como:

```text
clave → valor
```

En cambio, el conocimiento aprendido está distribuido en los parámetros y representaciones del modelo.

Podemos contrastarlo:

### Base de datos tradicional

```text
ID → registro

1023 → Jorge
1024 → María
1025 → Carlos
```

### Modelo neuronal

```text
datos
  ↓
entrenamiento
  ↓
parámetros distribuidos
  ↓
representaciones
  ↓
predicción
```

Esto explica parcialmente por qué un modelo puede:

* generalizar;
* combinar conceptos;
* producir respuestas nuevas;
* cometer errores;
* generar información incorrecta.

---

# 10. El modelo base no es una base de datos

Esta distinción será fundamental cuando estudiemos posteriormente:

* RAG;
* bases vectoriales;
* búsqueda;
* herramientas;
* agentes;
* memoria;
* Context Engineering.

Podemos resumirlo así:

```text
MODELO

Aprende patrones y representaciones
              │
              ↓
         parámetros


BASE DE DATOS

Almacena registros explícitos
              │
              ↓
         información


RAG

Recupera información externa
              │
              ↓
       contexto para el modelo
```

Un sistema moderno puede combinar los tres.

---

# 11. ¿Qué ocurre cuando enviamos un prompt?

Supongamos:

```text
Explica la segunda ley de Newton a un estudiante de 12 años.
```

El modelo no recibe directamente "significado humano".

Primero existe un proceso de transformación.

```text
PROMPT
  │
  ↓
TOKENIZACIÓN
  │
  ↓
TOKENS
  │
  ↓
REPRESENTACIONES
  │
  ↓
TRANSFORMER
  │
  ↓
DISTRIBUCIÓN DE PROBABILIDADES
  │
  ↓
SELECCIÓN DE TOKEN
  │
  ↓
NUEVO TOKEN
  │
  ↓
REPETICIÓN
  │
  ↓
RESPUESTA
```

Esto conecta directamente con los capítulos anteriores:

```text
Tokens
Embeddings
Transformer
Attention
Inferencia
Sampling
```

El prompt es solamente una parte del sistema.

---

# 12. El mismo prompt puede producir comportamientos diferentes

Consideremos:

```text
Explica la fotosíntesis.
```

Podemos enviar exactamente el mismo texto a diferentes modelos.

```text
                 PROMPT
                    │
          "Explica la fotosíntesis"
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
      Modelo A    Modelo B    Modelo C
        │           │           │
        ↓           ↓           ↓
     respuesta   respuesta   respuesta
```

Las respuestas pueden diferir debido a múltiples factores:

* arquitectura;
* parámetros;
* datos de entrenamiento;
* entrenamiento posterior;
* capacidad de contexto;
* tokenizer;
* configuración de inferencia;
* temperatura;
* sampling;
* instrucciones del sistema;
* herramientas disponibles;
* información proporcionada en el contexto.

Por eso:

> **Un prompt no tiene un comportamiento universal independiente del modelo.**

---

# 13. El prompt como entrada de un sistema

Una forma más precisa de pensar la Ingeniería de Prompt es:

```text
                   SISTEMA DE IA
┌──────────────────────────────────────────┐
│                                          │
│   Modelo                                 │
│      ↑                                   │
│      │                                   │
│   Contexto                               │
│      ↑                                   │
│      │                                   │
│   Prompt                                 │
│      ↑                                   │
│      │                                   │
│   Herramientas / datos / instrucciones   │
│                                          │
└──────────────────────────────────────────┘
                    │
                    ↓
                 SALIDA
```

Por esta razón, un ingeniero de prompt no debería preguntarse solamente:

> "¿Qué palabras debo escribir?"

Debe comenzar a preguntarse:

> "¿Qué sistema está interpretando estas palabras?"

---

# 14. Modelo base y capacidad de seguir instrucciones

Una característica importante de los modelos base es que **no necesariamente están optimizados para comportarse como asistentes**.

Imaginemos un modelo entrenado principalmente con grandes cantidades de texto.

Podemos proporcionarle:

```text
Escribe una función Python que calcule el factorial.
```

Un modelo instruccional puede producir directamente:

```python
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)
```

Un modelo base podría interpretar la entrada como una secuencia que debe continuar.

Por ejemplo:

```text
Escribe una función Python que calcule el factorial.

Una función para calcular el factorial puede implementarse...
```

o generar código, dependiendo del entrenamiento.

La diferencia no es simplemente "inteligencia".

Es también una diferencia de **objetivo de entrenamiento y comportamiento aprendido**.

---

# 15. Modelo base ≠ modelo conversacional

Este punto es especialmente importante.

Un modelo base puede tener una gran capacidad lingüística y aun así no comportarse como:

```text
Usuario
   ↕
Asistente
```

El comportamiento conversacional normalmente requiere etapas adicionales de entrenamiento y diseño del sistema.

Una representación simplificada:

```text
                 MODELO BASE
                      │
                      ↓
            Instruction Tuning
                      │
                      ↓
                Alineación
                      │
                      ↓
              Modelo asistente
```

Más adelante estudiaremos cada etapa.

---

# 16. ¿Qué es entonces un modelo instruccional?

Un modelo **instruction-tuned** ha recibido entrenamiento adicional orientado a responder siguiendo instrucciones.

Por ejemplo:

```text
INSTRUCCIÓN

Resume este texto en cinco puntos.
```

El modelo aprende patrones de interacción como:

```text
instrucción
     ↓
interpretación
     ↓
respuesta apropiada
```

Esto no significa que siga perfectamente todas las instrucciones.

Puede:

* interpretar mal una petición;
* ignorar una restricción;
* producir información incorrecta;
* entrar en conflicto con otras instrucciones;
* sufrir problemas con contextos largos;
* responder de forma diferente dependiendo del modelo.

Por eso la Ingeniería de Prompt sigue siendo necesaria.

---

# 17. Modelo base, instruction tuning y alineación

Podemos establecer una distinción conceptual:

| Etapa              | Objetivo principal                                                     |
| ------------------ | ---------------------------------------------------------------------- |
| Preentrenamiento   | Aprender patrones a partir de grandes cantidades de datos              |
| Modelo base        | Resultado del preentrenamiento                                         |
| Instruction Tuning | Mejorar la capacidad de seguir instrucciones                           |
| Alineación         | Orientar el comportamiento hacia determinados objetivos y preferencias |
| Sistema de IA      | Integrar modelo, contexto, herramientas y reglas                       |

Es importante no confundir estos conceptos.

Un **modelo** y un **sistema de IA** no son necesariamente lo mismo.

---

# 18. Modelo frente a sistema

Esta diferencia será fundamental en los niveles posteriores.

Podemos tener:

```text
                 MODELO
                   │
                   ↓
              LLM / VLM
                   │
                   +
          ┌────────┼────────┐
          ↓        ↓        ↓
       Prompt    RAG     Tools
          │        │        │
          └────────┼────────┘
                   ↓
              SISTEMA IA
```

Por ejemplo, un chatbot empresarial podría utilizar:

```text
LLM
 +
prompt del sistema
 +
documentación empresarial
 +
base de datos
 +
buscador
 +
API de CRM
 +
memoria
 +
reglas de seguridad
```

El LLM es solamente uno de los componentes.

---

# 19. ¿Por qué esto importa para Ingeniería de Prompt?

Porque una misma instrucción puede funcionar de forma diferente dependiendo de dónde se ejecute.

Por ejemplo:

```text
"Resume el documento."
```

En un modelo sin contexto adicional:

```text
¿Qué documento?
```

En un sistema con un archivo adjunto:

```text
PROMPT
  +
DOCUMENTO
  ↓
RESUMEN
```

En un sistema RAG:

```text
PROMPT
  +
CONSULTA
  ↓
RECUPERACIÓN
  ↓
DOCUMENTOS
  ↓
CONTEXTO
  ↓
MODELO
  ↓
RESPUESTA
```

En un agente:

```text
PROMPT
  ↓
MODELO
  ↓
DECISIÓN
  ↓
HERRAMIENTA
  ↓
RESULTADO
  ↓
MODELO
  ↓
DECISIÓN
  ↓
RESPUESTA
```

Por tanto, el mismo texto puede tener comportamientos radicalmente diferentes dependiendo del sistema.

---

# 20. Una analogía sencilla

Imagina que tienes un automóvil.

El **motor** es comparable al modelo.

Pero un automóvil completo también tiene:

* transmisión;
* dirección;
* frenos;
* sensores;
* combustible;
* computadora;
* conductor.

Podemos representarlo:

```text
MOTOR
  │
  ↓
MODELO

AUTOMÓVIL
  │
  ├── motor
  ├── sensores
  ├── controles
  ├── navegación
  └── conductor

SISTEMA DE IA
  │
  ├── modelo
  ├── contexto
  ├── herramientas
  ├── memoria
  ├── reglas
  └── usuario
```

Un motor potente no convierte automáticamente a un automóvil en un buen sistema de transporte.

De la misma manera:

> **Un modelo potente no garantiza que una aplicación de IA esté bien diseñada.**

---

# 21. Modelos base abiertos y propietarios

Los modelos base pueden aparecer dentro de diferentes ecosistemas.

### Modelos con pesos disponibles

Algunos proyectos permiten acceder a sus pesos bajo determinadas licencias.

Esto facilita actividades como:

* investigación;
* evaluación;
* adaptación;
* despliegue local;
* fine-tuning.

Pero:

> **"Pesos disponibles" no significa necesariamente "software completamente libre".**

Hay que revisar siempre:

* licencia;
* restricciones comerciales;
* condiciones de uso;
* disponibilidad de datos;
* arquitectura;
* pesos;
* código;
* tokenizer.

---

# 22. Modelo, pesos, arquitectura y checkpoint

Estos conceptos suelen mezclarse.

### Arquitectura

Describe cómo está construido el modelo.

Ejemplo:

```text
Transformer
```

### Pesos

Son los valores numéricos aprendidos durante el entrenamiento.

```text
θ₁
θ₂
θ₃
...
θₙ
```

### Checkpoint

Es una versión guardada del estado del entrenamiento.

```text
Entrenamiento
    │
    ├── checkpoint 1
    ├── checkpoint 2
    ├── checkpoint 3
    └── checkpoint final
```

### Modelo

En la práctica, cuando hablamos de un modelo desplegable, normalmente nos referimos al conjunto de componentes necesarios para realizar inferencia, aunque la composición exacta depende del ecosistema.

---

# 23. Modelo base y tokenizer

El tokenizer merece especial atención porque conecta directamente el texto con el modelo.

Supongamos:

```text
"inteligencia artificial"
```

El tokenizer puede dividirlo en diferentes unidades dependiendo del modelo.

Conceptualmente:

```text
Texto
   ↓
Tokenizer
   ↓
[token₁, token₂, token₃, ...]
   ↓
Modelo
```

Dos modelos diferentes pueden utilizar tokenizadores diferentes.

Por lo tanto:

```text
MISMO TEXTO
     │
     ├── Tokenizer A → tokens diferentes
     │
     └── Tokenizer B → tokens diferentes
```

Esto puede afectar:

* cantidad de tokens;
* coste;
* longitud efectiva del contexto;
* representación de idiomas;
* código;
* caracteres especiales.

---

# 24. Modelo base y contexto

El modelo base no recibe únicamente el último mensaje.

Durante la inferencia puede recibir una secuencia completa de tokens.

Conceptualmente:

```text
┌────────────────────────────────────┐
│             CONTEXTO               │
│                                    │
│ instrucciones                      │
│ conversación                       │
│ documentos                         │
│ ejemplos                           │
│ herramientas                       │
│ prompt                             │
│                                    │
└────────────────────────────────────┘
                  │
                  ↓
                MODELO
                  │
                  ↓
                SALIDA
```

Esto introduce una idea fundamental:

> **El comportamiento del modelo depende de la interacción entre modelo y contexto.**

Esta idea será desarrollada ampliamente en el Nivel 5: **Context Engineering**.

---

# 25. Modelo base y comportamiento emergente

Los modelos grandes pueden mostrar capacidades que no se describen fácilmente como simples reglas programadas manualmente.

Por ejemplo:

* generación de código;
* traducción;
* clasificación;
* resumen;
* transformación de texto;
* resolución de determinados problemas;
* reconocimiento de patrones.

Sin embargo, debemos ser cuidadosos con la palabra **emergencia**.

En investigación de modelos de lenguaje existe un debate importante sobre cómo medir y explicar algunas aparentes capacidades emergentes.

Por ello, no debemos enseñar:

> "Cuando el modelo crece, mágicamente aparecen nuevas capacidades."

Una explicación más rigurosa es:

> **Al aumentar escala, datos, capacidad del modelo y calidad del entrenamiento, pueden aparecer cambios cualitativos o mejoras en determinadas capacidades; la forma en que esas capacidades emergen y cómo deben medirse sigue siendo objeto de investigación.**

---

# 26. Modelo base y alucinaciones

Un modelo base puede generar texto estadísticamente plausible sin que ese texto sea necesariamente verdadero.

Por ejemplo:

```text
Pregunta:
¿Quién descubrió X?
```

El modelo puede generar:

```text
X fue descubierto por...
```

aunque la afirmación sea incorrecta.

Esto ocurre porque:

```text
probabilidad lingüística
        ≠
verdad factual
```

Esta distinción será fundamental cuando estudiemos:

* RAG;
* herramientas;
* evaluación;
* grounding;
* seguridad;
* agentes.

---

# 27. El modelo no "busca en Internet" automáticamente

Otro error conceptual frecuente es asumir:

> "El modelo sabe algo porque acaba de buscarlo."

Un modelo base tradicional puede generar una respuesta utilizando únicamente sus parámetros y el contexto recibido.

Un sistema conectado a Internet puede funcionar de otra manera:

```text
Usuario
  ↓
Modelo
  ↓
Buscador
  ↓
Internet
  ↓
Resultados
  ↓
Contexto
  ↓
Modelo
  ↓
Respuesta
```

La capacidad de búsqueda pertenece al **sistema**, no necesariamente al modelo base.

---

# 28. Modelo base + herramientas

Podemos extender el sistema:

```text
                 MODELO
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
      Web         Python     Base de datos
        │          │          │
        └──────────┼──────────┘
                   ↓
                resultados
                   │
                   ↓
                 modelo
                   │
                   ↓
               respuesta
```

Aquí comienza a desaparecer la frontera entre:

```text
prompt
```

y

```text
sistema
```

Esto será esencial cuando lleguemos a **Tool Calling, agentes y sistemas de IA**.

---

# 29. Ejemplo completo

Supongamos que una empresa quiere crear un asistente para auditoría.

El usuario pregunta:

```text
Analiza este archivo y encuentra anomalías.
```

Un modelo base por sí solo podría no tener acceso al archivo.

El sistema completo podría funcionar así:

```text
                USUARIO
                   │
                   ↓
               PROMPT
                   │
                   ↓
             APLICACIÓN
                   │
          ┌────────┴────────┐
          ↓                 ↓
       ARCHIVO             REGLAS
          │                 │
          └────────┬────────┘
                   ↓
              PROCESAMIENTO
                   │
                   ↓
               CONTEXTO
                   │
                   ↓
                 LLM
                   │
          ┌────────┴────────┐
          ↓                 ↓
       análisis          herramientas
          │                 │
          └────────┬────────┘
                   ↓
                resultado
                   │
                   ↓
               evaluación
                   │
                   ↓
              respuesta
```

El prompt es importante.

Pero el prompt por sí solo **no constituye el sistema de auditoría**.

---

# 30. Una ecuación conceptual

Podemos representar el comportamiento de un sistema de IA mediante una función conceptual:

$$
Y = F(M, C, P, T, S, I)
$$

donde:

* \(Y\) = salida;
* \(M\) = modelo;
* \(C\) = contexto;
* \(P\) = prompt;
* \(T\) = herramientas disponibles;
* \(S\) = configuración del sistema;
* \(I\) = configuración de inferencia.

No es una ecuación física del modelo.

Es un **modelo conceptual de ingeniería**.

Su propósito es recordar que:

$$
\boxed{\text{Salida} \neq f(\text{Prompt})}
$$

Una formulación más realista sería:

$$
\boxed{\text{Salida} =
f(\text{Modelo},\text{Contexto},\text{Prompt},\text{Inferencia},\text{Sistema})}
$$

Esta idea será una de las bases de todo el repositorio.

---

# 31. Implicaciones para Prompt Engineering

Llegamos ahora al punto central.

Si dos modelos reciben:

```text
Analiza este contrato.
```

no podemos asumir que responderán igual.

Podemos tener:

```text
                 MISMO PROMPT
                      │
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
    Modelo A       Modelo B       Modelo C
       │              │              │
       ↓              ↓              ↓
    respuesta      respuesta      respuesta
       │              │              │
       └──────────────┼──────────────┘
                      ↓
                  DIFERENCIAS
```

Las diferencias pueden venir de:

* entrenamiento;
* arquitectura;
* tamaño;
* tokenizer;
* fine-tuning;
* alignment;
* contexto;
* ventana de contexto;
* inferencia;
* sampling;
* herramientas;
* restricciones del sistema.

Por eso una técnica de prompting que funciona perfectamente en un modelo puede funcionar peor en otro.

---

# 32. Error común: crear prompts universales

Una de las ideas que debemos cuestionar es:

> "Existe un prompt perfecto que funciona para cualquier IA."

No existe una garantía general de este tipo.

Un prompt puede ser:

```text
excelente para un modelo
```

y:

```text
mediocre para otro
```

o incluso:

```text
contraproducente en otro sistema.
```

La Ingeniería de Prompt profesional debe estudiar la interacción:

```text
PROMPT
   +
MODELO
   +
CONTEXTO
   +
INFERENCIA
   +
SISTEMA
```

---

# 33. Ejemplo práctico

Supongamos que tenemos tres sistemas:

```text
Sistema A
LLM generalista

Sistema B
LLM especializado en código

Sistema C
LLM de razonamiento
```

Utilizamos:

```text
Resuelve este problema y explica el procedimiento.
```

El resultado puede variar porque cada sistema ha sido diseñado y entrenado con diferentes objetivos.

El ingeniero de IA debe preguntarse:

```text
¿Qué modelo estoy utilizando?

¿Qué entrenamiento recibió?

¿Qué contexto recibe?

¿Qué capacidad de razonamiento tiene?

¿Qué herramientas posee?

¿Cómo realiza la inferencia?

¿Qué restricciones tiene?
```

Solo después:

```text
¿Cómo debo diseñar el prompt?
```

---

# 34. Modelo mental para el alumno

A partir de este capítulo, el alumno debe abandonar progresivamente este modelo:

```text
PROMPT
  ↓
IA
  ↓
RESPUESTA
```

y reemplazarlo por:

```text
                 SISTEMA DE IA

        ┌─────────────────────────┐
        │         MODELO          │
        └────────────┬────────────┘
                     │
      ┌──────────────┼──────────────┐
      ↓              ↓              ↓
   contexto        prompt       herramientas
      │              │              │
      └──────────────┼──────────────┘
                     ↓
                 INFERENCIA
                     │
                     ↓
                   SALIDA
                     │
                     ↓
                 EVALUACIÓN
```

Este cambio de perspectiva es fundamental.

---

# 35. Lo que debe recordar un programador

Un programador puede pensar inicialmente:

```python
respuesta = modelo(prompt)
```

Pero un sistema real puede ser más parecido a:

```python
contexto = recuperar_contexto(consulta)

respuesta = modelo(
    system_prompt=reglas,
    context=contexto,
    user_prompt=consulta,
    tools=herramientas,
    generation_config=configuracion
)

resultado = evaluar(respuesta)
```

El código exacto depende de la plataforma.

La idea importante es:

> **La ingeniería de IA trabaja sobre el sistema completo, no solamente sobre la cadena de texto llamada prompt.**

---

# 36. Lo que debe recordar un no programador

No necesitas saber Python para comprender esta idea.

Puedes pensar:

```text
Modelo
   ↓
"cerebro matemático"

Contexto
   ↓
"información que recibe"

Prompt
   ↓
"instrucción o solicitud"

Herramientas
   ↓
"capacidades externas"

Inferencia
   ↓
"proceso mediante el cual genera la salida"

Respuesta
   ↓
"resultado producido por el sistema"
```

La analogía no es técnicamente perfecta, pero permite construir un modelo mental inicial.

---

# 37. Lo que debe recordar un estudiante avanzado

A nivel avanzado debemos evitar antropomorfismos.

No debemos describir un modelo simplemente como:

> "Un cerebro que entiende instrucciones."

Es más preciso describirlo como un sistema parametrizado que transforma una secuencia de entradas, bajo una arquitectura y configuración determinadas, en una distribución sobre posibles salidas.

Conceptualmente:

$$
P(y|x,\theta)
$$

donde:

* \(x\) representa la entrada;
* \(y\) representa una posible salida;
* \(\theta\) representa los parámetros.

Durante la generación autoregresiva:

$$
P(y_1,\ldots,y_n|x)
=
\prod_{t=1}^{n}
P(y_t|x,y_{<t})
$$

Esto significa que la generación puede entenderse como una sucesión de predicciones condicionadas por el contexto disponible.

Esta formulación será retomada cuando estudiemos con mayor profundidad:

* inferencia;
* sampling;
* temperatura;
* modelos de razonamiento;
* decodificación.

---

# 38. Preguntas que el ingeniero debe hacer

Cuando un prompt no funciona, no debemos modificar inmediatamente las palabras.

Primero debemos diagnosticar.

### Pregunta 1

¿El problema está en el modelo?

```text
¿Tiene la capacidad necesaria?
```

### Pregunta 2

¿El problema está en el contexto?

```text
¿Recibió la información necesaria?
```

### Pregunta 3

¿El problema está en el prompt?

```text
¿La instrucción es ambigua?
```

### Pregunta 4

¿El problema está en la inferencia?

```text
¿La configuración de generación está afectando el resultado?
```

### Pregunta 5

¿El problema está en el sistema?

```text
¿Faltan herramientas, recuperación de información o validación?
```

Esto evita el error de intentar resolver todos los problemas modificando el prompt.

---

# 39. Diagnóstico profesional

Podemos crear este flujo:

```text
                 RESPUESTA INCORRECTA
                         │
                         ↓
                ¿Qué componente falla?
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       MODELO         CONTEXTO        PROMPT
          │              │              │
          └──────────────┼──────────────┘
                         ↓
                    INFERENCIA
                         │
                         ↓
                     SISTEMA
                         │
                         ↓
                    HERRAMIENTAS
                         │
                         ↓
                    EVALUACIÓN
```

Esto representa una transición importante:

> **De Prompt Engineering a System Engineering.**

---

# 40. Relación con los siguientes capítulos

Este capítulo prepara al alumno para estudiar:

```text
01 — Modelos Base
        │
        ↓
02 — Instruction-Tuned
        │
        ↓
03 — Dense Transformers
        │
        ↓
04 — Mixture of Experts
        │
        ↓
05 — Modelos de Razonamiento
        │
        ↓
06 — Modelos Multimodales
        │
        ↓
07 — Modelos de Código
        │
        ↓
08 — Modelos Matemáticos
        │
        ↓
09 — Long Context
        │
        ↓
10 — Modelos Híbridos
        │
        ↓
11 — Comparación de Arquitecturas
```

Cada capítulo deberá responder una pregunta fundamental:

> **¿Cómo cambia el comportamiento del sistema y qué consecuencias tiene esto para la Ingeniería de Prompt?**

---

# 41. Resumen

Un **modelo base** es el resultado de una etapa fundamental de entrenamiento en la que el modelo aprende patrones y representaciones a partir de grandes cantidades de datos.

Debemos distinguir:

```text
MODELO BASE
    ↓
resultado del preentrenamiento


INSTRUCTION-TUNED
    ↓
modelo ajustado para seguir instrucciones


SISTEMA DE IA
    ↓
modelo + contexto + prompt + herramientas
+ configuración + lógica de aplicación
```

Las ideas fundamentales son:

1. **Modelo base no significa modelo malo o pequeño.**
2. **El modelo base y el modelo instruccional tienen objetivos de entrenamiento diferentes.**
3. **Los parámetros contienen conocimiento aprendido de forma distribuida; no son una base de datos tradicional.**
4. **El prompt no modifica los parámetros durante una inferencia normal.**
5. **El mismo prompt puede producir resultados diferentes en diferentes modelos.**
6. **La arquitectura y el entrenamiento afectan el comportamiento del modelo.**
7. **Un modelo no es necesariamente lo mismo que un sistema de IA.**
8. **Las herramientas, el contexto y la configuración de inferencia también afectan la respuesta.**
9. **No existe un prompt universal que garantice el mismo comportamiento en todos los modelos.**
10. **La Ingeniería de Prompt debe evolucionar hacia Ingeniería de Sistemas de IA.**

La idea que debe quedar grabada es:

$$
\boxed{
\text{PROMPT} \neq \text{SISTEMA DE IA}
}
$$

y:

$$
\boxed{
\text{COMPORTAMIENTO}
=
f(
\text{MODELO},
\text{CONTEXTO},
\text{PROMPT},
\text{INFERENCIA},
\text{HERRAMIENTAS},
\text{SISTEMA}
)
}
$$

---

# 42. Conceptos que el alumno debe dominar

Al finalizar este capítulo, el alumno debería poder explicar con sus propias palabras:

* qué es un modelo base;
* qué es preentrenamiento;
* qué son los parámetros;
* qué diferencia existe entre modelo base e instruction-tuned;
* por qué un modelo no es una base de datos;
* qué diferencia existe entre modelo y sistema;
* por qué el mismo prompt puede producir resultados diferentes;
* qué relación existe entre modelo, contexto y prompt;
* por qué las herramientas cambian las capacidades de un sistema;
* por qué no existe un prompt universal;
* por qué la Ingeniería de Prompt forma parte de una disciplina más amplia: la **Ingeniería de Sistemas de IA**.

---

## Idea final

El objetivo de este nivel no es enseñar al alumno a memorizar nombres de modelos.

Es enseñarle a pensar como ingeniero:

```text
NO:

"¿Qué prompt uso?"

SINO:

"¿Qué modelo tengo?"

        ↓

"¿Cómo fue entrenado?"

        ↓

"¿Qué arquitectura utiliza?"

        ↓

"¿Qué contexto recibe?"

        ↓

"¿Cómo realiza la inferencia?"

        ↓

"¿Qué herramientas tiene?"

        ↓

"¿Qué restricciones existen?"

        ↓

"¿Qué prompt necesita este sistema?"

        ↓

"¿Cómo voy a evaluar el resultado?"
```

Ese cambio de mentalidad constituye uno de los fundamentos de la **Ingeniería de Prompt profesional**.
