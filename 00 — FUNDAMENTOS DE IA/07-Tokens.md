# 07 — Tokens y tokenización

> **Pregunta central:** ¿Cómo convierte un modelo de IA el texto que escribimos en algo que una red neuronal puede procesar?

Cuando una persona escribe:

```text
Analiza esta factura y encuentra errores.
```

el modelo no recibe directamente las palabras como las entiende un ser humano.

Antes de procesarlas, el texto debe convertirse en una representación numérica.

Una simplificación del proceso es:

```text
Texto
  ↓
Tokenización
  ↓
Tokens
  ↓
IDs numéricos
  ↓
Embeddings
  ↓
Procesamiento del modelo
  ↓
Predicción
  ↓
Tokens generados
  ↓
Texto
```

Comprender esta cadena es fundamental para entender:

* contexto;
* límites de contexto;
* costo de inferencia;
* generación de texto;
* rendimiento;
* diseño de prompts;
* RAG;
* procesamiento multilingüe;
* modelos multimodales;
* eficiencia computacional.

---

# 1. ¿Qué es un token?

Un **token** es una unidad de texto que un modelo utiliza como entrada o salida durante el procesamiento.

Un token puede corresponder a:

* una palabra completa;
* una parte de una palabra;
* un carácter;
* un signo de puntuación;
* un espacio o una combinación relacionada con espacios;
* fragmentos de código;
* símbolos especiales.

No existe una regla universal según la cual:

```text
1 palabra = 1 token
```

De hecho, normalmente no funciona así.

---

# 2. La idea más sencilla

Imaginemos esta frase:

```text
Hola mundo
```

Un tokenizer podría dividirla conceptualmente como:

```text
["Hola", " mundo"]
```

y asignar IDs:

```text
["Hola", " mundo"]
       ↓
[15342, 8821]
```

Los números son únicamente identificadores dentro del vocabulario del tokenizer.

Por tanto:

```text
Texto
↓
Tokens
↓
IDs
```

El modelo trabaja con los IDs y las representaciones numéricas derivadas de ellos.

---

# 3. Un token no es necesariamente una palabra

Esta es probablemente la idea más importante del módulo.

Supongamos:

```text
extraordinariamente
```

Un tokenizer podría dividirla conceptualmente como:

```text
extra
ordin
ariamente
```

No significa que todos los tokenizers utilicen exactamente esa división.

Otro tokenizer podría producir una segmentación diferente.

Por eso debemos hablar de:

> **tokens**

y no simplemente de:

> **palabras**

---

# 4. ¿Por qué no utilizar directamente palabras?

Una primera idea podría ser crear un diccionario:

```text
casa
perro
gato
computadora
auditoría
Python
...
```

y asignar un número a cada palabra.

El problema aparece inmediatamente.

Existen:

* millones de palabras;
* variantes;
* errores ortográficos;
* nombres propios;
* palabras nuevas;
* términos técnicos;
* otros idiomas;
* código;
* URLs;
* números;
* combinaciones desconocidas.

Un vocabulario basado exclusivamente en palabras sería poco flexible.

---

# 5. El problema del vocabulario

Imaginemos que el vocabulario contiene:

```text
casa
casas
casita
casero
casería
```

Tendríamos que almacenar muchas variantes.

En cambio, un tokenizer basado en subpalabras puede reutilizar componentes.

Conceptualmente:

```text
casa
casas
casita
```

podrían compartir representaciones relacionadas con:

```text
cas
a
ita
s
```

La división real depende del algoritmo y del vocabulario.

La idea fundamental es:

> **Las subunidades permiten construir muchas palabras a partir de un vocabulario relativamente manejable.**

---

# 6. Tokenización

La **tokenización** es el proceso mediante el cual un texto se divide en unidades que el modelo puede procesar.

Podemos representarlo:

```text
"Los modelos aprenden"

          ↓

["Los", " modelos", " aprenden"]
```

Después:

```text
["Los", " modelos", " aprenden"]
              ↓
       IDs numéricos
```

Por ejemplo, conceptualmente:

```text
[128, 4921, 8712]
```

Los números anteriores son ilustrativos.

Cada tokenizer real utiliza su propio vocabulario e IDs.

---

# 7. El tokenizer es parte del sistema

Un error frecuente es pensar:

> "Todos los modelos tokenizan igual."

No.

Diferentes modelos pueden utilizar diferentes tokenizers y vocabularios.

Por eso:

```text
Texto
↓
Tokenizer A
↓
Tokens A
```

puede producir una secuencia diferente de:

```text
Texto
↓
Tokenizer B
↓
Tokens B
```

Incluso si ambos modelos son capaces de realizar tareas similares.

---

# 8. Tokenizer y modelo

Podemos visualizarlo como dos componentes relacionados:

```text
Texto
  ↓
Tokenizer
  ↓
IDs de tokens
  ↓
Modelo
  ↓
Tokens de salida
  ↓
Tokenizer / decodificación
  ↓
Texto
```

El tokenizer convierte texto en una representación discreta que el modelo puede utilizar.

---

# 9. ¿Qué significa que un token tenga un ID?

El vocabulario del tokenizer puede imaginarse como un diccionario:

```text
ID       Token
---------------------
0        <pad>
1        <unk>
2        hola
3        mundo
4         mundo
5        casa
...
```

Entonces:

```text
"hola mundo"
```

podría convertirse conceptualmente en:

```text
[2, 4]
```

Los IDs no contienen por sí mismos el significado de las palabras.

Son índices.

---

# 10. ID de token ≠ significado

Supongamos:

```text
perro → 1287
```

y:

```text
gato → 9214
```

No significa que:

```text
9214 > 1287
```

y por tanto "gato" sea matemáticamente mayor que "perro".

Los IDs son identificadores arbitrarios dentro del vocabulario.

El significado útil aparece posteriormente mediante representaciones aprendidas.

---

# 11. De token ID a embedding

Aquí aparece una conexión directa con el módulo anterior.

Después de obtener:

```text
token ID
```

el modelo necesita convertir ese identificador en una representación vectorial.

Conceptualmente:

```text
Token ID
   ↓
Embedding
   ↓
Vector
```

Por ejemplo:

```text
ID = 1257
```

podría apuntar a:

```text
[0.12, -0.84, 0.31, ..., 0.07]
```

Ese vector puede tener cientos o miles de dimensiones.

---

# 12. ¿Por qué necesitamos embeddings?

Una red neuronal no trabaja con la palabra:

```text
"perro"
```

como un objeto lingüístico humano.

Necesita valores numéricos.

Los embeddings permiten representar tokens mediante vectores.

Conceptualmente:

```text
"perro"
   ↓
ID
   ↓
[0.12, -0.43, 0.71, ...]
```

Después, el Transformer puede realizar operaciones matemáticas sobre esas representaciones.

---

# 13. Tokenización y embeddings son cosas diferentes

Es importante no confundirlos.

### Tokenización

Responde:

> ¿Cómo dividimos el texto?

```text
texto
↓
tokens
```

### Embedding

Responde:

> ¿Cómo representamos numéricamente esos tokens?

```text
token
↓
vector
```

La cadena es:

```text
Texto
↓
Tokenización
↓
Token IDs
↓
Embeddings
↓
Transformer
```

---

# 14. ¿Cómo se crean los tokens?

Existen diferentes algoritmos de tokenización.

Entre los enfoques históricamente importantes encontramos:

* WordPiece;
* BPE;
* Unigram;
* variantes de estos métodos;
* tokenización basada en caracteres;
* tokenización específica para diferentes modalidades.

En modelos modernos es frecuente encontrar métodos basados en **subpalabras**.

---

# 15. BPE

**BPE (Byte Pair Encoding)** es una familia de métodos de tokenización basada en la combinación progresiva de unidades frecuentes.

La idea simplificada:

```text
unidades pequeñas
       ↓
buscar combinaciones frecuentes
       ↓
fusionarlas
       ↓
repetir
       ↓
crear vocabulario
```

Por ejemplo, conceptualmente:

```text
c
a
s
a
```

podría terminar representándose mediante unidades mayores:

```text
ca
sa
```

La división real depende del corpus utilizado para construir el vocabulario.

---

# 16. WordPiece

WordPiece utiliza una estrategia relacionada con subunidades frecuentes.

Fue popularizado especialmente por arquitecturas como BERT y modelos derivados.

Conceptualmente:

```text
palabras frecuentes
→ tokens completos

palabras menos frecuentes
→ subunidades
```

Esto permite manejar palabras que no aparecen exactamente en el vocabulario.

---

# 17. Unigram

Los modelos de tokenización Unigram consideran diferentes segmentaciones posibles y seleccionan aquellas que resultan más adecuadas según el modelo estadístico utilizado.

La idea general es:

```text
Texto
 ↓
posibles segmentaciones
 ↓
evaluación
 ↓
segmentación seleccionada
```

Esto demuestra que la tokenización no es simplemente "separar por espacios".

---

# 18. Byte-level tokenization

Algunos sistemas utilizan representaciones basadas en bytes.

Esto permite manejar de forma robusta:

* caracteres desconocidos;
* símbolos;
* idiomas;
* código;
* emojis;
* textos extraños;
* combinaciones no presentes literalmente en el vocabulario.

La idea general es partir de unidades muy básicas y permitir que el vocabulario represente combinaciones frecuentes.

---

# 19. ¿Por qué aparecen tokens extraños?

Podemos encontrar tokens como:

```text
"Ġhola"
"▁hola"
```

o representaciones similares.

No debemos asumir que esos símbolos forman parte literalmente del texto que escribió el usuario.

Pueden ser marcadores utilizados internamente por determinados tokenizers para representar información como:

```text
inicio de palabra
espacio
frontera de palabra
```

La representación depende del tokenizer.

---

# 20. Los espacios importan

Una fuente frecuente de confusión es:

```text
"Hola mundo"
```

frente a:

```text
"Hola  mundo"
```

Los tokenizers pueden tratar los espacios de forma diferente.

Por ejemplo, conceptualmente:

```text
"Hola mundo"
→ ["Hola", " mundo"]
```

mientras:

```text
"Hola  mundo"
→ ["Hola", "  mundo"]
```

La tokenización real depende del sistema.

Por eso los espacios no deben asumirse como irrelevantes.

---

# 21. Puntuación

La puntuación también puede convertirse en tokens o formar parte de tokens.

Por ejemplo:

```text
Hola, Jorge.
```

podría producir conceptualmente:

```text
["Hola", ",", " Jorge", "."]
```

o una segmentación diferente.

Esto depende del tokenizer.

---

# 22. Números

Los números presentan un comportamiento especialmente interesante.

Por ejemplo:

```text
2026
```

puede representarse como:

```text
["2026"]
```

o dividirse en varias unidades:

```text
["20", "26"]
```

o incluso mediante otra segmentación.

No debemos asumir:

```text
1 número = 1 token
```

---

# 23. Fechas

Una fecha como:

```text
29/09/2026
```

puede dividirse de diferentes maneras:

```text
["29", "/", "09", "/", "2026"]
```

o mediante otra combinación de tokens.

Esto tiene importancia práctica porque grandes cantidades de datos estructurados pueden consumir más tokens de lo que intuitivamente imaginamos.

---

# 24. Código

Los tokens no son exclusivos del lenguaje natural.

Un fragmento:

```python
def suma(a, b):
    return a + b
```

también debe tokenizarse.

Puede dividirse en unidades relacionadas con:

```text
def
suma
(
a
,
b
)
:
return
a
+
b
```

pero el tokenizer puede producir una segmentación diferente.

Los modelos especializados en código suelen utilizar tokenizadores capaces de representar eficientemente estructuras de programación.

---

# 25. URLs

Una URL:

```text
https://ejemplo.com/api/clientes?id=12345
```

puede consumir muchos tokens porque contiene:

* protocolo;
* dominio;
* rutas;
* símbolos;
* parámetros;
* números.

Por eso incluir grandes cantidades de URLs en un prompt puede consumir más contexto de lo esperado.

---

# 26. Emojis

Los emojis también presentan comportamientos interesantes.

Por ejemplo:

```text
😀
```

puede ocupar una o varias unidades dependiendo del tokenizer.

Además, algunos emojis complejos están formados por múltiples puntos de código Unicode.

Por ejemplo, determinadas secuencias:

```text
👨‍💻
```

pueden implicar varios componentes.

Por eso:

```text
1 símbolo visible
```

no necesariamente significa:

```text
1 token
```

---

# 27. Unicode

Unicode permite representar una enorme variedad de caracteres.

Esto incluye:

* español;
* chino;
* árabe;
* japonés;
* cirílico;
* emojis;
* símbolos matemáticos;
* caracteres técnicos.

Los tokenizers deben trabajar con representaciones Unicode o transformaciones derivadas de ellas.

---

# 28. ¿Todos los idiomas usan la misma cantidad de tokens?

No necesariamente.

Esta es una consideración muy importante para sistemas multilingües.

La misma información semántica puede requerir diferentes cantidades de tokens dependiendo de:

* idioma;
* tokenizer;
* vocabulario;
* frecuencia de las palabras;
* escritura;
* código;
* símbolos utilizados.

Por eso:

```text
100 palabras
```

no significa necesariamente:

```text
100 tokens
```

ni implica el mismo costo entre idiomas.

---

# 29. Español y tokenización

El español tiene palabras que pueden dividirse en varias unidades dependiendo del tokenizer.

Por ejemplo:

```text
internacionalización
```

podría dividirse conceptualmente como:

```text
internacional
ización
```

o mediante otra segmentación.

La existencia de morfología rica significa que las palabras pueden contener:

* raíces;
* prefijos;
* sufijos;
* terminaciones.

Un tokenizer subword puede aprovechar estas regularidades.

---

# 30. Inglés ≠ idioma universal del tokenizer

Un tokenizer diseñado con grandes cantidades de texto en inglés puede representar el inglés de manera más eficiente que determinados idiomas con menor representación en su vocabulario.

Pero no debemos convertir esto en una regla absoluta.

Los tokenizers modernos pueden diseñarse explícitamente para ser multilingües.

La eficiencia depende del tokenizer concreto y de sus datos de construcción.

---

# 31. ¿Qué es el vocabulario?

El **vocabulario** de un tokenizer es el conjunto de unidades que puede representar directamente.

Por ejemplo:

```text
V = {
    token₁,
    token₂,
    token₃,
    ...
}
```

Cada token tiene normalmente un ID.

Podemos representarlo:

```text
V = {v₀, v₁, ..., vₙ₋₁}
```

donde:

```text
n = tamaño del vocabulario
```

---

# 32. Tamaño del vocabulario

Un tokenizer puede tener decenas de miles, cientos de miles u otras cantidades de tokens dependiendo del diseño.

Un vocabulario demasiado pequeño puede producir:

```text
más fragmentación
↓
más tokens por texto
```

Un vocabulario demasiado grande puede aumentar:

```text
tamaño de determinadas matrices
```

y otros costos.

Por tanto, diseñar un tokenizer implica compromisos.

---

# 33. Tokenización como compresión

Podemos pensar parcialmente en la tokenización como una forma de compresión lingüística.

Un texto frecuente puede representarse mediante unidades eficientes.

Por ejemplo:

```text
palabra muy frecuente
↓
1 token
```

Mientras:

```text
palabra extremadamente rara
↓
varios tokens
```

La eficiencia depende de qué tan bien el vocabulario represente los patrones del texto.

Pero cuidado:

> La tokenización no es simplemente compresión de archivos.

Es una representación discreta diseñada para servir como interfaz entre texto y modelo.

---

# 34. Tokens y contexto

Aquí aparece una relación fundamental.

Los modelos tienen límites de contexto expresados frecuentemente en **tokens**.

Por ejemplo, un sistema puede admitir:

```text
128.000 tokens
```

o:

```text
1.000.000 tokens
```

dependiendo del modelo y del producto.

Por tanto:

```text
contexto
```

no debe entenderse simplemente como:

```text
cantidad de palabras
```

sino como una cantidad de unidades tokenizadas según el sistema.

---

# 35. 100.000 palabras ≠ 100.000 tokens

Supongamos:

```text
Documento = 100.000 palabras
```

No podemos concluir automáticamente:

```text
100.000 tokens
```

Podrían ser:

```text
120.000 tokens
```

o:

```text
150.000 tokens
```

o una cantidad diferente.

Depende del texto y del tokenizer.

Por eso los sistemas reales calculan el número de tokens utilizando el tokenizer correspondiente.

---

# 36. ¿Por qué importan tanto los tokens para Prompt Engineering?

Porque cada elemento que colocamos en un prompt puede consumir contexto.

Por ejemplo:

```text
Rol
+
Instrucciones
+
Ejemplos
+
Documentos
+
Historial
+
Salida esperada
```

todo puede ocupar tokens.

Por tanto:

```text
Prompt Engineering
```

también implica:

```text
gestión eficiente del contexto
```

---

# 37. Un prompt demasiado largo

Imaginemos:

```text
Instrucciones
      +
50 documentos
      +
historial completo
      +
ejemplos
      +
pregunta
```

Aunque todo parezca útil, existe un límite físico y computacional.

Si superamos el contexto disponible:

```text
Entrada demasiado grande
        ↓
problema de límite
```

Dependiendo del sistema, puede ocurrir:

* truncamiento;
* rechazo;
* pérdida de información;
* reducción del espacio disponible para la respuesta;
* procesamiento diferente mediante mecanismos específicos.

---

# 38. Input tokens y output tokens

Normalmente podemos distinguir:

### Input tokens

Tokens que entran al modelo:

```text
prompt
+
contexto
+
documentos
+
historial
```

### Output tokens

Tokens generados por el modelo:

```text
respuesta
```

Conceptualmente:

```text
INPUT
████████████████████
       +
OUTPUT
██████████
```

Ambos pueden afectar costo, latencia y utilización de recursos dependiendo del servicio.

---

# 39. El modelo genera tokens, no párrafos

Cuando un LLM produce:

```text
La inteligencia artificial es...
```

no genera necesariamente todo el párrafo de una sola vez.

La generación autoregresiva puede representarse:

```text
Contexto
   ↓
predice token 1
   ↓
predice token 2
   ↓
predice token 3
   ↓
...
```

Formalmente:

```text
P(x₁, x₂, ..., xₙ)
=
∏ᵢ P(xᵢ | x₁, ..., xᵢ₋₁)
```

Cada token se predice condicionado por los anteriores.

---

# 40. Ejemplo de generación

Supongamos que el modelo recibe:

```text
La capital de Ecuador es
```

El modelo puede producir una distribución de probabilidades para el siguiente token:

```text
Quito       0.92
Guayaquil   0.02
Cuenca      0.01
...
```

Se selecciona un token según la estrategia de decodificación.

Después:

```text
La capital de Ecuador es Quito
```

y el modelo predice el siguiente token.

El proceso continúa.

---

# 41. Tokenización y generación son procesos relacionados

Podemos representar todo el ciclo:

```text
                 ENTRADA
                    │
                    ▼
                Tokenizar
                    │
                    ▼
               Token IDs
                    │
                    ▼
                Embeddings
                    │
                    ▼
                Transformer
                    │
                    ▼
              Probabilidades
                    │
                    ▼
             Seleccionar token
                    │
                    ▼
             Añadir al contexto
                    │
                    └───────┐
                            │
                            ▼
                     siguiente token
```

Después, los tokens generados se convierten nuevamente en texto.

---

# 42. Autoregresividad

En un modelo autoregresivo:

```text
x₁
↓
x₂
↓
x₃
↓
x₄
```

cada nuevo token depende del contexto disponible.

Matemáticamente:

```text
P(x₁,...,xₙ)
=
P(x₁)
P(x₂|x₁)
P(x₃|x₁,x₂)
...
P(xₙ|x₁,...,xₙ₋₁)
```

Esta propiedad es fundamental para comprender por qué la generación de texto se realiza secuencialmente.

---

# 43. Tokens y atención

Los tokens se convierten en representaciones y luego participan en los mecanismos de atención.

Simplificando:

```text
Tokens
  ↓
Embeddings
  ↓
Representaciones
  ↓
Q, K, V
  ↓
Attention
```

La atención permite que determinadas posiciones interactúen con otras posiciones dentro del contexto según el mecanismo de la arquitectura.

Por eso:

> El token es una unidad de entrada; la atención opera sobre representaciones derivadas de esos tokens.

---

# 44. Tokens no son embeddings

Esta distinción debe memorizarse:

```text
TOKEN
=
unidad discreta
```

mientras:

```text
EMBEDDING
=
representación vectorial
```

Ejemplo:

```text
"auditoría"
     ↓
token
     ↓
ID = 58291
     ↓
embedding
     ↓
[0.18, -0.72, 0.31, ...]
```

El ID no es el embedding.

---

# 45. Tokens y posiciones

El modelo necesita saber no solamente qué tokens existen, sino también su posición o estructura relativa.

Por ejemplo:

```text
El perro mordió al hombre.
```

no es equivalente a:

```text
El hombre mordió al perro.
```

Los mismos elementos pueden aparecer, pero su organización cambia.

Los Transformers utilizan mecanismos de información posicional, que pueden implementarse de diferentes maneras.

Esto se estudiará con más detalle en el módulo de arquitectura.

---

# 46. Posición y contexto

Podemos imaginar:

```text
Token 1
Token 2
Token 3
Token 4
...
Token N
```

Cada token ocupa una posición dentro del contexto.

El modelo necesita algún mecanismo para representar la estructura posicional.

Sin información posicional adecuada, determinadas diferencias sintácticas serían mucho más difíciles de representar.

---

# 47. Tokens especiales

Los sistemas de NLP pueden utilizar tokens especiales.

Por ejemplo:

```text
<BOS>
```

para indicar inicio de secuencia,

```text
<EOS>
```

para indicar final de secuencia,

o tokens específicos para:

* padding;
* separación;
* instrucciones;
* roles;
* multimodalidad;
* control.

Los nombres exactos dependen del tokenizer y de la arquitectura.

---

# 48. Chat templates

Los modelos conversacionales pueden utilizar una plantilla interna para transformar una conversación en una secuencia que el modelo pueda procesar.

Por ejemplo, conceptualmente:

```text
system:
Eres un asistente...

user:
Explica qué es un token.

assistant:
```

puede convertirse internamente en una representación estructurada con tokens especiales.

Esto significa que:

> El texto visible de una conversación no siempre coincide exactamente con la secuencia de tokens que recibe el modelo.

Esta distinción es importante para ingeniería avanzada.

---

# 49. Roles y tokens especiales

Una conversación puede contener:

```text
system
user
assistant
tool
```

dependiendo del sistema.

El modelo puede recibir representaciones especiales que marcan estas partes.

Por eso, cuando diseñamos prompts para sistemas modernos, debemos entender que el prompt puede formar parte de una estructura conversacional más amplia.

---

# 50. Tokenización de mensajes

Conceptualmente:

```text
Mensaje del usuario
        ↓
plantilla de conversación
        ↓
secuencia estructurada
        ↓
tokenización
        ↓
IDs
```

Por eso contar únicamente los caracteres visibles puede dar una estimación incorrecta del consumo real.

---

# 51. Tokens y costo

En muchos servicios de IA, el uso se mide en tokens.

Conceptualmente:

```text
Costo
≈
tokens de entrada
+
tokens de salida
```

La fórmula exacta depende del proveedor.

Algunos servicios tienen precios diferentes para:

* input;
* output;
* caché;
* razonamiento;
* almacenamiento;
* herramientas.

Por eso debemos consultar siempre la documentación del proveedor cuando necesitemos un cálculo económico real.

---

# 52. Tokens y latencia

Generar más tokens normalmente significa realizar más trabajo de inferencia.

Por tanto:

```text
respuesta de 50 tokens
```

y:

```text
respuesta de 10.000 tokens
```

no representan la misma carga computacional.

La latencia también depende de:

* hardware;
* arquitectura;
* longitud del contexto;
* batch;
* caché;
* sistema de serving;
* paralelización;
* cantidad de tokens generados.

---

# 53. Prefill y decode

En sistemas de inferencia modernos es útil distinguir dos fases.

### Prefill

El modelo procesa los tokens de entrada.

```text
Prompt + contexto
       ↓
   procesamiento
```

### Decode

El modelo genera nuevos tokens secuencialmente.

```text
token 1
 ↓
token 2
 ↓
token 3
 ↓
...
```

Esta separación es muy importante en ingeniería de sistemas LLM.

---

# 54. KV cache

Durante la generación autoregresiva, los Transformers pueden utilizar una estructura llamada **KV cache**.

Conceptualmente:

```text
Tokens anteriores
      ↓
Key / Value
      ↓
KV Cache
      ↓
reutilización durante generación
```

Esto evita recalcular determinadas representaciones de tokens anteriores en cada paso.

La KV cache es una de las razones por las que el consumo de memoria puede crecer con la longitud del contexto.

---

# 55. Tokens y memoria

Tenemos entonces diferentes componentes:

```text
Pesos del modelo
+
Activaciones
+
KV cache
+
buffers
+
runtime
```

Por eso:

```text
memoria del modelo
```

no es simplemente:

```text
número de parámetros × bytes
```

La longitud del contexto también puede afectar significativamente la memoria durante inferencia.

---

# 56. Contexto largo

Supongamos:

```text
Modelo
+
10.000 tokens
```

frente a:

```text
Modelo
+
100.000 tokens
```

La segunda situación puede requerir considerablemente más recursos.

El comportamiento exacto depende de la arquitectura y de las optimizaciones utilizadas.

Por eso ampliar la ventana de contexto no significa que el costo computacional sea irrelevante.

---

# 57. Tokenización y RAG

En un sistema RAG:

```text
Documentos
   ↓
Chunking
   ↓
Embeddings
   ↓
Recuperación
   ↓
Chunks relevantes
   ↓
Contexto
   ↓
LLM
```

Los documentos recuperados deben entrar finalmente en el contexto del modelo.

Por tanto:

```text
RAG
```

y:

```text
tokens
```

están estrechamente relacionados.

Si recuperamos demasiados fragmentos:

```text
más información
↓
más tokens
↓
mayor contexto
↓
mayor costo potencial
```

Pero recuperar demasiado también puede introducir ruido.

---

# 58. Chunking y tokens

Cuando dividimos documentos para RAG, podemos establecer límites relacionados con tokens.

Por ejemplo:

```text
Documento
↓
chunks de aproximadamente 500 tokens
```

y:

```text
overlap de 50 tokens
```

Esto es diferente de decir:

```text
500 palabras
```

porque las palabras y los tokens no son equivalentes.

---

# 59. Prompt demasiado económico

Reducir tokens no siempre significa mejorar el sistema.

Supongamos:

```text
Prompt A = 2.000 tokens
Prompt B = 500 tokens
```

Podríamos pensar:

```text
500 < 2.000
→ B es mejor
```

No necesariamente.

Si el prompt de 2.000 tokens contiene información crítica y el de 500 elimina restricciones importantes, el segundo puede producir peores resultados.

La optimización correcta es:

```text
mínimos tokens necesarios
+
máxima información útil
```

---

# 60. Prompt Engineering como optimización de información

Podemos pensar en una buena instrucción como:

```text
Información útil
───────────────
Tokens utilizados
```

No se trata simplemente de reducir tokens.

Se trata de maximizar la utilidad del contexto.

Por ejemplo:

```text
NO:
Escribe algo bueno sobre esto.

SÍ:
Analiza los datos proporcionados, identifica anomalías,
clasifícalas por riesgo y devuelve exactamente este esquema JSON.
```

La segunda instrucción puede utilizar más tokens, pero proporciona más información operacional.

---

# 61. Repetición innecesaria

Una fuente de desperdicio es repetir instrucciones:

```text
Debes responder en JSON.
Siempre debes responder en JSON.
Recuerda que debes responder en JSON.
No olvides responder en JSON.
```

Puede reemplazarse por:

```text
Devuelve exclusivamente JSON válido.
```

La reducción debe hacerse sin perder claridad.

---

# 62. Tokens y precisión

La tokenización también puede afectar cómo se representan:

* números;
* código;
* nombres;
* términos técnicos;
* idiomas;
* errores ortográficos.

Por ejemplo, una palabra rara puede dividirse en más tokens.

Eso puede hacer que el modelo procese esa palabra como una combinación de subunidades.

---

# 63. Errores ortográficos

Supongamos:

```text
auditoría
```

frente a:

```text
auditoria
```

o:

```text
audtoria
```

El tokenizer puede producir diferentes secuencias.

Los modelos modernos son bastante robustos frente a errores, pero la representación interna cambia.

Esto ayuda a explicar por qué una pequeña modificación textual puede producir comportamientos distintos.

---

# 64. Tokens y ataques

Desde una perspectiva de seguridad, los tokens también son importantes.

Los sistemas de IA pueden recibir:

* entradas extremadamente largas;
* secuencias repetitivas;
* contenido diseñado para aumentar el procesamiento;
* prompts adversariales;
* documentos maliciosos.

Una entrada puede parecer pequeña visualmente pero producir una cantidad considerable de tokens.

Por eso:

> La superficie de ataque de un sistema LLM debe analizar también la representación tokenizada y el costo computacional.

---

# 65. Token flooding

Un atacante puede intentar proporcionar una cantidad excesiva de contenido para:

```text
llenar el contexto
```

o:

```text
aumentar el costo computacional
```

o:

```text
desplazar instrucciones importantes
```

Esto puede relacionarse con:

* abuso de recursos;
* denegación de servicio;
* agotamiento de contexto;
* aumento de costos.

La defensa requiere controles de longitud, cuotas, validación y arquitectura adecuada.

---

# 66. Prompt injection y tokens

Un prompt injection no depende de que el atacante "modifique los parámetros".

El atacante intenta introducir instrucciones dentro del contenido que el modelo procesa.

Conceptualmente:

```text
Parámetros
     +
Instrucciones legítimas
     +
Contenido externo
     +
Instrucción maliciosa
     ↓
Inferencia
```

Todo termina formando parte de la entrada o del contexto.

Esto conecta directamente la tokenización con la seguridad de aplicaciones LLM.

---

# 67. Tokens y modelos multimodales

En sistemas multimodales, el concepto de "token" puede ampliarse.

No necesariamente todo token corresponde a texto.

Un modelo puede procesar representaciones derivadas de:

* imágenes;
* audio;
* vídeo;
* texto.

Dependiendo de la arquitectura, estas modalidades pueden convertirse en unidades discretas o representaciones que posteriormente interactúan con el modelo.

Por eso:

> **Token no debe entenderse exclusivamente como una palabra escrita.**

---

# 68. Tokens visuales

Una imagen puede dividirse conceptualmente en regiones o unidades que posteriormente se convierten en representaciones procesables.

Por ejemplo:

```text
Imagen
 ↓
patches / características visuales
 ↓
representaciones
 ↓
modelo multimodal
```

La implementación exacta depende de la arquitectura.

Por eso el término "token" puede utilizarse de forma más general para referirse a unidades procesables por el modelo.

---

# 69. Audio y tokens

En sistemas de audio también pueden utilizarse representaciones discretas o unidades equivalentes a tokens.

Por ejemplo:

```text
Audio
 ↓
representación acústica
 ↓
unidades discretas / embeddings
 ↓
modelo
```

Nuevamente, no debemos asumir que todos los sistemas utilizan exactamente el mismo mecanismo.

---

# 70. Tokenización no significa comprensión

Este punto es importante.

Cuando decimos:

```text
"El modelo tokeniza la frase."
```

no significa:

```text
"El modelo comprendió la frase."
```

La tokenización es principalmente una transformación de representación.

```text
Texto
↓
unidades
↓
IDs
```

La representación semántica se desarrolla posteriormente mediante embeddings y procesamiento de la arquitectura.

---

# 71. Tokenización no es inteligencia

Un tokenizer puede convertir:

```text
"El gato está sobre la mesa."
```

en IDs perfectamente válidos sin "saber" qué significa la oración.

La inteligencia o capacidad del sistema surge de las etapas posteriores:

```text
Tokens
↓
Embeddings
↓
Arquitectura
↓
Parámetros aprendidos
↓
Inferencia
```

---

# 72. Tokenización y parámetros

Existe una conexión interesante con el módulo anterior.

Si el vocabulario contiene `V` tokens y cada token se representa mediante un embedding de dimensión `d`, una matriz de embeddings puede tener aproximadamente:

```text
V × d
```

parámetros.

Por ejemplo:

```text
V = 100.000
d = 4.096
```

produce:

```text
100.000 × 4.096
=
409.600.000
```

valores.

Esto muestra cómo una decisión aparentemente relacionada con texto —el tamaño del vocabulario— puede afectar la cantidad de parámetros.

---

# 73. Tokenizer y embedding están acoplados al modelo

En muchos sistemas:

```text
Tokenizer
     ↓
IDs
     ↓
Embedding
```

La correspondencia entre IDs y filas de la matriz de embeddings debe ser consistente.

Si utilizamos un tokenizer incompatible con el modelo:

```text
Tokenizer incorrecto
       ↓
IDs incorrectos
       ↓
representaciones incorrectas
       ↓
resultados incorrectos
```

Por eso el tokenizer no debe considerarse un componente independiente intercambiable sin más.

---

# 74. Cambiar el tokenizer

Cambiar el tokenizer puede afectar:

* longitud de secuencias;
* eficiencia;
* vocabulario;
* embeddings;
* entrenamiento;
* contexto efectivo;
* representación de idiomas;
* código;
* números.

Por eso un tokenizer es una decisión arquitectónica importante.

---

# 75. Tokens y ventana de contexto

La ventana de contexto puede representarse:

```text
┌──────────────────────────────────────────┐
│              CONTEXTO                    │
│                                          │
│ token₁ token₂ token₃ ... tokenₙ          │
│                                          │
└──────────────────────────────────────────┘
```

Si el límite es:

```text
N tokens
```

todo lo que el sistema necesite procesar debe caber dentro de las restricciones correspondientes.

La gestión de contexto será estudiada en profundidad en el siguiente módulo.

---

# 76. Contexto efectivo

Debemos diferenciar:

```text
ventana máxima de contexto
```

de:

```text
contexto útil
```

Un modelo puede admitir una gran cantidad de tokens, pero eso no significa que todas las posiciones tengan exactamente la misma utilidad práctica para todas las tareas.

También pueden existir:

* límites de salida;
* límites del proveedor;
* restricciones de memoria;
* costos;
* degradación de rendimiento;
* mecanismos específicos de atención o recuperación.

---

# 77. Tokens y memoria de conversación

Una conversación larga puede acumular:

```text
mensaje 1
mensaje 2
mensaje 3
...
mensaje N
```

y todos esos mensajes pueden contribuir al contexto si el sistema decide mantenerlos.

Por eso una conversación aparentemente sencilla puede terminar consumiendo una gran cantidad de tokens.

---

# 78. Resumen visual

La cadena completa que debemos recordar es:

```text
                    TEXTO
                      │
                      ▼
                TOKENIZADOR
                      │
                      ▼
                   TOKENS
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
                 TRANSFORMER
                      │
                      ▼
               DISTRIBUCIÓN P
                      │
                      ▼
              TOKEN SELECCIONADO
                      │
                      ▼
                NUEVO CONTEXTO
                      │
                      └──────────► siguiente token
```

---

# 79. Modelo mental definitivo

Cuando escribas:

```text
"Analiza este informe financiero."
```

imagina:

```text
"Analiza este informe financiero."
                ↓
            TOKENIZACIÓN
                ↓
     [token₁, token₂, token₃, ...]
                ↓
             IDs
                ↓
          EMBEDDINGS
                ↓
        TRANSFORMER
                ↓
       ATENCIÓN + CAPAS
                ↓
          PROBABILIDADES
                ↓
        TOKEN GENERADO
                ↓
        TOKEN GENERADO
                ↓
              ...
                ↓
             TEXTO
```

Esto es mucho más cercano a lo que realmente ocurre que imaginar:

```text
Pregunta
↓
"la IA piensa"
↓
Respuesta
```

---

# 80. Nivel avanzado: formalización

Sea una secuencia de tokens:

```text
x = (x₁, x₂, ..., xₙ)
```

donde cada:

```text
xᵢ ∈ V
```

y `V` representa el vocabulario.

El tokenizer transforma una secuencia textual `s` en:

```text
T(s) = (x₁, x₂, ..., xₙ)
```

Posteriormente, una función de embedding puede transformar cada token:

```text
E : V → ℝᵈ
```

de modo que:

```text
xᵢ → eᵢ ∈ ℝᵈ
```

La secuencia se convierte entonces en:

```text
(e₁, e₂, ..., eₙ)
```

que puede ser procesada por la arquitectura del modelo.

---

# 81. Generación autoregresiva

Para generación de texto:

```text
P(x₁,...,xₙ)
=
∏ᵢ₌₁ⁿ P(xᵢ | x₁,...,xᵢ₋₁)
```

Esto significa que la probabilidad de una secuencia puede factorizarse como probabilidades condicionales de tokens sucesivos.

En inferencia, el modelo calcula una distribución sobre el próximo token:

```text
P(xₙ₊₁ | x₁,...,xₙ)
```

y el sistema de decodificación selecciona o muestrea el siguiente token.

Después se repite el proceso.

---

# 82. Tokens y longitud de secuencia

Sea:

```text
n = número de tokens
```

Entonces la secuencia procesada por el modelo tiene longitud `n`.

En arquitecturas Transformer, determinadas operaciones de atención pueden tener costos que dependen fuertemente de `n`, aunque las implementaciones modernas utilizan numerosas optimizaciones y variantes para reducir costos.

La idea importante para ingeniería es:

```text
más tokens
↓
más información que procesar
↓
mayores requisitos computacionales potenciales
```

---

# 83. Tokens y atención cuadrática

En la formulación clásica de atención completa, la interacción entre tokens puede representarse mediante una matriz:

```text
n × n
```

donde `n` es la longitud de la secuencia.

Esto produce una dependencia cuadrática en determinadas partes del cálculo:

```text
O(n²)
```

Por ejemplo:

```text
1.000 tokens
→ ~1.000.000 relaciones

10.000 tokens
→ ~100.000.000 relaciones
```

Esta comparación es conceptual y no representa directamente el costo total de un sistema real.

Las arquitecturas e implementaciones modernas pueden utilizar optimizaciones, atención local, atención dispersa, kernels eficientes u otros mecanismos.

---

# 84. ¿Por qué esto importa para ingeniería de IA?

Porque una decisión aparentemente sencilla:

```text
"Voy a enviar todo el documento al modelo."
```

puede tener consecuencias sobre:

```text
Tokens
↓
Contexto
↓
Memoria
↓
Computación
↓
Latencia
↓
Costo
```

Por eso un ingeniero de IA no debe pensar únicamente en:

> "¿Cabe el documento?"

También debe pensar:

> "¿Es eficiente procesarlo completo?"

---

# 85. Tokens y compresión de prompts

Una técnica de optimización puede consistir en reducir contenido redundante.

Por ejemplo:

```text
Texto original
= 8.000 tokens
```

Después de eliminar:

* repeticiones;
* instrucciones duplicadas;
* metadatos innecesarios;
* contenido irrelevante;

podemos obtener:

```text
Versión optimizada
= 3.000 tokens
```

Pero debemos verificar que la información importante se conserve.

La optimización correcta es:

```text
menos redundancia
+
misma información útil
```

no simplemente:

```text
menos texto
```

---

# 86. Tokens y agentes

En sistemas de agentes, el consumo de tokens puede multiplicarse porque el sistema puede realizar múltiples pasos:

```text
Usuario
 ↓
LLM
 ↓
herramienta
 ↓
resultado
 ↓
LLM
 ↓
herramienta
 ↓
resultado
 ↓
LLM
 ↓
respuesta
```

Cada paso puede incorporar nuevo contexto.

Por tanto:

```text
1 pregunta
```

no necesariamente significa:

```text
1 llamada pequeña
```

Un agente puede generar múltiples ciclos de inferencia.

Esto será importante cuando estudiemos arquitecturas de agentes y automatización.

---

# 87. Tokens y herramientas

Una llamada a una herramienta puede devolver:

```text
100 tokens
```

o:

```text
100.000 tokens
```

dependiendo del resultado.

Si ese resultado vuelve a introducirse en el contexto:

```text
herramienta
↓
resultado grande
↓
contexto
↓
LLM
```

el costo y la carga computacional pueden aumentar.

Por eso los agentes profesionales deben controlar:

* longitud;
* filtrado;
* resumen;
* truncamiento;
* relevancia;
* estructura de resultados.

---

# 88. Tokens y JSON

Un JSON puede ser eficiente estructuralmente, pero no necesariamente pequeño en tokens.

Por ejemplo:

```json
{
  "cuenta": "Inventarios",
  "riesgo": "CRITICO",
  "monto": 160852.12
}
```

contiene:

* nombres de campos;
* valores;
* signos;
* espacios;
* números.

Todos forman parte de la representación tokenizada.

Por eso un esquema JSON muy repetitivo puede consumir una cantidad significativa de tokens en grandes volúmenes.

---

# 89. Tokens y salida estructurada

Si pedimos:

```text
Devuelve 5.000 objetos JSON.
```

el modelo debe generar una gran cantidad de tokens.

Esto puede aumentar:

* latencia;
* costo;
* riesgo de errores;
* posibilidades de truncamiento.

En automatizaciones de IA, la eficiencia de la salida también debe diseñarse.

---

# 90. Tokens y precisión de instrucciones

Un prompt extremadamente corto:

```text
Analiza esto.
```

puede ser insuficiente.

Uno excesivamente largo:

```text
5.000 líneas de instrucciones repetidas
```

puede ser innecesariamente costoso y difícil de mantener.

El objetivo de una ingeniería profesional es:

```text
CLARIDAD
+
PRECISIÓN
+
CONTEXTO NECESARIO
+
ESTRUCTURA
−
REDUNDANCIA
```

---

# 91. Errores conceptuales que debemos evitar

## Error 1

> "Cada palabra es un token."

Incorrecto.

Una palabra puede corresponder a uno o varios tokens.

---

## Error 2

> "Cada token es una letra."

Incorrecto.

Un token puede ser una palabra completa, subpalabra, símbolo o combinación.

---

## Error 3

> "El ID del token representa su significado."

Incorrecto.

El ID es un índice dentro del vocabulario.

---

## Error 4

> "Token y embedding son lo mismo."

Incorrecto.

El token es una unidad discreta; el embedding es una representación vectorial.

---

## Error 5

> "Más tokens siempre significa mejor respuesta."

Incorrecto.

Más contexto puede aportar información o introducir ruido.

---

## Error 6

> "Más contexto siempre es mejor."

Incorrecto.

El contexto debe ser relevante y manejable.

---

## Error 7

> "Un prompt largo demuestra mejor ingeniería."

Incorrecto.

La calidad depende de la información útil y de cómo se estructura.

---

# 92. El principio de economía contextual

Una regla práctica:

> **Utiliza la cantidad de contexto necesaria para que el modelo pueda realizar correctamente la tarea, pero evita información irrelevante y redundante.**

Podemos expresarlo como:

```text
Contexto útil
──────────────
Contexto total
```

Queremos aumentar la proporción de información útil.

Esto será especialmente importante en:

* agentes;
* RAG;
* automatizaciones;
* sistemas empresariales;
* análisis documental;
* programación con LLM.

---

# 93. Checklist profesional de tokens

Antes de diseñar un sistema basado en LLM, conviene preguntar:

### Sobre entrada

* ¿Cuántos tokens produce normalmente el usuario?
* ¿Cuánto contexto agregamos?
* ¿Cuántos documentos recuperamos?
* ¿Cuánto historial conservamos?

### Sobre salida

* ¿Cuántos tokens necesita realmente la respuesta?
* ¿Podemos limitar la longitud?
* ¿Necesitamos JSON?
* ¿Podemos eliminar campos redundantes?

### Sobre arquitectura

* ¿Cuál es la ventana de contexto?
* ¿Cómo se comporta el sistema con contextos largos?
* ¿Qué memoria requiere?
* ¿Cómo funciona la KV cache?

### Sobre costos

* ¿Cómo se cobra el input?
* ¿Cómo se cobra el output?
* ¿Existen precios diferentes para caché o razonamiento?
* ¿Qué volumen tendrá producción?

---

# 94. Relación con los módulos anteriores

Ahora podemos conectar lo aprendido:

```text
03 — MODELO
¿Qué es un modelo?
        ↓
04 — ENTRENAMIENTO E INFERENCIA
¿Cómo aprende y cómo ejecuta?
        ↓
05 — DATOS
¿Con qué información aprende?
        ↓
06 — PARÁMETROS
¿Qué valores quedan aprendidos?
        ↓
07 — TOKENS
¿Cómo representamos el texto para procesarlo?
```

La siguiente pregunta natural es:

```text
¿Dónde caben esos tokens?
¿qué información puede ver simultáneamente el modelo?
¿qué ocurre cuando la conversación crece?
```

Eso nos lleva directamente al siguiente concepto:

**08 — Contexto y ventana de contexto.**

---

# 95. Mapa final

Debemos terminar este módulo con esta imagen mental:

```text
                    USUARIO
                       │
                       ▼
                  TEXTO HUMANO
                       │
                       ▼
                 TOKENIZADOR
                       │
                       ▼
                TOKENS / IDs
                       │
                       ▼
                  EMBEDDINGS
                       │
                       ▼
              REPRESENTACIONES
                       │
                       ▼
                  TRANSFORMER
                       │
             ┌─────────┴─────────┐
             │                   │
         PARÁMETROS           CONTEXTO
             │                   │
             └─────────┬─────────┘
                       ▼
                   INFERENCIA
                       │
                       ▼
                PROBABILIDADES
                       │
                       ▼
                 NUEVO TOKEN
                       │
                       ▼
               REPETIR PROCESO
                       │
                       ▼
                   TEXTO FINAL
```

La idea fundamental es:

> **El modelo no recibe directamente "palabras". Recibe una secuencia tokenizada que posteriormente se transforma en representaciones numéricas que la arquitectura puede procesar.**

Y para Prompt Engineering, la consecuencia es fundamental:

> **Diseñar un prompt también significa diseñar la información que terminará convirtiéndose en tokens y ocupará una determinada cantidad del contexto disponible.**

Por eso comprender tokens no es un detalle técnico secundario.

Es una pieza fundamental para comprender cómo funcionan los LLM, cómo optimizar prompts, cómo controlar costos y cómo diseñar sistemas de IA eficientes.
