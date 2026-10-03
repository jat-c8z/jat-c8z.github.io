# IA local autoalojada

**Septiembre 2026 · En marcha**

[← Volver a la portada](../index.md)

## El problema

Quería consultar un modelo de lenguaje sin que las conversaciones ni los documentos salieran de casa. Los documentos que manejo a diario son expedientes administrativos: no es una preferencia, es que no deben salir a ningún servicio externo.

## Qué monté

Un contenedor LXC en el hipervisor, con IP fija, corriendo **Ollama** como motor y **Open WebUI** como interfaz, con un modelo de 3B de parámetros.

## Decisión 1: dónde alojarlo

El PC principal es mucho más potente (Ryzen, 16 GB), pero la IA fue al servidor:

| Criterio | PC principal | Servidor |
|---|---|---|
| Disponibilidad | Solo encendido | Siempre |
| Acceso desde otros equipos | No | Sí, y desde fuera por Tailscale |
| Impacto mientras trabajo | Consume recursos | Ninguno |
| Valor formativo | Ninguno | Servicio autoalojado completo |

Gana el servidor, y no por potencia: **por disponibilidad**. Un servicio que solo funciona cuando estás delante no es un servicio.

## Decisión 2: contenedor y no máquina virtual

Un LXC comparte el kernel del hipervisor: sin hardware emulado, arranque en segundos y huella mínima. Con 8 GB de RAM y 119 GB de disco compartidos con el resto del laboratorio, eso decide si el servicio cabe o no cabe.

La contrapartida, asumida: menos aislamiento, y no sirve para practicar nada de bajo nivel. Para correr dos servicios es lo correcto.

## Decisión 3: motor e interfaz separados

Ollama sirve el modelo por una API HTTP. Open WebUI pone la interfaz web y habla con esa API. Están desacoplados a propósito: puedo cambiar la interfaz sin tocar el motor, o llamar a la API desde un script sin pasar por la web.

## Decisión 4: el tamaño del modelo

Un modelo de 3B cuantizado, no uno grande. El i3-5005U no tiene GPU utilizable y la RAM va compartida. Por encima de 7B en este hardware, o no arranca o tarda tanto que se deja de usar. Dimensionar a lo que el hardware aguanta de verdad, no a lo que suena mejor.

## Decisión 5: apagado por defecto

Lo uso poco, así que el contenedor está apagado y lo arranco a mano cuando lo necesito. Un servicio apagado no consume recursos ni se puede usar desde ningún otro equipo de la red mientras no lo estoy usando yo.

## Comandos de Ollama que uso

```
ollama list              # modelos descargados
ollama pull <modelo>     # descargar uno nuevo
ollama run <modelo>      # hablar con él por terminal
ollama rm <modelo>       # borrarlo (cada modelo ocupa GB)
ollama ps                # qué hay cargado en memoria ahora
systemctl status ollama  # que el servicio está vivo
```

## Gestión del recurso escaso

Cada modelo descargado ocupa gigas del mismo SSD donde vive todo el laboratorio. `ollama rm` de lo que no se usa no es limpieza opcional: dos o tres modelos probados y olvidados llenan el disco y tiran el hipervisor.

## Lo que reconozco

Un 3B en CPU inventa más y razona peor que los modelos grandes de pago. **Lo que se gana aquí es privacidad y aprendizaje, no calidad de respuesta.** Para trabajo serio con documentos largos la diferencia se nota, y lo sé porque lo uso.

## Módulos de ASIR que toca

Implantación de Sistemas Operativos, Planificación y Administración de Redes.
