# Proyecto Final Integrador: Asistente de Viajes Inteligente

## INTRODUCCION

### Que es este Proyecto y Por Que Importa

Este proyecto representa la culminacion de todo lo aprendido en el repositorio de Ingenieria de Prompts. No se trata de otro ejercicio: es un proyecto integrador de nivel profesional que simula exactamente lo que harias en un entorno empresarial real. Construiras un Asistente de Viajes Inteligente desde cero, aplicando cada tecnica, patron y estrategia que has estudiado.

La industria de viajes mueve mas de 9 billones de dolares anuales a nivel global (WTTC, 2025). Empresas como Booking Holdings, Expedia Group y Airbnb estan invirtiendo masivamente en IA generativa para transformar la experiencia de planificacion de viajes. El caso de uso tiene todo lo que un ingeniero de prompts necesita dominar: busquedas complejas, integracion de multiples fuentes de datos, restricciones de presupuesto, preferencias del usuario, y generacion de itinerarios estructurados.

Al completar este proyecto habras demostrado competencia en:

- **Diseno de Prompts**: Construccion de prompts de sistema, prompts de tarea, y Few-Shot examples para un dominio complejo.
- **Evaluacion Sistematica**: Creacion de datasets de prueba, definicion de metricas, ejecucion de evaluaciones, e iteracion basada en resultados.
- **Iteracion y Refinamiento**: Mejora continua de prompts basada en evidencia cuantitativa y cualitativa.
- **Function Calling**: Diseno de herramientas, descripciones de funciones, y manejo de respuestas estructuradas.
- **Orquestacion Multi-Step**: Coordinacion de multiples llamadas al modelo en secuencia y en paralelo.
- **Manejo de Costos**: Seguimiento de tokens, optimizacion de prompts para eficiencia, y balance calidad/costo.

```
                        PROYECTO FINAL INTEGRADOR
                        Asistente de Viajes Inteligente

    +-------------------+     +-------------------+     +-------------------+
    |   FASE 1          |     |   FASE 2          |     |   FASE 3          |
    |   Diseno de       |---->|   Outputs         |---->|   Function        |
    |   Prompts         |     |   Estructurados   |     |   Calling         |
    |   (Principiante)  |     |   (Intermedio)    |     |   (Avanzado)      |
    +-------------------+     +-------------------+     +-------------------+
             |                          |                          |
             v                          v                          v
    +-------------------+     +-------------------+     +-------------------+
    |   FASE 4          |     |   FASE 5          |     |   FASE 6          |
    |   Orquestacion    |<----|   Evaluacion      |<----|   Produccion      |
    |   (Avanzado)      |     |   (Maestro)       |     |   (Maestro)       |
    +-------------------+     +-------------------+     +-------------------+
```

---

## EL PROYECTO: Asistente de Viajes Inteligente

### Vision General

El objetivo es construir un asistente conversacional que ayude a usuarios a planificar viajes completos. El usuario describe su viaje deseado en lenguaje natural, y el asistente debe:

1. Buscar vuelos disponibles (simulados)
2. Recomendar hoteles segun ubicacion, presupuesto y clima
3. Crear un itinerario dia por dia
4. Mantenerse dentro del presupuesto total
5. Opcionalmente, enviar el plan por email

El asistente debe manejar conversaciones en espanol, ser tolerante a ambiguedades, y guiar al usuario cuando falte informacion critica.

### Arquitectura Conceptual del Sistema

```
                         +--------------------------+
                         |     USUARIO              |
                         |  "Quiero viajar a..."    |
                         +------------+-------------+
                                      |
                                      v
                         +------------+-------------+
                         |     ORQUESTADOR          |
                         |  (Planificador Central)  |
                         +--+--------+-----------+--+
                            |        |           |
              +-------------+  +-----+-----+  +--+--------------+
              v                v           v     v
    +---------+--------+  +----+----+  +---+-----+  +----------+----------+
    | search_flights   |  |search_  |  |get_     |  |calculate_budget     |
    | (Vuelos)         |  |hotels   |  |weather  |  |(Presupuesto)        |
    +------------------+  |(Hoteles)|  |(Clima)  |  +----------+----------+
                          +---------+  +---------+             |
                                                               v
                                                    +----------+----------+
                                                    | send_itinerary      |
                                                    | _email (Email)      |
                                                    +---------------------+
```

---

## FASE 1 - DISENO DE PROMPTS (Nivel: Principiante)

### Objetivo

Disenar el prompt de sistema que define la personalidad, capacidades y limites del asistente de viajes. Ademas, crear prompts especializados para cada tarea: busqueda de vuelos, recomendacion de hoteles, creacion de itinerarios y optimizacion de presupuesto.

### 1.1 Prompt de Sistema (System Prompt)

El prompt de sistema es la base de todo el asistente. Define quien es, como se comporta, que puede hacer y que no debe hacer.

```
# PROMPT DE SISTEMA - ASISTENTE DE VIAJES

## IDENTIDAD
Eres ViajeGPT, un asistente de planificacion de viajes con 10 anos de experiencia
como agente de viajes profesional. Has ayudado a mas de 5,000 viajeros a planificar
viajes memorables dentro de su presupuesto.

## PERSONALIDAD
- Amable y entusiasta, pero profesional
- Honesto sobre limitaciones (no inventes vuelos ni precios que no puedas verificar)
- Proactivo: si falta informacion, pregunta educadamente
- Detallista: te importan los pequenos detalles que hacen la diferencia

## CAPACIDADES
Puedes ayudar con:
- Busqueda de vuelos (origen, destino, fechas, pasajeros)
- Recomendaciones de hoteles (ubicacion, estrellas, amenities)
- Creacion de itinerarios dia por dia
- Optimizacion de presupuesto de viaje
- Consulta de clima en destino

## LIMITACIONES
- NO reservas vuelos ni hoteles (solo recomiendas)
- NO procesas pagos de ningun tipo
- NO compartes informacion personal del usuario
- NO recomiendas destinos o actividades peligrosas o ilegales
- Los precios son estimados y pueden variar

## FLUJO DE CONVERSACION
1. Si el usuario no proporciona destino, fechas ni presupuesto, pregunta por ellos
2. Confirma la informacion recibida antes de proceder
3. Ofrece opciones (no solo una) cuando sea posible
4. Resume el plan completo al final
5. Ofrece ajustar cualquier parte del plan

## IDIOMA
Responde siempre en espanol, a menos que el usuario solicite otro idioma.
Usa un tono calido pero profesional.
```

### 1.2 Prompts de Tarea Especifica

#### Prompt para Busqueda de Vuelos

```
Eres un especialista en busqueda de vuelos.

Busca vuelos con los siguientes parametros:
- ORIGEN: {origen}
- DESTINO: {destino}
- FECHA IDA: {fecha_ida}
- FECHA VUELTA: {fecha_vuelta}
- PASAJEROS: {num_pasajeros} adultos, {num_ninos} ninos
- PREFERENCIA DE CLASE: {clase} (economica, premium economy, business)
- ESCALAS: {escalas} (directo, 1 escala, sin preferencia)
- PRESUPUESTO MAXIMO POR PASAJERO: ${presupuesto_vuelo}

INSTRUCCIONES:
1. Proporciona 3-5 opciones de vuelos
2. Ordena por la mejor relacion calidad/precio, no solo por el mas barato
3. Para cada vuelo, incluye: aerolinea, horarios, duracion, escalas, y precio estimado
4. Destaca la mejor opcion y explica por que
5. Advierte si los precios estan por encima del presupuesto o son inusualmente altos

IMPORTANTE: Si no encuentras vuelos dentro del presupuesto, sugiere fechas alternativas
o aeropuertos cercanos que podrian ser mas economicos.
```

#### Prompt para Recomendacion de Hoteles

```
Eres un especialista en alojamiento y hoteles.

Recomienda hoteles con los siguientes criterios:
- DESTINO: {destino} (ciudad o zona especifica)
- FECHAS: del {fecha_entrada} al {fecha_salida} ({num_noches} noches)
- HUESPEDES: {num_huespedes}
- PRESUPUESTO POR NOCHE: ${presupuesto_noche}
- CATEGORIA PREFERIDA: {categoria} (economico, 3 estrellas, 4 estrellas, 5 estrellas, lujo)
- AMENITIES DESEADAS: {amenities} (wifi, desayuno, piscina, gimnasio, spa, etc.)
- UBICACION PREFERIDA: {ubicacion} (centro, playa, cerca de transporte, zona tranquila)

INSTRUCCIONES:
1. Proporciona 3-5 opciones de hoteles
2. Para cada hotel, incluye: nombre, estrellas, ubicacion, precio por noche,
   amenities destacadas, puntuacion (1-10), y distancia a puntos de interes
3. Considera la relacion ubicacion/precio: un hotel mas caro pero mejor ubicado
   puede ahorrar dinero en transporte
4. Si el clima en destino es relevante, menciona amenities utiles
   (ej. aire acondicionado en verano, calefaccion en invierno)
5. Advierte sobre zonas inseguras o hoteles con malas resenas

IMPORTANTE: Si el presupuesto es muy ajustado, sugiere alternativas como hostales,
apartamentos turisticos, o zonas un poco mas alejadas pero bien conectadas.
```

#### Prompt para Creacion de Itinerario

```
Eres un planificador de itinerarios turisticos con conocimiento profundo
de destinos mundiales.

Crea un itinerario detallado con los siguientes parametros:
- DESTINO: {destino}
- FECHAS: del {fecha_inicio} al {fecha_fin} ({num_dias} dias)
- VIAJEROS: {perfil_viajeros} (ej. pareja joven, familia con ninos, adulto mayor)
- INTERESES: {intereses} (cultura, gastronomia, naturaleza, compras, aventura, relax)
- RITMO PREFERIDO: {ritmo} (intenso - ver todo, moderado, relajado)
- PRESUPUESTO DIARIO: ${presupuesto_diario} (excluyendo vuelos y hotel)
- RESTRICCIONES: {restricciones} (movilidad reducida, dieta especial, etc.)

INSTRUCCIONES:
1. Crea un itinerario dia por dia
2. Cada dia debe incluir:
   - Manana: actividad principal + opcion alternativa
   - Almuerzo: sugerencia de restaurante o zona para comer (con rango de precios)
   - Tarde: actividad principal + opcion alternativa
   - Noche: cena sugerida + actividad nocturna opcional
3. Incluye tiempo de transporte entre actividades
4. Estima costos diarios (entradas, comidas, transporte local)
5. Sugiere un "wildcard": una actividad no turistica que los locales recomendarian
6. Anade tips practicos: mejor hora para visitar, si se necesita reserva previa,
   que llevar, como vestirse

IMPORTANTE: El itinerario debe ser realista. No programes mas actividades de las
que fisicamente se pueden hacer en un dia. Incluye tiempos de descanso.
```

#### Prompt para Optimizacion de Presupuesto

```
Eres un asesor financiero especializado en optimizacion de presupuestos de viaje.

Analiza y optimiza el siguiente presupuesto de viaje:
- DESTINO: {destino}
- DURACION: {dias} dias, {noches} noches
- PASAJEROS: {num_pasajeros}
- PRESUPUESTO TOTAL: ${presupuesto_total}
- GASTOS YA IDENTIFICADOS:
  * Vuelos: ${costo_vuelos}
  * Alojamiento: ${costo_alojamiento}
  * Restante para otros gastos: ${presupuesto_restante}

COSTOS DIARIOS ESTIMADOS EN {destino}:
- Comida: ${costo_comida_diario} por persona/dia
- Transporte local: ${costo_transporte_diario}
- Actividades/Entradas: ${costo_actividades_diario}
- Extras/Imprevistos: ${costo_extras_diario}

INSTRUCCIONES:
1. Calcula si el presupuesto total es suficiente
2. Identifica las categorias donde se puede ahorrar (especifica cuanto)
3. Sugiere un plan de gastos diarios realista
4. Recomienda un fondo de emergencia (10-15% del total)
5. Proporciona 5 tips concretos para ahorrar en {destino}
6. Si el presupuesto es insuficiente, sugiere ajustes en vuelos, hotel,
   duracion del viaje, o destino alternativo mas economico
7. Incluye un "semaforo de presupuesto":
   VERDE: Gastos esenciales que no se pueden recortar
   AMARILLO: Gastos que se pueden ajustar
   ROJO: Gastos que se pueden eliminar si es necesario
```

### 1.3 Diseno de Few-Shot Examples

Los Few-Shot examples ayudan al modelo a entender el formato y nivel de detalle esperado.

#### Few-Shot: Busqueda de Vuelos

```
USUARIO: Busco vuelos de CDMX a Madrid, salida 15 de julio 2026, regreso 30 de
julio 2026, 2 adultos, clase economica, presupuesto maximo $12,000 MXN por persona.

ASISTENTE: He encontrado estas opciones de vuelos CDMX (MEX) a Madrid (MAD):

**OPCION 1 - Recomendada: Mejor relacion calidad/precio**
- Aerolinea: Aeromexico
- Ruta: MEX -> MAD (directo)
- Salida: 15 julio 2026, 18:30 hrs
- Llegada: 16 julio 2026, 12:45 hrs (hora local)
- Duracion: 10h 15min
- Regreso: 30 julio 2026, 14:00 hrs
- Precio: $11,200 MXN por persona
- Incluye: 1 maleta documentada, 1 carry-on, comida a bordo

**OPCION 2: Mas economica**
- Aerolinea: Iberia
- Ruta: MEX -> MIA -> MAD (1 escala en Miami, 2h 30min)
- Salida: 15 julio 2026, 07:00 hrs
- Duracion total: 14h 45min
- Precio: $9,800 MXN por persona
- Incluye: 1 maleta documentada

**OPCION 3: Premium (por encima del presupuesto)**
- Aerolinea: Emirates
- Ruta: MEX -> BCN -> MAD (1 escala en Barcelona, 1h 45min)
- Precio: $14,500 MXN por persona
- Incluye: 2 maletas, asiento con mas espacio, menu gourmet

RECOMENDACION: La Opcion 1 (Aeromexico directo) ofrece el mejor balance. Son solo
$800 MXN mas que la opcion con escala, pero ahorras 4.5 horas y evitas la fatiga
de una conexion. Si puedes ajustar las fechas +/- 2 dias, los precios podrian
bajar hasta un 15%.
```

#### Few-Shot: Recomendacion de Hoteles

```
USUARIO: Recomiendame hoteles en el centro de Barcelona, 5 noches del 16 al 21
de julio 2026, 2 personas, presupuesto maximo 150 EUR por noche. Me interesa que
tenga wifi, aire acondicionado, y este cerca del metro.

ASISTENTE: He encontrado estas opciones de alojamiento en Barcelona:

**OPCION 1 - Recomendada: Mejor ubicacion para turismo**
- Hotel: H10 Catalunya Plaza
- Categoria: 4 estrellas
- Ubicacion: Plaza Catalunya (a 50m del metro, 10min caminando a Las Ramblas)
- Precio: 145 EUR/noche (725 EUR total)
- Amenities: WiFi gratuito, aire acondicionado, terraza con vista, desayuno buffet
- Puntuacion: 9.1/10
- Ideal para: Parejas que quieren estar en el corazon de la ciudad

**OPCION 2: Excelente relacion calidad/precio**
- Hotel: Hotel Praktik Rambla
- Categoria: 3 estrellas
- Ubicacion: Rambla Catalunya 27 (a 100m del metro Passeig de Gracia)
- Precio: 98 EUR/noche (490 EUR total)
- Amenities: WiFi, aire acondicionado, terraza comunitaria, cafe gratuito 24h
- Puntuacion: 8.7/10
- Nota: Habitaciones compactas pero funcionales. Desayuno no incluido (cafeterias
  cercanas desde 5 EUR)

**OPCION 3: Apartamento turistico**
- Apartamento: Aspasios Rambla Catalunya Suites
- Ubicacion: A 5min del metro Diagonal
- Precio: 130 EUR/noche (650 EUR total)
- Amenities: Cocina equipada, lavadora, WiFi, aire acondicionado
- Ideal para: Estancias largas o viajeros que prefieren cocinar

RECOMENDACION: La Opcion 1 (H10) es excepcional si valoras ubicacion y desayuno
incluido. La Opcion 2 (Praktik) te ahorra 235 EUR que puedes usar en experiencias.
La Opcion 3 es interesante si planeas desayunar/cenar en el apartamento.

CONSEJO LOCAL: Julio es temporada alta en Barcelona. Reserva con al menos 2-3
semanas de anticipacion. Las habitaciones con vista a calle pueden ser ruidosas
en fin de semana; si eres de sueno ligero, pide habitacion interior.
```

#### Few-Shot: Itinerario Dia a Dia

```
USUARIO: Necesito un itinerario para 3 dias en Roma, viajamos mi pareja y yo
(30 anos, primera vez en Italia), nos interesa historia, comida y fotografia.
Ritmo moderado. Presupuesto diario: 100 EUR por persona (sin hotel).

ASISTENTE: Aqui tienes un itinerario de 3 dias en Roma disenado para ustedes:

==============================
DIA 1: CORAZON DE LA ROMA ANTIGUA
==============================

MANANA (8:30 - 13:00):
- 8:30: Desayuno en Sant'Eustachio Il Caffe (cerca del Panteon).
  Cafe + cornetto: 5 EUR/persona. Este cafe es una institucion romana desde 1938.
- 9:15: Coliseo Romano. RESERVA PREVIA OBLIGATORIA.
  Entrada: 18 EUR/persona (incluye Foro Romano y Palatino).
  Duracion: 2.5 horas. Llega antes de las 9:00 para evitar filas.
  TIP FOTO: Las mejores fotos del Coliseo se toman desde Via Nicola Salvi,
  no desde la entrada principal.

ALMUERZO (13:00 - 14:15):
- Trattoria Luzzi (Via di San Giovanni in Laterano 88).
  Autentica comida romana. Pide la carbonara (9 EUR) o la amatriciana (8 EUR).
  Muy popular entre locales, no solo turistas.
  Costo estimado: 15-18 EUR/persona.

TARDE (14:30 - 18:30):
- 14:30: Foro Romano y Monte Palatino (incluido en tu entrada del Coliseo).
  Duracion: 2-2.5 horas. Camina por la Via Sacra, imagina como era hace 2000 anos.
- 17:00: Bocca della Verita (libre, donacion opcional).
  TIP FOTO: La fila avanza rapido. Perfecto para foto clasica.

NOCHE (19:30 - 21:30):
- Cena en Da Enzo al 29 (Trastevere). RESERVA PREVIA o llega a las 19:00.
  Especialidad: cacio e pepe, burrata, tiramisu.
  Costo estimado: 25-30 EUR/persona con vino.
- 21:30: Paseo nocturno por Trastevere. Calles empedradas, musica callejera,
  gelato en Fior di Luna (3 EUR).

COSTO ESTIMADO DIA 1: 98 EUR/persona

==============================
DIA 2: VATICANO Y RENACIMIENTO
==============================

[...el patron continua para los 3 dias...]

RESUMEN TOTAL: 284 EUR/persona por 3 dias (dentro del presupuesto de 300 EUR).
```

### 1.4 Documentacion de Decisiones de Diseno

Como ingeniero de prompts profesional, debes documentar POR QUE tomaste cada decision. Crea un documento con este formato:

```
DECISION DE DISENO #1: Personalidad del Asistente
Eleccion: Personalidad amable y entusiasta pero profesional.
Justificacion: Segun estudios de UX en chatbots de viajes (Smith et al., 2024),
los usuarios valoran asistencia calida pero honesta. Una personalidad demasiado
formal reduce el engagement; una demasiado casual reduce la confianza en
recomendaciones financieras.

DECISION DE DISENO #2: Estructura de Few-Shot Examples
Eleccion: 2-3 ejemplos detallados por categoria.
Justificacion: Para tareas de planificacion de viajes, se necesitan ejemplos con
suficiente profundidad para establecer el nivel de detalle esperado. Ejemplos muy
cortos producen respuestas genericas. Experimentos internos mostraron que 3
ejemplos es el punto optimo antes de que el prompt se vuelva demasiado largo
(>2000 tokens en Few-Shot) y el costo supere el beneficio.

DECISION DE DISENO #3: Prompt de Presupuesto con Semaforo
Eleccion: Categorizar gastos en VERDE/AMARILLO/ROJO.
Justificacion: La analogia visual del semaforo es universalmente entendida y
ayuda al modelo a priorizar recomendaciones. Las metaforas visuales simples
mejoran la calidad de la estructura de salida en un 23% segun nuestras pruebas.

[...continua para cada decision relevante...]
```

---

## FASE 2 - OUTPUTS ESTRUCTURADOS (Nivel: Intermedio)

### Objetivo

Definir esquemas JSON rigurosos para cada tipo de dato que el asistente debe generar, escribir prompts que produzcan datos estructurados validos, y crear un sistema de validacion que garantice la integridad de las salidas.

### 2.1 Esquemas JSON

#### Esquema: FlightOption

```json
{
  "type": "object",
  "required": ["id", "airline", "origin", "destination", "departure", "arrival",
               "duration_minutes", "stops", "price", "currency"],
  "properties": {
    "id": {
      "type": "string",
      "description": "Identificador unico del vuelo (ej. 'FL-001')"
    },
    "airline": {
      "type": "string",
      "description": "Nombre de la aerolinea"
    },
    "flight_numbers": {
      "type": "array",
      "items": {"type": "string"},
      "description": "Numeros de vuelo para cada tramo"
    },
    "origin": {
      "type": "string",
      "description": "Codigo IATA del aeropuerto de origen (ej. 'MEX')"
    },
    "destination": {
      "type": "string",
      "description": "Codigo IATA del aeropuerto de destino (ej. 'MAD')"
    },
    "departure": {
      "type": "string",
      "format": "date-time",
      "description": "Fecha y hora de salida en ISO 8601"
    },
    "arrival": {
      "type": "string",
      "format": "date-time",
      "description": "Fecha y hora de llegada en ISO 8601"
    },
    "duration_minutes": {
      "type": "integer",
      "minimum": 0,
      "description": "Duracion total en minutos"
    },
    "stops": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "airport": {"type": "string"},
          "duration_minutes": {"type": "integer"}
        }
      },
      "description": "Escalas intermedias (array vacio si es vuelo directo)"
    },
    "cabin_class": {
      "type": "string",
      "enum": ["economy", "premium_economy", "business", "first"],
      "description": "Clase de cabina"
    },
    "price": {
      "type": "number",
      "minimum": 0,
      "description": "Precio por pasajero"
    },
    "currency": {
      "type": "string",
      "description": "Codigo ISO 4217 de moneda (ej. 'MXN', 'USD', 'EUR')"
    },
    "baggage_included": {
      "type": "object",
      "properties": {
        "carry_on": {"type": "boolean"},
        "checked": {"type": "integer"}
      }
    },
    "refundable": {
      "type": "boolean",
      "description": "Si el boleto es reembolsable"
    }
  }
}
```

#### Esquema: HotelOption

```json
{
  "type": "object",
  "required": ["id", "name", "location", "price_per_night", "currency", "rating"],
  "properties": {
    "id": {
      "type": "string",
      "description": "Identificador unico del hotel (ej. 'HT-001')"
    },
    "name": {
      "type": "string",
      "description": "Nombre del hotel o propiedad"
    },
    "category": {
      "type": "string",
      "enum": ["hostel", "1_star", "2_star", "3_star", "4_star", "5_star",
               "apartment", "boutique", "resort"]
    },
    "location": {
      "type": "object",
      "required": ["city", "country", "address"],
      "properties": {
        "city": {"type": "string"},
        "country": {"type": "string"},
        "address": {"type": "string"},
        "neighborhood": {"type": "string"},
        "coordinates": {
          "type": "object",
          "properties": {
            "latitude": {"type": "number"},
            "longitude": {"type": "number"}
          }
        },
        "distance_to_center_km": {"type": "number"},
        "nearest_metro": {
          "type": "object",
          "properties": {
            "name": {"type": "string"},
            "distance_meters": {"type": "integer"}
          }
        }
      }
    },
    "price_per_night": {
      "type": "number",
      "minimum": 0
    },
    "currency": {
      "type": "string"
    },
    "total_price": {
      "type": "number",
      "description": "Precio total para la estancia completa"
    },
    "rating": {
      "type": "number",
      "minimum": 0,
      "maximum": 10
    },
    "review_count": {
      "type": "integer",
      "minimum": 0
    },
    "amenities": {
      "type": "array",
      "items": {"type": "string"},
      "description": "Lista de amenities (ej. ['wifi', 'air_conditioning', 'pool'])"
    },
    "cancellation_policy": {
      "type": "string",
      "enum": ["free_cancellation", "partial_refund", "non_refundable"]
    },
    "check_in_time": {"type": "string"},
    "check_out_time": {"type": "string"}
  }
}
```

#### Esquema: DailyItinerary

```json
{
  "type": "object",
  "required": ["day_number", "date", "activities", "estimated_cost"],
  "properties": {
    "day_number": {
      "type": "integer",
      "minimum": 1,
      "description": "Numero de dia del viaje (1, 2, 3...)"
    },
    "date": {
      "type": "string",
      "format": "date",
      "description": "Fecha en formato YYYY-MM-DD"
    },
    "theme": {
      "type": "string",
      "description": "Tema del dia (ej. 'Historia romana', 'Relax en playa')"
    },
    "weather_forecast": {
      "type": "object",
      "properties": {
        "condition": {"type": "string"},
        "temp_min_c": {"type": "number"},
        "temp_max_c": {"type": "number"},
        "rain_probability": {"type": "number"}
      }
    },
    "time_blocks": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["time", "activity", "duration_minutes"],
        "properties": {
          "time": {"type": "string", "description": "Hora de inicio (HH:MM)"},
          "activity": {"type": "string"},
          "description": {"type": "string"},
          "duration_minutes": {"type": "integer"},
          "cost": {"type": "number", "description": "Costo por persona"},
          "category": {"type": "string", "enum": [
            "transport", "sightseeing", "food", "culture", "shopping",
            "nature", "nightlife", "rest", "other"
          ]},
          "requires_reservation": {"type": "boolean"},
          "location": {
            "type": "object",
            "properties": {
              "name": {"type": "string"},
              "address": {"type": "string"}
            }
          },
          "tips": {"type": "array", "items": {"type": "string"}}
        }
      }
    },
    "estimated_cost": {
      "type": "object",
      "properties": {
        "food": {"type": "number"},
        "transport": {"type": "number"},
        "activities": {"type": "number"},
        "other": {"type": "number"},
        "total": {"type": "number"}
      }
    },
    "free_time_minutes": {
      "type": "integer",
      "description": "Tiempo libre estimado en el dia (para descanso/improvisacion)"
    }
  }
}
```

#### Esquema: TravelBudget

```json
{
  "type": "object",
  "required": ["total_budget", "currency", "breakdown", "status"],
  "properties": {
    "total_budget": {
      "type": "number",
      "description": "Presupuesto total del viaje"
    },
    "currency": {
      "type": "string",
      "description": "Moneda del presupuesto"
    },
    "breakdown": {
      "type": "object",
      "properties": {
        "flights": {"type": "number"},
        "accommodation": {"type": "number"},
        "food": {"type": "number"},
        "transport_local": {"type": "number"},
        "activities": {"type": "number"},
        "insurance": {"type": "number"},
        "emergency_fund": {"type": "number"},
        "other": {"type": "number"}
      }
    },
    "total_spent": {
      "type": "number",
      "description": "Suma de todos los gastos planificados"
    },
    "remaining": {
      "type": "number",
      "description": "Presupuesto total menos total gastado"
    },
    "status": {
      "type": "string",
      "enum": ["under_budget", "on_budget", "over_budget"],
      "description": "Estado del presupuesto"
    },
    "percentage_used": {
      "type": "number",
      "minimum": 0,
      "maximum": 100
    },
    "savings_suggestions": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "category": {"type": "string"},
          "suggestion": {"type": "string"},
          "potential_savings": {"type": "number"}
        }
      }
    },
    "traffic_light": {
      "type": "object",
      "properties": {
        "green": {
          "type": "array",
          "items": {"type": "string"},
          "description": "Gastos esenciales e inamovibles"
        },
        "yellow": {
          "type": "array",
          "items": {"type": "string"},
          "description": "Gastos ajustables"
        },
        "red": {
          "type": "array",
          "items": {"type": "string"},
          "description": "Gastos prescindibles"
        }
      }
    }
  }
}
```

### 2.2 Prompts para Salida Estructurada

Ejemplo de un prompt que solicita salida JSON validable:

```
Eres un buscador de vuelos. Tu unica responsabilidad es devolver opciones de vuelo
en formato JSON estrictamente valido.

PARAMETROS DE BUSQUEDA:
- Origen: {origen}
- Destino: {destino}
- Fecha ida: {fecha_ida}
- Fecha vuelta: {fecha_vuelta}
- Pasajeros: {pasajeros}
- Presupuesto por pasajero: ${presupuesto}
- Moneda: {moneda}

INSTRUCCIONES PARA EL OUTPUT:
1. Devuelve UNICAMENTE un objeto JSON valido, sin texto adicional antes o despues
2. El JSON debe tener esta estructura exacta:

{
  "search_params": {
    "origin": "string",
    "destination": "string",
    "departure_date": "YYYY-MM-DD",
    "return_date": "YYYY-MM-DD"
  },
  "results": [
    {
      "id": "FL-001",
      "airline": "string",
      "origin": "string (IATA)",
      "destination": "string (IATA)",
      "departure": "ISO 8601 datetime",
      "arrival": "ISO 8601 datetime",
      "duration_minutes": "integer",
      "stops": "integer",
      "stop_details": "array | null",
      "price": "number (por pasajero)",
      "currency": "string (ISO 4217)",
      "cabin_class": "economy | premium_economy | business | first",
      "baggage": {
        "carry_on": "boolean",
        "checked_bags": "integer"
      },
      "refundable": "boolean",
      "recommendation_score": "integer 1-10"
    }
  ],
  "meta": {
    "total_results": "integer",
    "cheapest_option_id": "string",
    "best_value_option_id": "string",
    "search_timestamp": "ISO 8601 datetime"
  }
}

3. Todos los campos son obligatorios; si no tienes un valor, usa null (no omitas el campo)
4. Los precios deben ser realistas para la ruta y fecha indicadas
5. Incluye al menos 3 resultados y maximo 7
6. Ordena los resultados por precio (menor a mayor)

NO incluyas markdown, explicaciones ni texto fuera del JSON.
Tu respuesta debe empezar con { y terminar con }.
```

### 2.3 Prueba de Validacion

```
EJERCICIO: Ejecuta el siguiente prompt 5 veces con el modelo de tu eleccion.
Para cada respuesta, pasa el resultado por el FlightOptionValidator.
Documenta cuantas respuestas pasaron validacion en el primer intento.

PROMPT DE PRUEBA:
"Busca vuelos de Buenos Aires (EZE) a Nueva York (JFK), salida 10 de agosto 2026,
regreso 20 de agosto 2026, 1 adulto, clase economica, presupuesto $800 USD.
Devuelve exclusivamente JSON valido con el formato FlightOption."

METRICAS A DOCUMENTAR:
- Tasa de JSON valido (sintacticamente correcto): X/5
- Tasa de JSON que pasa validacion completa: X/5
- Errores mas comunes encontrados
- Diferencias entre modelos (si pruebas mas de uno)
```

---

## FASE 3 - FUNCTION CALLING (Nivel: Avanzado)

### Objetivo

Disenar 5 herramientas (tools) que el asistente pueda invocar, escribir descripciones de funciones que sigan las mejores practicas de function calling, implementar versiones mock de cada herramienta, y manejar errores con gracia.

### 3.1 Diseno de Herramientas

```
                        ARQUITECTURA DE FUNCTION CALLING

    +-----------+         +---------------+         +-------------------+
    |  Usuario  |-------->|  LLM decide   |-------->|  Invocar funcion  |
    |  dice:    |         |  que funcion  |         |  con argumentos   |
    |  "Busca   |         |  usar         |         |  estructurados    |
    |   vuelos" |         +-------+-------+         +--------+----------+
    +-----------+                 |                          |
                                  | si necesita              v
                                  | mas datos       +--------+----------+
                           +------v------+         |  Mock Tool        |
                           |  Responder   |         |  (simula API      |
                           |  pidiendo    |         |   externa)        |
                           |  aclaracion  |         +--------+----------+
                           +--------------+                  |
                                                             v
                                                    +--------+----------+
                                                    |  LLM recibe       |
                                                    |  resultado de     |
                                                    |  la funcion y      |
                                                    |  genera respuesta  |
                                                    |  final al usuario  |
                                                    +-------------------+
```

### 3.2 Definicion de las 5 Herramientas

#### Herramienta 1: search_flights

```json
{
  "name": "search_flights",
  "description": "Busca vuelos disponibles entre dos aeropuertos en fechas especificas. Usa esta funcion cuando el usuario pregunte por vuelos, quiera saber precios de boletos, o necesite opciones de transporte aereo. No uses esta funcion si el usuario solo menciona un destino sin especificar que quiere volar.",
  "parameters": {
    "type": "object",
    "required": ["origin", "destination", "departure_date", "passengers"],
    "properties": {
      "origin": {
        "type": "string",
        "description": "Codigo IATA del aeropuerto de origen (3 letras, ej: 'MEX', 'EZE', 'MAD'). Si el usuario menciona una ciudad sin codigo, usa la funcion 'resolve_airport_code' primero o pregunta."
      },
      "destination": {
        "type": "string",
        "description": "Codigo IATA del aeropuerto de destino (3 letras)"
      },
      "departure_date": {
        "type": "string",
        "description": "Fecha de salida en formato YYYY-MM-DD. Por defecto, busca solo ida. Si es ida y vuelta, usa return_date."
      },
      "return_date": {
        "type": "string",
        "description": "Fecha de regreso en formato YYYY-MM-DD. Solo incluir si el usuario quiere vuelo redondo."
      },
      "passengers": {
        "type": "object",
        "properties": {
          "adults": {
            "type": "integer",
            "minimum": 1,
            "description": "Numero de pasajeros adultos (12+ anos). Minimo 1."
          },
          "children": {
            "type": "integer",
            "minimum": 0,
            "description": "Numero de ninos (2-11 anos)"
          },
          "infants": {
            "type": "integer",
            "minimum": 0,
            "description": "Numero de infantes (0-2 anos, en regazo)"
          }
        }
      },
      "cabin_class": {
        "type": "string",
        "enum": ["economy", "premium_economy", "business", "first"],
        "description": "Clase de cabina. Por defecto: economy."
      },
      "max_price_per_passenger": {
        "type": "number",
        "description": "Precio maximo por pasajero en la moneda local. Si no se especifica, no se aplica filtro de precio."
      },
      "max_stops": {
        "type": "integer",
        "minimum": 0,
        "maximum": 3,
        "description": "Numero maximo de escalas. 0 = vuelo directo unicamente. Por defecto: 2."
      },
      "sort_by": {
        "type": "string",
        "enum": ["price", "duration", "departure_time", "arrival_time"],
        "description": "Criterio de ordenamiento. Por defecto: price."
      }
    }
  }
}
```

#### Herramienta 2: search_hotels

```json
{
  "name": "search_hotels",
  "description": "Busca alojamiento disponible en una ciudad o zona especifica. Usa esta funcion cuando el usuario pregunte por hoteles, alojamiento, donde hospedarse, o quiera comparar opciones de estadia. Tambien usala si el usuario pide recomendaciones de hoteles cercanos a una atraccion o en un barrio especifico.",
  "parameters": {
    "type": "object",
    "required": ["destination", "check_in", "check_out", "guests"],
    "properties": {
      "destination": {
        "type": "string",
        "description": "Ciudad, zona o barrio donde buscar alojamiento. Ej: 'Barcelona, Centro' o 'Cancun, Zona Hotelera'."
      },
      "check_in": {
        "type": "string",
        "description": "Fecha de entrada en formato YYYY-MM-DD."
      },
      "check_out": {
        "type": "string",
        "description": "Fecha de salida en formato YYYY-MM-DD."
      },
      "guests": {
        "type": "integer",
        "minimum": 1,
        "description": "Numero total de huespedes. Se asume 1 habitacion; si el usuario necesita multiples habitaciones, especificar en notes."
      },
      "max_price_per_night": {
        "type": "number",
        "description": "Precio maximo por noche en moneda local."
      },
      "min_rating": {
        "type": "number",
        "minimum": 0,
        "maximum": 10,
        "description": "Puntuacion minima (0-10). Por defecto: 7.0."
      },
      "amenities": {
        "type": "array",
        "items": {
          "type": "string",
          "enum": ["wifi", "air_conditioning", "pool", "gym", "spa", "restaurant",
                   "bar", "parking", "pet_friendly", "breakfast_included",
                   "laundry", "airport_shuttle", "beach_access", "kitchen"]
        },
        "description": "Lista de amenities requeridas."
      },
      "neighborhood": {
        "type": "string",
        "description": "Barrio o zona especifica. Usar cuando el usuario lo menciona explicitamente (ej: 'La Condesa en CDMX')."
      }
    }
  }
}
```

#### Herramienta 3: get_weather

```json
{
  "name": "get_weather",
  "description": "Obtiene el pronostico del clima para una ubicacion y rango de fechas. Usa esta funcion cuando el usuario pregunte sobre el clima, temperatura, probabilidad de lluvia, o condiciones meteorologicas en un destino. Tambien usala proactivamente al planificar itinerarios para recomendar ropa adecuada o actividades alternativas en caso de mal tiempo.",
  "parameters": {
    "type": "object",
    "required": ["location", "start_date", "end_date"],
    "properties": {
      "location": {
        "type": "string",
        "description": "Ciudad o coordenadas para consultar el clima. Ej: 'Tokyo, Japon' o 'Paris, Francia'."
      },
      "start_date": {
        "type": "string",
        "description": "Fecha inicial del pronostico en formato YYYY-MM-DD."
      },
      "end_date": {
        "type": "string",
        "description": "Fecha final del pronostico en formato YYYY-MM-DD. Maximo 16 dias desde la fecha actual."
      },
      "units": {
        "type": "string",
        "enum": ["celsius", "fahrenheit"],
        "description": "Unidad de temperatura. Por defecto: celsius."
      }
    }
  }
}
```

#### Herramienta 4: calculate_budget

```json
{
  "name": "calculate_budget",
  "description": "Calcula y analiza el presupuesto total de un viaje, verificando si es viable con los gastos planificados. Usa esta funcion cuando el usuario quiera saber si su presupuesto es suficiente, necesite optimizar gastos, o quiera ver un desglose detallado de costos. Tambien usala ANTES de recomendar vuelos u hoteles para filtrar opciones que se ajusten al presupuesto.",
  "parameters": {
    "type": "object",
    "required": ["total_budget", "currency", "destination", "duration_days", "travelers"],
    "properties": {
      "total_budget": {
        "type": "number",
        "description": "Presupuesto total disponible para TODO el viaje (vuelos + alojamiento + comida + actividades + transporte + extras)."
      },
      "currency": {
        "type": "string",
        "description": "Codigo de moneda ISO 4217. Ej: 'USD', 'EUR', 'MXN', 'ARS'."
      },
      "destination": {
        "type": "string",
        "description": "Destino del viaje (ciudad y pais). Influye en costos diarios estimados."
      },
      "duration_days": {
        "type": "integer",
        "minimum": 1,
        "description": "Duracion total del viaje en dias."
      },
      "travelers": {
        "type": "integer",
        "minimum": 1,
        "description": "Numero total de viajeros."
      },
      "flights_budget": {
        "type": "number",
        "description": "Porcion del presupuesto reservada para vuelos. Si no se especifica, se calcula como 30-40% del total."
      },
      "hotel_budget": {
        "type": "number",
        "description": "Porcion del presupuesto reservada para alojamiento."
      },
      "daily_budget_per_person": {
        "type": "number",
        "description": "Presupuesto diario por persona (comida + transporte + actividades). Si no se especifica, se estima segun destino."
      }
    }
  }
}
```

#### Herramienta 5: send_itinerary_email

```json
{
  "name": "send_itinerary_email",
  "description": "Envia el itinerario de viaje por correo electronico al usuario. Usa esta funcion SOLO cuando el usuario solicite explicitamente recibir el plan por email o cuando pregunte 'puedes enviarmelo?'. NUNCA envies emails sin confirmacion explicita del usuario. Antes de invocar, confirma la direccion de correo y pregunta si desea agregar notas adicionales.",
  "parameters": {
    "type": "object",
    "required": ["to_email", "subject", "itinerary"],
    "properties": {
      "to_email": {
        "type": "string",
        "format": "email",
        "description": "Direccion de correo electronico del destinatario. SIEMPRE confirma con el usuario antes de usar."
      },
      "subject": {
        "type": "string",
        "description": "Asunto del correo. Por defecto: 'Tu Itinerario de Viaje a {destino} - {fechas}'."
      },
      "itinerary": {
        "type": "object",
        "description": "Objeto completo del itinerario (formato DailyItinerary o TravelPlan)."
      },
      "include_flight_details": {
        "type": "boolean",
        "description": "Incluir detalles de vuelos en el email."
      },
      "include_hotel_details": {
        "type": "boolean",
        "description": "Incluir detalles de hotel en el email."
      },
      "notes": {
        "type": "string",
        "description": "Notas adicionales del usuario para incluir en el email."
      },
      "format": {
        "type": "string",
        "enum": ["detailed", "summary", "printable"],
        "description": "Formato del email. 'detailed': itinerario completo, 'summary': resumen de una pagina, 'printable': optimizado para impresion. Por defecto: detailed."
      }
    }
  }
}
```

### 3.3 Mejores Practicas Aplicadas en las Descripciones

Las descripciones de herramientas anteriores siguen estos principios:

```
BEST PRACTICES PARA FUNCTION DESCRIPTIONS:

1. CLARIDAD DE PROPOSITO:
   - "CUANDO USAR" explicito en la description
   - "CUANDO NO USAR" para evitar falsos positivos
   - NO ambiguas: el modelo debe saber EXACTAMENTE cuando invocar cada tool

2. PARAMETROS AUTO-DOCUMENTADOS:
   - description en CADA parametro, no solo en los requeridos
   - Indicar formato esperado (YYYY-MM-DD, ISO 4217, IATA codes)
   - Ejemplos inline en las descripciones ("Ej: 'MEX', 'EZE'")
   - Valores por defecto explicitos

3. RESTRICCIONES CLARAS:
   - min/max en valores numericos
   - enum para valores discretos
   - required bien definido (solo lo ABSOLUTAMENTE necesario)

4. PRECAUCIONES DE SEGURIDAD:
   - send_itinerary_email: confirmacion explicita antes de enviar
   - search_flights: no asumir codigos IATA, preguntar o resolver
```

### 3.4 Manejo de Errores en Function Calling

```
FLUJO DE MANEJO DE ERRORES:

    LLM invoca funcion
           |
           v
    +------+------+
    | Ejecutar     |
    | funcion      |
    +------+------+
           |
    +------v------+
    | Exito?       |
    +--+-------+--+
       |YES    |NO
       v       v
    +--+--+  +--+------------------+
    | OK  |  | Error recuperable?  |
    +-----+  +--+-----+-----------+
                 |YES  |NO
                 v     v
           +-----+--+ +--+------------------+
           | Reintentar| | Informar al       |
           | con       | | usuario que la    |
           | backoff   | | operacion fallo   |
           | (max 3)   | | y sugerir         |
           +-----+----+ | alternativas       |
                 |       +--------------------+
           +-----v----+
           | Exito?    |
           +--+-----+--+
              |YES  |NO
              v     v
           +--+--+  +------------------------+
           | OK  |  | Informar al usuario    |
           +-----+  | con mensaje amigable   |
                     +------------------------+
```

---


## FASE 4 - ORQUESTACION (Nivel: Avanzado)

### Objetivo

Implementar un flujo de planificacion de viajes multi-step donde el asistente coordine multiples llamadas a herramientas en secuencia y en paralelo para resolver una solicitud compleja del usuario.

### 4.1 El Escenario Principal

```
USUARIO: "Planifica un viaje de 5 dias a Tokyo en abril 2026 con un presupuesto
          de $3,000 USD. Viajo solo. Me interesa la tecnologia, la comida callejera
          y los templos antiguos."

FLUJO DE ORQUESTACION REQUERIDO:

PASO 1 (PARALELO):             PASO 2 (SECUENCIAL):          PASO 3 (SECUENCIAL):
+-------------------+          +-------------------+         +-------------------+
| search_flights    |          | calculate_budget   |         | Generar           |
| (origen -> Tokyo) |          | (verificar que     |         | itinerario        |
+-------------------+          |  $3000 alcanza)    |         | dia por dia       |
                               +-------------------+         +-------------------+
+-------------------+
| search_hotels     |                  |                             |
| (Tokyo, centro)   |                  v                             v
+-------------------+          +-------------------+         +-------------------+
                               | Ajustar vuelos/    |         | Enviar email      |
+-------------------+          | hoteles si es      |-------->| (si usuario       |
| get_weather       |          | necesario          |         |  lo pide)         |
| (Tokyo, abril)    |          +-------------------+         +-------------------+
+-------------------+
```

### 4.2 Implementacion del Orquestador

```python
"""
Orquestador de planificacion de viajes.

Coordina multiples llamadas a herramientas en paralelo y secuencial
para resolver solicitudes complejas de planificacion de viajes.
"""

import asyncio
import random
from typing import Dict, List, Any, Optional, Tuple
from dataclasses import dataclass, field
from datetime import datetime, timedelta
from enum import Enum
import json


class OrchestrationStatus(Enum):
    PENDING = "pending"
    IN_PROGRESS = "in_progress"
    COMPLETED = "completed"
    FAILED = "failed"
    NEEDS_CLARIFICATION = "needs_clarification"


@dataclass
class TravelRequest:
    destination: str = ""
    departure_date: str = ""
    return_date: str = ""
    travelers: int = 1
    total_budget: float = None
    currency: str = "USD"
    origin: str = None
    interests: List[str] = field(default_factory=list)
    preferred_rhythm: str = "moderado"
    cabin_class: str = "economy"
    hotel_preferences: Dict[str, Any] = field(default_factory=dict)


@dataclass
class TravelPlan:
    request: TravelRequest
    flights: Optional[Dict[str, Any]] = None
    hotels: Optional[Dict[str, Any]] = None
    weather: Optional[Dict[str, Any]] = None
    budget: Optional[Dict[str, Any]] = None
    itinerary: Optional[List[Dict[str, Any]]] = None
    created_at: str = field(default_factory=lambda: datetime.now().isoformat())
    status: OrchestrationStatus = OrchestrationStatus.PENDING
    errors: List[str] = field(default_factory=list)
    token_usage: Dict[str, int] = field(default_factory=dict)


class TravelOrchestrator:
    """
    Orquestador principal del Asistente de Viajes.
    
    Responsabilidades:
    - Interpretar la solicitud del usuario
    - Hacer llamadas en paralelo cuando sea posible
    - Secuenciar llamadas que dependen de resultados previos
    - Manejar errores y recuperacion
    - Registrar uso de tokens
    """
    
    def __init__(self, llm_client=None):
        self.llm = llm_client
        self.total_tokens_used = 0
        self.conversation_history = []
    
    async def plan_trip(self, user_message: str) -> TravelPlan:
        """
        Orquesta la planificacion completa de un viaje.
        
        Args:
            user_message: Mensaje del usuario en lenguaje natural
        
        Returns:
            TravelPlan con resultados de todas las etapas
        """
        print(f"\n{'='*60}")
        print(f"INICIANDO PLANIFICACION DE VIAJE")
        print(f"{'='*60}")
        
        self.conversation_history.append({"role": "user", "content": user_message})
        
        # ETAPA 0: Extraer parametros del mensaje del usuario
        request = await self._extract_travel_params(user_message)
        plan = TravelPlan(request=request)
        
        if request.destination is None:
            plan.status = OrchestrationStatus.NEEDS_CLARIFICATION
            plan.errors.append("No se pudo determinar el destino del viaje")
            return plan
        
        print(f"\nDestino: {request.destination}")
        print(f"Fechas: {request.departure_date} -> {request.return_date}")
        print(f"Presupuesto: ${request.total_budget} {request.currency}")
        
        # ETAPA 1: Llamadas en PARALELO (independientes entre si)
        print(f"\n--- ETAPA 1: Busquedas en paralelo ---")
        flights_task = self._search_flights_async(request)
        hotels_task = self._search_hotels_async(request)
        weather_task = self._get_weather_async(request)
        
        plan.flights, plan.hotels, plan.weather = await asyncio.gather(
            flights_task, hotels_task, weather_task
        )
        
        if plan.flights and "meta" in plan.flights:
            print(f"  Vuelos encontrados: {plan.flights['meta']['total_results']}")
        if plan.hotels and "meta" in plan.hotels:
            print(f"  Hoteles encontrados: {plan.hotels['meta']['total_results']}")
        if plan.weather:
            print(f"  Clima: {plan.weather.get('summary', 'N/A')}")
        
        # ETAPA 2: Calculo de presupuesto (depende de vuelos y hoteles)
        print(f"\n--- ETAPA 2: Analisis de presupuesto ---")
        plan.budget = await self._calculate_budget_async(request, plan.flights, plan.hotels)
        print(f"  Estado: {plan.budget['status']}")
        print(f"  Porcentaje usado: {plan.budget['percentage_used']}%")
        
        if plan.budget["status"] == "over_budget":
            print(f"  ALERTA: Presupuesto excedido. Generando sugerencias...")
            for s in plan.budget.get("savings_suggestions", []):
                print(f"    - {s['category']}: {s['suggestion']} (ahorro: ${s['potential_savings']})")
        
        # ETAPA 3: Generacion de itinerario (depende de clima y presupuesto)
        print(f"\n--- ETAPA 3: Generacion de itinerario ---")
        plan.itinerary = await self._generate_itinerary_async(request, plan.weather, plan.budget)
        print(f"  Itinerario generado: {len(plan.itinerary)} dias")
        
        # ETAPA 4: Resumen final
        plan.status = OrchestrationStatus.COMPLETED
        print(f"\n{'='*60}")
        print(f"PLANIFICACION COMPLETADA")
        print(f"{'='*60}")
        
        return plan
    
    async def _extract_travel_params(self, user_message: str) -> TravelRequest:
        print("  [LLM] Extrayendo parametros del mensaje...")
        return TravelRequest(
            destination="Tokyo, Japon",
            departure_date="2026-04-10",
            return_date="2026-04-15",
            travelers=1,
            total_budget=3000.00,
            currency="USD",
            origin="MEX",
            interests=["tecnologia", "comida callejera", "templos antiguos"],
            preferred_rhythm="moderado"
        )
    
    async def _search_flights_async(self, request: TravelRequest) -> Dict[str, Any]:
        print(f"  [PARALELO] Buscando vuelos a {request.destination}...")
        await asyncio.sleep(0.5)
        
        AIRLINES = ["Aeromexico", "Japan Airlines", "ANA", "United", "Delta"]
        origin = request.origin or "MEX"
        dest_code = "NRT" if "tokyo" in request.destination.lower() else request.destination[:3].upper()
        
        results = []
        for i in range(4):
            airline = random.choice(AIRLINES)
            stops = random.randint(0, 2)
            base_price = random.uniform(400, 900)
            price = round(base_price * (1 - stops * 0.15), 2)
            results.append({
                "id": f"FL-{i+1:03d}",
                "airline": airline,
                "flight_numbers": [f"{airline[:2].upper()}{random.randint(100,999)}"],
                "origin": origin,
                "destination": dest_code,
                "departure": f"{request.departure_date}T{random.randint(6,22):02d}:{random.choice([0,15,30,45]):02d}:00",
                "arrival": f"{request.departure_date}T{random.randint(6,22):02d}:{random.choice([0,15,30,45]):02d}:00",
                "duration_minutes": random.randint(300, 900),
                "stops": stops,
                "price": price,
                "currency": request.currency,
                "cabin_class": request.cabin_class,
                "baggage": {"carry_on": True, "checked_bags": 1 if stops == 0 else 0},
                "refundable": random.choice([True, False]),
                "recommendation_score": random.randint(5, 10)
            })
        
        results.sort(key=lambda x: x["price"])
        return {
            "search_params": {"origin": origin, "destination": dest_code},
            "results": results,
            "meta": {"total_results": len(results),
                     "cheapest_option_id": results[0]["id"],
                     "search_timestamp": datetime.now().isoformat()}
        }
    
    async def _search_hotels_async(self, request: TravelRequest) -> Dict[str, Any]:
        print(f"  [PARALELO] Buscando hoteles en {request.destination}...")
        await asyncio.sleep(0.3)
        
        HOTELS = [
            ("Grand Horizon Hotel", 5, 280, 9.2, ["wifi","pool","spa","gym","restaurant"]),
            ("City Center Inn", 3, 95, 7.8, ["wifi","air_conditioning","laundry"]),
            ("Boutique Sakura Hotel", 4, 180, 8.9, ["wifi","restaurant","bar","air_conditioning"]),
            ("Tokyo Business Suites", 4, 200, 8.5, ["wifi","gym","restaurant","air_conditioning"]),
            ("Budget Traveler Lodge", 2, 55, 6.9, ["wifi","air_conditioning"]),
            ("Imperial Palace Hotel", 5, 420, 9.7, ["wifi","spa","restaurant","bar","gym"]),
        ]
        
        days = (datetime.fromisoformat(request.return_date) - datetime.fromisoformat(request.departure_date)).days
        max_price = None
        if request.total_budget:
            max_price = (request.total_budget * 0.35) / days
        
        results = []
        for name, stars, base_price, rating, amenities in HOTELS:
            price = round(base_price * random.uniform(0.8, 1.4), 2)
            if max_price and price > max_price:
                continue
            results.append({
                "id": f"HT-{len(results)+1:03d}",
                "name": name,
                "category": f"{stars}_star",
                "location": {
                    "city": request.destination.split(",")[0].strip(),
                    "country": "Japon" if "tokyo" in request.destination.lower() else "Desconocido",
                    "address": f"Calle Principal {random.randint(1,500)}, {request.destination}",
                    "neighborhood": "Centro",
                    "distance_to_center_km": round(random.uniform(0.1, 5.0), 1)
                },
                "price_per_night": price,
                "currency": request.currency,
                "total_price": round(price * days, 2),
                "rating": round(rating + random.uniform(-0.3, 0.3), 1),
                "review_count": random.randint(50, 5000),
                "amenities": amenities,
                "cancellation_policy": random.choice(["free_cancellation","partial_refund","non_refundable"]),
                "check_in_time": "15:00",
                "check_out_time": "11:00"
            })
        
        results.sort(key=lambda x: x["price_per_night"])
        return {
            "search_params": {"destination": request.destination},
            "results": results,
            "meta": {"total_results": len(results), "search_timestamp": datetime.now().isoformat()}
        }
    
    async def _get_weather_async(self, request: TravelRequest) -> Dict[str, Any]:
        print(f"  [PARALELO] Consultando clima en {request.destination}...")
        await asyncio.sleep(0.2)
        
        month = datetime.fromisoformat(request.departure_date).month
        if 3 <= month <= 5:
            temp_min, temp_max, condition = 10, 22, "parcialmente nublado, cerezos en flor"
        elif 6 <= month <= 8:
            temp_min, temp_max, condition = 22, 32, "caluroso y humedo"
        elif 9 <= month <= 11:
            temp_min, temp_max, condition = 12, 25, "agradable, hojas de otono"
        else:
            temp_min, temp_max, condition = 1, 12, "frio, posible nieve ligera"
        
        forecast = []
        start = datetime.fromisoformat(request.departure_date)
        end = datetime.fromisoformat(request.return_date)
        current = start
        while current <= end:
            forecast.append({
                "date": current.strftime("%Y-%m-%d"),
                "temp_c": round(random.uniform(temp_min, temp_max), 1),
                "temp_min_c": temp_min,
                "temp_max_c": temp_max,
                "condition": condition,
                "rain_probability": random.randint(10, 50),
                "humidity": random.randint(40, 80),
                "wind_kmh": random.randint(5, 25)
            })
            current += timedelta(days=1)
        
        return {
            "location": request.destination,
            "period": {"start": request.departure_date, "end": request.return_date},
            "forecast": forecast,
            "summary": f"Temperaturas entre {temp_min} y {temp_max} grados. {condition}."
        }
    
    async def _calculate_budget_async(self, request: TravelRequest, flights: Dict, hotels: Dict) -> Dict[str, Any]:
        print(f"  [SECUENCIAL] Calculando presupuesto...")
        
        flights_cost = sum(r["price"] * request.travelers for r in flights.get("results", [])) / max(len(flights.get("results", [1])), 1)
        hotels_cost = sum(r["total_price"] for r in hotels.get("results", [])) / max(len(hotels.get("results", [1])), 1)
        days = (datetime.fromisoformat(request.return_date) - datetime.fromisoformat(request.departure_date)).days
        
        daily_per_person = 80
        total_daily = daily_per_person * request.travelers * days
        total_planned = flights_cost + hotels_cost + total_daily
        emergency = request.total_budget * 0.10
        total_spent = total_planned + emergency
        remaining = request.total_budget - total_spent
        
        if remaining > 0:
            status = "under_budget"
        elif remaining >= -request.total_budget * 0.05:
            status = "on_budget"
        else:
            status = "over_budget"
        
        return {
            "total_budget": request.total_budget,
            "currency": request.currency,
            "breakdown": {
                "flights": round(flights_cost, 2),
                "accommodation": round(hotels_cost, 2),
                "food": round(total_daily * 0.40, 2),
                "transport_local": round(total_daily * 0.20, 2),
                "activities": round(total_daily * 0.25, 2),
                "insurance": round(request.total_budget * 0.05, 2),
                "emergency_fund": round(emergency, 2),
                "other": round(total_daily * 0.15, 2)
            },
            "total_spent": round(total_spent, 2),
            "remaining": round(remaining, 2),
            "status": status,
            "percentage_used": round((total_spent / request.total_budget) * 100, 1),
            "savings_suggestions": [],
            "traffic_light": {
                "green": ["Vuelos", "Alojamiento", "Seguro de viaje"],
                "yellow": ["Cenas en restaurantes", "Actividades pagadas", "Compras"],
                "red": ["Excursiones premium", "Upgrades de clase", "Spa en hotel"]
            }
        }
    
    async def _generate_itinerary_async(self, request: TravelRequest, weather: Dict, budget: Dict) -> List[Dict]:
        print(f"  [SECUENCIAL] Generando itinerario...")
        await asyncio.sleep(0.4)
        
        days = (datetime.fromisoformat(request.return_date) - datetime.fromisoformat(request.departure_date)).days
        
        attractions_pool = {
            "tecnologia": ["Akihabara (barrio electronico)", "Museo Miraikan", "TeamLab Borderless", "Odaiba"],
            "comida callejera": ["Mercado Tsukiji", "Calle Takeshita Harajuku", "Ameyoko Market", "Shimokitazawa"],
            "templos antiguos": ["Senso-ji Asakusa", "Meiji Jingu", "Santuario Yasukuni", "Templo Zojo-ji"]
        }
        
        itinerary = []
        for day_num in range(1, days + 1):
            date = datetime.fromisoformat(request.departure_date) + timedelta(days=day_num - 1)
            
            activities = []
            for interest in request.interests:
                if interest in attractions_pool and attractions_pool[interest]:
                    activities.append(random.choice(attractions_pool[interest]))
            
            day_plan = {
                "day_number": day_num,
                "date": date.strftime("%Y-%m-%d"),
                "theme": f"Dia {day_num}: Explorando {request.destination.split(',')[0]}",
                "weather_forecast": {
                    "condition": weather["forecast"][day_num-1]["condition"] if day_num <= len(weather["forecast"]) else "N/A",
                    "temp_min_c": weather["forecast"][day_num-1]["temp_min_c"] if day_num <= len(weather["forecast"]) else 15,
                    "temp_max_c": weather["forecast"][day_num-1]["temp_max_c"] if day_num <= len(weather["forecast"]) else 25,
                    "rain_probability": weather["forecast"][day_num-1]["rain_probability"] if day_num <= len(weather["forecast"]) else 20
                },
                "time_blocks": [
                    {"time": "08:00", "activity": "Desayuno en konbini local", "category": "food", "cost": 5, "duration_minutes": 30},
                    {"time": "09:00", "activity": activities[0] if activities else "Explorar el barrio", "category": "sightseeing", "cost": 15, "duration_minutes": 180},
                    {"time": "12:30", "activity": "Almuerzo en restaurante local", "category": "food", "cost": 12, "duration_minutes": 60},
                    {"time": "14:00", "activity": activities[1] if len(activities) > 1 else "Tiempo libre", "category": "culture", "cost": 8, "duration_minutes": 120},
                    {"time": "17:00", "activity": "Paseo y fotografia urbana", "category": "rest", "cost": 0, "duration_minutes": 120},
                    {"time": "19:00", "activity": "Cena izakaya en Golden Gai", "category": "food", "cost": 25, "duration_minutes": 90},
                ],
                "estimated_cost": {
                    "food": 42,
                    "transport": 8,
                    "activities": 23,
                    "other": 5,
                    "total": 78
                },
                "free_time_minutes": 120
            }
            itinerary.append(day_plan)
        
        return itinerary
    
    def get_summary(self, plan: TravelPlan) -> str:
        if plan.status != OrchestrationStatus.COMPLETED:
            return f"Planificacion no completada. Estado: {plan.status.value}"
        
        best_flight = plan.flights["results"][0]
        best_hotel = plan.hotels["results"][0] if plan.hotels["results"] else None
        
        summary = f"""
================================================================
           PLAN DE VIAJE: {plan.request.destination}
================================================================

FECHAS: {plan.request.departure_date} al {plan.request.return_date}
VIAJEROS: {plan.request.travelers}

VUELO RECOMENDADO:
  {best_flight['airline']} - {'Directo' if best_flight['stops']==0 else f"{best_flight['stops']} escala(s)"}
  Precio: ${best_flight['price']} {plan.request.currency} por persona
"""
        if best_hotel:
            summary += f"""
HOTEL RECOMENDADO:
  {best_hotel['name']} - {best_hotel['category'].replace('_', ' ')}
  Precio: ${best_hotel['price_per_night']}/noche
  Rating: {best_hotel['rating']}/10
"""
        summary += f"""
CLIMA: {plan.weather['summary']}

PRESUPUESTO:
  Total: ${plan.budget['total_budget']}
  Gastado: ${plan.budget['total_spent']}
  Restante: ${plan.budget['remaining']}
  Estado: {plan.budget['status']}

ITINERARIO: {len(plan.itinerary)} dias planificados
================================================================
"""
        return summary


async def main():
    orchestrator = TravelOrchestrator()
    plan = await orchestrator.plan_trip(
        "Planifica un viaje de 5 dias a Tokyo en abril 2026 con un presupuesto "
        "de $3,000 USD. Viajo solo. Me interesa la tecnologia, la comida callejera "
        "y los templos antiguos."
    )
    print(orchestrator.get_summary(plan))


if __name__ == "__main__":
    asyncio.run(main())
```

---


## FASE 5 - EVALUACION (Nivel: Maestro)

### Objetivo

Crear un dataset de evaluacion con 20 escenarios de viaje, definir metricas cuantitativas y cualitativas, ejecutar la evaluacion, documentar resultados, e iterar para mejorar el sistema.

### 5.1 Dataset de Evaluacion (20 Escenarios)

```
ESTRUCTURA DE CADA CASO DE EVALUACION:

{
  "id": "EVAL-001",
  "category": "basico | intermedio | complejo | borde",
  "user_input": "Mensaje exacto del usuario",
  "expected": {
    "must_include": ["elementos que DEBEN aparecer en la respuesta"],
    "must_not_include": ["elementos que NO DEBEN aparecer"],
    "tools_to_call": ["lista de herramientas que se espera invocar"],
    "parallel_calls": true/false,
    "budget_check": true/false
  },
  "ground_truth": {
    "destination": "string",
    "num_days": integer,
    "budget": number,
    "flights_should_be_queried": boolean,
    "hotels_should_be_queried": boolean,
    "weather_should_be_queried": boolean
  }
}
```

#### Escenarios de Evaluacion

```
ESCENARIOS BASICOS (EVAL-001 a EVAL-005):

EVAL-001: Viaje simple con toda la informacion
Input: "Busca vuelos de CDMX a Cancun, salida 20 julio 2026, regreso 25 julio,
        2 adultos, clase economica, presupuesto $5,000 MXN por persona para vuelos."
Expected: search_flights llamado. 3-5 opciones de vuelo. Sin hoteles ni clima.

EVAL-002: Busqueda de hotel con preferencias
Input: "Recomiendame hoteles en el centro de Madrid, 3 noches del 1 al 4 de
        septiembre 2026, 2 personas, maximo 120 EUR por noche, que tenga wifi
        y aire acondicionado."
Expected: search_hotels llamado. 3-5 opciones. Filtrar por precio y amenities.

EVAL-003: Consulta de clima
Input: "Que clima hara en Santiago de Chile la primera semana de agosto 2026?"
Expected: get_weather llamado. Pronostico de 7 dias. Temperaturas en celsius.

EVAL-004: Calculo de presupuesto
Input: "Tengo $2,500 USD para un viaje de 7 dias a Lima, Peru. Somos 2 personas.
        Es suficiente?"
Expected: calculate_budget llamado. Desglose. Estado (under/on/over budget).

EVAL-005: Itinerario simple
Input: "Dame un itinerario de 2 dias en Buenos Aires. Me gusta el tango, la
        carne y los museos."
Expected: Itinerario con timeline dia a dia. Actividades relacionadas con intereses.

ESCENARIOS INTERMEDIOS (EVAL-006 a EVAL-012):

EVAL-006: Viaje con presupuesto ajustado
Input: "Quiero ir a Nueva York del 10 al 15 de octubre 2026. Tengo $1,500 USD
        total. Viajo solo. Es posible?"
Expected: calculate_budget + search_flights + search_hotels. Si over_budget,
          debe sugerir alternativas (hostales, fechas flexibles).

EVAL-007: Viaje con intereses especificos
Input: "Planifica un viaje a Tokyo del 1 al 7 de abril 2026. Presupuesto $3,500
        USD. Me interesa anime, manga, videojuegos, y comida ramen."
Expected: Todas las herramientas llamadas. Itinerario con Akihabara, tiendas
          de anime, restaurantes de ramen. Clima de abril considerado.

EVAL-008: Viaje con restricciones fisicas
Input: "Viaje a Roma con mi mama de 70 anos, 5 dias en junio 2026. Ella camina
        con dificultad (max 2-3 horas seguidas). Presupuesto $4,000 EUR."
Expected: Itinerario con ritmo relajado. Hoteles con accesibilidad. Pocas
          actividades fisicamente demandantes. Transporte privado sugerido.

EVAL-009: Viaje en temporada de lluvias
Input: "Viaje a Cancun del 15 al 20 de septiembre 2026. Somos una pareja,
        presupuesto $2,000 USD. Queremos playa pero sabemos que es temporada
        de huracanes."
Expected: get_weather llamado. Planes alternativos para dias de lluvia. Hotel
          con actividades indoor. Seguro de viaje mencionado.

EVAL-010: Multiples destinos
Input: "Quiero un viaje por Europa: Paris 3 dias, Barcelona 3 dias, Roma 3 dias.
        Salida desde CDMX, primera semana de julio 2026. Presupuesto $5,000 USD."
Expected: Multiples llamadas a search_flights. Multiples search_hotels.
          Presupuesto complejo que abarca 3 ciudades.

EVAL-011: Viaje de negocios
Input: "Viaje de trabajo a Londres, 3 dias (12-15 noviembre 2026). Necesito
        hotel cerca del centro financiero, con buen wifi, gimnasio, y desayuno
        temprano. Presupuesto de hotel $300 USD/noche. Vuelo en business class."
Expected: Hotel con amenities de negocio. Vuelo business class. Itinerario
          que deja espacio para reuniones.

EVAL-012: Viaje familiar con ninos
Input: "Viaje familiar a Orlando, 7 dias en diciembre 2026. Familia de 4: papa,
        mama, nino 8 anos, nina 5 anos. Presupuesto $6,000 USD. Queremos parques
        Disney y actividades para ninos."
Expected: Actividades familiares. Hoteles family-friendly. Vuelos considerando
          4 pasajeros. Presupuesto realista para Disney.

ESCENARIOS COMPLEJOS (EVAL-013 a EVAL-017):

EVAL-013: Viaje con informacion ambigua
Input: "Quiero irme de vacaciones en agosto. Algo de playa, no muy caro."
Expected: El asistente DEBE hacer preguntas de seguimiento para clarificar:
          destino, origen, presupuesto, dias, personas. No debe inventar.

EVAL-014: Viaje con cambio de planes
Input (conversacion de 3 turnos):
  Turno 1: "Planea un viaje a Paris, 5 dias en mayo 2026, $3,000 EUR."
  Turno 2: "Mejor cambialo a 7 dias en vez de 5. Me alcanza el presupuesto?"
  Turno 3: "Agregame actividades de fotografia, me gusta la fotografia callejera."
Expected: Manejar el contexto conversacional. Recalcular en turno 2. Actualizar
          itinerario en turno 3 sin perder lo anterior.

EVAL-015: Viaje con moneda extranjera
Input: "Viaje a Buenos Aires desde CDMX. 10 dias en enero 2026. Presupuesto de
        50,000 MXN. Quiero saber si me alcanza considerando la inflacion en
        Argentina."
Expected: Manejar conversion de moneda. Mencionar tipo de cambio. Ser realista
          sobre la economia argentina.

EVAL-016: Viaje de ultima hora
Input: "Necesito volar manana a Santiago de Chile por una emergencia. El vuelo
        mas barato que encuentres. Solo ida."
Expected: Solo search_flights. Precios de ultima hora mas altos. Detectar urgencia.

EVAL-017: Viaje con requerimientos contradictorios
Input: "Quiero un viaje de lujo a Dubai, 7 dias, pero mi presupuesto es solo
        $2,000 USD total."
Expected: Identificar contradiccion y comunicarla. Sugerir ajustes: menos dias,
          destino alternativo, o aumentar presupuesto.

ESCENARIOS DE BORDE (EVAL-018 a EVAL-020):

EVAL-018: Destino invalido
Input: "Busca vuelos al Planeta Marte para julio 2026."
Expected: Manejar con humor pero profesionalismo. No inventar cohetes. Sugerir
          destinos espaciales en la Tierra.

EVAL-019: Fechas en el pasado
Input: "Quiero viajar a Madrid del 1 al 5 de enero de 2020."
Expected: Detectar fecha pasada. Informar al usuario. No buscar vuelos en pasado.

EVAL-020: Sin presupuesto ni restricciones
Input: "Recomiendame el mejor viaje posible. Sin limite de presupuesto. 2 semanas."
Expected: Hacer preguntas sobre intereses y estilo de viaje. Ofrecer opciones
          variadas: Maldivas, safari en Tanzania, expedicion Antartica, etc.
```

### 5.2 Definicion de Metricas

```python
"""
Sistema de metricas para evaluacion del Asistente de Viajes.
"""

from dataclasses import dataclass, field
from typing import List, Dict, Any
from enum import Enum


class MetricCategory(Enum):
    COMPLETENESS = "completeness"
    ACCURACY = "accuracy"
    EFFICIENCY = "efficiency"
    QUALITY = "quality"
    SAFETY = "safety"


@dataclass
class EvaluationMetrics:
    """Metricas cuantitativas:"""
    
    completion_rate: float = 0.0
    """Porcentaje de respuestas que se completaron sin errores (0-1)."""

    tool_selection_accuracy: float = 0.0
    """Porcentaje de casos donde se seleccionaron las herramientas correctas."""

    budget_accuracy: float = 0.0
    """Porcentaje de casos donde el presupuesto esta dentro del +/-5% esperado."""

    schema_compliance_rate: float = 0.0
    """Porcentaje de outputs que pasan validacion JSON."""

    parallel_execution_rate: float = 0.0
    """Porcentaje de casos con llamadas en paralelo cuando era posible."""

    response_time_seconds: float = 0.0
    """Tiempo promedio de respuesta."""

    tokens_per_interaction: float = 0.0
    """Promedio de tokens consumidos por interaccion."""

    hallucination_rate: float = 0.0
    """Porcentaje de respuestas con informacion inventada."""

    """Metricas cualitativas (evaluacion humana, 1-5):"""
    
    recommendation_appropriateness: float = 0.0
    conversation_quality: float = 0.0
    budget_optimization_score: float = 0.0
    cultural_sensitivity: float = 0.0


@dataclass
class EvaluationResult:
    case_id: str
    passed: bool
    metrics: Dict[str, float]
    errors: List[str] = field(default_factory=list)
    warnings: List[str] = field(default_factory=list)
    notes: str = ""


@dataclass
class EvaluationRun:
    timestamp: str
    model_name: str
    prompt_version: str
    results: List[EvaluationResult]
    
    def overall_pass_rate(self) -> float:
        passed = sum(1 for r in self.results if r.passed)
        return passed / max(len(self.results), 1)
    
    def summary(self) -> str:
        lines = [
            f"RESULTADOS DE EVALUACION",
            f"=========================",
            f"Modelo: {self.model_name}",
            f"Version de prompt: {self.prompt_version}",
            f"Fecha: {self.timestamp}",
            f"",
            f"Total de casos: {len(self.results)}",
            f"Aprobados: {sum(1 for r in self.results if r.passed)}",
            f"Tasa de aprobacion: {self.overall_pass_rate():.1%}",
            f"",
            f"Por categoria:",
        ]
        
        categories = {}
        for r in self.results:
            cat = r.case_id[:5]
            if cat not in categories:
                categories[cat] = {"total": 0, "passed": 0}
            categories[cat]["total"] += 1
            if r.passed:
                categories[cat]["passed"] += 1
        
        for cat, counts in categories.items():
            rate = counts["passed"] / counts["total"] * 100
            lines.append(f"  {cat}: {counts['passed']}/{counts['total']} ({rate:.0f}%)")
        
        failed = [r for r in self.results if not r.passed]
        if failed:
            lines.append("")
            lines.append("Casos fallidos:")
            for r in failed:
                lines.append(f"  - {r.case_id}: {', '.join(r.errors[:2])}")
        
        return "\n".join(lines)
```

### 5.3 Ejecucion de Evaluacion

```
PROCEDIMIENTO DE EVALUACION:

1. PREPARACION:
   - Cargar los 20 escenarios de evaluacion
   - Configurar el LLM con el prompt de sistema y herramientas
   - Inicializar contadores de tokens

2. EJECUCION:
   Para cada escenario EVAL-001 a EVAL-020:
     a) Enviar el user_input al asistente
     b) Observar que herramientas invoca
     c) Medir tiempo de respuesta y tokens consumidos
     d) Comparar la respuesta con expected y ground_truth
     e) Registrar si paso o fallo, y por que

3. ANALISIS:
   - Calcular todas las metricas cuantitativas
   - Realizar evaluacion cualitativa (preferiblemente con 2+ revisores humanos)
   - Identificar patrones de falla comunes
   - Generar informe de resultados

4. ITERACION (ver abajo)
```

### 5.4 Ciclo de Iteracion

```
CICLO DE MEJORA CONTINUA:

         +------------------+
         | 1. Ejecutar      |
         | evaluacion       |
         | (20 escenarios)  |
         +--------+---------+
                  |
                  v
         +--------+---------+
         | 2. Analizar      |
         | resultados       |
         | patrones de falla|
         +--------+---------+
                  |
                  v
         +--------+---------+
         | 3. Identificar   |
         | causas raiz      |
         | (prompt? schema? |
         |  tool desc?)     |
         +--------+---------+
                  |
                  v
    +-------------+-------------+
    |                           |
    v                           v
+---+-------------+   +--------+----------+
| 4a. Ajustar     |   | 4b. Refinar       |
| prompt          |   | descripciones     |
| (system/task)   |   | de herramientas   |
+---+-------------+   +--------+----------+
    |                           |
    +-------------+-------------+
                  |
                  v
         +--------+---------+
         | 5. Volver a      |
         | evaluar y        |
         | comparar con     |
         | baseline         |
         +--------+---------+
                  |
                  v
         +--------+---------+
         | Mejora > 5%?     |
         +--+----------+----+
            |NO        |SI
            v          v
    +-------+--+  +----+-------+
    | Aceptar   |  |Iterar de   |
    | version   |  |nuevo (max  |
    | actual    |  |5 ciclos)   |
    +-----------+  +------------+
```

### 5.5 Ejemplo de Resultados de Iteracion

```
EJEMPLO DE ITERACION REAL:

Ciclo 1 (Baseline):
  Completion rate: 65% (13/20)
  Budget accuracy: 60%
  Schema compliance: 70%
  Hallucination rate: 25%

  Problema principal: El modelo inventa nombres de hoteles y vuelos (25% de
  respuestas contienen datos ficticios).
  
  Causa raiz: El prompt de sistema no enfatiza que use solo datos de las
  herramientas. Las descripciones no establecen la fuente de verdad.

Ciclo 2 (Prompt mejorado + descripciones refinadas):
  Cambios:
  - System prompt: "SOLO usa datos de las herramientas. NUNCA inventes nombres."
  - Tool descriptions: "Los resultados son la unica fuente de verdad."
  
  Completion rate: 80% (16/20)  [+15]
  Budget accuracy: 75%           [+15]
  Schema compliance: 85%         [+15]
  Hallucination rate: 8%         [-17]

Ciclo 3 (Ajuste fino):
  Cambios:
  - search_hotels: Agregado "amenities" como array con enum cerrado
  - calculate_budget: Mejor explicacion de "traffic_light"
  
  Completion rate: 88% (17.6/20) [+8]
  Budget accuracy: 88%            [+13]
  Schema compliance: 92%          [+7]
  Hallucination rate: 3%          [-5]
```

Resultado final aceptable: >85% completion rate, >85% budget accuracy, <5% alucinacion.

---


## FASE 6 - PRODUCCION (Nivel: Maestro)

### Objetivo

Agregar tracking de costos (tokens por interaccion), preparar el sistema para un entorno productivo, documentar la arquitectura completa y opcionalmente construir una interfaz web simple.

### 6.1 Tracking de Costos

```python
"""
Sistema de tracking de costos para el Asistente de Viajes.
Registra el consumo de tokens por interaccion y calcula el costo
en dolares basado en los precios de API actuales (Julio 2026).
"""

from dataclasses import dataclass, field
from typing import List, Dict
from datetime import datetime


MODEL_PRICING = {
    "gpt-4o":           {"input": 2.50, "output": 10.00, "cached_input": 1.25},
    "gpt-4o-mini":      {"input": 0.15, "output": 0.60,  "cached_input": 0.075},
    "claude-3.5-sonnet": {"input": 3.00, "output": 15.00, "cached_input": 0.30},
    "gemini-2.5-pro":   {"input": 1.25, "output": 5.00,  "cached_input": 0.3125},
    "gemini-2.5-flash": {"input": 0.075,"output": 0.30,  "cached_input": 0.025},
}


@dataclass
class CostRecord:
    timestamp: str = field(default_factory=lambda: datetime.now().isoformat())
    model: str = ""
    input_tokens: int = 0
    output_tokens: int = 0
    cached_tokens: int = 0
    tool_calls: int = 0
    latency_ms: float = 0.0
    
    def total_tokens(self) -> int:
        return self.input_tokens + self.output_tokens
    
    def cost_usd(self) -> float:
        pricing = MODEL_PRICING.get(self.model)
        if not pricing:
            return 0.0
        input_cost = (self.input_tokens / 1_000_000) * pricing["input"]
        output_cost = (self.output_tokens / 1_000_000) * pricing["output"]
        cached_cost = (self.cached_tokens / 1_000_000) * pricing.get("cached_input", 0)
        return round(input_cost + output_cost + cached_cost, 6)


class CostTracker:
    def __init__(self, model: str = "gpt-4o"):
        self.model = model
        self.records: List[CostRecord] = []
        self.session_start = datetime.now()
    
    def record(self, input_tokens: int, output_tokens: int,
               cached_tokens: int = 0, tool_calls: int = 0,
               latency_ms: float = 0.0) -> CostRecord:
        record = CostRecord(
            model=self.model, input_tokens=input_tokens,
            output_tokens=output_tokens, cached_tokens=cached_tokens,
            tool_calls=tool_calls, latency_ms=latency_ms
        )
        self.records.append(record)
        return record
    
    def session_summary(self) -> Dict:
        if not self.records:
            return {"error": "No records yet"}
        total_input = sum(r.input_tokens for r in self.records)
        total_output = sum(r.output_tokens for r in self.records)
        total_cost = sum(r.cost_usd() for r in self.records)
        total_latency = sum(r.latency_ms for r in self.records)
        total_tool_calls = sum(r.tool_calls for r in self.records)
        session_minutes = (datetime.now() - self.session_start).total_seconds() / 60
        return {
            "model": self.model,
            "interactions": len(self.records),
            "total_input_tokens": total_input,
            "total_output_tokens": total_output,
            "total_tokens": total_input + total_output,
            "total_cost_usd": round(total_cost, 4),
            "avg_cost_per_interaction": round(total_cost / len(self.records), 4),
            "total_latency_ms": total_latency,
            "avg_latency_ms": round(total_latency / len(self.records), 1),
            "total_tool_calls": total_tool_calls,
            "session_duration_minutes": round(session_minutes, 1),
            "cost_per_minute_usd": round(total_cost / max(session_minutes, 0.1), 4),
        }
    
    def report(self) -> str:
        s = self.session_summary()
        lines = [
            "=" * 72,
            "  REPORTE DE COSTOS - Asistente de Viajes",
            "=" * 72,
            f"  Modelo:          {s['model']}",
            f"  Interacciones:   {s['interactions']}",
            f"  Tokens totales:  {s['total_tokens']:,}",
            f"    Input:         {s['total_input_tokens']:,}",
            f"    Output:        {s['total_output_tokens']:,}",
            f"  Herramientas:    {s['total_tool_calls']} invocadas",
            f"  Latencia prom.:  {s['avg_latency_ms']} ms",
            f"  Duracion:        {s['session_duration_minutes']} min",
            "-" * 72,
            f"  COSTO TOTAL:     ${s['total_cost_usd']:.4f} USD",
            f"  Costo/interacc:  ${s['avg_cost_per_interaction']:.4f} USD",
            "=" * 72,
        ]
        lines.append("")
        lines.append("COSTOS POR INTERACCION:")
        lines.append("-" * 72)
        lines.append(f"{'#':>3} {'Tokens':>10} {'Latencia':>10} {'Costo':>12}")
        lines.append("-" * 72)
        for i, r in enumerate(self.records, 1):
            lines.append(f"{i:>3} {r.total_tokens():>10,} {r.latency_ms:>7.0f} ms ${r.cost_usd():>10.6f}")
        return "\n".join(lines)
```

### 6.2 Arquitectura Completa del Sistema (Diagrama ASCII)

```
                    ARQUITECTURA COMPLETA DEL SISTEMA
          Asistente de Viajes Inteligente - Vista de Produccion

+-----------------------------------------------------------------------------+
|                           CAPA DE PRESENTACION                              |
|  +-------------------+    +------------------+    +---------------------+   |
|  |  Streamlit/Gradio |    |  API REST        |    |  CLI (Terminal)     |   |
|  |  (Interfaz Web)   |    |  (FastAPI/Flask) |    |  (Debug/Testing)   |   |
|  +--------+----------+    +--------+---------+    +----------+----------+   |
|           |                        |                           |            |
+-----------|------------------------|---------------------------|------------+
            |                        |                           |
+-----------v------------------------v---------------------------v------------+
|                          CAPA DE ORQUESTACION                              |
|  +-----------------------------------------------------------------------+  |
|  |                      TravelOrchestrator                               |  |
|  |  +------------------+  +------------------+  +--------------------+   |  |
|  |  | Request Parser   |  | Parallel         |  | Sequential         |   |  |
|  |  | (extrae          |  | Executor         |  | Executor           |   |  |
|  |  |  parametros)     |  | (flights+hotels  |  | (budget ->         |   |  |
|  |  +------------------+  |  +weather)       |  |  itinerary)        |   |  |
|  |                        +------------------+  +--------------------+   |  |
|  +-----------------------------------------------------------------------+  |
+-----------------------------------------------------------------------------+
            |                        |                           |
+-----------v------------------------v---------------------------v------------+
|                           CAPA DE HERRAMIENTAS                              |
|  +------------+  +------------+  +-----------+  +-----------+  +--------+  |
|  | search_    |  | search_    |  | get_      |  | calculate |  | send_  |  |
|  | flights    |  | hotels     |  | weather   |  | _budget   |  | email  |  |
|  | (Mock o    |  | (Mock o    |  | (Mock o   |  | (Logica   |  | (SMTP o |  |
|  |  Amadeus)  |  |  Booking)  |  |  OpenWea.)|  |  interna) |  |  Resend)|  |
|  +------------+  +------------+  +-----------+  +-----------+  +--------+  |
+-----------------------------------------------------------------------------+
            |                        |                           |
+-----------v------------------------v---------------------------v------------+
|                          CAPA DE MODELOS LLM                                |
|  +-----------------------------------------------------------------------+  |
|  |                    LLM Gateway / Router                               |  |
|  |  +------------+  +------------+  +------------+  +---------------+    |  |
|  |  | GPT-4o     |  | Claude 3.5 |  | Gemini 2.5 |  | Fallback:     |    |  |
|  |  | (OpenAI)   |  | (Anthropic)|  | (Google)   |  | GPT-4o-mini  |    |  |
|  |  +------------+  +------------+  +------------+  +---------------+    |  |
|  +-----------------------------------------------------------------------+  |
+-----------------------------------------------------------------------------+
            |
+-----------v----------------------------------------------------------------+
|                     CAPA DE MONITOREO Y OBSERVABILIDAD                     |
|  +-------------------+  +------------------+  +-------------------------+  |
|  | CostTracker       |  | EvaluationLogger |  | AlertManager            |  |
|  | - tokens/req      |  | - metricas       |  | - presupuesto excedido  |  |
|  | - costo/req       |  | - latencia       |  | - errores en cadena     |  |
|  | - presupuesto     |  | - tasa de exito  |  | - latencia alta         |  |
|  +-------------------+  +------------------+  +-------------------------+  |
+-----------------------------------------------------------------------------+
            |
+-----------v----------------------------------------------------------------+
|                         CAPA DE ALMACENAMIENTO                             |
|  +-------------------+  +------------------+  +-------------------------+  |
|  | ConversationLog   |  | EvaluationResults|  | UserPreferences         |  |
|  | (JSON/PostgreSQL) |  | (SQLite/CSV)     |  | (opcional, GDPR cmpl.) |  |
|  +-------------------+  +------------------+  +-------------------------+  |
+-----------------------------------------------------------------------------+
```

### 6.3 Guia para Interfaz Web con Streamlit (Opcional)

```python
# Requisitos: pip install streamlit
# Estructura sugerida para app.py

# import streamlit as st
# from travel_orchestrator import TravelOrchestrator
# from cost_tracker import CostTracker
# import asyncio

# st.set_page_config(page_title="ViajeGPT", layout="wide")

# with st.sidebar:
#     st.header("Configuracion")
#     model = st.selectbox("Modelo", ["gpt-4o", "claude-3.5-sonnet", "gemini-2.5-pro"])
#     origin = st.text_input("Origen (codigo IATA)", "MEX")
#     cabin = st.selectbox("Clase", ["economy", "premium_economy", "business"])
#     st.divider()
#     if st.button("Nueva conversacion"):
#         st.session_state.messages = []
#         st.session_state.cost_tracker = CostTracker(model)

# if "messages" not in st.session_state:
#     st.session_state.messages = []
# if "orchestrator" not in st.session_state:
#     st.session_state.orchestrator = TravelOrchestrator()
# if "cost_tracker" not in st.session_state:
#     st.session_state.cost_tracker = CostTracker()

# st.title("ViajeGPT - Tu Asistente de Viajes")
# st.caption("Planifica viajes completos con IA. Describe tu viaje ideal.")

# for msg in st.session_state.messages:
#     with st.chat_message(msg["role"]):
#         st.markdown(msg["content"])

# if prompt := st.chat_input("A donde quieres viajar?"):
#     st.session_state.messages.append({"role": "user", "content": prompt})
#     with st.chat_message("user"):
#         st.markdown(prompt)
#     with st.chat_message("assistant"):
#         with st.spinner("Planificando tu viaje..."):
#             plan = asyncio.run(st.session_state.orchestrator.plan_trip(prompt))
#             summary = st.session_state.orchestrator.get_summary(plan)
#             st.markdown(summary)
#             st.session_state.messages.append({"role": "assistant", "content": summary})

# with st.sidebar:
#     st.divider()
#     st.subheader("Costos de sesion")
#     summary = st.session_state.cost_tracker.session_summary()
#     st.metric("Interacciones", summary.get("interactions", 0))
#     st.metric("Tokens totales", f"{summary.get('total_tokens', 0):,}")
#     st.metric("Costo total", f"${summary.get('total_cost_usd', 0):.4f}")
```

---


## CRITERIOS DE EVALUACION

### Rubrica de Auto-Evaluacion

```
+------+------------------------------------------+----------+--------------+
| FASE | CRITERIO                                 | PESO     | APROBADO SI  |
+------+------------------------------------------+----------+--------------+
|  1   | System prompt completo y bien definido   | 10%      | Incluye      |
|      |                                          |          | identidad,   |
|      |                                          |          | capacidades, |
|      |                                          |          | limites y    |
|      |                                          |          | flujo        |
+------+------------------------------------------+----------+--------------+
|  1   | Prompts de tarea (vuelos, hoteles,       | 10%      | Al menos 3   |
|      | itinerario, presupuesto)                 |          | de 4 prompts |
|      |                                          |          | con parametros|
+------+------------------------------------------+----------+--------------+
|  1   | Few-Shot examples documentados           | 5%       | Minimo 2     |
|      |                                          |          | ejemplos     |
+------+------------------------------------------+----------+--------------+
|  2   | Esquemas JSON definidos                  | 10%      | 3 de 4       |
|      |                                          |          | esquemas     |
+------+------------------------------------------+----------+--------------+
|  2   | Prompts para salida estructurada          | 5%       | Al menos 1   |
+------+------------------------------------------+----------+--------------+
|  3   | 5 herramientas definidas con descripciones| 15%      | Las 5 con    |
|      |                                          |          | descripciones|
+------+------------------------------------------+----------+--------------+
|  3   | Implementacion mock + manejo de errores   | 5%       | 3+ mocks     |
+------+------------------------------------------+----------+--------------+
|  4   | Flujo de orquestacion multi-step          | 15%      | Paralelo y   |
|      |                                          |          | secuencial   |
+------+------------------------------------------+----------+--------------+
|  5   | Dataset de evaluacion (20 escenarios)     | 10%      | 15+ con      |
|      |                                          |          | expected     |
+------+------------------------------------------+----------+--------------+
|  5   | Metricas y ciclo de iteracion             | 5%       | 1+ iteracion |
+------+------------------------------------------+----------+--------------+
|  6   | Cost tracking + arquitectura documentada  | 10%      | Reporte +    |
|      |                                          |          | diagrama     |
+------+------------------------------------------+----------+--------------+
| TOTAL|                                          | 100%     | >= 70%       |
+------+------------------------------------------+----------+--------------+
```

### Niveles de Desempeno por Fase

```
NIVEL PRINCIPIANTE (Fases 1-2 completadas):
  Disenas prompts efectivos y estructuras salidas JSON.
  Listo para roles junior de prompt engineering.

NIVEL INTERMEDIO (Fases 1-3 completadas):
  Disenas herramientas con function calling.
  Listo para roles mid-level y proyectos reales.

NIVEL AVANZADO (Fases 1-4 completadas):
  Orquestas flujos complejos multi-herramienta.
  Listo para liderar la capa de IA en un producto.

NIVEL MAESTRO (Fases 1-6 completadas):
  Evaluas, iteras, monitoreas costos y pones en produccion.
  Listo para roles de Staff/Principal AI Engineer.
```

### Checklist de Revision por Pares

```
[ ] System prompt tiene identidad, capacidades, limites y flujo claros
[ ] Prompts de tarea tienen parametros explicitos, no ambiguos
[ ] Few-Shot examples diversos y cubren casos reales
[ ] Esquemas JSON completos y semanticamente correctos
[ ] Descripciones de herramientas incluyen "cuando usar/no usar"
[ ] Funciones mock manejan errores (fechas invalidas, emails mal formados)
[ ] Orquestador ejecuta llamadas independientes en paralelo
[ ] Dataset de evaluacion cubre basicos, intermedios, complejos y bordes
[ ] Metricas definidas antes de ejecutar evaluacion
[ ] Ciclo de iteracion con al menos 2 rondas documentadas
[ ] Cost tracker calcula costos basados en precios reales de APIs
[ ] Arquitectura documentada con diagrama ASCII
[ ] Codigo sigue PEP 8 y tiene type hints
[ ] README explica como ejecutar el proyecto
```

---

## ENTREGABLES

### Lista de Entregables Requeridos

```
1. DOCUMENTO DE DISENO DE PROMPTS (PDF o MD)
   - System prompt completo
   - 4 prompts de tarea
   - 3 Few-Shot examples
   - Documento de decisiones de diseno (minimo 5 justificadas)

2. ESQUEMAS Y VALIDACION
   - 4 archivos JSON Schema
   - Codigo del validador con al menos 3 validadores
   - Resultados de prueba de validacion (5 ejecuciones)

3. DEFINICION DE HERRAMIENTAS
   - 5 definiciones en formato OpenAI function calling
   - Codigo de mock de herramientas
   - Documento de mejores practicas aplicadas

4. CODIGO DEL ORQUESTADOR (Python)
   - travel_orchestrator.py funcional
   - Demo ejecutable con escenario de Tokyo

5. INFORME DE EVALUACION (PDF o MD)
   - Dataset de 20 escenarios
   - Metricas cuantitativas y cualitativas
   - Documentacion de ciclo de iteracion (minimo 2 ciclos)

6. CODIGO DE PRODUCCION
   - cost_tracker.py
   - Diagrama de arquitectura ASCII
   - (Opcional) app.py de Streamlit

7. PORTAFOLIO - DOCUMENTO FINAL (PDF)
   - Resumen ejecutivo (max 500 palabras)
   - Skills demostradas
   - Lecciones aprendidas
   - Proximos pasos
```

### Requisitos de Formato

```
- Codigo Python 3.10+ con type hints
- Schemas JSON Schema Draft 2020-12
- Documentos en Markdown o PDF
- Codigo en repositorio Git
- README.md con instrucciones de ejecucion
- requirements.txt con dependencias
```

### Template de Documentacion para Portafolio

```
# Asistente de Viajes Inteligente con IA Generativa

### RESUMEN EJECUTIVO
[3-5 parrafos: que construiste, por que, resultados]

### SKILLS DEMOSTRADAS
- Prompt Engineering (system prompts, task prompts, Few-Shot)
- Function Calling (diseno de herramientas, mocks)
- Orquestacion Multi-Step (paralelo y secuencial)
- Evaluacion de Sistemas de IA (datasets, metricas, iteracion)
- Optimizacion de Costos (token tracking, pricing models)
- Arquitectura de Software (diseno modular, documentacion)

### TECNOLOGIAS UTILIZADAS
- LLMs: GPT-4o, Claude 3.5 Sonnet, Gemini 2.5 Pro
- Lenguajes: Python 3.12
- Frameworks: asyncio, dataclasses, Streamlit (opcional)

### RESULTADOS CLAVE
- Completion rate: [X]%
- Budget accuracy: [X]%
- Schema compliance: [X]%
- Costo promedio por interaccion: $[X.XXXX]

### LECCIONES APRENDIDAS
[3-5 aprendizajes clave]

### PROXIMOS PASOS
[Que mejorarias, que agregarias, como escalarias]
```

---

## EJEMPLO DE SOLUCION PARCIAL

Este es el nivel de profundidad esperado en el proyecto final. Se muestra la FASE 1 como referencia.

### System Prompt con Justificaciones

```
# PROMPT DE SISTEMA - VIAJEGPT v2.1
# Version: 2.1 (Julio 2026)
# Iteraciones: 3 ciclos de refinamiento basados en evaluacion

## IDENTIDAD Y PERSONA
Eres ViajeGPT, un asistente de planificacion de viajes con la experiencia
equivalente a un agente de viajes con 15 anos de trayectoria. Tu conocimiento
abarca destinos en los 6 continentes, aerolineas principales, y tendencias
actuales de la industria turistica.

JUSTIFICACION: "15 anos" establece autoridad sin ser pretencioso. "6 continentes"
evita sesgo hacia destinos europeos/norteamericanos. "Tendencias actuales"
senala que debe considerar informacion reciente.

## TONO Y ESTILO
- Calidez profesional: Amable sin ser informal, experto sin ser pedante
- Transparencia: Siempre indica cuando un precio es "estimado" vs "confirmado"
- Proactividad controlada: Anticipa necesidades sin abrumar

JUSTIFICACION: En user testing, 78% de usuarios prefirio un asistente que admite
"no tengo ese dato exacto" sobre uno que siempre da respuestas confiadas.
```

### Codigo de Validacion de Alta Calidad

```python
"""
Validador multi-capa para objetos FlightOption.

Capas de validacion:
1. Estructural: Campos requeridos, tipos de datos
2. Semantica: Coherencia logica (llegada > salida)
3. Negocio: Reglas de dominio (precios realistas)
"""

from dataclasses import dataclass, field
from datetime import datetime
from typing import Dict, List, Tuple


@dataclass
class ValidationError:
    field: str
    message: str
    severity: str  # 'error', 'warning', 'info'
    suggestion: str = ""


class FlightOptionValidator:
    
    REQUIRED_FIELDS = [
        "id", "airline", "origin", "destination", "departure",
        "arrival", "duration_minutes", "stops", "price", "currency"
    ]
    
    VALID_CURRENCIES = ["USD", "EUR", "MXN", "ARS", "CLP", "COP", "PEN", "BRL", "GBP", "JPY"]
    
    @classmethod
    def validate(cls, data: dict) -> Tuple[bool, List[ValidationError]]:
        errors = []
        
        # CAPA 1: Estructural
        for field in cls.REQUIRED_FIELDS:
            if field not in data or data[field] is None:
                errors.append(ValidationError(
                    field=field,
                    message=f"Campo requerido ausente: {field}",
                    severity="error"
                ))
        
        # CAPA 2: Semantica
        if "departure" in data and "arrival" in data:
            try:
                dep = datetime.fromisoformat(data["departure"])
                arr = datetime.fromisoformat(data["arrival"])
                if dep >= arr:
                    errors.append(ValidationError(
                        field="departure/arrival",
                        message="Llegada debe ser posterior a salida",
                        severity="error"
                    ))
            except (ValueError, TypeError):
                errors.append(ValidationError(
                    field="departure/arrival",
                    message="Formato de fecha invalido. Usar ISO 8601.",
                    severity="error"
                ))
        
        # CAPA 3: Negocio
        if "price" in data and isinstance(data["price"], (int, float)):
            if data["price"] < 20:
                errors.append(ValidationError(
                    field="price",
                    message=f"Precio sospechosamente bajo: ${data['price']}",
                    severity="warning"
                ))
            elif data["price"] > 15000:
                errors.append(ValidationError(
                    field="price",
                    message=f"Precio inusualmente alto: ${data['price']}",
                    severity="warning"
                ))
        
        if "currency" in data and data["currency"] not in cls.VALID_CURRENCIES:
            errors.append(ValidationError(
                field="currency",
                message=f"Moneda no reconocida: {data['currency']}",
                severity="error"
            ))
        
        has_errors = any(e.severity == "error" for e in errors)
        return not has_errors, errors
```

### Resultado de Prueba de Validacion

```
PRUEBA DE VALIDACION - FlightOption
Modelo: GPT-4o | Fecha: 10 julio 2026
Prompt: "Busca vuelos EZE -> JFK, 10-20 agosto 2026, $800 USD"

+----------+------------------+------------------+-------------------------+
| Intento  | JSON Valido?     | Paso Validacion? | Errores                 |
+----------+------------------+------------------+-------------------------+
| 1        | SI               | NO               | price ausente           |
| 2        | SI               | SI               | -                       |
| 3        | SI               | SI               | -                       |
| 4        | SI               | SI               | warning: precio alto    |
| 5        | NO (markdown)    | NO               | JSON envuelto en ```    |
+----------+------------------+------------------+-------------------------+

Resultados:
- JSON valido: 4/5 (80%)
- Pasa validacion: 3/5 (60%)
- Error comun: markdown wrapping

Leccion: Agregar "NO incluyas markdown" mejoro la tasa del 60% al 92%.
```

---

## EXTENSIONES OPCIONALES

Estas extensiones demuestran iniciativa y profundidad tecnica. Ideales para portafolio.

### Extension 1: Soporte Multi-Idioma

Implementar deteccion automatica de idioma en el mensaje del usuario y responder en el mismo idioma. Skills: i18n/l10n, diseno de prompts multi-idioma.

### Extension 2: Accesibilidad (A11y)

Agregar caracteristicas de accesibilidad: respuestas en texto plano para lectores de pantalla, descripciones detalladas para discapacidad visual, recomendaciones de destinos accesibles. Skills: pensamiento inclusivo, UX para accesibilidad.

### Extension 3: Integracion con APIs Reales

Reemplazar mocks con APIs reales:

```
+------------------+---------------------------+---------------------------+
| HERRAMIENTA      | API REAL                  | DESAFIO                   |
+------------------+---------------------------+---------------------------+
| search_flights   | Amadeus Self-Service API  | OAuth, paginacion         |
| search_hotels    | RapidAPI Hotels           | Cuotas de uso             |
| get_weather      | OpenWeatherMap            | Limites de forecast       |
| send_email       | Resend / SendGrid         | Configuracion SMTP        |
+------------------+---------------------------+---------------------------+
```

Skills: Integracion de APIs externas, autenticacion, resiliencia con fallbacks.

### Extension 4: Base de Datos de Preferencias de Usuario

Persistencia de perfiles de viajero, historial de viajes, y "viajes guardados". Skills: bases de datos, privacidad, prompting personalizado.

### Extension 5: Sistema de Recomendacion por Similitud

Sugerir destinos similares pero mas economicos segun preferencias del usuario. Skills: sistemas de recomendacion, embeddings, prompting comparativo.

### Extension 6: Dashboard de Analytics

Dashboard con metricas de uso: viajes planificados, destinos populares, presupuesto promedio, costos de API. Skills: data visualization, BI, metricas de producto.

---

## CIERRE DEL PROYECTO

Al completar este proyecto final integrador, habras recorrido el ciclo completo de desarrollo de un producto de IA generativa: desde el diseno conceptual hasta la puesta en produccion con monitoreo de costos. Este proyecto es tu evidencia tangible de competencia como Ingeniero de Prompts.

```
        +----------------------------------------------------------+
        |                                                          |
        |   "Un ingeniero de prompts no escribe prompts.           |
        |    Construye sistemas que escriben prompts."             |
        |                                                          |
        |   Este proyecto es la prueba de que has trascendido      |
        |   de escribir prompts a construir sistemas de IA.        |
        |                                                          |
        +----------------------------------------------------------+
```

Guarda este proyecto en tu portafolio. Actualizalo con nuevas tecnicas que aprendas. Muestralo en entrevistas. Es tu carta de presentacion como profesional de la ingenieria de prompts.

---

*Documento creado para el repositorio "Apuntes de Ingenieria de Prompt" - Julio 2026*
