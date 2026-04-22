## 1. ¿Qué es Git?

**Git** es un sistema de control de versiones distribuido diseñado para gestionar el historial de cambios en archivos de texto, código fuente y documentación técnica. A diferencia de los sistemas centralizados, cada copia local de Git es un repositorio completo con historial y capacidad de seguimiento total.

### 1.1 Capacidades Principales

- **Trazabilidad:** Mantiene un registro exacto de quién, cuándo y por qué se realizó un cambio.
    
- **Sincronización:** Facilita el trabajo en múltiples máquinas y entornos (WSL, Windows, Servidores).
    
- **Seguridad:** Permite revertir cambios y recuperar versiones anteriores ante errores o incidentes.
    
- **Colaboración:** Es el estándar para equipos de Ciberseguridad, DevOps e Ingeniería.
    

## 2. Instalación en Diversos Sistemas Operativos

### 2.1 Microsoft Windows

1. **Descarga:** [git-scm.com/downloads](https://git-scm.com/downloads).
    
2. **Configuración Recomendada:**
    
    - Seleccionar **Visual Studio Code** como editor por defecto.
        
    - Habilitar la integración con **PowerShell** y **Git Bash**.
        
    - Mantener el nombre de la rama principal como `main`.
        

### 2.2 Distribuciones basadas en Debian (Ubuntu / Kali Linux)

Aunque muchas de estas distros ya incluyen Git (especialmente Kali), el procedimiento de instalación es el siguiente:

Bash

```bash
# Actualizar los índices de los repositorios
sudo apt update

# Instalar el paquete git
sudo apt install git -y

# Verificar versión e instalación
git --version
```

- **Ruta del binario:** `/usr/bin/git`.
    

### 2.3 Fedora (Gestor de paquetes DNF)

Bash

```bash
# Verificar actualizaciones
sudo dnf check-update

# Instalar git
sudo dnf install git -y

# Verificar versión
git --version
```

## 3. Configuración Inicial (Post-Instalación)

Una vez instalado el binario, es obligatorio configurar la identidad global. Esta información se adjuntará a cada commit que realices para asegurar la autoría.

Bash

```bash
# Configurar nombre de usuario/alias
git config --global user.name "beathunterzero"

# Configurar correo electrónico (debe coincidir con el de GitHub para vinculación)
git config --global user.email "rhodyn.ildefonso.1311@outlook.com"
```

### 3.1 Verificación de Configuración

Para auditar los valores establecidos y asegurar que no existan duplicados o errores de escritura:

Bash

```bash
git config --global --list
```

## 4. Notas de Implementación

- **Kali Linux:** Generalmente viene preinstalado debido a su enfoque en herramientas de auditoría que dependen de Git para clonar repositorios de exploits.
    
- **WSL:** Se recomienda instalar Git directamente dentro de la distribución de Linux (Ubuntu) para evitar conflictos de permisos con el binario de Windows.
    

---

### Referencias Externas

- [Git Documentation: Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
    
- [GitHub: First-time Git setup guide](https://docs.github.com/en/get-started/getting-started-with-git/set-up-git)
    
- [Linuxize: How to install Git on Ubuntu](https://linuxize.com/post/how-to-install-git-on-ubuntu-20-04/)
    

### Documentación Relacionada

[[02 - GitHub]]
[[03 - Token Personal (PAT)]]
[[04 - Autenticación SSH con GitHub]]
[[05 - Estructura de un repositorio]]
[[06 - Mover repositorios Git y GitHub de manera local]]
[[07 - Visibilidad de repositorios]]
[[08 - Buenas prácticas y manejos avanzado]]