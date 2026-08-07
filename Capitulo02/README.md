# "De datos crudos a modelo analítico" – Importar, limpiar y combinar datasets de demanda e inventario de bodega usando Power Query, dejando el modelo listo para su análisis en Power BI

## Metadata

| Campo | Detalle |
|-------|---------|
| **Duración** | 38 minutos |
| **Complejidad** | Media |
| **Nivel Bloom** | Crear |
| **Tecnologías** | Power BI Desktop 2.130.754.0, Power Query Editor, Lenguaje M |

---

## Descripción General

En esta práctica construirás un pipeline completo de preparación de datos en Power Query: conectarás Power BI Desktop a tres fuentes heterogéneas (demanda histórica en CSV, catálogo de productos en Excel e inventario de bodega en Excel), aplicarás transformaciones de limpieza y enriquecimiento, y combinarás las tablas mediante operaciones Merge para generar un modelo analítico integrado. El archivo resultante (`Practica2_Modelo_Analitico.pbix`) será el insumo obligatorio para la Práctica 3.

---

## Objetivos de Aprendizaje

Al finalizar esta práctica serás capaz de:

- [ ] Conectar Power BI Desktop a tres fuentes de datos distintas (CSV y Excel) mediante el asistente "Obtener datos".
- [ ] Aplicar transformaciones de limpieza en Power Query: corrección de tipos, eliminación de nulos/duplicados y estandarización de nombres.
- [ ] Crear columnas calculadas en Power Query usando lenguaje M para enriquecer el modelo (columna `AñoMes` y `Descripcion_Completa`).
- [ ] Combinar tablas mediante operaciones Merge por columna clave `SKU` para generar un modelo analítico integrado.
- [ ] Cargar el modelo transformado a Power BI Desktop y verificar su integridad antes de guardarlo.

---

## Prerrequisitos

### Conocimientos previos

| Requisito | Descripción |
|-----------|-------------|
| Práctica 1 completada | Comprensión del dataset de 5 SKUs y su comportamiento de demanda mensual |
| Navegación básica en Power BI Desktop | Saber abrir la aplicación, identificar el panel de campos y las vistas de datos |
| Conceptos de Power Query | Haber leído la Lección 2.1 sobre qué es Power Query y por qué transformar datos |

### Acceso y archivos requeridos

| Recurso | Ubicación |
|---------|-----------|
| `Practica2_Demanda_Cruda.csv` | `C:\ForecLog\Practicas\P2\` |
| `Practica2_Catalogo_Productos.xlsx` | `C:\ForecLog\Practicas\P2\` |
| `Practica2_Inventario_Bodega.xlsx` | `C:\ForecLog\Practicas\P2\` |
| Power BI Desktop | Versión 2.130.754.0 (agosto 2024) |

---

## Entorno del Laboratorio

### Hardware mínimo

| Componente | Especificación |
|------------|---------------|
| Procesador | 64 bits — Intel Core i5 8ª gen. o AMD Ryzen 5 3000+ |
| RAM | 8 GB mínimo (16 GB recomendado) |
| Disco | 2 GB libres |
| Pantalla | 1366 × 768 mínimo (1920 × 1080 recomendado) |

### Software requerido

| Software | Versión exacta |
|----------|---------------|
| Power BI Desktop | 2.130.754.0 (agosto 2024) |
| Sistema operativo | Windows 10/11 (64 bits) |
| Idioma de interfaz | Español (México) |

### Configuración inicial

1. Verifica que la carpeta de trabajo exista. Si no, créala:

```
mkdir C:\ForecLog\Practicas\P2
```

2. Coloca los tres archivos de práctica en `C:\ForecLog\Practicas\P2\`.

3. Confirma el idioma de Power BI Desktop: **Archivo → Opciones y configuración → Opciones → Configuración regional → Español (México)**.

---

## Estructura de los Archivos Fuente

Antes de iniciar, es fundamental conocer la estructura de cada archivo para anticipar los problemas de calidad que resolverás.

### `Practica2_Demanda_Cruda.csv`

| Columna | Contenido esperado | Problemas simulados |
|---------|-------------------|---------------------|
| `sku` | Código SKU (texto) | Algunos valores con espacios al inicio/final |
| `fecha` | Fecha como texto ("01/01/2024") | Tipo texto, no fecha |
| `demanda_unidades` | Cantidad demandada | Algunos nulos; un valor atípico negativo |
| `tipo_movimiento` | "Salida" o "Devolucion" | Incluye devoluciones que deben filtrarse |
| `notas` | Columna irrelevante | Debe eliminarse |

Datos: 5 SKUs × 12 meses = 60 filas de salida + ~5 filas de devolución + ~3 filas con nulos = ~68 filas totales.

### `Practica2_Catalogo_Productos.xlsx`

| Columna | Contenido |
|---------|-----------|
| `SKU` | Código SKU |
| `Nombre_Producto` | Nombre corto |
| `Categoria` | Categoría ("Embalaje", "Almacenaje", "Etiquetado") |
| `Unidad_Medida` | "Unidad", "Rollo", "Millar" |

5 filas (una por SKU).

### `Practica2_Inventario_Bodega.xlsx`

| Columna | Contenido | Problemas simulados |
|---------|-----------|---------------------|
| `SKU` | Código SKU | Formato limpio |
| `Mes` | Número del mes (1–12) | Tipo numérico |
| `Anio` | 2024 | Tipo numérico |
| `Stock_Disponible` | Unidades en bodega | Algunos duplicados de fila |
| `Stock_Minimo` | Nivel mínimo de seguridad | Limpio |

60 filas esperadas (5 SKUs × 12 meses) + ~3 duplicados = ~63 filas.

---

## Paso a Paso

### Paso 1 — Crear el archivo Power BI y conectar la primera fuente (Demanda)

**Objetivo:** Abrir Power BI Desktop y conectar el archivo CSV de demanda cruda.

**Instrucciones:**

1. Abre **Power BI Desktop**. En la pantalla de inicio selecciona **Informe en blanco**.
2. Ve a **Inicio → Obtener datos → Texto o CSV**.
3. Navega hasta `C:\ForecLog\Practicas\P2\Practica2_Demanda_Cruda.csv` y haz clic en **Abrir**.
4. En la ventana de vista previa, verifica que el delimitador detectado sea **Coma** y la codificación **UTF-8** (o 65001).
5. Haz clic en **Transformar datos** (NO en "Cargar") para abrir el Editor de Power Query.

**Resultado esperado:**

Se abre el Editor de Power Query con una consulta llamada `Practica2_Demanda_Cruda` mostrando las columnas `sku`, `fecha`, `demanda_unidades`, `tipo_movimiento` y `notas`. Todas las columnas aparecen inicialmente como tipo texto (icono ABC).

**Verificación:**

- El panel izquierdo ("Consultas") muestra una entrada: `Practica2_Demanda_Cruda`.
- El panel derecho ("Pasos aplicados") muestra los pasos automáticos: `Origen`, `Encabezados promovidos`, `Tipo cambiado` (si Power Query lo detectó automáticamente).
- El conteo de filas en la barra inferior debe ser aproximadamente 68 filas.

---

### Paso 2 — Conectar la segunda fuente (Catálogo de Productos)

**Objetivo:** Agregar el archivo Excel del catálogo de productos como segunda consulta.

**Instrucciones:**

1. Dentro del Editor de Power Query, ve a **Inicio → Nuevo origen → Archivo → Libro de Excel**.
2. Navega hasta `C:\ForecLog\Practicas\P2\Practica2_Catalogo_Productos.xlsx` y haz clic en **Abrir**.
3. En el panel **Navegador**, selecciona la hoja `Catalogo` (o la única hoja disponible).
4. Haz clic en **Aceptar**.

**Resultado esperado:**

Aparece una segunda consulta en el panel izquierdo llamada `Catalogo` con 5 filas y 4 columnas: `SKU`, `Nombre_Producto`, `Categoria`, `Unidad_Medida`.

**Verificación:**

- Las 5 filas corresponden a: SKU-A101, SKU-B202, SKU-C303, SKU-D404, SKU-E505.
- Los tipos de datos deben ser texto para todas las columnas.

---

### Paso 3 — Conectar la tercera fuente (Inventario de Bodega)

**Objetivo:** Agregar el archivo Excel de inventario como tercera consulta.

**Instrucciones:**

1. En el Editor de Power Query, ve a **Inicio → Nuevo origen → Archivo → Libro de Excel**.
2. Navega hasta `C:\ForecLog\Practicas\P2\Practica2_Inventario_Bodega.xlsx` y haz clic en **Abrir**.
3. En el panel **Navegador**, selecciona la hoja `Inventario`.
4. Haz clic en **Aceptar**.

**Resultado esperado:**

Tercera consulta en el panel izquierdo: `Inventario` con aproximadamente 63 filas y 5 columnas: `SKU`, `Mes`, `Anio`, `Stock_Disponible`, `Stock_Minimo`.

**Verificación:**

- El panel izquierdo ahora muestra tres consultas: `Practica2_Demanda_Cruda`, `Catalogo`, `Inventario`.
- La columna `Mes` muestra valores numéricos del 1 al 12.

---

### Paso 4 — Limpiar la tabla de Demanda Cruda

**Objetivo:** Aplicar transformaciones de limpieza: eliminar columna innecesaria, corregir tipos, filtrar devoluciones, eliminar nulos y recortar espacios.

**Instrucciones:**

1. Selecciona la consulta `Practica2_Demanda_Cruda` en el panel izquierdo.
2. **Eliminar columna innecesaria:**
   - Haz clic derecho en el encabezado de la columna `notas` → **Quitar**.
3. **Recortar espacios en la columna SKU:**
   - Selecciona la columna `sku`.
   - Ve a **Transformar → Formato → Recortar**.
4. **Estandarizar nombre de columna SKU:**
   - Haz doble clic en el encabezado `sku` y renómbralo a `SKU` (mayúsculas).
5. **Filtrar solo movimientos de salida:**
   - Haz clic en la flecha desplegable del encabezado `tipo_movimiento`.
   - Desmarca `Devolucion` y deja marcado solo `Salida`.
   - Haz clic en **Aceptar**.
6. **Corregir tipo de dato de la columna `fecha`:**
   - Selecciona la columna `fecha`.
   - Ve a **Transformar → Tipo de datos → Fecha**.
   - Si aparece el cuadro "Cambiar tipo de columna", selecciona **Reemplazar actual**.
7. **Corregir tipo de dato de `demanda_unidades`:**
   - Selecciona la columna `demanda_unidades`.
   - Ve a **Transformar → Tipo de datos → Número entero**.
   - Selecciona **Reemplazar actual**.
8. **Eliminar filas con valores nulos en `demanda_unidades`:**
   - Haz clic en la flecha desplegable de `demanda_unidades`.
   - Desmarca **(null)** si aparece en la lista.
   - Haz clic en **Aceptar**.
9. **Eliminar valores negativos (atípicos):**
   - Haz clic en la flecha desplegable de `demanda_unidades`.
   - Selecciona **Filtros de número → Es mayor o igual que…** → escribe `0` → **Aceptar**.
10. **Eliminar la columna `tipo_movimiento`** (ya no es necesaria tras el filtrado):
    - Clic derecho en `tipo_movimiento` → **Quitar**.
11. **Renombrar la columna `fecha`** a `Fecha` y `demanda_unidades` a `Demanda_Unidades`.

**Resultado esperado:**

La tabla `Practica2_Demanda_Cruda` ahora tiene 3 columnas (`SKU`, `Fecha`, `Demanda_Unidades`) con exactamente 60 filas (5 SKUs × 12 meses). Los tipos son: texto, fecha y número entero respectivamente.

**Verificación:**

- Barra inferior: **60 filas**.
- Columna `Fecha`: muestra fechas en formato `dd/mm/aaaa` (según configuración regional).
- Columna `Demanda_Unidades`: solo valores ≥ 0, sin nulos.
- Columna `SKU`: valores sin espacios extras (ej. "SKU-A101", no " SKU-A101").
- Panel derecho: debe mostrar ~10-11 pasos aplicados.

---

### Paso 5 — Crear columna calculada `AñoMes` en la tabla de Demanda

**Objetivo:** Enriquecer la tabla con una columna de período en formato "AAAA-MM" para facilitar combinaciones y análisis temporal.

**Instrucciones:**

1. Con la consulta `Practica2_Demanda_Cruda` seleccionada, ve a **Agregar columna → Columna personalizada**.
2. En el campo **Nombre de la nueva columna**, escribe: `AñoMes`
3. En el campo **Fórmula de columna personalizada**, escribe el siguiente código M:

```m
Date.ToText([Fecha], "yyyy-MM")
```

4. Haz clic en **Aceptar**.
5. Verifica que la nueva columna `AñoMes` muestra valores como `2024-01`, `2024-02`, etc.
6. Asegúrate de que el tipo de dato sea **Texto** (debería serlo por defecto).

**Resultado esperado:**

Nueva columna `AñoMes` con 60 valores en formato "AAAA-MM", tipo texto.

**Verificación:**

- Primer valor: `2024-01` (correspondiente a enero 2024).
- Último valor: `2024-12` (correspondiente a diciembre 2024).
- No hay valores nulos ni errores en la columna.

---

### Paso 6 — Limpiar la tabla de Inventario

**Objetivo:** Eliminar filas duplicadas, crear columna `AñoMes` equivalente y corregir tipos de datos.

**Instrucciones:**

1. Selecciona la consulta `Inventario` en el panel izquierdo.
2. **Corregir tipos de datos:**
   - Selecciona la columna `SKU` → tipo **Texto**.
   - Selecciona `Mes` → tipo **Número entero**.
   - Selecciona `Anio` → tipo **Número entero**.
   - Selecciona `Stock_Disponible` → tipo **Número entero**.
   - Selecciona `Stock_Minimo` → tipo **Número entero**.
3. **Eliminar filas duplicadas:**
   - Selecciona todas las columnas (Ctrl + A en el área de encabezados).
   - Ve a **Inicio → Quitar filas → Quitar duplicados**.
4. **Crear columna `AñoMes`** para poder combinar con la tabla de demanda:
   - Ve a **Agregar columna → Columna personalizada**.
   - Nombre: `AñoMes`
   - Fórmula:

```m
Text.From([Anio]) & "-" & Text.PadStart(Text.From([Mes]), 2, "0")
```

5. Haz clic en **Aceptar**.
6. Verifica que los valores sean consistentes: `2024-01`, `2024-02`, ..., `2024-12`.

**Resultado esperado:**

La tabla `Inventario` tiene exactamente 60 filas (tras eliminar duplicados), con una nueva columna `AñoMes` en formato texto "AAAA-MM".

**Verificación:**

- Barra inferior: **60 filas**.
- Columna `AñoMes`: valores de `2024-01` a `2024-12`, 12 valores únicos.
- Sin filas duplicadas (cada combinación SKU + AñoMes es única).

---

### Paso 7 — Limpiar la tabla de Catálogo

**Objetivo:** Verificar tipos y estandarizar la tabla de catálogo; crear columna `Descripcion_Completa`.

**Instrucciones:**

1. Selecciona la consulta `Catalogo` en el panel izquierdo.
2. **Verificar tipos de datos:** todas las columnas deben ser tipo **Texto**. Si alguna no lo es, corrígela.
3. **Recortar espacios** en la columna `SKU`:
   - Selecciona `SKU` → **Transformar → Formato → Recortar**.
4. **Crear columna `Descripcion_Completa`:**
   - Ve a **Agregar columna → Columna personalizada**.
   - Nombre: `Descripcion_Completa`
   - Fórmula:

```m
[SKU] & " - " & [Nombre_Producto] & " (" & [Categoria] & ")"
```

5. Haz clic en **Aceptar**.

**Resultado esperado:**

La tabla `Catalogo` tiene 5 filas y 5 columnas. La nueva columna muestra valores como:
- `SKU-A101 - Caja Cartón Grande (Embalaje)`
- `SKU-B202 - Paleta de Madera Estándar (Almacenaje)`
- `SKU-C303 - Film Stretch 500m (Embalaje)`
- `SKU-D404 - Etiqueta Código de Barras (Etiquetado)`
- `SKU-E505 - Esquinero Cartón (Embalaje)`

**Verificación:**

- 5 filas exactas.
- La columna `Descripcion_Completa` no contiene errores ni nulos.
- Cada valor incluye el SKU, nombre y categoría entre paréntesis.

---

### Paso 8 — Combinar Demanda con Catálogo (Merge)

**Objetivo:** Enriquecer la tabla de demanda con la información del catálogo mediante una operación Merge por columna `SKU`.

**Instrucciones:**

1. Selecciona la consulta `Practica2_Demanda_Cruda` en el panel izquierdo.
2. Ve a **Inicio → Combinar consultas → Combinar consultas**.
3. En el cuadro de diálogo:
   - **Tabla superior (izquierda):** `Practica2_Demanda_Cruda` — selecciona la columna `SKU` haciendo clic en su encabezado.
   - **Tabla inferior (derecha):** despliega y selecciona `Catalogo` — selecciona la columna `SKU`.
   - **Tipo de combinación:** `Externa izquierda (todas de la primera, coincidencias de la segunda)`.
4. Haz clic en **Aceptar**.
5. Aparecerá una nueva columna llamada `Catalogo` (con un icono de tabla). Haz clic en el icono de expansión (doble flecha) en el encabezado de esa columna.
6. En el panel de expansión:
   - **Desmarca** `SKU` (ya existe en la tabla).
   - **Marca:** `Nombre_Producto`, `Categoria`, `Unidad_Medida`, `Descripcion_Completa`.
   - Desmarca la opción **"Usar nombre de columna original como prefijo"**.
7. Haz clic en **Aceptar**.

**Resultado esperado:**

La tabla `Practica2_Demanda_Cruda` ahora tiene 7 columnas: `SKU`, `Fecha`, `Demanda_Unidades`, `AñoMes`, `Nombre_Producto`, `Categoria`, `Unidad_Medida`, `Descripcion_Completa`. Sigue teniendo 60 filas.

**Verificación:**

- 60 filas (sin cambio — merge izquierdo no agrega filas si la relación es N:1).
- La fila de SKU-A101 muestra `Caja Cartón Grande` en `Nombre_Producto` y `Embalaje` en `Categoria`.
- No hay valores nulos en las columnas expandidas (todos los SKUs tienen correspondencia en el catálogo).

---

### Paso 9 — Combinar con Inventario (Merge)

**Objetivo:** Agregar los datos de stock disponible y stock mínimo a la tabla de demanda enriquecida, combinando por `SKU` y `AñoMes`.

**Instrucciones:**

1. Con la consulta `Practica2_Demanda_Cruda` aún seleccionada, ve a **Inicio → Combinar consultas → Combinar consultas**.
2. En el cuadro de diálogo:
   - **Tabla superior:** `Practica2_Demanda_Cruda`.
   - Mantén presionada la tecla **Ctrl** y haz clic en las columnas `SKU` y `AñoMes` (en ese orden) para seleccionar ambas como clave compuesta.
   - **Tabla inferior:** selecciona `Inventario`.
   - Igualmente, selecciona las columnas `SKU` y `AñoMes` (Ctrl + clic en ese orden).
   - **Tipo de combinación:** `Externa izquierda (todas de la primera, coincidencias de la segunda)`.
3. Haz clic en **Aceptar**.
4. Expande la nueva columna `Inventario`:
   - **Marca:** `Stock_Disponible`, `Stock_Minimo`.
   - **Desmarca:** `SKU`, `Mes`, `Anio`, `AñoMes` (ya existen).
   - Desmarca **"Usar nombre de columna original como prefijo"**.
5. Haz clic en **Aceptar**.

**Resultado esperado:**

La tabla final tiene 10 columnas: `SKU`, `Fecha`, `Demanda_Unidades`, `AñoMes`, `Nombre_Producto`, `Categoria`, `Unidad_Medida`, `Descripcion_Completa`, `Stock_Disponible`, `Stock_Minimo`. Mantiene 60 filas.

**Verificación:**

- 60 filas exactas.
- Las columnas `Stock_Disponible` y `Stock_Minimo` muestran valores numéricos enteros sin nulos.
- Verifica un registro aleatorio: para SKU-C303 en enero 2024 (`AñoMes` = `2024-01`), los valores de stock deben coincidir con los del archivo original de inventario.

---

### Paso 10 — Renombrar la consulta final y cargar al modelo

**Objetivo:** Dar un nombre descriptivo a la consulta principal, deshabilitar la carga de las consultas auxiliares y cargar el modelo a Power BI Desktop.

**Instrucciones:**

1. **Renombrar la consulta principal:**
   - Haz clic derecho en `Practica2_Demanda_Cruda` en el panel izquierdo → **Cambiar nombre** → escribe `Modelo_Demanda_Inventario`.
2. **Deshabilitar carga de consultas auxiliares** (para que no aparezcan como tablas separadas en el modelo):
   - Haz clic derecho en la consulta `Catalogo` → **Habilitar carga** (desmarcar la casilla para que NO se cargue).
   - Haz clic derecho en la consulta `Inventario` → **Habilitar carga** (desmarcar).
   - Confirma con **Continuar** si aparece un aviso.
3. **Cargar al modelo:**
   - Ve a **Inicio → Cerrar y aplicar**.
4. Espera a que Power BI Desktop procese la carga (barra de progreso en la parte inferior).

**Resultado esperado:**

Power BI Desktop muestra la vista de informe. En el panel **Campos** (derecha) aparece una única tabla: `Modelo_Demanda_Inventario` con 10 campos.

**Verificación:**

- Panel de campos: solo `Modelo_Demanda_Inventario` visible.
- Haz clic en la vista **Datos** (icono de tabla en la barra lateral izquierda): verifica 60 filas y 10 columnas.
- Los tipos de datos se muestran correctamente: icono de calendario para `Fecha`, icono Σ para `Demanda_Unidades`, `Stock_Disponible` y `Stock_Minimo`.

---

### Paso 11 — Guardar el archivo .pbix

**Objetivo:** Guardar el modelo como archivo de práctica que será insumo para la Práctica 3.

**Instrucciones:**

1. Ve a **Archivo → Guardar como**.
2. Navega hasta `C:\ForecLog\Practicas\P2\`.
3. Nombre del archivo: `Practica2_Modelo_Analitico`
4. Haz clic en **Guardar**.

**Resultado esperado:**

Archivo `Practica2_Modelo_Analitico.pbix` guardado en `C:\ForecLog\Practicas\P2\`.

**Verificación:**

- Abre el Explorador de archivos y confirma que el archivo existe con tamaño > 0 KB.
- La barra de título de Power BI Desktop muestra: `Practica2_Modelo_Analitico — Power BI Desktop`.

---

## Validación y Pruebas

Realiza las siguientes verificaciones finales para confirmar que el modelo está correctamente construido:

| # | Verificación | Resultado esperado | ✓ |
|---|---|---|---|
| 1 | Número total de filas en `Modelo_Demanda_Inventario` | 60 | ☐ |
| 2 | Número de columnas | 10 | ☐ |
| 3 | Valores únicos en columna `SKU` | 5 (SKU-A101, SKU-B202, SKU-C303, SKU-D404, SKU-E505) | ☐ |
| 4 | Rango de fechas | 01/01/2024 a 01/12/2024 | ☐ |
| 5 | Valores nulos en `Demanda_Unidades` | 0 | ☐ |
| 6 | Valores nulos en `Stock_Disponible` | 0 | ☐ |
| 7 | Formato de `AñoMes` | "AAAA-MM" (ej. `2024-01`) | ☐ |
| 8 | Columna `Descripcion_Completa` contiene SKU + nombre + categoría | Sí | ☐ |
| 9 | Consultas auxiliares (`Catalogo`, `Inventario`) NO aparecen en panel de campos | Correcto | ☐ |
| 10 | Archivo guardado como `Practica2_Modelo_Analitico.pbix` en `C:\ForecLog\Practicas\P2\` | Existe | ☐ |

**Prueba adicional rápida:**

1. En la vista de informe, arrastra `SKU` al lienzo como tabla.
2. Arrastra `Demanda_Unidades` a la misma tabla.
3. Verifica que aparecen 5 filas con la suma de demanda anual por SKU.
4. Elimina la visualización de prueba (Supr) — no es parte del entregable.

---

## Solución de Problemas

### Problema 1: Error al combinar tablas — "La selección de columnas no coincide"

**Síntomas:** Al realizar el Merge en el Paso 9, Power Query muestra un mensaje de error indicando que las columnas seleccionadas no tienen coincidencias, o el resultado del merge muestra valores `null` en las columnas expandidas de inventario.

**Causa:** Los valores de la columna `AñoMes` en la tabla de demanda y en la tabla de inventario tienen formatos ligeramente diferentes. Por ejemplo, la demanda genera `"2024-1"` (sin cero inicial) mientras que inventario genera `"2024-01"`.

**Solución:**

1. Selecciona la consulta `Inventario` en el panel izquierdo.
2. Revisa los valores de la columna `AñoMes`. Si muestran `"2024-1"` en lugar de `"2024-01"`:
   - Elimina el paso de columna personalizada `AñoMes` (clic en la X junto al paso).
   - Recrea la columna con la fórmula corregida que incluye `Text.PadStart`:

```m
Text.From([Anio]) & "-" & Text.PadStart(Text.From([Mes]), 2, "0")
```

3. Regresa a la consulta principal y verifica que el merge ahora produce coincidencias.

---

### Problema 2: La columna `Fecha` muestra errores tras cambiar el tipo de dato

**Síntomas:** Al cambiar la columna `fecha` a tipo Fecha en el Paso 4, algunas celdas muestran `Error` en rojo en lugar de una fecha válida.

**Causa:** La configuración regional del sistema espera fechas en formato `MM/dd/yyyy` (inglés) pero el archivo CSV contiene fechas en formato `dd/MM/yyyy` (español). Power Query interpreta incorrectamente el día como mes.

**Solución:**

1. En el panel derecho ("Pasos aplicados"), elimina el paso de cambio de tipo que causó el error (clic en la X).
2. Selecciona la columna `fecha`.
3. Ve a **Transformar → Tipo de datos → Usar configuración regional…**
4. En el cuadro de diálogo:
   - **Tipo de datos:** Fecha
   - **Configuración regional:** Español (México)
5. Haz clic en **Aceptar**.
6. Verifica que todas las fechas se muestran correctamente sin errores.

---

## Limpieza

Al finalizar la práctica:

1. **NO elimines** el archivo `Practica2_Modelo_Analitico.pbix` — es insumo obligatorio para la Práctica 3.
2. Los archivos fuente originales (`.csv` y `.xlsx`) pueden conservarse en `C:\ForecLog\Practicas\P2\` como respaldo.
3. Si creaste visualizaciones de prueba durante la validación, elimínalas del lienzo para entregar el archivo limpio.
4. Cierra Power BI Desktop una vez guardado el archivo.

---

## Resumen

En esta práctica completaste el ciclo completo de preparación de datos en Power Query:

| Fase | Acciones realizadas |
|------|-------------------|
| **Conexión** | Importaste 3 fuentes heterogéneas (1 CSV + 2 Excel) |
| **Limpieza** | Eliminaste columnas innecesarias, filtraste devoluciones, corregiste tipos, eliminaste nulos/duplicados y recortaste espacios |
| **Enriquecimiento** | Creaste columnas calculadas con lenguaje M (`AñoMes`, `Descripcion_Completa`) |
| **Combinación** | Ejecutaste 2 operaciones Merge (demanda + catálogo por SKU; resultado + inventario por SKU + AñoMes) |
| **Carga** | Cargaste un modelo integrado de 60 filas × 10 columnas a Power BI Desktop |

**Conceptos clave aplicados:**
- Los pasos aplicados en Power Query son reproducibles: si los datos fuente se actualizan, basta con hacer clic en "Actualizar" para reaplicar toda la limpieza.
- Deshabilitar la carga de consultas auxiliares mantiene el modelo limpio y optimizado.
- La columna `AñoMes` como clave de combinación textual permite unir tablas que almacenan el período de formas diferentes (fecha completa vs. mes + año separados).

**Archivo entregable:** `C:\ForecLog\Practicas\P2\Practica2_Modelo_Analitico.pbix`

**Siguiente paso:** En la Práctica 3, utilizarás este archivo como punto de partida para construir medidas DAX de indicadores de inventario (rotación, cobertura, stock de seguridad) y crear el dashboard de monitoreo de demanda en Power BI.

### Recursos adicionales

- [Documentación oficial: Combinar consultas en Power Query](https://learn.microsoft.com/es-es/power-query/merge-queries-overview)
- [Referencia de funciones M: Text.PadStart](https://learn.microsoft.com/es-es/powerquery-m/text-padstart)
- [Referencia de funciones M: Date.ToText](https://learn.microsoft.com/es-es/powerquery-m/date-totext)
- [Mejores prácticas de Power Query en Power BI](https://learn.microsoft.com/es-es/power-bi/guidance/power-query-best-practices)
