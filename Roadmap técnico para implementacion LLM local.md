# Roadmap técnico para implementación de LLM interno

## Validación técnica

El roadmap cubre los siguientes dominios técnicos.

### Arquitectura

- RAG  
- embeddings  
- vector DB  
- LLM runtime  

### Pipeline

- chunking  
- retriever  
- reranker  
- prompt builder  
- generación  

### Seguridad

- ACL  
- prompt injection  
- data governance  

### Operación

- métricas  
- logging  
- monitoreo  
- backups  

### Evaluación

- golden set  
- recall retrieval  
- hallucination rate  

### Escalado

- CI evaluation  
- ingest incremental  
- modelo alternativo  

---

# Arquitectura general

El sistema permite implementar **uno o más casos de uso** sobre una misma infraestructura de LLM interno.

```
Usuario
   │
   ▼
API / Gateway
   │
   ▼
Agent Runtime
   │
   ├── Caso de uso A: Consulta de documentación
   │        └─ RAG
   │             ├─ Embeddings
   │             ├─ Vector DB
   │             └─ LLM
   │
   └── Caso de uso B: Análisis de logs
            └─ Motor de logs (Elastic / Loki)
                 └─ Query temporal
                        │
                        ▼
                    LLM (opcional para explicación)
```

---

# Componentes principales de producción

```
Usuario
   │
   ▼
API
   │
   ▼
Agent Runtime
   │
   ├─ MCP  → acceso a herramientas y fuentes
   │
   └─ LLM
```

## MCP (Model Context Protocol)

Estándar para conectar herramientas, bases de datos y servicios externos al modelo.

Permite exponer capacidades del sistema como **tools** accesibles por el modelo.

Ejemplos de herramientas MCP:

- `logs.query`
- `rag.search`
- `wiki.get`
- `service.status`
- `incident.history`

---

## Agent Runtime

Orquestador que decide **cómo utilizar herramientas y fuentes de información**.

Funciones principales:

- decidir qué herramienta utilizar  
- decidir cuándo consultar RAG  
- decidir cuándo consultar logs o APIs  
- coordinar pasos múltiples en una consulta compleja  
- controlar seguridad y límites de ejecución  

---

# Roadmap de implementación

---

# Índice implementación solo documentación (RAG)

0 Gobierno y riesgo  
1 Preparación del servidor  
2 Definición de caso de uso  
3.1 Arquitectura documentación  
4 Inventario de fuentes  
5.1 Preprocesamiento documentación  
6 Pipeline de embeddings  
7.1 Base vectorial  
8 Runtime LLM  
9.1 Pipeline RAG  
9.3 Agent Runtime  
10 Routing documentación  
11 API  
12 UI  
13 Evaluación documentación  
14 Operación  
15 Go-live  
16 Escalado  

No se implementa:

- 3.2 Logs  
- 5.2 Logs  
- 7.2 Logs  
- 9.2 Pipeline logs  
- 10.6 Routing logs  
- 13.7–13.10 Evaluación logs  

---

# Índice implementación solo análisis de logs

0 Gobierno  
1 Infraestructura  
2 Caso de uso  
3.2 Arquitectura logs  
5.2 Preprocesamiento logs  
7.2 Sistema de logs  
8 Runtime LLM  
9.2 Pipeline logs  
9.3 Agent Runtime  
11 API  
12 UI  
13.7–13.10 Evaluación logs  
14 Operación  
15 Go-live  
16 Escalado  

No se implementa:

- 6 Embeddings  
- 7.1 Vector DB  
- 9.1 Pipeline RAG  

---

# 0. Gobierno y riesgo

0.1 Clasificación de datos por fuente (PII / interno / público).  
0.2 Definir política de contenido permitido en respuestas.  
0.3 Modelo de amenazas (prompt injection, data exfiltration, abuso interno).  
0.4 Definir estrategia de aislamiento multi-tenant o separación por dominios.  

---

# 1. Preparación del servidor

1.1 Verificar requisitos de hardware (RAM / SSD / GPU si aplica).  
1.2 Verificar requisitos de sistema operativo y permisos.  
1.3 Instalar dependencias base (git, python, pip/venv, curl).  
1.4 Definir estructura estándar de proyecto y backups.  

---

# 2. Definición de caso de uso

2.1 Definir preguntas objetivo y público.  
2.2 Definir criterio de “respuesta correcta” y límites del sistema.  
2.3 Definir métricas del piloto (accuracy, latencia, alucinación).  
2.4 Crear golden set de preguntas reales (30–50 mínimo).  

## 2.5 Métricas de evaluación

2.5.1 Retrieval: recall@k / precision@k.  
2.5.2 Answering: exactitud / groundedness.  
2.5.3 Safety: uso correcto de “no sé”.  

---

## 2.6 Casos de uso identificados

La implementación puede abordar **uno o ambos casos de uso**.

### 2.6.1 Consulta de documentación dev interna

Arquitectura: **RAG**

Objetivo:

- centralizar conocimiento técnico disperso  
- responder consultas sobre documentación interna  
- citar fuentes  
- evitar alucinaciones  

---

### 2.6.2 Análisis de logs Apache / sistema

Arquitectura: **motor de logs + consultas estructuradas**

Objetivo:

- consultar eventos del sistema  
- analizar errores y comportamiento temporal  
- generar explicaciones o resúmenes de incidentes usando LLM  

---

# 3. Arquitectura técnica

## 3.1 Documentación (RAG)

3.1.1 Modelo LLM local (ej: Mistral 7B en Ollama).  
3.1.2 Embeddings (Sentence Transformers).  
3.1.3 Base vectorial (Qdrant o FAISS).  
3.1.4 Servicio API interno.  
3.1.5 Definir context window y límites de tokens.  
3.1.6 Definir estrategia de quantización o uso de GPU.  

---

## 3.2 Logs

3.2.1 Motor de logs (Elasticsearch o Loki).  
3.2.2 Esquema de indexación (timestamp, level, service, message, host).  
3.2.3 Búsqueda temporal y agregaciones.  

---

# 4. Inventario de fuentes documentales

4.1 Enumerar fuentes (repos, wiki, API docs, PDFs).  
4.2 Definir ownership de cada fuente.  
4.3 Exportar contenidos a repositorio de documentos fuente.  
4.4 Definir lista de exclusión (secretos, `.env`, tokens).  
4.5 Definir política de permisos por fuente.  

---

# 5. Preprocesamiento de datos

## 5.1 Documentación

5.1.1 Convertir a texto (md / txt).  
5.1.2 Limpiar ruido (menús, boilerplate).  
5.1.3 Deduplicación de contenido.  

5.1.4 Fragmentación (chunking).  

5.1.5 Chunking por estructura semántica.  

5.1.6 Definir tamaño y solapamiento de chunks.

- chunk size recomendado: **300–512 tokens**  
- overlap: **50–100 tokens**

5.1.7 Estrategia por tipo de documento (docs, PDFs, código).  

5.1.8 Test de calidad de chunking.

5.1.9 Agregar metadata (fuente, fecha, ruta, sección).

5.1.10 Sanitizar contenido potencialmente malicioso.

5.1.11 Guardar offsets para trazabilidad.

---

## 5.2 Logs

5.2.1 Ingesta de logs (Apache / aplicación).  
5.2.2 Parsing y normalización (timestamp, level, service, message).  
5.2.3 Eliminación de ruido.  
5.2.4 Indexación temporal.  
5.2.5 Definir política de rotación y retención.  

---

# 6. Pipeline de embeddings (solo documentación)

6.1 Generar embeddings para cada chunk.  
6.2 Persistir embeddings y metadata.  
6.3 Versionar dataset de embeddings.  
6.4 Versionar modelo de embeddings utilizado.  
6.5 Registrar hash del documento fuente.  

---

# 7. Almacenamiento

## 7.1 Documentación

7.1.1 Instalar base vectorial (Qdrant).  
7.1.2 Crear colecciones e índices.  
7.1.3 Ingestar embeddings.  
7.1.4 Definir metadata estable (source_id, version, ACL, domain).  
7.1.5 Definir estrategia de backup.  

---

## 7.2 Logs

7.2.1 Crear índices por fecha (`logs-YYYY-MM-DD`).  
7.2.2 Definir shards y replicación.  
7.2.3 Validar consultas por timestamp y nivel.  

---

# 8. Runtime LLM

8.1 Instalar Ollama.  
8.2 Descargar modelo (ej: Mistral 7B).  
8.3 Probar inferencia local.  
8.4 Definir parámetros de generación.  
8.5 Definir límites de contexto.  

---

# 9. Pipeline de consulta

## 9.1 Documentación (RAG)

9.1.1 Retriever semántico.  
9.1.2 Re-ranking de resultados.  
9.1.3 Context builder.  
9.1.4 Generación de respuesta.

### Guardrails

- threshold de similitud  
- citas obligatorias  
- detección de conflictos entre documentos  

9.1.6 Cache de consultas frecuentes.

---

## 9.2 Logs

9.2.1 Parser de consulta (tiempo / servicio / nivel).  
9.2.2 Query estructurada al motor de logs.  
9.2.3 Recuperación de contexto temporal.  
9.2.4 Context builder para diagnóstico.  
9.2.5 LLM opcional para explicación o resumen de incidentes.  

---

# 9.3 Agent Runtime

9.3.1 Implementar orquestador de herramientas.  
9.3.2 Integrar herramientas mediante MCP.  
9.3.3 Definir políticas de uso de herramientas.  
9.3.4 Definir límites de ejecución y seguridad.  

---

# 10. Routing

## Documentación

10.1 Definir dominios (DEV / MATERIAS).  
10.2 Clasificador multi-label liviano.  
10.3 Routing a colección vectorial correcta.  
10.4 Filtros ACL por documento.  
10.5 Routing híbrido si hay ambigüedad.  

---

## Logs

10.6 Routing por tipo de log

- Apache logs  
- Application logs  
- System logs  

---

# 11. API interna

11.1 Endpoint `/ask`.  
11.2 Autenticación y rate limiting.  
11.3 Logging de consultas.  
11.4 Trazabilidad de fuentes utilizadas.  
11.5 Control de abuso.  

---

# 12. UI mínima

12.1 Interfaz web interna.  
12.2 Mostrar fuentes citadas.  
12.3 Feedback de usuarios.  

---

# 13. Evaluación

## Documentación

13.1 Evaluación con golden set.  
13.2 Accuracy de respuestas.  
13.3 Hallucination rate.  
13.4 Recall del retrieval.  
13.5 Validación de citas.  
13.6 Revisión manual de respuestas.  

---

## Logs

13.7 Precision@10 en logs recuperados.  
13.8 Latencia de consulta.  
13.9 Tiempo para diagnóstico de incidente.  
13.10 Tasa de conclusiones incorrectas del LLM.  

---

# 14. Operación y seguridad

14.1 Control de acceso por rol.  
14.2 Política de logs y datos sensibles.

14.2.1 Qué se loguea (prompt, query, chunks, embeddings).  
14.2.2 Redacción o anonimización de PII.

14.3 Procedimiento de reindexación de documentos.  
14.4 Monitoreo de sistema (CPU / RAM / latencia).  
14.5 Backups y restore.  
14.6 ACL por documento en retrieval.  

---

# 15. Go-live controlado

15.1 Usuarios piloto.  
15.2 Canal de feedback.  
15.3 Iteración sobre fallas.  

---

# 16. Escalado

16.1 Ingesta automática de documentos.  
16.2 Mejora de re-ranking (cross-encoder / reranking avanzado).  
16.3 Evaluación de modelos alternativos.  
16.4 Ingesta incremental.  
16.5 Pipeline de evaluación continua.  