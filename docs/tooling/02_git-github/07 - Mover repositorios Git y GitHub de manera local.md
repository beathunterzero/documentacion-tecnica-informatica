## 1. Naturaleza portátil de Git

Git es un sistema distribuido, lo que implica que cada repositorio contiene toda la información necesaria para su funcionamiento dentro del directorio oculto .git.

Debido a que Git trabaja con rutas relativas, es posible mover un repositorio dentro del sistema de archivos sin afectar su integridad, siempre que se conserve la estructura completa.

---

## 2. Procedimiento de migración física

Para mover un repositorio entre directorios:

1. Cierre de procesos
    

- Asegurar que no existan editores, terminales o servicios utilizando archivos del repositorio
    

2. Traslado
    

- Mover la carpeta completa del proyecto
    
- Verificar que el directorio .git se incluya en el traslado
    

3. Acceso
    

- Abrir una nueva terminal en la ruta destino
    

---

## 3. Verificación de integridad técnica

Después del movimiento, es necesario validar que el repositorio sigue operativo.

---

### 3.1 Validación del estado local

```bash
git status
```

Resultado esperado

- Confirmación de rama activa y sincronización
    

Error común

- fatal: not a git repository
    
- Indica ausencia del directorio .git o ubicación incorrecta
    

---

### 3.2 Validación de la conexión remota

```bash
git remote -v
```

Resultado esperado

- URLs de fetch y push configuradas correctamente
    

La configuración remota se mantiene al mover el repositorio.

---

## 4. Test de sincronización

Se recomienda validar el flujo completo de comunicación.

---

### 4.1 Test de descarga

```bash
git pull
```

---

### 4.2 Test de subida

```bash
echo "test" > test_movimiento.txt
git add test_movimiento.txt
git commit -m "chore: validación de integridad tras migración local"
git push
```

---

### 4.3 Limpieza

Eliminar el archivo de prueba y sincronizar nuevamente.

---

## 5. Casos especiales en WSL

Sistemas de archivos

- Evitar mover repositorios entre /home y /mnt/c mediante herramientas gráficas
    
- Puede afectar permisos y enlaces simbólicos
    

Rutas en scripts

- Actualizar paths si existen referencias a la ubicación anterior
    

---

### Referencias externas

[https://git-scm.com/docs/gitfaq](https://git-scm.com/docs/gitfaq)  
[https://stackoverflow.com/questions/2192728/is-it-safe-to-move-a-git-repository-folder](https://stackoverflow.com/questions/2192728/is-it-safe-to-move-a-git-repository-folder)  
[https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-git)

---

### Documentación relacionada

[[01 - Git]]  
[[02 - GitHub]]  
[[08 - Visibilidad de repositorios]]  
[[09 - Buenas prácticas y manejos avanzado]]