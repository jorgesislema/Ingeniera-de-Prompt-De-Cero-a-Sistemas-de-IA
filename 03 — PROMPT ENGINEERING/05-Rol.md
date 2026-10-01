# 05 — Rol

## 1. Introducción

Una de las técnicas más conocidas de Prompt Engineering consiste en asignar un **rol** al modelo.

Ejemplos:

```text
"Actúa como un profesor de matemáticas."

"Actúa como un auditor financiero."

"Actúa como un ingeniero de software."

"Actúa como un experto en ciberseguridad."
```

Esta técnica puede ser útil, pero también es una de las que más se simplifican y se malinterpretan.

Decir:

> "Actúa como experto en ciberseguridad"

no convierte mágicamente al modelo en un experto.

Tampoco:

* aumenta sus parámetros;
* cambia sus pesos;
* crea conocimiento nuevo;
* le concede permisos;
* le proporciona acceso a herramientas;
* garantiza exactitud;
* garantiza experiencia profesional real.

El rol funciona principalmente como una **señal contextual que ayuda a definir la perspectiva, función, audiencia, criterios y estilo esperados para la tarea**.

Por eso, una definición más rigurosa sería:

> **Un rol es una especificación contextual que orienta al modelo hacia una determinada perspectiva, función o comportamiento esperado dentro de una tarea.**

---

# 2. El error de entender el rol como magia

Un prompt típico puede decir:

```text
Actúa como un experto mundial en auditoría.
```

Y posteriormente:

```text
Analiza estos movimientos contables.
```

Un principiante puede pensar:

```text
"Le dije que es experto."
        ↓
"Ahora sabe auditar."
```

Esto es incorrecto.

El modelo sigue siendo:

```text
MODELO
  │
  ├── mismos parámetros
  ├── mismo entrenamiento
  ├── mismas capacidades generales
  └── mismas limitaciones fundamentales
```

Lo que cambia principalmente es:

```text
CONTEXTO
   │
   ▼
encuadre de la tarea
```

Por tanto:

```text
ROL
  ≠
NUEVO MODELO
```

y:

```text
ROL
  ≠
NUEVO CONOCIMIENTO
```

---

# 3. ¿Qué modifica realmente un rol?

Un rol puede influir en diferentes dimensiones de la respuesta.

Por ejemplo:

```text
ROL
 │
 ├── perspectiva
 ├── vocabulario
 ├── prioridades
 ├── estilo
 ├── nivel técnico
 ├── criterios de análisis
 └── tipo de respuesta
```

Supongamos:

```text
Analiza este sistema.
```

Puede producir una respuesta general.

Pero:

```text
Actúa como arquitecto de software.
Analiza este sistema.
```

puede orientar la respuesta hacia:

* componentes;
* dependencias;
* escalabilidad;
* acoplamiento;
* disponibilidad;
* mantenibilidad;
* patrones arquitectónicos.

El contenido disponible puede ser el mismo.

Lo que cambia es el **marco desde el cual se interpreta la tarea**.

---

# 4. Rol como marco contextual

Podemos representarlo:

```text
                 MODELO
                    │
                    │
              ┌─────▼─────┐
              │ CONTEXTO  │
              │           │
              │   ROL     │
              │     +     │
              │  TAREA    │
              │     +     │
              │  DATOS    │
              └─────┬─────┘
                    │
                    ▼
                 RESPUESTA
```

El rol forma parte del contexto.

No está físicamente "dentro" de los parámetros del modelo.

Por eso:

```text
Prompt:
"Actúa como médico."

```

no significa:

```text
parámetros nuevos
```

sino:

```text
información contextual adicional
```

---

# 5. Rol, objetivo e instrucción

Es fundamental diferenciar tres conceptos.

## Objetivo

Define:

> ¿Qué queremos conseguir?

Ejemplo:

```text
Identificar anomalías en una transacción.
```

## Rol

Define:

> ¿Desde qué perspectiva debe abordarse?

Ejemplo:

```text
Analista de auditoría financiera.
```

## Instrucción

Define:

> ¿Qué operación debe realizar?

Ejemplo:

```text
Analiza las transacciones y señala las que presentan inconsistencias.
```

Podemos representarlo:

```text
ROL
 ↓
¿Desde qué perspectiva?

OBJETIVO
 ↓
¿Qué queremos conseguir?

INSTRUCCIÓN
 ↓
¿Qué debe hacer?

CONTEXTO
 ↓
¿Qué información tiene?

SALIDA
 ↓
¿Cómo debe entregarla?
```

Una formulación completa podría ser:

```text
ROL:
Analista de auditoría financiera.

OBJETIVO:
Detectar posibles anomalías.

INSTRUCCIÓN:
Analiza las transacciones proporcionadas.

CONTEXTO:
[datos]

SALIDA:
Devuelve una tabla con:
cuenta, riesgo, descripción y monto.
```

Esta estructura es mucho más precisa que:

```text
"Eres el mejor auditor del mundo."
```

---

# 6. Rol no es objetivo

Comparemos:

```text
Actúa como auditor.
```

con:

```text
Identifica transacciones duplicadas.
```

El primero define una perspectiva.

El segundo define una tarea.

Por eso:

```text
ROL
+
OBJETIVO
```

suele ser más útil que únicamente:

```text
ROL
```

Ejemplo:

```text
Actúa como auditor financiero.

Objetivo:
identificar transacciones duplicadas y explicar
por qué podrían representar un riesgo.
```

---

# 7. Rol no es instrucción

También debemos diferenciar:

```text
"Actúa como profesor."
```

de:

```text
"Explica el concepto utilizando un ejemplo sencillo."
```

El primero establece una función contextual.

El segundo especifica una operación.

Por tanto:

```text
ROL
     ↓
orienta

INSTRUCCIÓN
     ↓
opera
```

---

# 8. Rol no es conocimiento

Este es uno de los principios más importantes del capítulo.

Supongamos:

```text
Actúa como experto en derecho tributario ecuatoriano.
```

Eso no garantiza que el modelo:

* conozca la legislación vigente;
* conozca la última reforma;
* tenga acceso a normativa actual;
* interprete correctamente la legislación;
* pueda proporcionar asesoría jurídica válida.

Si la tarea requiere información actualizada, el sistema puede necesitar:

```text
ROL
+
FUENTES ACTUALIZADAS
+
CONTEXTO
+
VALIDACIÓN
```

Por ejemplo:

```text
Actúa como analista tributario.

Utiliza exclusivamente la normativa proporcionada
en los documentos adjuntos.

Indica la fuente utilizada para cada conclusión.
```

Aquí el rol no intenta crear conocimiento.

Se utiliza para establecer una perspectiva mientras el contexto proporciona las fuentes.

---

# 9. Rol y conocimiento externo

Una arquitectura más robusta puede ser:

```text
ROL
 │
 ▼
"Analista tributario"
 │
 ▼
PREGUNTA
 │
 ▼
RECUPERACIÓN
 │
 ▼
NORMATIVA ACTUAL
 │
 ▼
CONTEXTO
 │
 ▼
MODELO
```

Esta arquitectura es superior conceptualmente a:

```text
"Actúa como el mejor experto tributario del país."
```

porque el sistema no depende únicamente de una etiqueta de rol.

---

# 10. Rol y especialización

Un rol puede orientar al modelo hacia una especialización.

Por ejemplo:

```text
Ingeniero de software
```

puede priorizar:

* arquitectura;
* mantenibilidad;
* pruebas;
* dependencias;
* rendimiento.

Mientras:

```text
Analista de seguridad
```

puede priorizar:

* superficie de ataque;
* autenticación;
* autorización;
* exposición de datos;
* vulnerabilidades.

El mismo código puede recibir análisis diferentes.

```text
                  MISMO CÓDIGO
                       │
             ┌─────────┴─────────┐
             │                   │
             ▼                   ▼
       ROL SOFTWARE         ROL SEGURIDAD
             │                   │
             ▼                   ▼
      arquitectura          vulnerabilidades
      mantenibilidad        controles
      pruebas               amenazas
```

El rol funciona como una forma de **priorización contextual**.

---

# 11. Rol y perspectiva

La palabra "perspectiva" es especialmente importante.

Supongamos:

```text
Analiza este contrato.
```

Podemos utilizar:

```text
Rol: abogado corporativo
```

y obtener un análisis centrado en:

* obligaciones;
* responsabilidades;
* penalizaciones;
* cláusulas.

Pero:

```text
Rol: gerente financiero
```

puede orientar hacia:

* costes;
* pagos;
* riesgos financieros;
* compromisos económicos.

Y:

```text
Rol: especialista en seguridad
```

puede prestar atención a:

* confidencialidad;
* tratamiento de datos;
* acceso;
* incidentes.

El documento no cambió.

Cambió el **marco analítico solicitado**.

---

# 12. Rol y audiencia

Un rol puede combinarse con una audiencia.

Ejemplo:

```text
Rol:
profesor universitario de inteligencia artificial.

Audiencia:
estudiantes que no saben programación.

Tarea:
explica qué es un embedding.
```

La respuesta debería orientarse hacia:

```text
lenguaje sencillo
+
analogías
+
progresión pedagógica
```

En cambio:

```text
Rol:
investigador de aprendizaje automático.

Audiencia:
estudiantes de doctorado.

Tarea:
explica embeddings.
```

puede requerir:

```text
notación matemática
+
espacios vectoriales
+
funciones de similitud
+
limitaciones
+
literatura técnica
```

Por tanto:

```text
ROL
+
AUDIENCIA
```

permite especificar mejor la comunicación.

---

# 13. Rol y nivel técnico

Una buena definición de rol puede incluir el nivel esperado.

Ejemplo:

```text
Rol:
ingeniero de IA especializado en sistemas LLM.

Nivel de audiencia:
programador intermedio.

Explica:
desde conceptos básicos hasta detalles de arquitectura.
```

Esto es más útil que:

```text
"Actúa como experto."
```

porque "experto" no especifica:

* para quién;
* qué profundidad;
* qué vocabulario;
* qué objetivos;
* qué criterios.

---

# 14. Rol como contrato comunicativo

Un concepto útil es pensar en el rol como un **contrato comunicativo**.

Por ejemplo:

```text
ROL:
Profesor de programación

AUDIENCIA:
Principiante

COMPORTAMIENTO:
Explicar progresivamente

ESTILO:
Claro y práctico

CRITERIO:
No asumir conocimientos previos
```

Esto define expectativas.

No crea nuevas capacidades.

Podemos representarlo:

```text
ROL
 │
 ├── quién responde
 ├── desde qué perspectiva
 ├── para quién
 ├── con qué nivel
 └── con qué estilo
```

---

# 15. Rol genérico vs rol operacional

Comparemos:

### Rol genérico

```text
Actúa como experto en IA.
```

Es poco específico.

### Rol operacional

```text
Actúa como ingeniero de IA especializado en
evaluación de sistemas RAG.

Prioriza:
- precisión de recuperación;
- calidad de contexto;
- trazabilidad;
- coste;
- latencia.
```

El segundo proporciona criterios observables.

Esto lleva a un principio:

> **Un buen rol debe aportar información útil para la tarea, no solamente prestigio verbal.**

---

# 16. Roles débiles

Algunos roles utilizan lenguaje grandilocuente:

```text
Eres el mayor experto del mundo.
Eres un genio.
Eres una autoridad absoluta.
Eres el mejor especialista existente.
```

Estos roles suelen aportar poca información operacional.

No definen:

* qué hacer;
* cómo analizar;
* qué priorizar;
* qué fuentes utilizar;
* cómo validar.

Por ejemplo:

```text
Eres el mejor auditor del planeta.
```

es menos informativo que:

```text
Analiza los movimientos desde una perspectiva
de auditoría financiera.

Prioriza:
1. duplicados;
2. inconsistencias cronológicas;
3. importes anómalos;
4. debilidades de control.
```

---

# 17. Rol y criterios

Una de las formas más útiles de mejorar un rol consiste en asociarlo con criterios.

En lugar de:

```text
Actúa como analista de seguridad.
```

podemos especificar:

```text
Actúa como analista de seguridad de aplicaciones.

Evalúa:
- autenticación;
- autorización;
- validación de entradas;
- gestión de secretos;
- exposición de datos;
- dependencias.
```

El rol proporciona el marco.

Los criterios hacen operativo el análisis.

---

# 18. Rol + criterios + tarea

Una estructura robusta:

```text
ROL
 ↓
perspectiva

CRITERIOS
 ↓
qué observar

TAREA
 ↓
qué hacer

DATOS
 ↓
sobre qué trabajar

SALIDA
 ↓
cómo comunicarlo
```

Ejemplo:

```text
ROL:
Analista de seguridad de aplicaciones.

CRITERIOS:
Autenticación, autorización, validación de entradas
y gestión de secretos.

TAREA:
Analiza el código proporcionado.

DATOS:
[código]

SALIDA:
Enumera cada hallazgo con:
severidad, ubicación, evidencia y recomendación.
```

Esto tiene mucho más valor que una frase de autoridad.

---

# 19. Rol y restricciones

Un rol puede combinarse con restricciones.

Ejemplo:

```text
Actúa como analista financiero.

No inventes datos.

Utiliza únicamente la información proporcionada.

Si falta información, indícalo explícitamente.
```

Aquí tenemos:

```text
ROL
+
RESTRICCIONES
```

La combinación puede hacer que el comportamiento sea más predecible.

Sin embargo, las restricciones importantes no deberían depender únicamente del lenguaje natural cuando existe riesgo real.

---

# 20. Rol y formato de salida

También puede combinarse:

```text
ROL:
Auditor financiero.

TAREA:
Analiza las transacciones.

SALIDA:
Devuelve JSON válido con:
{
  "riesgo": "...",
  "cuenta": "...",
  "monto": 0,
  "descripcion": "..."
}
```

El rol establece la perspectiva.

La salida establece la representación.

Por tanto:

```text
ROL
≠
FORMATO
```

pero ambos pueden coexistir.

---

# 21. Rol y comportamiento

Un rol puede sugerir ciertos patrones de comportamiento.

Ejemplo:

```text
Actúa como profesor.

Explica primero el concepto,
después proporciona un ejemplo
y finalmente plantea un ejercicio.
```

Aquí es importante observar que:

```text
"profesor"
```

por sí solo no define necesariamente ese procedimiento.

La segunda parte sí lo define:

```text
explica
→ ejemplifica
→ ejercita
```

Esto demuestra que los mejores prompts no dependen únicamente del nombre del rol.

---

# 22. Rol y procedimientos

Un error frecuente es esperar que:

```text
Actúa como auditor.
```

haga que el modelo siga automáticamente una metodología específica.

No necesariamente.

Si necesitamos un procedimiento:

```text
1. Identifica duplicados.
2. Comprueba secuencias.
3. Detecta importes atípicos.
4. Evalúa riesgos.
5. Presenta evidencia.
```

debemos especificarlo.

Por tanto:

```text
ROL
     ↓
perspectiva

PROCEDIMIENTO
     ↓
operaciones
```

No son equivalentes.

---

# 23. Rol y metodologías profesionales

Supongamos que necesitamos un análisis de auditoría.

No basta:

```text
Actúa como auditor.
```

Puede ser necesario proporcionar:

```text
marco metodológico
criterios
políticas
definiciones
umbrales
fuentes
procedimiento
```

El modelo necesita contexto suficiente.

Por ejemplo:

```text
ROL:
Analista de auditoría.

MARCO:
Normativa proporcionada.

TAREA:
Identificar excepciones.

CRITERIO:
Toda transacción que incumpla la regla X.

EVIDENCIA:
Citar registro y monto.

SALIDA:
JSON.
```

La especialización se obtiene del conjunto, no únicamente de la etiqueta de rol.

---

# 24. Rol y autoridad

Una frase como:

```text
Actúa como administrador del sistema.
```

no debe interpretarse como:

```text
tienes permisos administrativos.
```

Esta distinción es crítica.

```text
ROL DECLARADO
     ≠
PERMISOS REALES
```

Un modelo puede recibir:

```text
"Actúa como administrador."
```

pero seguir sin tener:

* acceso a servidores;
* credenciales;
* permisos;
* APIs;
* archivos;
* bases de datos.

Las capacidades reales son proporcionadas por el sistema.

---

# 25. Rol y herramientas

Consideremos:

```text
Actúa como administrador de base de datos.
```

El modelo no obtiene automáticamente:

```text
acceso a PostgreSQL
```

Para ello necesitamos:

```text
MODELO
   │
   ▼
HERRAMIENTA
   │
   ▼
BASE DE DATOS
```

La arquitectura real podría ser:

```text
Rol
 │
 ▼
Modelo
 │
 ├── herramienta SQL
 │
 ├── herramienta de lectura
 │
 └── herramienta de validación
```

Por tanto:

> **El rol describe una función; las herramientas proporcionan capacidades operativas.**

---

# 26. Rol y permisos

Esta diferencia es todavía más importante en agentes.

Supongamos:

```text
Rol:
Administrador financiero.
```

No significa que el agente pueda:

```text
eliminar una cuenta
aprobar un pago
transferir dinero
```

Los permisos deben estar implementados mediante controles del sistema.

```text
MODELO
   │
   ▼
PROPUESTA DE ACCIÓN
   │
   ▼
POLÍTICA
   │
   ▼
AUTORIZACIÓN
   │
   ▼
EJECUCIÓN
```

Nunca debe utilizarse una simple instrucción de rol como mecanismo de autorización.

---

# 27. Rol y seguridad

Desde una perspectiva de seguridad:

```text
"Actúa como administrador"
```

es información contextual.

No es una política de acceso.

La seguridad debe depender de:

* autenticación;
* autorización;
* permisos;
* validación;
* aislamiento;
* herramientas controladas;
* políticas;
* supervisión.

Este principio puede resumirse:

> **El lenguaje puede describir una capacidad; la arquitectura debe determinar si esa capacidad existe realmente.**

---

# 28. Role Prompting

En literatura y práctica de Prompt Engineering aparece el concepto de **Role Prompting**.

La técnica consiste en proporcionar una identidad o función contextual para orientar la respuesta.

Ejemplo:

```text
Actúa como analista de datos.

Explica las anomalías encontradas en este dataset.
```

Su utilidad depende de:

* modelo;
* tarea;
* calidad del rol;
* información adicional;
* instrucciones;
* contexto;
* evaluación.

No existe garantía de que agregar un rol siempre mejore el resultado.

---

# 29. ¿Cuándo puede ser útil un rol?

Los roles pueden ser particularmente útiles cuando la tarea requiere una perspectiva específica.

Por ejemplo:

### Enseñanza

```text
Profesor de programación.
```

### Arquitectura

```text
Arquitecto de software.
```

### Seguridad

```text
Analista de seguridad.
```

### Análisis financiero

```text
Analista financiero.
```

### Comunicación

```text
Editor técnico.
```

### Revisión de código

```text
Ingeniero de software especializado en Python.
```

En cada caso, el rol ayuda a definir el marco desde el cual se interpreta la tarea.

---

# 30. ¿Cuándo un rol aporta poco?

Para tareas extremadamente simples:

```text
Calcula 15 × 8.
```

decir:

```text
Actúa como matemático experto.
```

probablemente aporta poco.

El problema no necesita una perspectiva profesional compleja.

Por tanto:

> **No todas las tareas necesitan un rol explícito.**

El Prompt Engineering profesional evita agregar instrucciones simplemente porque una técnica existe.

---

# 31. Rol y complejidad de la tarea

Podemos establecer una regla práctica:

```text
Tarea simple
   ↓
rol opcional

Tarea especializada
   ↓
rol potencialmente útil

Tarea profesional compleja
   ↓
rol + criterios + contexto + procedimiento
```

Ejemplo:

```text
5 + 5
```

no requiere:

```text
rol
contexto
metodología
```

Pero:

```text
Analiza un sistema empresarial en busca de
vulnerabilidades.
```

sí puede beneficiarse de:

```text
rol
+
criterios
+
contexto
+
metodología
+
formato
+
validación
```

---

# 32. Rol y modelos diferentes

Un mismo rol puede producir resultados diferentes en distintos modelos.

```text
"Actúa como auditor."

        │
        ├── Modelo A → respuesta A
        ├── Modelo B → respuesta B
        └── Modelo C → respuesta C
```

Esto ocurre porque los modelos tienen:

* diferentes datos de entrenamiento;
* diferentes arquitecturas;
* diferentes capacidades;
* diferentes alineamientos;
* diferentes mecanismos de inferencia;
* diferentes ventanas de contexto.

Por tanto:

> **Un rol no tiene un comportamiento universal independiente del modelo.**

---

# 33. Rol y modelos de razonamiento

En modelos orientados al razonamiento, el rol puede servir como parte del encuadre de la tarea.

Por ejemplo:

```text
Actúa como analista de problemas matemáticos.

Resuelve el problema y proporciona una explicación
verificable del resultado.
```

Sin embargo, no debe asumirse que:

```text
"Actúa como matemático"
```

garantiza una mejora en la capacidad de razonamiento.

El modelo puede ser capaz de resolver:

```text
sin rol
```

y el rol simplemente cambiar:

```text
la presentación
```

o:

```text
la estrategia contextual sugerida.
```

La mejora debe comprobarse experimentalmente.

---

# 34. Rol y modelos especializados

Los modelos especializados pueden reducir la necesidad de describir ciertas características de la tarea.

Por ejemplo:

```text
Modelo especializado en código
```

ya puede estar optimizado para:

* programación;
* sintaxis;
* depuración;
* generación de código.

Por tanto:

```text
Actúa como programador experto.
```

puede aportar menos que en un modelo general.

Esto demuestra nuevamente:

```text
ROL
+
MODELO
```

deben analizarse conjuntamente.

---

# 35. Rol y preentrenamiento

Los modelos aprenden asociaciones durante el entrenamiento.

Cuando escribimos:

```text
"Actúa como profesor."
```

el modelo ya tiene asociaciones aprendidas sobre:

```text
profesor
explicación
clase
ejemplos
preguntas
pedagogía
```

El rol puede activar o reforzar un determinado patrón contextual.

Pero esto no significa que exista un "modo profesor" almacenado como un módulo independiente.

Es más correcto pensar en:

```text
texto del rol
       ↓
representación contextual
       ↓
predicción condicionada
```

---

# 36. Rol y asociaciones semánticas

Supongamos:

```text
"Actúa como profesor de física."
```

El modelo puede asociar ese contexto con:

```text
leyes físicas
explicaciones
fórmulas
ejemplos
experimentos
```

Mientras:

```text
"Actúa como periodista."
```

puede asociarse con:

```text
titular
fuentes
resumen
estructura narrativa
preguntas
```

Estas asociaciones provienen del comportamiento aprendido por el modelo.

No debemos interpretarlas como reglas deterministas.

---

# 37. Rol y tono

Un rol también puede influir en el tono.

Por ejemplo:

```text
Rol:
profesor para principiantes.
```

puede orientar hacia:

```text
lenguaje sencillo
```

Mientras:

```text
Rol:
investigador académico.
```

puede orientar hacia:

```text
terminología técnica
```

Sin embargo, si el tono es crítico para la tarea, conviene especificarlo explícitamente.

Ejemplo:

```text
Utiliza lenguaje técnico, pero define cada término
antes de utilizarlo.
```

No debemos esperar que el nombre del rol controle perfectamente el estilo.

---

# 38. Rol y personalidad

Rol y personalidad tampoco son exactamente lo mismo.

### Rol

Responde a:

> ¿Qué función estás desempeñando?

### Personalidad

Responde más a:

> ¿Cómo te comportas o comunicas?

Ejemplo:

```text
Rol:
profesor de programación.

Personalidad comunicativa:
paciente, directa y orientada a ejemplos.
```

Podemos separarlos:

```text
FUNCIÓN
   ↓
Rol

FORMA DE COMUNICACIÓN
   ↓
Estilo / personalidad
```

Esta distinción es útil para diseñar prompts más precisos.

---

# 39. Rol y estilo

Ejemplo:

```text
Rol:
Analista financiero.

Estilo:
Técnico, conciso y basado en evidencia.
```

Aquí:

```text
Analista financiero
```

define la perspectiva.

Mientras:

```text
Técnico, conciso y basado en evidencia
```

define características comunicativas.

No deben confundirse.

---

# 40. Rol y criterios de calidad

Podemos utilizar el rol para definir una perspectiva y después establecer criterios verificables.

Ejemplo:

```text
Rol:
Revisor de código Python.

Criterios:
- corrección;
- legibilidad;
- complejidad;
- seguridad;
- pruebas;
- mantenibilidad.
```

Ahora podemos evaluar la respuesta.

Esto transforma:

```text
rol abstracto
```

en:

```text
rol + criterios observables
```

que es mucho más útil para sistemas profesionales.

---

# 41. Rol como componente modular

Un rol puede convertirse en un componente reutilizable.

Por ejemplo:

```text
ROL_AUDITOR = """
Analiza la información desde una perspectiva
de auditoría financiera.
Prioriza evidencia, trazabilidad y riesgos.
"""
```

Después:

```text
ROL_AUDITOR
+
TAREA
+
DATOS
+
FORMATO
```

Esto permite construir prompts modulares.

La ventaja es:

```text
cambiar rol
sin cambiar toda la plantilla.
```

Esto conecta con:

→ `12-Prompts-Modulares.md`

---

# 42. Roles parametrizados

También podemos convertir un rol en una plantilla.

```text
Actúa como {rol} especializado en {dominio}.

Audiencia:
{audiencia}

Prioriza:
{criterios}

Nivel:
{nivel}
```

Por ejemplo:

```text
rol = "ingeniero de IA"
dominio = "sistemas RAG"
audiencia = "programadores intermedios"
nivel = "avanzado"
```

La plantilla produce:

```text
Actúa como ingeniero de IA especializado en sistemas RAG.

Audiencia:
programadores intermedios.

Nivel:
avanzado.
```

Esto permite automatizar la generación de prompts.

---

# 43. Rol y composición

Un prompt puede utilizar varios roles conceptualmente:

```text
Analiza el problema como:
1. ingeniero de software;
2. especialista en seguridad;
3. arquitecto de sistemas.
```

Pero esto puede producir ambigüedad.

Por ejemplo:

```text
¿Qué criterio tiene prioridad?
```

Si los roles entran en conflicto:

```text
seguridad
vs
rendimiento
```

el modelo necesita criterios explícitos.

Por eso, múltiples roles no siempre mejoran el resultado.

---

# 44. Antipatrón: "comité de expertos"

Un prompt puede decir:

```text
Actúa simultáneamente como:
- el mejor programador;
- el mejor matemático;
- el mejor auditor;
- el mejor científico;
- el mejor abogado.
```

Esto puede producir una identidad poco definida.

Es preferible:

```text
Perspectiva principal:
ingeniero de IA.

Criterios adicionales:
seguridad y rendimiento.
```

La precisión suele ser más útil que acumular títulos.

---

# 45. Antipatrón: autoridad exagerada

Evitar:

```text
Eres el experto número uno del mundo.
```

Preferir:

```text
Analiza desde la perspectiva de un especialista
en auditoría financiera y prioriza evidencia,
trazabilidad y control interno.
```

La segunda formulación contiene información operacional.

---

# 46. Antipatrón: rol sin tarea

```text
Actúa como experto en Python.
```

¿Y después?

El modelo todavía necesita saber:

```text
¿Qué debe hacer?
```

Mejor:

```text
Actúa como ingeniero de software especializado en Python.

Revisa el código proporcionado y detecta:
- errores lógicos;
- problemas de rendimiento;
- vulnerabilidades;
- oportunidades de simplificación.
```

---

# 47. Antipatrón: rol sin contexto

```text
Actúa como auditor.
Analiza esto.
```

Pero:

```text
esto = ¿qué?
```

Falta información.

Un rol no puede compensar un contexto ausente.

```text
ROL
≠
DATOS
```

---

# 48. Antipatrón: rol como sustituto de fuentes

```text
Actúa como experto legal.
```

no sustituye:

```text
legislación actualizada
```

Ni:

```text
Actúa como médico.
```

sustituye:

```text
información clínica adecuada
```

Ni:

```text
Actúa como auditor.
```

sustituye:

```text
datos contables
políticas
criterios
evidencia
```

El rol orienta.

El contexto proporciona la información.

---

# 49. Antipatrón: rol como mecanismo de seguridad

Nunca asumir:

```text
Actúa como administrador.
```

equivale a:

```text
Tiene permisos administrativos.
```

Nunca asumir:

```text
Actúa como sistema interno.
```

equivale a:

```text
Puede acceder a sistemas internos.
```

Nunca asumir:

```text
Actúa como desarrollador con acceso al servidor.
```

equivale a:

```text
Puede ejecutar comandos.
```

Las capacidades deben existir fuera del lenguaje natural.

---

# 50. Cómo diseñar un buen rol

Podemos utilizar esta estructura:

```text
ROL = FUNCIÓN + DOMINIO + PERSPECTIVA + CRITERIOS
```

Por ejemplo:

```text
FUNCIÓN:
Analista

DOMINIO:
Ciberseguridad

PERSPECTIVA:
Seguridad de aplicaciones web

CRITERIOS:
Autenticación, autorización, validación de entradas
y exposición de datos.
```

Convertido en prompt:

```text
Actúa como analista de ciberseguridad especializado
en seguridad de aplicaciones web.

Analiza el sistema desde una perspectiva defensiva.

Prioriza:
- autenticación;
- autorización;
- validación de entradas;
- exposición de datos.
```

---

# 51. Plantilla profesional de rol

Una plantilla reutilizable:

```text
## ROL

Función:
[función profesional]

Dominio:
[área de especialización]

Perspectiva:
[enfoque desde el cual debe analizar]

Audiencia:
[quién recibirá la respuesta]

Nivel:
[básico / intermedio / avanzado / experto]

Criterios prioritarios:
- [criterio 1]
- [criterio 2]
- [criterio 3]
```

Después:

```text
## TAREA

[qué debe hacer]
```

Y:

```text
## CONTEXTO

[información necesaria]
```

Finalmente:

```text
## SALIDA

[formato esperado]
```

---

# 52. Ejemplo: profesor

### Rol débil

```text
Actúa como profesor.
```

### Rol mejorado

```text
Actúa como profesor de inteligencia artificial
especializado en enseñar LLM a estudiantes que
están comenzando.

Explica los conceptos desde lo básico hasta lo
avanzado.

Define los términos técnicos antes de utilizarlos
y utiliza ejemplos prácticos.
```

Aquí el rol aporta:

```text
función
+
dominio
+
audiencia
+
nivel
+
comportamiento esperado
```

---

# 53. Ejemplo: ingeniero de software

```text
Actúa como ingeniero de software especializado
en Python y sistemas backend.

Analiza el código desde estas perspectivas:

1. corrección;
2. mantenibilidad;
3. rendimiento;
4. seguridad;
5. pruebas.

No modifiques el código todavía.
Primero identifica los problemas y explica
su impacto.
```

Observamos:

```text
ROL
+
CRITERIOS
+
RESTRICCIÓN
+
PROCEDIMIENTO
```

---

# 54. Ejemplo: auditoría

```text
Actúa como analista de auditoría financiera.

Analiza las transacciones proporcionadas.

Prioriza:

1. duplicados;
2. inconsistencias cronológicas;
3. importes anómalos;
4. secuencias incompletas;
5. posibles debilidades de control.

Para cada hallazgo indica:

- cuenta;
- riesgo;
- descripción;
- monto;
- evidencia.

No inventes datos que no estén presentes.
```

Este ejemplo muestra por qué un buen prompt no depende exclusivamente de:

```text
"Actúa como auditor."
```

La calidad está en el conjunto.

---

# 55. Ejemplo: análisis de seguridad

```text
Actúa como analista de seguridad de aplicaciones.

Revisa el código proporcionado.

Evalúa:

- autenticación;
- autorización;
- validación de entradas;
- gestión de secretos;
- exposición de información;
- dependencias vulnerables.

Para cada hallazgo indica:

- vulnerabilidad;
- ubicación;
- evidencia;
- impacto;
- recomendación.

Distingue entre vulnerabilidades confirmadas
y riesgos que requieren verificación adicional.
```

Este rol está operacionalizado mediante criterios.

---

# 56. Rol y evaluación A/B

No debemos asumir que el rol mejora siempre el resultado.

Podemos comprobarlo experimentalmente.

### Prompt A

```text
Analiza el código y encuentra vulnerabilidades.
```

### Prompt B

```text
Actúa como analista de seguridad de aplicaciones.

Analiza el código y encuentra vulnerabilidades.
```

Mantener todo lo demás constante:

```text
Modelo
Contexto
Temperatura/configuración
Datos
Salida
```

Después comparar:

```text
Precisión
Recall
Falsos positivos
Falsos negativos
Calidad de explicación
```

Si B no mejora:

```text
¿Realmente necesitamos el rol?
```

Esta pregunta forma parte de la ingeniería profesional.

---

# 57. Rol y reproducibilidad

Si utilizamos un rol en producción, debemos registrarlo.

Por ejemplo:

```text
prompt_version = "audit_v3"
role = "financial_auditor"
model = "modelo-X"
context_version = "2026-09"
```

Esto permite reproducir experimentos.

Un sistema profesional no debería depender de prompts improvisados.

---

# 58. Rol y versionado

Los roles también pueden evolucionar.

```text
auditor_v1
auditor_v2
auditor_v3
```

Podemos cambiar:

```text
criterios
vocabulario
estructura
restricciones
```

y evaluar cuál produce mejores resultados.

Esto convierte el rol en un componente de software versionable.

---

# 59. Rol y evaluación cuantitativa

Podemos definir una evaluación:

$$
Score =
w_1 Accuracy +
w_2 Relevance +
w_3 Completeness +
w_4 Format
$$

y comparar:

```text
sin rol
vs
rol simple
vs
rol operacional
```

No existe un peso universal.

Los pesos dependen de la aplicación.

En un sistema de auditoría, por ejemplo, puede ser especialmente importante:

```text
exactitud
trazabilidad
completitud
```

mientras que en una aplicación educativa puede importar más:

```text
claridad
comprensión
adaptación al nivel
```

---

# 60. Rol y sobreespecificación

También es posible exagerar.

Ejemplo:

```text
Actúa como un ingeniero de software senior,
arquitecto principal, especialista en seguridad,
experto en DevOps, investigador de IA, profesor,
autor técnico y gerente de proyectos.
```

Puede producir un prompt innecesariamente grande.

Una mejor estrategia:

```text
Rol principal:
Ingeniero de software especializado en sistemas de IA.

Criterios adicionales:
seguridad y mantenibilidad.
```

El principio es:

> **Especificar lo necesario, no acumular títulos.**

---

# 61. Rol y separación de responsabilidades

En sistemas complejos conviene separar:

```text
ROL
↓
PERSPECTIVA

TAREA
↓
OPERACIÓN

RESTRICCIONES
↓
LÍMITES

HERRAMIENTAS
↓
CAPACIDADES

PERMISOS
↓
AUTORIZACIÓN

VALIDADOR
↓
CONTROL
```

Esto evita intentar resolver todos los problemas mediante lenguaje natural.

---

# 62. Rol dentro de un sistema de IA

Podemos visualizar una arquitectura:

```text
                    SISTEMA
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
      ROL            TAREA           CONTEXTO
       │               │                │
       └───────────────┼────────────────┘
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
                  VALIDACIÓN
```

Esto muestra que el rol es solamente uno de los componentes.

---

# 63. Rol y arquitectura del modelo

El comportamiento de un rol puede variar según la arquitectura.

```text
ROL
 │
 ├── Modelo general
 │
 ├── Modelo de razonamiento
 │
 ├── Modelo de código
 │
 ├── Modelo multimodal
 │
 └── Modelo especializado
```

Por ejemplo, un modelo especializado en código puede interpretar:

```text
"Actúa como programador."
```

de manera diferente a un modelo general.

Por eso:

> **Los patrones de prompting deben evaluarse sobre el modelo concreto donde serán utilizados.**

---

# 64. Rol y contexto largo

En contextos muy grandes, el rol debe competir por atención con otra información.

Podemos imaginar:

```text
ROL
│
├── instrucciones
├── historial
├── documentos
├── herramientas
├── datos
└── nueva consulta
```

Si el rol está enterrado entre enormes cantidades de información, su efecto puede variar.

Por eso conviene colocar y estructurar las instrucciones importantes de manera consistente.

No existe una posición universalmente óptima para todos los modelos y tareas.

La estrategia debe evaluarse.

---

# 65. Rol y redundancia

Puede ser útil repetir una función crítica en diferentes partes de una plantilla cuando la arquitectura lo justifique.

Pero la repetición excesiva puede:

* aumentar tokens;
* generar ruido;
* introducir contradicciones.

Ejemplo innecesario:

```text
Eres auditor.
Recuerda que eres auditor.
No olvides que eres auditor.
Actúa como auditor.
Piensa como auditor.
```

Preferible:

```text
Rol:
Analista de auditoría financiera.

Prioriza evidencia, trazabilidad y detección de anomalías.
```

La precisión suele superar a la repetición.

---

# 66. Rol y conflicto de instrucciones

Supongamos:

```text
Rol:
Profesor.

Instrucción:
Explica de forma sencilla.

Otra instrucción:
Utiliza terminología académica avanzada.
```

Existe una posible tensión.

La solución no es simplemente añadir más texto.

Podemos especificar:

```text
Utiliza terminología técnica, pero define cada término
en lenguaje sencillo.
```

Esto elimina la ambigüedad.

Por tanto:

> **Un rol no elimina la necesidad de resolver conflictos entre instrucciones.**

---

# 67. Rol y delimitadores

Podemos estructurarlo:

```text
### ROL

Analista de datos especializado en fraude.

### OBJETIVO

Identificar patrones sospechosos.

### DATOS

<datos>
...
</datos>

### SALIDA

...
```

Esto hace visible la arquitectura conceptual del prompt.

Los delimitadores ayudan a separar:

```text
rol
objetivo
datos
salida
```

y preparan el terreno para el siguiente capítulo:

**06 — Restricciones**

---

# 68. Rol y lenguaje natural

Un rol puede escribirse de muchas formas:

```text
Actúa como...
```

```text
Eres...
```

```text
Asume el papel de...
```

```text
Analiza desde la perspectiva de...
```

No debemos asumir que una frase específica es mágicamente superior.

La información semántica es más importante que la fórmula exacta.

Por ejemplo:

```text
Actúa como auditor financiero.
```

y:

```text
Analiza la información desde una perspectiva
de auditoría financiera.
```

pueden cumplir funciones similares.

Lo importante es qué información aporta el contexto.

---

# 69. ¿Es necesario decir "Actúa como"?

No.

Podemos escribir:

```text
Perspectiva:
auditoría financiera.
```

o:

```text
Analiza desde una perspectiva de auditoría financiera.
```

o:

```text
Rol:
analista de auditoría financiera.
```

La estructura puede adaptarse al sistema.

Lo importante es evitar supersticiones de prompting.

No existe una fórmula lingüística universal que garantice una respuesta determinada.

---

# 70. Rol como variable experimental

En un sistema profesional, podemos tratar el rol como una variable:

```text
R0 = sin rol
R1 = rol genérico
R2 = rol específico
R3 = rol + criterios
R4 = rol + criterios + procedimiento
```

Y medir:

```text
Calidad
Coste
Latencia
Consistencia
```

Esto convierte Prompt Engineering en un proceso experimental.

---

# 71. Nivel avanzado: rol como condicionamiento

Podemos representar conceptualmente la generación:

$$
P(Y|X,R,C)
$$

donde:

* \(Y\) = respuesta;
* \(X\) = solicitud;
* \(R\) = rol;
* \(C\) = contexto.

El rol forma parte de las condiciones sobre las cuales el modelo genera.

Podemos comparar:

$$
P(Y|X,C)
$$

contra:

$$
P(Y|X,R,C)
$$

La diferencia representa conceptualmente el efecto de introducir el rol.

No significa que exista una variable interna independiente llamada "ROL" dentro de todos los modelos.

Es una forma útil de describir el experimento.

---

# 72. Nivel avanzado: rol como información semántica

Desde una perspectiva de representación, el texto:

```text
"analista financiero"
```

produce una representación contextual que se combina con el resto de la entrada.

Podemos simplificar:

```text
"analista financiero"
        ↓
representación
        +
"analiza esta empresa"
        ↓
contexto combinado
        ↓
predicción
```

El modelo no necesita ejecutar una regla explícita como:

```text
IF role == auditor:
    activate_auditor_mode()
```

El comportamiento emerge de cómo el modelo representa y utiliza el contexto.

---

# 73. Nivel experto: rol vs persona

Conviene distinguir:

### Rol

Función dentro de una tarea.

```text
Analista de seguridad.
```

### Persona

Caracterización más amplia de identidad, estilo, valores o comportamiento.

```text
Analista de seguridad metódico,
conciso y orientado a evidencia.
```

Una persona puede contener un rol, pero no son conceptos idénticos.

Para sistemas profesionales, suele ser preferible definir propiedades observables en lugar de construir personajes excesivamente elaborados.

---

# 74. Nivel experto: múltiples perspectivas

En tareas complejas puede ser útil solicitar diferentes perspectivas.

Ejemplo:

```text
Analiza el sistema desde dos perspectivas:

1. ingeniería de software;
2. seguridad.

Después identifica los puntos donde ambas
perspectivas coinciden o entran en conflicto.
```

Esto es más controlable que:

```text
Actúa como 10 expertos simultáneamente.
```

La clave está en estructurar las perspectivas como tareas.

---

# 75. Nivel experto: rol dinámico

En agentes, el rol puede mantenerse constante mientras cambia la tarea:

```text
ROL:
Analista financiero.

Tarea 1:
Buscar anomalías.

Tarea 2:
Comparar periodos.

Tarea 3:
Generar informe.
```

O puede cambiar según la etapa:

```text
Etapa 1 → investigador
Etapa 2 → analista
Etapa 3 → redactor
Etapa 4 → validador
```

Esto puede representarse:

```text
INVESTIGAR
    ↓
ANALIZAR
    ↓
REDACTAR
    ↓
VALIDAR
```

La arquitectura debe definir cuándo y por qué cambia el rol.

---

# 76. Rol dinámico en agentes

Un agente puede tener:

```text
Estado
+
objetivo
+
herramientas
+
contexto
+
rol actual
```

Por ejemplo:

```text
Estado 1:
Rol = investigador

Estado 2:
Rol = analista

Estado 3:
Rol = verificador
```

Sin embargo, cambiar etiquetas de rol no concede automáticamente nuevas capacidades.

Las capacidades siguen dependiendo de:

```text
herramientas
permisos
modelo
arquitectura
```

---

# 77. Nivel PhD: rol y aprendizaje en contexto

Desde una perspectiva de investigación, puede estudiarse cómo las instrucciones de rol interactúan con el **in-context learning**.

Una hipótesis experimental podría ser:

> La incorporación de una descripción de rol modifica la distribución de respuestas al proporcionar señales semánticas sobre la tarea y el comportamiento esperado.

Esto puede evaluarse mediante:

```text
Grupo A:
sin rol

Grupo B:
rol genérico

Grupo C:
rol específico

Grupo D:
rol específico + criterios
```

Manteniendo constantes:

* modelo;
* datos;
* temperatura/configuración;
* longitud;
* salida;
* número de ejemplos.

Después:

```text
comparar métricas
```

Este enfoque evita convertir el Prompt Engineering en una colección de supersticiones.

---

# 78. Nivel PhD: hipótesis sobre el efecto del rol

Podemos formular:

$$
H_0:
R \text{ no produce mejora significativa}
$$

$$
H_1:
R \text{ produce una mejora significativa}
$$

Después se define:

```text
métrica
dataset
modelo
configuración
muestra
criterio de éxito
```

y se realiza el experimento.

Esto introduce una mentalidad científica:

```text
Hipótesis
   ↓
Experimento
   ↓
Medición
   ↓
Análisis
   ↓
Conclusión
```

---

# 79. Principio de mínima especificación suficiente

Una buena regla para diseñar roles es:

> **Utiliza la mínima especificación que proporcione una perspectiva útil para la tarea.**

No:

```text
"El mejor experto mundial..."
```

Sino:

```text
"Analista de seguridad especializado en aplicaciones web."
```

Y si es necesario:

```text
"Prioriza autenticación, autorización y validación de entradas."
```

La meta es aumentar la información útil sin introducir ruido.

---

# 80. Fórmula conceptual

Podemos representar un rol útil como:

$$
ROL =
FUNCIÓN +
DOMINIO +
PERSPECTIVA +
CRITERIOS
$$

Y un prompt profesional como:

$$
PROMPT =
ROL +
OBJETIVO +
INSTRUCCIONES +
CONTEXTO +
RESTRICCIONES +
SALIDA
$$

No son ecuaciones matemáticas del funcionamiento interno del modelo.

Son modelos conceptuales para diseñar prompts de forma sistemática.

---

# 81. Mapa conceptual

```text
                         ROL
                          │
          ┌───────────────┼────────────────┐
          │               │                │
       FUNCIÓN          DOMINIO        PERSPECTIVA
          │               │                │
          └───────────────┼────────────────┘
                          │
                      CRITERIOS
                          │
                          ▼
                       TAREA
                          │
                          ▼
                       MODELO
                          │
                          ▼
                      RESPUESTA
                          │
                          ▼
                      EVALUACIÓN
```

Y dentro de un prompt completo:

```text
                 PROMPT
                    │
     ┌──────────────┼──────────────┐
     │              │              │
    ROL           OBJETIVO     INSTRUCCIÓN
     │              │              │
     └──────────────┼──────────────┘
                    │
                    ▼
                 CONTEXTO
                    │
                    ▼
              RESTRICCIONES
                    │
                    ▼
                  SALIDA
```

---

# 82. Checklist para diseñar un rol

Antes de utilizar un rol, preguntar:

### Función

* ¿Qué función debe desempeñar?

### Dominio

* ¿Qué área conoce o debe priorizar?

### Perspectiva

* ¿Desde qué punto de vista debe analizar?

### Audiencia

* ¿Para quién debe responder?

### Nivel

* ¿Qué profundidad se necesita?

### Criterios

* ¿Qué aspectos debe priorizar?

### Tarea

* ¿Está claro qué debe hacer?

### Contexto

* ¿Tiene la información necesaria?

### Herramientas

* ¿Necesita capacidades externas?

### Permisos

* ¿Las capacidades reales están controladas fuera del prompt?

### Validación

* ¿Podemos medir si el rol realmente mejora el resultado?

---

# 83. Principios fundamentales

### Principio 1

> **Un rol orienta; no transforma mágicamente el modelo.**

### Principio 2

> **Un rol no crea conocimiento.**

### Principio 3

> **Un rol no concede permisos.**

### Principio 4

> **Un rol no sustituye al contexto.**

### Principio 5

> **Un rol no sustituye una metodología.**

### Principio 6

> **Un rol no garantiza exactitud.**

### Principio 7

> **Los roles deben evaluarse experimentalmente.**

### Principio 8

> **Los criterios observables suelen aportar más valor que los títulos grandilocuentes.**

### Principio 9

> **Las capacidades reales pertenecen a la arquitectura del sistema, no a la etiqueta del rol.**

---

# 84. Conclusión

El concepto de rol parece sencillo:

```text
"Actúa como..."
```

pero detrás existe una cuestión más profunda.

El rol es una forma de **condicionamiento contextual**.

Puede orientar:

```text
perspectiva
función
vocabulario
criterios
audiencia
nivel
estilo
```

pero no modifica mágicamente:

```text
parámetros
pesos
entrenamiento
permisos
herramientas
conocimiento actualizado
```

Por eso, un sistema profesional no debería depender de:

```text
"Eres un experto."
```

sino de una composición más completa:

```text
ROL
+
OBJETIVO
+
INSTRUCCIÓN
+
CONTEXTO
+
CRITERIOS
+
RESTRICCIONES
+
SALIDA
+
VALIDACIÓN
```

El concepto fundamental es:

> **El rol define desde qué perspectiva debe abordarse una tarea; no convierte al modelo en la profesión que describe.**

---

# 85. Ejercicio final

Construye tres versiones del mismo prompt.

### Versión A — Sin rol

```text
Analiza este código y encuentra problemas.
```

### Versión B — Rol genérico

```text
Actúa como ingeniero de software.

Analiza este código y encuentra problemas.
```

### Versión C — Rol operacional

```text
Actúa como ingeniero de software especializado
en Python y revisión de código.

Analiza el código desde estas perspectivas:

- corrección;
- seguridad;
- rendimiento;
- mantenibilidad;
- pruebas.

Para cada problema indica:

- ubicación;
- problema;
- impacto;
- recomendación.

Distingue entre errores confirmados y posibles riesgos.
```

Utiliza exactamente:

```text
mismo modelo
mismo código
misma configuración
```

y compara:

```text
precisión
completitud
falsos positivos
claridad
consistencia
coste
```

La finalidad del ejercicio no es demostrar que la versión C siempre será mejor.

La finalidad es descubrir **cuándo, por qué y bajo qué condiciones un rol aporta valor**.

---

## Idea para recordar

```text
ROL
  ↓
¿Desde qué perspectiva?

OBJETIVO
  ↓
¿Qué queremos conseguir?

INSTRUCCIÓN
  ↓
¿Qué debe hacer?

CONTEXTO
  ↓
¿Qué necesita saber?

RESTRICCIONES
  ↓
¿Qué límites debe respetar?

SALIDA
  ↓
¿Cómo debe responder?

VALIDACIÓN
  ↓
¿La respuesta es correcta?
```

Y la regla más importante:

> **No le digas al modelo simplemente quién quieres que "sea". Define qué perspectiva debe utilizar, qué debe hacer, qué información tiene, qué criterios debe aplicar y cómo se evaluará el resultado.**
