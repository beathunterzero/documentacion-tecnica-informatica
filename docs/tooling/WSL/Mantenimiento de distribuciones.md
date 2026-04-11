# Introducción

Cada distribución Linux dentro de WSL tiene su propio gestor de paquetes:

| Distro          | Gestor     | Comando base  |
| --------------- | ---------- | ------------- |
| Ubuntu / Debian | **APT**    | `sudo apt`    |
| Fedora          | **DNF**    | `sudo dnf`    |
| openSUSE        | **Zypper** | `sudo zypper` |

El mantenimiento básico consiste en:
- Actualizar listas de paquetes
- Instalar actualizaciones
- Limpiar paquetes innecesarios
- Liberar espacio

## Mantenimiento en Ubuntu / Debian / Kali (APT)

APT es el gestor más común en WSL.

### Actualizar lista de paquetes

```bash
sudo apt update
```

Esto **no actualiza nada**, solo refresca la lista de versiones disponibles.

### Actualizar paquetes instalados

```bash
sudo apt upgrade -y
```

Actualiza paquetes sin eliminar ni reemplazar dependencias.

### Actualización completa del sistema

```bash
sudo apt full-upgrade -y
```

Permite:
- Reemplazar paquetes
- Eliminar dependencias obsoletas
- Instalar nuevas dependencias necesarias

Es la forma recomendada para mantener el sistema al día.

### Eliminar paquetes innecesarios

```bash
sudo apt autoremove -y
```

Elimina dependencias que ya no se usan.

### Limpiar caché de paquetes

```bash
sudo apt clean
sudo apt auto-clean
```

- `clean` → borra **toda** la caché
- `auto-clean` → borra solo paquetes obsoletos

### Instalar herramientas de desarrollo

```bash
sudo apt install build-essential git curl wget -y
```

#### ¿Qué instala?

- `gcc`, `g++`, `make`
- librerías de desarrollo
- `git`, `curl`, `wget`

Es el equivalente al “kit básico de desarrollo”.

## Mantenimiento en Fedora (DNF)

Fedora usa **DNF**, un gestor moderno y muy eficiente.

### Actualizar lista de paquetes

```bash
sudo dnf check-update
```

Equivalente a `apt update`.

### Actualizar paquetes

```bash
sudo dnf upgrade --refresh -y
```

`--refresh` fuerza a DNF a obtener metadatos nuevos.

### Eliminar paquetes innecesarios

```bash
sudo dnf autoremove -y
```

### Limpiar caché

```bash
sudo dnf clean all
```

### Instalar herramientas de desarrollo

```bash
sudo dnf groupinstall "Development Tools" -y
sudo dnf install git curl wget -y
```

#### ¿Qué instala?

- `gcc`, `g++`, `make`
- herramientas de compilación
- utilidades de red

El _groupinstall_ es el equivalente a `build-essential`.

## Mantenimiento en openSUSE (Zypper)

openSUSE usa **Zypper**, uno de los gestores más potentes.

### Actualizar lista de paquetes

```bash
sudo zypper refresh
```

###  Actualizar paquetes

```bash
sudo zypper update -y
```

### Actualización completa del sistema

```bash
sudo zypper dist-upgrade -y
```

Equivalente a `apt full-upgrade`.

### Eliminar paquetes innecesarios

```bash
sudo zypper remove --clean-deps paquete
```

### Para limpieza automática:

```bash
sudo zypper packages --unneeded
```

### Limpiar caché

```bash
sudo zypper clean --all
```

### Instalar herramientas de desarrollo

```bash
sudo zypper install -t pattern devel_basis
sudo zypper install git curl wget
```

#### ¿Qué instala?

- compiladores
- herramientas de desarrollo
- librerías esenciales
- utilidades de red

`devel_basis` es el equivalente directo a `build-essential`.

# Mantenimiento general de WSL

Independiente de la distro, WSL tiene comandos propios.

### Apagar WSL (reiniciar kernel)

```powershell
wsl --shutdown
```

Útil cuando:
- Algo falla
- Docker se rompe
- WSL consume RAM
- Cambias configuraciones

### Actualizar WSL

```powershell
wsl --update
```

Actualiza:
- Kernel Linux
- Integración con Windows
- Mejoras de rendimiento

### Ver estado de WSL

```powershell
wsl --status
```

# Buenas prácticas de mantenimiento

- Actualiza tu distro al menos 1 vez por semana
- Usa `full-upgrade` o `dist-upgrade` para evitar conflictos
- Limpia la caché cada cierto tiempo
- Si una distro se rompe → elimínala y crea una nueva
- Considera usar **export/import** para tener plantillas limpias
- Mantén tus proyectos en `/home`, no en `/mnt/c`

*****
## También puedes ver:
[[Conceptos fundamentales de Docker]]
