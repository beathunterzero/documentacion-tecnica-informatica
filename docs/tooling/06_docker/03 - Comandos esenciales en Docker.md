## 1. Gestión de Imágenes

Las imágenes son plantillas inmutables que contienen el sistema de archivos y los parámetros necesarios para ejecutar una aplicación.

- **Listado de Recursos:** Visualiza todas las imágenes locales, incluyendo su ID, etiqueta y tamaño.
    
    `docker images`
    
- **Adquisición:** Descarga una imagen específica desde Docker Hub u otros registros.
    
    `docker pull <imagen>`
    
- **Eliminación:** Borra una imagen local (requiere que no existan contenedores asociados, incluso detenidos).
    
    `docker rmi <imagen>`
    

## 2. Operaciones con Contenedores

Los contenedores son instancias aisladas y ejecutables de una imagen.

- **Monitoreo de Estado:**
    
    - `docker ps`: Muestra únicamente contenedores en ejecución.
        
    - `docker ps -a`: Lista la totalidad de contenedores (activos, pausados y terminados).
        
- **Ciclo de Vida:**
    
    - **Ejecución:** Crea y arranca una instancia.
        
        `docker run <opciones> <imagen>` (Ejemplo: `-d` para segundo plano, `-p` para mapeo de puertos).
        
    - **Control de Estado:** * `docker stop <id>`: Detención controlada (SIGTERM).
        
        - `docker start <id>`: Reinicio de una instancia detenida.
            
    - **Limpieza:**
        
        - `docker rm <id>`: Elimina un contenedor específico (debe estar detenido).
            
        - `docker container prune`: Purga masiva de todos los contenedores detenidos.
            

## 3. Interacción y Depuración

Comandos críticos para el análisis técnico y la administración interna de servicios.

- **Acceso Interactivo:** Abre una terminal dentro del contenedor para auditoría o configuración.
    
    `docker exec -it <id> bash` (Usar `sh` si el contenedor es minimalista como Alpine).
    
- **Análisis de Logs:** * `docker logs <id>`: Volcado histórico de la salida estándar (STDOUT).
    
    - `docker logs -f <id>`: Seguimiento en tiempo real (Follow), esencial para monitoreo.
        
- **Transferencia de Archivos:**
    
    - Hacia el contenedor: `docker cp <archivo_local> <id>:<ruta_destino>`
        
    - Hacia el host: `docker cp <id>:<ruta_archivo> <destino_local>`
        

## 4. Inspección y Metadatos del Sistema

Herramientas para extraer información detallada de la infraestructura y objetos de Docker.

- **Diagnóstico de Objeto:** Devuelve la configuración técnica completa (redes, volúmenes, variables de entorno) en formato JSON.
    
    `docker inspect <id>`
    
- **Estado del Daemon:** Proporciona un resumen operativo del entorno (drivers, contenedores totales, imágenes).
    
    `docker info`
    
- **Versión:** Detalla la arquitectura y versión del cliente y servidor.
    
    `docker version`
    

---

### Referencias Externas

- [Docker CLI Reference Documentation](https://www.google.com/search?q=https://docs.docker.com/engine/reference/commandline/cli/)
    
- [Docker Cheat Sheet (Official)](https://docs.docker.com/get-started/docker_cheatsheet.pdf)
    
- [DigitalOcean: How to use Docker Exec](https://www.digitalocean.com/community/tutorials/how-to-use-docker-exec-to-run-commands-in-a-docker-container)
    

### Documentación Relacionada

[[01 - Conceptos fundamentales de Docker]]