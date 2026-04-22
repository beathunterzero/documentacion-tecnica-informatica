## 1. Gestión Inteligente con `.gitignore`

El archivo `.gitignore` es la primera línea de defensa para mantener un repositorio limpio, profesional y seguro. Su función es evitar que archivos temporales, binarios o sensibles se integren al historial de versiones.

### 1.1 Estándares de Exclusión

Existen categorías de archivos que, por norma general, **nunca** deben ser rastreados:

|**Categoría**|**Ejemplos**|**Razón**|
|---|---|---|
|**Dependencias**|`.venv/`, `node_modules/`|Pesados y regenerables mediante archivos de requisitos.|
|**Secretos**|`*.env`, `*.pem`, `*.key`|Riesgo crítico de seguridad (fuga de credenciales).|
|**Compilados**|`__pycache__/`, `*.pyc`, `*.exe`|Específicos de la arquitectura/máquina local.|
|**Sistema**|`.DS_Store`, `Thumbs.db`|Ruido visual generado por el SO (macOS/Windows).|

### 1.2 Configuración Global (User Level)

Para no repetir las mismas exclusiones en cada proyecto (como los archivos del sistema), se recomienda un `.gitignore` global en tu entorno WSL:

Bash

```bash
# Definir el archivo global
git config --global core.excludesfile ~/.gitignore_global

# Editar y agregar reglas comunes (ej. Thumbs.db, *.log)
nano ~/.gitignore_global
```

## 2. Reglas de Oro del Control de Versiones

1. **Atentado a la seguridad:** Nunca subir `.env`, claves SSH o tokens de API.
    
2. **Higiene del repositorio:** El `.gitignore` debe crearse **antes** del primer commit.
    
3. **Flujo preventivo:** Siempre ejecutar `git status` antes de un commit y `git pull` antes de un `push` para evitar conflictos.
    
4. **Atómica y Descriptiva:** Commits pequeños y con mensajes que expliquen el "porqué", no solo el "qué".
    

## 3. Operaciones de Limpieza y Control de Rastreo

### 3.1 Desvincular archivos ya subidos

Si accidentalmente subiste un archivo que debería estar ignorado (ej. un log), usa el siguiente comando para borrarlo del repositorio pero **mantenerlo** en tu disco local:

Bash

```bash
git rm --cached <archivo>
# Luego, asegúrate de añadirlo al .gitignore
```

### 3.2 Ignorar cambios en archivos rastreados (Skip-Worktree)

Útil para archivos de configuración local que Git ya conoce pero que no quieres que tus cambios personales se suban al repo:

- **Ignorar localmente:** `git update-index --skip-worktree <archivo>`
    
- **Revertir ignorado:** `git update-index --no-skip-worktree <archivo>`
    

## 4. Comandos Avanzados de Recuperación y Visualización

### 4.1 Visualización del Historial

Para ver el flujo de ramas y commits de forma compacta y visual:

Bash

```bash
git log --oneline --graph --decorate --all
```

### 4.2 Deshacer Cambios (Resets y Reverts)

- **Revert (Seguro):** Crea un nuevo commit que deshace los cambios de uno anterior. Ideal para repositorios compartidos.
    
    `git revert <hash_del_commit>`
    
- **Reset Soft:** Deshace el commit pero mantiene tus cambios en el _staging area_ (listos para volver a commitear).
    
    `git reset --soft HEAD~1`
    
- **Reset Hard (Peligroso):** Borra permanentemente el commit y todos los cambios realizados en los archivos.
    
    `git reset --hard HEAD~1`
    

### 4.3 El "Bolsillo" Temporal: Git Stash

Cuando necesitas cambiar de rama o hacer un pull urgente pero no quieres perder (ni commitear) tu trabajo actual:

- **Guardar:** `git stash` (mueve tus cambios a una pila temporal).
    
- **Recuperar:** `git stash pop` (trae los cambios de vuelta y los borra de la pila).
    

---

### Referencias Externas

- [Git Documentation: Undoing Things](https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things)
    
- [GitHub: A collection of useful .gitignore templates](https://github.com/github/gitignore)
    
- [Atlassian Git Tutorial: Git Reset vs Revert](https://www.atlassian.com/git/tutorials/undoing-changes/git-reset)
    

### Documentación Relacionada

[[01 - Git]]
[[02 - GitHub]]