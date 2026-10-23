# Incident simulation

- detected_at: 2026-10-23T01:30:38.169875+00:00
- signal: recall gap exceeded
- containment: REVIEW and safe baseline
- evidence: preserved report and model version
- communication: Data Scientist y ML Engineer notificados de inmediato (responsables tecnicos, student_inputs/raci.csv); el Institutional Owner informa a los representantes de las personas afectadas una vez evaluado el impacto, sin exponer datos personales (dataset sintetico en este ejercicio).
- corrective actions, owners and dates: (1) Data Scientist reaudita recall_gap/fpr_gap por subgrupo con el ultimo dato disponible, dentro de 2 dias habiles; (2) ML Engineer confirma o revierte al ultimo modelo que paso las 5 puertas de configs/governance.yml, dentro de 1 dia habil; (3) Institutional Owner cierra el postmortem con causa raiz y decision GO/REVIEW/STOP, dentro de 5 dias habiles desde la deteccion (docs/RUNBOOK.md, paso 6).
