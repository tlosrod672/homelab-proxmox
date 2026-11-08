# Plan de laboratorio

Plan por fases, ordenado de menos a más difícil. Regla: **un objetivo por semana, documentar cada práctica y probar siempre la restauración**.

| Fase | Contenido | Estado |
|---|---|---|
| **0. Puesta en marcha** | Rack y red, Wake-on-LAN, Proxmox en los 3 nodos, clúster y prueba de quórum, SMART de los discos | **Hecho** (clúster en marcha; comportamiento sin quórum observado en la práctica, ver [lecciones](lecciones-aprendidas.md)) |
| **1. Windows Server y dominio** | VM con Windows Server, Active Directory + DNS + DHCP, unir clientes, GPO, segundo controlador | Planificado |
| **2. Copias y recuperación** | Copias programadas de las VM, restauración completa y de un fichero, regla 3-2-1 | Planificado |
| **3. NAS** | OpenMediaVault, carpetas compartidas con permisos, Nextcloud | Planificado |
| **4. Red avanzada y seguridad** | VLAN, firewall virtual (OPNsense/pfSense), WireGuard, DNS de casa | Planificado |
| **5. Monitorización y automatización** | Uptime Kuma, Grafana/Prometheus o Zabbix, scripts (PowerShell y Bash), alertas | Planificado |
| **6. Alta disponibilidad** | HA en Proxmox, almacenamiento compartido, migración de VM, prueba de fallo controlada | Planificado |
| **7. Asistente de IA 24/7** | VM dedicada para documentar y mantener el laboratorio con un asistente de IA | **En marcha** |

## Hábitos

- Una nota por práctica: objetivo, pasos, errores y solución.
- **Snapshot** de la VM antes de cada cambio grande.
- Probar la restauración, no solo hacer la copia.
- Mezclar CPU Intel y AMD complica la migración en vivo: usar un tipo de CPU genérico en las VM cuando llegue la fase 6.

## Formación asociada

- Curso online «Administrador de Sistemas: de 0 a Experto» (Udemy): contenido de Proxmox con clúster de 3 nodos, Ceph, Windows Server y Active Directory. Ceph es parte del curso, no del laboratorio real.
- CFGS ASIR (2026-2028).
