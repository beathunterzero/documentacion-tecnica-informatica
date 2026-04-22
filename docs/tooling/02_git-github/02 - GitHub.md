## 1. ¿Qué es GitHub?

**GitHub** es una plataforma de desarrollo colaborativo basada en la nube que utiliza el sistema de control de versiones **Git**. Actúa como el nodo central donde se alojan, gestionan y sincronizan los repositorios locales, permitiendo la integración de flujos de trabajo profesionales y automatizaciones.

### 1.1 Utilidad en el Perfil Técnico

- **Respaldo:** Almacenamiento seguro de documentación técnica y scripts de ciberseguridad.
    
- **Sincronización:** Acceso al Vault de Obsidian y proyectos desde múltiples estaciones de trabajo.
    
- **Visibilidad:** Portafolio profesional para mostrar laboratorios de **CTH** y automatizaciones.
    
- **Control de Versiones:** Historial detallado de la evolución de cada proyecto.
    

## 2. Conceptos Fundamentales

Para interactuar con GitHub de manera efectiva, es necesario dominar su terminología técnica:

- **Repositorio (Repo):** El contenedor del proyecto. Puede ser **Público** (visible para la comunidad) o **Privado** (acceso restringido).
    
- **Branch (Rama):** Línea de desarrollo independiente. La rama base por defecto es `main`.
    
- **Commit:** Registro histórico de cambios (Snapshot) en el repositorio local.
    
- **Push:** Operación de subida de commits locales hacia el servidor de GitHub.
    
- **Pull:** Operación de descarga y fusión de cambios del servidor hacia la máquina local.
    
- **Pull Request (PR):** Proceso de revisión de código antes de integrar cambios en la rama principal.
    

## 3. Métodos de Autenticación

GitHub ha deprecado el uso de contraseñas tradicionales para operaciones de Git. Los métodos vigentes son:

|**Método**|**Descripción**|**Caso de Uso**|
|---|---|---|
|**SSH**|Claves criptográficas (pública/privada).|**Recomendado para uso diario en WSL.**|
|**PAT (Token)**|Token de acceso personal (contraseña temporal).|Automatizaciones y scripts efímeros.|
|**GitHub CLI (`gh`)**|Herramienta de línea de comandos oficial.|Gestión avanzada de repositorios desde terminal.|

## 4. Interacción GitHub ↔ WSL (Puntos Críticos)

Es fundamental entender que **WSL es un entorno Linux independiente**. Esto implica:

1. **Configuración Propia:** Debes configurar `user.name` y `user.email` dentro de la terminal de Ubuntu.
    
2. **Aislamiento de Credenciales:** WSL no hereda los tokens ni las sesiones de Windows o de GitHub Desktop.
    
3. **Autenticación Manual:** Requiere la generación de claves SSH o configuración de un _credential helper_ específico dentro de Linux.
    

## 5. Operaciones Básicas de Sincronización

### 5.1 Flujo de Subida de Cambios

Bash

```bash
# 1. Preparar archivos
git add .

# 2. Registrar cambios con mensaje descriptivo
git commit -m "feat: actualización de documentación de docker"

# 3. Enviar al servidor
git push origin main
```

### 5.2 Descarga y Clonación

- **Descargar actualizaciones:** `git pull origin main`
    
- **Clonar por primera vez (SSH):** `git clone git@github.com:beathunterzero/repo.git`
    

## 6. Estructura de Repositorio Recomendada

Para mantener el profesionalismo en GitHub, se sigue la estructura ya documentada, priorizando siempre un `README.md` detallado y un `.gitignore` robusto.

---

### Referencias Externas

- [GitHub Documentation: Getting Started](https://docs.github.com/en/get-started)
    
- [GitHub Skills: Interactive Courses](https://skills.github.com/)
    
- [Git and GitHub: A Beginner's Guide](https://www.freecodecamp.org/news/git-and-github-for-beginners/)
    

### Documentación Relacionada

[[01 - Git]]
[[03 - Token Personal (PAT)]]
[[04 - Autenticación SSH con GitHub]]
[[06 - Mover repositorios Git y GitHub de manera local]]
[[07 - Visibilidad de repositorios]]