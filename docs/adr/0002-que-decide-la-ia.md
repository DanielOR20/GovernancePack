# ADR-0002: Cálculos solares deterministas y validación humana obligatoria en cotizaciones, bonos y borrado

- Estado: Aceptado
- Fecha: 2026-11-13
- Decide: Daniel Ortega, desarrollador full stack

## Contexto

Sol de Casa estima el ahorro eléctrico mensual, el número de paneles solares de 550W (`panelesNecesarios`), el retorno de inversión (ROI) y gestiona el embudo de visitas técnicas, bonos de referidos (`₡50.000`) y permisos RBAC. Como estas cifras impactan decisiones financieras de familias y los márgenes operativos de la empresa, delegar el cálculo o la aprobación de contratos y bonos a un modelo probabilístico de IA generaría cotizaciones inconsistentes, promesas comerciales erróneas y fugas de dinero por visitas falsas.

## Decisión

El producto no ejecuta modelos de IA en tiempo de ejecución; todas las estimaciones se calculan con fórmulas deterministas auditables y toda decisión comercial, técnica o administrativa crítica requiere validación humana explícita:
- La Calculadora Solar (`src/views/CalculatorView.jsx`) usa únicamente aritmética determinista (`consumoKwh = Math.round(montoFactura / 105)`, `ahorroMensual = Math.round(montoFactura * 0.88)` y `panelesNecesarios = Math.max(4, Math.ceil(consumoKwh / 65))`) y presenta el resultado como estimación preliminar, nunca como contrato vinculante automático.
- Ninguna cotización se convierte en instalación sin que un asesor humano (`editor` o `admin`) registre el expediente y actualice manualmente el estado de la visita técnica (`Agendada`, `Inspección Realizada`, `Propuesta Enviada`, `Instalación en Curso`) en `src/views/OperationsView.jsx`.
- Los bonos del programa de referidos (`₡50.000` en `ClientDashboardView.jsx` o limpieza y mantenimiento en `RewardsScreen.jsx`) nunca se liquidan por el simple agendamiento; requieren que el vecino referido firme contrato y complete la instalación.
- El cambio de roles de usuario, la edición de parámetros globales del algoritmo (`tarifaKwh`, `bonoReferido`, `factorSolar`, `margenMinimo`) y la eliminación de usuarios o expedientes nunca se automatizan: están restringidos al rol `admin` y requieren confirmación humana explícita (`window.confirm` en `src/views/AdminFinancialView.jsx` y validación de `PERMISSIONS.DELETE_CRITICAL_RECORDS` en `src/views/OperationsView.jsx`).

## Alternativas descartadas

- Usar un LLM en tiempo de ejecución para cotizar paneles y ahorro según texto libre del vecino: descartado porque las alucinaciones numéricas comprometerían el cálculo de kWp, el ROI y la confianza del cliente, además de introducir latencia y costo por token innecesarios.
- Automatizar la aprobación de propuestas solares o el pago de bonos de referidos al enviar el formulario de la calculadora: descartado porque sin inspección presencial del techo (sombras, estructura de zinc/teja/concreto y cuadro eléctrico) ni contrato firmado se generarían pérdidas operativas e incentivos fraudulentos.

## Consecuencias

Se garantiza total transparencia, repetibilidad matemática y cero costo de inferencia en producción, evitando cotizaciones alucinadas o pagos de bonos sin respaldo. A cambio, el cierre de cada proyecto depende de que un asesor humano realice la visita técnica y actualice manualmente el estado en `OperationsView.jsx`, y cualquier ajuste a la tarifa eléctrica o factor solar debe ser aplicado manualmente por un `Super Admin` en `AdminFinancialView.jsx`.

## Cómo se verifica

- En `src/views/CalculatorView.jsx` (líneas 24-37) se comprueba que los cálculos de consumo, ahorro, paneles, potencia kWp y CO2 son funciones matemáticas puras sin llamadas a APIs externas.
- En `src/App.jsx` (líneas 66-95), `src/components/common/ProtectedRoute.jsx` y `src/components/common/Can.jsx` se verifica que las vistas operativas y financieras bloquean el acceso a roles no autorizados mostrando `AccessDenied.jsx` (HTTP 403 visual).
- En `src/views/OperationsView.jsx` (líneas 93-100 y 293-309) y `src/views/AdminFinancialView.jsx` (líneas 48-65) se comprueba que el borrado de expedientes exige `hasPermission(PERMISSIONS.DELETE_CRITICAL_RECORDS)` y la baja de usuarios exige confirmación en `window.confirm`.
