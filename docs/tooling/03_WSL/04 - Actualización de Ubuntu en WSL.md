## 1. Introducción a la actualización de Ubuntu en WSL

Actualizar Ubuntu dentro de WSL permite pasar de una versión del sistema operativo a otra sin reinstalar la distribución desde cero.

Este proceso es útil para:

- Mantener soporte de seguridad
    
- Actualizar paquetes base del sistema
    
- Mejorar compatibilidad con herramientas modernas
    
- Renovar el entorno de trabajo sin perder la distribución actual
    
- Mantener un ciclo de vida alineado con versiones LTS
    

---

## 2. Contexto del entorno

Cada distribución instalada en WSL funciona de forma independiente.

Actualizar Ubuntu dentro de WSL no actualiza Windows ni el kernel de WSL directamente. Solo actualiza el sistema operativo Linux de esa distribución.

Ejemplo de salto de versión:

```text
Ubuntu 24.04 LTS → Ubuntu 26.04 LTS
```

Antes de actualizar, se recomienda haber realizado una exportación completa de la distribución con `wsl --export`.

---

## 3. Verificar la versión actual de Ubuntu

Dentro de Ubuntu:

```bash
lsb_release -a
```

Ejemplo:

```text
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.4 LTS
Release:        24.04
Codename:       noble
```

También se puede validar información del sistema con:

```bash
cat /etc/os-release
```

---

## 4. Verificar distribuciones WSL desde Windows

Desde PowerShell:

```powershell
wsl -l -v
```

Ejemplo:

```text
  NAME              STATE           VERSION
* Ubuntu-24.04      Running         2
```

El campo `VERSION` debe mostrar:

```text
VERSION 2
```

Esto indica que la distribución está usando WSL2.

---

## 5. Crear respaldo antes de actualizar

Antes de actualizar Ubuntu, se recomienda apagar WSL y exportar la distribución.

Desde PowerShell:

```powershell
wsl --shutdown
wsl --export Ubuntu-24.04 D:\Backups\ubuntu-24.04-wsl-backup.tar
```

Este archivo `.tar` permite restaurar la distribución si la actualización falla.

La exportación debe conservarse hasta confirmar que el sistema actualizado funciona correctamente.

---

## 6. Actualizar paquetes actuales

Antes de ejecutar el salto de versión, se debe dejar la distribución completamente actualizada.

Dentro de Ubuntu:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove -y
sudo apt clean
```

Esto sincroniza repositorios, actualiza paquetes instalados y elimina dependencias que ya no son necesarias.

---

## 7. Instalar el gestor de actualización

El paquete `update-manager-core` permite usar el comando `do-release-upgrade`.

```bash
sudo apt install update-manager-core -y
```

Luego se valida la configuración de upgrades:

```bash
cat /etc/update-manager/release-upgrades
```

La opción recomendada para saltos entre versiones LTS es:

```text
Prompt=lts
```

Si el valor es diferente, se puede editar el archivo:

```bash
sudo nano /etc/update-manager/release-upgrades
```

Y dejarlo así:

```text
Prompt=lts
```

---

## 8. Ejecutar la actualización de versión

Para iniciar el proceso estándar de actualización:

```bash
sudo do-release-upgrade
```

Este comando busca una nueva versión disponible de Ubuntu y ejecuta el asistente de actualización.

Durante el proceso pueden aparecer preguntas sobre:

- Reemplazo de archivos de configuración
    
- Eliminación de paquetes obsoletos
    
- Reinicio de servicios
    
- Desactivación temporal de repositorios externos
    
- Confirmación de cambios antes de continuar
    

Si el sistema pregunta por archivos de configuración modificados manualmente, conviene revisar antes de reemplazarlos.

---

## 9. Uso de do-release-upgrade -d

En algunos casos, el salto entre versiones LTS puede no aparecer inmediatamente por el canal estándar.

Ejemplo:

```bash
sudo do-release-upgrade
```

Puede devolver que no hay una nueva versión disponible.

En ese caso, se puede intentar:

```bash
sudo do-release-upgrade -d
```

El parámetro `-d` permite detectar versiones más recientes antes de que el canal LTS estándar las ofrezca de forma general.

Debe usarse con cautela y preferiblemente solo si ya existe un respaldo completo de la distribución.

---

## 10. Reiniciar WSL después de actualizar

Al terminar la actualización, se recomienda apagar completamente WSL desde PowerShell:

```powershell
wsl --shutdown
```

Luego se vuelve a iniciar la distribución:

```powershell
wsl -d Ubuntu-24.04
```

O si la distribución se llama simplemente `Ubuntu`:

```powershell
wsl -d Ubuntu
```

---

## 11. Validar la nueva versión

Dentro de Ubuntu:

```bash
lsb_release -a
```

Ejemplo esperado:

```text
Distributor ID: Ubuntu
Description:    Ubuntu 26.04 LTS
Release:        26.04
Codename:       resolute
```

También se puede validar con:

```bash
cat /etc/os-release
```

---

## 12. Validar herramientas principales

Después de actualizar, se deben revisar las herramientas usadas en el entorno de trabajo.

```bash
python3 --version
git --version
docker --version
docker compose version
```

También se puede revisar el estado general del sistema con:

```bash
fastfetch
```

Si alguna herramienta no responde correctamente, puede requerir reinstalación o ajuste de configuración.

---

## 13. Cambio esperado en Python

Después de actualizar Ubuntu, puede ocurrir que el comando `python` no esté disponible por defecto.

Ejemplo:

```bash
python carnada.py
```

Puede fallar si `python` no apunta a Python 3.

La forma recomendada en Ubuntu moderno es:

```bash
python3 carnada.py
```

Esto no significa que el proyecto esté dañado.

Si se desea habilitar el comando `python` como alias del intérprete Python 3, se puede instalar:

```bash
sudo apt install python-is-python3 -y
```

Validación:

```bash
python --version
python3 --version
```

Para documentación técnica y proyectos en GitHub, es preferible usar explícitamente `python3`.

---

## 14. Validar proyectos después de actualizar

Para proyectos Python:

```bash
cd ~/projects/carnada
python3 carnada.py --help
```

Si el proyecto usa entorno virtual, puede ser recomendable recrearlo:

```bash
rm -rf .venv
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Para proyectos con Docker:

```bash
cd ~/projects/elastic-security-lab
docker compose config
docker compose up -d
```

Para proyectos Git:

```bash
git status
git remote -v
```

---

## 15. Limpieza posterior a la actualización

Después de confirmar que el sistema funciona correctamente, se puede limpiar el entorno.

```bash
sudo apt autoremove -y
sudo apt clean
```

También se pueden listar paquetes instalados manualmente:

```bash
apt-mark showmanual
```

Guardar la lista en un archivo:

```bash
apt-mark showmanual > ~/apt-packages-manual.txt
```

Listar todos los paquetes instalados:

```bash
dpkg-query -W -f='${binary:Package}\n' > ~/apt-packages-installed.txt
```

Esto ayuda a auditar el entorno después de la actualización.

---

## 16. Riesgos y consideraciones

Actualizar Ubuntu en WSL normalmente no elimina los proyectos locales, pero sí puede modificar el entorno donde se ejecutan.

Elementos que pueden cambiar:

- Versión de Python
    
- Versión de pip
    
- Paquetes instalados con APT
    
- Bibliotecas del sistema
    
- OpenSSL
    
- Git
    
- Docker
    
- Docker Compose
    
- Alias de shell
    
- Entornos virtuales
    
- Dependencias nativas
    

Por eso se recomienda validar los proyectos reales después del upgrade.

---

## 17. Flujo resumido

Desde PowerShell:

```powershell
wsl -l -v
wsl --shutdown
wsl --export Ubuntu-24.04 D:\Backups\ubuntu-24.04-wsl-backup.tar
```

Dentro de Ubuntu:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo apt autoremove -y
sudo apt clean
sudo apt install update-manager-core -y
cat /etc/update-manager/release-upgrades
sudo do-release-upgrade
```

Si no detecta la nueva versión:

```bash
sudo do-release-upgrade -d
```

Después de actualizar:

```powershell
wsl --shutdown
```

Validación dentro de Ubuntu:

```bash
lsb_release -a
python3 --version
git --version
docker --version
docker compose version
```

---

## 18. Buenas prácticas

Antes de actualizar:

- Exportar la distribución con `wsl --export`
    
- Confirmar que el archivo `.tar` fue creado correctamente
    
- Actualizar todos los paquetes actuales
    
- Verificar que los proyectos importantes estén versionados en Git
    
- Confirmar espacio disponible en disco
    

Durante la actualización:

- No cerrar la terminal
    
- Leer los mensajes del asistente de actualización
    
- Revisar antes de reemplazar archivos de configuración
    
- Permitir la eliminación de paquetes obsoletos si no son necesarios
    

Después de actualizar:

- Reiniciar WSL con `wsl --shutdown`
    
- Validar la nueva versión de Ubuntu
    
- Probar Python, Git, Docker y Docker Compose
    
- Probar proyectos reales
    
- Conservar el respaldo `.tar` durante un tiempo prudente
    

---

## 19. Conclusión

Actualizar Ubuntu en WSL permite mantener el entorno Linux vigente sin reinstalar la distribución desde cero.

El proceso recomendado consiste en exportar primero la distribución, actualizar los paquetes actuales, ejecutar `do-release-upgrade`, reiniciar WSL y validar herramientas críticas.

En el caso de un salto como:

```text
Ubuntu 24.04 LTS → Ubuntu 26.04 LTS
```

los proyectos no deberían alterarse como archivos, pero el entorno sí puede cambiar.

Por eso, la práctica correcta es combinar respaldo, actualización controlada y validación posterior.

---

### Referencias externas

[https://ubuntu.com/server/docs/how-to/software/upgrade-your-release/](https://ubuntu.com/server/docs/how-to/software/upgrade-your-release/)  
[https://documentation.ubuntu.com/desktop/en/latest/how-to/upgrade-ubuntu-desktop/](https://documentation.ubuntu.com/desktop/en/latest/how-to/upgrade-ubuntu-desktop/)  
[https://ubuntu.com/about/release-cycle](https://ubuntu.com/about/release-cycle)  
[https://documentation.ubuntu.com/release-notes/26.04/](https://documentation.ubuntu.com/release-notes/26.04/)  
[https://learn.microsoft.com/windows/wsl/basic-commands](https://learn.microsoft.com/windows/wsl/basic-commands)

---

### Documentación relacionada

[[01 - WSL]]  
[[02 - Mantenimiento de distribuciones]]
[[03 - Exportación de una distribución en WSL]] 