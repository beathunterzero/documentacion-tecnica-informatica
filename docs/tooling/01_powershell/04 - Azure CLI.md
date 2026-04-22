## 1. Definición y Propósito

La **Azure CLI** es una herramienta de comandos multiplataforma para conectarse a Azure y ejecutar comandos administrativos sobre los recursos de Azure. Para un perfil de **Incident Response** y **Cloud Security**, esta herramienta es indispensable para auditar configuraciones de red, gestionar identidades y automatizar la respuesta ante alertas en entornos de nube de Microsoft.

## 2. Instalación y Verificación (Windows)

Al igual que con el resto de herramientas en tu entorno, el uso de `winget` garantiza una instalación limpia y fácil de actualizar.

PowerShell

```powershell
# 1. Instalación mediante gestor de paquetes
winget install Microsoft.AzureCLI

# 2. Verificar versión y componentes instalados
az --version
```

## 3. Flujo de Autenticación y Contexto

Azure CLI utiliza un flujo de autenticación basado en navegador por defecto, lo que facilita el inicio de sesión seguro.

- **Login:** Abre una ventana del navegador para autenticarte.
    
    `az login`
    
- **Logout:** Elimina el token de acceso local. **Recomendado en entornos compartidos.**
    
    `az logout`
    

### 3.1 Gestión de Suscripciones

En entornos profesionales con múltiples suscripciones, es crítico validar en qué contexto se están ejecutando los comandos para evitar cambios accidentales en producción.

PowerShell

```powershell
# Listado de suscripciones disponibles
az account list --output table

# Seleccionar suscripción de trabajo (por Nombre o ID)
az account set --subscription "<ID-de-Suscripción>"
```

## 4. Comandos de Gestión y Auditoría

Operaciones de consulta rápida sobre la infraestructura desplegada:

|**Recurso**|**Comando**|**Utilidad Técnica**|
|---|---|---|
|**VM**|`az vm list`|Inventariado de activos y estado de máquinas virtuales.|
|**Resources**|`az resource list`|Vista global de todos los recursos del tenant.|
|**Groups**|`az group list`|Auditoría de contenedores lógicos de recursos.|
|**Creación**|`az group create`|Despliegue rápido de grupos (ej. para laboratorios aislados).|

### 4.1 Formatos de Salida (`--output`)

Azure CLI ofrece flexibilidad para la visualización o procesamiento de datos:

- **`table`:** Formato preferido para inspección visual rápida.
    
- **`json`:** Estándar para integración con otros scripts.
    
- **`yaml`:** Útil para configuraciones complejas y legibilidad de archivos.
    

## 5. Optimización mediante Alias en PowerShell

Integra estos alias en tu `$PROFILE` para acelerar la respuesta ante incidentes:

PowerShell

```powershell
Set-Alias azlogin "az login"
Set-Alias azvm "az vm list --output table"
Set-Alias azres "az resource list --output table"
```

## 6. Buenas Prácticas y Seguridad

- **Precisión del Contexto:** Siempre verifica la suscripción activa con `az account show` antes de ejecutar comandos de modificación o borrado.
    
- **Higiene de Sesión:** Ejecuta `az logout` al finalizar tareas en máquinas que no sean de tu propiedad exclusiva.
    
- **Manejo de Secretos:** No incluyas comandos que contengan contraseñas o secretos en el historial de PowerShell (puedes usar variables interactivas).
    
- **Uso de Tablas:** Utiliza `--output table` por defecto para minimizar errores de interpretación visual al leer grandes volúmenes de datos.
    

---

### Referencias Externas

- [Microsoft Learn: Get started with Azure CLI](https://learn.microsoft.com/en-us/cli/azure/get-started-with-azure-cli)
    
- [Azure CLI Command Reference](https://learn.microsoft.com/en-us/cli/azure/reference-index)
    

### Documentación Relacionada

[[01 - Powershell]]
[[03 - AWS CLI]]