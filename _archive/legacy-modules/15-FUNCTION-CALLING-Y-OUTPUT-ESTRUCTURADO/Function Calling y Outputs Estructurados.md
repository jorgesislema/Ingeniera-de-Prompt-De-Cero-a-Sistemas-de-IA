# 15. FUNCTION CALLING Y OUTPUTS ESTRUCTURADOS

**Fecha:** Julio 2026
**Nivel:** Ingenieria de Prompt (practico)
**Prerrequisitos:** Fundamentos de prompting, conocimiento basico de Python

---

## INDICE

```
1. INTRODUCCION ............................................................. linea  35
2. OUTPUTS ESTRUCTURADOS (JSON Mode) ....................................... linea  80
3. FUNCTION CALLING (Tools) ................................................ linea 220
4. MULTI-TOOL ORCHESTRATION ................................................ linea 400
5. PARALLEL FUNCTION CALLING ............................................... linea 530
6. STRUCTURED OUTPUT + FUNCTION CALLING COMBINADOS ......................... linea 630
7. BEST PRACTICES Y ANTI-PATRONES .......................................... linea 760
8. EJERCICIOS PRACTICOS .................................................... linea 870
9. RESUMEN Y SIGUIENTES PASOS .............................................. linea 1070
```

---

## 1. INTRODUCCION

### 1.1 Que es Function Calling y por que importa

Function Calling (llamada a funciones) es la capacidad que tienen los modelos de lenguaje modernos
de decidir CUANDO invocar una funcion externa y CON QUE parametros hacerlo. Es, sin exagerar,
la frontera que separa a un escritor de prompts de un ingeniero de prompts.

Un LLM sin function calling:
- Solo puede generar texto.
- Si le preguntas "cual es la temperatura en Madrid ahora mismo", inventa o alucina.
- No puede interactuar con sistemas externos (bases de datos, APIs, correo electronico...).

Un LLM con function calling:
- Puede decidir que necesita llamar a get_weather(city="Madrid").
- Recibe datos reales de una API meteorologica.
- Integra esos datos en su respuesta final como si los "supiera".

### 1.2 Explicacion como para un nino

Imagina que tienes un amigo invisible muy inteligente que vive en una habitacion sin ventanas.
Le puedes hacer preguntas y el te responde basandose en todo lo que ha leido en libros.

Si le preguntas "esta lloviendo ahora en mi calle?", el amigo invisible puede:
a) Inventarse una respuesta basada en estadisticas historicas (alucinacion).
b) Decir "no lo se, no tengo ventanas".

PERO si le das un telefono y una lista de numeros a los que llamar (las "funciones"),
ahora tu amigo invisible puede:
a) Decir "espera, voy a llamar a la estacion meteorologica".
b) Marcar el numero correcto, con los parametros correctos (tu ciudad).
c) Escuchar la respuesta real.
d) Decirte "si, esta lloviendo, la estacion dice que hay 90% de humedad y 12 grados".

Eso es function calling. El telefono son las herramientas (tools). La guia telefonica es la
descripcion de cada tool. Y tu amigo invisible es el LLM decidiendo cual usar.

### 1.3 El problema que resuelve

```
+-------------------+     "Cual es el precio de las acciones de Apple?"
|   Usuario         | -------------------------------------------------.
+-------------------+                                                  |
                                                                        v
                                              +------------------------------------------+
                                              | LLM SIN function calling                 |
                                              | Respuesta: "No tengo acceso a datos      |
                                              | en tiempo real. Las acciones de Apple    |
                                              | cotizaban alrededor de $190 en mi        |
                                              | ultima actualizacion de entrenamiento."  |
                                              +------------------------------------------+

+-------------------+     "Cual es el precio de las acciones de Apple?"
|   Usuario         | -------------------------------------------------.
+-------------------+                                                  |
                                                                        v
                                              +------------------------------------------+
                                              | LLM CON function calling                 |
                                              | "Necesito llamar a                       |
                                              |  get_stock_price(symbol='AAPL')"         |
                                              +------------------------------------------+
                                                        |
                                                        v
                                              +------------------------------------------+
                                              | Tu codigo ejecuta la funcion, consulta    |
                                              | una API financiera real, obtiene $213.45 |
                                              +------------------------------------------+
                                                        |
                                                        v
                                              +------------------------------------------+
                                              | LLM recibe el dato real y responde:      |
                                              | "El precio actual de Apple (AAPL) es     |
                                              |  de $213.45 por accion."                 |
                                              +------------------------------------------+
```

**En resumen:** Function calling convierte al LLM de un oraculo que "dice cosas" en un
sistema que "hace cosas". Esto es ingenieria de prompt, no solo escritura de prompts.

---

## 2. OUTPUTS ESTRUCTURADOS (JSON Mode)

### 2.1 Que es el output estructurado

Normalmente, un LLM devuelve texto libre. Por ejemplo:

```
El cliente Juan Perez compro 3 items: una laptop por $1200, un mouse por $45
y un teclado por $89. El total fue de $1334 y la compra se realizo el
15 de marzo de 2026.
```

Para un humano, esto es perfectamente legible. Pero para un programa que necesita procesar
cientos de facturas automaticamente, es un desastre. Tienes que hacer parsing, usar regex,
lidiar con variaciones de formato, fechas en distintos formatos, etc.

El output estructurado resuelve esto: le dices al LLM que responda en formato JSON, y el
modelo garantiza que su respuesta sera JSON valido y que seguira un esquema predefinido.

### 2.2 El diagrama de flujo

```
FLUJO TRADICIONAL (fragil):
======================

  Prompt -----> [ LLM ] -----> "El total es $1,334.00 y la fecha es 15/03/2026"
                                  |
                                  v
                            Tu codigo de parsing (regex, splits, dolor...)
                                  |
                                  v
                            {"total": 1334.0, "fecha": "2026-03-15"}  (a veces falla)


FLUJO CON OUTPUT ESTRUCTURADO (robusto):
=====================================

  Prompt + response_format -----> [ LLM ] -----> {"total": 1334.0, "fecha": "2026-03-15",
                                                   "cliente": "Juan Perez",
                                                   "items": [...]}
                                                    |
                                                    v
                                              json.loads() y listo.
                                              Sin parsing, sin regex, sin lagrimas.
```

### 2.3 Como usar JSON Mode con OpenAI SDK

Hay dos niveles de output estructurado: el basico (JSON Mode) y el avanzado (JSON Schema).

#### 2.3.1 JSON Mode basico (response_format)

```python
from openai import OpenAI

client = OpenAI()

respuesta = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "system",
            "content": "Eres un asistente que extrae datos de facturas."
        },
        {
            "role": "user",
            "content": """
            Factura #2026-0342
            Cliente: Maria Garcia
            Fecha: 2026-07-10
            Items:
              - Monitor 27" 4K: 2 unidades x $450 = $900
              - Cable HDMI 2.1: 5 unidades x $15 = $75
              - Soporte VESA: 1 unidad x $45 = $45
            Total: $1,020.00
            """
        }
    ],
    response_format={"type": "json_object"}  # <-- Activa el modo JSON
)

import json
datos = json.loads(respuesta.choices[0].message.content)

print(f"Cliente: {datos['cliente']}")
print(f"Total: {datos['total']}")
print(f"Numero de items: {len(datos['items'])}")
# Output:
# Cliente: Maria Garcia
# Total: 1020.0
# Numero de items: 3
```

**CRITICO:** En el prompt debes incluir la palabra "JSON" en algun lugar. Si no lo haces,
el modelo puede fallar. Por ejemplo, el system prompt debe decir algo como
"Responde SIEMPRE en formato JSON" o "Devuelve un objeto JSON con los siguientes campos...".

#### 2.3.2 JSON Schema forzado (recomendado para produccion)

Desde 2025, OpenAI soporta `json_schema` en `response_format`, lo que permite ESPECIFICAR
exactamente la estructura que debe tener la respuesta. El modelo NO PUEDE desviarse del esquema.

```python
from openai import OpenAI

client = OpenAI()

respuesta = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "system",
            "content": "Clasifica opiniones de clientes con su sentimiento y puntuacion."
        },
        {
            "role": "user",
            "content": "El producto llego roto y el servicio al cliente fue terrible. Nunca mas compro aqui."
        }
    ],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "analisis_sentimiento",
            "strict": True,
            "schema": {
                "type": "object",
                "properties": {
                    "sentimiento": {
                        "type": "string",
                        "enum": ["positivo", "negativo", "neutral"]
                    },
                    "puntuacion_confianza": {
                        "type": "number",
                        "minimum": 0,
                        "maximum": 1
                    },
                    "aspectos_mencionados": {
                        "type": "array",
                        "items": {
                            "type": "string"
                        }
                    },
                    "resumen_una_linea": {
                        "type": "string"
                    },
                    "requiere_accion": {
                        "type": "boolean"
                    }
                },
                "required": [
                    "sentimiento",
                    "puntuacion_confianza",
                    "aspectos_mencionados",
                    "resumen_una_linea",
                    "requiere_accion"
                ],
                "additionalProperties": False
            }
        }
    }
)

resultado = json.loads(respuesta.choices[0].message.content)
print(resultado)
# Output garantizado:
# {
#   "sentimiento": "negativo",
#   "puntuacion_confianza": 0.95,
#   "aspectos_mencionados": ["producto", "servicio al cliente", "entrega"],
#   "resumen_una_linea": "Cliente insatisfecho por producto danado y mal servicio.",
#   "requiere_accion": true
# }
```

### 2.4 Comparativa MALO vs BUENO: extrayendo datos de una factura

**MALO: Depender de parsing de texto libre**

```python
# ESTO ES FRAGIL. Si el LLM cambia una coma, un salto de linea,
# o el orden de los campos, tu codigo se rompe.

respuesta = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Extrae los datos de esta factura: ..."}]
)
texto = respuesta.choices[0].message.content

# Intentando extraer el total con regex (pesadilla)
import re
match = re.search(r'Total:\s*\$?([\d,]+\.?\d*)', texto)
# Esto falla si la factura dice "TOTAL", "total a pagar", "$ 1.020", etc.
```

**BUENO: Usar JSON Schema para garantizar estructura**

```python
respuesta = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Extrae los datos de esta factura: ..."}],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "factura",
            "strict": True,
            "schema": {
                "type": "object",
                "properties": {
                    "numero_factura": {"type": "string"},
                    "cliente": {"type": "string"},
                    "fecha": {"type": "string", "format": "date"},
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "descripcion": {"type": "string"},
                                "cantidad": {"type": "integer"},
                                "precio_unitario": {"type": "number"},
                                "subtotal": {"type": "number"}
                            },
                            "required": ["descripcion", "cantidad", "precio_unitario", "subtotal"],
                            "additionalProperties": False
                        }
                    },
                    "total": {"type": "number"},
                    "moneda": {"type": "string", "enum": ["USD", "EUR", "MXN", "ARS", "COP"]}
                },
                "required": ["numero_factura", "cliente", "fecha", "items", "total", "moneda"],
                "additionalProperties": False
            }
        }
    }
)

datos = json.loads(respuesta.choices[0].message.content)
# Ahora datos['total'] SIEMPRE es un numero.
# datos['items'] SIEMPRE es un array de objetos con la estructura correcta.
# No hay sorpresas. Es garantizado.
```

### 2.5 Caso practico: clasificador de sentimiento con puntuacion

```python
from openai import OpenAI
import json

client = OpenAI()

def clasificar_sentimiento(texto: str) -> dict:
    """
    Clasifica el sentimiento de un texto y devuelve un diccionario
    con el resultado estructurado y garantizado.
    """
    respuesta = client.chat.completions.create(
        model="gpt-4o",
        messages=[
            {
                "role": "system",
                "content": (
                    "Eres un analista de sentimiento experto. "
                    "Clasifica el texto del usuario como positivo, negativo o neutral. "
                    "Proporciona una puntuacion de confianza entre 0 y 1."
                )
            },
            {"role": "user", "content": texto}
        ],
        response_format={
            "type": "json_schema",
            "json_schema": {
                "name": "clasificacion_sentimiento",
                "strict": True,
                "schema": {
                    "type": "object",
                    "properties": {
                        "sentimiento": {
                            "type": "string",
                            "enum": ["positivo", "negativo", "neutral"]
                        },
                        "confianza": {
                            "type": "number",
                            "minimum": 0,
                            "maximum": 1
                        },
                        "palabras_clave": {
                            "type": "array",
                            "items": {"type": "string"}
                        },
                        "explicacion_breve": {
                            "type": "string"
                        }
                    },
                    "required": ["sentimiento", "confianza", "palabras_clave", "explicacion_breve"],
                    "additionalProperties": False
                }
            }
        },
        temperature=0.0  # Clasificacion debe ser determinista
    )

    return json.loads(respuesta.choices[0].message.content)


# Prueba
resultado = clasificar_sentimiento(
    "Me encanto el producto, llego antes de lo esperado y el embalaje era perfecto."
)
print(json.dumps(resultado, indent=2, ensure_ascii=False))
```

**Salida esperada (garantizada en estructura):**

```json
{
  "sentimiento": "positivo",
  "confianza": 0.96,
  "palabras_clave": ["encanto", "perfecto", "antes de lo esperado"],
  "explicacion_breve": "El cliente expresa satisfaccion con el producto y la entrega."
}
```

---

## 3. FUNCTION CALLING (Tools)

### 3.1 Que es exactamente Function Calling

Function Calling NO significa que el LLM ejecute codigo. El LLM nunca ejecuta nada.
Lo que hace es:

1. **Analizar** el mensaje del usuario.
2. **Decidir** si necesita usar una herramienta (tool).
3. **Devolver** el nombre de la funcion y los parametros en formato estructurado.
4. **TU codigo** ejecuta la funcion con esos parametros.
5. **TU codigo** le devuelve el resultado al LLM.
6. **El LLM** genera la respuesta final integrando el resultado.

```
 [Usuario] ---> [LLM] ---> "Necesito get_weather(city='Madrid', units='metric')"
                              |
                              v  (TU CODIGO)
                         import requests
                         response = requests.get(f".../weather?city=Madrid&units=metric")
                         datos = response.json()
                              |
                              v
 [Usuario] <--- [LLM] <--- "La temperatura en Madrid es de 28C con cielo despejado."
```

### 3.2 Explicacion como para un nino: el chef y el asistente

Eres un chef en un restaurante. Un cliente te pide "una ensalada con los ingredientes
mas frescos que tengas".

Sin un asistente:
- Vas a la cocina y usas lo que recuerdas que hay en la nevera.
- "Creo que hay lechuga... o era repollo? No me acuerdo."
- Preparas algo que quizas no sea lo optimo.

Con un asistente (function calling):
- Le dices al asistente: "Anda, revisa la nevera y dime que verduras hay".
- El asistente va, mira, y te dice: "Hay lechuga romana, tomate cherry, pepino y aguacate".
- Ahora TU (el chef/LLM) preparas la ensalada con datos REALES.

En esta analogia:
- El chef = el LLM
- El asistente = tus funciones/tools
- "Revisa la nevera" = la decision del LLM de llamar a una funcion
- Lo que el asistente encuentra = el resultado de la funcion

### 3.3 Anatomia de una Tool (definicion)

Cada tool se define con tres elementos:

```
+----------------------------------------------------+
| TOOL: get_weather                                  |
|                                                    |
| name: "get_weather"                                |
|   (identificador unico)                            |
|                                                    |
| description: "Obtiene el clima actual para una     |
|   ciudad especifica. Usa esta funcion cuando el    |
|   usuario pregunte sobre temperatura, lluvia,      |
|   humedad, viento o condiciones climaticas."       |
|   (ESTO ES PROMPT ENGINEERING APLICADO A TOOLS!)   |
|                                                    |
| parameters: JSON Schema que define:                |
|   - city (string, required): nombre de la ciudad   |
|   - units (string, enum): "metric" o "imperial"    |
|                                                    |
+----------------------------------------------------+
```

### 3.4 Ejemplo practico: Tool de clima

```python
from openai import OpenAI
import json
import requests  # O usa cualquier libreria HTTP

client = OpenAI()

# Paso 1: Definir la herramienta (tool definition)
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": (
                "Obtiene las condiciones climaticas actuales para una ciudad especifica. "
                "Usa esta funcion cuando el usuario pregunte sobre temperatura, humedad, "
                "lluvia, viento, o condiciones meteorologicas actuales o pronosticos. "
                "NO uses esta funcion para preguntas sobre clima historico o cambio climatico."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "Nombre de la ciudad, ej: 'Madrid', 'Buenos Aires', 'Ciudad de Mexico'"
                    },
                    "units": {
                        "type": "string",
                        "enum": ["metric", "imperial"],
                        "description": "Sistema de unidades: 'metric' para Celsius, 'imperial' para Fahrenheit"
                    }
                },
                "required": ["city", "units"],
                "additionalProperties": False
            }
        }
    }
]

# Paso 2: El usuario hace una pregunta
messages = [
    {"role": "user", "content": "Cual es la temperatura en Buenos Aires? Dame los datos en Celsius."}
]

# Paso 3: El LLM decide si necesita llamar a una funcion
response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
    tool_choice="auto"  # El LLM decide si usar tool o no
)

# Paso 4: Extraer la llamada a funcion que el LLM quiere hacer
tool_calls = response.choices[0].message.tool_calls

if tool_calls:
    # El LLM decidio que necesita ejecutar una tool
    for tool_call in tool_calls:
        function_name = tool_call.function.name
        arguments = json.loads(tool_call.function.arguments)

        print(f"LLM decidio llamar a: {function_name}")
        print(f"Con argumentos: {arguments}")
        # Output:
        # LLM decidio llamar a: get_weather
        # Con argumentos: {"city": "Buenos Aires", "units": "metric"}

        # Paso 5: TU CODIGO ejecuta la funcion real
        # (Aqui iria la llamada a la API de clima)
        # Simulamos la respuesta:
        weather_result = {
            "city": "Buenos Aires",
            "temperature": 22,
            "humidity": 65,
            "conditions": "parcialmente nublado",
            "wind_speed": 12,
            "units": "metric"
        }

        # Paso 6: Agregar el resultado al historial de mensajes
        messages.append({
            "role": "assistant",
            "content": None,
            "tool_calls": [{
                "id": tool_call.id,
                "type": "function",
                "function": {
                    "name": function_name,
                    "arguments": json.dumps(arguments)
                }
            }]
        })
        messages.append({
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": json.dumps(weather_result)
        })

    # Paso 7: El LLM genera la respuesta final con los datos reales
    final_response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages
    )

    print("\nRespuesta final del LLM:")
    print(final_response.choices[0].message.content)
    # Output:
    # La temperatura actual en Buenos Aires es de 22C con humedad del 65%
    # y condiciones parcialmente nubladas. El viento sopla a 12 km/h.
```

### 3.5 Ejemplo: Tool de busqueda en base de datos

```python
# Definicion de una tool para consultar una base de datos de pedidos
tools_bd = [
    {
        "type": "function",
        "function": {
            "name": "search_orders",
            "description": (
                "Busca pedidos en la base de datos por ID de pedido, email de cliente, "
                "o estado del pedido. Usa esta funcion cuando el usuario pregunte sobre "
                "el estado de un pedido, quiera consultar detalles de una orden, o necesite "
                "informacion sobre compras. Devuelve los pedidos que coinciden con los criterios."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "order_id": {
                        "type": "string",
                        "description": "ID del pedido, ej: 'ORD-2026-0042'. Opcional si se proporciona email."
                    },
                    "customer_email": {
                        "type": "string",
                        "description": "Email del cliente para buscar todos sus pedidos. Opcional si se proporciona order_id."
                    },
                    "status": {
                        "type": "string",
                        "enum": ["pendiente", "enviado", "entregado", "cancelado", "devuelto"],
                        "description": "Filtrar por estado del pedido. Opcional."
                    }
                },
                "required": [],
                "additionalProperties": False
            }
        }
    }
]

def ejecutar_busqueda_pedidos(order_id=None, customer_email=None, status=None):
    """
    Funcion real que consulta la base de datos.
    En produccion, esto seria una consulta SQL o llamada a API.
    """
    # Simulacion de base de datos
    pedidos_falsos = [
        {"order_id": "ORD-2026-0042", "customer_email": "maria@email.com",
         "producto": "Monitor 4K", "total": 450.00, "status": "entregado", "fecha": "2026-06-15"},
        {"order_id": "ORD-2026-0089", "customer_email": "carlos@email.com",
         "producto": "Teclado mecanico", "total": 129.00, "status": "enviado", "fecha": "2026-07-01"},
        {"order_id": "ORD-2026-0120", "customer_email": "maria@email.com",
         "producto": "Cable HDMI", "total": 15.00, "status": "pendiente", "fecha": "2026-07-09"},
    ]

    resultados = []
    for p in pedidos_falsos:
        match = True
        if order_id and p["order_id"] != order_id:
            match = False
        if customer_email and p["customer_email"] != customer_email:
            match = False
        if status and p["status"] != status:
            match = False
        if match:
            resultados.append(p)

    return resultados
```

### 3.6 Ejemplo: Tool de envio de email

```python
tools_email = [
    {
        "type": "function",
        "function": {
            "name": "send_email",
            "description": (
                "Envia un correo electronico al destinatario especificado. "
                "Usa esta funcion SOLO cuando el usuario solicite explicitamente enviar un email. "
                "ANTES de llamar a esta funcion, muestra al usuario un resumen de lo que vas a enviar "
                "y pide confirmacion. NO envies emails sin confirmacion explicita del usuario."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "to": {
                        "type": "string",
                        "format": "email",
                        "description": "Direccion de email del destinatario"
                    },
                    "subject": {
                        "type": "string",
                        "description": "Asunto del correo"
                    },
                    "body": {
                        "type": "string",
                        "description": "Cuerpo del mensaje en texto plano o HTML"
                    },
                    "cc": {
                        "type": "string",
                        "format": "email",
                        "description": "Direccion de email para copia. Opcional."
                    }
                },
                "required": ["to", "subject", "body"],
                "additionalProperties": False
            }
        }
    }
]

def enviar_email_real(to, subject, body, cc=None):
    """
    Envia un email real usando un servicio SMTP o API de email.
    En produccion, usa SendGrid, AWS SES, Mailgun, etc.
    ADVERTENCIA: Siempre implementa confirmacion del usuario antes de enviar.
    """
    print(f"\n[CONFIRMACION REQUERIDA]")
    print(f"  Para: {to}")
    print(f"  CC: {cc if cc else 'N/A'}")
    print(f"  Asunto: {subject}")
    print(f"  Cuerpo: {body[:100]}...")
    confirmacion = input("\n  Enviar email? (si/no): ")

    if confirmacion.lower() != "si":
        return {"enviado": False, "mensaje": "Envio cancelado por el usuario."}

    # Aqui iria la logica real de envio
    # sendgrid_client.send(...) o smtplib.SMTP(...)

    return {
        "enviado": True,
        "mensaje": f"Email enviado exitosamente a {to}",
        "timestamp": "2026-07-12T14:30:00Z"
    }
```

### 3.7 El parametro tool_choice

Controla cuando y como el LLM debe usar las herramientas.

```python
# Opcion 1: "auto" (recomendado para la mayoria de casos)
# El LLM decide por si mismo si necesita una herramienta o no.
response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
    tool_choice="auto"
)

# Opcion 2: "required"
# El LLM DEBE usar al menos una herramienta. Util si siempre esperas
# que se llame a una funcion (ej: pipeline de procesamiento automatico).
response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
    tool_choice="required"
)

# Opcion 3: "none"
# El LLM NO puede usar herramientas. Responde directamente.
# Util cuando temporalmente quieres desactivar tools sin quitarlas de la definicion.
response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
    tool_choice="none"
)

# Opcion 4: Forzar una tool especifica
# El LLM DEBE usar exactamente esta tool. Util en pipelines donde sabes
# que funcion debe ejecutarse.
response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools,
    tool_choice={"type": "function", "function": {"name": "get_weather"}}
)
```

---

## 4. MULTI-TOOL ORCHESTRATION

### 4.1 Que es la orquestacion multi-tool

Cuando le das al LLM acceso a VARIAS herramientas, el modelo debe decidir no solo
SI usar una herramienta, sino CUAL usar y en QUE ORDEN.

Es como si al asistente del chef le dieras acceso a:
- La nevera (consultar ingredientes)
- El libro de recetas (buscar recetas)
- El telefono del proveedor (hacer pedidos)
- La caja registradora (cobrar)

El asistente debe decidir cual usar segun lo que el cliente pida.

### 4.2 Diagrama de flujo multi-tool

```
Usuario: "Enviame un resumen del pedido ORD-2026-0042 por email a maria@email.com"

[LLM ANALIZA]
       |
       | "Necesito: 1) buscar el pedido, 2) enviar el resumen por email"
       |
       +----> [search_orders(order_id="ORD-2026-0042")]
       |              |
       |              v
       |      Resultado: {"order_id": "ORD-2026-0042", "producto": "Monitor 4K",
       |                   "total": 450.00, "status": "entregado", "fecha": "2026-06-15"}
       |
       |      (El LLM recibe esto y AHORA tiene los datos para componer el email)
       |
       +----> [send_email(to="maria@email.com",
       |                  subject="Resumen de tu pedido ORD-2026-0042",
       |                  body="Tu pedido de Monitor 4K por $450.00 fue entregado...")]
       |              |
       |              v
       |      Resultado: {"enviado": true, "timestamp": "..."}
       |
       v
[LLM] -> "Te he enviado un email a maria@email.com con el resumen de tu pedido
         ORD-2026-0042. El Monitor 4K por $450.00 fue entregado el 15 de junio."
```

### 4.3 La descripcion de la tool ES un prompt

Esto es fundamental y a menudo se pasa por alto. La descripcion de cada tool es,
literalmente, un prompt que le dice al LLM CUANDO y PARA QUE usar esa herramienta.

**MALO: Descripcion vaga o generica**

```python
# El LLM no sabra cuando llamar a estas funciones
tools_malas = [
    {
        "type": "function",
        "function": {
            "name": "get_data",
            "description": "Obtiene datos.",
            "parameters": {"type": "object", "properties": {"query": {"type": "string"}}}
        }
    },
    {
        "type": "function",
        "function": {
            "name": "send_stuff",
            "description": "Envia cosas.",
            "parameters": {"type": "object", "properties": {"to": {"type": "string"}, "content": {"type": "string"}}}
        }
    }
]
# Resultado: El LLM llamara a get_data para todo, o a send_stuff para todo,
# o no llamara a ninguna porque no entiende para que sirven.
```

**BUENO: Descripcion especifica, clara y con contexto**

```python
tools_buenas = [
    {
        "type": "function",
        "function": {
            "name": "lookup_customer_order",
            "description": (
                "Busca un pedido en la base de datos usando el ID de pedido o el email del cliente. "
                "Usa esta funcion cuando el usuario pregunte por el estado de un pedido, quiera "
                "consultar detalles de su compra, o hacer seguimiento de un envio. "
                "Devuelve: ID, producto, precio, estado (pendiente/enviado/entregado/cancelado), "
                "fecha de compra y numero de seguimiento del envio. "
                "NO uses esta funcion para preguntas sobre productos en general o disponibilidad."
            ),
            "parameters": { ... }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "check_product_inventory",
            "description": (
                "Consulta el inventario actual de un producto en un almacen especifico. "
                "Usa esta funcion cuando el usuario pregunte si hay stock disponible, "
                "cuantas unidades quedan, o si un producto esta agotado. "
                "Devuelve: cantidad disponible, ubicacion del almacen, fecha estimada "
                "de reposicion si esta agotado. "
                "NO uses esta funcion para precios de productos o detalles que no sean inventario."
            ),
            "parameters": { ... }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "initiate_refund",
            "description": (
                "Inicia un proceso de reembolso para un pedido especifico. "
                "Usa esta funcion SOLO cuando el usuario solicite explicitamente un reembolso "
                "o devolucion, y SOLO despues de haber verificado que el pedido existe "
                "(usa primero lookup_customer_order). "
                "Devuelve: ID de reembolso, monto, estado (iniciado/procesado/completado). "
                "IMPORTANTE: Esta funcion tiene efectos reales (dinero). Siempre pide "
                "confirmacion al usuario antes de ejecutarla."
            ),
            "parameters": { ... }
        }
    }
]
```

### 4.4 Ejemplo completo: agente de servicio al cliente

```python
from openai import OpenAI
import json

client = OpenAI()

# Definimos 3 herramientas de atencion al cliente
tools_atencion_cliente = [
    {
        "type": "function",
        "function": {
            "name": "lookup_order",
            "description": (
                "Busca un pedido por ID de orden o email del cliente. "
                "Usa cuando el cliente pregunte 'donde esta mi pedido', 'cuando llega', "
                "'cual es el estado de mi orden', 'que pedi', etc. "
                "Devuelve los datos completos del pedido."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "order_id": {
                        "type": "string",
                        "description": "ID del pedido (ej: ORD-2026-0042). Opcional si se da email."
                    },
                    "customer_email": {
                        "type": "string",
                        "description": "Email del cliente. Opcional si se da order_id."
                    }
                },
                "required": [],
                "additionalProperties": False
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "check_inventory",
            "description": (
                "Consulta el inventario de un producto. "
                "Usa cuando el cliente pregunte 'tienen stock de X', 'hay disponibilidad', "
                "'queda algun Y', 'se agoto Z'. "
                "Devuelve la cantidad disponible y fecha estimada de reposicion."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "product_name": {
                        "type": "string",
                        "description": "Nombre del producto a consultar"
                    }
                },
                "required": ["product_name"],
                "additionalProperties": False
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "initiate_refund",
            "description": (
                "Inicia un proceso de reembolso. "
                "Usa SOLO cuando el cliente pida explicitamente un reembolso, devolucion, "
                "o cancelacion con reembolso. PRIMERO verifica el pedido con lookup_order. "
                "PIDE CONFIRMACION al cliente antes de ejecutar por los efectos monetarios. "
                "Devuelve el ID de reembolso y el estado del proceso."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "order_id": {"type": "string", "description": "ID del pedido a reembolsar"},
                    "reason": {
                        "type": "string",
                        "enum": ["producto_danado", "no_recibido", "no_coincide", "arrepentimiento", "otro"],
                        "description": "Motivo del reembolso"
                    }
                },
                "required": ["order_id", "reason"],
                "additionalProperties": False
            }
        }
    }
]


# Simulacion de las funciones reales
def ejecutar_lookup_order(order_id=None, customer_email=None):
    """Simula una consulta a la base de datos de pedidos."""
    pedidos = {
        "ORD-2026-0042": {
            "order_id": "ORD-2026-0042",
            "cliente": "Maria Garcia",
            "email": "maria@email.com",
            "producto": "Monitor 4K 27 pulgadas",
            "total": 450.00,
            "estado": "entregado",
            "fecha_compra": "2026-06-15",
            "fecha_entrega": "2026-06-18",
            "tracking": "ENV-998877"
        },
        "ORD-2026-0089": {
            "order_id": "ORD-2026-0089",
            "cliente": "Carlos Lopez",
            "email": "carlos@email.com",
            "producto": "Teclado mecanico RGB",
            "total": 129.00,
            "estado": "enviado",
            "fecha_compra": "2026-07-01",
            "fecha_entrega": None,
            "tracking": "ENV-998880"
        }
    }

    if order_id and order_id in pedidos:
        return [pedidos[order_id]]
    if customer_email:
        return [p for p in pedidos.values() if p["email"] == customer_email]
    return []


def ejecutar_check_inventory(product_name):
    """Simula una consulta de inventario."""
    inventario = {
        "monitor 4k": {"disponible": 15, "almacen": "Madrid", "reposicion": None},
        "teclado mecanico": {"disponible": 0, "almacen": "Barcelona", "reposicion": "2026-07-20"},
        "cable hdmi": {"disponible": 342, "almacen": "Madrid", "reposicion": None},
    }
    return inventario.get(product_name.lower(), {"disponible": 0, "almacen": "N/A", "reposicion": "N/A"})


def ejecutar_initiate_refund(order_id, reason):
    """Simula el inicio de un reembolso."""
    return {
        "refund_id": f"REF-{order_id}-{reason[:4]}",
        "order_id": order_id,
        "reason": reason,
        "amount": 0,  # Se determinaria del pedido real
        "status": "iniciado",
        "estimated_days": "5-10 dias habiles"
    }


# Bucle de interaccion con el cliente
def atender_consulta(mensaje_usuario: str, historial: list = None) -> str:
    """
    Procesa una consulta del cliente usando el sistema multi-tool.
    Devuelve la respuesta del agente.
    """
    if historial is None:
        historial = [
            {
                "role": "system",
                "content": (
                    "Eres un agente de servicio al cliente amable y profesional. "
                    "Tu trabajo es ayudar a los clientes con sus pedidos, consultas "
                    "de inventario y solicitudes de reembolso. "
                    "Para reembolsos, SIEMPRE pide confirmacion antes de ejecutar."
                )
            }
        ]

    historial.append({"role": "user", "content": mensaje_usuario})

    response = client.chat.completions.create(
        model="gpt-4o",
        messages=historial,
        tools=tools_atencion_cliente,
        tool_choice="auto"
    )

    mensaje = response.choices[0].message

    # Si no hay tool calls, el LLM respondio directamente
    if not mensaje.tool_calls:
        historial.append({"role": "assistant", "content": mensaje.content})
        return mensaje.content

    # Procesar cada tool call
    historial.append({
        "role": "assistant",
        "content": mensaje.content,
        "tool_calls": [
            {
                "id": tc.id,
                "type": "function",
                "function": {
                    "name": tc.function.name,
                    "arguments": tc.function.arguments
                }
            } for tc in mensaje.tool_calls
        ]
    })

    for tool_call in mensaje.tool_calls:
        nombre_funcion = tool_call.function.name
        argumentos = json.loads(tool_call.function.arguments)

        # Ejecutar la funcion correspondiente
        if nombre_funcion == "lookup_order":
            resultado = ejecutar_lookup_order(
                order_id=argumentos.get("order_id"),
                customer_email=argumentos.get("customer_email")
            )
        elif nombre_funcion == "check_inventory":
            resultado = ejecutar_check_inventory(
                product_name=argumentos.get("product_name")
            )
        elif nombre_funcion == "initiate_refund":
            resultado = ejecutar_initiate_refund(
                order_id=argumentos.get("order_id"),
                reason=argumentos.get("reason")
            )
        else:
            resultado = {"error": f"Funcion desconocida: {nombre_funcion}"}

        historial.append({
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": json.dumps(resultado)
        })

    # Segunda llamada al LLM con los resultados de las tools
    final_response = client.chat.completions.create(
        model="gpt-4o",
        messages=historial
    )

    historial.append({
        "role": "assistant",
        "content": final_response.choices[0].message.content
    })

    return final_response.choices[0].message.content
```

---

## 5. PARALLEL FUNCTION CALLING

### 5.1 Que es el Parallel Function Calling

Cuando el LLM necesita ejecutar multiples funciones que NO dependen entre si,
OpenAI (y otros proveedores) permite llamarlas en PARALELO en una sola respuesta.

Esto reduce la latencia drasticamente y es una optimizacion clave en produccion.

### 5.2 Diagrama: secuencial vs paralelo

```
SECUENCIAL (lento):
====================

Usuario: "Clima en Madrid, Barcelona y Valencia?"

[LLM call 1] -> get_weather("Madrid") -> Espera respuesta
[LLM call 2] -> get_weather("Barcelona") -> Espera respuesta
[LLM call 3] -> get_weather("Valencia") -> Espera respuesta

Tiempo total: 3 x (tiempo de API + tiempo de LLM) = 6-9 segundos


PARALELO (rapido):
===================

Usuario: "Clima en Madrid, Barcelona y Valencia?"

[LLM call 1] -> get_weather("Madrid")    --+
                  get_weather("Barcelona") --+----> Se ejecutan SIMULTANEAMENTE
                  get_weather("Valencia")  --+

Tiempo total: 1 x (tiempo de API + tiempo de LLM) = 2-3 segundos
```

### 5.3 Cuando usar paralelo vs secuencial

```
+------------------------------------------------------------------+
| PREGUNTATE: La funcion B depende del resultado de la funcion A?   |
|                                                                   |
| SI -> Debes ejecutarlas SECUENCIALMENTE                           |
|        Ej: primero busca el pedido, luego con el total            |
|             calcula el reembolso.                                 |
|                                                                   |
| NO -> Puedes ejecutarlas en PARALELO                              |
|        Ej: busca el clima de 5 ciudades a la vez.                 |
|             Cada ciudad es independiente.                         |
+------------------------------------------------------------------+
```

### 5.4 Ejemplo practico de llamadas paralelas

```python
from openai import OpenAI
import json
import time

client = OpenAI()

tools_clima = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Obtiene el clima actual de una ciudad.",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "Nombre de la ciudad"
                    }
                },
                "required": ["city"],
                "additionalProperties": False
            }
        }
    }
]

def obtener_clima_real(ciudad):
    """Simula una llamada a API de clima con 1 segundo de latencia."""
    time.sleep(1)  # Simulamos latencia de red
    climas_falsos = {
        "madrid": {"temp": 32, "cond": "soleado"},
        "barcelona": {"temp": 28, "cond": "parcialmente nublado"},
        "valencia": {"temp": 30, "cond": "soleado"},
        "sevilla": {"temp": 38, "cond": "soleado"},
        "bilbao": {"temp": 22, "cond": "lluvia ligera"},
    }
    return climas_falsos.get(ciudad.lower(), {"temp": 20, "cond": "desconocido"})


# El LLM hara multiples llamadas en PARALELO si las ciudades son independientes
messages = [
    {"role": "user", "content": "Dame el clima de Madrid, Barcelona, Valencia, Sevilla y Bilbao"}
]

inicio = time.time()

response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages,
    tools=tools_clima,
    tool_choice="auto"
)

# El LLM devuelve TODAS las tool calls en una sola respuesta
tool_calls = response.choices[0].message.tool_calls
print(f"El LLM solicito {len(tool_calls)} llamadas en paralelo")

# Las podemos ejecutar en paralelo real (con threads, asyncio, etc.)
import concurrent.futures

def ejecutar_tool_call(tc):
    args = json.loads(tc.function.arguments)
    resultado = obtener_clima_real(args["city"])
    return tc.id, tc.function.name, args, resultado

# Ejecucion paralela real
with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    futuros = [executor.submit(ejecutar_tool_call, tc) for tc in tool_calls]
    resultados = []
    for futuro in concurrent.futures.as_completed(futuros):
        resultados.append(futuro.result())

# Construir el historial para la respuesta final
messages.append({"role": "assistant", "content": None, "tool_calls": [
    {"id": tc.id, "type": "function", "function": {"name": tc.function.name, "arguments": tc.function.arguments}}
    for tc in tool_calls
]})

for tc_id, nombre, args, resultado in resultados:
    messages.append({
        "role": "tool",
        "tool_call_id": tc_id,
        "content": json.dumps(resultado)
    })

final_response = client.chat.completions.create(
    model="gpt-4o",
    messages=messages
)

fin = time.time()

print(f"\nRespuesta (obtenida en {fin - inicio:.1f} segundos):")
print(final_response.choices[0].message.content)
# Con ejecucion secuencial, esto tardaria ~5+ segundos.
# Con paralelo, tarda ~1+ segundos.
```

### 5.5 Forzar llamadas paralelas

```python
# Para forzar que el LLM haga llamadas paralelas cuando es posible,
# usa tool_choice="required" si sabes que siempre debe usar tools,
# o estructura tu prompt para sugerir multiples operaciones.

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Compara el clima de estas 3 ciudades: Madrid, Paris, Londres"}],
    tools=tools_clima,
    tool_choice="auto"  # El LLM decidira si las hace en paralelo
)

# Si devuelve 3 tool_calls, el modelo decidio hacerlas en paralelo.
# Si devuelve 1 tool_call a la vez, tendras que iterar (menos eficiente).
```

---

## 6. STRUCTURED OUTPUT + FUNCTION CALLING COMBINADOS

### 6.1 La combinacion ganadora para produccion

Los sistemas mas robustos combinan AMBAS capacidades:

```
+------------------------------------------------------------------+
|                                                                   |
|  FUNCTION CALLING: Para OBTENER datos del mundo real.             |
|  STRUCTURED OUTPUT: Para ENTREGAR resultados en formato fiable.   |
|                                                                   |
|  La combinacion: un sistema que consulta el mundo real (tools)    |
|  y entrega resultados procesables por maquinas (JSON Schema).     |
|                                                                   |
+------------------------------------------------------------------+
```

### 6.2 Ejemplo: asistente de viajes con busqueda y tabla comparativa

```python
from openai import OpenAI
import json

client = OpenAI()

# Herramienta de busqueda de vuelos
tools_vuelos = [
    {
        "type": "function",
        "function": {
            "name": "search_flights",
            "description": (
                "Busca vuelos disponibles entre dos aeropuertos para una fecha especifica. "
                "Usa esta funcion cuando el usuario quiera buscar vuelos, comparar precios, "
                "o encontrar opciones de viaje. Devuelve aerolinea, precio, duracion, escalas "
                "y horario de salida/llegada."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "origin": {
                        "type": "string",
                        "description": "Codigo IATA del aeropuerto de origen (ej: MAD, BCN, EZE)"
                    },
                    "destination": {
                        "type": "string",
                        "description": "Codigo IATA del aeropuerto de destino (ej: JFK, LHR, CDG)"
                    },
                    "date": {
                        "type": "string",
                        "description": "Fecha del vuelo en formato YYYY-MM-DD"
                    }
                },
                "required": ["origin", "destination", "date"],
                "additionalProperties": False
            }
        }
    },
    {
        "type": "function",
        "function": {
            "name": "search_hotels",
            "description": (
                "Busca hoteles disponibles en una ciudad para un rango de fechas. "
                "Devuelve nombre del hotel, precio por noche, puntuacion, estrellas y servicios."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "Ciudad de destino"},
                    "check_in": {"type": "string", "description": "Fecha de entrada YYYY-MM-DD"},
                    "check_out": {"type": "string", "description": "Fecha de salida YYYY-MM-DD"},
                    "max_price_per_night": {
                        "type": "number",
                        "description": "Presupuesto maximo por noche. Opcional."
                    }
                },
                "required": ["city", "check_in", "check_out"],
                "additionalProperties": False
            }
        }
    }
]


def buscar_vuelos(origin, destination, date):
    """Simula una API de busqueda de vuelos."""
    return [
        {"aerolinea": "Iberia", "precio": 450.00, "duracion": "1h 25m",
         "escalas": 0, "salida": "08:00", "llegada": "09:25"},
        {"aerolinea": "Vueling", "precio": 120.00, "duracion": "1h 35m",
         "escalas": 0, "salida": "14:30", "llegada": "16:05"},
        {"aerolinea": "Air Europa", "precio": 210.00, "duracion": "3h 10m",
         "escalas": 1, "salida": "06:15", "llegada": "09:25"},
    ]


def buscar_hoteles(city, check_in, check_out, max_price_per_night=None):
    """Simula una API de busqueda de hoteles."""
    hoteles = [
        {"nombre": "Hotel Gran Via", "precio_por_noche": 180, "puntuacion": 4.5,
         "estrellas": 4, "servicios": ["WiFi", "desayuno", "gimnasio"]},
        {"nombre": "Hostel Central", "precio_por_noche": 45, "puntuacion": 3.8,
         "estrellas": 2, "servicios": ["WiFi"]},
        {"nombre": "Luxury Palace", "precio_por_noche": 350, "puntuacion": 4.9,
         "estrellas": 5, "servicios": ["WiFi", "desayuno", "spa", "piscina", "room service"]},
    ]
    if max_price_per_night:
        return [h for h in hoteles if h["precio_por_noche"] <= max_price_per_night]
    return hoteles


# Funcion principal que combina function calling con structured output
def planificar_viaje(mensaje_usuario: str) -> dict:
    """
    Busca vuelos y hoteles usando function calling,
    y devuelve una comparativa estructurada con JSON Schema.
    """

    messages = [{"role": "user", "content": mensaje_usuario}]

    # Paso 1: El LLM decide que herramientas usar
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages,
        tools=tools_vuelos,
        tool_choice="auto"
    )

    mensaje = response.choices[0].message

    if not mensaje.tool_calls:
        return {"error": "El modelo no necesito usar herramientas."}

    messages.append({
        "role": "assistant",
        "content": None,
        "tool_calls": [
            {"id": tc.id, "type": "function",
             "function": {"name": tc.function.name, "arguments": tc.function.arguments}}
            for tc in mensaje.tool_calls
        ]
    })

    # Paso 2: Ejecutar las herramientas
    for tool_call in mensaje.tool_calls:
        nombre = tool_call.function.name
        args = json.loads(tool_call.function.arguments)

        if nombre == "search_flights":
            resultado = buscar_vuelos(**args)
        elif nombre == "search_hotels":
            resultado = buscar_hoteles(**args)
        else:
            resultado = {"error": f"Funcion desconocida: {nombre}"}

        messages.append({
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": json.dumps(resultado)
        })

    # Paso 3: Pedir al LLM que devuelva el resultado en formato ESTRUCTURADO
    messages.append({
        "role": "system",
        "content": (
            "Ahora organiza toda la informacion obtenida en un formato estructurado "
            "de comparativa de viaje. Incluye un resumen, la mejor opcion calidad-precio "
            "y una recomendacion final."
        )
    })

    final_response = client.chat.completions.create(
        model="gpt-4o",
        messages=messages,
        response_format={
            "type": "json_schema",
            "json_schema": {
                "name": "comparativa_viaje",
                "strict": True,
                "schema": {
                    "type": "object",
                    "properties": {
                        "consulta_original": {"type": "string"},
                        "vuelos_disponibles": {
                            "type": "array",
                            "items": {
                                "type": "object",
                                "properties": {
                                    "aerolinea": {"type": "string"},
                                    "precio": {"type": "number"},
                                    "duracion": {"type": "string"},
                                    "escalas": {"type": "integer"},
                                    "salida": {"type": "string"},
                                    "llegada": {"type": "string"},
                                    "puntuacion_precio": {
                                        "type": "string",
                                        "enum": ["economico", "moderado", "caro"]
                                    }
                                },
                                "required": ["aerolinea", "precio", "duracion", "escalas", "puntuacion_precio"],
                                "additionalProperties": False
                            }
                        },
                        "hoteles_disponibles": {
                            "type": "array",
                            "items": {
                                "type": "object",
                                "properties": {
                                    "nombre": {"type": "string"},
                                    "precio_por_noche": {"type": "number"},
                                    "puntuacion": {"type": "number"},
                                    "estrellas": {"type": "integer"},
                                    "servicios": {"type": "array", "items": {"type": "string"}}
                                },
                                "required": ["nombre", "precio_por_noche", "puntuacion", "estrellas"],
                                "additionalProperties": False
                            }
                        },
                        "mejor_opcion": {
                            "type": "object",
                            "properties": {
                                "vuelo_recomendado": {"type": "string"},
                                "hotel_recomendado": {"type": "string"},
                                "costo_total_estimado": {"type": "number"},
                                "justificacion": {"type": "string"}
                            },
                            "required": ["vuelo_recomendado", "hotel_recomendado", "costo_total_estimado", "justificacion"],
                            "additionalProperties": False
                        }
                    },
                    "required": ["consulta_original", "vuelos_disponibles", "hoteles_disponibles", "mejor_opcion"],
                    "additionalProperties": False
                }
            }
        }
    )

    return json.loads(final_response.choices[0].message.content)


# Ejemplo de uso
resultado = planificar_viaje(
    "Quiero viajar de Madrid a Barcelona el 20 de julio. Buscame vuelos y hoteles, "
    "mi presupuesto para hotel es maximo 200 por noche."
)

print(json.dumps(resultado, indent=2, ensure_ascii=False))
```

### 6.3 El flujo completo combinado

```
                          +---------------------------+
                          | Usuario: "Vuelos y hotel  |
                          | de Madrid a Barcelona"    |
                          +---------------------------+
                                    |
                                    v
                          +---------------------------+
                          | LLM decide herramientas:  |
                          | search_flights(MAD->BCN)  |
                          | search_hotels(Barcelona)  |
                          +---------------------------+
                           /                        \
                          v                          v
              +--------------------+     +--------------------+
              | API de vuelos      |     | API de hoteles     |
              | (datos reales)     |     | (datos reales)     |
              +--------------------+     +--------------------+
                           \                        /
                            v                      v
                          +---------------------------+
                          | LLM recibe datos reales   |
                          | y los organiza segun      |
                          | JSON Schema predefinido   |
                          +---------------------------+
                                    |
                                    v
                          +---------------------------+
                          | Output estructurado:      |
                          | {vuelos: [...],           |
                          |  hoteles: [...],          |
                          |  mejor_opcion: {...}}     |
                          +---------------------------+
```

---

## 7. BEST PRACTICES Y ANTI-PATRONES

### 7.1 DOs (Buenas practicas)

```
+------------------------------------------------------------------+
| HAZ ESTO                                                         |
+==================================================================+
|                                                                  |
| [DO] Escribe descripciones de tools como si fueran prompts.      |
|      La descripcion debe incluir:                                |
|      - QUE hace la funcion                                       |
|      - CUANDO usarla (y cuando NO)                               |
|      - QUE devuelve (formato de la respuesta)                    |
|      - PRECAUCIONES (efectos secundarios, confirmaciones...)      |
|                                                                  |
| [DO] Valida los outputs de las tools antes de darselos al LLM.   |
|      Si la API fallo, no le pases un error HTTP en crudo.        |
|      Envuelvelo en un mensaje util.                              |
|                                                                  |
| [DO] Usa JSON Schema (strict=True) siempre en produccion.         |
|      El modo json_basic es mejor que texto libre, pero json_schema|
|      con strict=True es GARANTIZADO. No hay excusa para no usarlo.|
|                                                                  |
| [DO] Implementa timeouts en tus tool functions.                  |
|      Si una API externa tarda 30 segundos, el usuario se va.     |
|      Timeout de 5-10 segundos maximo para cada tool.             |
|                                                                  |
| [DO] Da al LLM solo las tools que necesita para la tarea.        |
|      Menos tools = decisiones mas precisas.                      |
|      Si el LLM tiene 30 tools, se confunde. Agrupa por contexto. |
|                                                                  |
| [DO] Usa temperature=0 o baja para decision-making.              |
|      Cuando el LLM decide que tool usar, quieres determinismo,   |
|      no creatividad.                                             |
|                                                                  |
+------------------------------------------------------------------+
```

### 7.2 DONTs (Anti-patrones)

```
+------------------------------------------------------------------+
| NO HAGAS ESTO                                                    |
+==================================================================+
|                                                                  |
| [DONT] No confies en el output de una tool sin validarlo.        |
|        MALO: pasar el resultado crudo de una API al LLM.         |
|        BUENO: validar estructura, sanitizar, y envolver en       |
|               un formato controlado antes de pasarlo.            |
|                                                                  |
| [DONT] No des tools con efectos secundarios sin confirmacion.    |
|        MALO: El LLM llama a send_email y se envia sin preguntar. |
|        BUENO: La tool pide confirmacion o devuelve un preview    |
|               para que el usuario apruebe antes del envio real.  |
|                                                                  |
| [DONT] No uses tool_choice="required" sin un buen motivo.        |
|        Si el usuario solo quiere charlar, forzar una tool call   |
|        degrada la experiencia. Usa "auto" como default.          |
|                                                                  |
| [DONT] No definas parametros de tool ambiguos o genericos.       |
|        MALO: {"query": {"type": "string"}}                       |
|        BUENO: {"product_name": {"type": "string",                |
|                  "description": "Nombre exacto del producto..."}}|
|                                                                  |
| [DONT] No hagas que el LLM "adivine" IDs o claves internas.     |
|        Si necesitas un customer_id, busca primero por email o    |
|        nombre. El LLM no puede inventar IDs de tu base de datos. |
|                                                                  |
| [DONT] No ignores los errores de tool execution.                 |
|        Si una tool falla, informa al LLM con un mensaje claro:   |
|        {"error": "API no disponible. Intenta de nuevo en 1 min"} |
|                                                                  |
+------------------------------------------------------------------+
```

### 7.3 El patron "Human in the Loop"

Para operaciones peligrosas o irreversibles (envios de email real, cobros, reembolsos,
eliminacion de datos), el patron correcto es:

```
+------------------------------------------------------------------+
| PATRON HUMAN-IN-THE-LOOP PARA OPERACIONES PELIGROSAS             |
+------------------------------------------------------------------+
|                                                                  |
|  1. El LLM detecta que necesita la tool peligrosa.               |
|  2. El LLM NO la llama directamente.                             |
|  3. El LLM explica al usuario lo que va a hacer.                 |
|  4. El LLM pide confirmacion explicita.                          |
|  5. El usuario responde "si" o "no".                             |
|  6. SOLO con "si", tu codigo ejecuta la tool.                    |
|  7. El resultado se devuelve al LLM para la respuesta final.     |
|                                                                  |
+------------------------------------------------------------------+

Ejemplo de implementacion:

```python
def ejecutar_operacion_segura(tool_call, argumentos):
    """
    Envuelve una tool peligrosa con confirmacion del usuario.
    """
    operaciones_peligrosas = ["send_email", "initiate_refund", "delete_record",
                               "process_payment", "modify_order"]

    if tool_call.function.name in operaciones_peligrosas:
        print(f"\n[ADVERTENCIA] El sistema quiere ejecutar: {tool_call.function.name}")
        print(f"[ADVERTENCIA] Parametros: {json.dumps(argumentos, indent=2)}")
        confirmacion = input("[ADVERTENCIA] Confirmas? (si/no): ")

        if confirmacion.lower() != "si":
            return {
                "ejecutado": False,
                "mensaje": "Operacion cancelada por el usuario.",
                "razon": "El usuario no confirmo la operacion."
            }

    # Si llega aqui, ejecutar normalmente
    return ejecutar_tool_real(tool_call.function.name, argumentos)
```

### 7.4 Validacion de outputs de tools

```python
def ejecutar_herramienta_segura(nombre_funcion, argumentos):
    """
    Ejecuta una herramienta con manejo de errores robusto.
    """
    try:
        if nombre_funcion == "get_weather":
            resultado = api_clima.get_current(city=argumentos["city"])

            # Validar que el resultado tiene la estructura esperada
            if "temperature" not in resultado:
                return {"error": "API de clima devolvio datos incompletos"}

            return {
                "exito": True,
                "datos": {
                    "ciudad": argumentos["city"],
                    "temperatura": resultado["temperature"],
                    "humedad": resultado.get("humidity", "N/A"),
                    "condiciones": resultado.get("conditions", "N/A")
                }
            }

    except TimeoutError:
        return {"error": "La API de clima no respondio a tiempo. Intenta de nuevo."}
    except Exception as e:
        # NUNCA devuelvas el error crudo al LLM sin contexto
        return {"error": f"No se pudo obtener el clima. Error: {str(e)[:200]}"}
```

---

## 8. EJERCICIOS PRACTICOS

### 8.1 Ejercicio Principiante: Tool de Calculadora

**Objetivo:** Crear un sistema que permita al usuario hacer calculos matematicos
usando function calling con una tool de calculadora.

**Instrucciones:**

Define una tool llamada `calculate` que acepte dos numeros y una operacion
(suma, resta, multiplicacion, division). El LLM debe decidir automaticamente
cuando usar la calculadora y con que parametros.

```python
# PLANTILLA PARA EL EJERCICIO
from openai import OpenAI
import json

client = OpenAI()

# TODO: Define la tool "calculate" aqui
tools_calculadora = [
    # TU CODIGO AQUI
]

# TODO: Implementa la funcion real de calculo
def calcular(operacion, a, b):
    # TU CODIGO AQUI
    pass

# TODO: Implementa el bucle que procesa mensajes del usuario
def chat_calculadora():
    messages = [
        {"role": "system", "content": "Eres una calculadora. Usa la herramienta calculate para operaciones matematicas."}
    ]

    print("Calculadora con IA. Escribe 'salir' para terminar.")
    while True:
        user_input = input("\nTu: ")
        if user_input.lower() == "salir":
            break

        # TU CODIGO AQUI: enviar mensaje al LLM, procesar tool calls, mostrar respuesta
        pass

# SOLUCION ESPERADA (implementala tu primero, luego compara):
```

**Solucion de referencia:**

```python
tools_calculadora = [
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": (
                "Realiza una operacion matematica entre dos numeros. "
                "Usa esta funcion cuando el usuario pida hacer calculos, sumas, restas, "
                "multiplicaciones o divisiones. Tambien para preguntas como 'cuanto es X mas Y'."
            ),
            "parameters": {
                "type": "object",
                "properties": {
                    "operation": {
                        "type": "string",
                        "enum": ["suma", "resta", "multiplicacion", "division"],
                        "description": "La operacion matematica a realizar"
                    },
                    "a": {
                        "type": "number",
                        "description": "El primer numero (operando izquierdo)"
                    },
                    "b": {
                        "type": "number",
                        "description": "El segundo numero (operando derecho)"
                    }
                },
                "required": ["operation", "a", "b"],
                "additionalProperties": False
            }
        }
    }
]

def calcular(operacion, a, b):
    if operacion == "suma":
        return {"resultado": a + b, "operacion": f"{a} + {b} = {a + b}"}
    elif operacion == "resta":
        return {"resultado": a - b, "operacion": f"{a} - {b} = {a - b}"}
    elif operacion == "multiplicacion":
        return {"resultado": a * b, "operacion": f"{a} x {b} = {a * b}"}
    elif operacion == "division":
        if b == 0:
            return {"error": "No se puede dividir por cero"}
        return {"resultado": a / b, "operacion": f"{a} / {b} = {a / b}"}
    else:
        return {"error": f"Operacion desconocida: {operacion}"}

def chat_calculadora():
    messages = [
        {"role": "system", "content": "Eres una calculadora inteligente. Usa la herramienta calculate para cualquier operacion matematica."}
    ]

    print("Calculadora con IA. Escribe 'salir' para terminar.\n")

    while True:
        user_input = input("Tu: ")
        if user_input.lower() == "salir":
            break

        messages.append({"role": "user", "content": user_input})

        response = client.chat.completions.create(
            model="gpt-4o",
            messages=messages,
            tools=tools_calculadora,
            tool_choice="auto",
            temperature=0.0
        )

        assistant_msg = response.choices[0].message

        if assistant_msg.tool_calls:
            messages.append({
                "role": "assistant",
                "content": None,
                "tool_calls": [
                    {"id": tc.id, "type": "function",
                     "function": {"name": tc.function.name, "arguments": tc.function.arguments}}
                    for tc in assistant_msg.tool_calls
                ]
            })

            for tc in assistant_msg.tool_calls:
                args = json.loads(tc.function.arguments)
                result = calcular(args["operation"], args["a"], args["b"])
                messages.append({
                    "role": "tool",
                    "tool_call_id": tc.id,
                    "content": json.dumps(result)
                })

            final_response = client.chat.completions.create(
                model="gpt-4o",
                messages=messages,
                temperature=0.0
            )
            respuesta_final = final_response.choices[0].message.content
            messages.append({"role": "assistant", "content": respuesta_final})
            print(f"IA: {respuesta_final}")
        else:
            print(f"IA: {assistant_msg.content}")
            messages.append({"role": "assistant", "content": assistant_msg.content})
```

**Prueba tu solucion con estas consultas:**
- "Cuanto es 15 + 27?"
- "Multiplica 145 por 32"
- "Si tengo 1000 euros y gasto 347, cuanto me queda?"
- "Cual es la raiz cuadrada de 144?" (El LLM deberia responder sin calculadora si ya lo sabe, o usarla si lo consideras)

---

### 8.2 Ejercicio Intermedio: Agente de Servicio al Cliente con 3 Tools

**Objetivo:** Construir un agente completo de atencion al cliente con busqueda de pedidos,
consulta de inventario e inicio de reembolsos. Implementa confirmacion para reembolsos.

**Especificaciones:**

1. **Tool 1: lookup_order(order_id, customer_email)**
   - Busca pedidos en una base de datos simulada.
   - Devuelve: order_id, cliente, producto, total, estado, tracking.

2. **Tool 2: check_inventory(product_name)**
   - Consulta disponibilidad de productos.
   - Devuelve: disponible (cantidad), reposicion (fecha o None).

3. **Tool 3: initiate_refund(order_id, reason)**
   - Inicia un reembolso.
   - DEBE pedir confirmacion al usuario antes de ejecutar.
   - Devuelve: refund_id, monto, estado.

4. **Bonus:** Usa JSON Schema para el output final cuando el usuario pida "resumen de mi cuenta"
   mostrando todos sus pedidos y su estado en formato estructurado.

**Base de datos simulada para el ejercicio:**

```python
PEDIDOS_SIMULADOS = [
    {
        "order_id": "ORD-2026-0101",
        "customer": "Ana Martinez",
        "email": "ana@email.com",
        "product": "Laptop Pro 15\"",
        "total": 1299.99,
        "status": "entregado",
        "purchase_date": "2026-06-01",
        "delivery_date": "2026-06-05",
        "tracking": "ENV-A001"
    },
    {
        "order_id": "ORD-2026-0102",
        "customer": "Ana Martinez",
        "email": "ana@email.com",
        "product": "Mouse inalambrico",
        "total": 45.00,
        "status": "enviado",
        "purchase_date": "2026-07-01",
        "delivery_date": None,
        "tracking": "ENV-A002"
    },
    {
        "order_id": "ORD-2026-0103",
        "customer": "Luis Rodriguez",
        "email": "luis@email.com",
        "product": "Monitor 27\" 4K",
        "total": 449.00,
        "status": "pendiente",
        "purchase_date": "2026-07-10",
        "delivery_date": None,
        "tracking": None
    },
]

INVENTARIO_SIMULADO = {
    "laptop": {"stock": 5, "warehouse": "Madrid", "restock_date": None},
    "mouse inalambrico": {"stock": 0, "warehouse": "Barcelona", "restock_date": "2026-07-25"},
    "monitor 4k": {"stock": 12, "warehouse": "Madrid", "restock_date": None},
    "teclado mecanico": {"stock": 3, "warehouse": "Valencia", "restock_date": None},
}
```

**Criterios de evaluacion:**
- El agente identifica correctamente que tool usar segun la consulta.
- Los reembolsos requieren confirmacion del usuario.
- Si un producto no esta en stock, el agente informa la fecha de reposicion.
- Si un pedido no existe, el agente lo comunica claramente.

---

### 8.3 Ejercicio Avanzado: Sistema Multi-Tool con Busqueda Web, Extraccion y Email

**Objetivo:** Disenar un sistema de investigacion automatizada que:

1. **Busque** informacion sobre un tema usando una tool de busqueda web simulada.
2. **Extraiga** datos estructurados de los resultados usando JSON Schema.
3. **Envie** un resumen por email (simulado) si el usuario lo solicita.

**Especificaciones:**

```
+------------------------------------------------------------------+
| FLUJO DEL SISTEMA                                                |
+------------------------------------------------------------------+
|                                                                  |
| 1. Usuario: "Investiga sobre la energia solar en 2026 y          |
|    enviame un resumen por email a juan@email.com"                |
|                                                                  |
| 2. LLM detecta que necesita: web_search("energia solar 2026")    |
|                                                                  |
| 3. Tu codigo ejecuta la busqueda web simulada y devuelve         |
|    resultados (titulo, snippet, url).                            |
|                                                                  |
| 4. LLM procesa los resultados y extrae datos clave usando        |
|    JSON Schema: {tema, datos_clave:[{dato, fuente}], tendencias, |
|    conclusion}                                                   |
|                                                                  |
| 5. LLM detecta que necesita: send_email(to="juan@email.com",     |
|    subject="Resumen: Energia Solar 2026", body=resumen)          |
|                                                                  |
| 6. Antes de enviar, el sistema pide confirmacion.                |
|                                                                  |
| 7. Con confirmacion, se "envia" el email y se confirma.          |
|                                                                  |
+------------------------------------------------------------------+
```

**Herramientas a implementar:**

```python
# Tool 1: Busqueda web (simulada)
# Parametros: query (string)
# Devuelve: array de resultados con {titulo, snippet, url}

# Tool 2: Envio de email (simulado, con confirmacion)
# Parametros: to, subject, body
# Devuelve: {enviado: bool, timestamp}

# Tool 3 (bonus): Guardar en base de conocimiento
# Parametros: topic (string), content (string)
# Devuelve: {guardado: bool, id: string}
```

**Base de datos simulada para busquedas web:**

```python
WEB_SIMULADA = {
    "energia solar": [
        {
            "titulo": "La energia solar alcanza record de eficiencia del 47% en 2026",
            "snippet": "Investigadores del MIT lograron un nuevo record mundial de eficiencia "
                       "en celulas solares de perovskita, alcanzando el 47.1% de conversion. "
                       "Esto representa un aumento del 15% respecto a 2024.",
            "url": "https://ejemplo.com/energia-solar-record-2026"
        },
        {
            "titulo": "China instala 300 GW de capacidad solar en el primer semestre de 2026",
            "snippet": "China continua liderando la expansion solar global con 300 GW instalados "
                       "entre enero y junio de 2026. La capacidad solar total del pais supera "
                       "los 1,200 GW.",
            "url": "https://ejemplo.com/china-solar-2026"
        },
        {
            "titulo": "El costo de paneles solares cae por debajo de $0.10 por vatio",
            "snippet": "Por primera vez en la historia, el costo de fabricacion de paneles "
                       "solares fotovoltaicos cayo por debajo de los 10 centavos de dolar "
                       "por vatio, acelerando la adopcion global.",
            "url": "https://ejemplo.com/costo-paneles-2026"
        }
    ],
    "inteligencia artificial": [
        {
            "titulo": "GPT-5 establece nuevos parametros en razonamiento matematico",
            "snippet": "El ultimo modelo de OpenAI resuelve el 96% de los problemas de la Olimpiada "
                       "Internacional de Matematicas, superando a la mayoria de medallistas de oro.",
            "url": "https://ejemplo.com/gpt5-matematicas-2026"
        }
    ]
}

def web_search(query):
    """Busqueda web simulada."""
    import time
    time.sleep(0.5)  # Simula latencia de red

    resultados = []
    query_lower = query.lower()
    for clave, datos in WEB_SIMULADA.items():
        if clave in query_lower:
            resultados.extend(datos)

    if not resultados:
        return [{"titulo": "Sin resultados", "snippet": f"No se encontraron resultados para '{query}'", "url": ""}]

    return resultados
```

**Criterios de evaluacion:**
- El sistema usa las 3 herramientas correctamente y en el orden adecuado.
- La extraccion de datos usa JSON Schema para garantizar estructura.
- El envio de email requiere confirmacion explicita del usuario.
- Manejo de errores: que pasa si la busqueda no encuentra resultados?
- El codigo es limpio y reusable (funciones separadas, constantes claras).

**Pistas para la implementacion:**

```python
# Estructura sugerida para el sistema:

class ResearchAgent:
    def __init__(self):
        self.client = OpenAI()
        self.tools = [...]  # Tus 3 definiciones de tools
        self.messages = [
            {"role": "system", "content": "Eres un asistente de investigacion..."}
        ]

    def handle_user_message(self, user_input: str) -> str:
        """Procesa un mensaje del usuario y devuelve la respuesta."""
        pass

    def execute_tool(self, tool_call) -> dict:
        """Ejecuta una tool especifica y devuelve el resultado."""
        pass

    def run(self):
        """Bucle principal de interaccion."""
        pass

# Punto de entrada
if __name__ == "__main__":
    agent = ResearchAgent()
    agent.run()
```

---

## 9. RESUMEN Y SIGUIENTES PASOS

### 9.1 Que aprendiste en este modulo

```
+------------------------------------------------------------------+
| RESUMEN DE CAPACIDADES ADQUIRIDAS                                |
+==================================================================+
|                                                                  |
| [1] OUTPUT ESTRUCTURADO                                          |
|     - JSON Mode basico (response_format={"type": "json_object"}) |
|     - JSON Schema con strict=True para garantia total            |
|     - Cuando usar cada uno                                       |
|                                                                  |
| [2] FUNCTION CALLING                                             |
|     - Anatomia de una tool: name, description, parameters        |
|     - El LLM decide QUE tool y CON QUE parametros                |
|     - TU codigo ejecuta la tool y devuelve el resultado           |
|     - El LLM integra el resultado en su respuesta                |
|                                                                  |
| [3] MULTI-TOOL ORCHESTRATION                                     |
|     - Dar al LLM multiples herramientas                          |
|     - Las descripciones de tools SON prompts                     |
|     - Como disenar buenas descripciones (concretas, contextuales)|
|                                                                  |
| [4] PARALLEL FUNCTION CALLING                                    |
|     - Llamadas independientes se ejecutan en paralelo            |
|     - Reduce latencia drasticamente                              |
|     - Cuando usar paralelo vs secuencial                         |
|                                                                  |
| [5] COMBINACION ESTRUCTURADO + TOOLS                             |
|     - Buscar datos con tools (function calling)                  |
|     - Entregar resultados con JSON Schema (structured output)    |
|     - El patron mas robusto para sistemas en produccion          |
|                                                                  |
| [6] BUENAS PRACTICAS                                             |
|     - Validar outputs de tools                                   |
|     - Human-in-the-loop para operaciones peligrosas              |
|     - Timeouts, manejo de errores, descripciones claras          |
|                                                                  |
+------------------------------------------------------------------+
```

### 9.2 La diferencia entre un prompt writer y un prompt engineer

```
+------------------------------------------------------------------+
| PROMPT WRITER                          PROMPT ENGINEER            |
+==================================================================+
| Escribe prompts en un chat            Disena sistemas             |
|                                        automatizados               |
|------------------------------------------------------------------|
| Se conforma con texto libre           Exige output estructurado   |
|                                        con JSON Schema             |
|------------------------------------------------------------------|
| El LLM es el producto final           El LLM es un componente     |
|                                        de un sistema mas grande    |
|------------------------------------------------------------------|
| No sabe que son las tools             Disena y orquesta           |
|                                        herramientas (tools)        |
|------------------------------------------------------------------|
| "Escribe mejor" = mejor prompt        "Integra mejor" = mejor     |
|                                        sistema                     |
+------------------------------------------------------------------+
```

La ingenieria de prompt NO es solo escribir buenos prompts. Es disenar sistemas
donde el LLM es una pieza que interactua con APIs, bases de datos, servicios de email,
y cualquier otra pieza de software. Function calling y structured outputs son las
herramientas que hacen posible esa integracion.

### 9.3 Siguientes pasos

```
+------------------------------------------------------------------+
| RUTA DE APRENDIZAJE SUGERIDA                                     |
+==================================================================+
|                                                                  |
| [AHORA]  15. Function Calling y Outputs Estructurados <-- ESTAS  |
|           AQUI                                                   |
|                                                                  |
| [LUEGO]  Explora estrategias de orquestacion:                    |
|          - Agentes autonomos (AutoGPT, CrewAI, LangGraph)        |
|          - RAG (Retrieval-Augmented Generation)                  |
|          - Planificacion multi-paso (chain-of-thought + tools)   |
|                                                                  |
| [DESPUES] Practica con frameworks:                               |
|          - LangChain para cadenas de tools                       |
|          - Semantic Kernel de Microsoft                          |
|          - El SDK nativo de OpenAI (el mas simple y directo)     |
|                                                                  |
| [AVANZADO] Evaluacion y monitoreo:                               |
|          - Como medir la calidad de las tool calls               |
|          - Metricas de precision en la seleccion de herramientas |
|          - A/B testing de descripciones de tools                 |
|                                                                  |
+------------------------------------------------------------------+
```

### 9.4 Checklist de autoevaluacion

Antes de pasar al siguiente modulo, asegurate de poder responder:

- [ ] Se cuando usar `response_format={"type": "json_object"}` y cuando usar `json_schema` con `strict=True`.
- [ ] Puedo definir una tool con name, description y parameters en JSON Schema.
- [ ] Entiendo que el LLM no ejecuta codigo: solo decide QUE llamar y tu codigo ejecuta.
- [ ] Se procesar `tool_calls` en la respuesta del LLM y devolver resultados con `role: "tool"`.
- [ ] Entiendo la diferencia entre `tool_choice="auto"`, `"required"` y `"none"`.
- [ ] Puedo disenar descripciones de tools que sean claras y especificas.
- [ ] Se cuando las llamadas pueden ser paralelas y cuando deben ser secuenciales.
- [ ] Implemento confirmacion del usuario para operaciones con efectos secundarios.
- [ ] Valido los outputs de las tools antes de pasarselos al LLM.
- [ ] Puedo combinar function calling con structured output en un mismo sistema.

---

## REFERENCIAS

- OpenAI Function Calling Documentation: https://platform.openai.com/docs/guides/function-calling
- OpenAI Structured Outputs Documentation: https://platform.openai.com/docs/guides/structured-outputs
- JSON Schema Specification: https://json-schema.org/
- OpenAI Python SDK (GitHub): https://github.com/openai/openai-python

---

```
+------------------------------------------------------------------+
|                                                                  |
|   "Un LLM sin tools es como un cerebro sin manos: puede pensar,  |
|    pero no puede actuar. Function calling le da manos al modelo." |
|                                                                  |
|   "Un LLM sin structured output es como un escritor que solo      |
|    escribe en servilletas. JSON Schema le da un formulario."      |
|                                                                  |
+------------------------------------------------------------------+
```

---

*Modulo 15 - Function Calling y Outputs Estructurados*
*Apuntes de Ingenieria de Prompt - Julio 2026*
