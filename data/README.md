# Carpeta `data/`

Aquí vive **`recetas.json`**: la base compartida de recetas de materiales.

- **No hace falta crearlo a mano.** La aplicación lo crea la primera vez que alguien con permiso usa *Recetas → Guardar en la base compartida*. Hasta entonces, la aplicación usa la sugerencia del historial que trae incorporada.
- Cada guardado es un *commit*: el historial del repositorio sirve de bitácora y permite volver a una versión anterior.
- También se puede subir a mano un archivo exportado con *Recetas → Exportar todas las recetas (JSON)*, con el nombre exacto `recetas.json`.

## `equipos.json`, `ots/<temporada>.json`, `reservas.json`

OTs, consumos y reservas de SAP. Las temporadas cerradas (`23-24`, `24-25`, `25-26`) se generaron una vez desde los exports IW39 e MB51; la temporada actual se renueva desde la aplicación con *Publicar datos de SAP para todos*. Formato compacto (arreglos por fila). No editar a mano.

## `stock.json`

Stock de los materiales de las recetas y las reservas, publicado desde la aplicación con *Programa semanal → Publicar datos de SAP para todos*. Se sobrescribe en cada publicación (el historial queda en los *commits*). No editar a mano.

No subir aquí los Excel del plan, del programa semanal ni del stock: la aplicación los lee en el navegador de cada persona.
