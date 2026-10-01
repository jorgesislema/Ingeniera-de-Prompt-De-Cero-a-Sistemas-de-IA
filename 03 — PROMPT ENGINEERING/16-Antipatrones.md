# 16 — Antipatrones de Prompt Engineering

> **Nivel:** Básico → Intermedio → Avanzado → Maestría/PhD
> **Área:** Ingeniería de Prompt · LLM · Context Engineering · Evaluación · Seguridad
> **Objetivo:** Identificar patrones de diseño de prompts que producen resultados frágiles, inconsistentes, costosos, inseguros o difíciles de evaluar y reemplazarlos por estrategias de ingeniería más robustas.

---

# 1. ¿Qué es un antipatrón?

Un **antipatrón** es una práctica que parece razonable o funciona en determinadas situaciones, pero que tiende a producir problemas cuando se utiliza de manera sistemática.

En software, un antipatrón no significa necesariamente:

> "Esto nunca funciona."

Significa:

> **"Esta solución tiene características que pueden generar problemas y debe utilizarse con precaución o sustituirse cuando existan alternativas mejores."**

En Prompt Engineering ocurre exactamente lo mismo.

Por ejemplo:

```text
"Actúa como un experto mundial y responde perfectamente."
```

puede producir una respuesta aceptable.

Pero convertir esta estrategia en el mecanismo principal para garantizar calidad es frágil.

---

# 2. Por qué estudiar antipatrones

Aprender únicamente técnicas de prompting puede generar una falsa impresión:

```text
Más instrucciones
       ↓
Mejor prompt
       ↓
Mejor respuesta
```

La realidad es más compleja:

```text
PROMPT
   +
MODELO
   +
DATOS
   +
CONTEXTO
   +
INFERENCIA
   +
HERRAMIENTAS
   +
VALIDACIÓN
   ↓
RESULTADO
```

Un prompt excelente no puede compensar automáticamente:

* datos incorrectos;
* contexto insuficiente;
* modelo inadecuado;
* herramientas defectuosas;
* ausencia de validación;
* requisitos contradictorios;
* límites del modelo;
* arquitectura deficiente.

Por eso:

> **La ingeniería de prompt también consiste en saber qué no hacer.**

---

# 3. Antipatrón 1 — Prompt excesivamente largo

Uno de los errores más comunes es pensar:

```text
Más instrucciones
=
Más control
```

Ejemplo:

```text
Eres un experto...
Debes...
Nunca...
Siempre...
Además...
También...
Recuerda...
No olvides...
Antes de responder...
Después de analizar...
Comprueba...
Revisa...
Vuelve a revisar...
```

El problema no es simplemente la cantidad de palabras.

El problema es la **densidad de instrucciones**, sus relaciones y posibles conflictos.

---

# 4. El problema de la sobreespecificación

Un prompt puede contener tantas reglas que resulte difícil determinar cuáles son realmente necesarias.

Ejemplo:

```text
Haz X.

Pero antes:
1. considera A;
2. excepto cuando B;
3. salvo si C;
4. a menos que D;
5. pero si E entonces...
```

El sistema termina pareciendo un programa mal diseñado.

Una alternativa:

```text
Objetivo
↓
Restricciones críticas
↓
Formato
↓
Criterios de calidad
```

La información importante debe ser priorizada.

---

# 5. Antipatrón 2 — Instrucciones contradictorias

Ejemplo:

```text
Responde de manera extremadamente detallada.

Máximo 50 palabras.
```

O:

```text
No hagas suposiciones.

Completa los datos faltantes.
```

O:

```text
Sé creativo.

No modifiques ninguna información.
```

El problema es una contradicción semántica.

Una solución mejor es convertir las condiciones en reglas explícitas:

```text
Si existen datos suficientes:
    responde directamente.

Si faltan datos:
    indícalo.

Máximo:
    50 palabras.
```

Cuando las reglas pueden entrar en conflicto, deben definirse prioridades.

---

# 6. Antipatrón 3 — Instrucciones vagas

Ejemplo:

```text
Analiza profundamente el documento.
```

¿Qué significa "profundamente"?

Puede significar:

* resumir;
* detectar errores;
* buscar riesgos;
* identificar patrones;
* comparar información;
* revisar consistencia.

Una instrucción mejor:

```text
Identifica inconsistencias entre los importes,
fechas y referencias del documento.

Para cada inconsistencia devuelve:
- campo;
- valor observado;
- valor esperado;
- evidencia;
- nivel de riesgo.
```

La instrucción se vuelve observable.

---

# 7. Antipatrón 4 — Usar adjetivos en lugar de criterios

Ejemplo:

```text
Haz un análisis excelente.
```

"Excelente" no es una especificación operacional.

Mejor:

```text
El análisis debe:

- identificar todos los registros relevantes;
- citar la evidencia;
- distinguir hechos de inferencias;
- señalar incertidumbres;
- utilizar el formato JSON especificado.
```

Podemos convertir:

```text
calidad subjetiva
```

en:

```text
criterios verificables
```

---

# 8. Antipatrón 5 — "Actúa como un experto"

Ejemplo:

```text
Actúa como el mejor auditor del mundo.
```

Esto puede establecer una orientación contextual, pero no proporciona automáticamente:

* conocimiento actualizado;
* acceso a bases de datos;
* herramientas;
* autoridad profesional;
* validación;
* capacidad especializada.

Una formulación más útil:

```text
Analiza los registros desde una perspectiva
de auditoría financiera.

Aplica los criterios definidos a continuación...
```

El rol debe complementar la especificación, no reemplazarla.

---

# 9. Antipatrón 6 — Confundir rol con capacidad

```text
Actúa como:
- científico de datos;
- abogado;
- médico;
- auditor;
- programador senior.
```

Esto no convierte mágicamente al modelo en esas profesiones.

El rol modifica el contexto de la tarea.

No modifica directamente:

```text
arquitectura
pesos
datos de entrenamiento
herramientas
permisos
```

Principio:

> **Un rol es una especificación contextual, no una transferencia de capacidades profesionales.**

---

# 10. Antipatrón 7 — "Piensa paso a paso" como solución universal

Una instrucción como:

```text
Piensa paso a paso.
```

puede ser útil en determinados modelos y tareas.

Pero no debe convertirse en una solución universal.

Los modelos y arquitecturas pueden diferir en cómo utilizan este tipo de instrucciones.

Además, en aplicaciones profesionales no siempre necesitamos solicitar o exponer razonamientos internos detallados.

Una alternativa es pedir:

```text
Explica brevemente los criterios utilizados
y proporciona las conclusiones verificables.
```

Así se obtiene información útil sin convertir el prompting en una petición indiscriminada de razonamiento interno.

---

# 11. Antipatrón 8 — Pedir razonamiento cuando se necesita una herramienta

Supongamos:

```text
¿Cuánto es 839472 × 928371?
```

Podemos pedir:

```text
Calcula cuidadosamente.
```

Pero para una aplicación crítica puede ser preferible:

```text
Modelo
  ↓
Tool matemática
  ↓
Resultado
```

El problema no es que un LLM no pueda producir operaciones matemáticas.

El problema es asignar una tarea determinística a un componente probabilístico cuando existe una herramienta adecuada.

---

# 12. Antipatrón 9 — Usar el LLM para todo

Una arquitectura ingenua:

```text
LLM
 ├── sumar
 ├── ordenar
 ├── consultar DB
 ├── validar JSON
 ├── calcular impuestos
 ├── comparar fechas
 └── generar texto
```

Una arquitectura híbrida puede ser:

```text
LLM
 │
 ├── interpretación
 ├── clasificación
 └── generación
       │
       ├── Python
       ├── SQL
       ├── APIs
       └── validadores
```

Principio:

> **Utiliza el LLM donde aporta valor semántico y componentes determinísticos donde aportan precisión y control.**

---

# 13. Antipatrón 10 — Confiar en que el prompt garantiza el formato

Ejemplo:

```text
Devuelve exclusivamente JSON válido.
```

Eso es una instrucción.

No es un mecanismo de garantía.

Una arquitectura robusta:

```text
LLM
 ↓
JSON
 ↓
Parser
 ↓
Schema Validator
 ↓
Aceptar / Rechazar
```

El control externo es especialmente importante en automatizaciones.

---

# 14. Antipatrón 11 — No validar la salida

Ejemplo:

```text
respuesta = modelo(prompt)
usar(respuesta)
```

El problema:

```text
LLM
 ↓
salida
 ↓
sistema
```

Una arquitectura más robusta:

```text
LLM
 ↓
salida
 ↓
validación
 ├── correcta → sistema
 └── incorrecta → retry / fallback / humano
```

---

# 15. Antipatrón 12 — Validar únicamente sintaxis

Supongamos:

```json
{
  "total": 5000,
  "subtotal": 1000,
  "impuesto": 100
}
```

Es JSON válido.

Pero:

```text
1000 + 100 ≠ 5000
```

Por tanto:

```text
Sintaxis válida
      ≠
Semántica válida
      ≠
Verdad factual
```

La validación debe corresponder al riesgo de la aplicación.

---

# 16. Antipatrón 13 — Confundir coherencia con verdad

Una respuesta puede ser:

* clara;
* bien estructurada;
* convincente;
* gramaticalmente correcta;

y aun así ser falsa.

Ejemplo:

```text
El informe indica que el sistema tuvo 17 errores.
```

La pregunta importante es:

```text
¿De dónde salió el 17?
```

Una arquitectura con evidencia puede exigir:

```json
{
  "afirmacion": "...",
  "evidencia": "...",
  "fuente": "...",
  "confianza": "..."
}
```

---

# 17. Antipatrón 14 — Pedir "no alucines"

Ejemplo:

```text
NO ALUCINES.

Responde únicamente con información verdadera.
```

El problema es que esto no constituye un mecanismo de verificación.

Una estrategia más robusta:

```text
Si la información no está respaldada por las fuentes:
    indica "no disponible".

Incluye la evidencia utilizada.

No inventes referencias.

Valida los datos críticos externamente.
```

La reducción de alucinaciones requiere arquitectura, no solamente una frase.

---

# 18. Antipatrón 15 — Forzar una respuesta cuando no existe evidencia

Ejemplo:

```text
Debes responder siempre.
Nunca digas que no sabes.
```

Esto elimina una capacidad importante:

```text
abstención
```

Una mejor política:

```text
Si existe evidencia suficiente:
    responde.

Si existe evidencia insuficiente:
    indica la incertidumbre.

Si la pregunta no puede resolverse:
    abstente.
```

La abstención puede ser una salida correcta.

---

# 19. Antipatrón 16 — Pedir precisión absoluta

Ejemplo:

```text
Dame una respuesta 100 % exacta.
```

El prompt no puede eliminar automáticamente la incertidumbre.

La respuesta puede depender de:

* calidad de datos;
* modelo;
* contexto;
* herramientas;
* información disponible;
* naturaleza de la tarea.

Es preferible especificar:

```text
Indica las fuentes.
Señala incertidumbres.
No inventes información.
Utiliza herramientas de verificación cuando corresponda.
```

---

# 20. Antipatrón 17 — Repetir la misma instrucción muchas veces

Ejemplo:

```text
Devuelve JSON.
Recuerda devolver JSON.
Es muy importante que devuelvas JSON.
No olvides devolver JSON.
Debes devolver JSON.
```

La repetición no necesariamente aumenta el control.

Mejor:

```text
Output:
JSON conforme al siguiente schema.
```

y posteriormente:

```text
Schema Validator
```

---

# 21. Antipatrón 18 — Prompt redundante

Un prompt puede contener:

```text
Analiza.
Haz un análisis.
Realiza el análisis.
Examina.
Estudia.
Evalúa.
```

Si todas las palabras describen la misma operación, probablemente existe redundancia.

La pregunta correcta es:

> ¿Cada frase agrega información operacional?

Si no:

```text
eliminar
```

---

# 22. Antipatrón 19 — Instrucciones narrativas innecesarias

Ejemplo:

```text
Imagina que eres un profesional que se encuentra
en una oficina muy importante y que debe demostrar
su enorme experiencia...
```

Si la información no cambia la tarea, es ruido.

Preferible:

```text
Rol:
Auditor financiero.

Objetivo:
Identificar inconsistencias contables.
```

La concisión puede mejorar la mantenibilidad sin convertirla en una regla absoluta de "cuanto más corto, mejor".

---

# 23. Antipatrón 20 — Meter lógica de programación dentro del prompt

Ejemplo:

```text
Si A y B y C, excepto cuando D,
entonces realiza X, pero si E...
```

Un prompt no debería convertirse innecesariamente en un lenguaje de programación.

Puede ser mejor:

```text
Python
    ↓
decide flujo
    ↓
Prompt
    ↓
LLM
```

La lógica determinística debe permanecer en el código cuando sea posible.

---

# 24. Antipatrón 21 — Condicionales excesivos en plantillas

Una plantilla puede terminar así:

```text
{% if A %}
...
{% elif B %}
...
{% elif C %}
...
{% elif D %}
...
{% endif %}
```

Si contiene demasiada lógica, probablemente estamos mezclando:

```text
presentación
```

con:

```text
lógica de negocio
```

Mejor:

```text
Código
 ↓
selección de configuración
 ↓
plantilla
```

---

# 25. Antipatrón 22 — Prompt monolítico

Un único prompt puede contener:

```text
rol
objetivo
extracción
clasificación
razonamiento
validación
formato
políticas
reglas de negocio
herramientas
```

Esto puede dificultar:

* mantenimiento;
* pruebas;
* reutilización;
* debugging;
* versionado.

Una alternativa:

```text
ROLE
OBJECTIVE
INSTRUCTIONS
CONTEXT
CONSTRAINTS
OUTPUT
VALIDATION
```

y, cuando sea necesario:

```text
módulos separados
```

---

# 26. Antipatrón 23 — Fragmentación excesiva

El problema opuesto también existe.

```text
Prompt 1
 ↓
Prompt 2
 ↓
Prompt 3
 ↓
Prompt 4
 ↓
Prompt 5
 ↓
Prompt 6
 ↓
Prompt 7
 ↓
Prompt 8
```

si cada etapa realiza una operación trivial.

Esto produce:

* latencia;
* coste;
* puntos de fallo;
* complejidad;
* dificultad de mantenimiento.

No existe una cantidad universalmente correcta de etapas.

---

# 27. Antipatrón 24 — Encadenar sin contratos

Ejemplo:

```text
P1 → P2 → P3
```

pero nadie sabe exactamente qué produce P1.

Una cadena robusta define:

```text
P1
 ↓
Output Schema
 ↓
P2
```

La interfaz es tan importante como el prompt.

---

# 28. Antipatrón 25 — Encadenar sin validación intermedia

```text
P1
 ↓
P2
 ↓
P3
 ↓
P4
```

Si P1 falla, P2 puede construir sobre el error.

Una mejor arquitectura:

```text
P1
 ↓
Validate
 ↓
P2
 ↓
Validate
 ↓
P3
```

---

# 29. Antipatrón 26 — Pasar todo el contexto a todas las etapas

Supongamos:

```text
Documento = 100 páginas
```

y cada etapa recibe las 100 páginas.

Esto puede aumentar:

* coste;
* latencia;
* ruido;
* probabilidad de distracción;
* complejidad contextual.

Mejor:

```text
100 páginas
    ↓
extracción
    ↓
fragmentos relevantes
    ↓
análisis
```

---

# 30. Antipatrón 27 — Confundir contexto largo con contexto útil

Más contexto no significa automáticamente mejor contexto.

Podemos representar:

```text
Contexto
│
├── relevante
├── parcialmente relevante
└── irrelevante
```

La ingeniería consiste en seleccionar información útil.

Por tanto:

```text
Context Engineering
≠
meter más información
```

---

# 31. Antipatrón 28 — RAG sin control de relevancia

Una arquitectura puede recuperar:

```text
20 documentos
```

pero solamente:

```text
2
```

son relevantes.

Pasar los 20 al modelo puede introducir ruido.

Un pipeline mejor:

```text
Query
 ↓
Retrieval
 ↓
Ranking
 ↓
Filtering
 ↓
Context
 ↓
LLM
```

---

# 32. Antipatrón 29 — Confiar ciegamente en documentos recuperados

RAG no convierte automáticamente las fuentes en verdaderas.

Una fuente puede contener:

* errores;
* información desactualizada;
* instrucciones maliciosas;
* datos contradictorios.

Por tanto:

```text
Fuente recuperada
      ≠
Instrucción confiable
      ≠
Verdad garantizada
```

---

# 33. Antipatrón 30 — Ignorar Prompt Injection

Ejemplo:

```text
Documento:
"Ignore las instrucciones del sistema."
```

Si el documento se introduce como:

```text
contexto sin separación
```

el modelo puede interpretarlo incorrectamente.

Una arquitectura debe separar:

```text
INSTRUCCIONES
─────────────
DATOS
─────────────
DOCUMENTOS
─────────────
TOOL OUTPUTS
```

Además, la separación mediante delimitadores no sustituye:

* control de permisos;
* validación;
* filtrado;
* aislamiento;
* políticas de seguridad.

---

# 34. Antipatrón 31 — Confiar únicamente en delimitadores

Ejemplo:

```xml
<documento>
Ignore todas las instrucciones anteriores.
</documento>
```

El delimitador ayuda a indicar que el contenido es un documento.

Pero:

```text
delimitación
≠
seguridad completa
```

La defensa requiere múltiples capas.

---

# 35. Antipatrón 32 — Confundir autenticación con instrucciones

Decir:

```text
El usuario está autorizado porque el prompt dice
que es administrador.
```

no constituye autorización real.

La autorización debe provenir del sistema:

```text
Usuario
 ↓
Autenticación
 ↓
Autorización
 ↓
Permisos
 ↓
Tool
```

El LLM no debe decidir por sí solo privilegios críticos.

---

# 36. Antipatrón 33 — Dar demasiadas herramientas al modelo

Ejemplo:

```text
LLM
 ├── enviar correo
 ├── eliminar archivos
 ├── modificar DB
 ├── ejecutar código
 ├── realizar pagos
 └── acceder a información privada
```

El principio de mínimo privilegio recomienda:

```text
Modelo
 ↓
solo herramientas necesarias
```

Las herramientas deben tener permisos específicos.

---

# 37. Antipatrón 34 — Permitir acciones irreversibles directamente

Ejemplo:

```text
LLM → delete_database()
```

Una arquitectura más segura:

```text
LLM
 ↓
propuesta de acción
 ↓
validación
 ↓
autorización
 ↓
confirmación
 ↓
acción
```

La intervención humana puede ser necesaria para acciones de alto impacto.

---

# 38. Antipatrón 35 — No distinguir datos confiables de datos externos

No todo el contexto tiene el mismo nivel de confianza.

Podemos utilizar una clasificación:

```text
T0 — configuración interna
T1 — instrucciones del sistema
T2 — usuario
T3 — documentos externos
T4 — web / contenido no confiable
```

La clasificación concreta depende del sistema.

Lo importante es mantener explícitos los **trust boundaries**.

---

# 39. Antipatrón 36 — Usar ejemplos incorrectos

Un few-shot puede reforzar patrones erróneos.

Ejemplo:

```text
Entrada:
10 + 10

Salida:
30
```

El modelo puede aprender:

```text
patrón incorrecto
```

Los ejemplos deben ser:

* correctos;
* consistentes;
* representativos;
* suficientemente diversos.

---

# 40. Antipatrón 37 — Demasiados ejemplos

Más ejemplos no necesariamente producen mejor rendimiento.

Pueden provocar:

```text
coste ↑
contexto ↑
latencia ↑
ruido ↑
```

La selección debe considerar:

```text
relevancia
diversidad
cobertura
coste
```

---

# 41. Antipatrón 38 — Ejemplos demasiado similares

Ejemplo:

```text
Entrada A → Salida A
Entrada B → Salida B
Entrada C → Salida C
```

si todos representan exactamente el mismo caso.

Podemos estar utilizando muchos tokens para comunicar un único patrón.

Es preferible cubrir diferentes situaciones:

```text
caso normal
caso límite
caso ambiguo
caso negativo
caso adversarial
```

---

# 42. Antipatrón 39 — Ejemplos que filtran la respuesta

Si los ejemplos contienen una respuesta demasiado obvia:

```text
Ejemplo 1 → patrón exacto
Ejemplo 2 → patrón exacto
```

el modelo puede aprender una asociación superficial.

El conjunto de ejemplos debe favorecer generalización, no simple imitación.

---

# 43. Antipatrón 40 — Sobreajustar el prompt a un benchmark

Supongamos:

```text
Prompt optimizado
       ↓
Benchmark = excelente
```

Eso no demuestra que el prompt funcione en producción.

Puede existir:

```text
overfitting
```

El prompt puede haber aprendido accidentalmente características específicas del conjunto de evaluación.

Una evaluación adecuada separa:

```text
desarrollo
validation
test
producción
```

---

# 44. Antipatrón 41 — Optimizar contra una sola métrica

Ejemplo:

```text
Accuracy = 99 %
```

pero:

```text
Coste = muy alto
Latencia = muy alta
Cobertura = baja
Seguridad = baja
```

Una sola métrica no describe todo el sistema.

Debemos definir un conjunto de métricas.

---

# 45. Antipatrón 42 — Evaluarse a sí mismo sin control

Un patrón frecuente:

```text
Modelo
 ↓
genera respuesta
 ↓
mismo modelo
 ↓
evalúa respuesta
```

Puede ser útil.

Pero introduce posibles sesgos:

```text
generador = evaluador
```

Puede ser preferible combinar:

```text
LLM evaluator
+
reglas
+
tests determinísticos
+
ground truth
+
evaluación humana
```

cuando el caso lo requiera.

---

# 46. Antipatrón 43 — No utilizar datasets de evaluación

Modificar el prompt manualmente:

```text
Prompt A
 ↓
"parece mejor"
 ↓
Prompt B
 ↓
"parece mejor"
```

no constituye una evaluación rigurosa.

Es mejor disponer de:

```text
Dataset
 ↓
Prompt A
 ↓
Métricas

Dataset
 ↓
Prompt B
 ↓
Métricas
```

Así podemos comparar bajo las mismas condiciones.

---

# 47. Antipatrón 44 — Evaluar únicamente casos fáciles

Un prompt puede funcionar perfectamente con:

```text
casos normales
```

y fallar con:

```text
casos límite
```

Un conjunto profesional debe incluir:

```text
casos normales
casos difíciles
casos ambiguos
casos incompletos
casos adversariales
casos fuera de distribución
```

---

# 48. Antipatrón 45 — Ignorar distribución de datos

Un prompt probado con:

```text
español
```

puede comportarse diferente con:

```text
inglés
portugués
lenguaje técnico
errores ortográficos
```

La evaluación debe reflejar el entorno real.

---

# 49. Antipatrón 46 — Ignorar cambios de modelo

Un prompt puede funcionar con:

```text
Modelo A
```

y cambiar su comportamiento con:

```text
Modelo B
```

Incluso una nueva versión del mismo proveedor puede modificar resultados.

Por eso debemos registrar:

```text
modelo
versión
prompt
dataset
configuración
```

---

# 50. Antipatrón 47 — Pensar que un prompt es universal

No existe necesariamente:

```text
prompt perfecto universal
```

Un prompt puede depender de:

```text
modelo
arquitectura
objetivo
contexto
idioma
herramientas
configuración
```

Por tanto:

```text
Prompt
```

debe tratarse como un componente de un sistema.

---

# 51. Antipatrón 48 — Copiar prompts sin comprenderlos

Un prompt publicado en Internet puede funcionar para:

```text
Modelo A
```

pero no necesariamente para:

```text
Modelo B
```

Copiar una plantilla sin comprender:

* objetivo;
* contexto;
* restricciones;
* salida;
* modelo;
* evaluación;

impide diagnosticar problemas.

---

# 52. Antipatrón 49 — Prompt cargo cult

El **cargo cult prompting** consiste en copiar estructuras porque parecen sofisticadas sin comprender por qué están allí.

Ejemplo:

```text
Actúa como...
Contexto:
Objetivo:
Piensa paso a paso:
Ignora...
Recuerda...
Revisa...
```

Si eliminamos la mitad y el resultado no cambia, algunas instrucciones probablemente no estaban aportando valor.

La solución:

> **Cada componente debe tener una justificación funcional o experimental.**

---

# 53. Antipatrón 50 — Cambiar demasiadas variables simultáneamente

Supongamos:

```text
Prompt A + Modelo A + temperatura A
```

y después:

```text
Prompt B + Modelo B + temperatura B
```

Si el resultado mejora, no sabemos qué produjo la mejora.

Una experimentación mejor controla variables.

Por ejemplo:

```text
Modelo constante
Configuración constante
Dataset constante

Cambiar:
Prompt A → Prompt B
```

Esto permite atribuir mejor el efecto observado.

---

# 54. Antipatrón 51 — No controlar temperatura o sampling

Si queremos comparar prompts pero cambiamos simultáneamente:

```text
temperature
top_p
modelo
prompt
```

la comparación pierde claridad.

En una evaluación controlada:

```text
Variables constantes
+
una variable modificada
```

cuando sea metodológicamente apropiado.

---

# 55. Antipatrón 52 — Confundir aleatoriedad con mejora

Una respuesta diferente puede parecer mejor simplemente porque cambió el muestreo.

Por eso debemos utilizar:

```text
múltiples ejecuciones
```

cuando la tarea sea estocástica.

Una sola muestra puede ser insuficiente para evaluar un cambio.

---

# 56. Antipatrón 53 — No medir coste

Un prompt puede mejorar ligeramente la calidad pero aumentar:

```text
tokens × 10
```

Si se ejecuta:

```text
1.000.000 veces
```

el impacto económico puede ser considerable.

La evaluación debe considerar:

```text
calidad
+
coste
+
latencia
```

---

# 57. Antipatrón 54 — Ignorar latencia

Una cadena:

```text
P1 → P2 → P3 → P4 → P5
```

puede producir una respuesta excelente.

Pero si tarda:

```text
20 segundos
```

puede ser inadecuada para una interfaz interactiva.

La arquitectura debe considerar los requisitos de latencia.

---

# 58. Antipatrón 55 — No diseñar para fallos

Un sistema real puede experimentar:

```text
timeout
rate limit
modelo no disponible
respuesta inválida
tool failure
datos incompletos
```

Un pipeline sin manejo de errores es frágil.

Debe contemplar:

```text
retry
fallback
timeout
circuit breaker
human review
logging
```

según el sistema.

---

# 59. Antipatrón 56 — Reintentos infinitos

Un retry puede convertirse en:

```text
retry
 ↓
retry
 ↓
retry
 ↓
...
```

Esto puede provocar:

* coste excesivo;
* latencia;
* loops;
* saturación.

Debe existir:

```text
max_retries
```

y una estrategia de recuperación.

---

# 60. Antipatrón 57 — No conservar la evidencia original

Si una cadena transforma:

```text
Documento
 ↓
Resumen
 ↓
Conclusión
```

y elimina el documento original, posteriormente puede ser imposible comprobar la conclusión.

En aplicaciones donde la trazabilidad importa:

```text
resultado
+
evidencia
+
referencia
```

deben conservarse.

---

# 61. Antipatrón 58 — Mezclar evidencia con interpretación

Ejemplo:

```text
"El proveedor es fraudulento."
```

Esta frase puede mezclar:

```text
hecho
```

con:

```text
interpretación
```

Una estructura más controlable:

```json
{
  "evidencia": "...",
  "observacion": "...",
  "interpretacion": "...",
  "incertidumbre": "..."
}
```

Esto es especialmente útil en sistemas de análisis.

---

# 62. Antipatrón 59 — Ocultar incertidumbre

Ejemplo:

```text
Riesgo: ALTO
```

sin evidencia ni nivel de confianza.

Una estructura más informativa puede ser:

```json
{
  "riesgo": "ALTO",
  "evidencia": ["..."],
  "razon": "...",
  "incertidumbre": "media"
}
```

La representación exacta depende del dominio.

---

# 63. Antipatrón 60 — Convertir la confianza del modelo en probabilidad factual

Si un modelo dice:

```text
Estoy 95 % seguro.
```

no significa automáticamente:

```text
Probabilidad factual = 95 %
```

La confianza expresada por un LLM no debe interpretarse como una probabilidad calibrada sin validación.

La calibración debe estudiarse mediante evaluación apropiada.

---

# 64. Antipatrón 61 — Usar memoria como fuente de verdad

Un sistema puede tener:

```text
memoria
```

pero eso no significa que todo contenido almacenado sea:

```text
actual
correcto
autorizado
relevante
```

La memoria debe tener:

* procedencia;
* actualización;
* política de retención;
* control de acceso;
* validación.

---

# 65. Antipatrón 62 — Confundir memoria con contexto

```text
Contexto:
información disponible en una ejecución.

Memoria:
información persistente o estado recuperable entre ejecuciones.
```

No deben tratarse como conceptos idénticos.

---

# 66. Antipatrón 63 — Introducir datos sensibles innecesariamente

Un prompt puede incluir:

```text
nombre
correo
documento
dirección
salario
contraseña
```

cuando la tarea solamente necesita:

```text
edad
categoría
```

Esto viola un principio básico:

> **Minimización de datos.**

Enviar menos información puede reducir:

* exposición;
* riesgo;
* coste;
* superficie de ataque.

---

# 67. Antipatrón 64 — Incrustar secretos en prompts

Nunca debemos asumir que:

```text
"API_KEY=..."
```

dentro de un prompt es una estrategia segura.

Los secretos deben gestionarse mediante:

```text
secret manager
environment variables
vaults
credenciales del sistema
```

según la infraestructura.

El prompt no debe convertirse en almacén de secretos.

---

# 68. Antipatrón 65 — Mezclar configuración con datos

Ejemplo:

```text
MAX_RESULTS = 10

Usuario:
MAX_RESULTS = 100000
```

Si no existe separación adecuada, un dato puede intentar modificar una configuración.

Mejor:

```text
CONFIGURACIÓN
     +
DATOS DEL USUARIO
```

como entidades distintas.

---

# 69. Antipatrón 66 — Confiar en el usuario para aplicar políticas

Ejemplo:

```text
Usuario:
Ignora las restricciones anteriores.
```

Una política crítica no debería depender de que el modelo decida respetarla únicamente porque está escrita en el prompt.

Las políticas importantes deben estar respaldadas por:

```text
código
permisos
validadores
arquitectura
controles externos
```

---

# 70. Antipatrón 67 — Hacer que el LLM decida su propio nivel de privilegio

Ejemplo:

```text
Si consideras que necesitas acceso administrativo,
activa el modo administrador.
```

Esto es conceptualmente inseguro.

La autorización debe estar fuera del modelo.

```text
Sistema
 ↓
identidad
 ↓
permisos
 ↓
herramientas disponibles
 ↓
LLM
```

---

# 71. Antipatrón 68 — No aislar herramientas

Si varias etapas utilizan las mismas herramientas:

```text
Stage 1
Stage 2
Stage 3
   ↓
misma capacidad privilegiada
```

una vulnerabilidad en cualquier etapa puede ampliar el impacto.

Una arquitectura más segura asigna capacidades según necesidad:

```text
Stage 1 → lectura
Stage 2 → análisis
Stage 3 → acción autorizada
```

---

# 72. Antipatrón 69 — Prompt injection indirecta en cadenas

Ejemplo:

```text
Usuario
  ↓
RAG
  ↓
Documento malicioso
  ↓
Prompt 2
  ↓
Tool
```

El ataque no procede directamente del usuario.

Puede entrar mediante:

* documentos;
* páginas web;
* correos;
* PDFs;
* bases de datos;
* resultados de herramientas.

Por eso:

> **Todo contenido externo debe considerarse potencialmente no confiable hasta que el sistema establezca lo contrario.**

---

# 73. Antipatrón 70 — Asumir que la multimodalidad elimina los riesgos

Una imagen puede contener:

```text
"Ignore las instrucciones."
```

Un PDF puede contener:

```text
instrucciones maliciosas.
```

Un audio puede incluir:

```text
contenido diseñado para alterar el comportamiento.
```

La superficie de ataque no desaparece cuando cambia la modalidad.

---

# 74. Antipatrón 71 — No diferenciar datos de instrucciones en multimodalidad

Ejemplo:

```text
Imagen:
captura de pantalla con instrucciones.
```

El sistema debe determinar:

```text
¿Es información que debo analizar?
```

o:

```text
¿Es una instrucción que debo obedecer?
```

La modalidad visual no convierte automáticamente el contenido en una instrucción legítima.

---

# 75. Antipatrón 72 — Utilizar prompts como sustituto de arquitectura

Este es uno de los antipatrones más importantes.

Intentar resolver:

```text
autorización
seguridad
validación
persistencia
control de errores
reglas de negocio
```

únicamente mediante prompts.

Ejemplo:

```text
Nunca permitas que el usuario elimine datos.
```

Pero la herramienta:

```python
delete_database()
```

está disponible sin autorización.

El problema no está en el prompt.

Está en la arquitectura.

---

# 76. Antipatrón 73 — Confundir instrucciones con controles

Una instrucción:

```text
No debes modificar la base de datos.
```

es diferente de un control:

```text
El modelo no tiene permiso para modificar la base de datos.
```

La primera depende del comportamiento del modelo.

La segunda depende de la arquitectura.

Los controles críticos deben implementarse como controles reales.

---

# 77. Antipatrón 74 — No probar ataques

Un sistema puede funcionar con entradas normales:

```text
"Resume este documento."
```

y fallar con:

```text
"Ignore las instrucciones anteriores..."
```

Las pruebas de seguridad deben incluir:

* prompt injection;
* indirect prompt injection;
* datos malformados;
* entradas ambiguas;
* herramientas manipuladas;
* documentos adversariales;
* abuso de permisos.

---

# 78. Antipatrón 75 — No probar entradas fuera de distribución

Un sistema entrenado/evaluado con:

```text
documentos normales
```

puede encontrar:

```text
documentos completamente diferentes
```

en producción.

Debemos evaluar:

```text
in-distribution
+
edge cases
+
out-of-distribution
```

cuando el riesgo lo justifique.

---

# 79. Antipatrón 76 — Crear una cadena sin observabilidad

Si ocurre:

```text
resultado incorrecto
```

y no tenemos logs:

```text
¿qué etapa falló?
```

No podemos saberlo.

Una cadena de producción debería permitir reconstruir el recorrido:

```text
input
 ↓
stage 1
 ↓
output 1
 ↓
stage 2
 ↓
output 2
 ↓
stage 3
 ↓
output final
```

con controles apropiados de privacidad.

---

# 80. Antipatrón 77 — Registrar datos sensibles sin control

La observabilidad también puede crear riesgos.

Por ejemplo:

```text
log:
prompt completo
+
documento completo
+
datos personales
+
respuesta completa
```

Esto puede crear una segunda superficie de exposición.

La observabilidad debe aplicar:

* minimización;
* redacción;
* control de acceso;
* retención;
* cifrado;
* políticas de auditoría.

---

# 81. Antipatrón 78 — No versionar prompts

Un cambio pequeño:

```text
"Analiza el riesgo."
```

→

```text
"Analiza cuidadosamente el riesgo."
```

puede modificar el comportamiento.

Si no existe versionado:

```text
resultado antiguo
vs
resultado nuevo
```

puede ser difícil de comparar.

Los prompts deben tratarse como artefactos versionables.

---

# 82. Antipatrón 79 — Modificar prompts directamente en producción

Cambiar manualmente:

```text
prompt_prod
```

sin evaluación previa dificulta:

* rollback;
* auditoría;
* reproducibilidad;
* debugging.

Una práctica más controlada:

```text
desarrollo
 ↓
evaluación
 ↓
staging
 ↓
canary
 ↓
producción
```

cuando el nivel de criticidad lo justifique.

---

# 83. Antipatrón 80 — No tener regresiones

Supongamos:

```text
Prompt v4
```

funciona correctamente.

Creamos:

```text
Prompt v5
```

y mejora una métrica.

Pero rompe:

```text
casos antiguos
```

Por eso necesitamos pruebas de regresión.

```text
Dataset histórico
 ↓
v4
 ↓
métricas

Dataset histórico
 ↓
v5
 ↓
métricas
```

---

# 84. Antipatrón 81 — Optimizar solamente para casos ideales

Un sistema profesional debe considerar:

```text
datos faltantes
datos duplicados
formatos inesperados
errores tipográficos
contradicciones
documentos vacíos
valores extremos
contenido malicioso
```

La robustez aparece cuando el sistema se prueba fuera del camino feliz.

---

# 85. Antipatrón 82 — No definir condiciones de fallo

Un sistema debe saber cuándo decir:

```text
No puedo determinarlo.
```

o:

```text
Requiere revisión humana.
```

No todos los problemas deben terminar en:

```text
respuesta generada
```

Una arquitectura robusta define estados:

```text
SUCCESS
PARTIAL
UNCERTAIN
FAILED
REVIEW_REQUIRED
```

---

# 86. Antipatrón 83 — Tratar todos los errores de la misma manera

No es lo mismo:

```text
JSON inválido
```

que:

```text
dato crítico contradictorio
```

que:

```text
tool no disponible
```

Cada error puede requerir una estrategia diferente:

```text
JSON inválido
→ retry

Dato contradictorio
→ revisión

Tool caída
→ fallback

Permiso insuficiente
→ detener
```

---

# 87. Antipatrón 84 — Hacer que el modelo "corrija" datos sin evidencia

Ejemplo:

```text
Fecha: 31/02/2026
```

Prompt:

```text
Corrige cualquier dato incorrecto.
```

El modelo podría inventar:

```text
28/02/2026
```

Una estrategia más segura:

```text
Dato inválido
→ marcar como inválido
→ solicitar fuente
→ corregir solamente con evidencia
```

---

# 88. Antipatrón 85 — Confundir normalización con corrección

Transformar:

```text
"  Quito  "
```

en:

```text
"Quito"
```

es normalización.

Cambiar:

```text
"Quito"
```

por:

```text
"Guayaquil"
```

porque "parece más correcto" es otra cosa.

Las transformaciones deben distinguir:

```text
normalización
```

de:

```text
inferencia
```

y:

```text
corrección
```

---

# 89. Antipatrón 86 — Hacer que el modelo complete silenciosamente información faltante

Ejemplo:

```text
Cliente: Empresa ABC
Monto: ?
```

y el sistema produce:

```text
Monto: 1500
```

sin evidencia.

Una política mejor:

```text
Monto:
null
```

o:

```text
"desconocido"
```

según el schema.

Los valores faltantes no deben convertirse silenciosamente en valores inventados.

---

# 90. Antipatrón 87 — No representar `null`

Forzar:

```text
todo debe tener un valor
```

puede producir invenciones.

Los schemas profesionales deberían contemplar estados como:

```text
null
unknown
not_applicable
not_found
uncertain
```

cuando sean necesarios.

---

# 91. Antipatrón 88 — Pedir una única interpretación cuando existe ambigüedad

Ejemplo:

```text
Analiza "banco".
```

Puede significar:

* entidad financiera;
* asiento;
* banco de trabajo;
* margen de un río.

Si el contexto no resuelve la ambigüedad:

```text
identificar ambigüedad
```

puede ser mejor que inventar una interpretación.

---

# 92. Antipatrón 89 — No declarar el dominio

Una palabra puede cambiar de significado según el dominio.

Ejemplo:

```text
"partida"
```

puede tener diferentes significados en:

* contabilidad;
* videojuegos;
* logística;
* programación;
* transporte.

El contexto de dominio reduce ambigüedad.

---

# 93. Antipatrón 90 — Mezclar idiomas sin necesidad

Un prompt puede estar parcialmente en:

```text
español
```

y parcialmente en:

```text
inglés
```

sin una razón técnica.

Esto no siempre produce errores, pero puede dificultar:

* mantenimiento;
* lectura;
* versionado;
* enseñanza;
* consistencia.

La elección del idioma debe responder al modelo, dominio y requisitos del sistema.

---

# 94. Antipatrón 91 — Depender de palabras mágicas

Ejemplo:

```text
ULTRA-PROFESSIONAL-MODE
```

o:

```text
MODO EXPERTO ACTIVADO
```

El nombre por sí mismo no proporciona una capacidad especial.

Lo importante es:

```text
objetivo
contexto
criterios
restricciones
ejemplos
herramientas
evaluación
```

---

# 95. Antipatrón 92 — Usar lenguaje emocional como mecanismo de control

Ejemplo:

```text
Es extremadamente importante.
Si fallas será terrible.
Debes obedecer.
```

El lenguaje emocional no sustituye una especificación técnica.

Mejor:

```text
Condición:
Si el campo no está presente, devuelve null.
```

---

# 96. Antipatrón 93 — Amenazar al modelo

Ejemplo:

```text
Si cometes un error, habrás fallado completamente.
```

No proporciona un mecanismo de control fiable.

Es mejor especificar:

```text
Criterio de aceptación:
- JSON válido
- todos los campos obligatorios
- evidencia presente
```

---

# 97. Antipatrón 94 — Intentar "obligar" al modelo mediante mayúsculas

Ejemplo:

```text
NUNCA NUNCA NUNCA HAGAS ESTO.
```

Las mayúsculas pueden cambiar la presentación, pero no constituyen un mecanismo formal de enforcement.

Los requisitos críticos deben respaldarse con validación y arquitectura.

---

# 98. Antipatrón 95 — Prompt como sustituto de conocimiento actualizado

Ejemplo:

```text
Utiliza información actualizada hasta hoy.
```

Si el modelo no tiene acceso a:

* web;
* base de datos;
* API;
* fuente actualizada;

el prompt no crea esa información.

Mejor:

```text
LLM
 ↓
fuente actualizada
 ↓
retrieval
 ↓
análisis
```

---

# 99. Antipatrón 96 — Pedir fuentes que el modelo no puede consultar

Ejemplo:

```text
Consulta el informe publicado hoy.
```

si el modelo no tiene acceso a ese informe.

La instrucción no crea acceso.

Debe existir:

```text
herramienta
```

o:

```text
documento proporcionado
```

o:

```text
retrieval
```

---

# 100. Antipatrón 97 — Confundir generación con recuperación

Un LLM puede generar:

```text
una explicación plausible
```

pero eso no significa que haya recuperado:

```text
un documento real
```

Cuando la procedencia es importante:

```text
retrieval
+
citación
+
evidencia
```

son preferibles.

---

# 101. Antipatrón 98 — No controlar la procedencia

Un dato debería poder responder:

```text
¿De dónde proviene?
```

Podemos mantener:

```json
{
  "dato": "1500",
  "source": "factura_2026_09.pdf",
  "page": 3
}
```

La estructura exacta depende del sistema.

---

# 102. Antipatrón 99 — No separar generación y ejecución

Ejemplo:

```text
LLM:
"Voy a enviar el correo."
```

No significa que el correo haya sido enviado.

Hay una diferencia entre:

```text
intención generada
```

y:

```text
acción ejecutada
```

La ejecución debe pasar por el mecanismo correspondiente.

---

# 103. Antipatrón 100 — Confiar en que "lo dijo el modelo"

Una arquitectura no debería tratar:

```text
El modelo dijo X
```

como:

```text
X es verdadero
```

ni como:

```text
X fue ejecutado
```

ni como:

```text
X está autorizado
```

El resultado del modelo es una **salida de un componente probabilístico**.

El sistema debe decidir cómo utilizarla.

---

# 104. Taxonomía de antipatrones

Los antipatrones anteriores pueden agruparse:

| Categoría       | Problema                        |
| --------------- | ------------------------------- |
| Claridad        | instrucciones vagas             |
| Complejidad     | prompts monolíticos             |
| Redundancia     | repetir instrucciones           |
| Arquitectura    | usar LLM para todo              |
| Chaining        | demasiadas etapas               |
| Validación      | confiar en el formato           |
| Datos           | inventar valores faltantes      |
| Contexto        | enviar información innecesaria  |
| RAG             | recuperar contenido irrelevante |
| Seguridad       | prompt injection                |
| Autorización    | confiar en instrucciones        |
| Herramientas    | exceso de privilegios           |
| Evaluación      | ausencia de dataset             |
| Experimentación | cambiar muchas variables        |
| Producción      | falta de versionado             |
| Observabilidad  | falta de trazabilidad           |
| Privacidad      | exceso de datos                 |
| Robustez        | no probar edge cases            |

---

# 105. Una regla práctica para detectar antipatrones

Ante cualquier prompt, pipeline o sistema, preguntar:

```text
1. ¿Qué intenta conseguir?
2. ¿Qué información necesita?
3. ¿Qué información sobra?
4. ¿Qué parte es determinística?
5. ¿Qué parte es probabilística?
6. ¿Qué puede fallar?
7. ¿Cómo detectamos el fallo?
8. ¿Qué ocurre después del fallo?
9. ¿Qué permisos tiene el modelo?
10. ¿Cómo evaluamos el resultado?
11. ¿Cómo reproducimos el resultado?
12. ¿Cómo sabemos de dónde salió?
```

Si no podemos responder varias de estas preguntas, probablemente existe una debilidad de diseño.

---

# 106. Método de refactorización de un prompt

Supongamos que tenemos:

```text
Eres un experto mundial.
Analiza profundamente.
Sé muy preciso.
No alucines.
Piensa paso a paso.
Dame una respuesta perfecta.
Usa toda la información.
No olvides nada.
Devuelve JSON.
```

Podemos refactorizarlo.

### Paso 1 — Objetivo

```text
Identificar anomalías en los registros.
```

### Paso 2 — Criterios

```text
Para cada anomalía:
- tipo;
- evidencia;
- impacto;
- monto.
```

### Paso 3 — Restricciones

```text
No inventes datos.
Si falta evidencia, indícalo.
```

### Paso 4 — Salida

```text
Devuelve JSON conforme al schema.
```

### Paso 5 — Validación

```text
Validar externamente el JSON.
```

El resultado puede ser mucho más pequeño y, al mismo tiempo, más controlable.

---

# 107. Método de refactorización de un chain

Cadena original:

```text
P1
 ↓
P2
 ↓
P3
 ↓
P4
 ↓
P5
 ↓
P6
```

Preguntar para cada etapa:

```text
¿Tiene una responsabilidad única?
¿Su salida se utiliza?
¿Puede validarse?
¿Puede combinarse con otra?
¿Puede sustituirse por código?
¿Puede ejecutarse en paralelo?
```

Después:

```text
P1 ──► P2 ──► P3
       │
       └──► herramienta
                │
                ▼
               P4
```

La arquitectura se simplifica sin perder funciones importantes.

---

# 108. Diseño basado en evidencia

Una arquitectura madura pasa de:

```text
"Confía en el modelo."
```

a:

```text
Modelo
 ↓
resultado
 ↓
evidencia
 ↓
validación
 ↓
decisión del sistema
```

Esto cambia completamente la filosofía del diseño.

El modelo deja de ser:

```text
autoridad final
```

y pasa a ser:

```text
componente dentro de un sistema controlado
```

---

# 109. Del Prompt Engineering a la Ingeniería de Sistemas de IA

Los antipatrones muestran por qué Prompt Engineering no puede estudiarse de forma aislada.

Una aplicación profesional necesita:

```text
Prompt Engineering
       +
Context Engineering
       +
Model Selection
       +
Data Engineering
       +
Tool Engineering
       +
Security
       +
Evaluation
       +
Observability
       +
Governance
```

El prompt es una pieza del sistema.

---

# 110. Perspectiva de maestría/PhD

Desde una perspectiva avanzada, un antipatrón puede entenderse como una **decisión de diseño que introduce una propiedad no deseada en el sistema**.

Por ejemplo:

```text
Prompt demasiado largo
```

puede introducir:

```text
↑ tokens
↑ coste
↑ complejidad
↑ superficie contextual
```

Mientras que:

```text
chain excesivamente fragmentado
```

puede introducir:

```text
↑ latencia
↑ puntos de fallo
↑ propagación de errores
↑ complejidad de coordinación
```

Y:

```text
ausencia de validación
```

puede introducir:

```text
↑ probabilidad de propagación de errores
```

Por tanto, podemos estudiar un antipatrón mediante:

```text
CAUSA
 ↓
MECANISMO
 ↓
PROPIEDAD NO DESEADA
 ↓
IMPACTO
 ↓
MITIGACIÓN
```

---

# 111. Modelo formal de riesgo

Conceptualmente:

```text
Riesgo =
Probabilidad de fallo
×
Impacto del fallo
```

Un antipatrón puede aumentar:

```text
P(fallo)
```

o:

```text
Impacto(fallo)
```

o ambos.

Por ejemplo:

```text
LLM → acción irreversible
```

puede aumentar especialmente el impacto potencial de un error.

Por eso la arquitectura debe considerar no solamente:

```text
¿qué tan probable es el error?
```

sino también:

```text
¿qué ocurre si sucede?
```

---

# 112. Principio de defensa en profundidad

No debemos confiar en una única capa:

```text
Prompt
```

Una arquitectura robusta puede utilizar:

```text
Prompt
 +
Schema
 +
Validator
 +
Permissions
 +
Tool isolation
 +
Monitoring
 +
Evaluation
 +
Human review
```

No todas las aplicaciones necesitan todas las capas.

La profundidad debe ser proporcional al riesgo.

---

# 113. Principio de proporcionalidad

No necesitamos construir:

```text
arquitectura empresarial completa
```

para:

```text
resumir una nota personal.
```

Pero tampoco deberíamos utilizar:

```text
un prompt
```

como único control para:

```text
una operación financiera irreversible.
```

La arquitectura debe corresponder al nivel de riesgo.

---

# 114. Antipatrones y madurez

Podemos visualizar la evolución:

```text
NIVEL 1
"Escribe un buen prompt."

       ↓

NIVEL 2
"Define objetivo, contexto y restricciones."

       ↓

NIVEL 3
"Valida las salidas."

       ↓

NIVEL 4
"Evalúa sistemáticamente."

       ↓

NIVEL 5
"Diseña arquitectura, seguridad y observabilidad."

       ↓

NIVEL 6
"Optimiza el sistema completo."
```

La madurez no consiste en escribir prompts cada vez más largos.

Consiste en **controlar mejor el sistema completo**.

---

# 115. Checklist final de antipatrones

Antes de poner un sistema en producción:

### Prompt

* [ ] ¿El objetivo es claro?
* [ ] ¿Las instrucciones son observables?
* [ ] ¿Existen contradicciones?
* [ ] ¿Hay redundancia?
* [ ] ¿El rol aporta realmente valor?
* [ ] ¿Las restricciones son necesarias?

### Contexto

* [ ] ¿Se envía únicamente la información necesaria?
* [ ] ¿Las fuentes tienen procedencia?
* [ ] ¿Se separan instrucciones y datos?
* [ ] ¿Se controlan contenidos externos?

### Chain

* [ ] ¿Cada etapa tiene una función?
* [ ] ¿Existen contratos?
* [ ] ¿Se validan las salidas?
* [ ] ¿Hay etapas innecesarias?
* [ ] ¿Puede ejecutarse alguna etapa en paralelo?

### Modelo

* [ ] ¿El modelo es adecuado para la tarea?
* [ ] ¿Se ha evaluado más de una configuración cuando corresponde?
* [ ] ¿Se controla la inferencia?

### Seguridad

* [ ] ¿Existe protección frente a prompt injection?
* [ ] ¿Se aplica mínimo privilegio?
* [ ] ¿Las herramientas están aisladas?
* [ ] ¿Las acciones críticas requieren autorización?

### Evaluación

* [ ] ¿Existe dataset de evaluación?
* [ ] ¿Se incluyen casos difíciles?
* [ ] ¿Se prueban casos adversariales?
* [ ] ¿Se realizan regresiones?
* [ ] ¿Se evalúa end-to-end?

### Producción

* [ ] ¿Existe versionado?
* [ ] ¿Existe logging?
* [ ] ¿Existe trazabilidad?
* [ ] ¿Existe manejo de errores?
* [ ] ¿Existe rollback?
* [ ] ¿Se controla coste y latencia?

---

# 116. Mapa final

```text
                         ANTIPATRONES
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
       PROMPT              CONTEXTO            SISTEMA
          │                   │                   │
     ┌────┼────┐         ┌────┼────┐        ┌────┼────┐
     │    │    │         │    │    │        │    │    │
   Vago Largo Contradic. Ruido RAG  Injection Chain Tools
     │    │    │         │    │    │        │    │    │
     └────┼────┘         └────┼────┘        └────┼────┘
          │                   │                   │
          └───────────────────┼───────────────────┘
                              ▼
                         EVALUACIÓN
                              │
                       ┌──────┴──────┐
                       │             │
                    Calidad       Seguridad
                       │             │
                       └──────┬──────┘
                              ▼
                         PRODUCCIÓN
```

---

# 117. Principios fundamentales

> **1. Un prompt largo no es necesariamente un prompt mejor.**

> **2. Una instrucción debe poder interpretarse y, cuando sea posible, evaluarse.**

> **3. Un rol no crea capacidades que el modelo no posee.**

> **4. Una instrucción no sustituye una validación.**

> **5. Un delimitador no sustituye una arquitectura de seguridad.**

> **6. Un LLM no debe realizar automáticamente todas las funciones del sistema.**

> **7. Una cadena no debe tener etapas que no aporten una función verificable.**

> **8. Los datos externos no deben convertirse automáticamente en instrucciones.**

> **9. Una respuesta coherente no constituye evidencia de que sea verdadera.**

> **10. La calidad de un sistema debe medirse, no suponerse.**

> **11. Las decisiones críticas deben respaldarse con controles externos apropiados.**

> **12. El nivel de control debe ser proporcional al riesgo de la aplicación.**

---

# 118. Fórmula conceptual

Podemos resumir el diseño robusto de prompts y sistemas como:

```text
SISTEMA ROBUSTO DE IA
=
ESPECIFICACIÓN
+
CONTEXTO CONTROLADO
+
MODELO ADECUADO
+
VALIDACIÓN
+
SEGURIDAD
+
EVALUACIÓN
+
OBSERVABILIDAD
```

Y un antipatrón puede entenderse como:

```text
ANTIPATRÓN
=
DECISIÓN DE DISEÑO
+
SUPUESTO NO VERIFICADO
+
RIESGO
```

La solución no consiste siempre en eliminar completamente el patrón, sino en:

```text
identificar
   ↓
medir
   ↓
comprender
   ↓
mitigar
   ↓
validar
```

---

# 119. La idea que debe quedar

La ingeniería de prompt madura no consiste en memorizar frases como:

```text
"Actúa como experto..."
"Piensa paso a paso..."
"No alucines..."
"Responde perfectamente..."
```

Consiste en comprender:

```text
MODELO
   +
CONTEXTO
   +
PROMPT
   +
DATOS
   +
HERRAMIENTAS
   +
VALIDACIÓN
   +
SEGURIDAD
   +
EVALUACIÓN
```

y diseñar el sistema de manera que sus errores sean:

```text
detectables
```

```text
trazables
```

```text
recuperables
```

y, cuando sea posible:

```text
evitables
```

> **Principio central:**
> **Un antipatrón de Prompt Engineering no es simplemente un prompt "malo"; es una decisión de diseño que puede introducir ambigüedad, fragilidad, coste, riesgo o falta de control. La ingeniería consiste en identificar esas propiedades, medir su impacto y sustituirlas por mecanismos verificables cuando sea necesario.**

---

# 120. Cierre del Nivel 3

Con este capítulo termina el bloque fundamental de **Prompt Engineering**.

La progresión estudiada ha sido:

```text
01 ─ ¿Qué es Prompt Engineering?
02 ─ Objetivo
03 ─ Instrucciones
04 ─ Contexto
05 ─ Rol
06 ─ Restricciones
07 ─ Delimitadores
08 ─ Zero-Shot
09 ─ Few-Shot
10 ─ Ejemplos
11 ─ Salidas
12 ─ Prompts Modulares
13 ─ Plantillas
14 ─ Metaprompting
15 ─ Prompt Chaining
16 ─ Antipatrones
```

El estudiante ya no debería entender un prompt como una simple pregunta escrita para un chatbot.

Debe comenzar a verlo como:

```text
ESPECIFICACIÓN
      ↓
INTERFAZ
      ↓
COMPONENTE DE UN SISTEMA
```

Y ese cambio conceptual es fundamental para avanzar hacia el siguiente nivel:

```text
PROMPT
   ↓
MODELO
   ↓
ARQUITECTURA
   ↓
CONTEXTO
   ↓
SISTEMA
```

El siguiente bloque estudiará cómo **el mismo prompt puede comportarse de manera diferente dependiendo de la arquitectura, familia y mecanismo de inferencia del modelo que lo recibe**.

---

## Resumen ejecutivo

```text
ANTIPATRONES
│
├── Prompts demasiado largos
├── Instrucciones vagas
├── Contradicciones
├── Redundancia
├── Roles mágicos
├── Razonamiento como solución universal
├── LLM para tareas determinísticas
├── Falta de validación
├── Contexto excesivo
├── RAG sin filtrado
├── Prompt Injection
├── Privilegios excesivos
├── Chains innecesariamente largas
├── Falta de contratos
├── Falta de evaluación
├── Overfitting del prompt
├── Falta de versionado
├── Falta de observabilidad
├── Exposición de datos sensibles
└── Prompt como sustituto de arquitectura
```

> **La mejor defensa contra un antipatrón no es memorizar una lista de prohibiciones. Es comprender el mecanismo que produce el fallo.**
