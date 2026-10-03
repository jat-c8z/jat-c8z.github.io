# Competencias demostradas

Qué sé hacer, dónde está la prueba y a qué módulo de ASIR corresponde. Nada que no haya hecho de verdad.

[← Volver a la portada](index.md)

## Virtualización

*Módulo: Implantación de Sistemas Operativos*

| Competencia | Prueba |
|---|---|
| Instalar y configurar un hipervisor de tipo 1 desde cero | [Instalar Proxmox VE desde cero](procedimientos/instalar-proxmox.md) |
| Elegir entre hipervisor de tipo 1 y tipo 2 según el caso | [Virtualización con Proxmox VE](proyectos/proxmox.md) |
| Crear y administrar máquinas virtuales | [Crear una VM en Proxmox](procedimientos/crear-vm.md) |
| Crear y administrar contenedores LXC | [Crear un contenedor LXC](procedimientos/crear-lxc.md) |
| Decidir entre VM y contenedor según el servicio | [IA local autoalojada](proyectos/ia-local.md) |
| Dimensionar CPU, RAM y disco a hardware limitado | [Virtualización con Proxmox VE](proyectos/proxmox.md) |
| Snapshots y estrategia de copias de seguridad | [Snapshots y copias de seguridad](procedimientos/snapshots-y-copias.md) |

## Sistemas Linux

*Módulo: Implantación de Sistemas Operativos*

| Competencia | Prueba |
|---|---|
| Instalar Debian en modo servidor, sin entorno gráfico | [Crear una VM en Proxmox](procedimientos/crear-vm.md) |
| Particionado y sistemas de ficheros (ext4, swap) | [Crear una VM en Proxmox](procedimientos/crear-vm.md) |
| Repositorios y paquetes con APT | [Instalar Proxmox VE desde cero](procedimientos/instalar-proxmox.md) |
| Servicios con systemd (`enable`, `status`, arranque automático) | [IA local autoalojada](proyectos/ia-local.md) |
| Acceso remoto por SSH | [Acceso remoto con Tailscale](proyectos/tailscale.md) |
| Diagnóstico de conectividad (`ip a`, `ping`, `curl`) | [Crear un contenedor LXC](procedimientos/crear-lxc.md) |

## Redes

*Módulo: Planificación y Administración de Redes*

| Competencia | Prueba |
|---|---|
| Direccionamiento IPv4, máscaras y tramos reservados | [Crear un contenedor LXC](procedimientos/crear-lxc.md) |
| Bridges de red en un hipervisor | [Glosario](referencia/glosario.md) |
| IP estática frente a DHCP, y cuándo cada una | [Virtualización con Proxmox VE](proyectos/proxmox.md) |
| Inventario de red: barrido, identificación por OUI, puertos | [Auditoría de seguridad](proyectos/auditoria.md) |
| Entender el NAT de operador y sus consecuencias de diseño | [Acceso remoto con Tailscale](proyectos/tailscale.md) |
| VPN en malla con WireGuard | [Acceso remoto con Tailscale](proyectos/tailscale.md) |

## Hardware

*Módulo: Fundamentos de Hardware*

| Competencia | Prueba |
|---|---|
| Inventariar hardware y comprobar capacidades reales | [Virtualización con Proxmox VE](proyectos/proxmox.md) |
| Configurar la BIOS: virtualización, arranque seguro, orden de arranque | [Instalar Proxmox VE desde cero](procedimientos/instalar-proxmox.md) |
| Identificar el cuello de botella real de un equipo | [Virtualización con Proxmox VE](proyectos/proxmox.md) |

## Seguridad

*Transversal*

| Competencia | Prueba |
|---|---|
| Auditoría de seguridad de puesto y red, con método repetible | [Auditoría de seguridad](proyectos/auditoria.md) |
| Scripts de recogida de datos en PowerShell | [Auditoría de seguridad](proyectos/auditoria.md) |
| Verificar correcciones contra la máquina, no contra la lista | [Auditoría de seguridad](proyectos/auditoria.md) |
| Endurecer el acceso remoto: 2FA, aprobación de equipos | [Acceso remoto con Tailscale](proyectos/tailscale.md) |
| Gestión de credenciales: gestor, 2FA y nada de credenciales en la documentación | Toda esta web |

## Documentación y método

| Competencia | Prueba |
|---|---|
| Documentación técnica separando histórico, estado y procedimiento | [Portada](index.md) |
| Procedimientos reproducibles con comprobación final | Todos los procedimientos |
| Dejar por escrito las decisiones y su porqué | Todas las fichas de proyecto |
| Reconocer y corregir un error propio en un informe | [Auditoría de seguridad](proyectos/auditoria.md) |

Esto es lo mismo que hago en mi trabajo tramitando expedientes: el criterio que no queda escrito no se puede repetir ni defender después.
