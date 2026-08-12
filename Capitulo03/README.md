# "Centro de Monitoreo de Demanda e Inventario"

Construcción de un dashboard integral en Power BI que integre rotación, cobertura, stock de seguridad, rupturas de stock, tendencia de consumo y pronóstico vs. demanda real, a partir del modelo preparado en la Práctica 2.

## 1. Metadatos

| Campo | Valor |
|-------|-------|
| **Duración** | 55 minutos |
| **Complejidad** | Alta |
| **Nivel Bloom** | Crear |
| **Archivo de entrada** | `C:\ForecLog\Practicas\P2\Practica2_Modelo_Analitico.pbix` |
| **Archivo de salida** | `C:\ForecLog\Practicas\P3\Practica3_Dashboard_Monitoreo.pbix` |

---

## 2. Descripción General

En esta práctica construirás un dashboard completo de monitoreo de demanda e inventario en Power BI Desktop. Partirás del modelo analítico creado en la Práctica 2, generarás una tabla de Calendario con DAX, establecerás las relaciones del esquema de estrella, crearás medidas DAX para cuatro indicadores clave de inventario (rotación, cobertura, stock de seguridad y rupturas de stock) y finalmente construirás visualizaciones interactivas que incluyen tarjetas KPI, gráficos de tendencia, pronóstico automático mediante el panel de Analítica y segmentadores dinámicos por SKU y categoría.

---

## 3. Objetivos de Aprendizaje

Al completar esta práctica serás capaz de:

- [ ] Establecer el modelo de datos en Power BI Desktop definiendo relaciones entre las tablas de demanda, inventario, catálogo y una tabla de calendario generada con DAX.
- [ ] Crear medidas DAX para los cuatro indicadores clave de inventario: Rotación de Inventario, Cobertura de Inventario (días), Stock de Seguridad y Contador de Rupturas de Stock.
- [ ] Construir visualizaciones de tendencia y estacionalidad de la demanda histórica usando gráficos de líneas con segmentación temporal.
- [ ] Activar y configurar el pronóstico automático mediante el panel de Analítica (Analytics Pane) y construir una visualización comparativa de pronóstico vs. demanda real.
- [ ] Implementar segmentadores (slicers) por producto/categoría y filtros interactivos para habilitar el análisis dinámico en el dashboard final.

---

## 4. Prerrequisitos

### Conocimientos previos

| Requisito | Descripción |
|-----------|-------------|
| Práctica 2 completada | Contar con el archivo `Practica2_Modelo_Analitico.pbix` con las tablas Demanda, Inventario y Catálogo cargadas correctamente |
| DAX básico | Comprender la diferencia entre una medida y una columna calculada |
| Indicadores de inventario | Conocimiento conceptual de rotación, cobertura, stock de seguridad y rupturas de stock |
| Navegación en Power BI | Saber alternar entre Vista de Informe, Vista de Datos y Vista de Modelo |

### Acceso y software

| Software | Versión requerida |
|----------|-------------------|
| Power BI Desktop | 2.130.754.0 (agosto 2024) |
| Idioma de interfaz | Español (México) |
| Sistema operativo | Windows 10/11 (64 bits) |

---

## 5. Entorno del Laboratorio

### Estructura de carpetas

```
C:\ForecLog\Practicas\
├── P2\
│   └── Practica2_Modelo_Analitico.pbix   ← archivo de entrada
└── P3\
    └── Practica3_Dashboard_Monitoreo.pbix ← archivo de salida (se creará)
```

### Verificación previa al inicio

1. Confirmar que la carpeta `C:\ForecLog\Practicas\P3\` existe. Si no, crearla:

```powershell
New-Item -ItemType Directory -Path "C:\ForecLog\Practicas\P3" -Force
```

2. Verificar que Power BI Desktop está en la versión correcta:
   - Abrir Power BI Desktop → **Archivo** → **Acerca de** → Confirmar versión `2.130.754.0` o superior.

3. Verificar idioma:
   - **Archivo** → **Opciones y configuración** → **Opciones** → **Configuración regional** → Confirmar **Español (México)**.

### Dataset de referencia (5 SKUs del curso)

| SKU | Producto | Categoría |
|-----|----------|-----------|
| SKU-A101 | Caja Cartón Grande | Embalaje |
| SKU-B202 | Paleta de Madera Estándar | Soporte |
| SKU-C303 | Film Stretch 500m | Embalaje |
| SKU-D404 | Etiqueta Código de Barras | Identificación |
| SKU-E505 | Esquinero Cartón | Protección |

---

## 6. Instrucciones Paso a Paso

---

### Paso 1: Abrir el modelo base y guardar copia de trabajo

**Objetivo:** Crear el archivo de la Práctica 3 a partir del modelo de la Práctica 2 sin modificar el original.

**Instrucciones:**

1. Abrir Power BI Desktop.
2. Hacer clic en **Archivo** → **Abrir informe** → **Examinar** → navegar a `C:\ForecLog\Practicas\P2\Practica2_Modelo_Analitico.pbix` → **Abrir**.
3. Esperar a que el modelo cargue completamente (verificar que no aparezcan errores en la barra inferior).
4. Hacer clic en **Archivo** → **Guardar como** → navegar a `C:\ForecLog\Practicas\P3\` → nombrar el archivo `Practica3_Dashboard_Monitoreo.pbix` → **Guardar**.

**Resultado esperado:** El archivo se guarda en la ruta P3 y la barra de título muestra `Practica3_Dashboard_Monitoreo`.

**Verificación:** En el Explorador de archivos, confirmar que el archivo existe en `C:\ForecLog\Practicas\P3\` con tamaño > 0 KB.

---

### Paso 2: Crear la tabla Calendario con DAX

**Objetivo:** Generar una tabla de dimensión temporal que cubra el rango de datos del curso (enero 2024 – diciembre 2025) con columnas de Año, Mes, Trimestre y NombreMes.

**Instrucciones:**

1. En Power BI Desktop, ir a la pestaña **Modelado** en la cinta de opciones.
2. Hacer clic en **Nueva tabla**.
3. En la barra de fórmulas DAX que aparece, escribir el siguiente código:

```dax
Calendario =
ADDCOLUMNS(
    CALENDAR(DATE(2024, 1, 1), DATE(2025, 12, 31)),
    "Año", YEAR([Date]),
    "Mes", MONTH([Date]),
    "NombreMes", FORMAT([Date], "MMMM"),
    "Trimestre", "T" & ROUNDUP(MONTH([Date]) / 3, 0),
    "DiaSemana", WEEKDAY([Date], 2)
)
```

4. Presionar **Enter** para confirmar la creación de la tabla.
5. En el panel **Datos** (lado derecho), verificar que aparece la tabla `Calendario` con las columnas: `Date`, `Año`, `Mes`, `NombreMes`, `Trimestre`, `DiaSemana`.
6. Renombrar la columna `Date` a `Fecha`:
   - Ir a **Vista de Datos** (ícono de tabla en la barra lateral izquierda).
   - Seleccionar la tabla `Calendario`.
   - Hacer clic derecho en el encabezado de columna `Date` → **Cambiar nombre** → escribir `Fecha` → **Enter**.
7. Marcar como tabla de fechas:
   - En **Vista de Tabla** (Costado Izquierdo de PowerBI), con la tabla `Calendario` seleccionada, ir a la pestaña **Herramientas de tabla** en la cinta.
   - Hacer clic en **Marcar como tabla de fechas** → Activar → seleccionar la columna `Fecha` → **Aceptar**.

**Resultado esperado:** La tabla `Calendario` contiene 731 filas (365 días de 2024 + 366 días de 2025, siendo 2024 año bisiesto). La columna `Fecha` va del 01/01/2024 al 31/12/2025. Aparece un ícono de calendario pequeño junto al nombre de la tabla indicando que está marcada como tabla de fechas.

**Verificación:** En Vista de Datos, seleccionar la tabla Calendario y verificar en la barra inferior que muestra **731 filas**. 

---

### Paso 3: Configurar relaciones en la Vista de Modelo

**Objetivo:** Establecer las relaciones del esquema de estrella entre las tablas de hechos (Demanda, Inventario) y las dimensiones (Catálogo, Calendario).

**Instrucciones:**

1. Cambiar a **Vista de Modelo** (ícono de tres rectángulos conectados en la barra lateral izquierda).
2. Organizar visualmente las tablas arrastrándolas: colocar `Calendario` arriba al centro, `Catálogo` a la izquierda, `Demanda` en el centro-abajo e `Inventario` a la derecha-abajo.
3. **Relación 1 — Calendario → Demanda:**
   - Arrastrar el campo `Fecha` de la tabla `Calendario` hacia el campo `Fecha` (o `FechaDespacho`) de la tabla `Demanda`.
   - En el cuadro de diálogo que aparece, verificar:
     - Cardinalidad: **Muchos a uno (*:1)**
     - Dirección de filtro cruzado: **Única** (de Calendario hacia Demanda)
     - Marcar **Activar esta relación** ✓
   - Hacer clic en **Aceptar**.

4. **Relación 2 — Catálogo → Demanda:**
   - Arrastrar el campo `SKU` (o `CodigoProducto`) de la tabla `Catálogo` hacia el campo `SKU` (o `CodigoProducto`) de la tabla `Demanda`.
   - Verificar:
     - Cardinalidad: **Muchos a uno (*:1)**
     - Dirección de filtro cruzado: **Única** (de Catálogo hacia Demanda)
     - Activar esta relación ✓
   - Hacer clic en **Aceptar**.

5. **Relación 3 — Catálogo → Inventario:**
   - Arrastrar el campo `SKU` de la tabla `Catálogo` hacia el campo `SKU` de la tabla `Inventario`.
   - Verificar:
     - Cardinalidad: **Muchos a uno (*:1)**
     - Dirección de filtro cruzado: **Única** (de Catálogo hacia Inventario)
     - Activar esta relación ✓
   - Hacer clic en **Aceptar**.

6. **Relación 4 — Calendario → Inventario (inactiva):**
   - Arrastrar el campo `Fecha` de la tabla `Calendario` hacia el campo `Fecha` (o `FechaCorte`) de la tabla `Inventario`.
   - Si Power BI muestra un aviso de ambigüedad, verificar:
     - Cardinalidad: **Muchos a uno (*:1)**
     - Dirección de filtro cruzado: **Única**
     - **Desmarcar** "Activar esta relación" (dejar inactiva)
   - Hacer clic en **Aceptar**.
   - La línea de relación aparecerá como **línea discontinua** indicando que es inactiva.

**Resultado esperado:** En la Vista de Modelo se observan 4 líneas de relación: 3 líneas sólidas (activas) y 1 línea discontinua (inactiva entre Calendario e Inventario). Las flechas de filtro apuntan desde las dimensiones hacia las tablas de hechos.

**Verificación:** Hacer doble clic en cada relación y confirmar que la cardinalidad es `*:1` y la dirección es `Única`. No deben existir relaciones bidireccionales ni relaciones muchos-a-muchos.

---

### Paso 4: Crear medidas DAX para indicadores clave de inventario

**Objetivo:** Definir las cuatro medidas DAX que calcularán Rotación de Inventario, Cobertura (días), Stock de Seguridad y Rupturas de Stock.

**Instrucciones:**

1. En la **Vista de Informe**, seleccionar la tabla `Demanda` en el panel de Datos (lado derecho).
2. Ir a la pestaña **Modelado** → **Nueva medida**.
3. Crear la medida **Demanda Total**:

```dax
Demanda Total =
SUM(Demanda[UnidadesDespachadas])
```

4. Crear la medida **Demanda Promedio Mensual**:

```dax
Demanda Promedio Mensual =
AVERAGEX(
    VALUES(Calendario[Mes]),
    CALCULATE(SUM(Demanda[UnidadesDespachadas]))
)
```

5. Seleccionar la tabla `Inventario` en el panel de Datos. Crear la medida **Stock Actual**:

```dax
Stock Actual =
CALCULATE(
    SUM(Inventario[StockDisponible]),
    LASTDATE(Inventario[FechaCorte])
)
```

6. Crear la medida **Inventario Promedio**:

```dax
Inventario Promedio =
AVERAGEX(
    VALUES(Inventario[FechaCorte]),
    CALCULATE(SUM(Inventario[StockDisponible]))
)
```

7. Ahora crear las **cuatro medidas de indicadores clave**. Seleccionar la tabla `Demanda` y crear cada una:

**Medida: Rotación de Inventario**

```dax
Rotación de Inventario =
DIVIDE(
    [Demanda Total],
    [Inventario Promedio],
    0
)
```

**Medida: Cobertura Días**

```dax
Cobertura Días =
VAR DemandaPromDiaria = DIVIDE([Demanda Total], COUNTROWS(VALUES(Calendario[Fecha])), 0)
RETURN
    DIVIDE(
        [Stock Actual],
        DemandaPromDiaria,
        0
    )
```

**Medida: Stock de Seguridad**

```dax
Stock de Seguridad =
VAR Z = 1.65
VAR DesvDemanda =
    STDEV.P(Demanda[UnidadesDespachadas])
VAR LeadTime =
    AVERAGE(Catalogo[LeadTimeDias])
RETURN
    Z * DesvDemanda * SQRT(LeadTime)
```

> **Nota:** El valor Z = 1.65 corresponde a un nivel de servicio del 95%. El campo `LeadTimeDias` proviene de la tabla Catálogo.

**Medida: Rupturas de Stock**

```dax
Rupturas de Stock =
COUNTROWS(
    FILTER(
        Inventario,
        Inventario[StockDisponible] = 0
    )
)
```

8. Formatear las medidas:
   - Seleccionar `Rotación de Inventario` → en la pestaña **Herramientas de medida** → Formato: **Número decimal** (2 decimales).
   - Seleccionar `Cobertura Días` → Formato: **Número entero**.
   - Seleccionar `Stock de Seguridad` → Formato: **Número entero**.
   - Seleccionar `Rupturas de Stock` → Formato: **Número entero**.

**Resultado esperado:** En el panel de Datos, bajo las tablas correspondientes, aparecen las medidas con ícono de calculadora (fx). Al hacer clic en cada medida, la barra de fórmulas muestra el código DAX correcto.

**Verificación:** Crear una tabla temporal en el lienzo (Visualización → Tabla) y arrastrar las 4 medidas. Verificar que los valores no sean todos cero ni presenten errores. Valores de referencia aproximados para el dataset completo (sin filtro de SKU):
- Rotación de Inventario: valor > 0
- Cobertura Días: valor entre 10 y 90
- Stock de Seguridad: valor > 0
- Rupturas de Stock: valor ≥ 0 (número entero)

Eliminar la tabla temporal después de verificar.

---

### Paso 5: Construir Página 1 — Tarjetas KPI de indicadores

**Objetivo:** Crear la primera página del dashboard con tarjetas (cards) que muestren los cuatro indicadores clave de inventario de forma prominente.

**Instrucciones:**

1. En la Vista de Informe, hacer clic derecho en la pestaña de página actual (parte inferior) → **Cambiar nombre de página** → escribir `KPIs Inventario`.
2. En el panel **Visualizaciones** (lado derecho), seleccionar el visual **Tarjeta** (ícono con un número grande).
3. Arrastrar la medida `Rotación de Inventario` al campo **Campos** de la tarjeta.
4. Posicionar la tarjeta en la esquina superior izquierda del lienzo. Redimensionar a aproximadamente 250 x 120 px.
5. Con la tarjeta seleccionada, ir a **Formato** (ícono de rodillo de pintura):
   - **Etiqueta de categoría** → Activar → Texto: "Rotación de Inventario"
   - **Etiqueta de datos** → Tamaño de fuente: 28
6. Repetir los pasos 2-5 para crear tres tarjetas adicionales:
   - Tarjeta 2: medida `Cobertura Días` — etiqueta "Cobertura (Días)"
   - Tarjeta 3: medida `Stock de Seguridad` — etiqueta "Stock de Seguridad"
   - Tarjeta 4: medida `Rupturas de Stock` — etiqueta "Rupturas de Stock"
7. Alinear las cuatro tarjetas horizontalmente en la parte superior de la página:
   - Seleccionar las cuatro tarjetas (Ctrl + clic en cada una).
   - Pestaña **Formato** → **Alinear** → **Alinear arriba**.
   - **Formato** → **Distribuir** → **Distribuir horizontalmente**.
8. Agregar un título a la página:
   - Insertar un **Cuadro de texto** (pestaña **Insertar** → **Cuadro de texto**).
   - Escribir: **"Centro de Monitoreo de Demanda e Inventario"**
   - Formato: Negrita, tamaño 20, centrado.
   - Posicionar encima de las tarjetas KPI.

**Resultado esperado:** La página `KPIs Inventario` muestra un título y cuatro tarjetas alineadas horizontalmente, cada una con el valor numérico del indicador y su etiqueta descriptiva.

**Verificación:** Los valores de las tarjetas deben ser numéricos (no "Error" ni vacíos). Si alguna tarjeta muestra "En blanco", verificar que las relaciones del Paso 3 estén activas.

---

### Paso 6: Construir Página 2 — Gráfico de tendencia de demanda histórica

**Objetivo:** Crear un gráfico de líneas que muestre la evolución mensual de la demanda con capacidad de segmentación por SKU y período.

**Instrucciones:**

1. Crear una nueva página: clic en el ícono **+** en la parte inferior → Renombrar a `Tendencia Demanda`.
2. En el panel Visualizaciones, seleccionar **Gráfico de líneas** (ícono de línea con puntos).
3. Configurar los campos del gráfico:
   - **Eje X:** Arrastrar `Calendario[Fecha]` → Power BI creará automáticamente la jerarquía de fechas. Hacer clic en la flecha hacia abajo (▼) en el eje X del visual hasta llegar al nivel **Mes**.
   - **Eje Y:** Arrastrar la medida `Demanda Total`.
   - **Leyenda:** Arrastrar `Catálogo[SKU]`.
4. Redimensionar el gráfico para ocupar aproximadamente el 60% del ancho de la página y 50% de la altura.
5. Formatear el gráfico:
   - Seleccionar el visual → **Formato** → **Título** → Texto: "Tendencia de Demanda Mensual por SKU"
   - **Eje X** → Título: "Mes"
   - **Eje Y** → Título: "Unidades Despachadas"
6. **Agregar segmentador de SKU:**
   - En un espacio vacío del lienzo, seleccionar el visual **Segmentador** (ícono de embudo con lista).
   - Arrastrar `Catálogo[SKU]` al campo **Campo** del segmentador.
   - Con el segmentador seleccionado → **Formato** → **Configuración del segmentador** → Estilo: **Lista** (o **Menú desplegable** si se prefiere ahorrar espacio).
   - Posicionar a la izquierda del gráfico de líneas.
7. **Agregar segmentador temporal (por Trimestre):**
   - Insertar otro segmentador.
   - Arrastrar `Calendario[Trimestre]` al campo del segmentador.
   - Formato → Estilo: **Botones** (tiles).
   - Posicionar encima del gráfico de líneas.

**Resultado esperado:** La página muestra un gráfico de líneas con 5 líneas de color diferente (una por SKU) que muestran la tendencia mensual de enero a diciembre 2024. Los segmentadores permiten filtrar por SKU individual y por trimestre.

**Verificación:** 
- Hacer clic en "SKU-C303" en el segmentador de SKU → el gráfico debe mostrar solo una línea.
- Hacer clic en "T1" en el segmentador de Trimestre → el gráfico debe mostrar solo enero, febrero y marzo.
- Hacer clic en "Borrar filtros" (ícono de goma) en cada segmentador para restaurar la vista completa.

---

### Paso 7: Configurar pronóstico automático con el Panel de Analítica

**Objetivo:** Activar la función de pronóstico integrada de Power BI en el gráfico de líneas para proyectar la demanda futura con intervalos de confianza del 95%.

**Instrucciones:**

1. En la página `Tendencia Demanda`, seleccionar el gráfico de líneas creado en el Paso 6.
2. **Importante:** Para que el pronóstico funcione, el Eje X debe contener un campo de fecha continuo (no jerárquico). Modificar la configuración:
   - En el panel Campos del visual, en **Eje X**, hacer clic en la flecha desplegable junto a `Fecha` → seleccionar **Fecha** (no la jerarquía "Año > Trimestre > Mes > Día").
   - Esto cambia el eje a un eje de fecha continuo.
3. **Importante:** Remover el campo de Leyenda temporalmente para que el pronóstico funcione sobre una sola serie:
   - Remover `Catálogo[SKU]` del campo **Leyenda** (arrastrar fuera o hacer clic en la X).
   - El gráfico ahora muestra una sola línea agregada de demanda total.
4. Con el gráfico seleccionado, ir al panel **Analítica** (ícono de lupa con gráfico, al lado del ícono de formato/rodillo de pintura).
5. Expandir la sección **Pronóstico**.
6. Hacer clic en **+ Agregar** para activar el pronóstico.
7. Configurar los parámetros:
   - **Unidades de pronóstico:** 3 (puntos hacia el futuro)
   - **Unidad:** Meses
   - **Intervalo de confianza:** 95%
   - **Estacionalidad:** Detectar automáticamente (o establecer en 12 si se conoce el patrón anual)
   - **Color de línea de pronóstico:** Seleccionar un color diferente al de la línea principal (ej. naranja o rojo punteado).
8. El gráfico ahora mostrará:
   - La línea sólida de demanda histórica (enero–diciembre 2024).
   - Una línea punteada/extendida con el pronóstico (enero–marzo 2025).
   - Una banda sombreada representando el intervalo de confianza del 95%.

**Resultado esperado:** El gráfico de líneas muestra la demanda histórica seguida de una extensión de pronóstico con banda de confianza. La línea de pronóstico se extiende 3 meses más allá del último dato histórico.

**Verificación:** 
- La banda de confianza debe ser visible como un área sombreada alrededor de la línea de pronóstico.
- Si el pronóstico no aparece, verificar que el Eje X esté configurado como fecha continua (no jerárquica) y que no haya campo en Leyenda.
- Pasar el cursor sobre la zona de pronóstico → debe aparecer un tooltip con el valor pronosticado y los límites inferior/superior del intervalo.

---

### Paso 8: Construir gráfico comparativo Pronóstico vs. Demanda Real

**Objetivo:** Crear una visualización que permita comparar los valores pronosticados contra la demanda real para evaluar la precisión del modelo.

**Instrucciones:**

1. Crear una nueva página: clic en **+** → Renombrar a `Pronóstico vs Real`.
2. Primero, crear una medida auxiliar que simule un valor de pronóstico basado en promedio móvil (para comparación visual):

```dax
Pronóstico Promedio Móvil =
VAR MesesAtras = 3
VAR PromedioMovil =
    CALCULATE(
        AVERAGEX(
            VALUES(Calendario[Fecha]),
            [Demanda Total]
        ),
        DATESINPERIOD(
            Calendario[Fecha],
            MAX(Calendario[Fecha]),
            -MesesAtras,
            MONTH
        )
    )
RETURN
    PromedioMovil
```

3. Insertar un **Gráfico de líneas** en el lienzo.
4. Configurar los campos:
   - **Eje X:** `Calendario[Fecha]` (seleccionar el campo directo, no la jerarquía — usar nivel Mes).
   - **Valores del Eje Y:** Arrastrar `Demanda Total` (primera línea).
   - Arrastrar `Pronóstico Promedio Móvil` al mismo campo de Valores del Eje Y (segunda línea).
5. Formatear:
   - **Título:** "Pronóstico vs. Demanda Real"
   - En **Formato** → **Líneas** → configurar:
     - `Demanda Total`: línea sólida, color azul, grosor 2.5
     - `Pronóstico Promedio Móvil`: línea discontinua (guiones), color naranja, grosor 2
6. Agregar el segmentador de SKU (copiar del paso anterior o crear uno nuevo):
   - Insertar segmentador → Campo: `Catálogo[SKU]`.
   - Posicionar a la izquierda del gráfico.
7. Redimensionar el gráfico para ocupar la mayor parte de la página.

**Resultado esperado:** Un gráfico de líneas con dos series: una línea azul sólida (demanda real) y una línea naranja discontinua (pronóstico por promedio móvil). Ambas líneas se superponen mostrando las desviaciones entre pronóstico y realidad.

**Verificación:** 
- Seleccionar "SKU-C303" en el segmentador → las líneas deben mostrar mayor divergencia (SKU-C303 es el de mayor variabilidad/MAPE según el dataset del curso).
- Seleccionar "SKU-A101" → las líneas deben estar más cercanas entre sí (menor error de pronóstico).

---

### Paso 9: Agregar segmentadores interactivos y filtros finales

**Objetivo:** Implementar segmentadores por categoría de producto y configurar las interacciones entre visualizaciones para un análisis dinámico completo.

**Instrucciones:**

1. Volver a la página `KPIs Inventario` (primera página).
2. Insertar un segmentador de **Categoría**:
   - Visual: Segmentador → Campo: `Catálogo[Categoria]`.
   - Formato → Estilo: **Botones** (tiles).
   - Posicionar debajo de las tarjetas KPI.
3. Insertar un segmentador de **SKU**:
   - Visual: Segmentador → Campo: `Catálogo[SKU]`.
   - Formato → Estilo: **Menú desplegable**.
   - Posicionar junto al segmentador de Categoría.
4. Insertar un segmentador de **Período (Año-Mes)**:
   - Visual: Segmentador → Campo: `Calendario[Fecha]`.
   - Power BI lo convertirá automáticamente en un segmentador de rango de fechas (slider).
   - Posicionar en la parte inferior de la página.
5. **Configurar interacciones entre visualizaciones:**
   - Seleccionar el segmentador de Categoría.
   - Ir a la pestaña **Formato** (de la cinta superior) → **Editar interacciones**.
   - Verificar que todas las tarjetas KPI muestren el ícono de **Filtro** (embudo) activo. Si alguna muestra "Ninguno", hacer clic en el ícono de embudo para activar la interacción.
   - Hacer clic en **Editar interacciones** nuevamente para salir del modo de edición.
6. **Repetir la adición de segmentadores en las otras páginas:**
   - Ir a la página `Tendencia Demanda` → verificar que ya tiene segmentadores de SKU y Trimestre (del Paso 6).
   - Ir a la página `Pronóstico vs Real` → verificar que ya tiene segmentador de SKU (del Paso 8).
7. **Sincronizar segmentadores entre páginas (opcional pero recomendado):**
   - Seleccionar el segmentador de SKU en la página `KPIs Inventario`.
   - Ir a **Vista** → **Sincronizar segmentadores** (panel lateral).
   - Marcar la casilla de sincronización para las tres páginas.
   - Repetir para el segmentador de Categoría si existe en múltiples páginas.

**Resultado esperado:** La página `KPIs Inventario` ahora tiene segmentadores de Categoría, SKU y rango de fechas. Al seleccionar una categoría (ej. "Embalaje"), las tarjetas KPI se actualizan mostrando solo los indicadores para SKU-A101, SKU-C303 y SKU-E505.

**Verificación:**
- Seleccionar Categoría = "Embalaje" → verificar que `Rotación de Inventario` cambia de valor.
- Seleccionar SKU = "SKU-C303" → verificar que `Cobertura Días` muestra un valor específico para ese producto.
- Mover el slider de fecha para seleccionar solo T1 (ene–mar 2024) → los KPIs deben reflejar solo ese período.
- Limpiar todos los filtros y confirmar que los valores vuelven al total general.

---

### Paso 10: Guardar el archivo final

**Objetivo:** Guardar el dashboard completo como archivo de salida de la Práctica 3.

**Instrucciones:**

1. Presionar **Ctrl + S** para guardar.
2. Verificar que el archivo se guarda en `C:\ForecLog\Practicas\P3\Practica3_Dashboard_Monitoreo.pbix`.
3. Revisar rápidamente las tres páginas del dashboard:
   - Página 1 (`KPIs Inventario`): 4 tarjetas + 3 segmentadores + título.
   - Página 2 (`Tendencia Demanda`): gráfico de líneas con pronóstico + 2 segmentadores.
   - Página 3 (`Pronóstico vs Real`): gráfico comparativo + 1 segmentador.

**Resultado esperado:** El archivo `Practica3_Dashboard_Monitoreo.pbix` está guardado y contiene un dashboard funcional de 3 páginas con indicadores, visualizaciones y segmentadores interactivos.

**Verificación:** Cerrar Power BI Desktop y volver a abrir el archivo desde `C:\ForecLog\Practicas\P3\`. Confirmar que todas las visualizaciones cargan correctamente y los segmentadores funcionan.

---

## 7. Validación y Pruebas

Realizar las siguientes pruebas de aceptación para confirmar que el dashboard funciona correctamente:

| # | Prueba | Acción | Resultado esperado |
|---|--------|--------|--------------------|
| 1 | Modelo de datos | Vista de Modelo → contar relaciones | 4 relaciones: 3 activas + 1 inactiva |
| 2 | Tabla Calendario | Vista de Datos → seleccionar Calendario | 731 filas, columnas: Fecha, Año, Mes, NombreMes, Trimestre, DiaSemana |
| 3 | Medida Rotación | Crear tabla temporal con Rotación de Inventario sin filtros | Valor numérico > 0 |
| 4 | Medida Rupturas | Filtrar por SKU con stock = 0 | Rupturas de Stock ≥ 1 |
| 5 | Pronóstico Analytics | Página Tendencia → verificar banda de confianza | Banda sombreada visible en zona de pronóstico |
| 6 | Segmentador SKU | Seleccionar SKU-C303 → verificar todas las páginas | Todas las visualizaciones se filtran a SKU-C303 |
| 7 | Comparativo | Página Pronóstico vs Real → verificar dos líneas | Línea azul (real) y naranja (pronóstico) visibles |

---

## 8. Solución de Problemas

### Problema 1: El pronóstico no aparece en el panel de Analítica

**Síntomas:** Al seleccionar el gráfico de líneas y abrir el panel de Analítica, la opción "Pronóstico" no está disponible o aparece deshabilitada (gris).

**Causa:** El Eje X del gráfico está configurado con una jerarquía de fechas (Año > Trimestre > Mes > Día) en lugar de un campo de fecha continuo. El pronóstico de Power BI solo funciona con ejes de fecha continuos y sin campo en Leyenda.

**Solución:**
1. Seleccionar el gráfico de líneas.
2. En el panel de Campos del visual, en **Eje X**, hacer clic en la flecha desplegable junto al campo de fecha.
3. Seleccionar el nombre del campo directamente (ej. `Fecha`) en lugar de cualquier nivel de la jerarquía.
4. Remover cualquier campo del área **Leyenda** del visual.
5. El panel de Analítica ahora debe mostrar la opción "Pronóstico" disponible.

---

### Problema 2: Las tarjetas KPI muestran "En blanco" o valores incorrectos

**Síntomas:** Una o más tarjetas de indicadores muestran el texto "(En blanco)" o valores que no cambian al usar los segmentadores.

**Causa:** Las relaciones entre tablas no están configuradas correctamente. Específicamente, el campo de unión entre `Catálogo` y `Demanda` (o `Inventario`) tiene tipos de datos incompatibles (ej. uno es Texto y el otro es Número entero), o la relación no se creó.

**Solución:**
1. Ir a **Vista de Modelo**.
2. Verificar que existen las líneas de relación entre las tablas. Si falta alguna, crearla arrastrando los campos correspondientes.
3. Hacer doble clic en cada relación existente y verificar:
   - Que los campos de unión sean los correctos (`SKU` con `SKU`, `Fecha` con `Fecha`).
   - Que la cardinalidad sea `*:1`.
   - Que la relación esté **activa** (casilla marcada).
4. Si los tipos de datos no coinciden:
   - Ir a **Vista de Datos** → seleccionar la columna problemática.
   - En la pestaña **Herramientas de columna** → cambiar el **Tipo de datos** al correcto.
5. Volver a la Vista de Informe y verificar que las tarjetas muestran valores numéricos.

---

## 9. Limpieza

Al finalizar la práctica:

1. **Conservar** el archivo `C:\ForecLog\Practicas\P3\Practica3_Dashboard_Monitoreo.pbix` — es el insumo obligatorio para la Práctica 4.
2. **No modificar** el archivo original `C:\ForecLog\Practicas\P2\Practica2_Modelo_Analitico.pbix`.
3. Si durante la práctica se crearon tablas temporales de verificación en el lienzo, eliminarlas seleccionándolas y presionando **Suprimir**.
4. Verificar que no quedaron páginas de prueba adicionales en el archivo (solo deben existir las 3 páginas del dashboard).

---

## 10. Resumen

En esta práctica completaste la construcción de un dashboard integral de monitoreo de demanda e inventario en Power BI Desktop. Los logros clave incluyen:

| Componente | Resultado |
|------------|-----------|
| Tabla Calendario | Creada con `CALENDAR()` + `ADDCOLUMNS()`, 731 filas, marcada como tabla de fechas |
| Modelo de datos | Esquema de estrella con 4 relaciones (3 activas + 1 inactiva) |
| Medidas DAX | 6 medidas creadas: Demanda Total, Demanda Promedio Mensual, Stock Actual, Inventario Promedio + 4 KPIs (Rotación, Cobertura, Stock de Seguridad, Rupturas) |
| Visualizaciones | 3 páginas: KPIs con tarjetas, Tendencia con pronóstico, Comparativo Real vs. Pronóstico |
| Interactividad | Segmentadores por SKU, Categoría y rango de fechas con sincronización entre páginas |

### Próximos pasos

El archivo `Practica3_Dashboard_Monitoreo.pbix` será utilizado directamente en la **Práctica 4**, donde emplearás Microsoft 365 Copilot para interpretar la variabilidad de SKU-C303 (el SKU crítico con mayor MAPE y menor cobertura), generar escenarios de demanda y redactar recomendaciones de reabastecimiento.

### Recursos adicionales

- [Documentación oficial: Función CALENDAR en DAX](https://learn.microsoft.com/es-es/dax/calendar-function-dax)
- [Documentación oficial: Crear relaciones en Power BI Desktop](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-create-and-manage-relationships)
- [Documentación oficial: Pronóstico en gráficos de líneas (Analytics Pane)](https://learn.microsoft.com/es-es/power-bi/transform-model/desktop-analytics-pane)
- [Referencia DAX: Función DIVIDE](https://learn.microsoft.com/es-es/dax/divide-function-dax)
- [SQLBI: Mejores prácticas para esquema de estrella](https://www.sqlbi.com/articles/star-schema-vs-snowflake-schema-in-power-bi/)

---
