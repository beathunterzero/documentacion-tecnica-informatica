## 1. Definición de OVA y OVF

Para asegurar la portabilidad de tus laboratorios de ciberseguridad, VMware utiliza estándares abiertos que permiten empaquetar una máquina virtual completa en un solo archivo.

- **OVF (Open Virtualization Format):** Es un estándar basado en una carpeta que contiene varios archivos (el archivo de configuración `.ovf`, los discos `.vmdk` y archivos de manifiesto).
    
- **OVA (Open Virtual Application):** Es un archivo único comprimido (un "tarball") que contiene todo el paquete OVF. Es el formato preferido para compartir laboratorios o mover activos entre tu PC de escritorio y laptops de campo.
    

## 2. Exportación de una VM a OVA

Este proceso es vital para crear copias de seguridad externas de tus máquinas "DNI" o para compartir entornos de DFIR configurados.

### 2.1 Pasos para Exportar

1. **Apagar la VM:** La exportación requiere que el activo esté en estado _Powered Off_.
    
2. **Limpieza:** Elimina archivos temporales y vacía la papelera dentro del Guest para reducir el tamaño del archivo final.
    
3. **Ejecución:** Ve a **File > Export to OVF...**
    
4. **Formato:** En la ventana de guardado, selecciona `.ova` en el tipo de archivo si deseas un solo archivo contenedor.
    
5. **Destino:** Guarda el archivo en una unidad externa o en tu almacenamiento de backups de largo plazo.
    

## 3. Importación de un archivo OVA

Permite desplegar rápidamente máquinas pre-configuradas (como Kali Purple o entornos vulnerables de VulnHub).

### 3.1 Pasos para Importar

1. **Ejecución:** Ve a **File > Open...** y selecciona el archivo `.ova`.
    
2. **Aceptación de EULA:** Si el paquete incluye licencias, deberás aceptarlas.
    
3. **Configuración de Nombre y Ruta:** * **Name:** Aplica tu nomenclatura (ej. `ENDP-WIN-2604012`).
    
    - **Storage path:** Elige una carpeta en tu SSD con suficiente espacio.
        
4. **Importación:** VMware procesará la descompresión y registrará la VM en tu inventario.
    

## 4. Consideraciones Técnicas y Errores Comunes

- **Snapshots:** Al exportar a OVA, generalmente **se pierde la línea de tiempo de snapshots**. La máquina resultante será una consolidación del estado actual. Si necesitas los snapshots, debes copiar la carpeta completa de la VM en lugar de exportarla.
    
- **Hardware Incompatible:** Al importar una VM creada en una versión más reciente de VMware, podrías recibir un error. Puedes intentar editar el archivo `.ovf` (si es OVF) o usar VMware vCenter Converter.
    
- **MAC Address:** Al importar, VMware te preguntará si "Copié la máquina" o "Moví la máquina". Selecciona **"I Copied It"** para generar nuevas direcciones MAC y evitar conflictos de red en tu laboratorio.
    

## 5. Mejores Prácticas de Portabilidad

1. **Documentación Adjunta:** Siempre guarda un archivo `.txt` junto al OVA con las credenciales (`rhodyn/rhodyn`) y la IP estática que tenía configurada.
    
2. **Compresión:** Los archivos OVA ya están comprimidos, pero si necesitas enviarlos por red, puedes usar herramientas como 7-Zip para dividirlos en partes más pequeñas.
    
3. **Verificación de Integridad:** Tras una importación, siempre verifica que las **VMware Tools** sigan funcionando correctamente y que el adaptador de red esté en el modo deseado (NAT/Host-Only).
    

---

### Referencias Externas

- [VMware Docs: Export an OVF Template](https://www.google.com/search?q=https://docs.vmware.com/en/VMware-Workstation-Pro/17/com.vmware.ws.using.doc/GUID-D815858E-A343-417A-8994-67D8919A30E5.html)
    

### Documentación Relacionada

[[01 - Virtualización (Virtual Machines)]]