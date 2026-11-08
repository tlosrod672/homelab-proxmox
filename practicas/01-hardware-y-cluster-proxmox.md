# Práctica 01 · Hardware, instalación de Proxmox VE y creación del clúster

**Fecha:** octubre de 2026 · **Material de estudio (ASIR)**

## Objetivo
Dejar los tres nodos con su hardware definitivo (RAM y tarjeta de red), instalar Proxmox VE en los dos HP EliteDesk y crear el clúster de 3 nodos.

## 1. Hardware (antes de instalar)
- **Por qué antes:** se abre cada equipo una sola vez y el instalador ya ve todas las tarjetas de red.
- **RAM:** el Lenovo con 4 × 16 GB (64 GB, DDR4 a 2400 MT/s); los dos HP con 4 × 8 GB (32 GB).
- **Tarjeta Intel i210** en una ranura **x1**: una red de 1 Gbit/s usa ~125 MB/s y una PCIe x1 ofrece ~500 MB/s; la x16 queda libre para 10 GbE o NVMe. Soporte bajo (equipos SFF).
- Retiradas las tarjetas antiguas del HP (puerto paralelo y serie).
- **Error y solución:** tras cambiar la RAM, el equipo encendía (LED fijo, sin pitidos) pero no daba imagen. Era el **entrenamiento de memoria** del primer arranque; tras esperar unos minutos arrancó con normalidad.
- **BIOS (F10) revisada:** VT-x y VT-d activos, SATA en AHCI (sin RAID: mejor para ZFS), ahorro de energía S5 profundo desactivado (necesario para Wake-on-LAN), "After Power Loss" = "Previous State", UEFI sin Legacy y **USB Storage Boot activado** (sin esto no arranca el instalador).

## 2. Instalación de Proxmox VE 9
- **ZFS (RAID0 de un disco) solo en el SSD**; el HDD se deja en "do not use" durante la instalación. Teclado British English igual que en el nodo principal.
- **Interfaz de gestión:** la NIC de placa, no la i210. En el instalador, `eno1` es la de placa y `ens8`/`enp1s0` la tarjeta de la ranura.
- Mensajes `modprobe: FATAL/ERROR` en el arranque del instalador: normales.
- Después de instalar: desactivar los repositorios *enterprise* (pve y ceph), añadir **No-Subscription**, *Refresh* y *Upgrade*. Resultado: Proxmox VE 9.2 con el kernel `-pve` más reciente.
- **Nombres de red fijados (Proxmox 9):** el instalador fija los nombres de las tarjetas presentes (`nic0`, `nic1`) por MAC, por lo que no cambian si se mueve la tarjeta de ranura. En el nodo principal la i210 se instaló después y salió con un nombre de ranura, así que la fijé a mano:
  ```
  cp /etc/network/interfaces /root/interfaces.bak
  pve-network-interface-pinning generate --interface <nombre-actual> --target-name nic1
  # reiniciar
  ```

## 3. Clúster `homelab`
- En el primer nodo: *Datacenter → Cluster → Create Cluster* (nombre `homelab`, enlace 0 por la NIC de gestión) → *Join Information* → *Copy*.
- En los otros dos: *Datacenter → Cluster → Join Cluster*, pegar la información y la contraseña de root del primer nodo, y elegir su propio enlace 0. La página web se corta al unirse (cambia el certificado): recargar.
- Comprobación con `pvecm status`: **3 nodos, Quorate: Yes, quórum 2**, transporte knet cifrado.
- **Quórum:** con 3 nodos la mayoría es 2, así que el clúster aguanta 1 nodo caído. Con 2 nodos, al caer uno el otro se bloquea.

## 4. El HDD como almacén `hdd`
- Tipo **Directory (ext4)** y no ZFS: un almacén ZFS solo guarda discos de VM/CT; un Directory guarda ISO, plantillas, copias y *snippets*.
- Primer nodo: *Disks → Wipe Disk* (comprobando el **modelo**: es el HDD, no el SSD) → *Directory → Create* (`hdd`, ext4, con **Add Storage** marcado).
- Segundo nodo: igual pero con **Add Storage desmarcado** (el ID de almacén es único en el clúster), y después *Datacenter → Storage → hdd → Edit → Nodes* con los dos nodos.
- Contenido permitido: ISO, plantillas de contenedor, copias, *snippets*, importación. **Sin** "Disk image" ni "Container": las VM van al SSD.
- **No compartido:** cada nodo tiene su propio HDD; lo compartido de verdad será el NAS.

## Pendiente de esta práctica
- Segundo enlace de corosync por la tarjeta i210 en una red separada, cuando estén el rack y el switch.
- Política de retención de copias del almacén `hdd` (al hacer la primera copia).

## Preguntas de repaso
1. ¿Por qué con 3 nodos el clúster aguanta 1 caído y con 2 no?
2. ¿Qué diferencia hay entre `local-zfs` y `hdd` en lo que pueden guardar? ¿Por qué no queremos discos de VM en el HDD?
3. ¿Por qué al crear el *Directory* en el segundo nodo había que desmarcar "Add Storage"?
4. Si muevo la i210 de ranura, ¿cambia su nombre en Proxmox 9? ¿Por qué?
5. En un WD Blue SSD, ¿qué atributo SMART **no** sirve para medir el desgaste y cuál sí?
