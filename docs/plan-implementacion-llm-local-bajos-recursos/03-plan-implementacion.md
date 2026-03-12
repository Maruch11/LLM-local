# Plan de Implementación: Piloto RAG para Documentación Técnica

**Fecha de inicio**: 7 de marzo de 2026 (sábado noche)  
**Fecha de conclusión**: 14 de marzo de 2026 (viernes noche)  
**Recurso**: Mariana Emilia Mazzoccoli  
**Horas estimadas**: 13 horas útiles distribuidas en 6 días

---

## 1. Timeline Ejecutivo

### 1.1 Distribución de Horas y Milestones

```
Sábado 7/3   (4 horas)  → Preparación + Setup Ambiente
  └─ 23:00 inicio documento

Domingo 8/3  (0 horas)  → Descanso / Repaso

Lunes 9/3    (1 hora)   → Setup completado + Primeros tests
  
Martes 10/3  (2 horas)  → Ingesta de datos + Qdrant indexado
  
Miércoles 11/3 (1 hora) → Testing + Refinamiento de prompts
  
Jueves 12/3  (2 horas)  → CLI funcional + Documentación
  
Viernes 13/3 (3 horas)  → Demo interna + Feedback
  
Sábado 14/3  (0 horas)  → Entregables finales (si es necesario iterar)
```

### 1.2 Milestones Clave

| Milestone | Fecha | Estado | Entregable |
|-----------|-------|--------|-----------|
| **M1: Ambiente configurado** | Lunes 9/3 | 🎯 | Ollama + Python envs funcionando |
| **M2: Datos indexados** | Martes 10/3 | 🎯 | Qdrant con 30 minutas procesadas |
| **M3: RAG en Python** | Miércoles 11/3 | 🎯 | Script que recupera y responde |
| **M4: CLI operacional** | Jueves 12/3 | 🎯 | Interfaz de línea de comandos |
| **M5: Demo funcional** | Viernes 13/3 | 🎯 | Presentación interna |

---

## 2. Fase 1: Sábado 7/3 (4 horas) - Preparación y Setup

### 2.1 Tareas

#### Tarea 1.1: Instalación de Ollama (30 min)

```
Objetivo: Tener Ollama corriendo en Windows con Phi 2.7B

Pasos:
1. Descargar Ollama desde https://ollama.ai
2. Ejecutar installer Windows
3. Verificar instalación:
   - Abrir PowerShell
   - Ejecutar: ollama --version
   - Esperado: "ollama version X.X.X"

4. Descargar modelo Phi 2.7B:
   - Ejecutar: ollama pull phi
   - Esperado: Descarga ~3-4 GB (toma ~10-15 min dependiendo conexión)
   - Output final: "success"

5. Test básico:
   - Ejecutar: ollama serve (en una PowerShell)
   - En otra PowerShell:
     curl http://localhost:11434/api/generate -d '{
       "model": "phi",
       "prompt": "¿Qué es RAG?"
     }'
   - Esperado: Respuesta JSON con generación

Duración estimada: 30 minutos (descarga incluida)
Criterio de éxito: ollama serve corre sin errores, modelo responde
```

#### Tarea 1.2: Crear Ambiente Python (45 min)

```
Objetivo: Ambiente Python aislado para el proyecto

Pasos:
1. Abrir PowerShell como Admin
2. Navegar a carpeta de trabajo:
   cd C:\Users\[TuUsuario]\Documents\rag-piloto
   (crear carpeta si no existe)

3. Crear ambiente virtual:
   python -m venv venv_rag
   
4. Activar ambiente:
   .\venv_rag\Scripts\Activate.ps1
   (si da error de permisos, ejecutar):
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

5. Verificar Python:
   python --version
   (Esperado: Python 3.10+)

6. Instalar dependencias base:
   pip install --upgrade pip
   pip install langchain ollama qdrant-client sentence-transformers python-dotenv click
   
   (Toma ~5-10 min dependiendo conexión)

7. Crear archivo requirements.txt para documentación:
   pip freeze > requirements.txt

Duración estimada: 45 minutos
Criterio de éxito: venv_rag activado, librerías instaladas sin errores
```

#### Tarea 1.3: Preparar Estructura de Proyecto (30 min)

```
Objetivo: Carpetas y archivos organizados

Estructura esperada:
rag-piloto/
├── venv_rag/              # Ambiente virtual
├── datos/
│   ├── minutas_raw/       # Minutas originales (Word/PDF)
│   ├── minutas_txt/       # Minutas convertidas a texto
│   └── qdrant_storage/    # Base de datos vectorial
├── src/
│   ├── main.py            # Punto de entrada CLI
│   ├── rag_handler.py     # Lógica RAG (retriever + generator)
│   ├── embedder.py        # Manejo de embeddings
│   └── utils.py           # Funciones auxiliares
├── tests/
│   └── test_rag.py        # Tests básicos
├── docs/
│   └── README.md          # Documentación proyecto
├── .env                   # Variables de entorno
├── requirements.txt       # Dependencias
└── config.py              # Configuración

Pasos:
1. Crear carpetas:
   mkdir datos\minutas_raw
   mkdir datos\minutas_txt
   mkdir src
   mkdir tests
   mkdir docs

2. Crear archivos base (vacíos):
   touch src/main.py
   touch src/rag_handler.py
   touch src/embedder.py
   touch src/utils.py
   touch config.py
   touch .env

3. Crear .gitignore:
   echo "venv_rag/" >> .gitignore
   echo "datos/qdrant_storage/" >> .gitignore
   echo ".env" >> .gitignore
   echo "__pycache__/" >> .gitignore

Duración estimada: 30 minutos
Criterio de éxito: Estructura lista, git inicializado
```

#### Tarea 1.4: Primer Commit (15 min)

```
Objetivo: Versionar estado inicial

Pasos:
1. Inicializar git:
   git init
   git add .
   git commit -m "init: estructura base del proyecto RAG"

2. (Opcional) Subir a GitHub:
   git remote add origin https://github.com/Maruch11/rag-piloto.git
   git push -u origin main

Duración estimada: 15 minutos
Criterio de éxito: Primer commit registrado
```

### 2.2 Checklist Fin de Sábado

- ✅ Ollama instalado y Phi 2.7B descargado
- ✅ Ambiente Python venv_rag creado y activado
- ✅ Dependencias instaladas (LangChain, Qdrant, sentence-transformers)
- ✅ Estructura de carpetas lista
- ✅ Git inicializado y primer commit
- ✅ ollama serve corre sin errores

**Si algo falla**: Documentar error y continuar lunes. No es bloqueante.

---

## 3. Fase 2: Lunes 9/3 (1 hora) - Setup Completado + Tests

### 3.1 Tareas

#### Tarea 2.1: Validar Ollama (15 min)

```
Objetivo: Confirmar Ollama y Phi funcionan después del fin de semana

Pasos:
1. Abrir 2 PowerShells

   PowerShell 1 (Server):
   cd C:\Users\[TuUsuario]\Documents\rag-piloto
   .\venv_rag\Scripts\Activate.ps1
   ollama serve

   PowerShell 2 (Client):
   cd C:\Users\[TuUsuario]\Documents\rag-piloto
   .\venv_rag\Scripts\Activate.ps1
   
   python -c "
   from langchain.llms import Ollama
   llm = Ollama(model='phi')
   respuesta = llm('Hola, ¿qué es una minuta de reunión?')
   print(respuesta)
   "

2. Esperado: Respuesta de Phi (5-10 segundos)

Duración estimada: 15 minutos
Criterio de éxito: Phi responde en PowerShell
```

#### Tarea 2.2: Test de Embeddings (15 min)

```
Objetivo: Verificar sentence-transformers funciona

Pasos:
1. En PowerShell (misma de arriba):
   
   python -c "
   from sentence_transformers import SentenceTransformer
   model = SentenceTransformer('all-MiniLM-L6-v2')
   
   # Primera vez baja modelo (~22 MB)
   textos = [
       'El sistema calcula automáticamente',
       'La base de datos tiene 40000 registros',
       'Los gatos duermen mucho'
   ]
   
   embeddings = model.encode(textos)
   print('Embeddings generados:', embeddings.shape)
   # Esperado: (3, 384)
   "

2. Esperado: Output mostrando (3, 384)

Duración estimada: 15 minutos (descarga modelo en primera ejecución)
Criterio de éxito: Embeddings generados sin errores
```

#### Tarea 2.3: Test Qdrant (15 min)

```
Objetivo: Verificar vector database funciona

Pasos:
1. En PowerShell:
   
   python -c "
   from qdrant_client import QdrantClient
   from qdrant_client.models import Distance, VectorParams, PointStruct
   
   # Cliente en memoria (para desarrollo)
   client = QdrantClient(':memory:')
   
   # Crear colección
   client.recreate_collection(
       collection_name='test',
       vectors_config=VectorParams(size=384, distance=Distance.COSINE),
   )
   
   # Insertar punto de prueba
   client.upsert(
       collection_name='test',
       points=[
           PointStruct(id=1, vector=[0.1]*384, payload={'texto': 'prueba'})
       ]
   )
   
   # Buscar
   resultado = client.search(
       collection_name='test',
       query_vector=[0.1]*384,
       limit=1
   )
   
   print('Búsqueda funciona:', len(resultado) > 0)
   "

2. Esperado: "Búsqueda funciona: True"

Duración estimada: 15 minutos
Criterio de éxito: Qdrant puede crear colección e insertar/buscar
```

### 3.2 Checklist Fin de Lunes

- ✅ Ollama + Phi responden preguntas
- ✅ SentenceTransformers genera embeddings
- ✅ Qdrant crea colecciones e indexa vectores
- ✅ Todos los tests básicos pasan

**Si algo falla**: Revisar logs, reinstalar librerías si es necesario.

---

## 4. Fase 3: Martes 10/3 (2 horas) - Ingesta de Datos + Qdrant Indexado

### 4.1 Tareas

#### Tarea 3.1: Crear/Preparar Minutas Ficticio (30 min)

```
Objetivo: Tener 30 minutas simuladas en formato texto para indexar

IMPORTANTE: Como aclaraste, TÚ crearás el set de datos.

Estructura que asumiremos:
- 30 archivos .txt o .pdf
- Cada uno es una minuta de reunión
- Contenido: Requerimientos funcionales del sistema de selección
- Tienen formato similar:
  "Minuta del 15/02/2026
   Asistentes: [nombres]
   Tema: [tema]
   
   Discusión:
   [contenido de la minuta]
   
   Conclusiones:
   [conclusiones]"

Pasos para crear set:
1. Ir a /datos/minutas_raw/
2. Crear 5-10 minutas manualmente (breve, 100-200 palabras cada una)
   sobre el sistema de selección:
   - Arquitectura del sistema
   - Flujo de evaluación
   - Base de datos (40000 registros)
   - Cálculo automático de resultados
   - Reportes
   - Escalabilidad
   - etc.

3. Guardar como:
   minuta_20260201_arquitectura.txt
   minuta_20260208_evaluacion.txt
   minuta_20260215_bd.txt
   etc.

Nota: Simulando 30, pero comenzar con 5-10 mientras testeas.
      Después duplicar o generar más.

Duración estimada: 30 minutos
Criterio de éxito: Carpeta minutas_raw contiene 5-10 .txt
```

#### Tarea 3.2: Crear Script de Ingesta (60 min)

```
Objetivo: Script que lee minutas, genera embeddings, indexa en Qdrant

Archivo: src/ingesta.py

Pseudocódigo (implementar en Python):
"""
1. Leer todos los .txt de /datos/minutas_raw/
2. Para cada minuta:
   a. Convertir a texto limpio (remover espacios extras, etc)
   b. Fragmentar en chunks de ~512 tokens
   c. Para cada chunk:
      i. Generar embedding (SentenceTransformers)
      ii. Insertar en Qdrant con metadatos (fecha, fuente)
3. Guardar colección en /datos/qdrant_storage/ (persistencia)
4. Imprimir estadísticas:
   - Cantidad de minutas procesadas
   - Cantidad de chunks generados
   - Tamaño colección Qdrant
"""

Código esqueleto esperado (~100 líneas):

```python
# src/ingesta.py

import os
from pathlib import Path
from sentence_transformers import SentenceTransformer
from qdrant_client import QdrantClient
from qdrant_client.models import Distance, VectorParams, PointStruct
import re

def cargar_minutas(directorio):
    """Lee todos los .txt del directorio"""
    minutas = {}
    for archivo in Path(directorio).glob("*.txt"):
        with open(archivo, 'r', encoding='utf-8') as f:
            minutas[archivo.name] = f.read()
    return minutas

def fragmentar_texto(texto, max_tokens=512):
    """Fragmenta texto en chunks (aprox max_tokens)"""
    palabras = texto.split()
    chunks = []
    chunk_actual = []
    
    for palabra in palabras:
        chunk_actual.append(palabra)
        # Heurística: 1 token ≈ 1.3 palabras
        if len(chunk_actual) >= int(max_tokens / 1.3):
            chunks.append(" ".join(chunk_actual))
            chunk_actual = []
    
    if chunk_actual:
        chunks.append(" ".join(chunk_actual))
    
    return chunks

def indexar_en_qdrant(minutas, output_dir="datos/qdrant_storage"):
    """Procesa minutas y las indexa en Qdrant"""
    
    # Inicializar modelo de embeddings
    print("Cargando SentenceTransformers...")
    embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
    
    # Inicializar Qdrant
    print("Inicializando Qdrant...")
    client = QdrantClient(path=output_dir)
    
    # Crear colección
    collection_name = "minutas"
    client.recreate_collection(
        collection_name=collection_name,
        vectors_config=VectorParams(size=384, distance=Distance.COSINE),
    )
    
    # Procesar minutas
    punto_id = 1
    total_chunks = 0
    
    for nombre_archivo, contenido in minutas.items():
        print(f"Procesando {nombre_archivo}...")
        
        # Extraer fecha del nombre (suposición: minuta_YYYYMMDD_tema.txt)
        fecha = nombre_archivo.split("_")[1] if "_" in nombre_archivo else "desconocida"
        
        # Fragmentar
        chunks = fragmentar_texto(contenido)
        
        # Indexar cada chunk
        for i, chunk in enumerate(chunks):
            # Generar embedding
            embedding = embedding_model.encode(chunk)
            
            # Crear punto
            punto = PointStruct(
                id=punto_id,
                vector=embedding.tolist(),
                payload={
                    "archivo": nombre_archivo,
                    "fecha": fecha,
                    "chunk_idx": i,
                    "texto": chunk[:200]  # Primeros 200 chars para referencia
                }
            )
            
            # Insertar
            client.upsert(
                collection_name=collection_name,
                points=[punto]
            )
            
            punto_id += 1
            total_chunks += 1
        
        print(f"  → {len(chunks)} chunks indexados")
    
    # Estadísticas
    print("\n=== ESTADÍSTICAS ===")
    print(f"Minutas procesadas: {len(minutas)}")
    print(f"Chunks totales: {total_chunks}")
    print(f"Colección guardada en: {output_dir}")
    
    return client

if __name__ == "__main__":
    # Ejecutar
    minutas = cargar_minutas("datos/minutas_raw")
    print(f"Minutas encontradas: {len(minutas)}")
    
    indexar_en_qdrant(minutas)
    
    print("✓ Ingesta completada")
```

Pasos para implementar:
1. Crear archivo src/ingesta.py
2. Copiar código anterior (o similar)
3. Ejecutar:
   python src/ingesta.py
4. Esperado:
   - Output mostrando minutas procesadas
   - Carpeta datos/qdrant_storage/ creada
   - Archivo collection.json dentro (prueba de persistencia)

Duración estimada: 60 minutos
Criterio de éxito: Script corre sin errores, Qdrant indexa chunks
```

### 4.2 Checklist Fin de Martes

- ✅ 30 minutas (o subset) preparadas en /datos/minutas_raw/
- ✅ Script ingesta.py crea colección Qdrant
- ✅ Chunks indexados con embeddings y metadatos
- ✅ Qdrant persiste en disco (/datos/qdrant_storage/)

**Commit sugerido**: `git commit -m "feat: ingesta de minutas a Qdrant"`

---

## 5. Fase 4: Miércoles 11/3 (1 hora) - Testing + Refinamiento de Prompts

### 5.1 Tareas

#### Tarea 4.1: Script RAG Base (30 min)

```
Objetivo: Script que usa Qdrant + Phi para responder preguntas

Archivo: src/rag_handler.py

Pseudocódigo (implementar en Python):

```python
# src/rag_handler.py

from langchain.llms import Ollama
from langchain.embeddings import HuggingFaceEmbeddings
from langchain.vectorstores import Qdrant
from qdrant_client import QdrantClient

class RAGHandler:
    def __init__(self, qdrant_path="datos/qdrant_storage"):
        print("Inicializando RAG Handler...")
        
        # Embeddings
        self.embeddings = HuggingFaceEmbeddings(
            model_name="all-MiniLM-L6-v2"
        )
        
        # Vector store
        qdrant_client = QdrantClient(path=qdrant_path)
        self.vector_store = Qdrant(
            client=qdrant_client,
            collection_name="minutas",
            embeddings=self.embeddings
        )
        
        # LLM
        self.llm = Ollama(model="phi", temperature=0.3)
        
        print("RAG Handler inicializado ✓")
    
    def responder(self, pregunta, num_documentos=3):
        """
        Responde una pregunta basada en minutas indexadas
        
        Args:
            pregunta: str, la pregunta del usuario
            num_documentos: int, cantidad de chunks a recuperar
        
        Returns:
            str, respuesta con citas
        """
        
        # 1. Recuperar documentos relevantes
        print(f"\nBuscando documentos para: '{pregunta}'")
        docs = self.vector_store.similarity_search(
            pregunta, 
            k=num_documentos
        )
        
        print(f"Encontrados {len(docs)} documentos relevantes")
        
        # 2. Construir contexto
        contexto = "\n\n".join([
            f"[Fuente: {doc.metadata['archivo']}]\n{doc.page_content}"
            for doc in docs
        ])
        
        # 3. Construir prompt
        prompt = f"""Basándote en la siguiente documentación técnica, responde la pregunta.
Si la respuesta no está en los documentos, di "No encuentro esta información en las minutas".
Siempre cita la fuente (archivo y fecha) de donde obtienes la información.

DOCUMENTACIÓN:
{contexto}

PREGUNTA: {pregunta}

RESPUESTA:"""
        
        # 4. Generar respuesta
        print("\nGenerando respuesta...")
        respuesta = self.llm(prompt)
        
        return respuesta, docs

if __name__ == "__main__":
    handler = RAGHandler()
    
    # Test
    pregunta = "¿Cómo calcula el sistema los resultados de la evaluación?"
    respuesta, docs = handler.responder(pregunta)
    
    print("\n=== RESPUESTA ===")
    print(respuesta)
    print(f"\n=== {len(docs)} DOCUMENTOS USADOS ===")
    for i, doc in enumerate(docs, 1):
        print(f"{i}. {doc.metadata['archivo']}")
```

Pasos:
1. Crear src/rag_handler.py
2. Implementar código anterior
3. Testear:
   python -c "
   from src.rag_handler import RAGHandler
   rag = RAGHandler()
   resp, docs = rag.responder('¿Qué es el sistema?')
   print(resp)
   "

Duración estimada: 30 minutos
Criterio de éxito: RAG responde una pregunta, cita fuentes
```

#### Tarea 4.2: Refinamiento de Prompts (30 min)

```
Objetivo: Ajustar prompts para mejor calidad de respuestas

Pasos:
1. Ejecutar 5-10 preguntas de prueba:
   - "¿Cuántos registros tiene la BD?"
   - "¿Cuáles son las etapas de evaluación?"
   - "¿Cómo se calculan automáticamente los resultados?"
   - "¿Hay límites de escalabilidad?"
   - "¿Quién es responsable de mantenimiento?"

2. Evaluar cada respuesta:
   - ¿Cita fuentes?
   - ¿Es correcta según minutas?
   - ¿Es clara y útil?

3. Ajustar prompt si es necesario:
   - Más específico (ej: "Responde en máximo 3 párrafos")
   - Mejor formato (ej: "Usa viñetas para listar")
   - Mejor instrucción sobre citación

Ejemplo de prompt mejorado:
"""
Eres un asistente técnico que responde preguntas sobre un sistema de selección de personal.
Basándote en la documentación proporcionada, responde de forma clara y concisa.

REGLAS:
1. Siempre cita la fuente exacta (archivo de minuta)
2. Si no encuentras la respuesta, di "No disponible en documentación"
3. Si hay información contradictoria, menciona ambas fuentes
4. Responde en máximo 2 párrafos

DOCUMENTACIÓN:
[...]

PREGUNTA: [...]

RESPUESTA:"""

4. Documentar cambios de prompts en archivo:
   src/prompts.py (guardar variaciones de prompts)

Duración estimada: 30 minutos
Criterio de éxito: Prompts refinados, respuestas mejores calidad
```

### 4.3 Checklist Fin de Miércoles

- ✅ RAG handler implementado y funcional
- ✅ 10 preguntas de prueba ejecutadas
- ✅ Prompts refinados según pruebas
- ✅ Respuestas citan fuentes correctamente

**Commit sugerido**: `git commit -m "feat: RAG handler con generación de respuestas"`

---

## 6. Fase 5: Jueves 12/3 (2 horas) - CLI Operacional + Documentación

### 6.1 Tareas

#### Tarea 5.1: Crear Interfaz CLI (60 min)

```
Objetivo: Script interactivo donde usuario hace preguntas

Archivo: src/main.py

Pseudocódigo:

```python
# src/main.py

import click
from src.rag_handler import RAGHandler

@click.group()
def cli():
    """Asistente de Documentación Técnica - RAG Local"""
    pass

@cli.command()
@click.option('--pregunta', prompt='Pregunta', help='Tu pregunta sobre documentación')
@click.option('--docs', default=3, help='Cantidad de documentos a recuperar')
def consultar(pregunta, docs):
    """Hacer una consulta al sistema RAG"""
    
    print("\n" + "="*60)
    print("ASISTENTE DE DOCUMENTACIÓN - RAG LOCAL")
    print("="*60 + "\n")
    
    try:
        rag = RAGHandler()
        respuesta, documentos = rag.responder(pregunta, num_documentos=docs)
        
        print("\n" + "="*60)
        print("RESPUESTA")
        print("="*60)
        print(respuesta)
        
        print("\n" + "="*60)
        print(f"DOCUMENTOS USADOS ({len(documentos)})")
        print("="*60)
        for i, doc in enumerate(documentos, 1):
            print(f"{i}. {doc.metadata['archivo']} (chunk {doc.metadata['chunk_idx']})")
            print(f"   Fecha: {doc.metadata['fecha']}")
            print()
    
    except Exception as e:
        print(f"❌ Error: {str(e)}")
        print("\nVerifica que:")
        print("  1. Ollama está corriendo (ollama serve)")
        print("  2. Los datos están indexados (python src/ingesta.py)")

@cli.command()
def info():
    """Información sobre el sistema RAG"""
    print("""
    ASISTENTE DE DOCUMENTACIÓN TÉCNICA
    ====================================
    
    Modelo LLM: Phi 2.7B (CPU-only)
    Vector DB: Qdrant (local)
    Embeddings: sentence-transformers (all-MiniLM-L6-v2)
    
    Uso:
      python src/main.py consultar --pregunta "Tu pregunta aquí"
    
    Ejemplos:
      python src/main.py consultar --pregunta "¿Cuántos registros tiene la BD?"
      python src/main.py consultar --pregunta "¿Cuáles son los pasos de evaluación?"
    """)

if __name__ == "__main__":
    cli()
```

Pasos:
1. Crear src/main.py
2. Implementar código anterior
3. Testear:
   python src/main.py info
   python src/main.py consultar --pregunta "¿Qué es el sistema?"

Duración estimada: 60 minutos
Criterio de éxito: CLI interactivo funciona, responde preguntas
```

#### Tarea 5.2: Documentación del Proyecto (60 min)

```
Objetivo: README y documentación para reproducir el piloto

Archivo: docs/README.md

Contenido esperado:

# Asistente RAG para Documentación Técnica

## Descripción

Sistema piloto que permite hacer consultas sobre documentación técnica
usando Retrieval-Augmented Generation (RAG) con LLM local.

### Caso de Uso
Sistema de Selección de Personal (Laravel, BD 40k registros)

## Requisitos

- Windows 10/11 Pro
- Python 3.10+
- 8 GB RAM
- 50 GB disco libre
- Conexión internet (para descargas iniciales)

## Instalación Rápida

### 1. Instalar Ollama y descargar modelo

```bash
# Descargar desde https://ollama.ai
# Instalar Windows installer
# En PowerShell:
ollama pull phi
ollama serve  # En terminal separada
```

### 2. Configurar ambiente Python

```bash
cd C:\Users\[TuUsuario]\Documents\rag-piloto
python -m venv venv_rag
.\venv_rag\Scripts\Activate.ps1

pip install -r requirements.txt
```

### 3. Preparar datos

```bash
# Colocar minutas (Word/PDF) en datos/minutas_raw/
# Convertir a .txt si es necesario

# Indexar en Qdrant
python src/ingesta.py
```

### 4. Consultar

```bash
python src/main.py consultar --pregunta "¿Cómo calcula el sistema?"
```

## Arquitectura

- **Ollama**: Runtime para ejecutar Phi 2.7B
- **Phi 2.7B**: Modelo LLM local (3 GB)
- **Qdrant**: Base de datos vectorial
- **SentenceTransformers**: Generador de embeddings
- **LangChain**: Orquestación de componentes
- **Click**: Framework CLI

Ver: arquitectura.md (documento detallado)

## Rendimiento Esperado

- **Latencia**: 5-8 segundos por respuesta (CPU-only)
- **Tokens/seg**: 25-40 (Phi 2.7B en i5-1235U)
- **Usuarios concurrentes**: 1 (no paralelizado)

## Limitaciones Actuales

- ❌ No soporta múltiples usuarios simultáneos
- ❌ Respuestas lentas (CPU-only, ideal para GPU dedicada)
- ❌ Contexto limitado a 2048 tokens (ventana Phi)
- ✅ Local y privado (sin cloud)
- ✅ Escalable a hardware mejor

## Roadmap

- [ ] Fase 1 (Piloto): CLI funcional + demo
- [ ] Fase 2 (Beta): GPU dedicada + Web UI + múltiples usuarios
- [ ] Fase 3 (Producción): Infraestructura distribuida + auditoría

## Estructura de Carpetas

```
rag-piloto/
├── venv_rag/
├── datos/
│   ├── minutas_raw/       # Minutas originales
│   ├── minutas_txt/       # Convertidas a texto
│   └── qdrant_storage/    # Base vectorial
├── src/
│   ├── main.py            # CLI
│   ├── rag_handler.py     # Lógica RAG
│   ├── ingesta.py         # Indexación
│   └── utils.py
├── docs/
├── tests/
├── requirements.txt
└── README.md
```

## Troubleshooting

### Error: "Ollama connection refused"
→ Asegurate que `ollama serve` está corriendo en otra terminal

### Error: "Model not found: phi"
→ Ejecuta `ollama pull phi` (3-4 GB, puede tardar)

### Respuestas muy lentas (>30 seg)
→ Normal en CPU-only. GPU sería 5x más rápido. Ver roadmap.

### Memoria insuficiente
→ Cerrar otras aplicaciones pesadas. 8 GB es el mínimo.

## Contacto

Preguntas: revisar documento arquitectura.md o plan-implementacion.md

---

Duración estimada: 60 minutos
Criterio de éxito: README completo y claro
```

### 6.2 Checklist Fin de Jueves

- ✅ CLI completamente funcional
- ✅ Usuarios pueden hacer consultas interactivamente
- ✅ README.md con instrucciones de instalación
- ✅ Troubleshooting incluido
- ✅ Arquitectura documentada

**Commit sugerido**: `git commit -m "feat: CLI operacional y documentación completa"`

---

## 7. Fase 6: Viernes 13/3 (3 horas) - Demo Interna + Feedback

### 7.1 Preparación de Demo (60 min)

```
Objetivo: Dejar todo listo para presentación

Tareas:
1. Crear script de demostración:
   - 5 preguntas pre-seleccionadas (buenas respuestas)
   - Ejecutar cada una antes del evento
   - Documentar tiempos de respuesta

2. Crear presentación (~10 minutos):
   - Problema que resuelve
   - Cómo funciona (diagrama RAG)
   - Demo en vivo
   - Limitaciones y roadmap
   - Aprobación solicitada

3. Validar ambiente:
   - Ollama running
   - Qdrant indexado
   - Todas las dependencias

4. Preparar hardware:
   - Cerrar aplicaciones pesadas
   - Tener 3-4 GB RAM libres para demo
```

### 7.2 Demo en Vivo (90 min)

```
Agenda:
- 10 min: Presentación (problema, solución, arquitectura)
- 5 min: Demostración 5 preguntas
- 10 min: Explicación limitaciones + roadmap
- 10 min: Preguntas y feedback
- 15 min: Lluvia de ideas si es positivo

Preguntas sugeridas para demo:
1. "¿Cuál es la arquitectura del sistema?"
2. "¿Cuántos registros maneja la base de datos?"
3. "¿Cómo se calculan automáticamente los resultados?"
4. "¿Hay limitaciones de escalabilidad?"
5. "¿Quién puede hacer mantenimiento si me voy?"

Respuestas esperadas:
- Respuestas correctas, con citas de minutas
- Latencia 5-8 segundos (aceptable para demo)
- Claridad sobre fuentes
```

### 7.3 Feedback y Siguiente Pasos (30 min)

```
Posibles resultados:

A) "Aprobado, continuar a Fase 2"
   → Siguiente: GPU dedicada + Web UI
   → Timeline: 2-3 semanas más

B) "Aprobado con ajustes"
   → Identificar qué necesita cambio
   → Iteración rápida

C) "Rechazado, necesita más trabajo"
   → Documentar feedback específico
   → Ajustar y re-presentar

D) "Interesante pero low priority"
   → Guardar proyecto para después
   → Documentar para futuro
```

### 7.4 Checklist Fin de Viernes

- ✅ Demo ejecutada exitosamente
- ✅ Feedback recopilado
- ✅ Siguiente fase clara
- ✅ Documentación final actualizada

**Commit sugerido**: `git commit -m "demo: piloto exitoso, lista para feedback"`

---

## 8. Entregables Finales

### 8.1 Código

```
Repositorio GitHub: Maruch11/rag-piloto
├── src/main.py (CLI)
├── src/rag_handler.py (Lógica RAG)
├── src/ingesta.py (Indexación)
├── requirements.txt
└── docs/README.md
```

### 8.2 Documentación

```
1. analisis-hardware.md (Este fin de semana)
   - Especificaciones laptop
   - Limitaciones y restricciones
   - Viabilidad con Phi 2.7B

2. arquitectura.md (Este fin de semana)
   - Componentes técnicos
   - Diagrama RAG
   - Stack tecnológico
   - Flujo de datos

3. plan-implementacion.md (Este documento)
   - Timeline detallado
   - Tareas y criterios de éxito
   - Entregables

4. README.md en código
   - Instalación y uso
   - Troubleshooting
```

### 8.3 Demo

```
Video/Registro (opcional):
- Screen recording de 10 min mostrando:
  1. Sistema funcionando
  2. 5 preguntas y respuestas
  3. Cómo escalaría con GPU

```

---

## 9. Risk Management

### 9.1 Riesgos Identificados

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|------------|---------|-----------|
| Ollama no instala en Windows 11 | Media | Alto | Usar WSL2 como workaround |
| Qdrant tarda mucho indexando | Baja | Medio | Optimizar chunks size |
| Phi genera respuestas malas | Media | Medio | Refinamiento de prompts |
| RAM insuficiente durante demo | Media | Alto | Cerrar apps, usar solo 3-5 minutas |
| No hay tiempo suficiente | Baja | Medio | Priorizar MVP mínimo |

### 9.2 Plan B (Si faltan horas)

**Prioridad descending:**
1. ✅ Setup Ollama + Phi funcionando
2. ✅ Ingesta básica de minutas
3. ✅ RAG respondiendo preguntas
4. ⚠️ CLI pulida (si no hay tiempo, CLI basic funciona)
5. ⚠️ Documentación completa (README mínimo válido)
6. ⚠️ Demo slick (demo funcional basta)

---

## 10. Conclusión

Este plan es:
- ✅ **Realista**: 13 horas distribuidas en 6 días
- ✅ **Alcanzable**: MVP mínimo viable en Jueves
- ✅ **Escalable**: Roadmap claro a Fase 2
- ✅ **Demostrable**: Demo impactante Viernes

**Objetivo final**: Presentar un piloto funcionando que demuestre:
1. Entiendas arquitectura RAG
2. Implementes soluciones con LLM + Python
3. Seas capaz de escalar a producción

---

**Documento preparado para**: Mariana Emilia Mazzoccoli  
**Propósito**: Guía de implementación paso-a-paso del piloto  
**Inicio**: 7/3/2026 (sábado noche)  
**Fin previsto**: 13/3/2026 (viernes noche)
**Bibliografía**:
Ollama: https://github.com/ollama/ai#readme + https://ollama.ai/library/phi
LangChain: https://python.langchain.com/docs/get_started/introduction
Qdrant: https://qdrant.tech/documentation/
SentenceTransformers: https://www.sbert.net/docs/usage/semantic_search.html
Click (CLI): https://click.palletsprojects.com/
Python venv: https://docs.python.org/3/tutorial/venv.html