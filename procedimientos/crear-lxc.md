# Crear un contenedor LXC

Probado con Proxmox VE 9.2 el 11/09/2026. Deja un contenedor Debian con IP fija, listo para instalarle servicios. Es mi camino por defecto para cualquier cosa que no necesite kernel propio.

[← Volver a la portada](../index.md)

## Cuándo un LXC y cuándo una VM

| | LXC | VM |
|---|---|---|
| Kernel | El del hipervisor | Propio |
| Arranque | Segundos | Medio minuto |
| RAM y disco | Muy poco | Mucho más |
| Sirve para | Servicios: web, base de datos, Ollama | Practicar sistemas, otro SO, Windows |
| Aislamiento | Menor | Mayor |

Con 8 GB de RAM y 119 GB de disco: **LXC por defecto y VM solo cuando haga falta de verdad**.

## Antes de empezar

- Una plantilla descargada: `local → CT Templates → Templates` → buscar `debian-13-standard` → `Download`.
- Una IP libre del tramo reservado para el laboratorio.
- Un ID libre, en la misma serie que las VMs.
- Tiempo: 10 minutos.

## Pasos

1. **`Create CT`** arriba a la derecha.
2. **General:** ID, hostname en minúsculas y contraseña del administrador (al gestor de contraseñas). Dejar **unprivileged container** marcado: el administrador de dentro no es el del hipervisor.
3. **Template:** la plantilla descargada.
4. **Disks:** el tamaño justo. Un contenedor con un par de servicios vive con 8-16 GB; si va a guardar modelos de IA, hace falta más.
5. **CPU:** 1-2 núcleos.
6. **Memory:** según el servicio. Swap a 512 MB.
7. **Network:**
    - Bridge `vmbr0`
    - IPv4: **Static**, con la IP elegida y su máscara (`/24`)
    - Gateway: la puerta de enlace de tu red
    - IPv6: estático sin dirección, o desactivado
8. **DNS:** el de tu red, o heredado del host.
9. **Confirm**, con `Start after created` marcado.
10. **Entrar** por `Console`, o con `pct enter <ID>` desde el shell del hipervisor.
11. **Actualizar y dejarlo listo:**
    ```
    apt update && apt upgrade -y
    apt install -y curl sudo
    ```
12. **Arranque automático,** si es un servicio que tiene que estar siempre: `Options → Start at boot → Yes`. Sin esto, tras un corte de luz no vuelve solo.

## Comprobación final

Desde el contenedor:

```
ip a                        # tiene la IP fija
ping -c3 <puerta-de-enlace>
ping -c3 8.8.8.8
```

Y desde otro equipo de la red, un `ping` a la IP del contenedor.

## Errores que ya me he comido

| Síntoma | Causa | Arreglo |
|---|---|---|
| Sin internet dentro del contenedor | Gateway sin poner al configurar la IP estática | `Network → eth0` → rellenar Gateway |
| No resuelve nombres pero sí hace ping a IPs | DNS mal puesto | `DNS` → el servidor DNS de tu red |
| Tras reiniciar el host, el contenedor está parado | Falta `Start at boot` | Paso 12 |

## Si hay que deshacerlo

`Stop` → `More → Remove`, con **Purge** marcado.
