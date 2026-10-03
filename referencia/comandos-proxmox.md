# Comandos de Proxmox que uso

Todo desde el shell del hipervisor, por SSH o con el botón `Shell` de la interfaz.

[← Volver a la portada](../index.md)

## Ver qué hay

```bash
pveversion                 # versión de Proxmox
qm list                    # máquinas virtuales y su estado
pct list                   # contenedores y su estado
pvesm status               # almacenamientos y espacio libre
df -h                      # disco real del hipervisor
free -h                    # memoria
```

## Máquinas virtuales (`qm`)

```bash
qm start 101               # arrancar
qm shutdown 101            # apagado ordenado (necesita qemu-guest-agent)
qm stop 101                # corte de corriente, solo si no responde
qm reboot 101
qm config 101              # ver su configuración
qm destroy 101 --purge     # borrarla y quitarla de las tareas de copia
```

## Contenedores (`pct`)

Los mismos verbos, cambiando `qm` por `pct`.

```bash
pct start 102
pct shutdown 102
pct enter 102              # entrar al contenedor sin SSH, desde el hipervisor
pct exec 102 -- df -h      # ejecutar un comando dentro sin entrar
pct config 102
```

`pct enter` es la salvación cuando un contenedor se ha quedado sin red y no se puede entrar por SSH.

## Snapshots

```bash
qm snapshot 101 antes-de-X --description "qué voy a hacer"
qm listsnapshot 101
qm rollback 101 antes-de-X
qm delsnapshot 101 antes-de-X
```

Se borran el mismo día. Ver [Snapshots y copias de seguridad](../procedimientos/snapshots-y-copias.md).

## Copias de seguridad

```bash
vzdump 101 --storage <almacen-de-copias> --mode snapshot --compress zstd
qmrestore /ruta/al/fichero.vma.zst 901    # restaurar con ID nuevo, nunca encima
```

## Red

```bash
ip a                       # interfaces: la física sin IP, vmbr0 con la IP fija
cat /etc/network/interfaces
ifreload -a                # recargar la red sin reiniciar
```

`ifreload -a` con la red mal configurada te deja fuera del servidor. Hay que tener la consola física a mano antes de tocar nada de aquí.

## Mantenimiento

```bash
apt update && apt full-upgrade
pveam update                # actualizar la lista de plantillas LXC
pveam available             # plantillas descargables
pveam download local debian-13-standard_13.0-1_amd64.tar.zst
```

## Registros

```bash
journalctl -xe              # últimos errores del sistema
journalctl -u pveproxy      # log de la interfaz web
tail -f /var/log/syslog
```

## Documentación oficial que consulto

- [Documentación de Proxmox VE](https://pve.proxmox.com/pve-docs/)
- [Wiki de Proxmox](https://pve.proxmox.com/wiki/Main_Page)
- [Manual de instalación de Debian](https://www.debian.org/releases/stable/installmanual)
- [Documentación de Tailscale](https://tailscale.com/kb/)
- [Cómo funciona el NAT traversal de Tailscale](https://tailscale.com/blog/how-nat-traversal-works)
- [Ollama](https://ollama.com/) y [Open WebUI](https://docs.openwebui.com/)

Cuando algo no funciona: primero el mensaje de error literal en la documentación oficial, después el foro oficial, y solo entonces una búsqueda abierta.
