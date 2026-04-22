## 1. Gestión de Alias (Accesos Directos)

En PowerShell, un **Alias** es un nombre alternativo que apunta a un cmdlet, función, archivo ejecutable o script. Su objetivo principal es aumentar la velocidad operativa en la terminal reduciendo comandos extensos a abreviaturas simples.

- **Comando de asignación:** `Set-Alias`
    
- **Sintaxis:** `Set-Alias <nombre_alias> <comando_original>`
    

### 1.1 Limitación de la Sesión Actual

Por defecto, los alias creados directamente en la consola son **volátiles**. Al cerrar la ventana de PowerShell, las definiciones se pierden. Para que un alias sea persistente, debe integrarse en el archivo de configuración del perfil.

## 2. El Perfil de PowerShell ($PROFILE)

El **PowerShell Profile** es un script que se ejecuta automáticamente al iniciar cada sesión de la terminal. Es el equivalente al archivo `.bashrc` o `.zshrc` en sistemas Linux/Unix.

### 2.1 Utilidad del Perfil

- **Alias Permanentes:** Definición de comandos cortos para Git, Docker o navegación.
    
- **Funciones Personalizadas:** Automatización de tareas complejas (como tu orquestador de Docker).
    
- **Variables de Entorno:** Configuración de paths de editores o herramientas.
    
- **Estética y Prompt:** Personalización del aspecto visual de la consola.
    

## 3. Implementación y Configuración

### 3.1 Localización y Creación

PowerShell almacena la ruta del perfil en la variable automática `$PROFILE`. Para gestionarlo, sigue este flujo:

PowerShell

```powershell
# 1. Verificar si el archivo ya existe físicamente
Test-Path $PROFILE

# 2. Crear el archivo (y las carpetas necesarias) si no existe
New-Item -Path $PROFILE -ItemType File -Force

# 3. Editar el perfil para agregar configuraciones
notepad $PROFILE
```

### 3.2 Ejemplo de Configuración en el Perfil

Una vez abierto el archivo en Notepad, puedes agregar líneas como estas para que estén siempre disponibles:

PowerShell

```powershell
# Alias para navegación rápida
Set-Alias -Name ll -Value Get-ChildItem
Set-Alias -Name .. -Value "cd .."

# Alias para herramientas externas
Set-Alias -Name gs -Value "git status"
Set-Alias -Name d -Value docker
```

## 4. Políticas de Ejecución

Al intentar cargar el perfil por primera vez, es posible que PowerShell bloquee el script por motivos de seguridad.

- **Error común:** `...cannot be loaded because running scripts is disabled on this system.`
    
- **Solución (Ejecutar como Administrador):**
    
    `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`
    

---

### Referencias Externas

- [Microsoft Learn: About Profiles](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles)
    
- [Microsoft Learn: Set-Alias Cmdlet](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/set-alias)
    

### Documentación Relacionada

[[01 - Powershell]]
[[04 - Automatización para Docker en PowerShell]]