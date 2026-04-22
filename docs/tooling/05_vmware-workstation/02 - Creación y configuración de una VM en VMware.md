## 1. Proceso de Creación (Wizard)

Para mantener el estándar de laboratorio definido, la creación de cada activo debe seguir un flujo que garantice el rendimiento y el aislamiento necesario para tareas de DFIR y Pentesting.

### 1.1 Configuración de Hardware (Custom)

Se recomienda utilizar la opción **"Custom (advanced)"** para tener control total sobre los dispositivos virtuales:

1. **Compatibility:** Seleccionar la versión más reciente de Workstation disponible para asegurar soporte de hardware moderno.
    
2. **Installer Disk Image (iso):** Seleccionar la imagen del SO (ej. Debian 13 o Windows 10).
    
3. **Naming:** Aplicar estrictamente la nomenclatura (ej. `ENDP-DEB-2604007`).
    
4. **Processors:** Asignar al menos 2 núcleos. Para entornos de análisis pesado (Kali Purple), subir a 4 núcleos.
    
5. **Memory:** * **Linux Terminal:** 2 GB.
    
    - **Windows/Kali Desktop:** 4 GB - 8 GB.
        

## 2. Configuración de Almacenamiento

El manejo del disco virtual es crítico para la portabilidad y el mantenimiento:

- **Disk Type:** SCSI o NVMe (Recomendado por velocidad).
    
- **Capacity:** * 20 GB - 40 GB para Endpoints ligeros.
    
    - 80 GB+ para máquinas de análisis (DFIR/PENT).
        
- **Disk Provisioning:** Seleccionar **"Split virtual disk into multiple files"**. Esto facilita mover la VM entre unidades de almacenamiento (SSD/HDD) y realizar backups rápidos.
    

## 3. Configuración de Red (Networking)

Dependiendo del rol de la máquina, se debe elegir el adaptador correcto:

- **NAT:** Permite salida a internet compartiendo la IP del host Windows. Ideal para descargar actualizaciones iniciales.
    
- **Host-Only:** Crea una red privada entre el host y las VMs. **Obligatorio** para análisis de malware o laboratorios aislados donde no se desea tráfico hacia el router real.
    
- **Bridge:** La VM obtiene una IP directamente del router físico. Usar con precaución.
    

## 4. Post-Instalación: VMware Tools

Una vez instalado el sistema operativo, es mandatorio instalar las **VMware Tools** (u `open-vm-tools` en Linux):

Bash

```bash
# En Debian/Ubuntu/Kali:
sudo apt update
sudo apt install open-vm-tools-desktop -y
sudo reboot
```

**Beneficios:**

- Sincronización de reloj con el host.
    
- Resolución de pantalla dinámica.
    
- Carpetas compartidas y portapapeles bidireccional.
    

## 5. Checklist de Finalización

Antes de considerar la VM como "lista para producción", verifica:

1. [ ] El hostname interno coincide con el nombre de la VM.
    
2. [ ] Se ha asignado la IP estática correspondiente al inventario.
    
3. [ ] Se ha creado el snapshot inicial (Clean State).
    
4. [ ] Las credenciales coinciden con el estándar (`rhodyn/rhodyn`).
    

---

### Referencias Externas

- [VMware Docs: Creating a Virtual Machine in Workstation](https://docs.vmware.com/en/VMware-Workstation-Pro/index.html)
    

### Documentación Relacionada

[[01 - Virtualización (Virtual Machines)]]
[[03 - Estándar de nomenclatura e inventario de VMs]]
[[04 - Gestión de snapshots y reversión de estado]]