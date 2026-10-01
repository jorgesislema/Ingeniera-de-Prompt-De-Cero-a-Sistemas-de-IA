# ¿Qué es un LLM?

> **Nivel:** Fundamentos técnicos de LLM
> **Ruta:** Ingeniería de Prompt — De Cero a Sistemas de IA
> **Prerequisitos:** `00-FUNDAMENTOS/`
> **Nivel académico:** Inicial → Avanzado
> **Actualizado:** septiembre de 2026

---

## 1. Objetivo

Al terminar este capítulo, el estudiante podrá:

* Explicar qué significa **LLM**.
* Diferenciar un LLM de un modelo tradicional de Machine Learning.
* Comprender por qué un LLM puede trabajar con lenguaje natural.
* Explicar, a nivel conceptual, qué ocurre desde que recibe una entrada hasta que genera una salida.
* Comprender la relación entre **tokens, parámetros, contexto, entrenamiento e inferencia**.
* Entender por qué dos LLM pueden responder de manera diferente al mismo prompt.
* Comprender por qué un prompt no funciona de forma aislada.
* Identificar las principales etapas que dieron origen a los LLM modernos.
* Diferenciar **modelo base**, **modelo instruido** y sistemas que incorporan herramientas.
* Prepararse para estudiar Transformer, Attention, pretraining, fine-tuning e instruction tuning.

---

# 2. ¿Qué significa LLM?

**LLM** significa:

> **Large Language Model**

En español:

> **Modelo de Lenguaje de Gran Escala**

Un LLM es un modelo de inteligencia artificial entrenado para trabajar con secuencias de lenguaje y aprender patrones estadísticos y representaciones que le permiten, entre otras cosas:

* completar texto;
* generar texto;
* responder preguntas;
* resumir información;
* traducir;
* clasificar contenido;
* transformar texto;
* escribir código;
* seguir instrucciones;
* mantener una interacción basada en el contexto proporcionado.

Ejemplos conocidos de familias de modelos de lenguaje incluyen:

* GPT;
* Claude;
* Gemini;
* Llama;
* Mistral;
* Qwen;
* DeepSeek;
* Gemma.

> **Importante:** que un modelo sea un LLM no significa que todos los LLM funcionen exactamente igual.

Pueden utilizar diferentes arquitecturas, datos, objetivos de entrenamiento, tamaños, métodos de ajuste, sistemas de inferencia y mecanismos adicionales.

---

# 3. ¿Por qué se llama "modelo de lenguaje"?

La palabra **lenguaje** no significa únicamente español, inglés o francés.

En el contexto de los LLM, el modelo aprende estructuras presentes en secuencias de información.

Por ejemplo:

```text
El cielo es de color ___
```

Un modelo de lenguaje puede asignar una probabilidad elevada a:

```text
azul
```

Pero también podría considerar otras posibilidades dependiendo del contexto:

```text
gris
```

```text
rojo
```

```text
oscuro
```

El modelo no necesita almacenar una regla explícita como:

```text
SI aparece "cielo"
ENTONCES responder "azul"
```

Aprende representaciones y relaciones a partir de grandes cantidades de datos durante el entrenamiento.

---

# 4. Una definición técnica

Una definición conceptual útil es:

> **Un LLM es un modelo de aprendizaje automático entrenado sobre grandes cantidades de datos lingüísticos para aprender representaciones y patrones de lenguaje y utilizarlos durante la inferencia para producir o transformar secuencias de tokens.**

Esta definición contiene varias ideas importantes:

```text
LLM
│
├── Modelo de aprendizaje automático
│
├── Datos
│
├── Representaciones
│
├── Patrones
│
├── Tokens
│
├── Entrenamiento
│
└── Inferencia
```

Cada una será estudiada con mayor profundidad en los siguientes capítulos.

---

# 5. LLM no significa "IA que piensa como un humano"

Esta distinción es fundamental.

Un LLM puede producir una respuesta que parece demostrar:

* razonamiento;
* conocimiento;
* planificación;
* comprensión;
* creatividad;
* análisis.

Sin embargo, no debemos asumir automáticamente que su funcionamiento interno es equivalente al pensamiento humano.

Por ejemplo, si preguntamos:

```text
Tengo 3 manzanas y compro 2 más.
¿Cuántas tengo?
```

El modelo puede generar:

```text
5
```

Esto demuestra que puede producir la respuesta correcta.

Pero no debemos concluir únicamente a partir de esa respuesta que el modelo utiliza exactamente el mismo proceso cognitivo que una persona.

### Principio fundamental

> **Comportamiento observable ≠ mecanismo interno idéntico al humano.**

Este principio será especialmente importante cuando estudiemos:

* razonamiento;
* chain-of-thought;
* modelos de razonamiento;
* agentes;
* evaluación;
* interpretabilidad.

---

# 6. El LLM como predictor de secuencias

Una forma sencilla de comprender un LLM es imaginarlo como un sistema que estima qué continuación resulta probable dada una secuencia de entrada.

Por ejemplo:

```text
La capital de Ecuador es
```

El modelo puede asignar probabilidades a diferentes continuaciones:

```text
Quito       → probabilidad alta
Guayaquil   → probabilidad menor
Cuenca      → probabilidad menor
...
```

El modelo utiliza esas probabilidades durante la generación.

Pero existe una precisión importante:

> Un LLM moderno no debe entenderse simplemente como un "autocompletador de palabras".

Trabaja con **tokens**, representaciones internas y relaciones entre elementos de una secuencia.

Además, durante el entrenamiento aprende estructuras mucho más complejas que asociaciones simples entre palabras.

---

# 7. Del texto a los tokens

Los LLM no reciben directamente el texto como nosotros lo vemos.

Por ejemplo:

```text
La inteligencia artificial transforma industrias.
```

se convierte mediante un proceso de tokenización en una secuencia de tokens.

De forma conceptual:

```text
Texto
  ↓
Tokenización
  ↓
Tokens
  ↓
Representaciones numéricas
  ↓
Procesamiento por el modelo
  ↓
Predicción
  ↓
Tokens generados
  ↓
Texto
```

La tokenización concreta depende del modelo y de su tokenizer.

Por esta razón, **no debemos asumir que un token equivale a una palabra**.

Puede representar:

* una palabra completa;
* parte de una palabra;
* signos;
* espacios o fragmentos;
* caracteres o combinaciones frecuentes.

El tema será estudiado con detalle en:

```text
02-Tokenizacion.md
```

---

# 8. ¿Dónde están los "conocimientos" del modelo?

Esta pregunta suele generar una confusión importante.

Un estudiante podría imaginar que un LLM contiene una enorme base de datos parecida a:

```text
Pregunta → Respuesta
```

No funciona de esa manera.

Durante el entrenamiento, el modelo modifica sus parámetros para aprender representaciones y relaciones estadísticas presentes en los datos.

De manera simplificada:

```text
DATOS
  ↓
ENTRENAMIENTO
  ↓
AJUSTE DE PARÁMETROS
  ↓
MODELO
```

Durante la inferencia:

```text
ENTRADA
  ↓
MODELO ENTRENADO
  ↓
PROBABILIDADES
  ↓
SELECCIÓN / DECODIFICACIÓN
  ↓
SALIDA
```

Por eso es más correcto hablar de **información codificada en los parámetros y representaciones aprendidas** que imaginar un archivo de texto gigante almacenado dentro del modelo.

---

# 9. Parámetros: una memoria que no funciona como una base de datos

En el capítulo `00-FUNDAMENTOS/06-Parametros.md` estudiamos qué son los parámetros.

Ahora debemos conectar ese concepto con los LLM.

Un LLM contiene una gran cantidad de parámetros.

Conceptualmente:

```text
Parámetros
      ↓
Representan patrones aprendidos
      ↓
Influyen en las predicciones
```

Durante el entrenamiento, los parámetros se ajustan para reducir el error asociado con el objetivo de entrenamiento.

Una simplificación matemática sería:

```text
Datos
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
```

Después del entrenamiento, los parámetros constituyen parte fundamental del modelo utilizado durante la inferencia.

---

# 10. ¿Un modelo más grande siempre es mejor?

No.

El número de parámetros es solamente **una característica** de un modelo.

El rendimiento depende también de factores como:

* calidad de los datos;
* diversidad de los datos;
* arquitectura;
* objetivo de entrenamiento;
* calidad del ajuste posterior;
* capacidad de razonamiento;
* contexto disponible;
* métodos de inferencia;
* herramientas utilizadas;
* especialización del modelo;
* técnicas de cuantización;
* eficiencia de la arquitectura;
* evaluación utilizada.

Por lo tanto:

```text
Más parámetros
≠
Automáticamente mejor modelo
```

Dos modelos con tamaños diferentes pueden presentar comportamientos distintos dependiendo de la tarea.

---

# 11. Modelo base y modelo instruido

No todos los modelos de lenguaje se encuentran en el mismo estado de entrenamiento.

Una distinción importante es:

```text
Modelo base
     ↓
Ajuste para seguir instrucciones
     ↓
Modelo instruido
```

## 11.1 Modelo base

Un modelo base ha sido entrenado principalmente para aprender patrones del lenguaje mediante objetivos de entrenamiento determinados.

Puede ser capaz de completar secuencias, pero no necesariamente está optimizado para comportarse como un asistente conversacional.

Por ejemplo, ante:

```text
Explica qué es Python.
```

un modelo base podría continuar el texto de muchas maneras.

No necesariamente responderá como un asistente.

---

## 11.2 Modelo instruido

Un modelo instruido ha recibido entrenamiento adicional para responder a instrucciones.

Por ejemplo:

```text
Usuario:
Explica qué es Python en tres frases.
```

El modelo está preparado para interpretar la petición como una instrucción y producir una respuesta acorde.

Esto es especialmente importante para Ingeniería de Prompt.

Un prompt diseñado para un modelo instruido puede tener un comportamiento muy diferente al utilizarlo con un modelo base.

---

# 12. El LLM no existe aislado

Esta es una de las ideas centrales de todo el repositorio.

Cuando un usuario utiliza un sistema como un asistente de IA, normalmente no interactúa únicamente con los parámetros del modelo.

Puede existir una arquitectura alrededor del modelo:

```text
Usuario
   ↓
Interfaz
   ↓
System Prompt
   ↓
Prompt del usuario
   ↓
Contexto
   ↓
Herramientas / memoria / documentos
   ↓
Modelo
   ↓
Inferencia
   ↓
Postprocesamiento
   ↓
Respuesta
```

Por eso:

> **El comportamiento que observa el usuario pertenece al sistema completo, no necesariamente al modelo aislado.**

Esta distinción será fundamental cuando estudiemos:

* prompt engineering;
* context engineering;
* RAG;
* tools;
* function calling;
* skills;
* agentes.

---

# 13. ¿Dónde entra el prompt?

El prompt proporciona información al modelo durante la inferencia.

Por ejemplo:

```text
Explica qué es un LLM para un estudiante de secundaria.
```

Podemos ampliar la instrucción:

```text
Actúa como profesor de inteligencia artificial.

Explica qué es un LLM para un estudiante
de secundaria.

Utiliza:
- lenguaje sencillo;
- un ejemplo cotidiano;
- una analogía;
- una explicación técnica breve.
```

La segunda entrada proporciona más restricciones.

Pero el resultado no depende solamente del texto del prompt.

Podemos representarlo así:

```text
                 MODELO
                   │
                   │
PROMPT ────────────┤
                   │
CONTEXTO ──────────┤
                   │
CONFIGURACIÓN ─────┤
                   │
HERRAMIENTAS ──────┤
                   ↓
               INFERENCIA
                   ↓
                SALIDA
```

Esta representación será uno de los principios fundamentales de Ingeniería de Prompt.

---

# 14. El mismo prompt puede producir resultados diferentes

Supongamos el prompt:

```text
Explica qué es un Transformer.
```

Podemos enviarlo a diferentes modelos:

```text
Modelo A
Modelo B
Modelo C
```

Las respuestas pueden ser diferentes.

Incluso utilizando el mismo modelo, pueden existir diferencias debido a:

* contexto;
* configuración de generación;
* temperatura;
* sampling;
* instrucciones del sistema;
* herramientas disponibles;
* historial de conversación;
* versión del modelo;
* límites de contexto;
* estado del sistema.

Por eso:

> **Un prompt no determina por sí solo una respuesta única.**

El prompt forma parte de un sistema de inferencia.

---

# 15. Un LLM genera texto de manera autoregresiva

En muchos LLM generativos, la generación ocurre de manera secuencial.

Supongamos:

```text
La capital de Ecuador es
```

El modelo produce un siguiente token.

Conceptualmente:

```text
La capital de Ecuador es → Quito
```

Después puede continuar:

```text
La capital de Ecuador es Quito → .
```

Y así sucesivamente.

Conceptualmente:

```text
Contexto inicial
      ↓
Predicción del siguiente token
      ↓
Se incorpora el token
      ↓
Nueva predicción
      ↓
Se incorpora el nuevo token
      ↓
...
```

Esto se conoce como **generación autoregresiva**.

No significa que todos los modelos modernos utilicen exactamente el mismo mecanismo para todas sus funciones, pero constituye un concepto fundamental para comprender la generación de texto en los LLM.

---

# 16. ¿Qué significa "predecir el siguiente token"?

Supongamos:

```text
El perro está corriendo por el
```

El modelo puede asignar diferentes probabilidades a posibles continuaciones:

```text
parque
patio
campo
camino
...
```

De manera simplificada:

```text
P(token | contexto)
```

Esto significa:

> Probabilidad de un token dado el contexto disponible.

Por ejemplo:

```text
P("parque" | "El perro está corriendo por el")
```

El modelo calcula distribuciones de probabilidad sobre posibles tokens.

Posteriormente, el mecanismo de decodificación determina cómo seleccionar el siguiente token.

Este tema será desarrollado en:

```text
06-Positional-Information.md
11-Inference.md
12-Sampling.md
```

---

# 17. Entonces, ¿el modelo simplemente predice?

Esta pregunta requiere una respuesta cuidadosa.

Sí, la predicción del siguiente token es un componente fundamental de muchos LLM generativos.

Pero decir:

> "El LLM solamente predice palabras"

es una simplificación excesiva.

Durante el entrenamiento, el modelo desarrolla representaciones internas que pueden capturar relaciones complejas entre:

* palabras;
* conceptos;
* estructuras sintácticas;
* patrones semánticos;
* código;
* información matemática;
* relaciones entre entidades;
* estructuras discursivas.

Además, los modelos modernos pueden incorporar entrenamiento adicional y mecanismos externos que permiten comportamientos más complejos.

Por eso es mejor decir:

> **Los LLM generativos utilizan predicción sobre tokens como mecanismo fundamental de generación, pero esa predicción se apoya en representaciones internas complejas aprendidas durante el entrenamiento.**

---

# 18. LLM y comprensión del lenguaje

Aquí debemos diferenciar dos conceptos:

### Comprensión funcional

El modelo puede comportarse como si hubiera comprendido una instrucción.

Por ejemplo:

```text
Resume este texto en cinco puntos.
```

Puede producir un resumen correcto.

### Comprensión humana

No debemos concluir automáticamente que el modelo posee una experiencia subjetiva o una comprensión humana del texto.

Por ello, en este repositorio utilizaremos preferentemente términos técnicos como:

* representación;
* inferencia;
* predicción;
* transformación;
* comportamiento;
* capacidad;
* patrón aprendido.

En lugar de asumir:

```text
"El modelo piensa exactamente como una persona."
```

---

# 19. LLM frente a un modelo tradicional de Machine Learning

No todos los modelos de Machine Learning son LLM.

Consideremos un modelo diseñado para detectar fraude bancario.

Entrada:

```text
edad
salario
monto
hora
país
historial
```

Salida:

```text
fraude = sí
```

Un LLM, en cambio, puede recibir:

```text
Analiza esta transacción y explica
por qué podría considerarse sospechosa.
```

y generar una respuesta textual.

La diferencia no es simplemente:

```text
ML = números
LLM = texto
```

La diferencia involucra:

* arquitectura;
* representación de los datos;
* objetivo de entrenamiento;
* escala;
* tipo de entrada;
* tipo de salida;
* capacidad de generación;
* procedimiento de inferencia.

---

# 20. LLM no significa necesariamente "chatbot"

Esta distinción es muy importante.

Un **LLM** es un modelo.

Un **chatbot** es una aplicación o interfaz que puede utilizar un modelo de lenguaje.

Podemos representarlo así:

```text
                 APLICACIÓN
                     │
              ┌──────┴──────┐
              │             │
           Interfaz      Herramientas
              │             │
              └──────┬──────┘
                     │
                    LLM
```

Por ejemplo:

```text
LLM
 ↓
Aplicación conversacional
 ↓
Chatbot
```

Pero también:

```text
LLM
 ↓
Generador de código
```

o:

```text
LLM
 ↓
Analizador de documentos
```

o:

```text
LLM
 ↓
Agente
```

Por tanto:

> **LLM y chatbot no son sinónimos.**

---

# 21. LLM multimodal

Los modelos modernos pueden incorporar capacidades que van más allá del texto.

Dependiendo del modelo y del sistema, pueden trabajar con modalidades como:

* texto;
* imágenes;
* audio;
* video;
* código;
* documentos.

Esto introduce un concepto importante:

> **Modelo de lenguaje** no necesariamente significa que el sistema completo trabaje exclusivamente con texto.

Puede existir una arquitectura multimodal en la que diferentes tipos de información sean transformados en representaciones que el modelo pueda procesar.

Conceptualmente:

```text
Texto ──────┐
            │
Imagen ─────┤
            ├──→ Representaciones ──→ Modelo ──→ Salida
Audio ──────┤
            │
Video ──────┘
```

La implementación concreta depende de la arquitectura del sistema.

---

# 22. El contexto cambia el comportamiento del LLM

Consideremos:

```text
Pregunta:
¿Qué significa "banco"?
```

Sin contexto puede existir ambigüedad.

Podría referirse a:

```text
banco financiero
```

o:

```text
banco para sentarse
```

Si agregamos:

```text
Estoy estudiando economía.
¿Qué significa "banco"?
```

el contexto cambia la interpretación.

Por tanto:

```text
Entrada
+
Contexto
↓
Interpretación / predicción
```

El contexto no es simplemente información adicional.

Puede modificar significativamente el comportamiento del modelo.

Este concepto será desarrollado en:

```text
13-Context-Window.md
```

---

# 23. LLM, memoria y contexto no son lo mismo

Otra confusión frecuente:

> "El modelo recuerda todo lo que hablamos."

No necesariamente.

Debemos distinguir entre:

### Parámetros

Información aprendida durante el entrenamiento.

### Contexto

Información proporcionada al modelo durante una determinada inferencia.

### Memoria de aplicación

Información que un sistema externo puede almacenar y volver a introducir posteriormente.

Conceptualmente:

```text
PARÁMETROS
   │
   │ conocimiento aprendido
   ↓
 MODELO
   ↑
   │
CONTEXTO
   │
   │ información disponible ahora
   │
MEMORIA / DATOS EXTERNOS
```

Una aplicación puede implementar memoria sin que esa información haya sido incorporada permanentemente a los parámetros del modelo.

---

# 24. ¿Puede un LLM aprender durante una conversación?

Aquí debemos utilizar con precisión la palabra **aprender**.

Durante una conversación, el modelo puede utilizar información previamente incluida en el contexto.

Por ejemplo:

```text
Usuario:
Mi empresa se llama Acme.

Usuario:
¿Qué debería mejorar Acme?
```

Si el nombre permanece disponible en el contexto, el modelo puede utilizarlo.

Pero eso no significa necesariamente que:

```text
los parámetros del modelo hayan sido modificados
```

Debemos distinguir:

```text
Usar información del contexto
        ≠
Modificar los parámetros del modelo
```

El entrenamiento y el aprendizaje persistente son mecanismos diferentes.

---

# 25. Alucinaciones

Un LLM puede producir información incorrecta con una apariencia convincente.

A este fenómeno se le suele denominar:

> **alucinación de IA** o **hallucination**.

Ejemplo:

```text
Usuario:
¿Cuál fue el artículo científico publicado por
"Juan Pérez" en 1874 sobre Transformers?
```

Si esa persona o publicación no existe, un modelo podría generar una respuesta inventada si no dispone de mecanismos adecuados para verificar la información.

Esto ocurre porque:

> **Generar una respuesta lingüísticamente plausible no garantiza que la información sea verdadera.**

Por eso los sistemas profesionales pueden incorporar:

* recuperación de documentos;
* búsqueda web;
* bases de datos;
* herramientas;
* validación;
* verificadores;
* sistemas de evaluación.

---

# 26. LLM + herramientas

Un LLM puede ser conectado a herramientas externas.

Por ejemplo:

```text
Usuario
  ↓
LLM
  ↓
Detecta necesidad de información
  ↓
Herramienta de búsqueda
  ↓
Resultado
  ↓
LLM
  ↓
Respuesta
```

Otra posibilidad:

```text
LLM
 ↓
Python
 ↓
Cálculo
 ↓
Resultado
 ↓
LLM
 ↓
Explicación
```

Esto cambia significativamente las capacidades del sistema.

Un modelo aislado y un sistema compuesto por:

```text
LLM + herramientas + datos + memoria + código
```

no deben considerarse equivalentes.

---

# 27. LLM + RAG

RAG significa:

> **Retrieval-Augmented Generation**

En español:

> **Generación aumentada mediante recuperación.**

El principio general consiste en recuperar información relevante desde una fuente externa y proporcionársela al modelo como contexto.

Conceptualmente:

```text
Pregunta
   ↓
Búsqueda / recuperación
   ↓
Documentos relevantes
   ↓
Contexto
   ↓
LLM
   ↓
Respuesta
```

Ejemplo empresarial:

```text
Usuario:
¿Cuál es la política de vacaciones de la empresa?
```

El sistema puede:

```text
1. Buscar la política correspondiente.
2. Recuperar el documento.
3. Introducir el contenido relevante en el contexto.
4. Solicitar al LLM una respuesta.
```

El LLM no necesita tener esa política almacenada permanentemente en sus parámetros.

---

# 28. LLM + agente

Un agente añade una capa adicional.

Conceptualmente:

```text
Objetivo
   ↓
LLM
   ↓
Decisión
   ↓
Herramienta
   ↓
Resultado
   ↓
LLM
   ↓
Nueva decisión
   ↓
...
```

Por ejemplo:

```text
Objetivo:
Investiga tres proveedores y compara sus características.
```

Un sistema agente podría:

```text
1. Planificar.
2. Buscar información.
3. Leer resultados.
4. Comparar datos.
5. Identificar información faltante.
6. Realizar nuevas búsquedas.
7. Generar el informe.
```

Esto será estudiado mucho más adelante.

No debemos confundir:

```text
LLM
```

con:

```text
Agente
```

Un agente puede utilizar un LLM como componente de razonamiento o decisión, pero el agente es el **sistema completo**.

---

# 29. Una visión de sistema

Llegados a este punto podemos construir una primera arquitectura conceptual:

```text
                    USUARIO
                       │
                       ↓
                  APLICACIÓN
                       │
                       ↓
                 INSTRUCCIONES
                       │
                       ↓
                  CONTEXTO
                 ↙    ↓    ↘
             memoria datos herramientas
                       │
                       ↓
                     LLM
                       │
                       ↓
                  INFERENCIA
                       │
                       ↓
                 TOKENS DE SALIDA
                       │
                       ↓
                  POSTPROCESO
                       │
                       ↓
                    USUARIO
```

Esta arquitectura explica una de las ideas más importantes de este repositorio:

> **La Ingeniería de Prompt estudia cómo diseñar instrucciones para producir comportamientos útiles, pero el comportamiento final depende del sistema completo que procesa esas instrucciones.**

---

# 30. ¿Dónde se encuentra realmente la Ingeniería de Prompt?

Podemos visualizar el sistema de esta manera:

```text
┌─────────────────────────────────────┐
│            SISTEMA DE IA            │
│                                     │
│   ┌───────────┐                     │
│   │ Contexto  │                     │
│   └─────┬─────┘                     │
│         │                           │
│   ┌─────▼─────┐                     │
│   │   Prompt  │ ← Ingeniería        │
│   └─────┬─────┘                     │
│         │                           │
│   ┌─────▼─────┐                     │
│   │    LLM    │                     │
│   └─────┬─────┘                     │
│         │                           │
│   ┌─────▼─────┐                     │
│   │ Inferencia│                     │
│   └─────┬─────┘                     │
│         │                           │
│   ┌─────▼─────┐                     │
│   │ Herramient│                     │
│   │    as     │                     │
│   └───────────┘                     │
│                                     │
└─────────────────────────────────────┘
```

Por eso un ingeniero de prompt necesita comprender mucho más que la redacción de instrucciones.

---

# 31. El error de pensar que "prompt engineering = escribir prompts bonitos"

Un prompt profesional no se evalúa por lo elegante que parece.

Se evalúa por su comportamiento.

Comparemos:

### Prompt informal

```text
Hazme un buen análisis de este documento.
```

### Prompt más especificado

```text
Analiza el documento.

Identifica:
1. inconsistencias;
2. datos faltantes;
3. valores atípicos;
4. riesgos potenciales.

Para cada hallazgo indica:
- descripción;
- evidencia;
- impacto;
- nivel de riesgo.

No inventes información que no aparezca en el documento.
```

El segundo prompt proporciona restricciones observables.

Pero incluso este prompt puede fallar dependiendo de:

* modelo;
* contexto;
* longitud del documento;
* capacidad de razonamiento;
* formato de salida;
* herramientas;
* configuración de inferencia.

Por eso aprenderemos a diseñar sistemas y no únicamente frases.

---

# 32. El prompt como entrada de un sistema probabilístico

Podemos construir una representación conceptual:

```text
Prompt + Contexto + Configuración
                │
                ↓
             Modelo
                │
                ↓
       Distribución de salida
                │
                ↓
          Decodificación
                │
                ↓
             Respuesta
```

Una formulación simplificada sería:

$$
P(Y \mid X, C, \theta)
$$

donde:

* \(X\) = entrada o prompt;
* \(C\) = contexto;
* \(\theta\) = parámetros del modelo;
* \(Y\) = salida generada.

Esta expresión no describe todos los componentes de un sistema moderno de IA, pero sirve para comprender una idea fundamental:

> **La salida depende de más elementos que el texto del prompt.**

---

# 33. El papel de la inferencia

Una vez entrenado el modelo, normalmente no modificamos sus parámetros para responder a cada usuario.

Utilizamos el modelo mediante **inferencia**.

Conceptualmente:

```text
MODELO ENTRENADO
       │
       ↓
     INPUT
       │
       ↓
   INFERENCIA
       │
       ↓
    OUTPUT
```

La inferencia incluye el proceso mediante el cual el modelo utiliza sus parámetros y la información disponible para producir una salida.

En los LLM generativos, la generación puede involucrar múltiples pasos de predicción de tokens.

---

# 34. Una analogía: el estudiante experto

Imaginemos un estudiante que pasó años estudiando miles de libros.

Después del entrenamiento académico:

```text
Libros
 ↓
Estudio
 ↓
Aprendizaje
 ↓
Conocimiento adquirido
```

Durante un examen:

```text
Pregunta
 ↓
Estudiante
 ↓
Respuesta
```

El examen no vuelve a enseñar al estudiante todos los libros.

La pregunta activa y utiliza conocimientos adquiridos previamente.

Un LLM tiene una diferencia fundamental:

> No debemos interpretar literalmente esta analogía como si el modelo tuviera una mente humana.

La analogía solamente ayuda a distinguir:

```text
Entrenamiento
```

de:

```text
Inferencia
```

---

# 35. Analogía con un motor

Otra analogía útil para comprender un sistema de IA:

```text
LLM = motor
Prompt = instrucciones
Contexto = información disponible
Herramientas = sistemas externos
Inferencia = proceso de ejecución
Aplicación = vehículo completo
```

Podemos tener:

```text
Motor potente
+
malas instrucciones
=
mal resultado
```

o:

```text
Motor adecuado
+
buen contexto
+
buenas instrucciones
+
herramientas adecuadas
=
sistema mucho más capaz
```

Pero tampoco debemos tomar la analogía literalmente.

Un LLM no es simplemente un motor mecánico y el prompt no controla directamente sus parámetros internos.

---

# 36. Lo que el alumno debe recordar

Al terminar este capítulo, el alumno debería poder explicar:

### 1. ¿Qué es un LLM?

Un modelo de lenguaje de gran escala entrenado para aprender patrones y representaciones de lenguaje y utilizarlos durante la inferencia.

### 2. ¿Qué recibe?

Principalmente secuencias de tokens, junto con el contexto disponible para el sistema.

### 3. ¿Qué produce?

Dependiendo del modelo y del sistema, puede producir texto, código u otras formas de salida.

### 4. ¿Dónde está lo aprendido?

En gran medida, en los parámetros y representaciones aprendidas durante el entrenamiento.

### 5. ¿El prompt contiene el conocimiento del modelo?

No.

El prompt proporciona instrucciones y contexto para una inferencia.

### 6. ¿El prompt controla directamente el modelo?

No.

Influye en la entrada que recibe el modelo, pero el comportamiento depende también de la arquitectura, parámetros, contexto, configuración y sistema que lo rodea.

### 7. ¿LLM significa chatbot?

No.

Un chatbot puede utilizar un LLM, pero un LLM es el modelo, mientras que el chatbot es una aplicación o sistema.

### 8. ¿Un LLM siempre dice la verdad?

No.

Puede generar información incorrecta o inventada.

### 9. ¿Un LLM es un agente?

No necesariamente.

Un agente es un sistema que puede utilizar un LLM junto con herramientas, memoria, planificación y mecanismos de ejecución.

---

# 37. Mapa conceptual

```text
                         INTELIGENCIA ARTIFICIAL
                                  │
                                  ↓
                         MACHINE LEARNING
                                  │
                                  ↓
                      DEEP LEARNING / REDES
                                  │
                                  ↓
                         MODELOS DE LENGUAJE
                                  │
                                  ↓
                              LLM
                                  │
              ┌───────────────────┼───────────────────┐
              ↓                   ↓                   ↓
           Tokens              Contexto          Parámetros
              │                   │                   │
              └───────────────────┼───────────────────┘
                                  ↓
                              INFERENCIA
                                  │
                                  ↓
                           GENERACIÓN
                                  │
                                  ↓
                              SALIDA
                                  │
                     ┌────────────┼────────────┐
                     ↓            ↓            ↓
                   Texto        Código      Herramientas
```

---

# 38. Relación con los siguientes capítulos

Este capítulo respondió:

> **¿Qué es un LLM?**

Ahora debemos estudiar cómo está construido.

La progresión será:

```text
01 ¿Qué es un LLM?
        ↓
02 Tokenización
        ↓
03 Embeddings
        ↓
04 Transformer
        ↓
05 Attention
        ↓
06 Información posicional
        ↓
07 Pretraining
        ↓
08 Fine-Tuning
        ↓
09 Instruction Tuning
        ↓
10 Alignment
        ↓
11 Inference
        ↓
12 Sampling
        ↓
13 Context Window
```

La secuencia no es accidental.

Primero:

```text
¿Qué es?
```

Después:

```text
¿Con qué información trabaja?
```

Luego:

```text
¿Cómo representa esa información?
```

Después:

```text
¿Cómo la procesa?
```

Y finalmente:

```text
¿Cómo genera una respuesta?
```

---

# 39. Relación con Ingeniería de Prompt

El objetivo final de estudiar un LLM no es convertir al estudiante necesariamente en investigador de arquitecturas neuronales.

El objetivo es comprender **qué está ocurriendo cuando diseñamos una instrucción**.

La progresión será:

```text
ENTENDER EL MODELO
        ↓
ENTENDER LA ARQUITECTURA
        ↓
ENTENDER EL CONTEXTO
        ↓
ENTENDER LA INFERENCIA
        ↓
DISEÑAR PROMPTS
        ↓
EVALUAR RESULTADOS
        ↓
OPTIMIZAR EL SISTEMA
```

Por eso:

> **La Ingeniería de Prompt comienza mucho antes de escribir el primer prompt.**

Comienza comprendiendo el sistema sobre el cual ese prompt será ejecutado.

---

# 40. Regla fundamental del repositorio

A partir de este punto utilizaremos la siguiente regla:

> **Nunca evaluar un prompt únicamente por su texto.**

Debemos analizar:

```text
PROMPT
   +
MODELO
   +
ARQUITECTURA
   +
CONTEXTO
   +
DATOS
   +
CONFIGURACIÓN
   +
HERRAMIENTAS
   +
INFERENCIA
   +
EVALUACIÓN
```

Esto permite pasar de:

```text
"¿Cómo escribo un mejor prompt?"
```

a una pregunta mucho más profesional:

```text
"¿Qué componente del sistema está provocando
este comportamiento y cómo puedo modificarlo
o compensarlo?"
```

Esa transición marca el paso de **usuario de IA** a **ingeniero de sistemas de IA**.

---

# 41. Ejercicio conceptual

Sin utilizar código, analiza la siguiente situación:

```text
Prompt:

"Resume este documento en 5 puntos."
```

El sistema produce un resumen deficiente.

Antes de modificar el prompt, plantea al menos cinco hipótesis:

```text
1. ¿El modelo es adecuado para la tarea?
2. ¿El documento cabe completamente en el contexto?
3. ¿El contexto contiene información irrelevante?
4. ¿El modelo está siguiendo instrucciones correctamente?
5. ¿La configuración de generación afecta el resultado?
6. ¿El documento contiene información ambigua?
7. ¿Existe una limitación de la arquitectura?
8. ¿Sería mejor utilizar RAG?
9. ¿Necesitamos dividir el documento?
10. ¿Necesitamos una etapa de evaluación?
```

El objetivo del ejercicio no es encontrar inmediatamente la solución.

El objetivo es aprender a **diagnosticar el sistema antes de modificar el prompt**.

---

# 42. Ejercicio para programadores

Considera conceptualmente la siguiente función:

```python
def generar_respuesta(prompt, modelo):
    return modelo(prompt)
```

Esta abstracción es útil para comenzar, pero representa un sistema demasiado simplificado.

Un sistema real podría parecer más cercano a:

```python
def generar_respuesta(
    system_prompt,
    user_prompt,
    contexto,
    modelo,
    herramientas,
    configuracion
):
    ...
```

La diferencia conceptual es enorme.

El resultado no depende solamente de:

```python
user_prompt
```

sino de múltiples componentes.

Este principio será fundamental cuando pasemos de **Prompt Engineering** a **Context Engineering** y posteriormente a **AI Systems Engineering**.

---

# 43. Preguntas de comprobación

### Básicas

1. ¿Qué significa LLM?
2. ¿Qué diferencia existe entre un LLM y un chatbot?
3. ¿Qué son los tokens?
4. ¿Qué papel cumplen los parámetros?
5. ¿Qué diferencia existe entre entrenamiento e inferencia?

### Intermedias

6. ¿Por qué el mismo prompt puede producir resultados diferentes?
7. ¿Por qué un modelo base puede comportarse de manera diferente a un modelo instruido?
8. ¿Qué relación existe entre contexto y predicción?
9. ¿Por qué una respuesta coherente no garantiza que sea verdadera?
10. ¿Qué diferencia existe entre un LLM y un sistema RAG?

### Avanzadas

11. ¿Por qué el número de parámetros no determina por sí solo la capacidad de un modelo?
12. ¿Qué diferencia conceptual existe entre información aprendida en los parámetros e información proporcionada mediante contexto?
13. ¿Cómo puede un sistema aumentar las capacidades de un LLM sin modificar sus parámetros?
14. ¿Por qué Ingeniería de Prompt debe considerar el modelo utilizado?
15. ¿Qué elementos forman parte del sistema que produce una respuesta de IA?

### Nivel maestría / investigación

16. ¿Qué limitaciones tiene la interpretación de un LLM como simple predictor del siguiente token?
17. ¿Cómo cambia el análisis cuando el sistema incorpora herramientas externas?
18. ¿Qué propiedades pertenecen al modelo y cuáles pertenecen al sistema que lo envuelve?
19. ¿Cómo debería diseñarse un experimento para determinar si una mejora procede realmente del prompt y no de un cambio en el modelo o contexto?
20. ¿Qué variables deberían controlarse para evaluar científicamente una técnica de prompting?

---

# 44. Idea central

Si solamente recuerdas una idea de este capítulo, debe ser esta:

> **Un LLM no es un chatbot, un prompt no es el modelo y una respuesta no depende únicamente del prompt.**

El sistema completo puede representarse como:

```text
                 DATOS DE ENTRENAMIENTO
                          │
                          ↓
                     ENTRENAMIENTO
                          │
                          ↓
                   PARÁMETROS + MODELO
                          │
                          ↓
              ┌─────────────────────────┐
              │       INFERENCIA        │
              │                         │
              │  Prompt + Contexto     │
              │       + Configuración  │
              │       + Herramientas   │
              └────────────┬────────────┘
                           ↓
                     PREDICCIÓN
                           ↓
                      DECODIFICACIÓN
                           ↓
                        SALIDA
                           ↓
                      EVALUACIÓN
```

Comprender esta cadena es el primer paso para dejar de tratar la IA como una caja negra.

El siguiente capítulo estudiará una de las primeras piezas técnicas de esa cadena:

**¿Cómo se transforma el texto que escribimos en los tokens que procesa un LLM?**

→ `02-Tokenizacion.md`
