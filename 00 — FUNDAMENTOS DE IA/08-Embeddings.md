# 08 — Embeddings

> **Pregunta central:** ¿Cómo transforma un modelo un token, una palabra, una imagen o un documento en una representación matemática que permita comparar, relacionar y procesar información?

En el módulo anterior vimos que el texto se transforma en **tokens**.

Ahora debemos dar el siguiente paso:

```text
Texto
  ↓
Tokenización
  ↓
Tokens
  ↓
IDs
  ↓
Embeddings
  ↓
Vectores
  ↓
Modelo
```

Un embedding es una **representación numérica, normalmente vectorial, de un elemento que permite que un sistema de aprendizaje automático trabaje con relaciones entre elementos en un espacio matemático**.

Los embeddings son uno de los conceptos más importantes de la IA moderna porque aparecen en:

* LLM;
* búsqueda semántica;
* RAG;
* sistemas de recomendación;
* clasificación;
* clustering;
* recuperación de documentos;
* búsqueda multimodal;
* sistemas de similitud;
* bases de datos vectoriales;
* agentes de IA.

---

# 1. La idea más sencilla

Supongamos que tenemos estas palabras:

```text
perro
gato
automóvil
avión
```

Para un ser humano, resulta evidente que:

```text
perro
```

y:

```text
gato
```

tienen cierta relación.

También sabemos que:

```text
automóvil
```

y:

```text
avión
```

están relacionados con transporte.

Una computadora, sin embargo, no recibe inicialmente esos conceptos como relaciones semánticas humanas.

Necesitamos una representación matemática.

Podemos imaginar:

```text
perro      → [0.82, 0.14, -0.31, ...]
gato       → [0.79, 0.18, -0.27, ...]
automóvil  → [-0.21, 0.71, 0.44, ...]
avión      → [-0.17, 0.68, 0.51, ...]
```

Estos números forman vectores.

La posición de cada elemento en ese espacio puede capturar determinadas relaciones aprendidas.

---

# 2. ¿Qué es un vector?

Antes de entender embeddings debemos entender una idea matemática básica.

Un vector es una secuencia ordenada de números.

Por ejemplo:

```text
[0.2, 0.7, -0.1]
```

es un vector de tres dimensiones.

Otro:

```text
[0.2, 0.7, -0.1, 0.8, 0.4]
```

tiene cinco dimensiones.

En aprendizaje automático podemos trabajar con vectores de cientos o miles de dimensiones.

Por ejemplo:

```text
[0.12, -0.73, 0.44, 0.18, ..., 0.07]
```

---

# 3. ¿Qué significa "dimensión"?

Si tenemos:

```text
v = [0.2, 0.7, -0.1]
```

el vector tiene:

```text
3 dimensiones
```

Si tenemos:

```text
v = [0.2, 0.7, -0.1, 0.4, 0.9]
```

tiene:

```text
5 dimensiones
```

En modelos reales, un embedding puede tener cientos, miles o más dimensiones, dependiendo del modelo.

Por ejemplo, conceptualmente:

```text
Embedding
=
[e₁, e₂, e₃, ..., e_d]
```

donde `d` representa la dimensionalidad.

---

# 4. Una advertencia importante

No debemos imaginar que cada dimensión tiene necesariamente un significado humano directo.

Sería tentador decir:

```text
dimensión 1 = animal
dimensión 2 = tamaño
dimensión 3 = color
```

pero normalmente no funciona de manera tan sencilla.

Las representaciones aprendidas pueden distribuir la información entre muchas dimensiones.

Por eso:

> **Un embedding no es una lista de características humanas explícitas.**

Es una representación aprendida.

---

# 5. De token a embedding

En el módulo anterior vimos:

```text
Texto
↓
Token
↓
ID
```

Ahora añadimos:

```text
ID
↓
Embedding
```

Podemos imaginar:

```text
"gato"
  ↓
token ID = 5821
  ↓
fila 5821 de una matriz
  ↓
vector
```

Por ejemplo:

```text
[0.14, -0.31, 0.72, 0.09, ...]
```

Ese vector puede utilizarse como representación inicial del token.

---

# 6. La matriz de embeddings

Supongamos que un vocabulario tiene:

```text
V = 100.000 tokens
```

y cada embedding tiene:

```text
d = 4.096 dimensiones
```

Podemos tener una matriz:

```text
E ∈ ℝ^(100000 × 4096)
```

Conceptualmente:

```text
             Dimensiones
        1      2      3     ... 4096
      ┌─────────────────────────────┐
ID 1  │ 0.2   -0.1    0.7   ...     │
ID 2  │ 0.4    0.8   -0.2   ...     │
ID 3  │-0.1    0.3    0.5   ...     │
...   │ ...    ...    ...   ...     │
ID N  │ 0.7   -0.4    0.2   ...     │
      └─────────────────────────────┘
```

Cada fila representa un token.

---

# 7. ¿El embedding contiene el significado completo?

No.

Esta es una distinción muy importante.

Podemos decir que el embedding proporciona una representación útil para el modelo, pero no debemos imaginar:

```text
Embedding
=
definición completa de la palabra
```

El significado depende de:

* representación;
* contexto;
* arquitectura;
* parámetros;
* entrenamiento;
* posición;
* relaciones con otros tokens.

Por eso un embedding no debe interpretarse como un diccionario matemático perfecto.

---

# 8. Embeddings aprendidos

Los embeddings normalmente no son diseñados manualmente.

Se aprenden.

Durante el entrenamiento, el modelo ajusta sus parámetros para producir representaciones útiles para su objetivo.

Conceptualmente:

```text
Datos
 ↓
Entrenamiento
 ↓
optimización
 ↓
representaciones
 ↓
embeddings útiles
```

Esto conecta directamente con el módulo anterior:

```text
Parámetros
↓
aprendizaje
↓
representaciones
```

---

# 9. La intuición del "espacio"

Una de las mejores formas de entender un embedding es imaginar un **espacio vectorial**.

Supongamos que reducimos artificialmente el problema a dos dimensiones.

Podríamos tener:

```text
             animales
                 ↑
                 │
        gato ●   │   ● perro
                 │
                 │
─────────────────┼────────────────→
                 │
       automóvil ●     ● avión
                 │
                 │
```

Los puntos representan embeddings.

La idea es que elementos con determinadas relaciones pueden aparecer relativamente próximos.

---

# 10. Cercanía semántica

Si dos elementos tienen embeddings similares, podemos utilizar una medida de similitud para estimar su relación.

Por ejemplo:

```text
embedding("perro")
```

puede estar más próximo a:

```text
embedding("gato")
```

que a:

```text
embedding("computadora")
```

Esto permite realizar:

```text
búsqueda semántica
```

sin exigir coincidencia exacta de palabras.

---

# 11. Similitud ≠ identidad

Si dos vectores están cerca:

```text
v₁ ≈ v₂
```

no significa que sean iguales.

Y tampoco significa necesariamente que sean sinónimos.

La similitud depende de:

* modelo;
* datos;
* espacio vectorial;
* métrica;
* contexto;
* dominio.

Por tanto:

> **La proximidad vectorial es una señal matemática, no una definición absoluta de significado.**

---

# 12. Distancia y similitud

Para comparar embeddings necesitamos una medida.

Algunas de las más conocidas son:

* distancia euclídea;
* similitud del coseno;
* producto punto;
* distancia Manhattan;
* métricas específicas para determinados sistemas.

Una de las más utilizadas en embeddings es la **similitud del coseno**.

---

# 13. Similitud del coseno

Dados dos vectores:

```text
A
B
```

la similitud del coseno puede expresarse como:

```text
cos(A,B) =
(A · B) / (||A|| ||B||)
```

donde:

* `A · B` = producto punto;
* `||A||` = norma de `A`;
* `||B||` = norma de `B`.

La idea geométrica es comparar el ángulo entre los vectores.

---

# 14. Intuición geométrica

Imaginemos:

```text
          B
         /
        /
       /
      / θ
     /
----/---------------- A
```

Si los vectores apuntan en direcciones similares:

```text
θ pequeño
```

y la similitud del coseno es alta.

Si apuntan en direcciones muy diferentes:

```text
θ grande
```

la similitud disminuye.

---

# 15. Ejemplo simple

Supongamos:

```text
A = [1, 0]
B = [1, 0]
```

Entonces:

```text
cos(A,B) = 1
```

Los vectores tienen exactamente la misma dirección.

Ahora:

```text
A = [1, 0]
B = [0, 1]
```

son perpendiculares:

```text
cos(A,B) = 0
```

Finalmente:

```text
A = [1, 0]
B = [-1, 0]
```

apuntan en direcciones opuestas:

```text
cos(A,B) = -1
```

La interpretación exacta de estos valores depende del espacio y del modelo.

---

# 16. ¿Por qué la similitud del coseno es útil?

Porque permite comparar representaciones independientemente, en gran medida, de su magnitud.

Esto resulta útil cuando queremos responder:

> ¿Qué elementos tienen representaciones similares?

Por ejemplo:

```text
Consulta:
"problemas con facturas"

        ↓

Embedding de la consulta

        ↓

comparar con embeddings de documentos

        ↓

documentos más similares
```

Esto es la base de muchas arquitecturas de búsqueda semántica.

---

# 17. Búsqueda por palabras vs búsqueda semántica

Supongamos un documento:

```text
"La empresa presenta obligaciones tributarias pendientes."
```

y una consulta:

```text
"deudas de impuestos"
```

Una búsqueda tradicional basada únicamente en coincidencia textual podría tener dificultades porque:

```text
obligaciones tributarias
```

no es exactamente:

```text
deudas de impuestos
```

Una búsqueda semántica puede representar ambas expresiones mediante embeddings y encontrar similitud conceptual.

---

# 18. Ejemplo empresarial

Tenemos estos documentos:

```text
D1:
"El cliente mantiene obligaciones tributarias vencidas."

D2:
"El inventario presenta diferencias físicas."

D3:
"Se detectaron errores en la conciliación bancaria."
```

Consulta:

```text
"deudas fiscales"
```

Los embeddings podrían permitir que:

```text
D1
```

sea recuperado aunque no contenga literalmente la frase:

```text
"deudas fiscales"
```

Esta es una de las aplicaciones más importantes de los embeddings.

---

# 19. Embeddings para RAG

Recordemos la arquitectura básica de RAG:

```text
Documentos
    ↓
Chunking
    ↓
Embeddings
    ↓
Base vectorial
    ↓
Consulta
    ↓
Embedding de consulta
    ↓
Búsqueda de similitud
    ↓
Documentos relevantes
    ↓
LLM
```

Los embeddings son el mecanismo que permite comparar semánticamente la consulta con los fragmentos almacenados.

---

# 20. Ejemplo completo de RAG

Supongamos que tenemos:

```text
Manual de auditoría
```

con 1.000 páginas.

Lo dividimos:

```text
Página 1
↓
chunk 1

Página 2
↓
chunk 2

...
```

Cada chunk se transforma:

```text
chunk
↓
embedding
```

y se almacena junto con información adicional:

```text
{
  "texto": "...",
  "embedding": [...],
  "pagina": 84,
  "documento": "manual.pdf"
}
```

Cuando el usuario pregunta:

```text
¿Cuándo debe documentarse una diferencia de auditoría?
```

se genera un embedding para la consulta.

Después:

```text
embedding consulta
        ↓
comparación
        ↓
chunks más similares
```

Los resultados recuperados se entregan al LLM como contexto.

---

# 21. Embedding de documentos

Un documento completo puede transformarse en un embedding.

Pero para RAG normalmente no conviene simplemente:

```text
documento de 500 páginas
↓
1 embedding
```

porque se pierde granularidad.

Es más habitual:

```text
documento
↓
chunks
↓
embedding por chunk
```

Esto permite recuperar fragmentos específicos.

---

# 22. Chunking y embeddings

Existe una relación directa:

```text
Chunking
↓
define unidades de recuperación
↓
Embeddings
↓
representan esas unidades
```

Si el chunk es demasiado grande:

```text
muchos conceptos
↓
embedding demasiado general
```

Si es demasiado pequeño:

```text
poco contexto
↓
representación incompleta
```

Por eso el diseño de chunking es una decisión de ingeniería.

---

# 23. Embeddings y búsqueda semántica

Una arquitectura sencilla:

```text
                 DOCUMENTOS
                     │
                     ▼
                  CHUNKS
                     │
                     ▼
                EMBEDDINGS
                     │
                     ▼
              BASE VECTORIAL
                     ▲
                     │
               embedding
                     │
                  CONSULTA
```

La base vectorial permite recuperar elementos cercanos según una métrica.

---

# 24. ¿Qué es una base de datos vectorial?

Es un sistema diseñado para almacenar y recuperar representaciones vectoriales de manera eficiente.

Ejemplos de tecnologías utilizadas en este ecosistema incluyen:

* FAISS;
* Qdrant;
* Milvus;
* Weaviate;
* pgvector;
* Pinecone;
* Elasticsearch con capacidades vectoriales;
* sistemas de bases de datos que incorporan búsqueda vectorial.

La elección depende de:

* escala;
* infraestructura;
* latencia;
* filtros;
* costos;
* integración;
* necesidades de producción.

---

# 25. No todo RAG necesita una base vectorial dedicada

Esta es una distinción importante.

Un sistema puede utilizar:

```text
PostgreSQL + pgvector
```

en lugar de una base vectorial independiente.

También existen sistemas híbridos:

```text
búsqueda léxica
+
búsqueda vectorial
```

Por tanto:

> **RAG no significa automáticamente "usar una base de datos vectorial independiente".**

RAG es una arquitectura de recuperación aumentada, no una tecnología única.

---

# 26. Búsqueda híbrida

Una búsqueda empresarial puede combinar:

### Búsqueda léxica

Busca coincidencias de términos.

```text
"factura 10382"
```

### Búsqueda semántica

Busca significado relacionado.

```text
"comprobante de compra"
```

### Búsqueda híbrida

Combina ambas.

```text
léxica
+
semántica
↓
ranking
```

Esto puede ser especialmente útil cuando necesitamos recuperar tanto conceptos como identificadores exactos.

---

# 27. Embeddings no sustituyen al LLM

Este error es frecuente.

Un embedding puede responder:

> "¿Qué elementos están relacionados matemáticamente con esta consulta?"

Pero no necesariamente:

> "¿Cuál es la respuesta completa y correcta a esta pregunta?"

Por eso una arquitectura típica es:

```text
Embedding
→ recuperación

LLM
→ interpretación + generación
```

---

# 28. Embedding model vs LLM

No todos los modelos que generan embeddings son necesariamente los mismos modelos utilizados para generar texto.

Podemos tener:

```text
Embedding Model
↓
vector
```

y:

```text
Generative LLM
↓
texto
```

Algunos ecosistemas utilizan modelos especializados para embeddings.

Otros modelos pueden producir representaciones internas que también pueden utilizarse para tareas de recuperación, dependiendo del sistema.

---

# 29. Embeddings estáticos vs contextuales

Este es un concepto avanzado importante.

Los embeddings clásicos de palabras pueden asignar una representación relativamente estable a una palabra.

Por ejemplo:

```text
banco
```

tendría una representación.

Pero la palabra:

```text
banco
```

puede significar:

```text
institución financiera
```

o:

```text
asiento
```

o:

```text
orilla de un río
```

Una representación estática tendría dificultades para distinguir completamente estos usos.

---

# 30. Embeddings contextuales

Los modelos modernos pueden generar representaciones que dependen del contexto.

Por ejemplo:

```text
"Fui al banco a depositar dinero."
```

y:

```text
"Me senté en el banco del parque."
```

Aunque contienen:

```text
banco
```

la representación contextual puede diferenciar sus significados.

Conceptualmente:

```text
banco + contexto A
↓
vector A

banco + contexto B
↓
vector B
```

Esta idea fue fundamental para la evolución del NLP moderno.

---

# 31. Embedding contextual ≠ embedding de vocabulario

Podemos diferenciar:

### Embedding de token

Una representación asociada al token dentro del vocabulario.

### Representación contextual

Una representación que emerge después de procesar el token dentro de su contexto.

Conceptualmente:

```text
Token
 ↓
embedding inicial
 ↓
Transformer
 ↓
representación contextual
```

La segunda depende de los demás tokens.

---

# 32. El mismo token puede tener diferentes representaciones contextuales

Por ejemplo:

```text
"El banco aprobó el préstamo."
```

frente a:

```text
"El banco estaba junto al río."
```

La representación interna de:

```text
banco
```

puede ser diferente después de pasar por las capas del Transformer.

Esto ayuda al modelo a representar polisemia y relaciones contextuales.

---

# 33. Embeddings de frases

También podemos representar:

```text
"El cliente tiene una deuda."
```

como un único vector.

Esto se conoce de manera general como un **sentence embedding** o representación de una frase.

Entonces:

```text
Frase A
↓
vector A
```

y:

```text
Frase B
↓
vector B
```

pueden compararse.

---

# 34. Embeddings de párrafos

La misma idea puede aplicarse a:

* párrafos;
* documentos;
* consultas;
* productos;
* perfiles;
* imágenes;
* fragmentos de código.

Por eso embedding no significa necesariamente:

```text
una palabra → un vector
```

Puede significar:

```text
objeto → representación vectorial
```

donde el objeto puede variar.

---

# 35. Embeddings de código

Podemos representar:

```python
def suma(a, b):
    return a + b
```

mediante un embedding.

Esto permite realizar tareas como:

```text
consulta:
"función que suma dos números"

        ↓

embedding

        ↓

buscar código semánticamente similar
```

Esto tiene aplicaciones en:

* búsqueda de código;
* documentación;
* detección de duplicados;
* recuperación de funciones;
* asistentes de programación.

---

# 36. Embeddings de imágenes

En sistemas multimodales, una imagen también puede tener una representación vectorial.

Por ejemplo:

```text
Imagen de un automóvil
↓
embedding
```

y:

```text
Texto:
"vehículo deportivo"
↓
embedding
```

Si ambos embeddings están entrenados en un espacio compatible, podemos realizar búsquedas cruzadas entre modalidades.

Esto se conoce como una forma de **representación multimodal compartida**.

---

# 37. Búsqueda texto → imagen

Una aplicación:

```text
Usuario:
"camisa negra formal"
```

↓

```text
embedding de texto
```

↓

```text
comparar con embeddings de imágenes
```

↓

```text
imágenes relevantes
```

Esta idea es utilizada en diferentes sistemas de búsqueda multimodal.

---

# 38. Embeddings y recomendaciones

Supongamos que representamos productos:

```text
Producto A → vector A
Producto B → vector B
Producto C → vector C
```

Si un usuario interactúa con:

```text
Producto A
```

podemos buscar productos vectorialmente cercanos.

Esto puede ayudar a construir sistemas de recomendación.

La calidad depende del modelo y de los datos utilizados para aprender las representaciones.

---

# 39. Embeddings y clustering

Los embeddings también permiten agrupar elementos.

Supongamos que tenemos:

```text
10.000 documentos
```

y calculamos un embedding para cada uno.

Después podemos aplicar algoritmos de clustering.

Conceptualmente:

```text
Documentos
 ↓
Embeddings
 ↓
Clustering
 ↓
Grupos temáticos
```

Podríamos descubrir agrupaciones relacionadas con:

* finanzas;
* inventarios;
* recursos humanos;
* ventas;
* auditoría.

---

# 40. Visualización de embeddings

Los embeddings pueden tener:

```text
768 dimensiones
```

o:

```text
1536 dimensiones
```

No podemos visualizarlos directamente.

Podemos utilizar técnicas de reducción dimensional como:

* PCA;
* t-SNE;
* UMAP.

Por ejemplo:

```text
1536 dimensiones
       ↓
      UMAP
       ↓
2 dimensiones
       ↓
gráfico
```

Pero hay una advertencia:

> Una proyección de alta dimensión a dos dimensiones puede distorsionar relaciones.

Por tanto, un gráfico bonito no demuestra automáticamente que el espacio original tenga exactamente esa estructura.

---

# 41. PCA

**PCA (Principal Component Analysis)** busca direcciones que expliquen una parte importante de la variabilidad de los datos.

Conceptualmente:

```text
1536 dimensiones
↓
componentes principales
↓
2 o 3 dimensiones
```

Es útil para exploración y visualización.

---

# 42. t-SNE

**t-SNE** intenta preservar determinadas relaciones locales al proyectar datos de alta dimensión.

Puede producir visualizaciones muy útiles para explorar agrupaciones.

Pero:

* depende de hiperparámetros;
* puede distorsionar distancias globales;
* diferentes ejecuciones pueden producir configuraciones diferentes.

Por eso no debe interpretarse un gráfico t-SNE como un mapa geográfico exacto del espacio semántico.

---

# 43. UMAP

**UMAP** es otra técnica de reducción dimensional utilizada ampliamente para explorar representaciones.

Puede ser útil para:

* visualización;
* clustering exploratorio;
* análisis de embeddings.

Pero comparte la advertencia fundamental:

> Una representación bidimensional es una proyección, no el espacio original.

---

# 44. Embeddings y analogías

Los embeddings clásicos popularizaron la idea de relaciones geométricas.

Un ejemplo famoso es:

```text
rey - hombre + mujer ≈ reina
```

La idea es que determinadas relaciones pueden aparecer como direcciones aproximadamente consistentes en el espacio vectorial.

Pero no debemos convertir esto en una propiedad universal de todos los embeddings modernos.

La geometría depende del modelo y del objetivo de entrenamiento.

---

# 45. Un embedding no es una ontología

Supongamos:

```text
perro
gato
animal
```

Que estén relacionados en un espacio vectorial no significa que el embedding contenga explícitamente una ontología formal:

```text
animal
├── perro
└── gato
```

El espacio captura patrones estadísticos aprendidos.

La estructura conceptual humana y la geometría vectorial no son necesariamente equivalentes.

---

# 46. Embeddings y sesgos

Los embeddings pueden reflejar patrones presentes en los datos con los que fueron entrenados.

Esto puede incluir:

* asociaciones culturales;
* sesgos lingüísticos;
* estereotipos;
* desequilibrios de representación.

Por ejemplo, si los datos contienen una asociación problemática, una representación vectorial puede aprender parte de esa relación.

Por eso los embeddings deben evaluarse y utilizarse con cuidado en sistemas sensibles.

---

# 47. Embeddings y dominio

Un embedding general puede funcionar bien en lenguaje cotidiano pero peor en un dominio altamente especializado.

Por ejemplo:

```text
medicina
derecho
finanzas
ingeniería
código
```

pueden contener terminología específica.

Por eso debemos evaluar:

```text
Embedding general
```

frente a:

```text
Embedding especializado
```

según la tarea.

No existe un embedding universalmente perfecto.

---

# 48. Evaluación de embeddings

No basta con preguntar:

> "¿El embedding parece bueno?"

Debemos medir el rendimiento en la tarea real.

Por ejemplo:

```text
Consulta
↓
retrieval
↓
documento correcto
```

Podemos medir:

* Recall@K;
* Precision@K;
* MRR;
* nDCG;
* tasa de recuperación;
* calidad del ranking;
* latencia;
* costo.

---

# 49. Recall@K

Supongamos:

```text
K = 5
```

y queremos saber si el documento relevante aparece entre los cinco primeros resultados.

Si aparece:

```text
Recall@5 = 1
```

para ese caso.

Si no:

```text
Recall@5 = 0
```

En conjuntos grandes podemos calcular una métrica agregada.

Esto es mucho más útil que evaluar únicamente la similitud matemática.

---

# 50. Similaridad alta no garantiza relevancia

Supongamos:

```text
Consulta:
"política de devoluciones"
```

y el embedding recupera:

```text
Documento:
"Política de compras"
```

Puede ser semánticamente cercano.

Pero si el usuario necesita:

```text
devoluciones
```

el documento puede no ser suficientemente relevante.

Por eso:

```text
similitud vectorial
```

es una señal de recuperación, no una garantía de relevancia.

---

# 51. Embeddings y reranking

Una arquitectura avanzada puede ser:

```text
Consulta
 ↓
Embedding
 ↓
recuperar 100 documentos
 ↓
Reranker
 ↓
seleccionar 10
 ↓
LLM
```

El embedding proporciona una recuperación inicial eficiente.

Un modelo de reranking puede evaluar con mayor detalle la relación entre:

```text
consulta ↔ documento
```

Esto puede mejorar la calidad de recuperación.

---

# 52. Dense retrieval

La búsqueda mediante embeddings suele denominarse **dense retrieval** porque los documentos se representan mediante vectores densos.

Conceptualmente:

```text
Documento
↓
vector denso
```

frente a métodos léxicos tradicionales basados en términos.

Un sistema moderno puede combinar ambos enfoques.

---

# 53. Sparse vs dense representations

Una representación dispersa puede ser:

```text
[0, 0, 0, 4, 0, 0, 0, 8, 0]
```

Una representación densa:

```text
[0.12, -0.42, 0.31, 0.17, 0.55, ...]
```

Los embeddings modernos suelen ser densos.

Esto permite representar información distribuida en muchas dimensiones.

---

# 54. Embeddings y búsqueda aproximada

Si tenemos:

```text
10 millones de vectores
```

comparar la consulta contra todos ellos puede ser costoso.

Por eso se utilizan técnicas de **Approximate Nearest Neighbor (ANN)**.

El objetivo es encontrar vectores cercanos sin necesariamente comparar exhaustivamente todos los elementos.

Entre las estructuras y algoritmos conocidos encontramos:

* HNSW;
* IVF;
* PQ;
* variantes de índices vectoriales.

---

# 55. HNSW

**HNSW (Hierarchical Navigable Small World)** construye una estructura de navegación que permite buscar vecinos aproximados eficientemente.

Conceptualmente:

```text
Muchos vectores
      ↓
estructura de búsqueda
      ↓
navegación
      ↓
vecinos cercanos
```

El objetivo es reducir el costo de búsqueda manteniendo buena calidad de recuperación.

---

# 56. Product Quantization

**Product Quantization (PQ)** permite comprimir representaciones vectoriales para reducir:

* memoria;
* costo de almacenamiento;
* costo de búsqueda.

La idea general es dividir el vector en partes y representar esas partes mediante códigos más compactos.

Esto conecta directamente:

```text
Embeddings
+
Cuantización
+
Búsqueda vectorial
```

---

# 57. Embeddings y almacenamiento

Un sistema que almacena millones de embeddings debe considerar:

```text
dimensionalidad
×
cantidad de vectores
×
bytes por dimensión
```

Por ejemplo, si almacenamos:

```text
1.000.000 vectores
```

de:

```text
1.536 dimensiones
```

en:

```text
4 bytes por dimensión
```

el almacenamiento bruto aproximado sería:

```text
1.000.000 × 1.536 × 4
≈ 6,144 GB
```

sin contar índices, metadatos y overhead.

Esto demuestra que los embeddings tienen implicaciones reales de infraestructura.

---

# 58. Embeddings y normalización

En algunos sistemas se normalizan los vectores para que tengan norma unitaria:

```text
||v|| = 1
```

Cuando todos los vectores están normalizados, el producto punto y la similitud del coseno pueden relacionarse directamente.

La decisión depende del modelo y de la infraestructura.

No debemos normalizar automáticamente todos los embeddings sin comprobar las recomendaciones del modelo utilizado.

---

# 59. Embeddings y distancia

Dependiendo del modelo y del sistema de recuperación podemos utilizar:

```text
coseno
producto punto
distancia euclídea
```

No existe una única métrica universalmente correcta.

La métrica debe ser compatible con:

* el embedding model;
* el entrenamiento;
* la normalización;
* el índice;
* la tarea.

---

# 60. Embeddings y fine-tuning

Los modelos de embeddings también pueden adaptarse.

Por ejemplo, podemos tener:

```text
Embedding general
↓
datos específicos del dominio
↓
adaptación
↓
Embedding especializado
```

Esto puede mejorar la recuperación en determinados escenarios.

Pero requiere evaluación rigurosa.

---

# 61. Embeddings y entrenamiento contrastivo

Muchos modelos modernos de representación se entrenan mediante objetivos que intentan acercar representaciones relacionadas y separar representaciones no relacionadas.

Una intuición simplificada:

```text
Consulta
   ↕
Documento relevante

→ acercarlos
```

y:

```text
Consulta
   ↔
Documento irrelevante

→ separarlos
```

Esto está relacionado con el aprendizaje contrastivo y otros objetivos de representación.

---

# 62. Ejemplo conceptual de entrenamiento contrastivo

Tenemos:

```text
Consulta:
"¿Cómo declarar impuestos?"

Documento positivo:
"Guía para declaraciones tributarias."

Documento negativo:
"Manual de mantenimiento de inventario."
```

El entrenamiento puede intentar producir:

```text
sim(consulta, positivo)
        ↑
      alta

sim(consulta, negativo)
        ↓
      baja
```

La función exacta depende del modelo y del objetivo.

---

# 63. Embeddings y espacio semántico

Podemos pensar en:

```text
                         Finanzas
                            ●
                         ●
                      ●
        Auditoría ●
                 \
                  \
                   ● Contabilidad
```

como una visualización simplificada.

Pero debemos recordar:

> El espacio real puede tener cientos o miles de dimensiones y no puede representarse fielmente en un dibujo bidimensional.

---

# 64. ¿Un embedding "entiende"?

No debemos utilizar la palabra "entiende" sin matices.

Un embedding puede capturar relaciones útiles para una tarea.

Por ejemplo:

```text
"factura vencida"
```

puede quedar cerca de:

```text
"cuenta por cobrar pendiente"
```

porque los datos y el objetivo de entrenamiento produjeron esa relación.

Pero eso no demuestra comprensión humana del concepto.

---

# 65. Embeddings y significado

Una definición útil:

> **Un embedding es una representación numérica aprendida que transforma un objeto en un vector dentro de un espacio donde determinadas relaciones relevantes para una tarea pueden expresarse mediante relaciones geométricas.**

Esta definición es más precisa que:

> "Un embedding es el significado de una palabra."

---

# 66. Embeddings y contexto

Ahora podemos distinguir tres niveles:

```text
TOKEN
↓
representación inicial
↓
EMBEDDING
↓
procesamiento contextual
↓
representación contextual
```

Y esto se conecta con el siguiente módulo:

```text
Tokens
↓
Embeddings
↓
Contexto
↓
Atención
↓
Inferencia
```

---

# 67. ¿El embedding es un parámetro?

Aquí debemos ser muy precisos.

La **matriz de embeddings de un modelo** puede formar parte de los parámetros entrenables del modelo.

Por ejemplo:

```text
E ∈ ℝ^(V×d)
```

puede ser una matriz de parámetros.

Pero el **vector obtenido al buscar una fila de esa matriz para un token concreto durante una ejecución** es una representación/activación derivada.

Por tanto:

```text
Matriz de embeddings
→ parámetros

Vector producido para una entrada
→ representación / activación
```

No debemos confundir ambos conceptos.

---

# 68. Embeddings y parámetros: relación completa

Podemos visualizar:

```text
                 PARÁMETROS
                     │
                     ▼
              Matriz de embeddings
                     │
                     ▼
                  Token ID
                     │
                     ▼
                Vector inicial
                     │
                     ▼
                Transformer
                     │
                     ▼
          Representación contextual
```

Esto conecta directamente el módulo 06 con el módulo 08.

---

# 69. Embeddings y atención

El Transformer no recibe simplemente:

```text
"gato"
```

sino representaciones numéricas.

Una simplificación:

```text
Tokens
  ↓
Embeddings
  ↓
Representaciones
  ↓
Q, K, V
  ↓
Atención
```

La atención permite que las representaciones de diferentes posiciones interactúen.

Por eso los embeddings son una de las puertas de entrada al procesamiento profundo del Transformer.

---

# 70. Embeddings posicionales

Además de representar qué token tenemos, el modelo necesita representar información relacionada con su posición.

Históricamente se utilizaron embeddings posicionales explícitos.

Otros Transformers modernos utilizan mecanismos como:

* RoPE;
* ALiBi;
* otras estrategias posicionales.

No todos los modelos modernos representan la posición exactamente de la misma manera.

Lo importante es:

```text
contenido
+
posición/estructura
↓
representación procesable
```

La arquitectura concreta se estudiará posteriormente.

---

# 71. Embeddings y modelos multimodales

En un sistema multimodal podemos tener:

```text
Texto
 ↓
Embedding textual
```

y:

```text
Imagen
 ↓
Embedding visual
```

Si ambos están alineados mediante entrenamiento:

```text
espacio compartido
```

podemos comparar modalidades.

Conceptualmente:

```text
"perro"
     ●
       \
        \
         ●
       imagen de perro
```

Esto permite aplicaciones como:

* búsqueda texto-imagen;
* clasificación multimodal;
* recuperación cruzada;
* sistemas de recomendación.

---

# 72. Embeddings y agentes

Los agentes pueden utilizar embeddings para:

* memoria semántica;
* recuperación de experiencias;
* búsqueda documental;
* selección de herramientas;
* recuperación de instrucciones;
* clasificación de tareas.

Por ejemplo:

```text
Nueva consulta
      ↓
Embedding
      ↓
buscar experiencias similares
      ↓
recuperar información
      ↓
LLM
```

Pero debemos distinguir entre:

```text
memoria externa
```

y:

```text
parámetros del modelo
```

Guardar un embedding en una base de datos no significa reentrenar el LLM.

---

# 73. Memoria semántica

Un sistema puede almacenar:

```text
Información
↓
Embedding
↓
Base vectorial
```

Posteriormente:

```text
Nueva consulta
↓
Embedding
↓
buscar recuerdos similares
↓
recuperar
```

Esto puede proporcionar una forma de memoria externa.

La arquitectura exacta depende del sistema.

---

# 74. Embeddings y seguridad

Los embeddings también tienen implicaciones de seguridad y privacidad.

Un vector puede parecer:

```text
[0.17, -0.31, 0.84, ...]
```

y no ser legible para una persona.

Pero eso no significa automáticamente que sea:

```text
datos sin valor
```

Dependiendo del sistema, los embeddings pueden permitir inferir información sobre el contenido original o facilitar ataques de recuperación.

Por eso no deben tratarse automáticamente como datos anónimos.

---

# 75. Embeddings y privacidad

Si una empresa transforma:

```text
historial médico
```

en embeddings:

```text
vector
```

no debemos asumir que la información deja de ser sensible.

Los embeddings forman parte de los datos derivados del sistema y deben manejarse según las políticas de privacidad y seguridad aplicables.

---

# 76. Embeddings y poisoning

En sistemas RAG, un atacante podría intentar introducir documentos diseñados para ser recuperados por determinadas consultas.

Conceptualmente:

```text
Documento malicioso
      ↓
embedding
      ↓
base vectorial
      ↓
recuperación
      ↓
LLM
```

Si el documento es recuperado:

```text
contenido malicioso
```

puede entrar en el contexto del modelo.

Esto conecta embeddings con:

* seguridad de RAG;
* data poisoning;
* retrieval poisoning;
* prompt injection indirecto.

---

# 77. Embeddings no validan la verdad

Una consulta puede recuperar un documento porque es semánticamente similar.

Eso no significa:

```text
documento recuperado
=
información verdadera
```

Por eso un sistema RAG profesional necesita también:

* fuentes confiables;
* metadatos;
* filtros;
* validación;
* ranking;
* evaluación;
* controles de acceso.

---

# 78. Embeddings y filtros

Una búsqueda empresarial puede combinar:

```text
similitud vectorial
+
filtros estructurados
```

Por ejemplo:

```text
Consulta:
"facturas vencidas"

Filtro:
empresa = "ABC"
año >= 2025
departamento = "Finanzas"
```

La búsqueda puede utilizar:

```text
vector similarity
+
metadata filtering
```

Esto suele ser mucho más útil que depender exclusivamente de similitud semántica.

---

# 79. Embeddings no son una base de conocimiento

Un embedding:

```text
vector
```

no sustituye automáticamente a:

```text
base de datos
```

Un sistema empresarial puede necesitar:

```text
Base relacional
+
búsqueda vectorial
+
documentos
+
LLM
```

Cada componente cumple una función diferente.

---

# 80. Cuándo usar embeddings

Son especialmente útiles cuando necesitamos:

* búsqueda semántica;
* recuperación de documentos;
* encontrar elementos similares;
* clustering;
* recomendación;
* clasificación;
* recuperación multimodal;
* memoria semántica.

No debemos introducir embeddings solamente porque sean una tecnología de moda.

La arquitectura debe responder a una necesidad concreta.

---

# 81. Cuándo NO son suficientes

Los embeddings no sustituyen necesariamente:

* SQL para consultas exactas;
* reglas de negocio;
* sistemas transaccionales;
* cálculos matemáticos;
* validaciones determinísticas;
* índices tradicionales;
* conocimiento estructurado.

Por ejemplo:

```text
¿Cuánto suman exactamente estas 5 facturas?
```

es una tarea para un sistema de cálculo, no simplemente para búsqueda semántica.

---

# 82. Embeddings y sistemas híbridos

Una arquitectura profesional puede ser:

```text
                CONSULTA
                   │
          ┌────────┴────────┐
          ▼                 ▼
   Búsqueda léxica    Búsqueda vectorial
          │                 │
          └────────┬────────┘
                   ▼
                 RANKING
                   │
                   ▼
                  RAG
                   │
                   ▼
                  LLM
```

Esto combina las fortalezas de diferentes mecanismos.

---

# 83. Ejemplo aplicado a auditoría

Supongamos un sistema de auditoría con:

```text
10.000 documentos
```

Incluye:

* políticas contables;
* contratos;
* facturas;
* procedimientos;
* informes;
* notas de auditoría.

El usuario pregunta:

```text
"¿Qué procedimiento corresponde cuando existe una diferencia
significativa entre inventario físico y contable?"
```

Podemos realizar:

```text
Pregunta
 ↓
Embedding
 ↓
búsqueda vectorial
 ↓
documentos relevantes
 ↓
filtros
 ↓
reranking
 ↓
contexto
 ↓
LLM
 ↓
respuesta fundamentada
```

Aquí el embedding funciona como mecanismo de recuperación.

---

# 84. Ejemplo aplicado a programación

Repositorio:

```text
500.000 líneas de código
```

Consulta:

```text
"¿Dónde se valida que el usuario tenga permisos antes de eliminar una cuenta?"
```

El sistema puede:

```text
consulta
↓
embedding
↓
búsqueda semántica
↓
funciones relevantes
↓
LLM
```

Esto puede ser mucho más útil que buscar literalmente:

```text
"permisos"
```

porque la lógica puede utilizar términos diferentes.

---

# 85. Ejemplo aplicado a Workflows empresariales

Supongamos un chatbot empresarial que recibe:

```text
"Quiero información sobre el curso avanzado de Excel."
```

El sistema puede transformar la consulta en un embedding y buscar:

```text
Cursos
Preguntas frecuentes
Precios
Horarios
Requisitos
```

Luego recuperar información relevante y entregarla al LLM.

Esto permite combinar:

```text
WhatsApp
+
RAG
+
Embeddings
+
LLM
+
CRM
```

---

# 86. El embedding como interfaz matemática

Podemos pensar en el embedding como una interfaz:

```text
MUNDO REAL
   ↓
texto / imagen / código / objeto
   ↓
EMBEDDING
   ↓
ESPACIO VECTORIAL
   ↓
comparación / recuperación / clasificación
```

Esta interfaz permite que operaciones matemáticas trabajen con información compleja.

Pero la representación siempre depende de cómo fue entrenado el modelo.

---

# 87. Nivel avanzado: función de embedding

Podemos representar un modelo de embeddings como:

```text
f : X → ℝᵈ
```

donde:

* `X` = conjunto de entradas;
* `d` = dimensionalidad;
* `f(x)` = embedding de `x`.

Por ejemplo:

```text
f("factura vencida")
→
v ∈ ℝ¹⁵³⁶
```

Luego podemos calcular:

```text
sim(f(x), f(y))
```

para medir cierta relación entre `x` e `y`.

---

# 88. Objetivo de representación

Un embedding útil intenta organizar el espacio de forma que determinadas relaciones relevantes sean representables.

Por ejemplo:

```text
x⁺ = documento relevante
x⁻ = documento irrelevante
```

queremos:

```text
sim(f(q), f(x⁺))
>
sim(f(q), f(x⁻))
```

para una consulta `q`.

La función exacta de entrenamiento puede utilizar diferentes pérdidas contrastivas o de ranking.

---

# 89. El espacio no es universal

Supongamos:

```text
Embedding Model A
```

y:

```text
Embedding Model B
```

Un vector producido por A:

```text
[0.2, 0.7, ...]
```

no puede compararse directamente con uno producido por B:

```text
[-0.1, 0.4, ...]
```

aunque tengan la misma dimensionalidad.

¿Por qué?

Porque pertenecen a espacios aprendidos diferentes.

Por eso:

> **No se deben mezclar embeddings de modelos diferentes sin una transformación o estrategia compatible.**

---

# 90. Cambiar de embedding model

Si una aplicación RAG cambia:

```text
Embedding Model A
```

por:

```text
Embedding Model B
```

normalmente debemos considerar regenerar los embeddings existentes.

No basta con cambiar el modelo de consulta.

Conceptualmente:

```text
Documentos
 ↓
Embedding A
 ↓
Base vectorial
```

si cambiamos a:

```text
Embedding B
```

deberíamos evaluar:

```text
re-embedding de documentos
```

para mantener un espacio compatible.

---

# 91. Embeddings y versiones

En sistemas profesionales debemos versionar:

```text
Embedding model
+
dimensionalidad
+
métrica
+
preprocesamiento
+
chunking
+
configuración
```

Porque cambiar cualquiera de estos elementos puede alterar el comportamiento de recuperación.

---

# 92. Evaluación de un sistema RAG

No debemos evaluar únicamente:

```text
¿el LLM respondió bien?
```

También debemos separar:

### Retrieval

¿Se recuperaron los documentos correctos?

### Generation

¿El LLM utilizó correctamente esos documentos?

Podemos representar:

```text
Consulta
 ↓
Retrieval
 ↓
Contexto
 ↓
Generation
 ↓
Respuesta
```

Un sistema puede fallar en cualquiera de las etapas.

---

# 93. Retrieval failure

Si el documento correcto nunca fue recuperado:

```text
Embedding
↓
búsqueda
↓
documento incorrecto
```

el LLM puede no tener la información necesaria.

Por eso:

> **Un problema de recuperación no siempre se soluciona mejorando el prompt del LLM.**

Puede ser necesario mejorar:

* embedding model;
* chunking;
* índice;
* filtros;
* query rewriting;
* reranking.

---

# 94. Generation failure

También puede ocurrir:

```text
documento correcto
↓
recuperado correctamente
↓
LLM interpreta mal
↓
respuesta incorrecta
```

Aquí el problema está más cerca de:

* prompt;
* modelo generativo;
* instrucciones;
* contexto;
* razonamiento;
* formato de salida.

Esta separación es fundamental para depurar sistemas RAG.

---

# 95. Query embedding vs document embedding

En un sistema de recuperación podemos tener:

```text
Documento
↓
document embedding
```

y:

```text
Consulta
↓
query embedding
```

Luego:

```text
similaridad(
    query embedding,
    document embedding
)
```

La forma exacta de entrenar ambos puede variar.

Algunos modelos utilizan arquitecturas o espacios diseñados específicamente para recuperación.

---

# 96. Asimetría en recuperación

No todas las consultas y documentos tienen la misma estructura.

Una consulta puede ser:

```text
"¿Cómo funciona el cierre contable?"
```

mientras un documento contiene:

```text
"Procedimiento mensual para cierre y conciliación..."
```

Los modelos de embedding modernos pueden entrenarse específicamente para manejar esta relación:

```text
query → document
```

Esto es diferente de simplemente comparar dos frases arbitrarias.

---

# 97. Embeddings y clasificación

También podemos utilizar embeddings como características para un clasificador.

Por ejemplo:

```text
Documento
 ↓
Embedding
 ↓
Clasificador
 ↓
Riesgo alto / medio / bajo
```

Esto puede combinar modelos de representación con algoritmos tradicionales de machine learning.

---

# 98. Embeddings y clustering empresarial

Podemos analizar miles de tickets:

```text
Ticket 1
Ticket 2
...
Ticket 100.000
```

y convertirlos en embeddings.

Después:

```text
Embeddings
↓
clustering
↓
temas emergentes
```

Esto puede descubrir categorías que no habían sido definidas manualmente.

---

# 99. Embeddings y deduplicación

Si tenemos:

```text
"Factura pendiente de pago."
```

y:

```text
"Factura aún no pagada."
```

los embeddings pueden indicar alta similitud.

Esto puede utilizarse para:

* detectar documentos similares;
* encontrar duplicados semánticos;
* agrupar preguntas equivalentes;
* consolidar tickets.

Pero la similitud no garantiza que dos elementos sean realmente duplicados.

---

# 100. Limitaciones de los embeddings

Los embeddings son extremadamente útiles, pero tienen limitaciones.

### 1. Dependencia del modelo

Distintos modelos generan espacios diferentes.

### 2. Dependencia del dominio

Un modelo general puede no ser ideal para un área especializada.

### 3. Pérdida de información

Convertir un documento en un vector puede perder detalles importantes.

### 4. Ambigüedad

La similitud semántica no garantiza relevancia exacta.

### 5. Sesgo

Los embeddings pueden reflejar sesgos de los datos.

### 6. Costos

Generar y almacenar millones de vectores tiene un costo.

### 7. Seguridad

Los embeddings deben protegerse como datos derivados potencialmente sensibles.

---

# 101. Un embedding no reemplaza el documento original

Esto es muy importante en RAG.

No deberíamos almacenar solamente:

```text
embedding
```

También necesitamos normalmente:

```text
texto original
+
metadatos
+
identificador
+
fuente
+
embedding
```

Porque cuando recuperamos un vector necesitamos saber:

```text
¿qué documento representa?
¿de dónde proviene?
¿qué página?
¿qué versión?
¿qué permisos tiene?
```

---

# 102. Embeddings y gobernanza

Un sistema empresarial serio debe considerar:

```text
Origen
↓
Documento
↓
Chunk
↓
Embedding
↓
Índice
↓
Recuperación
↓
LLM
↓
Respuesta
```

Esto permite establecer trazabilidad.

Si no sabemos de dónde proviene un embedding:

```text
vector
↓
?
```

tenemos un problema de gobernanza.

---

# 103. Embeddings y control de acceso

Supongamos que tenemos:

```text
Documento A → público
Documento B → confidencial
Documento C → solo RRHH
```

Una búsqueda vectorial no debería ignorar los permisos.

No basta con:

```text
"documento semánticamente similar"
```

Debe aplicarse:

```text
similitud
+
control de acceso
```

antes de entregar información al modelo.

Este es un punto crítico en RAG empresarial.

---

# 104. Embeddings y arquitectura de seguridad

Una arquitectura profesional puede ser:

```text
Usuario
   ↓
Autenticación
   ↓
Autorización
   ↓
Consulta
   ↓
Embedding
   ↓
Filtro de permisos
   ↓
Búsqueda vectorial
   ↓
Reranking
   ↓
Contexto permitido
   ↓
LLM
```

Nunca debemos asumir que la similitud vectorial reemplaza los controles de seguridad.

---

# 105. Embeddings y calidad de datos

Recordemos el módulo 05:

```text
Datos
↓
calidad
↓
representación
```

Si los documentos contienen:

* basura;
* duplicados;
* información incorrecta;
* datos obsoletos;
* contenido malicioso;

los embeddings pueden representar esa información igualmente.

Por tanto:

> **Un embedding no corrige automáticamente la calidad de los datos.**

---

# 106. Embeddings y actualización de conocimiento

Si cambia un documento:

```text
Versión 1
↓
embedding V1
```

y después:

```text
Versión 2
```

debemos considerar actualizar el embedding correspondiente.

Por eso un sistema RAG necesita un proceso de sincronización:

```text
Documento actualizado
↓
reprocesamiento
↓
nuevo embedding
↓
actualización del índice
```

---

# 107. Embeddings en un pipeline profesional

Podemos imaginar:

```text
             INGESTA
                │
                ▼
           DOCUMENTOS
                │
                ▼
             LIMPIEZA
                │
                ▼
            CHUNKING
                │
                ▼
           EMBEDDINGS
                │
                ▼
        ÍNDICE VECTORIAL
                │
                ▼
              QUERY
                │
                ▼
       QUERY EMBEDDING
                │
                ▼
       RETRIEVAL + FILTROS
                │
                ▼
            RERANKING
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

Este pipeline conecta varios conceptos que ya hemos estudiado.

---

# 108. Relación con Prompt Engineering

Los embeddings parecen estar lejos del Prompt Engineering, pero en realidad están muy relacionados.

Supongamos que diseñamos un sistema RAG.

El prompt podría ser perfecto:

```text
Utiliza exclusivamente la información proporcionada
y cita la fuente correspondiente.
```

Pero si el retrieval devuelve documentos incorrectos:

```text
query
↓
embedding deficiente
↓
documentos incorrectos
↓
prompt perfecto
↓
respuesta incorrecta
```

Por tanto:

> **No todos los problemas de un sistema LLM son problemas de prompt.**

Esta es una de las lecciones más importantes de ingeniería de IA.

---

# 109. Prompt Engineering vs Retrieval Engineering

Podemos separar:

### Prompt Engineering

Optimiza:

```text
instrucciones
estructura
restricciones
formato
contexto
```

### Retrieval Engineering

Optimiza:

```text
chunking
embeddings
índice
consulta
filtros
reranking
```

### Model Engineering

Optimiza:

```text
arquitectura
parámetros
fine-tuning
cuantización
inferencia
```

Un sistema profesional necesita las tres perspectivas.

---

# 110. Mapa completo hasta ahora

Después de los módulos anteriores:

```text
DATOS
  ↓
ENTRENAMIENTO
  ↓
PARÁMETROS
  ↓
MODELO
  ↓
TOKENIZACIÓN
  ↓
TOKENS
  ↓
EMBEDDINGS
  ↓
REPRESENTACIONES
  ↓
CONTEXTO
  ↓
ATENCIÓN
  ↓
INFERENCIA
  ↓
GENERACIÓN
```

Ahora el alumno puede empezar a ver el LLM como un **pipeline computacional**, no como una caja negra.

---

# 111. Nivel maestría/PhD: representación vectorial

Podemos formalizar un embedding como:

```text
fθ : X → ℝᵈ
```

donde:

* `X` = espacio de entradas;
* `θ` = parámetros aprendidos;
* `d` = dimensionalidad;
* `fθ(x)` = representación vectorial de `x`.

Para dos entradas `x₁` y `x₂` podemos calcular:

```text
sim(fθ(x₁), fθ(x₂))
```

utilizando una función de similitud.

Por ejemplo:

```text
cos(fθ(x₁), fθ(x₂))
```

El objetivo del entrenamiento puede estar diseñado para que determinados pares tengan representaciones próximas y otros alejadas.

---

# 112. Embeddings y función de pérdida

Un objetivo contrastivo simplificado podría intentar maximizar:

```text
sim(q, d⁺)
```

y minimizar:

```text
sim(q, d⁻)
```

donde:

* `q` = consulta;
* `d⁺` = documento relevante;
* `d⁻` = documento irrelevante.

Una función de pérdida concreta podría utilizar variantes de contrastive loss, triplet loss, InfoNCE u otros objetivos.

La elección depende del modelo.

---

# 113. Alta dimensionalidad

Un embedding de:

```text
1536 dimensiones
```

no puede visualizarse directamente.

Podemos imaginarlo como:

```text
v =
[
v₁,
v₂,
v₃,
...
v₁₅₃₆
]
```

Cada dimensión forma parte de una representación conjunta.

La información relevante puede distribuirse entre muchas dimensiones.

Esto se relaciona con el concepto de **representación distribuida** estudiado anteriormente.

---

# 114. La maldición de la dimensionalidad

Cuando aumenta la dimensionalidad, algunos métodos tradicionales de búsqueda y análisis pueden volverse menos eficientes.

Esto se relaciona con la llamada **maldición de la dimensionalidad**.

Por ejemplo:

```text
dimensión ↑
↓
espacio de búsqueda ↑
↓
dificultad de determinadas operaciones ↑
```

Por eso los sistemas vectoriales utilizan índices y técnicas especializadas.

---

# 115. Vecinos más cercanos

Una operación fundamental es:

```text
k-nearest neighbors
```

o:

```text
k-NN
```

Dado un vector de consulta:

```text
q
```

buscamos:

```text
v₁, v₂, ..., vₖ
```

que sean sus vecinos más cercanos según una métrica determinada.

En RAG:

```text
q = embedding de la pregunta
```

y:

```text
vᵢ = embeddings de documentos
```

---

# 116. Approximate Nearest Neighbor

Cuando la cantidad de vectores es enorme, buscar exactamente puede ser costoso.

Por eso usamos:

```text
ANN
=
Approximate Nearest Neighbor
```

La idea es:

```text
búsqueda ligeramente aproximada
+
muchísima mayor eficiencia
```

La ingeniería consiste en equilibrar:

```text
recall
vs
latencia
vs
memoria
vs
costo
```

---

# 117. El embedding como representación, no como verdad

Esta frase resume una idea crítica:

> **Un embedding representa patrones aprendidos; no representa necesariamente la verdad objetiva de un objeto.**

Si un modelo aprende asociaciones incorrectas:

```text
datos incorrectos
↓
representación incorrecta
```

Por eso los embeddings deben evaluarse en el contexto de la tarea.

---

# 118. Checklist profesional

Antes de implementar embeddings, debemos preguntar:

### Modelo

* ¿Qué embedding model utilizaremos?
* ¿Cuál es su dimensionalidad?
* ¿Está diseñado para nuestro idioma?
* ¿Está diseñado para nuestro dominio?

### Datos

* ¿Qué documentos entrarán?
* ¿Están limpios?
* ¿Hay duplicados?
* ¿Hay información obsoleta?

### Chunking

* ¿Cuál será el tamaño?
* ¿Habrá overlap?
* ¿Cómo se preservará el contexto?

### Recuperación

* ¿Qué métrica utilizaremos?
* ¿Qué índice?
* ¿Cuántos resultados recuperaremos?
* ¿Habrá reranking?

### Seguridad

* ¿Se respetan permisos?
* ¿Cómo se protegen los embeddings?
* ¿Existe riesgo de poisoning?

### Evaluación

* ¿Cómo mediremos Recall@K?
* ¿Cómo mediremos relevancia?
* ¿Cómo comprobaremos la respuesta final?

---

# 119. Lo que debemos recordar

Si solamente recuerdas diez cosas de este módulo:

### 1.

Un embedding es una representación vectorial aprendida.

### 2.

Un token y un embedding no son lo mismo.

### 3.

El ID de un token es un índice, no su significado.

### 4.

Los embeddings permiten representar relaciones mediante geometría.

### 5.

La similitud del coseno es una métrica común, pero no universal.

### 6.

Los embeddings son fundamentales para búsqueda semántica y RAG.

### 7.

Los embeddings pueden representar palabras, frases, documentos, código, imágenes y otros objetos.

### 8.

Las representaciones contextuales dependen del contexto procesado por el modelo.

### 9.

Un embedding similar no garantiza que dos elementos sean idénticos o verdaderos.

### 10.

Un sistema RAG puede fallar en retrieval aunque el prompt del LLM sea excelente.

---

# 120. La conexión definitiva

Hasta ahora hemos construido esta cadena:

```text
                     DATOS
                       │
                       ▼
                 ENTRENAMIENTO
                       │
                       ▼
                  PARÁMETROS
                       │
                       ▼
                    MODELO
                       │
                       ▼
                     TEXTO
                       │
                       ▼
                  TOKENIZACIÓN
                       │
                       ▼
                  TOKEN IDs
                       │
                       ▼
                  EMBEDDINGS
                       │
                       ▼
              REPRESENTACIONES
                       │
                       ▼
                   CONTEXTO
                       │
                       ▼
                  ATENCIÓN
                       │
                       ▼
                  INFERENCIA
                       │
                       ▼
              PROBABILIDADES
                       │
                       ▼
                NUEVOS TOKENS
                       │
                       ▼
                    TEXTO
```

Y en un sistema RAG aparece una segunda ruta:

```text
                    DOCUMENTOS
                        │
                        ▼
                      CHUNKS
                        │
                        ▼
                    EMBEDDINGS
                        │
                        ▼
                 ÍNDICE VECTORIAL
                        ▲
                        │
                   QUERY EMBEDDING
                        ▲
                        │
                      QUERY
                        │
                        ▼
                    RETRIEVAL
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

Estas dos rutas pueden interactuar.

---

# 121. Conclusión

Un embedding no es simplemente:

> "una palabra convertida en números".

Es una representación matemática aprendida que permite que un sistema de IA trabaje con relaciones entre elementos en un espacio vectorial.

La transición fundamental es:

```text
Texto
 ↓
Token
 ↓
ID
 ↓
Embedding
 ↓
Representación
 ↓
Procesamiento
```

Y en sistemas de recuperación:

```text
Consulta
 ↓
Embedding
 ↓
Similitud
 ↓
Recuperación
 ↓
Contexto
 ↓
LLM
```

La idea más importante para un ingeniero de IA es esta:

> **Los embeddings permiten convertir relaciones semánticas y estructurales en representaciones sobre las que pueden realizarse operaciones matemáticas.**

Esto hace posible construir sistemas que no dependen exclusivamente de coincidencias exactas de palabras.

Pero también introduce nuevos problemas de ingeniería:

```text
Embeddings
    ↓
¿qué modelo?
    ↓
¿qué dimensionalidad?
    ↓
¿qué métrica?
    ↓
¿qué chunking?
    ↓
¿qué índice?
    ↓
¿qué filtros?
    ↓
¿qué evaluación?
    ↓
¿qué seguridad?
```

Por eso los embeddings son mucho más que un detalle interno de un LLM: son una pieza fundamental de la infraestructura moderna de búsqueda, recuperación y sistemas de IA.

---

# 122. Próximo concepto

Ya conocemos:

```text
03 — Modelo
04 — Entrenamiento e inferencia
05 — Datos
06 — Parámetros
07 — Tokens
08 — Embeddings
```

El siguiente paso natural es comprender **dónde viven y cómo interactúan todos esos tokens y representaciones durante una ejecución**.

Eso nos lleva a:

**09 — Contexto y ventana de contexto**

donde estudiaremos:

```text
Tokens
   ↓
Contexto
   ↓
Ventana de contexto
   ↓
System / User / Assistant / Tool
   ↓
Historial
   ↓
RAG
   ↓
KV Cache
   ↓
Context rot
   ↓
Pérdida de información
   ↓
Gestión profesional del contexto
```

Y aquí comenzará a aparecer con mucha más claridad una de las ideas centrales de Prompt Engineering:

> **No basta con decirle al modelo qué hacer; también debemos controlar qué información tiene disponible para hacerlo.**
