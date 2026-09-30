# 05 — Datos

> **Objetivo:** comprender qué son los datos en inteligencia artificial, por qué son fundamentales para entrenar modelos, cómo se preparan, qué problemas pueden contener y cómo influyen en el comportamiento de un modelo de IA y de un LLM.

---

# 1. La idea fundamental

Si un modelo es un sistema que aprende patrones, necesitamos preguntarnos:

> **¿De dónde obtiene los ejemplos a partir de los cuales aprende esos patrones?**

La respuesta es:

> **De los datos.**

Podemos representar una simplificación del proceso:

```text
MUNDO REAL
    ↓
DATOS
    ↓
PREPARACIÓN
    ↓
ENTRENAMIENTO
    ↓
MODELO
    ↓
INFERENCIA
    ↓
PREDICCIÓN / GENERACIÓN
```

Por tanto:

> **Los datos son la materia prima a partir de la cual un modelo puede aprender patrones.**

Esto no significa que un modelo simplemente "guarde" los datos.

Durante el entrenamiento, los datos se utilizan para ajustar los parámetros del modelo.

---

# 2. ¿Qué es un dato?

Un dato es una representación de algún hecho, observación, característica, evento o información.

Ejemplos:

```text
Edad = 35
```

```text
Temperatura = 24.5 °C
```

```text
Ciudad = Quito
```

```text
Precio = $125.000
```

```text
"El cliente solicitó información."
```

```text
Imagen de un automóvil
```

```text
Grabación de una conversación
```

Todos ellos son datos.

Los datos pueden tener diferentes formatos:

* números;
* texto;
* imágenes;
* audio;
* vídeo;
* tablas;
* documentos;
* código;
* señales;
* registros de eventos.

---

# 3. Datos no significa solamente números

Una idea equivocada frecuente es:

> "Machine learning trabaja únicamente con números."

En realidad, muchos datos originalmente no son numéricos.

Por ejemplo:

```text
"Quito"
```

```text
"El cliente está satisfecho."
```

```text
una fotografía
```

```text
un archivo PDF
```

```text
una grabación de audio
```

Sin embargo, para que un modelo matemático pueda procesarlos, estos datos deben transformarse en representaciones numéricas adecuadas.

Conceptualmente:

```text
Texto
 ↓
tokens
 ↓
representaciones numéricas
 ↓
modelo
```

o:

```text
Imagen
 ↓
representación numérica
 ↓
modelo
```

Esto será fundamental cuando estudiemos:

* tokenización;
* embeddings;
* visión artificial;
* multimodalidad.

---

# 4. Datos → información → conocimiento

Conviene distinguir tres conceptos.

### Datos

Observaciones individuales.

```text
25
```

### Información

Datos organizados en un contexto.

```text
Temperatura registrada:
25 °C
```

### Conocimiento

Relaciones o patrones que permiten interpretar información y tomar decisiones.

Por ejemplo:

```text
Las temperaturas superiores a 30 °C
se asociaron frecuentemente con mayor
consumo de electricidad.
```

En inteligencia artificial podemos pensar:

```text
DATOS
  ↓
procesamiento
  ↓
patrones
  ↓
MODELO
```

Pero debemos tener cuidado:

> Un modelo no necesariamente posee "conocimiento" en el mismo sentido que una persona.

En ingeniería de IA, utilizamos el término para describir información o regularidades representadas por el sistema.

---

# 5. Un ejemplo sencillo

Supongamos que queremos predecir si un estudiante aprobará una asignatura.

Tenemos:

| Horas de estudio | Asistencia | Resultado |
| ---------------: | ---------: | --------- |
|                2 |       60 % | No        |
|                4 |       70 % | No        |
|                5 |       80 % | Sí        |
|                7 |       90 % | Sí        |
|                9 |       95 % | Sí        |

Estos registros son datos.

El modelo puede encontrar patrones estadísticos entre:

```text
horas de estudio
        +
asistencia
        ↓
probabilidad de aprobar
```

Después podemos introducir:

```text
6 horas
85 % asistencia
```

y obtener:

```text
probabilidad de aprobar ≈ X %
```

El modelo no necesita que exista exactamente una fila con:

```text
6 horas + 85 %
```

Puede generalizar a partir de los patrones aprendidos.

---

# 6. ¿Qué significa aprender de los datos?

Cuando decimos:

> "El modelo aprende de los datos."

no significa necesariamente que memorice cada registro.

Significa que el proceso de entrenamiento ajusta sus parámetros para representar determinadas regularidades estadísticas presentes en los datos.

Conceptualmente:

```text
Datos
 ↓
patrones
 ↓
optimización
 ↓
parámetros
 ↓
modelo
```

Por eso podemos decir:

> **Los datos influyen en qué patrones puede aprender un modelo.**

---

# 7. La calidad de los datos importa

Supongamos que queremos entrenar un modelo para detectar perros.

Tenemos 10.000 imágenes.

Pero descubrimos que:

```text
8.000 imágenes → perros
2.000 imágenes → gatos
```

y además muchas están mal etiquetadas.

El modelo recibe información incorrecta.

Podemos tener:

```text
datos deficientes
     ↓
aprendizaje deficiente
     ↓
modelo deficiente
```

Esto conduce a una idea clásica:

> **Garbage in, garbage out.**

En español:

> **Si introducimos datos de mala calidad, podemos obtener resultados de mala calidad.**

Pero esta frase debe utilizarse con cuidado.

Un modelo complejo puede aprender patrones útiles incluso de datos imperfectos, y la calidad de los datos no es el único factor que determina el resultado.

---

# 8. ¿Qué hace que un dato sea de calidad?

La calidad de los datos puede involucrar diferentes dimensiones:

* exactitud;
* completitud;
* consistencia;
* actualidad;
* relevancia;
* representatividad;
* unicidad;
* trazabilidad;
* procedencia;
* correcta etiquetación.

Por ejemplo, en una base de clientes:

```text
Nombre: Carlos
Edad: -17
País: Ecuador
```

La edad probablemente representa un problema.

Otro registro:

```text
Nombre: Carlos
Edad: 35
País: Ecuador
```

podría ser más plausible.

Pero "plausible" no significa necesariamente "correcto".

Por eso la calidad de datos requiere procesos de validación.

---

# 9. Datos estructurados

Los datos estructurados tienen una organización definida.

Por ejemplo:

| ID | Nombre | Edad | Ciudad    |
| -: | ------ | ---: | --------- |
|  1 | Ana    |   25 | Quito     |
|  2 | Carlos |   31 | Guayaquil |
|  3 | María  |   28 | Cuenca    |

Son comunes en:

* bases de datos;
* hojas de cálculo;
* sistemas empresariales;
* ERP;
* CRM;
* sistemas transaccionales.

Pueden almacenarse en:

```text
SQL
CSV
Excel
JSON
```

entre otros formatos.

---

# 10. Datos no estructurados

Los datos no estructurados no siguen necesariamente una tabla rígida.

Ejemplos:

```text
PDF
correo electrónico
fotografía
vídeo
audio
documento de Word
página web
publicación
```

Los LLM trabajan especialmente bien con grandes cantidades de información textual, pero antes de utilizar documentos reales suelen existir procesos de:

* extracción;
* limpieza;
* segmentación;
* tokenización;
* filtrado;
* normalización;
* deduplicación.

---

# 11. Datos semiestructurados

Entre ambos extremos encontramos datos semiestructurados.

Ejemplo:

```json
{
  "cliente": "Carlos",
  "edad": 31,
  "ciudad": "Quito"
}
```

Tiene estructura, pero no necesariamente una tabla rígida.

Otros ejemplos:

```text
JSON
XML
HTML
logs
```

Este tipo de datos es extremadamente común en sistemas modernos.

---

# 12. Datos etiquetados

En aprendizaje supervisado, normalmente necesitamos datos acompañados de una etiqueta o resultado esperado.

Ejemplo:

```text
Correo:
"Ganaste un premio. Haz clic aquí."

Etiqueta:
PHISHING
```

Otro:

```text
Correo:
"Reunión mañana a las 10:00."

Etiqueta:
LEGÍTIMO
```

El modelo utiliza estos ejemplos para aprender una relación entre entrada y salida.

---

# 13. Datos no etiquetados

En otros escenarios no tenemos una etiqueta explícita.

Ejemplo:

```text
Documento 1
Documento 2
Documento 3
Documento 4
...
```

No hemos indicado:

```text
documento 1 = categoría A
documento 2 = categoría B
```

Los modelos pueden aprender estructuras y representaciones directamente de estos datos mediante diferentes métodos.

Esto es especialmente importante en el preentrenamiento de modelos de lenguaje.

---

# 14. Aprendizaje supervisado

En aprendizaje supervisado tenemos ejemplos:

```text
entrada → respuesta esperada
```

Por ejemplo:

```text
imagen → perro
imagen → gato
imagen → perro
```

El modelo aprende a relacionar la entrada con la etiqueta.

Otro ejemplo:

```text
características de vivienda → precio
```

---

# 15. Aprendizaje no supervisado

En términos generales, el modelo recibe datos sin una etiqueta objetivo explícita y busca estructuras o representaciones útiles.

Por ejemplo:

```text
clientes
 ↓
modelo
 ↓
grupos detectados
```

Podría encontrar grupos de clientes con comportamientos similares.

No le dijimos necesariamente:

```text
"Estos clientes son grupo A."
```

El algoritmo intenta encontrar estructuras presentes en los datos.

---

# 16. Aprendizaje autosupervisado

Este concepto es especialmente importante para los LLM.

En el aprendizaje **autosupervisado**, el propio contenido de los datos proporciona señales para construir el objetivo de entrenamiento.

Ejemplo:

```text
El cielo es ___.
```

El propio texto puede proporcionar el token esperado:

```text
azul
```

Podemos construir:

```text
entrada:
El cielo es

objetivo:
azul
```

No necesitamos que un humano etiquete manualmente cada ejemplo.

Esto permite utilizar enormes cantidades de datos.

---

# 17. ¿Cómo se entrenan los LLM con texto?

Una simplificación conceptual:

```text
Texto
 ↓
tokenización
 ↓
secuencia de tokens
 ↓
contexto
 ↓
predicción
 ↓
comparación con token objetivo
 ↓
pérdida
 ↓
actualización de parámetros
```

Ejemplo:

```text
"El perro corre por el parque"
```

Podemos construir una tarea como:

```text
Entrada:
El perro corre por el

Objetivo:
parque
```

Después:

```text
Entrada:
El perro corre por el parque

Objetivo:
...
```

En un sistema real, el proceso es mucho más complejo y puede aprovechar múltiples posiciones de la secuencia.

---

# 18. ¿Qué es un corpus?

Un **corpus** es una colección de datos, especialmente utilizada para referirse a grandes colecciones de texto.

Ejemplo:

```text
CORPUS
│
├── libros
├── artículos
├── documentos
├── páginas web
├── código
└── otros textos
```

Los LLM pueden entrenarse con grandes colecciones de datos.

Pero "más corpus" no significa automáticamente "mejor corpus".

La calidad, diversidad, representatividad y composición son fundamentales.

---

# 19. Dataset

Un **dataset** es un conjunto organizado de datos utilizado para una finalidad determinada.

Ejemplo:

```text
dataset_clientes.csv
```

Puede contener:

```text
cliente
edad
ingresos
compras
canceló
```

En machine learning, los datasets suelen dividirse para diferentes funciones.

---

# 20. Training, validation y test

Una división clásica es:

```text
Dataset
   │
   ├── Training
   │
   ├── Validation
   │
   └── Test
```

### Training

Se utiliza para ajustar el modelo.

### Validation

Se utiliza para evaluar decisiones durante el desarrollo y seleccionar configuraciones.

### Test

Se utiliza para una evaluación final sobre datos que deben mantenerse separados del proceso de desarrollo.

---

# 21. ¿Por qué no utilizar todos los datos para entrenar?

Porque queremos saber si el modelo puede generalizar.

Supongamos que un estudiante memoriza exactamente las preguntas del examen.

Puede obtener:

```text
100 %
```

pero eso no demuestra necesariamente que haya aprendido el contenido.

En machine learning ocurre algo similar.

Queremos evaluar:

> **¿Puede el modelo funcionar correctamente con datos que no utilizó directamente para ajustar sus parámetros?**

---

# 22. Generalización

Un modelo generaliza cuando puede producir resultados útiles sobre datos nuevos.

Podemos representarlo:

```text
Datos de entrenamiento
        ↓
     modelo
        ↓
Datos nuevos
        ↓
   predicción
```

La capacidad de generalización es una de las propiedades fundamentales de machine learning.

---

# 23. Sobreajuste

El **overfitting** o sobreajuste ocurre cuando el modelo se adapta demasiado a los datos de entrenamiento y pierde capacidad de generalización.

Ejemplo:

```text
Entrenamiento:
99 %

Datos nuevos:
62 %
```

El modelo puede haber aprendido patrones demasiado específicos de los datos de entrenamiento.

Una analogía:

> Memorizar las respuestas de un examen no es lo mismo que comprender la materia.

---

# 24. Subajuste

El **underfitting** o subajuste ocurre cuando el modelo no consigue representar suficientemente bien los patrones relevantes.

Ejemplo:

```text
Entrenamiento:
60 %

Datos nuevos:
58 %
```

El modelo tiene un rendimiento bajo incluso sobre los datos utilizados durante el desarrollo.

Conceptualmente:

```text
underfitting → modelo demasiado simple / insuficientemente aprendido

good fit → equilibrio

overfitting → modelo demasiado adaptado a los datos de entrenamiento
```

---

# 25. Datos y distribución

Supongamos que entrenamos un modelo con:

```text
fotografías tomadas de día
```

pero posteriormente lo utilizamos con:

```text
fotografías nocturnas
```

La distribución de los datos cambió.

Esto puede afectar el rendimiento.

Podemos representarlo:

```text
Entrenamiento
P(datos A)

        ↓

Producción
P(datos B)
```

Si:

$$
P_{train}(x) \neq P_{real}(x)
$$

puede existir un problema de **distribution shift**.

---

# 26. Representatividad

Los datos deben representar razonablemente el entorno donde se utilizará el modelo.

Ejemplo:

Queremos desarrollar un sistema para analizar solicitudes de crédito en Ecuador.

Pero entrenamos únicamente con datos de:

```text
Estados Unidos
```

Podrían existir diferencias importantes:

* ingresos;
* moneda;
* legislación;
* comportamiento financiero;
* productos bancarios;
* población;
* condiciones económicas.

Por eso:

> **Un dataset debe evaluarse respecto del problema y de la población sobre la que se utilizará el modelo.**

---

# 27. Sesgo en los datos

Los datos pueden contener sesgos.

Por ejemplo:

```text
Dataset:
90 % ejemplos del grupo A
10 % ejemplos del grupo B
```

Si el fenómeno real es diferente, el modelo puede aprender una representación poco equilibrada.

También pueden existir sesgos históricos:

```text
decisiones humanas del pasado
        ↓
datos históricos
        ↓
modelo
        ↓
reproduce ciertos patrones
```

Un modelo no elimina automáticamente los sesgos presentes en los datos.

---

# 28. Correlación no significa causalidad

Supongamos que encontramos:

```text
ventas de helado ↑
accidentes acuáticos ↑
```

Podría existir correlación.

Pero eso no significa:

```text
helado → causa accidentes
```

Una variable externa como:

```text
temperatura
```

podría explicar ambas.

En machine learning:

> **Un modelo puede aprender correlaciones útiles para predecir sin haber aprendido relaciones causales.**

Esta diferencia es fundamental en ciencia de datos.

---

# 29. Datos duplicados

Los datasets pueden contener duplicados.

Ejemplo:

```text
Registro 1:
Cliente = 102
Compra = $50

Registro 2:
Cliente = 102
Compra = $50
```

Si los duplicados no son intencionales, pueden distorsionar el entrenamiento.

Un problema especialmente grave ocurre cuando ejemplos casi idénticos aparecen tanto en entrenamiento como en test.

Entonces podemos obtener una evaluación artificialmente optimista.

---

# 30. Data leakage

El **data leakage** ocurre cuando información que no debería estar disponible durante el entrenamiento termina filtrándose hacia el proceso de aprendizaje.

Ejemplo:

Queremos predecir si un cliente abandonará el servicio.

Tenemos:

```text
fecha de predicción
fecha de cancelación
```

Si utilizamos accidentalmente la fecha de cancelación como variable de entrada, el modelo obtiene información del futuro.

El resultado puede parecer excelente durante la evaluación.

Pero en producción esa información no estará disponible.

---

# 31. Leakage en LLM

En modelos generativos también existe el riesgo de contaminación entre datos de entrenamiento y evaluación.

Supongamos que una prueba contiene:

```text
Pregunta:
X

Respuesta correcta:
Y
```

y esa misma información aparece en los datos de entrenamiento.

El modelo puede haber visto ejemplos relacionados previamente.

Entonces un resultado alto no necesariamente demuestra capacidad de generalización.

Por eso evaluar modelos grandes requiere datasets y metodologías cuidadosamente diseñados.

---

# 32. Datos sintéticos

Los **datos sintéticos** son datos generados artificialmente.

Ejemplo:

```text
datos reales
     ↓
modelo generador
     ↓
datos sintéticos
```

Pueden utilizarse para:

* ampliar datasets;
* crear ejemplos poco frecuentes;
* pruebas;
* simulación;
* privacidad;
* entrenamiento;
* generación de escenarios.

Pero los datos sintéticos no son automáticamente equivalentes a datos reales.

---

# 33. El problema de los datos sintéticos

Si un modelo genera datos para entrenar otro modelo:

```text
Modelo A
   ↓
datos sintéticos
   ↓
Modelo B
```

pueden aparecer problemas si los datos sintéticos:

* pierden diversidad;
* reproducen errores;
* contienen sesgos;
* reducen variabilidad;
* amplifican patrones incorrectos.

Por eso los datos sintéticos deben evaluarse.

---

# 34. Datos generados por IA

Actualmente grandes cantidades de contenido pueden ser generadas por modelos:

```text
texto
imágenes
audio
código
vídeo
```

Esto crea un nuevo desafío:

> **¿Qué ocurre cuando datos generados por IA vuelven a formar parte de futuros datasets de entrenamiento?**

Si se incorporan sin control, pueden aparecer:

* contaminación;
* pérdida de diversidad;
* errores propagados;
* patrones artificiales;
* degradación de determinadas distribuciones.

Este problema es objeto de investigación activa.

---

# 35. Datos multimodales

Los sistemas modernos pueden trabajar con:

```text
texto
imagen
audio
vídeo
código
```

Por ejemplo:

```text
imagen de una factura
        +
texto de instrucciones
        ↓
modelo multimodal
        ↓
información estructurada
```

En un sistema multimodal, diferentes modalidades deben convertirse en representaciones que el modelo pueda procesar.

Esto introduce desafíos adicionales:

* sincronización;
* alineamiento;
* calidad;
* resolución;
* ruido;
* correspondencia entre modalidades.

---

# 36. Datos y tokenización

En un LLM, el texto debe transformarse mediante un tokenizador.

Por ejemplo, conceptualmente:

```text
"inteligencia artificial"
          ↓
      tokenización
          ↓
["inteligencia", "artificial"]
```

Pero el resultado real puede dividir palabras en fragmentos.

Por ejemplo:

```text
"inteligencia"
        ↓
["inteli", "gencia"]
```

El comportamiento exacto depende del tokenizador.

Por eso:

> **La cantidad de caracteres, palabras y tokens no es necesariamente la misma.**

Los tokens se estudiarán en profundidad en su propio módulo.

---

# 37. Datos y embeddings

Después de la tokenización, los tokens pueden transformarse en representaciones vectoriales denominadas **embeddings**.

Conceptualmente:

```text
texto
 ↓
tokens
 ↓
IDs
 ↓
embeddings
 ↓
vectores
 ↓
modelo
```

Un embedding representa información en un espacio numérico.

Por ejemplo, conceptualmente:

```text
"perro" → vector
"gato"  → vector
"automóvil" → vector
```

Las relaciones geométricas entre representaciones pueden capturar determinados patrones semánticos.

No debemos interpretar un embedding como una definición humana exacta de una palabra.

---

# 38. Datos de entrenamiento ≠ contexto

Esta distinción es fundamental.

### Datos de entrenamiento

Se utilizan para ajustar parámetros.

```text
datos
 ↓
entrenamiento
 ↓
parámetros
```

### Contexto

Se proporciona al modelo durante una inferencia.

```text
documento
 ↓
contexto
 ↓
inferencia
```

Por ejemplo:

```text
Entrenamiento:
millones de documentos
       ↓
modelo
```

Después:

```text
Usuario:
"Resume este documento."

Documento:
[texto nuevo]

       ↓

contexto
       ↓
modelo
       ↓
resumen
```

El documento no se convierte automáticamente en parte permanente de los parámetros.

---

# 39. Datos de RAG

En RAG encontramos otra categoría:

```text
documentos empresariales
       ↓
indexación
       ↓
recuperación
       ↓
contexto
       ↓
LLM
```

Estos documentos pueden no formar parte del entrenamiento original del modelo.

Son datos externos utilizados durante la inferencia.

Por eso una arquitectura RAG permite trabajar con información que:

* cambia frecuentemente;
* es privada;
* es específica de una empresa;
* apareció después del entrenamiento;
* no conviene incorporar mediante fine-tuning.

---

# 40. Datos para fine-tuning

Los datos utilizados para fine-tuning tienen otra función.

Ejemplo:

```text
Entrada:
Analiza esta factura.

Salida esperada:
{
  "total": 1200,
  "impuestos": 144
}
```

Miles de ejemplos de este tipo pueden utilizarse para adaptar un modelo.

La cadena sería:

```text
dataset especializado
        ↓
fine-tuning
        ↓
parámetros actualizados
        ↓
modelo adaptado
```

---

# 41. Comparación de los diferentes tipos de datos

| Tipo de datos               | ¿Cuándo se utilizan?                    | ¿Modifican parámetros? |
| --------------------------- | --------------------------------------- | ---------------------: |
| Datos de preentrenamiento   | Entrenamiento inicial                   |                     Sí |
| Datos de fine-tuning        | Entrenamiento adicional                 |                     Sí |
| Datos de validación         | Evaluación/desarrollo                   |        No directamente |
| Datos de test               | Evaluación final                        |                     No |
| Contexto                    | Inferencia                              |                     No |
| Datos de RAG                | Inferencia                              |      No necesariamente |
| Prompt                      | Inferencia                              |                     No |
| Feedback para entrenamiento | Puede alimentar entrenamiento posterior |         Potencialmente |

Esta tabla es fundamental.

---

# 42. Calidad de los datos frente a cantidad

Es tentador pensar:

```text
más datos
=
mejor modelo
```

Pero la realidad es más compleja.

Podemos tener:

```text
10 millones de datos malos
```

frente a:

```text
1 millón de datos cuidadosamente seleccionados
```

y el segundo conjunto puede ser más útil para una tarea determinada.

La calidad incluye:

* precisión;
* diversidad;
* relevancia;
* representatividad;
* cobertura;
* limpieza;
* procedencia;
* consistencia.

---

# 43. Diversidad de datos

Un dataset demasiado homogéneo puede limitar la capacidad de generalización.

Ejemplo:

```text
100.000 fotografías
```

pero todas:

```text
misma cámara
mismo lugar
misma iluminación
mismo ángulo
```

El modelo puede aprender patrones relacionados con esas condiciones.

Después recibe:

```text
otra cámara
otra iluminación
otro entorno
```

y puede fallar.

Por eso la diversidad puede ser importante.

---

# 44. Procedencia de los datos

En sistemas profesionales debemos preguntar:

> **¿De dónde provienen los datos?**

La procedencia (*data provenance*) permite conocer aspectos como:

* fuente;
* fecha;
* proceso de extracción;
* transformaciones;
* propietario;
* permisos;
* versión;
* historial.

En sistemas empresariales esto es importante para:

* auditoría;
* cumplimiento;
* seguridad;
* reproducibilidad;
* gobernanza.

---

# 45. Licencias y derechos

Los datos también pueden estar sujetos a restricciones legales o contractuales.

Por ejemplo:

```text
dataset
 ↓
licencia
 ↓
¿permite uso comercial?
¿permite redistribución?
¿permite entrenamiento?
¿requiere atribución?
```

No debemos asumir:

> "Si algo está disponible en Internet, puedo utilizarlo libremente para cualquier propósito."

Disponibilidad técnica y permiso legal son conceptos diferentes.

---

# 46. Datos privados

Los datos pueden contener:

* nombres;
* correos;
* teléfonos;
* documentos;
* información financiera;
* información empresarial;
* credenciales;
* información personal.

Por ello, un pipeline de IA debe considerar:

```text
dato
 ↓
clasificación
 ↓
protección
 ↓
procesamiento
 ↓
modelo
```

La seguridad y privacidad de los datos forman parte de la ingeniería del sistema de IA.

---

# 47. PII

PII significa **Personally Identifiable Information**, información que puede identificar directa o indirectamente a una persona, dependiendo del marco jurídico aplicable.

Ejemplos potenciales:

```text
nombre
correo
teléfono
dirección
identificadores
```

El tratamiento de PII requiere controles adecuados.

La regulación concreta depende del país, sector y contexto.

Por ello, un proyecto de IA profesional debe considerar:

* minimización;
* control de acceso;
* anonimización o seudonimización cuando corresponda;
* retención;
* trazabilidad;
* cifrado;
* políticas de uso.

---

# 48. Datos y seguridad de IA

Los datos no solamente pueden estar "malos".

También pueden ser **maliciosos**.

Ejemplo:

```text
documento aparentemente normal
        ↓
contiene instrucciones ocultas
        ↓
sistema RAG
        ↓
LLM
```

Esto puede convertirse en un escenario de **indirect prompt injection**.

Otro escenario:

```text
datos contaminados
       ↓
entrenamiento
       ↓
modelo
       ↓
comportamiento alterado
```

Esto se relaciona con **data poisoning**.

Por tanto:

> **La seguridad de los datos es también una dimensión de la seguridad del modelo.**

---

# 49. Data poisoning

El **data poisoning** consiste, en términos generales, en introducir datos manipulados o maliciosos con el objetivo de afectar el comportamiento de un modelo.

Conceptualmente:

```text
Dataset limpio
     +
datos maliciosos
     ↓
entrenamiento
     ↓
modelo
     ↓
comportamiento alterado
```

Los ataques reales pueden ser mucho más sofisticados.

Pueden intentar afectar:

* disponibilidad;
* precisión;
* determinadas entradas;
* clasificación;
* comportamiento ante patrones específicos.

---

# 50. Backdoors

Un escenario más específico es un **backdoor**.

Conceptualmente:

```text
Entrada normal
     ↓
comportamiento normal


Entrada + patrón desencadenante
     ↓
comportamiento alterado
```

Por ejemplo, un modelo podría comportarse normalmente salvo cuando aparece un patrón específico.

La investigación sobre ataques de este tipo es importante dentro de la seguridad de modelos.

---

# 51. Datos adversariales

También podemos construir entradas diseñadas para provocar errores en modelos.

Por ejemplo:

```text
entrada normal
       ↓
modelo
       ↓
clasificación correcta
```

frente a:

```text
entrada ligeramente modificada
       ↓
modelo
       ↓
clasificación incorrecta
```

Esto puede estudiarse dentro de **adversarial machine learning**.

La existencia y relevancia del problema depende del tipo de modelo y aplicación.

---

# 52. El dato puede afectar más allá del entrenamiento

Un error común es pensar:

> "Los problemas de datos solo importan cuando entrenamos."

No.

Los datos pueden intervenir en todo el ciclo:

```text
recolección
 ↓
almacenamiento
 ↓
preprocesamiento
 ↓
entrenamiento
 ↓
evaluación
 ↓
RAG
 ↓
inferencia
 ↓
monitorización
```

Un sistema de IA profesional necesita gobernar el dato durante todo su ciclo de vida.

---

# 53. Data pipeline

Un **data pipeline** es una secuencia de procesos mediante la cual los datos pasan desde una fuente hasta un destino.

Ejemplo:

```text
Fuentes
  ↓
Extracción
  ↓
Limpieza
  ↓
Transformación
  ↓
Validación
  ↓
Almacenamiento
  ↓
Dataset
  ↓
Entrenamiento
```

En sistemas modernos podemos tener pipelines mucho más complejos.

---

# 54. ETL y ELT

Dos conceptos frecuentes:

### ETL

```text
Extract
   ↓
Transform
   ↓
Load
```

### ELT

```text
Extract
   ↓
Load
   ↓
Transform
```

La arquitectura adecuada depende del sistema.

En proyectos de IA, el pipeline de datos puede incluir además:

```text
validación
deduplicación
filtrado
anonimización
tokenización
chunking
embeddings
indexación
```

---

# 55. Datos y chunking

En sistemas RAG, documentos largos suelen dividirse en fragmentos.

Ejemplo:

```text
Documento de 100 páginas
          ↓
       chunking
          ↓
chunk 1
chunk 2
chunk 3
...
chunk N
```

Después pueden generarse embeddings para facilitar la recuperación.

La forma en que dividimos los documentos puede afectar directamente la calidad del sistema.

Por ejemplo, un chunk demasiado pequeño puede perder contexto.

Uno demasiado grande puede dificultar la recuperación precisa y aumentar el costo de contexto.

---

# 56. Datos y evaluación

Un modelo no puede evaluarse correctamente sin datos de evaluación adecuados.

Supongamos:

```text
Modelo A:
95 % accuracy
```

Parece excelente.

Pero descubrimos:

```text
dataset de evaluación:
99 % clase A
1 % clase B
```

Un modelo que predijera siempre A podría alcanzar aproximadamente 99 % de accuracy.

Por eso:

> **Una métrica aislada puede ser engañosa si no conocemos la composición de los datos.**

---

# 57. Más datos no siempre solucionan el problema

Imaginemos:

```text
Problema:
el dataset tiene etiquetas incorrectas.
```

Agregar más ejemplos con las mismas etiquetas incorrectas puede aumentar la cantidad de datos sin solucionar el problema.

Podríamos tener:

```text
100.000 ejemplos incorrectos
```

en lugar de:

```text
10.000 ejemplos incorrectos
```

Seguimos teniendo un problema de calidad.

La ingeniería de datos debe preguntar:

> **¿Tenemos más datos o tenemos mejores datos?**

---

# 58. Datos y capacidad del modelo

Existe una relación importante entre:

```text
datos
+
parámetros
+
cómputo
```

El rendimiento de los modelos modernos depende de cómo se combinan estos factores.

Conceptualmente:

```text
Datos suficientes
      +
Modelo adecuado
      +
Cómputo suficiente
      ↓
aprendizaje
```

Si uno de ellos es muy limitado, puede convertirse en un cuello de botella.

La relación exacta depende del modelo, arquitectura, tarea y régimen de entrenamiento.

---

# 59. Escalado

La investigación moderna ha mostrado que aumentar:

* tamaño del modelo;
* cantidad/calidad de datos;
* cómputo;

puede mejorar determinadas capacidades.

Pero existe un principio importante:

> **El escalado debe ser adecuado al problema.**

No tiene sentido asumir:

```text
10× datos
=
10× calidad
```

La relación no es lineal.

Además, la calidad y composición de los datos pueden ser tan importantes como la cantidad.

---

# 60. Datos y modelos especializados

Supongamos:

```text
Modelo general
```

y queremos adaptarlo a:

```text
ciberseguridad
```

Podemos necesitar datos específicos:

```text
logs
vulnerabilidades
CVE
documentación técnica
código
incidentes
tráfico de red
```

La especialización del dataset puede ayudar al modelo a desarrollar o reforzar capacidades relevantes.

Pero nuevamente:

> **Especializar datos no garantiza automáticamente un modelo seguro, preciso o experto.**

Debe evaluarse.

---

# 61. Datos y dominio

Podemos distinguir:

### Datos generales

```text
literatura
noticias
web
código
documentos generales
```

### Datos de dominio

```text
auditoría
medicina
derecho
ciberseguridad
ingeniería
finanzas
```

Un modelo general puede tener amplios conocimientos, mientras que un sistema especializado puede incorporar datos específicos mediante:

* RAG;
* fine-tuning;
* herramientas;
* datasets especializados.

---

# 62. La regla de "datos primero"

En proyectos profesionales de IA es frecuente comenzar pensando:

> "¿Qué modelo voy a utilizar?"

Pero una pregunta igualmente importante es:

> **"¿Qué datos tengo y qué calidad tienen?"**

Un proyecto puede fracasar aunque utilice un modelo excelente si:

```text
datos incorrectos
+
datos incompletos
+
datos no representativos
```

producen un sistema poco fiable.

Por eso:

> **La ingeniería de IA también es ingeniería de datos.**

---

# 63. Datos y trazabilidad

En un proyecto profesional deberíamos poder responder:

```text
¿De dónde salió este dato?
¿Cuándo fue obtenido?
¿Quién lo modificó?
¿Qué transformaciones sufrió?
¿Qué versión se utilizó?
¿En qué entrenamiento participó?
```

Esto permite reproducibilidad y auditoría.

Un pipeline profesional puede mantener:

```text
dataset v1
dataset v2
dataset v3
```

y asociar cada modelo con una versión concreta:

```text
modelo v1.4
   ↓
dataset v3
   ↓
configuración X
   ↓
evaluación Y
```

---

# 64. Versionado de datos

Los datos cambian.

Por ejemplo:

```text
Dataset v1
10.000 registros

Dataset v2
15.000 registros

Dataset v3
18.000 registros
```

Si no registramos las versiones, puede ser difícil reproducir los resultados.

Por eso en proyectos profesionales se utiliza el concepto de:

> **Data Versioning**

y puede complementarse con:

> **Model Versioning**

---

# 65. Dataset → modelo → evaluación

Una cadena reproducible puede ser:

```text
Dataset v3
    ↓
preprocesamiento v2
    ↓
entrenamiento configuración A
    ↓
Modelo v1.0
    ↓
Evaluación dataset-test-v2
    ↓
métricas
```

Esto permite comparar experimentos.

Por ejemplo:

```text
Modelo A → 87 %
Modelo B → 91 %
Modelo C → 89 %
```

Pero debemos saber:

* con qué datos;
* qué versión;
* qué métrica;
* qué población;
* qué condiciones.

Sin esa información, una cifra aislada puede ser poco útil.

---

# 66. Datos y reproducibilidad

Una afirmación como:

> "Nuestro modelo alcanza 95 % de precisión."

debería generar preguntas:

```text
¿95 % en qué dataset?
¿qué población?
¿qué periodo?
¿qué definición de precisión?
¿qué versión del modelo?
¿qué versión de los datos?
¿hubo leakage?
¿cómo se dividieron los datos?
```

La evaluación profesional exige contexto.

---

# 67. Datos de producción

Después de desplegar un modelo, aparecen nuevos datos:

```text
usuarios
consultas
errores
predicciones
resultados reales
feedback
```

Estos datos pueden utilizarse para monitorización y mejora.

Podemos tener:

```text
Producción
   ↓
datos nuevos
   ↓
monitorización
   ↓
evaluación
   ↓
mejora
```

Pero no debemos incorporar automáticamente todos los datos de producción al entrenamiento.

Primero deben pasar por controles.

---

# 68. Feedback humano

En sistemas de IA generativa puede recogerse feedback:

```text
respuesta
 ↓
usuario
 ↓
👍 / 👎
 ↓
feedback
```

Ese feedback puede utilizarse para:

* evaluación;
* mejora de prompts;
* ajuste de sistemas;
* creación de datasets;
* entrenamiento posterior.

Pero:

> **Feedback ≠ entrenamiento automático.**

Debe existir un proceso que determine cómo y cuándo ese feedback se incorpora al ciclo de desarrollo.

---

# 69. El ciclo de vida de los datos en IA

Podemos representar un ciclo completo:

```text
RECOLECCIÓN
     ↓
ALMACENAMIENTO
     ↓
LIMPIEZA
     ↓
VALIDACIÓN
     ↓
TRANSFORMACIÓN
     ↓
VERSIONADO
     ↓
ENTRENAMIENTO
     ↓
EVALUACIÓN
     ↓
PRODUCCIÓN
     ↓
MONITORIZACIÓN
     ↓
NUEVOS DATOS
     ↓
MEJORA
```

Esto forma parte de la ingeniería de sistemas de IA.

---

# 70. Los datos también son una superficie de ataque

Desde la perspectiva de seguridad:

```text
DATOS
  │
  ├── manipulación
  ├── poisoning
  ├── fuga de información
  ├── PII
  ├── documentos maliciosos
  ├── prompt injection indirecto
  └── contaminación
```

Por eso un ingeniero de IA debe pensar simultáneamente en:

```text
calidad
+
seguridad
+
privacidad
+
gobernanza
```

---

# 71. Una visión completa

Podemos unir los conceptos:

```text
                    DATOS
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
      texto        imágenes      audio
          │           │           │
          └───────────┼───────────┘
                      ↓
              PREPROCESAMIENTO
                      ↓
                REPRESENTACIÓN
                      ↓
                 ENTRENAMIENTO
                      ↓
                  PARÁMETROS
                      ↓
                    MODELO
                      ↓
                  INFERENCIA
                      ↑
          ┌───────────┼───────────┐
          │           │           │
        prompt      contexto      RAG
          │           │           │
          └───────────┼───────────┘
                      ↓
                    SALIDA
```

---

# 72. Una distinción que nunca debemos olvidar

Tenemos cuatro conceptos diferentes:

```text
DATOS DE ENTRENAMIENTO
        ↓
modifican parámetros


DATOS DE FINE-TUNING
        ↓
modifican parámetros


DATOS DE CONTEXTO
        ↓
condicionan una inferencia


DATOS DE RAG
        ↓
se recuperan para proporcionar contexto
```

No son intercambiables.

---

# 73. ¿Qué controla realmente un prompt?

Después de estudiar modelos, entrenamiento e inferencia y ahora datos, podemos responder con mayor precisión.

Cuando escribimos:

```text
Analiza este documento y encuentra anomalías.
```

no estamos controlando directamente:

```text
los datos de entrenamiento
los parámetros
la arquitectura
el preentrenamiento
```

Estamos controlando principalmente:

```text
la entrada
las instrucciones
el contexto
las restricciones
los ejemplos
la forma solicitada de la salida
```

durante la inferencia.

---

# 74. Ejemplo completo

Supongamos que construimos un sistema de auditoría con IA.

Tenemos:

```text
Datos históricos
        ↓
dataset de entrenamiento
        ↓
modelo
```

Después, en producción:

```text
archivo Excel nuevo
        ↓
pipeline de datos
        ↓
limpieza
        ↓
transformación
        ↓
contexto
        ↓
LLM
        ↓
prompt
        ↓
hallazgos
```

El Excel nuevo no necesariamente reentrena el modelo.

Está siendo utilizado como **dato de entrada/contexto**.

Si además utilizamos documentos empresariales mediante RAG:

```text
políticas internas
normas
manuales
        ↓
RAG
        ↓
contexto
        ↓
LLM
```

Si posteriormente decidimos realizar fine-tuning:

```text
ejemplos históricos validados
        ↓
fine-tuning
        ↓
nuevo modelo
```

Tenemos tres mecanismos diferentes:

```text
entrada
RAG
fine-tuning
```

y cada uno tiene una función distinta.

---

# 75. Nivel avanzado: el modelo aprende una distribución

Desde una perspectiva estadística, podemos imaginar que los datos proceden de una distribución:

$$
(x,y)\sim P_{data}(x,y)
$$

El entrenamiento busca encontrar parámetros \(\theta\) que permitan al modelo aproximar relaciones presentes en esa distribución.

En un problema supervisado, podemos expresar:

$$
f_\theta(x)\approx y
$$

En un modelo generativo podemos trabajar con:

$$
p_\theta(x)
$$

o, en un modelo autoregresivo:

$$
p_\theta(x_1,\ldots,x_n)
=
\prod_{t=1}^{n}
p_\theta(x_t|x_{<t})
$$

Esto conecta directamente los conceptos:

```text
datos
 ↓
distribución
 ↓
objetivo de entrenamiento
 ↓
parámetros
 ↓
modelo
 ↓
inferencia
```

---

# 76. Datos fuera de distribución

Un caso importante es **OOD — Out-of-Distribution**.

Supongamos que:

```text
Entrenamiento:
datos normales
```

pero en producción recibimos:

```text
datos completamente diferentes
```

Entonces:

$$
x_{production}\not\sim P_{train}
$$

aproximadamente hablando, estamos fuera de la distribución observada durante entrenamiento.

Esto puede producir degradación del rendimiento.

Ejemplos:

```text
entrenamiento:
español formal

producción:
jerga técnica + errores + dialectos
```

o:

```text
entrenamiento:
fotografías de día

producción:
imágenes nocturnas
```

---

# 77. Concepto avanzado: calidad de datos como parte del modelo

En sistemas modernos no debemos pensar:

```text
modelo > datos
```

sino:

```text
datos
+
arquitectura
+
optimización
+
parámetros
+
evaluación
+
sistema de inferencia
```

El rendimiento observable es producto de todo el sistema.

Por eso dos organizaciones que utilizan la misma arquitectura pueden obtener resultados diferentes si sus:

* datos;
* procesos;
* fine-tuning;
* RAG;
* evaluaciones;
* prompts;
* herramientas;

son diferentes.

---

# 78. Regla de oro

> **Un modelo no aprende "la verdad". Aprende patrones a partir de los datos utilizados durante su entrenamiento.**

Por eso:

```text
datos incorrectos
      ↓
patrones incorrectos
      ↓
posible comportamiento incorrecto
```

Pero también:

```text
datos correctos
      +
mal diseño del modelo
      +
mala evaluación
```

pueden producir un sistema deficiente.

Los datos son fundamentales, pero no son el único factor.

---

# 79. Lo que el estudiante debe recordar

Si solamente debes recordar diez conceptos:

### 1. Los datos son la materia prima del aprendizaje automático.

### 2. Los datos pueden ser texto, números, imágenes, audio, vídeo, código y mucho más.

### 3. El modelo no es una copia literal del dataset.

### 4. El entrenamiento utiliza datos para ajustar parámetros.

### 5. La calidad de los datos influye en los patrones que puede aprender el modelo.

### 6. Los datos de entrenamiento y los datos de contexto cumplen funciones diferentes.

### 7. RAG proporciona información externa durante la inferencia.

### 8. Fine-tuning utiliza datos para modificar parámetros mediante entrenamiento adicional.

### 9. Los datos pueden contener sesgos, errores, leakage o información maliciosa.

### 10. La calidad de un sistema de IA depende de mucho más que del modelo.

---

# 80. Mapa conceptual

```text
                         DATOS
                           │
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
      estructurados   no estructurados   multimodales
          │                │                │
          └────────────────┼────────────────┘
                           ↓
                    PREPROCESAMIENTO
                           ↓
              ┌────────────┴────────────┐
              ↓                         ↓
       entrenamiento                 inferencia
              ↓                         ↓
         parámetros                  contexto
              ↓                         ↓
            modelo                    RAG
              │                         │
              └────────────┬────────────┘
                           ↓
                        SALIDA
                           ↓
                     EVALUACIÓN
                           ↓
                     PRODUCCIÓN
                           ↓
                    NUEVOS DATOS
```

---

# 81. Conexión con los próximos módulos

Después de comprender los datos, el estudiante ya puede comenzar a estudiar qué ocurre matemáticamente con ellos.

La progresión será:

```text
03 — ¿Qué es un modelo?
        ↓
04 — Entrenamiento e inferencia
        ↓
05 — Datos
        ↓
06 — Parámetros
        ↓
07 — Tokens
        ↓
08 — Contexto
        ↓
09 — Inferencia
        ↓
10 — Limitaciones de los LLM
```

Posteriormente:

```text
Datos
 ↓
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
Generación
 ↓
Prompt Engineering
```

---

# 82. Conclusión

Un sistema de inteligencia artificial no comienza con el prompt.

Comienza mucho antes:

```text
MUNDO REAL
    ↓
DATOS
    ↓
PREPARACIÓN
    ↓
ENTRENAMIENTO
    ↓
PARÁMETROS
    ↓
MODELO
    ↓
INFERENCIA
    ↓
PROMPT + CONTEXTO
    ↓
RESPUESTA
```

Por eso, cuando una persona escribe:

> "¿Por qué la IA respondió esto?"

no siempre debemos mirar únicamente el prompt.

También debemos preguntar:

```text
¿Qué modelo?
¿Qué arquitectura?
¿Qué datos?
¿Qué entrenamiento?
¿Qué postentrenamiento?
¿Qué contexto?
¿Qué información recuperada?
¿Qué parámetros de generación?
¿Qué herramientas?
¿Qué evaluación?
```

Esta forma de pensar marca la diferencia entre **utilizar una IA** y comenzar a **ingeniar sistemas de IA**.

> **El prompt controla una parte de la inferencia. El comportamiento del sistema es el resultado de una cadena mucho más grande que comienza con los datos.**
