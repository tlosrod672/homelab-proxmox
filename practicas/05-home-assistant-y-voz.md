# Práctica 05 · Home Assistant y asistente de voz

**Fecha:** octubre-noviembre de 2026

## Objetivo
Domótica y paneles útiles en casa, con un asistente de voz propio, sin depender de cuentas de terceros más de lo necesario.

## Piezas
- **Home Assistant OS** en un mini-PC independiente del clúster, con **Zigbee** (adaptador USB SONOFF ZBDongle-E con ZHA), integración con enchufes de medición de energía y copias automáticas diarias a un almacenamiento externo.
- **Panel en una tablet Android de pared** con una vista ligera de Home Assistant.
- **Encendido y apagado de los nodos de Proxmox desde Home Assistant** (Wake-on-LAN para encender).
- **Asistente de voz en la tablet:** palabra de activación en el propio dispositivo → reconocimiento de voz → modelo de lenguaje → síntesis de voz (los proveedores son intercambiables). Latencia de unos cinco segundos desde la palabra de activación hasta la respuesta (medida el 02-11-2026; los proveedores han cambiado desde entonces).

## Configuración de la tablet por ADB
La tablet se configura por **ADB** (Android Debug Bridge, con la depuración USB activada): tiempo de apagado de pantalla, permisos de la app de Home Assistant para controlar brillo y apagado, y sensores que la app expone (cargando, brillo, sensor de luz, pantalla activa). Las automatizaciones encienden la pantalla al entrar en la habitación y bajan el brillo por la noche.

## Lecciones
- **Forzar el cierre de la app de Home Assistant en la tablet le quita el papel de asistente predeterminado:** hay que volver a asignarlo con `cmd role add-role-holder android.app.role.ASSISTANT <paquete>`.
- **Una vista de panel pesada gasta CPU:** quitar vídeo en directo y animaciones bajó el uso de CPU de la tablet del 150 % a casi 0 %. Un panel debe ser ligero.
- **Sospecho que una palabra de activación experimental retrasaba el micro** (no confirmado): conviene medir tiempos antes de culpar al modelo o a la red.
- **Los servicios gratuitos o no oficiales pueden dejar de funcionar sin aviso** (me pasó con una voz de síntesis y con un modelo gratuito): conviene tener alternativas.
- **Un detalle de seguridad:** las credenciales y claves no van a archivos ni a este repositorio; se guardan en un gestor de contraseñas.

## Pendiente
- Satélite de voz físico (altavoz de conferencia USB + un servicio en una VM) para otras habitaciones.
- Panel de energía con el consumo del propio rack.

## Preguntas de repaso
1. ¿Por qué se configura la tablet por ADB en lugar de a mano?
2. ¿Qué componentes forman un asistente de voz y cuál introduce más latencia?
3. ¿Por qué es mejor un panel ligero en un dispositivo siempre encendido?
