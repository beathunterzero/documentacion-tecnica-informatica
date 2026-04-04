# Alias en PowerShell

PowerShell permite crear alias para simplificar comandos.

**Ejemplos:**

```powershell
Set-Alias ll Get-ChildItem
Set-Alias gs git status
```

Pero estos alias **solo duran la sesión actual**, a menos que se agreguen al perfil.

## ¿Qué es el PowerShell Profile?

El _PowerShell Profile_ es un archivo que se ejecuta automáticamente cada vez que abres PowerShell.

Sirve para:

- Alias permanentes
- Funciones personalizadas
- Variables
- Scripts de inicio

Es tu archivo de configuración personal.
### Ver si existe:

```powershell
Test-Path $PROFILE
```

### Crear si no existe:

```powershell
New-Item -Path $PROFILE -ItemType File -Force
```

### Abrirlo:

```powershell
notepad $PROFILE
```

*****
## Puedes ver también:
[[Alias permanente para Docker]]
