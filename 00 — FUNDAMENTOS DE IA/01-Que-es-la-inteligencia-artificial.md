# ¿Qué es la Inteligencia Artificial?

## 1. Objetivo

La **Inteligencia Artificial (IA)** es un campo de la informática dedicado a construir sistemas capaces de realizar tareas que normalmente asociamos con capacidades humanas, como reconocer patrones, comprender lenguaje, interpretar imágenes, tomar decisiones, resolver problemas, aprender de datos o generar contenido.

Sin embargo, esta definición puede resultar demasiado general.

Para comprender la IA moderna —y especialmente la Ingeniería de Prompt— necesitamos responder preguntas más precisas:

* ¿Qué significa realmente que una máquina sea "inteligente"?
* ¿Cómo aprende un sistema de IA?
* ¿Qué diferencia existe entre un programa tradicional y un modelo de IA?
* ¿Qué relación hay entre Inteligencia Artificial, Machine Learning, Deep Learning e IA generativa?
* ¿Qué es un modelo?
* ¿Por qué un modelo puede producir respuestas que no estaban escritas literalmente en su programación?
* ¿Dónde interviene el prompt?
* ¿Qué parte del comportamiento depende del prompt y qué parte depende del modelo?
* ¿Por qué comprender la IA es necesario para hacer Ingeniería de Prompt?

Este capítulo construye esas bases desde cero.

---

# 2. La idea más sencilla

Imagina una calculadora.

Le proporcionas:

```text
27 × 15
```

y devuelve:

```text
405
```

La calculadora no necesita comprender qué significa el número 27 ni qué representa una multiplicación en el mundo real. Ejecuta un conjunto de reglas matemáticas definidas.

Ahora imagina un sistema al que le proporcionas:

```text
Analiza esta factura y dime si encuentras posibles errores.
```

El sistema puede recibir un documento, interpretar texto, identificar patrones, clasificar información y producir una explicación.

La diferencia fundamental es que estamos tratando con un **modelo que ha aprendido patrones a partir de datos**, no solamente con una secuencia fija de instrucciones programadas manualmente para cada posible situación.

---

# 3. Programa tradicional frente a sistema de IA

Esta diferencia es fundamental para comprender todo lo que veremos posteriormente.

## 3.1. Programación tradicional

En un programa tradicional, un desarrollador escribe explícitamente las reglas.

Por ejemplo:

```text
SI edad >= 18
    entonces "mayor de edad"
SI edad < 18
    entonces "menor de edad"
```

Podemos representarlo así:

```text
Entrada
   ↓
Reglas programadas
   ↓
Salida
```

El comportamiento depende principalmente de las instrucciones que el programador escribió.

---

## 3.2. Machine Learning

En Machine Learning (aprendizaje automático), el enfoque cambia.

En lugar de escribir manualmente todas las reglas, proporcionamos datos y un procedimiento de aprendizaje mediante el cual el sistema ajusta los parámetros de un modelo para capturar patrones presentes en esos datos.

Conceptualmente:

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

Por ejemplo, queremos detectar transacciones potencialmente fraudulentas.

Podemos disponer de registros históricos:

```text
Monto | País | Hora | Tipo de operación | Fraude
--------------------------------------------------
120   | EC   | 10:20| Compra             | No
8500  | EC   | 03:15| Compra             | Sí
75    | EC   | 14:10| Compra             | No
9200  | XX   | 03:02| Compra             | Sí
```

El modelo intenta aprender patrones que permitan realizar predicciones sobre nuevas transacciones.

No significa que haya recibido una regla explícita como:

```text
SI monto > 8000 Y hora < 04:00
    entonces fraude
```

El modelo aprende una representación matemática a partir de los datos.

---

# 4. Entonces, ¿qué es un modelo?

Un **modelo de IA** es, simplificando, una representación matemática capaz de transformar determinadas entradas en determinadas salidas después de haber sido entrenada para una tarea o conjunto de tareas.

Podemos imaginarlo como una función:

```text
salida = f(entrada)
```

Por ejemplo:

```text
entrada
   ↓
 modelo
   ↓
 salida
```

En un sistema de clasificación:

```text
Imagen de un perro
       ↓
     modelo
       ↓
"perro"
```

En un modelo de lenguaje:

```text
"El cielo es de color"
          ↓
        modelo
          ↓
        "azul"
```

Esta representación es simplificada, pero introduce una idea esencial:

> **Un modelo no es una base de datos que simplemente busca una respuesta almacenada.**

Un modelo aprende parámetros durante su entrenamiento y utiliza esos parámetros durante la inferencia para producir una salida.

---

# 5. ¿La IA "piensa"?

Esta pregunta requiere precisión.

En lenguaje cotidiano podemos decir que una IA:

* "piensa";
* "razona";
* "entiende";
* "recuerda";
* "sabe".

Pero estas palabras pueden inducir a interpretaciones incorrectas.

En Ingeniería de IA debemos distinguir entre:

### Lenguaje informal

> "El modelo entendió mi pregunta."

y:

### Descripción técnica

> "El modelo produjo una respuesta coherente con la información y las instrucciones proporcionadas."

No debemos asumir automáticamente que una respuesta coherente demuestra que el modelo posee comprensión humana, conciencia o experiencia subjetiva.

---

# 6. Una IA no es necesariamente un cerebro digital

Una de las primeras confusiones que debe evitar un estudiante es imaginar que todos los sistemas de IA funcionan como una copia electrónica del cerebro humano.

No es así.

Las redes neuronales artificiales están inspiradas parcialmente en algunas ideas de la neurociencia, pero su funcionamiento matemático y computacional no equivale al funcionamiento de un cerebro humano.

Por ejemplo:

```text
Cerebro humano
≠
Red neuronal artificial
```

Una red neuronal artificial utiliza operaciones matemáticas sobre representaciones numéricas.

Esto no significa que sea poco sofisticada.

Significa que su funcionamiento debe estudiarse utilizando los conceptos apropiados:

```text
datos
parámetros
representaciones
funciones
optimización
inferencia
probabilidad
arquitectura
```

---

# 7. Las principales ramas que debemos distinguir

La expresión "Inteligencia Artificial" engloba numerosas áreas.

Una representación simplificada es:

```text
Inteligencia Artificial
│
├── Machine Learning
│   │
│   ├── Aprendizaje supervisado
│   ├── Aprendizaje no supervisado
│   └── Aprendizaje por refuerzo
│
├── Deep Learning
│   │
│   └── Redes neuronales profundas
│
├── Procesamiento del Lenguaje Natural
│
├── Visión por Computadora
│
├── Robótica
│
├── Sistemas de recomendación
│
└── IA Generativa
```

Estas categorías se solapan.

Por ejemplo, un modelo moderno de lenguaje puede utilizar:

```text
Machine Learning
      ↓
Deep Learning
      ↓
Transformer
      ↓
Modelo de lenguaje
      ↓
IA generativa
```

Por eso es importante no tratar estos términos como sinónimos.

---

# 8. Inteligencia Artificial

**Inteligencia Artificial** es el término más amplio.

Incluye métodos y sistemas destinados a resolver problemas que requieren algún tipo de capacidad que normalmente asociamos con la inteligencia.

Ejemplos:

* clasificación;
* predicción;
* reconocimiento de imágenes;
* planificación;
* recomendación;
* procesamiento de lenguaje;
* generación de texto;
* generación de imágenes;
* control de robots.

Por tanto:

```text
IA
```

es el conjunto amplio.

---

# 9. Machine Learning

**Machine Learning (ML)** es un subcampo de la Inteligencia Artificial en el que los sistemas utilizan datos y procedimientos de aprendizaje para ajustar modelos capaces de realizar predicciones o tomar decisiones.

Una forma sencilla de visualizarlo:

```text
Datos
  ↓
Aprendizaje
  ↓
Modelo
  ↓
Predicción
```

Ejemplo:

Queremos estimar el precio de una vivienda.

Podemos proporcionar:

```text
Superficie
Habitaciones
Ubicación
Antigüedad
Precio histórico
```

El modelo aprende relaciones entre estas variables y los precios observados.

Después puede recibir:

```text
120 m²
3 habitaciones
Quito
10 años
```

y producir una estimación.

---

# 10. Deep Learning

**Deep Learning (aprendizaje profundo)** es una familia de métodos de Machine Learning basada principalmente en redes neuronales con múltiples capas de procesamiento.

Una representación simplificada:

```text
Entrada
   ↓
Capa
   ↓
Capa
   ↓
Capa
   ↓
Salida
```

Las redes profundas han permitido grandes avances en:

* visión por computadora;
* reconocimiento de voz;
* procesamiento del lenguaje;
* generación de contenido;
* sistemas multimodales;
* robótica.

Los grandes modelos de lenguaje modernos pertenecen a esta familia de sistemas.

---

# 11. IA generativa

La **IA generativa** se refiere a sistemas capaces de generar contenido nuevo a partir de patrones aprendidos durante su entrenamiento.

Puede generar, entre otras cosas:

* texto;
* código;
* imágenes;
* audio;
* vídeo;
* estructuras de datos;
* combinaciones multimodales.

Por ejemplo:

```text
Prompt:
"Explica qué es un agujero negro para un estudiante de secundaria."

        ↓

Modelo generativo

        ↓

Texto generado
```

La salida no tiene por qué existir previamente como una respuesta completa almacenada.

El modelo genera una secuencia de salida utilizando sus parámetros, el contexto disponible y el mecanismo de inferencia.

---

# 12. ¿Dónde aparecen los LLM?

Un **Large Language Model (LLM)** es un modelo de lenguaje de gran escala.

Su función básica puede entenderse inicialmente como:

```text
contexto
   ↓
modelo
   ↓
predicción de tokens
   ↓
siguiente token
   ↓
nuevo contexto
   ↓
siguiente token
   ↓
...
```

Por ejemplo:

```text
"El agua hierve aproximadamente a"
```

El modelo puede asignar probabilidades a diferentes continuaciones:

```text
100 °C
90 °C
80 °C
...
```

La generación de texto consiste, simplificando, en seleccionar tokens sucesivos mediante el proceso de inferencia.

Esta explicación es deliberadamente simplificada. En capítulos posteriores estudiaremos tokenización, embeddings, Transformer, attention, logits, sampling y otros componentes.

---

# 13. El LLM no recibe palabras directamente

Esta idea será fundamental para comprender la Ingeniería de Prompt.

Cuando escribimos:

```text
Explica qué es Python.
```

el modelo no recibe literalmente las palabras como nosotros las percibimos.

El texto pasa primero por un proceso de **tokenización**.

Conceptualmente:

```text
Texto
  ↓
Tokenizer
  ↓
Tokens
  ↓
Representaciones numéricas
  ↓
Modelo
```

Un token puede representar una palabra completa, una parte de una palabra, signos de puntuación, espacios u otras unidades, dependiendo del tokenizer utilizado.

Por eso no debemos asumir que:

```text
1 palabra = 1 token
```

No existe una relación universal de ese tipo.

Estudiaremos tokenización con mayor profundidad en el capítulo correspondiente.

---

# 14. ¿Qué son los parámetros?

Un modelo de IA contiene numerosos valores numéricos denominados **parámetros**.

Durante el entrenamiento, estos valores se ajustan para que el modelo pueda representar patrones aprendidos a partir de los datos.

Una analogía sencilla:

Imagina una enorme mesa de mezclas con millones o miles de millones de controles.

```text
Control 1
Control 2
Control 3
...
Control N
```

Durante el entrenamiento se ajustan esos valores.

La analogía tiene limitaciones, pero ayuda a entender una idea:

> Los parámetros son parte de lo que determina el comportamiento aprendido del modelo.

No debemos confundir:

```text
parámetros del modelo
```

con:

```text
tokens de entrada
```

ni con:

```text
context window
```

Son conceptos diferentes.

---

# 15. Entrenamiento e inferencia

Esta distinción es esencial.

## Entrenamiento

Durante el entrenamiento se utilizan datos para ajustar los parámetros del modelo.

Conceptualmente:

```text
Datos
 ↓
Modelo
 ↓
Predicción
 ↓
Error
 ↓
Optimización
 ↓
Actualización de parámetros
 ↓
...
```

Este proceso puede repetirse una enorme cantidad de veces.

---

## Inferencia

Durante la inferencia utilizamos el modelo ya entrenado para producir una salida.

```text
Entrada
   ↓
Modelo entrenado
   ↓
Inferencia
   ↓
Salida
```

Cuando escribimos un prompt en una aplicación de IA, normalmente estamos interactuando con el modelo durante la **inferencia**, no entrenándolo desde cero.

Esta diferencia será fundamental cuando estudiemos Prompt Engineering.

---

# 16. Entonces, ¿qué hace realmente un prompt?

Un **prompt** es información proporcionada al modelo para orientar la generación de una salida.

Puede contener:

```text
instrucciones
contexto
datos
ejemplos
restricciones
formato esperado
preguntas
criterios
```

Por ejemplo:

```text
Analiza el siguiente texto.

Identifica:
1. Los problemas principales.
2. La evidencia que los sustenta.
3. Las posibles consecuencias.

Devuelve el resultado en JSON.
```

Este prompt no cambia necesariamente los parámetros internos del modelo.

En términos generales, modifica la **entrada o contexto utilizado durante la inferencia**.

Por eso:

```text
Prompt
≠
Entrenamiento
```

y:

```text
Prompt
≠
Fine-tuning
```

---

# 17. El prompt no controla todo

Esta es una de las ideas más importantes de todo el repositorio.

Dos usuarios pueden utilizar exactamente el mismo prompt y obtener resultados diferentes si cambian otras variables.

Por ejemplo:

```text
Prompt
+
Modelo
+
Versión del modelo
+
Contexto
+
Parámetros de inferencia
+
Herramientas
+
Datos
+
Estado
+
Sistema
```

pueden influir en el resultado.

Por eso no existe una fórmula universal como:

> "Este prompt funciona siempre."

Una afirmación de ese tipo carece de contexto.

En Ingeniería de Prompt debemos preguntar:

> **¿Funciona para qué modelo, qué tarea, qué datos, bajo qué condiciones y con qué criterio de evaluación?**

---

# 18. El mismo prompt puede comportarse diferente

Supongamos que utilizamos:

```text
Resume este documento en cinco puntos.
```

Con un modelo podemos obtener:

```text
1. ...
2. ...
3. ...
4. ...
5. ...
```

Con otro modelo podemos obtener:

```text
Resumen:

- ...
- ...
```

Y con un modelo especializado en razonamiento o con determinadas configuraciones pueden aparecer comportamientos diferentes.

Esto no significa automáticamente que un modelo sea "mejor".

Significa que:

> **El comportamiento de un prompt depende del sistema en el que se ejecuta.**

Esta observación será fundamental cuando estudiemos arquitecturas Dense, Mixture of Experts (MoE), modelos de razonamiento, modelos multimodales y otras arquitecturas.

---

# 19. ¿Qué es una arquitectura de modelo?

La **arquitectura** describe cómo está organizado y cómo procesa información un modelo.

No todos los modelos de IA tienen exactamente la misma arquitectura.

Por ejemplo, posteriormente estudiaremos:

```text
Transformer
Dense Transformer
Mixture of Experts (MoE)
Modelos de razonamiento
Modelos multimodales
Modelos especializados
```

Una arquitectura puede afectar aspectos como:

* cómo se procesa la información;
* cómo se distribuye el cálculo;
* qué modalidades puede procesar;
* cómo se utilizan los parámetros;
* cómo se realiza la inferencia;
* cómo escala el sistema.

Por tanto:

> **Comprender la arquitectura ayuda a comprender el comportamiento del modelo.**

Y comprender el comportamiento del modelo ayuda a hacer mejor Ingeniería de Prompt.

---

# 20. Una analogía para entender la relación

Imagina que quieres darle instrucciones a tres personas.

La instrucción es:

```text
"Prepara un informe sobre este problema."
```

Las tres reciben exactamente la misma frase.

Pero:

```text
Persona A
→ especialista en contabilidad

Persona B
→ especialista en programación

Persona C
→ especialista en derecho
```

La misma instrucción puede producir resultados completamente diferentes porque cada persona tiene conocimientos, capacidades y formas de procesar el problema diferentes.

En IA ocurre algo análogo:

```text
Mismo prompt
     │
 ┌───┼────────┐
 ↓   ↓        ↓
LLM A LLM B   LLM C
 │     │       │
 ↓     ↓       ↓
Salida Salida  Salida
```

No debemos asumir que todos los modelos interpretarán las instrucciones exactamente de la misma manera.

---

# 21. Una arquitectura especialmente importante: MoE

Más adelante estudiaremos **Mixture of Experts (MoE)** con detalle.

Por ahora basta comprender la idea.

En un modelo denso, simplificando:

```text
Entrada
   ↓
Red del modelo
   ↓
Salida
```

En un sistema MoE podemos representar conceptualmente:

```text
             Entrada
                ↓
             Router
          ↙    ↓    ↘
      Experto A B   Experto C
          ↘    ↓    ↙
             Salida
```

El router determina qué expertos participan en determinadas partes del procesamiento.

El usuario normalmente no controla directamente ese routing en una API comercial.

Sin embargo, el tipo de entrada puede influir en las representaciones internas que participan en ese proceso.

Por eso estudiaremos posteriormente una pregunta mucho más interesante:

> **¿Cómo puede cambiar el comportamiento de una misma estrategia de prompting cuando cambia la arquitectura del modelo?**

No asumiremos respuestas universales. Lo estudiaremos mediante arquitectura, documentación y experimentos.

---

# 22. ¿Por qué todo esto importa para Prompt Engineering?

Porque sería un error pensar:

```text
Prompt Engineering
=
escribir buenas frases
```

Una visión más completa es:

```text
                    TAREA
                      ↓
                   OBJETIVO
                      ↓
                   CONTEXTO
                      ↓
                    PROMPT
                      ↓
                  TOKENIZACIÓN
                      ↓
                 ARQUITECTURA
                      ↓
                   INFERENCIA
                      ↓
                    SALIDA
                      ↓
                 EVALUACIÓN
```

Y posteriormente:

```text
                    SALIDA
                      ↓
                 ¿Es correcta?
                 /           \
               NO             SÍ
               ↓               ↓
          modificar         validar
          sistema
```

Eso es mucho más cercano a la Ingeniería de Prompt profesional.

---

# 23. La IA como sistema, no solamente como modelo

Otra idea fundamental:

> **Una aplicación de IA moderna no necesariamente es solamente un modelo.**

Podemos tener:

```text
Usuario
   ↓
Aplicación
   ↓
Prompt
   ↓
Contexto
   ↓
LLM
   ↓
Herramienta
   ↓
Base de datos
   ↓
LLM
   ↓
Respuesta
```

Incluso podemos llegar a:

```text
Usuario
   ↓
Sistema
   ↓
Router
   ├── Modelo A
   ├── Modelo B
   └── Modelo C
        ↓
     Contexto
        ↓
       RAG
        ↓
      Skills
        ↓
      Tools
        ↓
      Agente
        ↓
    Evaluación
        ↓
     Respuesta
```

Esta evolución explica por qué la Ingeniería de Prompt constituye una base importante, pero no es el final del camino.

---

# 24. De Prompt Engineering a AI Systems Engineering

El recorrido educativo de este repositorio será:

```text
Prompt Engineering
        ↓
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

Cada nivel reutiliza conceptos anteriores.

Por ejemplo:

### Un agente necesita instrucciones.

Por tanto:

```text
Agente
 ↓
Prompt Engineering
```

### Una skill necesita instrucciones, contexto y una interfaz.

```text
Skill
 ↓
Prompt
+
Contexto
+
Input
+
Output
```

### Una herramienta necesita una especificación clara.

```text
Tool
 ↓
Input
 ↓
Ejecución
 ↓
Output
```

### Un sistema necesita evaluar todo lo anterior.

```text
Sistema
 ↓
Evaluación
 ↓
Observabilidad
 ↓
Seguridad
 ↓
Optimización
```

---

# 25. Qué NO debe aprender el estudiante

Este repositorio no pretende enseñar que:

```text
"Actúa como un experto..."
```

sea una fórmula mágica.

Tampoco pretende enseñar que:

```text
"Usa siempre Chain of Thought."
```

sea una regla universal.

Ni que:

```text
"Pon cinco ejemplos."
```

siempre sea mejor que cero ejemplos.

Ni que:

```text
"Los modelos MoE responden de esta manera..."
```

sea necesariamente cierto para todos los modelos MoE.

Ni que:

```text
"Un prompt largo es mejor."
```

sea una regla general.

El estudiante debe aprender a preguntar:

```text
¿Cuál es mi objetivo?
¿Qué modelo utilizo?
¿Qué arquitectura tiene?
¿Qué contexto recibe?
¿Qué restricciones existen?
¿Qué salida necesito?
¿Cómo voy a evaluar el resultado?
¿Qué evidencia tengo?
```

---

# 26. Ingeniería de Prompt como disciplina experimental

Una de las ideas centrales del repositorio será que la Ingeniería de Prompt debe tratarse como una disciplina experimental.

Supongamos que tenemos:

```text
Prompt A
```

y obtenemos:

```text
72 % de respuestas correctas
```

Creamos:

```text
Prompt B
```

y obtenemos:

```text
84 %
```

No podemos concluir inmediatamente que B es superior en todos los casos.

Necesitamos conocer:

* qué conjunto de datos utilizamos;
* qué modelo utilizamos;
* qué versión;
* qué configuración;
* qué métrica;
* cuántos casos;
* qué tipo de errores aparecieron;
* si el resultado es reproducible;
* cuál es el coste;
* cuál es la latencia.

Por eso la Ingeniería de Prompt se conecta naturalmente con:

```text
experimentación
evaluación
estadística
software engineering
seguridad
observabilidad
```

---

# 27. Nivel profesional

A nivel profesional, un ingeniero no debería decir simplemente:

> "Este prompt funciona."

Debería poder decir algo como:

> "En nuestro conjunto de evaluación de 500 casos, el prompt B redujo los errores de clasificación respecto al prompt A bajo el mismo modelo y configuración. La mejora se observó principalmente en los casos ambiguos. El coste promedio por solicitud aumentó debido al mayor tamaño del contexto."

La diferencia es fundamental.

La primera afirmación es una opinión.

La segunda es una afirmación basada en un experimento.

---

# 28. Nivel avanzado

A medida que avancemos, estudiaremos la interacción entre:

```text
Prompt
+
Modelo
+
Arquitectura
+
Contexto
+
Inferencia
+
Herramientas
+
Evaluación
```

Por ejemplo:

```text
¿Cómo cambia el comportamiento de un prompt
cuando el modelo utiliza MoE?

¿Cómo cambia cuando utiliza una arquitectura
especializada en razonamiento?

¿Cómo afecta un contexto extremadamente grande?

¿Cómo afecta la recuperación de documentos?

¿Cómo cambia el comportamiento cuando el modelo
puede llamar herramientas?

¿Cómo evaluar la robustez frente a prompt injection?
```

Estas preguntas llevan de Prompt Engineering hacia LLM Engineering y posteriormente hacia AI Systems Engineering.

---

# 29. Nivel de maestría y PhD

En el nivel más avanzado ya no basta con conocer técnicas existentes.

El estudiante debe aprender a formular preguntas de investigación.

Por ejemplo:

> ¿Cómo cambia la eficacia de una estrategia de prompting entre diferentes arquitecturas bajo una tarea controlada?

Esto puede convertirse en un experimento:

```text
Hipótesis
   ↓
Diseño experimental
   ↓
Selección de modelos
   ↓
Control de variables
   ↓
Conjunto de evaluación
   ↓
Experimentos
   ↓
Métricas
   ↓
Análisis estadístico
   ↓
Resultados
   ↓
Limitaciones
   ↓
Conclusión
```

Incluso podría llegar a plantearse una investigación como:

> ¿Existe una relación consistente entre determinadas características de una tarea y el comportamiento de diferentes arquitecturas de modelos bajo estrategias equivalentes de prompting?

La pregunta ya no es:

> "¿Cuál es el mejor prompt?"

Sino:

> **"¿Qué fenómeno estamos observando y bajo qué condiciones puede reproducirse?"**

Ese es el salto conceptual hacia investigación.

---

# 30. Modelo mental que debe conservar el estudiante

Al finalizar este capítulo, el estudiante debería conservar este modelo mental:

```text
                         INTELIGENCIA ARTIFICIAL
                                  │
                 ┌────────────────┼────────────────┐
                 ↓                ↓                ↓
                ML               DL          IA GENERATIVA
                                  │                │
                                  ↓                ↓
                             REDES NEURONALES     LLM
                                                    │
                          ┌─────────────────────────┤
                          ↓                         ↓
                     ARQUITECTURA               INFERENCIA
                          │                         │
                 ┌────────┼────────┐               │
                 ↓        ↓        ↓               │
               Dense     MoE   Multimodal          │
                          │                         │
                          └──────────┬──────────────┘
                                     ↓
                                   CONTEXTO
                                     │
                                     ↓
                                   PROMPT
                                     │
                                     ↓
                                  MODELO
                                     │
                                     ↓
                                  SALIDA
                                     │
                                     ↓
                                EVALUACIÓN
                                     │
                          ┌──────────┴──────────┐
                          ↓                     ↓
                       MEJORAR                VALIDAR
                          │
                          ↓
                    SISTEMA DE IA
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
           Tools        Skills       Agentes
             │            │            │
             └────────────┼────────────┘
                          ↓
                 SISTEMA DE PRODUCCIÓN
                          │
                  ┌───────┴───────┐
                  ↓               ↓
               Seguridad      Gobernanza
```

---

# 31. Principios fundamentales

Antes de continuar con el siguiente capítulo, deben quedar claras estas ideas:

### 1. IA no es sinónimo de LLM

Los LLM son solamente una familia de modelos dentro del amplio campo de la IA.

### 2. Un modelo no es una base de datos convencional

Aprende representaciones y patrones mediante parámetros ajustados durante el entrenamiento.

### 3. Entrenamiento e inferencia son procesos diferentes

Utilizar un modelo mediante un prompt normalmente corresponde a inferencia.

### 4. El prompt no es todo el sistema

El resultado depende también del modelo, arquitectura, contexto, configuración, herramientas y otros componentes.

### 5. No todos los modelos responden igual al mismo prompt

La arquitectura, entrenamiento, capacidades y configuración pueden modificar el comportamiento.

### 6. MoE no significa simplemente "modelo más rápido"

Su funcionamiento implica mecanismos de routing y expertos, y sus consecuencias deben estudiarse caso por caso.

### 7. Un prompt no controla directamente la arquitectura

El usuario normalmente no puede seleccionar manualmente qué parámetros o expertos internos utilizará el modelo.

### 8. Prompt Engineering no consiste en memorizar frases

Consiste en diseñar instrucciones y contexto para alcanzar objetivos concretos bajo condiciones determinadas.

### 9. La ingeniería requiere evaluación

Una modificación del prompt debe poder probarse y medirse.

### 10. El objetivo final es comprender el sistema

El profesional debe poder pasar de:

```text
"¿Qué prompt escribo?"
```

a:

```text
"¿Qué problema estoy resolviendo,
qué modelo y arquitectura tengo,
qué contexto recibe,
qué comportamiento espero,
cómo voy a medirlo
y cómo puedo mejorar el sistema?"
```

---

# 32. Resumen

La Inteligencia Artificial es un campo amplio de la informática que incluye múltiples métodos para construir sistemas capaces de realizar tareas asociadas con capacidades inteligentes.

Dentro de ella encontramos Machine Learning y Deep Learning. Los modelos modernos de lenguaje pertenecen a esta evolución y permiten construir sistemas de IA generativa capaces de producir texto, código y otros tipos de contenido.

Para interactuar con estos modelos utilizamos prompts, pero el prompt no debe estudiarse de forma aislada.

El comportamiento final emerge de la interacción entre:

```text
datos
+
entrenamiento
+
parámetros
+
arquitectura
+
contexto
+
prompt
+
inferencia
+
herramientas
+
estado
+
evaluación
```

Por esta razón, la Ingeniería de Prompt comienza con una pregunta aparentemente sencilla:

> **¿Qué es la Inteligencia Artificial?**

pero termina conduciendo hacia una pregunta mucho más profunda:

> **¿Cómo podemos diseñar, evaluar y controlar sistemas de IA de manera reproducible, segura y fundamentada técnicamente?**

Ese será el recorrido de este repositorio.

---

## Próximo concepto

El siguiente capítulo será:

**`02-IA-ML-DL-IA-GENERATIVA.md`**

En él se establecerá con precisión la relación entre:

```text
Inteligencia Artificial
        ↓
Machine Learning
        ↓
Deep Learning
        ↓
Modelos fundacionales
        ↓
Modelos de lenguaje
        ↓
IA generativa
```

La finalidad será eliminar desde el principio una de las mayores fuentes de confusión terminológica en IA.
