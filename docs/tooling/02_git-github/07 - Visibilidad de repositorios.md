## 1. Conceptos de Visibilidad en GitHub

La visibilidad determina quién tiene permiso para visualizar, clonar y contribuir en un repositorio. GitHub ofrece flexibilidad total, permitiendo alternar entre estados según la etapa de desarrollo o la sensibilidad de la información.

### 1.1 Repositorio Público

- **Alcance:** Visible para cualquier usuario en internet.
    
- **Uso Ideal:** Proyectos Open Source, documentación técnica compartida, portafolios y laboratorios de aprendizaje.
    
- **Interacción:** Cualquier persona puede clonar el repositorio, pero solo los colaboradores autorizados pueden realizar `push`.
    

### 1.2 Repositorio Privado

- **Alcance:** Solo tú y los colaboradores que invites explícitamente pueden verlo.
    
- **Uso Ideal:** Proyectos con datos sensibles, borradores de investigación, configuraciones de infraestructura interna o repositorios personales de estudio.
    
- **Interacción:** Requiere autenticación (SSH o PAT) incluso para la operación de `clone`.
    

## 2. Gestión de Visibilidad (Procedimiento)

Ambos cambios se gestionan desde la misma interfaz dentro del repositorio web en GitHub.

### 2.1 Flujo de Cambio de Estado

1. Navegar al repositorio en la web de GitHub.
    
2. Entrar en la pestaña **Settings** (Configuración).
    
3. Deslizarse hasta el final de la página a la sección **Danger Zone** (Zona de Peligro).
    
4. Hacer clic en el botón **Change visibility**.
    

## 3. Consideraciones Críticas al Cambiar Visibilidad

### 3.1 De Público a Privado

- **Forks:** Los forks (bifurcaciones) públicos creados por otros usuarios **permanecerán públicos**. No puedes controlar las copias que otros ya realizaron mientras el repo era visible.
    
- **Estrellas y Watchers:** Se pierden las estadísticas de "Stars" y "Watchers" al privatizar.
    
- **GitHub Pages:** Si tenías un sitio web desplegado mediante GitHub Pages, este se desactivará automáticamente.
    

### 3.2 De Privado a Público

- **Exposición Inmediata:** Todo el historial de commits (incluyendo errores antiguos o archivos borrados) se vuelve visible. **Precaución:** Asegúrate de que no haya secretos (API Keys, .env) en commits anteriores.
    
- **Colaboración:** Permite que la comunidad realice _Forks_ y _Pull Requests_.
    

## 4. Matriz de Decisión

|**Necesidad**|**Visibilidad Recomendada**|
|---|---|
|**Documentación Profesional / CV**|Público|
|**Scripts de Pentesting Propios**|Privado|
|**Proyectos Educativos Terminados**|Público|
|**Archivos de Configuración de Red**|Privado|

---

### Referencias Externas

- [GitHub Docs: Setting repository visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)
    
- [GitHub: About forks and visibility](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks)
    
- [Security Best Practices: Removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
    

### Documentación Relacionada

[[01 - Git]]
[[02 - GitHub]]
[[08 - Buenas prácticas y manejos avanzado]]