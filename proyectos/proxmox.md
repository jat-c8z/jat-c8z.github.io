# Virtualización con Proxmox VE

**Septiembre 2026 · En marcha**

[← Volver a la portada](../index.md)

## El problema

Necesitaba un entorno para practicar administración de sistemas y redes para ASIR, con varias máquinas a la vez, sin gastar dinero y sin depender de tener el PC principal encendido.

## Restricciones

Hardware disponible: un portátil de 2015 con i3-5005U de dos núcleos, 8 GB de RAM y un SSD de 128 GB. Presupuesto: cero.

Lo primero fue medir de qué disponía de verdad, no suponerlo:

| Comprobado | Resultado |
|---|---|
| RAM | 8 GB (7,7 GiB reales) |
| Disco | SSD de 128 GB (119 GB útiles) |
| VT-x | Activa |
| Tarjeta de red por cable | `enp2s0`, funcionando |

**Conclusión: el techo es el disco, no la RAM.** Esa conclusión condicionó todas las decisiones posteriores.

## Qué monté

Proxmox VE 9.2 sobre el portátil, con IP fija y conexión por cable. Encima, una VM con Debian 13 mínimo y un contenedor LXC con servicios.

## Decisiones y por qué

| Decisión | Alternativa descartada | Por qué |
|---|---|---|
| Proxmox VE (tipo 1) | VirtualBox, VMware Workstation (tipo 2) | Va directo sobre el hardware; con 8 GB no puedo gastar RAM en un escritorio |
| Proxmox | VMware ESXi | ESXi retiró la licencia gratuita |
| Proxmox | Hyper-V | Requiere Windows, y Proxmox integra VMs y LXC en la misma interfaz |
| ext4 | ZFS | ZFS necesita mucha RAM para caché y con un solo disco no aporta redundancia |
| Conexión por cable | Wi-Fi | El bridge sobre Wi-Fi falla: la mayoría de tarjetas no dejan pasar tramas con MAC ajena |
| IP fija en la máquina | Reserva DHCP en el router | Si cambio de router no pierdo el direccionamiento |
| LXC por defecto, VM cuando hace falta | Todo en VMs | El LXC comparte kernel: fracción de RAM y disco. Con 119 GB, decisivo |

## La VM de prácticas

Debian 13 desde la ISO *netinst*, 2 vCPU, 1 GiB de RAM y 16 GiB de disco con ext4 y swap. En la instalación solo marqué **servidor SSH** y **utilidades estándar**: nada de escritorio. Una máquina de servidor se instala vacía y se le añade lo que hace falta. Menos superficie de ataque, menos disco y menos actualizaciones.

Es VM y no contenedor porque ahí practico arranque, particiones y kernel, y para eso necesita kernel propio.

## Obstáculos resueltos

1. **`apt update` devolvía 401.** La instalación apunta al repositorio *enterprise*, que exige suscripción de pago. Sin cambiarlo a `pve-no-subscription`, el sistema no se actualiza nunca. Es lo primero que hago ahora en cualquier instalación.
2. **El instalador rechazaba el hostname.** Exige FQDN: tiene que llevar punto.
3. **Proxmox no mostraba la IP de la VM.** Faltaba `qemu-guest-agent` dentro de la máquina y activarlo en las opciones de la VM. Sin él, el apagado desde la interfaz es un corte de corriente y los snapshots no quedan consistentes.

## Resultado

Hipervisor funcionando desde el 03/09/2026 con dos máquinas. Procedimientos escritos y probados para [instalar el hipervisor](../procedimientos/instalar-proxmox.md), [crear una VM](../procedimientos/crear-vm.md) y [crear un contenedor](../procedimientos/crear-lxc.md): cualquiera de los tres se repite sin volver a investigar.

## Qué falta y lo sé

Las copias de seguridad. Hoy no existe ninguna: si se rompe el SSD se pierde el laboratorio entero. El plan está escrito (respaldo programado contra el NAS de casa, en modo snapshot, con retención corta por espacio y **restauración probada antes de darlo por bueno**) pero sin implantar. Ver [Snapshots y copias de seguridad](../procedimientos/snapshots-y-copias.md).

## Lo siguiente

| # | Qué | Módulo de ASIR |
|---|---|---|
| 1 | Copias de seguridad automáticas al NAS | Implantación de Sistemas Operativos |
| 2 | Windows Server en VM con Active Directory | Implantación de Sistemas Operativos |
| 3 | DNS y DHCP internos en la VM Debian | Planificación y Administración de Redes |
| 4 | Servidor web y MariaDB en un LXC | Gestión de Bases de Datos, Lenguajes de Marcas |
| 5 | Segmentación con VLANs | Planificación y Administración de Redes |
| 6 | Monitorización básica de disco con avisos | — |

## Módulos de ASIR que toca

Implantación de Sistemas Operativos, Fundamentos de Hardware, Planificación y Administración de Redes.
