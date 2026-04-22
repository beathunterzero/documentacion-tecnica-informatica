## 1. ¿Qué es PowerShell?

**PowerShell** es una solución de automatización de tareas multiplataforma que consiste en un shell de línea de comandos, un lenguaje de scripting y un marco de gestión de configuración. A diferencia de los shells tradicionales (como Bash o CMD) que procesan texto, PowerShell se basa en el framework **.NET** y procesa **objetos**, lo que permite una manipulación de datos mucho más robusta y estructurada.

### 1.1 Diferencias Críticas de Versión

Es fundamental distinguir entre las dos ramas principales de PowerShell para asegurar la compatibilidad de scripts y módulos:

|**Característica**|**Windows PowerShell**|**PowerShell (Core)**|
|---|---|---|
|**Versión**|5.1|7.x (Actual)|
|**Base**|.NET Framework|.NET 6+ (Open Source)|
|**Plataforma**|Solo Windows|Windows, Linux, macOS|
|**Estado**|Mantenimiento (Legacy)|Desarrollo Activo (Recomendado)|
|**Uso Ideal**|Administración de SO antiguo|**DevOps, CTH, Cloud y Multiplataforma**|

## 2. Anatomía de los Cmdlets

Los comandos nativos de PowerShell se denominan **cmdlets**. Su sintaxis está diseñada para ser intuitiva y predecible, utilizando una estructura de **Verbo-Sustantivo**.

- **Verbos comunes:** `Get` (recuperar), `Set` (establecer), `New` (crear), `Remove` (eliminar), `Start`/`Stop`.
    
- **Sustantivos:** Indican el recurso sobre el que se actúa (`Process`, `Service`, `Item`, `Content`).
    

## 3. Comandos Esenciales de Navegación y Gestión

Para un flujo de trabajo eficiente en la terminal, estos son los cmdlets fundamentales y sus alias (compatibles con Linux):

|**Cmdlet**|**Alias**|**Propósito Técnico**|
|---|---|---|
|`Get-ChildItem`|`ls` / `dir`|Lista archivos y directorios con sus propiedades.|
|`Set-Location`|`cd`|Cambia el contexto del directorio actual.|
|`Copy-Item`|`cp`|Duplica archivos o carpetas.|
|`Remove-Item`|`rm` / `del`|Elimina elementos (usar `-Recurse` para carpetas).|
|`Move-Item`|`mv`|Mueve o renombra archivos/directorios.|

## 4. Herramientas de Auto-Documentación

PowerShell incluye un sistema de ayuda integrado que es vital cuando se trabaja con módulos desconocidos:

- **`Get-Help <Comando>`:** Muestra la sintaxis, descripción y parámetros de un comando. (Ej: `Get-Help Get-Process -Examples`).
    
- **`Get-Command`:** Permite buscar comandos disponibles en el sistema. Útil para descubrir funciones de módulos recién instalados (como AWS o Azure CLI).
    
- **`Get-Member` (GM):** El comando más potente para analistas; muestra todas las propiedades y métodos de un objeto.
    
    - _Ejemplo:_ `Get-Process | Get-Member`
        

## 5. El Pipeline (`|`)

En PowerShell, el pipeline no solo pasa texto, pasa el **objeto completo**. Esto permite filtrar y seleccionar datos sin necesidad de usar herramientas complejas de procesamiento de texto como `sed` o `awk`.

- _Ejemplo de CTH:_ `Get-Process | Where-Object { $_.CPU -gt 100 }` (Filtra procesos con alto consumo de CPU).
    

---

### Referencias Externas

- [Microsoft Learn: PowerShell Documentation](https://learn.microsoft.com/en-us/powershell/scripting/overview)
    
- [PowerShell GitHub Repository](https://github.com/PowerShell/PowerShell)
    

### Documentación Relacionada

[[02 - Alias en Powershell]]
[[03 - AWS CLI]]
[[04 - Azure CLI]]
[[01 - WSL]]
[[01 - Git]]