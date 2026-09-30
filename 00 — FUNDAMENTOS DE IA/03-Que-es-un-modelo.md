# 03 — ¿Qué es un modelo?

> **Objetivo:** comprender qué es realmente un modelo de inteligencia artificial, cómo aprende, qué contiene, qué hace durante la inferencia y qué significa esto cuando escribimos un prompt.

---

## 1. La idea fundamental

Antes de estudiar tokens, embeddings, Transformers o LLM, debemos responder una pregunta aparentemente sencilla:

> **¿Qué es un modelo de inteligencia artificial?**

En términos simples:

> **Un modelo de IA es una representación matemática de patrones aprendidos a partir de datos que puede utilizarse para producir predicciones, clasificaciones, decisiones o contenido nuevo.**

Esta definición contiene varias ideas importantes:

* **representación matemática:** el modelo no es simplemente un conjunto de reglas escritas en lenguaje humano;
* **patrones:** aprende relaciones existentes en los datos;
* **datos:** el comportamiento del modelo depende de aquello con lo que fue entrenado;
* **aprendizaje:** sus parámetros se modifican durante el entrenamiento;
* **predicción:** utiliza lo aprendido para producir una salida ante una entrada nueva.

En un modelo tradicional de machine learning, la salida puede ser:

```text
Entrada → Modelo → Predicción
```

Por ejemplo:

```text
metros cuadrados = 120
habitaciones = 3
ubicación = Quito
antigüedad = 10 años

          ↓

       MODELO

          ↓

precio estimado = $135.000
```

En un LLM, la idea fundamental sigue siendo la misma, aunque la salida sea mucho más compleja:

```text
Prompt
   ↓
Tokenización
   ↓
Representación numérica
   ↓
Modelo
   ↓
Distribución de probabilidades
   ↓
Selección/generación de tokens
   ↓
Respuesta
```

---

# 2. Un modelo no es una respuesta almacenada

Una de las primeras confusiones que debemos eliminar es pensar que un modelo funciona como una enorme biblioteca:

> "Le hice una pregunta y encontró la respuesta que tenía guardada."

En general, esa no es la forma correcta de entender un LLM.

Supongamos que preguntamos:

```text
¿Cuál es la capital de Ecuador?
```

Un modelo generativo no necesita tener almacenada literalmente una tarjeta que diga:

```text
Pregunta:
¿Cuál es la capital de Ecuador?

Respuesta:
Quito
```

Durante la inferencia, el modelo procesa la entrada y calcula qué continuación resulta más probable según los patrones que aprendió.

De manera simplificada:

```text
¿Cuál es la capital de Ecuador?
                    ↓
             procesamiento
                    ↓
        probabilidades de salida
                    ↓
                  Quito
```

Esto explica una propiedad fundamental:

> **Un LLM genera una respuesta; no simplemente recupera una respuesta almacenada.**

Esto no significa que los modelos no puedan memorizar información. Pueden memorizar determinados patrones, secuencias o fragmentos de datos. La diferencia es que **memorizar información y funcionar como una base de datos son conceptos distintos**.

---

# 3. Modelo ≠ base de datos

Una base de datos está diseñada para almacenar y recuperar información.

Por ejemplo:

```text
ID    Nombre       Edad
1     Ana          25
2     Carlos       31
3     María        28
```

Podemos consultar:

```sql
SELECT * FROM personas WHERE edad = 31;
```

y recuperar:

```text
Carlos, 31
```

Un modelo de machine learning funciona de otra manera.

Podemos entrenarlo con ejemplos:

```text
Horas estudiadas → Nota

2 → 55
4 → 65
6 → 75
8 → 85
10 → 95
```

El modelo intenta aprender una relación:

```text
horas estudiadas → rendimiento
```

Después podemos introducir:

```text
7 horas
```

y el modelo podría estimar:

```text
≈ 80
```

No necesariamente existe una fila:

```text
7 → 80
```

El modelo **generaliza a partir de los patrones aprendidos**.

---

# 4. Modelo como función

Desde el punto de vista matemático, podemos representar un modelo como una función:

$$
y = f(x)
$$

donde:

* \(x\) = entrada;
* \(f\) = modelo;
* \(y\) = salida.

Ejemplo:

$$
precio = f(metros,\ habitaciones,\ ubicación,\ antigüedad)
$$

El modelo recibe características de una vivienda y produce una predicción.

En machine learning podemos escribir:

$$
\hat{y} = f_\theta(x)
$$

La notación \(\theta\) representa los **parámetros del modelo**.

Por tanto:

```text
entrada
   ↓
fθ(x)
   ↓
salida
```

Los parámetros \(\theta\) son una parte fundamental de lo que el modelo ha aprendido.

> **El modelo puede entenderse como una función cuyos parámetros fueron ajustados mediante entrenamiento para representar determinados patrones presentes en los datos.**

---

# 5. ¿Qué significa "aprender"?

Cuando decimos que una IA "aprende", no significa necesariamente que aprenda como una persona.

En machine learning, aprender significa principalmente:

> **ajustar parámetros internos para reducir el error entre las predicciones del modelo y los resultados esperados durante el entrenamiento.**

Supongamos:

```text
Entrada:
2 + 2

Respuesta esperada:
4

Respuesta del modelo:
5
```

Existe un error.

Durante el entrenamiento, un algoritmo utiliza ese error para modificar los parámetros del modelo.

Simplificando enormemente:

```text
datos
  ↓
predicción
  ↓
comparación con resultado esperado
  ↓
error
  ↓
actualización de parámetros
  ↓
nueva predicción
```

Este proceso se repite muchas veces.

---

# 6. El modelo como un sistema de parámetros

Un modelo moderno puede contener desde miles hasta cientos de miles de millones de parámetros.

Podemos imaginar un parámetro como una variable numérica que participa en los cálculos internos del modelo.

Por ejemplo, imaginemos un modelo extremadamente sencillo:

$$
y = wx+b
$$

donde:

* \(x\) = entrada;
* \(w\) = peso;
* \(b\) = sesgo;
* \(y\) = salida.

Supongamos:

$$
w=2
$$

$$
b=1
$$

y:

$$
x=3
$$

Entonces:

$$
y=(2)(3)+1
$$

$$
y=7
$$

En un modelo real existen cantidades enormes de parámetros y operaciones mucho más complejas.

La idea fundamental, sin embargo, permanece:

```text
entrada
   ↓
parámetros
   ↓
cálculos
   ↓
salida
```

---

# 7. Entrenamiento: donde se modifican los parámetros

Durante el entrenamiento, los parámetros se ajustan.

Una representación simplificada sería:

```text
Parámetros iniciales
        ↓
      datos
        ↓
     predicción
        ↓
      error
        ↓
actualización de parámetros
        ↓
   nueva predicción
        ↓
       ...
```

Matemáticamente, podemos imaginar:

$$
\theta_{nuevo}
=
\theta_{anterior}
-
\eta\nabla_\theta L
$$

donde:

* \(\theta\) = parámetros;
* \(L\) = función de pérdida;
* \(\nabla_\theta L\) = gradiente de la pérdida respecto de los parámetros;
* \(\eta\) = tasa de aprendizaje.

No es necesario dominar cálculo diferencial para comprender prompting.

Sin embargo, esta ecuación es importante porque muestra algo fundamental:

> **El comportamiento aprendido de un modelo está relacionado con sus parámetros y con el proceso utilizado para ajustarlos.**

---

# 8. Función de pérdida

Para que un modelo pueda aprender necesitamos alguna forma de medir qué tan equivocada fue su predicción.

A esta medida se la denomina **función de pérdida** (*loss function*).

Conceptualmente:

$$
\text{pérdida}
=
\text{qué tan diferente fue la predicción del resultado esperado}
$$

Ejemplo sencillo:

```text
Resultado esperado: 100
Predicción:          90

Error:               10
```

El entrenamiento intenta encontrar configuraciones de parámetros que produzcan pérdidas menores.

Podemos visualizarlo así:

```text
        pérdida
           ↑
           │       ●
           │      / \
           │     /   \
           │    /     \
           │ ● /       \ ●
           │
           └────────────────→ parámetros
                    ↓
                  mínimo
```

El entrenamiento busca, de manera simplificada, configuraciones que minimicen la función de pérdida.

---

# 9. ¿Qué aprende realmente un modelo?

Esta pregunta es más profunda de lo que parece.

Un modelo no aprende únicamente "datos".

Aprende **regularidades estadísticas presentes en los datos**.

Por ejemplo, después de observar numerosos ejemplos de texto puede aprender relaciones entre:

* palabras;
* estructuras gramaticales;
* conceptos;
* entidades;
* estilos;
* patrones sintácticos;
* relaciones semánticas;
* secuencias;
* estructuras de documentos;
* diferentes formas de expresar una misma idea.

En un LLM, el aprendizaje ocurre sobre representaciones numéricas de los datos.

Por eso es más preciso decir:

> **El modelo aprende representaciones y relaciones estadísticas útiles para realizar determinadas tareas.**

---

# 10. Generalización

Una propiedad fundamental de machine learning es la **generalización**.

Generalizar significa que el modelo puede producir una salida razonable ante datos que no vio exactamente durante el entrenamiento.

Ejemplo:

Durante el entrenamiento observa:

```text
2 × 3 = 6
2 × 4 = 8
2 × 5 = 10
```

Después recibe:

```text
2 × 7 = ?
```

Si aprendió la regularidad adecuada, puede producir:

```text
14
```

No necesitamos asumir que encontró una fila almacenada:

```text
2 × 7 = 14
```

Está utilizando un patrón aprendido.

---

# 11. Generalización no significa comprensión humana

Aquí debemos introducir una distinción importante.

Que un modelo pueda producir respuestas coherentes no demuestra automáticamente que posea comprensión humana.

Por ejemplo, un modelo puede aprender relaciones extremadamente complejas entre palabras y conceptos sin tener:

* conciencia;
* experiencias personales;
* intenciones humanas;
* percepción del mundo equivalente a la humana.

Por eso debemos evitar frases como:

> "El modelo piensa exactamente como una persona."

Es más preciso decir:

> **El modelo transforma representaciones de entrada mediante operaciones matemáticas aprendidas y produce una salida según los patrones representados en sus parámetros y el contexto disponible.**

---

# 12. Modelo paramétrico

Los modelos de machine learning suelen denominarse **modelos paramétricos** cuando su comportamiento está determinado por un conjunto de parámetros aprendidos.

Podemos representarlo como:

$$
f_\theta(x)
$$

donde:

* \(x\) representa la entrada;
* \(\theta\) representa los parámetros;
* \(f\) representa la transformación realizada;
* \(y\) representa la salida.

En un modelo pequeño:

```text
x → [p1, p2, p3, p4] → y
```

En un modelo profundo:

```text
x
↓
capa
↓
capa
↓
capa
↓
...
↓
salida
```

Cada capa realiza transformaciones sobre las representaciones internas.

En un LLM moderno, estas transformaciones incluyen mecanismos como:

* embeddings;
* atención;
* redes feed-forward;
* normalización;
* proyecciones lineales;
* conexiones residuales;
* mecanismos específicos de la arquitectura.

Estos componentes se estudiarán posteriormente.

---

# 13. ¿Qué ocurre cuando termina el entrenamiento?

Aquí aparece una distinción esencial:

## Entrenamiento

Modifica los parámetros.

```text
datos
 ↓
modelo
 ↓
error
 ↓
actualización
 ↓
parámetros modificados
```

## Inferencia

Utiliza los parámetros existentes para producir una salida.

```text
entrada
 ↓
modelo entrenado
 ↓
cálculos
 ↓
salida
```

Durante una inferencia normal, el modelo **no está reentrenándose después de cada pregunta**.

Por ejemplo:

```text
Usuario:
¿Qué es un árbol de decisión?

        ↓

Modelo entrenado

        ↓

Respuesta
```

La pregunta modifica el **contexto de la inferencia**, pero normalmente no modifica permanentemente los parámetros del modelo.

Esta diferencia será fundamental cuando estudiemos:

* contexto;
* memoria;
* RAG;
* fine-tuning;
* agentes;
* prompting.

---

# 14. Modelo + entrada ≠ modelo entrenándose

Supongamos que escribimos:

```text
Actúa como profesor de matemáticas.
Explica el concepto paso a paso.
Utiliza ejemplos sencillos.
```

El modelo recibe esas instrucciones como parte de la entrada.

Esto normalmente no significa:

```text
"El modelo acaba de aprender permanentemente a enseñar matemáticas."
```

Significa:

```text
instrucciones
     +
pregunta
     +
contexto
     ↓
  inferencia
     ↓
 respuesta
```

El prompt **condiciona la inferencia**.

Esta distinción es una de las bases de la ingeniería de prompts.

---

# 15. Entonces, ¿qué controla realmente un prompt?

Esta pregunta conecta directamente este módulo con el objetivo del repositorio.

Un prompt no suele modificar los parámetros permanentes del modelo.

En términos simplificados:

```text
                 ENTRENAMIENTO
                       ↓
              ┌─────────────────┐
              │    PARÁMETROS    │
              └─────────────────┘
                       ↓
                MODELO ENTRENADO
                       ↑
                       │
                    PROMPT
                       │
                 CONTEXTO
                       │
               CONFIGURACIÓN
                       ↓
                   INFERENCIA
                       ↓
                    SALIDA
```

Por tanto, cuando escribimos un prompt estamos principalmente controlando **las condiciones bajo las cuales el modelo realiza una inferencia**.

Podemos influir en:

* la tarea;
* el objetivo;
* el contexto proporcionado;
* el formato esperado;
* las restricciones;
* los ejemplos;
* el estilo;
* el nivel de detalle;
* los criterios de evaluación;
* el orden de procesamiento;
* la información relevante disponible para la respuesta.

Pero no estamos reprogramando directamente los parámetros del modelo.

---

# 16. Ejemplo: mismo modelo, diferentes prompts

Supongamos un mismo LLM.

Prompt A:

```text
¿Qué es la inteligencia artificial?
```

Podría producir una explicación general.

Prompt B:

```text
¿Qué es la inteligencia artificial?

Explícala para una persona de 12 años.
Utiliza una analogía con una calculadora.
No utilices terminología matemática.
Incluye tres ejemplos cotidianos.
```

La segunda respuesta probablemente tendrá características diferentes.

No porque hayamos creado otro modelo.

Tenemos:

```text
MISMO MODELO
     +
PROMPT DIFERENTE
     ↓
INFERENCIA DIFERENTE
     ↓
SALIDA DIFERENTE
```

Esta es una de las ideas centrales de la ingeniería de prompts.

---

# 17. Prompt como condición de inferencia

Podemos representar una inferencia simplificada como:

$$
y = f_\theta(x)
$$

donde:

* \(x\) = entrada;
* \(\theta\) = parámetros;
* \(y\) = salida.

Cuando trabajamos con LLM, la entrada \(x\) puede contener mucho más que una pregunta.

Podemos representarla como:

$$
x =
[
\text{instrucciones},
\text{contexto},
\text{ejemplos},
\text{pregunta},
\text{restricciones}
]
$$

Entonces:

$$
y = f_\theta(x)
$$

La ingeniería de prompts consiste, en gran medida, en diseñar cuidadosamente \(x\) para obtener comportamientos útiles y reproducibles del modelo.

---

# 18. El modelo no "ve" directamente nuestro texto

Aquí debemos dar un paso más técnico.

Cuando escribimos:

```text
Hola, ¿cómo estás?
```

el modelo no procesa los caracteres exactamente como los percibe una persona.

El texto pasa primero por un proceso de **tokenización**.

Simplificando:

```text
"Hola, ¿cómo estás?"

        ↓

["Hola", "¿", "cómo", "estás", "?"]

        ↓

tokens

        ↓

representaciones numéricas

        ↓

modelo
```

En un sistema real, los tokens dependen del tokenizador utilizado y no necesariamente coinciden con palabras completas.

Podemos encontrar:

```text
"inteligencia"
```

o fragmentos como:

```text
"inteli"
"gencia"
```

o representaciones diferentes.

Los tokens se estudiarán detalladamente en:

```text
05-Tokens.md
```

---

# 19. De tokens a representaciones internas

Después de la tokenización, los tokens se convierten en representaciones numéricas.

Una simplificación conceptual:

```text
Texto
 ↓
Tokens
 ↓
IDs numéricos
 ↓
Embeddings
 ↓
Representaciones internas
 ↓
Transformaciones del modelo
 ↓
Probabilidades
 ↓
Token siguiente
```

Por ejemplo:

```text
"El gato duerme"
```

podría transformarse conceptualmente en:

```text
Token 1 → representación numérica
Token 2 → representación numérica
Token 3 → representación numérica
```

El modelo trabaja con esas representaciones, no con conceptos escritos directamente en español.

---

# 20. Un LLM como función probabilística

Aquí llegamos a una idea fundamental.

Un modelo generativo de lenguaje puede entenderse, de forma simplificada, como un sistema que estima:

$$
P(x_t \mid x_1,x_2,\dots,x_{t-1})
$$

Esto significa:

> **la probabilidad del siguiente token dado el contexto anterior.**

Ejemplo:

```text
El cielo es
```

El modelo podría asignar probabilidades aproximadas:

```text
azul       → 0.82
grande     → 0.03
hermoso    → 0.02
verde      → 0.01
...
```

Los números anteriores son únicamente ilustrativos.

El concepto importante es:

> **El modelo genera texto de manera autoregresiva estimando distribuciones de probabilidad sobre posibles siguientes tokens.**

Después de seleccionar un token, ese token pasa a formar parte del contexto:

```text
El cielo es
        ↓
El cielo es azul
        ↓
El cielo es azul durante
        ↓
El cielo es azul durante el día
```

En cada paso se vuelve a calcular la distribución correspondiente.

---

# 21. ¿Por qué puede escribir párrafos completos?

Porque este proceso se repite muchas veces.

Conceptualmente:

```text
Prompt
  ↓
predicción token 1
  ↓
predicción token 2
  ↓
predicción token 3
  ↓
predicción token 4
  ↓
...
  ↓
respuesta
```

Esto permite construir:

```text
palabras
   ↓
frases
   ↓
párrafos
   ↓
documentos
   ↓
código
   ↓
respuestas estructuradas
```

Aunque la unidad fundamental de generación sigue siendo el **token**.

---

# 22. ¿Significa esto que el modelo solamente "adivina palabras"?

La expresión:

> "Los LLM solo predicen la siguiente palabra"

es útil para introducir el concepto, pero resulta demasiado simplificada para un nivel avanzado.

Es más correcto decir:

> **Los modelos de lenguaje entrenados autoregresivamente aprenden a modelar distribuciones sobre secuencias de tokens y utilizan el contexto disponible para generar nuevas secuencias.**

La predicción del siguiente token es el mecanismo fundamental de generación, pero el modelo puede desarrollar representaciones internas capaces de soportar comportamientos mucho más complejos.

Por ejemplo:

```text
razonamiento matemático
traducción
resumen
clasificación
generación de código
extracción estructurada
seguimiento de instrucciones
```

Estos comportamientos emergen de las representaciones y transformaciones aprendidas, no de una simple lista explícita de reglas.

---

# 23. ¿Qué significa que un modelo tenga "conocimiento"?

Cuando decimos:

> "El modelo sabe Python."

no debemos interpretar literalmente que existe dentro del modelo una carpeta llamada:

```text
/Python/
```

con todos los archivos del lenguaje.

El conocimiento de un modelo está distribuido en sus parámetros y representaciones internas.

Una analogía útil:

### Biblioteca

```text
Libro → capítulo → página → información
```

### Modelo

```text
datos de entrenamiento
        ↓
patrones
        ↓
parámetros
        ↓
representaciones
        ↓
comportamiento
```

Por eso recuperar información de un modelo no funciona exactamente como consultar una base de datos.

---

# 24. Conocimiento paramétrico

En LLM se suele utilizar el concepto de **conocimiento paramétrico**.

Es la información y las regularidades incorporadas en los parámetros durante el entrenamiento.

Por ejemplo, un modelo puede haber aprendido relaciones sobre:

```text
lenguaje
programación
matemáticas
historia
ciencia
estructuras de documentos
```

Sin embargo, ese conocimiento tiene limitaciones.

Puede:

* estar desactualizado;
* contener errores;
* ser incompleto;
* estar mezclado con información contradictoria;
* no contener información privada;
* no conocer acontecimientos posteriores a determinados datos de entrenamiento.

Por eso existen técnicas como:

* RAG;
* búsqueda web;
* herramientas;
* bases de datos;
* sistemas de recuperación;
* fine-tuning.

---

# 25. Modelo y RAG no son lo mismo

Un sistema RAG (*Retrieval-Augmented Generation*) introduce información externa durante la inferencia.

Sin RAG:

```text
Prompt
   ↓
Modelo
   ↓
Respuesta
```

Con RAG:

```text
Pregunta
   ↓
recuperación de información
   ↓
documentos relevantes
   ↓
contexto
   ↓
modelo
   ↓
respuesta
```

El modelo no necesariamente aprendió permanentemente esos documentos.

Los documentos pueden estar siendo proporcionados como contexto durante la inferencia.

Esta distinción será fundamental para comprender sistemas empresariales de IA.

---

# 26. Modelo, contexto y memoria

Estos tres conceptos suelen confundirse.

### Modelo

Contiene los parámetros aprendidos.

### Contexto

Es la información disponible para el modelo durante una inferencia determinada.

### Memoria

Dependiendo del sistema, puede existir un mecanismo externo que almacene información para reutilizarla posteriormente.

Conceptualmente:

```text
                 MODELO
              parámetros
                   │
                   ↓
             ┌───────────┐
Memoria ───→ │ CONTEXTO  │ ←── Prompt
             └───────────┘
                   ↓
                inferencia
                   ↓
                salida
```

Por eso:

> **Agregar información al contexto no equivale necesariamente a entrenar el modelo.**

---

# 27. ¿Puede un modelo equivocarse aunque su entrenamiento haya sido enorme?

Sí.

El tamaño del modelo o la cantidad de datos de entrenamiento no garantiza que cada respuesta sea correcta.

Una salida puede ser incorrecta por múltiples razones:

* datos de entrenamiento imperfectos;
* conocimiento incompleto;
* ambigüedad;
* contexto insuficiente;
* errores de razonamiento;
* generación probabilística;
* información contradictoria;
* problemas de recuperación;
* instrucciones mal diseñadas.

En LLM también puede producirse una **alucinación**, término utilizado para describir la generación de información que aparenta ser correcta pero no está respaldada por evidencia adecuada.

Esto será tratado con mayor profundidad en:

```text
09-Limitaciones-de-los-LLM.md
```

---

# 28. Modelo pequeño frente a modelo grande

No debemos asumir automáticamente:

```text
más parámetros = mejor en absolutamente todo
```

El comportamiento depende de múltiples factores:

* arquitectura;
* calidad de los datos;
* cantidad de datos;
* calidad del entrenamiento;
* objetivo de entrenamiento;
* alineamiento;
* postentrenamiento;
* capacidad de razonamiento;
* contexto;
* herramientas;
* especialización;
* eficiencia de inferencia.

Un modelo pequeño especializado puede superar a un modelo grande generalista en determinadas tareas.

Ejemplo conceptual:

```text
Modelo A
100B parámetros
generalista

Modelo B
7B parámetros
especializado en clasificación médica
```

Para una tarea muy específica, no sería correcto concluir que A necesariamente producirá mejores resultados simplemente por tener más parámetros.

---

# 29. Arquitectura ≠ parámetros

Otro concepto que debemos separar.

### Arquitectura

Define **cómo está construido el modelo**.

Por ejemplo:

```text
Transformer
Dense
MoE
Encoder
Decoder
Encoder-Decoder
```

### Parámetros

Son los valores numéricos aprendidos dentro de esa arquitectura.

Una analogía:

```text
Arquitectura = diseño de una fábrica

Parámetros = configuración aprendida de sus máquinas
```

Dos modelos pueden utilizar una arquitectura relacionada y tener:

* distinto número de parámetros;
* distintos datos;
* distinto entrenamiento;
* distinto comportamiento.

---

# 30. Modelo Dense

En un modelo **dense**, de forma simplificada, gran parte de la red participa en el procesamiento de cada token.

Conceptualmente:

```text
Token
  ↓
[████████████████]
[████████████████]
[████████████████]
  ↓
Salida
```

Esto contrasta con arquitecturas **Mixture of Experts (MoE)**, donde diferentes entradas pueden activar diferentes expertos.

```text
              ┌→ Experto A
Token → Router├→ Experto B
              ├→ Experto C
              └→ Experto D
```

Este tema se estudiará posteriormente en la sección de arquitectura.

Para prompting, la consecuencia práctica importante es:

> **La arquitectura determina parte de la forma en que el modelo procesa la información, pero el usuario normalmente interactúa con ella mediante una interfaz de inferencia.**

---

# 31. Modelo multimodal

No todos los modelos trabajan únicamente con texto.

Un sistema multimodal puede procesar diferentes tipos de información:

```text
Texto
Imagen
Audio
Video
Código
```

Conceptualmente:

```text
             ┌→ texto
Entrada ─────┼→ imagen
             ├→ audio
             └→ video
                    ↓
              representaciones
                    ↓
                  modelo
                    ↓
                  salida
```

Esto cambia la forma de diseñar prompts.

Por ejemplo:

```text
Analiza esta imagen.
Identifica los elementos visibles.
Después explica qué anomalías encuentras.
```

El prompt no es únicamente texto conceptual: puede estar acompañado de información de otras modalidades.

---

# 32. Modelo de clasificación frente a modelo generativo

No todos los modelos generan texto.

### Clasificación

Entrada:

```text
"El cliente está muy satisfecho."
```

Salida:

```text
POSITIVO
```

### Regresión

Entrada:

```text
metros cuadrados = 120
habitaciones = 3
```

Salida:

```text
135000
```

### Generación

Entrada:

```text
Escribe una explicación sobre árboles de decisión.
```

Salida:

```text
Los árboles de decisión...
```

La palabra **modelo** es mucho más amplia que **LLM**.

---

# 33. IA → ML → DL → modelos generativos → LLM

Podemos visualizar la relación:

```text
Inteligencia Artificial
│
└── Machine Learning
    │
    └── Deep Learning
        │
        ├── Modelos de visión
        ├── Modelos de audio
        ├── Modelos generativos
        │
        └── Modelos de lenguaje
             │
             └── LLM
```

No todos los modelos de IA son LLM.

Y no todos los modelos de machine learning utilizan redes neuronales.

Por eso debemos evitar utilizar:

```text
IA = ChatGPT
```

como si fueran sinónimos.

---

# 34. Modelo base y modelos postentrenados

En sistemas modernos podemos distinguir diferentes etapas.

Una simplificación:

```text
Datos
 ↓
Preentrenamiento
 ↓
Modelo base
 ↓
Postentrenamiento
 ↓
Modelo adaptado
 ↓
Sistema de inferencia
```

El **modelo base** aprende capacidades generales a partir de grandes cantidades de datos.

Después puede recibir procesos adicionales de entrenamiento para mejorar:

* seguimiento de instrucciones;
* seguridad;
* conversación;
* razonamiento;
* uso de herramientas;
* comportamiento específico.

Por eso el modelo que utiliza un usuario final puede ser mucho más que el resultado del preentrenamiento inicial.

---

# 35. Fine-tuning no es prompting

Esta diferencia es fundamental.

### Prompting

Modificamos la entrada:

```text
MODELO
  ↑
PROMPT
```

Los parámetros permanecen esencialmente iguales.

### Fine-tuning

Utilizamos datos adicionales para modificar parámetros del modelo:

```text
modelo original
      ↓
datos especializados
      ↓
entrenamiento adicional
      ↓
nuevo modelo
```

Por tanto:

> **Prompting cambia la condición de inferencia; fine-tuning cambia los parámetros mediante entrenamiento adicional.**

---

# 36. Prompting, RAG y fine-tuning

Podemos comparar:

| Técnica          | ¿Modifica parámetros? | ¿Añade información durante la consulta? | Uso típico                     |
| ---------------- | --------------------: | --------------------------------------: | ------------------------------ |
| Prompting        |                    No |         Sí, mediante el prompt/contexto | Controlar comportamiento       |
| RAG              |     No necesariamente |                                      Sí | Incorporar información externa |
| Fine-tuning      |                    Sí |                       No necesariamente | Adaptar comportamiento         |
| Preentrenamiento |                    Sí |               Sí, durante entrenamiento | Crear capacidades generales    |

Esta tabla será una referencia importante durante todo el repositorio.

---

# 37. Una visión completa de un LLM

Podemos reunir los conceptos anteriores:

```text
                 ENTRENAMIENTO
                       │
                       ↓
                 Datos masivos
                       │
                       ↓
                Tokenización
                       │
                       ↓
                Arquitectura
                       │
                       ↓
               Optimización
                       │
                       ↓
                  Parámetros
                       │
                       ↓
                MODELO ENTRENADO
                       │
                       │
                 INFERENCIA
                       ↑
                       │
        ┌──────────────┼──────────────┐
        │              │              │
      Prompt        Contexto       Herramientas
        │              │              │
        └──────────────┼──────────────┘
                       ↓
                  Tokenización
                       ↓
                 Procesamiento
                       ↓
             Distribución probabilística
                       ↓
                 Sampling/decoding
                       ↓
                    Tokens
                       ↓
                    Texto
```

Este diagrama representa la idea general, no todos los componentes internos de un sistema comercial.

---

# 38. Una analogía: el músico

Una analogía útil para principiantes es imaginar un músico.

### Entrenamiento

El músico estudia:

```text
notas
armonía
ritmo
técnica
canciones
```

### Parámetros

Representan, de manera muy simplificada, aquello que el músico ha aprendido y que determina su capacidad de ejecutar.

### Prompt

Sería una instrucción como:

```text
Toca una pieza de jazz lenta.
```

### Contexto

Podría incluir:

```text
instrumento
partitura
tempo
tonalidad
estilo
```

### Inferencia

Es la ejecución concreta:

```text
instrucciones + conocimientos + contexto
                     ↓
                 interpretación
                     ↓
                    música
```

La analogía tiene límites: un LLM no es una persona y no posee necesariamente comprensión o intención humana. Sirve únicamente para visualizar la separación entre **lo aprendido** y **las condiciones de ejecución**.

---

# 39. Una analogía más técnica: una calculadora extremadamente compleja

Podemos imaginar el modelo como una función matemática gigantesca:

```text
              parámetros
                  ↓
Entrada → [FUNCIÓN COMPLEJA] → salida
```

La función contiene una cantidad enorme de operaciones.

El prompt modifica la entrada:

```text
Entrada A → modelo → salida A

Entrada B → modelo → salida B
```

El mismo modelo puede producir comportamientos muy diferentes dependiendo de:

```text
entrada
contexto
instrucciones
configuración de generación
herramientas
```

Esta perspectiva resulta especialmente útil para ingeniería.

---

# 40. ¿Dónde entra la temperatura?

En un LLM, después de calcular probabilidades, existen mecanismos de **decodificación y sampling** que influyen en cómo se seleccionan los siguientes tokens.

Simplificando:

```text
Modelo
  ↓
logits
  ↓
probabilidades
  ↓
sampling / decoding
  ↓
token seleccionado
```

Parámetros como:

* temperature;
* top-p;
* top-k;

pueden modificar el comportamiento de la selección.

Por ejemplo, una temperatura más alta puede producir una distribución más dispersa y aumentar la diversidad de las muestras, mientras que una temperatura más baja tiende a concentrar la probabilidad.

Estos mecanismos no cambian los parámetros aprendidos del modelo.

Cambian **cómo se realiza la generación durante la inferencia**.

Se estudiarán con detalle posteriormente.

---

# 41. Modelo, pesos, parámetros y checkpoints

En la práctica técnica podemos encontrar términos como:

### Parámetros

Valores numéricos aprendidos.

### Pesos

En muchos contextos se utilizan como sinónimo informal de parámetros, aunque técnicamente un modelo puede contener diferentes tipos de parámetros.

### Checkpoint

Un estado guardado del modelo durante o después del entrenamiento.

Conceptualmente:

```text
Entrenamiento
    ↓
checkpoint 1
    ↓
checkpoint 2
    ↓
checkpoint 3
    ↓
modelo final
```

Un checkpoint puede contener los valores necesarios para continuar el entrenamiento o utilizar un estado determinado del modelo.

---

# 42. Tamaño del modelo

Cuando vemos:

```text
7B
70B
405B
```

la letra:

```text
B = billion
```

indica miles de millones de parámetros.

Por ejemplo:

```text
7B = aproximadamente 7.000 millones de parámetros
```

Sin embargo:

> **El número de parámetros no describe por sí solo la calidad, capacidad, velocidad o utilidad de un modelo.**

También debemos considerar:

* arquitectura;
* datos;
* entrenamiento;
* cuantización;
* contexto;
* hardware;
* especialización;
* postentrenamiento;
* capacidad de inferencia.

---

# 43. Parámetros activos en arquitecturas MoE

En modelos Mixture of Experts aparece otra distinción importante.

Podemos tener:

```text
Parámetros totales
        ≠
Parámetros activos por token
```

Un modelo puede contener muchos expertos, pero un router puede seleccionar únicamente algunos para procesar un token determinado.

Conceptualmente:

```text
Modelo:
100B parámetros totales

Token actual:
activa 20B parámetros
```

Los números anteriores son únicamente ilustrativos.

Esta arquitectura permite separar:

```text
capacidad total
```

de:

```text
costo computacional por token
```

Este concepto será importante cuando estudiemos MoE.

---

# 44. Modelo como sistema estadístico, no como oráculo

Un error frecuente es tratar al LLM como:

```text
Pregunta → verdad absoluta
```

Una representación más realista es:

```text
Entrada
   ↓
modelo
   ↓
distribución de posibles salidas
   ↓
decodificación
   ↓
respuesta
```

Por eso una respuesta fluida no garantiza que sea verdadera.

Esta distinción es especialmente importante en:

* medicina;
* derecho;
* finanzas;
* ciberseguridad;
* auditoría;
* ingeniería;
* investigación científica.

En estos ámbitos, la salida debe poder ser **verificada**.

---

# 45. ¿Por qué un modelo puede parecer que razona?

Esta es una pregunta avanzada.

Los LLM pueden producir secuencias de texto que muestran:

```text
descomposición del problema
→ pasos intermedios
→ comparación
→ conclusión
```

Esto puede dar lugar a un comportamiento que denominamos razonamiento.

Sin embargo, desde el punto de vista técnico debemos separar:

1. el comportamiento observable;
2. los mecanismos internos;
3. las interpretaciones filosóficas sobre "pensamiento".

En ingeniería de IA nos interesa principalmente:

> **qué comportamiento produce el sistema, bajo qué condiciones, con qué fiabilidad y cómo podemos evaluarlo.**

Por ello, no debemos asumir que una respuesta que contiene pasos lógicos demuestra que el modelo posee un proceso mental idéntico al humano.

---

# 46. Una definición técnica de alto nivel

Después de todo lo anterior podemos formular una definición más precisa:

> **Un modelo de inteligencia artificial es una función parametrizada, aprendida mediante un proceso de optimización sobre datos, que transforma representaciones de entrada en salidas según patrones incorporados en sus parámetros y según las condiciones de inferencia.**

Para un LLM:

> **Un LLM es un modelo de lenguaje de gran escala que aprende representaciones y distribuciones sobre secuencias de tokens y puede utilizarlas durante la inferencia para generar o transformar contenido lingüístico, código y, dependiendo de su arquitectura, información multimodal.**

---

# 47. Definición para recordar

Si solamente debes recordar cinco ideas de este capítulo:

### 1. Un modelo aprende patrones

```text
datos → entrenamiento → parámetros
```

### 2. Los parámetros representan parte de lo aprendido

```text
parámetros → comportamiento aprendido
```

### 3. La inferencia utiliza lo aprendido

```text
entrada + modelo → salida
```

### 4. Un prompt normalmente no reentrena el modelo

```text
prompt → condiciona la inferencia
```

### 5. Un LLM genera secuencias de tokens

```text
contexto → probabilidades → token → contexto actualizado → ...
```

---

# 48. Relación con los siguientes capítulos

Este capítulo proporciona la base conceptual para comprender los siguientes:

```text
03 — ¿Qué es un modelo?
        │
        ├── 04 — Parámetros
        │
        ├── 05 — Tokens
        │
        ├── 06 — Contexto
        │
        ├── 07 — Inferencia
        │
        ├── 08 — Entrenamiento vs. inferencia
        │
        └── 09 — Limitaciones de los LLM
```

Posteriormente, en la sección de arquitectura:

```text
Tokens
  ↓
Embeddings
  ↓
Transformers
  ↓
Attention
  ↓
Arquitectura
  ↓
Dense / MoE
  ↓
Sampling
  ↓
Generación
```

El objetivo no es convertir al estudiante en especialista en redes neuronales antes de aprender prompting.

El objetivo es que pueda responder correctamente:

> **¿Qué estoy controlando realmente cuando escribo un prompt?**

La respuesta comienza aquí:

> **No estoy reprogramando el modelo. Estoy proporcionando condiciones de entrada y contexto que condicionan su comportamiento durante la inferencia.**
