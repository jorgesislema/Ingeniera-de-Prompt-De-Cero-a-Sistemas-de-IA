# Respuestas - Casos de Estudio y Resolución de Problemas

## Casos de Estudio Nivel Básico - Respuestas

### 1. E-commerce: Clasificación de Productos

**Solución sencilla:**

Para clasificar 10,000 productos diarios en 50+ categorías, diseñaría un prompt efectivo con esta estructura:

```
Actúa como un especialista en categorización de productos con años de experiencia en e-commerce.

TAREA: Clasifica el siguiente producto en UNA SOLA de las categorías disponibles basándote en su descripción.

DESCRIPCIÓN DEL PRODUCTO:
[Descripción del producto]

CATEGORÍAS DISPONIBLES:
[Lista de 50+ categorías principales]

INSTRUCCIONES:
1. Analiza cuidadosamente la descripción del producto
2. Identifica características clave como: material, uso, tipo de producto, público objetivo
3. Selecciona SOLO UNA categoría principal que mejor represente el producto
4. Si el producto podría pertenecer a múltiples categorías, selecciona la más específica
5. Si no hay suficiente información, clasifica como "Requiere revisión manual"

FORMATO DE RESPUESTA:
Categoría: [Nombre exacto de la categoría]
Confianza: [Alta/Media/Baja]
Justificación: [Breve explicación de 1-2 líneas]
```

**¿Por qué funciona bien?**
1. **Estructura clara**: Define exactamente lo que necesitamos (una categoría)
2. **Instrucciones paso a paso**: Guía el proceso de análisis
3. **Manejo de casos difíciles**: Tiene categoría especial para casos dudosos
4. **Escala fácilmente**: Puede procesar muchos productos
5. **Explica decisiones**: La justificación ayuda a mejorar el sistema

**Para mejorar con el tiempo:**
- Añadir ejemplos de productos bien clasificados (few-shot learning)
- Actualizar la lista de categorías periódicamente
- Usar feedback de clasificaciones manuales para refinar el prompt

---

### 2. Atención al Cliente: Análisis de Sentimiento

**Solución sencilla:**

Para clasificar tickets de atención al cliente en niveles de urgencia (URGENTE, NORMAL, BAJO), usaría este prompt:

```
Actúa como un experto en servicio al cliente con 10 años de experiencia en priorización de tickets.

TAREA: Analiza el siguiente ticket de cliente y clasifícalo como URGENTE, NORMAL o BAJO.

TICKET DEL CLIENTE:
[Texto completo del ticket]

CRITERIOS DE CLASIFICACIÓN:
- URGENTE: Cliente expresa frustración extrema, menciona cancelar servicio, reporta pérdidas financieras, problema de seguridad, o sistema completamente caído
- NORMAL: Problemas que afectan el uso del servicio pero tienen solución temporal, dudas importantes o problemas recurrentes
- BAJO: Preguntas generales, sugerencias, pequeños inconvenientes, o solicitudes de información

INSTRUCCIONES:
1. Lee cuidadosamente todo el texto del ticket
2. Identifica palabras clave que indiquen urgencia o tono emocional
3. Reconoce ironía o sarcasmo (frases como "excelente servicio" en contexto negativo)
4. Evalúa el impacto real del problema reportado
5. Determina si el cliente está realmente afectado o simplemente insatisfecho

FORMATO DE RESPUESTA:
Clasificación: [URGENTE/NORMAL/BAJO]
Indicadores de urgencia: [Lista de frases o palabras clave que justifican la clasificación]
Explicación: [Breve justificación de 2-3 líneas]
Recomendación: [Sugerencia sobre cómo proceder]
```

**¿Por qué funciona bien?**
1. **Criterios claros**: Define exactamente qué hace que un ticket sea urgente, normal o de baja prioridad
2. **Detecta emociones ocultas**: Está programado para identificar sarcasmo e ironía
3. **Reduce falsos positivos**: No se deja engañar solo por palabras "fuertes" sino que evalúa el contexto
4. **Proporciona explicaciones**: Justifica cada clasificación para revisión humana
5. **Incluye próximos pasos**: Da recomendaciones para resolver el problema

**Consejos para mejorar:**
- Añadir ejemplos de cada categoría con explicación (few-shot)
- Actualizar periódicamente con nuevos patrones de lenguaje de los clientes
- Adaptar los criterios según el tipo de negocio (e-commerce, software, etc.)

---

### 3. Recursos Humanos: Filtrado de CVs

**Solución sencilla:**

Para filtrar 500 CVs semanales de forma automática, usaría este prompt efectivo:

```
Actúa como un reclutador senior de tecnología con experiencia evaluando candidatos para roles técnicos.

TAREA: Evalúa el siguiente CV y determina si el candidato cumple los requisitos mínimos para el puesto.

DESCRIPCIÓN DEL PUESTO:
[Título]: [Puesto específico como "Desarrollador Full Stack"]
[Requisitos Obligatorios]: [Lista de 3-5 habilidades/experiencias indispensables]
[Requisitos Deseables]: [Lista de 2-3 habilidades/experiencias adicionales]

CV DEL CANDIDATO:
[Texto del CV completo]

INSTRUCCIONES:
1. Analiza el CV completo para identificar habilidades, experiencia y formación
2. Compara las habilidades encontradas con los requisitos obligatorios
3. Ignora formato, presentación y elementos subjetivos (como aficiones)
4. No tengas en cuenta género, edad, nacionalidad o nombre al evaluar
5. Busca evidencia concreta de experiencia (no solo menciones de tecnologías)

FORMATO DE RESPUESTA:
Requisitos Obligatorios Cumplidos: [Sí/No, con lista de los que cumple]
Requisitos Deseables Cumplidos: [Lista de los que cumple]
Puntuación Global: [1-5]
Fortalezas: [2-3 puntos destacables]
Áreas de Mejora: [1-2 áreas donde no cumple requisitos]
Recomendación: [Avanzar/Rechazar/Revisar manualmente]
```

**¿Por qué funciona bien?**
1. **Objetivo y estructurado**: Se enfoca solo en requisitos concretos
2. **Reduce sesgos**: Instruye ignorar factores no relevantes (género, edad)
3. **Maneja diferentes formatos**: Busca información clave independientemente del formato
4. **Da justificación clara**: Explica exactamente por qué recomienda avanzar o no
5. **Solución escalable**: Funciona para diferentes posiciones cambiando los requisitos

**Para hacerlo mejor:**
- Incluir instrucciones para reconocer experiencia equivalente
- Actualizar periódicamente con nuevos términos técnicos del sector
- Añadir categorización por nivel de experiencia (junior/mid/senior)

---

### 4. Marketing: Generación de Copy

**Solución sencilla:**

Para generar 10 versiones diferentes de un anuncio manteniendo coherencia de marca:

```
Actúa como un copywriter senior con 8 años de experiencia en marketing digital y A/B testing.

TAREA: Genera 10 variaciones del anuncio base manteniendo el mensaje principal pero con diferentes enfoques.

ANUNCIO BASE:
[Texto del anuncio original]

GUÍA DE MARCA:
Tono: [Formal/Casual/Inspirador/etc.]
Palabras clave obligatorias: [Lista de 3-5 términos que deben aparecer]
Mensaje principal: [Beneficio principal del producto/servicio]
Call-to-action preferido: [Frases como "Compra ahora", "Regístrate", etc.]
Elementos a evitar: [Términos o enfoques no alineados con marca]

INSTRUCCIONES:
1. Crea 10 variaciones manteniendo siempre el beneficio principal
2. Cada variación debe tener entre [X-Y] caracteres
3. Varía: longitud de frases, estructura, llamada a la acción, tono emocional
4. Mantén constantes: palabras clave, mensaje core, personalidad de marca
5. Incluye al menos una variación de: pregunta, estadística, testimonio, problema-solución

FORMATO DE RESPUESTA:
Variación 1: [Texto completo]
Enfoque: [Tipo de enfoque usado, ej. "Pregunta directa"]

Variación 2: [Texto completo]
Enfoque: [Tipo de enfoque usado, ej. "Estadística impactante"]

[Y así sucesivamente hasta 10 variaciones]
```

**¿Por qué funciona bien?**
1. **Balanceo perfecto**: Permite creatividad pero dentro de límites claros
2. **Diversidad controlada**: Asegura variaciones realmente diferentes pero coherentes
3. **Respeta guías de marca**: Mantiene elementos esenciales de identidad
4. **Estructura para testing**: Cada variación tiene un enfoque claro para analizar resultados
5. **Fácil implementación**: Formato listo para copiar y usar en campañas

**Para mejorarlo:**
- Incluir ejemplos de anuncios exitosos anteriores como referencia
- Especificar audiencia objetivo para cada variación
- Agregar parámetros para plataformas específicas (Facebook, Google, etc.)

---

### 5. Educación: Evaluación Automática

**Solución sencilla:**

Para evaluar respuestas abiertas de ensayo de manera justa y consistente:

```
Actúa como un profesor experimentado con especialización en evaluación educativa.

TAREA: Evalúa la siguiente respuesta de ensayo según los criterios establecidos.

PREGUNTA DEL ENSAYO:
[Texto de la pregunta o tema propuesto]

RESPUESTA DEL ESTUDIANTE:
[Texto completo de la respuesta del estudiante]

RÚBRICA DE EVALUACIÓN:
Comprensión del tema (0-5 puntos): Entendimiento de conceptos clave
Calidad de argumentación (0-5 puntos): Lógica y coherencia de argumentos
Evidencia y ejemplos (0-5 puntos): Uso de datos relevantes para apoyar ideas
Estructura y claridad (0-3 puntos): Organización del texto y fluidez
Gramática y ortografía (0-2 puntos): Corrección lingüística

INSTRUCCIONES:
1. Lee completamente tanto la pregunta como la respuesta
2. Evalúa cada criterio de forma independiente según la rúbrica
3. Busca coincidencias textuales con fuentes comunes para detectar posible plagio
4. Proporciona feedback específico con ejemplos concretos de la respuesta
5. Sé objetivo y consistente, evaluando solo el contenido (no el estilo personal)

FORMATO DE RESPUESTA:
Puntuación Total: [X/20 puntos]

Desglose:
- Comprensión: [X/5] - [Breve justificación]
- Argumentación: [X/5] - [Breve justificación]
- Evidencia: [X/5] - [Breve justificación]
- Estructura: [X/3] - [Breve justificación]
- Gramática: [X/2] - [Breve justificación]

Feedback constructivo:
[3-4 comentarios específicos con ejemplos concretos de la respuesta]

Áreas de mejora:
[2-3 sugerencias específicas y accionables]

Alerta de plagio: [Sí/No] - [Si es sí, indicar secciones sospechosas]
```

**¿Por qué funciona bien?**
1. **Evaluación estructurada**: Usa una rúbrica clara con criterios específicos
2. **Objetividad**: Evalúa aspectos concretos, no impresiones generales
3. **Feedback accionable**: Ofrece comentarios específicos que ayudan a mejorar
4. **Detección de plagio**: Incluye verificación básica de originalidad
5. **Consistencia**: Mismo criterio para todos los estudiantes

**Para mejorarlo:**
- Agregar ejemplos de respuestas de diferentes niveles (excelente, buena, suficiente)
- Personalizar feedback según nivel educativo
- Incluir referencias a recursos adicionales relacionados con áreas débiles

---

### 6. Finanzas: Análisis de Reportes

**Solución sencilla:**

Para analizar reportes financieros corporativos y detectar riesgos automáticamente:

```
Actúa como un analista financiero senior con experiencia en evaluación de riesgo crediticio corporativo.

TAREA: Analiza el siguiente reporte financiero, extrae KPIs clave e identifica posibles riesgos.

REPORTE FINANCIERO:
[Texto/tablas del reporte financiero]

PERÍODO DE ANÁLISIS:
Año fiscal: [AAAA]
Comparativo con: [AAAA anterior]

INSTRUCCIONES:
1. Extrae y calcula los siguientes KPIs financieros:
   - Ratio de liquidez: Activo corriente / Pasivo corriente
   - Ratio de endeudamiento: Pasivo total / Patrimonio neto
   - Margen de beneficio: Beneficio neto / Ventas
   - ROA: Beneficio neto / Activos totales
   - ROE: Beneficio neto / Patrimonio neto

2. Identifica tendencias comparando con período anterior:
   - Variación en ventas (%)
   - Variación en beneficio neto (%)
   - Cambios significativos en estructura de costos

3. Detecta posibles señales de alerta ("red flags"):
   - Deterioro significativo de ratios respecto al año anterior
   - Inconsistencias entre beneficio y flujo de caja
   - Crecimiento de ingresos sin crecimiento proporcional en flujos
   - Aumento significativo de cuentas por cobrar
   - Cambios inusuales en inventarios

FORMATO DE RESPUESTA:
### ANÁLISIS FINANCIERO - [Nombre de la empresa]

#### 1. KPIs FINANCIEROS
| Indicador | Valor actual | Valor año anterior | Variación | Evaluación |
|-----------|--------------|-------------------|-----------|------------|
| Liquidez  | [valor]      | [valor]           | [%]       | [Bueno/Regular/Preocupante] |
| [Continuar con todos los KPIs...]

#### 2. PRINCIPALES TENDENCIAS
- [Tendencia 1 identificada con datos concretos]
- [Tendencia 2 identificada with datos concretos]
- [Tendencia 3 identificada con datos concretos]

#### 3. SEÑALES DE ALERTA
- [Red flag 1 con datos específicos y explicación]
- [Red flag 2 con datos específicos y explicación]
- [Si no hay red flags, indicar "No se detectaron señales de alerta significativas"]

#### 4. EVALUACIÓN DE RIESGO
Nivel de riesgo general: [Bajo/Medio/Alto]
Justificación: [2-3 líneas explicando la evaluación]

#### 5. RECOMENDACIONES
- [1-3 recomendaciones específicas para análisis adicional o acción]
```

**¿Por qué funciona bien?**
1. **Estructura clara y completa**: Organiza el análisis en secciones lógicas
2. **Automatiza cálculos complejos**: Extrae y calcula ratios financieros clave
3. **Detección sistemática**: Busca patrones específicos que indican riesgo
4. **Análisis comparativo**: No solo ve datos actuales sino tendencias
5. **Formato profesional**: Presenta resultados de forma clara y accionable

**Para mejorarlo:**
- Agregar análisis por sector/industria para comparar con estándares del mercado
- Incluir detección de posibles manipulaciones contables
- Adaptar indicadores según tipo de negocio (manufacturero, servicios, etc.)

---

### 7. Salud: Triaje de Síntomas

**Solución sencilla:**

Para un sistema seguro de triaje médico por telemedicina:

```
Actúa como un enfermero de triaje con 15+ años de experiencia en urgencias y telemedicina.

TAREA: Evalúa los síntomas descritos para determinar el nivel de prioridad sin realizar diagnósticos.

INFORMACIÓN DEL PACIENTE:
Edad: [X] años
Género: [M/F/Otro]
Síntomas principales: [Descripción proporcionada por el paciente]
Duración: [Tiempo desde inicio de síntomas]
Condiciones médicas previas: [Lista si están disponibles]

INSTRUCCIONES:
1. Evalúa SOLO el nivel de urgencia, NO proporciones diagnósticos
2. Clasifica en: EMERGENCIA (atención inmediata), URGENTE (24h), NORMAL (72h), RUTINA (1-2 semanas)
3. Si hay "banderas rojas" (síntomas potencialmente graves), destácalas claramente
4. Incluye siempre un descargo de responsabilidad médico
5. Para síntomas ambiguos, prioriza siempre la seguridad del paciente
6. Recomienda buscar atención de emergencia ante signos de alarma

SIGNOS DE ALARMA ABSOLUTOS (siempre clasificar como EMERGENCIA):
- Dificultad respiratoria severa
- Dolor torácico intenso
- Pérdida de conciencia
- Hemorragia importante
- Síntomas de ACV (cara caída, dificultad para hablar, debilidad en extremidades)
- Trauma craneal con vómitos o confusión
- Reacciones alérgicas graves con hinchazón de vías respiratorias

FORMATO DE RESPUESTA:
### EVALUACIÓN DE PRIORIDAD

**Nivel de prioridad recomendado: [EMERGENCIA/URGENTE/NORMAL/RUTINA]**

**Justificación:**
[2-3 líneas explicando factores clave para esta clasificación]

**Signos de alarma identificados:**
[Lista de síntomas preocupantes o "Ninguno identificado"]

**Recomendación:**
[Indicaciones claras sobre siguiente paso - buscar atención médica, monitorizar, etc.]

**IMPORTANTE:** Esta evaluación NO constituye un diagnóstico médico. Es solo una herramienta de triaje inicial. Si los síntomas empeoran o cambian, busque atención médica inmediatamente. Ante cualquier duda, acuda a un servicio de emergencias.
```

**¿Por qué funciona bien?**
1. **Prioriza seguridad**: Establece claramente "banderas rojas" que requieren atención inmediata
2. **Evita diagnósticos**: Se enfoca solo en nivel de urgencia, no en causas médicas
3. **Clara responsabilidad legal**: Incluye descargos de responsabilidad
4. **Estructura de decisión**: Criterios claros para cada nivel de prioridad
5. **Documentación adecuada**: Registra factores usados en la decisión

**Para mejorarlo:**
- Agregar preguntas específicas para síntomas comunes pero ambiguos
- Incluir recomendaciones sobre documentación necesaria para la consulta
- Adaptar a grupos demográficos (pediátrico, geriátrico, embarazadas)

---

### 8. Legal: Revisión de Contratos

**Solución sencilla:**

Para revisar contratos legales e identificar cláusulas problemáticas:

```
Actúa como un abogado corporativo senior con 12+ años de experiencia en revisión de contratos comerciales.

TAREA: Revisa el siguiente contrato para identificar cláusulas problemáticas, términos no estándar y riesgos legales potenciales.

CONTRATO A REVISAR:
[Texto completo del contrato]

TIPO DE CONTRATO: [Ej: Servicios, Compraventa, NDA, Laboral, etc.]

INSTRUCCIONES:
1. Analiza el contrato completo, sección por sección
2. Identifica cláusulas problemáticas, ambiguas o con riesgos legales
3. Compara con términos estándar para este tipo de contratos
4. Evalúa nivel de riesgo para cada issue (Alto/Medio/Bajo)
5. Sugiere modificaciones para cada problema identificado
6. Verifica cumplimiento con regulaciones aplicables

ELEMENTOS A EVALUAR ESPECÍFICAMENTE:
- Limitaciones de responsabilidad
- Indemnizaciones
- Términos de terminación
- Cláusulas de confidencialidad
- Jurisdicción y ley aplicable
- Derechos de propiedad intelectual
- Penalizaciones y sanciones
- Plazos y fechas límite

FORMATO DE RESPUESTA:
### REVISIÓN DE CONTRATO - [Tipo de contrato]

#### 1. RESUMEN EJECUTIVO
- Nivel de riesgo general: [Alto/Medio/Bajo]
- Número de issues identificados: [X] 
- Recomendación general: [Firmar/Renegociar/Rechazar]

#### 2. CLÁUSULAS PROBLEMÁTICAS IDENTIFICADAS

**Issue #1: [Nombre breve del problema]**
- Cláusula: [Número/Título de la cláusula]
- Texto actual: "[Cita textual del lenguaje problemático]"
- Problema: [Explicación clara del riesgo legal]
- Riesgo: [Alto/Medio/Bajo]
- Recomendación: [Sugerencia específica de modificación]

[Continuar con todos los issues encontrados...]

#### 3. TÉRMINOS FALTANTES
[Lista de cláusulas o protecciones estándar ausentes en este contrato]

#### 4. CUMPLIMIENTO REGULATORIO
[Observaciones sobre conformidad con regulaciones aplicables]

#### 5. PRÓXIMOS PASOS RECOMENDADOS
[Lista de acciones concretas a tomar]
```

**¿Por qué funciona bien?**
1. **Análisis estructurado**: Examina sistemáticamente todas las secciones clave
2. **Priorización clara**: Asigna niveles de riesgo para enfocar esfuerzos
3. **Comparación con estándares**: Identifica desviaciones de lo habitual en la industria
4. **Soluciones accionables**: No solo identifica problemas sino que propone soluciones
5. **Formato claro**: Organiza hallazgos de manera que facilita decisiones rápidas

**Para mejorarlo:**
- Personalizar elementos a evaluar según industria específica
- Añadir checklist por tipo de contrato (empleo, propiedad intelectual, etc.)
- Incluir referencias a jurisprudencia relevante en casos críticos

---

### 9. Logística: Optimización de Rutas

**Solución sencilla:**

Para optimizar rutas de entrega considerando múltiples factores:

```
Actúa como un especialista en logística con experiencia en optimización de flotas y planificación de rutas.

TAREA: Analiza los datos de entregas proporcionados y sugiere una estrategia de optimización de rutas.

DATOS DE ENTREGAS:
Número total de entregas: [1000+]
Ventanas de entrega: [Datos sobre horarios de entrega]
Ubicaciones: [Lista/Mapa de puntos de entrega]
Restricciones específicas: [Zonas de acceso limitado, horarios especiales, etc.]
Prioridades: [Entregas urgentes, clientes VIP, etc.]

RECURSOS DISPONIBLES:
Vehículos: [Número y tipos]
Conductores: [Cantidad disponible y horarios]
Depósitos/Hubs: [Ubicaciones de origen]

INSTRUCCIONES:
1. Analiza el patrón geográfico de las entregas
2. Identifica agrupaciones naturales por proximidad
3. Considera restricciones de tiempo, tráfico y acceso
4. Balancea carga de trabajo entre conductores/vehículos
5. Prioriza entregas según urgencia y valor para el negocio
6. Sugiere estrategia adaptable a cambios de última hora

FORMATO DE RESPUESTA:
### ESTRATEGIA DE OPTIMIZACIÓN DE RUTAS

#### 1. ANÁLISIS DE PATRONES GEOGRÁFICOS
- [Identificación de zonas de alta densidad de entregas]
- [Análisis de distancias y tiempos entre puntos]
- [Patrones de tráfico relevantes por franja horaria]

#### 2. AGRUPACIÓN PROPUESTA
- **Grupo A**: [Descripción de zona/sector] - [Número de entregas] - [Característica clave]
- **Grupo B**: [Descripción de zona/sector] - [Número de entregas] - [Característica clave]
- [Continuar con todos los grupos sugeridos]

#### 3. ESTRATEGIA DE PRIORIZACIÓN
- [Criterios claros para determinar qué entregas se realizan primero]
- [Manejo de excepciones (entregas urgentes, VIP, etc.)]

#### 4. ASIGNACIÓN DE RECURSOS
- [Distribución óptima de vehículos y conductores]
- [Balance de carga entre rutas]

#### 5. GESTIÓN DE RESTRICCIONES
- [Soluciones para zonas de acceso limitado]
- [Manejo de ventanas horarias específicas]
- [Adaptación a condiciones climáticas/eventos especiales]

#### 6. ESTRATEGIA DE ADAPTACIÓN EN TIEMPO REAL
- [Protocolos para reasignaciones urgentes]
- [Gestión de imprevistos (tráfico, cancelaciones, etc.)]

#### 7. KPIs PROPUESTOS PARA MONITOREO
- [Métricas clave para evaluar eficiencia]
- [Objetivos de optimización (tiempo, combustible, satisfacción)]
```

**¿Por qué funciona bien?**
1. **Enfoque sistemático**: Analiza el problema desde múltiples ángulos
2. **Balance de objetivos**: Considera eficiencia, servicio y restricciones
3. **Adaptabilidad**: Incluye estrategias para manejo de cambios en tiempo real
4. **Visión completa**: Ve tanto el panorama general como los detalles específicos
5. **Orientado a acción**: Proporciona recomendaciones concretas y aplicables

**Para mejorarlo:**
- Añadir análisis de impacto ambiental y eficiencia energética
- Incluir simulaciones de diferentes escenarios (normal, alta demanda, crisis)
- Integrar datos históricos de entregas para predicciones más precisas

---

### 10. Medios: Generación de Headlines

**Solución sencilla:**

Para generar titulares atractivos para artículos digitales:

```
Actúa como un editor senior de contenidos digitales con experiencia en optimización SEO y engagement.

TAREA: Genera 5 variantes de titular para el artículo, optimizadas para engagement y SEO.

INFORMACIÓN DEL ARTÍCULO:
Título actual: [Título original]
Tema principal: [Tema del artículo]
Palabras clave SEO: [Lista de 3-5 keywords principales]
Audiencia objetivo: [Perfil demográfico/intereses]
Tono editorial: [Formal/Informal/Provocativo/Educativo/etc.]

INSTRUCCIONES:
1. Crea titulares atractivos pero precisos (evita clickbait engañoso)
2. Incluye al menos una palabra clave SEO principal en cada titular
3. Mantén longitud óptima (50-70 caracteres)
4. Crea variantes con diferentes enfoques emocionales
5. Asegura que el contenido del titular refleje fielmente el artículo

TIPOS DE TITULARES A INCLUIR:
- Titular con números/datos
- Titular tipo pregunta
- Titular que destaque beneficio/solución
- Titular con elemento de curiosidad/intriga
- Titular directo/informativo

FORMATO DE RESPUESTA:
### PROPUESTAS DE TITULARES

**Opción 1: [Titular con números/datos]**
- Fortalezas: [Elementos positivos del titular]
- Keywords incluidas: [Palabras clave presentes]
- Enfoque emocional: [Ej: Sorpresa, curiosidad, etc.]
- Carácter count: [XX caracteres]

**Opción 2: [Titular tipo pregunta]**
- Fortalezas: [Elementos positivos del titular]
- Keywords incluidas: [Palabras clave presentes]
- Enfoque emocional: [Ej: Curiosidad, urgencia, etc.]
- Carácter count: [XX caracteres]

[Continuar con todas las opciones...]

**Recomendación general:**
[1-2 líneas sobre qué enfoque funciona mejor para este tipo de artículo y por qué]

**Consideraciones A/B testing:**
[Sugerencias sobre qué variables probar entre los titulares propuestos]
```

**¿Por qué funciona bien?**
1. **Balance SEO y engagement**: Optimiza para algoritmos sin sacrificar atractivo humano
2. **Variedad estratégica**: Diferentes enfoques emocionales y estructuras
3. **Precisión sin clickbait**: Atrae clicks pero mantiene confianza del lector
4. **Formato analítico**: Proporciona métricas y justificación para cada opción
5. **Adaptado a objetivos**: Considera audiencia y tono específicos del medio

**Para mejorarlo:**
- Añadir análisis de titulares exitosos de la competencia
- Incluir variantes optimizadas para diferentes plataformas (redes sociales vs. Google)
- Incorporar datos de CTR históricos para refinar recomendaciones

---

## Casos de Estudio Nivel Intermedio - Respuestas

### 11. Manufacturas: Control de Calidad

**Solución sencilla:**

Para análisis predictivo de mantenimiento en líneas de producción:

```
Actúa como un ingeniero de mantenimiento predictivo con 10+ años de experiencia en manufactura automatizada.

TAREA: Analiza los datos de sensores proporcionados para predecir posibles fallas y recomendar acciones de mantenimiento.

DATOS DE ENTRADA:
Máquina ID: [Identificador único]
Tipo de máquina: [Torno, Prensa, Soldadora, etc.]
Datos de sensores: [Temperatura, vibración, presión, corriente, etc.]
Período de análisis: [Últimas 24h/7días/30días]
Historial de mantenimiento: [Últimas intervenciones realizadas]

PARÁMETROS NORMALES DE OPERACIÓN:
[Rangos normales para cada tipo de sensor según especificaciones]

INSTRUCCIONES:
1. Compara valores actuales con rangos normales de operación
2. Identifica tendencias preocupantes en los últimos períodos
3. Correlaciona anomalías entre diferentes sensores
4. Evalúa criticidad de la máquina en el proceso productivo
5. Considera historial previo de fallas similares
6. Proporciona recomendaciones específicas y priorizadas

FORMATO DE RESPUESTA:
### ANÁLISIS PREDICTIVO - [Máquina ID]

#### 1. ESTADO ACTUAL
- Estado general: [Normal/Atención/Crítico]
- Sensores fuera de rango: [Lista con valores específicos]
- Tendencias identificadas: [Deterioro gradual/Cambio abrupto/Estable]

#### 2. PREDICCIÓN DE FALLAS
- Probabilidad de falla en 7 días: [Baja/Media/Alta] - [X%]
- Tipo de falla más probable: [Descripción específica]
- Componentes en riesgo: [Lista de partes específicas]

#### 3. IMPACTO EN PRODUCCIÓN
- Criticidad de la máquina: [Baja/Media/Alta]
- Impacto de parada: [Horas de producción perdidas estimadas]
- Máquinas alternativas disponibles: [Sí/No]

#### 4. RECOMENDACIONES PRIORITARIAS
1. **[Acción inmediata]** - [Descripción y justificación]
2. **[Acción a 48h]** - [Descripción y justificación]
3. **[Acción preventiva]** - [Descripción y justificación]

#### 5. PLAN DE MONITOREO
- Frecuencia de revisión recomendada: [Cada X horas]
- Sensores críticos a vigilar: [Lista específica]
- Umbrales de alerta: [Valores específicos para cada sensor]
```

**¿Por qué funciona bien?**
1. **Análisis multi-sensor**: Correlaciona diferentes tipos de datos para predicciones más precisas
2. **Contexto histórico**: Considera patrones previos de fallas
3. **Priorización clara**: Evalúa impacto en producción para priorizar acciones
4. **Predicciones específicas**: No solo dice "hay problema" sino qué tipo y cuándo
5. **Plan de acción**: Proporciona pasos concretos y tiempos específicos

---

### 12. Farmacéutica: Research Intelligence

**Solución sencilla:**

Para procesar literatura científica e identificar oportunidades de investigación:

```
Actúa como un científico investigador farmacéutico con PhD y 15+ años en drug discovery.

TAREA: Analiza el siguiente conjunto de papers científicos para extraer insights relevantes y identificar oportunidades de investigación.

DOCUMENTOS A ANALIZAR:
[Lista de papers con títulos, autores, journals y abstracts]

ÁREA DE INTERÉS PRINCIPAL:
[Ej: Oncología, Neurología, Inmunología, etc.]

CONTEXTO DE LA EMPRESA:
Pipeline actual: [Medicamentos en desarrollo]
Áreas de expertise: [Especialidades terapéuticas]
Tecnologías disponibles: [Plataformas de investigación]

INSTRUCCIONES:
1. Identifica hallazgos científicos relevantes para el área de interés
2. Evalúa credibilidad de fuentes (journal impact factor, autores reconocidos)
3. Detecta tendencias emergentes y patrones en la investigación
4. Identifica gaps en el conocimiento actual
5. Evalúa oportunidades de colaboración o licensing
6. Considera aspectos regulatorios y de propiedad intelectual

FORMATO DE RESPUESTA:
### RESEARCH INTELLIGENCE REPORT - [Área Terapéutica]

#### 1. HALLAZGOS CIENTÍFICOS CLAVE
**Hallazgo #1:** [Título del descubrimiento]
- Paper fuente: [Título, autores, journal]
- Relevancia: [Alta/Media/Baja]
- Implicaciones: [Descripción del impacto potencial]
- Aplicabilidad: [Cómo se relaciona con nuestro pipeline]

[Repetir para 3-5 hallazgos principales]

#### 2. TENDENCIAS EMERGENTES
- [Tendencia 1]: [Descripción y evidencia en múltiples papers]
- [Tendencia 2]: [Descripción y evidencia en múltiples papers]
- [Tendencia 3]: [Descripción y evidencia en múltiples papers]

#### 3. GAPS Y OPORTUNIDADES IDENTIFICADAS
- **Gap científico**: [Área no explorada suficientemente]
- **Oportunidad terapéutica**: [Nueva indicación o población]
- **Oportunidad tecnológica**: [Nueva plataforma o metodología]

#### 4. EVALUACIÓN DE CREDIBILIDAD
- Papers de alto impacto: [X] (IF >10)
- Papers de impacto medio: [X] (IF 5-10)
- Autores reconocidos involucrados: [Lista de KOLs]
- Consistencia entre estudios: [Alta/Media/Baja]

#### 5. RECOMENDACIONES ESTRATÉGICAS
1. **Investigación interna**: [Áreas para explorar internamente]
2. **Colaboraciones potenciales**: [Instituciones o grupos de interés]
3. **Oportunidades de licensing**: [Tecnologías específicas]
4. **Vigilancia competitiva**: [Competidores activos en el área]

#### 6. PRÓXIMOS PASOS
- [Acción específica 1 con timeline]
- [Acción específica 2 con timeline]
- [Acción específica 3 con timeline]
```

**¿Por qué funciona bien?**
1. **Evaluación crítica**: No solo resume sino que evalúa credibilidad y relevancia
2. **Perspectiva estratégica**: Conecta hallazgos científicos con oportunidades de negocio
3. **Detección de patrones**: Identifica tendencias que podrían no ser obvias en papers individuales
4. **Orientado a acción**: Proporciona recomendaciones específicas y ejecutables
5. **Contexto empresarial**: Adapta análisis a las capacidades y objetivos específicos

---

### 13. Gaming: Generación de Contenido

**Solución sencilla:**

Para generar contenido narrativo coherente en videojuegos:

```
Actúa como un game designer narrativo senior con experiencia en RPGs y sistemas procedurales.

TAREA: Genera contenido narrativo (historia, diálogos, quests) que sea coherente con el mundo del juego y se adapte al progreso del jugador.

CONTEXTO DEL MUNDO:
Setting: [Medieval fantasy, Sci-fi, Modern, etc.]
Tono: [Serio, Cómico, Épico, Misterioso, etc.]
Lore principal: [Historia del mundo, facciones, conflictos]

ESTADO DEL JUGADOR:
Nivel: [X]
Clase/Profesión: [Guerrero, Mago, etc.]
Decisiones previas: [Elecciones importantes tomadas]
Reputación: [Con diferentes facciones]
Ubicación actual: [Zona del mapa]

TIPO DE CONTENIDO A GENERAR:
[Quest principal, Quest secundaria, Diálogo NPC, Lore texto, etc.]

INSTRUCCIONES:
1. Mantén coherencia absoluta con el lore establecido
2. Adapta el contenido al nivel y progreso del jugador
3. Incluye referencias a decisiones previas del jugador
4. Balancea dificultad narrativa con gameplay
5. Crea contenido que incentive exploración y rejugabilidad
6. Considera impacto en futuras decisiones y story branches

FORMATO DE RESPUESTA:
### CONTENIDO GENERADO - [Tipo de contenido]

#### 1. RESUMEN EJECUTIVO
- Título: [Nombre del quest/diálogo/contenido]
- Tipo: [Principal/Secundario/Opcional]
- Duración estimada: [X minutos de gameplay]
- Nivel recomendado: [X-Y]

#### 2. CONTEXTO NARRATIVO
- Conexión con historia principal: [Cómo se relaciona]
- NPCs involucrados: [Personajes clave y sus motivaciones]
- Ubicaciones: [Lugares donde se desarrolla]

#### 3. CONTENIDO DETALLADO

**Para Quests:**
- Objetivo principal: [Descripción clara]
- Objetivos secundarios: [Lista de sub-tareas]
- Mecánicas de gameplay: [Combate, puzzle, exploración]
- Recompensas: [XP, items, story progression]

**Para Diálogos:**
- [NPC]: "[Línea de diálogo]"
- Opciones del jugador:
  1. "[Opción 1]" → [Consecuencia/reacción]
  2. "[Opción 2]" → [Consecuencia/reacción]
  3. "[Opción 3]" → [Consecuencia/reacción]

#### 4. VARIABLES Y ADAPTACIONES
- Variaciones por clase: [Diálogos específicos por profesión]
- Variaciones por reputación: [Cambios según standing con facciones]
- Referencias a decisiones previas: [Cómo se menciona el historial]

#### 5. IMPACTO EN EL MUNDO
- Cambios permanentes: [Qué se modifica en el mundo]
- Nuevas opciones desbloqueadas: [Contenido futuro habilitado]
- Efecto en NPCs: [Cómo reaccionan otros personajes]

#### 6. CONSIDERACIONES TÉCNICAS
- Assets requeridos: [Modelos, texturas, sonidos nuevos]
- Complejidad de implementación: [Baja/Media/Alta]
- Localización: [Elementos que requieren traducción]
```

**¿Por qué funciona bien?**
1. **Coherencia narrativa**: Mantiene consistencia con el mundo establecido
2. **Personalización**: Adapta contenido a las decisiones específicas del jugador
3. **Balance narrativo-gameplay**: Considera tanto historia como mecánicas de juego
4. **Escalabilidad**: Estructura permite generar contenido variado consistentemente
5. **Implementación práctica**: Incluye consideraciones técnicas para desarrollo

---

### 14. Inmobiliaria: Valoración Automatizada

**Solución sencilla:**

Para valoración automatizada de propiedades inmobiliarias:

```
Actúa como un tasador inmobiliario certificado con 15+ años de experiencia en valuación de propiedades.

TAREA: Analiza la información de la propiedad y genera una estimación de valor precisa considerando factores locales y de mercado.

INFORMACIÓN DE LA PROPIEDAD:
Dirección: [Ubicación específica]
Tipo: [Casa, Apartamento, Terreno, Comercial]
Área construida: [m2]
Área del terreno: [m2]
Habitaciones: [Número]
Baños: [Número]
Año de construcción: [AAAA]
Estado de conservación: [Excelente/Bueno/Regular/Malo]
Características especiales: [Piscina, jardín, garaje, etc.]

DATOS DE MERCADO LOCAL:
Zona/Barrio: [Nombre específico]
Ventas recientes similares: [Lista de comparables si están disponibles]
Tendencia de precios: [Alza/Baja/Estable últimos 12 meses]
Tiempo promedio de venta: [X días]

INSTRUCCIONES:
1. Evalúa la propiedad usando método de comparación de mercado
2. Ajusta por diferencias específicas (ubicación, estado, características)
3. Considera tendencias actuales del mercado local
4. Evalúa factores de plusvalía y depreciación
5. Calcula rango de valor con nivel de confianza
6. Identifica fortalezas y debilidades que afectan el precio

FORMATO DE RESPUESTA:
### VALUACIÓN INMOBILIARIA - [Dirección]

#### 1. ESTIMACIÓN DE VALOR
- **Valor estimado**: $[X] - $[Y] USD
- **Valor central**: $[Z] USD
- **Precio por m2**: $[X] USD/m2
- **Nivel de confianza**: [Alto/Medio/Bajo] - [XX%]

#### 2. ANÁLISIS COMPARATIVO
**Propiedad comparable #1:**
- Dirección: [Ubicación]
- Precio de venta: $[X] USD
- Fecha de venta: [MM/AAAA]
- Ajustes aplicados: [+/-$X por ubicación, +/-$Y por estado]

[Repetir para 2-3 comparables adicionales]

#### 3. FACTORES QUE AFECTAN EL VALOR

**Factores Positivos:**
- [Factor 1]: [Impacto estimado +$X]
- [Factor 2]: [Impacto estimado +$Y]
- [Factor 3]: [Impacto estimado +$Z]

**Factores Negativos:**
- [Factor 1]: [Impacto estimado -$X]
- [Factor 2]: [Impacto estimado -$Y]

#### 4. ANÁLISIS DE UBICACIÓN
- Categoría de zona: [Residencial alta/media/popular]
- Servicios cercanos: [Colegios, hospitales, transporte]
- Desarrollo futuro: [Proyectos que puedan afectar valor]
- Plusvalía proyectada: [X% anual estimado]

#### 5. RECOMENDACIONES
**Para Vendedores:**
- Precio sugerido de listado: $[X] USD
- Mejoras recomendadas: [Lista específica con ROI]
- Estrategia de marketing: [Destacar fortalezas principales]

**Para Compradores:**
- Rango de oferta recomendado: $[X] - $[Y] USD
- Due diligence sugerida: [Inspecciones específicas]
- Potencial de inversión: [Análisis de crecimiento]

#### 6. LIMITACIONES Y CONSIDERACIONES
- [Factores que podrían afectar la precisión]
- [Recomendaciones para validación adicional]
- [Vigencia de la valuación: XX días]
```

**¿Por qué funciona bien?**
1. **Metodología probada**: Usa técnicas estándar de valuación inmobiliaria
2. **Análisis comparativo**: Basa estimaciones en datos reales de mercado
3. **Factores locales**: Considera características específicas del área
4. **Transparencia**: Explica cómo llega a cada estimación
5. **Accionable**: Proporciona recomendaciones específicas para diferentes usuarios

**Para mejorarlo:**
- Agregar análisis por sector/industria para comparar con estándares del mercado
- Incluir detección de posibles manipulaciones contables
- Adaptar indicadores según tipo de negocio (manufacturero, servicios, etc.)

---

### 15. Energía: Optimización de Grid

**Solución sencilla:**

Para optimizar distribución de energía en redes eléctricas inteligentes:

```
Actúa como un ingeniero de sistemas eléctricos con especialización en smart grids y energías renovables.

TAREA: Analiza el estado actual de la red eléctrica y optimiza la distribución de energía considerando eficiencia, costo y sostenibilidad.

ESTADO ACTUAL DE LA RED:
Demanda total: [MW]
Fuentes de generación activas:
- Plantas térmicas: [X MW] - Costo: $[Y]/MWh
- Energía solar: [X MW] - Costo: $[Y]/MWh  
- Energía eólica: [X MW] - Costo: $[Y]/MWh
- Hidroeléctrica: [X MW] - Costo: $[Y]/MWh
- Baterías disponibles: [X MWh] - Estado de carga: [Y%]

PREDICCIONES:
Demanda próximas 24h: [Curva de demanda esperada]
Generación renovable próximas 24h: [Predicción solar/eólica]
Precio del mercado eléctrico: [Proyección horaria]

RESTRICCIONES Y OBJETIVOS:
- Minimizar costos operativos
- Maximizar uso de renovables
- Mantener reserva mínima: [X MW]
- Limitar emisiones de CO2
- Estabilidad de la red: Frecuencia 50Hz ±0.2Hz

INSTRUCCIONES:
1. Optimiza mix de generación para próximas 24 horas
2. Programa carga/descarga de baterías estratégicamente
3. Identifica oportunidades de demand response
4. Calcula costos operativos y emisiones
5. Prepara plan de contingencia para imprevistos
6. Considera impacto en estabilidad de red

FORMATO DE RESPUESTA:
### OPTIMIZACIÓN DE GRID ELÉCTRICO - [Fecha]

#### 1. ESTRATEGIA DE DESPACHO OPTIMIZADA

**Distribución por fuente (próximas 6 horas):**
| Hora | Térmica | Solar | Eólica | Hidro | Baterías | Total |
|------|---------|--------|--------|--------|----------|-------|
| 06:00| X MW   | Y MW  | Z MW  | A MW  | B MW    | C MW |
[Continuar para 24 horas]

#### 2. GESTIÓN DE ALMACENAMIENTO
- **Horarios de carga**: [XX:XX - YY:XX] cuando hay exceso renovable
- **Horarios de descarga**: [XX:XX - YY:XX] durante picos de demanda
- **Estado final de baterías**: [X% de capacidad]

#### 3. ANÁLISIS ECONÓMICO
- **Costo operativo total**: $[X] para 24h
- **Ahorro vs. operación tradicional**: $[Y] ([Z%])
- **Precio promedio por MWh**: $[X]
- **Ingresos por servicios auxiliares**: $[Y]

#### 4. IMPACTO AMBIENTAL
- **Emisiones CO2 totales**: [X] toneladas
- **Reducción vs. mix tradicional**: [Y] toneladas ([Z%])
- **% energía renovable**: [X%] del total generado

#### 5. DEMAND RESPONSE RECOMENDADO
- **Cargas diferibles identificadas**: [X MW] disponibles
- **Incentivos sugeridos**: $[Y]/MWh para reducción
- **Horarios críticos**: [XX:XX - YY:XX] máxima necesidad

#### 6. PLAN DE CONTINGENCIA
**Escenario 1: Falla de planta térmica principal**
- Fuentes de respaldo: [Lista priorizada]
- Tiempo de respuesta: [X minutos]
- Costo adicional: $[Y]

**Escenario 2: Baja generación renovable**
- Aumento en térmicas: [X MW]
- Uso de reservas: [Y MW]
- Impacto en costos: +$[Z]

#### 7. RECOMENDACIONES OPERATIVAS
1. **Mantenimiento programado**: [Ventanas óptimas identificadas]
2. **Inversiones sugeridas**: [Mejoras de infraestructura]
3. **Ajustes de pronóstico**: [Áreas para mejorar predicciones]
```

**¿Por qué funciona bien?**
1. **Optimización multi-objetivo**: Balancea costo, sostenibilidad y estabilidad
2. **Horizonte temporal**: Planifica tanto operación inmediata como estrategia diaria
3. **Gestión de incertidumbre**: Incluye planes de contingencia para diferentes escenarios
4. **Métricas claras**: Cuantifica beneficios económicos y ambientales
5. **Operacionalmente viable**: Considera restricciones técnicas reales del sistema

---

### 16. Agricultura: Precision Farming

**Solución sencilla:**

Para agricultura de precisión con análisis de datos IoT:

```
Actúa como un agrónomo especializado en agricultura de precisión con experiencia en análisis de datos de sensores y optimización de cultivos.

TAREA: Analiza los datos de sensores, clima y mercado para generar recomendaciones específicas de manejo de cultivos.

DATOS DE SENSORES (ÚLTIMO PERÍODO):
Humedad del suelo: [% por zona del campo]
Temperatura del suelo: [°C por zona]
pH del suelo: [Valores por zona]
Nutrientes NPK: [Niveles por zona]
Temperatura ambiente: [Promedio últimos 7 días]
Precipitación: [mm últimos 30 días]
Índice de vegetación (NDVI): [Valores por zona]

INFORMACIÓN DEL CULTIVO:
Tipo de cultivo: [Maíz, Soja, Trigo, etc.]
Etapa fenológica: [Germinación, Crecimiento, Floración, etc.]
Fecha de siembra: [DD/MM/AAAA]
Variedad: [Nombre específico]
Área total: [hectáreas]

DATOS DE MERCADO:
Precio actual: $[X]/tonelada
Proyección de precios: [Tendencia esperada]
Costo de insumos: [Precios de fertilizantes, pesticidas]

INSTRUCCIONES:
1. Evalúa el estado actual del cultivo por zonas del campo
2. Identifica áreas que requieren atención específica
3. Optimiza aplicación de insumos según necesidades locales
4. Considera factores climáticos y proyecciones meteorológicas
5. Calcula rentabilidad esperada con diferentes estrategias
6. Proporciona cronograma de actividades específicas

FORMATO DE RESPUESTA:
### RECOMENDACIONES DE MANEJO - [Tipo de cultivo]

#### 1. EVALUACIÓN DEL ESTADO ACTUAL

**Zona A (Sector Norte - X hectáreas):**
- Estado general: [Excelente/Bueno/Regular/Preocupante]
- Problemas identificados: [Lista específica]
- Rendimiento esperado: [ton/ha]

**Zona B (Sector Centro - X hectáreas):**
- Estado general: [Evaluación]
- Problemas identificados: [Lista específica]
- Rendimiento esperado: [ton/ha]

[Continuar para todas las zonas identificadas]

#### 2. RECOMENDACIONES DE FERTILIZACIÓN

**Aplicación Variable por Zona:**
- **Zona A**: [X kg/ha de NPK] - Justificación: [Niveles actuales bajos en P]
- **Zona B**: [Y kg/ha de NPK] - Justificación: [pH elevado, aplicar con corrector]
- **Zona C**: [Z kg/ha de NPK] - Justificación: [Mantener niveles actuales]

**Cronograma de aplicación:**
- Próxima aplicación: [Fecha específica]
- Método recomendado: [Foliar/Al suelo/Fertirrigación]
- Condiciones ideales: [Clima y humedad requeridos]

#### 3. MANEJO DE RIEGO

**Necesidades hídricas por zona:**
- Zonas con déficit: [Lista específica] - Aplicar [X mm]
- Zonas con exceso: [Lista específica] - Mejorar drenaje
- Programación óptima: [Horarios y frecuencias]

#### 4. CONTROL FITOSANITARIO

**Riesgos identificados:**
- Plagas detectadas: [Especies específicas y nivel de infestación]
- Enfermedades: [Signos de patógenos observados]
- Malezas: [Especies dominantes por zona]

**Estrategia de control:**
- Tratamientos requeridos: [Productos específicos y dosis]
- Timing crítico: [Ventanas de aplicación óptimas]
- Monitoreo: [Frecuencia de inspección recomendada]

#### 5. ANÁLISIS ECONÓMICO

**Proyección de costos:**
- Fertilizantes: $[X] total
- Pesticidas: $[Y] total
- Riego: $[Z] total
- Otros insumos: $[W] total

**Proyección de ingresos:**
- Rendimiento esperado: [X ton/ha promedio]
- Ingreso bruto: $[Y] (precio actual)
- Margen neto proyectado: $[Z] ([W%])

#### 6. CRONOGRAMA DE ACTIVIDADES

**Próximos 15 días:**
- Día 1-3: [Actividades específicas]
- Día 4-7: [Actividades específicas]
- Día 8-15: [Actividades específicas]

**Próximos 30 días:**
- Semana 3: [Actividades planificadas]
- Semana 4: [Actividades planificadas]

#### 7. RECOMENDACIONES PARA PRÓXIMA TEMPORADA
- Variedades a considerar: [Recomendaciones basadas en performance]
- Mejoras de infraestructura: [Drenaje, riego, caminos]
- Ajustes en manejo: [Lecciones aprendidas]
```

**¿Por qué funciona bien?**
1. **Manejo diferenciado**: Reconoce variabilidad dentro del campo
2. **Integración de datos**: Combina sensores, clima y mercado
3. **Optimización económica**: Balancea inversión en insumos con retorno esperado
4. **Cronograma específico**: Proporciona plan de acción con fechas concretas
5. **Perspectiva temporal**: Considera tanto necesidades inmediatas como planificación futura

---

### 17. Retail: Demand Forecasting

**Solución sencilla:**

Para pronóstico de demanda en cadenas retail:

```
Actúa como un analista de demand planning con experiencia en retail y forecasting estadístico.

TAREA: Analiza datos históricos y factores externos para generar pronósticos precisos de demanda por tienda y producto.

DATOS HISTÓRICOS:
Ventas últimos 24 meses: [Datos por producto/tienda/mes]
Estacionalidad identificada: [Patrones recurrentes]
Promociones pasadas: [Historial de descuentos y su impacto]
Stock-outs previos: [Productos y fechas de agotamiento]

FACTORES EXTERNOS:
Eventos especiales próximos: [Feriados, eventos locales]
Campañas de marketing planificadas: [Presupuesto y alcance]
Competencia: [Nuevas aperturas, promociones competidoras]
Tendencias de mercado: [Cambios en preferencias del consumidor]

INFORMACIÓN POR TIENDA:
Ubicación: [Zona geográfica]
Perfil de clientes: [Demografia principal]
Tamaño de tienda: [m2]
Categorías principales: [Productos más vendidos]

INSTRUCCIONES:
1. Analiza patrones estacionales y tendencias por categoría
2. Evalúa impacto de promociones y eventos especiales
3. Considera factores locales específicos por tienda
4. Identifica productos con mayor variabilidad de demanda
5. Calcula niveles de inventario óptimos
6. Proporciona alertas tempranas para posibles problemas

FORMATO DE RESPUESTA:
### PRONÓSTICO DE DEMANDA - [Período proyectado]

#### 1. RESUMEN EJECUTIVO
- **Crecimiento esperado total**: [X%] vs. mismo período año anterior
- **Categorías de alto crecimiento**: [Lista top 3]
- **Categorías en declive**: [Lista con mayor caída]
- **Confianza del pronóstico**: [Alta/Media/Baja] - [XX%]

#### 2. PRONÓSTICO POR CATEGORÍA

**Categoría A: [Nombre]**
- Demanda proyectada: [X unidades] ([Y%] vs. año anterior)
- Pico de demanda esperado: [Fecha/semana específica]
- Factores clave: [Eventos o tendencias que influyen]
- Nivel de inventario recomendado: [X días de stock]

[Repetir para categorías principales]

#### 3. ANÁLISIS POR TIENDA

**Tienda 1: [Ubicación]**
- Ventas proyectadas: $[X] ([Y%] vs. año anterior)
- Productos estrella: [Top 5 con mayor demanda]
- Riesgos identificados: [Factores que podrían afectar]
- Ajustes recomendados: [Cambios en mix de productos]

[Repetir para tiendas principales]

#### 4. IMPACTO DE EVENTOS ESPECIALES

**Evento 1: [Feriado/Promoción]**
- Fecha: [XX/XX/XXXX]
- Categorías afectadas: [Lista con % de incremento esperado]
- Preparación requerida: [Semanas de anticipación]
- Stock adicional necesario: [% sobre demanda normal]

#### 5. GESTIÓN DE INVENTARIOS

**Productos de alta rotación:**
- [Producto 1]: Stock mínimo [X] unidades, máximo [Y] unidades
- [Producto 2]: Stock mínimo [X] unidades, máximo [Y] unidades

**Productos estacionales:**
- Inicio de temporada: [Fecha para aumentar stock]
- Pico de demanda: [Semana específica]
- Fin de temporada: [Fecha para liquidación]

#### 6. ALERTAS Y RIESGOS

**Alertas tempranas:**
- [Producto/categoría]: Riesgo de stock-out en [fecha]
- [Producto/categoría]: Exceso de inventario proyectado
- [Tienda específica]: Demanda inusualmente baja detectada

**Factores de riesgo:**
- Cambios en competencia: [Impacto estimado en ventas]
- Variabilidad climática: [Productos sensibles al clima]
- Fluctuaciones económicas: [Categorías más vulnerables]

#### 7. RECOMENDACIONES OPERATIVAS

**Ajustes inmediatos (próximas 2 semanas):**
1. [Acción específica] para [producto/categoría]
2. [Acción específica] para [tienda/región]
3. [Acción específica] para [evento próximo]

**Planificación a mediano plazo (1-3 meses):**
- Renegociación con proveedores: [Productos con mayor demanda]
- Redistribución entre tiendas: [Optimización de inventarios]
- Ajustes de precios: [Productos con baja rotación]

#### 8. MÉTRICAS DE SEGUIMIENTO
- Accuracy del pronóstico: [Meta >XX%]
- Nivel de servicio: [Meta >XX% de disponibilidad]
- Rotación de inventario: [Meta XX vueltas/año]
- Margen de error aceptable: [±XX% para productos A]
```

**¿Por qué funciona bien?**
1. **Análisis multi-dimensional**: Considera múltiples factores que afectan la demanda
2. **Granularidad apropiada**: Proporciona pronósticos tanto a nivel agregado como específico
3. **Orientado a acción**: Incluye recomendaciones operativas específicas
4. **Gestión de riesgo**: Identifica y alerta sobre posibles problemas
5. **Métricas de control**: Establece indicadores para medir precisión

---

### 18. Telecomunicaciones: Network Optimization

**Solución sencilla:**

Para optimización de redes de telecomunicaciones:

```
Actúa como un ingeniero de redes de telecomunicaciones con especialización en optimización de performance 4G/5G.

TAREA: Analiza métricas de performance de la red y recomienda optimizaciones para mejorar calidad de servicio y eficiencia operativa.

MÉTRICAS ACTUALES DE RED:
Cobertura: [% del área objetivo]
Throughput promedio: [Mbps por tecnología]
Latencia: [ms promedio]
Tasa de caída de llamadas: [%]
Congestión de red: [% de células en horarios pico]
Utilización de espectro: [% por banda de frecuencia]

INFRAESTRUCTURA DISPONIBLE:
Número de celdas 4G: [X]
Número de celdas 5G: [Y]
Backhaul disponible: [Fibra/Microondas/Satellite - capacidades]
Equipos disponibles: [Inventory de equipos para upgrades]

PATRONES DE TRÁFICO:
Picos de demanda: [Horarios y ubicaciones]
Tipos de aplicaciones dominantes: [Video, voz, datos]
Movilidad de usuarios: [Patrones de handover]
Crecimiento proyectado: [% anual por tipo de servicio]

INSTRUCCIONES:
1. Identifica bottlenecks y áreas problemáticas en la red
2. Prioriza optimizaciones según impacto en experiencia del usuario
3. Balancea inversión en equipos vs. mejoras de configuración
4. Considera eficiencia energética y costo operativo
5. Planifica para crecimiento futuro de demanda
6. Asegura cumplimiento de SLAs con clientes

FORMATO DE RESPUESTA:
### PLAN DE OPTIMIZACIÓN DE RED - [Área/Región]

#### 1. DIAGNÓSTICO ACTUAL

**Performance General:**
- Nivel de servicio actual: [Excelente/Bueno/Regular/Deficiente]
- Principales problemas identificados: [Lista priorizada]
- SLAs en riesgo: [Cuáles no se están cumpliendo]
- Impacto en satisfacción del cliente: [Score actual]

**Análisis por Tecnología:**
- **4G**: Utilización [X%], Problemas: [Lista específica]
- **5G**: Cobertura [Y%], Oportunidades: [Áreas de expansión]

#### 2. ÁREAS CRÍTICAS IDENTIFICADAS

**Zona Crítica #1: [Ubicación específica]**
- Problema principal: [Descripción técnica]
- Impacto: [Usuarios afectados, degradación de servicio]
- Causa raíz: [Análisis técnico]
- Prioridad: [Alta/Media/Baja]

[Repetir para todas las zonas críticas]

#### 3. RECOMENDACIONES DE OPTIMIZACIÓN

**Optimizaciones de Configuración (Sin CAPEX):**
1. **Ajuste de parámetros RF:**
   - Celdas afectadas: [Lista específica]
   - Parámetros a modificar: [Potencia, tilts, azimuts]
   - Impacto esperado: [Mejora en throughput/cobertura]
   - Timeline: [Días para implementación]

2. **Optimización de algoritmos:**
   - Load balancing: [Algoritmos específicos]
   - Handover: [Thresholds a ajustar]
   - Scheduler: [Modificaciones recomendadas]

**Inversiones Requeridas (Con CAPEX):**
1. **Nuevos sitios necesarios:**
   - Ubicaciones: [Coordenadas específicas]
   - Tipo de sitio: [Macro/Micro/Small cell]
   - Costo estimado: $[X] por sitio
   - Impacto esperado: [Cobertura y capacidad]

2. **Upgrades de equipos:**
   - Sitios a modernizar: [Lista específica]
   - Equipos requeridos: [Modelos y cantidades]
   - Inversión total: $[Y]

#### 4. OPTIMIZACIÓN DE CAPACIDAD

**Gestión de Tráfico:**
- Redistribución entre bandas: [Estrategia específica]
- Carrier aggregation: [Combinaciones óptimas]
- Offloading a WiFi: [Ubicaciones estratégicas]

**Planificación de Espectro:**
- Refarming de frecuencias: [De 3G/4G hacia 5G]
- Nuevas bandas a desplegar: [Timeline y prioridades]
- Coordinación de interferencias: [Medidas específicas]

#### 5. MEJORAS EN CALIDAD DE EXPERIENCIA

**Por Tipo de Servicio:**
- **Video streaming**: [Optimizaciones específicas]
- **Llamadas VoLTE**: [Mejoras en calidad de voz]
- **Aplicaciones IoT**: [Optimización para low latency]
- **Gaming móvil**: [Reducción de jitter]

#### 6. EFICIENCIA OPERATIVA

**Reducción de Costos:**
- Ahorro energético: [Técnicas de sleep mode]
- Automatización: [Procesos a automatizar]
- Mantenimiento predictivo: [KPIs a monitorear]

**Monitoreo y Control:**
- Dashboards recomendados: [Métricas clave]
- Alertas automáticas: [Thresholds críticos]
- Reportes de performance: [Frecuencia y distribución]

#### 7. CRONOGRAMA DE IMPLEMENTACIÓN

**Fase 1 (0-30 días): Optimizaciones inmediatas**
- [Lista de actividades sin CAPEX]
- Recursos requeridos: [Personal y tiempo]
- Impacto esperado: [Mejoras cuantificadas]

**Fase 2 (1-6 meses): Inversiones menores**
- [Small cells y mejoras puntuales]
- Presupuesto: $[X]
- ROI esperado: [Beneficios vs. inversión]

**Fase 3 (6-18 meses): Expansión mayor**
- [Nuevos sitios y upgrades masivos]
- Presupuesto: $[Y]
- Impacto transformacional: [Cambio en performance]

#### 8. MÉTRICAS DE ÉXITO
- Mejora en throughput: [Meta +XX%]
- Reducción en latencia: [Meta -XX ms]
- Aumento en satisfacción: [Score >X.X]
- ROI de inversiones: [Meta >XX% en Y años]
```

**¿Por qué funciona bien?**
1. **Diagnóstico comprehensivo**: Identifica problemas técnicos específicos
2. **Priorización económica**: Balancea mejoras gratuitas vs. inversiones
3. **Impacto medible**: Cuantifica beneficios de cada mejora propuesta
4. **Enfoque temporal**: Separa acciones inmediatas de planificación a largo plazo
5. **Orientado al cliente**: Relaciona mejoras técnicas con experiencia del usuario

---

### 19. Seguros: Claims Processing

**Solución sencilla:**

Para procesamiento automatizado de reclamos de seguros:

```
Actúa como un adjustador de seguros senior con 12+ años de experiencia en evaluación de reclamos y detección de fraude.

TAREA: Analiza el reclamo presentado, evalúa su validez, detecta posibles señales de fraude y recomienda acciones específicas.

INFORMACIÓN DEL RECLAMO:
Número de póliza: [XXXX-YYYY]
Tipo de seguro: [Auto, Hogar, Vida, Salud, etc.]
Fecha del incidente: [DD/MM/AAAA]
Fecha de reporte: [DD/MM/AAAA]
Monto reclamado: $[X]
Descripción del incidente: [Relato del asegurado]

DOCUMENTACIÓN ADJUNTA:
- Fotos del daño: [Disponible/No disponible]
- Reporte policial: [Sí/No]
- Facturas/presupuestos: [Cantidad y montos]
- Testimonios: [Número de testigos]
- Reportes médicos: [Si aplica]

HISTORIAL DEL ASEGURADO:
Años como cliente: [X]
Reclamos previos: [Número y tipos en últimos 5 años]
Pagos de prima: [Al día/Atrasados]
Cambios recientes en póliza: [Aumentos de cobertura, etc.]

INSTRUCCIONES:
1. Evalúa consistencia del relato con evidencia presentada
2. Identifica red flags de posible fraude
3. Verifica que el daño esté cubierto por la póliza
4. Calcula monto justo de indemnización
5. Recomienda investigación adicional si es necesario
6. Determina nivel de autorización requerido

FORMATO DE RESPUESTA:
### EVALUACIÓN DE RECLAMO - [Número de claim]

#### 1. RESUMEN EJECUTIVO
- **Recomendación**: [Aprobar/Rechazar/Investigar]
- **Monto recomendado**: $[X] (vs. $[Y] reclamado)
- **Nivel de riesgo de fraude**: [Bajo/Medio/Alto]
- **Tiempo estimado de resolución**: [X días]

#### 2. ANÁLISIS DE COBERTURA
- **Póliza vigente**: [Sí/No] al momento del incidente
- **Evento cubierto**: [Sí/No] según términos de la póliza
- **Deducible aplicable**: $[X]
- **Límites de cobertura**: $[Y] máximo para este tipo de daño
- **Exclusiones aplicables**: [Lista si existen]

#### 3. EVALUACIÓN DEL INCIDENTE

**Consistencia del relato:**
- Descripción del asegurado: [Coherente/Inconsistente]
- Evidencia física: [Concuerda/No concuerda] con el relato
- Testimonios: [Confirman/Contradicen] la versión del asegurado
- Timeline: [Lógico/Problemático]

**Análisis de daños:**
- Daños reportados: [Lista específica]
- Daños verificables en fotos: [Lista comparativa]
- Estimación de costo de reparación: $[X] - $[Y]
- Daños pre-existentes identificados: [Lista si existen]

#### 4. INDICADORES DE FRAUDE

**Red Flags Identificados:**
- [✓/✗] Reporte tardío sin justificación válida
- [✓/✗] Historial de múltiples reclamos recientes
- [✓/✗] Aumento de cobertura poco antes del incidente
- [✓/✗] Inconsistencias en declaraciones
- [✓/✗] Documentación sospechosa o alterada
- [✓/✗] Patrones inusuales en el incidente

**Análisis de riesgo:**
- Score de fraude: [X/10]
- Factores agravantes: [Lista específica]
- Factores atenuantes: [Lista específica]

#### 5. VALIDACIÓN FINANCIERA

**Evaluación de presupuestos:**
- Presupuesto 1: $[X] - [Realista/Inflado/Insuficiente]
- Presupuesto 2: $[Y] - [Comparativa con mercado]
- Valor de mercado estimado: $[Z]

**Cálculo de indemnización:**
- Monto del daño validado: $[X]
- Menos deducible: -$[Y]
- Menos depreciación: -$[Z]
- **Total recomendado**: $[W]

#### 6. INVESTIGACIÓN ADICIONAL REQUERIDA

**Si es necesaria más investigación:**
- Tipo de investigación: [Campo/Documental/Pericial]
- Aspectos específicos a verificar: [Lista detallada]
- Profesionales requeridos: [Adjustador, perito, investigador]
- Costo estimado de investigación: $[X]
- Timeline para completar: [X días]

#### 7. RECOMENDACIONES ESPECÍFICAS

**Para aprobación:**
- Monto a autorizar: $[X]
- Forma de pago: [Cheque/Transferencia/Pago a proveedor]
- Condiciones especiales: [Si aplican]
- Documentación adicional requerida: [Lista]

**Para rechazo:**
- Motivos específicos: [Causales legales]
- Comunicación al asegurado: [Template de carta]
- Derecho de apelación: [Proceso explicado]

**Para investigación:**
- Suspensión temporal: [Días]
- Investigadores asignados: [Nombres/empresas]
- Presupuesto autorizado: $[Y]
- Reportes esperados: [Fechas específicas]

#### 8. GESTIÓN DE EXPECTATIVAS

**Comunicación con asegurado:**
- Mensaje clave: [Explicación del proceso]
- Timeline comunicado: [Expectativas realistas]
- Próximos pasos: [Qué se espera del asegurado]
- Contacto asignado: [Adjustador responsable]

#### 9. AUTORIZACIÓN REQUERIDA
- Monto dentro de autoridad del adjustador: [Sí/No]
- Nivel de aprobación necesario: [Supervisor/Manager/Director]
- Justificación para escalamiento: [Razones específicas]
```

**¿Por qué funciona bien?**
1. **Evaluación sistemática**: Sigue proceso estándar de la industria
2. **Detección de fraude**: Identifica patrones sospechosos específicos
3. **Análisis financiero**: Valida montos de forma objetiva
4. **Cumplimiento legal**: Asegura adherencia a términos de póliza
5. **Eficiencia operativa**: Acelera procesamiento mientras mantiene calidad

---

### 20. Transporte: Fleet Management

**Solución sencilla:**

Para gestión inteligente de flotas de transporte:

```
Actúa como un gestor de flotas con experiencia en optimización operativa y logística de transporte.

TAREA: Analiza datos operativos de la flota y optimiza asignación de vehículos, rutas y mantenimiento para maximizar eficiencia y minimizar costos.

DATOS DE LA FLOTA:
Total de vehículos: [X]
Tipos de vehículos: [Camiones, furgonetas, autos - cantidades]
Ubicación actual: [GPS de cada vehículo]
Estado operativo: [Activo/Mantenimiento/Fuera de servicio]
Consumo promedio: [Litros/100km por tipo]
Costo operativo: $/km por vehículo

OPERACIONES ACTUALES:
Rutas activas: [Número y destinos]
Carga promedio: [% de capacidad utilizada]
Distancia diaria promedio: [km por vehículo]
Tiempo de conducción: [Horas por conductor/día]
Demanda de servicios: [Solicitudes pendientes]

DATOS DE MANTENIMIENTO:
Próximos servicios programados: [Vehículos y fechas]
Historial de averías: [Frecuencia por vehículo]
Costos de mantenimiento: [Promedio mensual]
Vida útil restante: [Años por vehículo]

INSTRUCCIONES:
1. Optimiza asignación de vehículos a rutas según capacidad y eficiencia
2. Identifica oportunidades de consolidación de rutas
3. Programa mantenimiento preventivo optimizando disponibilidad
4. Evalúa performance de conductores y vehículos
5. Calcula KPIs operativos y de costo
6. Recomienda mejoras en eficiencia y rentabilidad

FORMATO DE RESPUESTA:
### OPTIMIZACIÓN DE GESTIÓN DE FLOTA - [Período]

#### 1. ANÁLISIS DE PERFORMANCE ACTUAL

**Eficiencia Operativa:**
- Utilización promedio de flota: [XX%]
- Ocupación promedio de carga: [XX%]
- Distancia vacía: [XX%] del total recorrido
- Tiempo de inactividad: [X horas/día promedio]

**Costos Operativos:**
- Costo por kilómetro: $[X.XX]
- Consumo de combustible: [X litros/100km]
- Costo de mantenimiento: $[X]/mes por vehículo
- Costo total operativo: $[X]/día

#### 2. OPTIMIZACIÓN DE RUTAS

**Ruta A: [Origen - Destinos]**
- Vehículos asignados actuales: [X]
- Optimización propuesta: [Y vehículos]
- Ahorro en distancia: [XX km/día] ([X%])
- Ahorro en tiempo: [X horas/día]
- Reducción de costos: $[X]/día

[Repetir para rutas principales]

**Consolidación de rutas identificada:**
- Rutas candidatas: [Lista específica]
- Vehículos liberados: [X unidades]
- Ahorro total estimado: $[Y]/mes

#### 3. ASIGNACIÓN OPTIMIZADA DE VEHÍCULOS

**Por Tipo de Servicio:**
- **Carga pesada**: [X camiones grandes] asignados
- **Distribución urbana**: [Y furgonetas] asignadas
- **Servicios express**: [Z vehículos pequeños] asignados

**Criterios de asignación:**
- Capacidad de carga vs. demanda
- Eficiencia de combustible por ruta
- Restricciones de acceso urbano
- Disponibilidad de conductores certificados

#### 4. PROGRAMACIÓN DE MANTENIMIENTO

**Mantenimiento Preventivo Optimizado:**
- Vehículo 001: Servicio [DD/MM] - Duración: [X horas]
- Vehículo 002: Servicio [DD/MM] - Duración: [Y horas]
[Continuar según cronograma]

**Estrategia de rotación:**
- Vehículos de respaldo necesarios: [X unidades]
- Horarios óptimos para mantenimiento: [Fuera de horas pico]
- Talleres recomendados: [Ubicaciones estratégicas]

#### 5. ANÁLISIS DE PERFORMANCE POR CONDUCTOR

**Top Performers:**
- Conductor A: Eficiencia [XX%], Consumo [-X%] vs. promedio
- Conductor B: Sin incidentes [X] días, Tiempo [+X%] productivo

**Áreas de mejora:**
- Conductor C: Consumo excesivo [+XX%], Requiere entrenamiento
- Conductor D: Tiempo de ruta [+XX%] vs. estándar

**Programa de capacitación recomendado:**
- Eco-driving: [X conductores]
- Seguridad vial: [Y conductores]
- Tecnología de flota: [Z conductores]

#### 6. GESTIÓN DE COMBUSTIBLE

**Estrategia de abastecimiento:**
- Estaciones preferidas: [Ubicaciones con mejores precios]
- Programa de tarjetas corporativas: [Descuentos negociados]
- Horarios óptimos: [Evitar horas pico de precio]

**Eficiencia energética:**
- Vehículos más eficientes: [Ranking por consumo]
- Oportunidades de eco-driving: [Ahorro potencial XX%]
- Evaluación de vehículos eléctricos: [ROI en X años]

#### 7. TECNOLOGÍA Y MONITOREO

**Dashboard recomendado:**
- KPIs en tiempo real: [Ubicación, consumo, estado]
- Alertas automáticas: [Thresholds críticos]
- Reportes automáticos: [Diarios, semanales, mensuales]

**Inversiones en tecnología:**
- GPS avanzado: $[X] - ROI en [Y] meses
- Sensores de combustible: $[Z] - Prevención de robo
- App para conductores: $[W] - Mejora comunicación

#### 8. PROYECCIÓN FINANCIERA

**Ahorros identificados (mensual):**
- Optimización de rutas: $[X]
- Mejor utilización de flota: $[Y]
- Mantenimiento preventivo: $[Z]
- Eficiencia de combustible: $[W]
- **Total ahorros**: $[SUMA]/mes

**Inversiones requeridas:**
- Tecnología: $[X] (una vez)
- Capacitación: $[Y] (anual)
- **ROI esperado**: [Basado en peak sales projections]

#### 9. PLAN DE IMPLEMENTACIÓN

**Fase 1 (Semanas 1-2): Optimizaciones inmediatas**
- Reasignación de rutas sin costo
- Implementación de mejores prácticas
- Capacitación básica de conductores

**Fase 2 (Mes 1-3): Mejoras tecnológicas**
- Instalación de sistemas de monitoreo
- Aplicación móvil para conductores
- Programas de mantenimiento preventivo

**Fase 3 (Mes 4-6): Optimización avanzada**
- Análisis de datos históricos
- Refinamiento de algoritmos de asignación
- Evaluación de renovación de flota

#### 10. MÉTRICAS DE SEGUIMIENTO
- Reducción en costo por km: [Meta -XX%]
- Aumento en utilización: [Meta +XX%]
- Mejora en satisfacción del cliente: [Score >X.X]
- Reducción en tiempo de entrega: [Meta -XX%]
```

**¿Por qué funciona bien?**
1. **Optimización integral**: Considera todos los aspectos de gestión de flota
2. **Enfoque en ROI**: Cuantifica beneficios de cada mejora propuesta
3. **Implementación práctica**: Proporciona plan de acción con fases específicas
4. **Monitoreo continuo**: Establece métricas para evaluación constante
5. **Balance costo-eficiencia**: Optimiza operaciones manteniendo calidad de servicio

---

## Casos de Estudio Nivel Intermedio - Respuestas (Continuación)

### 41. Bienes Raíces: Valuación Automatizada

**Solución sencilla:**

Para estimar el valor de propiedades basado en características y mercado local:

```
Actúa como un tasador inmobiliario certificado con experiencia en análisis de mercado y valoración de propiedades.

DATOS DE LA PROPIEDAD:
- Tipo: [Casa/Apartamento/Terreno/Comercial]
- Ubicación: [Dirección específica o zona]
- Superficie total: [m² construidos]
- Superficie terreno: [m² del lote]
- Habitaciones: [Número y distribución]
- Baños: [Cantidad y tipo]
- Antigüedad: [Años desde construcción]
- Estado de conservación: [Excelente/Bueno/Regular/Malo]

CARACTERÍSTICAS ADICIONALES:
- Amenidades: [Piscina, jardín, garage, etc.]
- Servicios incluidos: [Agua, luz, gas, internet]
- Seguridad: [Vigilancia, alarmas, zona segura]
- Transporte: [Proximidad a transporte público]
- Servicios cercanos: [Escuelas, hospitales, centros comerciales]

DATOS DE MERCADO:
- Ventas recientes en la zona: [3-5 propiedades similares con precios]
- Tendencia del mercado: [Subiendo/Estable/Bajando]
- Tiempo promedio de venta: [Días en el mercado]
- Oferta vs demanda: [Relación actual en la zona]
- Desarrollos futuros: [Proyectos que afectarán la zona]

FACTORES ECONÓMICOS:
- Tasas de interés actuales: [Porcentaje hipotecario]
- Situación económica local: [Empleo, industrias principales]
- Inflación: [Impacto en precios inmobiliarios]
- Políticas gubernamentales: [Subsidios, impuestos]

METODOLOGÍA DE VALUACIÓN:
1. Analiza propiedades comparables recientes (últimos 6 meses)
2. Ajusta por diferencias en tamaño, estado y amenidades
3. Considera la ubicación y accesibilidad
4. Evalúa el potencial de apreciación
5. Calcula rango de valor basado en múltiples métodos

FORMATO DE RESPUESTA:
Valor estimado: $[Precio] [Moneda]
Rango de confianza: $[Mínimo] - $[Máximo]
Valor por m²: $[Precio por metro cuadrado]
Confianza en la estimación: [Alta/Media/Baja]

Justificación del valor:
- Comparables utilizados: [Referencias de ventas similares]
- Ajustes aplicados: [Por qué se ajustó arriba/abajo]
- Factores positivos: [Qué aumenta el valor]
- Factores negativos: [Qué disminuye el valor]

Análisis de mercado:
- Tiempo estimado de venta: [Días/Meses]
- Estrategia de precio: [Agresivo/Competitivo/Premium]
- Mejor época para vender: [Cuándo maximizar precio]
- Mejoras recomendadas: [ROI de posibles renovaciones]

Riesgos y oportunidades:
- Factores de riesgo: [Qué podría afectar negativamente]
- Potencial de apreciación: [Proyección a 5 años]
- Recomendación de inversión: [Comprar/Esperar/Pasar]
```

**¿Por qué funciona bien?**
1. **Métodos múltiples**: No se basa en un solo factor de valoración
2. **Contextualizado**: Considera el mercado local específico
3. **Temporal**: Incluye timing óptimo para transacciones
4. **Risk-aware**: Identifica factores de riesgo y oportunidad
5. **Actionable**: Da recomendaciones específicas para vendedores/compradores

**Para mejorar:**
- Integrar con bases de datos de transacciones en tiempo real
- Añadir análisis de cashflow para propiedades de inversión
- Incluir factores ambientales y de cambio climático

---

### 42. Construcción: Planificación de Proyectos

**Solución sencilla:**

Para crear cronogramas realistas y gestionar recursos en proyectos de construcción:

```
Actúa como un ingeniero civil y director de proyectos con experiencia en construcción y planificación de cronogramas.

DATOS DEL PROYECTO:
- Tipo de construcción: [Residencial/Comercial/Industrial]
- Alcance: [m² a construir, número de pisos]
- Ubicación: [Zona, acceso, servicios disponibles]
- Presupuesto total: [Monto disponible]
- Fecha límite: [Cuándo debe estar listo]
- Cliente: [Particular/Empresa/Gobierno]

ESPECIFICACIONES TÉCNICAS:
- Tipo de estructura: [Concreto/Acero/Mixta/Madera]
- Acabados requeridos: [Básico/Medio/Alto/Lujo]
- Instalaciones especiales: [Elevadores, aires, sistemas inteligentes]
- Normativas aplicables: [Códigos locales, ambientales]
- Certificaciones necesarias: [LEED, sostenibilidad]

RECURSOS DISPONIBLES:
- Equipo de trabajo: [Arquitectos, ingenieros, obreros disponibles]
- Maquinaria: [Qué equipos están disponibles]
- Proveedores: [Materiales locales vs importados]
- Subcontratistas: [Especialidades que se tercerizan]
- Financiamiento: [Flujo de caja disponible]

RESTRICCIONES:
- Climáticas: [Época de lluvias, temperaturas extremas]
- Permisos: [Tiempo para obtener licencias]
- Acceso al sitio: [Limitaciones de transporte]
- Vecindario: [Restricciones de ruido, horarios]
- Fuerza laboral: [Disponibilidad de trabajadores calificados]

PLANIFICACIÓN REQUERIDA:
1. Divide el proyecto en fases principales
2. Identifica dependencias críticas entre actividades
3. Calcula duraciones realistas para cada fase
4. Asigna recursos considerando disponibilidad
5. Incluye contingencias para riesgos comunes

FORMATO DE RESPUESTA:
Cronograma general:
Fase 1: Preliminares ([X] semanas)
- Permisos y licencias: [Tiempo específico]
- Preparación del terreno: [Tiempo específico]
- Instalaciones temporales: [Tiempo específico]

Fase 2: Cimentación ([X] semanas)
- Excavación: [Tiempo específico]
- Cimentación: [Tiempo específico]
- Impermeabilización: [Tiempo específico]

[Continuar para todas las fases hasta acabados finales]

Cronograma detallado crítico:
- Ruta crítica: [Actividades que no pueden retrasarse]
- Hitos principales: [Fechas clave de entrega]
- Puntos de revisión: [Inspecciones obligatorias]

Recursos por fase:
- Personal requerido: [Número y especialidad por mes]
- Maquinaria necesaria: [Equipos y duración de uso]
- Materiales críticos: [Qué pedir con anticipación]

Gestión de riesgos:
- Riesgos climáticos: [Impacto y mitigación]
- Riesgos de suministro: [Materiales críticos]
- Riesgos de personal: [Disponibilidad de especialistas]
- Contingencia temporal: [Buffer adicional recomendado]
- Contingencia presupuestal: [Porcentaje extra sugerido]
```

**¿Por qué funciona bien?**
1. **Realista**: Considera restricciones reales de tiempo, recursos y clima
2. **Estructurado**: Divide proyectos complejos en fases manejables
3. **Risk-management**: Identifica y mitiga riesgos antes de que ocurran
4. **Resource-aware**: Optimiza uso de personal, maquinaria y materiales
5. **Trackeable**: Define métricas claras de seguimiento

**Para mejorarlo:**
- Integrar con software de gestión de proyectos
- Añadir análisis de valor ganado (earned value)
- Incluir optimización automática de cronogramas

---

### 43. Telecomunicaciones: Diagnóstico de Red

**Solución sencilla:**

Para identificar y resolver problemas de conectividad y rendimiento de red:

```
Actúa como un ingeniero de redes senior con experiencia en diagnóstico y optimización de infraestructura de telecomunicaciones.

PROBLEMA REPORTADO:
- Síntomas: [Lentitud/Desconexiones/No conecta/Intermitente]
- Usuarios afectados: [Número y ubicación]
- Desde cuándo: [Tiempo de duración del problema]
- Patrones observados: [Horarios, aplicaciones específicas]
- Impacto en el negocio: [Crítico/Alto/Medio/Bajo]

INFORMACIÓN DE RED:
- Topología: [Tipo de red, equipos principales]
- Proveedores de internet: [ISPs y capacidades]
- Equipos de red: [Routers, switches, access points]
- Ancho de banda contratado: [Capacidad total]
- Número de usuarios: [Conectados simultáneamente]

DATOS DE MONITOREO:
- Utilización actual: [Porcentaje de ancho de banda usado]
- Latencia promedio: [ms de respuesta]
- Packet loss: [Porcentaje de paquetes perdidos]
- Jitter: [Variación en latencia]
- Disponibilidad: [Porcentaje de uptime]

APLICACIONES CRÍTICAS:
- VoIP/Video conferencias: [Calidad reportada]
- Aplicaciones empresariales: [ERP, CRM, cloud services]
- Transferencia de archivos: [Velocidades observadas]
- Navegación web: [Tiempo de carga de páginas]
- Backup/sincronización: [Impacto en procesos automáticos]

DIAGNÓSTICO SISTEMÁTICO:
1. Analiza los síntomas vs métricas de red
2. Identifica la capa del problema (física/enlace/red/aplicación)
3. Localiza el punto de falla más probable
4. Evalúa si es problema de capacidad, configuración o hardware
5. Prioriza soluciones por impacto y facilidad de implementación

FORMATO DE RESPUESTA:
Diagnóstico principal: [Problema más probable identificado]
Confianza: [Alta/Media/Baja] en el diagnóstico
Capa afectada: [Física/Enlace/Red/Transporte/Aplicación]
Ubicación del problema: [Dónde está ocurriendo]

Causa raíz probable:
- Primaria: [Explicación técnica del problema]
- Secundarias: [Otras posibles causas]
- Factores contribuyentes: [Qué agrava el problema]

Plan de acción inmediato:
1. [Acción urgente]: [Qué hacer ahora para mitigar]
2. [Verificación]: [Cómo confirmar que funciona]
3. [Monitoreo]: [Qué métricas observar]

Solución definitiva:
- Corto plazo: [Arreglo temporal en días]
- Mediano plazo: [Solución robusta en semanas]
- Largo plazo: [Mejoras de infraestructura]

Herramientas de diagnóstico recomendadas:
- Tests a ejecutar: [Ping, traceroute, bandwidth tests]
- Monitoreo continuo: [Herramientas a implementar]
- Documentación: [Qué registrar para futuro]

Prevención futura:
- Mejoras de configuración: [Cambios recomendados]
- Actualizaciones necesarias: [Hardware/software]
- Protocolos de monitoreo: [Alertas automáticas]
- Capacitación: [Skills necesarios para el equipo]
```

**¿Por qué funciona bien?**
1. **Metodología estructurada**: Sigue un proceso lógico de diagnóstico
2. **Multi-capa**: Analiza problemas desde hardware hasta aplicación
3. **Priorizado**: Separa soluciones urgentes de mejoras a largo plazo
4. **Preventivo**: Incluye medidas para evitar recurrencia
5. **Técnico y práctico**: Combina análisis profundo con acciones concretas

**Para mejorar:**
- Integrar con herramientas de monitoreo automatizado
- Añadir base de conocimiento de problemas comunes
- Incluir análisis predictivo de fallas

---

### 44. Consultoría: Análisis de Procesos

**Solución sencilla:**

Para identificar ineficiencias y optimizar procesos empresariales:

```
Actúa como un consultor senior en optimización de procesos con experiencia en metodologías Lean y Six Sigma.

PROCESO A ANALIZAR:
- Nombre del proceso: [Proceso específico]
- Área/Departamento: [Dónde ocurre]
- Objetivo del proceso: [Para qué existe]
- Frecuencia: [Diario/Semanal/Mensual/Por demanda]
- Volumen: [Cantidad de casos por período]

SITUACIÓN ACTUAL:
- Pasos actuales: [Secuencia de actividades]
- Personas involucradas: [Roles y responsabilidades]
- Sistemas utilizados: [Software, herramientas]
- Tiempo total promedio: [Duración end-to-end]
- Costo por proceso: [Estimado en horas/dinero]

PROBLEMAS IDENTIFICADOS:
- Quejas frecuentes: [De clientes internos/externos]
- Cuellos de botella: [Dónde se ralentiza el proceso]
- Errores comunes: [Qué falla frecuentemente]
- Reprocesos: [Qué se tiene que hacer dos veces]
- Esperas innecesarias: [Tiempos muertos identificados]

MÉTRICAS ACTUALES:
- Tiempo de ciclo: [Tiempo promedio total]
- Tasa de error: [Porcentaje de casos con problemas]
- Satisfacción del usuario: [Score actual]
- Costo operativo: [Gasto mensual en el proceso]
- Capacidad: [Casos máximos por día/semana]

ANÁLISIS REQUERIDO:
1. Mapea el proceso actual paso a paso
2. Identifica actividades que no agregan valor
3. Encuentra duplicaciones y redundancias
4. Evalúa puntos de decisión y aprobaciones
5. Propone un proceso optimizado

FORMATO DE RESPUESTA:
Análisis del estado actual:
- Eficiencia general: [Porcentaje de tiempo productivo]
- Principales desperdicios identificados:
  1. [Tipo de desperdicio]: [Impacto estimado]
  2. [Tipo de desperdicio]: [Impacto estimado]
  3. [Tipo de desperdicio]: [Impacto estimado]

Proceso optimizado propuesto:
Paso 1: [Actividad] - [Responsable] - [Tiempo] - [Sistema/Tool]
Paso 2: [Actividad] - [Responsable] - [Tiempo] - [Sistema/Tool]
[Continuar para todos los pasos]

Mejoras implementadas:
- Eliminaciones: [Pasos removidos y por qué]
- Simplificaciones: [Actividades combinadas o reducidas]
- Automatizaciones: [Procesos que pueden ser automáticos]
- Paralelizaciones: [Actividades que pueden ocurrir simultáneamente]

Impacto esperado:
- Reducción de tiempo: [Porcentaje o minutos/horas]
- Reducción de costos: [Ahorro mensual estimado]
- Mejora en calidad: [Reducción de errores esperada]
- Aumento de capacidad: [Casos adicionales procesables]
- ROI de la mejora: [Tiempo de recuperación]

Plan de implementación:
- Fase 1: [Cambios a probar primero]
- Métricas a monitorear: [KPIs específicos]
- Criterios de éxito: [Cómo medir si funciona]
- Plan de rollback: [Si los cambios no funcionan]
- Timeline: [Duración del test]

Consideraciones a largo plazo: [Impacto en meta-game]
```

**¿Por qué funciona bien?**
1. **Metodología probada**: Usa principios Lean/Six Sigma establecidos
2. **Cuantificado**: Mide mejoras específicas y ROI
3. **Implementable**: Divide mejoras en fases realizables
4. **Sostenible**: Incluye métricas para mantener mejoras
5. **Centrado en valor**: Elimina actividades que no aportan

**Para mejorar:**
- Integrar con herramientas de process mining
- Añadir análisis de variabilidad y capacidad
- Incluir simulación de procesos para validar mejoras

---

### 45. Sector Público: Gestión de Servicios Ciudadanos

**Solución sencilla:**

Para optimizar servicios públicos y mejorar la experiencia ciudadana:

```
Actúa como un especialista en administración pública con experiencia en modernización de servicios gubernamentales y atención ciudadana.

SERVICIO A ANALIZAR:
- Tipo de servicio: [Licencias/Permisos/Beneficios/Trámites]
- Dependencia responsable: [Departamento/Secretaría]
- Número de usuarios: [Cantidad mensual de solicitudes]
- Marco legal: [Leyes y reglamentos aplicables]
- Nivel de servicio: [Municipal/Estatal/Federal]

PROCESO ACTUAL:
- Canales de atención: [Presencial/Online/Telefónico/Mobile]
- Pasos del trámite: [Secuencia actual de actividades]
- Documentos requeridos: [Lista de requisitos]
- Tiempo de respuesta: [Actual vs compromiso legal]
- Costo para el ciudadano: [Tasas y honorarios]

PROBLEMAS IDENTIFICADOS:
- Quejas frecuentes: [Principales motivos de insatisfacción]
- Tiempos de espera: [En oficinas y para resolución]
- Rechazos comunes: [Por qué se niegan solicitudes]
- Duplicación de esfuerzos: [Información pedida múltiples veces]
- Falta de información: [Ciudadanos sin claridad del proceso]

RECURSOS DISPONIBLES:
- Personal actual: [Número y capacitación del equipo]
- Tecnología: [Sistemas actuales, capacidades digitales]
- Presupuesto: [Recursos disponibles para mejoras]
- Marco normativo: [Flexibilidad para cambios]
- Alianzas: [Otras dependencias o proveedores]

ANÁLISIS REQUERIDO:
1. Evalúa la experiencia del ciudadano end-to-end
2. Identifica puntos de fricción y demoras
3. Analiza oportunidades de digitalización
4. Propone mejoras en eficiencia y transparencia
5. Considera impacto en equidad y accesibilidad

FORMATO DE RESPUESTA:
Diagnóstico del servicio:
- Nivel de satisfacción actual: [Score estimado]
- Principales pain points ciudadanos:
  1. [Problema]: [Frecuencia e impacto]
  2. [Problema]: [Frecuencia e impacto]
  3. [Problema]: [Frecuencia e impacto]

- Ineficiencias operativas:
  1. [Proceso ineficiente]: [Costo/tiempo perdido]
  2. [Proceso ineficiente]: [Costo/tiempo perdido]

Propuesta de mejora:
Journey ciudadano optimizado:
1. [Paso mejorado]: [Cómo será la nueva experiencia]
2. [Paso mejorado]: [Cómo será la nueva experiencia]
[Continuar para todo el proceso]

Mejoras específicas:
- Digitalización: [Procesos que pueden ser online]
- Simplificación: [Requisitos que pueden eliminarse]
- Automatización: [Decisiones que pueden ser automáticas]
- Interoperabilidad: [Datos compartidos entre dependencias]
- Transparencia: [Información proactiva al ciudadano]

Plan de implementación:
Fase 1 (0-3 meses): [Mejoras sin inversión tecnológica]
- Mejoras de proceso: [Cambios organizacionales]
- Capacitación: [Training del personal]
- Comunicación: [Información clara a ciudadanos]

Fase 2 (3-12 meses): [Mejoras con tecnología básica]
- Portal web: [Funcionalidades básicas online]
- Sistemas internos: [Mejoras de eficiencia]
- Integración: [Conexión con otros sistemas]

Fase 3 (1-2 años): [Transformación digital completa]
- Servicios móviles: [Apps y interfaces móviles]
- Inteligencia artificial: [Chatbots, procesamiento automático]
- Analytics: [Métricas y mejora continua]

Impacto esperado:
- Reducción tiempo de trámite: [De X a Y días]
- Aumento satisfacción: [Score objetivo]
- Reducción costos operativos: [Ahorro anual]
- Incremento en cobertura: [Más ciudadanos atendidos]
- Transparencia: [Métricas públicas de desempeño]

Métricas de éxito:
- Tiempo promedio de trámite: [Objetivo específico]
- Porcentaje de trámites digitales: [Meta digital]
- Satisfacción ciudadana: [Score NPS objetivo]
- Eficiencia operativa: [Casos por empleado]
- Transparencia: [Información publicada automáticamente]
```

**¿Por qué funciona bien?**
1. **Centrado en el ciudadano**: Prioriza la experiencia del usuario final
2. **Realista**: Considera restricciones normativas y presupuestales
3. **Gradual**: Implementa mejoras en fases alcanzables
4. **Medible**: Define métricas específicas de éxito
5. **Transparente**: Incluye rendición de cuentas y comunicación

**Para mejorar:**
- Integrar con plataformas de gobierno digital
- Añadir análisis de accesibilidad y inclusión
- Incluir métricas de impacto social y económico

---

### 46. Startups: Validación de Modelo de Negocio

**Solución sencilla:**

Para validar la viabilidad de ideas de negocio y productos antes del lanzamiento:

```
Actúa como un mentor de startups experimentado con experiencia en validación de modelos de negocio y metodologías Lean Startup.

IDEA DE NEGOCIO:
- Producto/Servicio: [Descripción clara de la propuesta]
- Problema que resuelve: [Pain point específico]
- Mercado objetivo: [Segmento de clientes]
- Propuesta de valor: [Qué beneficio único ofrece]
- Modelo de ingresos: [Cómo planea generar dinero]

HIPÓTESIS CLAVE:
- Problema: [La gente realmente tiene este problema]
- Solución: [Nuestro producto resuelve el problema]
- Mercado: [Existe suficiente demanda]
- Monetización: [Los clientes pagarán por esto]
- Canales: [Podemos llegar a los clientes eficientemente]

INVESTIGACIÓN INICIAL:
- Competencia directa: [Quién hace algo similar]
- Competencia indirecta: [Soluciones alternativas]
- Tamaño de mercado: [TAM, SAM, SOM estimados]
- Entrevistas a clientes: [Número realizadas y insights]
- Análisis de tendencias: [Crecimiento del sector]

RECURSOS DISPONIBLES:
- Capital inicial: [Monto disponible para validación]
- Equipo: [Fundadores y habilidades]
- Tiempo: [Timeline para validación]
- Red de contactos: [Acceso a clientes potenciales]
- Habilidades técnicas: [Capacidad de desarrollar MVP]

PLAN DE VALIDACIÓN:
1. Define las hipótesis más críticas a probar
2. Diseña experimentos específicos para cada hipótesis
3. Establece métricas de éxito claras
4. Calcula el costo y tiempo de cada experimento
5. Prioriza experimentos por impacto y facilidad

FORMATO DE RESPUESTA:
Evaluación inicial:
- Fortaleza de la propuesta: [Alta/Media/Baja]
- Riesgos principales identificados:
  1. [Riesgo]: [Probabilidad e impacto]
  2. [Riesgo]: [Probabilidad e impacto]
  3. [Riesgo]: [Probabilidad e impacto]

Hipótesis prioritarias a validar:
1. [Hipótesis más crítica]: [Por qué es crucial]
2. [Segunda hipótesis]: [Por qué es importante]
3. [Tercera hipótesis]: [Por qué debe probarse]

Plan de experimentos:
Experimento 1: [Nombre del test]
- Objetivo: [Qué hipótesis valida]
- Método: [Cómo se ejecutará]
- Métricas: [Qué se medirá]
- Criterio de éxito: [Número específico para considerar exitoso]
- Duración: [Tiempo estimado]
- Costo: [Recursos necesarios]

[Repetir para cada experimento]

MVP recomendado:
- Tipo: [Landing page/Prototipo/Wizard of Oz/Concierge]
- Características mínimas: [Funcionalidades esenciales]
- Tiempo de desarrollo: [Estimado]
- Costo de desarrollo: [Presupuesto necesario]
- Métricas a trackear: [KPIs específicos]

Roadmap de validación:
Semana 1-2: [Actividades iniciales]
Semana 3-4: [Experimentos rápidos]
Semana 5-8: [Desarrollo y test de MVP]
Semana 9-12: [Análisis y iteración]

Criterios de decisión:
- Go/No-go: [Métricas específicas para continuar]
- Pivot indicators: [Cuándo cambiar dirección]
- Scale indicators: [Cuándo acelerar]

Next steps inmediatos:
1. [Acción específica]: [Timeline]
2. [Acción específica]: [Timeline]
3. [Acción específica]: [Timeline]
```

**¿Por qué funciona bien?**
1. **Risk-driven**: Identifica y prueba los riesgos más grandes primero
2. **Costo-efectivo**: Usa experimentos baratos antes de inversiones grandes
3. **Data-driven**: Define métricas específicas de éxito/fracaso
4. **Iterativo**: Permite pivots basados en aprendizajes
5. **Práctico**: Da next steps concretos y realizables

**Para mejorar:**
- Integrar con herramientas de customer development
- Añadir análisis de unit economics temprano
- Incluir frameworks de product-market fit

---

### 47. Pharma: Desarrollo de Medicamentos

**Solución sencilla:**

Para optimizar procesos de investigación y desarrollo farmacéutico:

```
Actúa como un especialista en desarrollo farmacéutico con experiencia en investigación clínica y regulaciones sanitarias.

COMPUESTO EN DESARROLLO:
- Tipo de medicamento: [Preventivo/Curativo/Paliativo]
- Indicación terapéutica: [Enfermedad o condición específica]
- Mecanismo de acción: [Cómo funciona el medicamento]
- Fase actual: [Preclínica/Fase I/II/III]
- Población objetivo: [Características de pacientes]

DATOS CIENTÍFICOS:
- Eficacia demostrada: [Resultados en estudios previos]
- Perfil de seguridad: [Efectos adversos conocidos]
- Comparación con estándar: [Vs tratamientos actuales]
- Ventajas diferenciales: [Qué lo hace único]
- Limitaciones conocidas: [Contraindicaciones, restricciones]

CONTEXTO REGULATORIO:
- Región objetivo: [FDA/EMA/COFEPRIS/Otros]
- Designaciones especiales: [Orphan drug/Fast track/Breakthrough]
- Guías aplicables: [Guidelines relevantes]
- Precedentes regulatorios: [Productos similares aprobados]
- Timeline regulatorio: [Tiempo estimado para aprobación]

RECURSOS DISPONIBLES:
- Presupuesto: [Fondos disponibles para desarrollo]
- Equipo científico: [Investigadores y especialistas]
- Facilities: [Laboratorios, manufacturing capabilities]
- Partners: [CROs, instituciones académicas]
- Propiedad intelectual: [Patentes y exclusividades]

ANÁLISIS REQUERIDO:
1. Evalúa la viabilidad científica y comercial
2. Identifica riesgos críticos en el desarrollo
3. Optimiza el diseño de estudios clínicos
4. Planifica la estrategia regulatoria
5. Estima timeline y recursos necesarios

FORMATO DE RESPUESTA:
Evaluación del potencial:
- Probabilidad de éxito técnico: [Porcentaje basado en datos]
- Potencial comercial: [Peak sales estimadas]
- Ventaja competitiva: [Qué tan diferenciado está]
- Urgencia médica: [Necesidad no cubierta]

Estrategia de desarrollo:
Fase actual - Objetivos:
- Endpoints primarios: [Qué se debe demostrar]
- Diseño de estudio: [Tipo, duración, pacientes]
- Criterios de éxito: [Métricas específicas]
- Plan de mitigación: [Para riesgos identificados]

Próximas fases:
Fase [X] ([Timeline]):
- Objetivos: [Qué se busca probar]
- Diseño propuesto: [Estructura del estudio]
- Población: [Tipo y número de pacientes]
- Duración: [Tiempo de seguimiento]
- Endpoints: [Primarios y secundarios]

Plan regulatorio:
- Meetings recomendados: [Con autoridades sanitarias]
- Documentos clave: [IND, CTA, Briefing packages]
- Timeline submission: [Cuándo enviar cada documento]
- Estrategia de aprobación: [Regular/Expedited pathways]

Risk assessment:
Riesgos técnicos:
1. [Riesgo]: [Probabilidad] - [Impacto] - [Mitigación]
2. [Riesgo]: [Probabilidad] - [Impacto] - [Mitigación]

Riesgos regulatorios:
1. [Riesgo]: [Probabilidad] - [Impacto] - [Mitigación]
2. [Riesgo]: [Probabilidad] - [Impacto] - [Mitigación]

Riesgos comerciales:
1. [Riesgo]: [Probabilidad] - [Impacto] - [Mitigación]

Inversión requerida:
- Costo fase actual: [Presupuesto específico]
- Costo desarrollo completo: [Hasta aprobación]
- Timeline total: [Años hasta market]
- NPV estimado: [Valor presente neto]
- Peak sales projection: [Ventas máximas anuales]

Decisiones críticas próximas:
- Go/No-go gates: [Criterios específicos de decisión]
- Timeline de decisiones: [Cuándo evaluar cada gate]
- Métricas de seguimiento: [KPIs del desarrollo]

Recomendaciones estratégicas:
1. [Recomendación específica]: [Justificación]
2. [Recomendación específica]: [Justificación]
3. [Recomendación específica]: [Justificación]
```

**¿Por qué funciona bien?**
1. **Risk-based**: Identifica y mitiga riesgos en múltiples dimensiones
2. **Evidence-based**: Se basa en datos científicos y precedentes
3. **Regulatorio-aware**: Considera requerimientos de autoridades
4. **Comercialmente viable**: Evalúa potencial de mercado
5. **Resource-optimized**: Optimiza uso de tiempo y capital

**Para mejorar:**
- Integrar con bases de datos de desarrollo farmacéutico
- Añadir modelos predictivos de éxito clínico
- Incluir análisis de landscape competitivo dinámico

---

### 48. Aerospace: Mantenimiento Predictivo

**Solución sencilla:**

Para predecir fallas en componentes aeroespaciales y optimizar mantenimiento:

```
Actúa como un ingeniero aeroespacial especializado en mantenimiento predictivo y análisis de confiabilidad de sistemas críticos.

COMPONENTE/SISTEMA:
- Tipo: [Motor/Aviónica/Estructura/Hidráulico/Eléctrico]
- Aeronave: [Modelo específico, tipo de operación]
- Criticidad: [Crítico para vuelo/Importante/Secundario]
- Antigüedad: [Años de servicio, horas de vuelo]
- Ambiente operativo: [Comercial/Militar/Carga]

DATOS DE OPERACIÓN:
- Horas de vuelo actuales: [Total acumuladas]
- Ciclos de operación: [Número de aterrizajes/despegues]
- Condiciones de uso: [Rutas típicas, altitudes]
- Historial de mantenimiento: [Últimas intervenciones]
- Modificaciones: [Upgrades o cambios realizados]

MONITOREO ACTUAL:
- Sensores instalados: [Tipo y ubicación]
- Parámetros monitoreados: [Temperatura, vibración, presión]
- Frecuencia de datos: [Cada segundo/minuto/vuelo]
- Alertas actuales: [Warnings o cautions activos]
- Tendencias observadas: [Cambios en parámetros]

DATOS HISTÓRICOS:
- Fallas previas: [Tipo, frecuencia, causas raíz]
- Patrones estacionales: [Variaciones por clima/uso]
- Componentes relacionados: [Sistemas que interactúan]
- Mantenimiento preventivo: [Intervalos y efectividad]
- Benchmarking: [Comparación con flota similar]

ANÁLISIS PREDICTIVO:
1. Analiza tendencias en parámetros clave
2. Identifica patrones pre-falla
3. Calcula la probabilidad de falla en diferentes horizontes
4. Optimiza los intervalos de mantenimiento
5. Prioriza acciones según riesgo y costo

FORMATO DE RESPUESTA:
Estado actual del componente:
- Condición general: [Excelente/Buena/Regular/Deficiente]
- Vida útil remanente: [Horas/ciclos estimados]
- Confiabilidad actual: [Porcentaje para próximos 100 vuelos]
- Parámetros en alerta: [Fuera de rango normal]

Predicción de fallas:
Probabilidad de falla en:
- Próximos 100 vuelos: [Porcentaje]
- Próximos 500 vuelos: [Porcentaje]
- Próximos 1000 vuelos: [Porcentaje]

Tipo de falla más probable: [Modo de falla específico]
Indicadores tempranos: [Qué monitorear de cerca]
Consecuencias de falla: [Impacto en seguridad/operación]

Plan de mantenimiento optimizado:
Mantenimiento inmediato (0-50 vuelos):
- [Acción específica]: [Descripción y justificación]
- [Acción específica]: [Descripción y justificación]

Mantenimiento programado (50-200 vuelos):
- [Acción específica]: [Descripción y justificación]
- [Acción específica]: [Descripción y justificación]

Reemplazo recomendado: [Timeline basado en análisis]

Optimización de monitoreo:
- Parámetros críticos: [Qué observar más frecuentemente]
- Umbrales de alerta: [Valores específicos para intervenir]
- Frecuencia de inspección: [Cada X vuelos/horas]
- Herramientas adicionales: [Equipos de diagnóstico recomendados]

Análisis costo-beneficio:
- Costo de mantenimiento predictivo: [Inversión en monitoreo]
- Ahorro vs mantenimiento programado: [Diferencia anual]
- Reducción de AOG (Aircraft on Ground): [Horas evitadas]
- Mejora en disponibilidad: [Porcentaje adicional]
- ROI del programa: [Tiempo de recuperación]

Recomendaciones estratégicas:
Corto plazo (1-3 meses):
- [Acción específica]: [Impacto esperado]

Mediano plazo (3-12 meses):
- [Mejora de sistema]: [Beneficio a largo plazo]

Largo plazo (1-3 años):
- [Actualización tecnológica]: [Capacidades mejoradas]

Alertas críticas:
- Condiciones para grounding inmediato: [Límites absolutos]
- Escalation matrix: [A quién alertar cuándo]
- Procedimientos de emergencia: [Si falla durante vuelo]
```

**¿Por qué funciona bien?**
1. **Safety-first**: Prioriza la seguridad operacional sobre todo
2. **Data-driven**: Usa datos históricos y en tiempo real
3. **Cost-optimized**: Balancea costo de mantenimiento vs riesgo
4. **Predictivo**: Anticipa problemas antes de que ocurran
5. **Regulatorio-compliant**: Cumple con estándares aeronáuticos

**Para mejorar:**
- Integrar con sistemas de health monitoring avanzados
- Añadir machine learning para detección de patrones complejos
- Incluir análisis de degradación de múltiples componentes

---

### 49. Biotecnología: Análisis Genómico

**Solución sencilla:**

Para interpretar datos genómicos y proporcionar insights clínicos:

```
Actúa como un bioinformático especializado en genómica clínica con experiencia en interpretación de variantes genéticas y medicina personalizada.

INFORMACIÓN DEL PACIENTE:
- Edad: [Años]
- Sexo: [Masculino/Femenino]
- Etnia: [Background genético]
- Historia clínica: [Condiciones médicas relevantes]
- Historia familiar: [Enfermedades genéticas conocidas]
- Indicación para test: [Por qué se solicitó el análisis]

DATOS GENÓMICOS:
- Tipo de test: [WGS/WES/Panel específico/Farmacogenómica]
- Plataforma: [Tecnología de secuenciación usada]
- Cobertura: [Profundidad promedio de lectura]
- Calidad: [Score de calidad del secuenciamiento]
- Genes analizados: [Panel específico o genoma completo]

VARIANTES IDENTIFICADAS:
- Variantes patogénicas: [Número y localización]
- Variantes de significado incierto: [VUS count]
- Variantes farmacogenómicas: [Relevantes para medicamentos]
- Variantes portadoras: [Para enfermedades recesivas]
- Variantes de riesgo: [Para enfermedades complejas]

BASES DE DATOS CONSULTADAS:
- ClinVar: [Clasificaciones conocidas]
- OMIM: [Asociaciones enfermedad-gen]
- PharmGKB: [Información farmacogenómica]
- Population databases: [Frecuencias poblacionales]
- Literature: [Evidencia científica actualizada]

ANÁLISIS REQUERIDO:
1. Clasifica variantes según guidelines ACMG
2. Evalúa relevancia clínica para el fenotipo
3. Identifica implicaciones farmacogenómicas
4. Assess riesgo para familiares
5. Proporciona recomendaciones clínicas

FORMATO DE RESPUESTA:
Resumen ejecutivo:
- Hallazgos principales: [1-2 variantes más relevantes]
- Relevancia clínica: [Alta/Media/Baja para condición actual]
- Recomendaciones inmediatas: [Acciones a tomar]

Variantes patogénicas/probablemente patogénicas:
Variante 1: [Nomenclatura HGVS]
- Gen: [Símbolo del gen]
- Condición asociada: [Enfermedad específica]
- Herencia: [Patrón de herencia]
- Penetrancia: [Porcentaje de expresión]
- Edad de inicio típica: [Rango de edad]
- Manifestaciones clínicas: [Síntomas esperados]
- Manejo recomendado: [Seguimiento clínico]

[Repetir para otras variantes relevantes]

Farmacogenómica:
- Metabolizador CYP2D6: [Status - Normal/Pobre/Rápido/Ultrarrápido]
- Implicaciones: [Medicamentos afectados]
- Recomendaciones de dosificación: [Ajustes específicos]
- Medicamentos a evitar: [Contraindicaciones genéticas]

Variantes de significado incierto (VUS):
- Número total: [Cantidad]
- Más relevantes clínicamente: [2-3 variantes]
- Recomendación: [Seguimiento/Reclasificación futura]

Screening familiar:
- Familiares en riesgo: [Quiénes deberían ser evaluados]
- Probabilidades de herencia: [Porcentajes específicos]
- Timing recomendado: [Cuándo hacer screening]
- Consejería genética: [Recomendada/Obligatoria]

Prevención y manejo:
Recomendaciones inmediatas:
1. [Acción específica]: [Justificación genética]
2. [Acción específica]: [Justificación genética]

Seguimiento a largo plazo:
- Evaluaciones periódicas: [Qué exámenes, cada cuánto]
- Signos de alerta: [Síntomas a monitorear]
- Especialistas recomendados: [Referencias necesarias]

Limitaciones del estudio:
- Regiones no cubiertas: [Limitaciones técnicas]
- Variantes estructurales: [No detectadas]
- Mosaicismo: [Posibilidad de false negatives]
- Reinterpretación: [Necesidad de updates futuros]

Recomendaciones adicionales:
- Tests complementarios: [Si son necesarios]
- Estudios funcionales: [Para confirmar patogenicidad]
- Participación en registros: [Para investigación]
- Seguimiento de literatura: [Updates en evidencia]
```

**¿Por qué funciona bien?**
1. **Evidence-based**: Usa bases de datos y literatura científica actualizada
2. **Clinically relevant**: Enfoca en implicaciones médicas prácticas
3. **Family-oriented**: Considera implicaciones para familiares
4. **Actionable**: Proporciona recomendaciones específicas de manejo
5. **Transparent**: Reconoce limitaciones y incertidumbres

**Para mejorar:**
- Integrar con registros clínicos electrónicos
- Añadir análisis de polygenic risk scores
- Incluir predicciones de respuesta a terapias específicas

---

### 50. Seguros: Evaluación de Riesgos

**Solución sencilla:**

Para evaluar y preciar riesgos en diferentes líneas de seguros:

```
Actúa como un actuario senior con experiencia en suscripción y evaluación de riesgos en múltiples líneas de seguros.

TIPO DE PÓLIZA:
- Línea de seguro: [Vida/Auto/Hogar/Salud/Comercial]
- Cobertura solicitada: [Monto y tipo de protección]
- Vigencia: [Período de cobertura]
- Deducible: [Monto propuesto]
- Territorio: [Ubicación geográfica]

INFORMACIÓN DEL ASEGURADO:
- Perfil demográfico: [Edad, género, ocupación]
- Historial de reclamaciones: [Últimos 5 años]
- Score crediticio: [Si aplica y está disponible]
- Estilo de vida: [Factores de riesgo relevantes]
- Experiencia previa: [Años como asegurado]

FACTORES DE RIESGO ESPECÍFICOS:
[Para Auto: Historial de manejo, tipo de vehículo, uso]
[Para Vida: Salud, hábitos, actividades de riesgo]
[Para Hogar: Construcción, ubicación, protecciones]
[Para Salud: Condiciones preexistentes, age, lifestyle]
[Para Comercial: Industria, tamaño, exposiciones]

DATOS DE MERCADO:
- Pérdidas promedio sector: [Por tipo de riesgo]
- Tendencias de siniestralidad: [Últimos 3 años]
- Competencia: [Precios de mercado]
- Regulaciones: [Restricciones o requerimientos]
- Catástrofes recientes: [Impacto en la zona]

ANÁLISIS REQUERIDO:
1. Evalúa la probabilidad de siniestro
2. Estima la severidad esperada de pérdidas
3. Calcula la prima técnica necesaria
4. Considera factores de selección adversa
5. Determina términos y condiciones apropiados

FORMATO DE RESPUESTA:
Clasificación de riesgo:
- Categoría: [Estándar/Preferido/Subestándar/Decline]
- Score de riesgo: [1-100 scale]
- Justificación: [Principales factores que influyen en la clasificación]

Análisis actuarial:
- Frecuencia esperada: [Siniestros por año]
- Severidad promedio: [Costo promedio por siniestro]
- Pérdida máxima probable: [PML en percentil 99]
- Exposición total: [Máxima pérdida posible]

Factores de riesgo identificados:
Factores positivos (reducen riesgo):
1. [Factor]: [Impacto en %]
2. [Factor]: [Impacto en %]

Factores negativos (aumentan riesgo):
1. [Factor]: [Impacto en %]
2. [Factor]: [Impacto en %]

Cálculo de prima:
- Prima base técnica: $[Monto]
- Ajustes por factores de riesgo: [+/-] $[Monto]
- Gastos de administración: [%] $[Monto]
- Utilidad objetivo: [%] $[Monto]
- Prima comercial recomendada: $[Monto final]

Términos y condiciones:
- Deducible recomendado: $[Monto]
- Exclusiones especiales: [Si aplican]
- Límites sublímites: [Restricciones específicas]
- Coaseguro requerido: [Si aplica]
- Inspecciones: [Requerimientos especiales]

Recomendaciones de suscripción:
Decisión: [Aceptar/Condicionar/Declinar]

Si acepta:
- Condiciones especiales: [Requerimientos]
- Monitoreo requerido: [Seguimiento periódico]
- Renovación: [Consideraciones futuras]

Si condiciona:
- Mejoras requeridas: [Qué debe cambiar]
- Timeline: [Cuándo implementar]
- Re-evaluación: [Criterios para aceptación]

Análisis de rentabilidad:
- Combined ratio esperado: [%]
- Margen de utilidad: [%]
- Período de payback: [Meses]
- Valor presente neto: [NPV a 5 años]

Gestión de cartera:
- Concentración por zona: [Impacto en portfolio]
- Correlación con otros riesgos: [Dependencias]
- Necesidad de reaseguro: [Si aplica]
- Límites de retención: [Máximo a retener]

Monitoreo y triggers:
- Alertas de renovación: [Cambios que requieren revisión]
- Métricas de seguimiento: [KPIs específicos]
- Frecuencia de revisión: [Cada cuánto re-evaluar]
```

**¿Por qué funciona bien?**
1. **Actuarialmente sólido**: Usa métodos estadísticos probados
2. **Risk-adjusted**: Precifica apropiadamente cada riesgo específico
3. **Market-aware**: Considera competencia y regulaciones
4. **Portfolio-conscious**: Ve el impacto en toda la cartera
5. **Dinámico**: Incluye monitoreo y ajustes futuros

**Para mejorar:**
- Integrar con modelos predictivos avanzados
- Añadir análisis de riesgos emergentes (cyber, clima)
- Incluir optimización de portfolio en tiempo real
