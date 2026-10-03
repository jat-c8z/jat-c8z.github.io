# Instalar Proxmox VE desde cero

Probado en un Lenovo ideapad 100-15IBD con Proxmox VE 9.2 el 03/09/2026. Deja un equipo con Proxmox funcionando, IP fija y repositorio correcto. **Borra el disco entero.**

[← Volver a la portada](../index.md)

## Antes de empezar

- ISO de Proxmox VE desde la web oficial de descargas.
- Un pendrive de 2 GB o más. Se borra.
- Rufus o Ventoy para grabarlo, en modo **DD**, no ISO.
- Cable de red conectado al router. El instalador no configura Wi-Fi.
- Una IP libre del tramo que tengas reservado para el laboratorio.
- La contraseña del administrador generada antes en el gestor de contraseñas, no improvisada.
- Tiempo: 30-40 minutos.

## Pasos

1. **Activar la virtualización en la BIOS.** En el ideapad, F2 al arrancar → `Configuration` → **Intel Virtual Technology: Enabled**. Sin esto Proxmox se instala pero las VMs no arrancan.
2. **Desactivar el arranque seguro** (Secure Boot) y poner el pendrive el primero en el orden de arranque.
3. **Grabar la ISO** en el pendrive con Rufus en modo DD.
4. **Arrancar del pendrive** y elegir *Install Proxmox VE (Graphical)*.
5. **Aceptar la licencia.**
6. **Elegir el disco de destino.** Aquí se borra todo. Con un solo disco y sin redundancia posible, el sistema de ficheros es **ext4**; ZFS necesita mucha más RAM.
7. **País, zona horaria y teclado:** España / Europe-Madrid / Spanish.
8. **Contraseña del administrador y correo.** El correo es donde Proxmox manda los avisos.
9. **Red**, el paso que importa:
    - Interfaz: la tarjeta de cable (`enp2s0` en este equipo).
    - Hostname: un FQDN, con punto. Sin punto el instalador lo rechaza.
    - Dirección IP: la IP fija elegida, con su máscara (`/24`).
    - Puerta de enlace y DNS: los de tu red.
10. **Revisar el resumen e instalar.** Al terminar, quitar el pendrive y reiniciar.
11. **Entrar por web** desde otro equipo de la red, por `https://` y el puerto que indica la pantalla final del instalador. El aviso de certificado no fiable es normal: el certificado es autofirmado.
12. **Cambiar el repositorio a `pve-no-subscription`.** Sin esto no se actualiza nunca. En la interfaz: `Updates → Repositories` → deshabilitar los repositorios `enterprise` y añadir el `No-Subscription`. Luego `Updates → Refresh` y `Upgrade`.

## Comprobación final

Por SSH o desde la consola:

```
pveversion          # debe decir pve-manager/9.2
ip a                # enp2s0 sin IP, vmbr0 con la IP fija
apt update          # tiene que terminar sin errores 401
df -h               # cuánto disco queda de verdad
```

Si `apt update` da un 401, el repositorio *enterprise* sigue activo: vuelve al paso 12.

## Errores que ya me he comido

| Síntoma | Causa | Arreglo |
|---|---|---|
| El instalador no acepta el hostname | Falta el punto | Poner un FQDN, con dominio |
| `apt update` devuelve 401 | Repositorio *enterprise* activo sin suscripción | Cambiar a `pve-no-subscription` |
| Las VMs no arrancan | VT-x desactivada en BIOS | Activarla y reiniciar |
| Aviso de «no valid subscription» al entrar | Normal con el repositorio gratuito | Se ignora, no afecta a nada |

## Si hay que deshacerlo

No hay vuelta atrás: el paso 6 borra el disco. Lo que hubiera antes tiene que estar respaldado **antes** de empezar.
