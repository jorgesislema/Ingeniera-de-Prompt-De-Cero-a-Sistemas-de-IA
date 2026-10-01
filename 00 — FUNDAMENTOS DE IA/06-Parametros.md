# 06 — ¿Qué son los parámetros de un modelo de IA?

> **Pregunta central:** ¿Qué es exactamente lo que el modelo aprende durante el entrenamiento?

Cuando hablamos de modelos de inteligencia artificial aparecen constantemente expresiones como:

* "Este modelo tiene 7 mil millones de parámetros."
* "Este modelo tiene cientos de miles de millones de parámetros."
* "El modelo tiene más capacidad."
* "Este modelo fue cuantizado a 4 bits."
* "Podemos hacer fine-tuning de sus parámetros."
* "Lo entrenamos con LoRA sin modificar todo el modelo."

Pero ¿qué significa realmente **parámetro**?

Un parámetro es, en términos generales, un **valor numérico aprendido durante el entrenamiento que forma parte de la función que utiliza el modelo para transformar sus entradas en salidas**.

En un modelo neuronal moderno, estos valores son principalmente **pesos y sesgos**.

---

# 1. La idea más sencilla

Imaginemos una función matemática muy simple:

```text
y = 3x + 2
```

Aquí tenemos:

* `x` → entrada
* `y` → salida
* `3` → parámetro
* `2` → parámetro

Si cambiamos los parámetros:

```text
y = 10x - 5
```

la función se comporta de manera diferente.

La idea fundamental en una red neuronal es similar, pero a una escala gigantesca.

En lugar de tener:

```text
2 parámetros
```

podemos tener:

```text
7.000.000.000 parámetros
```

o:

```text
70.000.000.000 parámetros
```

o más.

Cada parámetro participa, directa o indirectamente, en las transformaciones que realiza el modelo.

---

# 2. Una definición más técnica

Podemos representar un modelo mediante una función:

```text
y = fθ(x)
```

donde:

* `x` = entrada
* `y` = salida
* `f` = función implementada por la arquitectura del modelo
* `θ` = conjunto de parámetros del modelo

Por tanto:

```text
θ = {θ₁, θ₂, θ₃, ..., θₙ}
```

El símbolo `θ` representa **todos los parámetros aprendibles del modelo**.

Por ejemplo:

```text
θ₁ = 0.372
θ₂ = -1.284
θ₃ = 0.0087
...
θₙ = 0.913
```

No debemos imaginar estos números como una lista de "respuestas almacenadas".

Son valores que permiten que la arquitectura realice determinadas transformaciones matemáticas.

---

# 3. Parámetros no significa "conocimientos almacenados"

Esta es una de las ideas más importantes de todo el curso.

Un modelo lingüístico no funciona normalmente como una base de datos:

```text
Pregunta:
¿Quién escribió Don Quijote?

Base de datos:
Miguel de Cervantes
```

y simplemente recupera esa respuesta.

Durante el entrenamiento, el modelo modifica millones o miles de millones de parámetros para aprender patrones estadísticos y representaciones útiles.

Por eso es mejor pensar:

```text
DATOS
   ↓
ENTRENAMIENTO
   ↓
AJUSTE DE PARÁMETROS
   ↓
MODELO
   ↓
ENTRADA
   ↓
SALIDA
```

Los parámetros son parte de lo que queda aprendido después del entrenamiento.

---

# 4. Una analogía sencilla: las perillas

Imaginemos una enorme consola de sonido.

Cada perilla controla algo diferente:

```text
[Volumen]
[Graves]
[Agudos]
[Reverb]
[Balance]
...
```

Un modelo neuronal puede imaginarse como una consola con una cantidad gigantesca de valores internos.

Durante el entrenamiento, el sistema ajusta esos valores.

Algunos cambios pueden hacer que el modelo:

* represente mejor determinado patrón;
* reconozca determinadas relaciones;
* produzca mejores predicciones;
* genere lenguaje más coherente;
* represente características visuales;
* aprenda relaciones entre diferentes conceptos.

Pero la analogía tiene una limitación importante:

> Los parámetros de una red neuronal no suelen tener una interpretación humana simple de "esta perilla controla exactamente X".

En un modelo moderno, la información está **distribuida** entre muchas representaciones y transformaciones.

---

# 5. ¿Qué es un peso?

Un **peso** (`weight`) es un parámetro que determina cuánto influye una señal en una transformación matemática.

Una operación extremadamente simplificada podría ser:

```text
y = wx
```

Si:

```text
w = 2
x = 5
```

entonces:

```text
y = 10
```

Si:

```text
w = 0.1
```

entonces:

```text
y = 0.5
```

El peso modifica la contribución de `x`.

En redes neuronales reales existen enormes cantidades de estos pesos organizados principalmente en **matrices y tensores**.

---

# 6. ¿Qué es un sesgo?

Además de pesos, muchas capas utilizan un **sesgo** (`bias`).

Una transformación sencilla puede expresarse como:

```text
y = wx + b
```

donde:

* `w` = peso
* `x` = entrada
* `b` = sesgo

Por ejemplo:

```text
w = 2
x = 5
b = 3
```

Entonces:

```text
y = (2)(5) + 3
y = 13
```

El sesgo permite desplazar la transformación.

En una red neuronal, los pesos y sesgos son parámetros que pueden aprenderse durante el entrenamiento.

---

# 7. De una ecuación a una red neuronal

Una sola operación es muy sencilla:

```text
y = wx + b
```

Pero una red neuronal utiliza muchas transformaciones.

Conceptualmente:

```text
Entrada
   ↓
Transformación
   ↓
Activación
   ↓
Transformación
   ↓
Activación
   ↓
Transformación
   ↓
Salida
```

Cada transformación puede contener matrices con enormes cantidades de parámetros.

Por ejemplo:

```text
y = Wx + b
```

Ahora:

```text
W = matriz de pesos
x = vector de entrada
b = vector de sesgos
```

Una matriz:

```text
W =
[ 0.2   -0.7   1.1 ]
[ 0.5    0.3  -0.2 ]
[-0.8    0.4   0.9 ]
```

ya contiene múltiples parámetros.

Los modelos modernos simplemente llevan esta idea a escalas enormes.

---

# 8. Parámetros y tensores

En aprendizaje profundo, los parámetros no tienen por qué almacenarse como simples números aislados.

Normalmente están organizados como:

* vectores;
* matrices;
* tensores.

Por ejemplo:

```text
Vector:

[0.2
 0.8
-0.1]
```

Una matriz:

```text
[0.2  0.8 -0.1]
[0.4 -0.2  0.7]
```

Y un tensor puede tener más dimensiones.

Por eso, cuando hablamos de "parámetros", en realidad estamos hablando de los valores numéricos contenidos en estas estructuras.

---

# 9. ¿Cómo aprende el modelo los parámetros?

Aquí aparece una de las ideas fundamentales del aprendizaje automático.

Inicialmente, los parámetros no contienen el comportamiento final del modelo.

El entrenamiento comienza con valores iniciales y los modifica repetidamente.

El proceso simplificado es:

```text
1. Inicializar parámetros
          ↓
2. Introducir datos
          ↓
3. Generar una predicción
          ↓
4. Comparar predicción con objetivo
          ↓
5. Calcular error
          ↓
6. Calcular cómo modificar parámetros
          ↓
7. Actualizar parámetros
          ↓
8. Repetir millones/miles de millones de veces
```

El mecanismo exacto depende del tipo de modelo y del algoritmo de entrenamiento.

---

# 10. La función de pérdida

Para entrenar un modelo necesitamos cuantificar qué tan buena o mala fue una predicción.

Utilizamos una **función de pérdida** (`loss function`).

Podemos representarla como:

```text
L(θ)
```

La pérdida depende de los parámetros actuales.

Simplificando:

```text
Pérdida alta
     ↓
El modelo está funcionando peor
```

y:

```text
Pérdida baja
     ↓
El modelo está funcionando mejor
```

No significa necesariamente que una pérdida baja implique un modelo perfecto.

La función de pérdida mide el objetivo específico definido para el entrenamiento.

---

# 11. El gradiente

Ahora necesitamos responder una pregunta:

> ¿En qué dirección debemos modificar los parámetros para reducir la pérdida?

Aquí aparece el **gradiente**.

El gradiente:

```text
∇θ L
```

indica cómo cambia la función de pérdida respecto de los parámetros.

Una forma simplificada de pensarlo es:

```text
Gradiente
   ↓
dirección de cambio de la pérdida
```

El entrenamiento utiliza esa información para modificar los parámetros.

---

# 12. Descenso de gradiente

Una formulación clásica es:

```text
θ ← θ − η∇θL
```

donde:

* `θ` = parámetros
* `L` = función de pérdida
* `∇θL` = gradiente respecto de los parámetros
* `η` = tasa de aprendizaje (`learning rate`)
* `←` = actualizar

La idea intuitiva es:

```text
Parámetros actuales
        ↓
calcular gradiente
        ↓
determinar dirección
        ↓
dar un pequeño paso
        ↓
nuevos parámetros
```

Y repetir.

---

# 13. ¿Qué hace el learning rate?

El **learning rate** determina aproximadamente el tamaño del paso utilizado durante la actualización.

Si es demasiado grande:

```text
     ↓
el entrenamiento puede dar saltos excesivos
```

Si es demasiado pequeño:

```text
     ↓
el aprendizaje puede ser extremadamente lento
```

Una analogía:

Imagina que estás bajando una montaña buscando el punto más bajo.

```text
Pasos gigantes
→ puedes pasar de un lado a otro

Pasos muy pequeños
→ avanzas demasiado lentamente

Pasos adecuados
→ puedes acercarte progresivamente al mínimo
```

Esta analogía simplifica considerablemente la realidad de la optimización de redes profundas, pero sirve para comprender el principio.

---

# 14. Backpropagation

Para redes neuronales aparece otro concepto fundamental:

**backpropagation**, o propagación hacia atrás del error.

Su objetivo es calcular cómo contribuyen los diferentes parámetros al error mediante derivadas.

Simplificando:

```text
Entrada
   ↓
Forward pass
   ↓
Predicción
   ↓
Loss
   ↓
Backward pass
   ↓
Gradientes
   ↓
Actualización de parámetros
```

La propagación hacia adelante calcula la salida.

La propagación hacia atrás calcula información necesaria para actualizar los parámetros.

---

# 15. Forward pass

Durante un `forward pass`, los datos atraviesan el modelo.

Conceptualmente:

```text
x
↓
Capa 1
↓
Capa 2
↓
Capa 3
↓
...
↓
Predicción
```

Cada capa realiza operaciones utilizando sus parámetros.

Por ejemplo:

```text
h₁ = f(W₁x + b₁)

h₂ = f(W₂h₁ + b₂)

y = W₃h₂ + b₃
```

Aquí:

```text
W₁, W₂, W₃
b₁, b₂, b₃
```

son parámetros.

---

# 16. ¿Qué ocurre durante el backward pass?

Después de calcular la pérdida:

```text
L
```

el entrenamiento calcula derivadas respecto de los parámetros:

```text
∂L/∂W
∂L/∂b
```

Esto permite determinar cómo pequeños cambios en los parámetros afectan la pérdida.

Finalmente, un optimizador utiliza esa información para actualizar los parámetros.

---

# 17. ¿Todos los números del modelo son parámetros?

No.

Esta distinción es fundamental.

Un modelo contiene muchos tipos de valores.

Por ejemplo:

```text
Parámetros aprendibles
Estados temporales
Activaciones
Datos de entrada
Máscaras
Metadatos
Configuraciones
Constantes
```

No todo número que aparece durante una ejecución es un parámetro.

Los **parámetros** son principalmente los valores que forman parte del estado aprendido del modelo y que fueron optimizados durante su entrenamiento o adaptación.

---

# 18. Parámetros entrenables y no entrenables

Podemos distinguir entre:

### Parámetros entrenables

Son valores que se permiten modificar durante un proceso de entrenamiento o fine-tuning.

```text
requires_grad = true
```

conceptualmente.

### Parámetros congelados

Son parámetros que permanecen sin actualizarse durante determinado proceso.

Por ejemplo:

```text
Modelo base
↓
parámetros congelados
↓
adaptador entrenable
```

Esto es especialmente importante en técnicas modernas de adaptación eficiente.

---

# 19. ¿Qué significa que un modelo tenga 7 mil millones de parámetros?

Cuando se dice:

```text
Modelo de 7B
```

normalmente significa aproximadamente:

```text
7 × 10⁹ parámetros
```

es decir:

```text
7.000.000.000
```

No significa:

```text
7.000.000.000 hechos
```

ni:

```text
7.000.000.000 palabras
```

ni:

```text
7.000.000.000 respuestas
```

Es una medida de la cantidad de valores que forman parte de los parámetros del modelo.

---

# 20. ¿Más parámetros significa automáticamente mejor?

No.

Esta es una simplificación muy común:

```text
más parámetros = mejor IA
```

No es una regla universal.

El comportamiento depende de múltiples factores:

```text
Arquitectura
+
Datos
+
Calidad de datos
+
Objetivo de entrenamiento
+
Cantidad de entrenamiento
+
Optimización
+
Contexto
+
Datos de adaptación
+
Evaluación
+
Inferencia
```

Un modelo más pequeño y especializado puede superar a otro más grande en determinadas tareas.

Por eso el número de parámetros debe interpretarse dentro del contexto de la arquitectura y del entrenamiento.

---

# 21. Capacidad del modelo

Los parámetros están relacionados con la **capacidad de representación** del modelo.

En términos intuitivos:

```text
Más parámetros
        ↓
más grados de libertad
        ↓
potencialmente más capacidad para representar funciones complejas
```

Pero:

```text
más capacidad
≠
mejor aprendizaje
```

Si los datos son deficientes, el modelo puede aprender patrones deficientes.

Por eso:

```text
Modelo grande + malos datos
```

no garantiza buen rendimiento.

---

# 22. Parámetros y conocimiento

Decir:

> "El conocimiento está guardado en los parámetros"

puede ser una simplificación útil, pero técnicamente necesita matices.

Los parámetros codifican representaciones y regularidades aprendidas durante el entrenamiento.

No son una biblioteca explícita:

```text
Parámetro 1 → Ecuador
Parámetro 2 → Quito
Parámetro 3 → Python
```

La información suele estar distribuida entre muchas dimensiones y capas.

Por eso tampoco podemos interpretar normalmente un parámetro individual como:

```text
θ₁₂₃₄₅ = "conocimiento sobre historia"
```

La representación es mucho más compleja.

---

# 23. Representación distribuida

En redes neuronales modernas, una característica fundamental es la **representación distribuida**.

Una determinada característica puede depender de muchos parámetros.

Y un mismo parámetro puede contribuir a múltiples comportamientos.

Conceptualmente:

```text
Concepto A
 ↘
   Parámetros compartidos
 ↗
Concepto B
```

Por eso no suele ser correcto buscar una relación uno-a-uno:

```text
1 parámetro = 1 concepto
```

La realidad es mucho más distribuida.

---

# 24. Parámetros de un Transformer

En un Transformer moderno existen distintos grupos de parámetros.

De forma simplificada podemos encontrar parámetros asociados con:

```text
Embeddings
      ↓
Atención
      ↓
Proyecciones
      ↓
Redes feed-forward
      ↓
Normalización
      ↓
Salida
```

Por ejemplo, en un bloque Transformer podemos encontrar matrices asociadas a las proyecciones:

```text
WQ
WK
WV
WO
```

correspondientes conceptualmente a:

```text
Query
Key
Value
Output
```

Además existen matrices en las capas feed-forward y otros parámetros dependiendo de la arquitectura.

No necesitamos todavía estudiar cada ecuación para comprender ingeniería de prompts, pero sí debemos saber que el prompt atraviesa estas transformaciones.

---

# 25. Parámetros y embeddings

Los embeddings también pueden estar asociados con grandes matrices de parámetros.

Supongamos un vocabulario de:

```text
100.000 tokens
```

y una dimensión de embedding:

```text
4.096
```

Una matriz de embeddings tendría aproximadamente:

```text
100.000 × 4.096
```

valores.

Eso equivale a:

```text
409.600.000
```

parámetros si todos ellos son entrenables.

Esto demuestra por qué los embeddings pueden representar una parte significativa de los parámetros de un modelo.

---

# 26. Parámetros en atención

En un Transformer, la atención necesita realizar transformaciones sobre las representaciones.

Simplificando:

```text
Q = XWQ
K = XWK
V = XWV
```

donde:

* `X` = representación de entrada
* `WQ` = matriz de parámetros para Query
* `WK` = matriz de parámetros para Key
* `WV` = matriz de parámetros para Value

Luego se calcula la atención.

Una de las ecuaciones fundamentales es:

```text
Attention(Q,K,V)
=
softmax(QKᵀ / √dₖ)V
```

La ecuación anterior describe la operación de atención, mientras que las matrices `WQ`, `WK` y `WV` contienen parámetros aprendidos.

Esta distinción es importante:

> **La atención es una operación; los pesos que participan en las proyecciones son parámetros aprendidos.**

---

# 27. Parámetros ≠ activaciones

Otra distinción esencial:

### Parámetros

Son parte del modelo aprendido.

```text
W
b
```

### Activaciones

Son valores que aparecen durante la ejecución del modelo como resultado de procesar una entrada.

```text
x
↓
modelo
↓
activaciones
↓
salida
```

Por ejemplo:

```text
Parámetros:
permanecen relativamente estables durante la inferencia

Activaciones:
cambian dependiendo del prompt
```

Esto tiene una consecuencia fundamental para Prompt Engineering:

> Cuando cambias el prompt, normalmente no estás cambiando los parámetros del modelo; estás cambiando la entrada que produce diferentes activaciones y, por tanto, diferentes salidas.

---

# 28. Prompt vs parámetros

Esta diferencia debe quedar completamente clara.

Supongamos:

```text
Modelo = M
Parámetros = θ
Prompt A = P₁
Prompt B = P₂
```

En inferencia normal:

```text
M(θ, P₁) → respuesta A

M(θ, P₂) → respuesta B
```

Los parámetros:

```text
θ
```

son los mismos.

Lo que cambia es:

```text
P₁ → P₂
```

y eso cambia las activaciones y la trayectoria computacional dentro del modelo.

---

# 29. Una consecuencia fundamental para Prompt Engineering

Cuando escribimos:

```text
"Actúa como auditor financiero..."
```

no estamos convirtiendo permanentemente al modelo en auditor.

No estamos modificando:

```text
θ
```

El prompt simplemente proporciona una entrada que condiciona la inferencia.

Por eso:

```text
Prompt Engineering
```

y:

```text
Training / Fine-tuning
```

son cosas diferentes.

---

# 30. Prompt Engineering vs Fine-tuning

### Prompt Engineering

Modifica la entrada:

```text
Modelo fijo
+
Prompt diferente
↓
Comportamiento diferente
```

### Fine-tuning

Modifica algunos o muchos parámetros:

```text
Modelo base
+
datos de adaptación
↓
actualización de parámetros
↓
modelo adaptado
```

Una forma simplificada:

```text
Prompt Engineering
→ cambia el contexto de entrada

Fine-tuning
→ cambia el modelo aprendido
```

---

# 31. ¿El prompt modifica los parámetros?

En una inferencia normal:

**No.**

Si escribes:

```text
"Responde como un abogado."
```

los parámetros del modelo no se actualizan como consecuencia de esa conversación.

El modelo utiliza sus parámetros actuales para procesar la entrada.

Podemos representarlo:

```text
θ fijo
+
prompt
↓
inferencia
↓
respuesta
```

No:

```text
θ
+
prompt
↓
cambiar θ
```

salvo que exista un proceso explícito de entrenamiento o adaptación.

---

# 32. Fine-tuning

En un fine-tuning convencional:

```text
Modelo base
      ↓
Datos especializados
      ↓
Entrenamiento adicional
      ↓
Actualización de parámetros
      ↓
Modelo adaptado
```

Por ejemplo:

```text
Modelo general
       ↓
documentos jurídicos preparados
       ↓
fine-tuning
       ↓
modelo adaptado al dominio
```

El modelo puede cambiar su comportamiento porque sus parámetros fueron actualizados.

---

# 33. Parámetros congelados

Una estrategia común consiste en congelar el modelo base:

```text
Modelo base
████████████████
     ↓
congelado
```

y entrenar solamente componentes adicionales:

```text
Adaptador
██
 ↓
entrenable
```

Esto reduce el costo de adaptación.

---

# 34. LoRA

Una técnica especialmente importante es **LoRA (Low-Rank Adaptation)**.

La idea general es evitar actualizar directamente todos los pesos de una matriz.

En lugar de modificar directamente:

```text
W
```

se puede representar una adaptación como:

```text
W' = W + ΔW
```

y aproximar:

```text
ΔW = BA
```

donde `A` y `B` son matrices de rango reducido.

Conceptualmente:

```text
Modelo original
      ↓
pesos congelados
      +
pequeños adaptadores
      ↓
modelo especializado
```

Esto permite reducir considerablemente la cantidad de parámetros que deben entrenarse.

---

# 35. Parámetros totales vs parámetros activos

Esta distinción es especialmente importante en arquitecturas **Mixture of Experts (MoE)**.

Un modelo MoE puede tener:

```text
Muchísimos parámetros totales
```

pero activar solamente una parte para cada token.

Conceptualmente:

```text
Entrada
   ↓
Router
   ↓
┌────┬────┬────┬────┐
│ E1 │ E2 │ E3 │ E4 │
└────┴────┴────┴────┘
      ↑    ↑
   expertos activos
```

Por ejemplo:

```text
Parámetros totales: 100B
Parámetros activos por token: 20B
```

La interpretación exacta depende de la arquitectura.

Por eso decir simplemente:

> "Este modelo tiene 100B"

no siempre describe cuántos parámetros participan en cada token.

---

# 36. Dense vs MoE

### Modelo denso

En términos generales, la mayoría de sus parámetros relevantes participan en el procesamiento de cada entrada.

```text
Entrada
 ↓
gran parte del modelo
 ↓
Salida
```

### MoE

Un router selecciona determinados expertos.

```text
Entrada
 ↓
Router
 ↓
Expertos seleccionados
 ↓
Salida
```

Por tanto:

```text
Parámetros totales
```

y:

```text
Parámetros activos
```

pueden ser diferentes.

Esto será importante cuando estudiemos arquitectura y optimización.

---

# 37. ¿Por qué importa la cantidad de parámetros para ejecutar un modelo?

Porque los parámetros deben almacenarse en memoria.

Supongamos un modelo con:

```text
7.000.000.000 parámetros
```

Si cada parámetro utiliza aproximadamente:

```text
16 bits = 2 bytes
```

el almacenamiento bruto aproximado sería:

```text
7.000.000.000 × 2 bytes
≈ 14 GB
```

Esto es una aproximación.

En una ejecución real existen otros costos:

* activaciones;
* caché KV;
* buffers;
* memoria del runtime;
* estructuras adicionales;
* overhead;
* paralelización.

Por eso:

> El tamaño de los parámetros no es igual a la memoria total necesaria para ejecutar el modelo.

---

# 38. Precisión numérica

Los parámetros pueden almacenarse utilizando diferentes precisiones.

Por ejemplo:

```text
FP32
FP16
BF16
INT8
INT4
```

Una simplificación:

| Representación | Bits por valor |
| -------------- | -------------: |
| FP32           |             32 |
| FP16           |             16 |
| BF16           |             16 |
| INT8           |              8 |
| INT4           |              4 |

Menos bits pueden reducir el uso de memoria.

Pero también pueden existir compromisos relacionados con:

* precisión;
* estabilidad;
* calidad;
* hardware;
* velocidad;
* compatibilidad.

---

# 39. Cuantización

La **cuantización** transforma la representación numérica de los parámetros para utilizar menos bits.

Por ejemplo:

```text
FP16
 ↓
INT8
```

o:

```text
FP16
 ↓
INT4
```

La idea no es simplemente "eliminar números".

Se intenta representar los valores con menor precisión de manera que el modelo conserve suficiente calidad para el uso previsto.

Conceptualmente:

```text
Modelo original
     ↓
cuantización
     ↓
modelo más pequeño
     ↓
menos memoria
```

La cuantización es una técnica de despliegue/optimización, no una nueva forma de entrenamiento necesariamente.

---

# 40. Parámetros y VRAM

En sistemas locales, los parámetros tienen una relación directa con los requisitos de memoria.

Simplificando:

```text
Número de parámetros
×
bytes por parámetro
≈
memoria de pesos
```

Por ejemplo:

```text
7B × 2 bytes
≈ 14 GB
```

Pero la VRAM necesaria para ejecutar el modelo puede ser superior debido a otros componentes.

Además, si el modelo utiliza múltiples GPUs, los parámetros pueden distribuirse entre ellas.

---

# 41. Parámetros y arquitectura

El número de parámetros depende de la arquitectura.

Dos modelos pueden tener:

```text
10B parámetros
```

pero no necesariamente tener:

* la misma arquitectura;
* la misma cantidad de capas;
* la misma dimensión oculta;
* el mismo mecanismo de atención;
* el mismo tokenizer;
* los mismos datos;
* el mismo entrenamiento;
* el mismo rendimiento.

Por eso:

> **"Tiene más parámetros" no es una descripción completa de un modelo.**

---

# 42. Parámetros y número de capas

Un Transformer puede representarse simplificadamente como:

```text
Embedding
   ↓
Bloque 1
   ↓
Bloque 2
   ↓
Bloque 3
   ↓
...
   ↓
Bloque N
   ↓
Salida
```

Cada bloque contiene parámetros.

Por tanto, aumentar:

* número de capas;
* dimensión interna;
* tamaño de las matrices;
* vocabulario;

puede aumentar significativamente el número total de parámetros.

---

# 43. ¿Un parámetro tiene significado por sí solo?

Generalmente, no.

Esta es una pregunta avanzada de interpretabilidad.

Podríamos imaginar:

```text
θ₁₂₃₄ = "matemáticas"
```

pero normalmente la situación real no es tan sencilla.

El comportamiento surge de interacciones entre grandes cantidades de parámetros.

Por eso:

```text
parámetro individual
```

no equivale necesariamente a:

```text
concepto individual
```

La interpretación puede requerir estudiar grupos de parámetros, neuronas, representaciones, circuitos o comportamientos.

---

# 44. Interpretabilidad

La **interpretabilidad** intenta comprender cómo las estructuras internas del modelo contribuyen a sus comportamientos.

Existen diferentes niveles de análisis:

```text
Parámetros
   ↓
Neuronas / componentes
   ↓
Representaciones
   ↓
Capas
   ↓
Circuitos
   ↓
Comportamientos
```

En modelos modernos, esto sigue siendo un área activa de investigación.

No debemos asumir que conocemos completamente qué representa cada parámetro.

---

# 45. Parámetros y conocimiento distribuido

Supongamos que un modelo conoce relaciones entre:

```text
París
Francia
Europa
capital
```

No necesariamente existe una única posición de memoria:

```text
"París = capital de Francia"
```

El comportamiento puede emerger de múltiples representaciones distribuidas.

Esto ayuda a explicar por qué editar directamente parámetros para cambiar un conocimiento específico puede ser mucho más complejo de lo que parece.

---

# 46. Parámetros y alucinaciones

Un modelo puede producir información incorrecta aunque tenga miles de millones de parámetros.

¿Por qué?

Porque:

```text
muchos parámetros
≠
base de datos perfecta
```

El modelo está realizando inferencia basada en representaciones aprendidas y en la información disponible durante la generación.

Además intervienen:

* datos de entrenamiento;
* objetivo de entrenamiento;
* arquitectura;
* contexto;
* recuperación externa;
* instrucciones;
* método de decodificación;
* limitaciones del modelo.

Por eso aumentar el número de parámetros no elimina automáticamente las alucinaciones.

---

# 47. Parámetros y contexto

Ahora podemos diferenciar tres conceptos:

### Parámetros

```text
Conocimiento y capacidades aprendidas
```

### Contexto

```text
Información proporcionada durante la inferencia
```

### Prompt

```text
Una parte del contexto utilizada para dirigir la interacción
```

Una representación conceptual:

```text
          MODELO
        Parámetros θ
             │
             │
             ▼
Prompt ──► Contexto ──► Inferencia ──► Salida
```

El prompt no reemplaza los parámetros.

El contexto tampoco modifica normalmente los parámetros.

---

# 48. ¿Qué ocurre cuando le damos un documento?

Supongamos que tenemos:

```text
Modelo
+
PDF de 300 páginas
```

El PDF puede entrar en el contexto o mediante un sistema RAG.

Eso no significa necesariamente:

```text
PDF → modifica parámetros
```

En una interacción normal:

```text
PDF
 ↓
procesamiento
 ↓
contexto
 ↓
inferencia
 ↓
respuesta
```

El modelo puede utilizar esa información sin que sus pesos sean reentrenados.

---

# 49. In-Context Learning vs aprendizaje de parámetros

Cuando un modelo parece "aprender" algo durante una conversación, debemos tener cuidado con el término.

Por ejemplo:

```text
Usuario:
Mi empresa utiliza "cliente crítico" para clientes con deuda > $10.000.

Modelo:
Entendido.
```

Durante la conversación puede utilizar esa definición posteriormente.

Eso no significa necesariamente que haya actualizado sus parámetros.

Podemos tener:

```text
Aprendizaje en contexto
```

sin:

```text
actualización de parámetros
```

Esta distinción será fundamental cuando estudiemos contexto e inferencia.

---

# 50. Una comparación fundamental

| Concepto                 |                          ¿Modifica parámetros? | ¿Cuándo ocurre?          |
| ------------------------ | ---------------------------------------------: | ------------------------ |
| Prompt                   |                                             No | Inferencia               |
| Contexto                 |                                No, normalmente | Inferencia               |
| RAG                      |                                No, normalmente | Inferencia               |
| Fine-tuning              |                                             Sí | Entrenamiento/adaptación |
| LoRA                     |            Sí, pero principalmente adaptadores | Entrenamiento/adaptación |
| Entrenamiento desde cero |                                             Sí | Entrenamiento            |
| Cuantización             | Cambia la representación numérica de los pesos | Optimización/despliegue  |

Esta tabla será una referencia importante para los siguientes módulos.

---

# 51. Una visión completa

Podemos resumir el ciclo de aprendizaje:

```text
                    DATOS
                      │
                      ▼
                ENTRENAMIENTO
                      │
                      ▼
              Optimización de θ
                      │
                      ▼
               PARÁMETROS θ
                      │
                      ▼
                   MODELO
                      │
             ┌────────┴────────┐
             │                 │
          PROMPT             CONTEXTO
             │                 │
             └────────┬────────┘
                      ▼
                  INFERENCIA
                      │
                      ▼
                    SALIDA
```

Esto nos permite comprender dónde está cada componente.

---

# 52. Una pregunta clave: ¿el prompt puede cambiar el comportamiento del modelo sin cambiar sus parámetros?

Sí.

Esta es precisamente una de las razones por las que existe el **Prompt Engineering**.

Tenemos:

```text
θ = constante
```

pero:

```text
x₁ ≠ x₂
```

Entonces:

```text
fθ(x₁) ≠ fθ(x₂)
```

El modelo puede producir comportamientos muy diferentes ante entradas diferentes aunque sus parámetros sean exactamente los mismos.

Por eso una buena ingeniería de prompts consiste, entre otras cosas, en diseñar adecuadamente la entrada que recibe el modelo.

---

# 53. ¿Entonces los parámetros determinan completamente la respuesta?

No necesariamente.

La salida depende de múltiples factores.

Una representación conceptual sería:

```text
Salida =
f(
    parámetros,
    prompt,
    contexto,
    herramientas,
    estado,
    configuración de inferencia,
    datos recuperados
)
```

En un LLM moderno, además pueden intervenir mecanismos específicos de generación y postprocesamiento.

Por eso:

```text
mismo modelo
```

no garantiza:

```text
misma respuesta
```

si cambia el contexto o la configuración de inferencia.

---

# 54. Parámetros y temperatura

Supongamos:

```text
Modelo = mismo
Parámetros = mismos
Prompt = mismo
```

Podemos modificar la estrategia de generación.

Por ejemplo:

```text
temperature = 0
```

frente a:

```text
temperature = 1
```

El modelo sigue teniendo los mismos parámetros.

Lo que cambia es la forma en que se seleccionan los tokens durante la generación.

Esto será estudiado con mayor profundidad en el módulo de **inferencia**.

---

# 55. Parámetros y entrenamiento continuo

Un sistema puede recibir nuevos datos y ser actualizado mediante diferentes mecanismos.

No debemos asumir que:

```text
Nueva conversación
```

significa automáticamente:

```text
actualización de parámetros
```

Existen procesos distintos:

```text
Conversación
↓
contexto temporal

Fine-tuning
↓
actualización de parámetros

Pretraining
↓
entrenamiento a gran escala

RAG
↓
recuperación externa
```

Cada mecanismo resuelve problemas diferentes.

---

# 56. ¿Qué significa "modelo de 70B"?

La expresión:

```text
70B
```

normalmente significa aproximadamente:

```text
70.000 millones de parámetros
```

Pero para comparar modelos correctamente debemos preguntar:

* ¿Es un modelo denso o MoE?
* ¿Cuántos parámetros son activos por token?
* ¿Qué arquitectura utiliza?
* ¿Qué tokenizer utiliza?
* ¿Con qué datos fue entrenado?
* ¿Qué objetivo de entrenamiento utilizó?
* ¿Qué contexto soporta?
* ¿Qué precisión estamos utilizando?
* ¿Está cuantizado?
* ¿Qué benchmark estamos evaluando?

El número `70B` por sí solo no responde estas preguntas.

---

# 57. Parámetros y capacidad computacional

Más parámetros generalmente implican más operaciones o mayores requisitos de memoria, aunque la relación exacta depende de la arquitectura.

Conceptualmente:

```text
Más parámetros
      ↓
más información que almacenar
      ↓
potencialmente más operaciones
      ↓
mayor costo de inferencia
```

Pero arquitecturas como MoE pueden cambiar significativamente esta relación porque no necesariamente activan todos los parámetros para cada token.

---

# 58. Parámetros y escalabilidad

En modelos grandes aparecen tres recursos fundamentales:

```text
Datos
Computación
Parámetros
```

El rendimiento no depende exclusivamente de uno.

Una forma conceptual:

```text
Datos adecuados
      +
Arquitectura adecuada
      +
Cantidad adecuada de parámetros
      +
Computación suficiente
      +
Optimización adecuada
      ↓
Modelo útil
```

La investigación moderna sobre escalamiento estudia precisamente cómo interactúan estos factores.

---

# 59. Nivel avanzado: espacio de parámetros

Desde una perspectiva matemática, podemos considerar todos los parámetros como un único vector:

```text
θ ∈ ℝⁿ
```

donde:

```text
n = número total de parámetros
```

Si un modelo tiene:

```text
n = 7.000.000.000
```

entonces `θ` puede imaginarse como un punto en un espacio de aproximadamente siete mil millones de dimensiones.

El entrenamiento intenta encontrar regiones de ese espacio donde el modelo tenga un comportamiento útil respecto del objetivo definido.

---

# 60. La función objetivo

Podemos representar el entrenamiento como:

```text
θ* = argminθ L(θ)
```

Esto significa:

> Encontrar los parámetros `θ*` que minimizan la función de pérdida `L`, según el proceso de optimización utilizado.

En la práctica, los modelos modernos utilizan optimización numérica a gran escala y el proceso no es simplemente una búsqueda directa del mínimo global.

---

# 61. Mínimo global y mínimos locales

La superficie de pérdida de una red neuronal puede ser extremadamente compleja.

Conceptualmente:

```text
Loss
 ^
 |       /\       /\
 |      /  \_____/  \
 |_____/             \____
 +--------------------------> parámetros
```

El entrenamiento busca regiones de baja pérdida.

En modelos modernos de alta dimensión, la geometría real es mucho más compleja que esta representación bidimensional.

Por eso las explicaciones de "bajar una montaña" son solamente una analogía.

---

# 62. Optimización moderna

El descenso de gradiente puro es solo una idea básica.

En sistemas reales se utilizan optimizadores y técnicas de entrenamiento más sofisticadas, como variantes de:

* SGD;
* Adam;
* AdamW;
* learning-rate schedules;
* weight decay;
* gradient clipping;
* mixed precision;
* distributed training.

No todos los modelos utilizan exactamente la misma configuración.

El objetivo general sigue siendo:

```text
modificar θ
para optimizar una función objetivo
```

---

# 63. Parámetros y generalización

Un modelo no debe simplemente memorizar los datos de entrenamiento.

Debe aprender patrones que puedan generalizar a datos que no ha visto.

Idealmente:

```text
Entrenamiento
      ↓
patrones útiles
      ↓
Generalización
      ↓
datos nuevos
```

Una cantidad enorme de parámetros puede proporcionar mucha capacidad, pero la generalización depende también de:

* datos;
* regularización;
* arquitectura;
* optimización;
* distribución de datos;
* escala del entrenamiento.

---

# 64. Parámetros y sobreajuste

Un modelo puede aprender demasiado específicamente los datos de entrenamiento.

Esto se conoce como **overfitting** o sobreajuste.

Conceptualmente:

```text
Aprende ejemplos concretos
        ↓
pero no generaliza correctamente
```

Por eso:

```text
más parámetros
```

no implica automáticamente:

```text
más generalización
```

El comportamiento final depende del sistema completo.

---

# 65. Parámetros y seguridad

Los parámetros también tienen importancia en seguridad de IA.

Existen investigaciones sobre:

* model editing;
* weight manipulation;
* backdoors;
* poisoned training;
* parameter-efficient fine-tuning;
* model extraction;
* weight theft;
* malicious fine-tuning.

Un modelo no es únicamente un archivo de "números".

Los pesos pueden representar un activo tecnológico importante.

Por eso la seguridad de los modelos incluye proteger:

```text
Datos
+
Código
+
Pesos
+
Configuraciones
+
Infraestructura
+
Interfaces
```

---

# 66. ¿Se pueden modificar directamente los parámetros?

Sí, técnicamente.

Pero modificar parámetros directamente no significa necesariamente saber qué comportamiento se producirá.

Existen técnicas de:

* model editing;
* fine-tuning;
* continued pretraining;
* LoRA;
* adapters;
* pruning;
* quantization.

Cada una modifica o transforma el estado del modelo de una manera diferente.

---

# 67. Parámetros y poda

La **poda** (`pruning`) intenta eliminar o reducir determinadas partes del modelo consideradas poco necesarias bajo un criterio específico.

Conceptualmente:

```text
Modelo grande
████████████████████
        ↓
      poda
        ↓
Modelo reducido
██████████████
```

Dependiendo de la técnica, pueden eliminarse pesos, estructuras o componentes.

La finalidad puede ser:

* reducir memoria;
* acelerar inferencia;
* disminuir costos.

Pero puede existir un compromiso con el rendimiento.

---

# 68. Parámetros y sparsity

La **sparsity** o dispersidad significa que una gran cantidad de valores pueden ser cero o tratados como inactivos bajo una estructura determinada.

Por ejemplo:

```text
[0, 0, 0, 4, 0, 0, 7, 0]
```

puede representarse de manera más eficiente que una estructura completamente densa si el hardware y el algoritmo aprovechan esa dispersidad.

Esto conecta los parámetros con técnicas de optimización de modelos.

---

# 69. Parámetros totales no equivalen a calidad

Cuando comparemos modelos en el futuro debemos evitar razonamientos como:

```text
Modelo A = 70B
Modelo B = 30B

70B > 30B
por tanto
A es mejor
```

La conclusión no es válida.

Una comparación seria debe considerar el escenario concreto:

```text
Tarea
+
datos
+
arquitectura
+
entrenamiento
+
contexto
+
inferencia
+
evaluación
```

El número de parámetros es solamente una característica.

---

# 70. El mapa mental correcto

Debemos abandonar esta idea:

```text
PARÁMETROS
=
BASE DE DATOS
```

y utilizar esta:

```text
                 PARÁMETROS
                     θ
                     │
                     ▼
             FUNCIÓN APRENDIDA
                     │
                     ▼
              REPRESENTACIONES
                     │
                     ▼
                 INFERENCIA
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
       CONTEXTO                PROMPT
          │                     │
          └──────────┬──────────┘
                     ▼
                   SALIDA
```

---

# 71. La conexión con Prompt Engineering

Ahora podemos entender algo fundamental.

Un prompt no es simplemente:

> "una pregunta que le hacemos a ChatGPT".

Desde una perspectiva de ingeniería:

```text
Prompt
   ↓
Tokenización
   ↓
Representaciones
   ↓
Procesamiento por la arquitectura
   ↓
Interacción con parámetros aprendidos
   ↓
Activaciones
   ↓
Distribución de probabilidad
   ↓
Decodificación
   ↓
Tokens generados
```

Por eso estudiar Prompt Engineering sin entender mínimamente:

* parámetros;
* tokens;
* contexto;
* inferencia;
* arquitectura;

deja una parte importante del sistema sin explicar.

---

# 72. Ejemplo completo

Supongamos que tenemos un modelo con parámetros:

```text
θ
```

El usuario escribe:

```text
Analiza esta factura y encuentra posibles errores contables.
```

El sistema realiza conceptualmente:

```text
Prompt
  ↓
Tokenización
  ↓
Embeddings
  ↓
Transformer
  ↓
Atención + transformaciones
  ↓
Parámetros θ
  ↓
Activaciones
  ↓
Distribución de probabilidad
  ↓
Selección de token
  ↓
Siguiente token
  ↓
Repetición
  ↓
Respuesta
```

El prompt no reemplazó los parámetros.

El prompt **activó el modelo de una determinada manera**.

---

# 73. Un ejemplo todavía más importante

Tenemos el mismo modelo:

```text
θ = constante
```

Prompt A:

```text
Explica qué es una auditoría.
```

Prompt B:

```text
Actúa como auditor financiero. Analiza las siguientes transacciones,
identifica anomalías y presenta los hallazgos en JSON.
```

El modelo sigue teniendo:

```text
θ
```

sin cambios.

Pero la información de entrada es diferente.

Por tanto, las activaciones y la salida pueden cambiar radicalmente.

Esto explica por qué una ingeniería de prompts adecuada puede modificar significativamente el comportamiento observado sin entrenar nuevamente el modelo.

---

# 74. Error conceptual frecuente

### Incorrecto

> "Le enseñé al modelo una nueva regla porque se la escribí en el prompt."

### Más preciso

> "Le proporcioné una instrucción dentro del contexto de inferencia y el modelo utilizó esa información para generar la respuesta."

La primera frase puede ser una simplificación conversacional.

La segunda describe mejor lo que ocurre técnicamente.

---

# 75. Otro error frecuente

### Incorrecto

> "Los 70B parámetros son 70B conocimientos."

### Correcto

> "70B indica aproximadamente la cantidad de parámetros del modelo; esos parámetros forman parte de las representaciones y transformaciones aprendidas."

---

# 76. Otro error frecuente

### Incorrecto

> "Un modelo de 100B necesariamente es mejor que uno de 20B."

### Correcto

> "El número de parámetros es una característica importante del modelo, pero su rendimiento depende también de la arquitectura, los datos, el entrenamiento, la adaptación y el escenario de evaluación."

---

# 77. Otro error frecuente

### Incorrecto

> "RAG modifica el modelo."

### Correcto

> "RAG normalmente proporciona información externa durante la inferencia; no necesita modificar los parámetros del modelo."

---

# 78. Otro error frecuente

### Incorrecto

> "Una conversación larga entrena el modelo."

### Correcto

> "Una conversación puede proporcionar contexto para la inferencia. Eso no implica por sí mismo una actualización de los parámetros."

---

# 79. Parámetros: visión de ingeniería

Desde la perspectiva de un ingeniero de IA, los parámetros importan porque afectan:

```text
Capacidad
Memoria
Costo
Latencia
Hardware requerido
Fine-tuning
Cuantización
Despliegue
Escalabilidad
```

Pero no debemos analizar los parámetros de forma aislada.

La pregunta correcta no es únicamente:

> "¿Cuántos parámetros tiene?"

También debemos preguntar:

> "¿Qué arquitectura utiliza, cómo se entrenó, qué parámetros se activan, cómo se almacenan y qué rendimiento obtiene en la tarea que me interesa?"

---

# 80. Resumen conceptual

Podemos condensar todo el módulo en diez ideas:

### 1.

Un parámetro es un valor numérico aprendido que forma parte del modelo.

### 2.

Los pesos y sesgos son ejemplos fundamentales de parámetros.

### 3.

Los parámetros se organizan normalmente en vectores, matrices y tensores.

### 4.

Durante el entrenamiento, los parámetros se actualizan para optimizar una función objetivo.

### 5.

El gradiente proporciona información sobre cómo modificar los parámetros.

### 6.

Los parámetros no son una base de datos explícita de respuestas.

### 7.

El conocimiento aprendido suele estar distribuido entre muchas representaciones y parámetros.

### 8.

Un prompt normalmente no modifica los parámetros durante la inferencia.

### 9.

Fine-tuning y técnicas como LoRA sí pueden modificar o añadir parámetros entrenables.

### 10.

La cantidad de parámetros no determina por sí sola la calidad de un modelo.

---

# 81. Nivel maestría/PhD: formulación mínima

Podemos representar un modelo parametrizado como:

```text
fθ : X → Y
```

donde:

```text
θ ∈ ℝⁿ
```

es el conjunto de parámetros.

Durante entrenamiento buscamos:

```text
θ* = argminθ E_(x,y)~D [L(fθ(x), y)]
```

donde:

* `D` = distribución de datos;
* `x` = entrada;
* `y` = objetivo;
* `fθ(x)` = predicción;
* `L` = función de pérdida;
* `θ*` = parámetros obtenidos mediante el proceso de optimización.

En entrenamiento basado en gradiente:

```text
θₜ₊₁ = θₜ − ηₜ ∇θ L(θₜ)
```

o mediante un optimizador más sofisticado que modifique esta regla.

La idea fundamental permanece:

```text
DATOS
  ↓
OBJETIVO
  ↓
GRADIENTES
  ↓
OPTIMIZACIÓN
  ↓
PARÁMETROS
  ↓
MODELO
```

---

# 82. Lo que debemos llevarnos a Prompt Engineering

La cadena mental correcta es:

```text
DATOS
  ↓
ENTRENAMIENTO
  ↓
PARÁMETROS
  ↓
MODELO
  ↓
PROMPT
  ↓
CONTEXTO
  ↓
INFERENCIA
  ↓
ACTIVACIONES
  ↓
PROBABILIDADES
  ↓
GENERACIÓN
  ↓
RESPUESTA
```

Y existe una diferencia fundamental:

```text
                 ENTRENAMIENTO
                      │
                      ▼
               modifica θ
                      │
                      ▼
                   MODELO
                      │
                      │
                 INFERENCIA
                      ▲
                      │
              Prompt + Contexto
```

El **Prompt Engineering** opera principalmente en la parte de entrada e inferencia.

El **entrenamiento y fine-tuning** operan sobre el estado aprendido del modelo.

Comprender esta diferencia es indispensable para dejar de tratar a los LLM como una "caja mágica" y comenzar a analizarlos como sistemas computacionales.

---

# 83. Próximo concepto

Ahora que sabemos:

```text
¿Qué es un modelo?
        ↓
¿Cómo aprende?
        ↓
¿Qué datos utiliza?
        ↓
¿Qué son sus parámetros?
```

podemos estudiar el elemento que conecta directamente el lenguaje humano con el modelo:

```text
                TEXTO
                  ↓
             TOKENIZACIÓN
                  ↓
                TOKENS
                  ↓
              EMBEDDINGS
                  ↓
             REPRESENTACIÓN
                  ↓
              TRANSFORMER
```

El siguiente concepto fundamental es:

**07 — Tokens y tokenización.**

Allí veremos por qué un modelo no recibe directamente "palabras", cómo convierte el texto en números, por qué una palabra puede convertirse en varios tokens y por qué esto afecta directamente al contexto, costo, rendimiento y diseño de prompts.
