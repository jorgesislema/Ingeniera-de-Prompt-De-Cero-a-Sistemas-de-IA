# Resolución de Problemas y Diagnóstico de Prompts

## Introducción

Esta guía sistemática para la resolución de problemas de prompts está basada en metodologías de ingeniería de software aplicadas específicamente a la ingeniería de prompts. Proporciona marcos estructurados para diagnosticar, aislar y resolver problemas de prompts en entornos de producción.

---

## Marco de Diagnóstico Sistemático

### Metodología de 5 Capas

**Capa 1: Análisis de Síntomas (¿Qué está pasando?)**
**Capa 2: Validación de Entrada (¿El problema está en los datos?)**
**Capa 3: Validación de Lógica (¿El prompt está bien diseñado?)**
**Capa 4: Análisis de Contexto (¿Hay factores externos?)**
**Capa 5: Validación de Salida (¿El resultado es correcto pero no útil?)**

---

## Capa 1: Análisis de Síntomas

### Marco de Preguntas de Diagnóstico

**1.1 Problemas de Calidad de Salida**

¿Cuál es exactamente el problema con la salida?

**Lista de Síntomas:**
- [ ] **Inconsistencia:** Salidas diferentes para entradas similares
- [ ] **Incompletitud:** Falta información esperada
- [ ] **Irrelevancia:** Salida correcta pero no útil para el caso
- [ ] **Incorrectitud:** Errores de hechos o lógica detectables
- [ ] **Formato:** Estructura no cumple especificaciones
- [ ] **Tono:** Estilo inapropiado para el contexto

**Script de Diagnóstico:**
```
# PLANTILLA DE CARACTERIZACIÓN DE SÍNTOMAS

## Descripción del Problema
**Síntoma Principal:** [Descripción específica]
**Frecuencia:** [Siempre/A veces/Raramente] - [X]% de ejecuciones
**Impacto en Usuario:** [Bloquea flujo de trabajo/Requiere corrección manual/Molestia menor]
**Impacto en Negocio:** [Alto/Medio/Bajo] - [Cuantificado si es posible]

## Análisis de Variabilidad
**Consistente entre usuarios:** [Sí/No/Parcialmente]
**Consistente en el tiempo:** [Sí/No/Patrones observados]
**Consistente entre tipos de entrada:** [Sí/No/Tipos específicos afectados]

## Casos de Ejemplo
### Ejemplo Funcional (si existe):
Entrada: [Entrada exacta]
Salida Esperada: [Qué debería ocurrir]
Salida Real: [Qué ocurrió realmente]

### Ejemplo Fallido:
Entrada: [Entrada exacta]
Salida Esperada: [Qué debería ocurrir] 
Salida Real: [Qué ocurrió realmente]
Análisis de Diferencias: [Diferencias específicas]
```

**1.2 Problemas de Rendimiento**

**Problemas de Velocidad/Latencia:**
- Tiempo de respuesta >umbral esperado
- Timeouts o respuestas colgadas
- Tiempos de respuesta inconsistentes

**Uso de Recursos:**
- Consumo de tokens mayor al esperado
- Disparos de límite de tasa
- Escalada de costos

**Métricas de Diagnóstico:**
```
# PLANTILLA DE DIAGNÓSTICO DE RENDIMIENTO

## Análisis de Tiempo de Respuesta
Promedio: [X] segundos (vs esperado [Y] segundos)
P95: [X] segundos
P99: [X] segundos
Tasa de timeout: [X]%

## Análisis de Uso de Tokens
Tokens promedio por solicitud: [X] (vs esperado [Y])
Tokens de entrada: [X] | Tokens de salida: [Y]
Costo por solicitud: $[X]

## Análisis de Tasa de Error
Tasa de éxito: [X]%
Tipos de error:
- Timeout: [X]%
- Límite de tasa: [X]%
- Error del modelo: [X]%
- Respuesta inválida: [X]%
```

---

## Capa 2: Validación de Entrada

### 2.1 Evaluación de Calidad de Datos

**Lista de Datos de Entrada:**
```
# MARCO DE VALIDACIÓN DE ENTRADA

## Verificación de Completitud
- [ ] Todos los campos requeridos presentes
- [ ] Sin valores nulos/vacíos inesperados
- [ ] Datos dentro de rangos esperados
- [ ] Integridad referencial mantenida

## Validación de Formato
- [ ] Tipos de datos coinciden con especificaciones
- [ ] Codificación (UTF-8, etc.) correcta
- [ ] Delimitadores y estructura consistentes
- [ ] Caracteres especiales manejados correctamente

## Calidad de Contenido
- [ ] Sin datos truncados
- [ ] Consistencia lógica dentro del conjunto de datos
- [ ] Consistencia temporal (fechas, secuencias)
- [ ] Reglas de validación específicas del dominio cumplidas
```

**Proceso de Diagnóstico:**
```
# PASO 1: Aislar Variables de Entrada
Probar con:
1. Entrada mínima viable (caso más simple que debería funcionar)
2. Entrada buena conocida (ejemplo que funcionó previamente)
3. Variación sistemática (cambiar un elemento a la vez)

# PASO 2: Pruebas de Límites de Entrada
- Entradas de tamaño máximo
- Entradas de tamaño mínimo
- Casos límite (cadenas vacías, caracteres especiales)
- Caracteres internacionales e idiomas

# PASO 3: Verificación de Linaje de Datos
- Rastrear fuente de datos y transformaciones
- Verificar que no haya corrupción en el pipeline
- Validar pasos de preprocesamiento
- Verificar cambios recientes en sistemas previos
```

### 2.2 Problemas de Carga de Contexto

**Validación de Contexto:**
```
# AUDITORÍA DE COMPLETITUD DE CONTEXTO

## Elementos Esenciales de Contexto
- [ ] Rol/persona del usuario claramente definido
- [ ] Objetivo de negocio explícito
- [ ] Restricciones y limitaciones especificadas
- [ ] Criterios de éxito establecidos
- [ ] Conocimiento de dominio proporcionado

## Verificaciones de Calidad de Contexto
- [ ] La información es actual y relevante
- [ ] Sin información conflictiva
- [ ] Nivel de detalle apropiado
- [ ] Contexto cultural/lingüístico incluido
- [ ] Contexto histórico cuando sea relevante

## Análisis de Tamaño de Contexto
Tokens totales de contexto: [X]
Utilización de ventana de contexto: [X]%
Ubicación de info crítica: [Inicio/Medio/Final]
Densidad de información: [Alta/Media/Baja]
```

---

## Capa 3: Validación de Lógica

### 3.1 Análisis de Estructura del Prompt

**Evaluación de Flujo Lógico:**
```
# PLANTILLA DE AUDITORÍA DE LÓGICA DE PROMPT

## Análisis de Definición de Rol
**Puntuación de Claridad (1-10):** [X]
- ¿Es el rol lo suficientemente específico? [Sí/No]
- ¿Coincide con la complejidad de la tarea? [Sí/No]
- ¿Son realistas los requisitos de experiencia? [Sí/No]

## Análisis de Secuencia de Instrucciones
**Puntuación de Flujo Lógico (1-10):** [X]
Evaluación paso a paso:
1. [Instrucción] → Verificación lógica: [Válida/Inválida/Ambigua]
2. [Instrucción] → Verificación lógica: [Válida/Inválida/Ambigua]
3. [Instrucción] → Dependencias: [Paso anterior/Independiente]

## Análisis de Restricciones
**Completitud de Restricciones (1-10):** [X]
- Restricciones técnicas correctamente especificadas
- Restricciones de negocio incluidas
- Limitaciones de recursos reconocidas
- Requisitos de calidad definidos

## Análisis de Especificación de Salida
**Puntuación de Especificidad (1-10):** [X]
- Requisitos de formato claros
- Expectativas de contenido específicas
- Criterios de éxito medibles
- Ejemplos proporcionados cuando es necesario
```

### 3.2 Validación de Cadena de Razonamiento

**Para Prompts Complejos de Múltiples Pasos:**
```
# DIAGNÓSTICO DE CADENA DE RAZONAMIENTO

## Análisis de Dependencia de Pasos
Mapear cada paso lógico:
Paso 1: [Descripción] → Depende de: [Entrada/Suposición]
Paso 2: [Descripción] → Depende de: [Salida del Paso 1/Entrada adicional]
Paso N: [Descripción] → Depende de: [Pasos anteriores/Factores externos]

## Identificación de Brechas Lógicas
- Pasos faltantes en la cadena de razonamiento
- Saltos lógicos sin soporte
- Dependencias circulares
- Requisitos imposibles o contradictorios

## Análisis de Rutas Alternativas
- ¿Qué pasa si el Paso X falla?
- ¿Hay rutas de razonamiento alternativas?
- ¿Qué tan robusta es la lógica ante variaciones?
```

---

## Capa 4: Análisis de Contexto

### 4.1 Factores Ambientales

**Evaluación de Variables Externas:**
```
# AUDITORÍA DE CONTEXTO AMBIENTAL

## Factores Específicos del Modelo
Versión del modelo: [Versión específica]
Actualizaciones recientes del modelo: [Fecha de última actualización]
Limitaciones conocidas del modelo: [Problemas documentados]
Mejores prácticas específicas del modelo: [Verificación de cumplimiento]

## API e Infraestructura
Endpoint de API: [URL/Versión]
Estado de limitación de tasa: [Límites actuales y uso]
Salud del servicio: [Problemas conocidos]
Diferencias regionales: [Si se usan endpoints diferentes]

## Factores Temporales
Hora del día: [Períodos de uso pico/fuera de pico]
Día de la semana: [Patrones en rendimiento del modelo]
Cambios recientes: [Cambios de sistema/prompt en últimos 30 días]
Consideraciones estacionales: [Si es relevante para el contenido]
```

### 4.2 Variabilidad de Usuario y Contexto

**Análisis de Comportamiento del Usuario:**
```
# DIAGNÓSTICO DE CONTEXTO DE USUARIO

## Nivel de Experiencia del Usuario
Conocimiento del dominio: [Experto/Intermedio/Novato]
Familiaridad con IA: [Alta/Media/Baja]
Estilo de interacción esperado: [Técnico/Negocios/Informal]

## Variación de Caso de Uso
Caso de uso principal: [Según diseño]
Caso de uso real: [Cómo se está usando realmente]
Desviación de caso de uso: [Evolución en el tiempo]
Frecuencia de casos límite: [Qué tan frecuentes son solicitudes inusuales]

## Contexto Organizacional
Especificidades de la industria: [Regulaciones/estándares relevantes]
Cultura empresarial: [Comunicación formal/informal]
Expectativas de interesados: [Profundidad técnica, cronograma, formato]
Requisitos de integración: [Cómo se usará la salida]
```

---

## Capa 5: Validación de Salida

### 5.1 Marco de Calidad de Respuesta

**Evaluación de Calidad Multidimensional:**
```
# TARJETA DE PUNTUACIÓN DE CALIDAD DE SALIDA

## Precisión (Peso: 30%)
Corrección factual: [Puntuación 1-10]
- Hechos verificables: [Correctos/Incorrectos/No verificables]
- Consistencia lógica: [Consistente/Problemas menores/Problemas mayores]
- Demostración de experiencia en dominio: [Experto/Competente/Deficiente]

## Completitud (Peso: 25%)
Cumplimiento de requisitos: [Puntuación 1-10]
- Todos los elementos solicitados presentes: [Sí/Parcial/No]
- Profundidad apropiada: [Muy superficial/Correcta/Demasiado profunda]
- Información crítica faltante: [Ninguna/Menor/Mayor]

## Relevancia (Peso: 25%)
Alineación con la tarea: [Puntuación 1-10]
- Aborda la pregunta central: [Directamente/Parcialmente/Fuera de tema]
- Apropiado para la audiencia: [Sí/Algo/No]
- Accionable para el uso previsto: [Altamente/Moderadamente/No]

## Usabilidad (Peso: 20%)
Utilidad práctica: [Puntuación 1-10]
- El formato apoya el uso previsto: [Perfecto/Bueno/Pobre]
- Claridad del lenguaje: [Cristalina/Clara/Poco clara]
- Guía de implementación: [Detallada/Adecuada/Insuficiente]
```

### 5.2 Análisis de Consistencia de Salida

**Protocolo de Pruebas de Consistencia:**
```
# PLANTILLA DE VALIDACIÓN DE CONSISTENCIA

## Prueba de Reproducibilidad
Ejecución 1: [Fecha/Hora] → Salida: [Resumen/Hash]
Ejecución 2: [Fecha/Hora] → Salida: [Resumen/Hash]
Ejecución 3: [Fecha/Hora] → Salida: [Resumen/Hash]

Puntuación de Consistencia: [X]% de similitud
Análisis de Varianza:
- Diferencias de contenido: [Significativas/Menores/Ninguna]
- Diferencias de formato: [Significativas/Menores/Ninguna]
- Diferencias de calidad: [Significativas/Menores/Ninguna]

## Consistencia Entre Usuarios
Usuario A (Experto): [Resumen de salida]
Usuario B (Intermedio): [Resumen de salida]
Usuario C (Novato): [Resumen de salida]

Consistencia entre usuarios: [Alta/Media/Baja]
Variaciones dependientes de experiencia: [Esperadas/Inesperadas]

## Consistencia Temporal
Semana 1 calidad promedio: [Puntuación]
Semana 2 calidad promedio: [Puntuación]
Semana 3 calidad promedio: [Puntuación]

Tendencia: [Mejorando/Estable/Degradando]
Cambios significativos: [Fecha y descripción]
```

---

## Manuales de Resolución de Problemas

### Manual 1: "Calidad de Salida Inconsistente"

**Síntomas:** El mismo prompt produce resultados de calidad variable
**Sospechosos Principales:** Configuración de temperatura, carga de contexto, variaciones de entrada

**Pasos de Diagnóstico:**
1. **Aislar Variables**
   ```
   # Probar con entradas idénticas múltiples veces
   # Documentar configuración de temperatura y muestreo
   # Verificar elementos aleatorios en el prompt
   ```

2. **Análisis de Contexto**
   ```
   # Verificar que la ventana de contexto no se esté truncando
   # Verificar contexto dinámico que cambia
   # Validar relevancia y precisión del contexto
   ```

3. **Optimización de Parámetros**
   ```
   # Probar temperatura: 0.0 → 0.3 → 0.7 → 1.0
   # Probar valores top_p: 0.1 → 0.5 → 0.9
   # Documentar compensaciones calidad vs. creatividad
   ```

**Marco de Solución:**
```
SI inconsistencia > 20% de varianza de calidad:
  → Bajar temperatura a 0.2 o menos
  → Agregar restricciones más específicas
  → Incluir ejemplos de calidad en el prompt

SI se desea varianza creativa pero se debe mantener calidad:
  → Usar temperatura 0.5-0.7 con requisitos de formato estrictos
  → Agregar pasos de validación de calidad en el prompt
  → Implementar validación de post-procesamiento
```

### Manual 2: "Tiempos de Respuesta Lentos"

**Síntomas:** Tiempos de respuesta significativamente por encima de la línea base
**Sospechosos Principales:** Conteo de tokens, elección de modelo, problemas de API

**Pasos de Diagnóstico:**
1. **Análisis de Tokens**
   ```
   # Medir conteo de tokens de entrada
   # Analizar requisitos de tokens de salida
   # Verificar verbosidad innecesaria en el prompt
   ```

2. **Optimización del Prompt**
   ```
   # Eliminar información redundante
   # Comprimir contexto sin perder significado
   # Usar viñetas vs. párrafos donde sea posible
   ```

3. **Verificación de Infraestructura**
   ```
   # Probar desde diferentes ubicaciones geográficas
   # Verificar estado de API y problemas conocidos
   # Monitorear limitación de tasa
   ```

**Marco de Solución:**
```
SI conteo de tokens > 8K:
  → Comprimir contexto e instrucciones
  → Dividir en múltiples prompts más pequeños
  → Usar resumen para entradas grandes

SI problemas de infraestructura:
  → Implementar lógica de reintento con retroceso exponencial
  → Agregar encolado de solicitudes para períodos de alto tráfico
  → Considerar endpoints o modelos alternativos
```

### Manual 3: "Salida Incorrecta o Irrelevante"

**Síntomas:** La salida tiene buen formato pero no aborda la necesidad real
**Sospechosos Principales:** Instrucciones ambiguas, contexto faltante, errores de lógica

**Pasos de Diagnóstico:**
1. **Claridad de Instrucciones**
   ```
   # Revisar el prompt en busca de lenguaje ambiguo
   # Verificar si la complejidad de la tarea coincide con el detalle de instrucciones
   # Verificar que los ejemplos se alineen con las expectativas
   ```

2. **Completitud de Contexto**
   ```
   # Mapear conocimiento requerido vs. contexto proporcionado
   # Verificar terminología específica del dominio
   # Verificar que el contexto de negocio sea suficiente
   ```

3. **Validación de Lógica**
   ```
   # Recorrer el prompt paso a paso
   # Identificar suposiciones que se están haciendo
   # Verificar brechas o saltos lógicos
   ```

**Marco de Solución:**
```
SI instrucciones ambiguas:
  → Agregar ejemplos específicos de la salida deseada
  → Usar especificación basada en restricciones
  → Dividir tareas complejas en subtareas

SI contexto faltante:
  → Proporcionar antecedentes específicos del dominio
  → Incluir restricciones de negocio relevantes
  → Agregar criterios de éxito y ejemplos

SI errores de lógica:
  → Simplificar la cadena de razonamiento
  → Agregar pasos de validación dentro del prompt
  → Usar prompting de cadena de pensamiento
```

---

## Monitoreo y Prevención

### Marco de Monitoreo en Tiempo Real

**Panel de Métricas de Calidad:**
```
# MONITOREO DE SALUD DE PROMPTS

## Tendencias de Calidad de Respuesta
- Puntuación de calidad promedio (promedio móvil de 7 días)
- Varianza de calidad (medida de consistencia)
- Calificaciones de satisfacción del usuario
- Tasas de finalización de tareas

## Métricas de Rendimiento
- Tiempo de respuesta promedio
- Tiempo de respuesta percentil 95
- Tasas de error por tipo
- Eficiencia de uso de tokens

## Patrones de Uso
- Tendencias de volumen de solicitudes
- Cambios en comportamiento del usuario
- Frecuencia de casos límite
- Variaciones estacionales
```

### Aseguramiento Automatizado de Calidad

**Puertas de Calidad:**
```
# PIPELINE DE QA AUTOMATIZADO

## Pruebas Pre-Producción
- Suite de pruebas de regresión (50+ casos de prueba)
- Puntos de referencia de rendimiento
- Validación entre modelos
- Criterios de aceptación del usuario

## Monitoreo de Producción
- Puntuación de calidad en tiempo real
- Detección de anomalías para patrones de respuesta
- Disparadores de reversión automática
- Integración de retroalimentación del usuario

## Mejora Continua
- Marco de pruebas A/B
- Análisis de tendencias de rendimiento
- Optimización de patrones de uso
- Identificación proactiva de problemas
```

---

## Procedimientos de Respuesta de Emergencia

### Marco de Respuesta a Incidentes

**Niveles de Severidad:**

**P0 - Crítico:** Falla completa o degradación mayor de calidad
- Tiempo de respuesta: <15 minutos
- Acciones: Reversión inmediata, comandante de incidente asignado

**P1 - Alto:** Impacto significativo en experiencia del usuario
- Tiempo de respuesta: <1 hora
- Acciones: Corrección rápida o cambio de configuración

**P2 - Medio:** Impacto moderado, solución alternativa disponible
- Tiempo de respuesta: <4 horas
- Acciones: Corrección planificada en próximo despliegue

**P3 - Bajo:** Problemas menores, impacto mínimo en usuario
- Tiempo de respuesta: <24 horas
- Acciones: Incluir en ciclo de mantenimiento regular

### Procedimientos de Reversión

**Disparadores de Reversión Inmediata:**
- Puntuación de calidad cae >30% de la línea base
- Tasa de error excede 25%
- Quejas de usuarios exceden umbral
- Preocupaciones de seguridad identificadas

**Proceso de Reversión:**
1. **Acción Inmediata (0-5 minutos)**
   - Revertir a última configuración buena conocida
   - Notificar a interesados
   - Iniciar evaluación de impacto

2. **Estabilización a Corto Plazo (5-30 minutos)**
   - Validar efectividad de reversión
   - Monitorear problemas en cascada
   - Preparar comunicación

3. **Análisis de Causa Raíz (30 minutos - 24 horas)**
   - Documentar cronología del incidente
   - Identificar factores contribuyentes
   - Desarrollar estrategias de prevención

---

*Este marco de resolución de problemas debe ser personalizado para cada organización y caso de uso específico, pero proporciona una base sólida para el diagnóstico sistemático de problemas de prompts.*
