## 1. Introducción al Mantenimiento de Paquetes

Cada distribución dentro de WSL opera de forma independiente y posee su propio gestor de paquetes. Mantener estas instancias actualizadas no solo garantiza la compatibilidad con Docker y Python, sino que cierra vulnerabilidades de seguridad en el kernel de usuario.

### 1.1 Comparativa de Gestores

|**Distribución**|**Gestor**|**Comando Base**|
|---|---|---|
|**Ubuntu / Debian / Kali**|**APT**|`sudo apt`|
|**Fedora**|**DNF**|`sudo dnf`|
|**openSUSE**|**Zypper**|`sudo zypper`|

## 2. Gestión en Entornos Debian-Based (APT)

Es el estándar más común en WSL. Para un mantenimiento profesional, el orden de los factores sí altera el producto.

- **Sincronización:** `sudo apt update` (Refresca el índice de versiones).
    
- **Actualización Recomendada:** `sudo apt full-upgrade -y`. A diferencia de `upgrade`, este comando gestiona cambios de dependencias y elimina paquetes obsoletos.
    
- **Higiene del Sistema:**
    
    Bash
    
    ```bash
    sudo apt autoremove -y  # Elimina huérfanos
    sudo apt clean          # Borra toda la caché de descargas
    ```
    
- **Kit de Desarrollo (Build-Essential):** Vital para compilar herramientas de seguridad:
    
    `sudo apt install build-essential git curl wget -y`
    

## 3. Gestión en Fedora (DNF)

DNF es conocido por su resolución inteligente de dependencias y su manejo de metadatos.

- **Refresco Forzado:** `sudo dnf upgrade --refresh -y`. Obliga a DNF a ignorar la caché local de metadatos para buscar actualizaciones reales.
    
- **Limpieza Total:** `sudo dnf clean all`.
    
- **Herramientas de Compilación:**
    
    `sudo dnf groupinstall "Development Tools" -y`
    

## 4. Gestión en openSUSE (Zypper)

Zypper ofrece una granularidad muy alta, ideal para entornos de laboratorio estables.

- **Sincronización:** `sudo zypper refresh`.
    
- **Migración de Distribución:** `sudo zypper dist-upgrade -y` (Equivalente a full-upgrade).
    
- **Patrones de Desarrollo:**
    
    `sudo zypper install -t pattern devel_basis`
    

## 5. Mantenimiento del Motor WSL (Host Windows)

Existen situaciones donde el mantenimiento no es dentro de la Linux, sino en el orquestador de Windows.

### 5.1 Gestión de Recursos y Errores

- **El "Hard Reset":** `wsl --shutdown`. Es la solución definitiva cuando el consumo de RAM de WSL se dispara o cuando Docker Desktop pierde la comunicación con el kernel.
    
- **Actualización del Micro-Kernel:** `wsl --update`. Microsoft publica parches de seguridad y mejoras de rendimiento para el kernel Linux de forma independiente a Windows Update.
    
- **Auditoría de Estado:** `wsl --status`. Muestra la versión del kernel y la distribución predeterminada.
    

## 6. Reglas de Oro de Higiene Técnica

1. **Frecuencia:** Ejecutar el ciclo de actualización al menos **una vez por semana**.
    
2. **Localización de Datos:** Prohibido trabajar proyectos pesados en `/mnt/c/`. Los archivos de laboratorios y código deben residir en el sistema de archivos de Linux (`/home/rhodyn/`) para evitar latencia de I/O.
    
3. **Resiliencia:** Si una distribución de laboratorio se corrompe por una instalación fallida, no pierdas tiempo reparándola; usa `wsl --unregister` y despliega una nueva.
    
4. **Plantillas:** Una vez que una distro tenga su "Kit de Desarrollo" instalado, expórtala como `.tar` para tener un punto de restauración limpio.
    

---

### Referencias Externas

- [Microsoft Learn: Advanced settings in WSL](https://learn.microsoft.com/en-us/windows/wsl/wsl-config)
    
- [Debian Wiki: APT Commands Guide](https://wiki.debian.org/Apt)
    
- [Fedora Docs: Managing Software with DNF](https://www.google.com/search?q=https://docs.fedoraproject.org/en-US/fedora/rawhide/system-administrators-guide/package-management/DNF/)
    

### Documentación Relacionada

[[01 - WSL]]
