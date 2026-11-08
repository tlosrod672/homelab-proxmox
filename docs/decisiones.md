# Decisiones técnicas

Cada decisión con su motivo, para poder revisarla cuando cambie el contexto.

| Decisión | Alternativas | Por qué |
|---|---|---|
| **Clúster de 3 nodos** | 2 nodos | Con 3 nodos hay mayoría (2) aunque caiga uno. Con 2, al caer uno el otro se bloquea (o hay que añadir un voto externo). |
| **ZFS en el SSD de cada nodo** (un solo disco por nodo) | ext4/LVM | Instantáneas baratas, sumas de verificación y la base para **replicación** entre nodos y HA más adelante. A cambio, ZFS escribe algo más que ext4 y hay que vigilar el desgaste (ver SMART). Sin redundancia por nodo (un disco): la redundancia viene de las copias y de tener 3 nodos. |
| **HDD como almacén *Directory* (ext4), no ZFS** | ZFS en el HDD | Un almacén ZFS solo guarda discos de VM/CT; un Directory guarda también ISO, plantillas, copias y *snippets*. Quiero el HDD para copias y material voluminoso, no para discos de VM. |
| **Discos de VM solo en el SSD** | VM en el HDD | Rendimiento y menos desgaste del HDD. En el almacén `hdd` no se marca "Disk image" ni "Container". |
| **Interfaz de gestión por la NIC de placa, no por la i210** | i210 para gestión | La i210 queda reservada para el segundo enlace de corosync y tráfico futuro; la de placa es la fija. |
| **Tarjeta i210 en ranura x1** | Ranura x16 | Una red de 1 Gbit/s usa ~125 MB/s y PCIe x1 da ~500 MB/s: sobra. La x16 queda libre para 10 GbE o NVMe. |
| **Abrir el equipo una sola vez** (RAM y tarjeta de red antes de instalar) | Instalar y luego ampliar | El instalador ve todas las tarjetas desde el primer arranque y no hay que reabrir ni renombrar interfaces. |
| **Tailscale con *subnet router* en un LXC** | VPN en cada equipo, abrir puertos | Sin abrir puertos en el router; un único punto de entrada controlado y fácil de revocar; no hay que instalar el cliente en cada equipo. |
| **LXC sin privilegios para el subnet router** | LXC privilegiado, VM | Más seguro (el root del contenedor es un usuario normal en el host) y mucho más ligero que una VM. Solo se le pasa `/dev/net/tun` y se activa *nesting*. |
| **VM (no LXC) para el asistente de IA** | LXC | Aislamiento más fuerte para un servicio con acceso a herramientas; permite snapshot de la máquina entera antes de cambios. |
| **Repositorios *No-Subscription* de Proxmox** | Suscripción de pago | Es un laboratorio personal; los repositorios *enterprise* se desactivan para evitar errores de actualización. |
| **Snapshot antes de cada cambio grande** | Confiar en no equivocarse | Volver atrás en segundos; es el hábito que más tiempo ahorra. |
| **Documentar sin datos sensibles** | Notas con IPs y claves | El repositorio es público: ni secretos, ni IPs, ni nombres internos. |
