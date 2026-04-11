# Imagen

Una **imagen** es la base de todo en Docker.

### ¿Qué es exactamente una imagen?

- Es una **plantilla inmutable**: una vez creada, no cambia.
- Contiene:
    - Sistema operativo base (ej. Debian, Alpine)
    - Binarios y dependencias
    - Configuración inicial
    - Scripts o entrypoints
- Se construye por capas (_layers_), lo que permite reutilización y eficiencia.

### Idea clave

Una imagen es como un **ISO** o una **fotografía congelada** de un sistema.

### Ejemplos comunes de imágenes

- `ubuntu` → sistema base
- `debian` → sistema base estable
- `python:3.12` → Python preinstalado
- `nginx` → servidor web listo para usar

### Características importantes

- No ejecuta procesos por sí misma.
- Puede ser versionada: `python:3.12`, `python:3.10`.
- Puede ser personalizada con un `Dockerfile`.

# Contenedor

Un **contenedor** es una instancia viva de una imagen.

### Qué es un contenedor

- Es un **entorno aislado** que ejecuta procesos.
- Se crea a partir de una imagen.
- Puede tener cambios temporales (archivos, configuraciones, logs).
- Puede iniciarse, detenerse, reiniciarse o eliminarse.

### Idea clave

Si la imagen es la **receta**, el contenedor es el **plato servido**.

### Características

- Tiene su propio sistema de archivos (copy-on-write).
- Puede tener redes, volúmenes y puertos asignados.
- Su estado no es permanente a menos que se use un volumen.

# Diferencia clave entre Imagen y Contenedor

|Imagen|Contenedor|
|---|---|
|Plantilla|Instancia|
|No cambia|Puede cambiar|
|No ejecuta procesos|Ejecuta procesos|
|Se almacena|Se ejecuta|
|Inmutable|Mutable|
|Reutilizable|Desechable|

# ¿Por qué `docker run` crea contenedores nuevos?

Cada vez que ejecutas:

```bash
docker run hello-world
```

Docker realiza:
1. Busca la imagen localmente.
2. Si no existe, la descarga.
3. Crea un contenedor nuevo basado en esa imagen.
4. Ejecuta el proceso definido en la imagen.
5. El proceso termina.
6. El contenedor queda detenido.

### ¿Por qué se acumulan contenedores?

Porque **cada** `docker run` **crea un contenedor nuevo**, incluso si usas la misma imagen.

Ejemplo:

```bash
docker ps -a
```

Verás muchos contenedores "Exited".

# Contenedores persistentes vs no persistentes

## Contenedores no persistentes (ejemplo: hello-world)

- Ejecutan un proceso corto.
- Terminan inmediatamente.
- No se pueden reiniciar porque su proceso ya finalizó.

Ejemplo:

```bash
docker run hello-world
```

## Contenedores persistentes (ejemplo: Debian)

```bash
docker run -it --name debianlab debian bash
```

Puedes detenerlo y volver a entrar:

```bash
docker stop debianlab
docker start -ai debianlab
```

### ¿Por qué este sí es persistente?

Porque el proceso `bash` puede iniciarse y detenerse sin destruir el contenedor.

# Qué es el Docker Engine

El **Docker Engine** es el corazón de Docker. Es el motor que permite:

- Crear contenedores
- Ejecutarlos
- Gestionar redes
- Gestionar almacenamiento
- Descargar imágenes

### Componentes principales

#### containerd

- Servicio que gestiona el ciclo de vida de contenedores.
- Es el "administrador" de contenedores.

#### runc

- Ejecuta contenedores a nivel del sistema.
- Implementa el estándar OCI.

#### Networking

- Maneja redes:
    - bridge
    - host
    - overlay
    - macvlan

#### Almacenamiento

- Maneja capas de imágenes y sistemas de archivos.
### Idea clave

Docker Engine es el **motor**, containerd es el **administrador**, runc es el **ejecutor**.

# Qué son `docker-desktop` y `docker-desktop-data` en WSL

Cuando usas Docker Desktop en Windows, este crea dos distribuciones WSL:

### docker-desktop

- Contiene el entorno donde corre el Docker Engine.
- Es donde vive el daemon.

### docker-desktop-data

- Almacena:
    - Imágenes
    - Contenedores
    - Volúmenes
    - Configuraciones internas

### Advertencia importante

**Nunca debes modificar estos entornos manualmente.** Podrías corromper Docker Desktop.

*****************
## También puedes ver:
[[Instalación y configuración con WSL de Docker]]
[[Comandos esenciales en Docker]]
[[Buenas prácticas en Docker]]
