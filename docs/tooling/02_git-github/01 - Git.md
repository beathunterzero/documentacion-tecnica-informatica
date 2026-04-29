## 1. ¿Qué es Git?

Git es un sistema de control de versiones distribuido diseñado para gestionar el historial de cambios en archivos de texto, código fuente y documentación técnica.

A diferencia de los sistemas centralizados, cada copia local de un repositorio Git contiene el historial completo, lo que permite trabajar de forma independiente y sincronizar cambios posteriormente.

---

### 1.1 Capacidades principales

Trazabilidad

- Registra quién, cuándo y por qué se realizó un cambio
    

Sincronización

- Permite trabajar en múltiples entornos (WSL, Windows, servidores)
    

Recuperación

- Facilita revertir cambios y restaurar versiones anteriores
    

Colaboración

- Estándar en equipos de ciberseguridad, DevOps e ingeniería
    

---

## 2. Instalación en distintos sistemas operativos

### 2.1 Microsoft Windows

Descarga:  
[https://git-scm.com/downloads](https://git-scm.com/downloads)

Configuración recomendada:

- Seleccionar Visual Studio Code como editor por defecto
    
- Habilitar integración con PowerShell y Git Bash
    
- Mantener la rama principal como main
    

---

### 2.2 Distribuciones basadas en Debian (Ubuntu / Kali Linux)

```bash
# Actualizar repositorios
sudo apt update

# Instalar Git
sudo apt install git -y

# Verificar instalación
git --version
```

Ruta del binario:  
/usr/bin/git

---

### 2.3 Fedora (DNF)

```bash
# Verificar actualizaciones
sudo dnf check-update

# Instalar Git
sudo dnf install git -y

# Verificar instalación
git --version
```

---

## 3. Configuración inicial

Después de la instalación, es necesario definir la identidad global del usuario. Esta información se asocia a cada commit.

```bash
# Configurar nombre de usuario
git config --global user.name "beathunterzero"

# Configurar correo electrónico
git config --global user.email "rhodyn.ildefonso.1311@outlook.com"
```

---

### 3.1 Verificación de configuración

```bash
git config --global --list
```

Permite validar los valores configurados y detectar errores o duplicados.

---

## 4. Notas de implementación

Kali Linux

- Generalmente incluye Git preinstalado debido a su uso en herramientas de seguridad
    

WSL

- Se recomienda instalar Git dentro de la distribución Linux para evitar conflictos de permisos con el binario de Windows
    

---

### Referencias externas

[https://git-scm.com/book/en/v2/Getting-Started-Installing-Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)  
[https://docs.github.com/en/get-started/getting-started-with-git/set-up-git](https://docs.github.com/en/get-started/getting-started-with-git/set-up-git)  
[https://linuxize.com/post/how-to-install-git-on-ubuntu-20-04/](https://linuxize.com/post/how-to-install-git-on-ubuntu-20-04/)

---

### Documentación relacionada

[[02 - GitHub]]  
[[03 - Token Personal (PAT)]]  
[[04 - Autenticación SSH con GitHub]]  
[[05 - Estructura de un repositorio]]  
[[07 - Mover repositorios Git y GitHub de manera local]]  
[[08 - Visibilidad de repositorios]]  
[[09 - Buenas prácticas y manejos avanzado]]