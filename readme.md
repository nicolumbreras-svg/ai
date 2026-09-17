# Bot Vendedor de Autos Usados (Asistente IA con n8n)

Un agente de inteligencia artificial conversacional desarrollado en **n8n** e integrado con **Telegram**, **Google Gemini**, **Google Sheets**, **Google Calendar** y **Gmail**. El bot asume el rol de "Juan", un asesor de ventas de vehículos usados enfocado en brindar soporte, recomendar modelos según las necesidades del cliente y agendar citas de manera automatizada.

---

## 📐 Arquitectura del Sistema

```
[Cliente (Telegram)] 
       │
       ▼
[Telegram Trigger] ──► [AI Agent (Juan)] ──► [Respuesta a Telegram] ──► [Log de Observabilidad (Gmail)]
                             │
       ┌─────────────────────┼─────────────────────┐
       ▼                     ▼                     ▼
[Google Sheets]     [Code Tool (Horarios)]    [Google Calendar]
(Catálogo de Autos)   (Generador ISO -03:00)   (Agendamiento de Citas)
```

---

## 🛠️ Componentes e Integraciones

### 1. Desencadenador y Salida Conversacional
* **Telegram Trigger:** Escucha las interacciones de los usuarios en tiempo real a través de la API de Telegram.
* **Telegram Node (`Send a text message`):** Devuelve la respuesta procesada por el agente en formato HTML directamente al chat del usuario.

### 2. Motor de Inteligencia Artificial y Memoria
* **AI Agent (`@n8n/n8n-nodes-langchain.agent`):** Agente central configurado con prompts detallados sobre el rol, ámbito, reglas de negociación y flujo de agendamiento.
* **Modelo LLM (`Google Gemini Chat Model`):** Utiliza `gemini-3.5-flash-lite` para procesar el lenguaje natural de forma rápida y costo-eficiente.
* **Memoria (`Simple Memory`):** Mantiene una ventana de contexto de hasta 10 interacciones basada en la ID del chat (`chat.id`). Reinicia el historial si el usuario envía la palabra clave `"Hola"`.

### 3. Herramientas (Tools) Integradas
* **Google Sheets (`Get row(s) in sheet`):** Permite al agente consultar el inventario de autos usados y la información corporativa para brindar fichas técnicas detalladas.
* **Code Tool (`generar_horarios_disponibles`):** Script personalizado en JS que calcula dinámicamente 5 opciones de horarios hábiles en formato ISO con zona horaria de Argentina (`es-AR`, `-03:00`).
* **Google Calendar (`Create an event`):** Programa automáticamente reuniones de 1 hora en la agenda del vendedor tras confirmación explícita del cliente.

### 4. Observabilidad y Supervisión
* **Gmail Node (`Send a message`):** Registra cada mensaje del cliente y la respuesta generada por la IA, enviando un reporte automático a la casilla de supervisión para auditoría y control de calidad.

---

## 🤖 Persona y Reglas del Agente ("Juan")

* **Nombre:** Juan (35 años, vendedor apasionado y cordial).
* **Tono de comunicación:** Formal pero cercano, tuteo respetuoso, sin lenguaje inclusivo ni lunfardo.
* **Objetivo:** Recopilar datos del cliente (nombre, zona, preferencias) y recomendar vehículos del catálogo hasta concertar una cita presencial.
* **Escalamiento:** Si una duda técnica no figura en la planilla, sugiere resolverla en persona coordinando una reunión.

---

## 🚀 Requisitos de Despliegue en n8n

Para ejecutar este flujo de trabajo en una instancia de n8n se requieren las siguientes credenciales configuradas:

1. **Telegram API:** Token del bot de Telegram.
2. **Google Gemini (PaLM) API:** API Key para acceder al modelo de IA.
3. **Google Sheets OAuth2:** Acceso de lectura al documento del catálogo.
4. **Google Calendar OAuth2:** Acceso de escritura al calendario asignado.
5. **Gmail OAuth2:** Permisos para el envío automatizado de logs.