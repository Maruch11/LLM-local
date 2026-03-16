# Entorno inicial para pruebas de LLM interno

## Objetivo

Parámetros de referencia a solicitar a **Infraestructura** para un entorno inicial en servidor donde realizar **pruebas controladas de exploración de uso de LLM internos**.

El enfoque es **gradual y pragmático**, comenzando con **caso de uso concretos del equipo de Desarrollo**: consulta de documentación técnica.

El modelo se expondrá mediante un **endpoint interno del runtime del modelo** (por ejemplo **Ollama**), permitiendo su consumo vía API.

Infraestructura deberá proporcionar:

- **host**
- **puerto**
- acceso al **endpoint interno**

Este entorno es **solo para pruebas de arquitectura**, no para rendimiento final.

---

## Objetivos de esta primera etapa

El entorno permitirá:

- probar arquitectura **RAG**
- probar **pipeline** de procesamiento
- ejecutar **modelos pequeños**
- medir **consumo de CPU y RAM**

Componentes a evaluar:

- chunking
- embeddings
- retrieval

---

## Modelos previstos

Modelos pequeños **2B–3B parámetros**, por ejemplo:

- Phi-2
- Gemma 2B
- TinyLlama

Estos modelos permiten pruebas funcionales sin requerir GPU obligatoria.

---

## Parámetros orientativos del entorno

### Hardware / VM

- CPU: **4–8 vCPU**
- RAM: **≥16 GB** (ideal 32 GB)
- Disco: **≥100 GB SSD**

El almacenamiento se utiliza para:

- modelos
- embeddings
- base vectorial
- logs
- contenedores

---

### Sistema operativo

Linux server:

- Ubuntu Server
- Debian

---

### Software base

- **Docker**
- **Docker Compose**

Se utilizarán contenedores para aislar los servicios del pipeline.

---

### Red

- acceso **SSH**
- acceso **solo desde red interna**
- apertura de puerto para API (Ejemplo: Ollama → 11434)

---

### Permisos

Se requiere:

- acceso **sudo**
- posibilidad de instalar **contenedores**
- posibilidad de instalar **paquetes**

---

## Servicios previstos

Con este entorno se podrán ejecutar:

- **runtime LLM**  
  (Ollama / llama.cpp)

- **vector database**  
  (Qdrant)

- **pipeline RAG**

- **API interna** para consumo por aplicaciones

---

## Alcance de esta etapa

Este entorno permitirá validar:

- arquitectura
- pipeline
- consumo de recursos

No está destinado aún a:

- pruebas de carga
- producción
- optimización de rendimiento