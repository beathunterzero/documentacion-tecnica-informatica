# ¿Qué es Docker Compose?

Docker Compose es una herramienta que permite:
- Definir múltiples contenedores en un solo archivo.
- Configurar redes, volúmenes, variables y dependencias.
- Levantar toda una aplicación con un solo comando.
- Gestionar el ciclo de vida de los servicios.

### Idea clave

Compose es como un **orquestador local**: describe cómo deben funcionar varios contenedores juntos.

# ¿Qué es un archivo YAML?

Docker Compose usa archivos **YAML** (`docker-compose.yml`).

### YAML significa:

**Y**AML **A**in't **M**arkup **L**anguage.

### Características:

- Es un formato **legible por humanos**.
- Usa indentación para definir estructura.
- No usa llaves ni corchetes.
- Es ideal para configuraciones declarativas.

### Ejemplo simple:

```yaml
version: "3.9"
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

# Estructura de un archivo docker-compose.yml

### Componentes principales:

### version

Define la versión del esquema de Compose.
### services

Lista de contenedores que formarán la aplicación.
### image

Imagen a usar.
### build

Construye una imagen desde un Dockerfile.
### ports

Mapeo de puertos host → contenedor.
### volumes

Persistencia de datos.
### environment

Variables de entorno.
### depends_on

Define dependencias entre servicios.
### Ejemplo completo:

```yaml
version: "3.9"

services:
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
    volumes:
      - dbdata:/var/lib/mysql

  web:
    build: ./app
    ports:
      - "8080:80"
    depends_on:
      - db

volumes:
  dbdata:
```

# Iniciar servicios con Docker Compose

Para levantar todos los servicios definidos:

```bash
docker compose up
```

### Opciones útiles:

#### Modo detach (en segundo plano)

```bash
docker compose up -d
```

#### Reconstruir imágenes

```bash
docker compose up --build
```

# Detener servicios

Detiene los contenedores sin eliminarlos:

```bash
docker compose stop
```

# Reiniciar servicios

Reinicia todos los servicios:

```bash
docker compose restart
```

Reiniciar un servicio específico:

```bash
docker compose restart web
```

# Eliminar contenedores, redes y volúmenes

### Eliminar contenedores creados por Compose:

```bash
docker compose down
```

### Eliminar contenedores + redes + volúmenes:

```bash
docker compose down -v
```

### Eliminar imágenes asociadas:

```bash
docker compose down --rmi all
```

# Política de reinicio (`restart`)

Permite definir cómo se comporta un servicio cuando falla o se detiene.
### Valores disponibles:

|Política|Descripción|
|---|---|
|`no`|No reinicia (por defecto)|
|`always`|Siempre reinicia|
|`on-failure`|Reinicia solo si el proceso falla|
|`unless-stopped`|Reinicia excepto si lo detienes manualmente|

### Ejemplo:

```yaml
services:
  web:
    image: nginx
    restart: always
```

# Leer logs de servicios

### Logs de todos los servicios:

```bash
docker compose logs
```

### Logs en tiempo real:

```bash
docker compose logs -f
```

### Logs de un servicio específico:

```bash
docker compose logs -f web
```

# Ejecutar comandos dentro de servicios

Similar a `docker exec`, pero usando el nombre del servicio:

```bash
docker compose exec web bash
```

Si el contenedor no tiene bash:

```bash
docker compose exec web sh
```

# Ver el estado de los servicios

```bash
docker compose ps
```

# Construir imágenes definidas en Compose

```bash
docker compose build
```

Reconstruir sin usar caché:

```bash
docker compose build --no-cache
```

# Redes en Docker Compose

Compose crea automáticamente una red para que los servicios se comuniquen por nombre.

Ejemplo:
- El servicio `web` puede conectarse a `db` usando `db:3306`.

Puedes definir redes personalizadas:

```yaml
networks:
  redinterna:
    driver: bridge
```

# Volúmenes en Docker Compose

Permiten persistir datos.

```yaml
volumes:
  dbdata:
```

Asignación en un servicio:

```yaml
services:
  db:
    volumes:
      - dbdata:/var/lib/mysql
```

# Variables de entorno en Compose

### Definidas en el YAML:

```yaml
environment:
  APP_ENV: production
```

### Usando archivo `.env`:

```code
APP_ENV=production
```

Y en el YAML:

```yaml
environment:
  - APP_ENV=${APP_ENV}
```

# Limpieza de recursos creados por Compose

Eliminar contenedores detenidos:

```bash
docker compose down
```

Eliminar volúmenes:

```bash
docker compose down -v
```

Eliminar imágenes:

```bash
docker compose down --rmi all
```

******
## También puedes ver:
[[Buenas prácticas en Docker]]
[[GitHub]]