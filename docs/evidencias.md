# Evidencias

Datos reales del laboratorio, anonimizados (sin IP ni nombres de red).

## Estado del clúster (lectura por API de solo lectura, 08-11-2026)

```
Clúster: homelab · Quórum: OK · Nodos: 3

pve1   ONLINE   12 núcleos   RAM 13,2 / 62,2 GB (21 %)
pve2   ONLINE    4 núcleos   RAM  2,1 / 31,2 GB (7 %)
pve3   ONLINE    4 núcleos   RAM  2,2 / 31,2 GB (7 %)
(los tres con ~1 día de uptime en ese momento)
```

## Lecturas SMART

Registro mensual completo en la [práctica 03](../practicas/03-salud-de-discos-smart.md). Resumen de octubre y noviembre:

| Equipo | Disco | Resultado |
|---|---|---|
| Nodo principal | SSD M.2 256 GB | 3 % de desgaste, 14,5 TB escritos, 0 errores de integridad |
| Nodo 2 | SSD 500 GB + HDD 500 GB | 0 sectores reasignados ni pendientes |
| Nodo 3 | SSD 500 GB + HDD 500 GB | 0 sectores reasignados ni pendientes |
| Lectura de noviembre | 5 discos de los 3 nodos | Sin alarmas; desgaste igual que en octubre |

## Pendiente de añadir

- Salida anonimizada de `pvecm status` (sin IP) y captura del panel de Proxmox.
- Resultado de una prueba de fallo de quórum documentada como práctica con método.
