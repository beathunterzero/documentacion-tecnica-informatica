# ¿Qué es PowerShell?

PowerShell es una **shell de línea de comandos** y un **lenguaje de scripting** creado por Microsoft. Está diseñado para:
- Automatizar tareas
- Administrar sistemas
- Ejecutar comandos avanzados
- Trabajar con objetos (no solo texto)
- Integrarse con Windows, WSL, Docker, Git y más

PowerShell es mucho más potente que el CMD tradicional.

## Versiones de PowerShell

### Windows PowerShell

- Viene preinstalado en Windows
- Basado en .NET Framework
- Versión típica: 5.1
- Ya no recibe nuevas funciones

### PowerShell (Core)

- Instalado manualmente
- Basado en .NET 6+
- Multiplataforma (Windows, Linux, macOS)
- Versión actual: 7.x
- Es el recomendado para usuarios técnicos

## Comandos básicos

PowerShell usa **cmdlets**, que siguen la estructura:

Código

```syntax
Verbo-Sustantivo
```

Ejemplos:

```powershell
Get-Process
Get-Service
Set-ExecutionPolicy
Get-ChildItem
```

### Comandos esenciales:

|Comando|Descripción|
|---|---|
|`Get-Help`|Muestra ayuda|
|`Get-Command`|Lista comandos disponibles|
|`Get-ChildItem`|Lista archivos (equivalente a `ls`)|
|`Set-Location`|Cambia de directorio (equivalente a `cd`)|
|`Copy-Item`|Copia archivos|
|`Remove-Item`|Elimina archivos|

*****
## Puedes ver también:
[[Alias en Powershell]]
[[Alias permanente para Docker]]
