# "¿Qué tan confiable es mi pronóstico?" – Cálculo de MAPE por SKU y clasificación de confiabilidad (alta/media/baja) para priorizar qué productos requieren revisión de inventario

## Metadata

| Campo | Detalle |
|-------|---------|
| **Duración** | 35 minutos |
| **Complejidad** | Fácil |
| **Nivel Bloom** | Aplicar |
| **Herramienta principal** | Microsoft Excel (Microsoft 365 / Excel 2021) |
| **Archivo de entrada** | `C:\ForecLog\Practicas\P1\Practica1_Demanda_SKU.xlsx` |

---

## Descripción General

En esta práctica aplicarás las funciones `TENDENCIA()` y `PRONOSTICO.LINEAL()` sobre la demanda histórica de 5 SKUs de bodega para generar pronósticos mensuales. A continuación, medirás la precisión de cada pronóstico calculando el Error Absoluto, el MAE y el MAPE período a período. Finalmente, clasificarás cada SKU en niveles de confiabilidad (alta / media / baja) para determinar cuáles requieren revisión prioritaria de inventario. Este resultado será la referencia para identificar el SKU crítico en la Práctica 4.

---

## Objetivos de Aprendizaje

- [ ] Aplicar `TENDENCIA()` y `PRONOSTICO.LINEAL()` para generar pronósticos sobre 12 períodos de demanda histórica por SKU.
- [ ] Utilizar la Hoja de Pronóstico de Excel para visualizar la proyección automática con intervalos de confianza del SKU de mayor volumen.
- [ ] Calcular el error absoluto, MAE y MAPE por SKU mediante fórmulas de Excel.
- [ ] Clasificar cada SKU según su MAPE (alta ≤ 10 %, media 10–20 %, baja > 20 %) usando `SI()` anidada.
- [ ] Identificar qué productos requieren revisión prioritaria de inventario con base en la clasificación obtenida.

---

## Prerrequisitos

### Conocimientos previos

| Requisito | Descripción |
|-----------|-------------|
| Navegación básica en Excel | Ingresar fórmulas, copiar rangos, dar formato a celdas |
| Conceptos de demanda | Diferencia entre demanda histórica y demanda proyectada (Temas 1.1 y 1.2) |
| Regresión lineal (concepto) | Comprender que una recta de tendencia resume la dirección de los datos |

### Acceso requerido

- Equipo con Microsoft Excel (Microsoft 365 versión 2308+ o Excel 2021) instalado y configurado en **Español (México)**.
- Archivo `Practica1_Demanda_SKU.xlsx` descargado en `C:\ForecLog\Practicas\P1\`.
- Verificar que la opción **Datos → Hoja de pronóstico** esté visible en la cinta de Excel.

---

## Entorno del Laboratorio

### Hardware mínimo

| Componente | Especificación |
|------------|---------------|
| Procesador | 64 bits — Intel Core i5 8.ª gen. o AMD Ryzen 5 3000+ |
| RAM | 8 GB (16 GB recomendado) |
| Disco libre | 2 GB |
| Pantalla | 1366 × 768 (1920 × 1080 recomendado) |

### Software requerido

| Software | Versión |
|----------|---------|
| Microsoft Excel | Microsoft 365 Apps v2308 (build 16.0.16731.20170+) o Excel 2021 |
| Sistema operativo | Windows 10/11 (64 bits) |

### Preparación del entorno

1. Crea la carpeta de trabajo si no existe:

```
C:\ForecLog\Practicas\P1\
```

2. Coloca el archivo `Practica1_Demanda_SKU.xlsx` dentro de esa carpeta.

3. Abre Excel → **Archivo → Opciones → Idioma** → confirma que el idioma de edición y de presentación sea **Español (México)**.

---

## Estructura del Archivo de Datos

El archivo `Practica1_Demanda_SKU.xlsx` contiene una hoja llamada **Demanda** con la siguiente estructura:

| Columna A | Columna B | Columna C | Columna D | Columna E | Columna F |
|-----------|-----------|-----------|-----------|-----------|-----------|
| **Mes** | **SKU-A101** | **SKU-B202** | **SKU-C303** | **SKU-D404** | **SKU-E505** |
| 1 | 520 | 180 | 410 | 1000 | 310 |
| 2 | 535 | 175 | 450 | 1020 | 325 |
| 3 | 548 | 190 | 380 | 1045 | 340 |
| 4 | 560 | 185 | 500 | 1060 | 332 |
| 5 | 575 | 178 | 420 | 1080 | 348 |
| 6 | 590 | 192 | 530 | 1100 | 360 |
| 7 | 605 | 170 | 390 | 1125 | 355 |
| 8 | 618 | 188 | 560 | 1140 | 370 |
| 9 | 632 | 182 | 430 | 1160 | 365 |
| 10 | 645 | 195 | 580 | 1180 | 380 |
| 11 | 660 | 172 | 410 | 1200 | 375 |
| 12 |  |  |  |  |  |

> **Nota:** SKU-A101 muestra tendencia creciente estable; SKU-B202 es relativamente plano con fluctuaciones; SKU-C303 presenta alta variabilidad (será el SKU crítico); SKU-D404 tiene tendencia creciente consistente; SKU-E505 muestra crecimiento moderado.

---

## Paso a Paso

### Paso 1 — Revisión y comprensión del dataset

**Objetivo:** Familiarizarte con la estructura de datos y verificar que el archivo está completo.

**Instrucciones:**

1. Abre el archivo `C:\ForecLog\Practicas\P1\Practica1_Demanda_SKU.xlsx` en Excel.
2. Ubica la hoja **Demanda**. Confirma que existen 12 filas de datos (meses 1 a 12) y 5 columnas de SKU (B a F).
3. Observa visualmente qué SKUs muestran tendencia clara y cuáles presentan variabilidad alta. Anota mentalmente tus observaciones.
4. Selecciona el rango `A1:F13` y aplica formato de tabla: **Inicio → Dar formato como tabla → Estilo claro** (cualquier estilo). Acepta que la tabla tiene encabezados.
5. Renombra la tabla como `TblDemanda` desde la pestaña **Diseño de tabla → Nombre de la tabla**.

**Resultado esperado:** Una tabla formateada con 12 registros y 6 columnas (Mes + 5 SKUs), nombre `TblDemanda`.

**Verificación:** En la barra de nombres (esquina superior izquierda) escribe `TblDemanda` y presiona Enter; la tabla completa debe seleccionarse.

---

### Paso 2 — Generar pronósticos con PRONOSTICO.LINEAL()

**Objetivo:** Aplicar `PRONOSTICO.LINEAL()` para calcular el valor pronosticado de cada mes (1–12) usando la regresión lineal ajustada a los propios datos históricos, simulando un pronóstico retrospectivo (fitted values).

**Instrucciones:**

1. Inserta una nueva hoja y nómbrala **Pronósticos**.
2. En la celda `A1` escribe `Mes`; en `B1` escribe `A101_Real`; en `C1` escribe `A101_Pronostico`. Repite el patrón para los 5 SKUs (columnas D–K para los SKUs restantes: Real y Pronóstico alternados).

   Estructura de encabezados sugerida:

   | A | B | C | D | E | F | G | H | I | J | K |
   |---|---|---|---|---|---|---|---|---|---|---|
   | Mes | A101_Real | A101_Pron | B202_Real | B202_Pron | C303_Real | C303_Pron | D404_Real | D404_Pron | E505_Real | E505_Pron |

3. En `A2:A13` escribe los números 1 a 12.
4. En `B2:B13` vincula la demanda real de SKU-A101 desde la hoja Demanda:
   ```excel
   =TblDemanda[@[SKU-A101]]
   ```
   Copia hacia abajo hasta la fila 13.

5. En `C2` escribe la fórmula de pronóstico lineal para el mes 1 de SKU-A101:
   ```excel
   =PRONOSTICO.LINEAL(A2;TblDemanda[SKU-A101];TblDemanda[Mes])
   ```
   > Esta fórmula calcula qué valor habría predicho la recta de regresión para cada mes. Usamos referencias absolutas en `y_conocidos` y `x_conocidos` para poder copiar la fórmula.

6. Copia `C2` hacia abajo hasta `C13`.
7. Repite los pasos 4–6 para los demás SKUs, ajustando la referencia de columna en la hoja Demanda:
   - SKU-B202: columna C de Demanda → columnas D (Real) y E (Pronóstico) en hoja Pronósticos.
   - SKU-C303: columna D de Demanda → columnas F (Real) y G (Pronóstico).
   - SKU-D404: columna E de Demanda → columnas H (Real) e I (Pronóstico).
   - SKU-E505: columna F de Demanda → columnas J (Real) y K (Pronóstico).

**Resultado esperado:** 12 valores pronosticados por SKU. Para SKU-A101, el pronóstico del mes 1 debería ser aproximadamente **517.6** y el del mes 12 aproximadamente **677.4** (valores de la recta ajustada).

**Verificación:** Compara el pronóstico del mes 6 de SKU-D404. Con tendencia lineal consistente, el valor pronosticado debe estar cercano al valor real (≈ 1100). Si la diferencia es mayor a 30 unidades, revisa las referencias de la fórmula.

**Regresa a la hoja Demanda, y aplicando lo aprendido, calcula los valores del mes 12**

---

### Paso 3 — Generar pronósticos con TENDENCIA()

**Objetivo:** Aplicar `TENDENCIA()` como fórmula matricial para obtener los 12 valores ajustados simultáneamente y verificar que coinciden con `PRONOSTICO.LINEAL()`.

**Instrucciones:**

1. En la hoja **Pronósticos**, inserta una columna adicional al final por cada columna de pronóstico, con encabezado `_Tend`. Ejemplo: en `L1` escribe `A101_Tend`.

   > **Alternativa simplificada:** Para evitar reestructurar, usa las columnas L a P (una por SKU) para los resultados de TENDENCIA.

2. Selecciona el rango `L2:L13` (12 celdas).
3. Escribe la fórmula:
   ```excel
   =TENDENCIA(Demanda!$B$2:$B$13, Demanda!$A$2:$A$13, $A$2:$A$13)
   ```
4. Confirma con **Ctrl + Mayús + Enter** (en versiones anteriores a Microsoft 365 dinámico). En Microsoft 365 basta con Enter.
5. Repite para los demás SKUs en columnas M, N, O y P, cambiando la referencia de `y_conocidos`:
   - M: `Demanda!$C$2:$C$13` (SKU-B202)
   - N: `Demanda!$D$2:$D$13` (SKU-C303)
   - O: `Demanda!$E$2:$E$13` (SKU-D404)
   - P: `Demanda!$F$2:$F$13` (SKU-E505)

**Resultado esperado:** Los valores de `TENDENCIA()` deben ser **idénticos** a los de `PRONOSTICO.LINEAL()` para cada mes y SKU, ya que ambas funciones calculan la misma regresión lineal simple.

**Verificación:** Resta la columna de TENDENCIA menos la de PRONOSTICO.LINEAL para SKU-A101 (por ejemplo, `=L2-C2`). El resultado debe ser **0** (o un valor despreciable < 0.001 por redondeo).

**Regresa a la hoja Demanda, y aplicando lo aprendido, calcula los valores del mes 12, puedes agregar una nueva fila**


---

### Paso 4 — Hoja de Pronóstico para el SKU de mayor volumen

**Objetivo:** Utilizar la herramienta visual Hoja de Pronóstico para proyectar la demanda del SKU con mayor volumen total (SKU-D404) e interpretar los intervalos de confianza.

**Instrucciones:**

1. Regresa a la hoja **Demanda**.
2. Selecciona las columnas `A1:A13` (Mes) y `E1:E13` (SKU-D404). Para seleccionar columnas no contiguas, selecciona A1:A13, mantén presionada la tecla **Ctrl** y selecciona E1:E13.

   > **Importante:** Si la selección no contigua genera error, copia temporalmente las dos columnas (Mes y SKU-D404) a una hoja auxiliar de forma contigua y selecciona desde ahí.

3. Ve a la pestaña **Datos → Hoja de pronóstico** (grupo "Pronóstico").
4. En el asistente que aparece:
   - **Fin del pronóstico:** establece el valor **15** (proyectar 3 meses adicionales: 13, 14, 15).
   - **Inicio del pronóstico:** deja el valor predeterminado (13).
   - **Intervalo de confianza:** mantén **95 %**.
   - Tipo de gráfico: **Línea**.
5. Haz clic en **Crear**.

**Resultado esperado:** Excel genera una nueva hoja con:
- Tabla con columnas: Mes | Pronóstico | Límite inferior de confianza | Límite superior de confianza.
- Gráfico de líneas que muestra la demanda histórica (meses 1–12) y la proyección (meses 13–15) con banda sombreada.
- Valores proyectados para SKU-D404 aproximados: Mes 13 ≈ 1240, Mes 14 ≈ 1260, Mes 15 ≈ 1280.

**Verificación:** Confirma que el intervalo de confianza del mes 15 es más amplio que el del mes 13 (la incertidumbre crece con el horizonte de pronóstico). Verifica que la banda sombreada sea visible en el gráfico.

---

### Paso 5 — Cálculo de Error Absoluto y Error Porcentual Absoluto

**Objetivo:** Construir las columnas de error período a período para cada SKU, como base para el cálculo de MAE y MAPE.

**Instrucciones:**

1. Crea una nueva hoja y nómbrala **Errores**.
2. Establece la siguiente estructura de encabezados (fila 1):

   | A | B | C | D | E |
   |---|---|---|---|---|
   | Mes | SKU | Real | Pronóstico | Error_Abs |

   Agrega en `F1`: `Error_Pct_Abs`.

3. Llena los datos para los 5 SKUs × 12 meses = **60 filas** (filas 2 a 61). Estructura sugerida:
   - Columna A: número de mes (1–12, repetido 5 veces).
   - Columna B: identificador del SKU (ejemplo: "SKU-A101" para las primeras 12 filas, "SKU-B202" para las siguientes 12, etc.).
   - Columna C: demanda real (vinculada desde hoja Demanda).
   - Columna D: pronóstico (vinculado desde hoja Pronósticos, columna correspondiente de PRONOSTICO.LINEAL).

   > **Tip de productividad:** Puedes construir las primeras 12 filas para SKU-A101 y luego copiar/ajustar para los demás SKUs.

4. En `E2` calcula el error absoluto:
   ```excel
   =ABS(C2 - D2)
   ```
5. En `F2` calcula el error porcentual absoluto:
   ```excel
   =ABS(C2 - D2) / C2
   ```
   > **Nota:** Si algún valor real fuera 0, esta fórmula generaría un error de división. En nuestro dataset no hay ceros, pero en la práctica profesional se recomienda proteger con `=SI(C2=0, 0, ABS(C2-D2)/C2)`.

6. Copia `E2:F2` hacia abajo hasta la fila 61.
7. Formatea la columna F como **Porcentaje** con 2 decimales: selecciona F2:F61 → **Inicio → Formato de número → Porcentaje**.

**Resultado esperado:** Para SKU-A101 (tendencia muy lineal), los errores porcentuales absolutos deben ser bajos (< 3 %). Para SKU-C303 (alta variabilidad), los errores deben ser significativamente mayores (muchos > 15 %).

**Verificación:** Revisa la fila correspondiente a SKU-C303, mes 3 (demanda real = 380, pronóstico lineal ≈ 445). El error absoluto debe ser ≈ 65 y el error porcentual absoluto ≈ 17.1 %. Si obtienes valores muy diferentes, verifica las referencias.

---

### Paso 6 — Cálculo de MAE y MAPE por SKU

**Objetivo:** Calcular los indicadores agregados de precisión del pronóstico para cada SKU.

**Instrucciones:**

1. En la hoja **Errores**, ubica un área libre debajo de los datos (por ejemplo, fila 65 en adelante) o crea una nueva hoja llamada **Resumen**.
2. En la hoja **Resumen** (o área libre), crea la siguiente tabla:

   | A | B | C | D |
   |---|---|---|---|
   | **SKU** | **MAE** | **MAPE** | **Clasificación** |
   | SKU-A101 | | | |
   | SKU-B202 | | | |
   | SKU-C303 | | | |
   | SKU-D404 | | | |
   | SKU-E505 | | | |

3. Para calcular el MAE de SKU-A101 (asumiendo que las filas 2–13 de la hoja Errores corresponden a SKU-A101), en la celda B2 de Resumen escribe:
   ```excel
   =PROMEDIO(Errores!E2:E13)
   ```

4. Para el MAPE de SKU-A101, en C2:
   ```excel
   =PROMEDIO(Errores!F2:F13)
   ```
   > El resultado ya estará en formato decimal (por ejemplo, 0.0085 = 0.85 %). Formatea como porcentaje.

5. Repite para los demás SKUs ajustando los rangos:
   - SKU-B202: filas 14–25 de hoja Errores.
   - SKU-C303: filas 26–37.
   - SKU-D404: filas 38–49.
   - SKU-E505: filas 50–61.

   ```excel
   B3: =PROMEDIO(Errores!E14:E25)
   C3: =PROMEDIO(Errores!F14:F25)
   B4: =PROMEDIO(Errores!E26:E37)
   C4: =PROMEDIO(Errores!F26:F37)
   B5: =PROMEDIO(Errores!E38:E49)
   C5: =PROMEDIO(Errores!F38:F49)
   B6: =PROMEDIO(Errores!E50:E61)
   C6: =PROMEDIO(Errores!F50:F61)
   ```

**Resultado esperado (valores aproximados):**

| SKU | MAE (unidades) | MAPE |
|-----|----------------|------|
| SKU-A101 | ≈ 3–5 | ≈ 0.5–1.0 % |
| SKU-B202 | ≈ 5–8 | ≈ 3–5 % |
| SKU-C303 | ≈ 55–70 | ≈ 12–25 % |
| SKU-D404 | ≈ 3–6 | ≈ 0.3–0.5 % |
| SKU-E505 | ≈ 5–10 | ≈ 1.5–3 % |

> Los valores exactos dependerán de la regresión lineal ajustada. Lo importante es que SKU-C303 muestre un MAPE significativamente mayor que los demás (> 20 %).

**Verificación:** La suma de los errores absolutos de SKU-D404 dividida entre 12 debe ser menor a 10 unidades, dado que este SKU tiene tendencia muy consistente.

---

### Paso 7 — Clasificación de confiabilidad con SI() anidada

**Objetivo:** Crear una clasificación automática de confiabilidad del pronóstico para cada SKU usando lógica condicional.

**Instrucciones:**

1. En la columna D de la hoja **Resumen** (columna "Clasificación"), celda `D2`, escribe la siguiente fórmula `SI()` anidada:
   ```excel
   =SI(C2<=0.1, "Alta", SI(C2<=0.2, "Media", "Baja"))
   ```

   > **Lógica:**
   > - MAPE ≤ 10 % (0.1) → **Alta** confiabilidad
   > - MAPE > 10 % y ≤ 20 % (0.2) → **Media** confiabilidad
   > - MAPE > 20 % → **Baja** confiabilidad

2. Copia `D2` hacia abajo hasta `D6` (5 SKUs).

3. Aplica formato condicional para resaltar visualmente:
   - Selecciona `D2:D6`.
   - Ve a **Inicio → Formato condicional → Nueva regla → Utilizar una fórmula**.
   - Regla 1: `=D2="Baja"` → Relleno rojo claro, texto rojo oscuro.
   - Regla 2: `=D2="Media"` → Relleno amarillo claro, texto naranja.
   - Regla 3: `=D2="Alta"` → Relleno verde claro, texto verde oscuro.

4. Agrega una fila de encabezado con negrita y bordes a toda la tabla resumen.

**Resultado esperado:**

| SKU | MAE | MAPE | Clasificación |
|-----|-----|------|---------------|
| SKU-A101 | ~4 | ~0.8 % | **Alta** |
| SKU-B202 | ~7 | ~3.8 % | **Alta** |
| SKU-C303 | ~63 | ~22 % | **Baja** |
| SKU-D404 | ~4 | ~0.4 % | **Alta** |
| SKU-E505 | ~7 | ~2.0 % | **Alta** |

**Verificación:**
- SKU-C303 debe aparecer clasificado como **"Baja"** (celda con relleno rojo).
- Los demás SKUs deben clasificarse como **"Alta"**.
- Si algún SKU aparece como "Media", verifica que los datos de demanda en la hoja original coincidan con los proporcionados.

---

### Paso 8 — Interpretación y conclusión del análisis

**Objetivo:** Documentar la conclusión operativa: qué SKU requiere revisión prioritaria de inventario.

**Instrucciones:**

1. En la hoja **Resumen**, debajo de la tabla (fila 9 en adelante), escribe un bloque de texto con las siguientes conclusiones:

   ```
   CONCLUSIÓN DEL ANÁLISIS DE CONFIABILIDAD:
   
   • SKU-C303 (Film Stretch 500m) presenta un MAPE > 20%, clasificación BAJA.
     → Este producto tiene demanda altamente variable que el modelo lineal no captura.
     → ACCIÓN: Requiere revisión prioritaria de inventario y análisis con métodos 
       alternativos (suavización exponencial, análisis de estacionalidad, IA generativa).
   
   • Los demás SKUs (A101, B202, D404, E505) tienen MAPE ≤ 10%, clasificación ALTA.
     → El pronóstico lineal es confiable para planificación de reabastecimiento.
   
   • SKU-C303 será el SKU crítico a analizar en la Práctica 4 con Microsoft Copilot.
   ```

2. Guarda el archivo con el nombre `Practica1_Demanda_SKU_Completo.xlsx` en `C:\ForecLog\Practicas\P1\`.

**Resultado esperado:** Archivo guardado con 4 hojas: Demanda, Pronósticos, Errores, Resumen.

**Verificación:** Cierra y reabre el archivo. Navega a la hoja Resumen y confirma que las fórmulas se recalculan correctamente y la clasificación se mantiene.

---

## Validación y Pruebas

Realiza las siguientes comprobaciones finales para confirmar que el laboratorio se completó correctamente:

| # | Criterio de validación | Resultado esperado | ✓ |
|---|------------------------|-------------------|---|
| 1 | La hoja Pronósticos contiene valores de PRONOSTICO.LINEAL para los 5 SKUs × 12 meses | 60 valores numéricos sin errores (#¡REF!, #¡VALOR!) | ☐ |
| 2 | Los valores de TENDENCIA() coinciden con PRONOSTICO.LINEAL() (diferencia = 0) | Todas las restas entre ambas columnas = 0 | ☐ |
| 3 | La Hoja de Pronóstico de SKU-D404 muestra proyección para meses 13–15 con intervalo de confianza | Gráfico con banda sombreada visible; tabla con 3 filas de pronóstico | ☐ |
| 4 | La hoja Errores tiene 60 filas con Error_Abs y Error_Pct_Abs calculados | Ninguna celda vacía o con error en E2:F61 | ☐ |
| 5 | El MAPE de SKU-C303 es > 20 % | Valor en celda correspondiente > 0.20 | ☐ |
| 6 | La clasificación de SKU-C303 es "Baja" | Celda muestra texto "Baja" con formato rojo | ☐ |
| 7 | Los demás SKUs tienen clasificación "Alta" | 4 celdas con texto "Alta" y formato verde | ☐ |

---

## Solución de Problemas

### Problema 1: La función PRONOSTICO.LINEAL() devuelve el error `#¿NOMBRE?`

**Síntomas:** Al escribir `=PRONOSTICO.LINEAL(...)` en una celda, Excel muestra `#¿NOMBRE?` en lugar de un valor numérico.

**Causa:** El idioma de Excel no está configurado en Español, o la versión de Excel es anterior a 2016. En versiones en inglés la función se llama `FORECAST.LINEAR()`. También puede ocurrir si se escribe incorrectamente (por ejemplo, `PRONOSTICO.LINEAR` sin la "E" en LINEAL).

**Solución:**
1. Verifica el idioma: **Archivo → Opciones → Idioma** → debe ser Español (México).
2. Si el idioma es correcto, verifica la ortografía exacta: `PRONOSTICO.LINEAL` (sin tilde en "O", con punto entre las dos palabras).
3. Si usas Excel en inglés, sustituye por `=FORECAST.LINEAR(x, known_ys, known_xs)`.
4. Si la versión es Excel 2013 o anterior, usa la función legacy `=PRONOSTICO(x, y_conocidos, x_conocidos)`.

---

### Problema 2: La opción "Hoja de pronóstico" está deshabilitada (gris) en la pestaña Datos

**Síntomas:** Al ir a **Datos → Hoja de pronóstico**, el botón aparece en gris y no se puede hacer clic.

**Causa:** Excel requiere que la selección activa contenga al menos dos columnas contiguas con datos numéricos (una de fechas/períodos y una de valores), con un mínimo de datos suficientes. Si las columnas seleccionadas no son contiguas o contienen texto en las celdas de datos, la opción se deshabilita.

**Solución:**
1. Asegúrate de seleccionar **exactamente dos columnas contiguas** antes de hacer clic en Hoja de pronóstico.
2. Si las columnas Mes y SKU-D404 no están contiguas en tu hoja, copia ambas columnas a una hoja temporal de forma adyacente (A = Mes, B = Demanda) y vuelve a intentar.
3. Verifica que la columna de Mes contenga solo números (1–12) o fechas válidas, sin celdas vacías ni texto.
4. Confirma que tienes al menos 12 puntos de datos (Excel requiere suficientes datos para detectar patrones).
5. Si persiste, cierra y reabre Excel; en casos raros un complemento puede interferir con la función.

---

## Limpieza

Al finalizar la práctica:

1. Guarda la versión final del archivo como `Practica1_Demanda_SKU_Completo.xlsx` en `C:\ForecLog\Practicas\P1\`.
2. **No elimines** este archivo — los resultados de MAPE y clasificación serán referencia conceptual para la Práctica 4.
3. Si creaste hojas temporales auxiliares durante la Hoja de Pronóstico, puedes eliminarlas (clic derecho en la pestaña → Eliminar) para mantener el libro limpio.
4. Cierra Excel.

---

## Resumen

### Lo que lograste en esta práctica

| Logro | Herramienta utilizada |
|-------|----------------------|
| Pronósticos lineales período a período para 5 SKUs | `PRONOSTICO.LINEAL()` |
| Pronósticos matriciales verificados | `TENDENCIA()` |
| Proyección visual con intervalos de confianza | Hoja de Pronóstico (ETS) |
| Medición de error absoluto por período | `ABS()` |
| Indicadores MAE y MAPE por SKU | `PROMEDIO()` |
| Clasificación automática de confiabilidad | `SI()` anidada |
| Identificación del SKU crítico (SKU-C303) | Análisis de MAPE |

### Hallazgo clave

**SKU-C303 (Film Stretch 500m)** es el producto con menor confiabilidad de pronóstico (MAPE > 20 %). Su demanda altamente variable no se captura adecuadamente con un modelo lineal. Este SKU será el foco del análisis con IA generativa en la Práctica 4, donde se explorarán escenarios de demanda y recomendaciones de reabastecimiento.

### Conexión con las siguientes prácticas

- **Práctica 2:** Importarás datos crudos en Power Query y construirás un modelo analítico en Power BI Desktop.
- **Práctica 3:** Crearás un dashboard de monitoreo con indicadores DAX de inventario.
- **Práctica 4:** Usarás Microsoft Copilot para interpretar la variabilidad de SKU-C303 y generar recomendaciones de reabastecimiento.

### Recursos adicionales

- [Documentación oficial: PRONOSTICO.LINEAL](https://support.microsoft.com/es-es/office/pronostico-lineal-funci%C3%B3n-pronostico-lineal-bbf8f931-aa08-4f64-a46b-abb4a7e5a2c7)
- [Documentación oficial: TENDENCIA](https://support.microsoft.com/es-es/office/tendencia-funci%C3%B3n-tendencia-e2f135f0-8827-4096-9873-9a7cf7b51ef1)
- [Crear una previsión en Excel](https://support.microsoft.com/es-es/office/crear-una-previsi%C3%B3n-en-excel-para-windows-22c500da-6da7-45e5-bfdc-60a7062329fd)
- [Concepto de MAPE — Investopedia (inglés)](https://www.investopedia.com/terms/m/mean-absolute-percentage-error.asp)

---
