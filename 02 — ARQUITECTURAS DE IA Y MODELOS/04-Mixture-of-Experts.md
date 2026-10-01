# 04 — Mixture of Experts (MoE)

> **Nivel:** Fundamentos → Ingeniería → Maestría / PhD
> **Área:** Arquitecturas de modelos de IA
> **Prerrequisitos:** Transformer, Attention, Dense Transformers, entrenamiento e inferencia
> **Actualizado:** septiembre de 2026

---

# 1. Introducción

En el capítulo anterior estudiamos los **Dense Transformers**.

La idea fundamental era:

> En una arquitectura densa, cada token atraviesa las mismas capas y, en términos generales, utiliza los mismos parámetros de esas capas.

Esto funciona muy bien, pero aparece un problema cuando queremos construir modelos extremadamente grandes.

Supongamos que queremos un modelo con:

```text
1000 mil millones de parámetros
```

Una arquitectura densa tendría que realizar una enorme cantidad de operaciones utilizando esos parámetros para procesar cada token.

La pregunta es:

> **¿Necesitamos utilizar todos los parámetros para cada token?**

La respuesta de las arquitecturas **Mixture of Experts (MoE)** es:

> **No necesariamente.**

En lugar de hacer que todos los parámetros participen en cada procesamiento, podemos tener múltiples **expertos** y utilizar solamente un subconjunto de ellos para cada token.

La idea general es:

```text
                 TOKEN
                   │
                   ▼
                ROUTER
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
     Experto 1  Experto 2  Experto 3
        │          │          │
        │       seleccionados │
        └──────────┬──────────┘
                   ▼
              COMBINACIÓN
                   │
                   ▼
              REPRESENTACIÓN
                   │
                   ▼
              SIGUIENTE CAPA
```

Esto permite aumentar considerablemente la cantidad total de parámetros del modelo sin aumentar proporcionalmente el cálculo realizado para cada token.

---

# 2. ¿Qué significa "Expert"?

Un **expert** o experto es, conceptualmente, un bloque de parámetros especializado dentro de una arquitectura MoE.

En muchos LLM modernos, los expertos reemplazan o complementan principalmente la parte **Feed-Forward Network (FFN)** de un Transformer.

Un Transformer denso puede verse aproximadamente así:

```text
Token
  │
  ▼
Self-Attention
  │
  ▼
FFN
  │
  ▼
Siguiente bloque
```

En un MoE:

```text
Token
  │
  ▼
Self-Attention
  │
  ▼
Router
  │
  ├──► Expert 1
  ├──► Expert 2
  ├──► Expert 3
  ├──► Expert 4
  ├──► ...
  └──► Expert N
           │
           ▼
       combinación
           │
           ▼
     Siguiente bloque
```

Los expertos suelen tener estructuras similares.

Por ejemplo:

```text
Expert 1 = FFN₁
Expert 2 = FFN₂
Expert 3 = FFN₃
...
Expert N = FFNₙ
```

Lo importante es que:

```text
FFN₁ ≠ FFN₂ ≠ FFN₃
```

porque cada experto tiene sus propios parámetros.

---

# 3. La idea intuitiva

Imaginemos una empresa con especialistas:

```text
                  CLIENTE
                     │
                     ▼
              RECEPCIONISTA
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
    Contabilidad   Legal       Ingeniería
```

El recepcionista decide a quién enviar cada consulta.

Si llega:

> "¿Cómo calculo la depreciación de un activo?"

puede enviarla a Contabilidad.

Si llega:

> "¿Qué significa esta cláusula contractual?"

puede enviarla a Legal.

Si llega:

> "¿Cómo optimizo este algoritmo?"

puede enviarla a Ingeniería.

El sistema no necesita enviar cada consulta a todos los especialistas.

Eso es aproximadamente la intuición detrás del **routing** en MoE.

Pero existe una diferencia importante:

> En un LLM real, los expertos no son necesariamente especialistas humanos perfectamente interpretables como "el experto de matemáticas" o "el experto de Python".

La especialización puede emerger de manera distribuida y no siempre es fácilmente interpretable.

---

# 4. Dense Transformer vs MoE

Comparemos las dos arquitecturas.

## Transformer denso

```text
                 TOKEN
                   │
                   ▼
              Attention
                   │
                   ▼
                  FFN
                   │
                   ▼
              siguiente
```

## Transformer MoE

```text
                 TOKEN
                   │
                   ▼
              Attention
                   │
                   ▼
                Router
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
       E1         E2         E3
        │          │          │
        └──────┬───┴──────────┘
               ▼
            combinar
               │
               ▼
            siguiente
```

En el modelo denso:

```text
1 token → FFN
```

En MoE:

```text
1 token → router → algunos expertos
```

La diferencia fundamental está en la **sparsity**, es decir, en que solamente una parte de los parámetros expertos participa en el cálculo de cada token.

---

# 5. ¿Por qué crear MoE?

Existen dos objetivos principales:

## 5.1 Aumentar la capacidad del modelo

Podemos almacenar muchos más parámetros.

Por ejemplo:

```text
Modelo denso:

50B parámetros
↓
50B parámetros disponibles
↓
gran cantidad de cálculo por token
```

MoE:

```text
500B parámetros totales
↓
solo una fracción activa por token
↓
menor cálculo relativo por token
```

La cifra exacta depende de la arquitectura.

Por eso debemos distinguir:

```text
PARÁMETROS TOTALES
        ≠
PARÁMETROS ACTIVOS
```

---

# 6. Parámetros totales vs parámetros activos

Este concepto es fundamental para comprender MoE.

Supongamos:

```text
8 expertos
```

y cada experto tiene:

```text
7B parámetros
```

Entonces tenemos aproximadamente:

```text
8 × 7B = 56B
```

parámetros asociados a los expertos.

Pero supongamos que el router selecciona:

```text
top-k = 2
```

Entonces cada token utiliza solamente dos expertos.

Conceptualmente:

```text
Parámetros totales:
56B

Parámetros expertos activos por token:
14B
```

Esto no significa que el modelo sea simplemente equivalente a un modelo denso de 14B.

Los 56B parámetros forman parte de la capacidad total del sistema, pero cada token solamente activa una parte.

---

# 7. La arquitectura completa

Una capa MoE puede representarse así:

```text
                    Hidden State
                         │
                         ▼
                    ┌─────────┐
                    │ Router  │
                    └────┬────┘
                         │
             ┌───────────┼───────────┐
             │           │           │
             ▼           ▼           ▼
          Expert 1    Expert 2    Expert 3
             │           │           │
             │           │           │
             └───────────┼───────────┘
                         ▼
                    Weighted Sum
                         │
                         ▼
                  MoE Layer Output
```

El router determina:

1. qué expertos utilizar;
2. con qué peso;
3. cómo combinar sus resultados.

---

# 8. El Router

El **router** es uno de los componentes centrales de MoE.

Recibe la representación actual del token:

```text
h ∈ Rᵈ
```

donde:

* `h` = representación del token;
* `d` = dimensión del estado oculto.

El router transforma esa representación en puntuaciones para los expertos.

Una formulación simplificada sería:

$$
s = W_r h
$$

donde:

* `W_r` = matriz del router;
* `h` = hidden state;
* `s` = puntuaciones de routing.

Si tenemos `N` expertos:

```text
s = [s₁, s₂, s₃, ..., sₙ]
```

Cada valor indica cuánto considera el router a ese experto para ese token.

---

# 9. Softmax del Router

Una forma común de transformar las puntuaciones en probabilidades es:

$$
p_i =
\frac{e^{s_i}}
{\sum_{j=1}^{N}e^{s_j}}
$$

Obtenemos:

```text
Expert 1 → 0.05
Expert 2 → 0.10
Expert 3 → 0.60
Expert 4 → 0.20
Expert 5 → 0.05
```

El router podría seleccionar los expertos con mayor puntuación.

Por ejemplo:

```text
top-k = 2
```

Seleccionaría:

```text
Expert 3 → 0.60
Expert 4 → 0.20
```

Los demás quedarían fuera de la ruta de ese token.

---

# 10. ¿Qué significa Top-K?

**Top-K routing** significa:

> Seleccionar los `K` expertos con mayor puntuación para cada token.

Ejemplo:

```text
Expert      Score

E1          0.04
E2          0.12
E3          0.55  ← seleccionado
E4          0.21  ← seleccionado
E5          0.03
E6          0.05
```

Si:

```text
K = 2
```

tenemos:

```text
E3 + E4
```

Si:

```text
K = 1
```

tenemos:

```text
E3
```

Esto produce una arquitectura **sparse**.

---

# 11. Sparse Mixture of Experts

La expresión:

> **Sparse Mixture of Experts (SMoE)**

es especialmente importante.

"Sparse" significa que no todos los expertos se utilizan para cada token.

Supongamos:

```text
64 expertos
```

pero:

```text
top-k = 2
```

Entonces:

```text
64 expertos disponibles
        ↓
2 expertos activos
        ↓
62 expertos no participan
```

para ese token y esa capa.

Esto permite tener una gran capacidad total sin ejecutar todos los expertos para cada token.

---

# 12. El cálculo de salida

Supongamos que el router selecciona dos expertos:

```text
Expert 3
Expert 7
```

El token se procesa:

$$
y_3 = E_3(h)
$$

$$
y_7 = E_7(h)
$$

y posteriormente se combinan:

$$
y = p_3y_3 + p_7y_7
$$

donde:

* `y₃` = salida del experto 3;
* `y₇` = salida del experto 7;
* `p₃` = peso del router;
* `p₇` = peso del router.

Visualmente:

```text
                 h
                 │
              Router
                 │
          ┌──────┴──────┐
          ▼             ▼
       Expert 3      Expert 7
          │             │
          ▼             ▼
       y₃ × p₃       y₇ × p₇
          │             │
          └──────┬──────┘
                 ▼
              SUMA
                 │
                 ▼
                 y
```

---

# 13. Un ejemplo numérico simplificado

Supongamos:

```text
Expert 1 = 0.10
Expert 2 = 0.70
Expert 3 = 0.20
```

y:

```text
top-k = 2
```

Se seleccionan:

```text
Expert 2 → 0.70
Expert 3 → 0.20
```

Supongamos que:

```text
E2(h) = [2, 4]
E3(h) = [6, 1]
```

Entonces, conceptualmente:

$$
y = 0.70[2,4] + 0.20[6,1]
$$

$$
y = [1.4,2.8] + [1.2,0.2]
$$

$$
y = [2.6,3.0]
$$

El resultado combinado continúa hacia el resto del Transformer.

> En implementaciones reales existen detalles adicionales de normalización, routing, capacidad y combinación.

---

# 14. ¿Dónde están los expertos?

Una simplificación frecuente es imaginar:

```text
Attention
   ↓
Expert
```

Pero una arquitectura Transformer MoE suele mantener la atención y sustituir o modificar la parte FFN.

Conceptualmente:

```text
Transformer Block

        │
        ▼
   Self-Attention
        │
        ▼
      Router
        │
   ┌────┼────┐
   ▼    ▼    ▼
  FFN₁ FFN₂ FFN₃
   └────┼────┘
        ▼
     Output
```

Por eso:

> **MoE no significa necesariamente que todo el Transformer sea un conjunto de expertos.**

En muchas arquitecturas, la atención continúa siendo compartida y la especialización ocurre principalmente en las capas FFN/MoE.

---

# 15. ¿Todos los tokens utilizan los mismos expertos?

No.

Esta es una de las propiedades fundamentales de MoE.

Supongamos:

```text
Prompt:

"Calcula la integral de x²."
```

Un token puede activar:

```text
E2 + E7
```

Mientras otro token puede activar:

```text
E1 + E5
```

Y otro:

```text
E3 + E7
```

Incluso dentro del mismo prompt.

Podemos imaginar:

```text
Token 1 → E2 + E7
Token 2 → E1 + E5
Token 3 → E2 + E3
Token 4 → E4 + E7
Token 5 → E1 + E2
```

Por tanto:

> **El routing ocurre a nivel de token y puede cambiar a través de las capas.**

Esto es crucial para entender cómo interactúa un prompt con un MoE.

---

# 16. El prompt y el Router

Aquí aparece una de las preguntas más importantes para Ingeniería de Prompt:

> ¿El prompt puede influir en qué experto procesa un token?

Sí, indirectamente.

El prompt modifica las representaciones internas:

```text
Prompt
   ↓
Tokens
   ↓
Embeddings
   ↓
Attention
   ↓
Hidden States
   ↓
Router
   ↓
Expertos seleccionados
```

Por tanto:

```text
CAMBIO DEL PROMPT
        ↓
CAMBIO DEL CONTEXTO
        ↓
CAMBIO DE ACTIVACIONES
        ↓
CAMBIO DE SCORES DEL ROUTER
        ↓
POSIBLE CAMBIO DE EXPERTOS ACTIVADOS
```

Esto no significa:

> "Escribí Python y el modelo activó el experto de Python."

Esa afirmación sería demasiado simplista.

El routing depende de representaciones internas y puede ser dinámico, distribuido y diferente entre capas.

---

# 17. Un mismo token puede tener diferentes rutas

Consideremos:

```text
"Python"
```

El token puede participar en diferentes contextos:

```text
"Python es un lenguaje de programación."
```

o:

```text
"Python es una serpiente."
```

La representación contextual del token cambia.

Por tanto:

```text
Python
   │
   ├── contexto programación
   │       ↓
   │     hidden state A
   │       ↓
   │     routing A
   │
   └── contexto animal
           ↓
         hidden state B
           ↓
         routing B
```

El token no debe entenderse simplemente como una palabra con un significado fijo.

El contexto modifica su representación.

---

# 18. Routing por capa

Además, un token no tiene necesariamente una única ruta durante todo el modelo.

Podemos imaginar:

```text
Layer 1:

Token → E2 + E5

Layer 2:

Token → E1 + E4

Layer 3:

Token → E3 + E7

Layer 4:

Token → E2 + E6
```

Esto ocurre porque la representación `h` cambia después de cada bloque.

Por tanto:

$$
h_1 \rightarrow Router_1
$$

$$
h_2 \rightarrow Router_2
$$

$$
h_3 \rightarrow Router_3
$$

etc.

La ruta puede ser dinámica a través de la profundidad del modelo.

---

# 19. El problema del balanceo

Aquí aparece uno de los problemas técnicos más importantes de MoE.

Supongamos:

```text
64 expertos
```

pero el router decide enviar:

```text
80% de los tokens → Expert 7
```

y:

```text
20% → los otros 63 expertos
```

Tendríamos:

```text
                 TOKENS
                    │
                    ▼
                 ROUTER
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
      E7          E1...E6      E8...E64
      ████████      █             ▏
      ████████      █             ▏
      ████████
```

Esto es un problema.

Un experto recibe demasiado trabajo mientras otros permanecen infrautilizados.

---

# 20. Load Balancing

Por eso los sistemas MoE necesitan mecanismos para fomentar una utilización razonablemente equilibrada de los expertos.

Esto se conoce como:

> **Load balancing**

El objetivo no es necesariamente que todos los expertos reciban exactamente el mismo número de tokens.

El objetivo es evitar una concentración perjudicial.

Conceptualmente:

```text
MAL BALANCEADO

E1  ██
E2  █
E3  █
E4  █████████████
E5  █
E6  █
```

Mejor:

```text
E1  █████
E2  ████
E3  █████
E4  ████
E5  █████
E6  ████
```

---

# 21. Auxiliary Loss

Una técnica histórica y muy importante consiste en agregar una pérdida auxiliar relacionada con el balanceo de los expertos.

La función objetivo puede representarse conceptualmente como:

$$
L =
L_{task}
+
\lambda L_{balance}
$$

donde:

* `L_task` = pérdida principal del modelo;
* `L_balance` = pérdida asociada al balanceo;
* `λ` = peso de esa pérdida.

Esto introduce una tensión:

```text
Aprender la tarea
       +
utilizar adecuadamente los expertos
```

El router debe aprender a enviar tokens a expertos útiles sin colapsar hacia unos pocos expertos.

---

# 22. Expert Collapse

Un posible problema es que ciertos expertos reciban una cantidad desproporcionada de tokens.

Podemos representar el fenómeno como:

```text
                 ROUTER
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
      E1          E2           E3
       │           │            │
       █           █████████    █
                   ↑
              demasiados
               tokens
```

Esto puede producir:

* expertos sobrecargados;
* expertos infrautilizados;
* menor eficiencia;
* problemas de entrenamiento;
* pérdida de capacidad efectiva.

Por eso el diseño del router es crítico.

---

# 23. Capacity Factor

Otro concepto importante es la **capacidad de los expertos**.

Supongamos un batch con:

```text
1000 tokens
```

y:

```text
10 expertos
```

Idealmente podríamos tener:

```text
100 tokens por experto
```

Pero el routing real puede producir:

```text
E1 → 80
E2 → 120
E3 → 95
E4 → 150
...
```

¿Qué ocurre si un experto recibe más tokens de los que puede procesar eficientemente?

Aquí aparece el concepto de:

> **Capacity factor**

La capacidad asignada puede ser superior a la distribución ideal para permitir cierta variación.

Conceptualmente:

$$
Capacity =
\frac{Tokens \times K}{Experts}
\times CapacityFactor
$$

La formulación exacta depende de la implementación.

---

# 24. ¿Qué ocurre con los tokens que exceden la capacidad?

Dependiendo de la arquitectura, implementación y estrategia de routing, puede haber diferentes mecanismos.

Por ejemplo:

```text
Token
  ↓
Router
  ↓
Expert lleno
  ↓
overflow
```

El sistema puede:

* descartar determinadas rutas;
* enviar el token a otra ruta;
* utilizar mecanismos de capacidad dinámica;
* utilizar estrategias que reduzcan la necesidad de descarte.

Por eso:

> **"Top-k" por sí solo no describe completamente un sistema MoE.**

También debemos estudiar:

```text
Router
+
Top-k
+
Capacity
+
Load balancing
+
Communication
+
Expert computation
+
Combination
```

---

# 25. Expert Choice Routing

No todos los diseños utilizan exactamente:

```text
Token → selecciona expertos
```

Existe también la idea de **Expert Choice Routing**, donde los expertos seleccionan tokens.

En términos conceptuales:

```text
Token Choice:

TOKEN
  ↓
elige expertos
```

frente a:

```text
Expert Choice:

EXPERT
  ↓
elige tokens
```

Esto modifica el problema de asignación y puede proporcionar diferentes propiedades de balanceo y capacidad.

---

# 26. Shared Experts

Una evolución importante de ciertas arquitecturas MoE es utilizar:

```text
Expertos compartidos
+
Expertos enrutados
```

La intuición es que algunas capacidades son comunes a muchos tokens.

Podemos representar:

```text
                TOKEN
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
 Shared Experts        Routed Experts
        │                   │
        │            ┌──────┼──────┐
        │            ▼      ▼      ▼
        │           E1     E7     E12
        │            │      │       │
        └────────────┴──────┴───────┘
                     │
                     ▼
                   salida
```

La familia DeepSeekMoE popularizó una arquitectura que utiliza expertos compartidos junto con una segmentación más fina de expertos enrutados.

Esto refleja una idea importante:

> No todo conocimiento necesita ser tratado como completamente exclusivo.

---

# 27. Fine-Grained Experts

Otra estrategia consiste en dividir los expertos en unidades más pequeñas.

En lugar de:

```text
8 expertos grandes
```

podemos construir conceptualmente:

```text
64 expertos pequeños
```

y activar una combinación de ellos.

La ventaja potencial es tener mayor flexibilidad en las combinaciones.

En DeepSeekMoE, por ejemplo, se propuso dividir finamente los expertos y activar una cantidad proporcionalmente mayor de expertos pequeños, junto con expertos compartidos.

---

# 28. Especialización de expertos

Es tentador decir:

```text
Expert 1 = matemáticas
Expert 2 = programación
Expert 3 = español
Expert 4 = derecho
```

Pero debemos tener cuidado.

No podemos asumir automáticamente que los expertos tienen especializaciones humanas limpias.

La especialización puede aparecer como:

* patrones lingüísticos;
* estructuras sintácticas;
* dominios;
* características semánticas;
* patrones estadísticos;
* tipos de razonamiento;
* combinaciones de características.

Y puede ser distribuida.

Por tanto, es más correcto decir:

> **Los expertos pueden desarrollar patrones de especialización debido al entrenamiento y al routing, pero sus funciones internas no necesariamente corresponden a categorías humanas simples.**

---

# 29. MoE no significa "un experto por tema"

Esta distinción es fundamental para Ingeniería de Prompt.

Incorrecto:

```text
Prompt sobre Python
       ↓
Experto Python
```

Más correcto:

```text
Prompt
  ↓
representaciones contextuales
  ↓
router
  ↓
scores
  ↓
expertos seleccionados
  ↓
combinación
```

El comportamiento emerge del sistema completo.

---

# 30. MoE y entrenamiento

Durante el entrenamiento tenemos:

```text
Dataset
   ↓
Tokens
   ↓
Transformer
   ↓
Router
   ↓
Expertos
   ↓
Output
   ↓
Loss
   ↓
Backpropagation
```

El router aprende progresivamente a asignar tokens.

Los expertos también actualizan sus parámetros.

Existe una interacción entre:

```text
Router
   ↕
Expertos
   ↕
Representaciones
```

Esto hace que el entrenamiento MoE sea considerablemente más complejo que simplemente duplicar una FFN.

---

# 31. MoE e inferencia

Durante inferencia:

```text
Prompt
   ↓
Tokenización
   ↓
Prefill
   ↓
Attention
   ↓
Router
   ↓
Expertos seleccionados
   ↓
Logits
   ↓
Sampling
   ↓
Nuevo token
```

Para cada token, solamente determinadas rutas expertas son activadas.

Pero esto no significa que MoE sea automáticamente más rápido en cualquier hardware o implementación.

---

# 32. El problema de la memoria

MoE reduce el cálculo activo, pero no elimina el almacenamiento de parámetros.

Supongamos:

```text
Modelo MoE:

500B parámetros totales
50B activos por token
```

Aunque solamente se calculen aproximadamente 50B parámetros por token, el sistema todavía necesita acceso a una gran cantidad de pesos.

Por tanto:

```text
COMPUTACIÓN
     ≠
MEMORIA
```

Un modelo puede ser eficiente en FLOPs activos y continuar siendo costoso en memoria.

---

# 33. Comunicación entre GPUs

Aquí aparece uno de los mayores desafíos de ingeniería.

Supongamos:

```text
GPU 1 → Expertos 1-4
GPU 2 → Expertos 5-8
GPU 3 → Expertos 9-12
GPU 4 → Expertos 13-16
```

Los tokens necesitan ser enviados a las GPUs que contienen los expertos seleccionados.

Podemos imaginar:

```text
GPU 1
  │
  ├──────────► GPU 3
  │
  └──────────► GPU 4

GPU 2
  │
  └──────────► GPU 1
```

Esto produce comunicación entre dispositivos.

En sistemas distribuidos aparece el patrón conocido como:

> **All-to-All communication**

---

# 34. Expert Parallelism

Una estrategia habitual es distribuir expertos entre diferentes dispositivos.

```text
              MoE Layer
                  │
      ┌───────────┼───────────┐
      ▼           ▼           ▼
     GPU1        GPU2        GPU3
    E1-E4        E5-E8       E9-E12
```

Esto se conoce como:

> **Expert Parallelism**

Pero normalmente se combina con otras formas de paralelismo:

* data parallelism;
* tensor parallelism;
* pipeline parallelism;
* expert parallelism.

En sistemas grandes, el rendimiento depende tanto de la arquitectura matemática como de la infraestructura de comunicación.

---

# 35. Compute vs Communication

Una manera importante de pensar en MoE es:

```text
Dense:

mucho cálculo
+
relativamente simple routing
```

MoE:

```text
menos cálculo activo
+
routing
+
movimiento de tokens
+
comunicación
+
gestión de capacidad
```

Por tanto:

> **La eficiencia teórica en FLOPs no garantiza automáticamente una menor latencia real.**

El hardware, la interconexión y la implementación importan enormemente.

---

# 36. Prefill y Decode

Durante inferencia existen dos fases conceptualmente importantes.

## Prefill

Se procesa inicialmente el prompt:

```text
Prompt completo
      ↓
   Prefill
      ↓
activaciones
      ↓
KV Cache
```

Muchos tokens se procesan en paralelo.

## Decode

Después se generan nuevos tokens:

```text
Token generado
      ↓
siguiente token
      ↓
siguiente token
      ↓
...
```

Cada nuevo token atraviesa nuevamente el sistema de routing.

Por tanto, el patrón de acceso a expertos puede cambiar durante la generación.

---

# 37. Ejemplo completo

Supongamos:

```text
Prompt:

"Analiza este código Python y explica
por qué la función produce un error."
```

Podemos conceptualizar:

```text
             PROMPT
                │
                ▼
             TOKENS
                │
                ▼
           Transformer
                │
                ▼
             Router
                │
        ┌───────┼────────┐
        ▼       ▼        ▼
       E2       E7       E11
        │       │        │
        └───────┼────────┘
                ▼
             combinar
                │
                ▼
          representación
                │
                ▼
            siguiente
```

Pero no debemos concluir:

```text
E7 = experto Python
```

El router simplemente seleccionó determinados parámetros expertos para esa representación concreta.

---

# 38. ¿Qué cambia para el Prompt Engineer?

Aquí aparece la conexión directa con Ingeniería de Prompt.

En un Transformer denso:

```text
Prompt
   ↓
activaciones
   ↓
mismo conjunto general de parámetros
```

En MoE:

```text
Prompt
   ↓
activaciones
   ↓
routing
   ↓
subconjunto de expertos
```

Por tanto, el prompt puede afectar no solamente:

```text
qué información recibe el modelo
```

sino indirectamente:

```text
qué representaciones internas produce
        ↓
qué decisiones toma el router
        ↓
qué expertos participan
```

---

# 39. Prompt ambiguo vs prompt estructurado

Supongamos:

```text
"Analiza esto."
```

Existe poca información contextual.

Ahora:

```text
"Actúa como auditor financiero.
Analiza las transacciones buscando:
1. duplicados,
2. anomalías de fechas,
3. inconsistencias contables.
Devuelve los hallazgos en JSON."
```

El segundo prompt proporciona mucho más contexto.

Esto puede modificar las representaciones internas y, por tanto, las rutas computacionales internas.

Pero debemos formular correctamente la conclusión:

> Un prompt más específico puede cambiar las activaciones y potencialmente el routing; no significa que el ingeniero pueda controlar explícitamente qué experto se activa.

---

# 40. No existe normalmente un comando:

```text
USE EXPERT 7
```

El usuario normalmente no controla directamente:

```text
router → expert
```

El routing es parte del modelo.

Por eso:

```text
Prompt Engineering
        ≠
Expert Routing Control
```

Sin embargo:

```text
Prompt Engineering
        ↓
cambio de contexto
        ↓
cambio de representación
        ↓
posible cambio de routing
```

Esta distinción será muy importante cuando estudiemos sistemas de IA más avanzados.

---

# 41. MoE y comportamiento reproducible

Supongamos que ejecutamos:

```text
Prompt A
```

y obtenemos una determinada secuencia de rutas.

Si cambiamos:

```text
Prompt A
```

por:

```text
Prompt A + una instrucción adicional
```

las representaciones pueden cambiar.

Por tanto:

```text
Prompt
  ↓
Hidden states
  ↓
Router scores
  ↓
Routing
  ↓
Expert computation
```

El prompt forma parte de la cadena causal que conduce al comportamiento observable.

Sin embargo, el usuario no puede observar directamente todo ese proceso mediante la salida normal del modelo.

---

# 42. MoE y temperatura

No debemos confundir:

```text
Router
```

con:

```text
Sampling
```

Son mecanismos diferentes.

El router decide aproximadamente:

```text
qué expertos participan
```

El sampling decide:

```text
qué token producir
```

Una arquitectura simplificada:

```text
Prompt
  │
  ▼
Transformer + Router + Experts
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
Token
```

Por tanto:

```text
Router ≠ Temperature
```

---

# 43. MoE y temperatura: una distinción crítica

Supongamos:

```text
Temperature = 0.2
```

Eso afecta el proceso de selección de tokens en la generación, dependiendo de la implementación.

No significa:

```text
"activar menos expertos"
```

Del mismo modo:

```text
Temperature = 1.5
```

no significa:

```text
"activar más expertos"
```

Son mecanismos diferentes.

---

# 44. MoE y contexto

También debemos distinguir:

```text
Context Window
```

de:

```text
Expert Capacity
```

El contexto determina cuántos tokens puede considerar el modelo dentro de una ventana determinada.

La capacidad del experto determina cuánto tráfico de tokens puede manejar una determinada ruta dentro de una capa/implementación.

Por tanto:

```text
Context Window
      ≠
Expert Capacity
```

---

# 45. MoE y parámetros

Otro error frecuente:

> "Un modelo de 500B siempre es mucho más costoso que uno de 70B."

No necesariamente, si estamos comparando arquitectura y carga de trabajo sin más detalles.

Debemos distinguir:

```text
Total Parameters
Active Parameters
FLOPs
Memory
Bandwidth
Communication
Latency
```

Una arquitectura MoE puede tener:

```text
muchísimos parámetros totales
```

pero:

```text
una cantidad mucho menor de parámetros activos por token.
```

Eso es precisamente una de sus propiedades fundamentales.

---

# 46. Ejemplo: Mixtral

Un ejemplo conocido de esta arquitectura es **Mixtral 8×7B**.

Su diseño utiliza ocho bloques FFN expertos y selecciona dos expertos para cada token en cada capa MoE. El trabajo original describe aproximadamente 47B parámetros totales y alrededor de 13B parámetros activos por token.

Conceptualmente:

```text
8 expertos
   │
   ├── E1
   ├── E2
   ├── E3
   ├── E4
   ├── E5
   ├── E6
   ├── E7
   └── E8
        │
        ▼
   Router top-2
        │
   ┌────┴────┐
   ▼         ▼
  E3        E7
   └────┬────┘
        ▼
      salida
```

Este ejemplo es especialmente útil para comprender la diferencia entre:

```text
capacidad total
```

y:

```text
cómputo activo.
```

---

# 47. DeepSeekMoE

DeepSeekMoE introdujo estrategias diferentes para mejorar la utilización de expertos.

Dos ideas particularmente importantes son:

### 1. Fine-grained expert segmentation

Dividir los expertos en unidades más pequeñas.

### 2. Shared experts

Mantener determinados expertos compartidos para capturar conocimiento común.

La motivación es aumentar la flexibilidad de la combinación de expertos y reducir redundancias entre expertos enrutados.

Es una buena demostración de que:

> **MoE no es una única arquitectura.**

Es una familia de diseños.

---

# 48. Familias de MoE

Cuando alguien dice:

> "Este modelo es MoE"

todavía necesitamos preguntar:

```text
¿Qué tipo de MoE?
```

Porque pueden variar:

* número de expertos;
* top-k;
* función de routing;
* función de combinación;
* capacidad;
* balanceo;
* expertos compartidos;
* tamaño de expertos;
* granularidad;
* distribución entre GPUs;
* estrategia de entrenamiento;
* estrategia de inferencia.

Por eso MoE debe entenderse como un **paradigma arquitectónico**, no como una única implementación.

---

# 49. MoE como función matemática

Una forma general de representar una capa MoE es:

$$
y = \sum_{i=1}^{N} g_i(x)E_i(x)
$$

donde:

* `x` = representación de entrada;
* `Eᵢ(x)` = salida del experto `i`;
* `gᵢ(x)` = peso asignado por el router;
* `N` = número total de expertos.

En una MoE dispersa:

$$
g_i(x) = 0
$$

para la mayoría de expertos.

Por ejemplo:

```text
N = 64

g(x):

E1   = 0
E2   = 0
E3   = 0.63
E4   = 0
E5   = 0.37
...
E64  = 0
```

Solamente:

```text
E3 + E5
```

participan activamente.

---

# 50. Una interpretación desde álgebra lineal

En una arquitectura densa:

$$
y = E(x)
$$

Existe una transformación principal.

En MoE:

$$
y =
\sum_i g_i(x)E_i(x)
$$

Tenemos un conjunto de transformaciones:

$$
E_1,E_2,\ldots,E_N
$$

y una función:

$$
g(x)
$$

que determina cómo combinarlas.

Por eso podemos pensar en MoE como:

> **Una familia de transformaciones condicionadas por la entrada.**

---

# 51. Conditional Computation

Este concepto es fundamental.

MoE pertenece a una idea más amplia:

> **Conditional Computation**

La cantidad o ruta de cálculo depende de la entrada.

En lugar de:

```text
Input A
   ↓
misma ruta
```

podemos tener:

```text
Input A → ruta A
Input B → ruta B
Input C → ruta C
```

Esto abre la posibilidad de utilizar recursos computacionales de forma adaptativa.

---

# 52. Sparse ≠ Quantized

Dos conceptos que suelen confundirse:

### MoE

Reduce el cálculo activo mediante routing.

### Quantization

Reduce la precisión numérica de los parámetros y/o activaciones.

Son dimensiones diferentes.

Podemos tener:

```text
Dense + FP16
Dense + INT4
MoE + FP16
MoE + INT4
```

Por tanto:

```text
MoE ≠ Quantization
```

Incluso pueden combinarse.

---

# 53. Sparse ≠ Long Context

Tampoco debemos confundir:

```text
MoE
```

con:

```text
Long Context
```

MoE trata principalmente de:

```text
qué parámetros computacionales participan
```

Long Context trata principalmente de:

```text
cuánta información contextual puede manejar el modelo
```

Son problemas diferentes.

---

# 54. Sparse ≠ RAG

RAG tampoco es MoE.

RAG:

```text
consulta
  ↓
retriever
  ↓
documentos
  ↓
LLM
```

MoE:

```text
token
  ↓
router
  ↓
expertos
  ↓
LLM
```

Pueden utilizarse juntos:

```text
Usuario
   ↓
RAG
   ↓
Contexto recuperado
   ↓
LLM MoE
   ↓
Router
   ↓
Expertos
   ↓
Respuesta
```

---

# 55. MoE + RAG

Este punto es especialmente importante para sistemas empresariales.

Podemos tener:

```text
             USUARIO
                 │
                 ▼
              QUERY
                 │
                 ▼
             RETRIEVER
                 │
                 ▼
             DOCUMENTOS
                 │
                 ▼
             CONTEXTO
                 │
                 ▼
             LLM MoE
                 │
              ROUTER
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
       E1       E4       E9
        │        │        │
        └────────┼────────┘
                 ▼
              respuesta
```

Esto demuestra por qué:

> **Arquitectura del modelo, contexto y prompting son capas diferentes del sistema.**

---

# 56. El pipeline completo

Hasta ahora podemos ampliar nuestro modelo conceptual:

```text
MODELO
   │
   ▼
ARQUITECTURA
   │
   ├── Attention
   │
   ├── FFN / Experts
   │
   └── Router
   │
   ▼
CONTEXTO
   │
   ▼
PROMPT
   │
   ▼
INFERENCIA
   │
   ├── Routing
   ├── Attention
   ├── Expert computation
   └── Sampling
   │
   ▼
RESPUESTA
   │
   ▼
EVALUACIÓN
```

Esta es una de las ideas centrales de todo el repositorio.

---

# 57. ¿Puede el prompt "activar" un experto?

Debemos responder cuidadosamente.

### Incorrecto:

> "Escribe matemáticas para activar el experto matemático."

### Más correcto:

> El contenido del prompt modifica las representaciones internas del modelo y esas representaciones pueden influir en las decisiones del router.

La diferencia parece pequeña, pero conceptualmente es enorme.

El prompt engineer normalmente no controla directamente:

```text
Expert 3
```

Controla:

```text
información
estructura
contexto
instrucciones
restricciones
ejemplos
```

y esas variables afectan el procesamiento interno.

---

# 58. Implicación para Prompt Engineering

Esto conduce a una conclusión importante:

> **La calidad del prompt no depende únicamente del texto visible; también depende de cómo ese texto interactúa con la arquitectura del modelo.**

Podemos representar:

```text
Prompt
   │
   ▼
Tokenización
   │
   ▼
Embeddings
   │
   ▼
Attention
   │
   ▼
Hidden States
   │
   ▼
Router
   │
   ▼
Experts
   │
   ▼
Logits
   │
   ▼
Sampling
   │
   ▼
Respuesta
```

Por eso dos modelos que reciben exactamente el mismo prompt pueden producir respuestas diferentes.

---

# 59. Mismo prompt, diferente arquitectura

Supongamos:

```text
Prompt:

"Explica cómo funciona un Transformer."
```

Modelo A:

```text
Dense Transformer
```

Modelo B:

```text
MoE Transformer
```

Aunque el prompt sea idéntico:

```text
Prompt A = Prompt B
```

el procesamiento interno puede ser diferente.

```text
Dense:

Prompt
 ↓
Attention
 ↓
FFN
 ↓
Output
```

```text
MoE:

Prompt
 ↓
Attention
 ↓
Router
 ↓
Expertos
 ↓
Output
```

Por tanto:

> **Un prompt no tiene un comportamiento universal independiente de la arquitectura.**

Esta es una de las ideas fundamentales de este repositorio.

---

# 60. ¿MoE siempre es mejor?

No debemos formularlo así.

MoE introduce ventajas potenciales:

* gran capacidad total;
* menor cálculo activo relativo;
* conditional computation;
* posibilidad de especialización;
* escalabilidad.

Pero también introduce costos y complejidad:

* routing;
* balanceo;
* comunicación;
* memoria;
* implementación distribuida;
* capacidad de expertos;
* posibles problemas de utilización;
* complejidad de entrenamiento e inferencia.

Por tanto:

```text
MoE
=
trade-offs
```

No existe una arquitectura universalmente óptima para todas las situaciones.

---

# 61. Tabla conceptual

| Característica                 | Dense Transformer               | MoE Transformer              |
| ------------------------------ | ------------------------------- | ---------------------------- |
| Parámetros totales             | Menores para una capacidad dada | Pueden ser muy grandes       |
| Parámetros activos             | Alta proporción                 | Subconjunto                  |
| Routing                        | No requiere routing de expertos | Sí                           |
| Expertos                       | No                              | Sí                           |
| Cómputo condicional            | Limitado                        | Fundamental                  |
| Balanceo                       | No aplica igual                 | Crítico                      |
| Comunicación distribuida       | Menor complejidad específica    | Puede ser crítica            |
| Memoria                        | Depende del tamaño              | Puede ser muy elevada        |
| Especialización                | Distribuida                     | Puede emerger entre expertos |
| Complejidad de infraestructura | Menor                           | Mayor                        |

---

# 62. Métricas que un ingeniero debe observar

Cuando evaluamos un modelo MoE no basta con preguntar:

> "¿Cuántos parámetros tiene?"

Debemos observar:

```text
1. Parámetros totales
2. Parámetros activos
3. FLOPs activos
4. Número de expertos
5. Top-k
6. Utilización por experto
7. Balance de carga
8. Capacidad
9. Comunicación
10. Memoria
11. Latencia
12. Throughput
13. Calidad de salida
```

Esto es mucho más cercano a la ingeniería real.

---

# 63. Diagnóstico de un sistema MoE

Si tenemos acceso a métricas internas, podríamos observar:

```text
Expert utilization

E1 → 13%
E2 → 15%
E3 → 17%
E4 → 14%
E5 → 16%
E6 → 12%
E7 → 7%
E8 → 6%
```

Esto nos indica que existe cierta desigualdad.

Pero:

> **Utilización desigual no implica automáticamente que el modelo esté fallando.**

La interpretación depende de la arquitectura, entrenamiento, distribución de datos y estrategia de routing.

---

# 64. ¿Qué debería investigar un AI Engineer?

Cuando recibe un modelo MoE, debería preguntar:

```text
¿Qué cantidad de expertos existen?

¿Cuántos se activan por token?

¿Dónde están ubicados?

¿Cómo funciona el router?

¿Cómo se balancea la carga?

¿Cómo se maneja el overflow?

¿Existen expertos compartidos?

¿Cómo están distribuidos entre GPUs?

¿Qué comunicación requiere el modelo?

¿Cuántos parámetros están activos?

¿Cuánta memoria necesita?

¿Cuál es la latencia real?
```

Esto transforma el conocimiento de:

```text
"sé qué significa MoE"
```

en:

```text
"sé analizar una arquitectura MoE".
```

---

# 65. Errores frecuentes

## Error 1

> "MoE significa que cada experto conoce un tema."

No necesariamente.

---

## Error 2

> "Un modelo de 500B utiliza 500B parámetros para cada token."

No necesariamente.

---

## Error 3

> "MoE siempre es más rápido."

No necesariamente.

---

## Error 4

> "Top-k controla la temperatura."

Incorrecto.

---

## Error 5

> "El prompt selecciona directamente un experto."

Incorrecto como modelo general.

---

## Error 6

> "MoE elimina el problema de memoria."

Incorrecto.

---

## Error 7

> "Más expertos siempre significa mejor modelo."

No necesariamente.

---

# 66. Modelo mental definitivo

Para recordar MoE podemos utilizar esta secuencia:

```text
TOKEN
  │
  ▼
REPRESENTACIÓN
  │
  ▼
ROUTER
  │
  ▼
SCORES
  │
  ▼
TOP-K
  │
  ▼
EXPERTOS
  │
  ▼
COMBINACIÓN
  │
  ▼
SIGUIENTE PARTE DEL TRANSFORMER
```

Y desde el punto de vista de infraestructura:

```text
                 TOKEN
                   │
                   ▼
                ROUTER
                   │
         ┌─────────┼─────────┐
         ▼         ▼         ▼
       GPU 1     GPU 2     GPU 3
       E1-E4     E5-E8     E9-E12
         │         │         │
         └─────────┼─────────┘
                   ▼
               COMBINAR
```

---

# 67. Conexión con el modelo mental del repositorio

Hasta ahora hemos estudiado:

```text
MODELO
  ↓
TOKENS
  ↓
EMBEDDINGS
  ↓
TRANSFORMER
  ↓
ATTENTION
  ↓
FFN
```

Ahora incorporamos:

```text
MODELO
  ↓
TOKENS
  ↓
EMBEDDINGS
  ↓
TRANSFORMER
  ↓
ATTENTION
  ↓
ROUTER
  ↓
EXPERTOS
  ↓
COMBINACIÓN
```

Esto permite comprender por qué:

> **La arquitectura interna modifica la forma en que un prompt es procesado.**

---

# 68. Del prompt al experto: la cadena completa

Podemos construir una cadena conceptual:

$$
Prompt
\rightarrow Tokens
\rightarrow Embeddings
\rightarrow Attention
\rightarrow Hidden\ States
\rightarrow Router
\rightarrow Experts
\rightarrow Logits
\rightarrow Sampling
\rightarrow Token
$$

Cada etapa transforma la información.

Por eso no debemos pensar:

```text
Prompt → respuesta
```

sino:

```text
Prompt
   ↓
representación
   ↓
procesamiento
   ↓
routing
   ↓
transformaciones
   ↓
distribución de probabilidad
   ↓
sampling
   ↓
respuesta
```

Este cambio de mentalidad es fundamental para pasar de **prompting básico** a **ingeniería de sistemas de IA**.

---

# 69. Nivel avanzado: ¿el router aprende "qué pensar"?

No.

Esta es una interpretación antropomórfica que debemos evitar.

El router no "piensa":

> "Este problema necesita matemáticas."

Más correctamente:

```text
hidden state
     ↓
función de routing
     ↓
scores
     ↓
selección
```

La selección es una consecuencia matemática del modelo entrenado.

Podemos estudiar las correlaciones entre routing y determinadas características, pero no debemos convertir esas correlaciones automáticamente en explicaciones semánticas.

---

# 70. Nivel Maestría / PhD: la investigación sobre routing

A nivel avanzado aparecen preguntas como:

```text
¿Por qué ciertos expertos reciben determinados tokens?

¿Cómo emerge la especialización?

¿Existe redundancia entre expertos?

¿El router aprende representaciones semánticamente coherentes?

¿Cómo afecta el ruido del router al entrenamiento?

¿Cómo evitar expert collapse?

¿Cómo optimizar all-to-all communication?

¿Cómo cambia el routing durante el entrenamiento?

¿La especialización es estable?

¿Cómo influye la granularidad de los expertos?

¿Cómo diseñar mejores funciones de gating?
```

Estas preguntas llevan desde la ingeniería de modelos hacia la investigación científica.

---

# 71. Una observación importante sobre interpretabilidad

Podemos registrar:

```text
Token → Expert
```

y encontrar patrones.

Por ejemplo:

```text
tokens relacionados con código
       ↓
mayor frecuencia en determinados expertos
```

Eso puede ser evidencia de cierta especialización.

Pero no debemos concluir automáticamente:

> "Este experto es el experto de programación."

La interpretación rigurosa requiere experimentos controlados.

Por ejemplo:

```text
1. Construir dataset controlado
2. Registrar routing
3. Medir utilización
4. Intervenir sobre expertos
5. Evaluar cambios
6. Comparar contra controles
```

Esto convierte una observación en una investigación reproducible.

---

# 72. Experimento conceptual

Podemos diseñar un experimento:

### Dataset A

```text
1000 problemas matemáticos
```

### Dataset B

```text
1000 fragmentos de código
```

### Dataset C

```text
1000 textos generales
```

Registramos:

```text
token
layer
expert
routing weight
```

Después calculamos:

$$
P(Expert_i | Domain)
$$

Podemos investigar si ciertos expertos tienen una asociación estadística con determinados dominios.

Pero:

$$
P(Expert_i | Domain)
$$

no demuestra por sí sola una especialización semántica causal.

---

# 73. Conexión con la Ingeniería de Prompt

Este capítulo modifica nuestra definición de prompt engineering.

En un nivel inicial:

> Prompt Engineering = escribir buenas instrucciones.

En un nivel más avanzado:

> Prompt Engineering = diseñar entradas que produzcan el comportamiento deseado de un modelo bajo determinadas condiciones de contexto, arquitectura e inferencia.

Con MoE añadimos:

```text
Prompt
   ↓
representaciones
   ↓
routing
   ↓
subred computacional activa
```

Por eso la arquitectura importa.

---

# 74. Regla práctica

Cuando trabajes con un modelo MoE:

### No pienses:

> "¿Qué palabra activa al experto correcto?"

Piensa:

> "¿Qué contexto y estructura de entrada permiten al modelo construir una representación adecuada para la tarea?"

Esto es mucho más robusto.

---

# 75. Checklist de Ingeniería

Antes de evaluar un modelo MoE:

### Arquitectura

* [ ] ¿Es realmente MoE?
* [ ] ¿Cuántos expertos tiene?
* [ ] ¿Dónde están ubicados?
* [ ] ¿Qué componentes son expertos?

### Routing

* [ ] ¿Utiliza top-1?
* [ ] ¿Utiliza top-2?
* [ ] ¿Utiliza otro mecanismo?
* [ ] ¿Cómo calcula los scores?

### Capacidad

* [ ] ¿Cuál es la capacidad por experto?
* [ ] ¿Existe capacity factor?
* [ ] ¿Cómo se maneja overflow?

### Balanceo

* [ ] ¿Cómo se evita la concentración?
* [ ] ¿Existe auxiliary loss?
* [ ] ¿Existen mecanismos alternativos?

### Infraestructura

* [ ] ¿Cómo se distribuyen los expertos?
* [ ] ¿Existe expert parallelism?
* [ ] ¿Cuánto all-to-all communication existe?

### Inferencia

* [ ] ¿Cuántos parámetros están activos?
* [ ] ¿Cuánta memoria necesita?
* [ ] ¿Cuál es la latencia?
* [ ] ¿Cuál es el throughput?

### Prompt Engineering

* [ ] ¿El prompt proporciona contexto suficiente?
* [ ] ¿La tarea está claramente especificada?
* [ ] ¿Se ha evaluado con diferentes prompts?
* [ ] ¿Se ha probado consistencia entre ejecuciones?

---

# 76. Ejercicio 1 — Conceptual

Supón:

```text
16 expertos
top-k = 2
```

Pregunta:

> ¿Cuántos expertos procesa directamente cada token?

Respuesta:

```text
2
```

Pero:

> ¿Cuántos expertos existen en total?

```text
16
```

Por eso:

```text
total ≠ activos
```

---

# 77. Ejercicio 2 — Routing

Tenemos:

```text
E1 = 0.05
E2 = 0.12
E3 = 0.51
E4 = 0.08
E5 = 0.24
```

Con:

```text
top-k = 2
```

Los expertos seleccionados son:

```text
E3
E5
```

Porque:

```text
0.51 > 0.24 > 0.12 > 0.08 > 0.05
```

---

# 78. Ejercicio 3 — Prompt

Compara:

```text
Prompt A:

"Explica Python."
```

con:

```text
Prompt B:

"Explica Python desde la perspectiva de un ingeniero
de software. Incluye sintaxis, tipos de datos,
funciones, manejo de errores y ejemplos prácticos."
```

Pregunta:

> ¿Podría cambiar el procesamiento interno?

Sí.

La razón conceptual es:

```text
Prompt diferente
      ↓
representación diferente
      ↓
activaciones diferentes
      ↓
routing potencialmente diferente
```

Pero no podemos afirmar qué experto concreto se activará sin observar el modelo.

---

# 79. Ejercicio 4 — Arquitectura

Explica la diferencia:

```text
Dense Transformer
```

vs.

```text
MoE Transformer
```

Una respuesta correcta debería mencionar:

```text
Dense:
misma transformación general

MoE:
routing condicional
+
subconjunto de expertos
```

---

# 80. Preguntas de nivel avanzado

1. ¿Por qué MoE puede aumentar la capacidad sin aumentar proporcionalmente los FLOPs activos?

2. ¿Por qué los parámetros activos no equivalen a los parámetros totales?

3. ¿Qué problema intenta solucionar el load balancing?

4. ¿Por qué el routing puede convertirse en un cuello de botella?

5. ¿Por qué MoE puede necesitar comunicación all-to-all?

6. ¿Qué diferencia existe entre token-choice routing y expert-choice routing?

7. ¿Por qué no debemos afirmar que cada experto corresponde a una disciplina humana?

8. ¿Cómo puede un prompt modificar indirectamente el routing?

9. ¿Por qué temperature y routing son mecanismos diferentes?

10. ¿Por qué MoE y quantization son dimensiones independientes?

11. ¿Qué ventajas pueden aportar los shared experts?

12. ¿Qué problema intenta solucionar la segmentación fina de expertos?

---

# 81. Lo que debemos llevarnos

La idea más importante de este capítulo puede resumirse así:

> **Un modelo MoE contiene muchos expertos, pero cada token utiliza solamente una parte de ellos mediante un mecanismo de routing.**

La arquitectura puede representarse como:

```text
             TOKEN
                │
                ▼
          HIDDEN STATE
                │
                ▼
             ROUTER
                │
                ▼
             TOP-K
                │
        ┌───────┼───────┐
        ▼       ▼       ▼
       E2      E7      E11
        │       │       │
        └───────┼───────┘
                ▼
             COMBINAR
                │
                ▼
             OUTPUT
```

Y la idea de Ingeniería de Prompt:

```text
PROMPT
   ↓
CONTEXTO
   ↓
REPRESENTACIONES
   ↓
ROUTING
   ↓
EXPERTOS
   ↓
LOGITS
   ↓
SAMPLING
   ↓
RESPUESTA
```

---

# 82. Resumen final

## Mixture of Experts

MoE es una arquitectura que utiliza múltiples expertos y un mecanismo de routing para seleccionar qué subconjunto participa en el procesamiento de cada token.

### Conceptos fundamentales

```text
Expert
Router
Gating
Top-K
Sparse Activation
Load Balancing
Capacity
Overflow
Shared Experts
Expert Parallelism
All-to-All
Active Parameters
Total Parameters
Conditional Computation
```

### Idea fundamental

```text
Muchos parámetros disponibles
          ↓
pocos parámetros activos por token
          ↓
mayor capacidad potencial
          +
cálculo condicional
```

### Pero también:

```text
Mayor capacidad
       +
Mayor complejidad
       +
Routing
       +
Balanceo
       +
Comunicación
       +
Memoria
```

---

# 83. Preparación para el siguiente capítulo

Ahora sabemos que un Transformer puede ser:

```text
Denso
```

o:

```text
Sparse / MoE
```

y que el procesamiento puede ser condicionado por el token.

El siguiente paso es estudiar otra dimensión fundamental:

> **¿Qué ocurre cuando el modelo no solamente predice el siguiente token, sino que está optimizado para resolver problemas mediante procesos de razonamiento más explícitos o deliberativos?**

Esto nos lleva al siguiente capítulo:

```text
05-Modelos-de-Razonamiento.md
```

Allí estudiaremos:

```text
LLM tradicional
       ↓
modelos de razonamiento
       ↓
reasoning tokens
       ↓
deliberación
       ↓
test-time compute
       ↓
verificación
       ↓
search
       ↓
reasoning + tools
```

y, especialmente importante para este repositorio:

```text
¿Cómo cambia el Prompt Engineering
cuando el modelo incorpora mecanismos
de razonamiento?
```
