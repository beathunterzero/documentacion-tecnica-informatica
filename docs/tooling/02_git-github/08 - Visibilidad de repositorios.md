## 1. Conceptos de visibilidad en GitHub

La visibilidad define quién puede acceder a un repositorio, incluyendo su capacidad para visualizar, clonar y colaborar.

GitHub permite alternar entre estados de visibilidad según el contexto del proyecto o la sensibilidad de la información.

---

### 1.1 Repositorio público

Alcance

- Visible para cualquier usuario en internet
    

Uso recomendado

- Proyectos open source
    
- Documentación técnica
    
- Portafolio profesional
    
- Laboratorios de aprendizaje
    

Interacción

- Cualquier usuario puede clonar
    
- Solo colaboradores autorizados pueden realizar cambios
    

---

### 1.2 Repositorio privado

Alcance

- Acceso restringido a propietarios y colaboradores autorizados
    

Uso recomendado

- Información sensible
    
- Configuraciones internas
    
- Proyectos en desarrollo
    
- Entornos de laboratorio no publicados
    

Interacción

- Requiere autenticación para cualquier operación
    

---

## 2. Gestión de visibilidad

El cambio de visibilidad se realiza desde la interfaz web del repositorio.

---

### 2.1 Flujo de cambio

1. Acceder al repositorio en GitHub
    
2. Ir a Settings
    
3. Desplazarse a la sección Danger Zone
    
4. Seleccionar Change visibility
    

---

## 3. Consideraciones al cambiar visibilidad

---

### 3.1 De público a privado

Forks

- Las copias existentes permanecen públicas
    

Estadísticas

- Se pierden estrellas y watchers
    

GitHub Pages

- Se desactiva si estaba configurado
    

---

### 3.2 De privado a público

Exposición de historial

- Todo el historial de commits se vuelve visible
    

Riesgo

- Posible exposición de credenciales o datos sensibles
    

Colaboración

- Permite forks y pull requests
    

---

## 4. Criterios de decisión

Documentación profesional / portafolio

- Público
    

Proyectos educativos finalizados

- Público
    

Scripts o herramientas sensibles

- Privado
    

Configuraciones internas

- Privado
    

---

### Referencias externas

[https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/managing-repository-settings/setting-repository-visibility)  
[https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks)  
[https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)

---

### Documentación relacionada

[[01 - Git]]  
[[02 - GitHub]]  
[[09 - Buenas prácticas y manejos avanzado]]