# Práctica 02 · Subnet router de Tailscale en un LXC de Proxmox

**Fecha:** octubre de 2026

## Objetivo
Llegar desde el portátil, fuera de casa, a cualquier equipo de la red local (Proxmox, router…) **sin abrir puertos** y sin instalar Tailscale en cada equipo.

## Resultado
Funciona: desde el portátil, fuera de la red local, cargaron la interfaz web de Proxmox y la del router a través del túnel (probado el 27-10-2026).

## Conceptos
- **LXC:** contenedor que comparte el núcleo del host; más ligero que una VM. Se crea desde una **plantilla**, no desde una ISO.
- **Sin privilegios (*unprivileged*):** el root del contenedor es un usuario normal en el host, por lo que es más seguro.
- **`/dev/net/tun`:** dispositivo de Linux para crear interfaces de red por software (Tailscale crea `tailscale0`); hay que pasárselo al contenedor.
- **Reenvío IP (`ip_forward`):** Linux no pasa tráfico entre interfaces por defecto; un router lo necesita.
- **Subnet router:** nodo de Tailscale que anuncia una red local (`--advertise-routes`). Un administrador debe **aprobar la ruta** en el panel (medida de seguridad).

## Pasos
1. En el nodo: almacén `local` → *CT Templates* → descargar la plantilla de **Debian 13**.
2. Crear el CT: **sin privilegios** y con *nesting*, plantilla Debian 13, disco de **4 GiB** en `local-zfs`, **1 núcleo**, **512 MiB** de RAM + 512 MiB de swap, red `eth0` en `vmbr0` con una IP fija de la red local, sin arrancar.
3. En el CT: *Resources → Add → Device Passthrough* con `/dev/net/tun`; *Options → Start at boot = Yes*; arrancar.
4. En la consola del CT (como root):
   ```bash
   apt update && apt full-upgrade -y
   ls -l /dev/net/tun
   apt install -y curl
   curl -fsSL https://pkgs.tailscale.com/stable/debian/trixie.noarmor.gpg -o /usr/share/keyrings/tailscale-archive-keyring.gpg
   curl -fsSL https://pkgs.tailscale.com/stable/debian/trixie.tailscale-keyring.list -o /etc/apt/sources.list.d/tailscale.list
   apt update && apt install -y tailscale
   echo 'net.ipv4.ip_forward = 1' > /etc/sysctl.d/99-tailscale.conf
   sysctl -p /etc/sysctl.d/99-tailscale.conf
   tailscale up --advertise-routes=<RED_LOCAL>/24 --accept-dns=false
   tailscale status
   ```
5. En el panel de Tailscale: *Machines → (el contenedor) → Edit route settings* → **aprobar la ruta**; desactivar la caducidad de la clave del nodo.
6. En un equipo que ya está dentro de la red local (p. ej., un PC de sobremesa con Tailscale), **desmarcar** "usar rutas de Tailscale" para que no dé el rodeo por el contenedor. En el portátil que se usa fuera de casa, marcado.

## Avisos
- `tailscale up` muestra dos avisos que **no son errores**: "IPv6 forwarding is disabled" (solo uso IPv4) y "UDP GRO forwarding is suboptimally configured" (optimización de rendimiento).
- Si el portátil está en otra red con el **mismo rango**, la ruta choca: usar entonces las direcciones propias de Tailscale.
- Si el nodo que aloja el contenedor está apagado, el subnet router cae; los equipos con Tailscale propio siguen siendo accesibles directamente.
- **Sin quórum** (dos nodos apagados) el contenedor no arranca: ver [lecciones aprendidas](../docs/lecciones-aprendidas.md).

## Preguntas de repaso
1. ¿Por qué un LXC sin privilegios es más seguro que uno privilegiado?
2. ¿Qué hace `ip_forward` y por qué lo necesita un subnet router?
3. ¿Por qué hay que aprobar la ruta en el panel de Tailscale?
4. ¿Qué ventaja tiene un subnet router frente a instalar Tailscale en cada equipo?
