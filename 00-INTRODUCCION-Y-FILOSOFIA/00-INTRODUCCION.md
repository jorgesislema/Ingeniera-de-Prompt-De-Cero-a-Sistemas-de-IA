
# 00. Introducción y filosofía del ingeniero de prompts

## Cómo utilizar este repositorio

Este repositorio está diseñado como una ruta progresiva para aprender **ingeniería de prompts**, desde los conceptos fundamentales hasta el diseño de sistemas avanzados basados en modelos de lenguaje.

La ruta está pensada para personas con diferentes niveles de experiencia: desde quienes nunca han programado hasta programadores, científicos de datos e ingenieros de IA.

El objetivo no es aprender a escribir instrucciones «bonitas» para una IA. El objetivo es aprender a **diseñar instrucciones reproducibles, evaluables, seguras y adecuadas para un objetivo concreto**.

### Ruta de aprendizaje

Fase

Módulos

Enfoque

**Fundamentos**

00, 01, 02

Filosofía, modelos de lenguaje y funcionamiento básico

**Técnicas**

03, 04

Diseño de prompts, contexto, ejemplos y técnicas avanzadas

**Aplicación**

05, 06, 07, 08

Arquitecturas, herramientas, costos, evaluación y seguridad

**Profesionalización**

09, 10, 11

Plantillas, ejercicios, proyectos y aplicaciones reales

**Referencia e investigación**

12, 13

Técnicas avanzadas, tendencias y glosario

### Recomendaciones de estudio

1.  Estudia los módulos en orden cuando sea tu primera vez con el tema.
2.  Realiza los ejercicios prácticos antes de avanzar.
3.  No memorices las estructuras de los prompts: aprende **por qué funcionan**.
4.  Compara diferentes versiones de un mismo prompt.
5.  Mide los resultados cuando sea posible.
6.  Aprende a identificar cuándo un problema **no se resuelve mejorando el prompt**, sino cambiando el modelo, los datos, las herramientas o la arquitectura.
7.  Aplica cada concepto a problemas reales de tu área profesional.
8.  Registra los errores encontrados y las modificaciones realizadas.

Una regla importante para todo el repositorio será:

> **No basta con obtener una respuesta correcta una vez. Debemos aprender a obtener resultados útiles de forma consistente, verificable y adecuada al contexto.**

----------

# 1. ¿Qué es un ingeniero de prompts?

Una persona puede utilizar una IA simplemente escribiendo una pregunta y leyendo la respuesta.

Eso no es necesariamente malo. Es el uso normal de una herramienta.

El problema aparece cuando necesitamos resultados **repetibles, controlables y evaluables**.

### Usuario ocasional

```
Necesito analizar este documento.
¿Qué opinas?
```

La instrucción deja muchas decisiones en manos del modelo.

### Usuario que diseña un prompt

```
Analiza el documento como un auditor financiero.

Objetivo:
Identificar inconsistencias entre ingresos, gastos y totales.

Instrucciones:
1. Identifica las cifras relevantes.
2. Comprueba los cálculos.
3. Señala inconsistencias.
4. No inventes información que no aparezca en el documento.
5. Si no existen datos suficientes, indícalo explícitamente.

Formato de salida:
- Hallazgo
- Evidencia
- Impacto
- Nivel de riesgo
- Información faltante
```

La diferencia no está únicamente en que el segundo prompt sea más largo.

La diferencia está en que el segundo **reduce ambigüedad, define el objetivo, establece restricciones y especifica cómo evaluar la respuesta**.

Por tanto:

> **La ingeniería de prompts consiste en diseñar instrucciones y contexto para conseguir resultados útiles, consistentes y evaluables de un modelo de IA.**

En sistemas profesionales, además, el prompt es solamente una parte del sistema.

También debemos considerar:

```
Datos
  ↓
Contexto
  ↓
Prompt
  ↓
Modelo
  ↓
Herramientas
  ↓
Respuesta
  ↓
Evaluación
  ↓
Corrección o nueva ejecución
```

Esta idea será fundamental a lo largo de todo el repositorio.

----------

# 2. La filosofía del ingeniero de IA

Un error frecuente consiste en pensar:

> «Si escribo un prompt suficientemente bueno, la IA hará cualquier cosa correctamente».

No es así.

Un modelo de lenguaje tiene capacidades y limitaciones. Genera respuestas a partir de patrones aprendidos y del contexto disponible, pero puede producir información incorrecta, incompleta o inventada.

Por eso, el ingeniero no debe limitarse a preguntar:

> «¿Qué prompt debo escribir?»

Debe aprender a preguntar:

> «¿Cuál es el problema, qué información necesita el modelo, qué resultado espero, cómo voy a medirlo y cómo voy a detectar errores?»

Este cambio de perspectiva es uno de los fundamentos de la ingeniería de IA.

----------

# 3. Las tres reglas fundamentales

## 3.1 Contexto adecuado = mejores decisiones

Un modelo necesita información suficiente para realizar una tarea correctamente.

Pero **más contexto no significa automáticamente mejor contexto**.

### Ejemplo

Supongamos que queremos clasificar una factura.

Podríamos enviar:

```
Aquí tienes 100 páginas de documentos de la empresa.
Analízalos y dime si esta factura tiene errores.
```

O podemos seleccionar la información relevante:

```
Objetivo:
Determinar si la factura coincide con la orden de compra.

Datos relevantes:
- Número de factura: F-1025
- Total facturado: $1.250
- Orden de compra: $1.200
- Impuesto: $150
- Total de la orden: $1.350
```

El segundo enfoque puede ser mucho más eficiente porque proporciona información **relevante y estructurada**.

Además, dependiendo del modelo y del proveedor, procesar más tokens puede aumentar el costo, la latencia o ambas cosas.

Por eso:

> **No debemos buscar el máximo contexto. Debemos buscar el contexto necesario y relevante.**

### Importante

El costo de los tokens depende del modelo, proveedor, modalidad de uso y configuración.

Por tanto, no debemos enseñar:

> «Cada token siempre cuesta dinero».

La formulación correcta es:

> **En muchos servicios de IA, el uso de tokens influye en el costo y en otros recursos computacionales. Por eso, optimizar el contexto puede ser importante tanto económica como técnicamente.**

----------

# 3.2 Seguridad = responsabilidad

Un prompt puede contener información sensible:

-   nombres;
-   direcciones;
-   información financiera;
-   código propietario;
-   credenciales;
-   información de clientes;
-   datos personales;
-   documentos internos.

Antes de enviar información a un modelo externo debemos conocer las políticas y configuración del servicio utilizado.

No debemos asumir que:

> «Si es una IA gratuita, mis datos se utilizarán para entrenar el modelo».

Tampoco debemos asumir lo contrario.

La política depende del proveedor, del producto, del tipo de cuenta y de la configuración correspondiente.

### Ejemplo incorrecto

```
Analiza esta base de datos completa de clientes.

Nombre:
Juan Pérez
Cédula:
XXXXXXXXXX
Teléfono:
0999999999
...
```

### Enfoque más seguro para una prueba

```
Analiza los siguientes registros anonimizados:

Cliente_001
Cliente_002
Cliente_003
```

Y, en un entorno profesional, debemos aplicar controles adicionales de seguridad, privacidad, acceso y almacenamiento.

Por tanto:

> **Antes de introducir información en un sistema de IA, debemos saber qué información estamos enviando, a quién la estamos enviando y bajo qué condiciones será procesada.**

----------

# 3.3 Verificación = calidad

Una respuesta convincente no necesariamente es una respuesta correcta.

Los modelos de lenguaje pueden producir:

-   datos incorrectos;
-   referencias inexistentes;
-   cálculos erróneos;
-   interpretaciones equivocadas;
-   código con errores;
-   información inventada.

A este fenómeno se lo suele denominar **alucinación**.

### Ejemplo

Preguntamos:

```
¿Cuál fue el crecimiento exacto de la empresa X entre 2018 y 2019?
```

El modelo podría proporcionar una cifra con apariencia de precisión aunque no tenga acceso a los datos necesarios.

Una respuesta profesional debería poder decir:

```
No puedo determinar el crecimiento con la información proporcionada.
Necesito los ingresos de 2018 y 2019.
```

Por eso:

> **La confianza de una respuesta no demuestra su veracidad.**

El ingeniero debe diseñar mecanismos de verificación adecuados al riesgo.

### Diferentes niveles de verificación

**Tarea de bajo riesgo**

```
Generar cinco nombres para una aplicación.
```

Puede bastar una revisión humana.

**Tarea de mayor riesgo**

```
Analizar estados financieros.
```

Necesitamos comprobaciones adicionales.

**Tarea crítica**

```
Tomar una decisión que pueda producir consecuencias legales,
financieras, médicas o de seguridad.
```

La respuesta del modelo no debería considerarse suficiente por sí sola. Se necesitan controles, fuentes confiables y revisión humana o sistemas especializados, según el caso.

----------

# 4. Del prompt aislado al sistema

Una de las primeras ideas que debe aprender un estudiante es que un prompt no existe necesariamente de forma aislada.

En una aplicación real podemos tener:

```
Usuario
   ↓
Aplicación
   ↓
Recuperación de información
   ↓
Contexto
   ↓
Prompt
   ↓
Modelo
   ↓
Herramientas
   ↓
Respuesta
   ↓
Validación
   ↓
Usuario
```

Por ejemplo, un chatbot empresarial puede recibir:

```
Pregunta del cliente
        ↓
Identificación de intención
        ↓
Búsqueda en la base de conocimiento
        ↓
Construcción del contexto
        ↓
Modelo de lenguaje
        ↓
Validación
        ↓
Respuesta
```

Aquí, mejorar únicamente el prompt puede no solucionar un problema causado por:

-   información incorrecta;
-   recuperación deficiente;
-   modelo inadecuado;
-   falta de validación;
-   contexto insuficiente;
-   errores de programación.

Esta es una de las diferencias entre **prompt engineering** e **ingeniería de sistemas de IA**.

----------

# 5. Pensamiento sistémico

El pensamiento lineal suele representar el proceso así:

```
Problema
   ↓
Prompt
   ↓
Respuesta
   ↓
Uso
```

El pensamiento sistémico observa más elementos:

```
Usuario
   ↕
Objetivo
   ↕
Datos
   ↕
Contexto
   ↕
Prompt
   ↕
Modelo
   ↕
Herramientas
   ↕
Respuesta
   ↕
Evaluación
   ↕
Retroalimentación
```

Una modificación en una parte puede afectar a las demás.

### Ejemplo

Tenemos un chatbot que responde incorrectamente sobre productos.

Podemos pensar:

> «El prompt está mal».

Pero existen varias posibilidades:

```
¿El prompt está mal?
¿La información está desactualizada?
¿La recuperación de documentos falla?
¿El modelo interpreta mal los datos?
¿El contexto es demasiado grande?
¿La aplicación está enviando información incorrecta?
¿La respuesta necesita validación?
```

El pensamiento sistémico evita solucionar únicamente el síntoma.

----------

# 6. Metacognición aplicada a la ingeniería de prompts

La **metacognición** es la capacidad de reflexionar sobre nuestro propio proceso de pensamiento y aprendizaje.

En ingeniería de prompts podemos aplicarla de esta manera:

Antes de escribir:

> ¿Qué quiero conseguir?

Mientras diseñamos:

> ¿Por qué estoy incluyendo esta instrucción?

Después de obtener la respuesta:

> ¿Qué funcionó?

> ¿Qué falló?

> ¿Por qué pudo haber fallado?

> ¿Cómo puedo comprobarlo?

Después de modificar el prompt:

> ¿Realmente mejoró el resultado o simplemente produjo una respuesta diferente?

Este último punto es especialmente importante.

**Una respuesta diferente no necesariamente es una respuesta mejor.**

----------

# 7. Cinco niveles de desarrollo

Los siguientes niveles son un **marco pedagógico de este repositorio**, no una clasificación oficial de profesionales.

Nivel

Pregunta principal

Enfoque

**1. Usuario**

«¿Cómo consigo que la IA haga esto?»

Resultado inmediato

**2. Diseñador**

«¿Cómo debo estructurar la instrucción?»

Prompt

**3. Evaluador**

«¿Cómo sé si la respuesta es buena?»

Métricas y pruebas

**4. Ingeniero**

«¿Cómo integro el modelo en un sistema?»

Arquitectura

**5. Investigador**

«¿Cómo puedo descubrir o desarrollar mejores métodos?»

Experimentación e investigación

La progresión importante es:

```
Usar
  ↓
Diseñar
  ↓
Evaluar
  ↓
Construir
  ↓
Investigar
```

El objetivo del repositorio no es simplemente convertir al estudiante en alguien que escribe mejores prompts.

El objetivo final es que pueda **analizar, diseñar, evaluar y construir sistemas basados en modelos de IA**.

----------

# 8. Patrones de pensamiento avanzado

## 8.1 Pensamiento estructural

Un prompt puede analizarse mediante componentes.

Una estructura inicial puede ser:

```
Rol
↓
Contexto
↓
Objetivo
↓
Datos
↓
Restricciones
↓
Criterios de calidad
↓
Formato de salida
```

### Ejemplo

En lugar de:

```
Analiza este código.
```

Podemos especificar:

```
Rol:
Actúa como revisor de código Python.

Contexto:
El código forma parte de una aplicación de procesamiento de datos.

Objetivo:
Identificar errores y problemas de diseño.

Restricciones:
No modifiques el código todavía.

Criterios:
Busca errores lógicos, problemas de rendimiento y riesgos de seguridad.

Salida:
1. Problema
2. Línea afectada
3. Explicación
4. Riesgo
5. Recomendación
```

Esto no garantiza una respuesta correcta, pero reduce la ambigüedad.

----------

# 8.2 Pensamiento anticipatorio

Un ingeniero no analiza únicamente la respuesta actual.

También piensa:

```
¿Qué puede salir mal?
```

Por ejemplo:

```
Prompt actual
      ↓
Respuesta esperada
      ↓
Interpretaciones alternativas
      ↓
Posibles errores
      ↓
Consecuencias
      ↓
Mecanismos de prevención
```

### Ejemplo

Tenemos:

```
Resume este documento.
```

Una posible interpretación es:

> «Haz un resumen general».

Pero otra puede ser:

> «Extrae únicamente las conclusiones».

Otra:

> «Resume para un ejecutivo».

Otra:

> «Resume conservando todas las cifras importantes».

El ingeniero identifica estas ambigüedades antes de que se conviertan en errores.

----------

# 9. Evaluación inicial

Antes de comenzar el repositorio, evalúa tu nivel actual.

Utiliza una escala de **1 a 5**:

Dimensión

Pregunta

Puntuación

**Fundamentos de IA**

¿Comprendes conceptos como tokens, contexto, embeddings y atención?

___/5

**Diseño de prompts**

¿Puedes diseñar instrucciones claras y reproducibles?

___/5

**Programación**

¿Puedes crear scripts básicos para trabajar con modelos de IA?

___/5

**Evaluación**

¿Puedes determinar objetivamente si una respuesta es correcta?

___/5

**Pensamiento sistémico**

¿Puedes identificar las diferentes partes de un sistema de IA?

___/5

**Seguridad**

¿Reconoces riesgos relacionados con datos, instrucciones y modelos?

___/5

**Experimentación**

¿Puedes comparar diferentes enfoques y documentar resultados?

___/5

### Importante

Una puntuación baja no representa un problema.

Esta evaluación únicamente establece el **punto de partida**.

----------

# 10. Plan de desarrollo

Después de realizar la evaluación, identifica:

### 1. Fortalezas

¿Qué conocimientos ya tienes?

Ejemplo:

```
Programación Python: 4/5
```

### 2. Áreas de mejora

¿Qué necesitas desarrollar?

Ejemplo:

```
Evaluación de modelos: 2/5
```

### 3. Acciones

Convierte cada debilidad en una acción concreta.

Ejemplo:

```
Estudiar métricas de evaluación de LLM
Realizar 10 ejercicios
Crear un pequeño conjunto de pruebas
Comparar dos prompts
```

### 4. Evidencia de progreso

No utilices únicamente:

> «Creo que ya aprendí».

Utiliza evidencias:

```
Antes:
El modelo produce resultados inconsistentes.

Después:
El resultado supera 90 % de precisión en mi conjunto de pruebas.
```

La métrica dependerá de la tarea.

### 5. Revisión periódica

Cada cierto tiempo:

```
¿Qué aprendí?
¿Qué todavía no comprendo?
¿Qué errores sigo cometiendo?
¿Qué puedo demostrar mediante un proyecto?
```

----------

# 11. Conceptos fundamentales para el aprendizaje

Concepto

Definición práctica

**Mentalidad de crecimiento**

Entender que las habilidades pueden desarrollarse mediante aprendizaje, práctica y retroalimentación.

**Práctica deliberada**

Practicar una habilidad concreta, medir el resultado, identificar errores y volver a intentarlo con una mejora específica.

**Pensamiento sistémico**

Analizar cómo interactúan las diferentes partes de un sistema y cómo una modificación puede afectar a otras partes.

**Metacognición**

Analizar y supervisar nuestro propio proceso de pensamiento y aprendizaje.

**Resiliencia**

Capacidad de un sistema o proceso para soportar errores, cambios o perturbaciones y continuar funcionando.

**Antifragilidad**

Concepto utilizado para describir sistemas que pueden beneficiarse de determinadas perturbaciones, variaciones o situaciones de estrés.

**Kaizen**

Filosofía de mejora continua mediante cambios progresivos y sostenidos.

### Nota terminológica

Algunos términos aparecen frecuentemente en literatura técnica en inglés. A lo largo del repositorio se utilizará su equivalente en español siempre que exista uno claro.

Cuando sea necesario, se indicará también el término original en inglés.

----------

# 12. Ejercicio de reflexión inicial

Antes de continuar al módulo 01, responde por escrito:

### 1. Motivación

¿Por qué quieres aprender ingeniería de prompts?

No respondas únicamente:

> «Porque quiero aprender a usar IA».

Explica qué problema quieres resolver con ese conocimiento.

### 2. Nivel actual

¿Qué puedes hacer actualmente con una IA que no podías hacer hace un año?

### 3. Limitaciones

¿Cuál consideras que es tu principal dificultad?

Por ejemplo:

```
No comprendo cómo funcionan los modelos.
No sé estructurar prompts.
No sé evaluar respuestas.
No sé programar.
No sé integrar modelos mediante API.
No sé identificar riesgos de seguridad.
```

### 4. Objetivo a 90 días

Define un resultado que pueda demostrarse.

Ejemplo:

> «Crear una aplicación en Python que utilice un modelo de lenguaje, permita ejecutar diferentes prompts y compare automáticamente sus resultados mediante criterios definidos».

### 5. Aplicación práctica

Define un problema real de tu área profesional que puedas utilizar como proyecto durante el aprendizaje.

----------

# 13. Una regla para todo el repositorio

A partir de este módulo utilizaremos una regla transversal:

> **No debemos optimizar un prompt antes de comprender el problema que queremos resolver.**

El proceso será:

```
Problema
   ↓
Objetivo
   ↓
Información necesaria
   ↓
Diseño de la solución
   ↓
Prompt
   ↓
Ejecución
   ↓
Evaluación
   ↓
Iteración
```

Y cuando el prompt no sea suficiente:

```
Prompt
   ↓
¿No funciona?
   ↓
Analizar causa
   ├── Datos
   ├── Contexto
   ├── Modelo
   ├── Herramientas
   ├── Arquitectura
   ├── Evaluación
   └── Prompt
```

Esta forma de pensar será más importante que memorizar cientos de técnicas.

----------

# Lo que viene después

## Módulo 01: Conceptos fundamentales de los modelos de lenguaje

En el siguiente módulo estudiaremos los conceptos necesarios para comprender qué ocurre cuando enviamos información a un modelo de lenguaje:

-   tokens;
-   tokenización;
-   embeddings;
-   vectores;
-   parámetros;
-   pesos;
-   atención;
-   ventana de contexto;
-   inferencia;
-   entrenamiento;
-   temperatura;
-   modelos de lenguaje;
-   limitaciones de los LLM.

El objetivo no será memorizar definiciones.

La meta será poder responder una pregunta fundamental:

> **¿Qué ocurre técnicamente entre el momento en que escribimos un prompt y el momento en que recibimos una respuesta?**

Una vez comprendido esto, las técnicas de prompting dejan de parecer una colección de «trucos» y empiezan a entenderse como herramientas de ingeniería.