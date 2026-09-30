# 12. Temperatura y Sampling

> **Objetivo:** comprender cómo un modelo de lenguaje transforma una distribución de probabilidades en una elección concreta de tokens y cómo técnicas como **temperatura, Top-K y Top-P** modifican ese proceso.

---

## 1. Introducción

En los módulos anteriores vimos que un modelo de lenguaje no genera directamente una respuesta completa.

El proceso puede simplificarse así:

```text
PROMPT
   ↓
CONTEXTO
   ↓
MODELO
   ↓
LOGITS
   ↓
PROBABILIDADES
   ↓
SELECCIÓN DE UN TOKEN
   ↓
NUEVO CONTEXTO
   ↓
SIGUIENTE TOKEN
   ↓
...
   ↓
RESPUESTA
```

En cada paso, el modelo calcula una distribución de probabilidad sobre los posibles tokens.

Por ejemplo, ante:

> "El cielo es"

podríamos imaginar una distribución simplificada:

| Token  | Probabilidad |
| ------ | -----------: |
| azul   |         0.72 |
| gris   |         0.10 |
| claro  |         0.07 |
| oscuro |         0.04 |
| verde  |         0.01 |
| otros  |         0.06 |

El modelo todavía tiene que decidir:

> **¿Qué token selecciono?**

Aquí aparece el concepto de **sampling** o **muestreo**.

Y antes del sampling pueden aplicarse mecanismos que modifican la distribución, entre ellos la **temperatura**.

---

# 2. ¿Qué es sampling?

**Sampling** significa seleccionar una muestra de una distribución de probabilidad.

En un modelo de lenguaje, significa:

> Seleccionar el siguiente token utilizando la distribución de probabilidades producida por el modelo.

No necesariamente se selecciona siempre el token con mayor probabilidad.

---

## 3. Un ejemplo sencillo

Supongamos:

```text
"Mi comida favorita es"
```

El modelo calcula:

```text
pizza       0.50
pasta       0.30
arroz       0.10
ensalada    0.05
sopa        0.05
```

Una estrategia podría elegir siempre:

```text
pizza
```

porque tiene la mayor probabilidad.

Pero un sistema de sampling podría seleccionar:

```text
pizza
```

la mayoría de las veces,

y ocasionalmente:

```text
pasta
```

o:

```text
arroz
```

dependiendo de la distribución.

---

# 4. ¿Por qué no elegir siempre el token más probable?

Porque hacerlo siempre puede producir respuestas:

* demasiado deterministas;
* repetitivas;
* conservadoras;
* poco variadas;
* menos útiles en tareas creativas.

Supongamos que queremos generar nombres para una cafetería.

El modelo podría producir:

```text
Café Central
Café Central
Café Central
Café Central
```

si siempre escogemos el token más probable en cada paso.

Con sampling, diferentes tokens plausibles pueden tener oportunidad de ser seleccionados.

Por ejemplo:

```text
Café Luna
Café Andino
Aroma Central
Grano Norte
Casa del Café
```

Esto introduce **variabilidad controlada**.

---

# 5. Sampling no significa generar aleatoriamente

Este punto es fundamental.

Sampling **no significa elegir cualquier token al azar**.

El modelo sigue respetando la distribución de probabilidad.

Si tenemos:

```text
azul       0.80
verde      0.10
rojo       0.05
elefante   0.0001
```

el token `azul` tiene muchas más posibilidades de ser seleccionado que `elefante`.

Por tanto:

```text
sampling ≠ azar uniforme
```

Es más correcto pensar:

```text
sampling = aleatoriedad condicionada por una distribución
```

---

# 6. Greedy decoding

Una de las estrategias más simples es **greedy decoding**.

Consiste en seleccionar siempre:

```text
argmax(probabilidad)
```

Es decir:

> elegir el token con mayor probabilidad.

Ejemplo:

```text
A → 0.10
B → 0.65
C → 0.20
D → 0.05
```

Greedy selecciona:

```text
B
```

Si después la nueva distribución es:

```text
A → 0.70
B → 0.15
C → 0.10
D → 0.05
```

selecciona:

```text
A
```

Y continúa así.

---

# 7. Ventaja y limitación de Greedy

### Ventaja

Es sencillo y reproducible.

### Limitación

Una decisión localmente probable no necesariamente produce la mejor secuencia completa.

Supongamos:

```text
Primer token:

A = 0.55
B = 0.45
```

Si elegimos `A`, podríamos terminar en una secuencia:

```text
A → 0.10 → 0.10 → 0.05...
```

Mientras que:

```text
B → 0.80 → 0.80 → 0.90...
```

podría producir una secuencia globalmente más probable.

Por eso, la generación de lenguaje no puede entenderse simplemente como:

> "escoger siempre el token más probable".

---

# 8. Temperatura

La **temperatura** es un parámetro utilizado para modificar la distribución de probabilidad antes del sampling.

Su objetivo conceptual es controlar qué tan concentrada o dispersa está la distribución.

Podemos pensar en ella como un mecanismo que controla la **sensibilidad de la distribución a las diferencias entre logits**.

---

# 9. Antes de la temperatura: logits

Recordemos que el modelo normalmente produce **logits**.

Ejemplo:

```text
Token A → 5.0
Token B → 3.0
Token C → 1.0
Token D → 0.0
```

Los logits todavía **no son probabilidades**.

Para obtener probabilidades se utiliza normalmente una función Softmax.

---

# 10. Softmax

La forma estándar es:

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

donde:

* \(z_i\) = logit del token \(i\);
* \(e\) = número de Euler;
* \(P_i\) = probabilidad resultante.

La suma de todas las probabilidades es:

$$
\sum_i P_i = 1
$$

---

# 11. Softmax con temperatura

La temperatura \(T\) puede incorporarse así:

$$
P_i =
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

donde:

$$
T > 0
$$

La temperatura modifica la distribución.

---

# 12. Temperatura baja

Cuando:

$$
T < 1
$$

las diferencias entre logits se vuelven más importantes.

La distribución tiende a concentrarse.

Ejemplo conceptual:

```text
Antes:

A → 0.50
B → 0.30
C → 0.15
D → 0.05
```

Con temperatura más baja:

```text
A → 0.75
B → 0.18
C → 0.06
D → 0.01
```

La distribución se vuelve más concentrada.

---

# 13. Temperatura alta

Cuando:

$$
T > 1
$$

las diferencias entre logits se suavizan.

Ejemplo conceptual:

```text
Antes:

A → 0.50
B → 0.30
C → 0.15
D → 0.05
```

Con temperatura más alta:

```text
A → 0.36
B → 0.30
C → 0.22
D → 0.12
```

La distribución se vuelve más dispersa.

---

# 14. Temperatura igual a 1

Cuando:

$$
T = 1
$$

obtenemos el comportamiento estándar de Softmax:

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

La temperatura no modifica los logits.

---

# 15. Una forma intuitiva de entender la temperatura

Podemos utilizar esta analogía:

### Temperatura baja

```text
"Quédate cerca de la opción más probable."
```

### Temperatura alta

```text
"Considera también alternativas menos probables."
```

Pero esta analogía tiene una precisión importante:

> La temperatura no le dice al modelo qué contenido debe producir.

Modifica la distribución utilizada para seleccionar tokens.

---

# 16. Temperatura no cambia los parámetros del modelo

Este es un concepto fundamental para ingeniería de IA.

Si tenemos:

```text
Modelo
θ = parámetros entrenados
```

y cambiamos:

```text
temperature = 0.2
```

no estamos entrenando nuevamente el modelo.

Tampoco estamos modificando sus pesos.

El flujo es más parecido a:

```text
PARÁMETROS
     ↓
MODELO
     ↓
LOGITS
     ↓
TEMPERATURA
     ↓
PROBABILIDADES
     ↓
SAMPLING
```

Por tanto:

> La temperatura es una configuración del proceso de generación, no un cambio en los parámetros aprendidos.

---

# 17. Temperatura no significa "más inteligencia"

Una confusión frecuente es:

```text
temperatura alta = modelo más inteligente
temperatura baja = modelo menos inteligente
```

Esto es incorrecto.

La temperatura modifica principalmente la **distribución de selección**.

No aumenta:

* el número de parámetros;
* el conocimiento aprendido;
* la capacidad de razonamiento intrínseca;
* la ventana de contexto;
* la calidad del entrenamiento.

---

# 18. Temperatura y creatividad

En tareas creativas, una temperatura mayor puede producir más diversidad.

Por ejemplo:

```text
Escribe una historia sobre un robot.
```

Con una configuración más conservadora podrían aparecer respuestas más previsibles.

Con una configuración más permisiva pueden aparecer combinaciones menos frecuentes.

Pero:

> Más diversidad no significa automáticamente más calidad.

También puede aumentar la probabilidad de:

* desviaciones;
* incoherencias;
* errores;
* afirmaciones poco probables.

---

# 19. Temperatura y tareas deterministas

En tareas como:

* extracción estructurada;
* clasificación;
* transformación de datos;
* generación de JSON;
* consultas empresariales;
* procesamiento contable;
* análisis regulatorio;

normalmente interesa reducir la variabilidad.

Por ejemplo:

```text
Entrada
   ↓
LLM
   ↓
JSON
```

Si el sistema debe producir una estructura estricta, una generación altamente variable puede dificultar la consistencia.

Pero no debe asumirse que:

> temperatura = 0 garantiza respuestas idénticas o deterministas.

El comportamiento exacto depende del modelo, proveedor, infraestructura y parámetros adicionales.

---

# 20. Temperatura cero

Conceptualmente:

```text
T → 0
```

hace que la distribución se concentre fuertemente alrededor del logit máximo.

En el límite ideal:

$$
P(\text{token máximo}) \rightarrow 1
$$

y:

$$
P(\text{otros tokens}) \rightarrow 0
$$

Pero una implementación real puede tener particularidades.

Por ello es mejor interpretar:

```text
temperature muy baja
```

como:

> generación muy concentrada y poco variable,

en lugar de asumir automáticamente:

> determinismo matemático absoluto.

---

# 21. El problema de la temperatura por sí sola

Supongamos:

```text
A → 0.90
B → 0.04
C → 0.03
D → 0.02
E → 0.01
```

Incluso con sampling, seleccionar `E` es extremadamente improbable.

Ahora imaginemos:

```text
A → 0.30
B → 0.25
C → 0.20
D → 0.15
E → 0.10
```

Aquí existen muchas alternativas plausibles.

La temperatura puede modificar la distribución, pero existen otras técnicas para controlar qué candidatos participan en el sampling.

Ahí aparecen:

* Top-K
* Top-P

---

# 22. Top-K Sampling

**Top-K** limita el sampling a los \(K\) tokens con mayor probabilidad.

Supongamos:

```text
A → 0.40
B → 0.25
C → 0.15
D → 0.10
E → 0.05
F → 0.03
G → 0.02
```

Si:

```text
K = 3
```

solamente participan:

```text
A
B
C
```

Los demás quedan fuera del conjunto candidato para ese paso.

---

# 23. ¿Por qué utilizar Top-K?

Porque un vocabulario moderno puede contener una cantidad enorme de tokens.

No siempre tiene sentido permitir que todos sean candidatos efectivos para cada decisión.

Top-K establece:

> "Solo considera los K candidatos más probables."

---

# 24. Problema de Top-K

Un valor fijo de \(K\) no siempre se adapta bien a la distribución.

Ejemplo 1:

```text
A → 0.80
B → 0.10
C → 0.05
D → 0.03
E → 0.02
```

Ejemplo 2:

```text
A → 0.21
B → 0.20
C → 0.19
D → 0.18
E → 0.12
F → 0.10
```

El mismo:

```text
K = 3
```

produce un comportamiento diferente en cada caso.

Esto motivó técnicas como Top-P.

---

# 25. Top-P Sampling

Top-P también se conoce como **nucleus sampling**.

En lugar de elegir un número fijo de tokens, selecciona el conjunto mínimo de tokens cuya probabilidad acumulada alcanza un determinado umbral \(P\).

Ejemplo:

```text
A → 0.50
B → 0.25
C → 0.15
D → 0.06
E → 0.03
F → 0.01
```

Supongamos:

```text
P = 0.90
```

Acumulamos:

```text
A = 0.50
A+B = 0.75
A+B+C = 0.90
```

Por tanto, el conjunto candidato sería:

```text
A
B
C
```

---

# 26. Diferencia entre Top-K y Top-P

| Técnica           | Criterio                             |
| ----------------- | ------------------------------------ |
| Top-K             | cantidad fija de tokens              |
| Top-P             | masa de probabilidad acumulada       |
| Greedy            | solo el máximo                       |
| Sampling completo | toda la distribución disponible      |
| Temperatura       | modifica la forma de la distribución |

Ejemplo:

```text
Top-K = 10
```

significa:

> aproximadamente 10 candidatos.

Mientras:

```text
Top-P = 0.90
```

significa:

> candidatos suficientes para acumular aproximadamente el 90 % de la masa de probabilidad.

---

# 27. Temperatura + Top-K + Top-P

Estos mecanismos pueden combinarse conceptualmente.

Un flujo simplificado puede ser:

```text
LOGITS
   ↓
TEMPERATURA
   ↓
PROBABILIDADES
   ↓
TOP-K / TOP-P
   ↓
DISTRIBUCIÓN RESTRINGIDA
   ↓
SAMPLING
   ↓
TOKEN
```

Sin embargo, el orden exacto y los detalles de implementación pueden variar entre modelos y proveedores.

---

# 28. Un ejemplo completo

Supongamos que el modelo produce:

```text
Token A → logit 8
Token B → logit 7
Token C → logit 6
Token D → logit 2
Token E → logit 1
```

Con:

```text
T = 1
```

se calcula:

$$
P_i = softmax(z_i)
$$

Después podríamos aplicar:

```text
Top-K = 3
```

y quedarnos con:

```text
A
B
C
```

Después se realiza sampling sobre ese conjunto.

Finalmente se selecciona, por ejemplo:

```text
B
```

Ese token se incorpora al contexto.

---

# 29. El proceso se repite

La generación no termina después de seleccionar un token.

Supongamos:

```text
Prompt:
"El gato"
```

El modelo selecciona:

```text
" duerme"
```

Ahora el contexto es:

```text
"El gato duerme"
```

Se vuelve a ejecutar el proceso:

```text
"El gato duerme"
       ↓
logits
       ↓
temperatura
       ↓
probabilidades
       ↓
sampling
       ↓
" tranquilamente"
```

Después:

```text
"El gato duerme tranquilamente"
```

Y continúa.

---

# 30. Por eso una pequeña variación puede propagarse

Este fenómeno es importante.

Supongamos que dos generaciones comienzan:

```text
Generación A:
El gato duerme...

Generación B:
El gato descansa...
```

Ahora los contextos son diferentes.

Por tanto, el siguiente cálculo también será diferente.

```text
CONTEXTO A
   ↓
DISTRIBUCIÓN A
   ↓
TOKEN A
```

mientras:

```text
CONTEXTO B
   ↓
DISTRIBUCIÓN B
   ↓
TOKEN B
```

Una pequeña diferencia inicial puede producir secuencias cada vez más diferentes.

---

# 31. Sampling y reproducibilidad

La generación puede depender de una fuente de aleatoriedad.

Por ello, dos generaciones con:

```text
mismo prompt
mismo modelo
misma configuración
```

pueden producir resultados diferentes cuando se utiliza sampling estocástico.

Conceptualmente:

```text
P(mismo resultado) < 1
```

Esto es diferente de un procedimiento determinista.

---

# 32. Seed

Algunos sistemas permiten utilizar una **seed** o semilla.

Una semilla permite inicializar el generador pseudoaleatorio de una forma reproducible.

Conceptualmente:

```text
Prompt
+
Modelo
+
Configuración
+
Seed
↓
Generación reproducible
```

Pero:

> una seed no garantiza necesariamente reproducibilidad absoluta entre versiones de modelos, proveedores o infraestructuras.

Cambios en:

* modelo;
* tokenizer;
* backend;
* kernels;
* precisión numérica;
* algoritmo de decoding;

pueden alterar el resultado.

---

# 33. Temperatura y probabilidad condicional

Recordemos del módulo anterior:

$$
P(x_t \mid x_{<t})
$$

significa:

> probabilidad del token actual dado todo el contexto anterior.

La temperatura modifica la distribución utilizada para realizar la selección.

Podemos representar conceptualmente:

$$
P_T(x_t \mid x_{<t})
=
softmax
\left(
\frac{z_t}{T}
\right)
$$

Por tanto:

```text
Contexto
   ↓
Logits
   ↓
Temperatura
   ↓
Distribución
   ↓
Sampling
```

---

# 34. Temperatura no modifica el contexto

Otra distinción importante:

```text
temperature
```

no agrega información al modelo.

No aumenta:

```text
context window
```

No agrega:

```text
documentos
```

No crea:

```text
conocimiento
```

No realiza:

```text
RAG
```

No cambia:

```text
embeddings
```

Modifica la distribución utilizada en la generación.

---

# 35. Temperatura tampoco corrige alucinaciones por sí sola

Supongamos que el modelo no dispone de información suficiente.

Cambiar:

```text
T = 0.2
```

no garantiza verdad.

Y cambiar:

```text
T = 1.5
```

tampoco proporciona conocimiento adicional.

La relación correcta es:

```text
Temperatura
      ↓
selección / diversidad

RAG
      ↓
acceso a información externa

Entrenamiento
      ↓
parámetros aprendidos

Prompt
      ↓
condicionamiento de la generación
```

Son mecanismos diferentes.

---

# 36. Temperatura baja no significa verdad

Este error es especialmente peligroso en aplicaciones profesionales.

Supongamos:

```text
Modelo:
"El contrato establece una multa de $50.000."
```

Con temperatura baja, el modelo puede repetir esa afirmación de forma muy consistente.

Pero:

> consistencia no equivale a exactitud factual.

Podemos tener:

```text
Respuesta incorrecta
+
alta confianza
+
baja temperatura
```

Por eso:

```text
determinismo ≠ veracidad
```

---

# 37. Ejemplo en auditoría

Supongamos que un sistema analiza:

```text
Libro Mayor
```

y debe producir:

```json
{
  "riesgo": "ALTO",
  "monto": 150000
}
```

Una temperatura baja puede ayudar a reducir variaciones de formato o selección.

Pero no garantiza que:

```text
150000
```

sea el monto correcto.

La exactitud depende también de:

* calidad de los datos;
* extracción;
* cálculos;
* contexto;
* reglas;
* recuperación documental;
* instrucciones;
* validaciones;
* modelo;
* controles deterministas externos.

Por tanto:

```text
sampling controlado
≠
sistema de auditoría confiable
```

---

# 38. Sampling y JSON estructurado

Supongamos que solicitamos:

```text
Devuelve únicamente JSON.
```

El sampling puede influir en la probabilidad de producir:

```text
{
  "riesgo": "ALTO"
}
```

frente a:

```text
Claro, aquí tienes el resultado:

{
  "riesgo": "ALTO"
}
```

Sin embargo, en sistemas profesionales no conviene depender únicamente de temperatura.

Se pueden utilizar además:

* esquemas estructurados;
* validación JSON;
* constrained decoding;
* function calling;
* tool calling;
* validadores externos;
* reintentos controlados;
* salidas tipadas.

---

# 39. Decoding

El término **decoding** se refiere al proceso mediante el cual los logits o probabilidades se convierten en una secuencia concreta de tokens.

Podemos visualizarlo:

```text
MODELO
   ↓
LOGITS
   ↓
DECODING
   ├── Greedy
   ├── Sampling
   ├── Temperature
   ├── Top-K
   ├── Top-P
   └── otras restricciones
   ↓
TOKEN
```

Por tanto:

> el modelo calcula una distribución; el algoritmo de decoding determina cómo utilizarla para construir la salida.

---

# 40. Modelo vs algoritmo de decoding

Esta distinción es muy importante.

Supongamos:

```text
Modelo A
```

y utilizamos:

```text
Temperature = 0.2
```

Después utilizamos:

```text
Temperature = 1.0
```

El modelo base puede ser exactamente el mismo.

Lo que cambia es el proceso de generación.

Podemos representarlo:

```text
                 ┌── decoding A
MODELO ──────────┤
                 └── decoding B
```

Por eso dos sistemas pueden utilizar los mismos pesos y producir comportamientos diferentes dependiendo de su configuración de generación.

---

# 41. Sampling y arquitectura del modelo

En este punto podemos conectar los conceptos estudiados.

El modelo recibe tokens:

```text
TOKEN IDs
   ↓
EMBEDDINGS
   ↓
ARQUITECTURA DEL MODELO
   ↓
REPRESENTACIÓN DEL CONTEXTO
   ↓
LOGITS
```

Después:

```text
LOGITS
   ↓
TEMPERATURA
   ↓
PROBABILIDADES
   ↓
SAMPLING
   ↓
SIGUIENTE TOKEN
```

Por tanto, la temperatura no modifica la arquitectura.

No cambia:

* Transformer;
* atención;
* embeddings;
* número de capas;
* parámetros;
* pesos.

Actúa en una etapa posterior.

---

# 42. Relación con el prompt

El prompt sí puede cambiar completamente la distribución.

Por ejemplo:

### Prompt A

```text
Completa:

El agua hierve a
```

### Prompt B

```text
Escribe una respuesta creativa:

El agua hierve a
```

El contexto modifica los logits.

Después la temperatura actúa sobre esos logits.

Podemos visualizar:

```text
PROMPT
  ↓
CONTEXTO
  ↓
MODELO
  ↓
LOGITS
  ↓
TEMPERATURA
  ↓
SAMPLING
  ↓
TOKEN
```

Esto explica por qué:

> el prompt no controla directamente una respuesta concreta; condiciona la distribución sobre posibles respuestas.

---

# 43. Una analogía útil

Imaginemos un restaurante.

El modelo genera un menú de opciones:

```text
Pizza      60 %
Pasta      25 %
Ensalada   10 %
Sopa        5 %
```

La temperatura sería como modificar qué tan estrictamente seguimos ese ranking probabilístico.

### Temperatura baja

```text
Pizza
```

domina fuertemente.

### Temperatura mayor

```text
Pizza
Pasta
Ensalada
```

tienen mayor oportunidad relativa.

Pero la temperatura:

> no inventa nuevos platos que el sistema no haya considerado.

---

# 44. Entropía y temperatura

Aquí aparece una conexión matemática importante.

La entropía de una distribución discreta es:

$$
H(P) =
-\sum_i P_i \log P_i
$$

Una distribución muy concentrada tiene menor entropía.

Ejemplo:

```text
A = 0.99
B = 0.01
```

Hay poca incertidumbre.

Una distribución más uniforme tiene mayor entropía.

Ejemplo:

```text
A = 0.25
B = 0.25
C = 0.25
D = 0.25
```

Hay mayor incertidumbre.

En términos generales, aumentar la temperatura tiende a producir distribuciones más planas y, por tanto, mayor entropía.

Pero la relación exacta depende de la distribución de logits.

---

# 45. Temperatura desde una perspectiva matemática

Partimos de:

$$
z_1,z_2,\ldots,z_n
$$

Los logits.

Con temperatura:

$$
z_i' = \frac{z_i}{T}
$$

Después:

$$
P_i =
\frac{e^{z_i'}}
{\sum_j e^{z_j'}}
$$

o equivalentemente:

$$
P_i =
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

### Si \(T < 1\)

Las diferencias relativas entre logits se amplifican antes de Softmax.

### Si \(T > 1\)

Las diferencias relativas se reducen.

---

# 46. ¿Por qué dividir por temperatura?

Supongamos:

```text
zA = 10
zB = 8
```

Con:

$$
T=1
$$

tenemos:

```text
10
8
```

La diferencia es:

```text
2
```

Con:

$$
T=2
$$

obtenemos:

```text
5
4
```

La diferencia pasa a ser:

```text
1
```

Las opciones quedan relativamente más cercanas.

Con:

$$
T=0.5
$$

obtenemos:

```text
20
16
```

La diferencia pasa a ser:

```text
4
```

La distribución se vuelve más concentrada.

---

# 47. Temperatura como transformación, no como conocimiento

Podemos resumir:

```text
LOGITS
   │
   │ contienen la señal producida por el modelo
   ▼
TEMPERATURA
   │
   │ transforma la escala relativa
   ▼
PROBABILIDADES
   │
   │ representan una distribución
   ▼
SAMPLING
   │
   │ selecciona
   ▼
TOKEN
```

Esto permite separar tres conceptos:

### Modelo

Produce logits.

### Temperatura

Transforma la distribución.

### Sampling

Selecciona una realización concreta.

---

# 48. ¿Qué ocurre si no utilizamos sampling?

Dependiendo del sistema, puede utilizarse una estrategia determinista como:

```text
argmax
```

Es decir:

$$
x_t = \arg\max_x P(x|contexto)
$$

Mientras que sampling puede representarse conceptualmente como:

$$
x_t \sim P(x|contexto)
$$

La diferencia es fundamental:

```text
argmax
→ seleccionar máximo

sampling
→ muestrear según distribución
```

---

# 49. Sampling multinomial

Una forma clásica de sampling consiste en tratar las probabilidades como intervalos.

Supongamos:

```text
A = 0.50
B = 0.30
C = 0.20
```

Podemos imaginar una línea:

```text
0.00        0.50       0.80       1.00
|------------|----------|-----------|
      A           B           C
```

Si el generador aleatorio produce:

```text
0.35
```

seleccionamos:

```text
A
```

Si produce:

```text
0.72
```

seleccionamos:

```text
B
```

Si produce:

```text
0.91
```

seleccionamos:

```text
C
```

Esto es una intuición del muestreo categórico.

---

# 50. Sampling y cadenas autoregresivas

Un LLM autoregresivo genera:

$$
x_1,x_2,x_3,\ldots,x_n
$$

utilizando:

$$
P(x_1,\ldots,x_n)
=
\prod_{t=1}^{n}
P(x_t|x_{<t})
$$

En cada posición:

```text
contexto
   ↓
P(x_t | contexto)
   ↓
sampling
   ↓
x_t
```

El token elegido pasa a formar parte del siguiente contexto.

Por ello:

```text
sampling
```

ocurre repetidamente durante toda la generación.

---

# 51. El efecto acumulativo

Supongamos que una respuesta contiene 100 tokens.

Si cada token tiene varias alternativas posibles, existen cantidades enormes de secuencias potenciales.

Una pequeña diferencia en el token 5 puede cambiar:

```text
token 6
token 7
token 8
...
token 100
```

Por ello, el sampling puede producir respuestas significativamente diferentes incluso cuando comienzan de manera similar.

---

# 52. Beam Search

Existe otra familia de estrategias denominada **Beam Search**.

En lugar de mantener solamente una posibilidad, mantiene varias secuencias candidatas.

Conceptualmente:

```text
Inicio
 ├── A
 │    ├── X
 │    └── Y
 │
 └── B
      ├── X
      └── Z
```

El algoritmo conserva las secuencias con mejores puntuaciones acumuladas.

Beam Search ha sido históricamente importante en tareas de generación como:

* traducción automática;
* reconocimiento de voz;
* generación secuencial.

En LLM conversacionales modernos, el decoding utilizado puede ser diferente y depende del sistema.

---

# 53. Greedy, Sampling y Beam Search

| Estrategia  | Idea principal                             |
| ----------- | ------------------------------------------ |
| Greedy      | elegir el token más probable               |
| Sampling    | muestrear según probabilidades             |
| Top-K       | sampling entre los K candidatos            |
| Top-P       | sampling dentro de una masa probabilística |
| Beam Search | explorar varias secuencias candidatas      |

No existe una estrategia universalmente correcta para todas las tareas.

La estrategia debe relacionarse con el objetivo del sistema.

---

# 54. ¿Qué ocurre con código?

En generación de código normalmente interesa:

* coherencia;
* sintaxis;
* reproducibilidad;
* adherencia a requisitos.

Una distribución excesivamente dispersa puede introducir más variabilidad.

Por ello, un sistema de generación de código puede preferir una configuración más conservadora, además de utilizar:

* validación sintáctica;
* ejecución de pruebas;
* linters;
* compilación;
* evaluación automática.

Nuevamente:

```text
temperatura baja
```

no garantiza:

```text
código correcto
```

---

# 55. ¿Qué ocurre con escritura creativa?

En escritura creativa puede ser útil permitir mayor diversidad.

Por ejemplo:

```text
"Escribe cinco metáforas sobre la lluvia."
```

Una distribución más flexible puede producir alternativas menos previsibles.

Pero también puede aumentar:

```text
repetición
incoherencia
desviación temática
```

si se lleva demasiado lejos.

Por eso temperatura es un parámetro de **control**, no un control de calidad.

---

# 56. ¿Qué ocurre en extracción de datos?

Supongamos:

```text
Texto:
El cliente pagó $12.500 el 15 de septiembre.
```

Queremos:

```json
{
  "monto": 12500,
  "fecha": "15-09"
}
```

Aquí normalmente importa más:

```text
consistencia
estructura
precisión
validación
```

que creatividad.

Por tanto, el diseño del sistema debe favorecer una generación controlada y, sobre todo, validaciones externas.

---

# 57. Una distinción fundamental: generación vs decisión empresarial

Un LLM puede producir:

```text
"riesgo alto"
```

mediante una distribución probabilística.

Pero una organización puede tener una regla:

```text
Si monto > 100000
y evidencia = insuficiente
→ escalar a auditor humano
```

La segunda es una **regla determinista de negocio**.

Por eso una arquitectura profesional puede combinar:

```text
LLM
+
reglas
+
validadores
+
datos
+
human-in-the-loop
```

No todo debe resolverse mediante sampling.

---

# 58. Sampling y sistemas críticos

En sistemas de alto impacto conviene distinguir:

```text
Generación probabilística
```

de:

```text
Decisión controlada
```

Ejemplo:

```text
LLM:
"El documento parece presentar una inconsistencia."

Sistema:
IF inconsistencia_detectada
AND monto > umbral
THEN enviar a revisión humana
```

El LLM puede ayudar a interpretar información.

La regla puede controlar una acción crítica.

---

# 59. Temperatura no es un mecanismo de seguridad

Cambiar:

```text
temperature = 0
```

no protege automáticamente contra:

* prompt injection;
* datos maliciosos;
* RAG poisoning;
* fuga de información;
* herramientas peligrosas;
* instrucciones contradictorias;
* generación incorrecta.

La seguridad requiere controles específicos.

Por ejemplo:

```text
Autenticación
Autorización
Validación
Sandboxing
Filtrado
Tool permissions
Guardrails
Auditoría
Rate limiting
Human-in-the-loop
```

---

# 60. Relación con Prompt Engineering

El prompt modifica el contexto:

```text
PROMPT
   ↓
CONTEXTO
   ↓
MODELO
   ↓
LOGITS
   ↓
TEMPERATURA
   ↓
SAMPLING
   ↓
SALIDA
```

Por tanto, un prompt engineer necesita entender que:

> el prompt no selecciona directamente el token; modifica las condiciones bajo las cuales el modelo calcula su distribución.

Esto explica por qué una pequeña modificación del prompt puede cambiar significativamente la salida.

---

# 61. Ejemplo de Prompt Engineering

### Prompt A

```text
Explica la inteligencia artificial.
```

### Prompt B

```text
Explica la inteligencia artificial a un estudiante
que nunca ha programado. Utiliza un ejemplo de
un restaurante y termina con tres preguntas de
autoevaluación.
```

El segundo prompt condiciona de forma diferente la distribución de posibles continuaciones.

Después, temperatura y sampling operan sobre esa distribución.

Por tanto:

```text
Prompt
```

y:

```text
Temperature
```

no son equivalentes.

El primero cambia el **contexto**.

El segundo cambia la **forma de seleccionar** a partir de la distribución resultante.

---

# 62. Error conceptual frecuente

No debemos pensar:

```text
Prompt
→ respuesta
```

como una relación determinista simple.

Es más correcto:

```text
Prompt
   ↓
Contexto
   ↓
Distribución condicional
   ↓
Decoding
   ↓
Respuesta
```

Esta diferencia es fundamental para comprender los LLM.

---

# 63. Temperatura y "creatividad"

La palabra "creatividad" puede ser útil como simplificación pedagógica, pero técnicamente debemos ser precisos.

La temperatura controla la dispersión de la distribución.

No existe una variable interna llamada:

```text
CREATIVIDAD = 0.8
```

que el modelo esté aumentando.

Es más correcto decir:

> una temperatura mayor puede permitir seleccionar tokens menos probables y producir mayor diversidad en las salidas.

---

# 64. Temperatura y alucinaciones

Podemos tener:

### Temperatura baja

```text
Respuesta incorrecta
Respuesta incorrecta
Respuesta incorrecta
```

### Temperatura alta

```text
Respuesta incorrecta A
Respuesta incorrecta B
Respuesta incorrecta C
```

En ambos casos puede existir un problema factual.

Por tanto:

```text
Temperatura ≠ verificador de hechos
```

Para reducir alucinaciones son relevantes mecanismos como:

* grounding;
* RAG;
* recuperación de fuentes;
* herramientas;
* validación;
* citación;
* evaluación;
* restricciones de dominio.

---

# 65. Relación con RAG

En un sistema RAG:

```text
Usuario
   ↓
Consulta
   ↓
Retriever
   ↓
Documentos
   ↓
Contexto
   ↓
LLM
   ↓
Logits
   ↓
Decoding
   ↓
Respuesta
```

La temperatura aparece cerca del final.

Por tanto:

> una mala recuperación no se arregla simplemente modificando la temperatura.

Si el sistema recuperó el documento incorrecto:

```text
Retriever incorrecto
```

la generación puede ser perfectamente coherente y aun así estar basada en información incorrecta.

---

# 66. Temperatura y evaluación

Para evaluar un sistema de generación debemos controlar la configuración.

Si hacemos:

```text
Prueba A:
temperature = 0.2
```

y:

```text
Prueba B:
temperature = 1.2
```

las diferencias de resultados pueden deberse parcialmente al decoding.

Por ello, en evaluaciones experimentales conviene registrar:

```text
modelo
versión
prompt
datos
temperatura
Top-K
Top-P
seed
fecha
configuración
```

Esto mejora la reproducibilidad.

---

# 67. Parámetros de generación

Un sistema puede tener muchos parámetros relacionados con decoding.

Entre ellos pueden aparecer:

```text
temperature
top_p
top_k
seed
max_tokens
stop sequences
frequency penalties
presence penalties
repetition controls
```

No todos los proveedores implementan los mismos parámetros.

Tampoco necesariamente utilizan exactamente las mismas definiciones o algoritmos.

Por ello:

> los nombres de los parámetros no deben interpretarse como una garantía de comportamiento idéntico entre proveedores.

---

# 68. Temperature frente a Top-P

Una diferencia conceptual importante:

### Temperature

Modifica:

```text
la forma de la distribución
```

### Top-P

Modifica:

```text
el conjunto de candidatos considerados
```

Ejemplo:

```text
Temperatura:
"aplana o concentra la distribución."

Top-P:
"descarta candidatos fuera de la masa acumulada seleccionada."
```

---

# 69. Temperature frente a Top-K

### Temperature

Trabaja sobre la escala de logits antes de obtener la distribución.

### Top-K

Selecciona los K candidatos de mayor probabilidad.

Por tanto:

```text
Temperature → forma de distribución

Top-K → cantidad de candidatos
```

---

# 70. ¿Debemos usar Temperature y Top-P simultáneamente?

No existe una regla universal.

Depende del sistema y del objetivo.

Es importante comprender que añadir parámetros no significa automáticamente obtener un sistema mejor.

Cada parámetro agrega una dimensión de comportamiento que debe ser evaluada.

En ingeniería profesional:

```text
cambiar configuración
→ ejecutar evaluación
→ medir resultado
→ comparar
```

es preferible a:

```text
subir todos los parámetros
→ asumir que mejora
```

---

# 71. Ejemplo experimental

Podemos construir un pequeño experimento.

Prompt:

```text
Escribe una idea para una aplicación de IA.
```

Ejecutamos:

```text
T = 0.2
```

10 veces.

Después:

```text
T = 0.8
```

10 veces.

Y:

```text
T = 1.2
```

10 veces.

Registramos:

```text
diversidad
coherencia
repetición
errores
adherencia al prompt
```

Así podemos observar empíricamente el efecto del decoding.

---

# 72. Métricas posibles

Para un experimento de este tipo podemos medir:

### Diversidad

Por ejemplo:

```text
número de respuestas únicas
```

### Repetición

```text
n-gram repetition rate
```

### Adherencia

Comparar contra criterios definidos.

### Calidad

Evaluación humana o métricas automáticas apropiadas.

### Exactitud

Cuando existe una respuesta verificable.

Esto es mucho más riguroso que afirmar:

> "temperatura alta = creatividad".

---

# 73. Nivel avanzado: calibración

La probabilidad de un modelo no debe confundirse automáticamente con una probabilidad frecuentista perfectamente calibrada.

Un modelo puede producir:

```text
P = 0.95
```

sin que el evento ocurra aproximadamente el 95 % de las veces bajo la población y condiciones correspondientes.

La **calibración** estudia precisamente esta relación.

Una herramienta común es:

### Expected Calibration Error (ECE)

Conceptualmente compara:

```text
confianza declarada
```

con:

```text
frecuencia observada
```

Esto es especialmente importante en sistemas donde las probabilidades se utilizan para tomar decisiones.

---

# 74. Nivel avanzado: temperatura como transformación de distribución

Supongamos una distribución:

$$
P_i =
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

Con temperatura:

$$
P_i(T)=
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

Podemos observar:

$$
\lim_{T\to 0^+}P_i(T)
$$

que tiende a concentrarse sobre los máximos de los logits.

Mientras:

$$
T\to\infty
$$

tiende a producir una distribución cada vez más uniforme:

$$
P_i(T)\rightarrow \frac{1}{N}
$$

si existen \(N\) candidatos y se considera el límite ideal.

---

# 75. Caso de logits iguales

Supongamos:

$$
z_1=z_2=z_3
$$

Entonces:

$$
P_1=P_2=P_3=\frac13
$$

Cambiar la temperatura no altera la igualdad:

$$
\frac{z_1}{T}
=
\frac{z_2}{T}
=
\frac{z_3}{T}
$$

Por tanto:

> la temperatura no puede crear una preferencia que no esté presente en las diferencias entre los logits.

---

# 76. Nivel avanzado: energía y Softmax

Softmax puede interpretarse desde una perspectiva relacionada con modelos de energía.

Si definimos una energía:

$$
E_i=-z_i
$$

entonces:

$$
P_i
\propto
e^{-E_i/T}
$$

Esto conecta el comportamiento de la temperatura con ideas de mecánica estadística y modelos probabilísticos.

A menor temperatura:

```text
diferencias de energía
→ mayor influencia
```

A mayor temperatura:

```text
diferencias de energía
→ menor influencia relativa
```

Esta interpretación ayuda a comprender por qué la temperatura modifica la concentración de la distribución.

---

# 77. Nivel avanzado: temperatura y entropía

Consideremos:

$$
H(P)=-\sum_i P_i\log P_i
$$

En términos generales, al aumentar \(T\):

```text
distribución
→ más plana
→ mayor incertidumbre
→ mayor entropía
```

Y al reducir \(T\):

```text
distribución
→ más concentrada
→ menor incertidumbre
→ menor entropía
```

Esto es una tendencia conceptual importante, aunque el comportamiento exacto depende de los logits y del conjunto de candidatos.

---

# 78. Una advertencia sobre "probabilidad del token"

Supongamos:

```text
"Quito" → 0.70
```

Eso significa, en el contexto matemático del modelo:

> el token correspondiente tiene aproximadamente esa probabilidad dentro de la distribución utilizada.

No significa:

```text
Quito es verdadero con 70 % de probabilidad.
```

Estas son afirmaciones diferentes.

La primera es una propiedad de la distribución del modelo.

La segunda sería una afirmación epistemológica sobre el mundo.

No debemos confundirlas.

---

# 79. Probabilidad lingüística ≠ probabilidad de verdad

Este principio es crítico:

```text
P(token | contexto)
```

no es igual a:

```text
P(afirmación verdadera | mundo)
```

Un modelo puede asignar una probabilidad elevada a una frase porque:

* es lingüísticamente común;
* encaja con el contexto;
* coincide con patrones aprendidos;
* es una continuación plausible.

Eso no demuestra que la afirmación sea verdadera.

---

# 80. ¿Qué controla realmente el prompt?

Podemos ahora responder una pregunta central del Prompt Engineering.

El prompt:

```text
NO
```

controla directamente:

```text
pesos
parámetros
conocimiento almacenado
```

durante una inferencia normal.

Pero sí puede modificar:

```text
contexto
↓
representación interna
↓
logits
↓
distribución
↓
sampling
↓
respuesta
```

Por eso:

> diseñar prompts es, entre otras cosas, diseñar condiciones de inferencia.

---

# 81. Cadena completa

Llegados a este punto podemos unir prácticamente todo lo estudiado:

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
TOKENS
  ↓
EMBEDDINGS
  ↓
CONTEXTO
  ↓
ARQUITECTURA
  ↓
LOGITS
  ↓
TEMPERATURA
  ↓
PROBABILIDADES
  ↓
TOP-K / TOP-P
  ↓
SAMPLING
  ↓
TOKEN
  ↓
NUEVO CONTEXTO
  ↓
REPETICIÓN
  ↓
RESPUESTA
```

Esta cadena es una de las estructuras conceptuales más importantes del curso.

---

# 82. ¿Dónde está el Prompt Engineering?

El Prompt Engineering puede influir especialmente en:

```text
PROMPT
   ↓
CONTEXTO
   ↓
CONDICIONAMIENTO DEL MODELO
   ↓
DISTRIBUCIÓN
   ↓
SALIDA
```

Pero no debemos imaginar que el prompt "programa" directamente los pesos del modelo.

La distinción es:

```text
Entrenamiento
→ modifica parámetros

Prompt
→ modifica condiciones de inferencia

Sampling
→ selecciona una realización de la distribución
```

---

# 83. Aplicación profesional

Una arquitectura profesional puede verse así:

```text
                    ┌──────────────┐
                    │   PROMPT     │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │   CONTEXTO   │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │    MODELO    │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │    LOGITS    │
                    └──────┬───────┘
                           ↓
                  ┌──────────────────┐
                  │ DECODING         │
                  │                  │
                  │ Temperature      │
                  │ Top-K            │
                  │ Top-P            │
                  │ Constraints      │
                  └────────┬─────────┘
                           ↓
                    ┌──────────────┐
                    │    TOKEN     │
                    └──────┬───────┘
                           ↓
                    NUEVO CONTEXTO
```

---

# 84. Errores que un ingeniero debe evitar

## Error 1

> "Temperatura alta hace al modelo más inteligente."

Incorrecto.

---

## Error 2

> "Temperatura baja hace al modelo más preciso."

No necesariamente.

Puede hacerlo más consistente, pero la precisión factual depende de muchos otros factores.

---

## Error 3

> "Temperatura 0 elimina todas las diferencias."

No necesariamente. Depende de la implementación y del sistema.

---

## Error 4

> "Top-P = 0.9 significa que el modelo usa el 90 % de los tokens."

Incorrecto.

Significa aproximadamente una masa acumulada de probabilidad del 90 %, según la distribución y el procedimiento de selección.

---

## Error 5

> "Sampling es azar puro."

Incorrecto.

El sampling está condicionado por la distribución de probabilidad.

---

## Error 6

> "Una probabilidad de 90 % significa que la respuesta es 90 % verdadera."

Incorrecto.

Es una probabilidad dentro del modelo, no una garantía de verdad.

---

## Error 7

> "La temperatura modifica los parámetros."

Incorrecto.

Actúa durante la generación, no durante el entrenamiento normal.

---

# 85. Tabla de conceptos

| Concepto     | Función                                                |
| ------------ | ------------------------------------------------------ |
| Logit        | puntuación producida antes de Softmax                  |
| Softmax      | transforma logits en distribución                      |
| Probabilidad | representa la distribución de candidatos               |
| Temperature  | modifica la concentración de la distribución           |
| Sampling     | selecciona una muestra                                 |
| Greedy       | selecciona el máximo                                   |
| Top-K        | restringe a K candidatos                               |
| Top-P        | restringe por masa acumulada                           |
| Seed         | controla la inicialización del proceso pseudoaleatorio |
| Decoding     | proceso general de convertir distribución en salida    |
| Beam Search  | explora varias secuencias candidatas                   |
| Entropía     | mide incertidumbre de la distribución                  |

---

# 86. Nivel de maestría: formulación completa

Podemos expresar el proceso de generación como:

$$
z_t=f_\theta(x_{<t})
$$

donde:

* \(f_\theta\) es el modelo;
* \(\theta\) son los parámetros;
* \(x_{<t}\) es el contexto anterior;
* \(z_t\) son los logits.

Posteriormente:

$$
p_t(i)
=
\frac{\exp(z_{t,i}/T)}
{\sum_j\exp(z_{t,j}/T)}
$$

Después se puede aplicar una transformación de candidatos:

$$
C_t = TopK(p_t)
$$

o:

$$
C_t = TopP(p_t)
$$

y finalmente:

$$
x_t \sim p_t(\cdot \mid C_t)
$$

El token seleccionado:

$$
x_t
$$

se incorpora al contexto:

$$
x_{<t+1}
=
(x_{<t},x_t)
$$

y el proceso se repite.

---

# 87. El ciclo autoregresivo completo

```text
                 ┌─────────────────────────────┐
                 │                             │
                 │         CONTEXTO            │
                 │                             │
                 └──────────────┬──────────────┘
                                ↓
                         ┌────────────┐
                         │   MODELO   │
                         └─────┬──────┘
                               ↓
                         ┌────────────┐
                         │   LOGITS   │
                         └─────┬──────┘
                               ↓
                       ┌───────────────┐
                       │ TEMPERATURA   │
                       └──────┬────────┘
                              ↓
                       ┌───────────────┐
                       │ PROBABILIDAD  │
                       └──────┬────────┘
                              ↓
                     ┌──────────────────┐
                     │ TOP-K / TOP-P    │
                     └────────┬─────────┘
                              ↓
                       ┌────────────┐
                       │ SAMPLING   │
                       └─────┬──────┘
                             ↓
                         NUEVO TOKEN
                             │
                             │
                             └───────────────┐
                                             ↓
                                         CONTEXTO
```

---

# 88. ¿Qué debe recordar un ingeniero de IA?

Podemos resumir todo el módulo en ocho ideas:

### 1. El modelo produce logits

```text
Modelo → logits
```

### 2. Los logits se transforman en probabilidades

```text
logits → Softmax → probabilidades
```

### 3. La temperatura modifica esa distribución

```text
temperatura → concentración/dispersión
```

### 4. Sampling selecciona un token

```text
distribución → token
```

### 5. Top-K limita candidatos por cantidad

```text
Top-K → K candidatos
```

### 6. Top-P limita candidatos por masa probabilística

```text
Top-P → masa acumulada
```

### 7. El token seleccionado vuelve al contexto

```text
token → contexto → siguiente predicción
```

### 8. Probabilidad no significa verdad

```text
probabilidad lingüística
≠
probabilidad de verdad
```

---

# 89. Mapa mental

```text
                    DECODING
                       │
        ┌──────────────┼──────────────┐
        │              │              │
     Greedy        Sampling       Beam Search
                       │
              ┌────────┼────────┐
              │        │        │
         Temperature  Top-K    Top-P
              │
              ↓
        Distribución
              │
              ↓
          Selección
              │
              ↓
            Token
```

---

# 90. Relación con los módulos anteriores

La progresión conceptual queda:

```text
03 - Modelo
       ↓
04 - Entrenamiento e inferencia
       ↓
05 - Datos
       ↓
06 - Parámetros
       ↓
07 - Tokens
       ↓
08 - Embeddings
       ↓
09 - Contexto
       ↓
10 - Predicción y generación
       ↓
11 - Probabilidad
       ↓
12 - Temperatura y Sampling
```

Ahora podemos comprender una parte mucho más precisa de lo que ocurre después de que el modelo calcula su distribución.

---

# 91. Ejercicio conceptual 1

Supongamos:

```text
A = 0.60
B = 0.20
C = 0.10
D = 0.10
```

Pregunta:

> ¿Cuál tiene mayor probabilidad?

Respuesta:

```text
A
```

Pero:

> ¿Es imposible seleccionar B mediante sampling?

No.

Mientras B tenga probabilidad positiva, puede ser seleccionado mediante un esquema de sampling compatible.

---

# 92. Ejercicio conceptual 2

Supongamos:

```text
A = 0.95
B = 0.03
C = 0.02
```

Pregunta:

> ¿Temperatura alta garantiza que C será seleccionado?

No.

Puede aumentar su probabilidad relativa dependiendo de los logits y la temperatura, pero no implica que necesariamente será seleccionado.

---

# 93. Ejercicio conceptual 3

Supongamos:

```text
Top-K = 2
```

y:

```text
A = 0.50
B = 0.30
C = 0.15
D = 0.05
```

Los candidatos serán:

```text
A
B
```

C y D quedan fuera del conjunto de candidatos de ese paso.

---

# 94. Ejercicio conceptual 4

Supongamos:

```text
Top-P = 0.80
```

y:

```text
A = 0.50
B = 0.20
C = 0.15
D = 0.10
E = 0.05
```

Acumulamos:

```text
A       → 0.50
A+B     → 0.70
A+B+C   → 0.85
```

Por tanto, dependiendo de la definición exacta del procedimiento de nucleus sampling, el conjunto debe extenderse hasta alcanzar o superar el umbral:

```text
A
B
C
```

---

# 95. Ejercicio conceptual 5

Tenemos:

```text
Prompt
↓
Modelo
↓
Logits
↓
Temperature
↓
Sampling
↓
Token
```

Pregunta:

> ¿En qué etapa se modifican los pesos del modelo?

Respuesta:

```text
En ninguna.
```

Durante inferencia normal:

```text
los pesos permanecen sin modificar.
```

---

# 96. Conexión con ingeniería de sistemas de IA

En un sistema real, no debemos pensar solamente:

```text
"¿Qué temperatura utilizo?"
```

La pregunta profesional es:

```text
¿Qué comportamiento debe tener el sistema?
```

Después:

```text
¿Qué modelo necesito?
```

Luego:

```text
¿Qué contexto necesita?
```

Después:

```text
¿Qué estrategia de decoding es adecuada?
```

Y finalmente:

```text
¿Cómo valido la salida?
```

Esto cambia el enfoque de:

```text
prompt tweaking
```

a:

```text
AI system engineering
```

---

# 97. Arquitectura profesional simplificada

```text
                 USUARIO
                    ↓
                 PROMPT
                    ↓
          ┌──────────────────┐
          │ CONTEXTO / RAG   │
          └────────┬─────────┘
                   ↓
                MODELO
                   ↓
                 LOGITS
                   ↓
              DECODING
        ┌──────────┼──────────┐
        ↓          ↓          ↓
 Temperature     Top-K      Top-P
        └──────────┼──────────┘
                   ↓
                SAMPLING
                   ↓
                 SALIDA
                   ↓
          ┌────────────────┐
          │ VALIDACIÓN     │
          └───────┬────────┘
                  ↓
             SISTEMA FINAL
```

En sistemas profesionales puede existir además:

```text
guardrails
tool calling
validación estructural
reglas de negocio
observabilidad
logging
evaluación
human-in-the-loop
```

---

# 98. Idea central del módulo

La idea fundamental que debe quedar grabada es:

> **El modelo produce una distribución de probabilidad; el decoding determina cómo convertir esa distribución en una secuencia concreta de tokens.**

Dentro de ese proceso:

```text
Temperature
```

modifica la concentración de la distribución,

mientras:

```text
Top-K
Top-P
```

restringen el conjunto de candidatos,

y:

```text
Sampling
```

realiza la selección probabilística.

Por tanto:

```text
LOGITS
   ↓
TEMPERATURA
   ↓
PROBABILIDADES
   ↓
RESTRICCIÓN DE CANDIDATOS
   ↓
SAMPLING
   ↓
TOKEN
```

Y el ciclo vuelve a comenzar.

---

# 99. Regla de oro

> **La temperatura controla cómo de concentrada está la distribución; sampling decide cómo se selecciona una realización de esa distribución.**

Y una segunda regla es todavía más importante para ingeniería de IA:

> **Una salida más determinista no es necesariamente una salida más verdadera.**

---

# 100. Checklist de dominio

Antes de avanzar al siguiente módulo deberías poder explicar sin memorizar:

* [ ] Qué es sampling.
* [ ] Qué diferencia existe entre sampling y azar uniforme.
* [ ] Qué es Greedy Decoding.
* [ ] Qué son los logits.
* [ ] Cómo Softmax convierte logits en probabilidades.
* [ ] Qué hace la temperatura.
* [ ] Qué ocurre cuando \(T<1\).
* [ ] Qué ocurre cuando \(T>1\).
* [ ] Qué significa aproximadamente \(T\rightarrow0\).
* [ ] Por qué temperatura no modifica los parámetros.
* [ ] Qué es Top-K.
* [ ] Qué es Top-P.
* [ ] Diferencia entre Top-K y Top-P.
* [ ] Qué es nucleus sampling.
* [ ] Qué significa decoding.
* [ ] Qué es una seed.
* [ ] Por qué sampling puede producir diferentes respuestas.
* [ ] Por qué pequeñas diferencias pueden propagarse durante la generación.
* [ ] Qué relación existe entre temperatura y entropía.
* [ ] Por qué probabilidad de token no significa probabilidad de verdad.
* [ ] Cómo interactúan prompt, contexto, logits, temperatura y sampling.
* [ ] Por qué temperatura baja no garantiza exactitud.
* [ ] Por qué RAG y temperatura solucionan problemas diferentes.
* [ ] Por qué un sistema profesional necesita validación además del LLM.

---

# 101. Resumen final

```text
PROMPT
   ↓
CONTEXTO
   ↓
MODELO
   ↓
LOGITS
   ↓
SOFTMAX + TEMPERATURA
   ↓
DISTRIBUCIÓN
   ↓
TOP-K / TOP-P
   ↓
SAMPLING
   ↓
TOKEN
   ↓
NUEVO CONTEXTO
   ↓
REPETIR
```

El modelo no "elige una frase" de una sola vez.

Construye la respuesta **token por token**, utilizando distribuciones probabilísticas condicionadas por el contexto.

La temperatura permite controlar la concentración de esas distribuciones.

Top-K y Top-P permiten restringir los candidatos.

Sampling permite introducir una selección probabilística.

Y el proceso completo se repite autoregresivamente hasta producir la respuesta.

La consecuencia más importante para Prompt Engineering es:

> **El prompt condiciona la distribución; el decoding controla cómo se convierte esa distribución en una salida concreta.**

Comprender esta diferencia marca el paso desde aprender simplemente a "escribir prompts" hacia comprender realmente la **ingeniería de inferencia de sistemas basados en modelos de lenguaje**.
