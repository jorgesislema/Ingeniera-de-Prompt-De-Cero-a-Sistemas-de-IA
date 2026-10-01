# 01 — ¿Qué es Prompt Engineering?

> **El Prompt Engineering es la disciplina de diseñar, estructurar, probar y optimizar las entradas que proporcionamos a un modelo de IA para obtener resultados útiles, consistentes, controlables y verificables.**

---

## 1. El concepto fundamental

Cuando una persona comienza a utilizar un modelo de IA suele pensar:

> "Escribir un buen prompt significa hacer una buena pregunta."

Esta definición es demasiado limitada.

Un prompt puede contener mucho más que una pregunta.

Puede contener:

* instrucciones;
* contexto;
* datos;
* ejemplos;
* restricciones;
* criterios de evaluación;
* formato de salida;
* información sobre el objetivo;
* variables;
* referencias;
* documentos;
* mensajes anteriores;
* instrucciones para utilizar herramientas.

Por tanto:

```text
PROMPT
   │
   ├── Instrucciones
   ├── Contexto
   ├── Datos
   ├── Ejemplos
   ├── Restricciones
   ├── Formato esperado
   └── Objetivo
          │
          ▼
       MODELO
          │
          ▼
       INFERENCIA
          │
          ▼
       SALIDA
```

Pero incluso este esquema es incompleto.

En un sistema real, el resultado también puede depender de:

```text
                  ┌──────────────────┐
                  │      MODELO      │
                  └────────┬─────────┘
                           │
                           ▼
┌──────────┐        ┌──────────────┐        ┌─────────────┐
│  PROMPT  │ ─────► │   CONTEXTO   │ ─────► │  INFERENCIA │
└──────────┘        └──────────────┘        └──────┬──────┘
                                                   │
                           ┌───────────────────────┤
                           │                       │
                           ▼                       ▼
                      HERRAMIENTAS             SAMPLING
                           │                       │
                           └───────────┬───────────┘
                                       ▼
                                  RESPUESTA
                                       │
                                       ▼
                                  EVALUACIÓN
```

Por eso una definición más completa sería:

> **Prompt Engineering es la práctica de diseñar entradas e instrucciones para interactuar de manera controlada con modelos de IA, teniendo en cuenta el modelo, el contexto, el mecanismo de inferencia, las herramientas disponibles, las restricciones del sistema y los criterios utilizados para evaluar la salida.**

---

# 2. Prompt Engineering no es "hablar bonito con la IA"

Uno de los errores más comunes consiste en creer que un prompt funciona mejor cuanto más elaborado, educado o sofisticado parece.

Por ejemplo:

```text
"Por favor, actúa como un experto mundial,
con más de 50 años de experiencia, analiza
profundamente este texto y dame una respuesta
excelente."
```

Este prompt puede producir una respuesta útil.

Pero la calidad no depende simplemente de utilizar palabras como:

* experto;
* profesional;
* mundial;
* avanzado;
* profundamente;
* excelente.

Lo importante es especificar **qué debe hacer el sistema**.

Una instrucción más operacional podría ser:

```text
Analiza el documento.

Identifica:
1. afirmaciones verificables;
2. datos numéricos;
3. contradicciones;
4. información faltante.

Para cada problema proporciona:
- evidencia;
- explicación;
- nivel de riesgo.

Devuelve el resultado en JSON válido.
```

La diferencia fundamental es:

```text
PROMPT ORIENTADO A LA RETÓRICA
          ↓
"Quiero una respuesta excelente."

PROMPT ORIENTADO A LA TAREA
          ↓
"Identifica X, analiza Y y devuelve Z
bajo estas restricciones."
```

El segundo permite evaluar objetivamente si el modelo cumplió.

---

# 3. El objetivo no es obtener "una respuesta bonita"

En Prompt Engineering profesional, una respuesta aparentemente buena no necesariamente es una respuesta correcta.

Supongamos que necesitamos extraer información de facturas.

Tenemos 1.000 documentos.

Un prompt produce:

```text
Factura: 001-123
Cliente: Empresa ABC
Total: $1.250
```

La respuesta parece correcta.

Pero un sistema profesional debe preguntar:

* ¿Extrae siempre el mismo campo?
* ¿Qué ocurre cuando falta el número de factura?
* ¿Qué ocurre cuando hay dos fechas?
* ¿Qué ocurre si el total está escrito con coma decimal?
* ¿Qué ocurre si el documento contiene instrucciones maliciosas?
* ¿Devuelve JSON válido?
* ¿Qué porcentaje de documentos procesa correctamente?
* ¿Cómo detectamos errores?
* ¿Qué sucede cuando cambiamos de modelo?

Aquí aparece una diferencia fundamental:

```text
USUARIO PRINCIPIANTE

"¿La respuesta parece buena?"

             ↓

INGENIERO DE IA

"¿El sistema produce resultados correctos,
consistentes, verificables y adecuados
para la tarea?"
```

---

# 4. Prompt Engineering como problema de ingeniería

La palabra **Engineering** es importante.

Ingeniería significa diseñar algo bajo restricciones y comprobar que funciona.

Podemos representar el proceso así:

```text
              OBJETIVO
                 │
                 ▼
          ┌──────────────┐
          │   DISEÑAR    │
          │    PROMPT    │
          └──────┬───────┘
                 │
                 ▼
             EJECUTAR
                 │
                 ▼
             OBSERVAR
                 │
                 ▼
             EVALUAR
                 │
          ┌──────┴──────┐
          │             │
       FUNCIONA       FALLA
          │             │
          ▼             ▼
       MANTENER      MODIFICAR
                        │
                        └──────► PROBAR
```

Este ciclo se conoce como un proceso iterativo.

Por tanto:

> **Un prompt profesional no debería considerarse terminado porque produjo una respuesta correcta una vez.**

Debe probarse bajo diferentes condiciones.

---

# 5. Prompt Engineering y programación

Prompt Engineering comparte algunas características con la programación, aunque no son exactamente lo mismo.

En programación tradicional:

```python
def sumar(a, b):
    return a + b
```

Si proporcionamos:

```text
a = 5
b = 3
```

esperamos:

```text
8
```

El comportamiento está definido mediante reglas explícitas.

Con un modelo generativo:

```text
Prompt
   ↓
Modelo probabilístico
   ↓
Salida
```

El resultado puede depender de múltiples factores.

Por ejemplo:

```text
Prompt
   +
Modelo
   +
Contexto
   +
Parámetros de inferencia
   +
Herramientas
   +
Estado de la conversación
   ↓
Salida
```

Esto tiene una consecuencia importante:

> **Diseñar prompts requiere pensar en sistemas probabilísticos, no únicamente en instrucciones deterministas.**

---

# 6. Un mismo prompt puede producir resultados diferentes

Supongamos:

```text
Explica qué es un transformer.
```

El modelo podría producir una explicación:

### Respuesta A

> Un Transformer es una arquitectura de redes neuronales utilizada ampliamente en procesamiento de lenguaje natural.

### Respuesta B

> Un Transformer es una arquitectura basada en mecanismos de atención que permite procesar relaciones entre elementos de una secuencia.

### Respuesta C

> Imagina que tienes una frase y quieres saber qué palabras son importantes para interpretar cada palabra. La atención permite modelar esas relaciones.

Las tres respuestas pueden ser razonables.

Pero tienen características diferentes.

Esto nos lleva a una idea esencial:

```text
MISMO PROMPT
     │
     ├── Modelo A ──► Respuesta A
     │
     ├── Modelo B ──► Respuesta B
     │
     └── Modelo C ──► Respuesta C
```

Por eso:

> **Un prompt no tiene un significado operativo completamente independiente del modelo que lo procesa.**

---

# 7. El modelo forma parte del prompt

Esta idea será fundamental durante todo el repositorio.

Consideremos:

```text
Prompt
"Resuelve este problema matemático
y explica el procedimiento."
```

Ahora utilizamos:

```text
Modelo A → modelo general
Modelo B → modelo especializado en razonamiento
Modelo C → modelo pequeño
Modelo D → modelo especializado en matemáticas
```

El comportamiento puede cambiar.

No necesariamente porque el prompt haya cambiado.

Cambió el sistema que interpreta el prompt.

Por eso debemos abandonar la idea:

```text
PROMPT = RESULTADO
```

y utilizar:

```text
PROMPT + MODELO + CONTEXTO + INFERENCIA
                    ↓
                 RESULTADO
```

---

# 8. Prompt Engineering no controla directamente los parámetros internos

Un error frecuente consiste en imaginar que una instrucción como:

```text
"Piensa cuidadosamente."
```

modifica directamente los parámetros del modelo.

No funciona así.

Los parámetros del modelo fueron aprendidos durante procesos de entrenamiento.

Conceptualmente:

```text
ENTRENAMIENTO
     │
     ▼
Datos + optimización
     │
     ▼
Parámetros aprendidos
     │
     ▼
MODELO
```

Cuando enviamos un prompt:

```text
PROMPT
   │
   ▼
MODELO YA ENTRENADO
   │
   ▼
INFERENCIA
   │
   ▼
SALIDA
```

El prompt normalmente **no reentrena el modelo**.

Le proporciona información e instrucciones dentro del proceso de inferencia.

Esta distinción es esencial:

| Concepto           | Momento                  |
| ------------------ | ------------------------ |
| Preentrenamiento   | Entrenamiento            |
| Fine-tuning        | Entrenamiento/adaptación |
| Instruction tuning | Entrenamiento/adaptación |
| Prompt             | Inferencia               |
| Contexto           | Inferencia               |
| Sampling           | Inferencia               |

---

# 9. Prompt Engineering y contexto

Un prompt tampoco existe necesariamente como un único bloque de texto.

En sistemas modernos podemos tener:

```text
┌───────────────────────────────┐
│       SISTEMA                 │
│ reglas generales              │
└──────────────┬────────────────┘
               │
┌──────────────▼────────────────┐
│       INSTRUCCIONES            │
│ tarea                         │
└──────────────┬────────────────┘
               │
┌──────────────▼────────────────┐
│       CONTEXTO                 │
│ documentos / datos / ejemplos │
└──────────────┬────────────────┘
               │
┌──────────────▼────────────────┐
│       SOLICITUD                │
│ pregunta del usuario           │
└──────────────┬────────────────┘
               │
               ▼
             MODELO
```

Por esta razón, en este repositorio debemos diferenciar:

**Prompt Engineering**

de

**Context Engineering**

El Prompt Engineering estudia principalmente cómo diseñamos instrucciones y entradas.

El Context Engineering estudia cómo construimos y administramos el conjunto de información que recibe el modelo para realizar una tarea.

Más adelante veremos esta diferencia en profundidad.

---

# 10. Componentes fundamentales de un prompt

Un prompt puede representarse mediante varios componentes.

```text
                    PROMPT
                      │
       ┌──────────────┼───────────────┐
       │              │               │
       ▼              ▼               ▼
 INSTRUCCIÓN      CONTEXTO        RESTRICCIONES
       │              │               │
       └──────────────┼───────────────┘
                      │
              ┌───────┴────────┐
              ▼                ▼
          EJEMPLOS        SALIDA ESPERADA
```

## 10.1 Instrucción

Define qué debe hacer el modelo.

Ejemplo:

```text
Clasifica cada comentario como:
positivo, negativo o neutro.
```

---

## 10.2 Contexto

Proporciona información necesaria para realizar la tarea.

```text
Producto: Laptop X15
Precio: $850
RAM: 16 GB
Almacenamiento: 512 GB SSD
```

---

## 10.3 Restricciones

Determinan qué debe o no debe hacer.

```text
Utiliza únicamente la información proporcionada.
No inventes especificaciones.
```

---

## 10.4 Ejemplos

Muestran patrones de entrada y salida.

```text
Entrada:
"El producto llegó rápidamente."

Salida:
"positivo"
```

---

## 10.5 Formato de salida

Especifica cómo debe responder.

```text
Devuelve:

{
  "categoria": "...",
  "confianza": 0.0
}
```

---

# 11. Una forma útil de pensar un prompt

Para comenzar a diseñar un prompt podemos hacernos seis preguntas:

```text
1. ¿QUÉ debe hacer?
        ↓
2. ¿CON QUÉ información?
        ↓
3. ¿BAJO QUÉ restricciones?
        ↓
4. ¿QUÉ debe evitar?
        ↓
5. ¿CÓMO debe responder?
        ↓
6. ¿CÓMO sabremos si lo hizo correctamente?
```

Esta última pregunta es especialmente importante.

Muchos prompts especifican la tarea:

```text
"Resume este documento."
```

Pero no especifican cómo evaluar el resultado.

Un diseño más profesional podría establecer:

```text
Resume el documento.

Requisitos:
- máximo 150 palabras;
- conserva los datos numéricos;
- no agregues información externa;
- identifica las conclusiones principales;
- utiliza lenguaje técnico.
```

Ahora podemos comprobar objetivamente varios requisitos.

---

# 12. Ejemplo progresivo

Supongamos que queremos analizar un texto financiero.

## Nivel 1 — Pregunta básica

```text
Analiza este documento.
```

Problema:

¿Qué significa analizar?

Puede significar:

* resumir;
* buscar errores;
* detectar riesgos;
* extraer datos;
* comparar cifras;
* evaluar cumplimiento.

La tarea es ambigua.

---

## Nivel 2 — Definir la tarea

```text
Analiza el documento financiero e identifica
posibles inconsistencias.
```

Mejor.

Pero todavía falta información.

---

## Nivel 3 — Definir criterios

```text
Analiza el documento financiero.

Identifica:

1. inconsistencias numéricas;
2. fechas contradictorias;
3. duplicados;
4. datos faltantes.
```

Ahora la tarea es mucho más concreta.

---

## Nivel 4 — Definir salida

```text
Para cada inconsistencia devuelve:

- tipo;
- descripción;
- evidencia;
- monto afectado;
- nivel de riesgo.
```

Ahora podemos evaluar la estructura.

---

## Nivel 5 — Definir restricciones

```text
Utiliza únicamente los datos presentes
en el documento.

No inventes información.

Si no existe evidencia suficiente,
indica "evidencia insuficiente".
```

Ahora reducimos uno de los problemas importantes de los sistemas generativos: la generación de información no sustentada.

---

## Nivel 6 — Diseñar para automatización

```text
Devuelve exclusivamente JSON válido
con esta estructura:

{
  "hallazgos": [
    {
      "tipo": "...",
      "descripcion": "...",
      "evidencia": "...",
      "monto": 0,
      "riesgo": "BAJO|MEDIO|ALTO|CRITICO"
    }
  ]
}
```

Ahora el resultado puede integrarse en software.

La evolución fue:

```text
Pregunta
   ↓
Tarea
   ↓
Criterios
   ↓
Restricciones
   ↓
Formato
   ↓
Automatización
```

Esto es mucho más cercano a la Ingeniería de Prompt profesional.

---

# 13. Prompt Engineering como interfaz entre humanos y modelos

Podemos considerar el prompt como una interfaz.

```text
┌─────────────────────┐
│       HUMANO        │
│                     │
│ intención           │
│ objetivo            │
│ conocimiento        │
└──────────┬──────────┘
           │
           │ lenguaje
           ▼
┌─────────────────────┐
│       PROMPT        │
│                     │
│ instrucciones       │
│ contexto            │
│ restricciones       │
│ ejemplos            │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│       MODELO        │
│                     │
│ representación      │
│ parámetros          │
│ arquitectura        │
│ inferencia          │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│      RESPUESTA      │
└─────────────────────┘
```

El ingeniero de prompt trabaja precisamente en esa interfaz.

Su objetivo es reducir la distancia entre:

```text
INTENCIÓN HUMANA
```

y

```text
COMPORTAMIENTO DEL SISTEMA
```

---

# 14. El problema de la ambigüedad

Los humanos podemos interpretar fácilmente frases incompletas debido al contexto.

Por ejemplo:

> "Hazlo más profesional."

Una persona puede preguntar:

* ¿qué parte?
* ¿para quién?
* ¿qué significa profesional?
* ¿qué tono?
* ¿qué extensión?
* ¿qué restricciones?

Un modelo puede intentar inferir las respuestas.

Pero inferir no significa necesariamente conocer.

Por eso una buena práctica es convertir conceptos subjetivos en criterios observables.

En lugar de:

```text
Hazlo profesional.
```

podemos definir:

```text
Utiliza lenguaje formal.

Evita expresiones coloquiales.

Organiza la información mediante
títulos y listas.

No utilices afirmaciones que no
estén respaldadas por los datos.
```

Transformamos:

```text
CONCEPTO ABSTRACTO
        ↓
CRITERIOS OBSERVABLES
```

Esta transformación es una de las habilidades centrales de la Ingeniería de Prompt.

---

# 15. Prompt Engineering y especificación

Un prompt profesional se parece en muchos aspectos a una especificación técnica.

Una especificación intenta responder:

```text
¿Qué debe hacer el sistema?
¿Qué datos recibe?
¿Qué restricciones existen?
¿Qué resultado debe producir?
¿Cómo se valida?
```

Un prompt bien diseñado puede utilizar la misma lógica.

```text
ENTRADA
   ↓
PROCESAMIENTO ESPERADO
   ↓
RESTRICCIONES
   ↓
SALIDA
   ↓
VALIDACIÓN
```

Por eso, a medida que avanzamos hacia sistemas profesionales, el Prompt Engineering comienza a mezclarse con:

* ingeniería de software;
* diseño de sistemas;
* evaluación;
* gestión de contexto;
* seguridad;
* automatización;
* ciencia de datos;
* interacción humano-computadora.

---

# 16. Prompt Engineering no garantiza verdad

Un punto fundamental:

> **Un prompt bien diseñado no convierte automáticamente al modelo en una fuente de verdad.**

Podemos escribir:

```text
"No inventes información."
```

Pero esa instrucción no garantiza matemáticamente que nunca exista una afirmación incorrecta.

El modelo sigue siendo un sistema generativo.

Por ello debemos distinguir:

```text
INSTRUCCIÓN
     ≠
GARANTÍA
```

Una instrucción puede orientar el comportamiento.

La verificación requiere mecanismos adicionales.

Por ejemplo:

```text
MODELO
  ↓
RESPUESTA
  ↓
VALIDACIÓN
  ↓
FUENTE / REGLA / CÓDIGO / HUMANO
  ↓
RESULTADO ACEPTADO
```

Este concepto será fundamental cuando estudiemos:

* RAG;
* herramientas;
* generación estructurada;
* evaluación;
* agentes;
* seguridad.

---

# 17. Prompt Engineering no sustituye el conocimiento del dominio

Supongamos que queremos crear un sistema para detectar anomalías contables.

Un ingeniero puede diseñar un excelente prompt.

Pero si no comprende:

* contabilidad;
* registros;
* débitos;
* créditos;
* conciliaciones;
* materialidad;
* controles internos;

será difícil determinar si la respuesta realmente tiene sentido.

Por eso:

```text
PROMPT ENGINEERING
          +
CONOCIMIENTO DEL DOMINIO
          +
CONOCIMIENTO DEL MODELO
          +
EVALUACIÓN
          ↓
SISTEMA ÚTIL
```

La Ingeniería de Prompt no elimina la necesidad de conocimiento especializado.

En muchos sistemas profesionales, la calidad depende de la combinación de ambos.

---

# 18. Prompt Engineering y diferentes modelos

Un mismo prompt puede comportarse de forma diferente en diferentes modelos.

Ejemplo conceptual:

```text
Prompt:

"Extrae todos los clientes cuyo saldo
sea superior a $10.000 y devuelve JSON."
```

Podemos probar:

```text
             MISMO PROMPT
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
     Modelo A  Modelo B  Modelo C
        │         │         │
        ▼         ▼         ▼
      JSON      JSON      TEXTO
```

Esto no significa automáticamente que un modelo sea "mejor".

Puede indicar diferencias en:

* entrenamiento;
* instruction tuning;
* arquitectura;
* capacidad de seguimiento de instrucciones;
* formato soportado;
* contexto;
* herramientas;
* parámetros de inferencia;
* restricciones del proveedor.

Por eso, en este repositorio aprenderemos a pensar:

> **"¿Qué comportamiento produce este sistema bajo estas condiciones?"**

en lugar de:

> **"¿Cuál es el prompt mágico?"**

---

# 19. El mito del "prompt perfecto"

No existe un prompt universalmente perfecto.

Un prompt puede ser excelente para:

```text
resumir documentos
```

y completamente inadecuado para:

```text
generar código
```

o:

```text
extraer datos estructurados
```

o:

```text
resolver problemas matemáticos
```

o:

```text
controlar un agente con herramientas
```

Por tanto:

```text
PROMPT
  │
  ├── Tarea
  ├── Modelo
  ├── Contexto
  ├── Restricciones
  ├── Herramientas
  └── Criterios de evaluación
          │
          ▼
       DISEÑO
```

El prompt debe diseñarse **para un sistema y una tarea concreta**.

---

# 20. De Prompt Engineering a System Engineering

Al principio podemos estudiar:

```text
¿Cómo escribo una instrucción?
```

Pero posteriormente la pregunta cambia:

```text
¿Cómo diseño un sistema que utilice
modelos de IA de manera confiable?
```

La evolución conceptual es:

```text
PROMPT
  ↓
PROMPT ENGINEERING
  ↓
CONTEXT ENGINEERING
  ↓
TOOLS
  ↓
RAG
  ↓
AGENTS
  ↓
EVALUATION
  ↓
SECURITY
  ↓
AI SYSTEM ENGINEERING
```

Este repositorio seguirá precisamente esa evolución.

---

# 21. Mapa conceptual

```text
                         INGENIERÍA DE PROMPT
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
              ▼                   ▼                   ▼
            OBJETIVO            MODELO             CONTEXTO
              │                   │                   │
              ▼                   ▼                   ▼
         Instrucciones        Arquitectura         Datos
         Restricciones        Entrenamiento         Documentos
         Ejemplos             Inferencia            Historial
              │                   │                   │
              └───────────────────┼───────────────────┘
                                  │
                                  ▼
                              PROMPT
                                  │
                                  ▼
                              INFERENCIA
                                  │
                   ┌──────────────┼──────────────┐
                   │              │              │
                   ▼              ▼              ▼
               Sampling       Herramientas     Modelo
                   │              │              │
                   └──────────────┼──────────────┘
                                  ▼
                               SALIDA
                                  │
                                  ▼
                              EVALUACIÓN
                                  │
                       ┌──────────┴──────────┐
                       ▼                     ▼
                    ACEPTAR                ITERAR
                                             │
                                             ▼
                                       NUEVO DISEÑO
```

---

# 22. La ecuación conceptual del Prompt Engineering

No existe una ecuación matemática universal que determine la calidad de un prompt.

Sin embargo, podemos utilizar un modelo conceptual:

```text
Calidad del resultado

≈

f(
    calidad de la instrucción,
    calidad del contexto,
    capacidad del modelo,
    arquitectura,
    inferencia,
    herramientas,
    restricciones,
    evaluación
)
```

Es importante notar el símbolo:

```text
≈
```

y no:

```text
=
```

Porque estamos describiendo una relación conceptual, no una ley matemática.

Una formulación todavía más importante es:

```text
Resultado útil
≠
Prompt bueno solamente
```

En sistemas reales:

```text
Resultado útil
=
Prompt
+
Modelo
+
Contexto
+
Inferencia
+
Herramientas
+
Validación
```

La forma exacta de esta relación depende del sistema.

---

# 23. ¿Qué estudia realmente un ingeniero de prompt?

Un profesional no debería limitarse a memorizar técnicas.

Debe aprender a responder preguntas como:

### Sobre el problema

```text
¿Qué necesito conseguir?
```

### Sobre el modelo

```text
¿Qué modelo estoy utilizando?
¿Qué capacidades tiene?
¿Qué limitaciones tiene?
```

### Sobre el contexto

```text
¿Qué información necesita?
¿Qué información sobra?
¿Cómo se organiza?
```

### Sobre la instrucción

```text
¿La tarea está definida sin ambigüedad?
```

### Sobre la salida

```text
¿Qué formato necesito?
¿Cómo comprobaré que es correcto?
```

### Sobre el sistema

```text
¿Necesita herramientas?
¿Necesita RAG?
¿Necesita memoria?
¿Necesita intervención humana?
```

### Sobre seguridad

```text
¿Qué ocurre si la entrada contiene instrucciones maliciosas?
```

### Sobre evaluación

```text
¿Cómo sé que el sistema realmente funciona?
```

---

# 24. Error fundamental: confundir longitud con calidad

Un prompt de 2.000 palabras no es necesariamente mejor que uno de 100.

Ejemplo:

```text
PROMPT A

"Resume este documento en 100 palabras
e identifica tres conclusiones principales."
```

Puede ser suficiente.

Mientras que:

```text
PROMPT B

Actúa como un experto...
utiliza razonamiento profundo...
sé extremadamente preciso...
analiza exhaustivamente...
hazlo profesional...
piensa como un investigador...
```

puede agregar muchas palabras sin definir realmente la tarea.

La pregunta correcta no es:

> "¿Cuántas palabras tiene mi prompt?"

Sino:

> **"¿Cuánta ambigüedad relevante queda sin resolver?"**

---

# 25. El principio de mínima especificación suficiente

Una buena práctica consiste en proporcionar suficiente información para definir correctamente la tarea, pero evitar instrucciones innecesarias.

```text
MUY POCA ESPECIFICACIÓN
          │
          ▼
       AMBIGÜEDAD
          │
          ▼
       RESULTADO
       INCONSISTENTE


MUCHA ESPECIFICACIÓN IRRELEVANTE
          │
          ▼
     COMPLEJIDAD
          │
          ▼
     CONFLICTOS
          │
          ▼
       DIFICULTAD
```

Buscamos:

```text
        ESPECIFICACIÓN
              │
              ▼
     SUFICIENTE Y RELEVANTE
              │
              ▼
         COMPORTAMIENTO
          MÁS CONTROLABLE
```

Este principio será especialmente importante cuando trabajemos con modelos pequeños y sistemas automatizados.

---

# 26. Prompt Engineering como proceso experimental

Una de las mejores formas de aprender Prompt Engineering es experimentar.

Supongamos:

### Prompt A

```text
Resume el documento.
```

### Prompt B

```text
Resume el documento en 100 palabras.
```

### Prompt C

```text
Resume el documento en 100 palabras.
Identifica tres ideas principales.
No agregues información externa.
```

### Prompt D

```text
Resume el documento en 100 palabras.

Incluye:
- tres ideas principales;
- datos numéricos importantes.

No agregues información externa.

Devuelve:

{
  "resumen": "...",
  "ideas": [],
  "datos": []
}
```

Podemos comparar:

```text
Prompt A ──► Resultado A
Prompt B ──► Resultado B
Prompt C ──► Resultado C
Prompt D ──► Resultado D
                  │
                  ▼
              EVALUACIÓN
```

Esto transforma el aprendizaje de:

```text
"Creo que este prompt funciona mejor."
```

a:

```text
"Probé cuatro variantes utilizando
criterios definidos y observé diferencias."
```

Esta mentalidad experimental es esencial.

---

# 27. Prompt Engineering y reproducibilidad

En investigación y sistemas profesionales interesa poder repetir un experimento.

Por ejemplo:

```text
Modelo:
X

Prompt:
versión 03

Temperatura:
T

Contexto:
documento A

Fecha:
2026-09-30

Resultado:
...
```

Podemos entonces cambiar una variable:

```text
Prompt v03
     │
     ▼
Resultado A

Prompt v04
     │
     ▼
Resultado B
```

Si modificamos simultáneamente:

* prompt;
* modelo;
* temperatura;
* contexto;

será difícil saber qué provocó el cambio.

Por eso la evaluación profesional utiliza conceptos como:

* variables;
* casos de prueba;
* conjuntos de evaluación;
* métricas;
* versiones;
* experimentos controlados.

---

# 28. Del prompt individual al sistema

Un prompt utilizado manualmente por una persona puede ser suficiente para una tarea puntual.

Pero cuando queremos automatizar:

```text
100
1.000
10.000
1.000.000
```

de solicitudes, aparecen nuevos problemas.

```text
PROMPT MANUAL
     │
     ▼
USUARIO
     │
     ▼
RESPUESTA
```

se transforma en:

```text
DATOS
  │
  ▼
APLICACIÓN
  │
  ▼
PROMPT
  │
  ▼
MODELO
  │
  ▼
VALIDACIÓN
  │
  ▼
BASE DE DATOS
  │
  ▼
USUARIO / SISTEMA
```

Aquí ya no estamos diseñando solamente un prompt.

Estamos diseñando un **sistema de IA**.

---

# 29. Prompt Engineering en 2026

En los sistemas modernos, el Prompt Engineering ya no se limita a escribir texto para un chatbot.

Puede involucrar:

* modelos de lenguaje;
* modelos multimodales;
* modelos especializados en código;
* modelos de razonamiento;
* salidas estructuradas;
* llamadas a herramientas;
* búsqueda;
* RAG;
* memoria;
* agentes;
* evaluación automática;
* evaluación humana;
* guardrails;
* observabilidad;
* seguridad;
* orquestación de modelos.

Por eso el concepto moderno debe entenderse como una disciplina dentro de una ingeniería de sistemas de IA más amplia.

---

# 30. Diferencia entre usuario de IA e ingeniero de prompt

| Usuario de IA              | Ingeniero de Prompt                  |
| -------------------------- | ------------------------------------ |
| Hace una pregunta          | Define una tarea                     |
| Busca una respuesta        | Define criterios de éxito            |
| Reescribe si falla         | Experimenta sistemáticamente         |
| Evalúa subjetivamente      | Utiliza criterios y métricas         |
| Usa un modelo              | Considera las capacidades del modelo |
| Puede ignorar el contexto  | Diseña el contexto                   |
| Acepta la respuesta        | Verifica la salida                   |
| Piensa en una conversación | Piensa en un sistema                 |

La diferencia no está simplemente en escribir prompts más largos.

Está en **pensar de manera sistemática sobre la interacción entre intención, modelo, contexto, inferencia y resultado**.

---

# 31. Principio central del capítulo

Podemos resumir todo el capítulo mediante una cadena:

```text
INTENCIÓN HUMANA
       ↓
ESPECIFICACIÓN
       ↓
PROMPT
       ↓
CONTEXTO
       ↓
MODELO
       ↓
INFERENCIA
       ↓
SALIDA
       ↓
EVALUACIÓN
       ↓
ITERACIÓN
```

Y existe una segunda cadena que debemos mantener siempre presente:

```text
PROMPT
   ≠
MODELO

PROMPT
   ≠
CONTEXTO

PROMPT
   ≠
SISTEMA COMPLETO
```

Un prompt es **una parte del sistema**.

---

# 32. Regla de oro

> ## No preguntes solamente "¿qué prompt debo escribir?"
>
> ## Pregunta:
>
> **"¿Qué comportamiento quiero obtener, qué información necesita el modelo, qué modelo está ejecutando la tarea, qué restricciones existen y cómo voy a comprobar que el resultado es correcto?"**

Esta pregunta marca el paso de utilizar IA a **diseñar sistemas que utilizan IA**.

---

# 33. Qué aprenderemos a continuación

Después de comprender qué es Prompt Engineering, debemos descomponerlo en sus elementos.

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
              │
              ▼
07 — Delimitadores
              │
              ▼
08 — Zero-Shot
              │
              ▼
09 — Few-Shot
              │
              ▼
10 — Ejemplos
              │
              ▼
11 — Salidas
              │
              ▼
12 — Prompts Modulares
              │
              ▼
13 — Plantillas
              │
              ▼
14 — Metaprompting
              │
              ▼
15 — Prompt Chaining
              │
              ▼
16 — Antipatrones
```

Cada técnica deberá estudiarse respondiendo cuatro preguntas:

```text
¿QUÉ ES?
   ↓
¿CÓMO FUNCIONA?
   ↓
¿POR QUÉ PUEDE FUNCIONAR?
   ↓
¿CUÁNDO FALLA?
```

De esta manera, el alumno no memorizará una colección de "trucos de prompting".

Aprenderá a **razonar sobre el comportamiento de los sistemas de IA**.

---

# 34. Resumen del capítulo

**Prompt Engineering** es la disciplina de diseñar y optimizar entradas e instrucciones para interactuar con modelos de IA de manera controlada y reproducible.

Sus elementos fundamentales incluyen:

```text
                 PROMPT ENGINEERING
                         │
       ┌─────────────────┼─────────────────┐
       ▼                 ▼                 ▼
    OBJETIVO          CONTEXTO          MODELO
       │                 │                 │
       ▼                 ▼                 ▼
 INSTRUCCIONES        DATOS          ARQUITECTURA
 RESTRICCIONES       EJEMPLOS        CAPACIDADES
 FORMATO             HISTORIAL       INFERENCIA
       │                 │                 │
       └─────────────────┼─────────────────┘
                         ▼
                       SALIDA
                         │
                         ▼
                     EVALUACIÓN
                         │
                         ▼
                      ITERACIÓN
```

La idea más importante es:

> **Un prompt no existe aislado. Su comportamiento depende del modelo, la arquitectura, el contexto, el entrenamiento, el mecanismo de inferencia, las herramientas y el sistema que lo ejecuta.**

Por eso, el objetivo de este repositorio no será enseñar únicamente a escribir prompts.

Será enseñar a comprender **por qué una instrucción produce determinado comportamiento y cómo diseñar sistemas de IA que puedan evaluarse, mejorarse y controlarse**.

---

## Conceptos que el estudiante debe dominar

Al terminar este capítulo, el estudiante debería poder explicar con sus propias palabras:

* qué es Prompt Engineering;
* qué diferencia existe entre prompt e instrucción;
* por qué el modelo influye en el resultado;
* por qué el contexto forma parte del comportamiento del sistema;
* por qué un prompt no garantiza que la respuesta sea verdadera;
* por qué la evaluación es necesaria;
* qué diferencia existe entre escribir un prompt y diseñar un sistema;
* por qué un prompt largo no necesariamente es mejor;
* por qué los prompts deben probarse experimentalmente;
* qué relación existe entre Prompt Engineering y Context Engineering.

### Idea para recordar

```text
┌─────────────────────────────────────────────┐
│                                             │
│  NO BUSQUES EL "PROMPT MÁGICO".             │
│                                             │
│  ENTIENDE EL SISTEMA.                       │
│                                             │
│  OBJETIVO → CONTEXTO → PROMPT → MODELO     │
│                    ↓                        │
│                INFERENCIA                   │
│                    ↓                        │
│                 SALIDA                      │
│                    ↓                        │
│               EVALUACIÓN                    │
│                                             │
└─────────────────────────────────────────────┘
```
