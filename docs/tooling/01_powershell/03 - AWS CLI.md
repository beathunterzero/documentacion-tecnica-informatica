## 1. Definición y Propósito

La **AWS CLI** es una herramienta de código abierto que permite interactuar con los servicios de Amazon Web Services mediante comandos en el shell. Para un perfil orientado a **Cloud Security** e **Incident Response**, la CLI es vital para realizar auditorías rápidas, extraer evidencias de logs y gestionar infraestructura sin depender de la consola web.

## 2. Instalación y Verificación (Windows)

En Windows, el método más eficiente y moderno es utilizar el gestor de paquetes nativo `winget`.

PowerShell

```powershell
# 1. Instalación mediante Windows Package Manager
winget install Amazon.AWSCLI

# 2. Reiniciar la terminal y verificar versión
aws --version
```

## 3. Configuración de Identidad

Antes de ejecutar comandos, es necesario vincular la CLI con una identidad de IAM (_Identity and Access Management_).

### 3.1 Inicialización de Credenciales

Ejecuta el comando interactivo:

Bash

```bash
aws configure
```

El sistema solicitará cuatro parámetros críticos:

1. **AWS Access Key ID:** El identificador de tu clave.
    
2. **AWS Secret Access Key:** La clave secreta (solo se muestra al crear el usuario en AWS).
    
3. **Default region name:** Ej. `us-east-1`.
    
4. **Default output format:** Ej. `json` o `table`.
    

### 3.2 Almacenamiento Local de Secretos

Las credenciales se almacenan de forma local en archivos de texto plano. Es fundamental no compartir el acceso a esta carpeta:

- **Ubicación:** `~/.aws/credentials`
    
- **Comando de lectura (PowerShell):** `Get-Content ~/.aws/credentials`
    

## 4. Comandos Esenciales de Gestión

Operaciones rápidas para auditoría y administración de recursos comunes:

|**Servicio**|**Comando**|**Propósito Técnico**|
|---|---|---|
|**S3**|`aws s3 ls`|Listado de buckets para auditoría de almacenamiento.|
|**EC2**|`aws ec2 describe-instances`|Inventario de servidores y estado de ejecución.|
|**IAM**|`aws iam list-users`|Revisión de identidades activas en el tenant.|
|**S3**|`aws s3 cp <file> s3://<bucket>/`|Exfiltración controlada o respaldo de evidencias.|

### 4.1 Control de Salida (`--output`)

La CLI permite moldear la respuesta según la necesidad del analista:

- **`json`:** Ideal para automatización y parsing con herramientas como `jq`.
    
- **`table`:** Recomendado para lectura humana y reportes rápidos.
    
- **`text`:** Útil para procesamiento simple en scripts de Bash o PowerShell.
    

## 5. Automatización con Alias en PowerShell

Para optimizar el flujo de trabajo en incidentes, integra estos alias en tu `$PROFILE`:

PowerShell

```powershell
Set-Alias awsec2 "aws ec2 describe-instances --output table"
Set-Alias awss3 "aws s3 ls"
Set-Alias awsiam "aws iam list-users --output table"
```

## 6. Buenas Prácticas de Seguridad (Cloud Hardening)

- **Principio de Menor Privilegio:** Evitar el uso de `AdministratorAccess` para tareas diarias; usar políticas granulares.
    
- **Higiene de Secretos:** **Nunca** incluir `Access Keys` en archivos de configuración que se suban a GitHub.
    
- **Autenticación Fuerte:** Habilitar **MFA** (Multi-Factor Authentication) incluso para el acceso vía CLI.
    
- **Rotación:** Cambiar las claves de acceso periódicamente (cada 90 días) para mitigar el impacto de una posible fuga.
    

---

### Referencias Externas

- [AWS CLI User Guide: Configuring the CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
    
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
    

### Documentación Relacionada

[[01 - Powershell]]
[[04 - Azure CLI]]