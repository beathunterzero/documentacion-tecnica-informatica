## Imágenes

Las imágenes son plantillas a partir de las cuales se crean contenedores.

```bash
docker images
```

Muestra todas las imágenes descargadas en tu máquina. Incluye nombre, etiqueta, ID, tamaño y fecha de creación.


```bash
docker pull <imagen>
```

Descarga una imagen desde Docker Hub (u otro registro). Ejemplo: `docker pull nginx`


```bash
docker rmi <imagen>
```

Elimina una imagen local. Puedes usar el nombre o el ID. Si la imagen está siendo usada por un contenedor, deberás eliminar primero el contenedor.

## Contenedores

Los contenedores son instancias ejecutables basadas en imágenes.

```bash
docker ps
```

Lista los contenedores **en ejecución**.


```bash
docker ps -a
```

Lista **todos** los contenedores, incluyendo los detenidos.


```bash
docker run <imagen>
```

Crea y ejecuta un contenedor basado en la imagen indicada. Ejemplo: `docker run nginx` Puedes agregar opciones como `-d` (modo demonio) o `-p` (puertos).


```bash
docker stop <id>
```

Detiene un contenedor en ejecución de forma segura.


```bash
docker start <id>
```

Inicia un contenedor que está detenido.


```bash
docker rm <id>
```

Elimina un contenedor. Debe estar detenido para poder borrarlo.


```bash
docker container prune
```

Elimina **todos los contenedores detenidos**. Ideal para limpiar el sistema.

## Acceder a un contenedor

```bash
docker exec -it <id> bash
```

Abre una terminal interactiva dentro del contenedor. Requiere que el contenedor tenga `bash` instalado (si no, usar `sh`).

Ejemplo: `docker exec -it 123abc sh`

## Logs

```bash
docker logs <id>
```

Muestra los logs generados por el contenedor.


```bash
docker logs -f <id>
```

Muestra los logs en tiempo real (modo _follow_). Útil para monitorear aplicaciones.

## Información del sistema

```bash
docker info
```

Muestra información detallada del entorno Docker:

- Número de contenedores
- Número de imágenes
- Driver de almacenamiento
- Configuración del sistema


```bash
docker version
```

Muestra la versión del cliente y del servidor Docker.

## Inspeccionar contenedores

```bash
docker inspect <id>
```

Devuelve información detallada en formato JSON:

- Configuración
- Variables de entorno
- Volúmenes
- Puertos
- Red

Muy útil para depuración.

## Copiar archivos

```bash
docker cp archivo.txt <id>:/ruta/
```

Copia un archivo **desde tu máquina al contenedor**.


```bash
docker cp <id>:/ruta/archivo.txt .
```

Copia un archivo **desde el contenedor a tu máquina**.

**************
## También puedes ver:
[[Buenas prácticas en Docker]]