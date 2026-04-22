## 1. Objetivo del Entorno

Configurar una arquitectura donde se utilice **Docker Engine** exclusivamente desde la terminal de **Ubuntu WSL2**, minimizando el consumo de recursos en Windows al prescindir de la interfaz gráfica (UI) y procesos secundarios innecesarios.

## 2. Instalación de Docker Desktop (Windows)

Durante el proceso de instalación, es crítico seleccionar los parámetros adecuados para garantizar la eficiencia técnica y evitar el "bloatware" de procesos automáticos.

### 2.1 Configuración de Parámetros de Instalación

|**Opción**|**Estado**|**Justificación Técnica**|
|---|---|---|
|**Use WSL 2 instead of Hyper-V**|✔️|Requerido para el motor de kernel Linux nativo.|
|**Enable WSL Integration**|✔️|Permite que los binarios de Docker sean accesibles desde Ubuntu.|
|**Add docker.internal names to hosts**|✔️|Facilita el networking entre contenedores y el host.|
|**Use containerd for pulling images**|✔️|Implementa el runtime estándar más eficiente.|
|**Start Docker Desktop when you sign in**|❌|**Crucial:** Evita el consumo de RAM/CPU al arrancar Windows.|
|**Open Docker Dashboard when starts**|❌|Evita la carga de la interfaz gráfica.|
|**Enable Scout / SBOM**|❌|Desactiva servicios de escaneo adicionales para ahorrar recursos.|

## 3. Configuración de Integración con WSL

Una vez instalado, se debe habilitar el puente de comunicación entre el motor de Windows y la distribución de Linux:

1. Abrir Docker Desktop temporalmente.
    
2. Navegar a **Settings → Resources → WSL Integration**.
    
3. Activar el switch para la distribución **Ubuntu**.
    
4. Habilitar **"Enable integration with my default WSL distro"**.
    
5. **Cerrar completamente Docker Desktop.**
    

## 4. Verificación en Ubuntu WSL

Desde la terminal de Ubuntu, ejecutar los siguientes comandos para validar la disponibilidad del servicio:

Bash

```bash
# Verificar versiones de cliente y servidor
docker --version

# Consultar estado del daemon y recursos
docker info

# Prueba de ejecución (Hello World)
docker run --rm hello-world
```

## 5. Troubleshooting: Resolución de Errores Comunes

Si el entorno presenta fallos de conexión o permisos, aplicar las siguientes correcciones:

### 5.1 El daemon no responde o no inicia

Si Docker se queda bloqueado o no es detectado tras un reinicio:

PowerShell

```powershell
# Desde PowerShell como Administrador o usando tus alias:
dockerstop
wsl --shutdown
dockerstart --background
```

### 5.2 Error: "Cannot connect to the Docker daemon"

Suele deberse a que el usuario actual no pertenece al grupo de privilegios de Docker:

Bash

```bash
sudo usermod -aG docker $USER
# Importante: Cerrar y volver a abrir la sesión de WSL para aplicar cambios.
```

### 5.3 Error de permisos en el Socket (`/var/run/docker.sock`)

Si recibes errores de "Permission Denied" al ejecutar comandos sin `sudo`:

Bash

```bash
sudo chmod 666 /var/run/docker.sock
```

---

### Referencias Externas

- [Docker Desktop WSL 2 backend - Official Docs](https://docs.docker.com/desktop/wsl/)
    
- [Microsoft Learn: Best practices for setting up Docker on WSL 2](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers)
    
- [Ubuntu Wiki: Docker on WSL2](https://www.google.com/search?q=https://ubuntu.com/tutorials/install-and-configure-docker-inside-wsl-2)
    

### Documentación Relacionada

[[01 - Conceptos fundamentales de Docker]]
[[01 - Git]]
[[01 - Powershell]]
[[01 - WSL]]