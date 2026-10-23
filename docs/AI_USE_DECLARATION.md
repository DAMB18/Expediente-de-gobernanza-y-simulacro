# Declaración de uso de inteligencia artificial

## Herramientas utilizadas
Claude (Anthropic), en su modalidad Cowork, vinculada al computador del estudiante mediante un puente de dispositivo. A diferencia de los proyectos previos de la Unidad 03, aqui la IA ejecuto directamente en el equipo del estudiante (via el puente) los comandos de Git (`git add`, `git commit`, configuracion local de `user.name`/`user.email`) y escribio archivos del proyecto (`student_inputs/*`, `docs/*`, `reports/lab11/DECISION_RECORD.md`, `reports/lab13/INCIDENT_POSTMORTEM.md`, y correcciones en `app.py` y `scripts/lab11_impact.py`). El estudiante ejecuto personalmente, en todos los casos, los scripts de entrenamiento, auditoria, gobernanza, el simulacro de incidente, el tablero Streamlit y `pytest`, y reviso cada archivo escrito por la IA antes de avanzar al siguiente paso.

## Propósito de cada uso
- LAB11: redaccion de `student_inputs/lab11_impact.yml`, `stakeholders.csv` y `risk_register.csv` (impacto previsto, mapa de interesados y 8 riesgos), fundamentada en `configs/governance.yml` y el RACI ya existente; redaccion de `reports/lab11/DECISION_RECORD.md` a partir de la salida real de `scripts/lab11_impact.py` (prioridades R02=9, R01=8, R07=8).
- Correccion de un error de sintaxis preexistente en `scripts/lab11_impact.py` (una cadena mal formada impedia ejecutar el script por completo) y commit separado de un cambio de formato automatico de `ruff` sin commitear en `check_environment.py`/`simulate_incident.py`, ambos detectados antes de empezar LAB11.
- LAB12: interpretacion de las metricas reales de equidad (globales, por grupo e interseccionales) de los modelos `logistic` y `tree` generadas por la ejecucion del estudiante, y redaccion de `student_inputs/lab12_decision.yml` (dano prioritario, metrica, tolerancia, evidencia, limitacion, recomendacion) a partir de esas cifras.
- LAB13: redaccion de `docs/MODEL_CARD.md` y `docs/RUNBOOK.md` con la evidencia acumulada de LAB11-13 (`reports/lab13/deployment_decision.json`, riesgos de LAB11, auditoria de LAB12); se completaron los campos de comunicacion y acciones correctivas de `reports/lab13/INCIDENT_POSTMORTEM.md`; correccion de un parametro de Streamlit (`use_container_width`) marcado como obsoleto desde 2025-12-31 en `app.py`, detectado por el propio estudiante al correr el tablero.
- Estructuracion de todos los commits de Git (esqueleto, formato, correccion de codigo y evidencia de cada laboratorio por separado) y redaccion de los mensajes.

## Prompts o decisiones relevantes
- El estudiante comparti el texto completo de las guias de LAB11, LAB12 y LAB13, y la ruta del proyecto ya descargado en su equipo.
- Decision explicita de preferir el modelo `logistic` sobre `tree` en LAB12 (y por lo tanto en LAB13, donde `scripts/lab13_governance.py` usa `logistic` por defecto) por mejor equidad, menor costo y ninguna perdida de utilidad frente a `tree` — decision tomada con evidencia cuantitativa de ambas corridas, no por omision.
- Decision explicita de no publicar el DSS en Streamlit Community Cloud por ahora; el checklist de LAB13 lo marca como opcional y el proyecto queda documentado y ejecutable localmente.
- El propio estudiante detecto y reporto el aviso de obsolescencia de `use_container_width` al correr el tablero; la IA confirmo la causa y aplico la correccion minima recomendada por Streamlit.

## Elementos verificados por el equipo
- Todas las cifras citadas en `MODEL_CARD.md`, `DECISION_RECORD.md`, `lab12_decision.yml` y `INCIDENT_POSTMORTEM.md` provienen de archivos generados por la ejecucion real del estudiante (`reports/lab11/risk_register_scored.csv`, `reports/lab12/{logistic,tree}/*`, `reports/lab13/deployment_decision.json`), leidos directamente del repositorio, no transcritos de memoria.
- Las 10 pruebas automatizadas (`uv run pytest -q`) pasan sobre el proyecto completo de LAB11 a LAB13.
- El tablero Streamlit (`uv run streamlit run app.py`) abre sin excepciones y muestra proposito, usos prohibidos, metricas, puertas y la advertencia de caso sintetico.
- La recomendacion `GO` de `scripts/lab13_governance.py` se verifico puerta por puerta contra `configs/governance.yml` (recall, recall_gap, fpr_gap, latencia, tamano del modelo), no se dio por buena solo por el campo `status`.

## Cambios realizados sobre resultados generados
- Ninguna cifra de equidad, costo o gobernanza fue modificada, redondeada de forma enganosa o inventada; todas provienen directamente de los archivos JSON/CSV generados por las corridas del estudiante.
- La interpretacion de que las celdas interseccionales con soporte menor a 15 casos no son confiables es un juicio metodologico explicito, no una forma de ocultar resultados incomodos: los valores extremos de esas celdas se reportan igual, con la advertencia de soporte insuficiente al lado.
- La decision `REVIEW` de LAB11 (por MEASURE pendiente) y el `GO` condicionado a revision humana de LAB13 se mantienen ambas explicitas en los documentos finales; no se forzo una sola conclusion de cierre para los tres laboratorios.

## Responsabilidad asumida
El estudiante (Daniel Alexander Batista) ejecuto personalmente todo el codigo de entrenamiento, auditoria, gobernanza, pruebas y el tablero en su propio equipo, reviso cada archivo antes de que el trabajo avanzara al siguiente paso, y puede explicar cualquier fragmento de analisis, codigo o conclusion presentada en esta entrega. Ningun dato, metrica o referencia fue inventado: todo proviene de ejecuciones reales documentadas en este repositorio. El dataset `territorial_risk_synthetic.csv` es sintetico y no representa personas, territorios ni tasas reales.
