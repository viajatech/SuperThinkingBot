![](https://github.com/viajatech/SuperThinkingBot/blob/main/image_fx_%20-%202024-12-21T034632.017.jpg)
----

# SuperBot Thinking by ViajaTech – README

¡Bienvenido(a) a **SuperBot Thinking by ViajaTech**! Este es un **chatbot** con interfaz gráfica (GUI) que integra:
- **Modelo de lenguaje** (vía [LM Studio](https://github.com/lmstudio-ai))  
- **Estrategias de test-time compute** para permitir que el modelo “piense más” según el presupuesto de cómputo que definas  
- **Memoria conversacional**, de modo que el chatbot pueda recordar el historial de mensajes y responder de forma coherente  
- **Reconocimiento de voz** (speech-to-text)  
- **Síntesis de voz** con Azure Speech  

> **Créditos Apache 2.0 a ** David Ruiz, conocido como "**Viaja Tech**", por la visión y dirección de este proyecto.

---

## ¿Qué es SuperBot Thinking by ViajaTech?

**SuperBot Thinking by ViajaTech** es un proyecto que **demuestra** cómo combinar varias tecnologías de inteligencia artificial en un **único** script de Python:

1. **Gradio** para la interfaz de usuario.  
2. **SpeechRecognition** para convertir voz a texto.  
3. **Azure Cognitive Services** para convertir texto en voz.  
4. **OpenAI API** apuntando a [LM Studio](https://github.com/lmstudio-ai) (configurado como servidor local).  
5. **Estrategias de test-time compute** (Simple, Best-of-N, Weighted Best-of-N, Beam Search, DVTS) que permiten al modelo “iterar” sobre sus respuestas y, en algunos casos, **optimizar** la generación de respuestas.  
6. **Memoria conversacional** para retener el historial de la conversación y permitir que el chatbot recuerde lo que ha sucedido en interacciones anteriores.

El objetivo es que **tanto usuarios principiantes** como **avanzados** se beneficien de un **ejemplo funcional** de un “asistente A.I.” que:
- Tiene una **GUI** sencilla para chatear.  
- **Recuerda** lo que se dijo previamente.  
- **Piensa** con diferentes métodos de test-time compute scaling.

---

## ¿Cómo funciona?

### 1. Arranque y Configuración

- Clona o descarga este repositorio.
- Verifica que cuentas con **LM Studio** corriendo localmente en `http://localhost:1234/v1` (o ajusta la URL en el script si cambió).  
- **Instala** todas las dependencias (ver más abajo).  

### 2. Ejecución del Script
```bash
python superbot_with_memory.py
```
- Se abrirá (o te dará un enlace) a la **Interfaz de Gradio** con dos pestañas: **Configuración** y **Chat**.

### 3. Configuración
- Define el **Contexto** inicial (ej. “Eres un asistente A.I. preparado para ayudar…”).
- Coloca tu **nombre** y el **nombre del chatbot**.
- Ajusta la **Temperatura** (creatividad), el **modelo** (ej. `"meta-llama-3.1-8b-instruct"` en LM Studio), y selecciona si deseas **voz** (Azure TTS).
- Elige la **Estrategia** de test-time compute (ej. Simple, Best-of-N, Weighted Best-of-N, Beam Search, DVTS).
- Ajusta el **Test-time Compute** (niveles de iteraciones o “profundidad” de razonamiento).
- Haz clic en **Iniciar Chatbot**.

### 4. Chat
- Verás un **chat** en vivo donde podrás escribir mensajes de texto y el chatbot responderá.
- Si habilitas **voz**, el chatbot leerá sus respuestas.
- Para usar **voz de entrada**, haz clic en **Hablar** (usa Google Speech Recognition).
- El historial del chat se mostrará en **Chat** y se mantiene internamente en una estructura `messages`. ¡La magia de la memoria conversacional ocurre aquí!

### 5. Memoria Conversacional
Cada vez que envías un mensaje, se construye un `combined_context` que incluye el **contexto inicial** y el **historial previo** (rol user/assistant). El modelo, por tanto, recibe toda la conversación anterior.  

Esto permite consultas como:  
> “¿Qué me dijiste hace un momento?”  
El chatbot buscará en su historial y responderá de forma coherente.

### 6. Estrategias de Test-time Compute
En la **pestaña de Configuración**, encontrarás un **Radio** con:
- **Simple**: Emula un razonamiento interno (chain-of-thought) con la profundidad especificada.  
- **Best-of-N** y **Weighted Best-of-N**: Generan varias respuestas y eligen la mejor según un reward (dummy).  
- **Beam Search** y **DVTS**: Implementaciones simplificadas de búsquedas más elaboradas que optimizan la salida del modelo en el test-time.  

Estas estrategias **no requieren** entrenamiento adicional; se basan en llamar varias veces al mismo modelo, con la diferencia de cómo se escogen las respuestas y cuántas iteraciones se ejecutan.

---

## Desafíos y Soluciones

1. **Retención de Memoria**:  
   - Al inyectar todo el historial en cada turno, el modelo puede “recordar” lo dicho.  
   - **Desafío**: Si el historial es muy extenso, podría exceder la capacidad de tokens.  
   - **Solución**: Ajustar `max_tokens` u optar por estrategias que “resuman” el historial si se torna muy largo (no implementado en este ejemplo, pero es un posible upgrade).

2. **Test-time Compute**:  
   - **Desafío**: Llamar repetidamente a la API aumenta la latencia y el costo computacional.  
   - **Solución**: Exponer un **slider** que permita al usuario ajustar cuánto compute invertir (Test-time Compute).

3. **Integración con Azure TTS y Reconocimiento de Voz**:  
   - **Desafío**: Configuración adecuada de credenciales y librerías.  
   - **Solución**: Parametrizar `speech_key` y `service_region`; uso de `speechrecognition` y `azure.cognitiveservices.speech`.

4. **Licenciamiento y Créditos**:  
   - **Desafío**: Asegurar que este proyecto sea reutilizable y que se reconozca la autoría.  
   - **Solución**: Emplear [Licencia Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).  

---

## Complejidad del Proyecto

- **Nivel Básico**: Puedes usarlo simplemente en modo “Simple” con un par de parámetros.  
- **Nivel Avanzado**: Explora **Best-of-N**, **Beam Search** o **DVTS**, ajusta penalizaciones, metas de tokens, etc.  
- **Ingeniería**: Requiere cierto conocimiento de Python, configuración de entornos virtuales, y la necesidad de correr un servidor LM Studio (o endpoint GPT-compatible).

---

## Dependencias Requeridas (instálalas vía `pip install`)

```bash
pip install azure-cognitiveservices-speech
pip install openai
pip install gradio
pip install speechrecognition
pip install TTS
```

**Opcional**: Ajusta la versión de `openai` o `gradio` según tu entorno.  

---

## Licencia Apache 2.0

Este proyecto se distribuye bajo la **Licencia Apache 2.0**:

- Puedes **usar** el código, **modificarlo**, y **redistribuirlo** en proyectos personales o comerciales.  
- **Debes** dar **crédito** a **David Ruiz (“Viaja Tech”)**, como se hace en este README.  
- No puedes usar el nombre del autor para **promocionar** productos derivados sin autorización.  
- No hay garantía de idoneidad, el software se entrega “tal cual”.

[Texto completo de la licencia Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

---

## Agradecimientos

- A **Todos mis seguidores por su apoyo en mis múltiples redes sociales** (“Viaja Tech”).
---

¡Listo! Espero que este **SuperBot Thinking by ViajaTech** te sea útil como referencia para combinar **Memoria conversacional, TTS, Speech-to-text, y estrategias de test-time compute** con un **modelo** LLM. Si tienes dudas, ¡no dudes en abrir un _issue_ o contactarnos!  
