# Crear una VM en Proxmox

Probado con Proxmox VE 9.2 y Debian 13 el 03/09/2026. Deja una máquina virtual instalada, en red y con el agente funcionando.

[← Volver a la portada](../index.md)

## Antes de empezar

- Espacio en disco suficiente. Comprobarlo primero con `df -h`: en mi laboratorio es el recurso escaso.
- La ISO del sistema subida a Proxmox: `local → ISO Images → Upload`.
- Un ID libre. Mi convenio: 1xx para máquinas, y el siguiente número libre.
- Tiempo: 20-30 minutos con Debian netinst.

## Pasos

1. **`Create VM`** arriba a la derecha.
2. **General:** VM ID y nombre en minúsculas y sin espacios.
3. **OS:** la ISO subida. Type `Linux`, Version `6.x - 2.6 Kernel`.
4. **System:** por defecto, pero marcando **QEMU Agent**. Luego hay que instalarlo dentro, pero si no se marca aquí Proxmox no lo escucha.
5. **Disks:** tamaño ajustado a lo que va a hacer. Un Debian mínimo de servidor vive con 15-20 GB. Formato QCOW2 si quiero snapshots.
6. **CPU:** 1-2 núcleos. En un i3 de 2 núcleos, darle 4 a una VM no la hace más rápida: la ralentiza por espera de planificación.
7. **Memory:** 1-2 GB para un Debian sin escritorio. Hay que dejar RAM para el hipervisor y las demás máquinas.
8. **Network:** bridge `vmbr0`, modelo `VirtIO (paravirtualized)`. VirtIO es un driver que sabe que está en una VM: mucho más rápido que emular una tarjeta física.
9. **Confirm** y arrancar.
10. **Instalar el sistema** por la consola de la VM. Para Debian netinst:
    - Idioma, ubicación y teclado.
    - Nombre de la máquina y dominio (se puede dejar vacío).
    - Contraseñas del administrador y del usuario normal, las dos al gestor de contraseñas.
    - Particionado: **guiado, todo el disco**, todos los ficheros en una partición. Debian crea ext4 y swap.
    - En `tasksel`, **desmarcar el entorno de escritorio** y dejar solo **servidor SSH** y **utilidades estándar del sistema**.
    - GRUB en el disco principal (`/dev/sda`).
11. **Instalar el agente** dentro de la VM:
    ```
    apt update && apt install qemu-guest-agent
    systemctl enable --now qemu-guest-agent
    ```
12. **Reiniciar la VM** para que Proxmox la vea con el agente activo.

## Comprobación final

En el resumen de la VM en Proxmox tiene que aparecer su **IP**. Si aparece, el agente funciona. Si pone «Guest Agent not running», falta el paso 11 o el reinicio.

Dentro de la VM:

```
ip a                       # tiene IP de la red local
ping -c3 <puerta-de-enlace> # llega al router
ping -c3 8.8.8.8           # llega a internet
```

## Por qué el agente importa

1. Proxmox ve la IP de la máquina en el resumen.
2. El apagado desde la interfaz es un apagado ordenado, no un corte de corriente.
3. Los snapshots pueden congelar el sistema de ficheros antes de tirar la foto, así que quedan consistentes.

## Errores que ya me he comido

| Síntoma | Causa | Arreglo |
|---|---|---|
| La VM no arranca, error de KVM | VT-x desactivada en BIOS | Activarla |
| Sin red dentro de la VM | Bridge mal elegido o tarjeta no VirtIO | `Hardware → Network Device`, bridge `vmbr0` |
| Proxmox no muestra la IP | Falta `qemu-guest-agent` o el reinicio | Paso 11 y reiniciar |
| Se acaba el disco al crearla | No comprobé el espacio antes | `df -h` antes de empezar, siempre |

## Si hay que deshacerlo

`Stop`, luego `More → Remove`, con **Purge** marcado para que salga también de las tareas de copia. El disco se libera al momento.
