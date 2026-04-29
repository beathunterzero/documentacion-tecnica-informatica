## 1. Definición y Propósito

El cambio de nombre de un repositorio es una operación administrativa que permite mejorar el **branding**, la claridad técnica o la alineación con estándares profesionales sin afectar el historial de versiones.

Git está diseñado para soportar este cambio sin pérdida de información, siempre que se actualicen correctamente las referencias remotas.

---

## 2. Arquitectura del Cambio

El proceso involucra dos componentes independientes:

- **Repositorio Remoto (GitHub):** Identificado por su URL
    
- **Repositorio Local:** Identificado por la carpeta que contiene `.git`
    

> **Nota técnica:** Git no depende del nombre de la carpeta local, sino del contenido del directorio `.git`.

---

## 3. Cambio de Nombre en GitHub

### 3.1 Procedimiento

1. Acceder al repositorio en GitHub
    
2. Ir a **Settings**
    
3. En **Repository name**, modificar el nombre
    
4. Guardar cambios
    

### 3.2 Comportamiento del Sistema

- GitHub crea un **redirect automático** desde el nombre antiguo
    
- Las URLs antiguas siguen funcionando temporalmente
    
- No se pierde historial, issues ni ramas
    

---

## 4. Actualización del Repositorio Local

### 4.1 Verificación de remoto actual

```bash
git remote -v
```

---

### 4.2 Actualización de URL

#### HTTPS

```bash
git remote set-url origin https://github.com/usuario/nuevo-repo.git
```

#### SSH

```bash
git remote set-url origin git@github.com:usuario/nuevo-repo.git
```

---

### 4.3 Validación

```bash
git remote -v
git push
```

---

## 5. Cambio de Nombre del Directorio Local

### 5.1 Procedimiento (Windows PowerShell)

```powershell
Rename-Item "nombre-antiguo" "nuevo-nombre"
```

O mediante explorador de archivos.

---

### 5.2 Validación

```bash
cd nuevo-nombre
git status
```

Resultado esperado:

```text
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
```

---

## 6. Consideraciones Técnicas

|Elemento|Impacto|
|---|---|
|Historial de commits|No afectado|
|Branches|No afectados|
|Conexión remota|Debe actualizarse manualmente|
|Carpeta local|No afecta a Git|
|Redirección GitHub|Automática|

---

## 7. Riesgos y Errores Comunes

- No actualizar el `remote` → errores de push
    
- Uso de SSH sin clave configurada en Windows → `Permission denied (publickey)`
    
- Scripts con rutas absolutas → fallos tras renombrar carpeta
    
- Confusión entre entornos (WSL vs Windows)
    

---

## 8. Buenas Prácticas

- Mantener coherencia entre:
    
    - Nombre del repo
        
    - Nombre del directorio local
        
    - Branding en README
        
- Verificar siempre con:
    
    ```bash
    git status
    git remote -v
    ```
    
- Preferir:
    
    - **HTTPS en Windows**
        
    - **SSH en WSL/Linux**
        

---

### Referencias Externas

- [https://docs.github.com/en/repositories/creating-and-managing-repositories/renaming-a-repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/renaming-a-repository)
    
- [https://git-scm.com/docs/git-remote](https://git-scm.com/docs/git-remote)
    

---

### Documentación Relacionada

[[01 - Git]]
[[02 - GitHub]]
[[04 - Autenticación SSH con GitHub]]
[[07 - Mover repositorios Git y GitHub de manera local]]