# Laboratorio ASIR de Julián

Estudio ASIR (Administración de Sistemas Informáticos en Red) mientras trabajo tramitando expedientes de subvenciones. Para practicar he montado en casa un laboratorio de virtualización sobre un portátil de 2015 reconvertido. Aquí está documentado: qué monté, por qué lo decidí así, qué falló y qué falta.

## Qué hay montado

| Capa | Qué |
|---|---|
| Hardware | Lenovo ideapad 100-15IBD: i3-5005U, 8 GB de RAM, SSD de 128 GB |
| Hipervisor | Proxmox VE 9.2 (tipo 1, sobre Debian) |
| Máquina virtual | Debian 13 mínimo, con SSH y `qemu-guest-agent` |
| Contenedor | LXC con Ollama y Open WebUI, modelo de 3B |
| Acceso remoto | Tailscale (WireGuard en malla), sin puertos abiertos |

En marcha desde septiembre de 2026.

## Proyectos

- [Virtualización con Proxmox VE](proyectos/proxmox.md): el hipervisor, las decisiones y los tres obstáculos del primer día.
- [IA local autoalojada](proyectos/ia-local.md): un modelo de lenguaje en un contenedor, sin que nada salga de casa.
- [Acceso remoto con Tailscale detrás de CGNAT](proyectos/tailscale.md): llegar al laboratorio desde fuera sin IP pública y sin abrir puertos.
- [Auditoría de seguridad de la red de casa](proyectos/auditoria.md): método, tipo de hallazgos y el error que cometí.

## Procedimientos probados

- [Instalar Proxmox VE desde cero](procedimientos/instalar-proxmox.md)
- [Crear una VM en Proxmox](procedimientos/crear-vm.md)
- [Crear un contenedor LXC](procedimientos/crear-lxc.md)
- [Snapshots y copias de seguridad](procedimientos/snapshots-y-copias.md)

## Referencia

- [Comandos de Proxmox que uso](referencia/comandos-proxmox.md)
- [Glosario](referencia/glosario.md)
- [Competencias demostradas](competencias.md)

## Las cuatro decisiones que defiendo

1. **Hipervisor de tipo 1 y no de tipo 2.** En un equipo con 8 GB, gastar 2 en un escritorio que nadie mira es la diferencia entre que quepan dos máquinas o ninguna.
2. **Contenedores LXC por defecto, máquinas virtuales solo cuando hacen falta.** Un LXC comparte kernel, arranca en segundos y ocupa una fracción. La máquina donde practico particiones y arranque sí tiene que ser VM.
3. **Acceso remoto por malla cifrada, no por puertos abiertos.** Mi línea va detrás de NAT de operador y abrir puertos no funcionaría. Lo elegiría igual con IP pública: un puerto que no existe no se puede atacar.
4. **Auditar la red antes de seguir construyendo.** Montar un laboratorio sobre una red que no conoces es montarlo sobre arena.

## Lo que reconozco que falta

**No hay copias de seguridad del laboratorio todavía.** Si muere el SSD, se pierde todo. El plan está escrito en [Snapshots y copias de seguridad](procedimientos/snapshots-y-copias.md), con restauración probada como condición para darlo por bueno, pero sin implantar. Es lo siguiente.

## Cómo lo documento

Tres notas por cada cosa que monto: el **diario** de lo que pasó ese día con los errores incluidos, la **ficha** del estado actual de cada máquina y el **procedimiento** limpio para repetirlo. Es lo mismo que hago en mi trabajo con los expedientes: si no queda escrito por qué se decidió algo, no está terminado.

Lo que publico aquí sale de esas notas, sin contraseñas, direcciones de mi red ni nombres de usuario.
