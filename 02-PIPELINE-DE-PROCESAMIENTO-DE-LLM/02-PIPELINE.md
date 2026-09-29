----------

### 02. Pipeline de Procesamiento de LLMs"

-   "01-CONCEPTOS-FUNDAMENTALES-DE-LLM"  
    
    
-   pipeline
    
-   tokenización
    
-   embeddings
    
-   attention
    
-   transformer
    
-   arquitectura
    
-   MoE
    
-   GQA
    
-   MQA
    
-   MLA
    
-   KV-cache
    
-   inferencia
    
-   reasoning
    
-   multimodalidad  
    version: "2.0.0"  
    last_updated: "2026-09-29"
    

----------

# 02. Pipeline de Procesamiento de LLMs

## 1. Introducción

En el módulo anterior aprendimos qué son los tokens, embeddings, atención, ventana de contexto, logits y sampling.

Ahora vamos a responder una pregunta más profunda:

> ¿Qué ocurre realmente dentro de un modelo de lenguaje desde que recibe una entrada hasta que genera una respuesta?

Comprender este proceso es fundamental para la ingeniería de prompts, pero también constituye la base para estudiar:

-   arquitectura de Transformers;
    
-   entrenamiento de modelos;
    
-   inferencia;
    
-   optimización de memoria;
    
-   modelos Dense y MoE;
    
-   modelos de razonamiento;
    
-   modelos multimodales;
    
-   long-context;
    
-   cuantización;
    
-   serving;
    
-   agentes;
    
-   evaluación de modelos;
    
-   optimización de costos y latencia.
    

El objetivo no es memorizar una lista de componentes.

El objetivo es comprender **qué problema resuelve cada componente y cómo afecta al comportamiento del modelo**.

----------

# 2. Una advertencia importante: no existe un único pipeline universal

Una representación educativa habitual es:

```text
Texto
  ↓
Tokenización
  ↓
IDs
  ↓
Embeddings
  ↓
Información posicional
  ↓
Transformer
  ↓
Logits
  ↓
Probabilidades
  ↓
Sampling
  ↓
Siguiente token
  ↓
Repetir

```

Este esquema es excelente para comprender un LLM causal basado en Transformer.

Pero no debe interpretarse como una descripción exacta de todos los modelos modernos.

Existen diferencias importantes entre:

-   modelos decoder-only;
    
-   modelos encoder-only;
    
-   modelos encoder-decoder;
    
-   modelos Dense;
    
-   modelos MoE;
    
-   modelos multimodales;
    
-   modelos con atención densa;
    
-   modelos con atención dispersa;
    
-   modelos con diferentes mecanismos posicionales;
    
-   modelos con arquitecturas híbridas.
    

Por ejemplo, en 2026 DeepSeek ha introducido una arquitectura denominada **Causal Encoder–Decoder** en DeepSeek-V4.1-Flash, mientras que otros modelos continúan utilizando arquitecturas decoder-only.

Por eso debemos aprender el pipeline como un **conjunto de conceptos**, no como una receta idéntica para todos los modelos.

----------

# 3. Vista general del pipeline

Para un modelo causal de lenguaje podemos representar el proceso de manera simplificada:

```text
                 ENTRADA
                    │
                    ▼
          Texto / imagen / audio
                    │
                    ▼
          Procesamiento de entrada
                    │
                    ▼
               Tokenización
                    │
                    ▼
              Token IDs
                    │
                    ▼
        Representaciones vectoriales
                    │
                    ▼
      Información de posición/contexto
                    │
                    ▼
       ┌──────────────────────────┐
       │     Transformer × N       │
       │                           │
       │ Attention                 │
       │ Residual connections      │
       │ Normalization             │
       │ FFN / MoE                 │
       └──────────────────────────┘
                    │
                    ▼
                 Logits
                    │
                    ▼
        Restricciones / filtros
                    │
                    ▼
       Sampling / selección token
                    │
                    ▼
             Token generado
                    │
                    ▼
          ¿Terminar generación?
             │             │
            No             Sí
             │              │
             └──────┐       ▼
                    │     SALIDA
                    ▼
               siguiente paso

```

Este flujo tiene una característica fundamental:

> La generación autoregresiva reutiliza la secuencia anterior y produce la respuesta token por token.

----------

# 4. Texto de entrada: el modelo no recibe "palabras"

Supongamos que escribimos:

```text
El gato duerme.

```

Para nosotros existen tres palabras.

Para el modelo no existe directamente:

```text
El
gato
duerme

```

Existe una secuencia de unidades definida por su tokenizer.

Podría ser conceptualmente:

```text
["El", " gato", " duerme", "."]

```

o una segmentación diferente.

Por eso:

> Palabra humana ≠ token necesariamente.

Un token puede representar:

-   una palabra completa;
    
-   parte de una palabra;
    
-   varios caracteres;
    
-   un símbolo;
    
-   puntuación;
    
-   espacio asociado;
    
-   secuencias de bytes;
    
-   unidades especiales utilizadas por el modelo.
    

La tokenización depende del tokenizer utilizado.

----------

# 5. Tokenización

## 5.1 ¿Qué hace realmente un tokenizer?

El tokenizer convierte una secuencia de entrada en unidades que el modelo puede representar mediante identificadores enteros.

Conceptualmente:

```text
Texto
 ↓
Tokenizer
 ↓
Tokens
 ↓
IDs

```

Ejemplo hipotético:

```text
"El gato duerme"

↓
["El", " gato", " duerme"]

↓

[1452, 3841, 8271]

```

Los números son solamente identificadores.

El número `3841` no significa que el token tenga un significado matemático de "gato".

Simplemente identifica una entrada dentro del vocabulario del modelo.

----------

# 6. El vocabulario

Un tokenizer posee un vocabulario.

Conceptualmente:

```text
ID       Token
----------------
0        <PAD>
1        <EOS>
2        <BOS>
...
1452     "El"
3841     " gato"
8271     " duerme"

```

El modelo utiliza estos identificadores para acceder a representaciones numéricas.

Por eso aparece una transición:

```text
Token ID
   ↓
Embedding
   ↓
Vector

```

----------

# 7. BPE, WordPiece, Unigram y SentencePiece

No todos los modelos utilizan el mismo sistema de tokenización.

Entre los enfoques conocidos encontramos:

### BPE

Byte Pair Encoding aprende fusiones de unidades frecuentes.

Conceptualmente:

```text
unidades pequeñas
       ↓
pares frecuentes
       ↓
fusiones
       ↓
tokens

```

BPE se encuentra en numerosos modelos modernos, aunque las implementaciones concretas pueden variar.

### WordPiece

Fue utilizado ampliamente en arquitecturas como BERT.

Su objetivo también es construir unidades subléxicas, pero su procedimiento de aprendizaje y criterio de segmentación no es idéntico a BPE.

### Unigram

Utiliza un modelo probabilístico de segmentación y fue utilizado, entre otros, en familias basadas en SentencePiece.

### SentencePiece

Es importante no confundir:

```text
SentencePiece ≠ un único algoritmo de tokenización.

```

SentencePiece es un sistema de entrenamiento/tokenización que puede utilizar diferentes algoritmos, incluyendo BPE y Unigram.

----------

# 8. Un error frecuente: "el tokenizer limpia el texto"

No debemos enseñar que todos los tokenizers hacen:

```text
quitar tildes
quitar puntuación
pasar todo a minúsculas
eliminar stopwords

```

Eso sería incorrecto.

Un tokenizer moderno puede conservar diferencias de:

```text
Hola
hola
HOLA

```

y también:

```text
gato
gatos
gatito

```

La tokenización depende de la implementación concreta.

Por ello, no debemos asumir que el modelo recibe texto "limpio".

----------

# 9. Unicode y normalización

Unicode introduce otro nivel de complejidad.

Dos representaciones visualmente similares pueden tener diferentes representaciones internas.

Por ejemplo, ciertos caracteres acentuados pueden representarse:

```text
carácter precompuesto

```

o como:

```text
carácter base + marca combinada

```

Una aplicación puede normalizar Unicode antes de enviar los datos.

Pero:

> La normalización Unicode no es una etapa universal idéntica para todos los LLM.

Esto debe distinguirse de la tokenización.

----------

# 10. De tokens a IDs

Una vez realizada la segmentación:

```text
"El gato duerme"

```

puede convertirse en:

```text
["El", " gato", " duerme"]

```

y posteriormente:

```text
[1452, 3841, 8271]

```

El modelo trabaja con esos identificadores.

La relación puede representarse como:

```text
token → ID

```

Esta operación es determinista para un tokenizer dado y una entrada determinada.

Pero el ID por sí mismo no contiene el significado semántico.

----------

# 11. De IDs a vectores: Embedding Lookup

Ahora ocurre una transformación fundamental.

Supongamos que el modelo tiene:

```text
vocab_size = 100000
d_model = 4096

```

Puede existir una matriz de embeddings:

```text
E ∈ R^(100000 × 4096)

```

Cada fila corresponde a una representación vectorial asociada a un token.

Si tenemos:

```text
ID = 3841

```

podemos seleccionar:

```text
E[3841]

```

obteniendo un vector:

```text
[0.12, -0.44, 0.83, ..., 0.07]

```

con 4096 componentes en este ejemplo.

----------

# 12. Importante: embedding no significa "significado en una coordenada"

Una explicación demasiado simplificada sería:

```text
gato = [animal, pequeño, doméstico, ...]

```

Eso no describe correctamente cómo funcionan estos vectores.

Las dimensiones individuales normalmente no corresponden a etiquetas humanas simples.

El significado está distribuido en muchas dimensiones.

Por eso es mejor pensar:

```text
Embedding
=
representación matemática aprendida

```

y no:

```text
Dimensión 1 = animal
Dimensión 2 = doméstico
Dimensión 3 = masculino

```

----------

# 13. Embedding inicial vs representación contextual

Aquí aparece una distinción fundamental.

## Representación inicial

Un token puede comenzar asociado a un vector aprendido.

```text
"banco"
   ↓
vector inicial

```

Ese vector es asociado al token.

Pero después de pasar por las capas del Transformer, la representación de esa posición cambia dependiendo del contexto.

Por ejemplo:

```text
El banco aprobó el préstamo.

```

y:

```text
Me senté en el banco del parque.

```

La palabra puede ser la misma.

Pero su representación contextual dentro del modelo será diferente.

Conceptualmente:

```text
Banco
  ↓
representación inicial
  ↓
Transformer
  ↓
representación contextual

```

----------

# 14. Información posicional

El Transformer necesita alguna forma de representar el orden.

No basta con conocer:

```text
gato
come
pez

```

porque:

```text
El gato come el pez.

```

no significa lo mismo que:

```text
El pez come el gato.

```

El modelo necesita información que permita distinguir posiciones y relaciones relativas.

----------

# 15. Positional Encoding no significa necesariamente "sumar senos"

Los primeros Transformers popularizaron embeddings posicionales sinusoidales.

La fórmula clásica es:

PE(pos,2i)=sin⁡(pos100002i/d)PE(pos,2i)=\sin\left(\frac{pos}{10000^{2i/d}}\right) PE(pos,2i+1)=cos⁡(pos100002i/d)PE(pos,2i+1)=\cos\left(\frac{pos}{10000^{2i/d}}\right)

Pero los modelos modernos utilizan diferentes estrategias.

Entre ellas encontramos:

-   positional embeddings aprendidos;
    
-   RoPE;
    
-   variantes de RoPE;
    
-   métodos de extrapolación de contexto;
    
-   arquitecturas que combinan diferentes tratamientos posicionales.
    

Por ejemplo, Llama 4 utiliza una arquitectura denominada **iRoPE**, combinando capas de atención intercaladas y tratamiento posicional diferente entre capas para alcanzar contextos extremadamente largos. Meta reporta hasta 10 millones de tokens para Llama 4 Scout.

----------

# 16. RoPE: Rotary Position Embedding

RoPE es una de las técnicas más importantes para comprender los LLM modernos.

La idea general es introducir información posicional mediante rotaciones aplicadas a las representaciones de Query y Key.

De manera conceptual:

```text
Q + posición
      ↓
rotación

K + posición
      ↓
rotación

```

Esto permite que los productos:

QKTQK^T

contengan información relacionada con la posición relativa.

RoPE se convirtió en una técnica muy utilizada en arquitecturas modernas.

Para ingeniería de IA, esto importa especialmente cuando estudiamos:

-   contexto largo;
    
-   extrapolación de posiciones;
    
-   atención;
    
-   KV cache;
    
-   eficiencia de inferencia.
    

----------

# 17. El Transformer

Después de obtener las representaciones iniciales comienza la parte central del modelo.

Un bloque Transformer moderno puede contener aproximadamente:

```text
Input
  ↓
Normalization
  ↓
Attention
  ↓
Residual connection
  ↓
Normalization
  ↓
FFN / MoE
  ↓
Residual connection
  ↓
Output

```

El orden exacto puede variar según la arquitectura.

Por eso debemos evitar afirmar que todos los Transformers tienen exactamente la misma implementación.

----------

# 18. Self-Attention

La fórmula fundamental es:

Attention(Q,K,V)=softmax(QKTdk)VAttention(Q,K,V) = softmax \left( \frac{QK^T}{\sqrt{d_k}} \right)V

Esta fórmula merece estudiarse con profundidad.

----------

# 19. Q, K y V

Partimos de una representación:

XX

El modelo genera:

Q=XWQQ=XW^Q K=XWKK=XW^K V=XWVV=XW^V

Podemos utilizar una analogía:

-   Query: qué información necesita una posición.
    
-   Key: qué características ofrece una posición.
    
-   Value: qué información aporta realmente esa posición.
    

Pero debemos recordar:

> Q, K y V son transformaciones matemáticas aprendidas; no son literalmente preguntas, etiquetas y respuestas.

La analogía sirve para comprender el mecanismo, no para describir literalmente lo que "piensa" el modelo.

----------

# 20. Paso 1: calcular similitudes

Se calcula:

QKTQK^T

Si tenemos:

```text
L = número de posiciones
d_k = dimensión de cada cabeza

```

entonces:

(L,dk)(dk,L)(L,d_k)(d_k,L)

produce:

(L,L)(L,L)

Es decir, una matriz donde cada posición puede tener una puntuación respecto de otras posiciones.

----------

# 21. Paso 2: escalar

Se divide por:

dk\sqrt{d_k}

Esto ayuda a controlar la escala de los productos y evita que el softmax se vuelva excesivamente saturado debido a magnitudes grandes.

----------

# 22. Paso 3: Softmax

Se aplica:

softmax(zi)=ezi∑jezjsoftmax(z_i) = \frac{e^{z_i}} {\sum_j e^{z_j}}

Esto transforma los scores en pesos normalizados.

Ejemplo simplificado:

```text
scores:

[2.0, 1.0, 0.1]

```

pueden convertirse aproximadamente en:

```text
[0.66, 0.24, 0.10]

```

Los valores de una fila suman aproximadamente:

```text
1.0

```

----------

# 23. Paso 4: combinar Values

Finalmente:

Attention(Q,K,V)=AVAttention(Q,K,V)=AV

donde:

A=softmax(QKT/dk)A=softmax(QK^T/\sqrt{d_k})

Cada posición obtiene una combinación ponderada de los Values.

----------

# 24. Multi-Head Attention

Una sola atención puede aprender determinadas relaciones.

Multi-Head Attention permite disponer de varias cabezas.

Conceptualmente:

```text
                    Input
                      │
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
      Head 1        Head 2        Head 3
        ↓             ↓             ↓
      Attn 1        Attn 2        Attn 3
        └─────────────┼─────────────┘
                      ↓
                   Concat
                      ↓
                    Wᵒ
                      ↓
                   Output

```

Es tentador afirmar:

```text
Head 1 = gramática
Head 2 = nombres
Head 3 = pronombres

```

pero eso sería demasiado fuerte.

Algunas cabezas pueden mostrar patrones interpretables, pero no existe una regla universal donde cada cabeza tenga una función semántica humana claramente identificable.

----------

# 25. Causal Attention

En un modelo autoregresivo, cuando el modelo genera el token siguiente no debe utilizar información futura.

Por ejemplo:

```text
El gato ___

```

El modelo puede utilizar:

```text
El
gato

```

pero no debería acceder al token futuro que todavía no ha generado.

Esto se implementa mediante una máscara causal.

Conceptualmente:

```text
        El gato duerme
El      ✓   ✗     ✗
gato    ✓   ✓     ✗
duerme  ✓   ✓     ✓

```

La máscara impide determinadas conexiones hacia posiciones futuras.

Esta propiedad es fundamental para comprender los modelos decoder-only.

----------

# 26. Atención densa y costo cuadrático

En atención estándar, la matriz:

QKTQK^T

tiene dimensión:

L×LL\times L

Por ello, el costo de atención aumenta aproximadamente de forma cuadrática con la longitud de secuencia:

O(L2)O(L^2)

Esto explica por qué pasar de:

```text
4K → 8K

```

no es simplemente "el doble de trabajo" para la atención.

Aproximadamente:

```text
4K² = 16 millones
8K² = 64 millones

```

La relación es aproximadamente:

```text
4 veces

```

Este problema es una de las razones por las que la investigación en long-context ha producido técnicas como:

-   sparse attention;
    
-   sliding-window attention;
    
-   grouped-query attention;
    
-   multi-query attention;
    
-   latent attention;
    
-   compresión de KV;
    
-   kernels optimizados;
    
-   arquitecturas híbridas.
    

----------

# 27. MHA, MQA y GQA

No debemos estudiar atención únicamente como MHA.

Existen diferentes formas de organizar las cabezas de Query, Key y Value.

## MHA

Multi-Head Attention tradicional:

```text
Q heads = K heads = V heads

```

Cada cabeza dispone de sus propias representaciones.

## MQA

Multi-Query Attention:

```text
muchas Q
una K/V compartida

```

Esto reduce memoria de KV durante inferencia.

## GQA

Grouped-Query Attention:

```text
muchas Q
menos grupos de K/V

```

Es un punto intermedio.

Qwen3, por ejemplo, utiliza diferentes configuraciones Q/KV según el tamaño del modelo; sus modelos MoE grandes reportan 64 cabezas de Query y 4 de Key/Value.

----------

# 28. ¿Por qué importa GQA para un ingeniero de IA?

Porque el problema no termina cuando el modelo genera el primer token.

Durante la generación se mantiene información de Key y Value para reutilizarla.

Eso nos lleva al:

# KV Cache

----------

# 29. KV Cache

Sin KV cache, el modelo tendría que recalcular gran parte de la información anterior en cada paso.

Con KV cache:

```text
Prompt
  ↓
Procesamiento inicial
  ↓
K/V almacenados
  ↓
Token nuevo
  ↓
Se reutilizan K/V anteriores
  ↓
Se calcula únicamente lo necesario

```

Esto reduce muchísimo el trabajo repetitivo durante la generación.

----------

# 30. Prefill y Decode

La inferencia moderna suele dividirse conceptualmente en dos fases.

## Prefill

El modelo procesa el prompt inicial.

```text
"Analiza este documento de 50 páginas..."

```

Aquí puede procesar muchos tokens en paralelo.

## Decode

Después genera:

```text
token 1
token 2
token 3
...

```

de forma autoregresiva.

Por eso podemos representar:

```text
          PROMPT
             ↓
          PREFILL
             ↓
         KV CACHE
             ↓
     ┌──── DECODE ────┐
     │                │
     ↓                │
 token nuevo          │
     ↓                │
 actualizar KV ───────┘

```

Esta distinción es fundamental para comprender:

-   latencia;
    
-   throughput;
    
-   costos;
    
-   memoria GPU;
    
-   serving;
    
-   optimización de agentes.
    

----------

# 31. FlashAttention

Otro avance importante es la optimización de cómo se calcula la atención.

FlashAttention no cambia simplemente la fórmula matemática de atención.

Optimiza su implementación para utilizar mejor la jerarquía de memoria del hardware y reducir movimientos innecesarios de datos.

La idea importante para un ingeniero es:

```text
misma operación matemática
+
mejor implementación
=
menor uso de memoria / mayor eficiencia

```

Esto demuestra una lección importante:

> El rendimiento de un LLM no depende únicamente de la arquitectura matemática. También depende de cómo se implementa en hardware.

----------

# 32. Feed-Forward Network

Después de la atención aparece normalmente una red feed-forward.

Una forma simplificada es:

FFN(x)=W2σ(W1x)FFN(x)=W_2\sigma(W_1x)

En arquitecturas modernas aparecen variantes como:

-   GELU;
    
-   SiLU;
    
-   SwiGLU;
    
-   otras variantes de redes feed-forward.
    

Una representación conceptual:

```text
x
 ↓
Linear
 ↓
Activación
 ↓
Linear
 ↓
Output

```

La atención permite intercambiar información entre posiciones.

La FFN transforma cada representación mediante una red no lineal.

----------

# 33. ¿Dónde aparece el conocimiento del modelo?

No existe una única matriz llamada:

```text
BASE DE CONOCIMIENTO

```

El comportamiento aprendido se encuentra distribuido en los parámetros de múltiples componentes.

Por eso una pregunta como:

> "¿En qué parámetro está almacenada la información sobre Ecuador?"

no tiene una respuesta simple.

El conocimiento aprendido está distribuido en la red.

----------

# 34. Normalización

Los Transformers utilizan mecanismos de normalización para estabilizar el procesamiento.

Entre los métodos importantes encontramos:

-   LayerNorm;
    
-   RMSNorm;
    
-   variantes arquitectónicas.
    

Por ejemplo:

RMSNorm(x)=x1d∑ixi2+ϵ⊙gRMSNorm(x) = \frac{x}{\sqrt{\frac{1}{d}\sum_i x_i^2+\epsilon}} \odot g

La implementación exacta depende de la arquitectura.

Para ingeniería avanzada, la normalización es importante porque afecta:

-   estabilidad;
    
-   entrenamiento;
    
-   escalabilidad;
    
-   comportamiento de las capas;
    
-   rendimiento.
    

----------

# 35. Residual Connections

Una estructura fundamental es:

xnuevo=x+f(x)x_{nuevo}=x+f(x)

Por ejemplo:

```text
x
│
├───────────────┐
│               │
↓               │
Attention       │
│               │
└────── + ──────┘
        │
        ↓

```

Estas conexiones ayudan a que la información pueda atravesar muchas capas y facilitan el entrenamiento de redes profundas.

----------

# 36. Transformer completo

Podemos resumir un bloque de manera conceptual:

```text
Input
  │
  ▼
Normalization
  │
  ▼
Attention
  │
  ▼
Residual
  │
  ▼
Normalization
  │
  ▼
FFN / MoE
  │
  ▼
Residual
  │
  ▼
Output

```

El modelo puede contener muchas de estas capas.

Por ejemplo:

```text
Embedding
   ↓
Transformer Layer 1
   ↓
Transformer Layer 2
   ↓
Transformer Layer 3
   ↓
...
   ↓
Transformer Layer N
   ↓
Final normalization
   ↓
LM Head

```

----------

# 37. Dense vs MoE

Esta distinción es fundamental para estudiar la generación de modelos modernos.

## Modelo Dense

En un modelo Dense, cada token atraviesa las partes principales de la red correspondientes a todas las capas.

Simplificando:

```text
Token
 ↓
Bloque completo
 ↓
Bloque completo
 ↓
Bloque completo
 ↓
...

```

## Modelo MoE

En Mixture-of-Experts existen múltiples expertos.

Conceptualmente:

```text
                 Token
                   ↓
                 Router
          ┌────────┼────────┐
          ↓        ↓        ↓
       Expert 1 Expert 2 Expert 3
          │        │        │
          └────────┼────────┘
                   ↓
                combinar

```

No todos los expertos tienen que procesar cada token.

----------

# 38. ¿Qué hace realmente el Router?

El router calcula qué expertos deben recibir un token.

Conceptualmente:

g(x)=softmax(Wrx)g(x)=softmax(W_rx)

Después puede seleccionar:

```text
Top-1

```

o:

```text
Top-2

```

u otro número de expertos.

El resultado puede expresarse como:

y=∑i∈TopKgi(x)Ei(x)y=\sum_{i\in TopK}g_i(x)E_i(x)

El número exacto de expertos y el método de routing dependen de la arquitectura.

----------

# 39. Un error frecuente: "un experto es médico"

No necesariamente.

No debemos enseñar:

```text
Expert 1 = medicina
Expert 2 = programación
Expert 3 = matemáticas

```

Los expertos no reciben necesariamente esas etiquetas.

La especialización puede emerger durante el entrenamiento.

Podemos encontrar patrones de especialización, pero no debemos asumir una correspondencia humana explícita.

----------

# 40. Otro error: "MoE siempre es mejor"

Tampoco.

MoE ofrece una estrategia para aumentar capacidad manteniendo relativamente limitado el cómputo por token.

Pero introduce problemas:

-   memoria;
    
-   comunicación entre GPUs;
    
-   balanceo de expertos;
    
-   routing;
    
-   capacidad de expertos;
    
-   latencia;
    
-   complejidad de serving;
    
-   estabilidad del entrenamiento.
    

Por tanto:

```text
MoE ≠ magia

```

Es un compromiso arquitectónico.

----------

# 41. Ejemplos reales de MoE

La evolución reciente muestra claramente la importancia de MoE.

### DeepSeek-V3

DeepSeek-V3 utiliza:

```text
671B parámetros totales
37B parámetros activados aproximadamente por token

```

y fue entrenado con 14.8T tokens.

### Qwen3

Qwen3 incluye:

```text
Qwen3-30B-A3B
30B totales
3B activos

```

y:

```text
Qwen3-235B-A22B
235B totales
22B activos

```

según la documentación oficial de Qwen.

### Llama 4

Llama 4 Scout:

```text
109B totales
17B activos
16 expertos
10M tokens de contexto

```

Llama 4 Maverick:

```text
400B totales
17B activos
128 expertos

```

Meta describe además capas Dense y MoE intercaladas en Maverick.

----------

# 42. La evolución hacia 2026

Para septiembre de 2026 ya no basta con estudiar:

```text
Dense vs MoE

```

También debemos conocer:

```text
Dense
MoE
GQA
MQA
MLA
Sparse Attention
Long Context
KV Compression
Speculative Decoding
Multimodalidad nativa
Reasoning / Test-Time Compute
Arquitecturas híbridas

```

Un ejemplo especialmente importante es DeepSeek.

DeepSeek-V4 introdujo técnicas orientadas a contexto de aproximadamente un millón de tokens y nuevas estrategias de atención, incluyendo compresión y DeepSeek Sparse Attention.

En septiembre de 2026, DeepSeek-V4.1-Flash introdujo además una arquitectura Causal Encoder–Decoder y una configuración asimétrica de cómputo para entrada y salida. DeepSeek reporta 552B parámetros totales y 8B activos para procesamiento de entrada y 16B para salida.

Esto es importante porque demuestra que:

> La arquitectura de los LLM está evolucionando más allá del Transformer decoder-only clásico que normalmente se enseña primero.

----------

# 43. Logits

Después de procesar la secuencia, el modelo necesita decidir qué token puede aparecer después.

La última representación pasa por una proyección hacia el vocabulario.

Conceptualmente:

h∈Rdmodelh \in R^{d_{model}}

se transforma en:

z∈RVz \in R^{V}

donde:

```text
V = tamaño del vocabulario

```

Estos valores son los:

# Logits

----------

# 44. Los logits no son probabilidades

Supongamos:

```text
Token       Logit
------------------
"gato"       5.2
"perro"      4.7
"casa"       1.8
"avión"     -0.4

```

No podemos decir directamente:

```text
gato = 5.2%

```

Eso es incorrecto.

Son puntuaciones antes de convertirlas en una distribución de probabilidad.

----------

# 45. Softmax

Para obtener probabilidades:

Pi=ezi∑jezjP_i= \frac{e^{z_i}} {\sum_j e^{z_j}}

Por ejemplo:

```text
Logits
[5.2, 4.7, 1.8, -0.4]

```

se transforman en algo como:

```text
Probabilidades
[0.60, 0.36, 0.03, 0.01]

```

Los números concretos son solamente ilustrativos.

----------

# 46. Selección del siguiente token

Ahora el modelo debe decidir qué token producir.

Existen diferentes estrategias.

## Greedy decoding

Selecciona el token de mayor probabilidad.

```text
P(A) = 0.60
P(B) = 0.30
P(C) = 0.10

→ A

```

Es determinista si el resto de condiciones permanece igual.

----------

# 47. Sampling

En sampling se utiliza la distribución para seleccionar entre diferentes candidatos.

Esto permite generar respuestas diferentes ante la misma entrada.

Aquí aparece:

```text
temperature
top-k
top-p

```

----------

# 48. Temperature

Una formulación simplificada es:

Pi=softmax(zi/T)P_i= softmax(z_i/T)

donde:

```text
T = temperature

```

Cuando:

```text
T < 1

```

la distribución tiende a concentrarse.

Cuando:

```text
T > 1

```

tiende a distribuirse más.

Pero hay que evitar una explicación frecuente y errónea:

```text
temperature baja = inteligente
temperature alta = creativo

```

Eso es una simplificación.

Temperature modifica la distribución de selección; no cambia directamente los conocimientos del modelo ni aumenta su capacidad de razonamiento.

----------

# 49. Top-k

Top-k limita la selección a los k tokens más probables.

Ejemplo:

```text
Top-k = 3

gato   0.50
perro  0.30
casa   0.10
avión  0.04
árbol  0.03
...

```

Solamente se consideran:

```text
gato
perro
casa

```

----------

# 50. Top-p

Top-p, también llamado nucleus sampling, selecciona el conjunto mínimo de tokens cuya probabilidad acumulada alcanza aproximadamente un umbral:

```text
p = 0.90

```

Por ejemplo:

```text
gato     0.50
perro    0.25
casa     0.10
árbol    0.07
avión    0.03
...

```

La selección puede detenerse cuando la suma alcanza aproximadamente:

```text
0.90

```

La ventaja es que el número de candidatos puede variar según la distribución.

----------

# 51. Generación autoregresiva

Supongamos:

```text
Entrada:

"El gato"

```

El modelo genera:

```text
" duerme"

```

Ahora la secuencia es:

```text
"El gato duerme"

```

El modelo genera:

```text
" en"

```

Después:

```text
"El gato duerme en"

```

Y continúa.

Conceptualmente:

```text
X1
 ↓
X1 X2
 ↓
X1 X2 X3
 ↓
X1 X2 X3 X4
 ↓
...

```

Por eso se llama:

> Generación autoregresiva.

----------

# 52. ¿Por qué el modelo puede escribir una respuesta de 500 palabras si genera un token a la vez?

Porque cada nuevo token se convierte en parte del contexto de generación.

Simplificando:

```text
Token 1
   ↓
Token 2
   ↓
Token 3
   ↓
Token 4
   ↓
...

```

Sin embargo, implementaciones modernas utilizan mecanismos como KV cache para no recalcular innecesariamente todo el pasado en cada paso.

----------

# 53. Stop condition

La generación termina cuando ocurre alguna condición.

Por ejemplo:

```text
EOS

```

o:

```text
max_output_tokens

```

o una condición definida por la aplicación.

También puede detenerse porque una API detecta una secuencia de parada configurada.

----------

# 54. Context Window

La ventana de contexto representa la cantidad máxima de información que un modelo puede manejar dentro de una determinada ejecución, según la implementación y el producto.

Puede incluir:

```text
System instructions
+
Prompt
+
Conversación
+
Documentos
+
Resultados de herramientas
+
Tokens generados

```

No debemos confundir:

```text
Context window

```

con:

```text
Memory permanente

```

Son conceptos diferentes.

----------

# 55. Un contexto enorme no garantiza comprensión perfecta

Este punto es fundamental para ingeniería de IA.

Supongamos:

```text
Modelo A
1M tokens

```

Eso no significa:

```text
1M tokens = comprensión perfecta de 1M tokens

```

Una ventana grande aumenta la capacidad de entrada, pero el rendimiento depende de:

-   arquitectura;
    
-   entrenamiento;
    
-   posición de la información;
    
-   relevancia;
    
-   ruido;
    
-   recuperación;
    
-   estructura del prompt;
    
-   tarea;
    
-   distribución de los datos.
    

Por eso:

> Capacidad de contexto ≠ calidad automática sobre todo el contexto.

----------

# 56. Long Context y "Lost in the Middle"

En investigación se ha observado que la información situada en determinadas posiciones dentro de contextos largos puede ser recuperada con menor eficacia que información colocada en posiciones más favorables.

Esto se conoce popularmente como:

> Lost in the Middle.

No debe convertirse en una ley universal.

La importancia práctica es:

```text
1 millón de tokens disponibles

```

no significa que debamos introducir:

```text
1 millón de tokens irrelevantes

```

----------

# 57. Diseño de contexto

Un buen sistema de IA debería priorizar:

```text
Relevancia
   ↓
Estructura
   ↓
Orden
   ↓
Contexto necesario

```

En lugar de:

```text
"mete todo el documento"

```

Por eso aparecen técnicas como:

-   RAG;
    
-   retrieval;
    
-   reranking;
    
-   chunking;
    
-   context compression;
    
-   summarization;
    
-   query rewriting;
    
-   metadata filtering.
    

Estas técnicas se estudiarán en módulos posteriores.

----------

# 58. Razonamiento y Test-Time Compute

Una evolución importante de los modelos modernos es separar:

```text
generación directa

```

de:

```text
procesamiento adicional durante inferencia

```

Algunos modelos pueden utilizar más cómputo para resolver problemas complejos.

Conceptualmente:

```text
Problema
   ↓
generación de posibles pasos
   ↓
evaluación
   ↓
refinamiento
   ↓
respuesta

```

Esto se conoce de forma general como:

> Test-Time Compute.

No significa necesariamente que el modelo "piense como una persona".

Significa que el sistema utiliza recursos computacionales adicionales durante la inferencia.

----------

# 59. Chain-of-Thought

Chain-of-Thought describe el uso de pasos intermedios para ayudar a resolver determinadas tareas.

Una formulación simplificada:

```text
Problema
 ↓
Paso 1
 ↓
Paso 2
 ↓
Paso 3
 ↓
Respuesta

```

Pero es importante una distinción moderna:

> El razonamiento interno de un sistema no debe equipararse automáticamente con una cadena de texto visible para el usuario.

Los modelos y sistemas modernos pueden utilizar procesos internos de razonamiento que no necesariamente se exponen literalmente.

Por ello, en ingeniería de IA debemos distinguir:

```text
razonamiento interno

```

de:

```text
explicación presentada al usuario

```

----------

# 60. GRPO

GRPO, Group Relative Policy Optimization, se hizo especialmente conocido por su uso en investigaciones de razonamiento de DeepSeek.

La intuición simplificada es:

```text
Problema
   ↓
Generar varias respuestas
   ↓
Evaluarlas
   ↓
Compararlas dentro del grupo
   ↓
Reforzar las mejores

```

Una representación conceptual:

```python
respuestas = [
    modelo.producir(problema)
    for _ in range(K)
]

rewards = [
    evaluar(r)
    for r in respuestas
]

media = mean(rewards)

ventajas = [
    r - media
    for r in rewards
]

```

El código anterior es educativo y no representa una implementación completa de GRPO.

----------

# 61. Verificadores

Para tareas donde existe una forma objetiva de comprobar una respuesta, podemos utilizar verificadores.

Ejemplos:

```text
Problema matemático
       ↓
Modelo genera solución
       ↓
Verificador
       ↓
¿Resultado correcto?

```

También podemos verificar:

-   código ejecutable;
    
-   pruebas unitarias;
    
-   ecuaciones;
    
-   restricciones;
    
-   formato;
    
-   reglas de negocio;
    
-   consultas SQL;
    
-   resultados numéricos.
    

Esto es especialmente importante porque:

> Generar una respuesta convincente no demuestra que sea correcta.

----------

# 62. Reasoning no elimina la necesidad de verificación

Un modelo puede utilizar más tokens de razonamiento y aun así equivocarse.

Por eso una arquitectura profesional puede ser:

```text
Modelo
 ↓
Respuesta
 ↓
Verificador
 ↓
¿Correcto?
 ├── Sí → entregar
 └── No → corregir / regenerar

```

Esta idea será fundamental en módulos posteriores sobre:

-   agentes;
    
-   evaluación;
    
-   AI engineering;
    
-   seguridad;
    
-   automatización.
    

----------

# 63. Speculative Decoding

Otra tecnología importante de inferencia es speculative decoding.

La idea general:

```text
Modelo pequeño
      ↓
propone varios tokens
      ↓
Modelo grande
      ↓
verifica / acepta / rechaza

```

Si una parte importante de las propuestas es aceptada, puede reducir la latencia.

Conceptualmente:

```text
Draft model
     ↓
[ t1 t2 t3 t4 ]
     ↓
Target model
     ↓
acepta / rechaza

```

Esto demuestra nuevamente que:

> La velocidad de un LLM no depende únicamente del número de parámetros.

También depende de cómo se realiza la inferencia.

DeepSeek, por ejemplo, incorporó un módulo de speculative decoding en una versión de DeepSeek-V4-Pro publicada en 2026.

----------

# 64. Cuantización

Los modelos pueden utilizar diferentes precisiones numéricas.

Ejemplos:

```text
FP32
FP16
BF16
FP8
INT8
INT4

```

La cuantización intenta representar los pesos con menos bits.

Por ejemplo:

```text
FP16
↓
INT8
↓
INT4

```

Puede reducir:

-   memoria;
    
-   ancho de banda;
    
-   requisitos de hardware.
    

Pero puede introducir pérdida de precisión.

Por tanto:

```text
menos bits ≠ siempre mejor

```

Es un compromiso entre:

```text
calidad
memoria
velocidad
costo

```

Llama 4 Scout, por ejemplo, fue diseñado para poder ejecutarse en un único H100 mediante cuantización Int4 según Meta.

----------

# 65. Inferencia en producción

Hasta ahora hemos visto:

```text
Modelo

```

Pero una aplicación real necesita un sistema de serving.

Podemos tener:

```text
Usuario
   ↓
API
   ↓
Load Balancer
   ↓
Inference Server
   ↓
GPU cluster
   ↓
Modelo

```

Aquí aparecen tecnologías y conceptos como:

-   batching;
    
-   continuous batching;
    
-   KV cache;
    
-   paged attention;
    
-   tensor parallelism;
    
-   pipeline parallelism;
    
-   quantization;
    
-   speculative decoding;
    
-   GPU memory management;
    
-   scheduling;
    
-   autoscaling.
    

Frameworks de serving ampliamente utilizados incluyen proyectos como:

-   vLLM;
    
-   SGLang;
    
-   TensorRT-LLM;
    
-   llama.cpp;
    
-   motores especializados de distintos proveedores.
    

La arquitectura del modelo y la arquitectura de serving son problemas diferentes, aunque estén estrechamente relacionados.

----------

# 66. Prefill vs Decode desde la perspectiva de costos

Podemos pensar:

```text
PREFILL

```

como procesamiento masivo del contexto inicial.

Y:

```text
DECODE

```

como generación autoregresiva.

Esto permite comprender por qué dos solicitudes pueden tener costos y latencias muy diferentes:

### Solicitud A

```text
100 tokens de entrada
20 tokens de salida

```

### Solicitud B

```text
100000 tokens de entrada
20 tokens de salida

```

Aunque ambas generen solamente 20 tokens, el trabajo inicial es completamente diferente.

Esto es especialmente importante en:

-   RAG;
    
-   agentes;
    
-   análisis documental;
    
-   coding agents;
    
-   sistemas con conversaciones largas.
    

----------

# 67. Multimodalidad

Los modelos modernos ya no necesariamente reciben solamente texto.

Pueden procesar:

```text
texto
imagen
audio
video
documentos
código

```

Una arquitectura multimodal puede utilizar diferentes componentes:

```text
Imagen
  ↓
Vision Encoder
  ↓
Representación visual
  ↓
Proyección / integración
  ↓
Modelo multimodal

```

Mientras que:

```text
Texto
 ↓
Tokenizer
 ↓
Text embeddings
 ↓
Modelo

```

Algunas arquitecturas realizan una integración temprana de modalidades.

Llama 4, por ejemplo, utiliza multimodalidad nativa y un enfoque de early fusion para integrar tokens de texto y visión dentro del backbone del modelo.

----------

# 68. No todos los modelos multimodales funcionan igual

No debemos enseñar:

```text
LLM + cámara = modelo multimodal

```

Hay múltiples diseños.

Un sistema puede contener:

```text
Vision encoder
+
LLM
+
Projection layer

```

Otro puede utilizar:

```text
early fusion

```

Otro:

```text
cross-attention

```

Otro puede emplear una arquitectura completamente distinta.

Por eso debemos estudiar multimodalidad como una familia de arquitecturas.

----------

# 69. Pipeline multimodal simplificado

Podemos representar:

```text
                 Entrada
             ┌──────┼──────┐
             ↓      ↓      ↓
           Texto  Imagen  Audio
             ↓      ↓      ↓
         Tokenizer Encoder Encoder
             │      │      │
             └──────┼──────┘
                    ↓
             Representaciones
                    ↓
          Modelo multimodal
                    ↓
                  Logits
                    ↓
              Generación

```

El detalle exacto depende del modelo.

----------

# 70. Pipeline completo moderno

Para una visión de nivel de maestría podemos representar un sistema moderno así:

```text
                    INPUT
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
        Texto       Imagen       Audio
          ↓           ↓           ↓
      Tokenizer    Encoder      Encoder
          │           │           │
          └───────────┼───────────┘
                      ↓
            Representaciones
                      ↓
             Positional info
                      ↓
          ┌─────────────────────┐
          │   Transformer /     │
          │   Hybrid Backbone   │
          │                     │
          │ Attention           │
          │ GQA/MQA/MLA        │
          │ Sparse Attention    │
          │ FFN / MoE           │
          │ Norm                │
          │ Residual            │
          └─────────────────────┘
                      ↓
                  LM Head
                      ↓
                   Logits
                      ↓
            Constraints / masks
                      ↓
              Sampling / Decode
                      ↓
               Output Tokens
                      ↓
              Detokenización
                      ↓
                    Texto

```

Este diagrama representa una familia de sistemas modernos, no una arquitectura única.

----------

# 71. De token a respuesta: ejemplo completo

Supongamos:

```text
Pregunta:

¿Cuál es la capital de Ecuador?

```

## Paso 1

Tokenización:

```text
¿Cuál
es
la
capital
de
Ecuador
?

```

La segmentación real puede ser diferente.

## Paso 2

Los tokens se convierten en IDs.

```text
[...]

```

## Paso 3

Los IDs se transforman en representaciones vectoriales.

## Paso 4

Se incorpora información posicional.

## Paso 5

Las capas Transformer procesan la secuencia.

## Paso 6

La representación final produce logits.

## Paso 7

Los logits se transforman en probabilidades.

## Paso 8

Se selecciona un token.

Por ejemplo:

```text
"Quito"

```

## Paso 9

Ese token se incorpora a la secuencia.

## Paso 10

El proceso continúa hasta terminar.

----------

# 72. ¿Dónde "está" la respuesta?

Esta pregunta es excelente para comprender los LLM.

No debemos pensar:

```text
Base de datos:

"Capital de Ecuador" → "Quito"

```

como si necesariamente existiera una tabla explícita.

El modelo aprendió relaciones estadísticas y representaciones distribuidas durante el entrenamiento.

Cuando recibe:

```text
¿Cuál es la capital de Ecuador?

```

su estado interno produce una distribución de posibles continuaciones.

Una de ellas puede ser:

```text
Quito

```

Por eso:

> Un LLM no debe confundirse con una base de datos tradicional.

----------

# 73. Pretraining

Hasta ahora hemos hablado de inferencia.

Pero ¿cómo aprendió el modelo?

Durante pretraining, de manera simplificada, se presentan grandes cantidades de datos y el modelo aprende a predecir tokens.

Ejemplo:

```text
El gato duerme en el ___

```

El objetivo puede ser aprender una distribución donde:

```text
cama
suelo
sofá
...

```

tengan determinadas probabilidades.

El proceso se repite a una escala enorme.

----------

# 74. Objetivo autoregresivo

Para muchos modelos de lenguaje causales:

P(x1,x2,…,xn)=∏t=1nP(xt∣x<t)P(x_1,x_2,\ldots,x_n) = \prod_{t=1}^{n} P(x_t|x_{<t})

Esta ecuación es fundamental.

Dice que la probabilidad de una secuencia puede factorizarse como producto de probabilidades condicionales.

Por eso el modelo aprende:

```text
P(token siguiente | tokens anteriores)

```

----------

# 75. Loss

Durante entrenamiento se utiliza una función de pérdida.

Una de las más importantes es la entropía cruzada.

Simplificando:

L=−∑iyilog⁡(pi)L=-\sum_i y_i\log(p_i)

donde:

```text
y = distribución objetivo
p = distribución predicha

```

El entrenamiento intenta reducir esta pérdida mediante optimización.

----------

# 76. Backpropagation

Durante entrenamiento:

```text
Datos
 ↓
Forward pass
 ↓
Predicción
 ↓
Loss
 ↓
Backpropagation
 ↓
Gradientes
 ↓
Actualización de parámetros

```

Durante inferencia, normalmente no se actualizan los pesos.

Esto es una distinción fundamental:

```text
Training ≠ Inference

```

----------

# 77. Post-training

Después del pretraining, un modelo puede recibir diferentes procesos de post-training.

Entre ellos:

```text
SFT
Preference optimization
RL
Reasoning training
Tool-use training
Safety training
Distillation

```

El objetivo puede ser mejorar:

-   seguimiento de instrucciones;
    
-   conversación;
    
-   código;
    
-   razonamiento;
    
-   uso de herramientas;
    
-   seguridad;
    
-   comportamiento específico.
    

----------

# 78. SFT

Supervised Fine-Tuning:

```text
Entrada
+
Respuesta deseada

```

El modelo aprende a producir respuestas similares a los ejemplos proporcionados.

Ejemplo:

```text
Usuario:
Resume este texto.

Respuesta objetivo:
El texto explica...

```

Miles o millones de ejemplos pueden utilizarse en distintos procesos de entrenamiento.

----------

# 79. Preference Optimization

Otra familia de técnicas intenta enseñar preferencias.

Conceptualmente:

```text
Respuesta A
Respuesta B

Humano / evaluador:
A es preferible a B

```

El entrenamiento intenta aumentar la probabilidad de respuestas que cumplen las preferencias deseadas.

Aquí aparecen métodos como:

-   RLHF;
    
-   DPO;
    
-   variantes de preference optimization.
    

----------

# 80. Distillation

Un modelo grande puede actuar como profesor.

```text
Teacher
   ↓
genera conocimiento / señales
   ↓
Student
   ↓
modelo más pequeño

```

Esto puede utilizarse para reducir tamaño o costo manteniendo parte del comportamiento.

Llama 4, por ejemplo, documenta el uso de un modelo Behemoth como teacher para modelos más pequeños mediante procesos de destilación.

----------

# 81. El número de parámetros no cuenta toda la historia

Es un error pensar:

```text
500B > 100B

```

por lo tanto:

```text
500B siempre mejor

```

Hay que considerar:

-   parámetros totales;
    
-   parámetros activos;
    
-   arquitectura;
    
-   datos;
    
-   entrenamiento;
    
-   post-training;
    
-   contexto;
    
-   razonamiento;
    
-   herramientas;
    
-   cuantización;
    
-   serving;
    
-   objetivo de la tarea.
    

En MoE esta distinción es especialmente importante.

----------

# 82. Estado tecnológico: septiembre de 2026

Para este repositorio debemos utilizar los modelos como ejemplos históricos y técnicos, no como una clasificación de "mejor modelo".

El panorama de septiembre de 2026 incluye, entre otros:

Familia

Característica relevante

GPT-5.6

Modelos propietarios con variantes orientadas a diferentes niveles de razonamiento y costo

Gemini

Modelos multimodales con contexto extenso

Claude

Modelos propietarios orientados a lenguaje, código y razonamiento

Llama 4

MoE multimodal con contextos extremadamente largos

Qwen3

Familia Dense y MoE con diferentes escalas

DeepSeek-V4/V4.1

MoE, long-context, atención optimizada y nuevas estrategias de inferencia

Mistral

Familias Dense, MoE, multimodales, reasoning y modelos especializados

Otros modelos abiertos

GLM, Kimi, Gemma y múltiples familias especializadas

Como ejemplo de evolución, la documentación de OpenAI disponible en septiembre de 2026 registra GPT-5.6 con una ventana de contexto de 1.05 millones de tokens y hasta 128K tokens de salida.

No debemos convertir estos datos en un ranking porque:

```text
modelo más grande
≠
modelo universalmente mejor

```

La selección debe depender de la tarea.

----------

# 83. Tabla técnica de referencia

Una tabla útil para estudiar arquitectura sería:

Concepto

Pregunta que responde

Tokenizer

¿Cómo se divide la entrada?

Token ID

¿Cómo identifica el modelo cada unidad?

Embedding

¿Cómo se representa inicialmente?

Positional encoding

¿Cómo representa el orden?

Attention

¿Cómo interactúan las posiciones?

GQA/MQA

¿Cómo se reducen costos de K/V?

MLA

¿Cómo se comprime/reorganiza la información de atención?

Sparse Attention

¿Cómo reducir el costo de contexto largo?

FFN

¿Cómo se transforma cada representación?

MoE

¿Qué subredes procesan cada token?

Normalization

¿Cómo estabilizar las representaciones?

Residual

¿Cómo transportar información entre capas?

Logits

¿Qué puntuaciones asigna al vocabulario?

Softmax

¿Cómo obtener una distribución?

Sampling

¿Cómo seleccionar el siguiente token?

KV Cache

¿Cómo evitar recomputación durante decode?

Quantization

¿Cómo reducir memoria y costo?

Speculative decoding

¿Cómo acelerar generación?

RAG

¿Cómo incorporar conocimiento externo?

Tool use

¿Cómo conectar el modelo con sistemas externos?

Verifier

¿Cómo comprobar resultados?

----------

# 84. Una distinción fundamental: modelo vs sistema de IA

Un LLM no es necesariamente una aplicación completa.

Podemos tener:

```text
LLM

```

pero una aplicación empresarial puede ser:

```text
Usuario
   ↓
Frontend
   ↓
Backend
   ↓
Prompt builder
   ↓
RAG
   ↓
Retriever
   ↓
Reranker
   ↓
LLM
   ↓
Tool
   ↓
Verifier
   ↓
Database
   ↓
Respuesta

```

Por eso:

> Ingeniería de prompts no es lo mismo que ingeniería de sistemas de IA.

El prompt es solamente una parte del sistema.

----------

# 85. El pipeline de una aplicación moderna

Para un sistema profesional podemos tener:

```text
                    USUARIO
                       │
                       ▼
                  INPUT
                       │
                       ▼
              Validación / Safety
                       │
                       ▼
              Construcción contexto
                       │
             ┌─────────┴─────────┐
             ↓                   ↓
           RAG                 Tools
             ↓                   ↓
         Retrieval           APIs / DB
             │                   │
             └─────────┬─────────┘
                       ↓
                    PROMPT
                       ↓
                    LLM
                       ↓
                 Verificación
                       ↓
                Post-procesado
                       ↓
                    OUTPUT

```

Esta arquitectura será mucho más importante para un ingeniero de IA que memorizar solamente la fórmula de atención.

----------

# 86. Errores conceptuales que debemos evitar

## Error 1

"Un token es una palabra."

Incorrecto.

```text
Token ≠ palabra necesariamente

```

## Error 2

"Todos los LLM utilizan BPE."

Incorrecto.

Existen diferentes tokenizadores y arquitecturas.

## Error 3

"El embedding contiene directamente el significado."

Demasiado simplificado.

Es una representación matemática aprendida.

## Error 4

"Cada dimensión del embedding tiene un significado."

Incorrecto como regla general.

## Error 5

"Attention significa que el modelo mira palabras como una persona."

Es una analogía, no una descripción literal.

## Error 6

"Cada cabeza tiene una función fija."

No necesariamente.

## Error 7

"MoE significa que existe un experto médico."

No.

## Error 8

"Más parámetros siempre significa mejor."

Incorrecto.

## Error 9

"Más contexto siempre significa mejor respuesta."

Incorrecto.

## Error 10

"Temperature alta hace al modelo más inteligente."

Incorrecto.

## Error 11

"Reasoning garantiza una respuesta correcta."

Incorrecto.

## Error 12

"Un modelo con contexto de 10M comprende perfectamente 10M tokens."

No necesariamente.

## Error 13

"LLM = base de datos."

Incorrecto.

## Error 14

"El modelo genera la respuesta completa de una vez."

En generación autoregresiva, produce tokens progresivamente.

----------

# 87. Laboratorio conceptual 1: tokenización

Instalar:

```bash
pip install tiktoken

```

Ejemplo:

```python
import tiktoken

enc = tiktoken.get_encoding("o200k_base")

texto = "El gato duerme."

tokens = enc.encode(texto)

print("Texto:", texto)
print("IDs:", tokens)
print("Cantidad:", len(tokens))

for token_id in tokens:
    print(token_id, enc.decode([token_id]))

```

No debemos asumir que este tokenizer representa todos los modelos.

El objetivo del ejercicio es observar que:

```text
texto
 ↓
tokens
 ↓
IDs

```

depende del tokenizer.

----------

# 88. Laboratorio conceptual 2: visualizar embeddings

Podemos utilizar un modelo de embeddings y reducir dimensionalidad con PCA.

Conceptualmente:

```python
from sklearn.decomposition import PCA

vectors = ...

pca = PCA(n_components=2)

points = pca.fit_transform(vectors)

```

Después podemos visualizar:

```text
          gato
            *
       * animal
     perro

                 vehículo
                    *
                 carro

```

Pero debemos recordar:

> La visualización en dos dimensiones es una proyección. No representa literalmente todas las dimensiones del embedding.

----------

# 89. Laboratorio conceptual 3: Softmax

```python
import numpy as np

logits = np.array([5.2, 4.7, 1.8, -0.4])

exp_logits = np.exp(logits - np.max(logits))
probabilidades = exp_logits / exp_logits.sum()

print(probabilidades)
print(probabilidades.sum())

```

El resultado permite observar:

```text
logits
  ↓
softmax
  ↓
probabilidades

```

----------

# 90. Laboratorio conceptual 4: Temperature

```python
import numpy as np

logits = np.array([5.2, 4.7, 1.8, -0.4])

def softmax_temperature(logits, temperature):
    scaled = logits / temperature
    exp_values = np.exp(scaled - np.max(scaled))
    return exp_values / exp_values.sum()

for temperature in [0.5, 1.0, 2.0]:
    print(
        temperature,
        softmax_temperature(logits, temperature)
    )

```

Observa cómo cambia la distribución.

La finalidad del ejercicio no es demostrar que:

```text
0.5 = mejor
2.0 = peor

```

sino comprender matemáticamente cómo cambia la distribución.

----------

# 91. Laboratorio conceptual 5: atención simplificada

Construye una pequeña implementación educativa:

```python
import numpy as np

Q = np.random.randn(3, 4)
K = np.random.randn(3, 4)
V = np.random.randn(3, 4)

scores = Q @ K.T

scores = scores / np.sqrt(K.shape[-1])

weights = np.exp(scores - scores.max(axis=-1, keepdims=True))
weights = weights / weights.sum(axis=-1, keepdims=True)

output = weights @ V

print("Scores:")
print(scores)

print("\nAttention weights:")
print(weights)

print("\nOutput:")
print(output)

```

Esto permite visualizar directamente:

```text
Q
K
V
 ↓
QKᵀ
 ↓
scale
 ↓
softmax
 ↓
weights
 ↓
weights × V

```

----------

# 92. Ejercicio de ingeniería

Pregunta:

Tenemos un sistema que recibe:

```text
100 documentos

```

cada uno con:

```text
10.000 tokens

```

Tenemos:

```text
1.000.000 tokens

```

de información.

El modelo admite:

```text
1.000.000 tokens

```

¿Debemos enviar automáticamente los 100 documentos completos?

No necesariamente.

Debemos preguntarnos:

1.  ¿Todos son relevantes?
    
2.  ¿Hay información duplicada?
    
3.  ¿Cuál contiene la evidencia necesaria?
    
4.  ¿Podemos recuperar solamente las partes relevantes?
    
5.  ¿Existe información contradictoria?
    
6.  ¿El modelo mantiene buen rendimiento con ese volumen?
    
7.  ¿Cuánto cuesta procesarlo?
    
8.  ¿Cuánto tarda?
    
9.  ¿Qué ocurre si la información relevante está enterrada entre datos irrelevantes?
    

Aquí empieza la diferencia entre:

```text
usar un LLM

```

y:

```text
diseñar un sistema con LLM

```

----------

# 93. Ejercicio de arquitectura

Compara conceptualmente:

### Sistema A

```text
Prompt enorme
↓
LLM
↓
Respuesta

```

### Sistema B

```text
Pregunta
↓
Retriever
↓
Documentos relevantes
↓
Reranker
↓
Contexto seleccionado
↓
LLM
↓
Verifier
↓
Respuesta

```

¿Cuál tiene más componentes?

El sistema B.

¿Significa automáticamente que será mejor?

No.

Significa que utiliza una arquitectura más compleja para controlar el flujo de información.

La calidad debe demostrarse mediante evaluación.

----------

# 94. Ejercicio de MoE

Supongamos:

```text
8 expertos

```

y cada token utiliza:

```text
2 expertos

```

Entonces podemos tener:

```text
Token 1 → Expertos 2 y 7
Token 2 → Expertos 1 y 4
Token 3 → Expertos 2 y 5
Token 4 → Expertos 3 y 8

```

Observa:

```text
No todos los expertos procesan todos los tokens.

```

Ahora analiza:

-   ¿qué ocurre si todos los tokens son enviados al mismo experto?
    
-   ¿qué ocurre si un experto recibe demasiados tokens?
    
-   ¿cómo afecta esto al balance?
    
-   ¿qué ocurre cuando el modelo está distribuido entre múltiples GPUs?
    

Estas preguntas llevan directamente al estudio avanzado de MoE.

----------

# 95. Nivel de maestría: preguntas que debes poder responder

Al terminar este módulo deberías poder explicar, sin memorizar una definición:

### Tokenización

¿Por qué dos modelos pueden tokenizar la misma frase de forma diferente?

### Embeddings

¿Por qué el vector inicial de un token no es todavía una representación contextual completa?

### Posición

¿Por qué un Transformer necesita información posicional?

### Attention

¿Por qué aparece:

QKTQK^T

en la fórmula?

### Scaling

¿Por qué dividimos por:

dk\sqrt{d_k}

?

### GQA

¿Por qué reducir el número de cabezas K/V puede disminuir el costo de memoria?

### KV Cache

¿Por qué es especialmente importante durante decode?

### MoE

¿Por qué un modelo puede tener cientos de miles de millones de parámetros y activar solamente una fracción por token?

### Long Context

¿Por qué una ventana mayor no garantiza mejor recuperación?

### Sampling

¿Por qué temperature no aumenta el conocimiento del modelo?

### Quantization

¿Por qué reducir precisión puede disminuir memoria?

### Speculative Decoding

¿Por qué un modelo pequeño puede ayudar a acelerar uno grande?

### Reasoning

¿Por qué aumentar el cómputo de inferencia puede mejorar determinadas tareas sin modificar necesariamente los pesos?

----------

# 96. Mapa conceptual final

```text
                    LLM
                     │
          ┌──────────┴──────────┐
          │                     │
       ENTRADA              ENTRENAMIENTO
          │                     │
      Tokenizer              Datos
          │                     │
        IDs                  Pretraining
          │                     │
      Embedding              Loss
          │                     │
      Posición              Gradients
          │                     │
          └──────┐        Post-training
                 │             │
                 ▼             │
             TRANSFORMER ◄─────┘
                 │
       ┌─────────┼─────────┐
       │         │         │
   Attention    FFN       MoE
       │         │         │
    MHA/GQA    SwiGLU    Router
    MQA/MLA               Experts
       │
       ▼
    KV Cache
       │
       ▼
     Logits
       │
       ▼
    Softmax
       │
       ▼
   Sampling
       │
       ▼
    Decode
       │
       ▼
    Output

```

----------

# 97. Lo que debes recordar

1.  Un token no es necesariamente una palabra.
    
2.  El tokenizer depende del modelo.
    
3.  Token ID es un identificador, no un significado.
    
4.  Los embeddings son representaciones matemáticas aprendidas.
    
5.  Las representaciones se vuelven contextuales dentro del Transformer.
    
6.  El orden necesita algún mecanismo posicional.
    
7.  RoPE es una de las técnicas posicionales importantes en LLM modernos.
    
8.  Attention utiliza Q, K y V para transformar representaciones según relaciones entre posiciones.
    
9.  MHA, MQA y GQA son diferentes estrategias de atención.
    
10.  KV cache es fundamental para la generación eficiente.
    
11.  FFN transforma las representaciones después de la atención.
    
12.  MoE permite aumentar capacidad activando solamente una parte de los expertos por token.
    
13.  Un experto no tiene necesariamente una especialidad humana explícita.
    
14.  Los logits son scores, no probabilidades.
    
15.  Softmax transforma logits en una distribución.
    
16.  Sampling determina cómo se seleccionan tokens.
    
17.  Temperature modifica la distribución de selección, no la inteligencia del modelo.
    
18.  La generación autoregresiva produce tokens progresivamente.
    
19.  Prefill y decode tienen características computacionales diferentes.
    
20.  Quantization permite reducir memoria y costo, con posibles compromisos de calidad.
    
21.  Speculative decoding puede acelerar la generación.
    
22.  Una ventana de contexto grande no garantiza comprensión perfecta.
    
23.  Reasoning y generación de texto visible no son necesariamente lo mismo.
    
24.  Un LLM no es una base de datos tradicional.
    
25.  El número de parámetros no determina por sí solo la calidad.
    
26.  El modelo es solamente una parte de un sistema de IA.
    
27.  RAG, herramientas, verificadores y serving forman parte de la ingeniería moderna de IA.
    
28.  Para comprender los LLM de 2026 debemos conocer Dense, MoE, GQA, MQA, MLA, sparse attention, long-context, KV cache, multimodalidad y técnicas modernas de inferencia.
    

----------

# 98. Conexión con el siguiente módulo

Ahora conocemos el recorrido general:

```text
Entrada
 ↓
Tokenización
 ↓
IDs
 ↓
Embeddings
 ↓
Posición
 ↓
Attention
 ↓
FFN / MoE
 ↓
Logits
 ↓
Probabilidades
 ↓
Sampling
 ↓
Token
 ↓
Repetición

```

El siguiente módulo debe comenzar a utilizar este conocimiento para estudiar:

# 03. Técnicas Fundamentales de Prompting

Donde conectaremos directamente la arquitectura del modelo con:

```text
Zero-shot
Few-shot
Role prompting
Instruction prompting
Constraints
Structured output
Prompt decomposition
Context engineering

```

Después podremos avanzar hacia:

```text
04. Técnicas Avanzadas de Razonamiento
05. Context Engineering
06. RAG
07. Agentes
08. Evaluación de LLMs
09. Seguridad de LLMs
10. Optimización y producción

```

La progresión correcta es:

```text
Fundamentos
     ↓
Arquitectura
     ↓
Prompting
     ↓
Context Engineering
     ↓
RAG
     ↓
Reasoning
     ↓
Agents
     ↓
Evaluation
     ↓
Security
     ↓
Production AI Engineering

```

Ese recorrido permite pasar de:

> "Sé escribir prompts"

a:

> "Comprendo cómo funciona el modelo, puedo diseñar el contexto que recibe, puedo evaluar sus resultados y puedo construir un sistema de IA alrededor de él."

----------

## Nota de actualización tecnológica

Este módulo debe conservar una separación estricta entre **fundamentos que cambian lentamente** y **ejemplos tecnológicos que cambian rápidamente**. Para los ejemplos de 2026 se han utilizado fuentes técnicas de los propios desarrolladores cuando están disponibles: DeepSeek documenta V4/V4.1, Qwen documenta Qwen3 y Meta documenta Llama 4; además, la documentación actual de OpenAI muestra la familia GPT-5.6 y sus capacidades de contexto.

Por esta razón, **no conviene convertir este módulo en un catálogo de modelos**. Los nombres, ventanas de contexto, precios y arquitecturas pueden cambiar. Lo que debe permanecer en el repositorio es el conocimiento transferible: **cómo funciona el pipeline, qué problema resuelve cada componente y cómo evaluar las diferencias entre arquitecturas**.