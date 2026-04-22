## 1. ¿Qué es una Virtual Machine (VM)?

Una **Virtual Machine (VM)** es un entorno computacional que funciona como un sistema informático virtual con su propia CPU, memoria, interfaz de red y almacenamiento, pero creado sobre un sistema de hardware físico (Host). En tu flujo de trabajo, la VM es el contenedor aislado donde ejecutas sistemas operativos completos para pruebas de seguridad sin comprometer tu Windows host.

## 2. El Hipervisor (El Motor)

El hipervisor es el software que crea y ejecuta las máquinas virtuales. En tu caso, utilizas **VMware Workstation**, que es un **Hipervisor de Tipo 2**:

- **Tipo 1 (Bare-metal):** Se instala directamente sobre el hardware (ej. ESXi, Proxmox).
    
- **Tipo 2 (Hosted):** Se instala como una aplicación sobre un sistema operativo (ej. VMware Workstation sobre Windows 10/11).
    

## 3. Conceptos Clave de Arquitectura

Para gestionar tus activos (`ENDP`, `DFIR`, `PENT`), es vital entender la relación entre el Host y el Guest:

- **Host Machine:** Tu computadora física con Windows. Es la que provee los recursos reales (RAM, Disco, CPU).
    
- **Guest Machine:** La máquina virtual (ej. Debian, Kali). Cree que tiene su propio hardware dedicado.
    
- **Encapsulación:** Una VM se guarda como un conjunto de archivos en una carpeta. Esto permite que puedas mover tu laboratorio completo simplemente copiando una carpeta a un disco externo.
    

## 4. Componentes Virtuales Críticos

|**Componente**|**Definición en Virtualización**|
|---|---|
|**vCPU**|Asignación de núcleos lógicos del procesador físico a la VM.|
|**vRAM**|Porción de la memoria física reservada para el Guest OS.|
|**vDisk (VMDK)**|Un archivo que simula un disco duro físico. Puede ser "pre-allocated" (reserva todo el espacio de golpe) o "thin provisioned" (crece según se llena).|
|**vNIC**|Tarjeta de red virtual que define cómo se comunica la VM (NAT, Bridge, Host-Only).|

## 5. Ventajas para el Analista de Ciberseguridad

- **Aislamiento (Sandboxing):** Si un malware infecta `ENDP-WIN-2604012`, el código malicioso no puede "saltar" a tu Windows personal (siempre que la red esté bien configurada).
    
- **Reproducibilidad:** Puedes configurar un laboratorio, fallar, y volver a empezar en segundos.
    
- **Multi-Entorno:** Ejecutar simultáneamente una red con Debian, Windows y Kali para simular ataques y defensas reales.
    
- **Portabilidad:** Exportar tus máquinas como archivos `.OVA` para compartirlas o llevarlas a otros entornos de trabajo.
    

## 6. Diferencia con Docker (Contenedores)

Es importante no confundir:

- **VM:** Virtualiza el **hardware**. Incluye un kernel completo. Es pesado pero ofrece aislamiento total. Ideal para análisis forense.
    
- **Docker:** Virtualiza el **sistema operativo**. Comparte el kernel del host (o de WSL). Es ligero y rápido. Ideal para servicios y herramientas de CTH.
    

---

### Referencias Externas

- [VMware: What is a Virtual Machine?](https://www.vmware.com/topics/glossary/content/virtual-machine.html)
    

### Documentación Relacionada

[[02 - Creación y configuración de una VM en VMware]]
[[03 - Estándar de nomenclatura e inventario de VMs]]
[[04 - Gestión de snapshots y reversión de estado]]
[[05 - Exportación e importación de OVAs]]
[[01 - Powershell]]
[[01 - WSL]]
[[01 - Conceptos fundamentales de Docker]]