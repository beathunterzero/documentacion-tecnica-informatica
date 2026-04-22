## 1. Fundamentos de Personalización en PowerShell

Para optimizar el flujo de trabajo con Docker en Windows, es fundamental dominar el uso de **Alias** y **Funciones**. Estas herramientas permiten reducir comandos complejos a instrucciones simples y automatizar tareas repetitivas de infraestructura.

### 1.1 Definición de Alias

Un **Alias** es un nombre abreviado que apunta a un cmdlet, función o ejecutable. Su propósito es mejorar la velocidad de interacción en la consola.

- **Sintaxis:** `Set-Alias nombre_corto "Ruta_o_Comando"`
    

### 1.2 Uso de Docker Desktop en Backend

Es posible iniciar el motor de Docker sin cargar la interfaz gráfica de usuario (GUI), lo cual ahorra recursos del sistema y acelera la disponibilidad del daemon para WSL o comandos externos.

- **Flag:** `--background`
    

## 2. Funciones de Automatización Avanzada

Las funciones permiten agrupar lógica, manejo de errores y documentación interna en un solo bloque ejecutable.

### 2.1 Inicialización de Servicio

Función diseñada para levantar el motor de contenedores de forma silenciosa.

PowerShell

```powershell
function Start-DockerDesktop {
    <#
    .SYNOPSIS
    Inicia Docker Desktop en segundo plano sin abrir la interfaz gráfica.
    #>
    Write-Host "Iniciando Docker Desktop..." -ForegroundColor Cyan
    Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe" --background
}
```

### 2.2 Detención y Limpieza Total

A diferencia de un cierre convencional, esta función asegura que no queden procesos residuales ni instancias de WSL bloqueando recursos.

PowerShell

```powershell
function Stop-DockerDesktop {
    <#
    .SYNOPSIS
    Detiene Docker Desktop, su daemon y todas las instancias WSL asociadas.
    #>
    Write-Host "Deteniendo Docker completamente..." -ForegroundColor Yellow

    # Detener servicio principal de Windows
    Stop-Service -Name "com.docker.service" -ErrorAction SilentlyContinue

    # Finalizar procesos relacionados en memoria
    Get-Process | Where-Object { $_.Name -like "*Docker*" } | 
        Stop-Process -Force -ErrorAction SilentlyContinue

    # Terminar distribuciones WSL específicas de Docker
    wsl --terminate docker-desktop >$null 2>$null
    wsl --terminate docker-desktop-data >$null 2>$null

    Write-Host "Docker ha sido detenido y limpiado correctamente." -ForegroundColor Green
}
```

## 3. Orquestación de Entorno (Docker + WSL)

Integración de servicios para levantar un entorno de desarrollo completo con una sola instrucción.

PowerShell

```powershell
function dockerup {
    <#
    .SYNOPSIS
    Inicia Docker Desktop y abre automáticamente la distribución Ubuntu en WSL.
    #>
    Write-Host "Levantando entorno Docker + WSL..." -ForegroundColor Cyan
    dockerstart # Llama a la función de inicio previamente definida
    Start-Sleep -Seconds 3
    wsl -d Ubuntu
}
```

## 4. Configuración del Perfil de PowerShell

Para que estos comandos estén disponibles en cada sesión, deben incluirse en el archivo de perfil (`$PROFILE`).

### 4.1 Definición de Alias y Entorno

PowerShell

```powershell
# Configuración de Variables de Entorno
$env:EDITOR = "notepad"

# Mapeo de Alias
Set-Alias -Name dockerstart -Value Start-DockerDesktop
Set-Alias -Name dockerstop -Value Stop-DockerDesktop
```

## 5. Ventajas de la Automatización Propuesta

- **Eficiencia Térmica/Recursos:** Al usar el modo `--background`, se evita el consumo innecesario de la UI.
    
- **Limpieza de Procesos:** El uso de `wsl --terminate` previene errores de corrupción de datos en contenedores al cerrar la sesión.
    
- **Documentación Técnica:** El uso de `.SYNOPSIS` permite que el comando `Get-Help` reconozca las funciones personalizadas.
    
- **Modularidad:** Estructura organizada que permite escalar la automatización a otros servicios (ej. Kubernetes local).
    

## 6. Implementación: Script Completo para $PROFILE

Copia este bloque en tu archivo de perfil de PowerShell para disponer de los comandos en cada nueva sesión:

PowerShell

```powershell
# ==============================================================================
# CONFIGURACIÓN DE ENTORNO
# ==============================================================================
$env:EDITOR = "notepad"

# ==============================================================================
# FUNCIONES (Automatización avanzada)
# ==============================================================================

function Start-DockerDesktop {
    <#
    .SYNOPSIS
    Inicia Docker Desktop en segundo plano sin abrir la interfaz gráfica.
    #>
    Write-Host "Iniciando Docker Desktop..." -ForegroundColor Cyan
    Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe" --background
}

function Stop-DockerDesktop {
    <#
    .SYNOPSIS
    Detiene Docker Desktop, su daemon y todas las instancias WSL asociadas.
    #>
    Write-Host "Deteniendo Docker completamente..." -ForegroundColor Yellow

    # Detener servicio principal
    Stop-Service -Name "com.docker.service" -ErrorAction SilentlyContinue

    # Matar procesos relacionados
    Get-Process | Where-Object { $_.Name -like "*Docker*" } |
        Stop-Process -Force -ErrorAction SilentlyContinue

    # Terminar distribuciones WSL de Docker
    wsl --terminate docker-desktop >$null 2>$null
    wsl --terminate docker-desktop-data >$null 2>$null

    Write-Host "Docker ha sido detenido y limpiado correctamente." -ForegroundColor Green
}

function dockerup {
    <#
    .SYNOPSIS
    Inicia Docker Desktop y abre automáticamente Ubuntu WSL.
    #>
    Write-Host "Levantando entorno Docker + WSL..." -ForegroundColor Cyan
    dockerstart
    Start-Sleep -Seconds 3
    wsl -d Ubuntu
}

# ==============================================================================
# ALIAS (Accesos directos)
# ==============================================================================

Set-Alias -Name dockerstart -Value Start-DockerDesktop
Set-Alias -Name dockerstop -Value Stop-DockerDesktop
```

---

### Referencias Externas

- [Docker Desktop for Windows: Command Line Interface](https://docs.docker.com/desktop/settings/windows/)
    
- [Microsoft Learn: About Aliases in PowerShell](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_aliases)
    
- [WSL Basic Commands: Terminating Distributions](https://learn.microsoft.com/en-us/windows/wsl/basic-commands)
    

### Documentación Relacionada

[[01 - Conceptos fundamentales de Docker]]