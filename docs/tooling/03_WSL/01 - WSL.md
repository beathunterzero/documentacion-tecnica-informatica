## 1. ¿Qué es WSL?

**WSL** es una capa de compatibilidad desarrollada por Microsoft que permite ejecutar un entorno de Linux completo directamente en Windows. A diferencia de las máquinas virtuales tradicionales (como VMware), WSL utiliza una arquitectura de micro-VM ligera que se integra profundamente con el kernel de Windows, eliminando la necesidad de gestionar recursos pesados o configurar _dual-boot_.

### 1.1 El Mejor de los Dos Mundos

- **Productividad de Windows:** Mantienes tus herramientas de diseño, comunicación y oficina.
    
- **Potencia de Linux:** Acceso nativo a herramientas de CTH, compiladores de Python, gestión de contenedores Docker y flujos de trabajo basados en terminal (Bash/Zsh).
    

## 2. Selección de Versión: WSL 1 vs. WSL 2

Para entornos de ingeniería y ciberseguridad, **WSL 2** es el estándar mandatorio.

- **WSL 2:** Utiliza un kernel de Linux real, ofrece un rendimiento de archivos significativamente superior y es el único que soporta **Docker Engine** de forma nativa.
    
- **Verificación:** Ejecuta en PowerShell: `wsl --version` o `wsl -l -v`.
    

## 3. Despliegue e Instalación

El proceso de instalación moderno se ha simplificado a un solo comando en una terminal de Windows con privilegios de administrador.

PowerShell

```powershell
# Instalación automática (incluye kernel y Ubuntu por defecto)
wsl --install

# Explorar otras distribuciones disponibles (Kali, Debian, etc.)
wsl --list --online

# Instalación de una distro específica
wsl --install -d Ubuntu-22.04
```

## 4. Configuración Inicial en la Distribución

Al iniciar una nueva instancia, se debe establecer la identidad del usuario Linux (independiente del usuario de Windows) y preparar el sistema para el trabajo técnico.

Bash

```bash
# Sincronización de repositorios y parcheo de seguridad
sudo apt update && sudo apt upgrade -y

# Instalación del kit de supervivencia (compiladores y red)
sudo apt install build-essential git curl wget -y
```

## 5. Interoperabilidad (Cruce de Archivos)

WSL rompe las barreras entre sistemas de archivos, facilitando el análisis de artefactos forenses que residen en Windows desde la potencia de Linux.

|**Dirección**|**Método**|**Ruta / Comando**|
|---|---|---|
|**Linux → Windows**|Puntos de montaje|`/mnt/c/Users/`|
|**Windows → Linux**|Ruta de red|`\\wsl$`|
|**Exploración Visual**|Desde WSL|`explorer.exe .`|

## 6. Operaciones de Ciclo de Vida y Backup

Como medida de resiliencia en laboratorios de CTH, es vital saber cómo respaldar y destruir instancias sin afectar el sistema host.

- **Auditoría:** `wsl --status` (Muestra versión de kernel y estado de red).
    
- **Mantenimiento:** `wsl --update` (Actualiza el kernel a la última versión de seguridad).
    
- **Backup (Exportar):** `wsl --export Ubuntu D:\Backups\ubuntu_cth.tar`
    
- **Restauración (Importar):** `wsl --import Ubuntu_Lab D:\WSL\Lab D:\Backups\ubuntu_cth.tar`
    
- **Destrucción (Unregister):** `wsl --unregister <NombreDistro>` (Borra permanentemente la distro y sus archivos).
    

---

### Referencias Externas

- [Microsoft Learn: WSL Official Documentation](https://learn.microsoft.com/en-us/windows/wsl/)
    
- [Microsoft Learn: Comparing WSL 1 and WSL 2](https://learn.microsoft.com/en-us/windows/wsl/compare-versions)
    

### Documentación Relacionada

[[02 - Mantenimiento de distribuciones]]
