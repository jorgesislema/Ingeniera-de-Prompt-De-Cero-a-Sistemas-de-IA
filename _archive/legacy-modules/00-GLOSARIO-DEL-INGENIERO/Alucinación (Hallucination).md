

# Alucinación (Hallucination)
## Cuando la IA “inventa” información que parece real

---

## ¿Qué es una alucinación?

**En términos sencillos**  
Imagina que le preguntas a un amigo sobre una película que jamás vio. Para no quedar mal, inventa detalles que suenan convincentes pero son totalmente falsos. Una **alucinación de IA** ocurre cuando el modelo genera contenido ficticio con gran seguridad y lo presenta como un hecho verídico.

**Desde un punto de vista técnico**  
Un modelo de lenguaje alucina cuando produce datos o afirmaciones que no provienen de su entrenamiento (o son incorrectas) y, aun así, los expone con alta confianza.

---

## Tipos de alucinaciones

| Tipo                         | Descripción                                                                                                            | Ejemplo                                                                                                                                    |
|------------------------------|------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| **Alucinación factual**      | Invención de datos, fechas, nombres o cifras.                                                                         | “TechnoMax Solutions fue fundada en 2018 por María González …” — la empresa no existe.                                                      |
| **Alucinación de atribución**| Citas, obras o declaraciones asignadas a la persona equivocada.                                                       | “Steve Jobs dijo en 2015: ‘La IA será la próxima gran revolución…’” — Jobs falleció en 2011.                                                |
| **Alucinación de contexto**  | Añade detalles inexistentes a documentos o conversaciones reales.                                                      | Al analizar un reporte de ventas, la IA afirma pérdidas de cuota de mercado que el documento no menciona.                                   |

---

## ¿Por qué ocurren las alucinaciones?

1. **Naturaleza del entrenamiento**  
   Los modelos aprenden patrones lingüísticos, no bases de datos de hechos.  
   *Analogía:* estudiante que redacta ensayos brillantes sin verificar fuentes.

2. **Presión por responder**  
   El modelo prefiere dar una respuesta “segura” antes que admitir desconocimiento.  
   *Ejemplo:*  
   - Pregunta: “¿Cuál es la capital de Zelandia del Norte?”  
   - IA incorrecta: “Northport.”  
   - IA correcta: “No existe tal país; quizá te refieres a Nueva Zelanda, cuya capital es Wellington.”

3. **Patrones de diálogo**  
   Tras millones de ejemplos de pregunta-respuesta, el modelo replica la autoridad del tono, incluso sin evidencia.

---

## Cómo detectar alucinaciones

### Señales de alerta

- **Datos muy específicos sin fuente clara**  
  > “Según un estudio de la Universidad de Barcelona de marzo 2024…”  
  Mejor: “Se recomienda verificar estudios específicos.”

- **Estadísticas con decimales exactos**  
  > “El 73.6 % de las empresas españolas implementaron IA en 2024.”  
  Mejor: “Muchas empresas han adoptado IA (verificar cifras actuales).”

- **Citas textuales sin referencia**  
  > “Elon Musk dijo en 2023: ‘La IA superará a los humanos en creatividad.’”  
  Mejor: “Musk ha opinado sobre IA (verificar cita exacta).”

### Técnicas de validación

def validate_claim(claim: str) -> bool:
"""
Devuelve True si al menos dos de tres fuentes confirman el claim.
"""
sources = [
search_google(claim),
check_wikipedia(claim),
verify_official_sources(claim)
]
confirmations = sum(bool(src) for src in sources)
return confirmations >= 2

text
undefined
Prompt de verificación:
“En tu respuesta mencionaste [DATO].

¿Nivel de confianza (1–10)?

¿A qué fuentes recomiendas acudir para confirmarlo?”

text

---

## Estrategias para prevenir alucinaciones

### 1. Prompts anti-alucinación

Eres un [ROL].
Si no tienes información verificada sobre un punto, responde:
“No tengo información verificada sobre X” en lugar de especular.

text
undefined
Modo analista riguroso:

Alta confianza → presenta el dato.

Media confianza → marca como “aproximado”.

Baja confianza → indica “requiere verificación” y sugiere fuentes.
Nunca inventes cifras exactas, fechas o citas textuales.

text

### 2. Fuentes explícitas

> “La inflación de 2024 fue del 3.1 % (alta confianza – INE).”

### 3. Doble validación

1. **Paso A** – Genera la respuesta.  
2. **Paso B** – Revisa tu texto e identifica afirmaciones especulativas; marca “requiere verificación”.

---

## Casos reales

| Caso                | Alucinación                                     | Consecuencia                                | Lección                                  |
|---------------------|-------------------------------------------------|---------------------------------------------|------------------------------------------|
| Bufete de abogados  | Inventó jurisprudencia ficticia                 | Sanción judicial, reputación dañada         | Verificación obligatoria de casos.       |
| Medio de noticias   | Escándalo financiero inexistente                | Demanda legal por difamación                | Cruzar fuentes antes de publicar datos.  |
| RR. HH. corporativo | Historial académico inventado para un candidato | Contratación errónea                        | Verificar credenciales externamente.     |

---

## Alucinaciones por modelo y prevención

| Modelo | Tendencia principal                       | Recomendación de prompt                                            |
|--------|-------------------------------------------|--------------------------------------------------------------------|
| GPT-4  | Nombres propios y fechas exactas          | “Indica confianza o ‘requiere verificación’ para nombres/fechas.” |
| Claude | Citas textuales y papers académicos       | “Solo citas si estás 100 % seguro; de lo contrario, parafrasea.”   |
| Gemini | Estadísticas numéricas muy específicas    | “Usa rangos (‘60-80 %’) salvo datos ampliamente aceptados.”        |

---

## Herramientas anti-alucinación

def add_verification_layer(prompt: str) -> str:
return (
"IMPORTANTE: antes de responder, pregúntate:\n"
"- ¿Es información verificable?\n"
"- ¿Estoy inventando detalles específicos?\n"
"- Si dudas, marca ‘Requiere verificación’ y sugiere fuentes.\n\n"
) + prompt

text
undefined
import re
def analyze_response_reliability(response: str) -> str:
risk_indicators = [
r'\d{1,2}.\d+%', # porcentajes exactos
r'según.*estudio', # menciones vagas a estudios
r'en \d{4} dijo', # citas con fechas
r'la empresa.*fundada en' # datos históricos específicos
]
risk_score = sum(bool(re.search(p, response)) for p in risk_indicators)
return "HIGH_RISK" if risk_score > 2 else "LOW_RISK"

text
undefined
class HallucinationDetector:
def init(self):
self.validators = [
WikipediaValidator(),
DateValidator(),
PersonValidator()
]
def validate_response(self, resp: str) -> str:
for claim in extract_factual_claims(resp):
if not all(v.verify(claim) for v in self.validators):
return f"⚠️ Verificar: {claim}"
return "✅ Sin alucinaciones evidentes"

text

---

## Estrategias sectoriales

| Sector      | Prompt seguro                                                                                                                |
|-------------|------------------------------------------------------------------------------------------------------------------------------|
| **Legal**   | “Nunca inventes casos o artículos. Si no hay certeza, indica ‘Verificar en bases como Westlaw o BOE’.”                       |
| **Médico**  | “Información educativa únicamente. Para dosificaciones o diagnósticos, remitir a un profesional sanitario.”                  |
| **Finanzas**| “Usa rangos para métricas y sugiere Bloomberg, Reuters o informes oficiales para cifras exactas.”                             |
| **Académico**| “Cita DOI o fuente; si no, marca ‘referencia a verificar’ y sugiere bases como Scopus o Google Scholar.”                     |

---

## Checklist anti-alucinación

### Antes de enviar un prompt
- [ ] ¿Especificaste que no se invente información?  
- [ ] ¿Solicitaste nivel de confianza?  
- [ ] ¿Indicaciones para manejar dudas?

### Al revisar una respuesta
- [ ] ¿Estadísticas sin fuente?  
- [ ] ¿Nombres/datos “demasiado perfectos”?  
- [ ] ¿Fechas exactas poco conocidas?  
- [ ] ¿Afirmaciones verificables externamente?

### Para uso empresarial
- [ ] ¿Proceso de fact-checking implantado?  
- [ ] ¿Equipo entrenado en detección de alucinaciones?  
- [ ] ¿Disclaimers en contenido generado por IA?

---

## Métricas de calidad anti-alucinación

def measure_hallucination_risk(responses: list[str]) -> float:
total_claims = count_claims(responses)
metrics = {
"specific_claims_ratio": count_specific_claims(responses) / total_claims,
"verification_suggestions": count_verification_prompts(responses),
"confidence_indicators": count_confidence_markers(responses)
}
return calculate_safety_score(metrics)

text

**KPIs recomendados**

| Métrica                                | Objetivo |
|----------------------------------------|----------|
| Tasa de “requiere verificación”        | > 70 % de afirmaciones específicas |
| Uso de disclaimers en info crítica     | 100 % |
| Claims sin fuente                      | < 10 % |

---

## Casos de estudio: implementación exitosa

1. **Consultora multinacional**  
   - Estandarizó prompts anti-alucinación  
   - Impuso verificación obligatoria  
   - Resultado: **90 % menos** datos incorrectos

2. **Medio digital**  
   - Política: la IA sólo sugiere ideas, no hechos no verificados  
   - Doble validación editorial  
   - Resultado: **0 incidentes** en 12 meses

---

> **Conclusión**  
> Las alucinaciones son inherentes a los modelos de lenguaje. Diseña prompts responsables, implementa validación y educa a los usuarios para minimizar su impacto y aprovechar de forma segura la IA.

---

### Lectura recomendada
*Temperatura y Top-P: cómo equilibrar creatividad y precisión en las respuestas de IA.*
