# Snapshots y copias de seguridad

**Estado: los snapshots los uso; las copias de seguridad están planificadas, sin implantar.** Hoy no existe ninguna copia del laboratorio. Es el riesgo abierto más serio que tengo, y por eso es lo siguiente de la lista.

[← Volver a la portada](../index.md)

## Snapshot y copia de seguridad no son lo mismo

| | Snapshot | Copia de seguridad |
|---|---|---|
| Qué es | Una foto del estado, guardada **en el mismo disco** | Un fichero completo, en **otro sitio** |
| Para qué | Deshacer un cambio que acabo de hacer | Sobrevivir a que se rompa el disco |
| Si muere el disco | Se pierde con él | Se conserva |
| Tiempo | Segundos | Minutos |

Un snapshot protege de mis errores, no de un fallo de hardware. Hacen falta los dos.

## Snapshots: cómo los uso

Antes de tocar algo que puede romper una máquina:

1. `Snapshots → Take Snapshot` en la VM o el contenedor.
2. Nombre descriptivo con fecha: `2026-09-21-antes-de-dns`.
3. Descripción de una línea: qué voy a hacer.
4. Hacer el cambio.
5. Si sale bien, **borrar el snapshot**. Si sale mal, `Rollback`.

Regla: los snapshots se borran el mismo día. Uno olvidado crece sin parar y se come el disco, que es justo lo que aquí no sobra.

Desde el shell:

```
qm snapshot 101 antes-de-dns --description "Antes de instalar bind9"
qm listsnapshot 101
qm rollback 101 antes-de-dns
qm delsnapshot 101 antes-de-dns
```

Para contenedores es igual, con `pct` en vez de `qm`.

## Copias de seguridad: el plan

Destino: el NAS de casa. Está siempre encendido, en red, y separa el dato de la máquina que lo genera.

1. **En el NAS:** una carpeta compartida solo para copias y un usuario propio para Proxmox, con permiso únicamente sobre esa carpeta.
2. **En Proxmox:** `Datacenter → Storage → Add → SMB/CIFS` (o NFS), apuntando a esa carpeta, con el contenido limitado a **VZDump backup file**.
3. **Programar:** `Datacenter → Backup → Add`
    - Schedule: semanal, de madrugada.
    - Selection mode: `All`, para que una máquina nueva entre sola en la copia.
    - Mode: **Snapshot**, que respalda en caliente sin parar la máquina.
    - Compression: `ZSTD`.
    - Retention: 2-3 copias. Más no cabe.
4. **Avisos por correo** solo si falla. Un backup que falla en silencio es peor que no tenerlo.

## Comprobación final

No está hecho hasta que **haya restaurado una copia**:

1. Lanzar el backup a mano una vez (`Backup now`) y ver que el fichero aparece en el NAS.
2. Restaurar esa copia con un **ID nuevo** (por ejemplo 901), nunca encima de la original.
3. Arrancar la restaurada y comprobar que entra y tiene sus datos.
4. Borrarla.

Una copia sin restaurar probada es una suposición, no una copia.
