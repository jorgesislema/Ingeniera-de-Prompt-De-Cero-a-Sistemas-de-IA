# 02 — Objetivo

> **Antes de diseñar un prompt debemos definir qué queremos conseguir. Un modelo de IA no puede optimizar correctamente una tarea cuyo resultado esperado no está suficientemente definido.**

---

# 1. El objetivo es el punto de partida

En Ingeniería de Prompt existe una tendencia a comenzar directamente escribiendo:

```text
"Actúa como un experto..."
```

o:

```text
"Analiza el siguiente documento..."
```

Pero existe una pregunta anterior:

> **¿Qué queremos conseguir exactamente?**

El prompt es un medio.

El objetivo es el fin.

Podemos representarlo así:

```text
OBJETIVO
   │
   ▼
¿QUÉ NECESITO CONSEGUIR?
   │
   ▼
¿QUÉ INFORMACIÓN NECESITO?
   │
   ▼
¿QUÉ DEBE HACER EL MODELO?
   │
   ▼
¿CÓMO DEBE RESPONDER?
   │
   ▼
¿CÓMO COMPROBARÉ EL RESULTADO?
```

Por eso:

```text
Objetivo
   ↓
Diseño del prompt
   ↓
Ejecución
   ↓
Evaluación
```

No debería invertirse el proceso:

```text
Prompt
   ↓
Respuesta
   ↓
"Ahora veo qué quería conseguir"
```

---

# 2. ¿Qué es un objetivo?

Un **objetivo** es una descripción del resultado que queremos alcanzar mediante una tarea.

Por ejemplo:

```text
"Quiero resumir este documento."
```

es un objetivo inicial.

Pero todavía es demasiado general.

Podemos convertirlo en:

```text
"Obtener un resumen de máximo 150 palabras
que conserve las conclusiones, cifras y fechas
importantes del documento."
```

Ahora tenemos un objetivo más preciso.

La diferencia:

```text
OBJETIVO VAGO

"Resume esto."


OBJETIVO ESPECÍFICO

"Genera un resumen de máximo 150 palabras,
conservando las conclusiones principales,
las cifras relevantes y las fechas importantes."
```

El segundo objetivo permite evaluar el resultado.

---

# 3. Objetivo ≠ Prompt

Estos conceptos deben mantenerse separados.

### Objetivo

Define **qué queremos conseguir**.

### Prompt

Define **cómo comunicamos al modelo la tarea y la información necesaria para intentarla**.

Por ejemplo:

```text
OBJETIVO:

Identificar anomalías en un conjunto de
movimientos contables.
```

Podemos construir diferentes prompts para conseguirlo.

```text
OBJETIVO
   │
   ├──► Prompt A
   │
   ├──► Prompt B
   │
   └──► Prompt C
```

El objetivo permanece relativamente estable mientras experimentamos con diferentes diseños.

Esto permite comparar prompts.

---

# 4. El objetivo debe describir un resultado

Un error común es describir solamente una actividad.

Por ejemplo:

```text
"Analiza este documento."
```

"Analizar" describe una actividad, pero no especifica qué resultado queremos.

Podríamos preguntarnos:

```text
¿Analizar qué?

¿Errores?
¿Riesgos?
¿Resumen?
¿Contradicciones?
¿Datos?
¿Tendencias?
¿Cumplimiento?
```

Un objetivo más útil sería:

```text
"Identificar inconsistencias numéricas y
duplicados dentro del documento."
```

Ahora podemos determinar si el sistema consiguió el objetivo.

---

# 5. Objetivo, tarea y resultado esperado

Conviene separar tres conceptos:

```text
OBJETIVO
   ↓
¿Qué quiero conseguir?

TAREA
   ↓
¿Qué debe hacer el modelo?

RESULTADO ESPERADO
   ↓
¿Qué debe producir?
```

### Ejemplo

**Objetivo:**

```text
Detectar posibles anomalías en transacciones.
```

**Tarea:**

```text
Analizar cada transacción y buscar
patrones anómalos.
```

**Resultado esperado:**

```text
Una lista de transacciones sospechosas
con evidencia y explicación.
```

Esquema:

```text
┌──────────────┐
│   OBJETIVO   │
│ detectar     │
│ anomalías    │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│    TAREA     │
│ analizar     │
│ transacciones│
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   RESULTADO  │
│ anomalías +  │
│ evidencia    │
└──────────────┘
```

---

# 6. La pregunta fundamental

Antes de escribir un prompt, utiliza esta pregunta:

> **¿Qué tendría que producir el sistema para considerar que mi objetivo fue alcanzado?**

Esta pregunta convierte un objetivo abstracto en algo evaluable.

Por ejemplo:

### Objetivo

```text
"Mejorar un texto."
```

Pregunta:

```text
¿Qué significa mejorar?
```

Podría significar:

* corregir ortografía;
* mejorar claridad;
* hacerlo más formal;
* reducir longitud;
* eliminar redundancias;
* adaptarlo a un público determinado.

Un objetivo mejor definido sería:

```text
"Corregir errores ortográficos y gramaticales
sin modificar el significado original."
```

Ahora tenemos un criterio verificable.

---

# 7. Objetivos vagos

Los objetivos vagos utilizan palabras que requieren interpretación.

Algunos ejemplos:

```text
"Hazlo bien."

"Mejora este texto."

"Analiza profundamente."

"Explícalo correctamente."

"Genera algo profesional."

"Busca información relevante."

"Dame una respuesta completa."
```

El problema no es que estas frases sean incorrectas.

El problema es que no especifican suficientemente:

```text
¿qué?
¿para quién?
¿con qué información?
¿bajo qué restricciones?
¿qué formato?
¿qué criterios?
¿qué significa éxito?
```

---

# 8. Convertir un objetivo vago en uno operativo

Podemos utilizar una transformación:

```text
OBJETIVO VAGO
     ↓
VERBO
     ↓
OBJETO
     ↓
CONDICIONES
     ↓
RESTRICCIONES
     ↓
RESULTADO
     ↓
CRITERIO DE ÉXITO
```

### Ejemplo

Objetivo inicial:

```text
"Analiza las ventas."
```

### Paso 1 — Verbo

```text
Analizar
```

### Paso 2 — Objeto

```text
Analizar las ventas mensuales.
```

### Paso 3 — Condición

```text
Analizar las ventas mensuales
del año 2026.
```

### Paso 4 — Propósito

```text
Analizar las ventas mensuales de 2026
para identificar tendencias.
```

### Paso 5 — Resultado

```text
Analizar las ventas mensuales de 2026
para identificar tendencias y meses
con cambios significativos.
```

### Paso 6 — Criterio

```text
Presentar las tres tendencias principales
y señalar los meses que presenten una
variación superior al 20 %.
```

Ahora tenemos un objetivo operacional.

---

# 9. Una estructura práctica para definir objetivos

Podemos utilizar esta plantilla conceptual:

```text
OBJETIVO =
VERBO
+
OBJETO
+
PROPÓSITO
+
CONDICIONES
+
RESULTADO
```

Por ejemplo:

```text
Identificar
+
transacciones duplicadas
+
para detectar posibles errores contables
+
considerando fecha, cuenta y monto
+
y devolver cada duplicado con su evidencia.
```

Resultado:

> **Identificar transacciones duplicadas para detectar posibles errores contables, comparando fecha, cuenta y monto, y devolver cada coincidencia junto con la evidencia utilizada.**

---

# 10. Los verbos importan

El verbo utilizado en el objetivo ayuda a determinar qué debe hacer el modelo.

Comparemos:

| Verbo       | Posible operación                           |
| ----------- | ------------------------------------------- |
| Resumir     | Reducir información conservando lo esencial |
| Clasificar  | Asignar categorías                          |
| Extraer     | Obtener información existente               |
| Comparar    | Identificar similitudes y diferencias       |
| Transformar | Modificar una representación                |
| Generar     | Crear contenido                             |
| Explicar    | Presentar información de forma comprensible |
| Detectar    | Identificar patrones o condiciones          |
| Evaluar     | Aplicar criterios                           |
| Validar     | Comprobar condiciones                       |
| Traducir    | Cambiar de idioma                           |
| Estructurar | Organizar información                       |
| Sintetizar  | Integrar información relevante              |
| Priorizar   | Ordenar según criterios                     |
| Corregir    | Identificar y modificar errores             |

No todos estos verbos implican la misma tarea.

Por ejemplo:

```text
EXTRAER
```

significa principalmente recuperar información.

Mientras:

```text
GENERAR
```

implica producir contenido nuevo.

Y:

```text
EVALUAR
```

requiere criterios contra los cuales comparar.

---

# 11. Objetivos de extracción y generación

Una distinción particularmente importante es:

```text
EXTRACCIÓN
```

frente a:

```text
GENERACIÓN
```

### Extracción

Queremos recuperar información que ya existe.

```text
Documento
   ↓
Modelo
   ↓
Nombre
Fecha
Monto
Número de factura
```

### Generación

Queremos producir información nueva.

```text
Tema
 +
Instrucciones
 ↓
Modelo
 ↓
Texto generado
```

### Transformación

Existe una tercera categoría:

```text
Texto original
      ↓
     IA
      ↓
Texto transformado
```

Por ejemplo:

* traducir;
* resumir;
* corregir;
* convertir a JSON;
* convertir una tabla a texto.

Comprender esta diferencia ayuda a diseñar mejores objetivos.

---

# 12. Objetivos simples y objetivos compuestos

No todos los objetivos tienen la misma complejidad.

### Objetivo simple

```text
Traducir este texto al inglés.
```

### Objetivo compuesto

```text
Analizar un documento financiero,
identificar anomalías,
clasificarlas por nivel de riesgo,
explicar la evidencia y generar
un informe estructurado.
```

El segundo contiene varias tareas:

```text
                OBJETIVO
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    Analizar     Detectar     Clasificar
                                  │
                                  ▼
                              Explicar
                                  │
                                  ▼
                              Estructurar
```

Cuando un objetivo contiene demasiadas operaciones, puede ser conveniente dividirlo.

---

# 13. Descomposición del objetivo

Una técnica fundamental consiste en transformar:

```text
OBJETIVO GRANDE
```

en:

```text
SUBOBJETIVO 1
SUBOBJETIVO 2
SUBOBJETIVO 3
...
```

Ejemplo:

```text
OBJETIVO:

Generar un informe de auditoría.
```

Podemos descomponerlo:

```text
1. Extraer datos.
       ↓
2. Validar datos.
       ↓
3. Detectar anomalías.
       ↓
4. Clasificar riesgos.
       ↓
5. Generar conclusiones.
       ↓
6. Construir informe.
```

Esta descomposición será fundamental cuando estudiemos:

* Prompt Chaining;
* workflows;
* agentes;
* automatización.

---

# 14. Objetivo y criterios de éxito

Un objetivo profesional debe estar acompañado, cuando sea posible, de criterios de éxito.

Por ejemplo:

```text
OBJETIVO:

Extraer información de facturas.
```

Criterios:

```text
- número de factura correcto;
- fecha correcta;
- proveedor correcto;
- total correcto;
- salida JSON válida.
```

Podemos representarlo:

```text
                 OBJETIVO
                    │
                    ▼
           EXTRAER INFORMACIÓN
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    Exactitud     Cobertura    Formato
       │            │            │
       ▼            ▼            ▼
     Datos        Campos       JSON
    correctos    completos     válido
```

El objetivo define **qué queremos**.

Los criterios definen **cómo sabremos si lo conseguimos**.

---

# 15. Objetivo y métricas

En sistemas profesionales podemos convertir criterios en métricas.

Supongamos una tarea de clasificación.

Tenemos:

```text
100 documentos
```

El modelo clasifica correctamente:

```text
92
```

Podemos calcular:

```text
Exactitud = 92 / 100 = 92 %
```

Aquí aparece una diferencia importante:

```text
PROMPT
  ↓
COMPORTAMIENTO
  ↓
RESULTADOS
  ↓
MÉTRICAS
```

El Prompt Engineering profesional no debería limitarse a observar ejemplos individuales.

Cuando el sistema lo requiere, debemos evaluar su comportamiento sobre conjuntos de prueba.

---

# 16. El objetivo determina el tipo de evaluación

No todas las tareas deben evaluarse de la misma manera.

### Clasificación

Podemos utilizar:

* exactitud;
* precisión;
* recall;
* F1.

### Extracción

Podemos evaluar:

* exactitud de campos;
* cobertura;
* coincidencia con datos de referencia.

### Generación de texto

Podemos evaluar:

* cumplimiento de instrucciones;
* factualidad;
* relevancia;
* estilo;
* criterios definidos por expertos.

### Código

Podemos evaluar:

```text
¿Compila?
¿Ejecuta?
¿Pasa las pruebas?
¿Produce el resultado esperado?
```

Por tanto:

> **El objetivo determina en gran medida cómo debemos evaluar el resultado.**

---

# 17. Objetivos SMART: utilidad y limitaciones

En gestión de proyectos suele utilizarse el marco SMART:

```text
S — Specific
M — Measurable
A — Achievable
R — Relevant
T — Time-bound
```

Puede ser útil para formular objetivos, pero no debe convertirse en una regla rígida para todos los prompts.

Por ejemplo:

```text
"Clasificar correctamente al menos el 95 %
de un conjunto de 1.000 documentos."
```

es medible.

Sin embargo, en una tarea creativa:

```text
"Generar cinco conceptos originales para
una campaña visual."
```

la evaluación puede requerir criterios cualitativos.

Por ello:

> **Los objetivos deben adaptarse a la naturaleza de la tarea.**

---

# 18. Objetivos deterministas y objetivos abiertos

Podemos distinguir entre tareas con resultados muy definidos y tareas con múltiples respuestas válidas.

### Determinista

```text
¿Cuánto es 25 × 40?
```

Resultado esperado:

```text
1000
```

### Abierto

```text
Propón tres ideas para enseñar
inteligencia artificial a estudiantes.
```

Puede haber muchas respuestas válidas.

Podemos representarlo:

```text
DETERMINISTA

Entrada ──► Resultado esperado


ABIERTO

Entrada ──► Resultado A
         ├─► Resultado B
         ├─► Resultado C
         └─► Resultado D
```

Esto afecta directamente al diseño de evaluación.

---

# 19. Objetivos y tolerancia al error

No todos los errores tienen el mismo costo.

Supongamos:

```text
Tarea A:
Generar nombres para una mascota.

Tarea B:
Extraer una dosis de un documento médico.

Tarea C:
Clasificar una transacción financiera.
```

Un error en la tarea A puede ser trivial.

Un error en B o C puede tener consecuencias importantes.

Por ello el objetivo debe considerar:

```text
OBJETIVO
   +
IMPACTO DEL ERROR
   ↓
NIVEL DE CONTROL NECESARIO
```

En tareas críticas puede ser necesario incorporar:

* validación;
* reglas deterministas;
* fuentes externas;
* revisión humana;
* herramientas;
* límites de acción.

---

# 20. Objetivo y riesgo

Una formulación útil es:

```text
RIESGO DEL SISTEMA
        ↓
¿Qué puede salir mal?
        ↓
¿Qué consecuencias tendría?
        ↓
¿Qué controles necesitamos?
```

Por ejemplo:

```text
Objetivo:
Detectar transacciones sospechosas.
```

No significa:

```text
"El modelo debe decidir automáticamente
qué transacciones son fraudulentas."
```

Podría diseñarse:

```text
Datos
 ↓
Modelo
 ↓
Posibles anomalías
 ↓
Evidencia
 ↓
Revisión
 ↓
Decisión humana
```

El objetivo determina el papel que debe tener el modelo.

---

# 21. Objetivo y autoridad del modelo

Una pregunta avanzada es:

> **¿Qué está autorizado a hacer el sistema para alcanzar el objetivo?**

No es lo mismo:

```text
"Recomienda qué documentos revisar."
```

que:

```text
"Elimina automáticamente los documentos
que considere incorrectos."
```

El segundo implica una acción con consecuencias.

Podemos separar:

```text
INFORMAR
   ↓
SUGERIR
   ↓
RECOMENDAR
   ↓
EJECUTAR
```

A medida que avanzamos hacia la ejecución, aumentan las necesidades de:

* validación;
* autorización;
* controles;
* auditoría;
* seguridad.

Esto será importante cuando estudiemos agentes y herramientas.

---

# 22. Objetivo y usuario final

Un mismo problema puede tener objetivos diferentes según quién utilizará el resultado.

Supongamos:

```text
Información:
Ventas mensuales.
```

Para un gerente:

```text
Objetivo:
Identificar tendencias y desviaciones.
```

Para un analista:

```text
Objetivo:
Obtener métricas y datos detallados.
```

Para un cliente:

```text
Objetivo:
Explicar el comportamiento de sus compras.
```

Por tanto:

```text
DATOS
 +
USUARIO
 +
PROPÓSITO
 =
OBJETIVO
```

El destinatario final debe considerarse durante el diseño.

---

# 23. Objetivo y nivel de abstracción

Un objetivo puede formularse en diferentes niveles.

### Nivel estratégico

```text
Mejorar el proceso de auditoría.
```

### Nivel funcional

```text
Detectar inconsistencias en registros contables.
```

### Nivel operativo

```text
Comparar fecha, cuenta, monto y descripción
para detectar registros potencialmente duplicados.
```

### Nivel técnico

```text
Generar una salida JSON con los campos:
id, tipo, evidencia, monto y riesgo.
```

Podemos verlo como una jerarquía:

```text
ESTRATÉGICO
    ↓
FUNCIONAL
    ↓
OPERATIVO
    ↓
TÉCNICO
```

Un buen sistema conecta estos niveles.

---

# 24. Ejemplo completo

Supongamos que queremos crear un sistema para analizar currículos.

## Objetivo inicial

```text
Analiza currículos.
```

Demasiado amplio.

### Objetivo mejorado

```text
Identificar candidatos que cumplan
los requisitos técnicos de una vacante.
```

### Objetivo operacional

```text
Analizar cada currículo y determinar
si el candidato cumple los requisitos
obligatorios definidos para la vacante.
```

### Criterios

```text
- tecnologías requeridas;
- años de experiencia;
- certificaciones obligatorias;
- formación requerida.
```

### Resultado

```text
{
  "cumple": true,
  "requisitos_cumplidos": [],
  "requisitos_faltantes": []
}
```

Ahora el objetivo puede convertirse en un prompt.

---

# 25. De objetivo a prompt

Podemos observar la transformación:

```text
OBJETIVO
"Identificar candidatos que cumplen
los requisitos técnicos."

        ↓

TAREA
"Analiza el currículo."

        ↓

CRITERIOS
"Compara tecnologías, experiencia
y certificaciones."

        ↓

RESTRICCIONES
"No inventes información."

        ↓

SALIDA
"Devuelve JSON."

        ↓

PROMPT
```

Esto demuestra por qué:

> **El prompt debería ser una implementación de una especificación, no un conjunto improvisado de frases.**

---

# 26. Objetivos mal definidos generan prompts problemáticos

Consideremos:

```text
Objetivo:
"Hacer un análisis completo."
```

Prompt:

```text
"Actúa como un experto y realiza
un análisis completo y profundo."
```

El problema original estaba en el objetivo.

No se solucionó agregando palabras.

Ahora definimos:

```text
Objetivo:

Identificar:
1. inconsistencias;
2. datos faltantes;
3. riesgos;
4. evidencia.
```

El prompt puede ser mucho más sencillo.

Esto produce una regla importante:

> **Cuando un prompt falla, antes de agregar instrucciones debemos revisar si el objetivo está correctamente definido.**

---

# 27. El antipatrón del objetivo implícito

Un antipatrón frecuente es esperar que el modelo adivine el objetivo.

Por ejemplo:

```text
Aquí tienes las ventas de enero a diciembre.

[datos]
```

¿Y qué debe hacer?

Podría:

* resumir;
* calcular;
* graficar;
* detectar anomalías;
* encontrar tendencias;
* pronosticar;
* comparar meses.

El modelo puede inferir una intención probable, pero no necesariamente la intención real.

Mejor:

```text
Analiza las ventas de enero a diciembre
y detecta los meses cuya facturación
se encuentre más de un 20 % por encima
o por debajo del promedio mensual.
```

---

# 28. Objetivo explícito vs. intención humana

La intención humana suele ser mucho más amplia que el texto escrito.

Por ejemplo, una persona puede pensar:

> "Quiero saber si este negocio está funcionando bien."

Pero el sistema necesita una definición operacional.

Podemos convertirlo en:

```text
Analizar:
- crecimiento mensual;
- margen;
- ingresos;
- costos;
- variación intermensual;
- tendencia.
```

Y posteriormente:

```text
Definir indicadores
        ↓
Calcular indicadores
        ↓
Comparar
        ↓
Detectar cambios
        ↓
Generar informe
```

La Ingeniería de Prompt ayuda a convertir:

```text
INTENCIÓN HUMANA
```

en:

```text
ESPECIFICACIÓN OPERACIONAL
```

---

# 29. Objetivo como contrato

Una forma avanzada de pensar el objetivo es considerarlo un **contrato entre el diseñador y el sistema**.

```text
┌──────────────────────────────────┐
│            OBJETIVO              │
├──────────────────────────────────┤
│ Entrada                           │
│ Tarea                             │
│ Condiciones                       │
│ Restricciones                     │
│ Resultado esperado                │
│ Criterios de éxito                │
└──────────────────────────────────┘
```

El modelo intenta producir una salida compatible con ese contrato.

Pero debemos recordar:

```text
CONTRATO
   ≠
GARANTÍA
```

La salida todavía necesita evaluación.

---

# 30. Objetivo y contexto

El objetivo también determina qué contexto es relevante.

Supongamos:

```text
Objetivo:
Detectar duplicados contables.
```

Probablemente serán relevantes:

```text
fecha
cuenta
monto
referencia
descripción
```

Pero quizá no sea necesario proporcionar:

```text
historial completo de conversaciones
```

si no aporta información a la tarea.

Por tanto:

```text
OBJETIVO
   ↓
¿QUÉ INFORMACIÓN NECESITO?
   ↓
CONTEXTO RELEVANTE
```

Esta relación será desarrollada en profundidad en el capítulo **04 — Contexto**.

---

# 31. Objetivo y eficiencia

Definir correctamente el objetivo también puede reducir:

* tokens innecesarios;
* información irrelevante;
* procesamiento;
* costos;
* latencia;
* errores causados por contexto excesivo.

Ejemplo:

```text
Objetivo:
Extraer número de factura y total.
```

No necesariamente necesitamos proporcionar:

```text
todo el historial comercial del cliente
```

si no afecta a la tarea.

La regla es:

> **El contexto debe estar relacionado con el objetivo.**

---

# 32. Objetivos jerárquicos

En sistemas complejos podemos tener varios niveles:

```text
OBJETIVO DEL SISTEMA
        │
        ▼
OBJETIVO DEL WORKFLOW
        │
        ▼
OBJETIVO DEL AGENTE
        │
        ▼
OBJETIVO DE LA TAREA
        │
        ▼
OBJETIVO DE LA LLAMADA AL MODELO
```

Ejemplo:

```text
SISTEMA:
Automatizar auditoría documental.

        ↓

WORKFLOW:
Analizar archivos contables.

        ↓

AGENTE:
Detectar anomalías.

        ↓

TAREA:
Comparar movimientos.

        ↓

LLAMADA:
Clasificar cada hallazgo.
```

Esto será fundamental en arquitecturas basadas en agentes.

---

# 33. Objetivo y prompt chaining

Cuando el objetivo es demasiado complejo, podemos dividirlo:

```text
OBJETIVO FINAL
     │
     ├──► Prompt 1
     │      Extraer datos
     │
     ├──► Prompt 2
     │      Validar datos
     │
     ├──► Prompt 3
     │      Analizar
     │
     └──► Prompt 4
            Generar informe
```

La ventaja es que cada paso tiene un objetivo más pequeño y verificable.

Más adelante estudiaremos esto como **Prompt Chaining**.

---

# 34. Objetivo y herramientas

Algunos objetivos pueden resolverse únicamente mediante generación de texto.

Otros requieren herramientas.

Ejemplo:

```text
Objetivo:
"Calcula la suma de 100.000 registros."
```

El modelo podría intentar realizar el cálculo.

Pero un sistema puede utilizar:

```text
Modelo
  ↓
Herramienta de código
  ↓
Cálculo
  ↓
Resultado
```

Por tanto:

> **Definir correctamente el objetivo también ayuda a determinar si el modelo debe responder directamente o utilizar una herramienta.**

---

# 35. Objetivo y razonamiento

No todos los objetivos requieren el mismo tipo de procesamiento.

Por ejemplo:

```text
Objetivo A:
Traducir una frase.
```

frente a:

```text
Objetivo B:
Resolver un problema matemático complejo.
```

o:

```text
Objetivo C:
Planificar una secuencia de acciones utilizando herramientas.
```

Estos objetivos pueden requerir diferentes capacidades del modelo.

Por eso la definición del objetivo también influye en la selección de:

* modelo;
* arquitectura;
* herramientas;
* estrategia de prompting;
* método de evaluación.

---

# 36. Árbol de decisión para definir un objetivo

Podemos utilizar este esquema:

```text
                 ¿QUÉ QUIERO CONSEGUIR?
                           │
                           ▼
                  ¿ES MEDIBLE?
                    /          \
                  NO            SÍ
                  │              │
                  ▼              ▼
          Definir criterios   Continuar
                  │
                  ▼
              ¿ES SIMPLE?
               /       \
             SÍ         NO
             │           │
             ▼           ▼
          Una tarea   Dividir
                         │
                         ▼
                    Subobjetivos
                         │
                         ▼
                 ¿CÓMO SE EVALÚA?
                         │
                         ▼
                    MÉTRICAS /
                    CRITERIOS
```

---

# 37. Lista de comprobación

Antes de construir un prompt, podemos revisar:

```text
□ ¿El objetivo está claramente definido?

□ ¿El verbo describe la operación?

□ ¿Está definido el objeto de la tarea?

□ ¿Sabemos quién utilizará el resultado?

□ ¿Sabemos qué información necesita el modelo?

□ ¿Existen restricciones?

□ ¿Está definido el resultado esperado?

□ ¿Podemos determinar si se alcanzó el objetivo?

□ ¿Existe un criterio de evaluación?

□ ¿El objetivo contiene varias tareas que
   deberían dividirse?

□ ¿El modelo realmente es adecuado para
   este objetivo?

□ ¿Necesitamos herramientas externas?
```

---

# 38. Ejercicio práctico 1

Transforma:

```text
"Analiza este texto."
```

en un objetivo operativo.

Una posible solución:

```text
Identificar las tres ideas principales del texto,
extraer los datos numéricos relevantes y señalar
las afirmaciones que requieran verificación.
```

Observa la estructura:

```text
Identificar
   +
tres ideas principales
   +
extraer datos numéricos
   +
señalar afirmaciones verificables
```

---

# 39. Ejercicio práctico 2

Objetivo inicial:

```text
"Haz un buen informe."
```

Problema:

```text
¿Qué significa "buen"?
```

Una posible especificación:

```text
Generar un informe de máximo 1.000 palabras
que contenga:

1. resumen ejecutivo;
2. principales hallazgos;
3. evidencia;
4. riesgos;
5. recomendaciones.

Utilizar únicamente la información proporcionada.
```

Ahora existen criterios concretos.

---

# 40. Ejercicio práctico 3

Objetivo:

```text
"Busca errores en estos datos."
```

Convertirlo en:

```text
Identificar registros que presenten:

- valores nulos inesperados;
- duplicados exactos;
- fechas inválidas;
- valores fuera del rango esperado.

Para cada anomalía proporcionar:
registro, tipo de error y evidencia.
```

El objetivo se convirtió en una especificación operacional.

---

# 41. Error avanzado: confundir objetivo con solución

Un error importante consiste en definir el objetivo utilizando directamente una técnica.

Por ejemplo:

```text
"Utilizar Chain of Thought para resolver
el problema."
```

Eso no es necesariamente el objetivo.

Es una posible estrategia.

El objetivo podría ser:

```text
Resolver correctamente el problema matemático
y proporcionar una respuesta verificable.
```

Después podemos investigar qué estrategia, modelo o herramienta resulta apropiada.

Separación:

```text
OBJETIVO
   ↓
ESTRATEGIA
   ↓
PROMPT
   ↓
MODELO
   ↓
RESULTADO
```

No debemos confundir el fin con el mecanismo.

---

# 42. Error avanzado: optimizar el prompt antes de definir el objetivo

Supongamos que tenemos:

```text
Prompt v1
Prompt v2
Prompt v3
Prompt v4
Prompt v5
```

Pero nunca definimos:

```text
¿Qué significa que funcionen?
```

Entonces no estamos optimizando realmente.

Podemos estar simplemente cambiando palabras.

El proceso correcto es:

```text
OBJETIVO
   ↓
CRITERIOS DE ÉXITO
   ↓
CASOS DE PRUEBA
   ↓
PROMPT
   ↓
RESULTADOS
   ↓
EVALUACIÓN
   ↓
OPTIMIZACIÓN
```

---

# 43. Principio de trazabilidad

En sistemas profesionales deberíamos poder responder:

```text
¿Por qué existe esta instrucción?
```

La respuesta debería relacionarse con:

```text
OBJETIVO
   ↓
REQUISITO
   ↓
INSTRUCCIÓN
```

Por ejemplo:

```text
Requisito:
La salida debe poder procesarse automáticamente.

        ↓

Instrucción:
Devuelve JSON válido.

        ↓

Resultado:
El sistema puede procesar la respuesta.
```

Esto reduce instrucciones arbitrarias.

---

# 44. Mapa conceptual

```text
                         OBJETIVO
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
          PROPÓSITO       TAREA        RESULTADO
             │              │              │
             │              ▼              ▼
             │          OPERACIÓN      FORMATO
             │              │              │
             └──────────────┼──────────────┘
                            ▼
                       CRITERIOS
                            │
                            ▼
                         MÉTRICAS
                            │
                            ▼
                       EVALUACIÓN
                            │
                            ▼
                        ITERACIÓN
```

---

# 45. Modelo mental definitivo

Podemos resumir el capítulo mediante esta secuencia:

```text
                 ¿QUÉ QUIERO?
                      │
                      ▼
                   OBJETIVO
                      │
                      ▼
              ¿CÓMO LO MEDIRÉ?
                      │
                      ▼
             CRITERIOS DE ÉXITO
                      │
                      ▼
              ¿QUÉ DEBE HACER?
                      │
                      ▼
                     TAREA
                      │
                      ▼
              ¿QUÉ NECESITA?
                      │
                      ▼
                   CONTEXTO
                      │
                      ▼
             ¿CÓMO LO INDICO?
                      │
                      ▼
                    PROMPT
                      │
                      ▼
                   MODELO
                      │
                      ▼
                 INFERENCIA
                      │
                      ▼
                   SALIDA
                      │
                      ▼
                 EVALUACIÓN
```

---

# 46. Idea central del capítulo

> **Un prompt no debe comenzar con "¿qué palabras debo escribir?", sino con "¿qué resultado necesito obtener y cómo sabré que lo obtuve?"**

El objetivo es el elemento que conecta:

```text
NECESIDAD HUMANA
       ↓
ESPECIFICACIÓN
       ↓
PROMPT
       ↓
MODELO
       ↓
RESULTADO
       ↓
EVALUACIÓN
```

Cuando el objetivo está mal definido, podemos terminar optimizando un prompt que resuelve el problema equivocado.

Cuando el objetivo está correctamente definido, podemos diseñar el prompt, seleccionar el modelo, determinar el contexto, elegir herramientas y construir una evaluación coherente.

---

# 47. Conexión con el siguiente capítulo

Una vez definido:

```text
¿QUÉ QUIERO CONSEGUIR?
```

la siguiente pregunta es:

> **¿Qué debe hacer exactamente el modelo para conseguirlo?**

Aquí entran las **instrucciones**.

La progresión será:

```text
01 — ¿Qué es Prompt Engineering?
          │
          ▼
02 — Objetivo
          │
          ▼
03 — Instrucciones
          │
          ▼
04 — Contexto
          │
          ▼
05 — Rol
          │
          ▼
06 — Restricciones
```

El objetivo define **el resultado que buscamos**.

La instrucción define **la operación que solicitamos al modelo**.

Esa diferencia será fundamental para todo el resto del repositorio.

---

## Conceptos que el estudiante debe dominar

Al finalizar este capítulo, el estudiante debería poder explicar:

* qué es un objetivo;
* por qué objetivo y prompt no son lo mismo;
* diferencia entre objetivo, tarea y resultado esperado;
* cómo transformar un objetivo ambiguo en uno operacional;
* por qué los verbos importan;
* diferencia entre extracción, generación y transformación;
* cómo descomponer objetivos complejos;
* qué son los criterios de éxito;
* relación entre objetivo y evaluación;
* relación entre objetivo y contexto;
* cómo influye el objetivo en la selección del modelo;
* por qué no se debe confundir objetivo con estrategia;
* por qué debemos definir el objetivo antes de optimizar un prompt.

### Regla para recordar

```text
┌──────────────────────────────────────────────┐
│                                              │
│        PRIMERO DEFINE EL RESULTADO.          │
│                                              │
│        DESPUÉS DISEÑA EL PROMPT.             │
│                                              │
│        Y FINALMENTE MIDE SI FUNCIONÓ.        │
│                                              │
└──────────────────────────────────────────────┘
```
