## 1. Instalación Base en Ubuntu/Debian

En sistemas Linux bajo WSL, Python suele venir preinstalado, pero frecuentemente carece de los módulos necesarios para la gestión de entornos virtuales y el gestor de paquetes `pip`. Es imperativo estandarizar la versión de ejecución para evitar discrepancias entre el desarrollo y el despliegue de herramientas de CTH.

### 1.1 Preparación del Sistema

Bash

```bash
# Actualizar repositorios e instalar paquetes core
sudo apt update
sudo apt install python3 python3-pip

# Instalar versión específica y entorno completo
sudo apt install python3.12-venv python3.12-full
```

- **Nota técnica:** El paquete `python3.12-full` garantiza que se incluyan librerías estándar extendidas que a veces se omiten en instalaciones minimalistas de WSL.
    

## 2. Gestión de Entornos Virtuales (Workflow Estándar)

Para mantener la integridad de tu sistema operativo Linux en WSL, la creación de un entorno aislado por proyecto no es opcional, es una **regla obligatoria**.

### 2.1 Creación y Activación

Bash

```bash
# 1. Crear entorno con versión explícita
python3.12 -m venv .venv

# 2. Activar entorno
source .venv/bin/activate

# 3. Actualizar el gestor de paquetes dentro del entorno
python -m pip install --upgrade pip
```

### 2.2 Método de Recuperación (Fallback)

Si la instalación de `pip` falla o el entorno se crea corrupto, utiliza `ensurepip` para forzar la disponibilidad del gestor:

Bash

```bash
rm -rf .venv
python3.12 -m venv .venv --without-pip
source .venv/bin/activate
python3 -m ensurepip --upgrade
python -m pip install --upgrade pip
```

## 3. Comandos de Verificación y Cierre

Para asegurar que el contexto de ejecución es el correcto, valida siempre las rutas de los binarios:

- **Verificación:** `python --version` (Debe retornar 3.12.x).
    
- **Finalización:** Para salir del entorno y volver al Python del sistema, ejecuta `deactivate`.
    

## 4. Reglas de Oro para Proyectos Python en WSL

| **Regla**             | **Descripción**                                                                                 |
| --------------------- | ----------------------------------------------------------------------------------------------- |
| **Versión Explícita** | Usar siempre `python3.12` en lugar de `python3` para evitar cambios por actualizaciones del SO. |
| **Estandarización**   | El entorno virtual **siempre** debe llamarse `.venv` y residir en la raíz del proyecto.         |
| **Aislamiento**       | Prohibido instalar librerías con `pip` fuera de un entorno activado.                            |
| **Seguridad**         | El directorio `.venv` debe estar listado en el `.gitignore` global.                             |
| **Ciclo de Vida**     | Revisión y regeneración anual de entornos programada para **Abril 2027**.                       |

---

### Referencias Externas

- [Ubuntu Documentation: Python in Ubuntu](https://www.google.com/search?q=https://ubuntu.com/tutorials/install-python%231-overview)
    
- [Python.org: venv — Creation of virtual environments](https://docs.python.org/3/library/venv.html)
    

### Documentación Relacionada

[[02 - Estructura de proyectos en Python]]
[[03 - Gestión de dependencias en Python]]
[[04 - Automatización y scripting en Python]]
[[05 - Buenas prácticas en Python]]
[[01 - Powershell]]
[[01 - WSL]]