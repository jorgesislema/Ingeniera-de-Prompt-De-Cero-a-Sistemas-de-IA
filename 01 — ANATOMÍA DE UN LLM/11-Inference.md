# 11. Inferencia en un modelo de IA

> **Nivel:** Fundamentos → Intermedio → Avanzado → Maestría/PhD
> **Área:** LLM, Deep Learning, Transformers, Generación de texto
> **Prerrequisitos:** Tokens, embeddings, Transformer, attention, pretraining, instruction tuning y alignment
> **Objetivo:** Comprender qué ocurre cuando un modelo recibe un prompt, procesa el contexto, calcula probabilidades y genera una respuesta token por token.

---

# 1. ¿Qué es la inferencia?

En Inteligencia Artificial, **inferencia (inference)** es el proceso mediante el cual un modelo ya entrenado recibe una entrada y produce una salida.

La diferencia fundamental es:

```text
ENTRENAMIENTO
────────────────────────
Datos
   ↓
Predicción
   ↓
Error
   ↓
Gradientes
   ↓
Actualización de parámetros
```

Mientras que:

```text
INFERENCIA
────────────────────────
Entrada
   ↓
Modelo entrenado
   ↓
Predicción
   ↓
Salida
```

Durante inferencia, normalmente **no estamos entrenando el modelo**.

Los parámetros ya fueron aprendidos.

---

# 2. Una analogía sencilla

Imagina a un estudiante que ya terminó de estudiar.

Durante el entrenamiento:

```text
Estudia
   ↓
Practica
   ↓
Comete errores
   ↓
Corrige
   ↓
Aprende
```

Durante un examen:

```text
Pregunta
   ↓
Utiliza lo aprendido
   ↓
Responde
```

El examen sería comparable, de forma simplificada, con la inferencia.

El estudiante no está necesariamente modificando su conocimiento fundamental mientras responde cada pregunta.

De manera similar:

> Durante la inferencia, un LLM utiliza sus parámetros aprendidos para procesar una entrada y generar una salida.

---

# 3. Entrenamiento vs inferencia

Esta diferencia es fundamental para Ingeniería de Prompt.

| Característica | Entrenamiento                   | Inferencia                         |
| -------------- | ------------------------------- | ---------------------------------- |
| Datos          | Grandes conjuntos               | Entrada actual                     |
| Parámetros     | Se modifican                    | Normalmente permanecen fijos       |
| Gradientes     | Sí                              | Normalmente no                     |
| Loss           | Sí                              | No como mecanismo de actualización |
| Objetivo       | Aprender                        | Generar/predicir                   |
| Coste          | Muy alto                        | Menor por consulta                 |
| Prompt         | Puede formar parte de los datos | Es entrada directa                 |
| Resultado      | Modelo actualizado              | Predicción/respuesta               |

Podemos resumirlo:

```text
TRAINING
→ cambia el modelo

INFERENCE
→ utiliza el modelo
```

---

# 4. El modelo que recibe el prompt

Supongamos que escribimos:

```text
¿Qué es la inteligencia artificial?
```

El modelo no recibe directamente una frase como nosotros la vemos.

El sistema realiza una serie de transformaciones.

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
Representaciones
  ↓
Logits
  ↓
Probabilidades
  ↓
Decoding / Sampling
  ↓
Token siguiente
```

Y el proceso se repite.

---

# 5. La visión completa

Una generación autoregresiva puede representarse:

```text
PROMPT
  ↓
TOKENIZACIÓN
  ↓
TOKENS
  ↓
EMBEDDINGS
  ↓
TRANSFORMER
  ↓
LOGITS
  ↓
SOFTMAX
  ↓
PROBABILIDADES
  ↓
DECODING
  ↓
SIGUIENTE TOKEN
  ↓
SE AGREGA AL CONTEXTO
  ↓
TRANSFORMER
  ↓
LOGITS
  ↓
SOFTMAX
  ↓
...
```

Esto continúa hasta producir:

```text
EOS / STOP
```

o alcanzar algún límite de generación.

---

# 6. ¿Qué significa autoregresivo?

Un modelo autoregresivo genera cada elemento utilizando los elementos anteriores.

Para lenguaje:

$$
x_1, x_2, x_3, ..., x_n
$$

la probabilidad conjunta puede expresarse como:

$$
P(x_1,x_2,\dots,x_n)
=
\prod_{t=1}^{n}P(x_t|x_1,\dots,x_{t-1})
$$

Esto significa:

```text
Token 1
   ↓
Token 2 depende del anterior
   ↓
Token 3 depende de los anteriores
   ↓
Token 4 depende de los anteriores
   ↓
...
```

Por eso un LLM generativo no produce necesariamente toda la respuesta como un único bloque matemático.

La generación ocurre de forma **autoregresiva**, aunque internamente existen optimizaciones importantes para hacerla eficiente.

---

# 7. Ejemplo sencillo

Supongamos que el contexto es:

```text
El cielo es
```

El modelo puede calcular una distribución aproximada:

```text
azul       0.70
grande     0.08
claro      0.06
hermoso    0.04
...
```

Si selecciona:

```text
azul
```

ahora el contexto pasa a ser:

```text
El cielo es azul
```

Y se calcula nuevamente:

```text
y          0.30
durante    0.12
en         0.08
.          0.07
...
```

Se selecciona otro token.

El proceso continúa:

```text
El
 ↓
El cielo
 ↓
El cielo es
 ↓
El cielo es azul
 ↓
El cielo es azul y
 ↓
...
```

---

# 8. El modelo no "escribe" como una persona

Esta diferencia conceptual es importante.

Un LLM no necesita tener una frase completa almacenada en alguna parte para comenzar a generarla.

En términos simplificados, calcula:

$$
P(\text{token siguiente}|\text{contexto})
$$

Después selecciona un token.

Luego vuelve a calcular:

$$
P(\text{nuevo token}|\text{contexto actualizado})
$$

Por eso:

> La generación puede entenderse como una secuencia de decisiones probabilísticas condicionadas por el contexto.

---

# 9. Primer paso: tokenización

El texto:

```text
La inteligencia artificial transforma datos.
```

se convierte mediante el tokenizer en unidades que pueden ser:

* palabras;
* fragmentos de palabras;
* caracteres;
* símbolos;
* espacios codificados;
* signos de puntuación.

Por ejemplo, de manera ilustrativa:

```text
"La"
" inteligencia"
" artificial"
" transforma"
" datos"
"."
```

Los tokens reales dependen del tokenizer.

Después cada token se convierte en un identificador numérico:

```text
"La"          → 512
" inteligencia" → 8421
" artificial" → 1937
...
```

Los números son únicamente identificadores.

No significa que:

```text
8421 > 1937
```

implique que "inteligencia" sea más importante que "artificial".

---

# 10. Segundo paso: embeddings

Los token IDs se convierten en vectores.

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
Token:
"inteligencia"

ID:
8421

Embedding:
[
  0.12,
 -0.41,
  0.73,
  ...
]
```

En un modelo real, el vector tiene muchas dimensiones.

La representación inicial puede expresarse como:

$$
E_i \in \mathbb{R}^{d}
$$

donde \(d\) es la dimensión del embedding.

---

# 11. Información de posición

El modelo también necesita representar la posición de los tokens.

Porque:

```text
El perro mordió al hombre.
```

no significa necesariamente lo mismo que:

```text
El hombre mordió al perro.
```

El modelo necesita información que permita diferenciar el orden.

Dependiendo de la arquitectura, pueden utilizarse mecanismos como:

* positional embeddings;
* RoPE;
* ALiBi;
* otros mecanismos posicionales.

La representación pasa conceptualmente de:

```text
TOKEN
+
POSICIÓN
```

hacia:

```text
REPRESENTACIÓN PARA EL TRANSFORMER
```

---

# 12. Tercer paso: Transformer

Ahora las representaciones atraviesan las capas del Transformer.

Una representación simplificada:

```text
Embeddings
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
```

Cada capa transforma las representaciones.

Dentro de una capa suelen aparecer componentes como:

```text
Input
 ↓
Normalization
 ↓
Attention
 ↓
Residual
 ↓
Normalization
 ↓
MLP / Feed Forward
 ↓
Residual
 ↓
Output
```

La implementación exacta depende de la arquitectura.

---

# 13. ¿Qué hace Attention durante inferencia?

Attention permite que una posición considere información de otras posiciones relevantes.

Supongamos:

```text
El banco cerró porque estaba inundado.
```

Para interpretar correctamente:

```text
banco
```

el contexto:

```text
inundado
```

puede ser relevante.

Attention permite establecer relaciones entre representaciones.

Conceptualmente:

```text
TOKEN ACTUAL
     │
     ├── ¿Qué otros tokens son relevantes?
     │
     ├── ¿Cuánto debería considerar cada uno?
     │
     └── Combinar información
```

---

# 14. Causal Attention

En un modelo autoregresivo, existe una restricción importante:

> Al predecir un token, el modelo no debe utilizar tokens futuros que todavía no existen.

Por ejemplo:

```text
El perro
```

no debería utilizar:

```text
corrió rápidamente
```

si esos tokens todavía no fueron generados.

Esto se implementa mediante una **causal mask** o mecanismo equivalente.

Conceptualmente:

```text
Token 1 → puede ver 1
Token 2 → puede ver 1,2
Token 3 → puede ver 1,2,3
Token 4 → puede ver 1,2,3,4
```

No:

```text
Token 1 → puede ver 1,2,3,4
```

en una generación causal estándar.

---

# 15. La matriz de atención

Para una secuencia:

```text
A B C D
```

una máscara causal puede visualizarse:

```text
      A B C D
A     ✓ ✗ ✗ ✗
B     ✓ ✓ ✗ ✗
C     ✓ ✓ ✓ ✗
D     ✓ ✓ ✓ ✓
```

La diagonal y la parte inferior son accesibles.

La parte superior queda bloqueada.

Esto mantiene la propiedad autoregresiva.

---

# 16. ¿Qué ocurre después de Attention?

La información continúa propagándose por la red.

Una capa Transformer puede representarse de forma simplificada:

```text
                ┌───────────────┐
                │    INPUT      │
                └───────┬───────┘
                        ↓
                Normalización
                        ↓
                  Self-Attention
                        ↓
                   Residual
                        ↓
                Normalización
                        ↓
                      MLP
                        ↓
                   Residual
                        ↓
                     OUTPUT
```

Este proceso se repite muchas veces.

---

# 17. Representaciones internas

A medida que la información atraviesa las capas, las representaciones se transforman.

Podemos pensar:

```text
Embedding
   ↓
Representación 1
   ↓
Representación 2
   ↓
Representación 3
   ↓
...
   ↓
Representación profunda
```

Estas representaciones no deben interpretarse como una lista simple de conceptos humanos.

No podemos asumir:

```text
dimensión 1 = inteligencia
dimensión 2 = género
dimensión 3 = tiempo
```

La información está distribuida y puede estar codificada mediante patrones complejos.

---

# 18. Logits

Después de procesar el contexto, el modelo necesita producir una puntuación para cada token posible.

Estas puntuaciones se llaman:

> **logits**

Supongamos un vocabulario pequeño:

```text
[
  "azul",
  "verde",
  "rojo",
  "grande",
  "..."
]
```

El modelo podría producir:

```text
azul     →  4.8
verde    →  2.1
rojo     →  1.2
grande   → -0.5
...
```

Estos valores **no son probabilidades**.

Son puntuaciones sin normalizar.

---

# 19. ¿Qué son los logits matemáticamente?

Si la representación final es:

$$
h \in \mathbb{R}^{d}
$$

y la matriz de salida es:

$$
W \in \mathbb{R}^{V \times d}
$$

entonces podemos representar:

$$
z = Wh + b
$$

donde:

* \(h\) = representación final;
* \(W\) = matriz de salida;
* \(b\) = sesgo;
* \(V\) = tamaño del vocabulario;
* \(z\) = vector de logits.

Por tanto:

$$
z \in \mathbb{R}^{V}
$$

Existe una puntuación para cada token del vocabulario.

---

# 20. Logit no significa probabilidad

Supongamos:

```text
azul    → 8.2
verde   → 5.1
rojo    → 2.4
```

No podemos decir:

```text
azul = 8.2%
```

Eso sería incorrecto.

Primero necesitamos convertir los logits en una distribución de probabilidades.

---

# 21. Softmax

La función Softmax transforma logits en probabilidades.

La fórmula es:

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

donde:

* \(z_i\) = logit del token \(i\);
* \(e\) = número de Euler;
* el denominador suma las exponenciales de todos los logits.

El resultado cumple:

$$
0 \leq P_i \leq 1
$$

y:

$$
\sum_i P_i = 1
$$

---

# 22. Ejemplo

Supongamos:

```text
azul    → 3
verde   → 2
rojo    → 1
```

Después de Softmax podríamos obtener aproximadamente:

```text
azul    → 0.665
verde   → 0.245
rojo    → 0.090
```

Ahora sí tenemos una distribución.

```text
azul  ███████████████████████████
verde ██████████
rojo  ███
```

La palabra con mayor probabilidad es:

```text
azul
```

Pero eso no significa necesariamente que siempre se seleccione azul.

Ahí entra el **decoding**.

---

# 23. Probabilidad condicional

Para un LLM, podemos representar:

$$
P(x_{t+1}|x_1,\dots,x_t)
$$

como:

> La probabilidad del siguiente token dado todo el contexto disponible.

Esta ecuación es una de las ideas más importantes para comprender los LLM.

```text
Contexto
   ↓
P(siguiente token | contexto)
   ↓
Distribución
```

---

# 24. El vocabulario completo

Un modelo puede tener un vocabulario de decenas de miles de tokens o más.

Si:

$$
V = 100000
$$

el modelo puede producir conceptualmente:

```text
logits[0]
logits[1]
logits[2]
...
logits[99999]
```

Cada posición corresponde a un token del vocabulario.

Después:

```text
100000 logits
      ↓
Softmax
      ↓
100000 probabilidades
```

---

# 25. ¿Cómo se elige el siguiente token?

Aquí entra el **decoding**.

El modelo genera una distribución:

```text
Token A → 0.40
Token B → 0.25
Token C → 0.15
Token D → 0.10
Token E → 0.10
```

Ahora necesitamos seleccionar un token.

Existen diferentes estrategias.

---

# 26. Greedy Decoding

La estrategia más sencilla es:

> seleccionar siempre el token con mayor probabilidad.

Ejemplo:

```text
A → 0.40
B → 0.25
C → 0.15
D → 0.10
E → 0.10
```

Se selecciona:

```text
A
```

Después se vuelve a calcular la distribución.

Ventaja:

```text
Simple
```

Problema:

```text
Puede producir respuestas repetitivas
o poco diversas.
```

---

# 27. Sampling

En lugar de elegir siempre el token más probable, podemos **muestrear** de la distribución.

Por ejemplo:

```text
A → 0.40
B → 0.25
C → 0.15
D → 0.10
E → 0.10
```

A tiene mayor probabilidad.

Pero B o C también podrían ser seleccionados.

Esto introduce variabilidad.

---

# 28. Temperature

La temperatura modifica la distribución antes del sampling.

Una forma conceptual es:

$$
P_i =
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

donde \(T\) es la temperatura.

### Temperatura baja

La distribución se concentra.

```text
A → 0.80
B → 0.12
C → 0.05
D → 0.03
```

### Temperatura alta

La distribución se vuelve más plana.

```text
A → 0.35
B → 0.27
C → 0.20
D → 0.18
```

No significa simplemente:

```text
temperature alta = creatividad
temperature baja = inteligencia
```

La temperatura modifica la distribución de selección.

---

# 29. Temperature no cambia los parámetros del modelo

Este punto es fundamental.

Cambiar:

```text
temperature = 0.2
```

no significa que el modelo se haya reentrenado.

Los parámetros siguen siendo los mismos.

Lo que cambia es:

```text
DISTRIBUCIÓN / DECODING
```

durante inferencia.

Por tanto:

```text
Temperature
≠
Fine-Tuning
≠
Training
```

---

# 30. Top-k Sampling

Otra estrategia consiste en conservar solamente los \(k\) tokens más probables.

Por ejemplo:

```text
Top-k = 3
```

Si tenemos:

```text
A → 0.40
B → 0.25
C → 0.15
D → 0.10
E → 0.10
```

solo consideramos:

```text
A
B
C
```

Después se renormaliza la distribución.

Conceptualmente:

```text
Vocabulario completo
       ↓
Top-k
       ↓
Candidatos
       ↓
Sampling
```

---

# 31. Top-p Sampling

Top-p, también conocido como **nucleus sampling**, selecciona el conjunto mínimo de tokens cuya probabilidad acumulada alcanza un umbral \(p\).

Ejemplo:

```text
A → 0.50
B → 0.25
C → 0.12
D → 0.07
E → 0.06
```

Si:

$$
p = 0.87
$$

podríamos conservar:

```text
A + B + C = 0.87
```

y excluir los demás.

La diferencia conceptual es:

```text
Top-k
→ cantidad fija de tokens

Top-p
→ masa de probabilidad variable
```

---

# 32. Temperature + Top-p

En muchos sistemas se pueden combinar técnicas de decoding.

Conceptualmente:

```text
Logits
   ↓
Temperature
   ↓
Top-p / Top-k
   ↓
Sampling
   ↓
Token
```

La implementación concreta y los parámetros disponibles dependen del sistema utilizado.

---

# 33. Beam Search

Otra técnica conocida es:

> **Beam Search**

En lugar de mantener una única secuencia, conserva varias secuencias candidatas.

Ejemplo conceptual:

```text
               Inicio
              /      \
             A        B
           /   \     / \
          C     D   E   F
```

El sistema conserva las secuencias que considera más prometedoras.

Beam search es especialmente relevante en determinados problemas de generación secuencial, aunque los LLM generativos modernos frecuentemente utilizan estrategias de sampling o variantes de decoding diferentes según la tarea.

---

# 34. Decoding no es el modelo

Es importante separar:

```text
MODELO
```

de:

```text
DECODING
```

El modelo produce una distribución.

El decoding decide cómo convertir esa distribución en tokens.

Podemos expresarlo:

```text
Modelo
  ↓
Logits
  ↓
Decoding
  ↓
Token
```

Por eso dos sistemas pueden utilizar el mismo modelo y producir comportamientos diferentes si emplean configuraciones de inferencia diferentes.

---

# 35. El ciclo autoregresivo completo

Supongamos:

```text
Prompt:

El cielo es
```

### Paso 1

```text
Contexto:
El cielo es

Modelo
 ↓
Probabilidades
 ↓
"azul"
```

### Paso 2

```text
Contexto:
El cielo es azul

Modelo
 ↓
Probabilidades
 ↓
"."
```

### Paso 3

```text
Contexto:
El cielo es azul.

Modelo
 ↓
Probabilidades
 ↓
"El"
```

Y continúa:

```text
Prompt
 ↓
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

---

# 36. Una respuesta es una secuencia de decisiones

Podemos representarla:

$$
y_1,y_2,\dots,y_n
$$

donde:

$$
y_1 \sim P(y_1|x)
$$

$$
y_2 \sim P(y_2|x,y_1)
$$

$$
y_3 \sim P(y_3|x,y_1,y_2)
$$

y así sucesivamente.

La respuesta completa puede expresarse:

$$
P(y_1,\dots,y_n|x)
=
\prod_{t=1}^{n}
P(y_t|x,y_1,\dots,y_{t-1})
$$

Esto formaliza la generación autoregresiva.

---

# 37. ¿Por qué una palabra puede cambiar toda la respuesta?

Supongamos:

```text
Contexto A:
El informe demuestra que...
```

El modelo selecciona:

```text
es
```

Ahora tenemos:

```text
El informe demuestra que es...
```

Pero si selecciona:

```text
no
```

obtenemos:

```text
El informe demuestra que no...
```

La nueva distribución será diferente.

Por eso:

```text
Pequeña diferencia inicial
          ↓
Nuevo contexto
          ↓
Nueva distribución
          ↓
Nueva elección
          ↓
Nueva distribución
          ↓
...
```

puede producir respuestas muy diferentes.

---

# 38. Sensibilidad al contexto

El modelo no calcula cada token de forma independiente.

Cada nuevo token puede modificar el contexto.

Por eso:

```text
Prompt A
→ Distribución A
→ Token A
→ Contexto A
→ Respuesta A
```

mientras:

```text
Prompt B
→ Distribución B
→ Token B
→ Contexto B
→ Respuesta B
```

Incluso una diferencia pequeña puede propagarse.

---

# 39. Prompt Engineering visto desde inferencia

Ahora podemos reinterpretar el prompt.

Un prompt:

```text
Explica este concepto.
```

produce una determinada distribución.

Mientras:

```text
Explica este concepto para un estudiante
de 12 años utilizando un ejemplo cotidiano.
```

modifica el contexto.

Por tanto:

```text
PROMPT
 ↓
CONTEXTO
 ↓
REPRESENTACIONES
 ↓
LOGITS
 ↓
PROBABILIDADES
 ↓
DECODING
 ↓
RESPUESTA
```

El prompt no selecciona directamente una respuesta.

**Modifica las condiciones que determinan la distribución de posibles continuaciones.**

---

# 40. ¿El modelo calcula todas las respuestas posibles?

No.

El vocabulario contiene muchas posibilidades, pero el modelo produce una distribución sobre los posibles siguientes tokens.

No genera simultáneamente todas las respuestas completas.

Por ejemplo:

```text
Contexto
   ↓
Distribución del siguiente token
   ↓
Token seleccionado
   ↓
Nuevo contexto
   ↓
Nueva distribución
```

La respuesta completa emerge de la sucesión de estas decisiones.

---

# 41. ¿Dónde aparece el razonamiento?

Cuando un modelo genera:

```text
Primero...
Segundo...
Por lo tanto...
```

todo ese texto forma parte de la secuencia generada.

Desde el punto de vista mecánico:

```text
Token
 ↓
Token
 ↓
Token
 ↓
Token
```

Algunos modelos modernos incorporan entrenamiento y mecanismos especializados para resolver tareas de razonamiento.

Pero no debemos asumir que:

```text
"generar una explicación"
```

equivale automáticamente a:

```text
"mostrar todos los procesos internos del modelo".
```

La salida textual es una representación generada, no una ventana transparente de toda la computación interna.

---

# 42. Hidden states

Durante el procesamiento existen representaciones internas conocidas como **hidden states**.

Podemos representarlas:

$$
H^{(0)}
\rightarrow
H^{(1)}
\rightarrow
H^{(2)}
\rightarrow
\dots
\rightarrow
H^{(L)}
$$

donde cada \(H^{(l)}\) corresponde conceptualmente a las representaciones después de una capa.

Estas representaciones contienen información distribuida.

No son simplemente:

```text
"la respuesta escondida"
```

dentro del modelo.

---

# 43. Logits y hidden states

Una simplificación útil es:

```text
Hidden State
     ↓
Output Projection
     ↓
Logits
     ↓
Softmax
     ↓
Probabilidades
```

Matemáticamente:

$$
h_t
\rightarrow
z_t = W h_t+b
\rightarrow
P_t
$$

donde \(h_t\) representa el estado asociado al punto de generación.

---

# 44. ¿Qué es el KV Cache?

Aquí aparece una optimización fundamental de la inferencia.

Durante atención se utilizan:

* Query;
* Key;
* Value.

En generación autoregresiva, muchos de los Keys y Values correspondientes a tokens anteriores pueden reutilizarse.

Por eso se utiliza:

> **KV Cache**

---

# 45. ¿Por qué necesitamos KV Cache?

Supongamos que generamos:

```text
Token 1
Token 2
Token 3
Token 4
...
```

Sin reutilización, sería necesario recalcular gran parte de la información anterior repetidamente.

Con KV Cache:

```text
Tokens anteriores
      ↓
Keys / Values almacenados
      ↓
Reutilización
      ↓
Nuevo token
```

Esto puede reducir significativamente el trabajo repetido durante generación.

---

# 46. KV Cache conceptualmente

Podemos imaginar:

```text
          CONTEXTO
              ↓
        ┌───────────┐
        │ K y V     │
        │ anteriores│
        └─────┬─────┘
              │
              ↓
         KV CACHE
              │
              ↓
       Nuevo token
              │
              ↓
      Nueva atención
```

El KV cache no es memoria semántica permanente.

Es una estructura de trabajo utilizada durante una sesión de inferencia.

---

# 47. KV Cache vs memoria

No debemos confundir:

```text
KV Cache
```

con:

```text
Memoria del usuario
```

El KV cache:

* pertenece al proceso de inferencia;
* contiene representaciones necesarias para continuar la generación;
* normalmente existe mientras dura la ejecución correspondiente.

Una memoria persistente del sistema es un problema arquitectónico diferente.

---

# 48. Prefill y Decode

La inferencia de un LLM suele dividirse conceptualmente en dos fases:

```text
PREFILL
```

y:

```text
DECODE
```

---

# 49. Prefill

Durante **prefill**, el sistema procesa el prompt inicial.

Por ejemplo:

```text
Prompt
 ↓
Tokenización
 ↓
Transformer
 ↓
KV Cache
```

Si tenemos:

```text
5000 tokens
```

el sistema debe procesar ese contexto inicial.

---

# 50. Decode

Después comienza la generación autoregresiva:

```text
Token nuevo
 ↓
Actualizar KV Cache
 ↓
Calcular siguiente token
 ↓
Actualizar KV Cache
 ↓
Calcular siguiente token
 ↓
...
```

Por tanto:

```text
PREFILL
→ procesar contexto inicial

DECODE
→ generar tokens nuevos
```

---

# 51. TTFT

Una métrica importante es:

> **TTFT — Time To First Token**

Es el tiempo desde que se envía una solicitud hasta que aparece el primer token generado.

Conceptualmente:

```text
Solicitud
   ↓
Procesamiento del prompt
   ↓
Primer token
```

El TTFT está muy relacionado con el trabajo de **prefill**.

---

# 52. TPOT

Otra métrica es:

> **Time Per Output Token**

Representa el tiempo necesario para producir tokens adicionales durante la generación.

Conceptualmente:

```text
Token 1
   ↓
Token 2
   ↓
Token 3
   ↓
Token 4
```

El comportamiento de esta fase depende de factores como:

* hardware;
* tamaño del modelo;
* longitud del contexto;
* KV cache;
* batching;
* cuantización;
* implementación del servidor.

---

# 53. Latencia y rendimiento

Podemos separar:

```text
TTFT
+
Tiempo de generación
=
Latencia total aproximada
```

Por eso dos modelos pueden tener:

```text
misma calidad aproximada
```

pero:

```text
diferente latencia
```

debido a diferencias de infraestructura y arquitectura.

---

# 54. Context Window

La inferencia está limitada por la capacidad del modelo/sistema para procesar contexto.

Podemos representar:

```text
┌──────────────────────────────┐
│       CONTEXT WINDOW         │
│                              │
│ System                       │
│ Developer                    │
│ User                         │
│ History                      │
│ Documents                    │
│ Tool results                 │
│ Generated content            │
│                              │
└──────────────────────────────┘
```

Si el contexto supera los límites soportados por la configuración utilizada, el sistema debe:

* truncar;
* resumir;
* seleccionar;
* comprimir;
* recuperar información relevante;
* o rechazar la solicitud.

---

# 55. Más contexto no significa automáticamente mejor respuesta

Podríamos pensar:

```text
1000 tokens
→ respuesta

100000 tokens
→ mejor respuesta
```

Pero no necesariamente.

El modelo debe procesar y utilizar la información relevante.

Podemos tener:

```text
Mucho contexto
+
Información irrelevante
+
Información contradictoria
+
Instrucciones mezcladas
```

y obtener una respuesta peor.

Por eso aparece:

> **Context Engineering**

que estudia cómo construir y administrar el contexto que recibe el modelo.

---

# 56. Attention no significa "comprensión perfecta"

Es frecuente escuchar:

> "El modelo usa attention, por lo tanto entiende todo el contexto."

No es correcto.

Attention permite calcular relaciones ponderadas entre representaciones.

Pero:

```text
Attention
≠
Comprensión humana
```

Además existen fenómenos como:

* pérdida de información;
* interferencia;
* contexto irrelevante;
* contradicciones;
* long-context degradation;
* atención desigual;
* problemas de recuperación.

---

# 57. Long Context

Los modelos modernos pueden trabajar con contextos muy grandes, pero esto no significa que cada token reciba el mismo tratamiento efectivo.

Podemos tener:

```text
Inicio
   ↓
Información relevante
   ↓
Mucho contenido
   ↓
Información relevante
   ↓
Final
```

El modelo puede no utilizar toda la información con la misma eficacia.

Esto se relaciona con fenómenos conocidos como:

> **Lost in the Middle**

donde información ubicada en posiciones intermedias puede ser recuperada con menor eficacia en determinados escenarios.

---

# 58. Batch Inference

En producción, los sistemas suelen atender múltiples solicitudes.

En lugar de:

```text
Solicitud A
   ↓
Modelo

Solicitud B
   ↓
Modelo

Solicitud C
   ↓
Modelo
```

pueden agrupar operaciones:

```text
Solicitud A ─┐
Solicitud B ─┼→ BATCH → GPU
Solicitud C ─┘
```

Esto puede mejorar la utilización del hardware.

---

# 59. Continuous Batching

Los servidores modernos pueden utilizar estrategias de **continuous batching**, donde las solicitudes entran y salen dinámicamente del proceso de generación.

Conceptualmente:

```text
Solicitud A ────────────────┐
Solicitud B ────────┐      │
Solicitud C ───────────────┤
Solicitud D ────┐         │
                ↓         ↓
             Scheduler
                  ↓
                 GPU
```

El objetivo es mejorar:

* throughput;
* utilización del hardware;
* latencia;
* eficiencia.

---

# 60. Throughput vs Latency

Dos métricas fundamentales:

### Latencia

Tiempo que tarda una solicitud.

### Throughput

Cantidad de trabajo procesado por unidad de tiempo.

Por ejemplo:

```text
Latencia:
2 segundos por solicitud

Throughput:
100 solicitudes por minuto
```

No son equivalentes.

Optimizar una puede afectar a la otra.

---

# 61. Inferencia en GPU

Los LLM realizan enormes cantidades de operaciones matriciales.

Por eso las GPU son especialmente importantes.

Conceptualmente:

```text
Modelo
 ↓
Matrices
 ↓
Multiplicaciones
 ↓
GPU
 ↓
Resultados
```

Las GPU pueden ejecutar grandes cantidades de operaciones paralelas.

---

# 62. Memoria durante inferencia

Durante inferencia necesitamos almacenar, entre otras cosas:

```text
Parámetros
+
Activaciones
+
KV Cache
+
Buffers
+
Estados temporales
```

Una representación simplificada:

```text
MEMORIA GPU
├── Pesos del modelo
├── Activaciones
├── KV Cache
├── Buffers
└── Overhead del sistema
```

Por eso un modelo grande puede requerir una cantidad considerable de memoria incluso cuando no está siendo entrenado.

---

# 63. Training vs Inference en memoria

Durante entrenamiento se necesitan recursos adicionales:

```text
Pesos
+
Activaciones
+
Gradientes
+
Estados del optimizador
```

Durante inferencia, normalmente no necesitamos mantener los gradientes ni los estados del optimizador.

Por eso:

```text
Training
→ mucho más intensivo en memoria

Inference
→ generalmente más eficiente
```

---

# 64. Quantization

Una técnica importante para inferencia es:

> **Quantization**

Consiste, de forma simplificada, en representar determinados valores numéricos utilizando menor precisión.

Por ejemplo:

```text
FP32
↓
FP16
↓
INT8
↓
INT4
```

La idea general es:

```text
Menor precisión
      ↓
Menor memoria
      ↓
Posiblemente mayor eficiencia
```

Pero existe un compromiso:

```text
Eficiencia
   ↕
Precisión / calidad
```

El impacto depende del modelo, método de cuantización y hardware.

---

# 65. Distillation

Otra técnica relacionada con eficiencia es:

> **Knowledge Distillation**

Un modelo grande puede actuar como:

```text
Teacher
```

y ayudar a entrenar:

```text
Student
```

Conceptualmente:

```text
Modelo grande
     ↓
Conocimiento / señales
     ↓
Modelo pequeño
```

El objetivo puede ser obtener un modelo más pequeño y eficiente conservando parte de la capacidad.

Esto ocurre durante entrenamiento, no durante el decoding normal.

---

# 66. Speculative Decoding

Una técnica de inferencia interesante es:

> **Speculative Decoding**

Conceptualmente utiliza:

```text
Modelo pequeño
      ↓
Propone varios tokens
      ↓
Modelo grande
      ↓
Verifica / acepta algunos
```

La idea es acelerar la generación aprovechando un modelo auxiliar más rápido.

Es un ejemplo importante de cómo la optimización de inferencia puede modificar la arquitectura del sistema sin cambiar necesariamente el modelo principal.

---

# 67. Inference-Time Compute

En algunos sistemas modernos se utiliza más computación durante inferencia para determinadas tareas.

Conceptualmente:

```text
Entrada
  ↓
Generación / búsqueda / razonamiento
  ↓
Evaluación
  ↓
Refinamiento
  ↓
Respuesta
```

Esto puede aumentar:

```text
calidad potencial
```

pero también:

```text
latencia
+
coste computacional
```

Por tanto:

> Más computación durante inferencia no es gratis.

---

# 68. Modelos de razonamiento

Algunos modelos están diseñados o entrenados para dedicar más cómputo a determinadas tareas de razonamiento.

Esto introduce una distinción útil:

```text
Generación directa
```

frente a:

```text
Generación con cómputo adicional
```

No debemos reducir esta diferencia únicamente a:

```text
"uno piensa y el otro no piensa".
```

Desde una perspectiva de ingeniería, interesa observar:

* entrenamiento;
* política de generación;
* presupuesto de cómputo;
* herramientas;
* verificación;
* búsqueda;
* decodificación;
* evaluación.

---

# 69. Inference-Time Scaling

Podemos conceptualizar:

```text
Problema sencillo
      ↓
Poco cómputo

Problema complejo
      ↓
Más cómputo
```

El sistema puede utilizar más recursos de inferencia para intentar obtener mejores resultados.

Pero:

```text
Más cómputo
≠
Garantía de corrección
```

Un sistema puede dedicar mucho tiempo a una respuesta y aun así equivocarse.

---

# 70. Tool Calling durante inferencia

Un modelo puede interactuar con herramientas.

Por ejemplo:

```text
Usuario
  ↓
LLM
  ↓
Decide utilizar calculadora
  ↓
Tool
  ↓
Resultado
  ↓
LLM
  ↓
Respuesta
```

La inferencia deja entonces de ser:

```text
Prompt → Texto
```

y pasa a ser:

```text
Prompt
 ↓
LLM
 ↓
Tool
 ↓
Resultado
 ↓
LLM
 ↓
Respuesta
```

---

# 71. Inferencia como bucle

Un sistema con herramientas puede verse como:

```text
┌─────────────────────────────┐
│                             │
│          CONTEXTO           │
│              ↓              │
│             LLM             │
│              ↓              │
│       ¿Necesita tool?       │
│          /       \          │
│        NO         SÍ        │
│        ↓           ↓        │
│    Respuesta      Tool      │
│                    ↓        │
│                 Resultado   │
│                    ↓        │
│                 Contexto    │
│                    │        │
└────────────────────┘
```

Esto es una base conceptual importante para comprender agentes.

---

# 72. Inferencia y agentes

En un agente:

```text
Objetivo
  ↓
LLM
  ↓
Plan / decisión
  ↓
Herramienta
  ↓
Resultado
  ↓
LLM
  ↓
Nueva decisión
  ↓
...
```

La inferencia se convierte en parte de un ciclo de control.

Por eso:

```text
LLM inference
```

es solamente uno de los componentes de:

```text
Agentic System
```

---

# 73. Inferencia y memoria

Podemos distinguir:

### Contexto actual

Información enviada en la solicitud.

### KV Cache

Estructura temporal para acelerar la atención durante generación.

### Memoria externa

Información persistente gestionada por el sistema.

### Base de conocimiento

Documentos recuperables.

Estas cuatro cosas no son equivalentes.

```text
CONTEXTO
≠
KV CACHE
≠
MEMORIA
≠
RAG
```

Esta distinción será fundamental en capítulos posteriores.

---

# 74. Inferencia determinista vs estocástica

Si utilizamos:

```text
Greedy decoding
```

el comportamiento puede ser muy estable.

Si utilizamos:

```text
Sampling
```

pueden aparecer diferentes respuestas.

Por ejemplo:

```text
Prompt
 ↓
Modelo
 ↓
Distribución
 ↓
Sampling
 ↓
Respuesta A
```

y otra ejecución:

```text
Prompt
 ↓
Modelo
 ↓
Distribución
 ↓
Sampling
 ↓
Respuesta B
```

Aunque el prompt sea idéntico.

---

# 75. Semilla aleatoria

Algunos sistemas permiten controlar una **seed**.

Conceptualmente:

```text
Prompt
+
Modelo
+
Parámetros de decoding
+
Seed
```

pueden producir resultados reproducibles bajo determinadas condiciones.

Pero reproducibilidad absoluta en sistemas distribuidos puede verse afectada por:

* hardware;
* kernels;
* versiones;
* optimizaciones;
* batching;
* infraestructura;
* cambios del modelo;
* cambios del proveedor.

Por tanto:

> Una seed no debe interpretarse automáticamente como garantía universal de reproducibilidad bit a bit.

---

# 76. El papel del prompt dentro de la inferencia

Ahora podemos construir una explicación mucho más precisa de Prompt Engineering.

Un prompt no hace:

```text
PROMPT
 ↓
"Programa nuevo"
```

Hace algo más parecido a:

```text
PROMPT
 ↓
TOKENIZACIÓN
 ↓
REPRESENTACIONES
 ↓
ATENCIÓN
 ↓
ESTADOS INTERNOS
 ↓
LOGITS
 ↓
DISTRIBUCIÓN
 ↓
DECODING
 ↓
TOKENS
```

Por eso modificar:

```text
"Explica esto."
```

por:

```text
"Explica esto en 5 pasos, utilizando
un ejemplo de contabilidad y termina
con una tabla."
```

puede modificar significativamente la distribución de generación.

---

# 77. El prompt como condicionamiento

Una formulación matemática útil es:

$$
P(Y|X)
$$

donde:

* \(X\) = contexto/prompt;
* \(Y\) = salida.

Modificar \(X\):

$$
X \rightarrow X'
$$

produce potencialmente:

$$
P(Y|X)
\neq
P(Y|X')
$$

Por eso dos prompts similares pueden producir distribuciones diferentes.

---

# 78. El modelo no "lee" instrucciones como un programa tradicional

Un programa tradicional puede tener:

```python
if edad >= 18:
    permitir()
else:
    bloquear()
```

Un LLM no funciona simplemente ejecutando reglas de este tipo para cada frase.

Su comportamiento emerge de:

```text
Parámetros
+
Arquitectura
+
Contexto
+
Inferencia
+
Decoding
```

Las instrucciones están representadas dentro del contexto y condicionan las probabilidades de generación.

Esto no significa que el modelo sea incapaz de seguir reglas.

Significa que:

> **La implementación subyacente no es equivalente a un intérprete clásico de reglas.**

---

# 79. Prompt vs programación tradicional

| Programación tradicional           | LLM                             |
| ---------------------------------- | ------------------------------- |
| Reglas explícitas                  | Patrones aprendidos             |
| Ejecución determinista normalmente | Generación probabilística       |
| Variables                          | Representaciones                |
| Funciones                          | Operaciones de red              |
| `if/else`                          | Comportamiento aprendido        |
| Salida definida por lógica         | Distribución de posibles tokens |
| Código ejecutable                  | Contexto + parámetros           |

Esto no significa que un LLM reemplace la programación tradicional.

Los sistemas profesionales suelen combinar ambos:

```text
Código
+
LLM
+
Base de datos
+
APIs
+
Validaciones
```

---

# 80. Un ejemplo completo de inferencia

Prompt:

```text
Explica qué es un embedding
para un programador principiante.
```

### Paso 1

Tokenización:

```text
Explica
qué
es
un
embedding
...
```

### Paso 2

Token IDs:

```text
[...]
```

### Paso 3

Embeddings:

```text
ID
 ↓
Vector
```

### Paso 4

Transformer:

```text
Attention
+
MLP
+
Residuals
+
Normalization
```

### Paso 5

Estado final:

```text
h
```

### Paso 6

Proyección:

$$
z = Wh+b
$$

### Paso 7

Softmax:

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

### Paso 8

Decoding:

```text
Token seleccionado
```

### Paso 9

Se agrega al contexto.

### Paso 10

Se repite.

Finalmente:

```text
Respuesta completa
```

---

# 81. Una visión de ingeniería

Podemos representar el pipeline completo:

```text
                  REQUEST
                     │
                     ↓
                TOKENIZER
                     │
                     ↓
                TOKEN IDs
                     │
                     ↓
                EMBEDDINGS
                     │
                     ↓
             POSITIONAL INFO
                     │
                     ↓
             TRANSFORMER
           ┌─────────┴─────────┐
           ↓                   ↓
       ATTENTION              MLP
           ↓                   ↓
           └─────────┬─────────┘
                     ↓
                HIDDEN STATE
                     ↓
               OUTPUT HEAD
                     ↓
                   LOGITS
                     ↓
                 SOFTMAX
                     ↓
              DECODING / SAMPLING
                     ↓
                NEXT TOKEN
                     │
                     ↓
                KV CACHE
                     │
                     ↓
              ¿STOP TOKEN?
               /         \
             NO           YES
             │             │
             ↓             ↓
        REPETIR        RESPUESTA
```

---

# 82. ¿Dónde puede fallar la inferencia?

La respuesta final puede fallar en diferentes etapas.

```text
Tokenización
   ↓
Representación
   ↓
Contexto
   ↓
Attention
   ↓
Procesamiento
   ↓
Logits
   ↓
Decoding
   ↓
Respuesta
```

Los problemas pueden incluir:

* contexto incorrecto;
* información insuficiente;
* información contradictoria;
* mala recuperación;
* limitaciones del modelo;
* errores de generación;
* sampling;
* restricciones de contexto;
* herramientas incorrectas;
* errores del sistema externo.

Por eso analizar solamente el texto final puede ocultar la causa real del fallo.

---

# 83. Observabilidad de inferencia

En sistemas profesionales conviene registrar información como:

```text
Modelo
Versión
Prompt
Contexto
Tokens de entrada
Tokens de salida
Tiempo de respuesta
TTFT
Parámetros de decoding
Herramientas utilizadas
Errores
Validaciones
Resultado
```

Esto permite investigar:

```text
¿Por qué respondió así?
```

en lugar de simplemente:

```text
La IA se equivocó.
```

---

# 84. Coste de inferencia

El coste puede depender de:

```text
Tokens de entrada
+
Tokens de salida
+
Modelo
+
Hardware
+
Número de solicitudes
+
Herramientas
+
Cómputo adicional
```

Podemos pensar conceptualmente:

$$
Costo \approx
C_{input}
+
C_{output}
+
C_{compute}
+
C_{tools}
$$

La fórmula real depende de la arquitectura y del proveedor.

---

# 85. Contexto largo y coste

Si el prompt aumenta:

```text
100 tokens
→ procesamiento relativamente pequeño

10 000 tokens
→ mayor procesamiento

100 000 tokens
→ mucho mayor volumen de información
```

Pero el coste y la latencia no dependen únicamente del número de tokens.

También influyen:

* arquitectura;
* implementación;
* caching;
* atención;
* hardware;
* batching;
* proveedor.

Por eso:

> **Optimizar contexto es también optimizar coste y rendimiento.**

---

# 86. Prefill vs Decode: implicación para Prompt Engineering

Esta distinción tiene una consecuencia práctica.

Un prompt muy largo aumenta principalmente el trabajo inicial de:

```text
PREFILL
```

Una respuesta muy larga aumenta el trabajo de:

```text
DECODE
```

Por tanto:

```text
Contexto enorme
→ mayor coste de procesamiento inicial

Respuesta enorme
→ mayor coste de generación
```

Esto ayuda a entender por qué una buena ingeniería de contexto no consiste en introducir toda la información disponible.

---

# 87. Inferencia y arquitectura

La inferencia depende de la arquitectura.

Por ejemplo:

```text
Transformer
   ↓
Attention
   ↓
MLP
   ↓
Output
```

Pero diferentes modelos pueden utilizar:

* distintas variantes de attention;
* diferentes mecanismos posicionales;
* arquitecturas dense;
* Mixture of Experts;
* diferentes heads;
* distintos tamaños;
* diferentes técnicas de cuantización;
* distintos sistemas de serving.

Por eso:

> El comportamiento del prompt depende parcialmente de la arquitectura que lo procesa.

---

# 88. Dense vs Mixture of Experts

En un modelo **dense**, conceptualmente todas las capas principales participan en cada token.

```text
Token
 ↓
Bloque completo
 ↓
Salida
```

En un modelo **Mixture of Experts (MoE)**, un router puede seleccionar determinados expertos.

```text
Token
 ↓
Router
 ↓
┌───────┬───────┬───────┬───────┐
│Expert1│Expert2│Expert3│Expert4│
└───────┴───────┴───────┴───────┘
       ↓
Expertos seleccionados
       ↓
Salida
```

Esto permite aumentar el número total de parámetros manteniendo un coste computacional por token potencialmente menor que activar todos los parámetros.

---

# 89. ¿Significa MoE que el prompt elige conscientemente un experto?

No.

El prompt modifica las representaciones de entrada.

Estas representaciones influyen en el comportamiento del router.

Podemos representarlo:

```text
Prompt
 ↓
Tokens
 ↓
Representaciones
 ↓
Router
 ↓
Expertos seleccionados
 ↓
Procesamiento
```

Por eso el contenido del prompt puede influir indirectamente en qué partes de un MoE participan.

Pero no debemos interpretarlo como:

> "El usuario selecciona manualmente el experto."

El proceso es interno al modelo.

---

# 90. Inferencia y arquitectura: idea central

Podemos resumir:

```text
PROMPT
   ↓
TOKENIZACIÓN
   ↓
REPRESENTACIÓN
   ↓
ARQUITECTURA
   ↓
INFERENCIA
   ↓
DISTRIBUCIÓN
   ↓
DECODING
   ↓
RESPUESTA
```

Por eso:

> **Prompt Engineering no puede estudiarse completamente separado de la arquitectura del modelo.**

---

# 91. Nivel avanzado: logits como interfaz matemática

Para comprender profundamente los LLM conviene pensar que existe una interfaz conceptual:

```text
Representación interna
        ↓
     LOGITS
        ↓
Distribución sobre vocabulario
```

Los logits permiten estudiar:

* probabilidad relativa;
* competencia entre tokens;
* efectos de temperatura;
* selección;
* calibración;
* decoding;
* comportamiento de la salida.

Por ejemplo, si:

$$
z_A \gg z_B
$$

entonces, manteniendo las demás condiciones constantes:

$$
P(A) > P(B)
$$

después de Softmax.

---

# 92. Logits y diferencias relativas

Softmax tiene una propiedad importante:

$$
softmax(z)
=
softmax(z+c)
$$

para una constante \(c\) aplicada a todos los logits.

Por ejemplo:

```text
[3, 2, 1]
```

y:

```text
[103, 102, 101]
```

producen la misma distribución.

Esto es importante para comprender que:

> Lo relevante en Softmax son las diferencias relativas entre logits, no su valor absoluto por sí solo.

---

# 93. Estabilidad numérica

Calcular directamente:

$$
e^{z_i}
$$

puede producir problemas numéricos cuando los logits son muy grandes.

Por eso se utiliza una transformación equivalente:

$$
softmax(z_i)
=
\frac{e^{z_i-\max(z)}}
{\sum_j e^{z_j-\max(z)}}
$$

Restar el máximo no cambia la distribución, pero ayuda a mantener los valores numéricamente manejables.

Este tipo de detalles es importante en implementaciones reales.

---

# 94. Sampling y entropía

Una distribución puede ser:

### Concentrada

```text
A → 0.95
B → 0.03
C → 0.02
```

### Difusa

```text
A → 0.35
B → 0.30
C → 0.20
D → 0.15
```

La **entropía** mide conceptualmente la incertidumbre de una distribución:

$$
H(P)=-\sum_i P_i\log P_i
$$

Una distribución más concentrada suele tener menor entropía.

Una distribución más uniforme suele tener mayor entropía.

La temperatura puede modificar esta distribución.

---

# 95. Entropía y generación

Podemos pensar:

```text
Baja entropía
      ↓
Pocas opciones dominantes
      ↓
Generación más concentrada
```

mientras:

```text
Alta entropía
      ↓
Más alternativas plausibles
      ↓
Mayor diversidad potencial
```

Pero:

> Diversidad no equivale automáticamente a calidad.

Una distribución más abierta puede introducir alternativas útiles o errores.

---

# 96. Calibration

Un concepto avanzado es la **calibración**.

Un modelo está bien calibrado, de forma idealizada, cuando sus probabilidades reflejan adecuadamente la frecuencia con la que sus predicciones son correctas.

Por ejemplo, entre predicciones a las que asigna aproximadamente:

```text
70% de confianza
```

esperaríamos que fueran correctas aproximadamente el 70% de las veces bajo condiciones apropiadas.

En LLM esto es más complejo porque:

* la salida es secuencial;
* el vocabulario es grande;
* la probabilidad del siguiente token no equivale directamente a probabilidad de verdad de una afirmación completa.

Por tanto:

> **Una alta probabilidad de un token no significa necesariamente que la afirmación completa sea verdadera.**

---

# 97. Probabilidad de token vs verdad

Supongamos que el modelo genera:

```text
La capital de Australia es...
```

Puede asignar una alta probabilidad al token:

```text
Canberra
```

Eso no significa que el sistema tenga una probabilidad explícita y perfectamente calibrada de:

```text
"La capital de Australia es Canberra."
```

La generación se construye token por token.

La factualidad de la afirmación completa es un problema distinto.

---

# 98. Teacher Forcing vs Inference

Durante entrenamiento de un modelo autoregresivo se suele utilizar el contexto real para predecir el siguiente token.

Por ejemplo:

```text
Entrada:
El cielo es

Objetivo:
azul
```

En el siguiente paso:

```text
Entrada:
El cielo es azul

Objetivo:
.
```

Durante inferencia, en cambio, el modelo debe utilizar sus propios tokens generados.

```text
Modelo
 ↓
Token generado
 ↓
Se incorpora al contexto
 ↓
Modelo
 ↓
Siguiente token
```

Esto crea una diferencia fundamental entre entrenamiento e inferencia.

---

# 99. Exposure Bias

Esta diferencia puede relacionarse con el concepto de:

> **Exposure Bias**

Durante entrenamiento, el modelo suele recibir tokens correctos como contexto.

Durante inferencia, puede recibir sus propias predicciones.

Si produce un error:

```text
Token incorrecto
      ↓
Contexto incorrecto
      ↓
Nueva predicción
      ↓
Otro error posible
```

Los errores pueden propagarse.

Este fenómeno es una de las razones por las que la generación autoregresiva puede desviarse.

---

# 100. Error accumulation

Podemos representarlo:

```text
Predicción correcta
      ↓
Predicción correcta
      ↓
Predicción incorrecta
      ↓
Contexto alterado
      ↓
Predicción incorrecta
      ↓
...
```

Esto explica por qué una pequeña desviación temprana puede producir una continuación completamente diferente.

---

# 101. Stop Conditions

La generación necesita saber cuándo detenerse.

Puede ocurrir por:

```text
EOS token
```

o:

```text
Stop sequence
```

o:

```text
max output tokens
```

o:

```text
límite del contexto
```

o:

```text
condición externa
```

Por ejemplo:

```text
Generación
 ↓
¿Apareció "</respuesta>"?
 ↓
Sí
 ↓
STOP
```

---

# 102. Output Tokens

El número de tokens generados puede afectar:

* coste;
* latencia;
* memoria;
* tiempo de respuesta.

Por eso una instrucción como:

```text
Responde en máximo 100 palabras.
```

no es únicamente una preferencia estética.

También puede actuar como una forma de:

```text
Control de longitud
+
Control de coste
+
Control de latencia
```

Aunque no constituye una garantía absoluta.

---

# 103. Inferencia y structured output

Supongamos que solicitamos:

```json
{
  "riesgo": "...",
  "monto": 0
}
```

El modelo sigue generando tokens:

```text
{
"
riesgo
"
:
...
}
```

No está generando mágicamente un objeto JSON como una estructura interna de Python.

Está generando tokens que representan una estructura textual.

Por eso pueden aparecer errores como:

```text
JSON inválido
```

a menos que el sistema utilice mecanismos específicos de structured output, constrained decoding o validación externa.

---

# 104. Constrained Decoding

En sistemas que necesitan estructuras estrictas, puede utilizarse una forma de:

> **Constrained Decoding**

El sistema restringe los tokens que pueden seleccionarse para mantener determinadas reglas.

Conceptualmente:

```text
Logits
 ↓
Restricciones
 ↓
Tokens permitidos
 ↓
Decoding
 ↓
Salida válida
```

Esto es diferente de simplemente pedir:

```text
"Devuelve JSON válido."
```

La instrucción textual depende del comportamiento del modelo.

Una restricción de decoding puede actuar directamente durante la generación.

---

# 105. Prompting vs Constrained Decoding

Podemos comparar:

### Prompt

```text
Devuelve solamente JSON.
```

El modelo recibe una instrucción.

### Constrained decoding

```text
El sistema restringe las opciones de generación
para cumplir una gramática o esquema.
```

Por eso:

```text
Prompt
→ influencia el comportamiento

Constraint
→ restringe el espacio de generación
```

Esta diferencia es muy importante para sistemas de producción.

---

# 106. Inferencia multimodal

La inferencia no está limitada al texto.

Un modelo multimodal puede recibir:

```text
Texto
+
Imagen
+
Audio
+
Video
```

y convertir estos datos en representaciones compatibles con el sistema.

Conceptualmente:

```text
Imagen ───────┐
Texto ────────┼→ Representaciones → Modelo → Salida
Audio ────────┤
Video ────────┘
```

La arquitectura exacta depende del modelo.

---

# 107. Inferencia multimodal y prompt

En un modelo multimodal, el prompt puede incluir:

```text
Texto:
"Analiza esta imagen."

Imagen:
[imagen]
```

El sistema no procesa únicamente la frase.

Debe integrar diferentes modalidades.

Por tanto:

```text
Prompt multimodal
→ representaciones multimodales
→ modelo
→ generación
```

Esto amplía el concepto de Prompt Engineering hacia:

> **Multimodal Prompt Engineering**

---

# 108. ¿Qué ocurre realmente cuando escribimos un prompt?

Podemos responder ahora con bastante precisión:

```text
1. El texto entra al sistema.
2. Se aplica el tokenizer.
3. Los tokens se convierten en IDs.
4. Los IDs se convierten en representaciones.
5. Se incorpora información posicional.
6. El Transformer procesa el contexto.
7. Attention mezcla información contextual.
8. Las capas transforman las representaciones.
9. Se obtiene un estado utilizado para la predicción.
10. Se calculan logits.
11. Softmax produce una distribución.
12. El mecanismo de decoding selecciona un token.
13. El token se agrega al contexto.
14. Se actualiza/reutiliza el KV cache.
15. El proceso se repite.
16. Una condición de parada termina la generación.
17. Los tokens se convierten nuevamente en texto.
```

---

# 109. Mapa completo

```text
                    PROMPT
                       │
                       ↓
                 TOKENIZACIÓN
                       │
                       ↓
                  TOKEN IDs
                       │
                       ↓
                  EMBEDDINGS
                       │
                       ↓
              INFORMACIÓN POSICIONAL
                       │
                       ↓
                TRANSFORMER
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
      ATTENTION                     MLP
          │                         │
          └────────────┬────────────┘
                       ↓
                 HIDDEN STATES
                       │
                       ↓
                  OUTPUT HEAD
                       │
                       ↓
                    LOGITS
                       │
                       ↓
                   SOFTMAX
                       │
                       ↓
               PROBABILIDADES
                       │
                       ↓
                DECODING
            ┌──────────┼──────────┐
            ↓          ↓          ↓
         GREEDY      TOP-K      TOP-P
            │          │          │
            └──────────┼──────────┘
                       ↓
                  TOKEN NUEVO
                       │
                       ↓
                  KV CACHE
                       │
                       ↓
                ¿TERMINAR?
                  /       \
                NO         SÍ
                │           │
                ↓           ↓
             REPETIR     RESPUESTA
```

---

# 110. La ecuación mental del LLM

Una forma extremadamente útil de recordar el proceso es:

$$
\boxed{
\text{Contexto}
\rightarrow
\text{Representación}
\rightarrow
\text{Logits}
\rightarrow
\text{Probabilidades}
\rightarrow
\text{Decoding}
\rightarrow
\text{Token}
}
$$

Y como el proceso es autoregresivo:

$$
\boxed{
x_{t+1}
\sim
P(x_{t+1}|x_{\leq t})
}
$$

Esta ecuación resume gran parte del mecanismo generativo de un LLM autoregresivo.

---

# 111. Relación con los capítulos anteriores

Hasta ahora:

```text
01 IA
 ↓
02 IA / ML / DL / Generativa
 ↓
03 Modelo
 ↓
04 Entrenamiento e Inferencia
 ↓
05 Datos
 ↓
06 Parámetros
 ↓
07 Tokens
 ↓
08 Embeddings
 ↓
09 Contexto
 ↓
10 Predicción y Generación
 ↓
11 Probabilidad
 ↓
12 Temperatura y Sampling
 ↓
13 Limitaciones
```

Después:

```text
LLM
 ↓
Tokenización
 ↓
Embeddings
 ↓
Transformer
 ↓
Attention
 ↓
Posición
 ↓
Pretraining
 ↓
Fine-Tuning
 ↓
Instruction Tuning
 ↓
Alignment
 ↓
INFERENCE
```

Ahora todos esos conceptos empiezan a conectarse en un único proceso.

---

# 112. Relación con Prompt Engineering

La ingeniería de prompt puede verse ahora desde cuatro niveles:

### Nivel 1 — Texto

```text
¿Qué escribo?
```

### Nivel 2 — Contexto

```text
¿Qué información recibe el modelo?
```

### Nivel 3 — Modelo

```text
¿Cómo procesa esa información?
```

### Nivel 4 — Inferencia

```text
¿Cómo se transforma esa información
en una distribución y posteriormente
en una respuesta?
```

Esto produce una comprensión más profunda:

```text
PROMPT
  ↓
CONTEXT
  ↓
MODEL
  ↓
INFERENCE
  ↓
DECODING
  ↓
OUTPUT
```

---

# 113. Implicación fundamental para el Prompt Engineer

Un prompt no es simplemente:

```text
una pregunta.
```

Es una forma de **condicionar el estado de entrada del proceso de inferencia**.

Por eso técnicas como:

* especificar el objetivo;
* proporcionar contexto;
* establecer restricciones;
* definir formato;
* proporcionar ejemplos;
* separar datos e instrucciones;
* estructurar tareas;
* controlar longitud;
* pedir verificación;
* indicar criterios de evaluación;

pueden modificar significativamente el comportamiento del sistema.

---

# 114. Pero el prompt tiene límites

El prompt no puede:

* cambiar directamente los pesos del modelo;
* añadir automáticamente conocimiento verdadero al modelo;
* eliminar las limitaciones arquitectónicas;
* garantizar una respuesta correcta;
* garantizar seguridad perfecta;
* convertir un modelo pequeño en uno mucho más capaz;
* sustituir una herramienta externa cuando se necesita información actual;
* garantizar cálculos exactos si no existe validación;
* convertir datos incorrectos en datos correctos.

Por eso:

> **Prompt Engineering es una parte de la ingeniería del sistema, no el sistema completo.**

---

# 115. Preguntas de nivel básico

1. ¿Qué es inferencia?
2. ¿Cuál es la diferencia entre entrenamiento e inferencia?
3. ¿Qué significa que un modelo sea autoregresivo?
4. ¿Qué son los logits?
5. ¿Qué hace Softmax?
6. ¿Qué es decoding?
7. ¿Qué diferencia existe entre greedy decoding y sampling?
8. ¿Qué hace temperature?
9. ¿Qué es top-k?
10. ¿Qué es top-p?

---

# 116. Preguntas de nivel intermedio

11. ¿Por qué el modelo necesita información posicional?
12. ¿Qué función cumple causal attention?
13. ¿Por qué una modificación pequeña del prompt puede cambiar mucho la respuesta?
14. ¿Qué es KV Cache?
15. ¿Cuál es la diferencia entre prefill y decode?
16. ¿Qué es TTFT?
17. ¿Qué es throughput?
18. ¿Por qué quantization puede acelerar o abaratar inferencia?
19. ¿Por qué una probabilidad alta de un token no equivale necesariamente a verdad?
20. ¿Qué es constrained decoding?

---

# 117. Preguntas de nivel avanzado

21. ¿Cómo se relacionan hidden states y logits?
22. ¿Por qué Softmax es invariante ante sumar la misma constante a todos los logits?
23. ¿Qué relación existe entre temperatura y entropía?
24. ¿Por qué los errores pueden acumularse durante generación autoregresiva?
25. ¿Qué diferencia existe entre contexto, KV cache y memoria persistente?
26. ¿Cómo afecta la longitud del prompt al prefill?
27. ¿Cómo afecta la longitud de la respuesta al decode?
28. ¿Por qué batch inference puede mejorar el throughput?
29. ¿Cómo puede un MoE modificar el coste de inferencia?
30. ¿Por qué prompt engineering no equivale a reprogramar los parámetros?

---

# 118. Preguntas de nivel maestría/PhD

31. ¿Cómo afecta la distribución de logits al comportamiento de decoding?

32. ¿Cómo cambia la entropía de una distribución cuando se modifica la temperatura?

33. ¿Qué propiedades matemáticas hacen que Softmax sea adecuada para transformar logits en una distribución categórica?

34. ¿Cómo influye la causal mask en la factorización autoregresiva de la probabilidad conjunta?

35. ¿Cómo afecta KV caching a la complejidad práctica de la generación autoregresiva?

36. ¿Qué diferencias existen entre la complejidad del prefill y la del decode?

37. ¿Qué relación existe entre la longitud de contexto, atención y uso de memoria?

38. ¿Cómo puede estudiarse experimentalmente la sensibilidad de un modelo a pequeñas perturbaciones del prompt?

39. ¿Cómo puede medirse la calibración de un modelo generativo?

40. ¿Qué limitaciones tiene utilizar la probabilidad del siguiente token como aproximación de la confianza del modelo sobre una afirmación completa?

---

# 119. Resumen final

La inferencia es el proceso mediante el cual un modelo entrenado transforma una entrada en una salida.

En un LLM autoregresivo:

```text
Prompt
 ↓
Tokens
 ↓
Embeddings
 ↓
Transformer
 ↓
Hidden States
 ↓
Logits
 ↓
Probabilidades
 ↓
Decoding
 ↓
Token
 ↓
Nuevo contexto
 ↓
Repetición
 ↓
Respuesta
```

Los conceptos fundamentales son:

| Concepto               | Función                                            |
| ---------------------- | -------------------------------------------------- |
| Tokenizer              | Convierte texto en tokens                          |
| Token ID               | Identifica cada token                              |
| Embedding              | Representa tokens mediante vectores                |
| Positional information | Representa información de posición                 |
| Attention              | Relaciona representaciones contextuales            |
| Transformer            | Procesa y transforma las representaciones          |
| Hidden state           | Representación interna de la información           |
| Logits                 | Puntuaciones para los tokens posibles              |
| Softmax                | Convierte logits en probabilidades                 |
| Decoding               | Selecciona tokens a partir de la distribución      |
| Temperature            | Modifica la concentración de la distribución       |
| Top-k                  | Limita candidatos a los k más probables            |
| Top-p                  | Limita candidatos por masa de probabilidad         |
| KV Cache               | Reutiliza Keys/Values durante generación           |
| Prefill                | Procesa el contexto inicial                        |
| Decode                 | Genera tokens posteriores                          |
| TTFT                   | Tiempo hasta el primer token                       |
| Throughput             | Cantidad de trabajo procesado por unidad de tiempo |
| Quantization           | Reduce precisión numérica para mejorar eficiencia  |
| Constrained Decoding   | Restringe el espacio de generación                 |

---

# 120. La idea que conecta todo

Hasta ahora podríamos haber pensado:

> "Un LLM recibe una pregunta y responde."

Ahora podemos describirlo de una manera mucho más precisa:

> **Un LLM recibe una secuencia de tokens, transforma esa secuencia mediante una arquitectura neuronal, produce logits sobre un vocabulario, convierte esos logits en una distribución de probabilidad y utiliza un mecanismo de decoding para seleccionar el siguiente token. Ese token se incorpora al contexto y el proceso se repite autoregresivamente hasta alcanzar una condición de parada.**

Y desde la perspectiva de Ingeniería de Prompt:

> **El prompt modifica la entrada y, por tanto, las condiciones bajo las cuales se ejecuta todo ese proceso de inferencia.**

La arquitectura puede resumirse:

```text
                  ┌─────────────────┐
                  │      PROMPT     │
                  └────────┬────────┘
                           ↓
                    TOKENIZACIÓN
                           ↓
                      EMBEDDINGS
                           ↓
                  INFORMACIÓN POSICIONAL
                           ↓
                    ┌──────────────┐
                    │ TRANSFORMER  │
                    │              │
                    │  Attention   │
                    │      +       │
                    │     MLP      │
                    └──────┬───────┘
                           ↓
                    HIDDEN STATES
                           ↓
                         LOGITS
                           ↓
                        SOFTMAX
                           ↓
                    PROBABILIDADES
                           ↓
                       DECODING
                           ↓
                     SIGUIENTE TOKEN
                           ↓
                      KV CACHE
                           ↓
                     NUEVO CONTEXTO
                           │
                           └───────────┐
                                       ↓
                                  ¿TERMINAR?
                                    /     \
                                  NO       SÍ
                                  │         │
                                  └────────→ RESPUESTA
```

---

# 121. Puente hacia el siguiente nivel

Ya podemos responder:

> **¿Qué ocurre cuando escribo un prompt?**

Pero todavía falta una pregunta fundamental:

> **¿Cómo se representa y administra todo el contexto que rodea al prompt?**

En un sistema real, el modelo puede recibir mucho más que el mensaje del usuario:

```text
┌────────────────────────────────────┐
│ SYSTEM INSTRUCTIONS                │
├────────────────────────────────────┤
│ DEVELOPER INSTRUCTIONS             │
├────────────────────────────────────┤
│ CONVERSATION HISTORY               │
├────────────────────────────────────┤
│ USER PROMPT                         │
├────────────────────────────────────┤
│ RETRIEVED DOCUMENTS                │
├────────────────────────────────────┤
│ TOOL RESULTS                        │
├────────────────────────────────────┤
│ MEMORY                              │
├────────────────────────────────────┤
│ OTHER CONTEXT                       │
└────────────────────────────────────┘
                  ↓
             LLM INFERENCE
```

Por tanto, la siguiente gran pregunta de Ingeniería de IA es:

> **¿Cómo diseñamos, seleccionamos, ordenamos, comprimimos y controlamos el contexto que recibe el modelo?**

Ahí comienza **Context Engineering**.

```text
PROMPT ENGINEERING
        ↓
CONTEXT ENGINEERING
        ↓
RAG
        ↓
MEMORY
        ↓
TOOLS
        ↓
AGENTS
        ↓
AI SYSTEMS ENGINEERING
```

La comprensión de inferencia es el puente que permite pasar de **"escribir buenos prompts"** a **"diseñar sistemas que controlan conscientemente las condiciones bajo las cuales un modelo genera sus respuestas"**.
