# ADR-0001: Uso exclusivo de asistentes de IA durante el desarrollo y cero llamadas a LLMs en tiempo de ejecución

- Estado: Aceptado
- Fecha: 2026-11-13
- Decide: Daniel Ortega, desarrollador full stack

## Contexto

Sol de Casa es una plataforma web en React y Vite para microgeneración solar residencial, cálculo de ahorro eléctrico, red de referidos vecinales y gestión operativa con control de acceso basado en roles (RBAC) y tokens JWT. El sistema requiere respuestas instantáneas en el navegador, costo operativo cero por consulta en la calculadora pública y total protección de los datos de contacto y consumo eléctrico de los propietarios y prospectos bajo la Ley 8968, sin depender de APIs externas de inteligencia artificial en producción.

## Decisión

No se integra ningún modelo ni proveedor de IA en tiempo de ejecución dentro del producto; la IA se utiliza únicamente como herramienta de asistencia para programación y documentación durante el desarrollo bajo reglas estrictas:
- Para el desarrollo, estructuración de componentes React (`src/views/`, `src/components/`), diseño de la matriz RBAC (`src/config/roles.js`) y redacción de documentación se emplean asistentes de código (Gemini / Claude en el IDE) operados por el desarrollador.
- El paquete de producción (`package.json`) solo incluye dependencias de interfaz (`react`, `react-dom`, `lucide-react`, `tailwindcss`) y no contiene SDKs de OpenAI, Anthropic, DeepSeek ni llamadas `fetch`/`axios` hacia proveedores de IA.
- Ningún código generado o sugerido por el asistente de programación se incorpora a la rama principal sin revisión humana de las fórmulas solares (`src/views/CalculatorView.jsx`) y de las guardias de permisos (`src/components/common/ProtectedRoute.jsx` y `src/components/common/Can.jsx`).
- Nunca se envían datos personales reales de clientes ni llaves privadas en los prompts del asistente de programación; todo el desarrollo y las pruebas usan exclusivamente los perfiles sintéticos definidos en `MOCK_USERS` (`src/config/roles.js`).

## Alternativas descartadas

- Integrar una API de LLM (como OpenAI o DeepSeek) directamente en el cliente React para responder consultas solares o calcular paneles: descartado porque expondría la llave de API en el navegador, añadiría costo por cada visitante en la calculadora pública y podría alucinar cifras de ahorro o retorno de inversión.
- Mantener un backend intermedio conectado a un LLM de pago para generar propuestas comerciales automáticas: descartado porque el cálculo fotovoltaico residencial en Costa Rica se resuelve con exactitud, sin latencia y a costo cero mediante fórmulas deterministas basadas en la tarifa por kWh (`₡105`) y horas sol pico (`4.8`).

## Consecuencias

El costo mensual de APIs de IA en producción es `$0 USD`, la calculadora responde en milisegundos sin riesgo de caída de un proveedor externo y ningún dato de usuarios o prospectos sale hacia terceros de IA. Como contrapartida, la aplicación no ofrece un chat conversacional abierto y cualquier cambio en las reglas de negocio o en la interfaz requiere edición de código revisada por el desarrollador siguiendo `AGENTS.md`.

## Cómo se verifica

- En `package.json` y en todo el árbol `src/` se comprueba la ausencia total de SDKs de IA, variables `OPENAI_API_KEY` o peticiones HTTP hacia servicios externos de modelos de lenguaje.
- En `src/config/roles.js` (líneas 84-119) se verifica que las cuentas incluidas en el repositorio corresponden únicamente a usuarios ficticios de demostración (`MOCK_USERS`).
- Mediante `.github/governance-pack/verificar-pack.mjs` y la revisión de pull requests contra las reglas de `AGENTS.md` antes de cada merge.
