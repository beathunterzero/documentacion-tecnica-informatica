# Alias permanente para Docker

Agregar dentro del perfil:

```powershell
Set-Alias dockerstart "C:\Program Files\Docker\Docker\Docker Desktop.exe"
```

Esto permite iniciar Docker sin abrir la UI:

```powershell
dockerstart --background
```

## Funciones personalizadas

PowerShell permite crear funciones para automatizar tareas complejas.
### Ejemplo: función `dockerup`

```txt
function dockerup {
    dockerstart --background
    Start-Sleep -Seconds 3
    wsl -d Ubuntu
}
```
### ¿Qué hace?

1. Inicia Docker Desktop en modo backend
2. Espera 3 segundos para que el daemon se active
3. Abre Ubuntu WSL automáticamente

Es una automatización elegante para tu flujo de trabajo.
## Estructura recomendada para tu perfil

Ejemplo profesional:

```txt
# ============================
# Alias
# ============================
Set-Alias dockerstart "C:\Program Files\Docker\Docker\Docker Desktop.exe"

# ============================
# Funciones personalizadas
# ============================
function dockerup {
    dockerstart --background
    Start-Sleep -Seconds 3
    wsl -d Ubuntu
}

# ============================
# Variables personalizadas
# ============================
$env:EDITOR = "notepad"
```

*****
# Puedes ver también:

