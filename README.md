# Plan anual de mantenimiento de riego · seguimiento y programación semanal

**Versión 0 (borrador)** · al 06/10/2026 · Área de riego, mantenimiento.
Aplicación web de **un solo archivo** (`index.html`): no necesita servidor. Lee los Excel en el navegador de cada persona; los archivos no se suben a ningún lado. Lo único compartido es la base de recetas, que vive en este mismo repositorio (`data/recetas.json`).

## Qué hace

| Pestaña | Para qué sirve |
|---|---|
| **Plan anual** | Muestra el plan (`MM.TT`) como en Excel: actividades, frecuencia, semana de inicio y casillas de color por semana. Calcula avance, pendientes, cumplimiento por semana y por área. Las casillas se marcan con clic o arrastrando. |
| **Programa semanal** | Arma la programación de la semana elegida: labores programadas, **pendientes** de semanas anteriores y **adelantos** de las siguientes. Ordena por sector (O1E1 … O3E2), reparte jornales por día y muestra el **Gantt**. Calcula materiales, costo y stock. |
| **Recetas** | Base editable de **materiales estándar por actividad** (cantidad por ejecución), con guardado en una **base compartida** (ver más abajo). |
| **Equipos** | Todos los equipos de riego con sus OTs por temporada: correctivas (OM01), preventivas (OM03), costo real y costo por mes. Los equipos del plan llevan la marca **PLAN**; al elegir uno se ven sus **actividades del plan** con las OTs preventivas asignadas a cada una, los materiales consumidos y la lista de órdenes. |
| **Reservas OT** | Una fila por orden con material reservado (IW13): se despliega para ver cada material, lo pendiente de retirar, el stock de hoy y el costo estimado. Se filtra por clase de orden, equipos del plan o fuera del plan. |

## Descargas a Excel

- **Plan:** *Descargar plan actualizado* entrega **el mismo archivo que cargaste** con solo las casillas modificadas. No toca fórmulas, formatos ni la hoja `tabla`; el Excel recalcula el avance al abrirse. El conteo de ejecuciones adicionales se guarda en una hoja nueva, **ADICIONALES**, que la aplicación vuelve a leer en la siguiente carga.
- **Programa semanal:** *Descargar programa* genera un libro con las hojas **WKxx** (jornales por día con columnas PLAN y EJEC, listas para llenar), **GANTT WKxx** (barras de colores por origen) y **MATERIALES WKxx**. Es un formato similar al de `PROGRAMA_SEMANAL_ACTIVIDADES.xlsx`, no una copia exacta.

## Base compartida de recetas

Las recetas se guardan en `data/recetas.json` de este repositorio; cada guardado queda como un *commit* (historial y posibilidad de volver atrás).

- **Leer:** cualquiera que abra la página ve las recetas de la base compartida.
- **Guardar:** pestaña **Recetas → Conectar para guardar**. Se pide un *token* de GitHub; luego **Guardar en la base compartida**. Solo pueden guardar quienes tengan permiso de escritura en el repositorio.
- **Crear el token:** GitHub → *Settings → Developer settings → Personal access tokens → Fine-grained tokens*. Elegir **solo este repositorio**, permiso **Contents: Read and write** y fecha de vencimiento. El token no se sube a ningún lado: queda en la pestaña del navegador (o en el equipo, si se marca *Recordar*).
- **Dos personas a la vez:** cada guardado lleva un número de versión. Si alguien guardó antes, la aplicación avisa y pide recargar, en vez de pisar su trabajo.
- Las ediciones que no se guardan quedan solo en el navegador de esa persona y tienen prioridad sobre la base compartida.

## Datos de SAP compartidos y temporadas

Las OTs se guardan **por temporada** para que la aplicación sea rápida:

| Archivo | Contenido | Cuándo se carga |
|---|---|---|
| `data/equipos.json` | Índice de equipos y resumen por temporada (órdenes, OM01, OM03, costo). Unos 50 KB | Al abrir la pestaña Equipos |
| `data/ots/26-27.json` | OTs de la temporada **actual** y sus consumos de material | Junto con el índice |
| `data/ots/23-24.json`, `24-25`, `25-26` | Temporadas **cerradas** | Solo cuando alguien las elige (o "Todas las temporadas"); luego quedan en memoria |
| `data/reservas.json` | Reservas de material de las OTs (IW13) | Al abrir Reservas OT |
| `data/stock.json` | Stock, pendientes y precio de los materiales de recetas y reservas | Al abrir la aplicación |

- **Temporadas cerradas:** ya vienen generadas y no cambian; no hace falta volver a subirlas.
- **Temporada actual:** una persona carga el maestro `APP_STOCK_MATERIALES.xlsx` en *Programa semanal* y pulsa **Publicar datos de SAP para todos**. En un solo paso se actualizan el stock, las reservas, las OTs de la temporada y el índice de equipos (4 *commits*).
- **Cierre de temporada:** el archivo de la temporada que termina queda como está, y la nueva se crea sola en la primera publicación.
- **Consumo de material por OT:** las líneas ya publicadas se conservan. Cuando el MB51 del maestro trae la columna **Orden** (hoja `MOV_TEMPORADA`), la publicación actualiza el consumo de esas órdenes (movimientos 261 y 262). Con el rango habitual del maestro, desde el inicio de la temporada hasta hoy, alcanza.
- Cada OT preventiva se **asigna a una actividad del plan** por equipo y similitud de texto (≥ 85 de 100), igual que en la auditoría de recetas.

## Stock compartido

El stock viene del maestro `APP_STOCK_MATERIALES.xlsx` (hoja `TABLERO`), que pesa varios MB. Para que nadie tenga que cargarlo:

1. **Una persona publica.** En *Programa semanal* carga el maestro recién actualizado y pulsa **Publicar datos de SAP para todos** (aparece cuando está conectada con su token, ver arriba).
2. La aplicación guarda en `data/stock.json` **solo los materiales de las recetas y de las reservas** (unos 320): descripción, unidad, stock, pendiente por SOLPED/OC en camino y precio. Pesa unos 25 KB.
3. **Los demás no cargan nada:** al abrir la aplicación ven *«Stock compartido al dd/mm»*, con la fecha del archivo y quién lo publicó.
4. Si alguien carga su propio archivo, ese manda en su pantalla y no se pisa.

La fecha que se muestra es la de modificación del archivo cargado (el maestro no trae una fecha de corte propia). Si se agrega un material nuevo a una receta, hay que **volver a publicar** para que aparezca su stock.

## Cómo usarla

1. Abrir la página y arrastrar (o seleccionar) el Excel del plan (hoja `MM.TT …`, con la columna **Código único**). La hoja `tabla` del mismo archivo aporta el nombre SAP (OTM), la familia y el equipo.
2. **Plan anual:** elegir campaña y *semana de corte* y marcar las casillas con el pincel:
   - **Registrar ejecución (+1):** cada clic suma *una* ejecución realizada. Casilla vacía → fuera de programa; programada → ejecutada; ya ejecutada → adicional, y los clics siguientes suben el número de adicionales.
   - **Programada / Ejecutada / Fuera prog. / Adicional:** dejan la casilla directamente en ese estado, sin contar nada.
   - **Ciclar estados:** cada clic pasa al siguiente estado; sirve para corregir.
3. **Programa semanal:** elegir semana, cuántas semanas de pendientes traer y cuántas adelantar; *Generar programa*. El stock aparece solo si ya fue publicado; si no, cargar `APP_STOCK_MATERIALES.xlsx` (hoja `TABLERO`).
4. **Recetas:** definir los materiales y cantidades estándar de cada actividad.

Símbolos de las casillas (los mismos del Excel): **1** contorno naranja = programada por ejecutar (en rojo si ya venció sin ejecutarse) · **2** contorno naranja + check azul = programada y ejecutada · **3** solo check azul = ejecutada fuera de programa · **4** cruz roja sobre celda turquesa = adicional (con el número de veces si son más de una).

## Publicar en GitHub Pages

1. Crear el repositorio y subir **el contenido** de esta carpeta (`index.html`, `README.md`, `.nojekyll`, `data/`, `docs/`) con *Add file → Upload files*.
2. *Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` → Save*.
3. En unos minutos la dirección aparece en esa misma pantalla.

> **Plan de GitHub:** con GitHub Free, Pages solo funciona con repositorios **públicos**; con repositorios privados se necesita Pro, Team o Enterprise. Aun con repositorio privado, el sitio publicado puede ser visible en internet si la organización lo permite.
> **Datos incluidos:** `index.html` trae incorporada la sugerencia de recetas (códigos de materiales, descripciones y precios históricos de SAP). Es información de la empresa: revisar la visibilidad del repositorio antes de compartir el enlace.

## Dependencias

Necesita internet al abrirse: las librerías de Excel (SheetJS, JSZip y ExcelJS) se cargan desde `cdnjs.cloudflare.com` y las tipografías desde Google Fonts (si faltan, usa una de respaldo). Para uso sin internet se pueden incluir dentro del repositorio.

## Límites de esta versión

- Las marcas del plan no se guardan en ningún servidor: se conservan descargando el Excel actualizado y volviéndolo a cargar.
- La base compartida de recetas depende de GitHub y de que cada editor cree su token. Una base de datos corporativa (con usuarios y permisos propios) queda para la revisión de TI.
- El Excel del programa semanal tiene un formato similar, no idéntico, al actual.
- Falta vincular con la app de stock (carrito de pedido).
- Revisión de TI pendiente: seguridad, base de datos compartida y migración.

Más detalle en [`docs/RESUMEN_PROYECTO.md`](docs/RESUMEN_PROYECTO.md).
