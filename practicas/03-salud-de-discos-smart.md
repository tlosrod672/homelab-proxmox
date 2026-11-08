# Práctica 03 · Salud de los discos con SMART

**Fecha:** octubre-noviembre de 2026 · **Frecuencia:** primera lectura al instalar cada equipo y, después, **una vez al mes**.

## Objetivo
Conocer el estado y el ritmo de desgaste de todos los discos del laboratorio, sobre todo de los SSD con ZFS (que escribe algo más que ext4), y detectar a tiempo cualquier señal de fallo.

## Cómo leerlo
- **Interfaz web de Proxmox:** nodo → *Disks* → elegir disco → *Show S.M.A.R.T. values*.
- **Línea de comandos:** `smartctl -a /dev/nvme0n1` (NVMe) o `smartctl -a /dev/sda` (SATA).
- **API de Proxmox** con un token de **solo lectura** (rol de auditor) para automatizar la lectura mensual.

## Qué anotar
- **NVMe:** `Percentage Used`, `Data Units Written`, `Power On Hours`, `Media and Data Integrity Errors`, `Available Spare`.
- **SSD SATA:** atributo de desgaste según marca, `Total_LBAs_Written`, `Power_On_Hours`, `Reallocated_Sector_Ct`.
- **HDD:** `Reallocated_Sector_Ct`, `Current_Pending_Sector`, `Offline_Uncorrectable`, `Power_On_Hours`. Cualquier valor mayor que 0 en los tres primeros = vigilar de cerca.

## Señales de alarma
Desgaste que sube más de un 1 % al mes, errores de integridad mayores que 0, sectores reasignados o pendientes que crecen o `Available Spare` bajando. Si ocurre: revisar las copias y planificar el reemplazo.

## Primeras lecturas (resumen)
| Disco | Lectura inicial |
|---|---|
| SSD M.2 256 GB del nodo principal (segunda mano, económico) | **3 % usado** con 14,5 TB escritos y ~19 000 horas → vida estimada muy superior a la supuesta (ESTIMADO) |
| SSD WD Blue 500 GB | Desgaste real bajo (ESTIMADO, sin verificar en la ficha del fabricante); el atributo 230 engaña (ver abajo) |
| SSD Crucial BX500 500 GB | Casi nuevo (~2 000 horas) |
| HDD WD Blue 500 GB (×2) | Sanos: 0 sectores reasignados ni pendientes; uno con ~4,4 años de encendido |
| Lectura de noviembre (5 discos de los 3 nodos) | **Sin alarmas**, desgaste igual que en octubre |

## Lecciones de lectura
- El atributo **230** del WD Blue SSD no es desgaste (valor empaquetado; Proxmox puede mostrar un 95 % engañoso). Mirar el atributo 173 (ciclos medios de borrado) y el 241 (GiB escritos).
- En el Crucial BX500, el **202** es el % de vida usado y el **246** son LBA escritos × 512 B.
- En NVMe, el `wearout` de Proxmox es el % **restante**.
- Identificar los discos por **modelo**, no por letra (`sda`/`sdb` cambian entre arranques).
- Una fila nueva por lectura, sin borrar las anteriores: así se ve el ritmo.

## Preguntas de repaso
1. ¿Por qué ZFS puede desgastar más un SSD que ext4?
2. ¿Qué tres atributos de un HDD son señales de deterioro que hay que vigilar?
3. ¿Por qué conviene guardar cada lectura en lugar de sobrescribir la anterior?
