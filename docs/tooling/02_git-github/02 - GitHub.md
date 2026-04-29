## 1. ¿Qué es GitHub?

GitHub es una plataforma de desarrollo colaborativo basada en la nube que utiliza Git como sistema de control de versiones. Funciona como un punto central para alojar, gestionar y sincronizar repositorios, además de facilitar flujos de trabajo profesionales y automatización.

---

### 1.1 Utilidad en el perfil técnico

Respaldo

- Almacenamiento seguro de documentación técnica y scripts
    

Sincronización

- Acceso a proyectos y notas desde múltiples entornos
    

Visibilidad

- Portafolio profesional para proyectos de Threat Hunting y automatización
    

Control de versiones

- Seguimiento detallado de cambios en cada proyecto
    

---

## 2. Conceptos fundamentales

Repositorio (repo)

- Contenedor del proyecto, puede ser público o privado
    

Branch (rama)

- Línea de desarrollo independiente; la principal es main
    

Commit

- Registro de cambios en el repositorio
    

Push

- Envío de cambios locales al repositorio remoto
    

Pull

- Descarga y sincronización de cambios desde el remoto
    

Pull Request (PR)

- Proceso de revisión antes de integrar cambios en la rama principal
    

---

## 3. Métodos de autenticación

GitHub ya no permite autenticación con contraseña para operaciones Git. Los métodos actuales son:

SSH

- Basado en claves criptográficas
    
- Recomendado para uso diario en WSL o entornos Linux
    

PAT (Personal Access Token)

- Token de acceso temporal
    
- Usado en automatizaciones o integraciones
    

GitHub CLI (gh)

- Herramienta oficial para gestión desde terminal
    

---

## 4. Interacción GitHub y WSL

WSL funciona como un entorno Linux independiente, lo que implica:

Configuración separada

- Definir user.name y user.email dentro de WSL
    

Aislamiento de credenciales

- No comparte sesiones ni credenciales con Windows
    

Autenticación propia

- Requiere configuración de SSH o credential helper en Linux
    

---

## 5. Operaciones básicas de sincronización

### 5.1 Flujo de subida de cambios

```bash
# Preparar archivos
git add .

# Registrar cambios
git commit -m "feat: actualización de documentación de docker"

# Enviar cambios
git push origin main
```

---

### 5.2 Descarga y clonación

Descargar cambios:  
git pull origin main

Clonar repositorio (SSH):  
git clone [git@github.com](mailto:git@github.com):beathunterzero/repo.git

---

## 6. Estructura de repositorio recomendada

Se recomienda mantener una estructura consistente y profesional, incluyendo:

- README.md con descripción clara del proyecto
    
- .gitignore adaptado al entorno y lenguaje utilizado
    

---

### Referencias externas

[https://docs.github.com/en/get-started](https://docs.github.com/en/get-started)  
[https://skills.github.com/](https://skills.github.com/)  
[https://www.freecodecamp.org/news/git-and-github-for-beginners/](https://www.freecodecamp.org/news/git-and-github-for-beginners/)

---

### Documentación relacionada

[[01 - Git]]  
[[03 - Token Personal (PAT)]]  
[[04 - Autenticación SSH con GitHub]]  
[[07 - Mover repositorios Git y GitHub de manera local]]  
[[08 - Visibilidad de repositorios]]