# ¿Qué es Git?

Git es un sistema de control de versiones distribuido que permite:
- Llevar historial de cambios
- Trabajar en múltiples máquinas
- Sincronizar proyectos
- Recuperar versiones anteriores
- Colaborar con otros

Es una herramienta esencial para Developers, DevOps, Security Specialist, Sysadmins y documentación técnica.

## Instalación de Git

### Windows

Descargar desde: https://git-scm.com/downloads
Durante la instalación:
- Mantener opciones por defecto
- Elegir VS Code como editor (opcional pero recomendado)
- Aceptar integración con PowerShell

Verificar instalación:

```powershell
git --version
```

### **Debian / Ubuntu / Kali**

Estas distribuciones usan `apt` como gestor de paquetes, por lo que el proceso es idéntico.
#### Actualizar repositorios

```bash
sudo apt update
```

#### Instalar Git

```bash
sudo apt install git -y
```

#### Verificar instalación

```bash
git --version
```

#### Ubicación típica del binario

```code
/usr/bin/git
```

#### Notas importantes

- Kali Linux ya suele traer Git preinstalado
- En Ubuntu Server puede no venir por defecto
- En Debian minimal tampoco viene instalado
### **Fedora**

Fedora usa `dnf` como gestor de paquetes.
#### Actualizar repositorios

```bash
sudo dnf check-update
```

#### Instalar Git

```bash
sudo dnf install git -y
```

#### Verificar instalación

```bash
git --version
```

#### Ubicación típica del binario

```code
/usr/bin/git
```

## **Configuración inicial después de instalar Git**

Independientemente del sistema operativo:

```bash
git config --global user.name "beathunterzero"
git config --global user.email "rhodyn.ildefonso.1311@outlook.com"
```

Verificar:

```bash
git config --global --list
```

*****
## Puedes ver también:
