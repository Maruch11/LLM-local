# Arquitectura del Sistema RAG para Documentación Técnica

**Fecha**: 7 de marzo de 2026  
**Sistema Piloto**: Asistente de Documentación para [Sistema de Selección de Personal]  
**Alcance**: Recuperación, análisis y resumen de minutas de reuniones técnicas <br>
**Documento preparado para**: Equipo Técnico de Desarrollo  
**Propósito**: Entendimiento arquitectónico del piloto RAG
---

## 1. Introducción a RAG

### 1.1 ¿Qué es RAG?

**RAG** = Retrieval-Augmented Generation (Generación Aumentada por Recuperación)

Es una arquitectura de IA que combina:

1. **Retriever (Buscador)**: Encuentra documentos relevantes en una base de datos
2. **Generator (Generador)**: Usa esos documentos para generar respuestas precisas con el LLM

**Diferencia clave vs. LLM tradicional:**
- ❌ LLM solo: Responde basado en su entrenamiento (puede "alucinar" o estar desactualizado)
- ✅ RAG: Responde basado en documentos que se le proporcionan (más preciso y verificable que alucinaciones del modelo solo).

### 1.2 Por qué RAG es ideal para el caso de uso seleccionado

| Problema | Solución RAG |
|----------|-------------|
| Documentación dispersa (OneDriver, GitLab, Postman, etc) | Centralización virtual mediante vector DB |
| Devs nuevos no saben dónde buscar | Búsqueda semántica: "¿Cómo escalar el sistema?" encuentra respuesta sin keywords exactos |
| Documentación desactualizada | Control: solo indexa docs que vos apruebes |
| Inconsistencias entre docs | Detección: RAG puede identificar contradicciones |
| Un solo dev "sabe" → Knowledge drain | Todo documentado, recuperable, sin dependencias personales |

---

## 2. Arquitectura Técnica: Componentes Principales

### 2.1 Diagrama Conceptual

```
┌─────────────────────────────────────────────────────────────┐
│                      USUARIO / CLI                           │
│              "¿Qué pasos seguir si falla X?"                │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
        ┌────────────────────────────────┐
        │   INTERFAZ: Script Python CLI  │
        │   (procesa input del usuario)  │
        └──────────────┬─────────────────┘
                       │
        ┌──────────────▼─────────────────┐
        │   COMPONENTE 1: RETRIEVER      │
        │                                 │
        │  • Query Embedding             │
        │  • Vector Similarity Search    │
        │  • Qdrant Vector Database      │
        │                                 │
        │  (Busca: "fragmentos de docs  │
        │   similares a la pregunta")   │
        └──────────────┬─────────────────┘
                       │
                       ▼
        ┌──────────────────────────────┐
        │   DOCUMENTOS RECUPERADOS      │
        │   (3-5 fragmentos más         │
        │    relevantes)                │
        └──────────────┬────────────────┘
                       │
        ┌──────────────▼─────────────────┐
        │   COMPONENTE 2: GENERADOR      │
        │                                 │
        │  • LLM: Phi 2.7B              │
        │  • Ollama (runtime)           │
        │  • Prompt Engineering         │
        │                                 │
        │  (Escribe respuesta usando     │
        │   docs recuperados como ref.)  │
        └──────────────┬─────────────────┘
                       │
                       ▼
        ┌──────────────────────────────┐
        │   RESPUESTA CON CITAS         │
        │                                 │
        │  "Según la minuta del 15/02:  │
        │   Los pasos son: 1) ... 2) ..." │
        └──────────────────────────────┘
```

### 2.2 Stack Tecnológico Seleccionado

| Componente | Herramienta | Versión | Justificación |
|-----------|-----------|---------|-------------|
| **LLM Runtime** | Ollama | Latest | Standar para LLM local en Windows, fácil instalación |
| **Modelo LLM** | Phi 2.7B | via Ollama | Optimizado para CPU-only, excelente en tareas técnicas |
| **Vector Database** | Qdrant | Latest | Ligero, eficiente, soporte nativo Python, ideal para datos pequeño-medianos |
| **Embedding Model** | sentence-transformers | all-MiniLM-L6-v2 | Gratuito, local, 384 dimensiones, buena performance |
| **Framework RAG** | LangChain | 0.1+ | Abstraye complejidad, facilita integración componentes |
| **Lenguaje** | Python 3.10+ | 3.11 recomendado | Requisito para LangChain + Ollama, estándar IA |
| **CLI/Web** | Flask + Click | Latest | CLI con Click, Web opcional con Flask |

---

## 3. Descripción de Componentes

### 3.1 Componente 1: Ollama + Phi 2.7B (Generator)

**Responsabilidad**: Generar respuestas basadas en documentos recuperados

**Cómo funciona:**
1. Ollama es un "contenedor" que ejecuta Phi 2.7B
2. Phi procesa:
   - La pregunta del usuario
   - Los documentos recuperados (contexto)
   - Y genera una respuesta coherente

**Instalación:**
```bash
# 1. Descargar Ollama desde https://ollama.ai
# 2. Instalar (Windows installer)
# 3. Desde PowerShell:
ollama pull phi  # Descarga modelo (3-4 GB, una sola vez)
ollama serve     # Inicia servidor en localhost:11434
```

**Uso en Python:**
```python
from langchain.llms import Ollama

llm = Ollama(model="phi")
respuesta = llm("Tu pregunta aquí")
```

**Configuración recomendada para 8 GB RAM:**
- Temperature: 0.3 (respuestas determinísticas, no creativas)
- Top-p: 0.9 (reduce "alucinaciones")
- Num_threads: 12 (usa todos los cores del i5)

---

### 3.2 Componente 2: Qdrant Vector Database (Retriever Storage)

**Responsabilidad**: Almacenar y recuperar documentos por similitud semántica

**¿Cómo funciona?**

Paso 1: Indexación (una sola vez)
```
Minuta 1: "El sistema calcula automáticamente la puntuación..."
          ↓
    Se convierte en vector (lista de 384 números)
          ↓
    Se almacena en Qdrant con metadatos (fecha, autor)

Minuta 2: "La base de datos contiene 40,000 registros..."
          ↓
    Se convierte en vector
          ↓
    Se almacena en Qdrant
```

Paso 2: Búsqueda (en tiempo de consulta)
```
Usuario pregunta: "¿Cuántos registros maneja el sistema?"
          ↓
    Se convierte en vector
          ↓
    Qdrant busca vectores similares (usando distancia coseno)
          ↓
    Retorna: Minuta 2 (la más similar)
```

**Instalación:**
```bash
pip install qdrant-client --break-system-packages
```

**Uso en Python:**
```python
from qdrant_client import QdrantClient

client = QdrantClient(":memory:")  # En memoria (para desarrollo)
# O para persistencia:
client = QdrantClient(path="./qdrant_storage")
```

**Tamaño en disco:**
- 30 minutas en promedio = ~2-5 MB en Qdrant
- Completamente viable en 8 GB RAM

---

### 3.3 Componente 3: Embedding Model (Retriever - Conversion a Vector)

**Responsabilidad**: Convertir texto (pregunta + documentos) en vectores

**¿Qué es un embedding?**
- Un embedding es una representación numérica de texto
- Capture el "significado" del texto
- Textos con significado similar = vectores cercanos

**Ejemplo conceptual:**
```
Documento: "El sistema escala automáticamente"
Embedding: [0.12, -0.45, 0.89, ..., -0.23]  # 384 números
                  ↑     ↑     ↑         ↑
              "auto"  "escala" ... (captura conceptos)

Pregunta: "¿Cómo escala el sistema?"
Embedding: [0.14, -0.47, 0.91, ..., -0.21]  # Similar al anterior
                                    ↓
                    ¡Son similares → Se recuperan juntos!
```

**Modelo seleccionado: sentence-transformers (all-MiniLM-L6-v2)**

| Propiedad | Valor | Impacto |
|-----------|-------|--------|
| Dimensiones | 384 | Tamaño de vector (pequeño = rápido) |
| Tamaño modelo | 22 MB | Descarga pequeña |
| Soporte idiomas | Multilingüe | ✅ Funciona con español |
| Velocidad | ~100 oraciones/seg | Rápido |
| Instalación | `pip install sentence-transformers` | Simple |

**Uso:**
```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
embedding = model.encode("Tu texto aquí")
# embedding es un array de 384 números
```

---

### 3.4 Componente 4: LangChain (Orquestación)

**Responsabilidad**: Conectar todos los componentes (Ollama, Qdrant, embeddings)

**Funciona como "glue" entre componentes:**
```python
from langchain.chat_models import ChatOllama
from langchain.vectorstores import Qdrant
from langchain.embeddings import HuggingFaceEmbeddings
from langchain.prompts import PromptTemplate
from langchain.chains import RetrievalQA

# 1. Embeddings
embeddings = HuggingFaceEmbeddings(model_name="all-MiniLM-L6-v2")

# 2. Vector store
vector_store = Qdrant(client=qdrant_client, collection_name="minutas", embeddings=embeddings)

# 3. LLM
llm = ChatOllama(model="phi")

# 4. Chain RAG
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=vector_store.as_retriever(search_kwargs={"k": 3}),
    return_source_documents=True
)

# 5. Uso
respuesta = qa_chain({"query": "¿Cuáles son los pasos para evaluar?"})
```

LangChain simplifica la integración de ~30 líneas a ~5 líneas de código.

---

## 4. Flujo de Datos: Desde Minuta Hasta Respuesta

### 4.1 Fase 1: Ingesta (Setup Inicial)

```
Minutas en Word/PDF (30 documentos)
        │
        ├─ Se convierten a texto plano (.txt)
        │
        ├─ Se fragmentan en "chunks" de 512 tokens (~1-2 párrafos)
        │   (Porque Phi procesa mejor textos medianos)
        │
        ├─ Cada chunk se convierte a vector (embedding)
        │
        └─> Se almacenan en Qdrant con metadatos
            ├─ Fecha de minuta
            ├─ Tema
            └─ Índice en minuta original
```

**Tiempo estimado**: ~5 minutos (una sola vez)

### 4.2 Fase 2: Consulta (Cada pregunta del usuario)

```
Usuario: "¿Qué pasa si la BD falla en evaluación?"
        │
        ├─ Se convierte a vector (embedding)
        │
        ├─ Qdrant busca vectores similares
        │   (Retorna: 3-5 chunks más cercanos)
        │
        ├─ Se construye contexto:
        │   "Basándome en las minutas:
        │    [Chunk 1: ...]
        │    [Chunk 2: ...]"
        │
        ├─ Se envía a Phi 2.7B:
        │   "Responde esta pregunta usando el contexto"
        │
        └─> Phi genera respuesta
            (5-8 segundos en CPU-only)
```

**Tiempo esperado por consulta**: 5-8 segundos (CPU-only i5-1235U)

---

## 5. Capacidades RAG en este Piloto

### 5.1 Búsqueda Semántica

**Capacidad**: Encontrar documentos sin necesidad de palabras clave exactas

**Ejemplo:**
```
Pregunta usuario: "¿Cómo reporto errores?"
Sistema busca: documentos sobre "errores", "reportar", "debug", "logs", "issues"
(Aunque las minutas usen palabra "reporte" o "incidente")
```

**Viabilidad**: ✅ Completa, incluida en arquitectura base

---

### 5.2 Resumen Automático

**Capacidad**: Sintetizar información de múltiples minutas

**Ejemplo:**
```
Pregunta: "Dame un resumen de los cambios en evaluación"
Sistema:
1. Busca todos los fragmentos sobre "evaluación"
2. Phi genera resumen coherente
3. Incluye referencias a qué minuta dijo qué
```

**Viabilidad**: ✅ Viable, Phi es bueno en síntesis

---

### 5.3 Análisis y Detección de Inconsistencias

**Capacidad**: Encontrar información contradictoria en documentos

**Ejemplo:**
```
Minuta A: "La evaluación calcula con weights: 40% test, 60% entrevista"
Minuta B: "La evaluación calcula con weights: 50% test, 50% entrevista"

Pregunta: "¿Hay inconsistencias en evaluación?"
Phi genera: "Hay 2 definiciones diferentes en minutas [A] y [B]"
```

**Viabilidad**: ⚠️ Parcial
- Detecta inconsistencias si están en los chunks recuperados
- Requiere prompt engineering específico
- No es detección automática de 100% consistencia

---

### 5.4 Recomendaciones basadas en Documentación

**Capacidad**: Sugerir acciones basadas en documentos

**Ejemplo:**
```
Pregunta: "Nuevo dev llega, ¿qué debería leer primero?"
Phi genera: "Según las minutas, deberías empezar por:
1. Arquitectura del sistema (minuta 1)
2. Flujo de evaluación (minutas 3,5,7)
3. Base de datos (minutas 2,10)"
```

**Viabilidad**: ✅ Viable pero con advertencia
- Phi puede generar recomendaciones lógicas
- Calidad depende de prompt engineering
- Requiere validación humana

---

## 6. Limitaciones Arquitectónicas Conocidas

### 6.1 Limitación 1: Contexto Finito

**Problema**: Phi 2.7B tiene "ventana de contexto" de ~2048 tokens
- Si recuperas 5 minutas grandes = 2000 tokens
- Quedan solo ~48 tokens para respuesta
- Respuestas cortas, potencialmente incompletas

**Solución implementada**:
- Fragmentar minutas en chunks pequeños (~512 tokens)
- Recuperar solo top-3 chunks más relevantes
- Iteración: usuario puede hacer preguntas de seguimiento

**Impacto en demo**: Medio (tolerable, esperable)

### 6.2 Limitación 2: Sin Razonamiento Temporal

**Problema**: RAG no entiende "línea de tiempo"
```
Pregunta: "¿Cuál fue el cambio más reciente?"
Phi: No puede ordenar por fecha automáticamente
```

**Solución**:
- Añadir metadatos de fecha en chunks
- Incluir "Minuta del 15/02" en contexto
- Phi usa eso para responder

**Impacto en demo**: Bajo (manejable)

### 6.3 Limitación 3: Dependencia de Calidad de Embeddings

**Problema**: Si embedding model es malo, recupera documentos incorrectos

**Mitigación**:
- sentence-transformers (all-MiniLM) es estado del arte para tamaño pequeño
- Testear con preguntas de prueba antes de demo

**Impacto en demo**: Bajo (modelo elegido es confiable)

---

## 7. Decisiones Arquitectónicas Justificadas

### 7.1 ¿Por qué Qdrant y no Pinecone/Weaviate?

| Aspecto | Qdrant | Pinecone | Weaviate |
|--------|--------|---------|---------|
| **Instalación** | Local, pip | Cloud solo | Local/Cloud |
| **Costo** | Gratuito | Pago | Gratuito |
| **Para 30 minutas** | Overkill pero simple | Overkill | Overkill |
| **Decisión** | ✅ Elegido | ❌ Cloud innecesario | ⚠️ Complejo |

**Justificación**: Qdrant es local, gratuito, y simple. Perfecto para MVP.

---

### 7.2 ¿Por qué LangChain y no implementar from scratch?

**from scratch (~200 líneas de código):**
```python
# Tendrías que:
# - Conectar embeddings a Qdrant
# - Manejar errores de conexión
# - Construir prompts manualmente
# - Gestionar tokenización
# - Parsear respuestas
```

**LangChain (~30 líneas):**
```python
# Solo:
qa_chain = RetrievalQA.from_chain_type(...)
resultado = qa_chain({"query": "..."})
```

**Decisión**: Usar LangChain acelera desarrollo en 10x.

---

### 7.3 ¿Por qué Phi y no Mistral?

Ya justificado en documento de Hardware. Resumen:
- Phi: 3 GB RAM, 25-40 tok/seg, excelente en documentación técnica
- Mistral: 5 GB RAM, 20-35 tok/seg, más preciso pero requiere más recursos

Para demo con 8 GB: **Phi es óptimo**.

---

## 8. Plan de Evolución Arquitectónica

### 8.1 Fase 1 (Piloto - Este fin de semana)

```
Entrada: 30 minutas en Word/PDF
  ↓
Sistema: CPU-only Phi 2.7B + Qdrant + embeddings locales
  ↓
Salida: CLI que responde preguntas sobre minutas
  ↓
Demo: "Funciona, con latencia 5-8 seg"
```

### 8.2 Fase 2 (Escalado - Si se aprueba)

```
Adiciones:
├─ GPU dedicada (RTX 4060 8GB): 5x más rápido
├─ Múltiples LLMs (Phi para velocidad, Mistral para precisión)
├─ Base de datos permanente
└─ Web UI (Flask + React)

Resultado: Sistema en producción para equipo dev
```

### 8.3 Fase 3 (Madurez - Escalado)

```
Adiciones:
├─ Servidor con GPU (4x RTX 4090)
├─ Soporte para 10+ sistemas (RAGs independientes)
├─ Integración con CI/CD
├─ Auditoría y compliance
└─ Soporte multitenant

Resultado: Plataforma organizacional de conocimiento
```

---

## 9. Consideraciones de Seguridad y Cumplimiento

### 9.1 Datos Confidenciales

**Riesgo**: Las minutas contienen información sensible

**Mitigación**:
- ✅ Todo corre localmente (no se envía a cloud)
- ✅ Qdrant almacena en disco local cifrado (opcional)
- ✅ Logs de consultas se almacenan localmente
- ✅ Acceso controlado vía autenticación (Fase 2)

### 9.2 Alucinaciones del Modelo

**Riesgo**: Phi podría generar información falsa

**Mitigación**:
- ✅ Siempre cita fuentes ("Minuta del 15/02 dice...")
- ✅ Temperature baja (0.3) = respuestas determinísticas
- ✅ Usuario debe validar respuestas contra originales
- ✅ Capacitación: "Es asistente, no oráculo"

### 9.3 Auditoría

**Para aprobación**:
- ✅ Todas las consultas se registran
- ✅ Se puede auditar qué minutas fueron usadas
- ✅ Se puede reproducir respuesta (determinística)

---

## 10. Conclusión Arquitectónica

**Arquitectura seleccionada es:**
- ✅ **Viable** con hardware actual (8 GB, CPU-only)
- ✅ **Simple** de implementar (3-4 componentes, 150 líneas código)
- ✅ **Escalable** (roadmap claro a producción)
- ✅ **Segura** (todo local, sin dependencias cloud)
- ⚠️ **Con latencia** (5-8 seg, aceptable para demo)

---
