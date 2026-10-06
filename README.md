# Plan anual de mantenimiento de riego · seguimiento y programación semanal

**Versión 0 (borrador)** · al 06/10/2026 · Área de riego, mantenimiento.
Aplicación web de **un solo archivo** (`index.html`): no necesita servidor ni base de datos. Lee los Excel en el navegador de cada persona; los archivos no se suben a ningún lado.

## Qué hace

| Pestaña | Para qué sirve |
|---|---|
| **Plan anual** | Muestra el plan (`MM.TT`) como en Excel: actividades, frecuencia, semana de inicio y casillas de color por semana. Calcula avance, pendientes, cumplimiento por semana y por área. Las casillas se marcan con clic o arrastrando. |
| **Programa semanal** | Arma la programación de la semana elegida: labores programadas, **pendientes** de semanas anteriores y **adelantos** de las siguientes. Ordena por sector (O1E1 … O3E2), reparte jornales por día y muestra el **Gantt**. Calcula materiales, costo y stock. |
| **Recetas** | Base editable de **materiales estándar por actividad** (cantidad por ejecución). |

## Cómo usarla

1. Abrir la página y cargar el Excel del plan (hoja `MM.TT …`, con la columna **Código único**). La hoja `tabla` del mismo archivo aporta el nombre SAP (OTM), la familia y el equipo.
2. **Plan anual:** elegir campaña y *semana de corte*; marcar la ejecución con el pincel **Registrar ejecución**.
3. **Programa semanal:** elegir semana, cuántas semanas de pendientes traer y cuántas adelantar; *Generar programa*. Opcional: cargar el stock (`APP_STOCK_MATERIALES.xlsx`, hoja `TABLERO`) para ver faltantes.
4. **Recetas:** definir los materiales y cantidades estándar de cada actividad.

Código de las casillas: 1 programada · 2 programada y ejecutada · 3 ejecutada fuera de programa · 4 adicional (la casilla morada muestra cuántas veces).

## Publicar en GitHub Pages

1. Crear el repositorio y subir **el contenido** de esta carpeta (`index.html`, `README.md`, `.nojekyll`, `data/`, `docs/`) con *Add file → Upload files*.
2. *Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` → Save*.
3. En unos minutos la dirección aparece en esa misma pantalla.

> **Plan de GitHub:** con GitHub Free, Pages solo funciona con repositorios **públicos**; con repositorios privados se necesita Pro, Team o Enterprise. Aun con repositorio privado, el sitio publicado puede ser visible en internet si la organización lo permite.
> **Datos incluidos:** `index.html` trae incorporada la sugerencia de recetas (códigos de materiales, descripciones y precios históricos de SAP). Es información de la empresa: revisar la visibilidad del repositorio antes de compartir el enlace.

## Dependencias

Necesita internet al abrirse: la librería de lectura de Excel (SheetJS) se carga desde `cdnjs.cloudflare.com` y la tipografía desde Google Fonts (si falta, usa una de respaldo). Para uso sin internet se pueden incluir dentro del repositorio.

## Límites de esta versión

- Lo que se marca en las casillas y las recetas editadas se **guardan solo en el navegador** de cada persona. No hay base compartida ni exportación del plan actualizado a Excel.
- Falta generar las hojas `WKxx` y `GANTT WKxx` en el formato exacto del programa semanal.
- Falta vincular con la app de stock (carrito de pedido).
- Revisión de TI pendiente: seguridad, base de datos compartida y migración.

Más detalle en [`docs/RESUMEN_PROYECTO.md`](docs/RESUMEN_PROYECTO.md).
