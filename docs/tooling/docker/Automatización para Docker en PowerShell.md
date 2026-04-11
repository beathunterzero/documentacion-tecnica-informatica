# ¿Qué es un alias en PowerShell?

Un **alias** es un nombre corto que apunta a un comando más largo.

Ejemplo:

```powershell
Set-Alias dockerstart "C:\Program Files\Docker\Docker\Docker Desktop.exe"
```

Esto permite ejecutar:

```powershell
dockerstart
```

En lugar de escribir la ruta completa.

# Iniciar Docker Desktop sin abrir la UI

Docker Desktop puede iniciarse en modo backend (sin interfaz gráfica):

```powershell
dockerstart --background
```

Esto es útil cuando solo necesitas el daemon para usar Docker desde WSL o PowerShell.

# Funciones personalizadas en PowerShell

PowerShell permite crear funciones que combinan varios comandos en una sola acción.

Ejemplo:

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

### ¿Qué hace esta función?

- **Muestra un mensaje en pantalla** Escribe _"Iniciando Docker Desktop..."_ en color cyan para indicar que el proceso está comenzando.
- **Ejecuta Docker Desktop** Llama al ejecutable principal de Docker Desktop ubicado en: `C:\Program Files\Docker\Docker\Docker Desktop.exe`
- **Lo inicia en segundo plano** Gracias a `--background`, Docker arranca **sin abrir la interfaz gráfica**, solo el daemon.

Es una automatización elegante para iniciar tu entorno de trabajo completo con un solo comando.

# PERFIL DOCKER POWERFUL (versión mejorada)


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

## ¿Qué mejoras tiene esta versión?

- Usa `--background` correctamente
- Limpia procesos de forma segura
- Termina WSL sin mostrar errores
- Tiene documentación interna (`.SYNOPSIS`)
- Está organizado por secciones
- Alias claros y consistentes

*****
## Puedes ver también:
[[Powershell]]