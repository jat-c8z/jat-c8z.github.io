# Auditoría de seguridad de la red de casa

**Septiembre 2026**

[← Volver a la portada](../index.md)

## El problema

Antes de seguir metiendo máquinas en la red quería saber qué había de verdad en ella y en qué estado estaba el PC desde el que la administro. Un laboratorio montado sobre una red que no conoces es un laboratorio inseguro.

## Alcance

PC con Windows 11, la red local completa y el router del operador.

## Método

1. **Recogida automatizada** con scripts de PowerShell de **solo lectura**: sistema, parches, antivirus, cortafuegos, cifrado, cuentas, acceso remoto, puertos a la escucha, persistencia, software instalado y configuración de red.
2. **Barrido de la red local**: cada dispositivo con su fabricante (por el prefijo de la MAC) y sus puertos abiertos.
3. **Revisión manual del panel del router**: exposición hacia internet, servicios, Wi-Fi, clientes y concesiones DHCP.
4. **Verificación posterior** con otro script sobre la máquina real, después de aplicar correcciones. **No cuenta lo que se dijo que se haría, cuenta lo que la máquina responde.**

## Qué encontró

Cuatro hallazgos críticos y uno alto, más varios medios y bajos. Por tipo:

1. Cifrado de disco.
2. Políticas de contraseñas del sistema.
3. Toda la protección dependiendo de un único producto antivirus.
4. Contraseñas reutilizadas en varios servicios.
5. Credenciales de fábrica en un equipo de red.

Sin indicios de compromiso. Estaban bien: el cifrado del Wi-Fi, SMBv1 desactivado, UAC activo, el escritorio remoto y la exposición hacia internet, que era nula.

## Hallazgo de arquitectura: NAT de operador

El router no tiene IP pública: la de su WAN está en el rango `100.64.0.0/10`. Implicación directa: **no se pueden abrir puertos, aunque se configuren.**

Eso reordenó el diseño del laboratorio. El acceso remoto pasó a ser una malla cifrada ([Tailscale](tailscale.md)), que además es mejor solución aunque hubiera IP pública.

Lo que **no** quedó demostrado es que esa IP pública la compartan otros clientes. Lo cerré sin esa prueba porque no cambia nada: compartida o no, no entra ninguna conexión desde internet. Y la prueba que tenía pensada (ShieldsUP) no servía, porque solo escanea la IP desde la que te conectas.

## El error que cometí y qué cambié

Identifiqué dos dispositivos como las dos cámaras de casa porque ninguno respondía a puertos y sabía que había dos cámaras. El panel del router demostró que uno era otro electrodoméstico conectado.

El razonamiento, coincidencia de síntomas más un recuento, es justo el que no vale en una auditoría. **Corrección de método: un dispositivo o está identificado con una prueba o figura como desconocido.**

Con esa regla identifiqué después los dos que quedaban sin nombre:

1. Uno, siguiendo el cable físico desde el puerto del router que marcaba la lista DHCP.
2. Otro, que por el prefijo de la MAC era de un fabricante concreto, pero no lo di por identificado hasta comprobar en su app que la MAC coincidía.

El prefijo de la MAC identifica al fabricante, no al dispositivo. Esa distinción es la que me falló la primera vez.

Lo dejé escrito en el informe como una revisión numerada. Un informe que corrige sus propias conclusiones vale más que uno que aparenta haber acertado a la primera.

## Repetirla

La auditoría se repite cada seis meses o al meter un equipo nuevo en la red. Al terminar tengo que poder contestar sin dudar:

1. ¿Hay algún dispositivo en mi red que no sepa qué es?
2. ¿Sigue cerrada la exposición hacia internet (reenvío de puertos, DMZ, UPnP)?
3. ¿Está cifrado el disco del PC?
4. ¿Hay alguna contraseña por defecto o reutilizada en algún sitio?
5. ¿Tengo copia de seguridad de lo que me importa, y la he restaurado alguna vez?

## Aprendizaje de método

1. Un script de recogida convierte la auditoría en algo **repetible**: se vuelve a lanzar dentro de seis meses y se compara, en vez de rehacerla de memoria.
2. Una corrección no está cerrada hasta que se verifica contra la máquina. Marcar una tarea como hecha no cambia el estado real de un disco.
3. El eslabón más débil de una red de casa suele ser el router del operador con su contraseña de fábrica, no los equipos.
4. El filtrado MAC no es una medida de seguridad: los móviles modernos cambian su MAC en cada red.

## Módulos de ASIR que toca

Planificación y Administración de Redes, Implantación de Sistemas Operativos.
