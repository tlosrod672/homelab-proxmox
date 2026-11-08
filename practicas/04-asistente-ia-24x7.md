# Práctica 04 · Asistente de IA 24/7 en una VM y base de conocimiento en Markdown

**Fecha:** octubre-noviembre de 2026 · **Estado:** en marcha

## Objetivo
Tener un asistente de IA (Claude, de Anthropic) accesible desde el móvil o el portátil, alojado en el laboratorio, que me ayude a **documentar, diagnosticar y mantener** el homelab, con un coste de uso bajo gracias a una buena organización de la información.

## Piezas
1. **Base de conocimiento en Markdown y Git** (privada): una carpeta por área, un índice que lleva a cada nota y reglas cortas por proyecto. El asistente lee primero el índice y solo abre lo necesario, lo que ahorra contexto y evita que la información se duplique.
2. **VM Debian 13** dedicada en un nodo (4 vCPU, 8 GB de RAM, 40 GB en ZFS, arranque automático y snapshot de partida). Corre la herramienta de línea de comandos del asistente como **servicio** (`systemd`) con acceso remoto desde la aplicación del móvil.
3. **Conectores:** Proxmox con un token de **solo lectura**, Home Assistant, correo y calendario con confirmación para acciones que envíen o borren, y avisos por Telegram.
4. **Skills** (instrucciones reutilizables): revisión mensual de la base de conocimiento, diagnóstico del laboratorio y una vigilancia semanal de novedades.
5. **Hooks y comprobaciones automáticas:** al empezar cada sesión se actualiza el repositorio y se ejecuta un detector de datos desfasados que avisa si hay notas que contradicen la realidad.

## Principios de seguridad
- **Permisos amplios en la VM, mitigaciones fuera de ella:** el asistente trabaja con un usuario de administración con `sudo` (decisión consciente en una máquina de laboratorio aislada); la mitigación real es que el token de Proxmox sea de **solo lectura**, que no haya secretos en archivos ni en el repositorio y que las acciones sensibles pidan confirmación.
- **Confirmación humana** antes de acciones irreversibles o que salen del laboratorio (enviar mensajes, borrar, comprar).
- **Snapshot** de la VM antes de cambios grandes.
- **Verificación:** una IA revisa mal su propio trabajo, así que antes de dar algo por hecho se comprueba el resultado (ejecutar, releer, consultar la fuente).

## Qué he aprendido
- La calidad depende de la **organización de la información** más que del modelo: índices cortos, un hecho en un solo sitio y reglas claras.
- Los datos **se quedan viejos**: un detector automático de incoherencias y una regla de "actualizar en todos los sitios" evitan que el conocimiento se desfase.
- La IA es una herramienta para ir más rápido y documentar mejor; **entender lo que se hace sigue siendo mío**.

## Pendiente
- Copia de seguridad de la VM (`vzdump`) a otro destino (regla 3-2-1).
- Segunda red (VLAN) para aislar el asistente del resto.

## Preguntas de repaso
1. ¿Por qué un token de solo lectura es mejor que uno con todos los permisos para este uso?
2. ¿Qué ventaja tiene indexar la información por capas frente a un único documento enorme?
3. ¿Por qué se hace un snapshot antes de cambios grandes?
