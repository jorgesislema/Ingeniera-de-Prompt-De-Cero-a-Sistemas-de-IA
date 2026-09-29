# 01. Conceptos fundamentales de los modelos de lenguaje

## Cómo funciona un modelo de lenguaje y por qué estos conceptos importan para diseñar prompts

----------

# Introducción: antes de diseñar prompts, debemos entender el modelo

En el módulo anterior aprendimos que la ingeniería de prompts no consiste simplemente en escribir preguntas más largas.

Para diseñar buenos prompts necesitamos comprender, al menos a nivel conceptual, **qué ocurre cuando enviamos texto a un modelo de lenguaje**.

Podemos imaginar el proceso de manera simplificada:

```
Texto del usuario
       ↓
Tokenización
       ↓
Representación numérica
       ↓
Procesamiento mediante Transformer
       ↓
Cálculo de probabilidades
       ↓
Selección de un token
       ↓
Nuevo contexto
       ↓
Repetición
       ↓
Respuesta
```

No debemos interpretar este esquema como si el modelo siguiera literalmente una serie de pasos independientes y aislados. En realidad, muchas operaciones ocurren mediante múltiples capas y transformaciones matemáticas.

Sin embargo, este modelo mental es suficiente para comenzar.

La pregunta fundamental de este módulo es:

> **¿Cómo pasa una secuencia de texto a convertirse en una respuesta generada por un modelo de lenguaje?**

----------

# 1. ¿Qué es un modelo de lenguaje?

Un **modelo de lenguaje** es un sistema que aprende patrones estadísticos y representaciones sobre el lenguaje a partir de grandes cantidades de datos.

Una de sus funciones fundamentales durante la generación es estimar qué elementos pueden continuar una secuencia determinada.

Por ejemplo:

```
El cielo es de color...
```

El modelo puede asignar diferentes probabilidades a posibles continuaciones:

```
azul       → probabilidad alta
verde      → probabilidad menor
automóvil  → probabilidad muy baja
```

Esto no significa que el modelo simplemente busque una frase almacenada en una base de datos.

Durante la inferencia utiliza los parámetros aprendidos durante el entrenamiento para calcular una distribución de probabilidad condicionada por el contexto.

Podemos representarlo de forma simplificada:

```
P(siguiente token | contexto)
```

Es decir:

> «¿Qué probabilidad tiene cada posible siguiente token dado todo el contexto disponible?»

----------

# 2. ¿El modelo piensa como una persona?

No debemos utilizar esta analogía literalmente.

Cuando decimos:

> «El modelo piensa».

normalmente estamos utilizando una simplificación para describir un proceso computacional complejo.

El modelo no necesita tener una mente humana para producir una respuesta coherente.

Por eso, en este repositorio utilizaremos preferentemente expresiones como:

-   procesa;
-   calcula;
-   representa;
-   transforma;
-   estima;
-   genera;
-   predice.

En lugar de afirmar como hecho que:

> «La IA piensa exactamente como un humano».

----------

# 3. Tokens: la unidad que el modelo procesa

## 3.1 ¿Qué es un token?

Un **token** es una unidad de texto definida por el sistema de tokenización utilizado por un modelo.

Un token puede representar:

-   una palabra completa;
-   parte de una palabra;
-   varios caracteres;
-   un espacio junto con otros caracteres;
-   signos de puntuación;
-   caracteres especiales.

Por eso:

> **Token no significa necesariamente palabra.**

### Ejemplo conceptual

La frase:

```
Hola mundo
```

podría dividirse de una determinada manera:

```
["Hola", " mundo"]
```

Pero otro tokenizador podría dividirla de forma diferente.

Por ejemplo:

```
["H", "ola", " mundo"]
```

Estos ejemplos son únicamente ilustrativos.

La división real depende del tokenizador concreto utilizado por el modelo.

----------

# 4. ¿Por qué existen los tokens?

Los modelos de lenguaje trabajan con representaciones numéricas.

Una computadora no recibe directamente:

```
Hola Jorge, ¿cómo estás?
```

como una secuencia semántica humana.

Primero necesita transformar el texto a unidades que puedan convertirse en representaciones numéricas.

Podemos imaginarlo así:

```
Texto
  ↓
Tokens
  ↓
Identificadores numéricos
  ↓
Representaciones vectoriales
  ↓
Procesamiento matemático
```

Por ejemplo, de manera simplificada:

```
"Hola mundo"
       ↓
["Hola", " mundo"]
       ↓
[15342, 8271]
```

Los números anteriores son únicamente ilustrativos.

No debemos memorizar números concretos porque cada vocabulario y modelo puede utilizar identificadores diferentes.

----------

# 5. Tokenización: una analogía sencilla

Imagina una biblioteca.

Un libro contiene:

```
"El aprendizaje automático permite analizar grandes cantidades de datos."
```

El bibliotecario podría dividirlo en unidades para organizarlo:

```
El
aprendizaje
automático
permite
analizar
...
```

Un sistema de tokenización hace algo parecido, pero con reglas propias.

No necesariamente utiliza palabras completas.

Puede dividir:

```
programación
```

en unidades como:

```
programa + ción
```

o de otra manera dependiendo del tokenizador.

La idea importante es:

> **El modelo no necesita que cada token sea una palabra completa.**

----------

# 6. ¿Cuántos tokens tiene un texto?

No existe una conversión universal como:

> «Una palabra siempre equivale a dos tokens».

La cantidad depende de:

-   idioma;
-   tokenizador;
-   modelo;
-   caracteres utilizados;
-   código;
-   símbolos;
-   espacios;
-   estructura del texto.

Por ejemplo, el código puede tener una relación entre caracteres y tokens muy diferente a la prosa normal.

Por eso debemos evitar reglas rígidas como:

```
1 token = 0,75 palabras
```

y utilizarlas únicamente como aproximaciones cuando una herramienta específica las respalde.

Para conocer el número real de tokens debemos utilizar el **tokenizador correspondiente al modelo o servicio**.

----------

# 7. Tokens y costo

En muchos servicios de modelos de lenguaje, el uso se factura utilizando tokens.

Normalmente podemos encontrar dos categorías:

```
Tokens de entrada
+
Tokens de salida
```

### Entrada

Es la información enviada al modelo.

Puede incluir:

```
Prompt
+
Instrucciones
+
Historial
+
Documentos
+
Resultados de herramientas
```

### Salida

Es la información generada por el modelo.

Por ejemplo:

```
Entrada:
2.000 tokens

Salida:
500 tokens
```

El costo dependerá del proveedor y del modelo.

Por eso no debemos memorizar precios dentro de un repositorio educativo permanente.

Los precios cambian con frecuencia.

Es mejor enseñar:

> **El costo de un sistema de IA depende, entre otros factores, del modelo, los tokens procesados, la modalidad de uso y las políticas de facturación del proveedor.**

----------

# 8. Un ejemplo real de economía de tokens

Supongamos que una aplicación procesa 10.000 solicitudes.

Cada solicitud contiene:

```
5.000 tokens de entrada
+
1.000 tokens de salida
```

Entonces tenemos:

```
Entrada:
10.000 × 5.000
= 50.000.000 tokens

Salida:
10.000 × 1.000
= 10.000.000 tokens
```

Total:

```
60.000.000 tokens
```

Si conseguimos reducir la entrada de:

```
5.000 → 2.000 tokens
```

la entrada pasa a:

```
10.000 × 2.000
= 20.000.000 tokens
```

Hemos reducido considerablemente el volumen procesado.

Por eso la optimización de contexto no es únicamente una cuestión de «hacer prompts cortos».

Puede convertirse en una decisión de:

-   costo;
-   latencia;
-   capacidad;
-   escalabilidad;
-   arquitectura.

----------

# 9. La ventana de contexto

La **ventana de contexto** es la cantidad máxima de información que un modelo puede considerar dentro de una determinada interacción o solicitud, según las características del modelo y del sistema que lo utiliza.

Puede incluir:

```
Instrucciones
+
Prompt
+
Historial
+
Documentos
+
Resultados de herramientas
+
Otros datos
```

No debemos confundir:

> **ventana de contexto**

con:

> **memoria humana permanente**.

Son conceptos diferentes.

----------

# 10. Ejemplo de ventana de contexto

Imaginemos un modelo con una ventana de contexto de:

```
100.000 tokens
```

Eso no significa que debamos enviar:

```
100.000 tokens
```

en cada consulta.

Supongamos que queremos responder una pregunta sobre un contrato de 300 páginas.

Una estrategia poco eficiente sería enviar constantemente:

```
Contrato completo
+
historial completo
+
documentos anteriores
+
pregunta
```

Una estrategia más eficiente podría ser:

```
Contrato
   ↓
Procesamiento
   ↓
Extracción de información relevante
   ↓
Contexto reducido
   ↓
Modelo
```

Aquí aparece una idea fundamental:

> **La capacidad máxima del modelo no determina cuánto contexto debemos utilizar.**

----------

# 11. Más contexto no significa mejor respuesta

Este es uno de los errores más importantes que debe evitar un ingeniero de prompts.

Imaginemos:

```
Pregunta:
¿Cuál es el valor total de las ventas de enero?
```

Y enviamos:

```
Toda la base de datos de la empresa
+
correos electrónicos
+
manuales
+
contratos
+
informes
+
historial completo
```

Podríamos estar dificultando la tarea.

El objetivo no es proporcionar:

> «todo lo que tenemos».

El objetivo es proporcionar:

> **la información necesaria para resolver correctamente la tarea.**

----------

# 12. El problema de la información irrelevante

Supongamos:

```
Pregunta:
¿Cuál es el precio del producto A?
```

Contexto:

```
Producto A → $50

Historia de la empresa
Manual de recursos humanos
Lista de empleados
Política de vacaciones
50 páginas de documentación
Información financiera
Producto B
Producto C
Producto D
```

La información relevante es:

```
Producto A → $50
```

El resto puede ser ruido.

Por eso:

> **El contexto debe ser relevante, no simplemente abundante.**

----------

# 13. "Lost in the Middle"

Existe una línea importante de investigación sobre cómo los modelos pueden presentar dificultades para utilizar información dependiendo de su posición dentro de contextos largos.

Un fenómeno conocido como **«Lost in the Middle»** describe situaciones en las que información ubicada en determinadas posiciones intermedias de un contexto largo puede utilizarse peor que información situada en otras posiciones.

Pero debemos evitar convertir esto en una regla absoluta como:

> «Todo lo que pongas en el medio será ignorado».

No es cierto.

El comportamiento depende de:

-   modelo;
-   tarea;
-   longitud del contexto;
-   estructura;
-   relevancia de la información;
-   posición;
-   instrucciones;
-   arquitectura;
-   mecanismo de recuperación utilizado.

----------

# 14. Cómo organizar contexto largo

Cuando tenemos documentos extensos podemos mejorar su utilización mediante estructuras claras.

Por ejemplo:

```
# OBJETIVO

Determinar si existen inconsistencias en las ventas.

# DATOS RELEVANTES

...

# DOCUMENTO 1

...

# DOCUMENTO 2

...

# CRITERIOS DE ANÁLISIS

...

# FORMATO DE RESPUESTA

...
```

Los encabezados no son magia.

Su función principal es **hacer que la información tenga una estructura explícita y fácil de interpretar**.

----------

# 15. Embeddings: convertir información en vectores

Ahora aparece uno de los conceptos más importantes.

Un modelo necesita representar información de una forma que pueda procesar matemáticamente.

Una de esas representaciones son los **vectores**.

Podemos imaginar:

```
"gato"
   ↓
[0.21, -0.53, 0.77, ...]
```

El vector real puede tener cientos o miles de dimensiones.

No debemos interpretar cada número como:

```
posición 1 = animal
posición 2 = tamaño
posición 3 = color
```

La representación es mucho más compleja.

----------

# 16. Una analogía para entender los embeddings

Imagina un mapa.

Tenemos:

```
gato
perro
león
automóvil
avión
pizza
```

En un espacio conceptual, podemos representar elementos de manera que algunos relacionados estén más próximos que otros.

Por ejemplo:

```
             gato
              ●
          ● perro

      ● león


                         ● automóvil

                                  ● avión

              ● pizza
```

La posición exacta de un concepto no tiene por qué corresponder a una propiedad humana concreta.

Lo importante es que las representaciones vectoriales pueden capturar **relaciones estadísticas aprendidas de los datos**.

----------

# 17. Embeddings estáticos y contextuales

Esta distinción es importante.

En representaciones estáticas tradicionales, una palabra podía tener una representación esencialmente fija.

Pero en modelos modernos basados en Transformers, las representaciones internas pueden cambiar según el contexto.

Consideremos:

```
El banco aprobó el préstamo.
```

y:

```
Me senté en el banco del parque.
```

La palabra:

```
banco
```

aparece igual, pero el contexto cambia completamente su significado.

Un sistema contextual puede representar esa palabra de manera diferente según la oración.

Por eso:

> **El significado que utiliza un modelo no depende únicamente de una palabra aislada, sino también de su contexto.**

----------

# 18. Atención: cómo se relacionan los tokens

Uno de los mecanismos fundamentales de los Transformers es la **atención**.

La idea intuitiva es:

> Cada elemento de una secuencia puede utilizar información de otros elementos para construir una representación contextual.

Consideremos:

```
El perro mordió al hombre porque estaba asustado.
```

Para interpretar:

```
estaba asustado
```

es importante analizar las relaciones entre las diferentes palabras.

La atención permite que el modelo establezca relaciones entre posiciones de la secuencia.

----------

# 19. Una analogía sencilla para entender la atención

Imagina una reunión con cinco personas.

Cada persona escucha a las demás.

Pero no todas las conversaciones tienen la misma importancia.

Para resolver una pregunta concreta:

```
¿Quién compró el automóvil?
```

una persona puede prestar mucha atención a:

```
Carlos
compró
el automóvil
```

y poca atención a información irrelevante.

La atención funciona como un mecanismo matemático que permite ponderar relaciones entre elementos.

No debemos interpretarlo como si el modelo tuviera «una mirada humana».

Es una analogía para entender la función.

----------

# 20. La fórmula de atención

La formulación clásica de la atención utilizada en Transformers es:

Attention(Q,K,V)=softmax(QKTdk)VAttention(Q,K,V) = softmax\left( \frac{QK^T}{\sqrt{d_k}} \right)V

No es necesario memorizarla todavía.

Debemos comprender qué significa.

### Q — Query

Representa:

> «¿Qué información estoy buscando?»

### K — Key

Representa:

> «¿Qué características tengo que podrían ser relevantes para una búsqueda?»

### V — Value

Representa:

> «¿Qué información debo aportar si resulto relevante?»

De forma conceptual:

```
Query
  ↓
Busca relaciones con Keys
  ↓
Calcula pesos
  ↓
Utiliza esos pesos sobre Values
  ↓
Nueva representación
```

----------

# 21. ¿Por qué aparece √dk?

La división por:

dk\sqrt{d_k}

ayuda a controlar la escala de los productos internos entre vectores.

Sin esta normalización, los valores pueden crecer demasiado cuando aumenta la dimensión de los vectores, haciendo que la función `softmax` produzca distribuciones excesivamente concentradas.

Para un estudiante principiante, lo importante es recordar:

> **La escala ayuda a mantener el cálculo numéricamente estable y adecuado para la función softmax.**

El detalle matemático se estudiará con mayor profundidad en módulos posteriores.

----------

# 22. Del texto a la respuesta

Podemos construir ahora un modelo mental simplificado:

```
"Escribe un correo profesional"
             ↓
       Tokenización
             ↓
    Identificadores de tokens
             ↓
Representaciones vectoriales
             ↓
       Capas Transformer
             ↓
      Atención y otras
      transformaciones
             ↓
           Logits
             ↓
        Probabilidades
             ↓
Selección del siguiente token
             ↓
"Estimado..."
             ↓
Nuevo contexto
             ↓
Siguiente token
             ↓
...
```

----------

# 23. ¿Qué son los logits?

Los **logits** son valores numéricos que representan las puntuaciones que el modelo asigna a posibles tokens antes de convertirlas en probabilidades mediante una función como `softmax`.

Imaginemos un vocabulario muy pequeño:

```
azul      → 4.8
verde     → 2.1
rojo      → 1.2
automóvil → -2.5
```

Estos valores no son todavía probabilidades.

Después de aplicar `softmax` obtenemos una distribución de probabilidad.

Simplificando:

```
azul       → 0.91
verde      → 0.06
rojo       → 0.02
automóvil  → 0.01
```

Los valores anteriores son únicamente ilustrativos.

----------

# 24. ¿Cómo se genera una respuesta?

Supongamos:

```
El cielo es
```

El modelo calcula probabilidades para el siguiente token.

Podría obtener:

```
azul       → 0.80
gris       → 0.10
rojo       → 0.05
verde      → 0.05
```

Se selecciona un token.

Supongamos:

```
azul
```

Ahora tenemos:

```
El cielo es azul
```

El modelo vuelve a calcular el siguiente token.

Después:

```
El cielo es azul durante
```

Y continúa.

Por eso podemos representar la generación como:

```
Contexto
   ↓
Predicción
   ↓
Nuevo token
   ↓
Contexto ampliado
   ↓
Nueva predicción
   ↓
Nuevo token
   ↓
...
```

----------

# 25. Esto no significa que el modelo sea simplemente un "autocompletado"

Decir:

> «Un LLM es solo un autocompletado».

es una simplificación excesiva.

La predicción del siguiente token es un mecanismo fundamental de generación, pero los modelos modernos desarrollan representaciones internas complejas que permiten realizar tareas como:

-   clasificación;
-   traducción;
-   generación de código;
-   extracción de información;
-   resumen;
-   razonamiento;
-   análisis multimodal, cuando el modelo lo permite;
-   uso de herramientas.

Por eso utilizaremos:

> **predicción del siguiente token**

como concepto fundamental, pero no como explicación completa de todas las capacidades del modelo.

----------

# 26. Sampling: cómo se selecciona el siguiente token

El modelo puede tener varias opciones plausibles.

Por ejemplo:

```
Respuesta A → 0,50
Respuesta B → 0,30
Respuesta C → 0,15
Respuesta D → 0,05
```

Existen diferentes estrategias para seleccionar tokens.

Entre ellas:

-   selección codiciosa;
-   muestreo;
-   temperatura;
-   `top-k`;
-   `top-p`.

Estas técnicas modifican cómo se selecciona la siguiente unidad a partir de la distribución calculada.

----------

# 27. Temperatura

La temperatura modifica la distribución utilizada durante el muestreo.

De forma intuitiva:

### Temperatura baja

El sistema tiende a concentrarse más en las opciones de mayor probabilidad.

```
A → 0,85
B → 0,10
C → 0,05
```

### Temperatura más alta

La distribución puede hacerse más uniforme:

```
A → 0,50
B → 0,30
C → 0,20
```

La interpretación exacta depende del sistema y de cómo implemente el muestreo.

Por eso debemos evitar reglas universales como:

```
0 = siempre correcto
1 = creativo
```

La temperatura **no convierte un modelo en inteligente o poco inteligente**.

Afecta el proceso de selección de tokens.

----------

# 28. Top-p

`top-p`, también conocido como **muestreo de núcleo**, limita la selección a un conjunto de tokens cuya probabilidad acumulada alcanza determinado valor.

Ejemplo conceptual:

```
A → 0,50
B → 0,25
C → 0,15
D → 0,05
E → 0,05
```

Con:

```
top-p = 0,75
```

podrían considerarse:

```
A + B
```

porque juntos alcanzan:

```
0,75
```

La implementación concreta puede variar según el proveedor.

----------

# 29. Temperature y top-p no son sustitutos mágicos del buen prompting

Un error frecuente es intentar solucionar un prompt mal diseñado cambiando parámetros.

Ejemplo:

```
Prompt ambiguo
+
temperature = 0
```

no necesariamente produce una respuesta correcta.

Si la información necesaria no está presente:

```
Prompt ambiguo
+
temperature = 0
```

seguirá siendo un prompt ambiguo.

Por eso:

> **Los parámetros de generación no sustituyen un buen diseño de la tarea.**

----------

# 30. La estructura de un buen prompt

El documento original utiliza:

```
Persona + Contexto + Instrucción
```

Como modelo pedagógico es útil, pero para un repositorio avanzado debemos ampliarlo.

Una estructura más general es:

```
OBJETIVO
   ↓
CONTEXTO
   ↓
DATOS
   ↓
INSTRUCCIONES
   ↓
RESTRICCIONES
   ↓
CRITERIOS DE CALIDAD
   ↓
FORMATO DE SALIDA
```

No todos los prompts necesitan todos los componentes.

----------

# 31. Ejemplo: prompt demasiado simple

```
Analiza este código.
```

El modelo debe adivinar:

-   qué lenguaje;
-   qué problema buscar;
-   qué nivel de revisión;
-   si debe modificarlo;
-   si debe buscar errores de seguridad;
-   si debe analizar rendimiento;
-   cómo presentar los resultados.

----------

# 32. Ejemplo mejor estructurado

```
OBJETIVO:

Revisar el siguiente código Python para identificar errores
lógicos y posibles problemas de seguridad.

CONTEXTO:

El código forma parte de una aplicación que procesa archivos CSV.

INSTRUCCIONES:

1. Identifica los problemas encontrados.
2. Explica por qué representan un problema.
3. Indica la línea afectada.
4. Propón una corrección.

RESTRICCIONES:

No reescribas todo el programa.

CRITERIOS:

Prioriza errores que puedan producir pérdida de datos,
ejecuciones incorrectas o vulnerabilidades.

FORMATO:

| Línea | Problema | Riesgo | Corrección |
```

Ahora el modelo tiene mucha menos ambigüedad.

----------

# 33. ¿Necesitamos siempre asignar un rol?

No.

Esta es una corrección importante respecto al documento original.

Podemos utilizar:

```
Actúa como experto en Python.
```

cuando realmente aporte valor.

Pero no debemos pensar que:

> «Actúa como experto» automáticamente convierte al modelo en experto.

El modelo no obtiene mágicamente nuevos conocimientos porque escribamos:

```
Eres el mejor programador del mundo.
```

El conocimiento y las capacidades del modelo dependen de su entrenamiento, arquitectura, contexto, herramientas y configuración.

El rol puede ayudar a orientar:

-   perspectiva;
-   estilo;
-   prioridades;
-   tipo de respuesta.

Pero no crea capacidades que el modelo no posee.

----------

# 34. El poder real del contexto

Comparemos:

### Prompt A

```
Haz un análisis financiero.
```

### Prompt B

```
Analiza estos estados financieros.

Objetivo:
Identificar inconsistencias entre ingresos, gastos y utilidad.

Contexto:
La empresa es una pequeña empresa comercial.

Datos:
[datos]

Restricciones:
No inventes cifras.
Si falta información, indícalo.

Salida:
1. Hallazgo
2. Evidencia
3. Impacto
4. Información faltante
```

El segundo prompt proporciona información que permite reducir la ambigüedad.

----------

# 35. Especificidad no significa escribir más

Este principio es fundamental.

Un prompt de:

```
2.000 palabras
```

no necesariamente es mejor que uno de:

```
200 palabras
```

La pregunta correcta es:

> **¿La información incluida ayuda a resolver la tarea?**

Comparemos:

```
Haz un informe profesional, excelente,
muy completo, claro, preciso, detallado,
interesante, profundo y de alta calidad.
```

con:

```
Genera un informe de 800 palabras.

Debe contener:
1. Problema
2. Evidencia
3. Causas
4. Riesgos
5. Recomendaciones

No inventes datos.
Cuando una afirmación no pueda verificarse,
indícalo explícitamente.
```

El segundo contiene menos palabras, pero más información operacional.

----------

# 36. Especificidad progresiva

No siempre conocemos desde el principio el prompt perfecto.

Podemos construirlo progresivamente.

### Versión 1

```
Ayúdame con mi currículo.
```

### Versión 2

```
Ayúdame a mejorar mi currículo para trabajar en ciencia de datos.
```

### Versión 3

```
Revisa mi currículo para un puesto junior de ciencia de datos.
Tengo experiencia en Python, SQL y aprendizaje automático.
```

### Versión 4

```
Revisa mi currículo para un puesto junior de ciencia de datos.

Contexto:
Tengo formación en Python, SQL y aprendizaje automático.

Objetivo:
Mejorar la claridad y relevancia del currículo.

Analiza:
1. Perfil profesional.
2. Experiencia.
3. Proyectos.
4. Habilidades.

No inventes experiencia.

Para cada problema indica:
- problema;
- motivo;
- propuesta de mejora.
```

El aprendizaje consiste en comprender **qué información adicional hizo mejorar el prompt**.

----------

# 37. Ejemplos dentro del prompt

Los ejemplos pueden ser especialmente útiles cuando queremos controlar:

-   formato;
-   estilo;
-   clasificación;
-   estructura;
-   transformación de datos.

Por ejemplo:

```
Clasifica cada comentario como POSITIVO o NEGATIVO.

Ejemplo:

Entrada:
"El producto llegó rápidamente."

Salida:
POSITIVO

Entrada:
"El producto llegó roto."

Salida:
NEGATIVO

Ahora clasifica:

"El producto funciona correctamente, pero llegó tarde."
```

El modelo recibe ejemplos concretos de la tarea.

A esto se lo conoce como **few-shot prompting** cuando proporcionamos ejemplos para orientar la tarea.

----------

# 38. Restricciones

Una restricción define límites.

Ejemplo:

```
Explica qué es un token.
```

Puede producir una respuesta extensa.

Podemos especificar:

```
Explica qué es un token para un estudiante
que nunca ha programado.

Máximo:
150 palabras.

Incluye:
1 ejemplo cotidiano.
1 ejemplo técnico.
```

Las restricciones pueden controlar:

-   longitud;
-   formato;
-   audiencia;
-   información permitida;
-   información prohibida;
-   estructura;
-   criterios de calidad.

----------

# 39. Formato de salida

Una de las herramientas más útiles para trabajar con modelos es especificar claramente la estructura de salida.

Por ejemplo:

```
Devuelve el resultado con esta estructura:

Problema:
...

Causa:
...

Riesgo:
...

Recomendación:
...
```

Para datos estructurados:

```
{
  "problema": "",
  "causa": "",
  "riesgo": "",
  "recomendacion": ""
}
```

Pero debemos comprender algo importante:

> **Mostrar un formato en el prompt ayuda a orientar la salida, pero no garantiza por sí solo que el modelo produzca JSON válido.**

En aplicaciones profesionales podemos utilizar mecanismos específicos de salida estructurada, esquemas o validación mediante código, cuando el proveedor los ofrece.

----------

# 40. Chat frente a API

Existen diferentes formas de utilizar un modelo.

## Chat

```
Usuario
   ↓
Interfaz
   ↓
Modelo
   ↓
Respuesta
```

Es adecuado para:

-   aprendizaje;
-   exploración;
-   análisis;
-   redacción;
-   experimentación;
-   tareas individuales.

## API

```
Aplicación
   ↓
API
   ↓
Modelo
   ↓
Respuesta
   ↓
Aplicación
```

Permite integrar modelos dentro de:

-   aplicaciones;
-   sistemas empresariales;
-   automatizaciones;
-   agentes;
-   procesos de análisis;
-   sistemas de atención al cliente.

La diferencia fundamental no está en que «los prompts sean diferentes».

Los principios de diseño siguen siendo aplicables.

La diferencia principal está en **cómo se integra el modelo dentro del sistema**.

----------

# 41. Modelos densos y modelos de expertos

Los modelos pueden utilizar diferentes arquitecturas.

Una distinción importante es:

### Modelo denso

De manera simplificada:

```
Entrada
  ↓
Gran parte de los parámetros
  ↓
Salida
```

### Modelo MoE

**Mixture of Experts**, o mezcla de expertos:

```
Entrada
  ↓
Mecanismo de enrutamiento
  ↓
Expertos seleccionados
  ↓
Combinación
  ↓
Salida
```

En un modelo MoE no necesariamente se activan todos los parámetros para cada token.

Esto puede permitir construir modelos con una gran cantidad total de parámetros manteniendo un costo computacional por token menor que si todos los parámetros estuvieran activos.

Pero:

> **MoE no significa automáticamente mejor ni peor.**

El rendimiento depende de la arquitectura concreta, entrenamiento, expertos, enrutamiento, tarea y otros factores.

----------

# 42. Modelos multimodales

Algunos modelos pueden trabajar con más de una modalidad.

Por ejemplo:

```
Texto
+
Imagen
```

o:

```
Texto
+
Audio
```

o:

```
Texto
+
Imagen
+
Audio
```

Esto permite tareas como:

```
Imagen de una factura
       ↓
Modelo multimodal
       ↓
Extracción de información
```

Pero nuevamente debemos distinguir:

> **capacidad del modelo**

de:

> **capacidad de la aplicación que lo integra**.

Que un modelo pueda procesar imágenes no significa que automáticamente pueda realizar OCR perfecto, detectar cualquier objeto o interpretar cualquier documento correctamente.

----------

# 43. Cómo seleccionar un modelo

No debemos enseñar:

> «Modelo X es el mejor».

La selección depende de la tarea.

Debemos considerar:

Criterio

Pregunta

Calidad

¿Qué nivel de precisión necesito?

Costo

¿Cuánto puedo gastar?

Latencia

¿Qué tan rápido necesito una respuesta?

Contexto

¿Cuánta información necesito procesar?

Modalidades

¿Necesito texto, imagen, audio o video?

Herramientas

¿Necesito búsqueda, código o funciones?

Privacidad

¿Dónde pueden procesarse los datos?

Disponibilidad

¿El modelo está disponible donde lo necesito?

Licencia

¿Puedo utilizarlo comercialmente?

Infraestructura

¿Necesito ejecutarlo localmente?

La selección de modelos será tratada con más profundidad en módulos posteriores.

----------

# 44. Modelos y versiones cambian

Los modelos comerciales cambian con frecuencia.

Por esa razón, un repositorio educativo no debería depender de una tabla fija de:

```
Modelo X = mejor
Modelo Y = peor
Precio X = ...
```

porque esa información puede quedar obsoleta.

Es mejor enseñar un **método de selección**.

Cuando se necesite comparar modelos concretos, debemos consultar la documentación y precios actuales del proveedor.

----------

# 45. Un error frecuente: intentar resolver todo con prompting

Supongamos que un sistema responde mal sobre productos.

Podemos intentar:

```
Mejorar el prompt.
```

Pero el problema podría estar en:

```
Datos incorrectos
      ↓
Base de conocimiento desactualizada
      ↓
Sistema de recuperación defectuoso
      ↓
Contexto incorrecto
      ↓
Modelo
      ↓
Prompt
```

Por eso un ingeniero debe diagnosticar antes de modificar.

La pregunta no es:

> «¿Cómo hago un prompt más grande?»

La pregunta es:

> **«¿Dónde está realmente el problema?»**

----------

# 46. Checklist antes de diseñar un prompt

## Antes

```
[ ] ¿Cuál es exactamente el objetivo?
[ ] ¿Cómo sabré si el resultado es correcto?
[ ] ¿Qué información necesita el modelo?
[ ] ¿Qué información es irrelevante?
[ ] ¿Existen restricciones?
[ ] ¿Qué formato necesito?
[ ] ¿Qué nivel de riesgo tiene la tarea?
[ ] ¿Necesito fuentes externas?
[ ] ¿Necesito herramientas?
[ ] ¿Qué modelo es apropiado?
```

## Durante el diseño

```
[ ] Objetivo claro
[ ] Contexto suficiente
[ ] Datos relevantes
[ ] Instrucciones concretas
[ ] Restricciones
[ ] Criterios de calidad
[ ] Formato de salida
[ ] Ejemplos cuando aporten valor
```

## Después

```
[ ] ¿Respondió exactamente a la tarea?
[ ] ¿Respetó el formato?
[ ] ¿Utilizó correctamente el contexto?
[ ] ¿Inventó información?
[ ] ¿Los datos son verificables?
[ ] ¿El resultado es útil?
[ ] ¿Debe modificarse el prompt?
[ ] ¿El problema realmente estaba en el prompt?
```

----------

# 47. Resumen conceptual

Al terminar este módulo debes poder explicar:

### Token

> Unidad de texto definida por el tokenizador que el modelo procesa como parte de su entrada o salida.

### Tokenización

> Proceso mediante el cual un texto se divide en tokens y estos se representan mediante identificadores que el sistema puede procesar.

### Embedding

> Representación vectorial utilizada para representar información de manera que pueda ser procesada matemáticamente y capturar relaciones aprendidas.

### Atención

> Mecanismo que permite que las representaciones de diferentes posiciones de una secuencia influyan entre sí de forma ponderada.

### Ventana de contexto

> Cantidad de información que el modelo puede considerar dentro de una determinada interacción, de acuerdo con las características de ese modelo y sistema.

### Logits

> Puntuaciones producidas antes de convertirlas en una distribución de probabilidades para seleccionar posibles tokens.

### Inferencia

> Proceso mediante el cual el modelo utiliza sus parámetros y el contexto proporcionado para producir una salida.

### Prompt

> Conjunto de instrucciones, contexto, datos y restricciones proporcionados al modelo para orientar una tarea.

----------

# 48. La idea más importante del módulo

No memorices:

```
Tokens
Embeddings
Attention
Logits
Sampling
```

como palabras independientes.

Debes poder visualizar la relación:

```
                 TEXTO
                   ↓
              TOKENIZACIÓN
                   ↓
                TOKENS
                   ↓
          REPRESENTACIONES
             VECTORIALES
                   ↓
              TRANSFORMER
                   ↓
              ATENCIÓN
                   ↓
          OTRAS TRANSFORMACIONES
                   ↓
                 LOGITS
                   ↓
            PROBABILIDADES
                   ↓
               MUESTREO
                   ↓
           SIGUIENTE TOKEN
                   ↓
             REPETICIÓN
                   ↓
                SALIDA
```

Y debes comprender una segunda relación todavía más importante:

```
              CALIDAD DEL SISTEMA
                     ↑
      ┌──────────────┼──────────────┐
      │              │              │
     Datos        Contexto        Modelo
      │              │              │
      └──────────────┼──────────────┘
                     │
                   Prompt
                     │
                  Herramientas
                     │
                 Evaluación
                     │
                 Iteración
```

**Un buen prompt no compensa datos deficientes, un modelo inadecuado o una arquitectura mal diseñada.**

Ese concepto será fundamental cuando pasemos de **ingeniería de prompts** a **ingeniería de sistemas de IA**.

----------

# Lo que viene después

## Módulo 02: Pipeline de procesamiento de un modelo de lenguaje

En el siguiente módulo profundizaremos en el recorrido interno:

```
Texto
 ↓
Tokenización
 ↓
IDs
 ↓
Embeddings
 ↓
Positional information
 ↓
Self-Attention
 ↓
Feed-Forward Networks
 ↓
Capas Transformer
 ↓
Logits
 ↓
Softmax
 ↓
Sampling
 ↓
Token generado
```

Aquí comenzaremos a pasar de la explicación conceptual a la **explicación matemática y computacional**.

El objetivo será que un estudiante que inicialmente no sabe programar pueda entender la idea con ejemplos sencillos, mientras que un programador pueda posteriormente profundizar en las ecuaciones, matrices, vectores y código Python.

----------

## Principios que debes conservar

```
1. Un token no es necesariamente una palabra.

2. Cada modelo puede utilizar un tokenizador diferente.

3. Más contexto no significa necesariamente mejor contexto.

4. Un modelo con una ventana enorme no obliga a utilizarla completa.

5. Un rol puede orientar una respuesta, pero no crea conocimientos mágicamente.

6. Un prompt estructurado reduce ambigüedad, pero no garantiza exactitud.

7. La temperatura modifica el muestreo; no convierte una respuesta incorrecta en correcta.

8. Un formato solicitado no sustituye la validación.

9. Un modelo puede producir una respuesta convincente y aun así equivocarse.

10. Prompt engineering es una parte de la ingeniería de IA, no toda la ingeniería de IA.
```