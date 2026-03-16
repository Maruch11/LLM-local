# Acceso inicial al servidor de pruebas LLM Local

## Objetivo

Documentar el acceso inicial al servidor provisto por Infraestructura y
la verificación del entorno base para comenzar pruebas de arquitectura
de **LLM local**.

Este entorno se utilizará para:

-   pruebas de arquitectura RAG
-   validación de pipeline (chunking, embeddings, retrieval)
-   ejecución de modelos pequeños
-   medición de consumo de recursos (CPU / RAM)

---

# Información del servidor

  Parámetro | Valor
---|---
  Sistema operativo | Debian GNU/Linux 13 (Trixie)
  Runtime de contenedores |  Docker
  Orquestación | Docker Compose
  Acceso | SSH
  Estado inicial | Servidor operativo sin contenedores desplegados

---

# Conexión al servidor

Acceso mediante SSH.

``` bash
ssh usuario@IP_SERVIDOR
```

Ejemplo:

``` bash
ssh usuario@10.26.xxx.xxx
```

En la primera conexión aparece:

    Are you sure you want to continue connecting (yes/no/[fingerprint])?

Esto ocurre porque el cliente SSH aún no conoce la huella criptográfica
del servidor.

Responder:

    yes

Luego ingresar la contraseña provista por Infraestructura, (bpm, recibir un documento tipo "one time secret", almacenar la contraseña en un gestor).

---

# Verificación inicial del entorno

Una vez conectado se ejecutan comandos básicos para verificar el
entorno.

---

## 1. Ver hostname del servidor

``` bash
hostname
```

**Qué hace**

Muestra el nombre del host del sistema.\
Permite confirmar que la conexión se estableció contra el servidor
correcto.

---
## 2. Ver versión del sistema operativo

``` bash
cat /etc/os-release
```

**Qué hace**

Muestra información del sistema operativo Linux instalado.

Resultado observado:

    PRETTY_NAME="Debian GNU/Linux 13 (trixie)"

Esto confirma la distribución y versión del sistema.

---

## 3. Verificar instalación de Docker

``` bash
docker --version
```

**Qué hace**

Muestra la versión del runtime de contenedores Docker instalado en el
servidor.

Resultado:

    Docker 29.3.0

Esto confirma que el entorno permite ejecutar contenedores.

---
## 4. Verificar Docker Compose

``` bash
docker compose version
```

**Qué hace**

Muestra la versión de Docker Compose, herramienta utilizada para definir
y ejecutar aplicaciones multi‑contenedor.

Resultado:

    Docker Compose 5.1.0

---

## 5. Ver contenedores activos

``` bash
docker ps
```

**Qué hace**

Lista los contenedores Docker actualmente en ejecución.

Resultado inicial:

    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

Esto indica que aún no hay contenedores desplegados.

------------------------------------------------------------------------

## 6. Ver estructura de directorios

``` bash
ls -la
```

**Qué hace**

Lista los archivos y directorios del directorio actual mostrando:

-   permisos
-   propietario
-   tamaño
-   fecha

Resultado relevante:

    llm/

Este directorio corresponde al proyecto creado por Infraestructura.

------------------------------------------------------------------------

# Acceso al directorio del proyecto

``` bash
cd llm
```

**Qué hace**

Cambia el directorio actual al directorio del proyecto donde se
ubicarán:

-   archivos de configuración
-   docker-compose
-   servicios del entorno LLM

------------------------------------------------------------------------

# Estado actual del entorno

Infraestructura dejó configurado:

-   servidor Debian 13
-   acceso SSH
-   Docker instalado
-   Docker Compose instalado
-   directorio inicial del proyecto

Pendiente:

-   creación de contenedores
-   definición de docker-compose
-   despliegue de aplicaciones LLM
