# BTC-Monitor-Inteligente-n8n-LLMs-Email-
Workflow en n8n que monitorea cada 1h el precio BTC-USD (Coinbase), guarda estado y logs en Google Sheets (state, runs_log), calcula variación 1h, clasifica con Gemini (LLM1: NORMAL/ALERTA en JSON) y bifurca: NORMAL genera insight (LLM3) y envía email; ALERTA consulta CoinGecko 24h, genera análisis (LLM2) y envía email.
