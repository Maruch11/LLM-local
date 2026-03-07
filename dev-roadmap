# Roadmap de Integración de LLM Interno — Equipo de Desarrollo

## Propósito

Este documento describe el **roadmap técnico para que el equipo de desarrollo integre un LLM interno** ya desplegado por el equipo de Infraestructura.

El equipo de desarrollo **no instala ni opera el modelo**, sino que **consume el servicio de LLM mediante APIs** para construir herramientas internas útiles para desarrolladores y equipos técnicos.

---

# Alcance

## Responsabilidades del equipo de desarrollo

- Definir casos de uso
- Integrar la API del LLM
- Construir lógica de aplicación
- Generar contexto para consultas
- Implementar endpoints backend
- Crear interfaz de usuario
- Medir resultados
- Iterar y mejorar prompts

## Fuera de alcance

Responsabilidad del equipo de Infraestructura o Plataforma:

- infraestructura GPU
- despliegue del modelo
- runtime del modelo (Ollama u otro)
- bases vectoriales
- servicios de embeddings
- operación de MCP
- mantenimiento de modelos

---
# Resultado esperado

Una herramienta interna capaz de:

- responder consultas técnicas
- explicar incidentes del sistema o de los sistemas 
- mejorar acceso a documentación interna
- acelerar diagnóstico de problemas

  ---
# Arquitectura general

```
Usuario
  │
  ▼
Interfaz aplicación
  │
  ▼
Backend aplicación
  │
  ├── Sistemas internos
  │
  ├── Logs / fuentes de datos
  │
  └── API LLM (gestionada por Infraestructura)
         │
         ▼
     Runtime del modelo
```

El backend es responsable de **construir el contexto y enviar prompts al LLM**.

---

# Interfaces esperadas de Infraestructura

Infraestructura debe exponer APIs para consumir el modelo.

## API de chat o generación

```
POST /llm/chat
POST /llm/generate
```

Ejemplo de request:

```json
{
  "model": "mistral",
  "messages": [
    {"role": "system", "content": "Eres un asistente técnico"},
    {"role": "user", "content": "Explica qué pasó con los errores 500 ayer"}
  ]
}
```

---

# Roadmap de desarrollo

---

# 0. Definición del proyecto

## 0.1 Identificar el problema

Ejemplos:

- documentación técnica dispersa
- diagnóstico de incidentes lento
- dificultad para interpretar logs
- conocimiento técnico fragmentado

## 0.2 Definir el valor esperado

Ejemplos:

- acelerar diagnóstico de problemas
- mejorar acceso a conocimiento interno
- reducir tiempo de búsqueda técnica

---

# 1. Definición de casos de uso

## 1.1 Asistente de documentación

Ejemplos de consultas:

```
¿Cómo se despliega el sistema en entorno de testing?
¿Dónde está documentado el flujo de autenticación?
¿Cómo se configura el acceso al sistema desde la red interna?
```

## 1.2 Análisis de logs

Ejemplos:

```
¿Qué causó los errores 500?
¿Hubo errores recurrentes en el servicio de autenticación?
Explica este mensaje de error del sistema.```

---

# 2. Definir métricas de éxito

Ejemplos:

| Métrica | Objetivo |
|------|------|
Accuracy respuestas | >70% |
Latencia respuesta | <5 segundos |
Adopción del equipo | uso activo |
Tiempo diagnóstico | reducción medible |

---

# 3. Flujo de consulta

```
Pregunta usuario
     │
     ▼
Backend aplicación
     │
     ├─ recuperar logs
     ├─ recuperar documentación
     │
     ▼
Construcción de contexto
     │
     ▼
LLM API
     │
     ▼
Respuesta generada
```

---

# 4. Integración backend

## 4.1 Endpoint interno

```
POST /ask
```

Ejemplo request:

```json
{
  "question": "¿Qué causó los errores ayer?"
}
```

---

## 4.2 Flujo backend

```
1 recibir pregunta
2 recuperar contexto
3 construir prompt
4 llamar API LLM
5 devolver respuesta
```

---

# 5. Construcción de contexto

El backend debe generar contexto relevante.

Ejemplo:

```
pregunta usuario
   │
   ├─ recuperar logs
   ├─ recuperar documentos
   │
   ▼
contexto combinado
   │
   ▼
prompt al LLM
```

Ejemplo prompt:

```
Eres un asistente técnico.

Responde solo usando la información proporcionada.

Si la información no está disponible, responde:
"No tengo suficiente información".
```

---

# 6. Diseño de prompts

Buenas prácticas:

- prompts determinísticos
- temperatura baja
- evitar respuestas abiertas

Prompt base:

```
Eres un asistente técnico interno.

Utiliza únicamente la información proporcionada.

Si la respuesta no puede inferirse con certeza,
indica que no tienes suficiente información.
```

---

# 7. Manejo de fallos

El sistema debe degradarse correctamente.

| Fallo | Comportamiento |
|------|------|
LLM no disponible | fallback a búsqueda |
timeout | respuesta parcial |
error API | mensaje claro |

---

# 8. Observabilidad

Registrar para cada consulta:

- pregunta usuario
- contexto utilizado
- prompt enviado
- respuesta generada
- latencia de respuesta

Esto permite mejorar el sistema.

---

# 9. Seguridad

## 9.1 Evitar envío de datos sensibles

No enviar:

- contraseñas
- tokens
- secretos
- configuraciones sensibles

## 9.2 Filtrar información antes de generar contexto

Solo datos permitidos deben enviarse al LLM.

---

# 10. Interfaz de usuario

## 10.1 Interfaz tipo chat

La interfaz debe permitir:

- hacer preguntas
- ver respuestas
- ver fuentes utilizadas

## 10.2 Aviso de transparencia

Mostrar en UI:

```
Respuesta generada por IA.
Verifique siempre las fuentes antes de tomar decisiones.
```

---

# 11. Feedback de usuarios

Permitir marcar respuestas:

```
👍 útil
👎 incorrecta
```

El feedback debe registrarse.

---

# 12. Evaluación del piloto

Crear un **golden set de preguntas**.

Evaluar:

- precisión de respuestas
- utilidad percibida
- velocidad de respuesta

---

# 13. Iteración

Mejoras posibles:

- mejorar prompts
- mejorar selección de contexto
- agregar nuevas fuentes

---

# 14. Go-Live piloto

Implementar para:

- usuarios limitados
- un equipo técnico
- entorno controlado

Recoger feedback.

---

# 15. Escalado

Si el piloto es exitoso:

- integrar más documentación
- integrar más sistemas
- extender a más equipos

---



sin que el equipo de desarrollo tenga que operar infraestructura de IA.
