## 1. Filosofía de Gestión de Activos

En un entorno de laboratorio profesional, las máquinas virtuales no son archivos temporales, sino **activos de análisis**. La estandarización de sus nombres permite una identificación rápida en logs de red, reportes de Velociraptor y auditorías de seguridad.

## 2. Estructura de la Nomenclatura (Naming Convention)

El nombre de cada VM se compone de tres bloques definidos: `[ROL]-[SISTEMA]-[ID_TEMPORAL]`

### 2.1 Bloque 1: Rol del Activo (Prefijo)

Define la función principal de la máquina en el laboratorio:

- **ENDP:** _Endpoint_ (Objetivo de pruebas o estación de usuario).
    
- **DFIR:** _Digital Forensics & Incident Response_ (Análisis y recolección).
    
- **PENT:** _Pentesting_ (Herramientas ofensivas y auditoría).
    
- **SERV:** _Servidor_ (Servicios de infraestructura, bases de datos).
    

### 2.2 Bloque 2: Sistema Operativo (OS)

Acrónimos de tres letras para identificar la base tecnológica:

- **DEB:** Debian | **FED:** Fedora | **WIN:** Windows.
    
- **KAL:** Kali Linux | **KALP:** Kali Linux Purple (SOC en una caja).
    

### 2.3 Bloque 3: ID Temporal y Único (DNI de la Máquina)

Formato: `YYMM###`

- **YY:** Año de creación (ej. `26` para 2026).
    
- **MM:** Mes de creación (ej. `04` para abril).
    
- **###:** Identificador correlativo único (DNI). **Regla de Oro:** Este número jamás se reutiliza, incluso si la VM es dada de baja.
    

## 3. Inventario Actual de Laboratorio

|**Hostname**|**OS**|**IP Estática**|**Rol / Propósito**|
|---|---|---|---|
|**ENDP-DEB-2604007**|Debian 13|192.168.197.130|Endpoint de pruebas Linux|
|**ENDP-FED-2604008**|Fedora 43|192.168.197.131|Endpoint de pruebas Linux|
|**DFIR-KALP-2604009**|Kali Purple|192.168.197.133|Laboratorio de Blue Team / SOC|
|**PENT-KAL-2604010**|Kali Linux|192.168.197.134|Pruebas de penetración|
|**ENDP-WIN-2604012**|Windows 10|192.168.197.137|Endpoint de pruebas Windows|

## 4. Ciclo de Vida del Activo

1. **Alta:** Se asigna el siguiente ID disponible y se documenta en el vault de Bitwarden (IP, credenciales, propósito).
    
2. **Mantenimiento:** Las credenciales deben ser consistentes (`rhodyn / rhodyn`) para facilitar el acceso rápido en laboratorios aislados.
    
3. **Baja (Decommissioning):** Si una VM se elimina, se debe registrar en el log de Bitwarden el motivo (ej. "Corrupción de FS", "Fin de ciclo de pruebas de malware"). El ID queda "quemado" permanentemente.
    

## 5. Mejores Prácticas de Red

- **IPs Estáticas:** Es fundamental mantener el direccionamiento en el rango `192.168.197.x` para que las herramientas de análisis (como dashboards de SIEM o Velociraptor) no pierdan la visibilidad del endpoint por cambios de DHCP.
    
- **Seguridad:** Aunque sean laboratorios, se recomienda no usar credenciales reales del host Windows.
    

---

### Referencias Externas

- [VMware Best Practices: Naming Conventions for Virtual Machines](https://www.google.com/search?q=https://kb.vmware.com/s/article/1003736)
    

### Documentación Relacionada

[[01 - Virtualización (Virtual Machines)]]
[[02 - Creación y configuración de una VM en VMware]]