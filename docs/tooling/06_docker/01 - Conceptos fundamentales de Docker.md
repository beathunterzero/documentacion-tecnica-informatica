## 1. La Imagen: El Plano Estático

Una imagen es una **plantilla inmutable** y de solo lectura que sirve como cimiento para los contenedores.

- **Composición:** Se estructura en capas (layers) que incluyen el OS base, binarios, dependencias y configuraciones.
    
- **Naturaleza:** Es estática; no ejecuta procesos por sí misma.
    
- **Analogía:** Es el equivalente a un archivo **ISO** o una **receta** de cocina.
    
- **Ejemplos:** `ubuntu`, `nginx`, `python:3.12`.
    

## 2. El Contenedor: La Instancia Viva

Un contenedor es la ejecución de una imagen. Es un entorno aislado y seguro donde corren las aplicaciones.

- **Naturaleza:** Mutable (mientras está activo) y ejecutable.
    
- **Estado:** Puede iniciarse, detenerse y eliminarse. Utiliza un sistema de archivos _Copy-on-Write_ (CoW).
    
- **Analogía:** Si la imagen es la receta, el contenedor es el **plato servido**.
    

### 2.1 Matriz Comparativa: Imagen vs. Contenedor

|**Característica**|**Imagen**|**Contenedor**|
|---|---|---|
|**Definición**|Plantilla / Clase|Instancia / Objeto|
|**Estado**|Inmutable (No cambia)|Mutable (Puede cambiar)|
|**Actividad**|Estática (No ejecuta)|Dinámica (Ejecuta procesos)|
|**Persistencia**|Permanente (Se almacena)|Efímera (Se desecha)|

## 3. Dinámica de Ejecución (`docker run`)

Cada ejecución del comando `docker run` genera un **nuevo contenedor**, independientemente de si la imagen ya ha sido utilizada anteriormente.

1. **Localización:** Busca la imagen local; si no, la descarga (_pull_).
    
2. **Creación:** Instancia un contenedor único con su propio ID writable.
    
3. **Ejecución:** Inicia el proceso definido (_entrypoint_).
    
4. **Finalización:** Al terminar el proceso, el contenedor pasa a estado `Exited`, pero sigue existiendo en el disco hasta ser removido (`rm`).
    

## 4. Arquitectura del Docker Engine

El Engine es el software cliente-servidor que orquesta los componentes de Docker.

- **containerd:** Daemon que gestiona el ciclo de vida completo de los contenedores (transferencia de imágenes, ejecución, almacenamiento y redes).
    
- **runc:** Herramienta de línea de comandos para ejecutar contenedores según la especificación OCI (_Open Container Initiative_).
    
- **Networking & Storage:** Componentes encargados de la comunicación entre contenedores (bridge, host) y la persistencia de datos (volúmenes).
    

## 5. Implementación en WSL (Windows)

Docker Desktop opera en Windows mediante dos distribuciones específicas de WSL que separan la lógica de los datos:

- **docker-desktop:** Distribución que aloja el motor (**Docker Engine**) y los daemons necesarios.
    
- **docker-desktop-data:** Repositorio persistente donde se almacenan físicamente las **imágenes, contenedores y volúmenes**.
    

> **Nota de Seguridad:** No manipular manualmente los archivos dentro de estas distribuciones en WSL, ya que cualquier cambio directo puede corromper la instancia de Docker Desktop.

---

### Referencias Externas

- [Docker Overview: Images and Containers](https://docs.docker.com/get-started/overview/)
    
- [Deep dive into containerd and runc](https://www.google.com/search?q=https://www.docker.com/blog/containerd-vs-runtime/)
    
- [Docker Desktop WSL 2 backend architecture](https://docs.docker.com/desktop/wsl/)
    

### Documentación Relacionada

[[02 - Instalación y configuración con WSL de Docker]]
[[03 - Comandos esenciales en Docker]]
[[05 - Buenas prácticas en Docker]]
[[06 - Docker Compose]]