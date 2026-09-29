# 16. AGENTES, SKILLS Y LOOPS
### El Corazon de la IA Autonoma

**Fecha:** Julio 2026
**Nivel:** Ingenieria de Prompt (avanzado)
**Prerrequisitos:** Fundamentos de prompting, Function Calling, conceptos de orquestacion

---

## INDICE

```
1. INTRODUCCION ............................................................. linea   40
2. AGENTES: LA IA QUE ACTUA ................................................. linea   75
3. SKILLS: EL CATALOGO DE HERRAMIENTAS ...................................... linea  290
4. LOOPS: EL CICLO DE VIDA AGENTIVO ......................................... linea  470
5. PATRONES COMBINADOS: AGENTES + SKILLS + LOOPS ............................ linea  650
6. EJERCICIOS PRACTICOS ..................................................... linea  820
7. RESUMEN Y SIGUIENTES PASOS ................................................ linea  920
```

---

## 1. INTRODUCCION

### 1.1 Que son los Agentes, Skills y Loops

Hasta ahora aprendiste a escribir prompts, usar function calling y orquestar workflows. Pero hay un salto cualitativo que separa al ingeniero de prompts del arquitecto de IA: **el paradigma agente-skill-loop**.

```
+------------------------------------------------------------------+
|                      PARADIGMA AGENTE                            |
|                                                                   |
|   +----------+     +----------+     +----------+                 |
|   |  AGENTE  |---->|  SKILL   |---->|  RESULTADO |               |
|   | (cerebro)|     | (manos)  |     | PARCIAL    |               |
|   +----------+     +----------+     +----------+                 |
|        ^                                  |                       |
|        |                                  v                       |
|        +--------  LOOP (ciclo)  ----------+                       |
|                                                                   |
|   El agente PIENSA, usa SKILLS para ACTUAR, y repite            |
|   en un LOOP hasta alcanzar el objetivo.                         |
+------------------------------------------------------------------+
```

**Agente:** una capa de inteligencia que razona, planifica y toma decisiones de forma autonoma. No solo responde: **actua**. Donde un LLM suelto dice "no lo se", un agente dice "voy a averiguarlo".

**Skill:** una herramienta, capacidad o conjunto de instrucciones encapsuladas que el agente puede invocar. No es un simple tool de function calling: un skill puede ser un prompt completo, un workflow, una consulta a base de datos, o incluso otro agente.

**Loop:** el ciclo iterativo de **pensar → actuar → observar → refinar**. Es lo que convierte una lista estatica de instrucciones en un sistema autonomo que se autocorrige y mejora su output en cada iteracion.

### 1.2 Explicacion como para un nino

Imagina que tenes un robot en tu casa con tres superpoderes:

- **El cerebro (Agente):** sabe que tenes que hacer la cena, mira la heladera y dice "con esto puedo hacer una tortilla de papa, pero me falta cebolla".

- **Las manos (Skills):** el robot tiene un catalogo de cosas que sabe hacer: picar cebolla, batir huevos, prender la cocina, pedir delivery por telefono. Cada "mano" es un skill distinto.

- **El reloj (Loop):** el robot no para hasta que la cena este lista. Pica, bate, cocina. Si la tortilla se quema, vuelve a empezar. Si se da cuenta de que no tiene suficiente aceite, activa el skill "pedir delivery de aceite".

El robot no se queda mirandote diciendo "la cena ideal requeriria los siguientes ingredientes". **Se mueve, prueba, falla, aprende y repite.** Eso es un agente con skills en un loop.

### 1.3 El problema que resuelve

```
+-------------------+     "Necesito un informe completo del mercado LATAM"
|   Usuario         | -------------------------------------------------.
+-------------------+                                                  |
                                                                        v
                                              +------------------------------------------+
                                              | LLM SIN agentes:                         |
                                              | "No tengo acceso a datos actualizados.    |
                                              |  Te sugiero buscar en Google y LinkedIn." |
                                              +------------------------------------------+

+-------------------+     "Necesito un informe completo del mercado LATAM"
|   Usuario         | -------------------------------------------------.
+-------------------+                                                  |
                                                                        v
                                              +------------------------------------------+
                                              | AGENTE con skills y loops:               |
                                              | LOOP 1: "Voy a investigar..."            |
                                              |   Skill buscar_web("mercado LATAM 2026") |
                                              |   Skill raspar_noticias(3 fuentes)       |
                                              | LOOP 2: "Analizando datos..."            |
                                              |   Skill analizar_datos(datos_crudos)     |
                                              | LOOP 3: "Verificando y refinando..."     |
                                              |   Skill verificar_fuentes(informe_parcial)|
                                              | LOOP 4: "Redactando final..."            |
                                              |   Skill redactar_informe(datos_validados)|
                                              +------------------------------------------+
                                                         |
                                                         v
                                              INFORME COMPLETO DE 15 PAGINAS CON
                                              FUENTES, GRAFICOS Y RECOMENDACIONES
```

---

## 2. AGENTES: LA IA QUE ACTUA

### 2.1 Anatomia de un Agente

Un agente no es un LLM. Un agente **contiene** un LLM, pero le agrega capas de autonomia, memoria y decision.

```
+------------------------------------------------------------------+
|                    ANATOMIA DE UN AGENTE                          |
|                                                                   |
|  +------------------------------------------------------------+  |
|  | CAPA 1: RAZONAMIENTO (LLM)                                  |  |
|  |   - Procesa la tarea                                         |  |
|  |   - Decide QUE skill usar                                    |  |
|  |   - Genera el plan de accion                                 |  |
|  +------------------------------------------------------------+  |
|                              |                                    |
|  +------------------------------------------------------------+  |
|  | CAPA 2: CATALOGO DE SKILLS                                  |  |
|  |   - Cada skill tiene nombre, descripcion y parametros       |  |
|  |   - El agente elige cual usar basado en contexto            |  |
|  +------------------------------------------------------------+  |
|                              |                                    |
|  +------------------------------------------------------------+  |
|  | CAPA 3: EJECUCION                                           |  |
|  |   - Invoca el skill con los parametros adecuados            |  |
|  |   - Captura el resultado                                    |  |
|  +------------------------------------------------------------+  |
|                              |                                    |
|  +------------------------------------------------------------+  |
|  | CAPA 4: MEMORIA                                             |  |
|  |   - Memoria de corto plazo (contexto de la tarea actual)    |  |
|  |   - Memoria de largo plazo (aprendizajes de ejecuciones)    |  |
|  |   - Guarda resultados parciales, errores, aprendizajes      |  |
|  +------------------------------------------------------------+  |
|                              |                                    |
|  +------------------------------------------------------------+  |
|  | CAPA 5: DECISION (LOOP)                                     |  |
|  |   - Evaluo si el objetivo esta cumplido?                    |  |
|  |   - Si NO: ajusto el plan y vuelvo a la capa 1             |  |
|  |   - Si SI: genero el output final                           |  |
|  +------------------------------------------------------------+  |
|                                                                   |
+------------------------------------------------------------------+
```

### 2.2 Tipos de Agentes

No todos los agentes son iguales. Elegir la arquitectura correcta es tan importante como escribir el prompt correcto.

| Tipo de Agente | Descripcion | Cuando usarlo | Ejemplo |
|---|---|---|---|
| **Agente Reactivo** | Responde solo a estimulos inmediatos, sin memoria ni planificacion | Tareas simples, baja latencia | Chatbot FAQ, clasificador de tickets |
| **Agente Planificador** | Arma un plan multi-paso ANTES de ejecutar | Tareas complejas con dependencias | Generacion de informes, planificacion de proyectos |
| **Agente Reflexivo** | Ejecuta, observa el resultado, se autocorrige | Tareas donde la calidad importa mas que la velocidad | Redaccion de documentos legales, analisis financiero |
| **Agente Multi-Modal** | Razona sobre texto, imagenes, codigo y las combina | Tareas que cruzan dominios | Analisis de documentos escaneados, diseño de UI desde wireframe |
| **Agente Social/Colaborativo** | Trabaja en equipo con otros agentes o humanos | Problemas que requieren multiple expertise | Code review multi-agente, diagnostico medico colaborativo |

#### Agente Planificador (Implementacion)

```python
from typing import Dict, List, Any, Optional
from dataclasses import dataclass
from enum import Enum
import json

class PlanStatus(Enum):
    DRAFT = "draft"
    EXECUTING = "executing"
    ADJUSTING = "adjusting"
    COMPLETED = "completed"
    FAILED = "failed"

@dataclass
class Step:
    id: str
    description: str
    skill_required: str
    parameters: Dict[str, Any]
    depends_on: List[str]
    status: PlanStatus = PlanStatus.DRAFT
    result: Optional[Any] = None
    attempts: int = 0

class PlanningAgent:
    """
    Agente que planifica antes de ejecutar.
    Ideal para tareas complejas con dependencias entre pasos.
    """

    def __init__(self, model_client, skill_registry):
        self.model = model_client
        self.skills = skill_registry
        self.plan: List[Step] = []
        self.memory: Dict[str, Any] = {}

    async def solve(self, task: str, context: Dict = None) -> Dict[str, Any]:
        # FASE 1: PLANIFICAR
        self.plan = await self._create_plan(task, context)

        # FASE 2: EJECUTAR (con loop de correccion)
        while not self._plan_completed():
            ready_steps = self._get_ready_steps()

            for step in ready_steps:
                result = await self._execute_step(step)

                if result["success"]:
                    self.memory[step.id] = result["data"]
                    step.status = PlanStatus.EXECUTING
                else:
                    # Ajustar el plan si un paso falla
                    await self._adjust_plan(step, result["error"])

        # FASE 3: SINTETIZAR
        return await self._synthesize_result()

    async def _create_plan(self, task: str, context: Dict) -> List[Step]:
        prompt = f"""
        TAREA: {task}

        Skills disponibles:
        {self._format_skills_catalog()}

        Crea un plan paso a paso para resolver esta tarea.
        Cada paso debe especificar:
        - Que skill necesita
        - De que otros pasos depende
        - Que parametros requiere

        Responde en JSON con esta estructura:
        {{
            "steps": [
                {{
                    "id": "paso_1",
                    "description": "...",
                    "skill_required": "nombre_del_skill",
                    "parameters": {{}},
                    "depends_on": []
                }}
            ]
        }}
        """

        response = await self.model.generate(prompt)
        plan_data = json.loads(response)

        return [
            Step(
                id=s["id"],
                description=s["description"],
                skill_required=s["skill_required"],
                parameters=s["parameters"],
                depends_on=s.get("depends_on", [])
            )
            for s in plan_data["steps"]
        ]

    def _get_ready_steps(self) -> List[Step]:
        return [
            step for step in self.plan
            if step.status in [PlanStatus.DRAFT, PlanStatus.ADJUSTING]
            and all(
                self._step_completed(dep)
                for dep in step.depends_on
            )
        ]

    async def _execute_step(self, step: Step) -> Dict:
        step.attempts += 1
        skill = self.skills.get(step.skill_required)

        if not skill:
            return {"success": False, "error": f"Skill '{step.skill_required}' no encontrado"}

        try:
            result = await skill.execute(step.parameters, context=self.memory)
            step.status = PlanStatus.COMPLETED
            step.result = result
            return {"success": True, "data": result}
        except Exception as e:
            return {"success": False, "error": str(e)}

    def _plan_completed(self) -> bool:
        return all(step.status == PlanStatus.COMPLETED for step in self.plan)

    def _step_completed(self, step_id: str) -> bool:
        for step in self.plan:
            if step.id == step_id:
                return step.status == PlanStatus.COMPLETED
        return False
```

### 2.3 Agentes vs LLMs vs Workflows

Una confusion frecuente:

| Concepto | Que hace | Quien decide | Ejemplo |
|---|---|---|---|
| **LLM puro** | Genera texto | El usuario (en el prompt) | "Escribi un mail de ventas" |
| **LLM + Function Calling** | Genera texto y llama funciones | El LLM (elige que funcion llamar) | "Busca el precio de AAPL y resumime las noticias" |
| **Workflow fijo** | Sigue pasos predefinidos | El programador (definio los pasos) | Pipeline ETL, cadena de procesamiento |
| **Agente autonomo** | Crea SU PROPIO plan, elige skills, se autocorrige | El agente (decide como resolver) | "Investiga el mercado LATAM y escribime un informe" |

**Regla de oro:** Un workflow es un mapa. Un agente es un GPS. El workflow sigue una ruta fija. El agente recalcula la ruta si hay un desvio.

### 2.4 La Personalidad del Agente: System Prompt como Brujula

El system prompt del agente es su "personalidad base". Define:
- Que puede y que NO puede hacer
- Como debe priorizar skills
- Cuando debe pedir ayuda a un humano
- Cual es su criterio de "tarea completada"

```python
SYSTEM_PROMPT_BASE = """
Eres un Agente de Investigacion de Mercado. Tu funcion es autonomamente
investigar, analizar y reportar sobre mercados latinoamericanos.

REGLAS INQUEBRANTABLES:
1. NUNCA inventes datos. Si no encontras informacion, reporta "dato no disponible".
2. SIEMPRE cita tus fuentes. Cada afirmacion debe tener una URL o referencia.
3. MAXIMO 3 intentos por skill. Si falla 3 veces, pasa al plan B.
4. Si no podes completar la tarea despues de 5 ciclos del loop principal,
   genera un informe parcial con lo que tengas y explica que faltó.

CRITERIO DE EXITO:
El informe debe incluir minimo 5 fuentes verificadas, analisis de tendencias
y recomendaciones accionables. Si falta alguno de estos elementos, el loop
debe continuar.

SKILLS DISPONIBLES:
{skills_catalog}

PLAN DE ESCALAMIENTO:
Si despues de 2 ciclos no avanzaste, simplifica el objetivo y reporta.
"""
```

---

## 3. SKILLS: EL CATALOGO DE HERRAMIENTAS

### 3.1 Que es un Skill (y que NO es)

Un **skill** no es simplemente una funcion de Python. Un skill es una **capacidad encapsulada** que el agente puede invocar con un proposito claro y un resultado predecible.

**Un skill SI es:**
- Una funcion con nombre, descripcion y parametros definidos
- Una sub-tarea autocontenida que resuelve UN problema
- Componible: skills simples se combinan para tareas complejas
- Testeable: cada skill se prueba de forma aislada

**Un skill NO es:**
- Un prompt suelto (eso es un mensaje, no un skill)
- Una funcion que hace "de todo un poco" (falta de cohesion)
- Un agente completo (un agente TIENE skills, no ES un skill)

```
+------------------------------------------------------------------+
|                    CATALOGO DE SKILLS                            |
|                                                                   |
|  Skill: buscar_web                     Tipo: Tool/API            |
|  "Busca en internet y devuelve resultados estructurados"         |
|  Parametros: query (str), max_results (int), sources (list)      |
|  Retorna: List[SearchResult] con titulo, url, snippet            |
|                                                                   |
|  Skill: analizar_sentimiento            Tipo: Prompt Skill       |
|  "Analiza el sentimiento de un texto (positivo/negativo/neutro)" |
|  Parametros: texto (str), idioma (str)                           |
|  Retorna: {sentimiento, confianza, palabras_clave}               |
|                                                                   |
|  Skill: comparar_productos              Tipo: Workflow Skill     |
|  "Compara N productos segun criterios dados"                     |
|  Parametros: productos (list), criterios (list)                  |
|  Retorna: tabla comparativa + recomendacion                      |
|                                                                   |
|  Skill: enviar_correo                   Tipo: Action Skill       |
|  "Envia un correo electronico a uno o mas destinatarios"         |
|  Parametros: to, subject, body, cc                                 |
|  Retorna: {enviado, message_id, timestamp}                       |
|                                                                   |
+------------------------------------------------------------------+
```

### 3.2 Tipos de Skills

| Tipo de Skill | Naturaleza | Ejemplo | Cuando usarlo |
|---|---|---|---|
| **Tool Skill** | Llama una API o funcion externa | `get_weather(city)` | Datos externos, acciones con side effects |
| **Prompt Skill** | Ejecuta un prompt especializado | `resumir_documento(texto)` | Transformacion de texto, analisis, generacion |
| **Workflow Skill** | Orquesta una cadena de sub-tareas | `investigar_tema(tema)` | Tareas compuestas, pipelines |
| **Memory Skill** | Lee o escribe en memoria del agente | `guardar_contexto(key, value)` | Persistencia entre ciclos del loop |
| **Human Skill** | Solicita intervencion humana | `pedir_aprobacion(propuesta)` | Decisiones criticas, validacion final |
| **Meta Skill** | Habilidad sobre otros skills | `elegir_mejor_skill(tarea, skills_disponibles)` | Routing, seleccion inteligente |

### 3.3 Definiendo Skills: La Ficha del Skill

Cada skill necesita una definicion clara para que el agente sepa CUANDO y COMO usarlo.

```python
from dataclasses import dataclass, field
from typing import Callable, Any, Dict, List, Optional
from enum import Enum

class SkillType(Enum):
    TOOL = "tool"
    PROMPT = "prompt"
    WORKFLOW = "workflow"
    MEMORY = "memory"
    HUMAN = "human"
    META = "meta"

@dataclass
class SkillDefinition:
    """Ficha tecnica de un skill. Esto es lo que el agente 'lee' para decidir."""

    name: str                              # Nombre unico: "buscar_web"
    description: str                       # Que hace: "Busca en internet y devuelve..."
    type: SkillType                        # Tipo de skill

    # Parametros que acepta
    parameters: Dict[str, Dict[str, str]] = field(default_factory=dict)
    # Ejemplo: {"query": {"type": "string", "description": "Terminos de busqueda"}}

    # Cuando usarlo
    use_cases: List[str] = field(default_factory=list)
    # Ejemplo: ["Cuando necesites informacion actualizada", "Para verificar hechos"]

    # Cuando NO usarlo
    constraints: List[str] = field(default_factory=list)
    # Ejemplo: ["No usar para busquedas de datos historicos previos a 2020"]

    # Que produce
    output_schema: Dict[str, Any] = field(default_factory=dict)

    # Ejemplos de uso correcto
    examples: List[Dict[str, Any]] = field(default_factory=list)

    # La funcion que realmente ejecuta
    handler: Optional[Callable] = None

    # Metadata
    cost_per_call: float = 0.0
    avg_latency_ms: int = 0
    requires_human_approval: bool = False

# Ejemplo: definiendo skills reales
SKILL_BUSCAR_WEB = SkillDefinition(
    name="buscar_web",
    description="Realiza una busqueda en internet y devuelve los resultados "
                "principales con titulo, URL y fragmento de contenido.",
    type=SkillType.TOOL,
    parameters={
        "query": {"type": "string", "description": "Terminos de busqueda"},
        "max_results": {"type": "integer", "description": "Cantidad maxima de resultados (default 5)"},
        "region": {"type": "string", "description": "Region de busqueda (ej: 'latam', 'us')"}
    },
    use_cases=[
        "Cuando necesites datos actualizados que el modelo no tiene",
        "Para verificar afirmaciones antes de incluirlas en el informe",
        "Para encontrar fuentes que citar"
    ],
    constraints=[
        "No uses mas de 10 resultados por busqueda (costo)",
        "No hagas mas de 3 busquedas identicas (loop infinito)",
        "Siempre guarda los resultados en memoria para no re-buscar"
    ],
    output_schema={
        "results": [
            {"title": "string", "url": "string", "snippet": "string"}
        ],
        "total_found": "integer"
    },
    examples=[
        {
            "input": {"query": "mercado fintech latam 2026", "max_results": 5},
            "output": "5 resultados de Google, Bing y fuentes financieras"
        }
    ]
)

SKILL_ANALIZAR_TEXTO = SkillDefinition(
    name="analizar_texto",
    description="Analiza un texto extrayendo: sentimiento, entidades nombradas, "
                "temas principales y un resumen ejecutivo.",
    type=SkillType.PROMPT,
    parameters={
        "texto": {"type": "string", "description": "Texto a analizar"},
        "idioma": {"type": "string", "description": "Idioma del texto (default: es)"},
        "nivel_detalle": {"type": "string", "description": "'alto', 'medio' o 'bajo'"}
    },
    use_cases=[
        "Para procesar los resultados de una busqueda web antes de sintetizar",
        "Para analizar feedback de clientes en masa"
    ],
    constraints=[
        "Texto maximo: 10,000 caracteres por llamada",
        "No analices informacion personal (PII)"
    ],
    output_schema={
        "sentimiento": "string (positivo|negativo|neutro)",
        "confianza": "float (0-1)",
        "entidades": ["string"],
        "temas": ["string"],
        "resumen": "string"
    }
)
```

### 3.4 Skill Registry: El Catalogo Central

El agente necesita un registro central donde "consultar" que skills tiene disponibles.

```python
class SkillRegistry:
    """
    Catalogo central de skills. El agente consulta este registro
    para decidir que skill usar en cada paso.
    """

    def __init__(self):
        self._skills: Dict[str, SkillDefinition] = {}

    def register(self, skill: SkillDefinition) -> None:
        """Registra un nuevo skill en el catalogo."""
        if skill.name in self._skills:
            raise ValueError(f"Skill '{skill.name}' ya esta registrado")

        # Validar que el skill tiene todo lo necesario
        self._validate_skill(skill)

        self._skills[skill.name] = skill

    def get(self, name: str) -> Optional[SkillDefinition]:
        """Obtiene un skill por nombre."""
        return self._skills.get(name)

    def list_for_agent(self, task_context: str = None) -> str:
        """
        Genera la descripcion de skills disponibles para pasarle al LLM.
        El formato esta optimizado para que el agente 'entienda' sus opciones.
        """
        catalog = []
        for name, skill in self._skills.items():
            entry = f"""
            SKILL: {skill.name}
            TIPO: {skill.type.value}
            DESCRIPCION: {skill.description}
            PARAMETROS: {json.dumps(skill.parameters, ensure_ascii=False)}
            USAR CUANDO: {', '.join(skill.use_cases)}
            NO USAR CUANDO: {', '.join(skill.constraints)}"""
            catalog.append(entry)

        return "\n---\n".join(catalog)

    def find_by_capability(self, description: str) -> List[SkillDefinition]:
        """
        Busca skills usando el propio LLM para matchear
        descripcion de necesidad con skills disponibles.
        """
        # En una implementacion real, aca usarias embeddings + cosine similarity
        # o le pasas la descripcion al LLM para que elija el skill correcto
        matching = []
        for skill in self._skills.values():
            if any(word.lower() in skill.description.lower()
                   for word in description.split()):
                matching.append(skill)
        return matching

    def _validate_skill(self, skill: SkillDefinition) -> None:
        """Valida que el skill este completo y bien definido."""
        if not skill.name:
            raise ValueError("Skill sin nombre")
        if not skill.description:
            raise ValueError(f"Skill '{skill.name}' sin descripcion")
        if not skill.use_cases:
            raise ValueError(f"Skill '{skill.name}' sin casos de uso definidos")
        if skill.type in [SkillType.TOOL, SkillType.WORKFLOW] and not skill.handler:
            raise ValueError(f"Skill '{skill.name}' de tipo {skill.type.value} "
                           "necesita un handler")
```

### 3.5 Composicion de Skills

Los skills no viven aislados. La magia sucede cuando el agente los combina.

```python
# Ejemplo: el agente compone skills para resolver una tarea compleja

async def ejecutar_investigacion_mercado(agent, tema: str):
    """
    El agente usa 4 skills en secuencia para producir un informe.
    Nota como cada skill alimenta al siguiente.
    """

    # SKILL 1: Buscar informacion cruda
    datos_crudos = await agent.use_skill("buscar_web", {
        "query": f"analisis mercado {tema} 2026 tendencias",
        "max_results": 10,
        "region": "latam"
    })

    # SKILL 2: Extraer y limpiar texto de las URLs encontradas
    textos_limpios = await agent.use_skill("extraer_contenido_web", {
        "urls": [r["url"] for r in datos_crudos["results"]],
        "formato": "texto_plano"
    })

    # SKILL 3: Analizar cada texto individualmente
    analisis = []
    for texto in textos_limpios:
        resultado = await agent.use_skill("analizar_texto", {
            "texto": texto["contenido"],
            "idioma": "es",
            "nivel_detalle": "alto"
        })
        analisis.append(resultado)

    # SKILL 4: Sintetizar todo en un informe
    informe = await agent.use_skill("redactar_informe", {
        "datos": analisis,
        "formato": "ejecutivo",
        "max_palabras": 2000,
        "incluir_graficos": True
    })

    return informe
```

---

## 4. LOOPS: EL CICLO DE VIDA AGENTIVO

### 4.1 El Loop Fundamental: Observar, Planificar, Actuar

El loop es el corazon que bombea autonomia. Sin loop, un agente es solo un workflow glorificado. Con loop, el agente **aprende y se corrige**.

```
+------------------------------------------------------------------+
|                    EL LOOP AGENTIVO                              |
|                                                                   |
|                     +------------------+                          |
|                     |  1. OBSERVAR     |                          |
|                     |  (Que se yo?      |                          |
|                     |   Que me falta?)  |                          |
|                     +--------+---------+                          |
|                              |                                    |
|                              v                                    |
|                     +------------------+                          |
|          +--------->|  2. PLANIFICAR   |                          |
|          |          |  (Que skill uso?  |                          |
|          |          |   En que orden?)  |                          |
|          |          +--------+---------+                          |
|          |                   |                                    |
|          |                   v                                    |
|          |          +------------------+                          |
|          |          |  3. EJECUTAR     |                          |
|          |          |  (Correr el skill,|                          |
|          |          |   capturar output)|                          |
|          |          +--------+---------+                          |
|          |                   |                                    |
|          |                   v                                    |
|          |          +------------------+                          |
|          |          |  4. EVALUAR      |                          |
|          |          |  (El resultado    |                          |
|          |          |   es suficiente?)|                          |
|          |          +--------+---------+                          |
|          |                   |                                    |
|          |        SI         |         NO                        |
|          |        v          |          |                         |
|          |   +--------+      +----------+                         |
|          |   | FIN    |                                            |
|          |   +--------+                                            |
|          |                                                         |
|          +---- El loop se repite con la nueva informacion -------+
|                                                                   |
+------------------------------------------------------------------+
```

### 4.2 Tipos de Loops

| Tipo de Loop | Descripcion | Patron | Ejemplo |
|---|---|---|---|
| **Loop de Refinamiento** | Mejora incremental de un output | Generar → Evaluar → Refinar → Evaluar | Escribir un informe en 3 pasadas |
| **Loop de Investigacion** | Buscar, analizar, buscar mas | Investigar → Analizar → Identificar gaps → Investigar mas | Due diligence de una empresa |
| **Loop de Verificacion** | Ejecutar y validar contra criterios | Ejecutar → Verificar → Corregir → Verificar | Escribir codigo y correr tests |
| **Loop de Escalamiento** | Intentar → Fallar → Simplificar → Intentar | Ejecutar → Evaluar → Degradar objetivo → Ejecutar | Tarea compleja que excede capacidad |
| **Loop de Colaboracion** | Agente A produce → Agente B revisa → Agente A ajusta | Productor → Reviewer → Productor (ajusta) | Code review multi-agente |

### 4.3 Implementacion del Loop Principal

```python
from enum import Enum
from typing import Dict, Any, Optional
import asyncio

class LoopStatus(Enum):
    CONTINUE = "continue"
    COMPLETE = "complete"
    FAILED = "failed"
    NEEDS_HUMAN = "needs_human"

class AgentLoop:
    """
    Implementacion generica del loop agente.
    Este es el motor que ejecuta el ciclo observar-planificar-actuar-evaluar.
    """

    def __init__(self, agent, skill_registry, max_iterations: int = 10):
        self.agent = agent
        self.skills = skill_registry
        self.max_iterations = max_iterations

        # Contadores y limites
        self.iteration = 0
        self.consecutive_failures = 0
        self.max_consecutive_failures = 3

        # Memoria del loop
        self.history: List[Dict[str, Any]] = []
        self.partial_results: List[Any] = []

    async def run(self, objective: str, success_criteria: Dict) -> Dict[str, Any]:
        """
        Ejecuta el loop principal hasta cumplir el objetivo o agotar intentos.
        """

        while self.iteration < self.max_iterations:
            self.iteration += 1

            print(f"\n=== LOOP ITERACION {self.iteration}/{self.max_iterations} ===")

            # PASO 1: OBSERVAR el estado actual
            state = await self._observe(objective, success_criteria)

            # PASO 2: PLANIFICAR el proximo paso
            plan = await self._plan(state, objective)

            # PASO 3: EJECUTAR el plan
            result = await self._execute(plan)

            # PASO 4: EVALUAR si alcanzamos el objetivo
            status = await self._evaluate(result, success_criteria)

            # Guardar en historial
            self.history.append({
                "iteration": self.iteration,
                "state": state,
                "plan": plan,
                "result": result,
                "status": status
            })

            # Manejar el resultado de la evaluacion
            if status == LoopStatus.COMPLETE:
                return await self._build_final_response(result)
            elif status == LoopStatus.NEEDS_HUMAN:
                return await self._request_human_intervention(state)
            elif status == LoopStatus.FAILED:
                self.consecutive_failures += 1
                if self.consecutive_failures >= self.max_consecutive_failures:
                    return await self._build_partial_response()
                # Si no, el loop continua y reintenta con enfoque diferente
            else:  # CONTINUE
                self.consecutive_failures = 0  # Resetear contador de fallos
                self.partial_results.append(result)
                # El loop naturalmente vuelve al paso 1 con la nueva informacion

        # Se agotaron las iteraciones
        return await self._build_partial_response()

    async def _observe(self, objective: str, criteria: Dict) -> Dict[str, Any]:
        """El agente evalua que sabe, que le falta y que aprendio hasta ahora."""

        prompt = f"""
        OBJETIVO: {objective}
        CRITERIOS DE EXITO: {json.dumps(criteria, ensure_ascii=False)}

        ITERACION ACTUAL: {self.iteration} de {self.max_iterations}

        RESULTADOS PARCIALES ACUMULADOS:
        {json.dumps(self.partial_results[-3:] if self.partial_results else 'Ninguno', ensure_ascii=False)}

        HISTORIAL DE INTENTOS:
        {json.dumps([h['status'].value for h in self.history[-5:]], ensure_ascii=False)}

        Analiza el estado actual y responde en JSON:
        {{
            "que_se": "que informacion ya tengo",
            "que_me_falta": "que necesito para cumplir los criterios de exito",
            "que_aprendi": "lecciones de intentos anteriores",
            "confianza": 0.0-1.0,
            "problema_principal": "cual es el obstaculo mas grande ahora"
        }}
        """

        response = await self.agent.model.generate(prompt)
        return json.loads(response)

    async def _plan(self, state: Dict, objective: str) -> Dict[str, Any]:
        """Basado en la observacion, el agente decide que hacer."""

        prompt = f"""
        OBJETIVO: {objective}

        ESTADO ACTUAL:
        - Se que: {state['que_se']}
        - Me falta: {state['que_me_falta']}
        - Aprendi: {state['que_aprendi']}

        SKILLS DISPONIBLES:
        {self.skills.list_for_agent(objective)}

        Basado en el estado actual, decidi cual es el MEJOR skill a usar AHORA.
        Responde en JSON:
        {{
            "skill": "nombre_del_skill",
            "parametros": {{}},
            "razonamiento": "por que este skill y no otro",
            "resultado_esperado": "que espero obtener"
        }}
        """

        response = await self.agent.model.generate(prompt)
        return json.loads(response)

    async def _execute(self, plan: Dict) -> Dict[str, Any]:
        """Ejecuta el skill elegido."""

        skill_name = plan["skill"]
        skill = self.skills.get(skill_name)

        if not skill:
            return {"error": f"Skill '{skill_name}' no encontrado", "success": False}

        try:
            result = await skill.handler(**plan["parametros"])
            return {"success": True, "data": result, "skill_used": skill_name}
        except Exception as e:
            return {"success": False, "error": str(e), "skill_used": skill_name}

    async def _evaluate(self, result: Dict, criteria: Dict) -> LoopStatus:
        """Evalua si el resultado cumple los criterios de exito."""

        if not result.get("success"):
            # Fallo en ejecucion. Evaluar si es recuperable.
            failure_count = sum(1 for h in self.history if h["status"] == LoopStatus.FAILED)
            if failure_count >= self.max_consecutive_failures:
                return LoopStatus.NEEDS_HUMAN
            return LoopStatus.FAILED

        # Evaluar calidad del resultado contra los criterios
        prompt = f"""
        CRITERIOS DE EXITO: {json.dumps(criteria, ensure_ascii=False)}
        RESULTADO OBTENIDO: {json.dumps(result, ensure_ascii=False)}
        ITERACION: {self.iteration} de {self.max_iterations}

        Evalua si el resultado cumple los criterios. Responde SOLO con una palabra:
        - COMPLETE: todos los criterios se cumplen satisfactoriamente
        - CONTINUE: falta informacion o calidad, pero vamos por buen camino
        - FAILED: el resultado no aporta, hay que cambiar de estrategia
        - NEEDS_HUMAN: se requiere decision humana para continuar
        """

        response = await self.agent.model.generate(prompt)
        response_clean = response.strip().upper()

        mapping = {
            "COMPLETE": LoopStatus.COMPLETE,
            "CONTINUE": LoopStatus.CONTINUE,
            "FAILED": LoopStatus.FAILED,
            "NEEDS_HUMAN": LoopStatus.NEEDS_HUMAN
        }
        return mapping.get(response_clean, LoopStatus.CONTINUE)
```

### 4.4 Anti-Patrones de Loops

| Anti-Patron | Sintoma | Solucion |
|---|---|---|
| **Loop Infinito** | El agente nunca llega a COMPLETE, siempre encuentra algo que refinar | Poner max_iterations bajo (3-5) y criterios de exito binarios |
| **Loop Timido** | El agente se rinde al primer obstaculo | Aumentar max_consecutive_failures; agregar skill de "pideme ayuda" |
| **Loop Obsesivo** | El agente usa el mismo skill una y otra vez esperando distinto resultado | Registrar skills usados; prohibir repetir skill >2 veces sin variar parametros |
| **Loop Amnesico** | El agente "olvida" lo que aprendio en iteraciones anteriores | Implementar memoria de loop explicita (self.partial_results, self.history) |
| **Loop Perfeccionista** | El agente nunca considera el resultado "suficientemente bueno" | Definir criterios de exito con umbrales numericos, no cualitativos |

### 4.5 Loops Multi-Agente

Cuando un solo agente no alcanza, los loops se distribuyen entre multiples agentes.

```python
class CollaborativeLoop:
    """
    Loop donde dos agentes colaboran: uno produce, el otro revisa.
    Cada ciclo mejora la calidad del output.
    """

    def __init__(self, producer_agent, reviewer_agent, max_rounds: int = 3):
        self.producer = producer_agent
        self.reviewer = reviewer_agent
        self.max_rounds = max_rounds

    async def run(self, task: str, quality_threshold: float = 0.8) -> Dict:
        """
        Productor genera → Reviewer evalua → Productor mejora.
        Se repite hasta alcanzar el umbral de calidad o agotar rondas.
        """

        draft = await self.producer.generate(task)

        for round_num in range(self.max_rounds):
            # El reviewer analiza el draft
            feedback = await self.reviewer.review(draft, task)

            if feedback["quality_score"] >= quality_threshold:
                return {"final_output": draft, "rounds": round_num + 1, "status": "approved"}

            # El producer incorpora el feedback
            draft = await self.producer.refine(draft, feedback["suggestions"])

        return {"final_output": draft, "rounds": self.max_rounds,
                "status": "max_rounds_reached", "warning": "Calidad objetivo no alcanzada"}
```

---

## 5. PATRONES COMBINADOS: AGENTES + SKILLS + LOOPS

### 5.1 El Patron Agente Investigador

El patron mas comun en produccion. Un agente que investiga, analiza y reporta.

```
+------------------------------------------------------------------+
|              PATRON: AGENTE INVESTIGADOR                          |
|                                                                   |
|  USUARIO: "Investiga el mercado de ciberseguridad LATAM"         |
|                                                                   |
|  LOOP 1 (Investigacion):                                          |
|    SKILL buscar_web("ciberseguridad latam 2026")                  |
|    SKILL buscar_web("ciberseguridad latam regulaciones")          |
|    SKILL buscar_web("startups ciberseguridad latam")              |
|    EVALUAR: tengo 15 fuentes. Suficiente? SI.                    |
|                                                                   |
|  LOOP 2 (Analisis):                                               |
|    SKILL extraer_contenido_web(15 urls)                           |
|    SKILL analizar_texto(cada fuente)                              |
|    EVALUAR: analisis completos? SI.                               |
|                                                                   |
|  LOOP 3 (Sintesis):                                               |
|    SKILL redactar_informe(datos_analizados)                       |
|    SKILL verificar_fuentes(informe)                                |
|    EVALUAR: todas las fuentes citadas? El informe tiene           |
|            tendencias + recomendaciones? SI.                      |
|                                                                   |
|  OUTPUT: Informe de 12 paginas con fuentes, graficos y            |
|          recomendaciones estrategicas.                            |
+------------------------------------------------------------------+
```

### 5.2 El Patron Agente con Memoria

El agente recuerda conversaciones pasadas, preferencias del usuario y resultados previos. Esto permite experiencias personalizadas que mejoran con el tiempo.

```python
class MemoryAugmentedAgent:
    """
    Agente que recuerda entre sesiones usando memoria de largo plazo.
    """

    def __init__(self, agent, vector_store):
        self.agent = agent
        self.memory = vector_store  # Base de datos vectorial (Pinecone, Chroma, etc.)
        self.user_id = None

    async def process(self, user_input: str, user_id: str) -> str:
        self.user_id = user_id

        # Recuperar contexto relevante de la memoria
        relevant_memories = await self.memory.search(
            query=user_input,
            filter={"user_id": user_id},
            top_k=5
        )

        # Construir prompt con contexto memorizado
        context = self._build_memory_context(relevant_memories)

        enhanced_prompt = f"""
        CONTEXTO DE SESIONES ANTERIORES CON ESTE USUARIO:
        {context}

        INPUT ACTUAL DEL USUARIO:
        {user_input}

        Responde considerando el historial de interacciones.
        """

        response = await self.agent.generate(enhanced_prompt)

        # Guardar esta interaccion en memoria para el futuro
        await self.memory.store(
            text=f"User: {user_input}\nAssistant: {response}",
            metadata={"user_id": user_id, "timestamp": datetime.now().isoformat()}
        )

        return response

    def _build_memory_context(self, memories: List) -> str:
        return "\n---\n".join([m["text"] for m in memories])
```

### 5.3 El Patron Agente con Guardrails

Todo agente autonomo necesita limites. Los guardrails son reglas que el agente no puede violar, verificadas en cada iteracion del loop.

```python
class GuardedAgent:
    """
    Agente que ejecuta dentro de una jaula de seguridad.
    Cada accion pasa por un guardrail antes de ejecutarse.
    """

    def __init__(self, agent, guardrails: List[Callable]):
        self.agent = agent
        self.guardrails = guardrails

    async def execute_skill(self, skill_name: str, params: Dict) -> Dict:
        # Verificar guardrails ANTES de ejecutar
        for guard in self.guardrails:
            allowed, reason = await guard.check(skill_name, params)
            if not allowed:
                return {
                    "success": False,
                    "blocked_by": guard.name,
                    "reason": reason
                }

        # Si pasa todos los guardrails, ejecutar
        return await self.agent.execute_skill(skill_name, params)

# Ejemplos de guardrails
class BudgetGuardrail:
    """No permite que el costo total supere un presupuesto."""

    def __init__(self, max_budget_usd: float):
        self.name = "budget_guard"
        self.max_budget = max_budget_usd
        self.spent = 0.0

    async def check(self, skill_name: str, params: Dict) -> tuple:
        estimated_cost = self._estimate_cost(skill_name, params)
        if self.spent + estimated_cost > self.max_budget:
            return False, f"Presupuesto excedido: ${self.spent:.2f} gastado de ${self.max_budget:.2f}"
        return True, "OK"

class PIIGuardrail:
    """Bloquea skills que podrian exponer datos personales."""

    def __init__(self):
        self.name = "pii_guard"
        self.pii_patterns = [r'\b\d{8}\b', r'\b[A-Z]{3}\d{6}\b']  # DNI, pasaporte

    async def check(self, skill_name: str, params: Dict) -> tuple:
        import re
        params_str = json.dumps(params)
        for pattern in self.pii_patterns:
            if re.search(pattern, params_str):
                return False, "Se detecto posible PII en los parametros"
        return True, "OK"

class RateLimitGuardrail:
    """Previene que el agente haga demasiadas llamadas en poco tiempo."""

    def __init__(self, max_calls_per_minute: int):
        self.name = "rate_limit_guard"
        self.max_calls = max_calls_per_minute
        self.calls_timestamps: List[float] = []

    async def check(self, skill_name: str, params: Dict) -> tuple:
        now = time.time()
        self.calls_timestamps = [t for t in self.calls_timestamps if now - t < 60]

        if len(self.calls_timestamps) >= self.max_calls:
            return False, f"Rate limit excedido: {self.max_calls} llamadas/minuto"
        return True, "OK"
```

### 5.4 Patron de Self-Improving Agent

Agentes que aprenden de sus propias ejecuciones. Cada iteracion del loop alimenta un sistema de aprendizaje que mejora decisiones futuras.

```python
class SelfImprovingAgent:
    """
    Agente que aprende de su propio historial de ejecucion.
    Guarda que skills funcionaron mejor para que tipo de tareas.
    """

    def __init__(self, agent, skill_registry):
        self.agent = agent
        self.skills = skill_registry
        self.performance_log: List[Dict] = []

    async def solve(self, task: str, task_type: str) -> Dict:
        # Consultar historial: que skills funcionaron para este tipo de tarea?
        relevant_history = self._get_relevant_history(task_type)

        # Si tengo historial, lo uso para priorizar skills
        if relevant_history:
            prioritized_skills = self._prioritize_skills(relevant_history)
        else:
            prioritized_skills = None

        # Ejecutar con skills priorizados
        result = await self.agent.solve(task, prioritized_skills)

        # Guardar resultado para aprendizaje futuro
        self.performance_log.append({
            "task": task,
            "task_type": task_type,
            "skills_used": result["skills_used"],
            "success": result["success"],
            "duration": result["duration"],
            "timestamp": datetime.now().isoformat()
        })

        return result

    def _get_relevant_history(self, task_type: str, limit: int = 10) -> List[Dict]:
        return [
            entry for entry in self.performance_log[-50:]
            if entry["task_type"] == task_type and entry["success"]
        ][-limit:]

    def _prioritize_skills(self, history: List[Dict]) -> List[str]:
        """Rankea skills por frecuencia de exito en tareas similares."""
        skill_scores = {}
        for entry in history:
            for skill in entry["skills_used"]:
                skill_scores[skill] = skill_scores.get(skill, 0) + 1
        return sorted(skill_scores, key=skill_scores.get, reverse=True)
```

---

## 6. EJERCICIOS PRACTICOS

### Ejercicio 1: Disena un Catalogo de Skills

**Objetivo:** Aprender a definir skills que un agente pueda usar efectivamente.

Define 5 skills para un "Agente de Viajes" que ayuda a planificar vacaciones completas. Cada skill debe incluir: nombre, descripcion, parametros, casos de uso y restricciones.

```
Agente de Viajes - Skills necesarios:
1. buscar_vuelos(origen, destino, fecha_ida, fecha_vuelta, pasajeros)
2. buscar_hoteles(ciudad, check_in, check_out, huespedes, estrellas_min)
3. comparar_precios(opciones_vuelos, opciones_hoteles, presupuesto_total)
4. verificar_clima(destino, fecha_viaje)
5. generar_itinerario(vuelo_elegido, hotel_elegido, preferencias_usuario)
```

### Ejercicio 2: Implementa un Loop de Refinamiento

**Objetivo:** Escribir un loop donde el agente genera un texto, lo evalua y lo refina.

```python
# Completa la implementacion de este loop de refinamiento para
# un agente que escribe resumenes ejecutivos.

class ExecutiveSummaryAgent:
    def __init__(self, model):
        self.model = model

    async def write_summary(self, document: str, max_words: int = 200) -> str:
        # TODO: Implementar el loop de refinamiento
        # 1. Generar primer borrador
        # 2. Evaluar: esta por debajo de max_words? Captura los puntos clave?
        # 3. Si no cumple, refinar y volver a evaluar
        # 4. Maximo 3 intentos de refinamiento
        pass
```

### Ejercicio 3: Agente Multi-Skill con Guardrails

**Objetivo:** Construir un agente que:
1. Tenga al menos 3 skills distintos
2. Tenga un budget guardrail que limite costo total a $0.50 USD
3. Tenga un loop con maximo 5 iteraciones
4. Genere un informe de analisis de sentimiento de noticias sobre un tema dado

---

## 7. RESUMEN Y SIGUIENTES PASOS

### 7.1 Mapa Conceptual

```
+------------------------------------------------------------------+
|                    ARQUITECTURA AGENTE COMPLETA                   |
|                                                                   |
|   USUARIO: "Haceme un informe del mercado LATAM"                 |
|                                                                   |
|   +------------------------------------------------------------+  |
|   |                        AGENTE                              |  |
|   |                                                             |  |
|   |  SYSTEM PROMPT (personalidad, reglas, criterios de exito)  |  |
|   |                                                             |  |
|   |  LOOP PRINCIPAL (max 10 iteraciones)                       |  |
|   |  +--> OBSERVAR (que se, que me falta)                      |  |
|   |  |--> PLANIFICAR (que skill uso ahora)                     |  |
|   |  |--> EJECUTAR (correr el skill)                           |  |
|   |  |--> EVALUAR (cumpli el criterio?)                        |  |
|   |  +---(si no) volver a OBSERVAR con nueva info              |  |
|   |                                                             |  |
|   |  SKILL CATALOG                                             |  |
|   |  +-- buscar_web(query)           --> Tool Skill            |  |
|   |  +-- analizar_texto(texto)       --> Prompt Skill          |  |
|   |  +-- redactar_informe(datos)     --> Prompt Skill          |  |
|   |  +-- verificar_fuentes(texto)    --> Tool Skill            |  |
|   |  +-- pedir_aprobacion(propuesta) --> Human Skill           |  |
|   |                                                             |  |
|   |  GUARDRAILS (ejecutados en cada skill)                     |  |
|   |  +-- Budget Guard    (no exceder $0.50)                    |  |
|   |  +-- PII Guard        (no exponer datos personales)        |  |
|   |  +-- Rate Limit Guard (max 30 llamadas/minuto)             |  |
|   |                                                             |  |
|   |  MEMORIA                                                    |  |
|   |  +-- Corto plazo: resultados parciales del loop actual     |  |
|   |  +-- Largo plazo: aprendizajes de ejecuciones anteriores   |  |
|   +------------------------------------------------------------+  |
|                              |                                    |
|                              v                                    |
|   INFORME FINAL (con fuentes, metricas y confianza)              |
+------------------------------------------------------------------+
```

### 7.2 Reglas de Oro del Arquitecto de Agentes

| # | Regla | Por que |
|---|---|---|
| 1 | **Todo agente necesita un limite de iteraciones** | Sin limite, un loop puede correr para siempre |
| 2 | **Cada skill debe ser testeable de forma aislada** | Si no sabes si el skill funciona solo, no sabras si el agente funciona |
| 3 | **Los criterios de exito deben ser binarios** | "Suficientemente bueno" es una receta para loops infinitos |
| 4 | **Nunca despliegues un agente sin guardrails** | Un agente sin limites es un riesgo de seguridad y costo |
| 5 | **La memoria es la diferencia entre un agente y un script** | Sin memoria, cada iteracion empieza de cero |
| 6 | **Empieza con 3 skills, no con 30** | Cada skill nuevo aumenta la complejidad de decision exponencialmente |
| 7 | **Siempre implementa un Plan B** | Si el objetivo original es inalcanzable, el agente debe degradarlo, no colgarse |

### 7.3 Relacion con Otros Modulos

| Si queres profundizar en... | Anda a... |
|---|---|
| Function Calling (skills tipo Tool) | [15-FUNCTION-CALLING](./15-FUNCTION-CALLING-Y-OUTPUT-ESTRUCTURADO/) |
| Workflows Multi-Agente | [14-ESTRATEGIAS-DE-ORQUESTACION/Workflows Multi-Agente](./14-ESTRATEGIAS-DE-ORQUESTACION/Workflows%20Multi-Agente.md) |
| Optimizacion de Recursos | [14-ESTRATEGIAS-DE-ORQUESTACION/Optimizacion de Recursos](./14-ESTRATEGIAS-DE-ORQUESTACION/Optimizaci%C3%B3n%20de%20Recursos%20y%20Escalabilidad.md) |
| Prompt Engineering Avanzado | [03-SECRETOS-DEL-MAESTRO/Prompt Engineering Avanzado](./03-SECRETOS-DEL-MAESTRO/Prompt%20Engineering%20Avanzado.md) |
| Seguridad y Defensa de Prompts | [12-SEGURIDAD-Y-DEFENSA-DE-PROMPTS](./12-SEGURIDAD-Y-DEFENSA-DE-PROMPTS/) |
| Investigacion Actual (Agentes) | [10-FRONTERA-DE-LA-INVESTIGACION](./10-FRONTERA-DE-LA-INVESTIGACION/) |

### 7.4 Proximos Pasos

1. **Construi tu primer agente:** Empeza con 1 skill y un loop simple de 3 iteraciones. No intentes el agente perfecto de entrada.
2. **Agrega skills incrementalmente:** Cada skill nuevo es un punto de fallo nuevo. Agrega de a uno y testea.
3. **Implementa observabilidad:** Logs, trazas, metricas. Sin visibilidad, un agente es una caja negra imposible de debuggear.
4. **Itera sobre el system prompt:** El 80% del comportamiento del agente viene de su system prompt. Es el skill mas importante.
5. **Estudia los agentes de produccion:** Analiza como funcionan Devin, GPT Engineer, AutoGPT, CrewAI. No los copies: entendelos.

---

*"Un LLM te da respuestas. Un agente con skills te da resultados. Un agente con skills y loops te da autonomia."*

---

**Version: 1.0 - Julio 2026**
