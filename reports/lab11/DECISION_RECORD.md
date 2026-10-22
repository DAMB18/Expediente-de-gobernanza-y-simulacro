# Decision record

## Evidencia
- 8 riesgos documentados y puntuados (likelihood x impact) en `reports/lab11/risk_register_scored.csv`.
- Riesgos de mayor prioridad: **R02 (9)** — el operador confia en el score del tablero sin revisar la evidencia del caso (sesgo de automatizacion); **R01 (8)** y **R07 (8)**, empatados — recall gap por subgrupo todavia sin medir, y huella de computo (Green AI) todavia sin verificar.
- `reports/lab11/rmf_summary.json`: GOVERN resuelto (owner y politica en `configs/governance.yml`), MAP resuelto (8 riesgos documentados), **MEASURE pendiente** (depende de la auditoria de equidad de LAB12, todavia no ejecutada), MANAGE resuelto (condicion de retiro y mecanismo de apelacion documentados en `student_inputs/lab11_impact.yml`).
- Pruebas automatizadas: 10/10 pasan (`uv run pytest -q`), incluyendo el rechazo de identificadores directos prohibidos y de campos sin completar (`REPLACE`).

## Incertidumbre
- Los controles de R01 (recall_gap), R03 (fpr_gap) y R07 (model_kb) dependen de metricas que todavia no existen: se generan en LAB12 y LAB13. Hasta entonces su riesgo residual real es desconocido, no solo "alto" en el papel.
- El control de R02 (mostrar siempre la evidencia y capacitar al operador) es organizacional, no tecnico: ninguna prueba automatizada puede verificarlo; depende de que el Institutional Owner lo implemente y lo sostenga.
- El dataset es sintetico (`docs/DATASET_CARD.md`): no se puede afirmar que su dinamica estadistica (por ejemplo la relacion entre `protected_group` y `target`) represente un caso real, lo que limita cuanto se puede generalizar de cualquier metrica futura.

## Responsable
- **Institutional Owner**: autoridad final de despliegue y punto de contacto para apelaciones (segun `student_inputs/raci.csv` y `lab11_impact.yml`).
- **Data Scientist**: responsable de que LAB12 mida `recall_gap` y `fpr_gap` por subgrupo antes de cualquier decision de despliegue.
- **ML Engineer**: responsable de medir `model_kb` y `latency_ms` en LAB13 contra las puertas de `configs/governance.yml`.

## Decision: REVIEW
No se recomienda **GO** todavia: MEASURE sigue pendiente — ninguno de los tres riesgos cuantitativos de mayor prioridad (R01, R02 vía su control organizacional, R07) tiene evidencia medida, solo evidencia planeada para LAB12/LAB13. Tampoco se recomienda **STOP**: el sistema esta bien gobernado en el papel (GOVERN, MAP y MANAGE completos, 10/10 pruebas automatizadas pasan, sin senal de dano). La condicion para pasar a GO es completar LAB12 (auditoria de equidad) y LAB13 (puertas de calidad y gobernanza) y confirmar que ningun riesgo de alta prioridad (R01, R02, R07) se materializo por encima de los umbrales declarados en `configs/governance.yml`.
