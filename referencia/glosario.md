# Glosario

Términos con su definición en corto y, cuando hace falta, por qué me importan en este laboratorio.

[← Volver a la portada](../index.md)

**Bridge (`vmbr0`)**: switch virtual dentro del hipervisor. Conecta las tarjetas de red virtuales de las máquinas con la tarjeta física, de forma que cada VM aparece en la red de casa como un equipo más, con su propia IP.

**CGNAT / NAT de operador**: el operador comparte una IP pública entre varios clientes. La IP que recibe el router está en `100.64.0.0/10`, que no es pública. Consecuencia práctica: no se pueden recibir conexiones entrantes ni abrir puertos. Por eso uso [Tailscale](../proyectos/tailscale.md).

**Contenedor LXC**: aislamiento a nivel de sistema operativo. El contenedor comparte el kernel del hipervisor en vez de llevar el suyo. Arranca en segundos y ocupa una fracción de lo que ocupa una VM. A cambio, menos aislamiento, y no sirve para practicar nada de kernel.

**Cuantización**: reducir la precisión numérica de los pesos de un modelo de IA (de 16 bits a 4, por ejemplo) para que ocupe menos memoria. Es lo que permite correr un modelo en una CPU vieja, a costa de algo de calidad.

**ext4**: sistema de ficheros por defecto en Linux. Fiable y ligero. Lo elegí frente a ZFS porque ZFS se come varios GB de RAM en caché y, con un solo disco, no hay redundancia que ganar.

**FQDN**: nombre completo de una máquina, con dominio. El instalador de Proxmox lo exige.

**Hipervisor de tipo 1 (bare metal)**: se instala directamente sobre el hardware, sin sistema operativo debajo. Proxmox, ESXi, Hyper-V Server. No gasta recursos en un escritorio.

**Hipervisor de tipo 2 (alojado)**: corre como un programa dentro de un sistema operativo normal. VirtualBox, VMware Workstation. Más cómodo, más lento y más consumo.

**KVM**: el módulo de virtualización del kernel de Linux. Es lo que hace el trabajo real por debajo de Proxmox. Necesita VT-x o AMD-V activo en la BIOS.

**MAC aleatorizada**: los móviles modernos cambian su dirección MAC en cada red para que no se les pueda seguir. Por eso el filtrado MAC no sirve como medida de seguridad ni como método de inventario.

**OUI**: los tres primeros bytes de una MAC, que identifican al fabricante. **Identifican al fabricante, no al dispositivo**: esa distinción es la que me falló en la [auditoría](../proyectos/auditoria.md).

**Proxmox VE**: distribución basada en Debian que integra hipervisor KVM, contenedores LXC, almacenamiento y copias de seguridad en una interfaz web. Libre.

**`qemu-guest-agent`**: servicio que se instala dentro de la VM para que el hipervisor pueda hablar con ella: ver su IP, apagarla de forma ordenada y congelar el sistema de ficheros antes de un snapshot.

**QCOW2**: formato de disco virtual que crece según se usa y admite snapshots. RAW es más rápido pero no los permite.

**Snapshot**: foto del estado de una máquina, guardada en el mismo disco. Sirve para deshacer un cambio propio. **No es una copia de seguridad**: si muere el disco, se va con él.

**Unprivileged container**: contenedor LXC donde el administrador de dentro no es el administrador del hipervisor. Es la opción segura por defecto.

**VT-x**: la extensión de virtualización de los procesadores Intel. Sin ella activada en la BIOS, las VMs no arrancan.

**VirtIO**: controladores paravirtualizados. La VM sabe que es una VM y habla directamente con el hipervisor en vez de fingir que tiene una tarjeta de red o un disco físico. Mucho más rápido.

**VZDump**: la herramienta de copias de seguridad de Proxmox. En modo *snapshot* respalda la máquina en caliente, sin pararla.

**WireGuard**: protocolo de VPN moderno, rápido y con poco código. Es lo que hay por debajo de Tailscale.
