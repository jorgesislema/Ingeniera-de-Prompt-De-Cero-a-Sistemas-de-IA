# Prompting vs Fine-Tuning vs RAG: Marco de Decision

> Julio 2026 | Tiempo estimado de lectura: 25 minutos | Nivel: Intermedio

---

## 1. INTRODUCCION

### 1.1 Por que este conocimiento es critico

En 2026, los modelos de lenguaje han alcanzado un nivel de sofisticacion sin precedentes. Sin embargo, la pregunta fundamental sigue siendo la misma: cual es la forma mas efectiva de hacer que un LLM resuelva mi problema concreto?

Elegir incorrectamente entre prompting, fine-tuning y RAG puede costarle a una organizacion meses de trabajo perdido, cientos de miles de dolares en computo innecesario, o peor aun: un sistema que funciona en pruebas pero falla estrepitosamente en produccion.

Este documento proporciona un marco de decision practico, basado en experiencia de campo con cientos de implementaciones empresariales, para que puedas tomar la decision correcta en cada contexto.

### 1.2 La analogia del chef

Imagina que necesitas preparar una cena para 100 personas. Tienes tres opciones para trabajar con tu chef:

**Prompting (dar instrucciones):** Le dices al chef exactamente lo que quieres. "Prepara un risotto cremoso, con esparragos frescos, sin ajo, para 100 personas. Usa arroz arborio. La textura debe ser al dente." El chef ya sabe cocinar; solo necesita instrucciones claras. Es rapido, flexible y no requiere entrenamiento previo. Pero si necesitas que cocine exactamente igual 1000 veces, o si tu cocina es muy especializada, las instrucciones pueden volverse larguisimas o inconsistentes.

**Fine-Tuning (mandar a la escuela):** Envias a tu chef a una escuela culinaria especializada en cocina molecular durante 3 meses. Cuando regresa, cocina exactamente como necesitas sin que tengas que darle instrucciones detalladas cada vez. Es consistente, eficiente y entiende profundamente tu estilo. Pero toma tiempo, cuesta dinero, y si cambias de tipo de cocina, tienes que volver a entrenarlo.

**RAG (dar un libro de recetas):** Le das a tu chef acceso a una biblioteca completa de recetas y libros de cocina. Cuando necesita preparar algo, consulta los libros en tiempo real. No necesita saberlo todo de memoria; solo necesita saber buscar. Es ideal cuando la informacion cambia constantemente (nuevas recetas cada dia) o cuando el volumen de conocimiento es demasiado grande para memorizarlo.

---

## 2. LAS TRES HERRAMIENTAS EXPLICADAS

### 2.1 Prompt Engineering

**Que es:**

El arte y la ciencia de disenar instrucciones textuales que guian a un modelo de lenguaje pre-entrenado hacia el comportamiento deseado, sin modificar sus pesos ni anadir fuentes externas de datos.

Tecnicas principales en 2026:
- Zero-shot prompting: instrucciones directas sin ejemplos
- Few-shot prompting: incluir 2-20 ejemplos en el prompt
- Chain-of-Thought (CoT): forzar razonamiento paso a paso
- Structured Output: JSON mode, function calling, grammars
- System prompting: instrucciones de alto nivel que enmarcan el comportamiento
- Prompt chaining: dividir tareas complejas en multiples llamadas encadenadas
- Meta-prompting: usar un LLM para generar o mejorar prompts

**Cuando brilla:**

- Tareas de proposito general donde el modelo ya tiene buen rendimiento base
- Prototipado rapido y experimentacion (iteras en minutos, no en horas)
- Cuando tienes pocos ejemplos etiquetados (menos de 50)
- Escenarios donde los requisitos cambian frecuentemente
- Tareas creativas o abiertas donde la consistencia no es critica
- Cuando el costo inicial debe ser cercano a cero
- Sistemas multi-proposito donde un mismo modelo resuelve tareas diversas

**Limitaciones:**

- Dependencia total de lo que el modelo "ya sabe" de su pre-entrenamiento
- Los prompts muy largos aumentan la latencia y el costo por llamada
- Comportamiento inconsistente entre versiones del mismo modelo
- Dificil garantizar formatos de salida exactos sin structured output
- Vulnerable a prompt injection y jailbreaking
- La calidad depende fuertemente de la habilidad del prompt engineer
- No escala bien para tareas que requieren conocimiento muy especializado
- Ventana de contexto limitada (aunque en 2026 ya existen modelos con 1M+ tokens, el costo y latencia crecen con el tamano del prompt)

### 2.2 Fine-Tuning

**Que es:**

Proceso de continuar el entrenamiento de un modelo base con un conjunto de datos especifico y curado, modificando sus pesos para especializarlo en un dominio o comportamiento particular.

Tipos principales en 2026:
- **Full fine-tuning:** Actualizar todos los pesos del modelo. Maximo control, maximo costo. Raramente usado fuera de labs de investigacion.
- **LoRA (Low-Rank Adaptation):** Anadir matrices entrenables de bajo rango. El estandar de facto en 2026. Eficiente, portable, compatible con multi-adapter serving.
- **QLoRA:** LoRA con cuantizacion a 4 bits. Permite fine-tuning en hardware de consumo.
- **DPO (Direct Preference Optimization):** Entrenar con pares de preferencias (respuesta buena vs mala). Ideal para ajustar estilo, tono, y comportamiento.
- **Instruction tuning:** Ensenar al modelo a seguir instrucciones en un formato especifico.
- **Domain-adaptive pre-training (DAPT):** Continuar pre-entrenamiento en un corpus de dominio antes del fine-tuning supervisado.

**Cuando brilla:**

- Necesitas un comportamiento MUY consistente y predecible
- Tienes un dataset curado de al menos 500-1000 ejemplos de alta calidad
- El dominio es muy especializado y el modelo base tiene poco conocimiento (ej. documentos legales de una jurisdiccion especifica, protocolos medicos propietarios)
- Quieres reducir la latencia eliminando la necesidad de prompts largos con muchos ejemplos
- Necesitas que el modelo siga un formato de salida especifico el 99.9% de las veces
- Quieres "enseñar" un estilo, tono o personalidad particular
- El costo por inferencia debe ser minimizado (modelos fine-tuned pueden ser mas pequeños para la misma tarea)

**Limitaciones:**

- Requiere un dataset de entrenamiento curado y de alta calidad (garbage in, garbage out)
- Costo inicial significativo: preparacion de datos + computo de entrenamiento + evaluacion
- El modelo se vuelve "fragil": excelente en su dominio, peor en tareas generales (catastrophic forgetting)
- Cada actualizacion del modelo base requiere potencialmente re-entrenar
- Dificil de iterar rapidamente: cada cambio requiere re-entrenar y re-evaluar
- Requiere conocimiento tecnico de ML: preparacion de datos, hiperparametros, evaluacion
- Los datos de entrenamiento pueden contener sesgos que el modelo amplificara
- Gobernanza y compliance mas compleja (que datos se usaron para entrenar?)

### 2.3 RAG (Retrieval-Augmented Generation)

**Que es:**

Arquitectura que combina un sistema de recuperacion de informacion (retrieval) con un modelo generativo. Cuando llega una consulta, el sistema primero busca documentos relevantes en una base de conocimiento externa, y luego inyecta esos documentos en el prompt del LLM para que genere una respuesta fundamentada.

Componentes clave en 2026:
- **Pipeline de ingestion:** Chunking estrategico, embeddings, almacenamiento vectorial
- **Estrategia de retrieval:** BM25 + denso (hibrido), re-ranking, query expansion, multi-hop retrieval
- **Generacion:** El LLM recibe query + documentos recuperados y genera respuesta con citas
- **Evaluacion:** Metrics de fidelidad (faithfulness), relevancia, y precision contextual

Enfoques avanzados en 2026:
- **Agentic RAG:** El modelo decide que buscar, cuando buscar, y como usar lo encontrado
- **Graph RAG:** Recuperacion sobre grafos de conocimiento en lugar de documentos planos
- **Multi-modal RAG:** Recuperacion de texto, imagenes, tablas, y codigo simultaneamente
- **RAPTOR:** Recuperacion jerarquica con resumenes a multiples niveles
- **Self-RAG:** El modelo evalua si necesita buscar y si lo encontrado es relevante
- **Corrective RAG:** Itera entre busqueda y generacion para refinar resultados

**Cuando brilla:**

- Necesitas acceso a informacion actualizada en tiempo real (ej. noticias, precios, clima)
- El volumen de conocimiento es demasiado grande para caber en una ventana de contexto
- La informacion cambia frecuentemente y no quieres re-entrenar
- Requieres trazabilidad: cada afirmacion debe estar respaldada por una fuente
- La base de conocimiento es propietaria y confidencial (no quieres que pase por fine-tuning)
- Necesitas control de acceso a nivel de documento (ciertos usuarios ven ciertos documentos)
- El dominio es amplio pero la informacion esta documentada
- Requieres alta precision factual (alucinaciones minimizadas al tener fuentes a mano)

**Limitaciones:**

- Agrega latencia significativa (busqueda + reranking + generacion)
- La calidad depende criticamente de la calidad del retrieval (garbage retrieved, garbage generated)
- Mayor complejidad operacional: mantener indices vectoriales, pipelines de ingestion, sincronizacion
- Costo operacional continuo: embeddings, almacenamiento vectorial, re-rankeo
- El chunking incorrecto puede romper la coherencia de la informacion
- Dificil manejar preguntas que requieren sintesis de multiples documentos dispersos
- La ventana de contexto se llena con documentos recuperados, dejando menos espacio para la respuesta
- No mejora el "razonamiento" del modelo, solo le da mejor informacion

---

## 3. MATRIZ DE DECISION

### 3.1 Arbol de decision principal

```
                              [Tu problema]
                                   |
                                   v
                    +------------------------------+
                    | Necesitas datos externos o   |
                    | informacion actualizada      |
                    | que el modelo no tiene?      |
                    +------------------------------+
                            |               |
                           SI              NO
                            |               |
                            v               v
                    +-------------+  +---------------------------+
                    | Usar RAG o  |  | Necesitas comportamiento  |
                    | Prompting   |  | extremadamente consistente|
                    | con RAG     |  | y predecible (+99% igual) |
                    +-------------+  | en cada ejecucion?        |
                                     +---------------------------+
                                              |            |
                                             SI           NO
                                              |            |
                                              v            v
                                     +------------+  +-------------------+
                                     | Fine-Tuning|  | Tienes al menos   |
                                     | (LoRA/DPO) |  | 500-1000 ejemplos |
                                     +------------+  | de alta calidad?  |
                                                     +-------------------+
                                                              |        |
                                                             SI       NO
                                                              |        |
                                                              v        v
                                                     +------------+ +----------+
                                                     | Fine-Tuning| | Prompting|
                                                     | o Prompting| | (Few-Shot|
                                                     | avanzado   | | o CoT)   |
                                                     +------------+ +----------+
```

### 3.2 Arbol de decision extendido

```
                              [INICIO]
                                 |
                                 v
                    +--------------------------+
                    | El conocimiento necesario|
                    | cambia cada dia/semana?  |
                    +--------------------------+
                         |             |
                        SI            NO
                         |             |
                         v             v
                    +--------+  +--------------------------+
                    |  RAG   |  | El comportamiento deseado |
                    +--------+  | es un formato, estilo o   |
                                | tono MUY especifico?      |
                                +--------------------------+
                                     |             |
                                    SI            NO
                                     |             |
                                     v             v
                              +-----------+ +----------------------+
                              |Fine-Tuning| | La tarea requiere     |
                              |(DPO/LoRA) | | razonamiento complejo |
                              +-----------+ | multi-paso?           |
                                            +----------------------+
                                                 |          |
                                                SI         NO
                                                 |          |
                                                 v          v
                                          +----------+ +------------+
                                          |Prompting | | Tienes     |
                                          |(CoT/ReAct| | ejemplos?  |
                                          |+ tools)  | +------------+
                                          +----------+   |     |    |
                                                        <50  50-500 >500
                                                         |     |     |
                                                         v     v     v
                                                      +-----+ +---+ +-----+
                                                      |Few- | |Few| |Fine-|
                                                      |Shot | |-  | |Tuning|
                                                      |     | |Shot| |     |
                                                      +-----+ |Ft?| +-----+
                                                              +---+
```

### 3.3 Matriz de decision por sector

```
+---------------------------+----------+-------------+------+
| SECTOR                    | PROMPTING| FINE-TUNING | RAG  |
+---------------------------+----------+-------------+------+
| Atencion al cliente       | Prototipo| Produccion  | Freq |
| Documentacion legal       | Bajo     | Medio       | Alto |
| Diagnostico medico        | Bajo     | Alto        | Alto |
| Generacion de contenido   | Alto     | Medio       | Bajo |
| Analisis financiero       | Medio    | Medio       | Alto |
| Educacion personalizada   | Medio    | Alto        | Alto |
| Busqueda empresarial      | Bajo     | Bajo        | Alto |
| Codigo y desarrollo       | Alto     | Medio       | Medio|
| Traduccion especializada  | Medio    | Alto        | Bajo |
| Chatbots de ventas        | Medio    | Alto        | Alto |
+---------------------------+----------+-------------+------+
```

---

## 4. TABLA COMPARATIVA DETALLADA

### 4.1 Comparacion multidimensional

```
+---------------------+------------------+------------------+------------------+
| DIMENSION           | PROMPTING        | FINE-TUNING      | RAG              |
+---------------------+------------------+------------------+------------------+
| Costo inicial       | $0 - $500        | $5,000 - $50,000 | $2,000 - $20,000 |
|                     | (tiempo humano)  | (datos + GPU)    | (infra + datos)  |
+---------------------+------------------+------------------+------------------+
| Costo mensual       | $100 - $5,000    | $500 - $3,000    | $1,000 - $10,000 |
| (operacional)       | (solo inferencia)| (inferencia)     | (infra + infer)  |
+---------------------+------------------+------------------+------------------+
| Velocidad de        | Minutos a horas  | Dias a semanas   | Horas a dias     |
| iteracion           |                  |                  |                  |
+---------------------+------------------+------------------+------------------+
| Precision factual   | Media (60-85%)   | Alta (85-95%)    | Muy alta (90-99%)|
| (con buenos datos)  |                  |                  |                  |
+---------------------+------------------+------------------+------------------+
| Consistencia        | Baja-Media       | Muy alta         | Media-Alta       |
| de comportamiento   | (depende prompt) |                  |                  |
+---------------------+------------------+------------------+------------------+
| Flexibilidad        | Muy alta         | Baja             | Alta             |
| (cambiar tarea)     |                  |                  |                  |
+---------------------+------------------+------------------+------------------+
| Datos necesarios    | 0-50 ejemplos    | 500-10,000+      | Documentos       |
|                     |                  | ejemplos         | (cualquier cant) |
+---------------------+------------------+------------------+------------------+
| Conocimiento tecnico| Bajo-Medio       | Alto             | Medio-Alto       |
| requerido           |                  |                  |                  |
+---------------------+------------------+------------------+------------------+
| Latencia por llamada| Baja (0.5-3s)   | Baja (0.5-3s)    | Alta (2-10s)     |
+---------------------+------------------+------------------+------------------+
| Trazabilidad de     | Ninguna          | Baja (datos de  | Alta (citas a    |
| fuentes             |                  | entrenamiento)   | documentos)      |
+---------------------+------------------+------------------+------------------+
| Actualizacion de    | Inmediata        | Requiere re-     | Inmediata (al    |
| conocimiento        | (cambiar prompt) | entrenamiento    | actualizar docs) |
+---------------------+------------------+------------------+------------------+
| Privacidad de datos | Depende del      | Datos en el      | Datos en tu      |
|                     | proveedor        | modelo (riesgo)  | infraestructura  |
+---------------------+------------------+------------------+------------------+
| Escalabilidad       | Excelente        | Buena            | Compleja         |
|                     | (API llama)      | (serving)        | (indices+API)    |
+---------------------+------------------+------------------+------------------+
```

### 4.2 Comparacion de riesgos

```
+---------------------+------------------+------------------+------------------+
| RIESGO              | PROMPTING        | FINE-TUNING      | RAG              |
+---------------------+------------------+------------------+------------------+
| Alucinaciones       | Riesgo alto      | Riesgo medio     | Riesgo bajo      |
|                     |                  |                  | (si retrieval OK)|
+---------------------+------------------+------------------+------------------+
| Prompt injection    | Riesgo alto      | Riesgo medio     | Riesgo medio-alto|
+---------------------+------------------+------------------+------------------+
| Deriva del modelo   | Moderado         | Alto (catastro-  | Moderado         |
| (model drift)       | (cambia version) | phic forgetting) |                  |
+---------------------+------------------+------------------+------------------+
| Fuga de datos       | Bajo (no guarda) | Alto (pesos      | Depende de la    |
|                     |                  | guardan info)    | infraestructura  |
+---------------------+------------------+------------------+------------------+
| Obsolescencia del   | Bajo (cambias    | Alto (reentrenar | Bajo (cambias    |
| conocimiento        | el prompt)       | cada vez)        | documentos)      |
+---------------------+------------------+------------------+------------------+
```

---

## 5. CUANDO COMBINARLAS

### 5.1 Prompting + RAG (El patron empresarial mas comun)

Este es el punto de partida para la mayoria de aplicaciones empresariales en 2026.

```
  [Usuario] --> [Consulta]
                    |
                    v
  [Base de conocimiento] --> [Embeddings + Busqueda vectorial]
                    |                    |
                    v                    v
            [Documentos relevantes] --> [Prompt construido]
                                             |
                                             v
                                      [LLM (API)] --> [Respuesta con citas]
```

**Por que funciona:**
- El prompting da la flexibilidad y el RAG da el conocimiento actualizado
- No necesitas modificar el modelo
- Las fuentes son auditables
- Iteras los prompts independientemente de la base de conocimiento

**Ejemplo concreto:**
Chatbot de soporte de una empresa de software. La base de conocimiento contiene la documentacion del producto (que cambia cada semana). El prompt define el tono, el formato de respuesta, y las reglas de escalacion. Cuando un usuario pregunta, el sistema busca en la documentacion actualizada y construye una respuesta fundamentada.

### 5.2 Fine-Tuning + RAG (Conocimiento especializado + datos frescos)

El patron mas potente para aplicaciones de alta precision.

```
  [Documentos de dominio] --> [Fine-Tuning del modelo]
                                      |
                                      v
                            [Modelo especializado]
                                      |
  [Base de conocimiento dinamica] --> [RAG]
                                      |
                                      v
                            [Sistema completo]
```

**Por que funciona:**
- El fine-tuning le da al modelo un entendimiento profundo del dominio (terminologia, estructura de documentos, formatos)
- El RAG le da acceso a datos actualizados y especificos
- El modelo fine-tuned "entiende mejor" los documentos que recupera, produciendo respuestas mas precisas

**Ejemplo concreto:**
Asistente medico especializado en oncologia. El modelo se fine-tunea con literatura medica, protocolos de tratamiento, y casos clinicos para entender profundamente el dominio. El RAG le da acceso al historial del paciente actual, resultados de laboratorio recientes, y guias clinicas actualizadas. El resultado: un modelo que "piensa" como oncologo y "sabe" del paciente especifico.

### 5.3 Prompting + Fine-Tuning (El modelo fine-tuned sigue necesitando buenos prompts)

Un error comun es asumir que un modelo fine-tuned no necesita prompt engineering.

**Realidad:** Los modelos fine-tuned son mas faciles de promptear (requieren menos ejemplos, menos instrucciones), pero:
- Siguen beneficiandose de prompts bien estructurados
- Siguen siendo sensibles a la formulacion de instrucciones
- Los system prompts definen el comportamiento en produccion

```
  [Fine-Tuning] --> Especializa el modelo en el dominio
  [Prompting]   --> Define la tarea especifica, tono, formato, restricciones
```

**Estrategia recomendada:**
1. Primero, fine-tunea el modelo con tus datos de dominio
2. Luego, disena prompts optimizados para el modelo fine-tuned
3. Itera los prompts mucho mas rapido que el fine-tuning

### 5.4 Las tres juntas (El stack completo)

Para aplicaciones criticas de alta exigencia:

```
+-------------------------------------------------------------+
|                    STACK COMPLETO AI                         |
|                                                              |
|  [Fine-Tuned Model]  <-- Entendimiento profundo del dominio |
|         +                                                    |
|  [RAG Pipeline]      <-- Datos actualizados y especificos   |
|         +                                                    |
|  [Prompt Engineering] <-- Control de comportamiento fino    |
|         +                                                    |
|  [Guardrails]         <-- Seguridad y compliance            |
|         +                                                    |
|  [Observabilidad]     <-- Monitoreo y evaluacion continua    |
|                                                              |
+-------------------------------------------------------------+
```

**Ejemplo concreto:**
Plataforma de analisis legal para un bufete de abogados:
- **Fine-Tuning:** El modelo se entrena con miles de documentos legales del bufete, entendiendo su estilo, jurisdiccion, y areas de practica
- **RAG:** Se conecta a bases de datos de jurisprudencia actualizada, nuevas leyes, y regulaciones
- **Prompting:** Instrucciones precisas sobre formato de analisis, nivel de detalle, y estructura del output
- **Guardrails:** Filtros para no revelar informacion de clientes, no dar consejo legal directo
- **Observabilidad:** Monitoreo de alucinaciones, citas incorrectas, y satisfaccion del abogado

---

## 6. CASOS DE ESTUDIO REALES

### 6.1 Caso 1: Startup con cero datos

**Contexto:** Una startup de 3 personas construyendo un asistente para generar descripciones de productos de e-commerce. No tienen datos etiquetados, no tienen clientes aun, presupuesto limitado ($500/mes).

**Enfoque elegido: Prompting (Zero-shot + Few-shot)**

**Por que:**
- Sin datos etiquetados, el fine-tuning es imposible
- Sin base de conocimiento, RAG no aplica
- Necesitan iterar rapidamente basado en feedback de primeros usuarios
- El presupuesto no permite infraestructura de fine-tuning ni RAG

**Implementacion:**
```
  [Plantilla de prompt base]
  "Eres un redactor de e-commerce experto. Genera una descripcion de producto
  basada en los siguientes atributos.

  Producto: {nombre}
  Categoria: {categoria}
  Caracteristicas clave: {features}
  Publico objetivo: {audiencia}
  Tono: {tono}

  Ejemplo de descripcion deseada:
  [2-3 ejemplos cuidadosamente seleccionados]

  Genera la descripcion:"
```

**Resultados:**
- Tiempo hasta MVP: 2 semanas
- Costo mensual: $300 en llamadas API
- Iteraciones de prompt: 15 en el primer mes
- Satisfaccion de usuarios iniciales: 78%
- Siguiente paso planeado: Fine-tuning cuando acumulen 1000+ descripciones aprobadas

### 6.2 Caso 2: Empresa con 10,000 ejemplos de soporte

**Contexto:** Una empresa de SaaS con 5 años de tickets de soporte resueltos. 10,000 pares (consulta del cliente, respuesta del agente) de alta calidad. Quieren automatizar el 60% de las consultas de nivel 1.

**Enfoque elegido: Fine-Tuning con LoRA**

**Por que:**
- Tienen un dataset grande y curado de ejemplos reales
- El formato de respuesta es muy consistente (los agentes siguen guias)
- La terminologia es especifica del producto
- Necesitan respuestas consistentes y predecibles
- El fine-tuning reducira la latencia al eliminar la necesidad de ejemplos en el prompt

**Implementacion:**
```
  [Preparacion de datos]
  1. Limpieza: eliminar tickets con datos personales, tickets duplicados
  2. Formateo: convertir a formato de chat (system: "Eres un agente de soporte...",
     user: consulta, assistant: respuesta)
  3. Curacion: filtrar respuestas con baja satisfaccion del cliente
  4. Aumentacion: generar variaciones para casos poco frecuentes
  5. Split: 80% train, 10% val, 10% test

  [Entrenamiento]
  - Modelo base: GPT-4o-mini (por costo/rendimiento)
  - Metodo: LoRA rank 16
  - Hiperparametros: learning rate 2e-4, 3 epoch, batch size 8
  - Tiempo de entrenamiento: 4 horas en 1x A100

  [Evaluacion]
  - Exact match de formato: 95%
  - Evaluacion humana de calidad: 8.7/10
  - Reduccion de alucinaciones vs prompting base: 60%
```

**Resultados:**
- Automatizacion alcanzada: 55% de tickets nivel 1
- Tiempo de respuesta: de 4 horas a 30 segundos
- Costo del fine-tuning: $1,200 (computo + ingenieria)
- ROI: positivo en el primer mes
- Mantenimiento: re-entrenamiento trimestral con nuevos tickets

### 6.3 Caso 3: Chatbot legal con leyes actualizadas

**Contexto:** Un despacho de abogados necesita un asistente que responda preguntas sobre legislacion laboral. Las leyes cambian cada mes, hay miles de paginas de regulaciones, y la precision factual es critica.

**Enfoque elegido: RAG con re-ranking**

**Por que:**
- Las leyes cambian constantemente (imposible fine-tuning frecuente)
- El volumen de documentos es enorme (varias GB de texto legal)
- Cada respuesta debe citar el articulo exacto de la ley
- No tienen suficientes ejemplos etiquetados para fine-tuning
- La precision factual es critica (un error legal puede tener consecuencias graves)

**Implementacion:**
```
  [Pipeline de ingestion]
  1. Recoleccion: scrapers de boletines oficiales, feeds RSS legislativos
  2. Chunking: segmentacion por articulo legal (chunks de 500-1000 tokens)
     con solapamiento estrategico para no cortar definiciones
  3. Embeddings: text-embedding-3-large con metadata enriquecida
     (jurisdiccion, fecha, tipo de norma)
  4. Almacenamiento: Pinecone con indices separados por rama del derecho

  [Pipeline de consulta]
  1. Query rewriting: reformular la pregunta del abogado para mejorar retrieval
  2. Busqueda hibrida: BM25 + embeddings con peso 0.3/0.7
  3. Re-ranking: Cohere Rerank para seleccionar top-5 chunks mas relevantes
  4. Generacion: GPT-4o con instrucciones estrictas de citacion
  5. Verificacion: comprobar que cada afirmacion tiene respaldo en los chunks
```

**Resultados:**
- Precision factual: 94% (vs 72% con prompting puro)
- Cobertura de legislacion: 15 jurisdicciones, 50,000+ documentos
- Tiempo de actualizacion: nuevas leyes disponibles en < 1 hora
- Costo operacional mensual: $3,500 (embeddings + busqueda + inferencia)
- Adopcion: 85 abogados usando el sistema diariamente

### 6.4 Caso 4: Asistente medico especializado

**Contexto:** Un sistema de salud privado quiere un asistente que ayude a medicos en consulta. Necesita conocimiento medico profundo (fine-tuning) combinado con datos del paciente actual (RAG sobre el historial clinico).

**Enfoque elegido: Fine-Tuning + RAG**

**Por que:**
- Conocimiento medico profundo: el modelo base no tiene suficiente precision en diagnostico diferencial
- Datos del paciente: historial clinico, resultados de laboratorio, alergias (cambian cada consulta)
- La combinacion permite "pensar como medico" (fine-tuning) + "conocer al paciente" (RAG)

**Implementacion:**
```
  FASE 1 - Fine-Tuning (3 meses)
  - Dataset: 50,000 historias clinicas anonimizadas con diagnosticos confirmados
  - Objetivo: enseñar al modelo el proceso de razonamiento clinico
  - Evaluacion medica: panel de 5 medicos revisando 500 casos

  FASE 2 - RAG Pipeline (2 meses)
  - Integracion con el EHR (Electronic Health Record) del hospital
  - Pipeline que recupera: historial del paciente, ultimos examenes,
    medicaciones actuales, alergias, factores de riesgo
  - Actualizacion en tiempo real cuando el medico anota algo en el EHR

  FASE 3 - Sistema integrado
  [Medico hace consulta]
       |
       v
  [RAG recupera datos del paciente]
       |
       v
  [Prompt construido con datos paciente + instrucciones clinicas]
       |
       v
  [Modelo fine-tuned genera analisis]
       |
       v
  [Respuesta con: posibles diagnosticos, examenes sugeridos,
   interacciones medicamentosas, referencias a guias clinicas]
```

**Resultados:**
- Precision diagnostica: comparable a medico con 5 años de experiencia
- Reduccion de errores de prescripcion: 35%
- Tiempo ahorrado por consulta: 8 minutos
- Costo total del proyecto: $180,000 (datos + entrenamiento + integracion)
- ROI anual estimado: $1.2M en eficiencia operativa
- Cumplimiento regulatorio: HIPAA, GDPR, con auditoria completa

---

## 7. CALCULO DE COSTOS

### 7.1 Costo por llamada segun enfoque

```
+-----------------------------+------------+-------------+------------+
| ESCALA                      | PROMPTING  | FINE-TUNING | RAG        |
+-----------------------------+------------+-------------+------------+
| 100 llamadas/dia            | $3/dia     | $2/dia      | $8/dia     |
| (3,000/mes)                 | $90/mes    | $60/mes     | $240/mes   |
+-----------------------------+------------+-------------+------------+
| 1,000 llamadas/dia          | $30/dia    | $15/dia     | $60/dia    |
| (30,000/mes)                | $900/mes   | $450/mes    | $1,800/mes |
+-----------------------------+------------+-------------+------------+
| 10,000 llamadas/dia         | $300/dia   | $120/dia    | $500/dia   |
| (300,000/mes)               | $9,000/mes | $3,600/mes  | $15,000/mes|
+-----------------------------+------------+-------------+------------+
| 100,000 llamadas/dia        | $3,000/dia | $800/dia    | $4,000/dia |
| (3,000,000/mes)             | $90,000/mes| $24,000/mes | $120,000/mes|
+-----------------------------+------------+-------------+------------+
```

*Nota: Estimaciones basadas en GPT-4o-mini para prompting/fine-tuning y pipeline de RAG con embeddings Ada + busqueda Pinecone + generacion GPT-4o-mini. Julio 2026.*

### 7.2 Analisis de punto de equilibrio (Break-even)

El fine-tuning tiene un costo inicial alto pero costo por llamada mas bajo. Existe un punto donde el fine-tuning se vuelve mas economico que el prompting con prompts largos.

```
Costo acumulado
    ^
    |                          /
    |                         /
    |                        /  Fine-Tuning (menor costo/llamada)
    |                       /
    |                      /-------
    |                     /|
    |                    / |
    |                   /  |
    |                  /   |
    |   Prompting ____/    |
    |   (mayor costo/  |   |
    |    llamada)      |   |
    |                  |   |
    +------------------+----+---------> Numero de llamadas
                        ^
                  Punto de equilibrio
                  (~50,000-200,000 llamadas)
```

**Calculo concreto (ejemplo):**

Prompting con prompt largo (~2000 tokens de entrada, 500 tokens de salida):
- Costo por llamada: $0.0035 (GPT-4o-mini)
- Sin costo inicial

Fine-Tuning con LoRA (modelo fine-tuned, prompt corto ~200 tokens):
- Costo por llamada: $0.0007 (mismo modelo, menos tokens de entrada)
- Costo inicial: $10,000 (preparacion datos + entrenamiento + evaluacion)

**Punto de equilibrio:** $10,000 / ($0.0035 - $0.0007) = ~3,571,000 llamadas

Sin embargo, si el prompting requiere GPT-4o completo para lograr la calidad necesaria:
- Costo por llamada prompting: $0.03
- Costo por llamada fine-tuning (GPT-4o-mini): $0.0007
- Punto de equilibrio: $10,000 / ($0.03 - $0.0007) = ~341,000 llamadas

**Conclusion:** El fine-tuning es economicamente ventajoso cuando:
1. El volumen de llamadas es alto (300K+ por mes)
2. El prompting requiere modelos mas caros para lograr la calidad deseada
3. Los prompts son muy largos (muchos ejemplos few-shot o contexto extenso)

### 7.3 Costos ocultos frecuentemente ignorados

```
+-----------------------------+------------+-------------+------------+
| COSTO OCULTO                | PROMPTING  | FINE-TUNING | RAG        |
+-----------------------------+------------+-------------+------------+
| Ingenieria de datos         | Bajo       | Alto        | Alto       |
| (limpieza, curacion)        |            |             |            |
+-----------------------------+------------+-------------+------------+
| Evaluacion y testing        | Medio      | Alto        | Medio-Alto |
+-----------------------------+------------+-------------+------------+
| Mantenimiento continuo      | Bajo       | Medio-Alto  | Alto       |
+-----------------------------+------------+-------------+------------+
| Onboarding del equipo       | Bajo       | Alto        | Medio      |
+-----------------------------+------------+-------------+------------+
| Monitoreo en produccion     | Medio      | Alto        | Alto       |
+-----------------------------+------------+-------------+------------+
| Cumplimiento/Compliance     | Bajo       | Alto        | Medio      |
+-----------------------------+------------+-------------+------------+
| Cambios de modelo base      | Bajo       | Muy alto    | Bajo-Medio |
| (migracion a nueva version) |            | (reentrenar)|            |
+-----------------------------+------------+-------------+------------+
```

---

## 8. EJERCICIO DE DECISION

### Instrucciones

Para cada uno de los siguientes 5 escenarios, determina que enfoque (o combinacion) utilizarias y justifica tu decision en 3-5 lineas. Considera: costo, tiempo, datos disponibles, consistencia requerida, y frecuencia de actualizacion del conocimiento.

### Escenario 1: Startup de recetas de cocina con IA

Eres el CTO de una startup que genera recetas personalizadas basadas en preferencias dieteticas y restricciones alimentarias del usuario. Tienes 3 meses para lanzar MVP. Presupuesto: $2,000. No tienes datos de recetas (las generas desde cero). Los usuarios esperan recetas creativas y variadas, no necesariamente identicas cada vez.

**Que enfoque usarias?**

```
Tu respuesta:
Enfoque elegido:

Justificacion:
```

### Escenario 2: Chatbot de compliance bancario

Trabajas en un banco que necesita un chatbot interno para que empleados consulten politicas y procedimientos de compliance. Hay 15,000 paginas de documentacion regulatoria que se actualizan semanalmente. El chatbot NUNCA debe inventar una politica. Cada respuesta debe citar el documento fuente exacto. Hay 5,000 empleados que lo usaran.

**Que enfoque usarias?**

```
Tu respuesta:
Enfoque elegido:

Justificacion:
```

### Escenario 3: Corrector de estilo para editorial

Una editorial quiere automatizar la correccion de estilo de manuscritos. Tienen 50,000 manuscritos historicos con las correcciones que hicieron editores humanos. El estilo es muy especifico (uso de comas, voz pasiva, longitud de parrafos, terminologia). El resultado debe ser EXTREMADAMENTE consistente. Las reglas de estilo cambian muy poco (una vez al año).

**Que enfoque usarias?**

```
Tu respuesta:
Enfoque elegido:

Justificacion:
```

### Escenario 4: Asistente de investigacion cientifica

Un laboratorio farmaceutico necesita un asistente que ayude a cientificos a revisar literatura, encontrar papers relevantes, y sugerir hipotesis basadas en investigacion reciente. Cada dia se publican 5,000+ nuevos papers en biomedicina. Los cientificos necesitan respuestas con referencias precisas. A veces necesitan sintesis de hallazgos contradictorios entre multiples papers.

**Que enfoque usarias?**

```
Tu respuesta:
Enfoque elegido:

Justificacion:
```

### Escenario 5: Sistema de clasificacion de emails

Necesitas construir un sistema que clasifique emails entrantes en 47 categorias distintas para un sistema de atencion al cliente empresarial. Tienes 2 millones de emails historicos etiquetados con la categoria correcta. El sistema debe clasificar con +98% de precision. Procesa 500,000 emails al dia. Las categorias cambian trimestralmente.

**Que enfoque usarias?**

```
Tu respuesta:
Enfoque elegido:

Justificacion:
```

### Respuestas recomendadas (para el instructor)

**Escenario 1:** Prompting (Few-shot). Sin datos para fine-tuning, sin documentos para RAG. Presupuesto limitado. La tarea es creativa y la variabilidad es deseable. Iterar prompts cuesta minutos, no dias.

**Escenario 2:** RAG puro. Documentacion que cambia semanalmente descarta fine-tuning. La necesidad de citas exactas y el volumen de documentos hacen que RAG sea la unica opcion viable. La auditoria de fuentes es obligatoria.

**Escenario 3:** Fine-Tuning (LoRA/DPO). 50,000 ejemplos de alta calidad, comportamiento extremadamente consistente requerido, reglas estables. El fine-tuning producira el comportamiento mas predecible. Las reglas cambian poco, asi que el re-entrenamiento es infrecuente.

**Escenario 4:** RAG + Agentes. El volumen de nuevos papers diarios hace imposible el fine-tuning. Se necesita RAG con re-ranking avanzado. Agentes que puedan buscar en multiples fuentes y sintetizar hallazgos contradictorios.

**Escenario 5:** Fine-Tuning. El volumen de datos (2M) y la necesidad de precision (+98%) apuntan a fine-tuning. La clasificacion de texto es una de las tareas donde el fine-tuning consistentemente supera al prompting. El re-entrenamiento trimestral es manejable con ese presupuesto.

---

## RESUMEN VISUAL

```
+----------------------------+
| ¿Cual enfoque usar?        |
+----------------------------+
|                            |
| SIN DATOS + CREATIVO       |
| --> Prompting              |
|                            |
| MUCHOS DATOS + CONSISTENTE |
| --> Fine-Tuning            |
|                            |
| DATOS CAMBIANTES + FUENTES |
| --> RAG                    |
|                            |
| DOMINIO ESPECIALIZADO +    |
| DATOS FRESCOS              |
| --> Fine-Tuning + RAG      |
|                            |
+----------------------------+
```

---

*Documento creado para el repositorio "Apuntes de Ingenieria de Prompt". Julio 2026.*
*Proxima lectura recomendada: "Tecnicas Avanzadas de Prompting" en la misma seccion.*
