# 10 — Predicción y generación

> **Nivel:** A-Núcleo 15 min / B-Programador / C-Maestría
> **Prerrequisitos:** `07-Tokens.md`, `09-Contexto.md`
> **Siguiente:** `11-Probabilidad.md` (incertidumbre), `12-Temperatura-y-sampling.md` (control)
> **Objetivos:** 1) Explicar `P(x1..xN)=Π P(xt|x<t)` con ejemplo. 2) Distinguir `logits→softmax→decoding`. 3) Entender por qué un error inicial se propaga.
> **Tiempo:** 20 min núcleo, 45 min completo.
> **Relación con 09:** 09 = *qué ve* el modelo (contexto C). Este archivo = *qué hace* con C paso a paso.

---

## A-NÚCLEO (no programadores, sin mates)

### 1. Idea en una frase

Un LLM no escribe como tú: **predice un token, lo añade, vuelve a predecir**. Repetir eso 200 veces = un párrafo.

```text
C = "La capital de Ecuador es"
paso1 → " Quito"  → C = "... es Quito"
paso2 → " y"      → C = "... Quito y"
paso3 → " tiene"  → ...
```

Eso es **generación autoregresiva**: *auto* = se alimenta a sí mismo.

### 2. Predicción ≠ verdad

Predecir = asignar probabilidades a continuaciones plausibles, no consultar una base de datos.

| Prompt | Continuación probable | ¿Verdad? |
|---|---|---|
| `La capital de Ecuador es` | ` Quito (0.92)` | sí |
| `Mi DNI es 1234` | ` 5678 (0.31), 0000 (0.12)...` | **no sabe, inventa fluido** |
| `2+2=` | ` 4 (0.98)` | sí por patrón, no por calcular |

Regla de oro: **`P(token|contexto) ≠ P(verdad|mundo)`**. Ver `GLOSSARY.md`.

### 3. Los 3 pasos siempre

```text
Contexto → [1 Predicción] → [2 Muestreo] → [3 Añadir y repetir]
               logits          token          nuevo contexto
```

1. **Predicción:** dado C, el modelo produce `logits` (puntajes) para todo el vocabulario.
2. **Muestreo (decoding):** convertimos puntajes en 1 token (greedy, temperatura, top-p — detalle en 12).
3. **Bucle:** el token se añade a C y se repite hasta `EOS / stop / max_tokens`.

Si el paso 1 se equivoca al inicio (`Quito → Guayaquil`), todo lo siguiente condiciona sobre ese error. Por eso **el error inicial se propaga**.

> Ejercicio A (5 min): con cualquier chatbot, pide `Completa: "El gato ___" ` 5 veces con T alta. Verás 5 finales distintos. Misma predicción base, distinto muestreo.

---

## B-PROFUNDIZACIÓN (programadores)

### 4. Factorización autoregresiva

La probabilidad conjunta de una secuencia se factoriza:

```text
P(x1..xN) = P(x1) · P(x2|x1) · P(x3|x1,x2) · ... · P(xN|x<N)
```

Ejemplo (simplificado):

```text
P(El,gato,duerme,.) = P(El) × P(gato|El) × P(duerme|El,gato) × P(.|El,gato,duerme)
```

Entrenar = maximizar esa `P` sobre datos reales (ver `04-Entrenamiento-e-inferencia.md`).
Generar = muestrear de esa `P` token a token.

### 5. Logits → softmax → token

```text
C (tokens) → Transformer → h (vector) → W_vocab → logits z ∈ R^V
P(i) = e^{z_i/T} / Σ_j e^{z_j/T}   (T=1 por defecto, ver 12)
token_{t+1} ~ Decoding(P)
```

* `logits` no son probabilidades (pueden ser negativos, no suman 1).
* `softmax` los convierte en probabilidades.
* `Decoding` elige: `greedy = argmax`, `sampling`, `top-k/p`, `beam`. Solo el detalle cambia, la fórmula base es la misma.

Pseudo-código mínimo (conceptual, sin dependencia):

```python
context = encode("La capital de Ecuador es")  # ids
for _ in range(max_tokens):
    logits = modelo(context)          # [V]
    probs = softmax(logits / T)       # [V]
    nxt = sample(probs, top_p=0.9)     # 1 id
    if nxt == EOS: break
    context.append(nxt)
print(decode(context))
```

Puntos que el programador debe fijar:

* `prefill` (procesa C de golpe) ≠ `decode` (1 token por paso, usa KV-cache).
* `max_tokens, stop=["</respuesta>"], EOS` cortan el bucle. Sin ellos, genera hasta el límite.
* `streaming` solo muestra el bucle en vivo, no cambia el algoritmo.

### 6. Por qué se propaga el error + exposure bias

Durante entrenamiento el modelo siempre ve contexto real (`teacher forcing`).
En inferencia ve su propio output. Si genera `Guayaquil` en paso 1, el paso 2 condiciona sobre `...es Guayaquil`, no sobre la verdad.

```text
Entrenamiento: P(Quito | "capital de Ecuador es")       ← contexto real
Inferencia:    P(y | "capital de Ecuador es Guayaquil") ← contexto contaminado
```

Mitigaciones (no magia):

* Instrucción + contexto verificable (RAG) antes de generar.
* Generar → verificar con código/datos (`LLM propone, Python/SQL dispone`).
* Temperatura baja + `json_schema` para tareas cerradas; no confíes en `T=0` como verdad (ver 12).

> Ejercicio B (15 min): mismo prompt, `T=0` vs `T=1.0`, 5 muestras cada uno. Tabla `Respuesta | ¿Correcta? | Tokens`. Concluye cuándo la variabilidad ayuda y cuándo daña.

### 7. Qué NO es generación

* No es retrieval: no abre Wikipedia, predice.
* No es cálculo: `29*37` lo aproxima por patrón; para exactitud usa tool.
* No es memoria continua: cada llamada parte de C. Sin C, no recuerda (ver 09).
* No es determinista aunque `T=0`: cuantización, batching y proveedor pueden variar 1 token.

---

## C-AVANZADO (maestría / PhD senior)

### 8. Puente formal

* `L = -1/N Σ log Pθ(xt|x<t)` (cross-entropy causal), `PPL = exp(L)`.
* `softmax(z+c) = softmax(z)` (invariancia a traslación; estabilidad con `z-max`).
* `Exposure bias` = divergencia `P_train(C_real)` vs `P_infer(C_generado)`. Mitigaciones: scheduled sampling, RL (PPO/DPO), verificación externa, speculative decoding (borrador pequeño + verificación grande, misma distribución si se acepta con criterio).
* Conexión con 11/12: 11 formaliza incertidumbre (`entropía H=-Σ p log p`), 12 controla el operador `Decoding_T,top-k,p`.

### 9. Experimento propuesto

Medir propagación: fija prefijo erróneo vs correcto, misma `T=0.3`, `n=50`.

```text
A: "La capital de Ecuador es Quito y" → tasa de alucinación posterior
B: "La capital de Ecuador es Guayaquil y" → tasa de alucinación posterior
Métrica: % continuaciones que corrigen vs que justifican el error.
```

Hipótesis esperada: B no se autocorrige (>80% sigue coherente con Guayaquil). Eso demuestra condicionamiento, no conocimiento.

Fuentes: Vaswani et al. 2017 (Transformer); Sennrich et al. 2016 (BPE); Liu et al. 2023 (Lost in the Middle, para contexto vs generación); Ouyang et al. 2022 (InstructGPT, teacher forcing vs inferencia).

---

## Resumen + checklist

* Generar = `predecir 1 token → añadir → repetir` sobre `P(xt|x<t)`.
* `logits → softmax(T) → decoding → token → nuevo C`.
* Error inicial contamina todo C futuro.
* `P(token|C) ≠ verdad`. Verifica fuera del modelo.

Checklist antes de seguir a 11:

- [ ] Explicas autoregresivo con ejemplo Quito sin mirar.
- [ ] Distingues `logits` vs `probs` vs `token`.
- [ ] Sabes dónde cortar (`EOS/stop/max_tokens`) y qué es `prefill/decode`.
- [ ] Tienes tabla `T=0 vs 1.0` de tu ejercicio B.

Mapa corregido 00: `03 Modelo → 04 Entrenamiento → 05 Datos → 06 Parámetros → 07 Tokens → 08 Embeddings → 09 Contexto → **10 Predicción (este)** → 11 Probabilidad → 12 Temperatura → 13 Limitaciones`.
