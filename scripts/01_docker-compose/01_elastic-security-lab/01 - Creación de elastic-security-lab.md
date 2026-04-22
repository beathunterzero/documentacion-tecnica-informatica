## 1. Introducción y Propósito

El **Elastic Security Lab** es un entorno SIEM (_Security Information and Event Management_) diseñado para centralizar, analizar y visualizar logs. Usamos la versión **8.17.10 (LTS)** por su estabilidad en **WSL2**.

## 2. Estructura de Directorios (FileSystem)

La organización es jerárquica para facilitar la escalabilidad del laboratorio:

Bash

```bash
elastic-security-lab/
├── datasets/            # Repositorio de logs crudos
│   ├── aws/             # Cloudtrail, Guardduty, S3, VPC
│   ├── azure/           # Activity, Audit, Firewall, Signin
│   ├── linux/           # Logs de sistemas Linux
│   ├── windows/         # Eventos de Windows
│   └── firewall/        # Tráfico de red
├── docs/                # Documentación y Diagramas
│   └── architecture/    # Diagramas Excalidraw y PNG
├── filebeat/            # Configuración del agente recolector
│   └── filebeat.yml
└── docker-compose.yml   # Orquestador del Stack
```


- `datasets/`: Repositorio organizado por plataforma (AWS, Azure, Linux, Windows, Firewall).
    
- `docs/architecture/`: Diagramas de diseño (`.excalidraw` y `.png`).
    
- `filebeat/`: Archivos de configuración del agente de ingesta.
    
- `docker-compose.yml`: El manifiesto que orquesta todo el stack.
    

---

## 3. Desglose Técnico: docker-compose.yml

### 3.1 Servicio: Elasticsearch (es01)

Es el motor de base de datos y búsqueda.

- `image: ...:8.17.10`: Versión LTS estable para abril 2026.
    
- `container_name: es01`: Identificador único del contenedor.
    
- `restart: unless-stopped`: Garantiza que el servicio suba tras un reinicio del host.
    
- `environment`:
    
    - `node.name=es01`: Nombre del nodo en el cluster.
        
    - `discovery.type=single-node`: Configuración para laboratorio (un solo servidor).
        
    - `xpack.security.enabled=true`: Habilita el control de accesos.
        
    - `ELASTIC_PASSWORD=changeme`: Define la clave maestra inicial.
        
    - `xpack.security.http.ssl.enabled=false`: Desactiva SSL para simplificar la conectividad en entorno local.
        
    - `ES_JAVA_OPTS=-Xms2g -Xmx2g`: Reserva **2GB de RAM** fijos para el proceso Java.
        
- `ulimits.memlock`: Permite que Elasticsearch bloquee su memoria para evitar que el sistema la mueva al disco (mejorando el rendimiento).
    
- `volumes`: Mapea `esdata` para que los logs no se borren al apagar el contenedor.
    
- `ports`: Expone el puerto `9200` para comunicación con la base de datos.
    
- `healthcheck`: Comando que verifica si el cluster está en estado "green" o "yellow" antes de permitir que otros servicios se conecten.
    

### 3.2 Servicio: Kibana

La interfaz gráfica para el analista.

- `depends_on`: Asegura que Kibana no intente arrancar hasta que Elasticsearch esté saludable.
    
- `environment`:
    
    - `ELASTICSEARCH_HOSTS`: Dirección interna de la base de datos.
        
    - `ELASTICSEARCH_USERNAME`: Usuario técnico `kibana_system`.
        
    - `SERVER_PUBLICBASEURL`: Define la URL de acceso local.
        
    - `XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY`: Llave para cifrar la base de datos de objetos de Kibana.
        
- `ports`: Expone el puerto `5601` para el acceso web.
    

### 3.3 Servicio: Filebeat

El recolector de logs que "lee" los datasets.

- `user: root`: Necesario para tener permisos de lectura sobre los archivos montados.
    
- `command: ["-e", "--strict.perms=false"]`: Envía logs a la consola y relaja la verificación de permisos de archivos en Windows/WSL.
    
- `volumes`: Monta el archivo de configuración y la carpeta de logs en modo **Solo Lectura (`:ro`)**.
    

---

## 4. Desglose Técnico: filebeat.yml

### 4.1 Inputs (Fuentes de Datos)

Cada bloque define un origen de datos distinto:

- `type: filestream`: Método moderno para leer archivos línea por línea.
    
- `paths`: Ubicación exacta de los logs (ej. `/datasets/aws/cloudtrail/*.log`).
    
- `fields`: Agrega metadatos (etiquetas) como `platform: "aws"`. Esto es vital para que el analista sepa de dónde viene el log sin abrirlo.
    
- `ignore_older: 0s`: Instruye a Filebeat a procesar todos los archivos, sin importar su antigüedad.
    

### 4.2 Output y Procesadores

- `output.elasticsearch`: Define a dónde se envían los datos procesados (User/Pass de Elastic).
    
- `setup.kibana`: Configuración para que Filebeat pueda crear automáticamente sus propios dashboards en Kibana.
    
- `processors`: Añaden metadatos automáticos del host, Docker y la nube (Cloud) para enriquecer la investigación forense.
    

---

## 5. Guía de Ejecución y Mantenimiento

1. **Levantar:** `docker-compose up -d`.
    
2. **Seguridad:** Resetear la contraseña de Kibana para que los servicios se hablen correctamente:
    
    `docker exec -it es01 bin/elasticsearch-reset-password -u kibana_system`
    
3. **Persistencia:** Si cambias la contraseña, actualízala en el archivo `.yml` y reinicia con `docker-compose down && docker-compose up -d`.
    
4. **Acceso:** `http://localhost:5601`.
    

---

### Documentación Relacionada

[[02 - Creación de un Data View en Kibana]]
[[03 - Crear un usuario en Elasticsearch desde Kibana]]
[[01 - Filosofía y estrategia del Threat Hunting]]
[[06 - Docker Compose]]