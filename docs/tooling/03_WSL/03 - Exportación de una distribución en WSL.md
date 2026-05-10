## 1. Introducción a la exportación de distribuciones

WSL permite exportar una distribución Linux instalada en Windows hacia un archivo `.tar`.

Este proceso genera un respaldo completo del entorno, incluyendo sistema de archivos, usuario, configuraciones, paquetes instalados y proyectos locales.

Exportar una distribución permite:

- Crear un respaldo antes de una actualización importante
    
- Migrar una distribución a otra máquina o disco
    
- Conservar una versión estable del entorno
    
- Restaurar el sistema si una actualización o prueba falla
    
- Mantener una base reutilizable para laboratorios o desarrollo
    

---

## 2. Verificar distribuciones instaladas

Antes de exportar, se debe identificar el nombre exacto de la distribución.

Desde PowerShell:

```powershell
wsl -l -v
```

Ejemplo:

```text
  NAME              STATE           VERSION
* Ubuntu-24.04      Running         2
```

El campo `NAME` indica el nombre que se debe usar en el comando de exportación.

El campo `VERSION` indica si la distribución usa WSL1 o WSL2.

```text
VERSION 1 = WSL1
VERSION 2 = WSL2
```

Para entornos modernos de desarrollo, laboratorios, Docker y herramientas técnicas, lo recomendable es usar WSL2.

---

## 3. Apagar WSL antes de exportar

Antes de realizar la exportación, se recomienda detener todas las instancias de WSL.

```powershell
wsl --shutdown
```

Esto ayuda a evitar inconsistencias en el sistema de archivos durante la creación del respaldo.

---

## 4. Exportar una distribución WSL

La sintaxis general es:

```powershell
wsl --export <NombreDistribucion> <RutaDestinoArchivoTar>
```

Ejemplo usando Ubuntu 24.04:

```powershell
wsl --export Ubuntu-24.04 D:\Backups\ubuntu-24.04-wsl-backup.tar
```

Flujo recomendado:

```powershell
wsl -l -v
wsl --shutdown
wsl --export Ubuntu-24.04 D:\Backups\ubuntu-24.04-wsl-backup.tar
```

En este ejemplo:

```text
Ubuntu-24.04
```

corresponde al nombre de la distribución.

```text
D:\Backups\ubuntu-24.04-wsl-backup.tar
```

corresponde a la ruta donde se guardará el respaldo.

---

## 5. Validar el archivo exportado

Después de exportar, se debe verificar que el archivo `.tar` fue creado correctamente.

```powershell
dir D:\Backups\
```

También se puede revisar información del archivo:

```powershell
Get-Item D:\Backups\ubuntu-24.04-wsl-backup.tar
```

Si el archivo existe y tiene un tamaño coherente con el uso de la distribución, la exportación fue completada correctamente.

---

## 6. Restaurar una distribución desde un archivo TAR

Para restaurar una distribución exportada se utiliza `wsl --import`.

La sintaxis general es:

```powershell
wsl --import <NuevoNombreDistribucion> <RutaInstalacion> <RutaArchivoTar> --version 2
```

Ejemplo:

```powershell
wsl --import Ubuntu-24.04-Restore D:\WSL\Ubuntu-24.04-Restore D:\Backups\ubuntu-24.04-wsl-backup.tar --version 2
```

En este ejemplo:

```text
Ubuntu-24.04-Restore
```

será el nombre de la distribución restaurada.

```text
D:\WSL\Ubuntu-24.04-Restore
```

será la ubicación donde se almacenará la distribución restaurada.

```text
D:\Backups\ubuntu-24.04-wsl-backup.tar
```

será el archivo `.tar` utilizado para la restauración.

Para iniciar la distribución restaurada:

```powershell
wsl -d Ubuntu-24.04-Restore
```

---

## 7. Consideración sobre el usuario por defecto

Después de importar una distribución desde un archivo `.tar`, es posible que WSL inicie como `root`.

Para cambiar al usuario normal:

```bash
su - nombre_usuario
```

Ejemplo:

```bash
su - rhodyn
```

También se puede configurar el usuario por defecto en `/etc/wsl.conf`.

```bash
sudo nano /etc/wsl.conf
```

Contenido:

```ini
[user]
default=rhodyn
```

Luego se debe reiniciar WSL desde PowerShell:

```powershell
wsl --shutdown
```

Al iniciar nuevamente la distribución, WSL debería entrar con el usuario configurado.

---

## 8. Casos de uso

La exportación de una distribución WSL es útil antes de:

- Actualizar Ubuntu a una nueva versión LTS
    
- Modificar paquetes críticos del sistema
    
- Limpiar dependencias
    
- Probar configuraciones nuevas
    
- Migrar el entorno a otra máquina
    
- Reinstalar Windows
    
- Mover una distribución a otro disco
    
- Realizar pruebas agresivas en laboratorios
    

---

## 9. Diferencia entre Git y wsl --export

Git protege el proyecto.

`wsl --export` protege el entorno completo.

Git permite respaldar:

- Código fuente
    
- Historial de cambios
    
- Ramas
    
- Commits
    
- Repositorios remotos
    

`wsl --export` permite respaldar:

- Sistema operativo Linux
    
- Usuario local
    
- Paquetes instalados
    
- Configuraciones
    
- Proyectos locales
    
- Entornos virtuales
    
- Archivos fuera de Git
    

Ambos enfoques son complementarios.

Lo recomendable es usar Git para versionar proyectos y `wsl --export` para proteger el entorno completo antes de cambios importantes.

---

## 10. Flujo resumido

Listar distribuciones:

```powershell
wsl -l -v
```

Apagar WSL:

```powershell
wsl --shutdown
```

Exportar distribución:

```powershell
wsl --export Ubuntu-24.04 D:\Backups\ubuntu-24.04-wsl-backup.tar
```

Validar archivo:

```powershell
dir D:\Backups\
```

Restaurar si es necesario:

```powershell
wsl --import Ubuntu-24.04-Restore D:\WSL\Ubuntu-24.04-Restore D:\Backups\ubuntu-24.04-wsl-backup.tar --version 2
```

Iniciar distribución restaurada:

```powershell
wsl -d Ubuntu-24.04-Restore
```

---

## 11. Buenas prácticas

Antes de exportar:

- Cerrar terminales abiertas de WSL
    
- Detener procesos importantes
    
- Apagar WSL con `wsl --shutdown`
    
- Verificar el nombre exacto de la distribución
    
- Confirmar espacio disponible en disco
    
- Guardar el archivo `.tar` fuera del sistema de archivos Linux
    

Después de exportar:

- Validar que el archivo `.tar` existe
    
- Revisar su tamaño
    
- Conservar el respaldo hasta confirmar que los cambios fueron exitosos
    
- No eliminarlo inmediatamente después de una actualización
    
- Guardar una copia adicional si el entorno es crítico
    

---

## 12. Conclusión

La exportación de una distribución en WSL permite crear un respaldo completo del entorno Linux.

Este proceso es especialmente útil antes de actualizar Ubuntu, migrar entornos, modificar paquetes importantes o realizar pruebas que puedan afectar la estabilidad del sistema.

En un entorno de trabajo con Python, Docker, Git, laboratorios y documentación técnica, `wsl --export` funciona como una capa adicional de recuperación.

La práctica recomendada es:

```text
Git para proteger los proyectos
wsl --export para proteger el entorno completo
```

---

### Referencias externas

[https://learn.microsoft.com/windows/wsl/basic-commands](https://learn.microsoft.com/windows/wsl/basic-commands)  
[https://learn.microsoft.com/windows/wsl/use-custom-distro](https://learn.microsoft.com/windows/wsl/use-custom-distro)  
[https://learn.microsoft.com/windows/wsl/install](https://learn.microsoft.com/windows/wsl/install)  
[https://documentation.ubuntu.com/wsl/latest/howto/import-export/](https://documentation.ubuntu.com/wsl/latest/howto/import-export/)

---

### Documentación relacionada

[[01 - WSL]]  
[[02 - Mantenimiento de distribuciones]]
[[04 - Actualización de Ubuntu en WSL]]