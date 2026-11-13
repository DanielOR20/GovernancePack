# ADR-0003: Almacenamiento local en navegador con aislamiento RBAC y cero transferencia de datos a terceros de IA

- Estado: Aceptado
- Fecha: 2026-11-13
- Decide: Daniel Ortega, desarrollador full stack

## Contexto

Sol de Casa maneja datos de usuarios del sistema (`MOCK_USERS` y `systemUsers`), expedientes de prospectos solares (`leads` con nombre, teléfono WhatsApp, ubicación residencial y monto de factura eléctrica en colones), métricas de ahorro de propietarios, códigos de referido (`referralCode`) y tokens de sesión JWT (`soldecasa_jwt_token`). Según la Ley 8968 de Costa Rica, los datos de contacto, dirección de vivienda y monto de facturación eléctrica constituyen datos personales de `Acceso restringido` que solo deben ser accedidos por los roles autorizados y nunca transferidos sin consentimiento a servicios externos.

## Decisión

En la versión actual de la aplicación (arquitectura SPA demostrativa con RBAC), todos los datos viven localmente en el cliente del usuario sin enviarse a proveedores de IA ni servidores de terceros, aplicando control de acceso por rol:
- El token de sesión JWT se almacena exclusivamente en `localStorage` del navegador bajo la llave `soldecasa_jwt_token` (`src/utils/jwt.js`), con una expiración de 8 horas (`exp: now + 3600 * 8`) verificada al iniciar la aplicación en `src/context/AuthContext.jsx` (`isJwtExpired`).
- Los datos de prospectos (`leads` en `src/views/OperationsView.jsx`), usuarios (`systemUsers` en `src/views/AdminFinancialView.jsx`) y simulaciones (`src/views/CalculatorView.jsx`) viven en el estado en memoria de React y se reinician o eliminan localmente sin transmitirse a servicios externos ni modelos de IA.
- La lectura y modificación de cada conjunto de datos se restringe mediante la matriz `ROLE_PERMISSIONS` (`src/config/roles.js`): el rol `guest` solo accede a la presentación y calculadora anónima; `user` solo ve su propio panel y código de referido (`VIEW_OWN_SAVINGS`); `editor` gestiona el embudo de prospectos (`VIEW_LEADS_FUNNEL`, `REGISTER_CLIENTS`, `UPDATE_TECHNICAL_VISITS`); y solo `admin` accede al panel financiero, gestión de usuarios y borrado de registros (`VIEW_FINANCIAL_DASHBOARD`, `MANAGE_SYSTEM_USERS`, `DELETE_CRITICAL_RECORDS`).
- La única salida de información hacia una plataforma externa ocurre cuando el propio usuario hace clic voluntariamente en compartir su código de referido o cálculo hacia WhatsApp (`https://wa.me/?text=...` en `src/views/ClientDashboardView.jsx`), enviando únicamente el enlace con `referralCode` y nunca datos de terceros.

## Alternativas descartadas

- Enviar los datos del formulario de prospectos o el historial de consumo eléctrico a un servicio externo de IA o analítica de terceros: descartado por el principio de minimización de datos de la Ley 8968, ya que no es necesario para dimensionar el sistema solar ni para agendar la visita técnica.
- Conservar sesiones indefinidas sin fecha de expiración ni opción de cierre: descartado porque en navegadores compartidos expondría las vistas protegidas; por ello `generateJwt` fija `exp` en 8 horas y `logout()` en `src/context/AuthContext.jsx` reemplaza inmediatamente el token por el perfil público `guest`.

## Consecuencias

Se cumple con la minimización de datos y se elimina el riesgo de transferencia internacional no autorizada a proveedores de IA bajo la Ley 8968, permitiendo además ejercer la supresión inmediata de registros desde la interfaz (`handleDeleteLead` en `OperationsView.jsx` y `handleDeleteUser` en `AdminFinancialView.jsx`) o limpiando la sesión del navegador. Como limitación actual documentada en el riesgo `R1` y `R2`, la persistencia en memoria y `localStorage` es de carácter demostrativo en cliente, por lo que para el paso a producción multiusuario deberá respaldarse en una base de datos relacional con verificación criptográfica de JWT en el servidor.

## Cómo se verifica

- En `src/utils/jwt.js` (líneas 6 y 86-108) se comprueba que el único almacenamiento persistente en navegador es `localStorage.getItem('soldecasa_jwt_token')` y que `isJwtExpired` valida el campo `exp`.
- En `src/config/roles.js` (líneas 36-81) y `src/App.jsx` (líneas 65-95) se verifica la segregación de acceso por rol mediante `ProtectedRoute` y `ROLE_PERMISSIONS`.
- En `src/views/ClientDashboardView.jsx` (líneas 23-27 y 149-157) se verifica que el enlace `wa.me` solo incluye el `referralCode` del propietario y ningún dato personal de los vecinos referidos.
