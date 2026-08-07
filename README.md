# Forecasting Aplicado a Logística de Excel a Power BI e IA Generativa

La sesión conecta de forma ágil los métodos de pronóstico construidos en Excel con la preparación de datos en Power Query, la construcción de dashboards interactivos en Power BI Desktop (gratuito) y el uso de IA generativa como asistente en la toma de decisiones de inventario, aplicados a un caso integrador de gestión de inventarios y bodegas. El enfoque es eminentemente práctico y visual, orientado al análisis de negocio: rotación de inventario, cobertura, stock de seguridad, quiebres de stock, tendencias de consumo, pronóstico vs. demanda real y escenarios asistidos por IA.

## Estructura

- `CapituloXX/README.md`: guía de laboratorio por capítulo.

## Lista de laboratorios

### Capítulo 1

- ["¿Qué tan confiable es mi pronóstico?" – Cálculo de MAPE por SKU y clasificación de confiabilidad (alta/media/baja) para priorizar qué productos requieren revisión de inventario](Capitulo01/README.md#qué-tan-confiable-es-mi-pronóstico-cálculo-de-mape-por-sku-y-clasificación-de-confiabilidad-altamediabaja-para-priorizar-qué-productos-requieren-revisión-de-inventario)
  - Descripción: Calcular el MAPE por SKU y clasificar la confiabilidad del pronóstico como alta, media o baja para priorizar qué productos requieren revisión de inventario.
  - Duración estimada: 35 min

### Capítulo 2

- ["De datos crudos a modelo analítico" – Importar, limpiar y combinar datasets de demanda e inventario de bodega usando Power Query, dejando el modelo listo para su análisis en Power BI](Capitulo02/README.md#de-datos-crudos-a-modelo-analítico-importar-limpiar-y-combinar-datasets-de-demanda-e-inventario-de-bodega-usando-power-query-dejando-el-modelo-listo-para-su-análisis-en-power-bi)
  - Descripción: Importar los datos de demanda e inventario de bodega, limpiar y transformar sus campos, combinar las tablas con el catálogo de productos y dejar el modelo analítico listo para su análisis en Power BI.
  - Duración estimada: 38 min

### Capítulo 3

- ["Centro de Monitoreo de Demanda e Inventario" – Construcción de un dashboard integral en Power BI que integre rotación, cobertura, stock de seguridad, rupturas de stock, tendencia de consumo y pronóstico vs. demanda real, a partir del modelo preparado en la Práctica 2](Capitulo03/README.md#centro-de-monitoreo-de-demanda-e-inventario-construcción-de-un-dashboard-integral-en-power-bi-que-integre-rotación-cobertura-stock-de-seguridad-rupturas-de-stock-tendencia-de-consumo-y-pronóstico-vs-demanda-real-a-partir-del-modelo-preparado-en-la-práctica-2)
  - Descripción: Construir en Power BI un dashboard integral a partir del modelo preparado en la Práctica 2, incorporando rotación, cobertura, stock de seguridad, rupturas de stock, tendencia de consumo y comparación entre pronóstico y demanda real.
  - Duración estimada: 55 min

### Capítulo 4

- ["Asistente Inteligente para Planeación de Inventarios y Reabastecimiento" – A partir del dashboard construido en Power BI, usar IA generativa para interpretar la variabilidad de un SKU crítico, generar sus tres escenarios de demanda y redactar una recomendación de reabastecimiento](Capitulo04/README.md#asistente-inteligente-para-planeación-de-inventarios-y-reabastecimiento-a-partir-del-dashboard-construido-en-power-bi-usar-ia-generativa-para-interpretar-la-variabilidad-de-un-sku-crítico-generar-sus-tres-escenarios-de-demanda-y-redactar-una-recomendación-de-reabastecimiento)
  - Descripción: Usar IA generativa a partir del dashboard construido en Power BI para interpretar la variabilidad de un SKU crítico, generar escenarios optimista, conservador y crítico, y redactar una recomendación de reabastecimiento aplicando buenas prácticas de seguridad y privacidad de datos.
  - Duración estimada: 17 min

## Flujo de colaboración

- Trabajar en `changes_course`.
- Crear Pull Request hacia `main`.
- Merge por `Squash and merge`.
