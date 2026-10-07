# Plan anual de mantenimiento de riego · seguimiento y programación semanal

**Versión 0 (borrador)** · al 06/10/2026 · Área de riego, mantenimiento.
Aplicación web de **un solo archivo** (`index.html`): no necesita servidor. Lee los Excel en el navegador de cada persona; los archivos no se suben a ningún lado. Lo único compartido es la base de recetas, que vive en este mismo repositorio (`data/recetas.json`).

## Qué hace

| Pestaña | Para qué sirve |
|---|---|
| **Plan anual** | Muestra el plan (`MM.TT`) como en Excel: actividades, frecuencia, semana de inicio y casillas de color por semana. Calcula avance, pendientes, cumplimiento por semana y por área. Las casillas se marcan con clic o arrastrando. |
| **Programa semanal** | Arma la programación de la semana elegida: labores programadas, **pendientes** de semanas anteriores y **adelantos** de las siguientes. Ordena por sector (O1E1 … O3E2), reparte jornales por día y muestra el **Gantt**. Calcula materiales, costo y stock. |
| **Recetas** | Base editable de **materiales estándar por actividad** (cantidad por ejecución), con guardado en una **base compartida** (ver más abajo). |

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

## Cómo usarla

1. Abrir la página y arrastrar (o seleccionar) el Excel del plan (hoja `MM.TT …`, con la columna **Código único**). La hoja `tabla` del mismo archivo aporta el nombre SAP (OTM), la familia y el equipo.
2. **Plan anual:** elegir campaña y *semana de corte* y marcar las casillas con el pincel:
   - **Registrar ejecución (+1):** cada clic suma *una* ejecución realizada. Casilla vacía → fuera de programa; programada → ejecutada; ya ejecutada → adicional, y los clics siguientes suben el número de adicionales.
   - **Programada / Ejecutada / Fuera prog. / Adicional:** dejan la casilla directamente en ese estado, sin contar nada.
   - **Ciclar estados:** cada clic pasa al siguiente estado; sirve para corregir.
3. **Programa semanal:** elegir semana, cuántas semanas de pendientes traer y cuántas adelantar; *Generar programa*. Opcional: cargar el stock (`APP_STOCK_MATERIALES.xlsx`, hoja `TABLERO`) para ver faltantes.
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
