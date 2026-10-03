# Acceso remoto con Tailscale detrás de CGNAT

**Septiembre 2026 · Terminado**

[← Volver a la portada](../index.md)

## El problema

Quería administrar el laboratorio desde fuera de casa, también por SSH desde el móvil. Pero mi línea va por CGNAT (NAT de operador): el router no tiene IP pública, la dirección de su WAN está en el rango `100.64.0.0/10`, y la IP que ve internet es otra distinta. **No se pueden abrir puertos, aunque se configuren.**

## La solución

Tailscale, una red privada en malla sobre WireGuard. Cada equipo abre una conexión **saliente** hacia el servicio de coordinación y, a partir de ahí, los equipos hablan entre sí por un túnel cifrado. Como todas las conexiones se inician desde dentro, el CGNAT no estorba.

Lo elegiría igual con IP pública: no queda ningún puerto escuchando en internet que alguien pueda escanear.

## Cómo funciona por dentro, en corto

1. Cada equipo se autentica contra la cuenta y recibe una clave.
2. El servicio de coordinación solo intercambia claves públicas y direcciones. **No ve el tráfico.**
3. Los equipos intentan hablar directamente perforando el NAT. Si no lo consiguen, el tráfico pasa por un relé (DERP), siempre cifrado extremo a extremo.

## Qué hice en el hipervisor

1. Instalar Tailscale desde el repositorio oficial con su script. Deja el servicio `tailscaled` en arranque automático.
2. `tailscale up` e iniciar sesión con la cuenta desde el navegador.
3. Aprobar el equipo en la consola de Tailscale, en **Machines**.
4. `tailscale ip -4` para saber su dirección dentro de la malla.
5. Entrar a la web de Proxmox por esa dirección, con datos móviles, para probar que funciona de verdad desde fuera.

## Qué falló y cómo lo resolví

| Síntoma | Qué era | Arreglo |
|---|---|---|
| El navegador decía «no ha enviado ningún dato» | Faltaba `https://`: el navegador entraba por `http://` a un puerto que solo habla HTTPS | Escribir la dirección completa, con `https://` y el puerto |
| El cliente SSH del móvil: «unknown node or service» | Había puesto la IP y el puerto de la web juntos en el campo de host | Solo la IP en ese campo, y el puerto de SSH en el suyo |
| `tailscale status`: «Machine is not yet approved by tailnet admin» | La aprobación de dispositivos deja los equipos nuevos bloqueados | Aprobarlo en **Machines** |

## Endurecer la malla

Tailscale mete a todos los equipos en la misma red. Un portátil comprometido dentro de la malla llega a lo mismo que si estuviera en casa. Por eso importa quién y qué entra.

| Medida | Por qué |
|---|---|
| 2FA en la cuenta con la que entro | Tailscale no tiene 2FA propio: hereda el de esa cuenta |
| Aprobación de dispositivos | Un equipo nuevo no entra en la malla hasta que lo apruebo yo |
| Lista de equipos revisada y limpia | Lo que no uso, fuera |
| Caducidad de claves revisada | Decidida equipo por equipo según si tiene que estar siempre accesible |
| *Shields up* en el PC de casa | Ningún equipo de la malla puede abrir conexiones hacia él; él sí llega a los demás |

**Descartado: Tailnet Lock.** Es la opción más fuerte (cada equipo nuevo lo firma uno de confianza), pero según la documentación de Tailscale no entra en el plan gratuito, no convive con la aprobación de dispositivos y, si se pierden las claves de desactivación, no hay forma de quitarlo. Con pocos equipos no compensa.

## Lo que aprendí

1. Con CGNAT el diseño cambia desde el principio: no es un detalle de configuración del router.
2. La aprobación de dispositivos funciona: un equipo con sesión iniciada no hace nada en la malla hasta que lo apruebo.
3. Lo que se expone hacia internet es lo que más pesa en la seguridad de una red de casa, y aquí es nada.

## Módulos de ASIR que toca

Planificación y Administración de Redes.
