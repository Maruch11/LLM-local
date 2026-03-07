# Glosario técnico — LLM local

## ACL (Access Control List)
Lista de control de acceso que define qué usuarios o sistemas pueden acceder a determinados recursos o datos.

## API (Application Programming Interface)
Interfaz que permite que un sistema se comunique con otro mediante solicitudes y respuestas estructuradas.

## Chunk
Fragmento de texto en el que se divide un documento para poder procesarlo e indexarlo en un sistema de recuperación de información.

## Chunking
Proceso de dividir documentos en múltiples chunks para facilitar su indexación y recuperación en sistemas RAG.

## Chunk Size
Cantidad de texto incluida en cada chunk. Influye en la calidad del retrieval y en el contexto que recibe el modelo.

## Chunk Overlap
Porción de texto que se repite entre chunks consecutivos para evitar pérdida de contexto en los límites del texto.

## CI Evaluation (Continuous Integration Evaluation)
Evaluación automática de calidad del sistema durante procesos de integración continua.

## Docker
Tecnología de contenedores que permite empaquetar aplicaciones y dependencias para ejecutarlas de forma consistente en distintos entornos.

## Embedding
Representación vectorial de un texto que captura su significado semántico y permite compararlo matemáticamente con otros textos.

## Embedding Model
Modelo de machine learning diseñado para transformar texto en vectores numéricos que representan su significado.

## Endpoint
URL específica expuesta por una API para recibir solicitudes y devolver respuestas.

## Golden Set
Conjunto de preguntas y respuestas de referencia utilizado para evaluar el desempeño de un sistema de IA.

## GPU (Graphics Processing Unit)
Unidad de procesamiento diseñada para operaciones paralelas intensivas. Se utiliza para acelerar la inferencia de modelos de IA.

## Hallucination Rate
Métrica que mide la frecuencia con la que un modelo genera información incorrecta o no respaldada por las fuentes.

## Ingest
Proceso de incorporación de documentos al sistema para su procesamiento e indexación.

## Ingest Incremental
Proceso de agregar nuevos documentos a un sistema sin tener que reprocesar todo el corpus existente.

## LLM (Large Language Model)
Modelo de inteligencia artificial entrenado sobre grandes volúmenes de texto para comprender y generar lenguaje natural.

## LLM Runtime
Entorno de ejecución donde se carga y se utiliza el modelo LLM para responder consultas.

## Logging
Registro sistemático de eventos y operaciones del sistema para diagnóstico y auditoría.

## Métricas
Indicadores cuantitativos utilizados para medir el rendimiento, calidad y comportamiento del sistema.

## Modelo
Archivo que contiene los pesos y la arquitectura de una red neuronal entrenada.

## Monitoreo
Proceso continuo de supervisión del estado y funcionamiento del sistema.

## On-Premise
Infraestructura informática instalada y operada dentro de la organización, en lugar de utilizar servicios en la nube.

## Parser
Componente que analiza documentos y extrae su contenido textual para que pueda ser procesado por el sistema.

## Pipeline
Secuencia de etapas por las que pasa una solicitud o documento dentro de un sistema.

## Prompt
Texto o instrucción que se envía a un modelo de lenguaje para obtener una respuesta.

## Prompt Builder
Componente que construye el prompt final combinando la consulta del usuario y el contexto recuperado.

## Prompt Injection
Técnica de ataque que intenta manipular el comportamiento del modelo mediante instrucciones maliciosas en el prompt.

## Qdrant
Base de datos vectorial diseñada para almacenar embeddings y realizar búsquedas semánticas eficientes.

## RAG (Retrieval Augmented Generation)
Arquitectura que combina recuperación de información con generación de texto para producir respuestas basadas en documentos.

## Recall (Retrieval Recall)
Métrica que mide qué proporción de los documentos relevantes fueron recuperados por el sistema.

## Retriever
Componente encargado de recuperar los documentos más relevantes desde la base vectorial.

## Reranker
Modelo adicional que reordena los resultados recuperados para priorizar los más relevantes.

## Runtime
Entorno donde se ejecuta una aplicación o modelo.

## Seguridad de datos
Conjunto de prácticas destinadas a proteger información sensible durante el procesamiento y almacenamiento.

## Token
Unidad mínima de texto procesada por un modelo de lenguaje.

## Tokenización
Proceso de dividir un texto en tokens que el modelo puede interpretar.

## Vector
Representación numérica multidimensional utilizada para capturar significado semántico.

## Vector Database
Base de datos optimizada para almacenar vectores y realizar búsquedas por similitud.

## VRAM
Memoria de la GPU utilizada para cargar modelos y procesar operaciones de inferencia.