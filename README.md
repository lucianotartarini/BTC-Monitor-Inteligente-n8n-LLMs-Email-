### BTC Monitor Inteligente



#### Descripción

Workflow en n8n que monitorea el precio de BTC cada 1 hora, calcula variación 1h, clasifica el evento con un LLM (ALERTA/NORMAL) y envía notificaciones por email. En caso de ALERTA, enriquece el análisis con contexto 24h (CoinGecko) y genera un reporte corto con un segundo LLM. Además registra logs en Google Sheets.



#### Requisitos

\- n8n (cloud o self-hosted)

\- Google Sheets (2 hojas: `state` y `runs\_log`)

\- Cuenta Gmail conectada a n8n para envío de emails

\- API Key de Google Gemini (AI Studio)

\- Acceso a Internet para consultar Coinbase y CoinGecko



#### Archivos incluidos

\- `workflow.json`: export del workflow

\- `prompts.txt`: prompts de LLM1, LLM2 y LLM3

\- `evidencias/`: capturas de ejecuciones e emails

\- `Documentacion\_Tecnica.pdf`: documentación del sistema



#### Instalación / Importación

1\. Abrir n8n → Workflows → Import from file.

2\. Seleccionar `workflow.json`.

3\. Revisar que los nodos aparezcan con nombres correctos.



#### Configuración de credenciales

Configurar en n8n:

\- Google Sheets OAuth (o credencial equivalente)

\- Gmail OAuth

\- Google Gemini (PaLM) API key



Ver sección "Credenciales" al final para detalles.



#### Configuración de Google Sheets

Crear una hoja con:

1. Tab `state`:Una fila con key `last\_btc\_usd` para persistir el último precio y timestamp.
   
2. Tab `runs\_log`: Headers sugeridos: `ts\_now, price\_now, price\_prev, pct\_change\_1h, status, direction, range\_24h\_pct, pos\_24h, result, error\_message`



#### Cómo ejecutar y probar

##### Ejecución automática

\- El workflow inicia con Schedule Trigger configurado cada 1 hora.



##### Pruebas reproducibles (manual)

Para forzar casos de prueba:

\- Caso NORMAL: configurar `threshold\_pct = 1` (umbral alto).

\- Caso ALERTA: configurar `threshold\_pct = 0.1` (umbral bajo) para disparar ALERTA con movimientos pequeños.



### Robustez y manejo de errores
- APIs (Coinbase y CoinGecko): se configuró `Retry on fail` para manejar fallos temporales (timeouts, 5xx o rate limits).
- LLMs (LLM1, LLM2, LLM3): cada nodo LLM tiene `Retry on fail` activado. Si luego de los reintentos el proveedor sigue fallando, el workflow finaliza con error (comportamiento intencional para evitar notificaciones incorrectas).
- Validación de formato: la salida de los LLMs se parsea y valida mediante nodos `Code` (JSON estructurado). Si el JSON no cumple el esquema esperado, el workflow se detiene con un error claro para depuración.
- Observabilidad: cada ejecución se registra en Google Sheets (`runs_log`) para auditoría y evidencias.



#### Evidencias

En `evidencias/` se incluyen capturas de:

\- Email NORMAL

\- Email ALERTA

\- Log en Google Sheets (`runs\_log`)

\- Panel de ejecución de n8n



#### Credenciales (Importante)

Este repositorio NO incluye credenciales reales. 

Ver cómo configurarlas:

\- Gemini: generar API key en Google AI Studio y cargarla en n8n.

\- Google Sheets/Gmail: crear credenciales OAuth en n8n.

