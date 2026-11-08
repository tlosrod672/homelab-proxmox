# Lecciones aprendidas

Errores reales, su causa y cómo los resolví.

## Hardware y arranque

- **El equipo enciende pero no da imagen tras cambiar la RAM** (LED fijo, sin pitidos): era el **entrenamiento de memoria** del primer arranque con módulos nuevos. Esperar unos minutos y arrancó normal.
- **El instalador de Proxmox no arranca desde USB:** en la BIOS de los HP hay que activar **USB Storage Boot**.
- **Mensajes `modprobe: FATAL/ERROR` al arrancar el instalador:** normales (módulos que el equipo no necesita); no son un fallo.
- **BIOS revisada en los HP:** virtualización (VT-x y VT-d) activa, SATA en AHCI (sin RAID, mejor para ZFS), Wake-on-LAN permitido (desactivar el ahorro de energía S5 profundo), "After Power Loss" en "Previous State" y UEFI sin Legacy.

## Red

- **Nombres de interfaz que cambian al mover una tarjeta:** Proxmox 9 *fija* los nombres de las tarjetas presentes en la instalación (`nic0`, `nic1`) por dirección MAC. Una tarjeta añadida **después** salió con un nombre de ranura y hubo que fijarla a mano con `pve-network-interface-pinning generate`, tras guardar una copia de `/etc/network/interfaces`.
- **La página web se corta al unir un nodo al clúster:** es normal, cambia el certificado; basta recargar.
- **Sin quórum el nodo "se rompe" a la vista:** con dos nodos apagados, el tercero perdió el quórum: la consola web dejó de funcionar, el contenedor del subnet router no arrancaba y `/etc/pve` pasó a solo lectura. Es el comportamiento esperado y confirma por qué importa tener mayoría. Remedio: encender otro nodo. `pvecm expected 1` solo como **último recurso y con los demás nodos apagados**, porque si no puede provocar *split-brain*. A largo plazo, un voto externo (QDevice).
- **Si el portátil está en otra red que también usa el mismo rango**, la ruta anunciada por Tailscale choca: en ese caso se usan las direcciones propias de Tailscale.

## Almacenamiento

- **El ID de un almacén es único en todo el clúster:** al crear el *Directory* `hdd` en el segundo nodo hay que **desmarcar "Add Storage"** y luego asignar los dos nodos desde Datacenter → Storage.
- **Antes de formatear un disco, identificarlo por modelo, no por letra:** `sda` y `sdb` pueden cambiar entre arranques.

## Salud de discos (SMART)

- **El atributo 230 de un WD Blue SSD (`Media_Wearout_Indicator`) no es el desgaste:** es un valor empaquetado y Proxmox puede mostrar un 95 % engañoso. El desgaste real se lee en el ciclo medio de programa/borrado (atributo 173) y en los GiB escritos (241).
- **En Crucial BX500:** el atributo 202 es el % de vida usado y el 246 son los LBA escritos × 512 B.
- **En NVMe, `wearout` de Proxmox es el % restante** (97 = 3 % usado).
- **Un disco de segunda mano trae su historia:** el SSD del nodo principal llegó con 14,5 TB escritos y miles de horas; la lectura inicial (3 % usado) permite estimar la vida restante en lugar de suponerla.

## Método

- Anotar el **orden correcto** antes de abrir el equipo ahorra reaperturas.
- **Regla pendiente de aplicar:** probar la restauración, no solo hacer la copia (fase 2 del plan).
- Una nota por práctica: lo que no se escribe se olvida y se repite.
