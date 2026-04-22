## 1. Definición y Propósito

**Docker Compose** es una herramienta diseñada para definir y gestionar aplicaciones multi-contenedor. Permite orquestar de manera declarativa servicios, redes y volúmenes en un único archivo de configuración, facilitando la replicación de entornos completos con un solo comando.

- **Función Principal:** Orquestador local para describir la interacción entre múltiples servicios.
    
- **Archivo de Configuración:** Utiliza el formato **YAML** (`docker-compose.yml`), el cual destaca por ser legible y estructurado mediante indentación.
    

## 2. Estructura del Archivo YAML

Un archivo `docker-compose.yml` se organiza en secciones clave que definen el estado deseado de la aplicación:

|**Directiva**|**Descripción**|
|---|---|
|**version**|Versión del formato de Compose (ej. "3.9").|
|**services**|Bloque donde se definen los contenedores (servicios).|
|**image / build**|Define si se usa una imagen existente o se construye desde un `Dockerfile`.|
|**ports**|Mapeo de puertos entre el host y el contenedor (`HOST:CONTAINER`).|
|**volumes**|Configuración de persistencia de datos y montajes.|
|**depends_on**|Establece el orden de inicio (ej. la web espera a la base de datos).|
|**restart**|Define la política de reinicio ante fallos o detenciones.|

## 3. Gestión del Ciclo de Vida (CLI)

Comandos esenciales para administrar el stack definido en el archivo Compose:

### 3.1 Despliegue y Control

- **Levantar Entorno:** Crea y arranca todos los servicios.
    
    - `docker compose up` (Usa `-d` para segundo plano/detach).
        
    - `docker compose up --build` (Fuerza la reconstrucción de imágenes).
        
- **Estado de Servicios:** Lista los contenedores asociados al proyecto y su estado.
    
    - `docker compose ps`
        
- **Reiniciar:**
    
    - `docker compose restart [servicio]`
        

### 3.2 Detención y Limpieza

- **Parar Servicios:** Detiene los contenedores pero mantiene los recursos.
    
    - `docker compose stop`
        
- **Eliminación Total:** Detiene y elimina contenedores y redes creadas.
    
    - `docker compose down`
        
    - `docker compose down -v` (Elimina también los **volúmenes** persistentes).
        
    - `docker compose down --rmi all` (Elimina también las **imágenes** asociadas).
        

## 4. Diagnóstico e Interacción

- **Monitoreo de Logs:**
    
    - `docker compose logs -f [servicio]` (Seguimiento en tiempo real).
        
- **Ejecución de Comandos:** Acceso directo a un servicio específico sin necesidad de usar el ID del contenedor.
    
    - `docker compose exec [servicio] bash`
        

## 5. Networking y Persistencia

- **Red Automática:** Compose crea una red interna por defecto. Los servicios pueden comunicarse entre sí utilizando su **nombre de servicio** como hostname (ej. `http://db:3306`).
    
- **Volúmenes Nombrados:** Permiten que los datos sobrevivan a la eliminación del contenedor, declarándose en la raíz del archivo y asignándose dentro de cada servicio.
    

## 6. Políticas de Reinicio (Restart)

|**Política**|**Descripción**|
|---|---|
|`no`|No reinicia automáticamente (por defecto).|
|`always`|Reinicia siempre que el contenedor se detenga.|
|`on-failure`|Reinicia solo si el contenedor falla (exit code distinto de 0).|
|`unless-stopped`|Reinicia siempre, excepto si el usuario lo detuvo manualmente.|

---

### Referencias Externas

- [Docker Compose Specification](https://docs.docker.com/compose/compose-file/)
    
- [Overview of Docker Compose CLI](https://docs.docker.com/compose/reference/)
    
- [YAML Ain't Markup Language (Official Site)](https://yaml.org/)
    

### Documentación Relacionada

[[01 - Conceptos fundamentales de Docker]]
[[05 - Buenas prácticas en Docker]]