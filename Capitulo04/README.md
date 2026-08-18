# "Asistente Inteligente para Planeación de Inventarios y Reabastecimiento"

## Metadatos

| Campo | Valor |
|---|---|
| **Duración** | 25 minutos |
| **Complejidad** | Media |
| **Nivel Bloom** | Crear |
| **Tecnologías** | Microsoft 365 Copilot, Power BI Desktop 2.130.754.0, Microsoft Edge / Google Chrome |

---

## Descripción General

En esta práctica de cierre integrarás los resultados de las Prácticas 1 y 3 con IA generativa. A partir del dashboard de Power BI identificarás los indicadores clave del SKU crítico (SKU-C303 – Film Stretch 500m), construirás prompts estructurados para Microsoft 365 Copilot, generarás tres escenarios de demanda futura y redactarás una recomendación formal de reabastecimiento lista para presentar al equipo de logística.

---

## Objetivos de Aprendizaje

- [ ] Identificar el SKU crítico del dashboard utilizando MAPE alto, cobertura baja y rupturas de stock como criterios de selección.
- [ ] Estructurar prompts efectivos para Copilot aplicando la técnica RCED (Rol, Contexto, Especificaciones de datos, Desenlace esperado) con datos anonimizados.
- [ ] Generar con IA generativa tres escenarios de demanda futura (optimista, conservador y crítico) con niveles de stock recomendado.
- [ ] Redactar con apoyo de IA una recomendación formal de reabastecimiento estructurada para el SKU crítico.
- [ ] Validar críticamente los outputs de la IA antes de documentarlos como entregable final.

---

## Prerrequisitos

### Conocimiento Previo

| Requisito | Detalle |
|---|---|
| Práctica 1 completada | Conocer el SKU con MAPE más alto (SKU-C303, MAPE >20%, confiabilidad baja) |
| Práctica 3 completada | Archivo `Practica3_Dashboard_Monitoreo.pbix` funcional con indicadores de inventario visibles |
| Técnica RCED | Comprender los cuatro componentes de un prompt efectivo (lección 4.1) |
| Buenas prácticas de privacidad | Saber anonimizar datos antes de compartirlos con IA generativa |

### Acceso Requerido

| Recurso | Verificación |
|---|---|
| Microsoft 365 Copilot | Sesión activa en https://copilot.microsoft.com |
| Power BI Desktop | Archivo `Practica3_Dashboard_Monitoreo.pbix` abierto |
| Conexión a Internet | Activa durante toda la práctica |
| Navegador | Microsoft Edge 128+ o Google Chrome 128+ |

---

## Entorno del Laboratorio

### Hardware Mínimo

| Componente | Requisito |
|---|---|
| RAM | 8 GB (recomendado 16 GB) |
| Disco libre | 2 GB |
| Procesador | 64 bits, Intel i5 8ª gen. o AMD Ryzen 5 3000+ |
| Pantalla | 1366×768 mínimo (1920×1080 recomendado) |

### Software

| Aplicación | Versión |
|---|---|
| Power BI Desktop | 2.130.754.0 (agosto 2024) |
| Microsoft 365 Copilot | Licencia estándar o acceso gratuito vía copilot.microsoft.com |
| Navegador | Edge 128.0.2739.42 / Chrome 128.0.6613.84 |

### Estructura de Archivos

```
C:\ForecLog\Practicas\P4\
├── Practica3_Dashboard_Monitoreo.pbix   (copiado desde P3)
└── Entregable_Reabastecimiento_SKU-C303.docx  (se creará en esta práctica)
```

### Preparación Inicial

1. Copiar el archivo de la Práctica 3 a la carpeta P4:

```powershell
Copy-Item "C:\ForecLog\Practicas\P3\Practica3_Dashboard_Monitoreo.pbix" "C:\ForecLog\Practicas\P4\"
```

2. Abrir `Practica3_Dashboard_Monitoreo.pbix` en Power BI Desktop.
3. Abrir el navegador y navegar a https://copilot.microsoft.com. Iniciar sesión con la cuenta de Microsoft 365.

---

## Procedimiento Paso a Paso

### Paso 1: Identificar y Documentar los Indicadores del SKU Crítico en el Dashboard

**Objetivo:** Extraer manualmente del dashboard de Power BI los valores clave del SKU-C303 (Film Stretch 500m) que servirán como contexto para los prompts de IA.

**Instrucciones:**

1. En Power BI Desktop, navegar a la página del dashboard que muestra los **indicadores de inventario por SKU** (tabla o tarjetas de KPI).
2. Localizar el SKU **SKU-C303 (Film Stretch 500m)**. Si el dashboard tiene segmentador (slicer), seleccionar únicamente este SKU.
3. Registrar los siguientes valores en una hoja de notas o documento de texto:

| Indicador | Dónde encontrarlo | Valor esperado (aproximado) |
|---|---|---|
| Demanda promedio mensual | Tarjeta o tabla de demanda | ~420 unidades |
| Desviación estándar de demanda | Tarjeta o medida calculada | ~135 unidades |
| MAPE | Tarjeta de precisión del pronóstico | >20% (ej. 24.8%) |
| Cobertura actual (días) | Tarjeta de cobertura | ~5 días |
| Rupturas de stock (últimos 12 meses) | Tarjeta o indicador de rupturas | 3–4 eventos |
| Stock actual | Tarjeta de inventario actual | ~70 unidades |

4. Verificar que el SKU-C303 es efectivamente el de **mayor MAPE** y **menor cobertura** comparado con los otros 4 SKUs del dataset.
5. Anotar también el **lead time del proveedor** (dato de referencia del curso): **7 días**.

**Resultado Esperado:**

Una tabla de valores documentados del SKU-C303 que servirá como insumo para los prompts de Copilot.

**Verificación:**

- El MAPE de SKU-C303 es el más alto de los 5 SKUs (>20%).
- La cobertura es la más baja (≤7 días).
- Se identifican al menos 3 rupturas de stock en el período analizado.

---

### Paso 2: Aplicar Buenas Prácticas de Seguridad y Privacidad

**Objetivo:** Preparar los datos para compartirlos con Copilot de forma segura, sin exponer información confidencial.

**Instrucciones:**

1. Revisar los datos recopilados en el Paso 1 y aplicar las siguientes reglas de anonimización:

| Dato original | Transformación para IA | Resultado |
|---|---|---|
| SKU-C303 | Usar código genérico | "Producto C" o "SKU de empaque industrial" |
| Film Stretch 500m | Descripción genérica | "Material de empaque tipo film" |
| Nombre de bodega/empresa | Omitir | No incluir |
| Nombre de proveedor | Omitir o generalizar | "Proveedor principal" |
| Precios unitarios exactos | No aplica en este ejercicio | No incluir |

2. Redactar una versión **anonimizada** de los indicadores. Ejemplo:

```text
Producto: Material de empaque industrial (código interno: Producto C)
- Demanda promedio mensual: 420 unidades
- Desviación estándar de demanda: 135 unidades
- Error de pronóstico (MAPE): 24.8%
- Cobertura actual de inventario: 5 días
- Rupturas de stock en los últimos 12 meses: 4 eventos
- Stock actual: 70 unidades
- Tiempo de entrega del proveedor: 7 días
```

3. Confirmar que **no se incluyen**: nombres de personas, nombres reales de proveedores, precios contractuales ni datos personales de clientes.

**Resultado Esperado:**

Un bloque de texto con datos anonimizados listo para ser pegado en Copilot.

**Verificación:**

- Ningún dato sensible (nombres propios, RFC, precios de contrato) aparece en el texto preparado.
- Los indicadores numéricos están completos y son suficientes para que la IA genere análisis útil.

---

### Paso 3: Construir el Prompt de Contexto e Interpretación de Variabilidad

**Objetivo:** Crear el primer prompt estructurado (técnica RCED) para que Copilot interprete la variabilidad de demanda del SKU crítico.

**Instrucciones:**

1. Abrir la ventana de chat de Microsoft 365 Copilot en el navegador (https://copilot.microsoft.com).

2. Escribir el siguiente prompt completo (copiar y adaptar según los valores reales de tu dashboard):

```text
Rol: Actúa como un analista experto en planeación de demanda e inventarios de bodega con experiencia en logística de distribución.

Contexto: Estoy evaluando un producto de empaque industrial (Producto C) que ha sido identificado como el SKU más crítico de nuestra operación. Este producto presenta el mayor error de pronóstico entre 5 productos monitoreados y la menor cobertura de inventario, lo que ha generado rupturas de stock recurrentes.

Datos disponibles:
- Demanda promedio mensual: 420 unidades
- Desviación estándar de demanda: 135 unidades (coeficiente de variación: 32%)
- Error de pronóstico (MAPE): 24.8%
- Cobertura actual de inventario: 5 días
- Rupturas de stock en últimos 12 meses: 4 eventos
- Stock actual en bodega: 70 unidades
- Tiempo de entrega del proveedor: 7 días
- Demanda diaria promedio: 14 unidades/día

Desenlace esperado: Interpreta la variabilidad de la demanda de este producto. Explica qué indica un MAPE del 24.8% combinado con un coeficiente de variación del 32%. Identifica al menos 3 causas posibles de esta alta variabilidad en un producto de empaque industrial. Presenta tu análisis en formato de bullet points, máximo 200 palabras.
```

3. Presionar **Enter** o el botón de enviar para obtener la respuesta de Copilot.

4. **Leer críticamente** la respuesta. Verificar que:
   - La IA identifica correctamente que un MAPE >20% indica pronóstico poco confiable.
   - Menciona que el coeficiente de variación del 32% señala demanda irregular/errática.
   - Las causas sugeridas son plausibles para un producto de empaque (estacionalidad, pedidos esporádicos grandes, dependencia de proyectos puntuales, etc.).

5. Si la respuesta es demasiado genérica, **iterar** con un prompt de seguimiento:

```text
Profundiza en la causa relacionada con pedidos esporádicos de gran volumen. 
¿Cómo afecta esto específicamente la planificación de inventarios cuando el 
lead time es de 7 días y la cobertura actual es de solo 5 días?
```

**Resultado Esperado:**

Una interpretación clara de la variabilidad del SKU que incluya:
- Diagnóstico del nivel de error del pronóstico.
- Explicación del impacto del coeficiente de variación.
- Al menos 3 causas posibles de la variabilidad.
- Conexión entre la variabilidad y el riesgo de ruptura de stock.

**Verificación:**

- La respuesta de Copilot es coherente con los datos proporcionados.
- No se generaron "alucinaciones" evidentes (datos inventados que contradigan los proporcionados).
- El análisis es accionable y comprensible para un equipo de logística.

---

### Paso 4: Generar los Tres Escenarios de Demanda Futura

**Objetivo:** Solicitar a Copilot la generación de escenarios optimista, conservador y crítico con niveles de stock recomendado para cada uno.

**Instrucciones:**

1. En la misma conversación de Copilot, escribir el siguiente prompt:

```text
Basándote en los datos del Producto C que te proporcioné anteriormente, genera tres escenarios de demanda para el próximo mes con sus respectivos niveles de stock recomendado. Usa los siguientes parámetros:

Escenario Optimista: Demanda +15% sobre el promedio histórico (420 unidades).
Escenario Conservador: Demanda igual al promedio histórico.
Escenario Crítico: Demanda con pico de +30% sobre el promedio (simula un pedido extraordinario).

Para cada escenario incluye:
1. Demanda proyectada del mes (en unidades).
2. Demanda diaria estimada (asumiendo 30 días).
3. Stock de seguridad recomendado (considerando la desviación estándar de 135 unidades y un nivel de servicio del 95%).
4. Punto de reorden sugerido (considerando lead time de 7 días).
5. Cantidad de reorden recomendada.
6. Riesgo principal asociado al escenario.

Presenta la información en formato de tabla comparativa.
```

2. Presionar **Enter** y esperar la respuesta.

3. **Validar los cálculos** de la IA contra tu propio razonamiento:

| Validación | Fórmula de referencia | Valor aproximado esperado |
|---|---|---|
| Stock de seguridad (Z=1.65 para 95%) | Z × σ × √(LT/30) | 1.65 × 135 × √(7/30) ≈ 108 unidades |
| Punto de reorden (conservador) | (Demanda diaria × LT) + SS | (14 × 7) + 108 = 206 unidades |
| Demanda optimista | 420 × 1.15 | 483 unidades/mes |
| Demanda crítica | 420 × 1.30 | 546 unidades/mes |

4. Si los cálculos de Copilot difieren significativamente de los valores de referencia, **solicitar corrección**:

```text
Revisa el cálculo del stock de seguridad. Usa la fórmula: Z × desviación estándar × raíz cuadrada de (lead time en días / 30). Con Z = 1.65 para nivel de servicio 95%, desviación estándar = 135 y lead time = 7 días. ¿Cuál es el resultado correcto?
```

5. Copiar la tabla de escenarios generada por Copilot en un documento de Word o bloc de notas para el entregable final.

**Resultado Esperado:**

Una tabla comparativa con tres escenarios que contenga para cada uno: demanda proyectada, demanda diaria, stock de seguridad, punto de reorden, cantidad de reorden y riesgo asociado.

Ejemplo de formato esperado:

| Parámetro | Optimista (+15%) | Conservador (=promedio) | Crítico (+30%) |
|---|---|---|---|
| Demanda mensual | 483 uds | 420 uds | 546 uds |
| Demanda diaria | 16.1 uds | 14 uds | 18.2 uds |
| Stock de seguridad | 108 uds | 108 uds | 108 uds |
| Punto de reorden | 221 uds | 206 uds | 235 uds |
| Cantidad de reorden | 375 uds | 312 uds | 438 uds |
| Riesgo principal | Sobrestock moderado | Ruptura si hay pico | Ruptura severa si no se anticipa |

**Verificación:**

- Los tres escenarios están claramente diferenciados.
- El stock de seguridad es consistente con la fórmula estadística (±15% de tolerancia).
- El punto de reorden incluye correctamente el consumo durante el lead time + stock de seguridad.
- Los riesgos identificados son lógicos para cada escenario.

---

### Paso 5: Redactar la Recomendación Formal de Reabastecimiento

**Objetivo:** Generar con apoyo de Copilot una recomendación estructurada de reabastecimiento para el SKU crítico, lista para presentar al equipo de logística.

**Instrucciones:**

1. En la misma conversación, escribir el siguiente prompt:

```text
Con base en el análisis de variabilidad y los tres escenarios de demanda generados para el Producto C, redacta una recomendación formal de reabastecimiento dirigida al equipo de logística. La recomendación debe incluir las siguientes secciones:

1. RESUMEN EJECUTIVO (2-3 oraciones con la conclusión principal)
2. SITUACIÓN ACTUAL (indicadores clave: MAPE, cobertura, rupturas, stock actual)
3. ANÁLISIS DE RIESGO (por qué este producto requiere atención inmediata)
4. ESCENARIOS DE DEMANDA (tabla resumen de los tres escenarios)
5. RECOMENDACIÓN DE ACCIÓN:
   - Cantidad sugerida de reorden inmediata
   - Punto de reorden para configurar en el sistema
   - Frecuencia de revisión sugerida
   - Justificación basada en escenarios
6. RIESGOS IDENTIFICADOS Y MITIGACIÓN

El tono debe ser profesional, directo y orientado a la acción. Extensión máxima: 400 palabras.
```

2. Presionar **Enter** y revisar la recomendación generada.

3. **Revisión crítica del output.** Verificar que:
   - [ ] Los datos numéricos coinciden con los proporcionados (no hay "alucinaciones").
   - [ ] La recomendación de cantidad de reorden es coherente con el escenario conservador como base y el crítico como techo.
   - [ ] El punto de reorden sugerido es ≥ 206 unidades (consumo en lead time + stock de seguridad).
   - [ ] Se menciona la frecuencia de revisión (semanal o bisemanal dado el alto MAPE).
   - [ ] El tono es profesional y accionable.

4. Si algún elemento requiere ajuste, solicitar una iteración específica:

```text
Ajusta la sección de RECOMENDACIÓN DE ACCIÓN: la cantidad de reorden inmediata 
debe cubrir el escenario crítico (546 unidades/mes) menos el stock actual (70 unidades), 
más el stock de seguridad (108 unidades). Recalcula y actualiza.
```

5. Copiar la recomendación final validada.

**Resultado Esperado:**

Un documento de recomendación con las 6 secciones solicitadas, datos consistentes y acciones concretas. Ejemplo de la sección de Recomendación de Acción:

```
5. RECOMENDACIÓN DE ACCIÓN:
   - Cantidad de reorden inmediata: 584 unidades 
     (demanda crítica 546 + stock de seguridad 108 - stock actual 70)
   - Punto de reorden a configurar: 206 unidades
   - Frecuencia de revisión: Semanal (dado MAPE >20%)
   - Justificación: La cobertura actual de 5 días es inferior al lead time 
     de 7 días, lo que garantiza ruptura de stock ante cualquier demora 
     del proveedor o pico de demanda no previsto.
```

**Verificación:**

- La recomendación contiene las 6 secciones solicitadas.
- Los números son verificables y consistentes con los datos del dashboard.
- El documento es presentable a un equipo de logística sin edición mayor.

---

### Paso 6: Documentar el Entregable Final

**Objetivo:** Consolidar todos los outputs en un documento formal que sirva como evidencia de la práctica.

**Instrucciones:**

1. Abrir un documento nuevo en Word (o bloc de notas) y guardarlo como:
   ```
   C:\ForecLog\Practicas\P4\Entregable_Reabastecimiento_SKU-C303.docx
   ```

2. Estructurar el documento con las siguientes secciones:

```
ENTREGABLE - PRÁCTICA 4
Asistente Inteligente para Planeación de Inventarios
Fecha: [fecha actual]
Alumno: [nombre]

═══════════════════════════════════════════
SECCIÓN 1: INDICADORES DEL SKU CRÍTICO
(Pegar tabla del Paso 1)

SECCIÓN 2: INTERPRETACIÓN DE VARIABILIDAD
(Pegar respuesta validada del Paso 3)

SECCIÓN 3: ESCENARIOS DE DEMANDA
(Pegar tabla comparativa del Paso 4)

SECCIÓN 4: RECOMENDACIÓN DE REABASTECIMIENTO
(Pegar recomendación formal del Paso 5)

SECCIÓN 5: NOTAS DE VALIDACIÓN
- Cálculos verificados: [Sí/No + detalle]
- Datos anonimizados correctamente: [Sí/No]
- Ajustes realizados al output de IA: [describir]
═══════════════════════════════════════════
```

3. En la **Sección 5 (Notas de Validación)**, documentar al menos un ajuste o corrección que hayas realizado al output de la IA. Esto demuestra pensamiento crítico.

4. Guardar el archivo.

**Resultado Esperado:**

Un archivo `.docx` completo con las 5 secciones, datos consistentes y al menos una nota de validación/corrección documentada.

**Verificación:**

- El archivo existe en `C:\ForecLog\Practicas\P4\`.
- Contiene las 5 secciones completas.
- La Sección 5 documenta al menos un ajuste crítico realizado.

---

## Validación y Pruebas

Utilizar la siguiente lista de verificación para confirmar que la práctica se completó exitosamente:

| # | Criterio de Evaluación | Cumple |
|---|---|---|
| 1 | Se identificó correctamente SKU-C303 como SKU crítico (mayor MAPE, menor cobertura) | ☐ |
| 2 | Los datos compartidos con Copilot están anonimizados (sin nombres de empresa/proveedor) | ☐ |
| 3 | Los prompts siguen la estructura RCED (Rol, Contexto, Datos, Desenlace) | ☐ |
| 4 | La interpretación de variabilidad identifica al menos 3 causas posibles | ☐ |
| 5 | Se generaron 3 escenarios diferenciados con parámetros numéricos específicos | ☐ |
| 6 | El stock de seguridad calculado está en rango razonable (90-130 unidades) | ☐ |
| 7 | El punto de reorden es ≥ 200 unidades | ☐ |
| 8 | La recomendación formal tiene las 6 secciones requeridas | ☐ |
| 9 | Se documentó al menos 1 corrección/ajuste al output de IA | ☐ |
| 10 | El entregable final está guardado en `C:\ForecLog\Practicas\P4\` | ☐ |

**Criterio de aprobación:** Mínimo 8 de 10 criterios cumplidos.

---

## Solución de Problemas

### Problema 1: Copilot genera cálculos de stock de seguridad incorrectos

**Síntomas:** El stock de seguridad sugerido por Copilot es significativamente diferente al esperado (ej. sugiere 50 unidades o 300 unidades en lugar de ~108).

**Causa:** Los modelos de lenguaje no siempre ejecutan cálculos matemáticos con precisión. Pueden confundir unidades, aplicar fórmulas incorrectas o usar supuestos diferentes (ej. usar desviación estándar diaria en lugar de mensual sin la conversión apropiada).

**Solución:**
1. Proporcionar la fórmula explícita en el prompt:
```text
Calcula el stock de seguridad usando EXACTAMENTE esta fórmula:
SS = Z × σ_mensual × √(LT/30)
Donde: Z = 1.65, σ_mensual = 135, LT = 7 días.
Muestra el cálculo paso a paso.
```
2. Verificar manualmente: 1.65 × 135 × √(7/30) = 1.65 × 135 × 0.483 = **107.6 ≈ 108 unidades**.
3. Si Copilot persiste en el error, usar el valor calculado manualmente y documentar la corrección en la Sección 5 del entregable.

---

### Problema 2: Copilot no está disponible o la sesión expira durante la práctica

**Síntomas:** Al acceder a https://copilot.microsoft.com aparece un error de conexión, mensaje de "servicio no disponible" o la conversación se pierde al recargar la página.

**Causa:** Problemas de conectividad a Internet, mantenimiento temporal del servicio de Microsoft, o la sesión de Microsoft 365 expiró por inactividad.

**Solución:**
1. Verificar la conexión a Internet abriendo otra página web.
2. Cerrar sesión y volver a iniciar sesión en https://copilot.microsoft.com.
3. Si el servicio está caído temporalmente, esperar 2-3 minutos y reintentar.
4. **Plan de contingencia:** Si Copilot no se recupera, utilizar la versión integrada en Microsoft Edge (icono de Copilot en la barra lateral del navegador) como alternativa.
5. Si ninguna opción funciona, documentar los prompts preparados en el entregable y anotar que la ejecución quedó pendiente por indisponibilidad del servicio. Los prompts bien estructurados demuestran la competencia del objetivo de aprendizaje.

---

## Limpieza

Esta práctica no requiere limpieza de recursos significativa dado que trabaja con archivos locales y un servicio web.

1. **Conversación de Copilot:** No es necesario eliminarla, pero si se desea por privacidad:
   - En copilot.microsoft.com, hacer clic en el ícono de historial.
   - Seleccionar la conversación y eliminarla.

2. **Archivos generados:** Conservar el entregable en `C:\ForecLog\Practicas\P4\` para evaluación del curso.

3. **Sesión de Copilot:** Cerrar sesión del navegador si se trabaja en equipo compartido.

---

## Resumen

En esta práctica se completó el ciclo analítico del curso: desde los datos crudos hasta una recomendación de reabastecimiento generada con apoyo de IA. Los aprendizajes clave son:

| Competencia desarrollada | Evidencia |
|---|---|
| Identificación de SKU crítico con criterios múltiples | Tabla de indicadores extraída del dashboard |
| Protección de datos al usar IA generativa | Datos anonimizados en todos los prompts |
| Prompt engineering con técnica RCED | 3 prompts estructurados ejecutados |
| Generación de escenarios con IA | Tabla de 3 escenarios con parámetros cuantificados |
| Pensamiento crítico sobre outputs de IA | Correcciones documentadas en Sección 5 |
| Comunicación profesional | Recomendación formal de 6 secciones |

### Recursos Adicionales

| Recurso | Enlace / Referencia |
|---|---|
| Documentación oficial de Microsoft 365 Copilot | https://support.microsoft.com/copilot |
| Guía de prompt engineering de Microsoft | https://learn.microsoft.com/en-us/ai/playbook/technology-guidance/generative-ai/working-with-llms/prompt-engineering |
| Fórmulas de stock de seguridad | Capítulo de inventarios del manual del curso |
| Políticas de privacidad de Microsoft Copilot | https://privacy.microsoft.com/privacystatement |

---
