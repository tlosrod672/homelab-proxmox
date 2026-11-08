# Proxmox homelab: 3-node cluster, secure remote access, home automation and an AI assistant

A personal systems and networking lab that I build and document step by step while studying **ASIR** (a Spanish higher vocational degree in Network Systems Administration). It is an engineering journal: what I did, why, what broke and how I fixed it. The notes are written in Spanish.

## What is running

- **Proxmox VE 9 cluster**, 3 nodes (one Lenovo mini-PC and two HP EliteDesk SFF), ZFS on each node's SSD, quorum of 3.
- **Secure remote access** with **Tailscale**: a subnet router inside an LXC container exposes the home LAN without opening any port.
- **A 24/7 Debian VM** running an AI assistant (Claude, by Anthropic) that helps me document, troubleshoot and schedule maintenance tasks, plus lightweight LXC containers and snapshots before every big change.
- **Home Assistant** (Zigbee, energy monitoring, wall tablet with a voice assistant).
- **Monthly SMART checks** of every disk, with a wear log.
- **Planned:** Windows Server with AD/DNS/DHCP and GPOs, backups, NAS, VLANs, monitoring and high availability (see the [lab plan](docs/plan-de-laboratorio.md)).

## Where to look

[`docs/arquitectura.md`](docs/arquitectura.md) · [`docs/decisiones.md`](docs/decisiones.md) · [`docs/lecciones-aprendidas.md`](docs/lecciones-aprendidas.md) · [`docs/evidencias.md`](docs/evidencias.md) · [`practicas/`](practicas) (one note per lab: goal, steps, warnings, review questions).

## Not done yet

Planned, not built: scheduled backups and tested restores, a Windows Server/AD lab domain, NAS, VLANs, monitoring, high availability and the second corosync link.

## Ground rules

No secrets in this repository: no passwords, tokens, keys or IP addresses. Rule I follow: snapshot before big changes (tested restores are still a pending goal). I use AI as a tool: an assistant (Claude, by Anthropic) drafts notes, reads cluster data through a read-only API token and runs maintenance tasks on a VM; I built the hardware, installed Proxmox, created the cluster, review and approve everything published, and verify AI-assisted results before accepting them.

Author: Tomás Losada Rodríguez · Desktop Support Technician · ASIR student · [LinkedIn](https://www.linkedin.com/in/tlosrod/) · [MIT](LICENSE)
