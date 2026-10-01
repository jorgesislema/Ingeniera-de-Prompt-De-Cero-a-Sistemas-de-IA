# 03 — Dense Transformers

> **Nivel 02 — Arquitecturas de IA y Modelos**

---

# 1. Objetivo del capítulo

En los capítulos anteriores estudiamos:

```text
01 — Modelos Base
        ↓
02 — Instruction-Tuned
        ↓
03 — Dense Transformers
```

Ahora entraremos en una de las arquitecturas fundamentales de los modelos modernos de lenguaje:

# Transformer Denso

Los Transformers constituyen la arquitectura dominante de una gran parte de los modelos de lenguaje modernos y también aparecen en sistemas de visión, audio, multimodales y otras aplicaciones.

Pero existe una distinción importante:

```text
Transformer
    │
    ├── Denso
    │
    └── Disperso / Sparse
            │
            └── Mixture of Experts
```

En este capítulo estudiaremos principalmente el **Transformer denso**.

La pregunta central será:

> **¿Qué significa que un Transformer sea denso y qué consecuencias tiene esto para el funcionamiento de un LLM y para la Ingeniería de Prompt?**

---

# 2. ¿Qué significa "denso"?

En este contexto, **denso** (*dense*) hace referencia a cómo se utilizan los parámetros del modelo durante el procesamiento.

En un Transformer denso, una entrada normalmente atraviesa las capas principales del modelo utilizando el conjunto completo de parámetros de esas capas.

Simplificando:

```text
Entrada
   │
   ↓
Capa 1 ─────── todos los parámetros
   │
   ↓
Capa 2 ─────── todos los parámetros
   │
   ↓
Capa 3 ─────── todos los parámetros
   │
   ↓
...
   │
   ↓
Capa N ─────── todos los parámetros
   │
   ↓
Salida
```

Esto contrasta con un modelo **sparse** o disperso:

```text
Entrada
   │
   ↓
Router
   │
   ├── Expert 1
   ├── Expert 2
   ├── Expert 3
   ├── Expert 4
   └── ...
          ↑
     solo algunos
```

La diferencia fundamental será:

```text
DENSE
entrada → conjunto completo de capas/parametrización principal


SPARSE / MoE
entrada → selección de determinados expertos
```

---

# 3. No confundir "denso" con "grande"

Esta es una de las primeras ideas que debe aprender el alumno.

Un modelo puede ser:

```text
pequeño + denso
```

o:

```text
grande + denso
```

También puede ser:

```text
grande + disperso
```

Por tanto:

```text
Tamaño
≠
Densidad
```

Son propiedades diferentes.

Podemos visualizarlo:

```text
                  MODELOS

             ┌───────────────┐
             │   PEQUEÑO     │
             └───────┬───────┘
                     │
              ┌──────┴──────┐
              ↓             ↓
            Denso         Sparse


             ┌───────────────┐
             │    GRANDE     │
             └───────┬───────┘
                     │
              ┌──────┴──────┐
              ↓             ↓
            Denso         Sparse
```

---

# 4. ¿Por qué estudiar Transformers?

Porque los modelos de lenguaje modernos utilizan arquitecturas basadas en Transformers en una enorme variedad de aplicaciones.

Un Transformer permite procesar relaciones entre diferentes partes de una secuencia mediante mecanismos de atención.

En un modelo de lenguaje autoregresivo podemos pensar:

```text
Texto
  ↓
Tokens
  ↓
Embeddings
  ↓
Transformer
  ↓
Representaciones
  ↓
Distribución de probabilidad
  ↓
Token siguiente
```

Este proceso se repite durante la generación.

---

# 5. Recordatorio: del texto a los tokens

Supongamos:

```text
La inteligencia artificial transforma la informática.
```

El tokenizer divide el texto en unidades.

Una representación simplificada:

```text
Texto
 │
 ↓
┌────┬──────────────┬────────────┬──────────────┐
│ La │ inteligencia │ artificial │ transforma...│
└────┴──────────────┴────────────┴──────────────┘
 │
 ↓
tokens
```

La división real depende del tokenizer.

No debemos asumir que:

```text
1 palabra = 1 token
```

Una palabra puede convertirse en:

```text
1 token
```

o:

```text
varios tokens
```

y determinados símbolos, espacios o fragmentos también pueden participar en la tokenización.

---

# 6. De tokens a representaciones

Los tokens se transforman en representaciones numéricas.

Podemos simplificar:

```text
"inteligencia"
       ↓
     token
       ↓
   embedding
       ↓
vector numérico
```

Por ejemplo:

$$
x_i \in \mathbb{R}^{d}
$$

donde:

* \(x_i\) es la representación del token;
* \(d\) es la dimensión del espacio de representación.

Una secuencia puede representarse como:

$$
X =
[x_1,x_2,x_3,\ldots,x_n]
$$

El Transformer procesa estas representaciones.

---

# 7. Arquitectura general de un Transformer

Un Transformer moderno puede tener una estructura conceptual como:

```text
Tokens
  │
  ↓
Embeddings
  │
  ↓
┌──────────────────────┐
│ Transformer Block 1  │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Transformer Block 2  │
└──────────┬───────────┘
           ↓
          ...
           ↓
┌──────────────────────┐
│ Transformer Block N  │
└──────────┬───────────┘
           ↓
     Representación
           ↓
     Capa de salida
           ↓
     Probabilidades
           ↓
       Siguiente token
```

Un Transformer moderno contiene normalmente múltiples bloques.

---

# 8. ¿Qué hay dentro de un Transformer Block?

Un bloque Transformer típico contiene componentes relacionados con:

* atención;
* redes feed-forward;
* normalización;
* conexiones residuales.

Una representación conceptual simplificada:

```text
                 ENTRADA
                    │
                    ↓
             Normalización
                    │
                    ↓
               Attention
                    │
                    ↓
            Conexión residual
                    │
                    ↓
             Normalización
                    │
                    ↓
           Feed-Forward Network
                    │
                    ↓
            Conexión residual
                    │
                    ↓
                  SALIDA
```

Las implementaciones modernas pueden introducir variaciones importantes.

Por ejemplo:

* RMSNorm;
* diferentes posiciones de normalización;
* diferentes mecanismos de atención;
* diferentes diseños de FFN;
* RoPE;
* GQA;
* MQA;
* variantes de activación.

Por eso debemos entender el diagrama como una representación conceptual y no como una especificación universal.

---

# 9. Attention

La atención es uno de los componentes fundamentales.

Su función conceptual es permitir que una representación considere información de otras posiciones de la secuencia.

Por ejemplo:

```text
El gato se subió al árbol porque estaba asustado.
```

Para interpretar correctamente determinadas relaciones, el modelo puede necesitar relacionar diferentes partes de la secuencia.

La atención permite calcular relaciones entre representaciones.

---

# 10. Query, Key y Value

La formulación clásica de atención utiliza:

* Query \(Q\)
* Key \(K\)
* Value \(V\)

La ecuación clásica de scaled dot-product attention es:

$$
Attention(Q,K,V)
=
softmax
\left(
\frac{QK^T}{\sqrt{d_k}}
\right)V
$$

Donde:

* \(Q\) representa consultas;
* \(K\) representa claves;
* \(V\) representa valores;
* \(d_k\) es la dimensión de las claves.

No es necesario memorizar todavía la ecuación.

Lo importante es comprender:

```text
Query
  │
  ↓
¿A qué información debería prestar atención?

Key
  │
  ↓
¿Qué información representa cada posición?

Value
  │
  ↓
¿Qué información se recupera?
```

---

# 11. Self-Attention

En un Transformer de lenguaje, la atención puede ser **self-attention**.

Esto significa que las representaciones de la secuencia interactúan entre sí.

Conceptualmente:

```text
Tokens:

[El] [gato] [subió] [al] [árbol]
  │     │      │      │      │
  └─────┴──────┴──────┴──────┘
             │
             ↓
       Self-Attention
```

Cada posición puede calcular relaciones con otras posiciones permitidas por la máscara de atención.

---

# 12. Causal Attention

En un modelo autoregresivo, el modelo no debería utilizar tokens futuros para predecir el token actual.

Supongamos:

```text
El gato come pescado
```

Cuando el modelo está prediciendo:

```text
pescado
```

puede utilizar:

```text
El
gato
come
```

pero no debería mirar el token futuro que intenta predecir.

Esto se implementa mediante una máscara causal.

Conceptualmente:

```text
          El  gato  come  pescado

El        ✓    ✗     ✗      ✗
gato      ✓    ✓     ✗      ✗
come      ✓    ✓     ✓      ✗
pescado   ✓    ✓     ✓      ✓
```

La forma exacta de interpretar la matriz depende de cómo representemos las consultas y posiciones, pero la idea es:

> **Cada posición solo puede utilizar la información permitida por la máscara causal.**

---

# 13. ¿Por qué importa esto para la generación?

Porque el modelo genera texto de forma autoregresiva.

Supongamos:

```text
Entrada:

La capital de Ecuador es
```

El modelo calcula una distribución:

```text
Quito      0.82
Guayaquil  0.03
Cuenca     0.02
...
```

Se selecciona un token.

Ahora:

```text
La capital de Ecuador es Quito
```

El modelo vuelve a calcular.

Después:

```text
La capital de Ecuador es Quito y
```

Y continúa.

Conceptualmente:

```text
Prompt
  ↓
predice token
  ↓
agrega token
  ↓
predice siguiente token
  ↓
agrega token
  ↓
...
```

---

# 14. ¿Dónde está lo "denso"?

En un Transformer denso, las capas principales del modelo procesan la representación utilizando sus parámetros completos.

Podemos imaginar una capa:

```text
                    ENTRADA
                       │
                       ↓
             ┌─────────────────┐
             │   Transformer   │
             │                 │
             │  Attention      │
             │  FFN            │
             │  Norm           │
             └────────┬────────┘
                      │
                      ↓
                    SALIDA
```

No existe un router que seleccione solamente un pequeño subconjunto de expertos especializados dentro de la FFN principal.

Esto será diferente en MoE.

---

# 15. Dense Feed-Forward Network

Dentro de cada bloque Transformer suele existir una red feed-forward.

Una forma simplificada:

$$
FFN(x)=W_2\,\sigma(W_1x+b_1)+b_2
$$

donde:

* \(x\) es la entrada;
* \(W_1\) y \(W_2\) son matrices de pesos;
* \(b_1\) y \(b_2\) son sesgos, dependiendo de la implementación;
* \(\sigma\) es una función de activación.

En arquitecturas modernas pueden utilizarse variantes como:

* GELU;
* SiLU;
* SwiGLU;
* otras funciones y diseños.

---

# 16. ¿Por qué se necesita una FFN?

La atención permite combinar información entre posiciones.

La red feed-forward transforma las representaciones de cada posición mediante parámetros aprendidos.

Una simplificación útil:

```text
Attention
   ↓
combina información entre tokens


FFN
   ↓
transforma la representación de cada token
```

Ambas partes trabajan juntas.

---

# 17. Conexiones residuales

Los Transformers utilizan conexiones residuales.

Conceptualmente:

```text
x
│
├──────────────────┐
│                  │
↓                  │
Transformación     │
│                  │
└─────── + <───────┘
         │
         ↓
       salida
```

Matemáticamente:

$$
y = x + F(x)
$$

Esto facilita el entrenamiento de redes profundas.

En lugar de aprender solamente:

$$
y=F(x)
$$

el bloque aprende una transformación residual:

$$
y=x+F(x)
$$

Las conexiones residuales son uno de los componentes importantes que permiten construir redes profundas.

---

# 18. Normalización

Los Transformers también utilizan mecanismos de normalización.

Dos conceptos importantes que el alumno encontrará son:

```text
LayerNorm
RMSNorm
```

Una implementación concreta puede utilizar uno u otro, dependiendo de la arquitectura.

Su propósito general está relacionado con estabilizar y facilitar el entrenamiento y procesamiento de las representaciones.

No debemos asumir que todos los Transformers utilizan exactamente la misma normalización.

---

# 19. Multi-Head Attention

La atención puede dividirse en múltiples cabezas:

**Multi-Head Attention — MHA**

Conceptualmente:

```text
                 INPUT
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
     Head 1      Head 2      Head 3
        │          │          │
        ↓          ↓          ↓
      atención   atención   atención
        │          │          │
        └──────────┼──────────┘
                   ↓
               combinación
                   │
                   ↓
                 OUTPUT
```

Cada cabeza puede aprender diferentes patrones de interacción.

No debemos interpretar literalmente que:

> "Head 1 entiende gramática y Head 2 entiende matemáticas."

El comportamiento real es más complejo y las interpretaciones de cabezas individuales dependen del modelo y del análisis realizado.

---

# 20. MHA, MQA y GQA

Los modelos modernos pueden utilizar variantes de atención.

### MHA

**Multi-Head Attention**

Cada cabeza tiene sus propias representaciones de Query, Key y Value.

### MQA

**Multi-Query Attention**

Varias cabezas de Query comparten representaciones de Key y Value.

### GQA

**Grouped-Query Attention**

Las cabezas de Query se agrupan y comparten determinadas representaciones de Key y Value.

Conceptualmente:

```text
MHA

Q1 K1 V1
Q2 K2 V2
Q3 K3 V3
Q4 K4 V4
```

Mientras que:

```text
GQA

Q1 ─┐
Q2 ─┤→ K1,V1
    │
Q3 ─┤→ K2,V2
Q4 ─┘
```

La nomenclatura exacta depende de la implementación.

---

# 21. ¿Por qué existen GQA y MQA?

Uno de los motivos importantes está relacionado con el coste de memoria durante la inferencia.

Los modelos autoregresivos mantienen información de Keys y Values previamente calculada.

Esto se conoce como:

**KV Cache**

Una gran cantidad de memoria puede ser utilizada por esta caché.

Reducir el número de representaciones K/V puede disminuir determinados costes de memoria y mejorar la eficiencia.

Simplificando:

```text
MHA
muchas K/V
   ↓
mayor memoria KV


GQA / MQA
menos K/V compartidas
   ↓
menor memoria KV
```

Esto tendrá importancia cuando estudiemos sistemas de inferencia y contexto largo.

---

# 22. Positional Information

El Transformer necesita información sobre el orden de los tokens.

Sin información posicional, la secuencia:

```text
El perro mordió al hombre.
```

no debería tratarse igual que:

```text
El hombre mordió al perro.
```

Necesitamos representar las posiciones.

Existen diferentes métodos.

Uno de los más conocidos en modelos modernos es:

**RoPE — Rotary Position Embedding.**

---

# 23. RoPE

RoPE incorpora información posicional mediante transformaciones rotacionales de las representaciones utilizadas en atención.

No necesitamos derivar toda la matemática aquí.

Conceptualmente:

```text
Tokens
  ↓
Representaciones
  ↓
información posicional
  ↓
Attention
```

La idea fundamental:

> **El modelo necesita información suficiente para distinguir relaciones que dependen del orden de los tokens.**

---

# 24. Transformer completo simplificado

Ahora podemos unir las piezas:

```text
                 TOKENS
                    │
                    ↓
                EMBEDDINGS
                    │
                    ↓
          INFORMACIÓN POSICIONAL
                    │
                    ↓
        ┌────────────────────────┐
        │   TRANSFORMER BLOCK    │
        │                        │
        │  Normalización         │
        │       ↓                │
        │  Self-Attention        │
        │       ↓                │
        │  Residual              │
        │       ↓                │
        │  Normalización         │
        │       ↓                │
        │  Feed-Forward Network  │
        │       ↓                │
        │  Residual              │
        └───────────┬────────────┘
                    ↓
               REPETIR N VECES
                    │
                    ↓
                 OUTPUT
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
               SAMPLING
                    │
                    ↓
              NUEVO TOKEN
```

Este es un modelo conceptual.

Las arquitecturas reales pueden modificar muchos de estos componentes.

---

# 25. Logits

Antes de obtener probabilidades, el modelo produce valores llamados:

**logits**.

Supongamos un vocabulario pequeño:

```text
Quito
Lima
Bogotá
Madrid
```

El modelo podría producir:

```text
Quito      5.2
Lima       2.1
Bogotá     1.8
Madrid     0.4
```

Estos valores no son probabilidades.

Posteriormente pueden transformarse mediante softmax:

$$
P_i=
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

donde \(z_i\) representa un logit.

Entonces obtenemos algo como:

```text
Quito      0.91
Lima       0.04
Bogotá     0.03
Madrid     0.02
```

Los valores son ilustrativos.

---

# 26. De logits a texto

El proceso conceptual completo es:

```text
Prompt
  ↓
Tokens
  ↓
Embeddings
  ↓
Transformer
  ↓
Logits
  ↓
Distribución
  ↓
Sampling / Decoding
  ↓
Token
  ↓
Repetir
```

Esto conecta directamente con los capítulos de:

* probabilidad;
* temperatura;
* sampling;
* inferencia.

---

# 27. ¿Qué ocurre en cada Transformer Block?

Imaginemos una frase:

```text
El estudiante estudia inteligencia artificial.
```

Los tokens entran en el primer bloque.

```text
Bloque 1
   ↓
representaciones iniciales transformadas
```

Después:

```text
Bloque 2
   ↓
representaciones más elaboradas
```

Después:

```text
Bloque 3
   ↓
nuevas transformaciones
```

Y así sucesivamente.

No debemos imaginar que cada capa tiene una función humana claramente definida como:

```text
Capa 1 = palabras
Capa 2 = gramática
Capa 3 = lógica
```

La realidad es distribuida y mucho más compleja.

---

# 28. Profundidad del modelo

Un Transformer puede contener muchos bloques.

Conceptualmente:

```text
Embedding
   ↓
Block 1
   ↓
Block 2
   ↓
Block 3
   ↓
...
   ↓
Block N
   ↓
Output
```

La profundidad es una de las dimensiones que pueden contribuir a la capacidad del modelo.

Pero:

> **Más capas no significa automáticamente mejor rendimiento en todas las tareas.**

También importan:

* datos;
* parámetros;
* ancho de las capas;
* arquitectura;
* entrenamiento;
* calidad de datos;
* postentrenamiento;
* inferencia.

---

# 29. Profundidad y ancho

Podemos imaginar un Transformer como una estructura con:

```text
PROFUNDIDAD
↓
número de bloques


ANCHO
↓
dimensión de las representaciones
```

Visualmente:

```text
              ANCHO
        ←──────────────→

       ┌─────────────────┐
       │     BLOCK 1     │
       ├─────────────────┤
       │     BLOCK 2     │
       ├─────────────────┤
       │     BLOCK 3     │
       ├─────────────────┤
       │       ...       │
       ├─────────────────┤
       │     BLOCK N     │
       └─────────────────┘
               ↑
               │
          PROFUNDIDAD
```

Estas dimensiones afectan a la capacidad y al coste.

---

# 30. Parámetros en un Transformer denso

Un modelo puede tener parámetros en diferentes componentes.

Simplificando:

```text
Parámetros
   │
   ├── embeddings
   │
   ├── attention
   │     ├── Wq
   │     ├── Wk
   │     ├── Wv
   │     └── Wo
   │
   ├── FFN
   │     ├── W1
   │     └── W2
   │
   ├── normalización
   │
   └── salida
```

La estructura exacta depende de la arquitectura.

---

# 31. Dense vs Sparse

Ahora podemos establecer una comparación.

| Característica                 | Transformer denso                 | Transformer disperso / MoE                      |
| ------------------------------ | --------------------------------- | ----------------------------------------------- |
| Parámetros activos por token   | Gran parte del modelo             | Subconjunto                                     |
| Router de expertos             | No                                | Sí                                              |
| Expertos especializados        | No como mecanismo principal       | Sí                                              |
| Computación por token          | Alta según tamaño                 | Puede reducirse respecto al total de parámetros |
| Parámetros totales             | Limitados por coste computacional | Puede ser muy grande                            |
| Complejidad de infraestructura | Menor en este aspecto             | Mayor                                           |
| Uso de memoria                 | Depende del tamaño                | Depende de pesos totales y expertos activos     |
| Distribución de carga          | Relativamente uniforme            | Debe gestionarse el balance entre expertos      |

Esta tabla es conceptual.

Los costes reales dependen de la implementación, hardware, cuantización, paralelismo y otras optimizaciones.

---

# 32. Una analogía

Imagina una empresa.

### Modelo denso

Todos los departamentos trabajan en cada proyecto.

```text
Proyecto
   │
   ├── Finanzas
   ├── Legal
   ├── Ingeniería
   ├── Marketing
   └── Recursos Humanos
```

### Modelo MoE

Existe un sistema de asignación:

```text
Proyecto
   │
   ↓
Router
   │
   ├── Ingeniería
   └── Finanzas
```

No todos los departamentos participan en cada solicitud.

Esta analogía es útil para comprender la diferencia conceptual, aunque no representa exactamente las operaciones matemáticas de un Transformer.

---

# 33. Ventajas conceptuales de los modelos densos

Los modelos densos presentan una arquitectura relativamente directa.

Esto puede facilitar:

* entrenamiento;
* análisis;
* despliegue;
* paralelización;
* predicción de costes;
* utilización de hardware.

No significa que sean siempre más eficientes o mejores.

Simplemente presentan un patrón computacional diferente.

---

# 34. Limitaciones de los modelos densos

Cuando aumentamos mucho el tamaño del modelo:

```text
más parámetros
      ↓
más computación
      ↓
más memoria
      ↓
mayores requisitos de infraestructura
```

Esto puede convertirse en un problema.

Por eso la investigación ha explorado arquitecturas como:

* MoE;
* cuantización;
* distillation;
* pruning;
* sparsity;
* técnicas de atención eficientes;
* optimizaciones de inferencia.

---

# 35. Dense Transformer y escala

Una idea histórica importante:

```text
más datos
+
más parámetros
+
más computación
        ↓
potencialmente
mayor capacidad
```

Pero la relación no es una regla simple.

El rendimiento depende de múltiples factores.

En investigación se estudian leyes de escalamiento (*scaling laws*) para analizar cómo cambian las pérdidas y capacidades cuando aumentan:

* parámetros;
* datos;
* cómputo.

Esto pertenece a un nivel más avanzado de estudio.

---

# 36. Scaling Laws

Una representación conceptual:

```text
            ESCALA

      Datos ────────┐
                    │
      Parámetros ───┼──→ rendimiento
                    │
      Cómputo ──────┘
```

La investigación sobre scaling laws intenta caracterizar estas relaciones cuantitativamente.

No debemos convertirlas en una regla simplista como:

> "Más parámetros siempre significa mejor modelo."

Eso no es correcto.

La calidad y cantidad de datos, el presupuesto computacional, la arquitectura y el entrenamiento importan.

---

# 37. El coste de inferencia

Supongamos un modelo denso enorme.

Cada token generado necesita pasar por las capas del modelo.

Conceptualmente:

```text
Token
  ↓
Block 1
  ↓
Block 2
  ↓
...
  ↓
Block N
  ↓
Siguiente token
```

Si generamos:

```text
1 token
```

tenemos una pasada.

Si generamos:

```text
100 tokens
```

tenemos aproximadamente 100 pasos autoregresivos de generación, aunque existen optimizaciones y detalles de implementación que hacen que el coste real sea más complejo.

---

# 38. Prefill y Decode

Durante la inferencia de LLMs suele ser útil distinguir dos fases:

## Prefill

Se procesa el contexto inicial.

```text
Prompt
   ↓
procesamiento inicial
   ↓
KV Cache
```

## Decode

Se generan tokens nuevos uno por uno.

```text
Token nuevo
   ↓
actualización KV Cache
   ↓
siguiente token
```

Visualmente:

```text
          PREFILL
Prompt ─────────────→ KV Cache
                         │
                         ↓
                       DECODE
                         │
               ┌─────────┴─────────┐
               ↓                   ↓
           token 1              token 2
               ↓                   ↓
           token 3              token 4
```

Esta distinción será importante al estudiar optimización de inferencia.

---

# 39. KV Cache

Durante la generación autoregresiva, el sistema puede conservar determinadas representaciones de Keys y Values para evitar recalcular todo el contexto desde cero en cada paso.

Conceptualmente:

```text
Prompt
   ↓
K/V
   ↓
KV Cache
   ↓
token nuevo
   ↓
actualización
```

Sin KV Cache, repetiríamos innecesariamente parte del trabajo.

Por eso la memoria necesaria para KV Cache se convierte en una consideración importante cuando:

* aumenta el contexto;
* aumenta el número de usuarios;
* aumenta el batch;
* aumenta la longitud de generación.

---

# 40. Dense Transformer y contexto largo

Aquí aparece una relación directa con Prompt Engineering.

Un prompt más largo significa:

```text
más tokens
    ↓
más procesamiento
    ↓
más memoria
```

Además, la atención puede tener costes importantes relacionados con la longitud de la secuencia, dependiendo del mecanismo utilizado.

Por eso:

> **Un modelo con una ventana de contexto grande no significa que cualquier prompt largo sea automáticamente eficiente o igualmente útil.**

Esto será desarrollado en profundidad en el nivel de **Context Engineering**.

---

# 41. El prompt dentro de un Transformer denso

Ahora podemos responder una pregunta central del repositorio:

> ¿Qué ocurre con el prompt cuando llega a un Transformer denso?

Conceptualmente:

```text
PROMPT
  │
  ↓
TOKENIZACIÓN
  │
  ↓
TOKENS
  │
  ↓
EMBEDDINGS
  │
  ↓
POSICIONES
  │
  ↓
TRANSFORMER DENSO
  │
  ├── Attention
  │
  ├── FFN
  │
  ├── Normalización
  │
  └── Residual
  │
  ↓
REPRESENTACIÓN FINAL
  │
  ↓
LOGITS
  │
  ↓
PROBABILIDADES
  │
  ↓
SAMPLING
  │
  ↓
TOKEN
```

Por eso el prompt no "entra directamente en una caja negra".

Es transformado progresivamente por múltiples capas.

---

# 42. ¿El prompt activa diferentes partes del modelo?

En un Transformer denso, no debemos imaginar un sistema equivalente a:

```text
Pregunta sobre matemáticas
        ↓
activa módulo matemático


Pregunta sobre Python
        ↓
activa módulo Python
```

Ese modelo mental se acerca más a una arquitectura con expertos especializados.

En un Transformer denso, las mismas capas principales procesan las diferentes entradas.

Sin embargo, las activaciones internas pueden ser diferentes.

Es decir:

```text
Prompt A
   ↓
mismos parámetros
   ↓
activaciones A


Prompt B
   ↓
mismos parámetros
   ↓
activaciones B
```

Esto es muy importante.

---

# 43. Parámetros frente a activaciones

Debemos diferenciar:

### Parámetros

Valores aprendidos durante el entrenamiento.

```text
W1
W2
W3
...
```

### Activaciones

Valores producidos cuando una entrada atraviesa el modelo.

```text
entrada
   ↓
activaciones
   ↓
siguiente capa
```

Podemos representarlo:

```text
ENTRENAMIENTO
      ↓
PARÁMETROS
      │
      │ permanecen fijos durante
      │ una inferencia normal
      ↓
INPUT
      ↓
ACTIVACIONES
      ↓
SALIDA
```

Esta distinción será crucial cuando estudiemos interpretabilidad y seguridad de modelos.

---

# 44. ¿El prompt cambia el modelo?

Durante una inferencia normal:

```text
Prompt
  ↓
cambia las activaciones
```

pero:

```text
Prompt
  X
  ↓
no modifica permanentemente los pesos
```

Por ejemplo:

```text
Modelo
θ = parámetros

Prompt A
→ activaciones A
→ respuesta A

Prompt B
→ activaciones B
→ respuesta B
```

El mismo modelo puede producir comportamientos muy diferentes ante entradas diferentes sin cambiar sus parámetros.

---

# 45. Esta idea explica el prompting

Aquí aparece una conexión fundamental:

```text
MISMO MODELO
     │
     ├── Prompt A → comportamiento A
     │
     ├── Prompt B → comportamiento B
     │
     └── Prompt C → comportamiento C
```

Por eso el diseño del contexto y del prompt puede modificar fuertemente la salida sin realizar ningún entrenamiento adicional.

---

# 46. ¿Puede el prompt "activar conocimiento"?

Esta expresión debe utilizarse con cuidado.

A veces se dice:

> "El prompt activa una parte del conocimiento del modelo."

Como metáfora puede resultar útil.

Pero técnicamente es más preciso decir:

> **El prompt modifica las entradas y, por tanto, las activaciones internas y la distribución de salida del modelo.**

No significa necesariamente que exista un archivo interno llamado:

```text
conocimiento_de_matematicas.bin
```

que se enciende cuando escribimos:

```text
matemáticas
```

---

# 47. Prompt y atención

El contenido del prompt afecta las relaciones calculadas mediante atención.

Por ejemplo:

```text
El banco aprobó el préstamo porque el cliente...
```

La interpretación de determinadas palabras depende del contexto.

Agregar información:

```text
El banco aprobó el préstamo porque el cliente presentó ingresos suficientes.
```

cambia las representaciones disponibles.

Podemos pensar:

```text
Prompt corto
   ↓
contexto limitado


Prompt enriquecido
   ↓
más información disponible
```

Esto no significa que más información siempre produzca mejores respuestas.

---

# 48. Más contexto no siempre significa mejor resultado

Supongamos:

```text
Prompt A:
Resume este contrato.
```

y:

```text
Prompt B:
Resume este contrato.
[100 páginas irrelevantes]
[muchos ejemplos]
[repeticiones]
[texto contradictorio]
```

El segundo contiene más tokens, pero puede dificultar la tarea.

Por eso debemos distinguir:

```text
cantidad de contexto
        ≠
calidad del contexto
```

Este principio será fundamental en **Context Engineering**.

---

# 49. Dense Transformer y determinismo

Un modelo denso no necesariamente produce exactamente la misma salida cada vez.

Esto depende de la configuración de inferencia y del sistema.

Podemos tener:

```text
Prompt
  ↓
logits
  ↓
decodificación determinista
  ↓
misma salida
```

o:

```text
Prompt
  ↓
distribución
  ↓
sampling
  ↓
salidas diferentes
```

Por eso debemos separar:

```text
arquitectura
```

de:

```text
estrategia de generación
```

---

# 50. Dense Transformer y temperatura

La temperatura modifica cómo se transforma la distribución de logits durante la generación.

Una formulación simplificada:

$$
P_i =
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

donde:

* \(z_i\) es un logit;
* \(T\) es la temperatura.

Conceptualmente:

```text
Temperatura baja
       ↓
distribución más concentrada


Temperatura alta
       ↓
distribución más dispersa
```

La temperatura no modifica los parámetros del Transformer.

Modifica la estrategia de selección durante la inferencia.

---

# 51. Dense Transformer y generación autoregresiva

Podemos visualizar todo el proceso:

```text
                 PROMPT
                   │
                   ↓
               Transformer
                   │
                   ↓
                 logits
                   │
                   ↓
              distribución
                   │
                   ↓
                sampling
                   │
                   ↓
                token 1
                   │
                   ↓
          agregar al contexto
                   │
                   ↓
               Transformer
                   │
                   ↓
                token 2
                   │
                   ↓
                  ...
```

Esto se repite hasta alcanzar:

* un token de finalización;
* una longitud máxima;
* una condición de parada;
* o una regla definida por la aplicación.

---

# 52. ¿Qué significa que el modelo sea "denso" durante la generación?

Simplificando:

```text
Token
  ↓
Block 1
  ↓
Block 2
  ↓
Block 3
  ↓
...
  ↓
Block N
```

El token atraviesa la arquitectura completa.

En una arquitectura MoE:

```text
Token
  ↓
Router
  ↓
Expertos seleccionados
  ↓
siguiente etapa
```

Esta diferencia tendrá consecuencias en:

* coste;
* escalabilidad;
* especialización;
* infraestructura;
* comportamiento;
* análisis.

---

# 53. Dense Transformer vs MoE

La comparación conceptual más importante:

```text
TRANSFORMER DENSO

             Token
               │
               ↓
       ┌───────────────┐
       │   BLOQUE      │
       │               │
       │ Attention     │
       │ FFN           │
       └───────┬───────┘
               ↓
             salida
```

Mientras:

```text
TRANSFORMER MoE

             Token
               │
               ↓
             Router
               │
        ┌──────┼──────┐
        ↓      ↓      ↓
      Exp 1  Exp 2  Exp 3
        │      │      │
        └──────┼──────┘
               ↓
             salida
```

En MoE no necesariamente se activan todos los expertos para cada token.

---

# 54. ¿Un modelo denso es más inteligente?

No podemos concluir eso simplemente por la arquitectura.

La capacidad depende de muchos factores:

```text
Arquitectura
+
datos
+
escala
+
entrenamiento
+
postentrenamiento
+
inferencia
+
herramientas
```

Por tanto, la pregunta:

> "¿Dense o MoE es más inteligente?"

está mal planteada como regla general.

La pregunta técnicamente útil es:

> **¿Qué propiedades computacionales y de comportamiento introduce cada arquitectura y para qué escenario resulta apropiada?**

---

# 55. Ventajas y compromisos

Podemos resumir conceptualmente:

## Transformer denso

Ventajas potenciales:

* arquitectura relativamente uniforme;
* comportamiento computacional más directo;
* infraestructura ampliamente estudiada;
* paralelización madura;
* ausencia de routing de expertos.

Compromisos:

* el coste computacional aumenta con el tamaño;
* todos los parámetros principales participan en el procesamiento;
* escalar capacidad puede resultar costoso.

## MoE

Ventajas potenciales:

* gran cantidad de parámetros totales;
* solo parte de los parámetros puede activarse por token;
* posibilidad de especialización de expertos.

Compromisos:

* routing;
* balance de carga;
* comunicación entre dispositivos;
* complejidad de entrenamiento e inferencia;
* gestión de expertos.

---

# 56. ¿Por qué esto importa para Prompt Engineering?

Aquí aparece una de las ideas centrales de todo el repositorio.

El alumno debe comenzar a comprender que:

```text
Prompt
   ↓
modelo
```

no es una relación universal.

La arquitectura puede afectar:

* cómo se utiliza el contexto;
* cómo se distribuye la computación;
* cómo se comportan diferentes tipos de tareas;
* cómo se escala el modelo;
* qué limitaciones aparecen;
* qué estrategias de prompting resultan útiles.

No significa que exista una regla sencilla:

```text
Dense → prompt A
MoE → prompt B
```

La relación es mucho más compleja.

---

# 57. El prompt no "selecciona capas" en un Dense Transformer

Otra idea que debemos evitar:

> "Si escribo una pregunta de matemáticas, el prompt activa las capas matemáticas."

En un Transformer denso no debemos asumir la existencia de módulos explícitos de ese tipo.

Una explicación más precisa:

```text
Prompt
   ↓
representaciones
   ↓
activaciones
   ↓
transformaciones sucesivas
   ↓
distribución de salida
```

Las representaciones internas pueden codificar información compleja, pero no debemos asignarles etiquetas humanas rígidas sin evidencia experimental.

---

# 58. Interpretabilidad

Aquí aparece una disciplina avanzada:

**Interpretabilidad de modelos de IA.**

Busca estudiar qué ocurre internamente en modelos complejos.

Algunas áreas incluyen:

* análisis de activaciones;
* circuitos;
* features;
* atención;
* representaciones;
* probing;
* mechanistic interpretability.

Una representación conceptual:

```text
                MODELO
                  │
          ┌───────┴────────┐
          ↓                ↓
      activaciones       pesos
          │                │
          ↓                ↓
     interpretación    análisis
```

La interpretabilidad de Transformers es un área activa de investigación.

No debemos presentar una interpretación simple de las neuronas o cabezas como una verdad universal.

---

# 59. Dense Transformer y escalabilidad

Cuando aumentamos:

```text
número de capas
       +
dimensión
       +
parámetros
```

también aumenta el coste.

Conceptualmente:

```text
Modelo pequeño
     ↓
menor coste


Modelo grande
     ↓
mayor coste
```

Esto impulsa la investigación hacia:

* MoE;
* cuantización;
* kernels optimizados;
* FlashAttention;
* speculative decoding;
* paged attention;
* batching;
* paralelismo tensorial;
* paralelismo de pipeline.

Algunos de estos conceptos pertenecen más a la ingeniería de inferencia que a la arquitectura del Transformer, pero están estrechamente relacionados.

---

# 60. FlashAttention

**FlashAttention** es una familia de algoritmos y kernels optimizados para calcular atención de manera más eficiente en memoria y hardware.

La idea fundamental no es:

> "Cambiar la atención por otra cosa."

Sino:

> **calcular la atención de manera más eficiente aprovechando la jerarquía de memoria del hardware y evitando materializar innecesariamente grandes matrices intermedias.**

Conceptualmente:

```text
Attention tradicional
        ↓
más movimientos de memoria


FlashAttention
        ↓
cálculo optimizado
+
mejor utilización de memoria
```

Esto es importante porque el rendimiento real de un LLM depende tanto de las matemáticas como de cómo se implementan en hardware.

---

# 61. Arquitectura matemática vs implementación

Debemos distinguir dos niveles:

### Nivel conceptual

```text
Transformer
Attention
FFN
Residual
Normalization
```

### Nivel de implementación

```text
CUDA
kernels
FlashAttention
tensor parallelism
quantization
KV cache
batching
hardware
```

Un ingeniero de IA profesional debe comprender ambos niveles progresivamente.

---

# 62. Dense Transformer y hardware

Un Transformer denso puede ejecutarse en:

* GPU;
* TPU;
* aceleradores especializados;
* CPU, con limitaciones y optimizaciones;
* otros dispositivos dependiendo del tamaño y la implementación.

La arquitectura matemática no cambia simplemente porque cambie el hardware.

Pero:

```text
mismo modelo
+
hardware diferente
=
rendimiento diferente
```

El coste real depende de:

* memoria;
* ancho de banda;
* capacidad de cómputo;
* precisión numérica;
* paralelismo;
* optimizaciones del runtime.

---

# 63. Precisión numérica

Los modelos pueden utilizar diferentes formatos numéricos.

Por ejemplo:

```text
FP32
FP16
BF16
INT8
INT4
```

La cuantización puede reducir memoria y, en determinados escenarios, acelerar inferencia.

Conceptualmente:

```text
FP16
 ↓
INT8
 ↓
INT4

menos bits
   ↓
menos memoria
```

Pero reducir precisión puede introducir compromisos de exactitud y rendimiento.

La cuantización será estudiada con mayor profundidad cuando abordemos optimización y despliegue.

---

# 64. Modelo denso y cuantización

La cuantización no convierte automáticamente un modelo denso en uno sparse.

Podemos tener:

```text
Dense + FP16
```

o:

```text
Dense + INT8
```

o:

```text
Dense + INT4
```

Mientras:

```text
MoE
```

describe una característica arquitectónica relacionada con la activación selectiva de expertos.

Por tanto:

```text
Denso / Sparse
```

y:

```text
FP16 / INT8 / INT4
```

son dimensiones diferentes.

---

# 65. Conceptos que no debemos mezclar

El alumno debe separar:

```text
ARQUITECTURA
├── Dense
├── MoE
└── otras variantes

PRECISIÓN
├── FP32
├── BF16
├── FP16
├── INT8
└── INT4

ENTRENAMIENTO
├── Pretraining
├── SFT
├── Preference Optimization
└── otros métodos

INFERENCIA
├── Greedy
├── Sampling
├── Temperature
├── Top-k
├── Top-p
└── otros métodos

SISTEMA
├── RAG
├── Tools
├── Memory
└── Agents
```

Esta separación conceptual evitará muchas confusiones posteriores.

---

# 66. Ejemplo práctico de diagnóstico

Supongamos que un usuario dice:

> "Mi prompt funciona bien en un modelo y mal en otro."

No debemos concluir inmediatamente:

> "El prompt está mal."

Podemos investigar:

```text
¿Son arquitecturas diferentes?
        ↓
¿Tienen distinto postentrenamiento?
        ↓
¿Utilizan diferentes tokenizadores?
        ↓
¿Tienen distinto contexto?
        ↓
¿La inferencia está configurada igual?
        ↓
¿Uno tiene herramientas y otro no?
        ↓
¿La salida está siendo evaluada de la misma forma?
```

Este es el tipo de razonamiento que queremos desarrollar en el repositorio.

---

# 67. Ejemplo: mismo prompt, dos arquitecturas

Prompt:

```text
Analiza estos 100 registros y encuentra duplicados.
```

Modelo A:

```text
Transformer denso
```

Modelo B:

```text
Transformer MoE
```

No debemos asumir que:

```text
MoE = mejor
```

ni:

```text
Dense = mejor
```

Debemos medir:

```text
precisión
recall
latencia
coste
uso de memoria
consistencia
capacidad de contexto
```

Esto introduce una idea fundamental:

> **Las arquitecturas deben compararse mediante métricas y tareas concretas, no mediante etiquetas generales.**

---

# 68. Implicaciones para sistemas empresariales

Imaginemos un sistema de auditoría basado en LLM.

Necesitamos evaluar:

```text
Modelo
  ↓
¿detecta correctamente anomalías?


Contexto
  ↓
¿recibe todos los datos necesarios?


Prompt
  ↓
¿define correctamente los criterios?


Inferencia
  ↓
¿produce resultados consistentes?


Salida
  ↓
¿es estructurada y validable?


Sistema
  ↓
¿puede comprobar los resultados?
```

Aquí se observa nuevamente:

```text
Prompt Engineering
        ↓
System Engineering
```

---

# 69. Checklist del ingeniero

Cuando encuentres un modelo Transformer, pregunta:

### Arquitectura

* ¿Es denso?
* ¿Es MoE?
* ¿Qué variante de Transformer utiliza?
* ¿Cuántas capas tiene?
* ¿Cuál es la dimensión del modelo?
* ¿Qué mecanismo de atención utiliza?

### Atención

* ¿MHA?
* ¿MQA?
* ¿GQA?
* ¿Utiliza RoPE u otro mecanismo posicional?

### Entrenamiento

* ¿Es modelo base?
* ¿Instruction-tuned?
* ¿Qué métodos de postentrenamiento utiliza?

### Inferencia

* ¿Qué precisión utiliza?
* ¿Hay cuantización?
* ¿Cómo funciona el KV Cache?
* ¿Qué estrategia de decoding utiliza?

### Sistema

* ¿Tiene RAG?
* ¿Tiene herramientas?
* ¿Tiene memoria?
* ¿Tiene validación?

---

# 70. Modelo mental completo

Después de este capítulo, el alumno debería visualizar un LLM aproximadamente así:

```text
                         USUARIO
                            │
                            ↓
                          PROMPT
                            │
                            ↓
                       TOKENIZADOR
                            │
                            ↓
                         TOKENS
                            │
                            ↓
                        EMBEDDING
                            │
                            ↓
                    POSICIÓN / RoPE
                            │
                            ↓
        ┌─────────────────────────────────┐
        │       TRANSFORMER DENSO         │
        │                                 │
        │   ┌─────────────────────────┐   │
        │   │ Attention               │   │
        │   └─────────────────────────┘   │
        │               ↓                 │
        │        Residual + Norm           │
        │               ↓                 │
        │   ┌─────────────────────────┐   │
        │   │ Feed-Forward Network    │   │
        │   └─────────────────────────┘   │
        │               ↓                 │
        │        Residual + Norm           │
        └────────────────┬────────────────┘
                         │
                  repetir N veces
                         │
                         ↓
                       LOGITS
                         │
                         ↓
                   DISTRIBUCIÓN
                         │
                         ↓
                      SAMPLING
                         │
                         ↓
                    NUEVO TOKEN
                         │
                         ↓
                    REPETICIÓN
                         │
                         ↓
                     RESPUESTA
```

---

# 71. La idea fundamental para Prompt Engineering

Hasta ahora hemos aprendido:

```text
MODELO BASE
     ↓
aprende patrones


INSTRUCTION TUNING
     ↓
aprende a seguir instrucciones


TRANSFORMER DENSO
     ↓
procesa representaciones mediante
capas de atención y redes feed-forward
```

Ahora podemos introducir una conclusión más profunda:

> **El prompt no es una orden que un programa ejecuta mediante reglas explícitas. Es una entrada que modifica las representaciones y activaciones de un modelo neuronal, afectando la distribución de posibles salidas.**

Por eso expresiones como:

> "Dile al modelo exactamente qué hacer"

son útiles pedagógicamente, pero técnicamente incompletas.

Una descripción más precisa es:

```text
Prompt
   ↓
entrada contextual
   ↓
representaciones
   ↓
transformaciones neuronales
   ↓
distribución de salida
   ↓
decodificación
   ↓
respuesta
```

---

# 72. Resumen

Un **Transformer denso** es una arquitectura en la que las capas principales procesan las entradas utilizando la parametrización completa correspondiente, en contraste con arquitecturas dispersas que seleccionan subconjuntos de parámetros, como ocurre en los modelos Mixture of Experts.

Las ideas fundamentales son:

1. **Denso no significa grande.**
2. **Dense y Sparse son propiedades arquitectónicas diferentes.**
3. **Un Transformer procesa tokens mediante múltiples bloques.**
4. **Los bloques incluyen mecanismos como atención, redes feed-forward, normalización y conexiones residuales.**
5. **Self-Attention permite modelar relaciones entre diferentes posiciones de la secuencia.**
6. **Los modelos autoregresivos utilizan atención causal para evitar utilizar información futura al predecir un token.**
7. **MHA, MQA y GQA son variantes del mecanismo de atención.**
8. **RoPE es una técnica utilizada para incorporar información posicional en muchos Transformers modernos.**
9. **Las activaciones dependen de la entrada, mientras que los parámetros se mantienen fijos durante una inferencia normal.**
10. **El prompt modifica las activaciones y la distribución de salida; normalmente no modifica los pesos del modelo.**
11. **La generación autoregresiva produce tokens sucesivamente.**
12. **KV Cache permite reutilizar información durante la generación.**
13. **Prefill y Decode son dos fases importantes de la inferencia.**
14. **La cuantización es independiente de que un modelo sea denso o MoE.**
15. **El rendimiento de un modelo depende tanto de la arquitectura como de la implementación y el hardware.**
16. **Más parámetros no garantizan automáticamente mejor rendimiento.**
17. **Un modelo denso no debe interpretarse como un conjunto de módulos explícitos para matemáticas, código, historia, etc.**
18. **El comportamiento final depende del modelo, entrenamiento, contexto, prompt, inferencia y sistema.**

La distinción esencial es:

$$
\boxed{
\text{Dense Transformer}
=
\text{procesamiento denso de las capas del modelo}
}
$$

frente a:

$$
\boxed{
\text{Sparse / MoE}
=
\text{activación selectiva de subconjuntos de parámetros}
}
$$

Y desde el punto de vista de Ingeniería de Prompt:

$$
\boxed{
\text{Prompt}
\rightarrow
\text{tokens}
\rightarrow
\text{representaciones}
\rightarrow
\text{Transformer}
\rightarrow
\text{logits}
\rightarrow
\text{decodificación}
\rightarrow
\text{respuesta}
}
$$

---

# 73. Preguntas de comprobación

## Nivel básico

1. ¿Qué significa que un Transformer sea denso?
2. ¿Denso significa necesariamente grande?
3. ¿Qué componentes principales encontramos en un Transformer?
4. ¿Qué es self-attention?
5. ¿Qué es una conexión residual?
6. ¿Qué es una FFN?

## Nivel intermedio

7. ¿Cuál es la diferencia entre MHA, MQA y GQA?
8. ¿Qué problema resuelve la información posicional?
9. ¿Qué es RoPE?
10. ¿Qué diferencia existe entre parámetros y activaciones?
11. ¿Qué es KV Cache?
12. ¿Cuál es la diferencia entre Prefill y Decode?
13. ¿Por qué un modelo con más contexto no necesariamente produce mejores respuestas?

## Nivel avanzado

14. ¿Por qué la atención causal es necesaria en un modelo autoregresivo?
15. ¿Qué representa matemáticamente \(QK^T/\sqrt{d_k}\)?
16. ¿Por qué GQA puede reducir determinados costes de memoria respecto a MHA?
17. ¿Cuál es la diferencia arquitectónica entre Dense Transformer y MoE?
18. ¿Por qué cuantización y sparsity son conceptos diferentes?
19. ¿Cómo puede el mismo conjunto de parámetros producir diferentes activaciones ante diferentes prompts?
20. ¿Por qué no es correcto afirmar que determinadas capas de un Transformer "son las capas de matemáticas"?
21. ¿Qué relación existe entre arquitectura, implementación de kernels y rendimiento real?
22. ¿Cómo afecta la longitud del contexto al coste de inferencia?
23. ¿Por qué Prompt Engineering no modifica normalmente los parámetros del modelo?
24. ¿Por qué el diseño de un prompt debe estudiarse conjuntamente con la arquitectura y configuración del modelo?

---

# 74. Preparación para el siguiente capítulo

La progresión conceptual queda ahora:

```text
01 — Modelos Base
        │
        ↓
02 — Instruction-Tuned
        │
        ↓
03 — Dense Transformers
        │
        ↓
04 — Mixture of Experts
```

En el siguiente capítulo estudiaremos **Mixture of Experts (MoE)**.

La pregunta será:

> **¿Cómo puede un modelo tener una enorme cantidad de parámetros y, aun así, utilizar solamente una parte de ellos para procesar cada token?**

Eso nos llevará a estudiar:

```text
Router
   ↓
Experts
   ↓
Top-K routing
   ↓
Sparse activation
   ↓
Load balancing
   ↓
Capacity
   ↓
Communication
   ↓
Inferencia MoE
```

Y, especialmente para este repositorio:

> **¿Qué significa todo esto para el comportamiento de un prompt?**
