# 02 — Tokenización

> **Nivel:** Fundamentos técnicos de LLM
> **Ruta:** Ingeniería de Prompt — De Cero a Sistemas de IA
> **Prerrequisito:** `01-Que-es-un-LLM.md`
> **Nivel académico:** Inicial → Maestría / PhD
> **Actualizado:** septiembre de 2026

---

# 1. Objetivo

Al finalizar este capítulo, el estudiante podrá:

* Explicar qué es un token.
* Explicar qué es la tokenización.
* Comprender por qué un token no equivale necesariamente a una palabra.
* Diferenciar caracteres, palabras, subpalabras y tokens.
* Comprender de manera conceptual cómo funciona un tokenizer.
* Entender por qué diferentes modelos pueden tokenizar el mismo texto de manera diferente.
* Comprender la relación entre tokens y ventana de contexto.
* Comprender cómo los tokens afectan costos y latencia.
* Explicar por qué los idiomas no consumen necesariamente la misma cantidad de tokens.
* Comprender qué ocurre con código, números, emojis y caracteres especiales.
* Entender la relación entre tokenización y embeddings.
* Comprender por qué la tokenización tiene consecuencias prácticas para Ingeniería de Prompt.
* Analizar problemas de tokenización a un nivel técnico más avanzado.

---

# 2. ¿Qué es un token?

Un **token** es una unidad de información textual que un modelo de lenguaje utiliza como entrada o salida.

Una forma sencilla de entenderlo es:

```text
Texto humano
     ↓
Tokenización
     ↓
Tokens
     ↓
Modelo
```

Pero existe una precisión fundamental:

> **Un token no es necesariamente una palabra.**

Dependiendo del tokenizer y del modelo, un token puede representar:

* una palabra completa;
* parte de una palabra;
* varios caracteres;
* un signo de puntuación;
* un espacio combinado con texto;
* un número o parte de un número;
* un fragmento de código;
* un emoji;
* caracteres especiales.

Por eso no debemos utilizar indistintamente:

```text
palabra = token
```

La relación correcta es:

```text
palabras ⟶ pueden dividirse en uno o varios tokens
```

---

# 3. ¿Por qué existen los tokens?

Los modelos de lenguaje trabajan con representaciones numéricas.

Un modelo neuronal no recibe directamente:

```text
Hola Jorge
```

como lo percibe una persona.

Necesita transformar esa información en una representación que pueda procesar matemáticamente.

Una simplificación conceptual es:

```text
Texto
  ↓
Tokenización
  ↓
Identificadores de tokens
  ↓
Representaciones numéricas
  ↓
Modelo
```

Por ejemplo:

```text
"Hola"
```

podría convertirse conceptualmente en:

```text
[15342]
```

y:

```text
"Hola mundo"
```

podría convertirse en algo como:

```text
[15342, 1917]
```

Los números anteriores son solamente ilustrativos.

> **No deben interpretarse como los identificadores reales de ningún modelo específico.**

Cada tokenizer posee su propio vocabulario e identificadores.

---

# 4. Una analogía sencilla

Imaginemos una biblioteca.

Una persona puede mirar:

```text
La inteligencia artificial está transformando la industria.
```

como una oración completa.

Pero el sistema necesita dividirla en unidades manejables.

Podemos imaginar:

```text
La | inteligencia | artificial | está | transformando | la | industria
```

Sin embargo, un tokenizer moderno puede dividirla de otra manera:

```text
La
inteligencia
artific
ial
está
transform
ando
la
industria
```

La división exacta dependerá del tokenizer.

La idea importante es:

> **El tokenizer determina cómo se descompone el texto antes de entrar al modelo.**

---

# 5. Tokenización no es simplemente "separar palabras"

Un error muy común es imaginar que tokenizar significa:

```python
texto.split(" ")
```

Por ejemplo:

```python
texto = "Hola mundo"
tokens = texto.split(" ")
```

El resultado sería:

```text
["Hola", "mundo"]
```

Eso puede servir para ciertos ejercicios de programación, pero **no representa la tokenización utilizada por los LLM modernos**.

Un tokenizer real debe considerar muchas posibilidades:

```text
Hola
Hola,
hola
HOLA
123
12.50
email@example.com
https://ejemplo.com
😊
#Python
print("Hola")
```

Cada uno puede dividirse de manera diferente.

---

# 6. Palabra, carácter, subpalabra y token

Para comprender la tokenización debemos distinguir varias unidades.

## 6.1 Carácter

Un carácter puede ser:

```text
A
b
ñ
7
?
.
😊
```

---

## 6.2 Palabra

Por ejemplo:

```text
inteligencia
```

---

## 6.3 Subpalabra

Una palabra puede dividirse en fragmentos:

```text
inteligencia
```

podría dividirse conceptualmente como:

```text
inteligen
cia
```

o:

```text
intelig
encia
```

No existe una división universal.

---

## 6.4 Token

Un token es la unidad definida por el tokenizer del modelo.

Puede coincidir con:

```text
una palabra
```

pero también con:

```text
una subpalabra
```

o:

```text
un fragmento
```

Por eso:

```text
carácter ≠ palabra ≠ token
```

---

# 7. ¿Cómo se construye un tokenizer?

Antes de utilizar un tokenizer, debe existir un vocabulario.

Conceptualmente:

```text
Corpus de entrenamiento
        ↓
Análisis de frecuencias
        ↓
Construcción del vocabulario
        ↓
Algoritmo de tokenización
        ↓
Tokenizer
```

El vocabulario contiene unidades que el sistema puede reconocer.

Podemos imaginar un vocabulario simplificado:

```text
["a", "la", "de", "el", "que", "ción", "ing", "##mente", ...]
```

El vocabulario real de un modelo puede contener decenas de miles o más unidades, dependiendo de su diseño.

---

# 8. Tokenización basada en palabras

Los primeros enfoques podían utilizar palabras completas.

Por ejemplo:

```text
El gato duerme
```

podría convertirse en:

```text
["El", "gato", "duerme"]
```

Ventaja:

```text
Una palabra = una unidad
```

Pero aparece un problema.

¿Qué ocurre con una palabra que el tokenizer nunca ha visto?

Por ejemplo:

```text
hiperespecialización
```

Si no está en el vocabulario, el sistema necesita alguna estrategia.

Esto lleva al problema conocido como:

> **Out-of-Vocabulary (OOV)**

o:

> **Fuera del vocabulario.**

---

# 9. El problema del vocabulario

Supongamos que tenemos este vocabulario:

```text
casa
casas
perro
perros
correr
corriendo
```

Ahora aparece:

```text
perrito
```

Si el vocabulario solamente contiene palabras completas, puede no encontrar una representación directa.

Una solución sería incluir miles o millones de palabras.

Pero eso genera problemas:

* vocabularios enormes;
* mayor consumo de memoria;
* dificultad para manejar palabras nuevas;
* problemas con idiomas morfológicamente complejos;
* poca flexibilidad.

Los métodos modernos utilizan estrategias de **subpalabras** para reducir este problema.

---

# 10. Tokenización por subpalabras

Una estrategia consiste en dividir las palabras en unidades menores.

Por ejemplo:

```text
desafortunadamente
```

podría dividirse conceptualmente como:

```text
des
afortun
adamente
```

La división real depende del tokenizer.

La ventaja es importante.

Si el modelo conoce:

```text
des
afortun
adamente
```

puede construir representaciones para palabras que no necesariamente aparecieron exactamente de la misma manera durante el entrenamiento.

Esto permite manejar un vocabulario mucho más flexible.

---

# 11. Principales familias de algoritmos

A nivel histórico y técnico, existen diferentes enfoques de tokenización.

Entre los más importantes están:

* **BPE — Byte Pair Encoding**
* **WordPiece**
* **Unigram Language Model**
* variantes basadas en bytes;
* esquemas híbridos.

No todos los modelos utilizan exactamente el mismo algoritmo.

---

# 12. BPE — Byte Pair Encoding

**BPE** significa:

> **Byte Pair Encoding**

Originalmente fue desarrollado como una técnica de compresión, pero posteriormente fue adaptado para tokenización.

La idea simplificada es:

1. comenzar con unidades pequeñas;
2. identificar pares frecuentes;
3. fusionarlos;
4. repetir el proceso;
5. construir unidades más grandes.

Imaginemos:

```text
a
b
c
d
```

Si:

```text
a + b
```

aparece frecuentemente, podemos crear:

```text
ab
```

Después:

```text
ab + c
```

puede convertirse en:

```text
abc
```

El proceso genera unidades frecuentes.

---

# 13. Ejemplo conceptual de BPE

Supongamos un corpus:

```text
casa
casas
casado
casamiento
```

Podríamos encontrar frecuencias como:

```text
ca
as
sa
ado
...
```

El algoritmo puede aprender combinaciones frecuentes.

Conceptualmente:

```text
c + a
 ↓
ca

ca + s
 ↓
cas

cas + a
 ↓
casa
```

El resultado es un vocabulario de unidades reutilizables.

> Este ejemplo es didáctico. Un tokenizer real utiliza reglas y procedimientos más sofisticados.

---

# 14. WordPiece

**WordPiece** es otro método de tokenización por subpalabras.

Fue utilizado en modelos de lenguaje importantes y popularizado especialmente por arquitecturas como BERT.

Conceptualmente:

```text
palabra
  ↓
subunidades
  ↓
tokens
```

Una palabra desconocida puede dividirse en piezas conocidas.

Por ejemplo, de manera ilustrativa:

```text
jugadores
```

podría dividirse como:

```text
juga + dores
```

La segmentación exacta depende del vocabulario.

---

# 15. Unigram Language Model

Otro enfoque es el denominado:

> **Unigram Language Model**

En lugar de construir las unidades exactamente mediante el mismo proceso de fusiones de BPE, este enfoque parte de un conjunto de candidatos y selecciona una segmentación basada en probabilidades.

Conceptualmente:

```text
Palabra
   ↓
Diferentes segmentaciones posibles
   ↓
Evaluación probabilística
   ↓
Segmentación seleccionada
```

Este tipo de enfoque está asociado con tokenizadores como **SentencePiece** en determinadas configuraciones.

---

# 16. SentencePiece

SentencePiece es una biblioteca y enfoque de tokenización que permite trabajar con texto sin depender obligatoriamente de separar previamente las palabras por espacios.

Esto es importante para idiomas donde los espacios no funcionan de la misma manera que en español o inglés.

Conceptualmente:

```text
Texto
 ↓
SentencePiece
 ↓
Subunidades
```

SentencePiece puede implementar diferentes modelos de segmentación, incluyendo variantes basadas en BPE y Unigram.

Por eso:

> **SentencePiece no debe confundirse con un único algoritmo de tokenización.**

Es una herramienta que puede implementar diferentes estrategias.

---

# 17. Tokenización basada en bytes

Algunos tokenizadores modernos utilizan unidades relacionadas con bytes.

Esto permite manejar de forma robusta una gran variedad de caracteres.

Por ejemplo:

```text
ASCII
Unicode
acentos
símbolos
emoji
idiomas diferentes
```

La ventaja es que se reduce el problema de caracteres completamente desconocidos.

Conceptualmente:

```text
Texto Unicode
      ↓
Bytes
      ↓
Unidades del tokenizer
      ↓
Tokens
```

Esto es especialmente importante para sistemas multilingües.

---

# 18. Unicode

Los estudiantes que programan deben conocer un concepto fundamental:

> **Los caracteres no son lo mismo que los bytes.**

Por ejemplo:

```text
A
```

es un carácter.

Pero:

```text
á
```

puede ocupar múltiples bytes dependiendo de la codificación utilizada, como UTF-8.

Esto importa porque algunos tokenizadores trabajan a nivel de bytes o utilizan mecanismos relacionados con ellos.

---

# 19. ¿Por qué "ñ" es importante?

Consideremos:

```text
año
```

La letra:

```text
ñ
```

forma parte del español.

Un sistema que solamente comprendiera ASCII tendría dificultades para representarla directamente.

Con Unicode y UTF-8 podemos representar una enorme variedad de caracteres.

Esto permite trabajar con:

```text
español
中文
العربية
русский
日本語
한국어
```

y muchos otros sistemas de escritura.

---

# 20. Los espacios también pueden importar

En algunos tokenizadores, el espacio puede formar parte de la unidad tokenizada.

Por ejemplo, conceptualmente:

```text
"Hola mundo"
```

podría representarse mediante unidades equivalentes a:

```text
"Hola"
" mundo"
```

en lugar de:

```text
"Hola"
"mundo"
```

Esto explica por qué los tokens que vemos en determinadas herramientas pueden parecer extraños.

No debemos asumir que:

```text
token = palabra visible
```

---

# 21. Puntuación

La puntuación también puede formar parte de los tokens.

Por ejemplo:

```text
Hola, Jorge.
```

contiene:

```text
Hola
,
Jorge
.
```

pero el tokenizer puede utilizar unidades diferentes.

Dependiendo del vocabulario, podría representar fragmentos como:

```text
"Hola"
","
" Jorge"
"."
```

La segmentación exacta dependerá del tokenizer.

---

# 22. Números

Los números son especialmente interesantes.

Consideremos:

```text
2026
```

No debemos asumir que:

```text
2026 = un token
```

Podría dividirse en varias unidades.

Por ejemplo, de forma ilustrativa:

```text
20
26
```

o:

```text
202
6
```

o:

```text
2
026
```

El resultado depende del tokenizer.

Lo mismo ocurre con:

```text
123456789
```

y:

```text
3.1415926535
```

Por ello, los LLM pueden presentar comportamientos particulares con:

* números largos;
* operaciones aritméticas;
* secuencias numéricas;
* identificadores;
* fechas;
* códigos.

---

# 23. Código fuente

La tokenización también se aplica al código.

Consideremos:

```python
def sumar(a, b):
    return a + b
```

El tokenizer debe representar elementos como:

```text
def
sumar
(
a
,
b
)
:
return
+
```

pero nuevamente:

> **La segmentación exacta depende del modelo.**

Un modelo entrenado intensivamente con código puede desarrollar un vocabulario especialmente eficiente para determinados patrones de programación.

Esto tiene consecuencias prácticas.

Un fragmento de código puede consumir una cantidad de tokens muy diferente de lo que una persona imaginaría contando palabras.

---

# 24. Emojis

Consideremos:

```text
😀
```

o:

```text
👨‍💻
```

Un emoji visualmente simple puede estar compuesto internamente por varios elementos Unicode.

Por tanto:

```text
1 símbolo visual
≠
necesariamente 1 token
```

Algunos emojis pueden requerir múltiples unidades.

Esto demuestra nuevamente que:

> **La apariencia visual de un texto no determina directamente su cantidad de tokens.**

---

# 25. ¿Cuántos tokens tiene una palabra?

No existe una respuesta universal.

Por ejemplo:

```text
casa
```

podría ser un único token en un determinado tokenizer.

Pero:

```text
electroencefalografista
```

podría dividirse en varios.

Y otra palabra podría estar representada de una manera diferente en otro modelo.

Por tanto:

```text
número de palabras
```

no permite calcular exactamente:

```text
número de tokens
```

sin utilizar el tokenizer correspondiente.

---

# 26. ¿Por qué diferentes modelos tokenizan diferente?

Porque cada modelo puede utilizar:

* un vocabulario diferente;
* un tokenizer diferente;
* un algoritmo diferente;
* un corpus diferente para construir el vocabulario;
* diferentes decisiones de diseño.

Por ejemplo:

```text
Texto:
"inteligencia artificial"
```

podría producir:

```text
Modelo A:
[token1, token2, token3]
```

mientras:

```text
Modelo B:
[token4, token5]
```

No significa que uno esté necesariamente equivocado.

Son representaciones diferentes.

---

# 27. El tokenizer forma parte del sistema del modelo

Una idea importante:

> **El tokenizer no es un detalle independiente del modelo.**

El modelo fue entrenado utilizando determinadas representaciones de entrada.

Por ello, utilizar un tokenizer incompatible con el modelo puede producir resultados incorrectos.

Conceptualmente:

```text
Tokenizer
    │
    ↓
IDs de tokens
    │
    ↓
Modelo entrenado para esos IDs
```

El vocabulario y la representación de tokens forman parte de la interfaz entre el texto y el modelo.

---

# 28. Token ID

Cada token del vocabulario suele tener asociado un identificador.

Conceptualmente:

```text
Token                ID
-------------------------
"hola"               153
"mundo"              981
"intelig"            4721
"."                  13
```

Los números anteriores son solamente ejemplos.

El proceso puede representarse:

```text
Texto
 ↓
Tokenizer
 ↓
Tokens
 ↓
Token IDs
 ↓
Modelo
```

Los IDs permiten representar las unidades textuales como entradas discretas para las siguientes etapas del modelo.

---

# 29. Token IDs no son embeddings

Esta distinción será fundamental para el próximo capítulo.

Un:

```text
Token ID
```

es un identificador discreto.

Un:

```text
Embedding
```

es una representación vectorial.

Conceptualmente:

```text
"hola"
  ↓
Token ID
  ↓
Embedding
  ↓
Vector numérico
```

No debemos confundir:

```text
token ID ≠ embedding
```

El próximo capítulo explicará cómo ocurre esa transición.

---

# 30. Del token al embedding

La cadena conceptual completa comienza a tomar forma:

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
  ↓
Predicción
```

Por ejemplo:

```text
"Hola mundo"
```

podría convertirse conceptualmente en:

```text
Texto
 ↓
["Hola", " mundo"]
 ↓
[15342, 8917]
 ↓
[v₁, v₂]
 ↓
Modelo
```

Los vectores \(v_1\) y \(v_2\) son representaciones numéricas.

La naturaleza y transformación exacta de esas representaciones será el tema de:

```text
03-Embeddings.md
```

---

# 31. Tokens de entrada y tokens de salida

En un sistema generativo debemos distinguir:

### Tokens de entrada

Son los tokens que el modelo recibe.

Incluyen elementos como:

```text
instrucciones
pregunta
contexto
documentos
historial
```

### Tokens de salida

Son los tokens generados por el modelo.

Por ejemplo:

```text
Entrada:
¿Qué es Python?

Salida:
Python es un lenguaje de programación...
```

Conceptualmente:

```text
TOKENS DE ENTRADA
        ↓
      MODELO
        ↓
TOKENS DE SALIDA
```

---

# 32. Tokens y ventana de contexto

Una de las consecuencias más importantes de la tokenización es su relación con la **ventana de contexto**.

Si un modelo admite una cantidad máxima determinada de tokens, esa capacidad se refiere a tokens, no directamente a palabras.

Por ejemplo, conceptualmente:

```text
Ventana disponible = 100 000 tokens
```

significa que el sistema debe gestionar su contexto dentro de ese límite según las reglas del modelo y de la aplicación.

No significa:

```text
100 000 palabras
```

---

# 33. ¿Qué ocupa la ventana de contexto?

Dependiendo del sistema, pueden consumir contexto:

```text
System prompt
+
instrucciones
+
historial
+
pregunta
+
documentos
+
resultados de herramientas
+
otros datos
```

Por tanto:

```text
Contexto
=
mucho más que el mensaje que escribe el usuario
```

Esto será fundamental en **Context Engineering**.

---

# 34. Ejemplo práctico

Supongamos que un sistema dispone de una ventana conceptual de:

```text
100 000 tokens
```

Y tenemos:

```text
System prompt        → 5 000
Historial            → 20 000
Documentos           → 60 000
Pregunta              → 2 000
Otros datos           → 10 000
```

El sistema estaría intentando procesar:

```text
5 000
+ 20 000
+ 60 000
+ 2 000
+ 10 000
----------------
97 000 tokens
```

Quedarían aproximadamente:

```text
3 000 tokens
```

para otros elementos, dependiendo de cómo el sistema gestione la generación y los límites específicos del modelo.

Este ejemplo es deliberadamente simplificado.

---

# 35. Tokens y costos

Los servicios comerciales de LLM suelen utilizar los tokens como una de las unidades para medir consumo.

Podemos encontrar:

```text
tokens de entrada
```

y:

```text
tokens de salida
```

Dependiendo del proveedor, pueden existir precios diferentes.

Conceptualmente:

```text
Costo =
tokens de entrada × tarifa de entrada
+
tokens de salida × tarifa de salida
```

Por tanto, un prompt innecesariamente largo puede aumentar el consumo.

Pero reducir tokens indiscriminadamente tampoco es siempre correcto.

Debemos buscar:

> **La cantidad mínima de información necesaria para producir un resultado confiable.**

---

# 36. Tokens y latencia

Más tokens también pueden implicar mayor procesamiento.

Conceptualmente:

```text
Más tokens
    ↓
Más procesamiento
    ↓
Potencialmente mayor latencia
```

Pero la latencia real depende de muchos factores:

* modelo;
* hardware;
* longitud del contexto;
* longitud de salida;
* arquitectura;
* batching;
* proveedor;
* infraestructura;
* concurrencia;
* caché;
* optimizaciones de inferencia.

Por ello:

```text
Más tokens ≠ automáticamente X milisegundos adicionales
```

No existe una regla universal.

---

# 37. Tokens y calidad

Una idea peligrosa sería:

> "Cuantos menos tokens use, mejor será el prompt."

No necesariamente.

Supongamos:

```text
Prompt A:
Analiza esto.
```

y:

```text
Prompt B:
Analiza el documento e identifica inconsistencias,
evidencia, impacto y nivel de riesgo.
No inventes información.
```

El prompt B utiliza más tokens, pero puede proporcionar restricciones útiles.

Por tanto:

```text
Optimización de tokens
≠
eliminar información indiscriminadamente
```

La meta es:

```text
calidad
+
precisión
+
eficiencia
```

---

# 38. Idiomas y eficiencia de tokenización

Los idiomas no necesariamente utilizan el mismo número de tokens para expresar una misma idea.

Esto puede depender de:

* idioma;
* frecuencia de palabras;
* sistema de escritura;
* vocabulario del tokenizer;
* composición morfológica;
* entrenamiento del modelo.

Por ejemplo, una frase equivalente en:

```text
español
inglés
alemán
japonés
chino
árabe
```

puede producir diferentes cantidades de tokens.

Por eso no debemos asumir:

```text
100 palabras en cualquier idioma
=
100 tokens
```

---

# 39. Español y tokenización

El español tiene características que pueden afectar la tokenización:

* acentos;
* ñ;
* conjugaciones;
* género;
* número;
* palabras largas;
* derivados;
* prefijos y sufijos.

Por ejemplo:

```text
nacionalización
```

puede requerir varias unidades.

Mientras que una palabra frecuente y corta como:

```text
de
```

puede tener una representación muy eficiente.

La eficiencia depende del vocabulario aprendido por el tokenizer.

---

# 40. Tokenización y morfología

Consideremos:

```text
descentralización
```

Desde el punto de vista lingüístico podemos analizar:

```text
des + central + ización
```

Pero un tokenizer no necesariamente utilizará exactamente esas unidades lingüísticas.

Esto es importante:

> **Subpalabra no significa necesariamente morfema.**

Un tokenizer busca unidades útiles según su algoritmo y los datos, no necesariamente unidades lingüísticas con significado humano.

---

# 41. Tokens frecuentes y tokens raros

Los tokenizadores suelen favorecer representaciones eficientes para patrones frecuentes.

Por ejemplo:

```text
the
de
la
and
que
```

pueden ser unidades muy frecuentes en determinados corpus.

Mientras que nombres poco comunes o términos técnicos pueden dividirse en más fragmentos.

Esto puede producir diferencias de eficiencia entre:

```text
palabras frecuentes
```

y:

```text
palabras raras
```

---

# 42. Palabras nuevas

Supongamos que aparece un término inventado:

```text
hiperautomatizacióncuántica
```

Aunque el término no estuviera explícitamente en el vocabulario como una unidad completa, un tokenizer basado en subpalabras o bytes puede dividirlo en unidades conocidas.

Conceptualmente:

```text
hiper
automatización
cuántica
```

o una segmentación completamente diferente.

Esto proporciona una gran ventaja:

> **El sistema puede representar texto que no coincide exactamente con las palabras almacenadas en su vocabulario.**

---

# 43. ¿Un token tiene significado?

No necesariamente.

Algunos tokens coinciden con unidades lingüísticas con significado:

```text
casa
```

Pero otros pueden ser fragmentos:

```text
acio
```

o:

```text
ción
```

o incluso unidades que no tienen significado independiente para una persona.

Por tanto:

> **Un token es principalmente una unidad de representación definida por el sistema de tokenización, no necesariamente una unidad semántica.**

---

# 44. Tokenización y semántica

Debemos separar:

```text
Tokenización
```

de:

```text
Comprensión semántica
```

La tokenización responde:

> ¿Cómo dividir este texto en unidades que el modelo pueda procesar?

La representación semántica responde a preguntas más profundas:

> ¿Qué relaciones y significados puede representar el modelo?

La transición conceptual será:

```text
Texto
 ↓
Tokens
 ↓
Embeddings
 ↓
Representaciones contextuales
 ↓
Procesamiento
```

Por eso el siguiente capítulo será:

```text
03-Embeddings.md
```

---

# 45. Tokenización y prompt engineering

Aquí comienza la conexión directa con nuestra disciplina principal.

Cuando diseñamos un prompt, debemos recordar:

```text
Prompt escrito por humano
          ↓
      Tokenización
          ↓
      Representación
          ↓
          LLM
```

El modelo no recibe exactamente la experiencia visual que tenemos nosotros al leer el prompt.

Recibe una representación tokenizada.

Por eso determinadas estrategias de prompting pueden verse afectadas por:

* longitud;
* repetición;
* estructura;
* código;
* símbolos;
* idiomas;
* formato;
* contenido muy raro;
* contexto extremadamente largo.

---

# 46. ¿Los prompts más cortos son siempre mejores?

No.

Comparemos:

```text
Explica esto.
```

con:

```text
Explica este concepto para un estudiante
que nunca ha programado.

Define el concepto.
Después proporciona un ejemplo cotidiano.
Finalmente muestra un ejemplo técnico.
```

El segundo utiliza más tokens.

Pero proporciona más especificaciones.

La optimización correcta no es:

```text
mínimo número de tokens
```

sino:

```text
máxima utilidad por token
```

---

# 47. Prompt compression

En sistemas con contextos muy grandes, puede ser útil reducir información redundante.

Por ejemplo:

```text
Texto original:
20 000 tokens
```

puede transformarse en:

```text
Resumen relevante:
4 000 tokens
```

Pero existe un riesgo:

```text
compresión
 ↓
pérdida de información
```

Por eso la compresión de contexto debe evaluarse.

No debemos asumir:

```text
menos tokens = mejor sistema
```

---

# 48. Tokenización y seguridad

La tokenización también tiene importancia en seguridad.

Los sistemas de IA pueden recibir entradas con:

* caracteres Unicode;
* espacios inusuales;
* caracteres invisibles;
* codificaciones;
* texto mezclado;
* caracteres similares visualmente;
* fragmentos inesperados.

Por ejemplo:

```text
admin
```

y una cadena visualmente parecida utilizando caracteres Unicode diferentes pueden no ser internamente equivalentes.

Esto puede afectar:

* filtros;
* validaciones;
* detección de patrones;
* moderación;
* clasificación;
* sistemas de control.

Por eso, en sistemas de IA seguros, no basta con analizar únicamente la apariencia visual del texto.

---

# 49. Unicode homoglyphs

Un concepto relacionado es el de:

> **Homoglyph**

Son caracteres visualmente similares pero diferentes.

Por ejemplo, determinados caracteres de alfabetos distintos pueden parecer similares.

Conceptualmente:

```text
A
```

puede parecerse visualmente a otro carácter Unicode.

Esto puede utilizarse para crear entradas difíciles de detectar mediante filtros simples.

La lección para ingeniería de sistemas es:

> **La representación visual de una cadena y su representación computacional pueden diferir.**

---

# 50. Tokens especiales

Los modelos pueden utilizar tokens especiales.

Dependiendo del sistema pueden existir tokens asociados conceptualmente con:

* inicio;
* fin;
* separación;
* padding;
* desconocido;
* roles;
* estructura del mensaje.

Por ejemplo, conceptualmente:

```text
<START>
```

o:

```text
<END>
```

No todos los modelos utilizan los mismos tokens especiales ni los exponen de la misma manera.

---

# 51. Roles en conversaciones modernas

En sistemas conversacionales, la información puede estar estructurada conceptualmente como:

```text
system
user
assistant
tool
```

El sistema puede transformar esta estructura en una representación que el modelo procese.

Esto significa que una conversación:

```text
Sistema:
...

Usuario:
...

Asistente:
...
```

no debe imaginarse necesariamente como un simple bloque de texto plano.

La implementación depende del proveedor y del modelo.

Esto será especialmente importante cuando estudiemos:

```text
Prompt
System prompt
Context
Tools
Agents
```

---

# 52. Tokenización no es igual en todas las APIs

Una aplicación puede utilizar una API que abstraiga gran parte del proceso.

El programador puede escribir:

```python
response = client.generate(
    "Explica qué es un LLM."
)
```

sin observar directamente:

```text
tokens
IDs
embeddings
logits
sampling
```

Pero que la API oculte esos pasos no significa que no existan.

Para Ingeniería de IA debemos distinguir:

```text
abstracción de la API
```

de:

```text
mecanismo interno del modelo
```

---

# 53. Cómo contar tokens correctamente

No existe una fórmula universal basada solamente en el número de caracteres.

La forma correcta es utilizar:

> **El tokenizer compatible con el modelo concreto.**

Conceptualmente:

```python
tokens = tokenizer.encode(texto)
cantidad = len(tokens)
```

La implementación exacta dependerá de la biblioteca y del modelo.

Por eso, si necesitamos conocer el consumo real:

```text
NO:
aproximar solamente por número de palabras

SÍ:
utilizar el tokenizer correspondiente
```

---

# 54. Ejemplo conceptual en Python

Un flujo simplificado puede verse así:

```python
texto = "La inteligencia artificial está cambiando el mundo."

tokens = tokenizer.encode(texto)

print(tokens)
print(len(tokens))
```

El resultado podría ser algo parecido a:

```text
[....]
```

La lista exacta depende del tokenizer.

Después podemos reconstruir aproximadamente:

```python
texto_reconstruido = tokenizer.decode(tokens)
```

Conceptualmente:

```text
texto
 ↓
encode()
 ↓
token IDs
 ↓
decode()
 ↓
texto
```

---

# 55. Encode y decode

Dos operaciones fundamentales:

### Encode

Convierte texto en tokens o identificadores.

```text
texto → tokens
```

### Decode

Convierte tokens en texto.

```text
tokens → texto
```

Conceptualmente:

```text
             ENCODE
Texto ─────────────────→ Tokens


             DECODE
Texto ←───────────────── Tokens
```

En implementaciones reales pueden existir detalles adicionales relacionados con normalización, bytes y tokens especiales.

---

# 56. Experimento práctico

Una buena práctica para un estudiante es seleccionar diferentes tipos de texto:

```text
Hola mundo.

Inteligencia artificial.

electroencefalografista

2026-09-30

3.141592653589793

Python

def sumar(a, b):
    return a + b

😀

¡Hola, Jorge!
```

y medir:

```text
cantidad de caracteres
cantidad de palabras
cantidad de tokens
```

Después comparar.

La pregunta importante es:

> ¿Existe una relación fija entre caracteres, palabras y tokens?

La respuesta es:

> **No.**

---

# 57. Ejercicio práctico recomendado

Construye una tabla:

| Texto                     | Caracteres | Palabras | Tokens |
| ------------------------- | ---------: | -------: | -----: |
| `Hola mundo`              |          ? |        ? |      ? |
| `inteligencia artificial` |          ? |        ? |      ? |
| `electroencefalografista` |          ? |        ? |      ? |
| `20260930`                |          ? |        ? |      ? |
| `😀`                      |          ? |        ? |      ? |
| código Python             |          ? |        ? |      ? |

Después repite el experimento utilizando dos tokenizadores diferentes.

Pregunta:

> ¿Por qué cambian los resultados?

---

# 58. Experimento avanzado

Selecciona una misma frase:

```text
La inteligencia artificial está transformando la educación.
```

Tradúcela:

```text
The artificial intelligence is transforming education.
```

y compara:

```text
palabras
caracteres
tokens
```

Después realiza el mismo experimento con:

```text
中文
العربية
日本語
```

La finalidad no es determinar qué idioma es "mejor".

La finalidad es observar cómo:

```text
idioma
+
vocabulario
+
tokenizer
```

afectan la representación.

---

# 59. Un error frecuente: "un token son aproximadamente cuatro caracteres"

En algunos contextos se utiliza una aproximación como:

```text
1 token ≈ 4 caracteres
```

Esta regla puede ser útil para obtener una estimación rápida en determinados textos y tokenizadores.

Pero:

> **No es una ley universal.**

Puede variar considerablemente según:

* idioma;
* texto;
* tokenizer;
* código;
* números;
* símbolos;
* caracteres Unicode.

Para cálculos reales debemos utilizar el tokenizer correspondiente.

---

# 60. Un segundo error: "100 tokens son 100 palabras"

Incorrecto.

Podríamos tener:

```text
100 tokens
```

representando:

```text
menos de 100 palabras
```

o:

```text
más de 100 unidades lingüísticas
```

dependiendo del texto.

La relación puede variar.

---

# 61. Un tercer error: "cada token tiene significado"

Incorrecto.

Un token puede ser:

```text
palabra
```

pero también:

```text
fragmento de palabra
```

o:

```text
símbolo
```

o:

```text
fragmento de código
```

o:

```text
unidad basada en bytes
```

---

# 62. Un cuarto error: "el tokenizer entiende el significado"

No debemos atribuir al tokenizer capacidades que pertenecen al modelo.

El tokenizer principalmente realiza una transformación:

```text
texto
 ↓
unidades
 ↓
IDs
```

La construcción de representaciones más complejas ocurre posteriormente.

Por tanto:

```text
Tokenizer
≠
LLM
```

---

# 63. Un quinto error: "todos los modelos usan el mismo tokenizer"

Incorrecto.

Los modelos pueden utilizar tokenizadores diferentes.

Por eso una cadena puede tener:

```text
Modelo A → 12 tokens
Modelo B → 15 tokens
Modelo C → 9 tokens
```

sin que exista una contradicción.

---

# 64. Tokenización y eficiencia del modelo

Una tokenización eficiente puede representar determinada información utilizando menos tokens.

Esto puede tener ventajas en:

* memoria;
* contexto;
* costo;
* velocidad;
* procesamiento.

Pero la eficiencia de tokenización no es el único factor que determina la eficiencia total del modelo.

También intervienen:

* arquitectura;
* hardware;
* atención;
* cuantización;
* batching;
* caché;
* implementación;
* optimizaciones de inferencia.

---

# 65. Tokenización y modelos multilingües

Los modelos que deben trabajar con muchos idiomas enfrentan un problema de diseño:

```text
¿Cómo construir un vocabulario
que sea eficiente para muchos idiomas?
```

Un vocabulario demasiado especializado en inglés podría ser menos eficiente para otros idiomas.

Un vocabulario extremadamente grande también puede tener costos de memoria y procesamiento.

Por eso la construcción del tokenizer es una decisión de ingeniería.

---

# 66. El tokenizer como frontera entre humano y modelo

Podemos pensar en el tokenizer como una frontera:

```text
             HUMANO
               │
               │ lenguaje
               ↓
          ┌───────────┐
          │ TOKENIZER │
          └─────┬─────┘
                │
                │ tokens
                ↓
              MODELO
```

El usuario trabaja con:

```text
lenguaje natural
```

mientras el modelo trabaja con:

```text
representaciones numéricas
```

El tokenizer conecta ambos mundos.

---

# 67. De texto a predicción

Ahora podemos ampliar el modelo mental del capítulo anterior:

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
                 EMBEDDINGS
                      │
                      ↓
                 TRANSFORMER
                      │
                      ↓
                DISTRIBUCIÓN
               DE PROBABILIDAD
                      │
                      ↓
                  SAMPLING /
                 DECODIFICACIÓN
                      │
                      ↓
                 TOKEN NUEVO
                      │
                      ↓
                    TEXTO
```

Todavía faltan varias piezas.

Las estudiaremos progresivamente.

---

# 68. Relación con el siguiente capítulo

Hasta ahora sabemos:

```text
Texto
 ↓
Tokenización
 ↓
Token IDs
```

Pero aparece una pregunta fundamental:

> **¿Cómo puede una red neuronal trabajar con un número que identifica un token?**

Por ejemplo:

```text
"casa" → 1523
```

El número `1523` no contiene por sí mismo una representación semántica útil.

Necesitamos transformar ese identificador en una representación vectorial.

La respuesta nos lleva al siguiente concepto:

```text
TOKEN ID
   ↓
EMBEDDING
   ↓
VECTOR
```

Por eso el siguiente capítulo será:

**`03-Embeddings.md`**

---

# 69. Conexión con Ingeniería de Prompt

El estudiante debe comenzar a cambiar su forma de pensar.

Al principio:

```text
"Escribo un prompt y la IA responde."
```

Después de estudiar tokenización:

```text
"Escribo texto."
        ↓
"El sistema lo tokeniza."
        ↓
"Los tokens se convierten en representaciones."
        ↓
"El modelo procesa esas representaciones."
        ↓
"El modelo produce una distribución de salida."
        ↓
"Se seleccionan tokens."
        ↓
"Los tokens se convierten nuevamente en texto."
```

Esta segunda visión es mucho más cercana a la realidad técnica.

---

# 70. Regla fundamental

> **Un LLM no procesa directamente las palabras como las ve un ser humano; procesa representaciones derivadas de tokens.**

Y una segunda regla:

> **La cantidad y composición de tokens dependen del tokenizer y del texto.**

Y una tercera:

> **Optimizar un sistema de IA no significa simplemente reducir el número de tokens; significa utilizar los tokens necesarios para obtener el comportamiento requerido de forma eficiente y confiable.**

---

# 71. Resumen

La cadena fundamental estudiada en este capítulo es:

```text
TEXTO
  ↓
TOKENIZACIÓN
  ↓
TOKENS
  ↓
TOKEN IDs
  ↓
EMBEDDINGS
```

Los puntos esenciales son:

1. Un token no es necesariamente una palabra.
2. Un tokenizer divide el texto en unidades utilizadas por el modelo.
3. Diferentes modelos pueden utilizar diferentes tokenizadores.
4. Las palabras pueden dividirse en múltiples tokens.
5. Un token puede ser solamente un fragmento de una palabra.
6. Los números, símbolos, emojis y código también se tokenizan.
7. Los tokens afectan la utilización de la ventana de contexto.
8. Los tokens pueden influir en costos y latencia.
9. Los idiomas pueden presentar diferentes eficiencias de tokenización.
10. Token ID y embedding son conceptos diferentes.
11. La tokenización no es comprensión semántica.
12. El tokenizer forma parte de la interfaz entre el texto y el modelo.
13. Comprender tokens es necesario para comprender contexto, costos e inferencia.
14. En Ingeniería de Prompt, la longitud visual del texto no es una medida suficiente de su costo computacional.

---

# 72. Pregunta central para el siguiente nivel

Ya sabemos cómo:

```text
Texto
 ↓
Tokens
```

Pero todavía no sabemos cómo esos tokens se convierten en una representación que una red neuronal pueda utilizar para establecer relaciones entre conceptos.

La siguiente pregunta es:

> **¿Cómo convierte un LLM un token como "casa" en una representación matemática que pueda utilizar el modelo?**

La respuesta comienza con:

# Embeddings

→ `03-Embeddings.md`
