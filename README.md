# Homelab Proxmox: clúster de 3 nodos, acceso remoto seguro, domótica y asistente de IA

Laboratorio personal de sistemas y redes que construyo y documento paso a paso mientras estudio el CFGS de **Administración de Sistemas Informáticos en Red (ASIR)**. No es un "manual perfecto": es un cuaderno de ingeniería con lo que hice, por qué lo decidí, qué falló y cómo lo arreglé.

> **English summary:** personal systems & networking lab: a 3-node **Proxmox VE 9** cluster with **ZFS**, secure remote access with **Tailscale** (subnet router in an LXC container), **Home Assistant** with a voice assistant, and a 24/7 Linux VM running an AI assistant for documentation and maintenance. Everything is documented as a lab journal (goal, steps, warnings, lessons). Notes are in Spanish. See [`README.en.md`](README.en.md).

## Qué hay montado

| Pieza | Qué es | Estado |
|---|---|---|
| **Clúster Proxmox VE 9** | 3 nodos (1 mini-PC Lenovo + 2 HP EliteDesk SFF), ZFS en el SSD de cada nodo, quórum de 3 | Activo |
| **Acceso remoto** | Tailscale con un *subnet router* en un contenedor LXC: llego a toda la red de casa sin abrir puertos | Activo |
| **Máquinas y contenedores** | VM Debian 13 24/7 con un asistente de IA, LXC ligeros, snapshots antes de cada cambio | Activo |
| **Home Assistant** | Domótica (Zigbee, enchufes con medición de energía), panel en tablet de pared y asistente de voz | Activo |
| **Salud de discos** | Lectura mensual de SMART de todos los discos y registro del desgaste | Activo |
| **Windows Server + AD/DNS/DHCP** | Dominio de laboratorio con GPO | Planificado |
| **Copias, NAS, VLAN, monitorización, HA** | Fases 2 a 6 del plan | Planificado |

El detalle de cada fase y su estado real está en [`docs/plan-de-laboratorio.md`](docs/plan-de-laboratorio.md).

## Cómo está organizado

- [`docs/arquitectura.md`](docs/arquitectura.md): hardware, red, almacenamiento y diagrama.
- [`docs/decisiones.md`](docs/decisiones.md): decisiones técnicas razonadas (qué elegí y por qué).
- [`docs/lecciones-aprendidas.md`](docs/lecciones-aprendidas.md): errores, causas y soluciones.
- [`docs/plan-de-laboratorio.md`](docs/plan-de-laboratorio.md): hoja de ruta por fases.
- [`docs/evidencias.md`](docs/evidencias.md): estado real del clúster y lecturas SMART.
- [`practicas/`](practicas): una nota por práctica (objetivo, pasos, avisos y preguntas de repaso).

## Prácticas

1. [Hardware, instalación de Proxmox y creación del clúster](practicas/01-hardware-y-cluster-proxmox.md)
2. [Subnet router de Tailscale en un LXC](practicas/02-tailscale-subnet-router-lxc.md)
3. [Salud de los discos con SMART](practicas/03-salud-de-discos-smart.md)
4. [Asistente de IA 24/7 en una VM y base de conocimiento en Markdown](practicas/04-asistente-ia-24x7.md)
5. [Home Assistant y asistente de voz](practicas/05-home-assistant-y-voz.md)

## Qué no está hecho todavía

Para no confundir lo planificado con lo hecho: **no hay aún** copias de seguridad programadas ni restauración probada (fase 2), ni dominio de Windows Server/AD propio (fase 1), ni NAS, VLAN, monitorización ni alta disponibilidad. Tampoco está montado el segundo enlace de corosync. Todo eso figura como *Planificado* en el [plan](docs/plan-de-laboratorio.md).

## Cómo trabajo

- **Documento cada práctica** con objetivo, pasos, avisos y preguntas de repaso.
- **Regla que me aplico:** snapshot de la VM antes de los cambios grandes. La **restauración de copias** es un objetivo pendiente (fase 2).
- **Sin secretos en el repositorio:** ni contraseñas, ni tokens, ni claves, ni direcciones IP. Las direcciones se describen como "la red local". Los nombres de máquinas (`pve1`, etc.) son genéricos.
- **Uso IA como herramienta, no como sustituto.** Un asistente (Claude, de Anthropic) me ayuda y yo reviso el resultado:
  - *Qué hace la IA:* redacta borradores de estas notas, lee por API (con un token de solo lectura) datos del clúster como el estado de los discos, ayudó a configurar la tablet por ADB y ejecuta tareas de mantenimiento en una VM.
  - *Qué hago yo:* monté el hardware, instalé Proxmox y creé el clúster siguiendo la práctica, decido qué se hace, reviso y apruebo lo que se publica, y compruebo en el laboratorio real los resultados que me da el asistente antes de darlos por buenos.

## Evidencias

[`docs/evidencias.md`](docs/evidencias.md) recoge el estado real del clúster y un resumen de lecturas SMART.

## Autor

Tomás Losada Rodríguez · Técnico Microinformático · estudiante de ASIR · [LinkedIn](https://www.linkedin.com/in/tlosrod/)

## Licencia

[MIT](LICENSE).
