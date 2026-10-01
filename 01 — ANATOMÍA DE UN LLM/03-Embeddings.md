# 03 — Embeddings

> **Nivel:** Fundamentos técnicos de LLM
> **Ruta:** Ingeniería de Prompt — De Cero a Sistemas de IA
> **Prerequisito:** `02-Tokenizacion.md`
> **Nivel académico:** Inicial → Maestría / PhD
> **Actualizado:** septiembre de 2026

---

# 1. Objetivo

Al finalizar este capítulo, el estudiante podrá:

* Explicar qué es un embedding.
* Diferenciar un token de un token ID y de un embedding.
* Comprender por qué una red neuronal necesita representaciones numéricas.
* Entender qué significa representar información mediante vectores.
* Comprender intuitivamente qué significa que dos vectores estén cerca.
* Entender qué es una matriz de embeddings.
* Comprender cómo un token ID se transforma en un vector.
* Diferenciar embeddings estáticos de representaciones contextuales.
* Comprender por qué el contexto modifica la representación de una palabra.
* Entender la relación entre embeddings y Transformers.
* Comprender el papel de los embeddings en LLM, RAG y búsqueda semántica.
* Conocer conceptos matemáticos como dimensión, norma, producto punto y similitud coseno.
* Entender las limitaciones de utilizar embeddings como representación del significado.
* Relacionar embeddings con Ingeniería de Prompt y Context Engineering.

---

# 2. La pregunta que debemos responder

En el capítulo anterior llegamos a:

```text
Texto
  ↓
Tokenización
  ↓
Tokens
  ↓
Token IDs
```

Por ejemplo:

```text
"casa"
   ↓
token
   ↓
ID = 1523
```

Pero aparece un problema.

El número:

```text
1523
```

es solamente un identificador.

No significa matemáticamente:

```text
1523 = casa
```

ni:

```text
1523 > 800 = más relacionado
```

El modelo necesita una representación mucho más rica.

Aquí aparecen los:

> **Embeddings**

---

# 3. ¿Qué es un embedding?

Un **embedding** es una representación numérica, normalmente vectorial, de un objeto o unidad de información.

En el contexto de los LLM, un embedding puede representar un token, una secuencia de tokens, un documento, una consulta u otra información, dependiendo del sistema.

Una representación extremadamente simplificada podría ser:

```text
"casa"
   ↓
[0.21, -0.74, 0.13, 0.82, ...]
```

Ese conjunto de números constituye un vector.

Podemos escribir:

$$
\mathbf{e} =
[e_1,e_2,e_3,\ldots,e_d]
$$

donde:

* \(\mathbf{e}\) = vector de embedding;
* \(e_i\) = una componente del vector;
* \(d\) = dimensión del embedding.

---

# 4. Una analogía sencilla

Imaginemos que queremos representar ciudades mediante coordenadas.

Podríamos utilizar:

```text
Ciudad        Latitud       Longitud
-------------------------------------
Quito         -0.18         -78.47
Guayaquil     -2.17         -79.92
Cuenca        -2.90         -79.00
```

Cada ciudad tiene una posición en un espacio.

En un embedding ocurre algo conceptualmente parecido:

```text
concepto
   ↓
posición en un espacio matemático
```

Pero existe una diferencia fundamental:

> En un embedding real no tenemos solamente dos dimensiones.

Podemos tener cientos o miles de dimensiones, dependiendo del modelo y del tipo de embedding.

---

# 5. ¿Qué significa "espacio vectorial"?

Un **espacio vectorial** es un entorno matemático en el que podemos representar objetos mediante vectores y realizar operaciones sobre ellos.

Imaginemos primero dos dimensiones:

```text
          y
          ↑
          │       ● gato
          │
          │
          │   ● perro
          │
          │
          └────────────────→ x
```

Cada punto representa un vector.

En un modelo real podríamos tener:

```text
[0.21, -0.74, 0.13, 0.82, ...]
```

con muchas dimensiones.

No podemos visualizar directamente un espacio de 768 o 3072 dimensiones, pero matemáticamente podemos trabajar con él.

---

# 6. ¿El embedding contiene una definición explícita?

No.

Supongamos:

```text
"perro"
```

tiene un embedding:

```text
[0.13, -0.41, 0.72, ...]
```

No significa que:

```text
0.13 = animal
-0.41 = mamífero
0.72 = doméstico
```

No debemos interpretar normalmente cada dimensión como una característica humana claramente identificable.

Las dimensiones forman parte de una representación distribuida.

Por eso:

> **Un embedding no es una lista de propiedades legibles por una persona.**

---

# 7. Representación distribuida

Los modelos neuronales suelen representar información de manera distribuida.

En lugar de:

```text
PERRO
=
animal + mamífero + mascota
```

podemos tener:

```text
PERRO
=
[0.17, -0.38, 0.71, ...]
```

y la información relevante está distribuida a través del vector.

Esto permite representar relaciones complejas.

---

# 8. Token ID vs embedding

Esta diferencia debe quedar absolutamente clara.

## Token ID

Es un identificador discreto.

```text
"casa"
   ↓
1523
```

## Embedding

Es un vector.

```text
1523
 ↓
[0.21, -0.74, 0.13, ...]
```

Por tanto:

```text
Token ID
≠
Embedding
```

Podemos representarlo:

```text
Texto
 ↓
Tokenizer
 ↓
Token ID
 ↓
Embedding
 ↓
Vector
```

---

# 9. ¿De dónde sale el embedding?

En un LLM típico existe una estructura conocida como:

> **Embedding matrix**

o:

> **Matriz de embeddings**

Conceptualmente:

$$
E \in \mathbb{R}^{V \times d}
$$

donde:

* \(V\) = tamaño del vocabulario;
* \(d\) = dimensión del embedding;
* \(E\) = matriz de embeddings.

Por ejemplo, imaginemos:

```text
Vocabulario = 50 000 tokens
Dimensión = 768
```

La matriz tendría conceptualmente:

$$
50\,000 \times 768
$$

valores.

Cada fila corresponde a una representación vectorial asociada con un token.

---

# 10. Visualización de una matriz de embeddings

Imaginemos una matriz pequeña:

| Token | Dimensión 1 | Dimensión 2 | Dimensión 3 | Dimensión 4 |
| ----- | ----------: | ----------: | ----------: | ----------: |
| casa  |        0.21 |       -0.74 |        0.13 |        0.82 |
| perro |        0.15 |       -0.31 |        0.68 |        0.72 |
| gato  |        0.17 |       -0.35 |        0.64 |        0.69 |
| avión |       -0.72 |        0.51 |       -0.12 |        0.20 |

Los números son completamente ilustrativos.

La matriz real de un modelo tiene muchas más dimensiones.

---

# 11. Lookup: obtener el vector correspondiente

Si el tokenizer produce:

```text
token ID = 1523
```

el modelo puede utilizar ese ID para seleccionar la fila correspondiente de la matriz de embeddings.

Conceptualmente:

```text
ID = 1523
     ↓
Matriz de embeddings
     ↓
Fila 1523
     ↓
Vector
```

Podemos representarlo como:

$$
\mathbf{e}_{1523}=E[1523]
$$

Este proceso suele conceptualizarse como un **embedding lookup**.

---

# 12. ¿Cómo se aprende la matriz?

La matriz de embeddings no suele ser diseñada manualmente por un programador.

Sus valores se aprenden durante el entrenamiento.

Conceptualmente:

```text
Datos
 ↓
Entrenamiento
 ↓
Predicciones
 ↓
Error
 ↓
Actualización de parámetros
 ↓
Matriz de embeddings
```

La matriz de embeddings forma parte de los parámetros aprendibles del modelo, en arquitecturas que utilizan una tabla de embeddings de este tipo.

---

# 13. Embeddings no son diccionarios

Un error común sería imaginar:

```text
casa → [0.2, 0.7, ...]
```

como si el vector fuera una definición matemática de "casa".

No.

El embedding es una representación que resulta útil para que el modelo aprenda y procese relaciones.

La información emerge de las interacciones entre:

* embeddings;
* capas de la red;
* atención;
* parámetros;
* contexto;
* entrenamiento.

---

# 14. La idea de proximidad

Una de las propiedades más interesantes de los espacios de embeddings es que podemos estudiar relaciones entre vectores.

Supongamos:

```text
perro
gato
automóvil
```

Conceptualmente podríamos encontrar:

```text
perro ───── gato
  │
  │
automóvil
```

donde:

```text
distancia(perro, gato)
<
distancia(perro, automóvil)
```

Esto puede reflejar que determinados patrones de representación hacen que "perro" y "gato" estén relacionados de alguna manera.

Pero debemos tener cuidado:

> **La cercanía geométrica no significa automáticamente "significado idéntico".**

---

# 15. Similitud semántica

En aplicaciones de embeddings se utiliza con frecuencia la idea de similitud.

Por ejemplo:

```text
Consulta:
"perro doméstico"

Documento A:
"Los perros son animales de compañía."

Documento B:
"El precio de los automóviles aumentó."
```

Un sistema de búsqueda semántica puede representar:

```text
consulta → vector
documento A → vector
documento B → vector
```

y comparar sus vectores.

Conceptualmente:

```text
consulta
   ↓
vector Q

documento A
   ↓
vector A

documento B
   ↓
vector B
```

Si:

$$
sim(Q,A) > sim(Q,B)
$$

el sistema puede considerar A más relacionado con la consulta.

---

# 16. Similitud coseno

Una medida muy utilizada para comparar vectores es la:

> **Similitud coseno**

Se define como:

$$
\cos(\theta)
=
\frac{\mathbf{A}\cdot\mathbf{B}}
{\|\mathbf{A}\|\|\mathbf{B}\|}
$$

donde:

* \(\mathbf{A}\) y \(\mathbf{B}\) son vectores;
* \(\mathbf{A}\cdot\mathbf{B}\) es el producto punto;
* \(\|\mathbf{A}\|\) y \(\|\mathbf{B}\|\) son sus normas.

La intuición es:

> Comparar la orientación de dos vectores, no simplemente su longitud.

---

# 17. Ejemplo sencillo de similitud coseno

Supongamos:

$$
A=[1,0]
$$

y:

$$
B=[1,0]
$$

Entonces:

$$
\cos(\theta)=1
$$

Los vectores apuntan en la misma dirección.

Ahora:

$$
A=[1,0]
$$

$$
C=[0,1]
$$

Entonces:

$$
\cos(\theta)=0
$$

Son ortogonales.

Finalmente:

$$
D=[-1,0]
$$

Entonces:

$$
\cos(\theta)=-1
$$

Apuntan en direcciones opuestas.

Estos ejemplos son bidimensionales para facilitar la comprensión.

Los embeddings reales suelen tener muchas más dimensiones.

---

# 18. Distancia vs similitud

No debemos confundir:

```text
distancia
```

con:

```text
similitud
```

En general:

```text
mayor similitud
→ representaciones más relacionadas según la métrica utilizada
```

mientras:

```text
menor distancia
→ puntos geométricamente más cercanos
```

Existen diferentes métricas:

* distancia euclídea;
* similitud coseno;
* producto punto;
* otras métricas especializadas.

La métrica adecuada depende de la tarea.

---

# 19. Embeddings estáticos

Históricamente, modelos como:

* Word2Vec;
* GloVe;
* FastText;

popularizaron representaciones vectoriales de palabras.

En estos enfoques, una palabra podía tener una representación relativamente fija.

Por ejemplo:

```text
banco
 ↓
vector
```

El problema aparece con palabras polisémicas.

Consideremos:

```text
Fui al banco a depositar dinero.
```

y:

```text
Me senté en el banco del parque.
```

La palabra:

```text
banco
```

tiene diferentes significados.

Un único vector fijo resulta limitado para representar ambos sentidos.

---

# 20. Representaciones contextuales

Los modelos modernos pueden producir representaciones que dependen del contexto.

Consideremos:

```text
El banco aprobó el préstamo.
```

y:

```text
Me senté en el banco.
```

Aunque aparece la misma palabra:

```text
banco
```

su representación dentro del procesamiento puede diferenciarse debido al contexto.

Conceptualmente:

```text
banco + contexto financiero
        ↓
representación contextual A
```

mientras:

```text
banco + contexto parque
        ↓
representación contextual B
```

Esta es una diferencia fundamental respecto de los embeddings estáticos.

---

# 21. Embedding inicial vs representación contextual

Aquí debemos introducir una distinción importante.

En un Transformer podemos pensar conceptualmente en:

```text
Token
 ↓
Embedding inicial
 ↓
Capas del Transformer
 ↓
Representación contextual
```

El embedding inicial proporciona una representación asociada al token.

Después, las capas del modelo transforman esa representación utilizando información contextual.

Por tanto:

```text
embedding inicial
≠
representación final contextual
```

Esta distinción será crucial cuando estudiemos:

```text
04-Transformer.md
05-Attention.md
```

---

# 22. Ejemplo con "banco"

Tenemos:

```text
Frase A:
El banco aprobó mi crédito.
```

y:

```text
Frase B:
Me senté en el banco del parque.
```

Podemos representar conceptualmente:

```text
                    banco
                      │
          ┌───────────┴───────────┐
          ↓                       ↓
     contexto financiero      contexto parque
          ↓                       ↓
  representación contextual  representación contextual
           A                       B
```

El token inicial puede ser el mismo.

La representación después del procesamiento contextual puede diferenciarse.

---

# 23. ¿El embedding "entiende" el significado?

No debemos decirlo de forma absoluta.

Es mejor decir:

> El embedding proporciona una representación matemática que permite al modelo trabajar con relaciones aprendidas entre unidades de información.

El significado no está contenido como una definición humana simple dentro de un vector.

El comportamiento semántico emerge del sistema completo.

---

# 24. Embeddings y geometría

Una de las ideas más poderosas es pensar en un espacio geométrico.

Imaginemos que tenemos:

```text
gato
perro
caballo
automóvil
avión
```

El modelo aprende representaciones que pueden organizarse de manera que ciertas relaciones sean capturadas geométricamente.

Podemos visualizar una reducción hipotética a dos dimensiones:

```text
           animales

      ● gato
        ● perro
           ● caballo


                         ● automóvil
                                  ● avión
```

Esta representación es solamente una visualización.

El embedding real puede tener cientos o miles de dimensiones.

---

# 25. No debemos interpretar dimensiones individualmente

Supongamos un vector:

```text
[0.12, -0.82, 0.31, 0.44, ...]
```

No debemos afirmar:

```text
dimensión 1 = género
dimensión 2 = animal
dimensión 3 = tamaño
```

salvo que una investigación específica demuestre una relación interpretable.

En redes neuronales profundas, las representaciones suelen ser distribuidas y altamente interdependientes.

Esto es una de las razones por las que la interpretabilidad de redes neuronales es un campo de investigación importante.

---

# 26. Embeddings y relaciones

En determinados modelos de representación, pueden aparecer relaciones geométricas interesantes.

Un ejemplo histórico famoso asociado a Word2Vec es:

$$
king - man + woman \approx queen
$$

Este ejemplo demuestra que ciertas relaciones lingüísticas pueden aparecer como estructuras geométricas.

Pero no debemos convertirlo en una regla universal.

Los embeddings modernos pueden tener:

* espacios distintos;
* objetivos distintos;
* propiedades diferentes;
* geometrías diferentes.

Por tanto:

> **Las propiedades de un embedding dependen del modelo y de cómo fue entrenado.**

---

# 27. Dimensionalidad

La cantidad de componentes de un vector se denomina:

> **Dimensionalidad**

Por ejemplo:

```text
Vector A:
[0.1, 0.2, 0.3]
```

tiene:

```text
3 dimensiones
```

Mientras:

```text
Vector B:
[0.1, 0.2, 0.3, 0.4, ..., 0.8]
```

podría tener:

```text
768 dimensiones
```

La dimensionalidad es una característica importante del espacio de representación.

---

# 28. ¿Más dimensiones significa mejor?

No necesariamente.

Una representación con más dimensiones puede tener mayor capacidad representacional, pero también puede implicar:

* mayor memoria;
* mayor costo computacional;
* almacenamiento mayor;
* búsqueda más costosa;
* posibles problemas de eficiencia.

La dimensión debe diseñarse de acuerdo con la tarea y arquitectura.

Por tanto:

```text
más dimensiones
≠
automáticamente mejor embedding
```

---

# 29. Embeddings de diferentes modelos no son directamente intercambiables

Supongamos:

```text
Modelo A:
"perro" → vector de 768 dimensiones
```

y:

```text
Modelo B:
"perro" → vector de 1024 dimensiones
```

No podemos asumir que:

```text
vector A
```

y:

```text
vector B
```

viven en el mismo espacio.

Incluso si ambos tienen:

```text
768 dimensiones
```

eso no significa que sean directamente comparables.

La geometría depende del modelo.

---

# 30. Embeddings para búsqueda semántica

Una aplicación muy importante es la búsqueda semántica.

Supongamos una colección:

```text
Documento 1:
Manual de vacaciones.

Documento 2:
Política de seguridad informática.

Documento 3:
Procedimiento de contratación.
```

Convertimos cada documento o fragmento en un embedding:

```text
Documento 1 → vector A
Documento 2 → vector B
Documento 3 → vector C
```

El usuario pregunta:

```text
¿Qué dice la empresa sobre los días libres?
```

La consulta también se transforma:

```text
consulta → vector Q
```

Después calculamos similitud:

```text
Q ↔ A
Q ↔ B
Q ↔ C
```

El documento más relacionado puede recuperarse.

---

# 31. Embeddings y RAG

Ahora podemos comprender mejor una parte de RAG.

La arquitectura conceptual es:

```text
                 DOCUMENTOS
                      │
                      ↓
                 CHUNKING
                      │
                      ↓
                  EMBEDDING
                      │
                      ↓
              VECTOR DATABASE
                      │
                      │
Usuario ──→ Embedding de consulta
                      │
                      ↓
                 BÚSQUEDA
                      │
                      ↓
             DOCUMENTOS RELEVANTES
                      │
                      ↓
                    LLM
                      │
                      ↓
                  RESPUESTA
```

Aquí los embeddings permiten buscar información por similitud semántica.

---

# 32. ¿El embedding reemplaza al LLM?

No.

Un modelo de embeddings y un LLM pueden ser modelos o componentes diferentes con objetivos diferentes.

Un modelo de embeddings puede estar optimizado para:

* búsqueda;
* recuperación;
* clustering;
* clasificación;
* similitud.

Un LLM generativo puede estar optimizado para:

* generación;
* transformación;
* diálogo;
* código;
* instrucciones;
* razonamiento.

Por eso:

```text
Embedding model
≠
LLM generativo
```

aunque ambos utilicen representaciones vectoriales.

---

# 33. Vector databases

Cuando tenemos muchos embeddings necesitamos una forma eficiente de almacenarlos y buscarlos.

Aquí aparecen las:

> **Bases de datos vectoriales**

Conceptualmente:

```text
Documento
 ↓
Embedding
 ↓
Vector Database
```

Después:

```text
Consulta
 ↓
Embedding
 ↓
Búsqueda vectorial
 ↓
Vectores similares
```

Estas tecnologías son fundamentales en sistemas RAG y búsqueda semántica.

---

# 34. Búsqueda semántica vs búsqueda por palabras

Supongamos que tenemos:

```text
Documento:
"Los empleados disponen de quince días de descanso anual."
```

El usuario busca:

```text
¿Cuántos días de vacaciones tengo?
```

Una búsqueda literal podría tener problemas porque:

```text
vacaciones
```

no aparece exactamente en el documento.

Una búsqueda semántica puede detectar una relación entre:

```text
vacaciones
```

y:

```text
descanso anual
```

mediante representaciones vectoriales.

Esto es una de las aplicaciones más importantes de los embeddings.

---

# 35. Embeddings y clustering

También podemos utilizar embeddings para agrupar información.

Supongamos:

```text
10 000 mensajes de clientes
```

Los transformamos:

```text
mensajes
 ↓
embeddings
 ↓
clustering
```

Podrían emerger grupos relacionados con:

```text
facturación
soporte
reclamos
ventas
cancelaciones
problemas técnicos
```

Sin necesidad de definir manualmente todas las categorías antes del análisis.

---

# 36. Embeddings y clasificación

Los embeddings también pueden utilizarse como representación para tareas de clasificación.

Conceptualmente:

```text
Texto
 ↓
Embedding
 ↓
Clasificador
 ↓
Categoría
```

Por ejemplo:

```text
"Mi tarjeta fue bloqueada"
```

podría representarse mediante un vector y posteriormente clasificarse como:

```text
soporte financiero
```

dependiendo del sistema.

---

# 37. Embeddings de imágenes, audio y otros datos

El concepto de embedding no pertenece exclusivamente al texto.

Podemos construir representaciones vectoriales de:

* imágenes;
* audio;
* video;
* documentos;
* código;
* usuarios;
* productos;
* eventos.

Por ejemplo:

```text
Imagen
 ↓
Modelo
 ↓
Vector
```

o:

```text
Audio
 ↓
Modelo
 ↓
Vector
```

Esto permite construir sistemas multimodales.

---

# 38. Espacios multimodales

En determinados sistemas se pueden diseñar representaciones donde diferentes modalidades puedan relacionarse.

Conceptualmente:

```text
Texto ───────┐
             │
Imagen ──────┼──→ Espacio de representación
             │
Audio ───────┘
```

Esto permite tareas como:

```text
imagen → texto relacionado
texto → imagen relacionada
imagen → imagen similar
```

La implementación concreta depende del modelo.

---

# 39. Embeddings y contexto

Hasta ahora hemos visto:

```text
token
 ↓
embedding
```

Pero en un Transformer el procesamiento no se detiene allí.

Tenemos:

```text
Tokens
 ↓
Embeddings iniciales
 ↓
Información posicional
 ↓
Capas Transformer
 ↓
Representaciones contextuales
```

Esto es fundamental.

El embedding inicial proporciona un punto de partida.

Las capas posteriores transforman esa representación considerando otros tokens.

---

# 40. Ejemplo: "Java"

Consideremos:

```text
Estoy aprendiendo Java.
```

y:

```text
Tomé un café en Java.
```

Dependiendo del contexto, la palabra puede referirse a:

```text
lenguaje de programación
```

o:

```text
una ubicación geográfica
```

La representación contextual permite que el sistema incorpore la información alrededor del token.

Conceptualmente:

```text
Java
 +
"programación"
 ↓
representación contextual A
```

mientras:

```text
Java
 +
"café"
 ↓
representación contextual B
```

---

# 41. Embedding y atención

El próximo paso será estudiar:

> **Transformer**

y posteriormente:

> **Attention**

La conexión conceptual es:

```text
Token
 ↓
Embedding
 ↓
Transformer
 ↓
Attention
 ↓
Contexto
 ↓
Representación contextual
```

La atención permite que las representaciones de los tokens incorporen información de otros elementos de la secuencia.

Por ejemplo:

```text
El perro mordió al hombre porque estaba asustado.
```

Para interpretar:

```text
"estaba"
```

el contexto completo puede ser importante.

La atención ayuda al modelo a establecer relaciones entre posiciones de la secuencia.

---

# 42. Una advertencia importante: embedding no significa "significado"

Es tentador decir:

> "El embedding es el significado de una palabra."

Es una simplificación demasiado fuerte.

Es mejor decir:

> **El embedding es una representación vectorial aprendida que permite al modelo trabajar matemáticamente con información y relaciones.**

El significado emerge del procesamiento y del sistema completo.

---

# 43. Una advertencia adicional: cercanía no implica verdad

Supongamos que dos conceptos aparecen muy cerca en un espacio de embeddings.

Eso no significa necesariamente que:

* sean verdaderos;
* sean equivalentes;
* uno implique al otro;
* tengan relación causal;
* uno sea correcto respecto al otro.

La similitud vectorial mide una relación geométrica según el espacio y la métrica utilizada.

No constituye por sí sola una prueba factual.

---

# 44. Sesgos en embeddings

Los embeddings aprenden patrones presentes en los datos utilizados para entrenar el modelo.

Si los datos contienen:

* sesgos;
* asociaciones históricas;
* estereotipos;
* errores;
* desequilibrios;

las representaciones pueden reflejar parte de ellos.

Por eso los embeddings no deben considerarse representaciones objetivas y neutrales del mundo.

Esto tiene implicaciones importantes en:

* clasificación;
* recomendación;
* búsqueda;
* contratación;
* sistemas de decisión;
* moderación.

La evaluación del modelo debe considerar estas posibilidades.

---

# 45. Embeddings y privacidad

Un embedding no debe asumirse automáticamente como una representación imposible de revertir.

Dependiendo del sistema y del tipo de información, pueden existir riesgos relacionados con:

* recuperación de información;
* inferencia;
* memorias;
* exposición de datos;
* ataques contra modelos;
* reconstrucción o extracción parcial.

Por ello, cuando se almacenan embeddings de información sensible, deben aplicarse controles de seguridad apropiados.

---

# 46. Embeddings en sistemas empresariales

Consideremos una empresa con:

```text
100 000 documentos
```

Podemos construir:

```text
documentos
 ↓
fragmentación
 ↓
embeddings
 ↓
índice vectorial
```

Después:

```text
Empleado:
"¿Cuál es el procedimiento para reportar
un incidente de seguridad?"
```

El sistema:

```text
pregunta
 ↓
embedding
 ↓
búsqueda semántica
 ↓
documentos relevantes
 ↓
LLM
 ↓
respuesta
```

Esto combina:

```text
Embeddings
+
búsqueda
+
LLM
```

y constituye una arquitectura típica de aplicaciones de IA modernas.

---

# 47. Embeddings y chunking

En RAG normalmente no convertimos un libro completo en un único vector.

Podemos dividirlo:

```text
Documento
   ↓
Fragmento 1
Fragmento 2
Fragmento 3
...
Fragmento N
```

Después:

```text
Fragmento 1 → embedding
Fragmento 2 → embedding
Fragmento 3 → embedding
...
```

Esto permite recuperar fragmentos relevantes.

El diseño del tamaño de los fragmentos afecta:

* precisión de recuperación;
* cantidad de contexto;
* costo;
* redundancia;
* calidad de respuesta.

Este tema aparecerá posteriormente en Context Engineering y RAG.

---

# 48. Embeddings no son memoria

Otra confusión frecuente:

> "Si guardo un embedding, el LLM ya recuerda el documento."

No necesariamente.

Un embedding es una representación vectorial utilizada por una aplicación.

Para utilizarlo como parte de un sistema de memoria o recuperación se necesita una arquitectura que:

1. almacene la representación;
2. permita recuperarla;
3. determine cuándo recuperarla;
4. incorpore la información relevante al contexto;
5. permita al modelo utilizarla.

Por tanto:

```text
embedding
≠
memoria completa
```

---

# 49. Un pipeline completo

Ahora podemos construir un pipeline más detallado:

```text
                   TEXTO
                     │
                     ↓
                TOKENIZADOR
                     │
                     ↓
                 TOKEN IDs
                     │
                     ↓
             MATRIZ DE EMBEDDINGS
                     │
                     ↓
             EMBEDDINGS INICIALES
                     │
                     ↓
        INFORMACIÓN POSICIONAL
                     │
                     ↓
                TRANSFORMER
                     │
                     ↓
                 ATTENTION
                     │
                     ↓
        REPRESENTACIONES CONTEXTUALES
                     │
                     ↓
              PREDICCIÓN
                     │
                     ↓
              NUEVOS TOKENS
```

Todavía debemos estudiar varios componentes.

---

# 50. El papel del embedding en un LLM

Podemos resumir su función así:

```text
Token ID
   ↓
Representación vectorial
   ↓
Entrada útil para la red neuronal
```

El embedding permite pasar de una representación discreta:

```text
1523
```

a una representación continua:

```text
[0.21, -0.74, 0.13, ...]
```

Esta transformación es fundamental para que las redes neuronales puedan procesar la información mediante operaciones matemáticas.

---

# 51. Discreto vs continuo

Esta distinción es importante.

### Token ID

Representación discreta:

```text
1523
```

### Embedding

Representación continua:

```text
[0.21, -0.74, 0.13, ...]
```

Podemos visualizar:

```text
TOKEN
  │
  │ identificación
  ↓
ID DISCRETO
  │
  │ lookup
  ↓
VECTOR CONTINUO
```

Esto permite que el modelo opere sobre espacios donde existen relaciones geométricas.

---

# 52. ¿Por qué no usar simplemente el número del token?

Supongamos:

```text
gato = 100
perro = 101
avión = 102
```

Si utilizáramos directamente esos números, podríamos introducir una relación artificial:

```text
distancia(100,101) = 1
distancia(100,102) = 2
```

Pero no existe ninguna razón para asumir que:

```text
gato
```

es el doble de parecido a:

```text
perro
```

que a:

```text
avión
```

El ID es solamente una etiqueta.

El embedding permite aprender una geometría más útil.

---

# 53. Ejemplo con estudiantes

Imaginemos una clase con estudiantes identificados:

```text
Jorge = 1
Ana = 2
Luis = 3
Pedro = 4
```

Los números son identificadores.

No podemos concluir:

```text
Ana está más relacionada con Jorge
```

simplemente porque:

```text
2 está cerca de 1
```

Los IDs no contienen esa información.

Un embedding podría representar características aprendidas:

```text
Jorge → vector
Ana   → vector
Luis  → vector
Pedro → vector
```

y las relaciones geométricas entre esos vectores podrían ser informativas para una tarea determinada.

---

# 54. Producto punto

Otra operación fundamental es el:

> **Producto punto**

Dados:

$$
A=[a_1,a_2,\ldots,a_n]
$$

y:

$$
B=[b_1,b_2,\ldots,b_n]
$$

tenemos:

$$
A\cdot B
=
\sum_{i=1}^{n}a_i b_i
$$

Por ejemplo:

$$
A=[1,2]
$$

$$
B=[3,4]
$$

entonces:

$$
A\cdot B=(1)(3)+(2)(4)=11
$$

El producto punto es fundamental en redes neuronales y aparecerá nuevamente al estudiar atención.

---

# 55. Norma de un vector

La norma euclídea de:

$$
A=[a_1,a_2,\ldots,a_n]
$$

puede expresarse como:

$$
\|A\|
=
\sqrt{
a_1^2+a_2^2+\cdots+a_n^2
}
$$

Para:

$$
A=[3,4]
$$

tenemos:

$$
\|A\|=\sqrt{3^2+4^2}=5
$$

Estas operaciones matemáticas forman la base de muchas técnicas de comparación vectorial.

---

# 56. ¿Qué aprende realmente el embedding?

Durante el entrenamiento, los valores de las representaciones se ajustan para ayudar al modelo a cumplir su objetivo.

De forma simplificada:

```text
Contexto
 ↓
Predicción
 ↓
Error
 ↓
Gradiente
 ↓
Actualización de parámetros
 ↓
Nueva representación
```

Con millones o miles de millones de ejemplos, estas actualizaciones pueden producir espacios donde determinadas relaciones sean útiles para el comportamiento del modelo.

---

# 57. Embeddings y entrenamiento

Recordemos el capítulo anterior:

```text
Datos
 ↓
Entrenamiento
 ↓
Parámetros
```

Ahora podemos ampliar:

```text
Datos
 ↓
Tokenización
 ↓
Tokens
 ↓
Embeddings
 ↓
Modelo
 ↓
Predicción
 ↓
Error
 ↓
Actualización de parámetros
```

Los embeddings iniciales pueden formar parte de los parámetros entrenables.

Por eso:

> **El espacio de embeddings es una consecuencia del proceso de aprendizaje del modelo.**

---

# 58. Embeddings y fine-tuning

Si un modelo recibe entrenamiento adicional, sus representaciones pueden cambiar.

Conceptualmente:

```text
Modelo base
 ↓
Fine-tuning
 ↓
Modelo especializado
```

Durante ese proceso pueden modificarse parámetros que afectan las representaciones.

Por eso:

```text
embedding del modelo A
```

y:

```text
embedding del modelo especializado B
```

no deben asumirse idénticos.

---

# 59. Embeddings y modelos de representación especializados

No todos los embeddings deben proceder de un LLM generativo.

Puede existir un modelo diseñado específicamente para producir embeddings.

Por ejemplo:

```text
Texto
 ↓
Modelo de embeddings
 ↓
Vector
```

Mientras que un LLM generativo sigue:

```text
Texto
 ↓
LLM
 ↓
Tokens generados
```

Los objetivos pueden ser diferentes.

Esto es importante al diseñar arquitecturas RAG.

---

# 60. ¿Cómo se evalúa un modelo de embeddings?

No basta con preguntar:

> "¿Los vectores parecen razonables?"

Debe evaluarse según la tarea.

Por ejemplo:

### Recuperación

¿Encuentra los documentos correctos?

### Clustering

¿Agrupa correctamente elementos relacionados?

### Clasificación

¿Permite separar categorías?

### Búsqueda

¿Los resultados relevantes aparecen primero?

### Multilingüismo

¿Mantiene relaciones entre diferentes idiomas?

La evaluación debe utilizar métricas apropiadas para el objetivo.

---

# 61. Limitaciones

Los embeddings presentan limitaciones.

### 1. Dependencia del modelo

Diferentes modelos producen espacios diferentes.

### 2. Pérdida de información

Una representación vectorial no necesariamente conserva toda la información original.

### 3. Ambigüedad

La similitud puede no representar exactamente la relación que queremos.

### 4. Sesgos

Puede reflejar patrones problemáticos presentes en los datos.

### 5. Dependencia de la métrica

La similitud depende de cómo comparemos los vectores.

### 6. Distribución de datos

El rendimiento puede degradarse cuando los datos reales son muy diferentes de los utilizados durante el entrenamiento.

---

# 62. Error conceptual: "más cerca = más relacionado en todo"

No.

La cercanía solamente tiene sentido dentro de:

```text
un espacio
+
un modelo
+
una métrica
+
una tarea
```

Por ejemplo:

```text
vector A
```

puede ser muy cercano a:

```text
vector B
```

para una tarea semántica general, pero eso no implica necesariamente:

```text
A = B
```

ni:

```text
A causa B
```

ni:

```text
A es verdadero
```

---

# 63. Conexión con Ingeniería de Prompt

¿Por qué un ingeniero de prompt debería estudiar embeddings?

Porque permite comprender que el modelo no procesa:

```text
"Escribe una respuesta profesional."
```

como una intención humana abstracta.

La entrada pasa por una cadena de representaciones:

```text
Texto
 ↓
Tokens
 ↓
IDs
 ↓
Embeddings
 ↓
Representaciones contextuales
 ↓
Transformaciones
 ↓
Predicción
```

Esto explica por qué:

* estructura;
* contexto;
* repetición;
* posición;
* vocabulario;
* formato;

pueden afectar el comportamiento.

---

# 64. Prompt y representación

Supongamos:

```text
Prompt A:
Resume este documento.
```

y:

```text
Prompt B:
Resume este documento en exactamente cinco puntos.
Cada punto debe incluir una evidencia concreta.
No inventes información.
```

El texto adicional modifica los tokens de entrada.

Esos tokens generan representaciones adicionales.

Esas representaciones participan posteriormente en el procesamiento del modelo.

Por tanto:

```text
Prompt diferente
 ↓
Tokens diferentes
 ↓
Representaciones diferentes
 ↓
Procesamiento diferente
 ↓
Posibles respuestas diferentes
```

---

# 65. Context Engineering

Esta conexión será todavía más importante cuando lleguemos a:

> **Context Engineering**

Un sistema puede proporcionar al modelo:

```text
instrucciones
+
documentos
+
historial
+
resultados de herramientas
+
datos estructurados
```

Todo ello termina formando parte de la información disponible para la inferencia.

Por tanto:

> **Diseñar el contexto significa diseñar qué información recibe el modelo, en qué forma y bajo qué condiciones.**

Los embeddings son una de las tecnologías utilizadas para construir ese contexto en sistemas de recuperación.

---

# 66. Ejemplo profesional: auditoría

Consideremos un sistema de IA para analizar documentos de auditoría.

Tenemos:

```text
1000 documentos
```

El sistema puede:

```text
Documentos
 ↓
Extracción de texto
 ↓
Chunking
 ↓
Embeddings
 ↓
Índice vectorial
```

El auditor pregunta:

```text
¿Qué documentos contienen evidencia
relacionada con ajustes manuales de inventario?
```

El sistema puede:

```text
pregunta
 ↓
embedding
 ↓
búsqueda semántica
 ↓
fragmentos relevantes
 ↓
contexto
 ↓
LLM
 ↓
análisis
```

Aquí podemos observar claramente la cooperación entre:

```text
Embeddings
+
RAG
+
LLM
+
Prompt
```

---

# 67. Ejemplo profesional: chatbot empresarial

Supongamos una empresa que recibe preguntas:

```text
¿Cuántos días de vacaciones tengo?
¿Cómo solicito un permiso?
¿Cuál es el horario?
¿Cómo reporto un incidente?
```

La arquitectura podría ser:

```text
Usuario
 ↓
Pregunta
 ↓
Embedding
 ↓
Búsqueda semántica
 ↓
Base documental
 ↓
Contexto
 ↓
LLM
 ↓
Prompt de respuesta
 ↓
Respuesta
```

Aquí el embedding no responde por sí mismo.

Su función es ayudar a localizar información relevante.

---

# 68. Ejemplo profesional: código

Supongamos un repositorio con:

```text
50 000 archivos de código
```

Podemos crear embeddings de:

```text
funciones
clases
documentación
fragmentos de código
```

Después un programador pregunta:

```text
¿Dónde se valida el token JWT?
```

El sistema puede recuperar fragmentos relacionados semánticamente.

Esto permite construir herramientas de asistencia para:

* navegación de código;
* documentación;
* búsqueda;
* análisis;
* mantenimiento.

---

# 69. Una visión completa

Ahora podemos ampliar nuevamente nuestra arquitectura:

```text
                       TEXTO
                         │
                         ↓
                    TOKENIZADOR
                         │
                         ↓
                    TOKEN IDs
                         │
                         ↓
                EMBEDDING MATRIX
                         │
                         ↓
                 VECTORES INICIALES
                         │
                         ↓
              INFORMACIÓN POSICIONAL
                         │
                         ↓
                    TRANSFORMER
                         │
                         ↓
                     ATTENTION
                         │
                         ↓
              REPRESENTACIÓN CONTEXTUAL
                         │
                         ↓
                  PREDICCIÓN DE TOKENS
                         │
                         ↓
                    DECODIFICACIÓN
                         │
                         ↓
                       TEXTO
```

Ya conocemos las primeras tres etapas.

Ahora estamos preparados para estudiar la arquitectura que transforma esas representaciones:

# Transformer

---

# 70. Lo que el alumno debe recordar

Si solamente recuerdas diez ideas, recuerda estas:

1. **Un embedding es una representación vectorial.**
2. **Un token ID no es un embedding.**
3. **El embedding permite representar información en un espacio numérico.**
4. **Las representaciones se aprenden durante el entrenamiento en modelos que utilizan embeddings aprendidos.**
5. **La proximidad entre vectores puede utilizarse para medir relaciones, pero no equivale automáticamente a significado o verdad.**
6. **Los embeddings iniciales de un LLM pueden transformarse mediante las capas del modelo en representaciones contextuales.**
7. **El contexto puede cambiar la representación funcional de una misma palabra.**
8. **Los embeddings son fundamentales en aplicaciones como búsqueda semántica y RAG.**
9. **Diferentes modelos pueden producir espacios de embeddings diferentes.**
10. **Comprender embeddings es necesario para comprender cómo los tokens llegan a convertirse en representaciones que el Transformer puede procesar.**

---

# 71. Cadena conceptual aprendida

Hasta ahora:

```text
                 TEXTO
                   │
                   ↓
              TOKENIZACIÓN
                   │
                   ↓
                TOKENS
                   │
                   ↓
              TOKEN IDs
                   │
                   ↓
               EMBEDDINGS
                   │
                   ↓
        REPRESENTACIONES NUMÉRICAS
```

En el próximo capítulo añadiremos:

```text
               EMBEDDINGS
                    ↓
                TRANSFORMER
                    ↓
                 ATTENTION
                    ↓
        REPRESENTACIONES CONTEXTUALES
```

---

# 72. Pregunta central para el siguiente capítulo

Ya sabemos que:

```text
"El gato duerme"
```

puede convertirse conceptualmente en:

```text
tokens
 ↓
IDs
 ↓
vectores
```

Pero todavía falta responder una pregunta mucho más importante:

> **¿Cómo puede el modelo determinar que "gato" está relacionado con "duerme", y que la relación entre los tokens depende del contexto?**

La respuesta comienza con una arquitectura que revolucionó el procesamiento del lenguaje:

# Transformer

→ `04-Transformer.md`
