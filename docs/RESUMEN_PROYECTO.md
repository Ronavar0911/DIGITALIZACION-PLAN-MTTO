# Plan anual de mantenimiento de riego: resumen del proyecto (al 06/10/2026)

**Área:** Riego, mantenimiento (planner) · **Estado:** versión 0 funcionando; falta la revisión de TI.
**Objetivo:** digitalizar el seguimiento del plan anual de mantenimiento (qué se ejecutó, cuándo y cuántas veces), crear de forma automática el programa de la semana siguiente (con pendientes y adelantos), distribuir jornales, mostrar el Gantt y estimar materiales, costo y stock.

---

## 1. Fuentes de datos

| Archivo | Qué aporta | Cómo entra |
|---|---|---|
| `RI-PG-004 … RIEGO.xlsx` | Plan anual: hoja `MM.TT 2026-2027` (actividades y semanas 2023–2027) y hoja `tabla` (diccionario ACTIVIDADES_PLAN ↔ SMARTBERRY ↔ OTMs) | Se carga en la app cada vez |
| `PROGRAMA_SEMANAL_ACTIVIDADES.xlsx` | Formato actual del programa (hojas `WKxx` y `GANTT WKxx`); sirvió de modelo | Solo referencia |
| `APP_STOCK_MATERIALES.xlsx` (hoja `TABLERO`) | Stock, pendiente por OC/SOLPED y precio unitario por material | Lo carga y publica una persona; los demás ven el stock compartido (`data/stock.json`) |
| IW39, MB51, IH08 (SAP, 03/07/2023 al 06/10/2026) | Historial de OTs y consumos, usado **una sola vez** para sugerir recetas | No se cargan en la app |

La hoja `tabla` es un **diccionario**, no una fuente de ejecución: la actividad se escribe distinto en el Excel (ACTIVIDADES_PLAN), en SmartBerry y en SAP (OTM). El nombre que se usa en el programa semanal es el de SAP, sin el sector.

---

## 2. Reglas de negocio acordadas

- **Campaña:** de la semana ISO 27 a la 26 del año siguiente (la 26-27 va de la semana 27 de 2026 a la 26 de 2027).
- **Códigos de las casillas semanales:** 1 programada sin ejecutar · 2 programada y ejecutada · 3 ejecutada fuera de programa · 4 adicional.
  - En pantalla se usan los símbolos del Excel: 1 contorno naranja (rojo si está vencida) · 2 contorno naranja + check azul · 3 check azul · 4 cruz roja sobre turquesa.
- **Filas de resumen por semana:** *Programadas* = 1+2 · *Ejecutadas* = 2+3 (mismo criterio que el Excel) · *Cumplimiento %* = 2 ÷ (1+2), es decir, solo las programadas que se ejecutaron.
- **Avance del plan** (igual que el Excel): ejecutadas (2+3) ÷ programadas (1+2). Con el archivo del 06/10/2026: **26,60 %** (340 de 1.278) en la campaña 26-27.
- **Pendiente:** casilla 1 en una semana anterior a la *semana de corte* (por defecto, la semana actual).
- **Cumplimiento semanal:** programadas ejecutadas (2) ÷ programadas (1+2). Se marca en rojo por debajo de **80 %** (umbral propuesto, por confirmar).
- **Código único por actividad:** `XXXXXXX_0000_NN` = 7 letras del equipo (SAP) + primer dígito y 3 últimos del DNI + n.º de la actividad dentro del equipo. 169 códigos únicos para 169 actividades; se agregó como columna G de la hoja `MM.TT`.
- **Adicionales:** con el pincel *Registrar ejecución*, la primera marca de una casilla programada la pasa a ejecutada; las siguientes cuentan como adicionales (la casilla muestra el número). En una semana sin programar, la primera marca es *fuera de programa*.
- **Área de mantenimiento:** columna *Familia* de la hoja `tabla` (MANT. EQ. RIEGO, EVALUACIONES, ESTR. HIDRÁULIC.).
- **Programa semanal:**
  - Incluye las labores programadas de la semana (casilla 1), los **pendientes** de las últimas 1, 2 o 4 semanas (o de toda la campaña) y, a elección, **adelantos** de las próximas semanas.
  - Si una labor está pendiente y además programada esa semana, aparece una sola vez.
  - Se omiten las actividades "No Act." (se pueden incluir).
  - Orden por sector: O1E1, O1E2, O2E1, O2E2, O3E1, O3E2; luego las labores sin sector; las CAPEX al final.
  - Los jornales de cada labor se reparten por día buscando la carga más pareja (lunes a viernes o a sábado) y se pueden editar. El Gantt se arma desde esos jornales.
  - Responsable por defecto: TELMO para evaluaciones y MARCO para el resto (editable; por confirmar).
- **Materiales:**
  - Cada actividad tiene una **receta estándar** editable: material y cantidad por ejecución. No se redondea.
  - El costo usa el precio unitario del stock; si falta, el del historial. Sin precio, se avisa.
  - Estado por material: **Alcanza** (stock ≥ necesario), **En camino** (cubierto por OC en tránsito o SOLPED sin OC) o **Falta**.
  - Una labor sin materiales dice "Sin materiales asignados" (sirve para el control de jornales).

---

## 3. Qué se construyó

1. **Aplicativo web** (`index.html`): pantalla de carga con logo; pestañas Plan anual, Programa semanal, Recetas, **Equipos** y **Reservas OT**.
   - **Descarga del plan:** el mismo Excel cargado, con solo las casillas modificadas (se edita el XML del archivo, así que se conservan fórmulas, formato condicional y modelo de datos). El conteo de adicionales va en una hoja `ADICIONALES`.
   - **Descarga del programa semanal:** hojas WKxx, GANTT WKxx y MATERIALES WKxx (formato similar al actual).
   - **Base compartida de recetas:** `data/recetas.json` en el repositorio, con guardado por token de GitHub, control de versiones y aviso de conflictos.
2. **Excel con la columna Código único** (`RI-PG-004 … con codigo.xlsx`): se insertó la columna G sin alterar fórmulas (recalculado y comparado con el original: 0 diferencias).
3. **Auditoría de recetas** (`AUDITORIA_RECETAS_MATERIALES.xlsx`): cruce de OTs preventivas (OM03) con el plan por equipo y similitud de texto (≥ 85 de 100).
   - 5.542 OTs OM03 en equipos del plan; 3.428 asignadas a una actividad (62 %).
   - De las 169 actividades: **35** sin OTs en el historial, **76** con OTs pero sin materiales, **28** con pocas ejecuciones con materiales y **30** con receta confiable.
   - Se evaluaron 368 combinaciones actividad–material; 24 (en 14 actividades) aparecen en el 25 % o más de las ejecuciones y quedaron preseleccionadas.

---

## 4. Decisiones y por qué

| Tema | Decisión |
|---|---|
| Código único | Incluye equipo y DNI; se agregó el n.º de actividad porque un mismo equipo llega a tener 8 actividades y el formato `XXXXXXX_0000` no alcanzaba |
| Conteo de ejecuciones | Un número dentro de la casilla (adicionales), sin crear un registro aparte |
| Recetas de materiales | **Estándar y editable**, no calculada del historial: en el historial, para la misma actividad, la mayoría de los materiales aparece en menos del 10 % de las ejecuciones |
| Redondeo de cantidades | No se aplica: redondear materiales caros de poco uso multiplicaba el costo por 4 (S/ 229 → S/ 916 en una semana de prueba) |
| Cargas en la app | Solo el plan y el stock. Los exports de SAP del historial no se vuelven a subir |
| Base compartida de recetas | Archivo JSON en el mismo repositorio, escrito con la API de GitHub: no requiere servicios nuevos, deja historial de cambios y limita la escritura a quienes tienen permiso. Se eligió para el borrador; la base corporativa se define con TI |
| Stock compartido | Una persona carga el maestro y publica solo los materiales de las recetas en `data/stock.json`; los demás lo ven sin cargar archivos. Evita que cada usuario suba un archivo de varios MB |
| Equipos y Reservas OT | Se incorporan a esta aplicación (venían de la app de stock): aquí están el plan, las OTs por equipo y su relación con las actividades. Todos los equipos de riego, con los del plan marcados |
| Temporadas anteriores | Datos **divididos por temporada** y cargados bajo demanda: al abrir solo se descarga el índice (~50 KB) y la temporada actual; las cerradas se piden al elegirlas. Evita cargar 3 años a la vez |
| Descarga del plan | Se edita solo el valor de las casillas marcadas dentro del .xlsx original, en lugar de regenerar el archivo, porque regenerarlo perdería formatos y el modelo de datos |
| Programa semanal | Pantalla aparte para no sobrecargar el plan anual |
| Hojas WK/GANTT | Primero se replica la lógica en pantalla; la exportación en el formato exacto queda para después |

---

## 5. Problemas encontrados y cómo se resolvieron

1. **Estructura del Excel cambió** (columnas y filas): el lector busca encabezados por nombre y no por posición.
2. **Insertar una columna en el Excel** sin romper fórmulas, formatos y modelo de datos: se editó el archivo por dentro y se verificó con un recálculo completo.
3. **Nombres de actividad distintos** entre plan, SmartBerry y SAP: se resolvió con la hoja `tabla` y similitud de texto; ~38 % de las OTs no calzaron y quedaron fuera de las recetas.
4. **Columnas fijas que se superponían** al desplazarse: las filas "No Act." eran translúcidas.
5. **Materiales sin precio** daban costo 0 sin aviso: ahora se marcan "sin precio".

---

## 6. Pendientes

- [ ] Publicar en GitHub Pages y probar la dirección.
- [ ] Agregar la columna **Orden** al layout del MB51 del maestro (mismo rango: desde el inicio de la temporada hasta hoy). La aplicación ya lo lee al publicar.
- [ ] Al cerrar cada temporada, revisar que el archivo `data/ots/<temporada>.json` quede completo.
- [ ] Definir quién publica el stock y con qué frecuencia (antes de armar el programa de cada semana).
- [ ] Probar las descargas y el guardado de recetas en GitHub con el equipo (se probaron en simulación, no contra el repositorio real).
- [ ] Comparar el Excel del programa semanal con el formato actual y ajustar columnas si hace falta.
- [ ] Validar las recetas estándar, empezando por las actividades de mayor gasto en materiales.
- [ ] Confirmar el umbral de 80 % y los responsables por defecto (TELMO / MARCO).
- [ ] Vincular con la app de stock (carrito de pedido con los faltantes de la semana).
- [ ] Revisión de TI: seguridad, base de datos corporativa en lugar de GitHub y migración.

---

## 7. Dónde está cada cosa

- **Repositorio:** `index.html` (la app), `docs/` (este resumen), `data/` (opcional: `recetas.json`).
- **Excel y auditoría:** archivos entregados en el chat de trabajo (no se suben al repositorio).
- **App de stock** (proyecto aparte): maestro `APP_STOCK_MATERIALES.xlsx` y su propio repositorio.
