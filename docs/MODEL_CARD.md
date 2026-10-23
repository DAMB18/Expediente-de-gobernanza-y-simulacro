# Model Card — complete after LAB13

## Intended use
Clasificador logistico (`LogisticRegression`, pipeline `src/inf8239_u04/model.py`) que asiste la priorizacion de casos sinteticos de riesgo territorial para revision humana (`app.py`, `student_inputs/lab11_impact.yml`). Usuarios: analistas y supervisores del equipo operativo que revisan la cola diaria a traves del tablero Streamlit. El sistema no sustituye la decision: solo prioriza y presenta evidencia; un responsable humano decide.

## Out-of-scope use
No usar para intervencion automatica, decision de elegibilidad de un programa ni declaracion de emergencia (usos prohibidos explicitos en `configs/governance.yml`). No representa personas, territorios ni tasas reales: el dataset es sintetico (`docs/DATASET_CARD.md`).

## Data and split
`data/territorial_risk_synthetic.csv`: 640 filas, 9 columnas, balance de `target` 55.2%/44.8%. Corte temporal (no aleatorio): entrenamiento hasta 2026-09-24, prueba desde 2026-09-25 (160 casos), sin fuga de futuro a pasado. Excluidas del modelo: `case_id`, `received_date`, `protected_group`, `target`; los identificadores directos (`name`, `document_number`, `email`, `phone`) son rechazados por `validate_schema()` si aparecen. **Limitacion documentada**: `age_band` esta marcado como `sensitive_features` en `configs/governance.yml` junto con `protected_group`, pero a diferencia de `protected_group` si se usa como variable predictora — puede actuar de proxy indirecto del atributo sensible excluido.

## Global and subgroup evaluation
Modelo seleccionado: **logistic** (ver justificacion Green AI abajo). Metricas globales sobre las 160 pruebas (`reports/lab13/deployment_decision.json`): accuracy 0.719, recall 0.717, fpr 0.279. Por `protected_group`: G1 (n=101) recall 0.653 / fpr 0.250; G2 (n=59) recall 0.791 / fpr 0.375 — `recall_gap=0.138` y `fpr_gap=0.125`, ambos dentro de las puertas de `configs/governance.yml` (`max_recall_gap`/`max_fpr_gap`=0.16).

Interseccional (`protected_group` x `age_band`, LAB12): la unica celda con soporte suficiente para interpretarse con confianza es **G1 | 45-59 (n=35)**, que concentra el peor desempeno real (recall 0.588, fpr 0.389) y explica buena parte del recall bajo de G1 en general. Las celdas G2 | 18-29 (n=11), G2 | 30-44 (n=14) y G2 | 45-59 (n=8) tienen muy poco soporte; sus valores extremos (recall/accuracy=1.0, fpr=0.0 o 1.0) son ruido de muestra pequena, no evidencia confiable.

Comparacion con `tree` (LAB12): mismo recall global (0.717) pero peor fpr (0.309), ambas brechas por fuera de las puertas (`recall_gap=0.181`, `fpr_gap=0.168`), 26% mas lento y 121% mas pesado — sin ninguna ventaja medible.

## Green AI evidence
`logistic`: ~0.07-0.17 s de entrenamiento (varia por corrida), 3.56 KB en disco, 0.033 ms de latencia por caso — muy por debajo de `max_latency_ms=500` y `max_model_kb=1024`. `tree` se descarto explicitamente por costo adicional sin mejora de equidad ni utilidad (ver `student_inputs/lab12_decision.yml`), consistente con el criterio Green AI: preferir la opcion suficiente, no la mas compleja.

## Limitations, human oversight and appeal
- Celdas interseccionales con `n<15` no deben interpretarse como evidencia (ver arriba).
- `age_band` sigue siendo predictora y sensible: una brecha observada no prueba discriminacion causal por si sola.
- Dataset sintetico: no representa personas ni tasas reales; cualquier generalizacion fuera de este curso requeriria datos reales y una nueva auditoria completa.
- `reports/lab13/deployment_decision.json` marca **GO** en los cinco controles automaticos (recall, recall_gap, fpr_gap, latencia, tamano), pero el campo `human_action` exige explicitamente "Document review and final decision": el GO automatico no autoriza el despliegue por si solo.
- Revision humana reforzada recomendada para el subgrupo G1 de 45 a 59 anios (n=35, mayor soporte y peor desempeno real) antes de cualquier uso.
- Apelacion: toda persona afectada por una decision derivada del sistema puede solicitar revision humana adicional ante el Institutional Owner (`student_inputs/lab11_impact.yml`).
- Retiro: el sistema se retira si `recall_gap`/`fpr_gap` superan los umbrales de `configs/governance.yml` en produccion, o si una auditoria externa reporta dano desproporcionado a un subgrupo protegido.
- Los 8 riesgos de `reports/lab11/risk_register_scored.csv` siguen vigentes; los de mayor prioridad son R02 (sesgo de automatizacion del operador, 9), R01 (recall gap, 8) y R07 (costo Green AI, 8 — ya mitigado al preferir `logistic`).

## Owner and review date
**Institutional Owner** (Accountable segun `student_inputs/raci.csv`) es responsable final de aprobar o rechazar el despliegue y punto de contacto para apelaciones. **Data Scientist** es responsable de volver a correr la auditoria de LAB12 si cambian los datos o el modelo. Proxima revision: antes de cualquier uso fuera del contexto academico de este curso, y en cualquier caso cada vez que cambien los datos de entrada o se reentrene el modelo.
