## 1. Definición de Snapshot

Un **Snapshot** es una captura del estado completo de una máquina virtual en un instante preciso. Incluye el contenido de la memoria (si la VM está encendida), la configuración de la VM y el estado de todos sus discos virtuales. En ciberseguridad, los snapshots son la herramienta de "guardado rápido" que permite realizar pruebas destructivas con una red de seguridad absoluta.

## 2. Casos de Uso en CTI/CTH

- **Estado Limpio (Golden Image):** Captura realizada justo después de instalar el OS y las herramientas base. Es el punto de retorno tras finalizar cualquier laboratorio.
    
- **Pre-Infección:** Captura antes de ejecutar un artefacto sospechoso o script de pentesting.
    
- **Análisis de Cambios:** Tomar un snapshot antes y después de una acción para comparar registros o archivos modificados.
    
- **Puntos de Control en Instalaciones:** Evita tener que reinstalar todo el SO si una configuración compleja falla.
    

## 3. Estados del Snapshot

VMware permite tomar capturas en dos estados diferentes:

|**Estado**|**Ventajas**|**Desventajas**|
|---|---|---|
|**VM Apagada**|Snapshot pequeño, rápido de crear y muy estable.|No guarda procesos en ejecución ni datos en RAM.|
|**VM Encendida**|Guarda el estado exacto de la ejecución (apps abiertas, memoria).|Archivo muy pesado, proceso de creación más lento.|

## 4. Operaciones Críticas en VMware Workstation

### 4.1 Crear un Snapshot (Take Snapshot)

1. Ve a **VM > Snapshot > Take Snapshot**.
    
2. **Nombre:** Usa nombres descriptivos (ej. `BASE_CONFIG_DONE`, `PRE_VELOCIRAPTOR_DEPLOY`).
    
3. **Descripción:** Detalla qué se instaló o qué estado tiene la IP/Servicios.
    

### 4.2 Revertir (Revert to Snapshot)

Regresa la VM al estado del snapshot seleccionado de forma inmediata.

- **Nota:** Perderás todos los cambios realizados desde que se tomó el snapshot hasta el momento actual.
    

### 4.3 Administrar (Snapshot Manager)

Permite visualizar la línea de tiempo de la VM. Puedes borrar snapshots antiguos para liberar espacio en disco o saltar entre diferentes ramas de configuración.

## 5. El Impacto en el Almacenamiento

Es vital entender que un snapshot **no es un backup completo**.

- VMware utiliza archivos delta (`.vmdk` diferenciales). Cuantos más cambios realices después de un snapshot, más crecerá el archivo diferencial.
    
- **Regla de Oro:** No acumules demasiados snapshots (máximo 2 o 3 por VM) si planeas usar la máquina por mucho tiempo, ya que puede degradar el rendimiento del disco y consumir espacio excesivo en tu Windows Host.
    

## 6. Buenas Prácticas para el Laboratorio

1. **Snapshot 00:** Siempre toma un snapshot llamado `CLEAN_INSTALL` con las VMware Tools instaladas y la IP estática configurada.
    
2. **Limpieza Post-Laboratorio:** Una vez terminado el análisis de un malware o prueba de red, revierte al estado limpio. No guardes estados infectados a menos que necesites análisis forense posterior.
    
3. **Consolidación:** Si una configuración es exitosa y definitiva, borra los snapshots previos para consolidar los cambios en el disco principal y recuperar espacio.
    

---

### Referencias Externas

- [VMware Docs: Using Snapshots to Manage Virtual Machines](https://www.google.com/search?q=https://docs.vmware.com/en/VMware-Workstation-Pro/17/com.vmware.ws.using.doc/GUID-60908779-7809-4C4F-941A-98A88E148483.html)
    

### Documentación Relacionada

[[01 - Virtualización (Virtual Machines)]]
[[02 - Creación y configuración de una VM en VMware]]
[[05 - Exportación e importación de OVAs]]