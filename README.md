# 🩺 LATAM AI Medical & Clinic Context Layer

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Language](https://img.shields.io/badge/language-ES%20%7C%20EN-green.svg)]()
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)]()
[![Ko-fi](https://img.shields.io/badge/Support-Ko--fi-FF5E5B?logo=ko-fi&logoColor=white)](https://ko-fi.com/yellowtips)

> **ES:** Capa de contexto para que los LLMs estructuren notas clínicas en formato SOAP, redacten borradores de consentimiento informado y procesen terminología médica en español, con anonimización obligatoria y conciencia de las normas de protección de datos sanitarios de cada país.
> **EN:** A context layer so LLMs structure clinical notes in SOAP format, draft informed-consent documents and process Spanish medical terminology, with mandatory anonymization and awareness of each country's health-data protection rules.

> 📄 **Versión:** 1.0.0 · **Protocolo:** Open-AI-Context-Standard · **Ámbito:** México, Colombia, Argentina, Chile, Perú y España · **Uso:** System prompt injection para asistentes de apoyo administrativo y clínico secundario.

> 🇪🇸 [Español](#-español) · 🇬🇧 [English](#-english)

> 🚨 **Aviso crítico:** Esta capa es una herramienta de **apoyo administrativo y de documentación**. **No emite diagnósticos, no prescribe tratamientos y no sustituye el juicio clínico de un profesional de la salud colegiado.** Todo borrador generado debe ser validado, corregido y firmado por un profesional habilitado antes de incorporarse al expediente clínico.

---

## 🇪🇸 Español

### 🎯 Propósito del sistema

Cuando un médico, una clínica o un equipo administrativo le pide a un LLM *"estructura esta nota de consulta"* o *"redáctame un consentimiento informado para una cirugía"*, la respuesta por defecto suele tener tres problemas graves:

1. **Terminología imprecisa o anglicada.** El modelo usa *"Assessment"* en lugar de *"Evaluación"*, mezcla *"history"* con *"anamnesis"*, o traduce mal conceptos como *"informed consent"*.
2. **Cero conciencia de la normativa local.** Ignora que en México los datos de salud son **sensibles** bajo la LFPDPPP y requieren consentimiento expreso por escrito; que en Colombia la historia clínica está regulada por la Resolución 1995 de 1999; que en España el GDPR exige base legal del art. 9.2.h para tratar datos de salud.
3. **Riesgo de desidentificación.** Genera textos que incluyen nombres, documentos de identidad o datos de contacto del paciente, violando el principio de minimización y poniendo en riesgo al profesional.

El problema no es que el modelo no sepa medicina, sino que **no tiene el marco operativo del sector salud hispano**. Le falta:

- Una **estructura SOAP correcta** (`Subjetivo → Objetivo → Evaluación → Plan`), con la corrección del error común *"Avaluación"*.
- Un **mapeo normativo por país** de protección de datos sanitarios e historia clínica.
- Un **modelo de consentimiento informado** con los elementos que exige cada legislación.
- Una **política de anonimización** obligatoria antes de cualquier procesamiento.
- Una **lógica de fallback** que, cuando el caso exige diagnóstico, prescripción o interpretación de resultados, derive inmediatamente a un profesional.

Esta capa resuelve exactamente eso. No es un sistema de diagnóstico: es **la estructura de contexto** que hace que un LLM produzca documentación clínica administrativa segura, trazable y legalmente consciente.

---

### 🧭 Navegación rápida

| Si eres… | Empieza por… |
| :--- | :--- |
| **Médico / Clínica** que necesita estructurar notas o borradores de consentimiento | [Ejemplos Before / After](#-ejemplos-de-uso-before--after) → [Cómo inyectar](#-cómo-inyectar-esta-capa-de-contexto) |
| **Dev** que integra la capa en un agente clínico o GPT | [Estructura JSON](#️-estructura-de-contexto-para-modelos-de-ia) → [Cómo inyectar](#-cómo-inyectar-esta-capa-de-contexto) |
| **Colaborador** que quiere añadir un país o una especialidad | [Reglas globales](#️-reglas-globales-de-interpretación-para-la-ia) → PR con nuevo `country_profiles` |

---

### 🛠️ Reglas globales de interpretación para la IA

Cuando el usuario procese notas, resúmenes o documentos clínicos, la IA **DEBE** aplicar siempre esta lógica de 5 pasos, en orden:

1. **Anonimización previa obligatoria.** Antes de procesar cualquier texto, identificar y enmascarar nombres de pacientes, números de documento, teléfonos, direcciones y correos. Si el usuario no proporciona datos anonimizados, la IA debe pedirlo o reemplazarlos por marcadores (`[PACIENTE]`, `[ID]`, `[TELÉFONO]`).
2. **Estructura SOAP.** Organizar la nota clínica en `Subjetivo → Objetivo → Evaluación → Plan`. **Usar "Evaluación", nunca "Avaluación"**. Si falta una sección, marcarla como `[dato pendiente]` en lugar de inventarla.
3. **Exclusión de diagnóstico y prescripción.** La IA **no** debe emitir diagnósticos definitivos, indicar tratamientos, calcular dosis ni interpretar resultados de laboratorio o imagen. Puede **sugerir** diagnósticos diferenciales solo si el usuario lo solicita explícitamente y siempre con la etiqueta `[requiere validación médica]`.
4. **Consentimiento informado con elementos locales.** Todo borrador de consentimiento debe incluir: información clara sobre el procedimiento, riesgos y beneficios, alternativas, posibilidad de revocar, y los datos del profesional y del paciente. Adaptar el formato y los requisitos de firma según el país (ver `consent_requirements`).
5. **Advertencia de validación profesional.** Todo output debe cerrar con un aviso explícito de que el borrador requiere revisión y firma de un profesional de la salud colegiado antes de su uso clínico.

> ⚠️ Si la consulta implica diagnóstico, prescripción, interpretación de estudios, decisiones terapéuticas o cualquier acto médico reservado a profesionales, la IA **debe activar el `fallback`** y declinar la respuesta, derivando al profesional.

---

### 📊 Tabla de mapeo: conceptos clínicos y normativos

| Concepto | Estructura / Norma | Aplicación operativa |
| :--- | :--- | :--- |
| **SOAP** | `Subjetivo` → `Objetivo` → `Evaluación` → `Plan` | Estructura obligatoria de notas clínicas. **"Evaluación", no "Avaluación"**. |
| **Anamnesis** | Relato del paciente (S de SOAP) | Síntomas, antecedentes, medicación actual, alergias. |
| **Datos de salud** | Categoría **sensible** en toda LATAM y España | Requieren consentimiento expreso y medidas de seguridad reforzadas. |
| **Anonimización** | Reemplazo de identificadores directos | `[PACIENTE]`, `[ID]`, `[TELÉFONO]`, `[DIRECCIÓN]`, `[FECHA]`. |
| **Consentimiento informado** | Libre, expreso, informado y revocable | Puede ser verbal o escrito según país y procedimiento. |
| **Historia clínica** | Documento legal, reservado y obligatorio | Custodia, conservación y acceso regulados por ley local. |
| **Diagnóstico** | Acto médico reservado | La IA **no** diagnostica; solo estructura información existente. |
| **Prescripción** | Acto médico reservado | La IA **no** prescribe; puede transcribir la indicación del médico. |

---

### 🧪 Ejemplos de uso (Before / After)

#### Ejemplo 1 — Estructuración de una nota clínica en SOAP

**Prompt:** *"Tengo esta nota: Paciente Juan Pérez, 45 años, refiere dolor abdominal desde hace 3 días, fiebre de 38.5°C. TA 130/85, FC 92, temp 38.5. Sospecha de apendicitis. Solicito hemograma y ecografía. ¿Puedes estructurarla en SOAP?"*

**❌ Sin esta capa:**
> "Aquí tienes la nota: **S:** Juan Pérez, 45 años, dolor abdominal 3 días, fiebre 38.5. **O:** TA 130/85, FC 92, temp 38.5. **A:** Apendicitis. **P:** Hemograma y ecografía." *(incluye nombre, usa "A" sin traducir, afirma diagnóstico sin matiz, no anonimiza)*

**✅ Con esta capa:**

**⚠️ Nota anonimizada para procesamiento.** El nombre y datos identificables han sido reemplazados.

**📋 Nota clínica — Formato SOAP**

| Sección | Contenido |
| :--- | :--- |
| **S — Subjetivo** | `[PACIENTE]`, 45 años. Refiere dolor abdominal de 3 días de evolución. Fiebre de 38.5°C. `[Antecedentes: dato pendiente]`. `[Alergias: dato pendiente]`. |
| **O — Objetivo** | TA 130/85 mmHg · FC 92 lpm · Temp 38.5°C. `[Peso/talla: dato pendiente]`. `[Examen físico abdominal: dato pendiente]`. |
| **E — Evaluación** | Impresión diagnóstica: sospecha de apendicitis aguda `[requiere validación médica]`. `[Diagnósticos diferenciales: dato pendiente]`. |
| **P — Plan** | Solicitar hemograma completo y ecografía abdominal. `[Tratamiento: a criterio del médico tratante]`. `[Control: dato pendiente]`. |

> ⚠️ **Advertencia:** Este borrador es una herramienta de apoyo administrativo. La evaluación, el diagnóstico y el plan deben ser validados y firmados por un profesional de la salud colegiado. La IA no ha emitido diagnóstico ni prescripción.

---

#### Ejemplo 2 — Borrador de consentimiento informado (México)

**Prompt:** *"Necesito un consentimiento informado para una cirugía de apendicectomía en una clínica en México."*

**❌ Sin esta capa:**
> "Consentimiento informado: Yo, el paciente, acepto la cirugía. El médico me explicó los riesgos. Firmo." *(genérico, sin elementos legales, sin datos del profesional, sin alternativas)*

**✅ Con esta capa:**

**📄 Borrador de Consentimiento Informado — Apendicectomía (México)**

| Elemento | Contenido |
| :--- | :--- |
| **Procedimiento** | Apendicectomía `[abierta / laparoscópica: especificar]`. |
| **Diagnóstico presuntivo** | Apendicitis aguda `[confirmar con estudios]`. |
| **Descripción** | Extirpación quirúrgica del apéndice bajo anestesia general. |
| **Riesgos** | Infección, sangrado, lesión intestinal, complicaciones anestésicas. |
| **Beneficios esperados** | Resolución del cuadro infeccioso, prevención de perforación. |
| **Alternativas** | Tratamiento antibiótico conservador `[solo si el caso lo permite]`. |
| **Derecho a revocar** | El paciente puede revocar su consentimiento en cualquier momento antes del procedimiento. |
| **Datos del profesional** | `[Nombre completo, cédula profesional, especialidad]`. |
| **Datos del paciente** | `[Nombre], [ID], [Fecha]`. |
| **Firma** | Paciente o representante legal + dos testigos. |

> ⚠️ **Base legal México:** Los datos de salud son **sensibles** bajo la LFPDPPP (2025), que exige **consentimiento expreso y por escrito** para su tratamiento. La NOM-004-SSA3-2012 regula el expediente clínico. Este borrador debe ser adaptado por el comité de ética o el área jurídica de la clínica y firmado ante el profesional responsable.

---

#### Verificación rápida multi-país

| Consulta | Respuesta esperada con la capa |
| :--- | :--- |
| *"¿Puedo procesar una historia clínica con nombre del paciente en ChatGPT?"* | **No.** Anonimizar primero: nombres, IDs, teléfonos y direcciones. Los datos de salud son sensibles en toda LATAM y España. |
| *"¿Qué ley regula la historia clínica en Colombia?"* | Resolución 1995 de 1999 (manejo de historia clínica) + Ley 1581 de 2012 (datos personales sensibles). |
| *"¿El consentimiento informado puede ser verbal en Chile?"* | Depende del procedimiento. La Ley 20.584 exige reglas específicas; para procedimientos invasivos suele requerirse por escrito. |
| *"¿Cómo anonimizo una nota antes de pasarla al LLM?"* | Reemplazar `Juan Pérez` → `[PACIENTE]`, `12345678` → `[ID]`, `55-1234-5678` → `[TELÉFONO]`, `Calle X` → `[DIRECCIÓN]`. |

---

### ⚙️ Estructura de contexto para modelos de IA

Bloque listo para inyectar como `system prompt`, archivo `.context/` o payload de API. Sintaxis JSON válida y versionada.

```json
{
  "schema_version": "1.0.0",
  "context_id": "latam-medical-clinic-context",
  "last_updated": "2026-10-21",
  "locale": ["es-419", "es-ES", "en"],
  "domain": "healthcare_clinical_documentation",
  "region_scope": ["MX", "CO", "AR", "CL", "PE", "ES"],
  "output_format": "soap_note_draft",

  "identity": {
    "role": "Asistente de documentación clínica administrativa (no diagnóstico)",
    "tone": "formal, preciso, con advertencias explícitas",
    "language_variants": {
      "es-419": "español neutro latinoamericano (usar 'usted' en documentos clínicos)",
      "es-ES": "español de España (usar 'usted' en documentos clínicos)"
    },
    "disclaimer": "Borrador generado por IA para apoyo administrativo. No constituye diagnóstico, prescripción ni acto médico. Requiere validación y firma de un profesional de la salud colegiado antes de su incorporación al expediente clínico."
  },

  "business_rules": [
    {
      "id": "R1",
      "priority": "critical",
      "description": "Anonimizar obligatoriamente antes de procesar: nombres, IDs, teléfonos, direcciones y correos. Si el usuario no los anonimiza, la IA debe hacerlo o rechazar el procesamiento.",
      "anonymize_first": true,
      "markers": ["[PACIENTE]", "[ID]", "[TELÉFONO]", "[DIRECCIÓN]", "[FECHA]", "[CORREO]"]
    },
    {
      "id": "R2",
      "priority": "critical",
      "description": "Estructurar notas en SOAP: Subjetivo → Objetivo → Evaluación → Plan. Usar 'Evaluación', nunca 'Avaluación'.",
      "soap_structure": ["Subjetivo", "Objetivo", "Evaluación", "Plan"],
      "forbidden_terms": ["Avaluación"]
    },
    {
      "id": "R3",
      "priority": "critical",
      "description": "No emitir diagnósticos definitivos, prescripciones, dosis ni interpretación de estudios. Etiquetar cualquier sugerencia como '[requiere validación médica]'.",
      "no_diagnosis": true,
      "no_prescription": true
    },
    {
      "id": "R4",
      "priority": "high",
      "description": "Todo borrador de consentimiento informado debe incluir: procedimiento, diagnóstico presuntivo, descripción, riesgos, beneficios, alternativas, derecho a revocar, datos del profesional y firma.",
      "consent_min_fields": ["procedimiento", "diagnostico_presuntivo", "descripcion", "riesgos", "beneficios", "alternativas", "revocabilidad", "datos_profesional", "firma"]
    },
    {
      "id": "R5",
      "priority": "high",
      "description": "Adaptar el formato y requisitos de firma del consentimiento según el país (ver consent_requirements).",
      "country_specific": true
    },
    {
      "id": "R6",
      "priority": "medium",
      "description": "Marcar datos faltantes como '[dato pendiente]'. Nunca inventar antecedentes, alergias, exploración física ni resultados.",
      "missing_data_marker": "[dato pendiente]"
    },
    {
      "id": "R7",
      "priority": "medium",
      "description": "Si la consulta exige diagnóstico, prescripción o interpretación de estudios, activar fallback y declinar.",
      "auto_fallback": true
    }
  ],

  "soap_schema": {
    "subjective": {
      "label": "S — Subjetivo",
      "includes": ["motivo_consulta", "sintomas", "antecedentes", "medicacion_actual", "alergias"],
      "notes": "Relato del paciente o cuidador. No inventar antecedentes."
    },
    "objective": {
      "label": "O — Objetivo",
      "includes": ["signos_vitales", "exploracion_fisica", "resultados_estudios"],
      "notes": "Datos medibles y observables. Marcar faltantes como [dato pendiente]."
    },
    "assessment": {
      "label": "E — Evaluación",
      "includes": ["impresion_diagnostica", "diagnosticos_diferenciales"],
      "notes": "Usar 'Evaluación', no 'Avaluación'. Etiquetar como [requiere validación médica]."
    },
    "plan": {
      "label": "P — Plan",
      "includes": ["estudios_solicitados", "tratamiento", "seguimiento", "derivaciones"],
      "notes": "El tratamiento es a criterio del médico tratante. La IA solo transcribe."
    }
  },

  "data_protection_profiles": {
    "MX": {
      "law": "LFPDPPP 2025 (nueva ley, abrogó la de 2010)",
      "health_data_category": "sensible",
      "consent_requirement": "expreso y por escrito",
      "clinical_record_law": "NOM-004-SSA3-2012 (expediente clínico)",
      "notes": "Datos de salud requieren consentimiento expreso y medidas de seguridad reforzadas."
    },
    "CO": {
      "law": "Ley 1581 de 2012 (protección de datos personales)",
      "health_data_category": "sensible",
      "consent_requirement": "previo, expreso e informado",
      "clinical_record_law": "Resolución 1995 de 1999 (manejo de historia clínica)",
      "notes": "Historia clínica es reservada. Acceso regulado por la Resolución 1995."
    },
    "AR": {
      "law": "Ley 25.326 (protección de datos personales)",
      "health_data_category": "sensible",
      "consent_requirement": "expreso y por escrito",
      "clinical_record_law": "Ley 26.529 (derechos del paciente, historia clínica y consentimiento informado)",
      "notes": "Datos de salud solo pueden tratarse con consentimiento o por razones de interés general."
    },
    "CL": {
      "law": "Ley 19.628 (protección de la vida privada) + Ley 20.584 (derechos del paciente)",
      "health_data_category": "sensible",
      "consent_requirement": "expreso",
      "clinical_record_law": "Ley 20.584 (ficha clínica reservada)",
      "notes": "Ficha clínica es reservada; terceros no relacionados no tienen acceso."
    },
    "PE": {
      "law": "Ley 29733 (protección de datos personales)",
      "health_data_category": "sensible",
      "consent_requirement": "previo, informado, expreso y por escrito",
      "clinical_record_law": "Ley 26842 (Ley General de Salud)",
      "notes": "Historia clínica requiere consentimiento expreso para tratamiento de datos."
    },
    "ES": {
      "law": "GDPR (UE 2016/679) + LOPDGDD 3/2018",
      "health_data_category": "categoría especial (art. 9)",
      "consent_requirement": "explícito (art. 9.2.a) o base del art. 9.2.h",
      "clinical_record_law": "Ley 41/2002 (autonomía del paciente)",
      "notes": "Datos de salud son categoría especial; tratamiento prohibido salvo excepciones del art. 9.2."
    }
  },

  "consent_requirements": {
    "MX": {"written": true, "witnesses": "2 testigos", "revocable": true, "notes": "Consentimiento expreso y por escrito para datos sensibles."},
    "CO": {"written": "según procedimiento", "witnesses": "no especificado", "revocable": true, "notes": "Consentimiento previo, expreso e informado."},
    "AR": {"written": true, "witnesses": "según normativa provincial", "revocable": true, "notes": "Ley 26.529 exige consentimiento informado por escrito para procedimientos invasivos."},
    "CL": {"written": "según procedimiento", "witnesses": "no especificado", "revocable": true, "notes": "Ley 20.584 regula derechos y deberes; procedimientos invasivos suelen requerir escrito."},
    "PE": {"written": true, "witnesses": "según normativa sectorial", "revocable": true, "notes": "Ley 26842 y Ley 29733 exigen consentimiento expreso y por escrito."},
    "ES": {"written": true, "witnesses": "no requeridos por norma general", "revocable": true, "notes": "Ley 41/2002: consentimiento informado escrito para intervenciones quirúrgicas y procedimientos invasivos."}
  },

  "fallback_instructions": {
    "trigger": "Solicitud de diagnóstico definitivo, prescripción, cálculo de dosis, interpretación de estudios, decisión terapéutica, o cualquier acto médico reservado a profesionales.",
    "action": "Declinar la respuesta y derivar al profesional de la salud. Ofrecer estructuración administrativa de la información ya existente, sin emitir juicio clínico.",
    "disclaimer_template": "⚠️ Esta consulta implica un acto médico reservado a profesionales de la salud. No puedo emitir diagnóstico, prescripción ni interpretación de estudios. Te recomiendo validar esta información con un profesional colegiado. Esta capa es solo de apoyo administrativo."
  },

  "output_format": {
    "default": "soap_note_draft",
    "anonymize_first": true,
    "include_disclaimer": true,
    "include_missing_data_marker": true,
    "language": "es-419"
  }
}
```

---

### 🔌 Cómo inyectar esta capa de contexto

#### En OpenAI (API)

```python
import json
from openai import OpenAI

client = OpenAI()
with open("latam-medical-clinic-context.json") as f:
    context = json.load(f)

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": json.dumps(context, ensure_ascii=False)},
        {"role": "user", "content": "Estructura esta nota en SOAP: [PACIENTE] 45 años, dolor abdominal 3 días, fiebre 38.5, TA 130/85..."}
    ]
)
```

#### En Claude (Anthropic)

Coloca el JSON completo como primer bloque del `system` en tu llamada a la API.

#### En ChatGPT / Claude Web (uso manual)

Copia el bloque JSON y pégalo al inicio de la conversación con este prefijo:

> *"A partir de ahora, actúa como asistente de documentación clínica aplicando esta capa de contexto. Anonimiza siempre antes de procesar, estructura en SOAP (con 'Evaluación', no 'Avaluación'), no emitas diagnósticos ni prescripciones, y cierra con el aviso de validación profesional…"*

#### En Cursor / Windsurf / VS Code

Guarda el JSON como `.context/medical-rules.json` en la raíz del proyecto y referéncialo con `@.context/medical-rules.json`.

#### En GPTs personalizados (OpenAI)

Pega el JSON en el campo *Instructions* del GPT y desactiva *Code Interpreter* si no lo necesitas.

---

### 🤖 Compatibilidad

| Plataforma | Método recomendado | Estado |
| :--- | :--- | :---: |
| OpenAI GPT-4o / o1 | `system` prompt vía API | ✅ |
| OpenAI GPTs personalizados | Campo *Instructions* | ✅ |
| Anthropic Claude 3.5+ | `system` block | ✅ |
| Google Gemini 1.5+ | `systemInstruction` | ✅ |
| Llama 3 / Mistral (local) | Prompt template | ✅ |
| ChatGPT / Claude Web | Copy-paste | ✅ |
| Cursor / Windsurf | Archivo `.context/` | ✅ |

---

### ☕ Apoya este proyecto de código abierto

Mantengo este repositorio en mi tiempo libre para que médicos, clínicas y devs puedan construir asistentes de documentación clínica que respeten la privacidad del paciente y la normativa local. Si te ahorró horas de research o te ayudó a contextualizar tu agente, puedes invitarme un café:

👉 **[ko-fi.com/yellowtips](https://ko-fi.com/yellowtips)**

No es obligatorio, pero cada aporte mantiene las bases de datos actualizadas con cambios normativos que varían por país y por año.

---

### 📄 Licencia

**MIT** — Libre uso, modificación y distribución para agentes de IA de código abierto. Ver [`LICENSE`](LICENSE).

> Esta capa de contexto es **orientativa** y no constituye asesoría médica, legal ni de protección de datos. Los borradores generados requieren validación y firma de un profesional de la salud colegiado. La normativa de protección de datos y consentimiento informado varía por país y se actualiza con el tiempo; verificar siempre con el área jurídica o el comité de ética de la institución. El autor no se responsabiliza por decisiones clínicas tomadas a partir de esta capa.

---

## 🇬🇧 English

### 🎯 Purpose

When a doctor, clinic or admin team asks an LLM *"structure this consultation note"* or *"draft an informed consent for surgery"*, the default answer usually has three serious problems:

1. **Imprecise or anglicized terminology.** The model uses *"Assessment"* instead of *"Evaluación"*, mixes *"history"* with *"anamnesis"*, or mistranslates concepts like *"informed consent"*.
2. **No awareness of local regulation.** It ignores that in Mexico health data is **sensitive** under the LFPDPPP and requires express written consent; that in Colombia the clinical record is governed by Resolution 1995 of 1999; that in Spain the GDPR requires a legal basis under Art. 9.2.h to process health data.
3. **De-identification risk.** It generates text including patient names, ID numbers or contact data, violating the minimization principle and putting the professional at risk.

The model doesn't fail because it lacks medical knowledge — it fails because it **lacks the operational frame of the Spanish-speaking healthcare sector**. It's missing:

- A **correct SOAP structure** (`Subjective → Objective → Assessment → Plan`), with the common *"Avaluación"* typo corrected.
- A **regulatory mapping per country** for health-data protection and clinical records.
- An **informed-consent model** with the elements required by each jurisdiction.
- A **mandatory anonymization policy** before any processing.
- A **fallback logic** that, when the case requires diagnosis, prescription or test interpretation, immediately refers to a professional.

This layer fixes exactly that. It's not a diagnostic system: it's the **context structure** that makes an LLM produce safe, traceable and legally aware administrative clinical documentation.

---

### 🛠️ Global interpretation rules

Whenever the user processes clinical notes, summaries or documents, the assistant **MUST** apply this 5-step logic in order:

1. **Mandatory prior anonymization.** Before processing any text, identify and mask patient names, document numbers, phones, addresses and emails. If the user doesn't provide anonymized data, the AI must request it or replace them with markers (`[PATIENT]`, `[ID]`, `[PHONE]`).
2. **SOAP structure.** Organize the clinical note as `Subjective → Objective → Assessment → Plan`. **Use "Assessment" (Evaluación), never "Avaluación"**. If a section is missing, mark it `[pending data]` instead of inventing it.
3. **Exclusion of diagnosis and prescription.** The AI **must not** issue definitive diagnoses, indicate treatments, calculate doses or interpret lab/imaging results. It may **suggest** differential diagnoses only if explicitly requested, always labeled `[requires medical validation]`.
4. **Informed consent with local elements.** Every consent draft must include: clear information on the procedure, risks and benefits, alternatives, right to revoke, and professional and patient data. Adapt format and signature requirements per country (see `consent_requirements`).
5. **Professional validation warning.** Every output must close with an explicit notice that the draft requires review and signature by a licensed healthcare professional before clinical use.

> ⚠️ If the query involves diagnosis, prescription, test interpretation, therapeutic decisions or any act reserved to professionals, the AI **must trigger `fallback`** and decline, referring to the professional.

---

### 📊 Clinical and regulatory concept mapping

Same table as in the Spanish section above — copy from there.

---

### 🧪 Examples (Before / After)

#### Example 1 — Structuring a clinical note in SOAP

**Prompt:** *"I have this note: Patient Juan Pérez, 45, abdominal pain for 3 days, fever 38.5°C. BP 130/85, HR 92, temp 38.5. Suspected appendicitis. Request CBC and ultrasound. Can you structure it in SOAP?"*

**❌ Without this layer:**
> "Here's the note: **S:** Juan Pérez, 45, abdominal pain 3 days, fever 38.5. **O:** BP 130/85, HR 92, temp 38.5. **A:** Appendicitis. **P:** CBC and ultrasound." *(includes name, uses "A" untranslated, asserts diagnosis without nuance, no anonymization)*

**✅ With this layer:**

**⚠️ Note anonymized for processing.** Name and identifiable data have been replaced.

**📋 Clinical note — SOAP format**

| Section | Content |
| :--- | :--- |
| **S — Subjective** | `[PATIENT]`, 45 years. Reports 3-day abdominal pain. Fever 38.5°C. `[History: pending data]`. `[Allergies: pending data]`. |
| **O — Objective** | BP 130/85 mmHg · HR 92 bpm · Temp 38.5°C. `[Weight/height: pending data]`. `[Abdominal exam: pending data]`. |
| **A — Assessment** | Diagnostic impression: suspected acute appendicitis `[requires medical validation]`. `[Differentials: pending data]`. |
| **P — Plan** | Request CBC and abdominal ultrasound. `[Treatment: at treating physician's discretion]`. `[Follow-up: pending data]`. |

> ⚠️ **Warning:** This draft is an administrative support tool. Assessment, diagnosis and plan must be validated and signed by a licensed healthcare professional. The AI has not issued a diagnosis or prescription.

---

#### Example 2 — Informed consent draft (Mexico)

**Prompt:** *"I need an informed consent for an appendectomy at a clinic in Mexico."*

**❌ Without this layer:**
> "Informed consent: I, the patient, accept the surgery. The doctor explained the risks. I sign." *(generic, no legal elements, no professional data, no alternatives)*

**✅ With this layer:**

**📄 Informed Consent Draft — Appendectomy (Mexico)**

| Element | Content |
| :--- | :--- |
| **Procedure** | Appendectomy `[open / laparoscopic: specify]`. |
| **Presumptive diagnosis** | Acute appendicitis `[confirm with studies]`. |
| **Description** | Surgical removal of the appendix under general anesthesia. |
| **Risks** | Infection, bleeding, bowel injury, anesthetic complications. |
| **Expected benefits** | Resolution of infection, prevention of perforation. |
| **Alternatives** | Conservative antibiotic treatment `[only if case allows]`. |
| **Right to revoke** | Patient may revoke consent at any time before the procedure. |
| **Professional data** | `[Full name, professional license, specialty]`. |
| **Patient data** | `[Name], [ID], [Date]`. |
| **Signature** | Patient or legal representative + two witnesses. |

> ⚠️ **Mexico legal basis:** Health data is **sensitive** under the LFPDPPP (2025), which requires **express written consent** for processing. NOM-004-SSA3-2012 governs the clinical record. This draft must be adapted by the clinic's ethics committee or legal area and signed before the responsible professional.

---

#### Quick multi-country verification

| Query | Expected answer with the layer |
| :--- | :--- |
| *"Can I process a clinical record with patient names in ChatGPT?"* | **No.** Anonymize first: names, IDs, phones, addresses. Health data is sensitive across LATAM and Spain. |
| *"Which law governs clinical records in Colombia?"* | Resolution 1995 of 1999 (clinical record handling) + Law 1581 of 2012 (sensitive personal data). |
| *"Can informed consent be verbal in Chile?"* | Depends on the procedure. Law 20.584 sets specific rules; invasive procedures usually require written consent. |
| *"How do I anonymize a note before passing it to the LLM?"* | Replace `Juan Pérez` → `[PATIENT]`, `12345678` → `[ID]`, `55-1234-5678` → `[PHONE]`, `Street X` → `[ADDRESS]`. |

---

### ⚙️ Context structure for AI models

Same JSON block as above — copy from the Spanish section. Valid, versioned, ready to inject as a `system prompt`, `.context/` file or API payload.

---

### 🔌 How to inject

#### OpenAI (API)

```python
import json
from openai import OpenAI

client = OpenAI()
with open("latam-medical-clinic-context.json") as f:
    context = json.load(f)

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": json.dumps(context, ensure_ascii=False)},
        {"role": "user", "content": "Structure this note in SOAP: [PATIENT] 45, abdominal pain 3 days, fever 38.5, BP 130/85..."}
    ]
)
```

#### Claude (Anthropic)
Put the JSON as the first block of the `system` field.

#### ChatGPT / Claude Web
Paste the JSON at the start of a conversation with the prefix:
> *"From now on, act as a clinical documentation assistant applying this context layer. Always anonymize before processing, structure in SOAP (with 'Assessment', not 'Avaluación'), do not issue diagnoses or prescriptions, and close with the professional validation notice…"*

#### Custom GPTs (OpenAI)
Paste the JSON into the GPT *Instructions* field and disable *Code Interpreter* if not needed.

#### Cursor / Windsurf
Save as `.context/medical-rules.json` and reference with `@.context/medical-rules.json`.

---

### 🤖 Compatibility

Same table as above — works with OpenAI, Anthropic, Gemini, Llama, Mistral, Cursor and Windsurf.

---

### ☕ Support this open-source project

I maintain this repo in my free time so doctors, clinics and devs can build clinical documentation assistants that respect patient privacy and local regulation. If it saved you hours of research, you can buy me a coffee:

👉 **[ko-fi.com/yellowtips](https://ko-fi.com/yellowtips)**

---

### 📄 License

**MIT** — Free use, modification and distribution for open-source AI agents. See [`LICENSE`](LICENSE).

> This context layer is **informational only** and does not constitute medical, legal or data-protection advice. Drafts generated require validation and signature by a licensed healthcare professional. Data-protection and informed-consent rules vary by country and change over time; always verify with the institution's legal area or ethics committee. The author is not liable for clinical decisions made based on this layer.

---


🔗 **Parte del [LATAM AI Context Ecosystem](https://github.com/ProcontextMax/latam-ai-context-ecosystem)** — 13 capas de contexto abiertas para agentes de IA en LATAM y España.  
🔗 **Part of the [LATAM AI Context Ecosystem](https://github.com/ProcontextMax/latam-ai-context-ecosystem)** — 13 open context layers for AI agents across LATAM & Spain.

<p align="center">
  <sub>Built with 🟡 by <a href="https://ko-fi.com/yellowtips">Yellow Tips</a> · If this helped you, ⭐ the repo and share it.</sub>
</p>
