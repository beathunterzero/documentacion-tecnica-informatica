## 1. Gestión inteligente con .gitignore

El archivo .gitignore permite definir qué archivos no deben ser rastreados por Git. Su correcta configuración evita la inclusión de datos innecesarios o sensibles en el repositorio.

---

### 1.1 Estándares de exclusión

Dependencias

- .venv/
    
- node_modules/  
    Motivo: Son pesadas y pueden regenerarse
    

Secretos

- *.env
    
- *.pem
    
- *.key  
    Motivo: Riesgo de exposición de credenciales
    

Archivos compilados

- **pycache**/
    
- *.pyc
    
- *.exe  
    Motivo: Dependientes del entorno local
    

Archivos del sistema

- .DS_Store
    
- Thumbs.db  
    Motivo: Generados automáticamente por el sistema operativo
    

---

### 1.2 Configuración global

Permite aplicar reglas comunes a todos los repositorios.

```bash
git config --global core.excludesfile ~/.gitignore_global
nano ~/.gitignore_global
```

---

## 2. Reglas de control de versiones

Seguridad

- No subir archivos con credenciales o claves
    

Inicialización

- Crear .gitignore antes del primer commit
    

Validación

- Ejecutar git status antes de cada commit
    
- Ejecutar git pull antes de push
    

Commits

- Mantener commits pequeños
    
- Usar mensajes descriptivos orientados al propósito
    

---

## 3. Limpieza y control de rastreo

---

### 3.1 Eliminar archivos del repositorio sin borrarlos localmente

```bash
git rm --cached <archivo>
```

Luego agregar el archivo al .gitignore.

---

### 3.2 Ignorar cambios en archivos rastreados

```bash
git update-index --skip-worktree <archivo>
git update-index --no-skip-worktree <archivo>
```

---

## 4. Comandos avanzados

---

### 4.1 Visualización de historial

```bash
git log --oneline --graph --decorate --all
```

---

### 4.2 Deshacer cambios

Revert

- Crea un commit que revierte cambios
    

```bash
git revert <hash_del_commit>
```

Reset soft

- Mantiene cambios en staging
    

```bash
git reset --soft HEAD~1
```

Reset hard

- Elimina cambios de forma permanente
    

```bash
git reset --hard HEAD~1
```

---

### 4.3 Uso de git stash

```bash
git stash
git stash pop
```

Permite guardar cambios temporalmente sin hacer commit.

---

### Referencias externas

[https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things](https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things)  
[https://github.com/github/gitignore](https://github.com/github/gitignore)  
[https://www.atlassian.com/git/tutorials/undoing-changes/git-reset](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)

---

### Documentación relacionada

[[01 - Git]]  
[[02 - GitHub]]