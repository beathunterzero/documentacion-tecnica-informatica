## 1. Naturaleza Portátil de Git

Una de las ventajas fundamentales de Git es que el repositorio es **autónomo**. Toda la base de datos de historial, ramas y configuraciones reside dentro del directorio oculto `.git`. Debido a que Git utiliza rutas relativas internamente, es posible mover la carpeta física del proyecto a cualquier ubicación del disco sin romper el control de versiones.

## 2. Procedimiento de Migración Física

Para mover un repositorio de un directorio a otro (ej. de `/home/rhodyn/temp` a `/home/rhodyn/projects/carnada`), sigue estos pasos:

1. **Cierre de Procesos:** Asegúrate de que VS Code, terminales o cualquier servicio (como Docker o servidores locales) que utilicen archivos del repo estén cerrados para evitar bloqueos de archivos.
    
2. **Traslado:** Mueve la carpeta completa. Es **crítico** asegurar que el directorio `.git` se mueva junto con los archivos de trabajo.
    
3. **Acceso:** Abre una nueva terminal en la ubicación de destino.
    

## 3. Verificación de Integridad Técnica

Una vez movido el repositorio, es necesario validar que Git sigue rastreando los archivos y que la conexión con el servidor remoto (GitHub) permanece operativa.

### 3.1 Validación del Estado Local

Ejecuta el siguiente comando para confirmar que Git reconoce el contexto:

Bash

```bash
git status
```

- **Resultado esperado:** `On branch main / Your branch is up to date...`
    
- **Error común:** Si recibes `fatal: not a git repository`, significa que el directorio `.git` no se movió correctamente o estás en la ruta equivocada.
    

### 3.2 Validación de la Conexión Remota

El puntero hacia GitHub se almacena en el archivo de configuración local del repo, por lo que no se pierde al mover la carpeta.

Bash

```bash
git remote -v
```

- **Verificación:** Deberías ver las URLs de `fetch` y `push` (ya sean HTTPS o SSH) apuntando a tu repositorio en GitHub.
    

## 4. Test de Sincronización

Para garantizar que el flujo de trabajo está al 100%, realiza una prueba de comunicación bidireccional:

1. **Test de Descarga (Pull):**
    
    Bash
    
    ```bash
    git pull
    ```
    
2. **Test de Subida (Push):** Crea y envía un archivo efímero para validar permisos de escritura:
    
    Bash
    
    ```bash
    echo "test" > test_movimiento.txt
    git add test_movimiento.txt
    git commit -m "chore: validación de integridad tras migración local"
    git push
    ```
    
3. **Limpieza:** Una vez confirmado el éxito del `push`, elimina el archivo de prueba y sincroniza de nuevo.
    

## 5. Casos Especiales en WSL

- **Entre Sistemas de Archivos:** Evita mover repositorios desde el sistema de archivos de Linux (`/home/ubuntu/...`) al de Windows (`/mnt/c/...`) mediante el explorador de archivos, ya que los permisos de Linux (`chmod`) y los enlaces simbólicos se perderán.
    
- **Rutas en Scripts:** Si tienes scripts de automatización que apuntan a la ruta antigua, recuerda actualizarlos con el nuevo path.
    

---

### Referencias Externas

- [Git FAQ: Moving a git repository to another directory](https://git-scm.com/docs/gitfaq)
    
- [StackOverflow: Is it safe to move a git repository?](https://www.google.com/search?q=https://stackoverflow.com/questions/2192728/is-it-safe-to-move-a-git-repository-folder)
    
- [Microsoft: Working with Git in WSL2](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git)
    

### Documentación Relacionada

[[01 - Git]]
[[02 - GitHub]]
[[07 - Visibilidad de repositorios]]
[[08 - Buenas prácticas y manejos avanzado]]
