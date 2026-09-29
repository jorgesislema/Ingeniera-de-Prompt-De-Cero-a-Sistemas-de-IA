# Prompting para Vision y Analisis de Imagenes

## INTRODUCCION

### Que es el Prompting de Vision

El prompting de vision es la tecnica de enviar imagenes a un modelo de lenguaje multimodal para que este las analice, describa, extraiga informacion o razone sobre su contenido. Tu proporcionas una imagen (o varias) junto con instrucciones en texto, y el modelo te responde con analisis basado en lo que "ve".

Es fundamental entender la diferencia entre dos conceptos que suenan similares pero son opuestos:

```
    PROMPTING DE VISION                      PROMPTING DE GENERACION
    (Analizar imagenes)                      DE IMAGENES (Crear imagenes)

    +----------+                             +----------+
    | Imagen   |                             | Texto    |
    | (input)  |                             | (prompt) |
    +-----+----+                             +-----+----+
          |                                         |
          v                                         v
    +-----+----+                             +-----+----+
    | LLM      |                             | Modelo de|
    | Multimod.|                             | Imagenes |
    +-----+----+                             +-----+----+
          |                                         |
          v                                         v
    +-----+----+                             +-----+----+
    | Texto    |                             | Imagen   |
    | (output) |                             | (output) |
    +----------+                             +----------+

    EJEMPLO:                                 EJEMPLO:
    "Analiza esta foto de        vs          "Genera una imagen de
     un auto y dime el modelo"               un auto deportivo rojo"
```

**Analogia**: El prompting de vision es como describirle una foto a un amigo por telefono. El prompting de generacion de imagenes es como pedirle a un artista que pinte algo. Este documento se enfoca exclusivamente en el primero: como hacer que una IA analice imagenes que tu le proporcionas.

### Por Que es Importante en Julio 2026

En los ultimos 18 meses, las capacidades de vision de los LLMs han madurado hasta un punto donde son confiables para casos de uso empresarial. GPT-4o, Claude 3.5 Sonnet, y Gemini 2.5 Pro pueden:

- Leer texto en imagenes con precision comparable al OCR especializado
- Analizar graficos, tablas y dashboards extrayendo insights numericos
- Describir escenas complejas para accesibilidad
- Comparar multiples imagenes detectando diferencias sutiles
- Identificar objetos, texto, personas, y contextos en fotografias

El costo ha bajado significativamente (aproximadamente $0.001-0.01 USD por imagen en Julio 2026), haciendo viable el procesamiento a escala.

---

## COMO FUNCIONA

### Procesamiento de Imagenes en Modelos Multimodales

Cuando envias una imagen a un LLM multimodal, ocurre lo siguiente:

```
                         PROCESAMIENTO DE UNA IMAGEN

    [Imagen JPEG/PNG]
           |
           v
    +------+------------------------------------+
    | 1. PREPROCESAMIENTO                       |
    |    - Redimension a resolucion optima      |
    |    - Compresion si excede limite          |
    |    - Normalizacion de color               |
    +------+------------------------------------+
           |
           v
    +------+------------------------------------+
    | 2. VISION ENCODER                         |
    |    - La imagen se divide en patches       |
    |    - Cada patch se convierte en embedding |
    |    - Se generan tokens de imagen          |
    |    - (GPT-4o: ~85 tokens/base 512x512)    |
    +------+------------------------------------+
           |
           v
    +------+------------------------------------+
    | 3. CONCATENACION                          |
    |    [Image Tokens] + [Text Tokens]         |
    |    forman una secuencia unificada         |
    +------+------------------------------------+
           |
           v
    +------+------------------------------------+
    | 4. TRANSFORMER (LLM)                      |
    |    - Procesa la secuencia completa        |
    |    - Atencion cruza entre imagen y texto  |
    |    - Razona sobre el contenido visual     |
    +------+------------------------------------+
           |
           v
    +------+------------------------------------+
    | 5. RESPUESTA EN TEXTO                     |
    |    - Descripcion, analisis, JSON, etc.    |
    +-------------------------------------------+
```

### Tokens de Imagen: Como se Calcula el Costo

Las imagenes se convierten en tokens y se cobran como tales. Entender esto es critico para optimizar costos:

```
COSTO APROXIMADO POR IMAGEN (Julio 2026):

+------------------+------------------+------------------+------------------+
| MODELO           | TOKENS POR       | COSTO POR        | COSTO POR        |
|                  | IMAGEN 512x512   | IMAGEN (USD)     | 1000 IMAGENES    |
+------------------+------------------+------------------+------------------+
| GPT-4o           | 85 (low detail)  | ~$0.00021        | ~$0.21           |
|                  | 255 (high)       | ~$0.00064        | ~$0.64           |
+------------------+------------------+------------------+------------------+
| Claude 3.5 Sonnet| ~150-300         | ~$0.00045-0.0009 | ~$0.45-0.90      |
+------------------+------------------+------------------+------------------+
| Gemini 2.5 Pro   | ~258            | ~$0.00032        | ~$0.32           |
+------------------+------------------+------------------+------------------+
```

**Regla practica**: Si procesas imagenes a escala (100K+ al mes), el costo se vuelve significativo. Siempre redimensiona imagenes antes de enviarlas y usa la resolucion minima necesaria para tu caso de uso.

### Resolucion y Nivel de Detalle

La mayoria de modelos ofrecen control sobre la resolucion:

```
RESOLUCION BAJA (Low/Auto):              RESOLUCION ALTA (High/Detailed):
- Imagen redimensionada a ~512x512       - Imagen analizada en mayor detalle
- Ideal para: clasificacion general,     - Ideal para: OCR, lectura de texto
  descripcion basica, moderacion          pequeno, analisis de graficos
- Costo: ~85 tokens                      - Costo: 3-4x tokens de low
- Velocidad: rapida                      - Velocidad: mas lenta
```

---

## ANATOMIA DE UN PROMPT DE VISION

Un prompt de vision efectivo tiene 5 elementos. Dominarlos es la diferencia entre resultados mediocres y analisis de nivel profesional.

```
                ANATOMIA DE UN PROMPT DE VISION (5 ELEMENTOS)

    +------------------------------------------------------------------+
    | ELEMENTO 1: LAS IMAGENES                                         |
    | Que mostrar, cuantas, resolucion, formato                        |
    +------------------------------------------------------------------+
           |
    +------v-----------------------------------------------------------+
    | ELEMENTO 2: LA PREGUNTA / TAREA                                  |
    | Que quieres saber sobre la imagen                                |
    +------------------------------------------------------------------+
           |
    +------v-----------------------------------------------------------+
    | ELEMENTO 3: NIVEL DE DETALLE                                     |
    | "Describe brevemente" vs "Analiza minuciosamente"               |
    +------------------------------------------------------------------+
           |
    +------v-----------------------------------------------------------+
    | ELEMENTO 4: FORMATO DE SALIDA                                    |
    | Texto libre, JSON estructurado, lista, tabla, secciones          |
    +------------------------------------------------------------------+
           |
    +------v-----------------------------------------------------------+
    | ELEMENTO 5: CONTEXTO                                             |
    | Por que preguntas, para que usaras la respuesta                  |
    +------------------------------------------------------------------+
```

### Elemento 1: Las Imagenes

```
MEJORES PRACTICAS:

- FORMATO: JPEG o PNG. Evitar BMP, TIFF, o formatos exoticos.
- RESOLUCION: Suficiente para ver el detalle necesario, no mas.
  Si necesitas leer texto pequeno, usa alta resolucion.
- MULTIPLES IMAGENES: Si comparas, enumeralas:
  "Imagen 1: [descripcion]. Imagen 2: [descripcion]."
- RECORTE: Si solo importa una parte de la imagen, recortala antes.
  Esto ahorra tokens y enfoca al modelo.
- CALIDAD: Fotos borrosas = resultados pobres. Si la imagen es mala,
  el modelo no puede "adivinar" lo que dice.

ERROR COMUN: Enviar una captura de pantalla de 4K cuando solo importa
un mensaje de error de 2 lineas. Recorta. Ahorraras ~95% de tokens.
```

### Elemento 2: La Pregunta / Tarea

La precision de tu pregunta determina la precision de la respuesta.

```
MALO: "Que ves en esta imagen?"
BUENO: "En esta imagen de un almacen, identifica y lista todos los
        productos que estan fuera de su estante designado."

MALO: "Dime que dice esto."
BUENO: "Extrae de esta factura: numero de factura, fecha, nombre
        del proveedor, lista de items con precios unitarios, subtotal,
        impuestos, y total. Devuelve en JSON."
```

### Elemento 3: Nivel de Detalle

```
NIVELES DE DETALLE (de menos a mas):

1. CLASIFICACION RAPIDA:
   "Esta imagen contiene contenido sensible? Responde SI o NO."

2. DESCRIPCION BREVE:
   "Describe esta imagen en una oracion."

3. DESCRIPCION DETALLADA:
   "Describe esta imagen en un parrafo, incluyendo objetos, colores,
    iluminacion, y composicion."

4. ANALISIS EXHAUSTIVO:
   "Analiza esta imagen en detalle. Identifica: todos los objetos,
    sus posiciones relativas, texto visible, personas presentes
    (sin identificarlas), iluminacion, angulo de la camara, estado
    de los objetos (nuevo/usado/danado), y cualquier anomalia."

5. INTERPRETACION + RECOMENDACION:
   "Analiza esta foto e interpreta lo que muestra. Luego, basado en
    tu analisis, proporciona 3 recomendaciones accionables."
```

### Elemento 4: Formato de Salida

Especificar el formato evita tener que "re-preguntar" para estructurar la respuesta.

```
FORMATOS COMUNES:

- TEXTO LIBRE: Para descripciones narrativas
  "Describe la escena en lenguaje natural."

- JSON ESTRUCTURADO: Para extraccion de datos
  "Devuelve UNICAMENTE un JSON con esta estructura: {...}"

- LISTA: Para enumeraciones
  "Lista en vinetas todos los objetos visibles."

- TABLA: Para comparaciones
  "Crea una tabla con columnas: Objeto, Cantidad, Estado, Ubicacion."

- SECCIONES: Para analisis multi-perspectiva
  "Estructura tu respuesta en: 1) Descripcion, 2) Analisis tecnico,
   3) Problemas detectados, 4) Recomendaciones."
```

### Elemento 5: Contexto

Explicar POR QUE preguntas y PARA QUE usaras la respuesta mejora significativamente la calidad.

```
MALO: "Que tipo de flor es esta?"
BUENO: "Soy un florista preparando un arreglo para una boda en
        jardin primaveral. Identifica esta flor y dime: nombre comun
        y cientifico, si es alergenica, cuanto dura cortada en un
        arreglo, y con que otras flores combina bien esteticamente."

El contexto permite al modelo:
- Priorizar informacion relevante para tu caso de uso
- Omitir detalles tecnicos que no necesitas
- Incluir informacion practica que no pediste explicitamente
```

---

## CASOS DE USO CON EJEMPLOS REALES

### CASO 1: OCR y Extraccion de Texto

**Escenario**: Un contador necesita digitalizar recibos y facturas en papel.

```
MALO: "Que dice este recibo?"

BUENO:
"Eres un asistente de contabilidad. Extrae de este recibo de compra:
1. Nombre del comercio
2. Fecha de la transaccion (formato YYYY-MM-DD)
3. Hora de la transaccion
4. Metodo de pago utilizado
5. Lista de items comprados (nombre, cantidad, precio unitario)
6. Subtotal (antes de impuestos)
7. Impuestos desglosados (IVA u otros)
8. Total pagado
9. Numero de recibo/factura si es visible

Devuelve UNICAMENTE un JSON valido con esta estructura:
{
  "merchant": "string",
  "date": "YYYY-MM-DD",
  "time": "HH:MM",
  "payment_method": "string",
  "items": [{"name": "string", "quantity": number, "unit_price": number}],
  "subtotal": number,
  "taxes": [{"type": "string", "amount": number}],
  "total": number,
  "receipt_number": "string | null"
}

Si algun campo no es legible, usa null y marca el campo como 'uncertain'.
NO inventes valores si el texto esta borroso o cortado."
```

### CASO 2: Analisis de Graficos y Datos

**Escenario**: Un gerente de ventas necesita analizar un grafico de rendimiento.

```
BUENO:
"Analiza este grafico de ventas mensuales. Identifica y reporta:

1. TENDENCIA GENERAL: La direccion general de las ventas en el periodo
   mostrado (creciente, decreciente, estable, ciclica).

2. PUNTOS EXTREMOS:
   - Mes con mayores ventas (valor aproximado)
   - Mes con menores ventas (valor aproximado)

3. ANOMALIAS: Cualquier patron inusual (picos inexplicados, caidas
   repentinas, estacionalidad atipica).

4. INSIGHTS ACCIONABLES: Basado en el grafico, proporciona 3 recomendaciones
   concretas para el dueno del negocio. Cada recomendacion debe:
   - Estar respaldada por datos VISIBLES en el grafico
   - Incluir una accion especifica
   - Mencionar la metrica que mejoraria

5. CRITICA DEL GRAFICO: Senala si el grafico tiene problemas de
   visualizacion que podrian inducir a error (ejes truncados, escalas
   inconsistentes, falta de etiquetas, colores confusos).

IMPORTANTE: Cita valores numericos aproximados basados en lo que VES.
Si los ejes no tienen numeros claros, indica 'valores no legibles' en
vez de inventar cifras."
```

### CASO 3: Descripcion de Productos para E-commerce

**Escenario**: Un vendedor en linea necesita descripciones de productos a partir de fotos.

```
BUENO:
"Eres un redactor de e-commerce especializado en moda. Analiza esta
foto de un producto y genera una ficha completa:

1. CATEGORIA: Tipo de prenda/articulo
2. TITULO SEO: Nombre descriptivo optimizado para busqueda (max 70 caracteres)
3. DESCRIPCION CORTA: 2-3 frases persuasivas para listado de productos
4. DESCRIPCION LARGA: Parrafo detallado incluyendo material aparente,
   estilo, ocasion de uso sugerida
5. CARACTERISTICAS VISIBLES: Color, patron, tipo de cuello, largo de
   manga, tipo de cierre, bolsillos, estampados, textura aparente
6. TALLA ESTIMADA: Si hay una persona o referencia, estima la talla
7. SUGERENCIAS DE COMBINACION: 3 ideas de outfits con esta prenda
8. PALABRAS CLAVE: 10 tags para busqueda

IMPORTANTE: Si algun detalle no es claramente visible en la foto,
indicalo como 'No visible en imagen'. No asumas caracteristicas."
```

### CASO 4: Diagnostico Visual (No Medico)

**Escenario A**: Planta con hojas amarillas.

```
BUENO:
"Eres un botanico especializado en plantas de interior. Analiza esta
foto de una planta con hojas amarillas.

1. Identifica la especie de planta (si es reconocible)
2. Describe los sintomas visibles (color, textura, distribucion del
   amarillamiento: bordes, centro, venas, hojas viejas vs nuevas)
3. Diagnostico probable: Las 3 causas mas probables del problema,
   ordenadas de mas a menos probable
4. Para cada causa, indica:
   - Como confirmarla (que revisar en la planta/suelo)
   - Solucion paso a paso
   - Tiempo estimado de recuperacion
5. Prevencion: 3 practicas para evitar que vuelva a ocurrir

NOTA: Esto no es un diagnostico profesional. Si la planta es valiosa,
consulta a un especialista local. Incluye esta advertencia en tu respuesta."
```

**Escenario B**: Luz de advertencia en el tablero del auto.

```
BUENO:
"Actua como un mecanico automotriz con 20 anos de experiencia. El usuario
ha tomado una foto de una luz de advertencia encendida en el tablero de
su auto, un Toyota Corolla 2022 segun indica.

Analiza la imagen y proporciona:

1. IDENTIFICACION DE LA LUZ:
   - Nombre de la luz de advertencia
   - Simbolo detectado y su significado
   - Nivel de urgencia: PUEDE ESPERAR / ATENDER PRONTO / DETENERSE YA

2. CAUSAS PROBABLES (3, de mas a menos comun) con solucion estimada

3. ACCION INMEDIATA: Que debe hacer el conductor AHORA

4. COSTO ESTIMADO DE REPARACION: Rango de precio en USD

ADVERTENCIA: Esto es orientacion general. Un diagnostico preciso requiere
escaner OBD-II y revision fisica por un mecanico certificado."
```

### CASO 5: Accesibilidad

**Escenario**: Generar descripciones para personas con discapacidad visual.

```
BUENO:
"Genera una descripcion para una persona con discapacidad visual.
Describe esta imagen siguiendo estos principios:

1. DE LO GENERAL A LO ESPECIFICO: Primero el contexto general
   ('Es una foto de una cocina moderna'), luego los detalles.

2. UBICACION ESPACIAL: Usa referencias claras.
   'En el centro de la imagen...', 'En la esquina superior derecha...',
   'En primer plano...', 'Al fondo...'

3. RELEVANCIA: Prioriza lo importante para entender la escena.

4. OBJETIVIDAD: Describe lo que ves, no interpretes emociones.

5. EXTENSION: Un parrafo de 3-5 oraciones para descripcion general.

Ejemplo de buen ALT text para web:
'Cocina moderna con isla central de marmol blanco, gabinetes gris oscuro,
y electrodomesticos de acero inoxidable. Ventana grande al fondo da a un jardin.'
```

### CASO 6: Verificacion y Compliance

**Escenario A**: Foto de gondola de supermercado para verificar planograma.

```
BUENO:
"Eres un auditor de retail. Analiza esta foto de una gondola de
supermercado y verifica el cumplimiento del planograma.

1. Identifica todos los productos visibles (marca y variante)
2. Para cada producto, indica posicion, precio visible, facings, orientacion
3. Anomalias: productos caidos, espacios vacios, fuera de seccion, etiquetas danadas
4. Puntuacion de cumplimiento: XX/10
5. Acciones correctivas requeridas"
```

**Escenario B**: Foto de sitio de construccion para identificar riesgos.

```
BUENO:
"Eres un inspector de seguridad industrial. Analiza esta foto de un
sitio de construccion. Identifica TODOS los riesgos de seguridad
visibles, priorizando peligro inmediato.

Clasifica cada riesgo:
- CRITICO: Riesgo de muerte o lesion grave inminente
- ALTO: Puede causar lesion seria
- MEDIO: Lesion menor
- BAJO: Incumplimiento administrativo

Para cada riesgo: describe el riesgo, la norma violada, accion correctiva,
y EPP faltante.

ADVERTENCIA: Analisis preliminar basado en una sola foto. No sustituye
inspeccion fisica completa."
```

---

## TECNICAS AVANZADAS

### Multi-Image Prompting: Comparacion de Imagenes

Enviar dos o mas imagenes simultaneamente para comparacion.

```
EJEMPLO:
"Compara estas dos imagenes de fachadas de restaurantes. Imagen 1 es una
foto tomada en 2023, Imagen 2 es del mismo restaurante en 2025.

Identifica TODOS los cambios visibles entre las dos imagenes. Para cada
cambio, indica: que cambio, donde esta (usa referencias espaciales), y
si el cambio parece ser una mejora o deterioro.

Cambios a buscar: pintura, senalizacion, iluminacion, vegetacion, muebles
de terraza, toldos, ventanas, estado general de limpieza."
```

### Chain-of-Thought (CoT) con Vision

Guiar al modelo a razonar paso a paso sobre una imagen.

```
TECNICA: Pedir analisis secuencial en vez de respuesta directa.

EJEMPLO:
"Analiza esta radiografia de equipaje de aeropuerto. Piensa paso a paso:

PASO 1: Identifica las formas y siluetas principales.
PASO 2: Para cada forma, determina si corresponde a un objeto permitido
        (ropa, libros, electronicos) o potencialmente restringido.
PASO 3: Senala cualquier objeto con forma, densidad o patron sospechoso.
PASO 4: Conclusion: ¿Requiere este equipaje inspeccion manual? Justifica.

NO te apresures. Razona cada paso antes de escribir la respuesta."
```

### Tree-of-Thought con Vision: Multiples Perspectivas

Analizar una misma imagen desde diferentes perspectivas y luego sintetizar.

```
EJEMPLO:
"Analiza esta foto de un producto usando la tecnica de 3 perspectivas.
Evaluaras cada perspectiva por separado y luego generaras una conclusion.

PERSPECTIVA 1 - ESTETICA: Composicion, iluminacion, colores, atractivo
visual, balance, enfoque. Puntua del 1-10.

PERSPECTIVA 2 - TECNICA: Calidad de la foto, resolucion, angulo,
profundidad de campo, ruido, aberraciones. Puntua del 1-10.

PERSPECTIVA 3 - COMERCIAL: Que tan bien vende el producto esta foto?
Claridad del producto, contexto de uso, diferenciacion, llamada a la
accion implicita. Puntua del 1-10.

SINTESIS: Basado en las 3 perspectivas, la puntuacion general es X/10.
Recomendacion final: USAR / RETOCAR / REHACER esta foto para e-commerce."
```

### Vision + Function Calling

Analizar una imagen, extraer datos estructurados, y luego llamar a una funcion con esos datos.

```
FLUJO:

    [Imagen de factura] --> [LLM extrae datos JSON] --> [Funcion contable]
    
    {
      "merchant": "Supermercado XYZ",
      "date": "2026-07-10",
      "total": 156.78,
      "items": [...]
    }
           |
           v
    [create_expense_record(merchant, date, total, items)]

IMPLEMENTACION CONCEPTUAL:

1. El usuario sube una foto de un recibo
2. El LLM analiza la imagen y extrae datos estructurados
3. El JSON extraido se pasa como argumento a una funcion
4. La funcion registra el gasto en el sistema contable
5. El asistente confirma: "Gasto registrado: $156.78 en Supermercado XYZ"
```

---

## LIMITACIONES Y CUANDO NO USARLO

### Limitaciones Tecnicas

```
1. PRECISION DE OCR:
   - Texto manuscrito: 70-85% de precision (varia segun caligrafia)
   - Texto en angulos extremos o distorsionado: degradacion significativa
   - Texto sobre fondos complejos: propenso a errores

2. ALUCINACIONES VISUALES:
   - El modelo puede "ver" objetos que no estan en la imagen
   - Puede inventar texto cuando la imagen es borrosa
   - Puede confundir objetos visualmente similares

3. SESGOS:
   - Los modelos pueden tener sesgos culturales en la interpretacion
   - Pueden asumir contextos occidentales por defecto
   - Pueden reforzar estereotipos en descripciones de personas

4. LIMITES DE CONTEXTO:
   - Imagenes de muy alta resolucion consumen muchos tokens
   - Multiples imagenes en un solo mensaje compiten por el contexto
```

### Cuando NO Usar Vision Prompting

```
NO USAR PARA:

1. IDENTIFICACION DE PERSONAS:
   Los modelos tienen restricciones eticas y legales para identificacion
   facial. No uses vision prompting para reconocer individuos especificos.

2. DIAGNOSTICO MEDICO:
   Aunque los modelos pueden analizar imagenes medicas, NO estan aprobados
   para diagnostico clinico. Siempre requiere un profesional medico.
   Puedes usarlo para triage preliminar o sugerencias educativas, pero
   NUNCA como diagnostico final.

3. VERIFICACION LEGAL DE DOCUMENTOS:
   No confies en la IA para verificar autenticidad de documentos legales,
   firmas, o identificaciones oficiales. Siempre requiere verificacion
   humana y/o forense.

4. DECISIONES DE ALTO RIESGO SIN SUPERVISION HUMANA:
   Cualquier decision que implique riesgo financiero, legal o de seguridad
   debe tener un humano en el loop. La IA es una herramienta de apoyo,
   no un reemplazo del juicio humano.

5. IMAGENES DE MUY BAJA CALIDAD:
   Si la imagen es extremadamente borrosa, oscura o pixelada, el modelo
   producira resultados poco confiables. Mejora la calidad de la imagen
   primero o acepta que la precision sera baja.
```

---


## COMPARATIVA DE MODELOS PARA VISION (Julio 2026)

Cada modelo tiene fortalezas y debilidades distintas para tareas de vision. Elegir el modelo correcto puede ser la diferencia entre un proyecto exitoso y uno frustrante.

### Tabla Comparativa

```
+------------------+---------------------+---------------------+-----------------------+
| MODELO           | FORTALEZAS          | LIMITACIONES        | MEJOR PARA            |
+------------------+---------------------+---------------------+-----------------------+
| GPT-4o           | Mejor vision       | Costo a escala      | Analisis general,     |
| (OpenAI)         | general del mercado| ($2.50/1M tokens   | OCR de documentos,    |
|                  | Excelente OCR      |  input). Latencia   | descripcion de        |
|                  | Structured Outputs | en picos de demanda | productos, extraccion |
|                  | nativo             |                     | de datos estructurados|
+------------------+---------------------+---------------------+-----------------------+
| Claude 3.5       | Excelente analisis | Mas lento que GPT-4o| Documentos largos     |
| Sonnet           | de documentos con  | en respuestas. No   | con imagenes,         |
| (Anthropic)      | imagenes (PDFs).   | tiene structured    | analisis profundo con |
|                  | Mejor en matices y | outputs nativo.     | razonamiento, textos  |
|                  | contexto cultural  | Ventana de contexto | legales/financieros   |
|                  |                    | menor que GPT-4.1   | con graficos          |
+------------------+---------------------+---------------------+-----------------------+
| Gemini 2.5 Pro   | Nativo multimodal  | Latencia variable   | Video + imagen,       |
| (Google)         | (disenado con      | en horas pico.      | analisis de grandes   |
|                  | vision integrada). | Menos ecosistema    | volumenes de imagenes,|
|                  | Excelente para     | de herramientas.    | busqueda visual,      |
|                  | video + imagen     |                     | proyectos Google Cloud|
+------------------+---------------------+---------------------+-----------------------+
| GPT-4.1          | Contexto masivo    | Modelo muy nuevo    | Documentos extensos   |
| (OpenAI)         | (1M tokens) con    | (Q2 2026), menos    | con muchas imagenes,  |
|                  | imagenes. Bueno    | probado en produc-  | analisis de manuales, |
|                  | para documentos    | cion. Costoso.      | catalogos y reportes  |
|                  | tecnicos extensos  |                     | de 100+ paginas       |
+------------------+---------------------+---------------------+-----------------------+
```

### Arbol de Decision: Que Modelo Usar

```
                         +--------------------------+
                         | Que necesitas analizar?  |
                         +--------------------------+
                                    |
              +---------------------+-----------------------+
              |                     |                       |
              v                     v                       v
    +---------+--------+  +--------+---------+   +---------+----------+
    | Documentos        |  | Imagenes         |   | Video + Imagenes   |
    | (PDF, manuales,   |  | individuales     |   | (frames, capturas  |
    |  reportes)        |  | (fotos, capturas)|   |  de video)         |
    +---------+--------+  +--------+---------+   +---------+----------+
              |                     |                       |
    +---------v--------+  +--------v---------+   +---------v----------+
    | Mucho texto +     |  | Necesitas JSON   |   | Google Cloud?      |
    | imagenes?         |  | estructurado?    |   +----+---------+----+
    +----+---------+----+  +----+--------+----+        |YES      |NO
         |YES      |NO         |YES      |NO            v         v
         v         v           v         v        +----+---+ +---+----+
    +----+--+ +----+----+ +----+--+ +----+----+  |Gemini  | |GPT-4o  |
    |Claude | |GPT-4.1  | |GPT-4o | |GPT-4o  |  |2.5 Pro | |o Gemini|
    |3.5    | |         | |       | |Claude  |  +--------+ +--------+
    |Sonnet | |         | |       | |3.5 Son.| 
    +-------+ +---------+ +-------+ +--------+
```

### Recomendaciones por Industria

```
+-------------------+---------------------+------------------------+
| INDUSTRIA         | MODELO RECOMENDADO  | RAZON                  |
+-------------------+---------------------+------------------------+
| Retail/E-commerce | GPT-4o              | Structured outputs,    |
|                   |                     | mejor relacion costo/  |
|                   |                     | beneficio para volumen |
+-------------------+---------------------+------------------------+
| Legal/Financiero  | Claude 3.5 Sonnet   | Mejor analisis de      |
|                   |                     | documentos complejos,  |
|                   |                     | matices contractuales  |
+-------------------+---------------------+------------------------+
| Manufactura/      | GPT-4o              | OCR preciso para       |
| Logistica         |                     | etiquetas, buen coste  |
+-------------------+---------------------+------------------------+
| Salud/Medicina    | Claude 3.5 Sonnet   | Mejor en razonamiento  |
| (investigacion)   |                     | matizado, menos pro-   |
|                   |                     | babilidad de alucinar  |
+-------------------+---------------------+------------------------+
| Medios/Entreten.  | Gemini 2.5 Pro      | Nativo multimodal,     |
|                   |                     | mejor para video       |
+-------------------+---------------------+------------------------+
| Educacion         | GPT-4o              | Buen balance costo/    |
|                   |                     | calidad, accesible     |
+-------------------+---------------------+------------------------+
```

---

## EJERCICIOS PRACTICOS

Cada ejercicio esta disenado para desarrollar una competencia especifica de vision prompting. Completa los 4 ejercicios en orden.

### Ejercicio 1: Organizacion de Espacio de Trabajo

```
OBJETIVO: Aprender a dar contexto y nivel de detalle en prompts de vision.

TAREA:
1. Toma una foto de tu escritorio o espacio de trabajo actual (tal como esta,
   sin ordenar ni limpiar).
2. Escribe un prompt pidiendo a la IA que analice la foto y sugiera mejoras
   de organizacion.
3. Ejecuta el prompt con al menos 2 modelos diferentes.
4. Compara las respuestas.

PROMPT SUGERIDO (mejoralo):
"Analiza esta foto de mi espacio de trabajo. Soy [TU PROFESION] y paso
[X] horas al dia aqui. Identifica:
1. Problemas de organizacion visibles (cables, papeles, objetos fuera de lugar)
2. Sugerencias especificas de mejora (nombra productos o soluciones concretas)
3. Aspectos positivos que ya estan bien
4. Un principio de organizacion que deberia adoptar segun como trabajo

Se especifico. Menciona exactamente lo que ves en la foto."

ENTREGABLE: Captura de ambas respuestas + tu reflexion sobre las diferencias.
```

### Ejercicio 2: Diagnostico de Errores Tecnicos

```
OBJETIVO: Practicar extraccion de informacion precisa de capturas de pantalla.

TAREA:
1. Provoca un error en tu computadora (desconecta un cable, cierra un puerto,
   escribe mal una URL en el navegador) y toma una captura de pantalla.
2. Escribe un prompt pidiendo a la IA que diagnostique el error.
3. La IA debe identificar: el tipo de error, la causa probable, y 3 soluciones
   ordenadas por probabilidad de exito.

PROMPT SUGERIDO:
"Actua como un ingeniero de soporte tecnico. Analiza esta captura de pantalla
de un error en mi computadora.

Identifica:
1. El mensaje de error exacto (transcribe el texto)
2. El codigo de error si es visible
3. El programa o componente que genero el error
4. Causa mas probable (top 3, ordenadas)
5. Para cada causa, una solucion paso a paso que un usuario no tecnico
   pueda seguir
6. Si ninguna funciona, cual es el siguiente paso (a quien contactar)

NO asumas mi sistema operativo. Pregunta si no es evidente en la captura."

ENTREGABLE: El prompt, la respuesta de la IA, y si la solucion funciono.
```

### Ejercicio 3: Extraccion de Datos Nutricionales

```
OBJETIVO: Dominar la extraccion de datos estructurados de imagenes.

TAREA:
1. Toma una foto de una etiqueta nutricional de cualquier producto alimenticio.
2. Escribe un prompt que extraiga TODA la informacion nutricional en JSON.
3. La salida debe incluir: tamano de porcion, calorias, macronutrientes
   (grasas, carbohidratos, proteinas), micronutrientes listados, ingredientes,
   alerGenos.

PROMPT SUGERIDO:
"Extrae de esta etiqueta nutricional TODA la informacion en formato JSON.

Estructura requerida:
{
  "product_name": "string",
  "serving_size": {"amount": number, "unit": "string"},
  "servings_per_container": number,
  "calories_per_serving": number,
  "macronutrients": {
    "total_fat_g": number,
    "saturated_fat_g": number,
    "trans_fat_g": number,
    "cholesterol_mg": number,
    "sodium_mg": number,
    "total_carbohydrates_g": number,
    "dietary_fiber_g": number,
    "total_sugars_g": number,
    "added_sugars_g": number,
    "protein_g": number
  },
  "micronutrients": [{"name": "string", "amount": number, "unit": "string", "dv_percent": number}],
  "ingredients": ["string"],
  "allergens": ["string"]
}

IMPORTANTE:
- Si un campo no aparece en la etiqueta, usa null (NO inventes valores)
- Si un valor dice '0g', reportalo como 0, no como null
- Los ingredientes deben estar en el mismo orden que aparecen en la etiqueta
- Si la imagen esta borrosa en alguna seccion, marcala como 'illegible'"

ENTREGABLE: El JSON generado + comparacion con lo que realmente dice la etiqueta.
```

### Ejercicio 4: Analisis Critico de Graficos

```
OBJETIVO: Desarrollar pensamiento critico sobre visualizaciones de datos.

TAREA:
1. Busca un grafico en un articulo de noticias (economia, clima, salud, etc.)
2. Toma una captura de pantalla que incluya el grafico Y su contexto (titular,
   fuente, eje, leyenda).
3. Pide a la IA un analisis critico que vaya mas alla de lo obvio.

PROMPT SUGERIDO:
"Actua como un analista de datos esceptico. Analiza este grafico de una noticia.
No te limites a describirlo. Cuestionalo.

1. DESCRIPCION OBJETIVA: Que muestra el grafico (sin interpretar)

2. ANALISIS DE HONESTIDAD VISUAL:
   - Los ejes empiezan en cero? Si no, cuanto exageran la diferencia?
   - La escala es consistente o cambia a mitad del grafico?
   - Los colores son enganosos? (rojo = malo, aunque el dato sea neutro)
   - Hay datos omitidos que cambiarian la interpretacion?

3. INTERPRETACION ALTERNATIVA: Como podria alguien con la opinion contraria
   interpretar este mismo grafico para apoyar su punto?

4. PREGUNTAS SIN RESPONDER: Que datos necesitarias para verificar la conclusion
   del articulo? Que NO te esta mostrando el grafico?

5. TITULAR VS DATOS: El titular del articulo es consistente con lo que MUESTRA
   el grafico, o esta exagerando?

6. VEREDICTO: En una escala del 1-10, que tan honesto es este grafico?
   1 = completamente enganoso, 10 = perfectamente objetivo."

ENTREGABLE: El analisis completo de la IA + tu opinion sobre si el analisis
fue justo o si la IA fue demasiado/esceptica.
```

---

*Documento creado para el repositorio "Apuntes de Ingenieria de Prompt" - Julio 2026*
