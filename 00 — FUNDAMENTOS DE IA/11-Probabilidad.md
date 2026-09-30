# 11 — Probabilidad

> **Objetivo:** comprender la probabilidad desde sus fundamentos matemáticos hasta su aplicación en inteligencia artificial y modelos de lenguaje. El objetivo no es convertir este módulo en un curso completo de estadística, sino construir la base necesaria para entender cómo un modelo de IA representa incertidumbre, calcula distribuciones de salida y toma decisiones durante la inferencia.

---

# 1. ¿Qué es la probabilidad?

La probabilidad es una herramienta matemática para representar **incertidumbre**.

Cuando no conocemos con certeza el resultado de un evento, podemos describir diferentes resultados posibles y asignarles probabilidades.

Por ejemplo:

```text
Lanzar una moneda
```

Resultados:

```text
Cara
Cruz
```

Si la moneda es ideal:

$$
P(Cara)=0.5
$$

$$
P(Cruz)=0.5
$$

La suma es:

$$
0.5+0.5=1
$$

Por definición:

$$
0 \leq P(A) \leq 1
$$

---

# 2. Probabilidad como distribución

Una probabilidad no solamente responde:

> "¿Qué tan probable es este evento?"

También permite construir una **distribución de probabilidad**.

Ejemplo:

```text
Resultado       Probabilidad

A                  0.50
B                  0.30
C                  0.15
D                  0.05
```

La suma:

$$
0.50+0.30+0.15+0.05=1
$$

La distribución representa cómo se reparte la probabilidad entre los resultados posibles.

---

# 3. Eventos

Un **evento** es un resultado o conjunto de resultados que nos interesa estudiar.

Ejemplo:

```text
Lanzar un dado
```

Espacio de resultados:

$$
\Omega=\{1,2,3,4,5,6\}
$$

Evento:

> obtener un número par.

Entonces:

$$
A=\{2,4,6\}
$$

Si el dado es justo:

$$
P(A)=\frac{3}{6}=0.5
$$

---

# 4. Espacio muestral

El **espacio muestral** contiene todos los resultados posibles de un experimento.

Por ejemplo:

```text
Dado:

Ω = {1,2,3,4,5,6}
```

Para un modelo de lenguaje, podemos pensar conceptualmente en algo mucho mayor:

```text
Ω = todos los tokens posibles del vocabulario
```

Si el vocabulario tuviera:

```text
100 000 tokens
```

la predicción del modelo produciría una distribución sobre esos posibles tokens.

---

# 5. Probabilidad en un LLM

Supongamos que el contexto es:

> "El cielo es"

El modelo podría producir una distribución simplificada:

```text
Token        Probabilidad

azul            0.70
claro           0.10
gris             0.08
rojo             0.02
verde            0.01
otros            0.09
```

La distribución completa tendría que sumar:

$$
1
$$

El modelo no solamente dice:

```text
azul
```

Conceptualmente produce algo parecido a:

```text
P(token | contexto)
```

---

# 6. Probabilidad condicional

Este concepto es fundamental para comprender los modelos de lenguaje.

La probabilidad condicional responde:

> ¿Cuál es la probabilidad de A suponiendo que conocemos B?

Se escribe:

$$
P(A|B)
$$

y se lee:

> "Probabilidad de A dado B".

---

# 7. Ejemplo sencillo

Supongamos:

```text
A = que una persona lleve paraguas
B = que esté lloviendo
```

Podríamos preguntar:

$$
P(paraguas|lluvia)
$$

Es decir:

> ¿Cuál es la probabilidad de que lleve paraguas dado que sabemos que está lloviendo?

Esto no es igual necesariamente a:

$$
P(paraguas)
$$

La información adicional cambia la probabilidad.

---

# 8. Probabilidad condicional en lenguaje

Supongamos:

```text
"El perro está"
```

Queremos estimar:

$$
P(x_{t+1}|\text{El perro está})
$$

Podríamos obtener:

```text
corriendo     0.35
durmiendo     0.25
comiendo      0.20
jugando       0.10
otros         0.10
```

Ahora añadimos contexto:

```text
"El perro está cansado y..."
```

La distribución cambia.

Por ejemplo:

```text
durmiendo     0.45
descansando   0.25
comiendo      0.08
jugando       0.03
...
```

El contexto condiciona la predicción.

---

# 9. Esta es la base matemática de los LLM

Un modelo autoregresivo puede representarse como:

$$
P(x_t|x_1,x_2,\ldots,x_{t-1})
$$

Es decir:

> probabilidad del token actual dado todos los tokens anteriores.

Este principio aparece una y otra vez en los modelos de lenguaje.

---

# 10. Regla del producto

La probabilidad conjunta de una secuencia puede expresarse mediante probabilidades condicionales.

Por ejemplo:

$$
P(A,B)=P(A)P(B|A)
$$

Para una secuencia más larga:

$$
P(x_1,x_2,\ldots,x_n)
=
\prod_{t=1}^{n}
P(x_t|x_1,\ldots,x_{t-1})
$$

Esta ecuación es extremadamente importante.

Explica cómo una probabilidad conjunta puede descomponerse en una serie de predicciones condicionadas.

---

# 11. Ejemplo de una frase

Supongamos:

```text
El gato duerme.
```

Simplificando:

$$
P(El,gato,dorme,.)
$$

puede escribirse como:

$$
P(El)
\times
P(gato|El)
\times
P(duerme|El,gato)
\times
P(.|El,gato,duerme)
$$

Un modelo autoregresivo utiliza precisamente este tipo de factorización.

---

# 12. Probabilidad conjunta

La **probabilidad conjunta** representa la probabilidad de que ocurran varios eventos conjuntamente.

Se escribe:

$$
P(A,B)
$$

Por ejemplo:

> Probabilidad de que llueva y una persona lleve paraguas.

$$
P(lluvia,paraguas)
$$

En modelos de lenguaje:

$$
P(x_1,x_2,\ldots,x_n)
$$

representa la probabilidad conjunta de una secuencia.

---

# 13. Probabilidad marginal

La **probabilidad marginal** representa la probabilidad de una variable sin condicionar explícitamente en otra.

Por ejemplo:

$$
P(A)
$$

Si conocemos una distribución conjunta:

$$
P(A,B)
$$

podemos obtener la marginal de \(A\):

$$
P(A)=\sum_B P(A,B)
$$

En modelos probabilísticos, esta operación aparece frecuentemente.

---

# 14. Probabilidad condicional y causalidad

Es importante no confundir:

```text
probabilidad condicional
```

con:

```text
causalidad
```

Por ejemplo:

$$
P(enfermedad|síntoma)
$$

no significa necesariamente:

> "el síntoma causa la enfermedad".

Simplemente representa una probabilidad condicionada por la información disponible.

Esto es especialmente importante en IA.

---

# 15. Probabilidad no significa causalidad

Un modelo puede aprender:

```text
A aparece frecuentemente junto a B
```

sin necesariamente aprender:

```text
A causa B
```

Esto importa mucho cuando utilizamos modelos para:

* medicina;
* finanzas;
* auditoría;
* seguridad;
* decisiones empresariales;
* análisis científico.

---

# 16. Probabilidad frecuentista vs. bayesiana

Existen diferentes interpretaciones de la probabilidad.

Dos grandes perspectivas son:

### Frecuentista

La probabilidad se interpreta en relación con frecuencias de eventos bajo repeticiones hipotéticas.

### Bayesiana

La probabilidad puede representar un grado de credencia sobre una hipótesis dada la información disponible.

No es necesario resolver aquí el debate filosófico entre ambas.

Para ingeniería de IA es más importante comprender que la probabilidad permite representar incertidumbre y actualizar creencias bajo determinadas hipótesis y datos.

---

# 17. Teorema de Bayes

El teorema de Bayes es:

$$
P(A|B)
=
\frac{P(B|A)P(A)}
{P(B)}
$$

Sus componentes:

```text
P(A)
→ prior

P(B|A)
→ verosimilitud

P(B)
→ evidencia

P(A|B)
→ posterior
```

---

# 18. Ejemplo conceptual de Bayes

Supongamos una auditoría.

Tenemos:

```text
A = transacción fraudulenta
B = transacción con una anomalía
```

Podemos preguntar:

$$
P(fraude|anomalía)
$$

Esto no es necesariamente igual a:

$$
P(anomalía|fraude)
$$

Son preguntas diferentes.

Bayes permite relacionarlas:

$$
P(fraude|anomalía)
=
\frac{
P(anomalía|fraude)P(fraude)
}{
P(anomalía)
}
$$

---

# 19. Importancia del prior

Supongamos que una anomalía es bastante frecuente incluso en transacciones legítimas.

Entonces:

```text
anomalía
≠
fraude
```

El porcentaje inicial de fraude importa.

Esto se denomina **prior**.

Es una de las razones por las que interpretar una señal aislada puede llevar a conclusiones incorrectas.

---

# 20. Distribuciones de probabilidad

Una distribución describe cómo se reparte la probabilidad.

Ejemplo discreto:

```text
A → 0.50
B → 0.30
C → 0.20
```

Ejemplo continuo:

```text
altura
peso
temperatura
tiempo
```

En IA aparecen ambos tipos de conceptos.

Los tokens de un vocabulario representan un espacio discreto.

---

# 21. Variable aleatoria

Una **variable aleatoria** es una variable cuyo resultado depende de un proceso probabilístico.

Por ejemplo:

$$
X = resultado\ de\ un\ dado
$$

Entonces:

$$
X\in\{1,2,3,4,5,6\}
$$

En un LLM podemos pensar conceptualmente:

$$
X_{t+1}=siguiente\ token
$$

donde \(X_{t+1}\) puede tomar valores del vocabulario.

---

# 22. Distribución discreta

Una variable discreta puede tomar valores separados.

Por ejemplo:

```text
Número del dado
```

En un LLM:

```text
token 1
token 2
token 3
...
token N
```

Cada token puede recibir una probabilidad.

---

# 23. Distribución continua

Una variable continua puede tomar infinitos valores dentro de un intervalo.

Por ejemplo:

```text
temperatura = 21.372...
```

Las distribuciones continuas se utilizan ampliamente en estadística y machine learning.

Sin embargo, la salida inmediata de un LLM autoregresivo sobre un vocabulario suele ser una distribución discreta sobre tokens.

---

# 24. Media o esperanza

La **esperanza matemática** representa el valor promedio esperado de una variable aleatoria bajo su distribución.

Para una variable discreta:

$$
E[X]=\sum_x xP(x)
$$

Ejemplo con un dado justo:

$$
E[X]
=
1\frac16+
2\frac16+
3\frac16+
4\frac16+
5\frac16+
6\frac16
$$

por lo que:

$$
E[X]=3.5
$$

No significa que vayamos a obtener 3.5 en una tirada.

Significa que ese es el valor esperado a largo plazo.

---

# 25. Varianza

La varianza mide cuánto se dispersan los valores respecto a la media.

$$
Var(X)=E[(X-E[X])^2]
$$

Una varianza alta significa mayor dispersión.

Una varianza baja significa resultados más concentrados.

En machine learning y estadística, la varianza es fundamental para estudiar incertidumbre y comportamiento de estimadores.

---

# 26. Desviación estándar

La desviación estándar es:

$$
\sigma=\sqrt{Var(X)}
$$

Tiene la ventaja de estar expresada en las mismas unidades que la variable original.

---

# 27. Incertidumbre

La probabilidad permite representar incertidumbre.

Supongamos:

```text
Modelo A:

A 0.99
B 0.005
C 0.005
```

La distribución está muy concentrada.

Ahora:

```text
Modelo B:

A 0.34
B 0.33
C 0.33
```

La distribución es mucho más incierta.

La salida más probable es A en ambos casos, pero el grado de concentración es muy diferente.

---

# 28. Entropía

La entropía de Shannon se define como:

$$
H(P)
=
-\sum_i P_i\log P_i
$$

Representa una medida de incertidumbre de una distribución.

Ejemplo:

```text
Distribución 1

A = 0.99
B = 0.01
```

Tiene baja entropía.

Mientras:

```text
Distribución 2

A = 0.50
B = 0.50
```

tiene mayor entropía.

---

# 29. Entropía en un LLM

Supongamos que el modelo recibe:

> "La capital de Francia es..."

Puede producir una distribución muy concentrada:

```text
París      0.97
Lyon       0.01
Marsella   0.005
...
```

En cambio, ante:

> "Escribe una historia sobre un robot..."

puede haber muchas continuaciones plausibles.

La distribución puede ser más dispersa.

Por tanto:

```text
mayor dispersión
→ mayor incertidumbre
→ mayor entropía
```

como intuición general.

---

# 30. Entropía no significa creatividad

Una distribución con mayor entropía tiene más incertidumbre.

Eso puede facilitar diversidad durante sampling.

Pero:

```text
entropía
≠
creatividad humana
```

Es importante mantener esta distinción.

---

# 31. Logaritmos y probabilidad

Los logaritmos aparecen constantemente en machine learning.

¿Por qué?

Porque convertir productos en sumas simplifica los cálculos:

$$
\log(ab)=\log(a)+\log(b)
$$

Si:

$$
P=P_1P_2P_3
$$

entonces:

$$
\log P
=
\log P_1+
\log P_2+
\log P_3
$$

Esto resulta muy útil cuando trabajamos con secuencias largas.

---

# 32. Log-probabilidad

Si un token tiene:

$$
P=0.8
$$

su log-probabilidad natural es:

$$
\log(0.8)\approx -0.223
$$

Las probabilidades menores que 1 tienen logaritmos negativos.

Cuanto menor es la probabilidad, más negativa será su log-probabilidad.

---

# 33. ¿Por qué los modelos utilizan log-probabilidades?

Principalmente por razones matemáticas y numéricas.

Supongamos:

```text
P1 = 0.1
P2 = 0.2
P3 = 0.05
P4 = 0.01
...
```

Multiplicar cientos o miles de probabilidades pequeñas puede producir valores extremadamente próximos a cero.

En logaritmos:

```text
producto
   ↓
suma
```

lo que es más manejable.

---

# 34. Cross-Entropy

Una función de pérdida muy importante es la **entropía cruzada**.

Para una observación cuyo token correcto es \(y\):

$$
L=-\log P(y|x)
$$

Si:

$$
P(y|x)=0.9
$$

entonces:

$$
L=-\log(0.9)
$$

es pequeña.

Si:

$$
P(y|x)=0.01
$$

la pérdida es mucho mayor.

---

# 35. Intuición de Cross-Entropy

Podemos pensarlo así:

```text
Modelo muy confiado en la respuesta correcta
        ↓
pérdida pequeña
```

```text
Modelo muy confiado en la respuesta incorrecta
        ↓
pérdida grande
```

Esto permite entrenar el modelo para aumentar la probabilidad de los tokens observados en los datos de entrenamiento.

---

# 36. Máxima verosimilitud

Una forma clásica de formular el entrenamiento es mediante **Maximum Likelihood Estimation (MLE)**.

Se busca encontrar parámetros \(\theta\) que hagan probable la información observada:

$$
\theta^*
=
\arg\max_\theta
P(D|\theta)
$$

donde:

* \(D\) = datos;
* \(\theta\) = parámetros del modelo.

En la práctica suele trabajarse con el logaritmo:

$$
\theta^*
=
\arg\max_\theta
\log P(D|\theta)
$$

o equivalentemente minimizar la pérdida negativa.

---

# 37. Probabilidad vs. puntuación

En machine learning podemos encontrar:

```text
score
logit
probabilidad
log-probabilidad
```

No son necesariamente lo mismo.

Por ejemplo:

```text
Logit:
4.2

Probabilidad:
0.87

Log-probabilidad:
-0.14
```

Un error frecuente es interpretar cualquier número producido por un modelo como "probabilidad".

---

# 38. Logits no son probabilidades

Recordemos:

```text
logits
  ↓
softmax
  ↓
probabilidades
```

Los logits pueden ser:

```text
-5.2
 0.3
 7.8
```

Las probabilidades deben cumplir:

```text
0 ≤ P ≤ 1
```

y sumar 1.

---

# 39. Softmax

Para logits \(z_i\):

$$
P_i=
\frac{e^{z_i}}
{\sum_j e^{z_j}}
$$

La función transforma un vector de puntuaciones en una distribución de probabilidad.

Ejemplo:

```text
Logits

A = 2.0
B = 1.0
C = 0.0
```

Después de softmax:

```text
A ≈ 0.665
B ≈ 0.245
C ≈ 0.090
```

La suma es aproximadamente:

$$
1
$$

---

# 40. ¿Por qué exponencial?

La exponencial:

$$
e^x
$$

garantiza valores positivos antes de la normalización.

Entonces:

```text
logits
 ↓
exponencial
 ↓
valores positivos
 ↓
normalización
 ↓
probabilidades
```

---

# 41. Temperature desde la probabilidad

La temperatura modifica los logits antes de softmax:

$$
P_i=
\frac{e^{z_i/T}}
{\sum_j e^{z_j/T}}
$$

Si:

$$
T<1
$$

la distribución tiende a concentrarse.

Si:

$$
T>1
$$

la distribución tiende a suavizarse.

Conceptualmente:

```text
T baja
→ distribución concentrada

T alta
→ distribución distribuida
```

---

# 42. Sampling

Una vez obtenida una distribución:

```text
A = 0.60
B = 0.25
C = 0.10
D = 0.05
```

podemos muestrear un resultado.

Una ejecución podría seleccionar:

```text
A
```

Otra:

```text
B
```

si el procedimiento es estocástico.

La probabilidad describe las posibilidades; el sampling produce una realización concreta.

---

# 43. Probabilidad y decisión

Supongamos:

```text
A = 0.55
B = 0.45
```

Seleccionar A no significa que A sea "verdadero".

Simplemente:

$$
P(A)>P(B)
$$

Si el sistema selecciona siempre el máximo:

```text
argmax
```

obtendrá A.

Si utiliza sampling:

```text
A
```

será más frecuente que B, pero B puede aparecer.

---

# 44. Top-k

Si tenemos:

```text
A 0.40
B 0.30
C 0.15
D 0.08
E 0.07
```

con:

```text
k = 3
```

nos quedamos con:

```text
A
B
C
```

y normalmente se renormalizan sus probabilidades.

---

# 45. Top-p

Supongamos:

```text
A 0.50
B 0.25
C 0.15
D 0.07
E 0.03
```

Con:

```text
p = 0.80
```

seleccionamos el conjunto mínimo cuya probabilidad acumulada alcance al menos 0.80:

```text
A + B + C = 0.90
```

Por tanto:

```text
A, B, C
```

formarían el conjunto de sampling.

---

# 46. Probabilidad y contexto

Una de las ideas más importantes de este curso es:

$$
P(token|contexto)
$$

El contexto modifica la distribución.

Por ejemplo:

```text
"El jugador lanzó la..."
```

puede favorecer:

```text
pelota
```

Mientras:

```text
"El pescador lanzó la..."
```

puede favorecer:

```text
red
```

La misma estructura lingüística puede producir distribuciones distintas.

---

# 47. El contexto no es solamente el último token

Un LLM moderno puede utilizar información distribuida por todo el contexto disponible.

Por ejemplo:

```text
"Jorge compró un automóvil nuevo.
Después de varias semanas, decidió..."
```

La predicción puede depender de información anterior, no solamente de:

```text
"decidió"
```

El mecanismo de atención permite que diferentes posiciones interactúen.

Esto será estudiado en profundidad en módulos posteriores.

---

# 48. Probabilidad y embeddings

Los embeddings representan tokens u otras unidades como vectores.

```text
Token
 ↓
Embedding
 ↓
representación vectorial
```

El modelo transforma estas representaciones mediante múltiples capas.

Al final:

```text
representación contextual
 ↓
proyección
 ↓
logits
 ↓
probabilidades
```

Por tanto, la probabilidad de salida aparece al final de una cadena de transformaciones numéricas.

---

# 49. Probabilidad y parámetros

Los parámetros del modelo determinan cómo se transforman las entradas.

Podemos representarlo:

$$
P_\theta(x_t|x_{<t})
$$

El subíndice:

$$
\theta
$$

indica que la distribución depende de los parámetros del modelo.

Por eso:

```text
mismo contexto
+
modelos con parámetros diferentes
=
distribuciones potencialmente diferentes
```

---

# 50. Probabilidad y entrenamiento

Durante el entrenamiento:

```text
Datos
 ↓
modelo
 ↓
probabilidad del token correcto
 ↓
loss
 ↓
gradientes
 ↓
actualización de parámetros
```

El modelo aprende a asignar probabilidades más altas a las continuaciones observadas en los datos.

---

# 51. Probabilidad y generalización

El objetivo no es simplemente memorizar cada secuencia.

El modelo aprende una función que puede generalizar a contextos no idénticos a los observados durante entrenamiento.

Conceptualmente:

```text
ejemplos
 ↓
patrones
 ↓
parámetros
 ↓
distribuciones
 ↓
predicciones nuevas
```

La calidad de esa generalización depende de muchos factores:

* datos;
* arquitectura;
* entrenamiento;
* escala;
* regularización;
* ajuste;
* evaluación;
* distribución de datos.

---

# 52. Calibración

Aquí aparece un concepto más avanzado:

**calibración probabilística**.

Un sistema está calibrado cuando, de manera aproximada, sus predicciones probabilísticas corresponden a las frecuencias observadas.

Ejemplo:

Si un sistema produce muchas predicciones con:

```text
confianza = 0.80
```

idealmente, aproximadamente el 80 % de ellas debería ser correcto bajo las condiciones de evaluación correspondientes.

---

# 53. Confianza no siempre es calibración

Un modelo puede ser:

```text
muy confiado
```

y estar equivocado.

Por ejemplo:

```text
Respuesta:
"París"

confianza aparente:
99 %
```

Pero eso no demuestra que:

```text
probabilidad real de corrección = 99 %
```

La calibración debe medirse empíricamente.

---

# 54. Overconfidence

Un modelo puede presentar **overconfidence**:

> asignar una confianza demasiado alta a predicciones incorrectas.

Esto es especialmente problemático en:

* medicina;
* finanzas;
* legal;
* auditoría;
* seguridad;
* control industrial.

Una respuesta segura no es necesariamente una respuesta correcta.

---

# 55. Incertidumbre aleatoria y epistémica

En aprendizaje automático se suele distinguir entre diferentes fuentes de incertidumbre.

### Aleatoria — aleatoric

Proviene de la variabilidad inherente de los datos.

### Epistémica

Está relacionada con incertidumbre sobre el modelo o aquello que no ha aprendido adecuadamente.

Esta clasificación es útil conceptualmente, aunque su tratamiento exacto en LLM modernos es más complejo que en modelos probabilísticos clásicos.

---

# 56. Un LLM no proporciona automáticamente incertidumbre confiable

Que el modelo produzca:

```text
"Estoy 95 % seguro..."
```

no significa que ese 95 % esté calibrado.

La frase puede ser simplemente otra secuencia generada.

Para obtener estimaciones de incertidumbre útiles pueden ser necesarios métodos adicionales:

* calibración;
* ensembles;
* múltiples muestras;
* evaluación estadística;
* verificación externa;
* recuperación de evidencia;
* modelos especializados.

---

# 57. Probabilidad de token vs. probabilidad de respuesta

Esto es extremadamente importante.

Un modelo puede tener:

```text
alta probabilidad
```

para cada token individual y aun así producir una respuesta incorrecta.

Además, una respuesta completa tiene una probabilidad conjunta:

$$
P(x_1,\ldots,x_n)
=
\prod_t P(x_t|x_{<t})
$$

Como se multiplican muchas probabilidades menores que 1, la probabilidad conjunta de una secuencia larga puede ser extremadamente pequeña.

Por eso no debe interpretarse ingenuamente como:

```text
probabilidad de verdad de toda la respuesta
```

---

# 58. Probabilidad de una secuencia

Supongamos:

```text
Token 1 = 0.9
Token 2 = 0.8
Token 3 = 0.7
```

La probabilidad conjunta simplificada sería:

$$
0.9\times0.8\times0.7=0.504
$$

Si añadimos muchos tokens, el producto disminuye rápidamente.

Por eso en la práctica se utilizan frecuentemente log-probabilidades.

---

# 59. Perplexity

Una métrica relacionada con la incertidumbre predictiva de un modelo de lenguaje es:

$$
PPL=e^L
$$

donde \(L\) representa una pérdida promedio adecuada.

Otra forma habitual es:

$$
PPL
=
\exp
\left(
-\frac{1}{N}
\sum_{t=1}^{N}
\log P(x_t|x_{<t})
\right)
$$

Intuitivamente:

> mide qué tan sorprendido está el modelo, en promedio, por los tokens observados.

---

# 60. ¿Qué significa una perplexity alta?

Una perplexity alta indica que el modelo asigna, en promedio, menor probabilidad a los tokens observados.

Una perplexity baja indica que asigna mayor probabilidad promedio.

Pero hay una advertencia fundamental:

> **Perplexity no equivale a calidad general de un asistente.**

Dos modelos pueden tener resultados diferentes en perplexity y comportarse de forma distinta en:

* seguimiento de instrucciones;
* código;
* razonamiento;
* seguridad;
* uso de herramientas;
* generación estructurada.

---

# 61. Probabilidad y alucinaciones

Un LLM puede generar:

```text
respuesta lingüísticamente muy probable
```

pero:

```text
hecho incorrecto
```

Esto ocurre porque el objetivo de generación y el criterio de verdad no son idénticos.

Podemos representar:

```text
Probabilidad lingüística
        │
        ▼
"¿Qué continuación parece plausible?"
        │
        ≠
        │
        ▼
Verificación factual
"¿Esto ocurrió realmente?"
```

---

# 62. RAG y probabilidad

En un sistema RAG:

```text
Pregunta
 ↓
embedding
 ↓
retrieval
 ↓
documentos
 ↓
contexto
 ↓
LLM
 ↓
probabilidades
 ↓
respuesta
```

El retrieval puede cambiar drásticamente la distribución de salida.

Por ejemplo:

```text
Sin documento:
"Creo que..."

Con documento:
"Según el informe..."
```

La evidencia proporcionada al contexto condiciona la generación.

---

# 63. Pero RAG no elimina la incertidumbre

Un documento recuperado puede ser:

* incorrecto;
* antiguo;
* irrelevante;
* manipulado;
* incompleto;
* mal recuperado.

Por tanto:

```text
RAG
≠
verdad automática
```

El sistema sigue necesitando mecanismos de validación.

---

# 64. Probabilidad en sistemas de clasificación

La probabilidad también aparece fuera de los LLM.

Ejemplo:

```text
Modelo de clasificación:

fraude       0.85
legítimo     0.15
```

Esto puede utilizarse para tomar decisiones.

Pero nuevamente:

```text
0.85
```

debe interpretarse según cómo fue entrenado y calibrado el modelo.

No significa automáticamente:

> "existe un 85 % de probabilidad objetiva de fraude".

---

# 65. Umbrales

Un sistema puede utilizar un umbral:

```text
si P(fraude) ≥ 0.80
    → enviar a revisión
```

Pero el umbral no debe elegirse arbitrariamente.

Debe considerar:

* coste de falsos positivos;
* coste de falsos negativos;
* distribución de datos;
* capacidad operativa;
* riesgo;
* regulación;
* objetivos del negocio.

---

# 66. Falso positivo y falso negativo

Supongamos:

```text
Fraude / No fraude
```

### Falso positivo

El sistema marca fraude cuando no lo era.

### Falso negativo

El sistema no detecta fraude cuando sí existía.

La probabilidad ayuda a modelar estas decisiones, pero la política de decisión depende del contexto.

---

# 67. Probabilidad y riesgo

Probabilidad y riesgo no son exactamente lo mismo.

Una formulación conceptual sencilla:

$$
Riesgo
\approx
Probabilidad
\times
Impacto
$$

Por ejemplo:

```text
Evento A
probabilidad baja
impacto enorme
```

puede ser relevante.

Mientras:

```text
Evento B
probabilidad alta
impacto pequeño
```

también puede ser importante.

La gestión de riesgo requiere ambas dimensiones.

---

# 68. Ejemplo aplicado a IA empresarial

Supongamos un sistema que clasifica documentos:

```text
Normal       0.92
Sospechoso   0.08
```

Podría parecer una señal clara.

Pero si el coste de no detectar un documento realmente sospechoso es muy alto, quizá no sea apropiado simplemente aceptar:

```text
0.92 → normal
```

El sistema podría necesitar:

```text
clasificación
 ↓
umbral
 ↓
revisión humana
```

---

# 69. Probabilidad y decisiones

La probabilidad describe incertidumbre.

La decisión incorpora además:

```text
costos
beneficios
restricciones
políticas
objetivos
```

Por eso:

```text
probabilidad
≠
decisión
```

Una IA puede producir probabilidades, pero la política de decisión puede estar definida por el sistema humano.

---

# 70. Conexión con Prompt Engineering

Un prompt puede modificar el contexto y, por tanto, modificar la distribución de probabilidad.

Ejemplo:

### Prompt A

```text
Responde.
```

### Prompt B

```text
Analiza el problema paso a paso,
identifica los supuestos,
separa hechos de inferencias
y entrega una conclusión estructurada.
```

Los contextos son diferentes.

Por tanto, las distribuciones internas pueden ser diferentes.

Conceptualmente:

$$
P(y|C_A)
\neq
P(y|C_B)
$$

El prompt funciona como una forma de **condicionamiento de la generación**.

---

# 71. Prompt no significa control absoluto

Modificar el contexto no garantiza una salida determinada.

Por ejemplo:

```text
Prompt:
"Devuelve exactamente JSON."
```

no implica necesariamente que cualquier modelo o configuración produzca JSON válido.

Puede existir:

```text
variabilidad
+
limitaciones del modelo
+
conflictos de instrucciones
+
errores de decoding
```

Por eso la ingeniería profesional utiliza:

```text
prompt
+
schema
+
validación
+
reintentos
+
restricciones
```

---

# 72. Probabilidad y temperatura

Podemos resumir:

```text
MODELO
  ↓
LOGITS
  ↓
TEMPERATURE
  ↓
SOFTMAX
  ↓
DISTRIBUCIÓN
  ↓
SAMPLING
  ↓
TOKEN
```

Pero algunas implementaciones pueden aplicar operaciones y parámetros en órdenes o mecanismos específicos diferentes.

Por eso, para sistemas reales:

> **la documentación de la API/modelo concreto tiene prioridad sobre la simplificación conceptual.**

---

# 73. Un ejemplo completo

Pregunta:

> "¿Cuál es la capital de Francia?"

El proceso conceptual:

```text
Prompt
 ↓
Tokenización
 ↓
Embeddings
 ↓
Transformer
 ↓
Representación contextual
 ↓
Logits
 ↓
Softmax
 ↓
Distribución
```

Por ejemplo:

```text
París       0.96
Lyon        0.01
Marsella    0.005
...
```

Después:

```text
Decoding
 ↓
París
```

La palabra:

```text
París
```

es la salida seleccionada.

---

# 74. Pero ¿qué pasa si el modelo se equivoca?

Supongamos que produce:

```text
Lyon
```

La existencia de una alta probabilidad interna para Lyon no convertiría a Lyon en la capital de Francia.

Esto demuestra:

```text
modelo probabilístico
        ≠
oráculo de verdad
```

La verdad factual debe verificarse cuando la tarea lo requiere.

---

# 75. Probabilidad en una arquitectura profesional

Un sistema robusto puede separar:

```text
                    USUARIO
                       │
                       ▼
                     PROMPT
                       │
                       ▼
                  RECUPERACIÓN
                       │
                       ▼
                    CONTEXTO
                       │
                       ▼
                     LLM
                       │
                       ▼
                DISTRIBUCIÓN
                       │
                       ▼
                   DECODING
                       │
                       ▼
                  RESPUESTA
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        VALIDACIÓN            POLÍTICAS
             │                   │
             └─────────┬─────────┘
                       ▼
                    SALIDA
```

La probabilidad es solamente una parte de la arquitectura.

---

# 76. Conceptos que NO deben confundirse

| Concepto         | Qué representa                                              |
| ---------------- | ----------------------------------------------------------- |
| Logit            | Puntuación previa a la normalización                        |
| Probabilidad     | Distribución normalizada                                    |
| Log-probabilidad | Logaritmo de una probabilidad                               |
| Entropía         | Incertidumbre de una distribución                           |
| Temperature      | Escalado de logits que modifica la distribución             |
| Sampling         | Selección probabilística                                    |
| Greedy           | Selección del máximo                                        |
| Top-k            | Limita candidatos por cantidad                              |
| Top-p            | Limita candidatos por masa acumulada                        |
| Confianza        | Medida declarada o calculada de seguridad en una predicción |
| Calibración      | Correspondencia entre confianza y frecuencia observada      |
| Verdad           | Correspondencia con el estado real de las cosas             |

---

# 77. Error conceptual frecuente

Un principiante puede pensar:

```text
LLM dice:
"París"

Entonces:
P(París)=0.98

Por tanto:
98 % de certeza de que es verdad.
```

Esto no es una interpretación correcta.

La probabilidad del token se refiere al proceso de generación condicionado por el contexto y los parámetros del modelo.

No es automáticamente una probabilidad epistemológica sobre la verdad de la afirmación.

---

# 78. Nivel de maestría: distribución predictiva

Podemos expresar un modelo probabilístico parametrizado como:

$$
p_\theta(y|x)
$$

donde:

* \(x\) = entrada;
* \(y\) = salida;
* \(\theta\) = parámetros.

En un LLM autoregresivo:

$$
p_\theta(x_{1:T})
=
\prod_{t=1}^{T}
p_\theta(x_t|x_{<t})
$$

La arquitectura transforma la secuencia de entrada en una distribución predictiva.

---

# 79. Nivel de maestría: máxima verosimilitud

Durante entrenamiento:

$$
\theta^*
=
\arg\max_\theta
\sum_{t}
\log
p_\theta(x_t|x_{<t})
$$

Equivalentemente:

$$
\theta^*
=
\arg\min_\theta
-\sum_t
\log
p_\theta(x_t|x_{<t})
$$

Este vínculo conecta:

```text
probabilidad
↓
log-probabilidad
↓
loss
↓
gradiente
↓
optimización
↓
parámetros
```

---

# 80. Nivel avanzado: energía y logits

En algunos enfoques probabilísticos, las puntuaciones pueden interpretarse mediante funciones de energía.

De forma conceptual:

$$
P(x)
\propto
e^{-E(x)}
$$

donde \(E(x)\) representa una energía asociada al estado.

Aunque un LLM estándar no debe reducirse simplemente a un modelo energético, esta perspectiva ayuda a comprender que las puntuaciones internas pueden transformarse en distribuciones mediante normalización.

---

# 81. Nivel avanzado: información

La teoría de la información está estrechamente relacionada con los modelos de lenguaje.

La información asociada a un evento puede representarse como:

$$
I(x)=-\log P(x)
$$

Por tanto:

```text
evento muy probable
→ poca información

evento poco probable
→ mucha información
```

Ejemplo:

```text
P = 0.9
```

produce poca sorpresa.

Mientras:

```text
P = 0.001
```

produce mucha más sorpresa.

---

# 82. Sorpresa (*surprisal*)

El término **surprisal** se utiliza para:

$$
S(x)=-\log P(x)
$$

Intuitivamente:

```text
alta probabilidad
→ baja sorpresa

baja probabilidad
→ alta sorpresa
```

Esta idea conecta directamente con:

* entropía;
* cross-entropy;
* perplexity;
* modelado del lenguaje.

---

# 83. Entropía como sorpresa esperada

La entropía puede interpretarse como la sorpresa esperada:

$$
H(X)=E[-\log P(X)]
$$

o:

$$
H(X)
=
-\sum_x P(x)\log P(x)
$$

Esto conecta elegantemente:

```text
probabilidad
 ↓
sorpresa
 ↓
entropía
```

---

# 84. Cross-entropy como evaluación predictiva

La cross-entropy mide, en esencia, cuánto penalizamos al modelo por asignar probabilidades a los resultados observados.

En lenguaje:

```text
¿Qué probabilidad asignó el modelo
al token que realmente apareció?
```

Si la probabilidad es alta:

```text
penalización baja
```

Si es baja:

```text
penalización alta
```

---

# 85. Por qué esto importa para IA

La probabilidad conecta prácticamente todos los módulos anteriores:

```text
TOKENS
  ↓
EMBEDDINGS
  ↓
REPRESENTACIONES
  ↓
LOGITS
  ↓
PROBABILIDADES
  ↓
DECODING
  ↓
GENERACIÓN
```

Y durante entrenamiento:

```text
PREDICCIÓN
  ↓
PROBABILIDAD
  ↓
LOSS
  ↓
GRADIENTE
  ↓
PARÁMETROS
```

Por eso la probabilidad no es un concepto aislado.

Es uno de los puentes matemáticos entre:

```text
machine learning
```

y:

```text
generative AI
```

---

# 86. Relación con el módulo 10

En el módulo anterior vimos:

```text
Predicción
↓
Logits
↓
Probabilidades
↓
Decoding
↓
Generación
```

Ahora podemos profundizar:

```text
Logits
 ↓
Softmax
 ↓
Distribución
 ↓
Probabilidad condicional
 ↓
Sampling / Argmax
 ↓
Token
```

Y podemos entender matemáticamente por qué la generación no es simplemente:

```text
"el modelo sabe la respuesta"
```

sino:

```text
el modelo calcula una distribución
sobre posibles continuaciones.
```

---

# 87. Relación con el siguiente nivel

Después de comprender probabilidad, podemos estudiar con mayor profundidad:

```text
Probabilidad
     ↓
Atención
     ↓
Transformers
     ↓
Representaciones contextuales
     ↓
Logits
     ↓
Decoding
```

En particular, será necesario entender cómo una arquitectura Transformer transforma:

```text
tokens
```

en:

```text
representaciones contextuales
```

que finalmente permiten calcular las distribuciones de salida.

---

# 88. Mapa conceptual

```text
                         PROBABILIDAD
                              │
              ┌───────────────┼────────────────┐
              │               │                │
              ▼               ▼                ▼
        Condicional       Distribución      Incertidumbre
              │               │                │
              ▼               ▼                ▼
        P(A | B)          P(x)             Entropía
              │               │                │
              └───────┬───────┘                │
                      ▼                        │
                MODELOS DE IA ◄────────────────┘
                      │
                      ▼
               LLM AUTOREGRESIVO
                      │
                      ▼
             P(token | contexto)
                      │
                      ▼
                    LOGITS
                      │
                      ▼
                   SOFTMAX
                      │
                      ▼
                PROBABILIDADES
                      │
             ┌────────┴────────┐
             ▼                 ▼
          GREEDY            SAMPLING
             │                 │
             └────────┬────────┘
                      ▼
                    TOKEN
                      │
                      ▼
                  GENERACIÓN
```

---

# 89. Checklist de dominio

Antes de avanzar, deberías poder responder:

### Nivel básico

* ¿Qué es una probabilidad?
* ¿Qué es una distribución?
* ¿Qué es un evento?
* ¿Qué es una variable aleatoria?
* ¿Qué significa \(P(A|B)\)?

### Nivel intermedio

* ¿Qué diferencia existe entre probabilidad conjunta y condicional?
* ¿Qué es Bayes?
* ¿Qué es una distribución discreta?
* ¿Qué son esperanza y varianza?
* ¿Qué es entropía?
* ¿Qué es log-probabilidad?

### Nivel LLM

* ¿Qué significa \(P(token|contexto)\)?
* ¿Qué son logits?
* ¿Qué hace softmax?
* ¿Qué diferencia existe entre logits y probabilidades?
* ¿Qué es sampling?
* ¿Qué hacen temperature, top-k y top-p?

### Nivel avanzado

* ¿Qué es cross-entropy?
* ¿Qué relación existe entre máxima verosimilitud y entrenamiento?
* ¿Qué es perplexity?
* ¿Qué es surprisal?
* ¿Qué significa calibración?
* ¿Por qué probabilidad de salida no equivale a probabilidad de verdad?

---

# 90. Idea central del módulo

La idea que debemos conservar es:

> **La probabilidad permite representar matemáticamente la incertidumbre. En un modelo de lenguaje, esa incertidumbre aparece como una distribución sobre posibles siguientes tokens condicionada por el contexto.**

La cadena fundamental es:

```text
CONTEXTO
   ↓
MODELO
   ↓
LOGITS
   ↓
DISTRIBUCIÓN DE PROBABILIDAD
   ↓
DECODING
   ↓
TOKEN
   ↓
NUEVO CONTEXTO
   ↓
NUEVA DISTRIBUCIÓN
   ↓
...
```

Y una distinción debe quedar grabada:

> **Que un modelo asigne alta probabilidad a una salida no demuestra que esa salida sea verdadera.**

Esa diferencia entre **probabilidad, confianza, incertidumbre y verdad** será fundamental para construir sistemas de IA confiables.
