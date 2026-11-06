# Analizador Multi-zona con Auditoría de Píxeles (Versión 1)

Este script permite realizar análisis geoespaciales interactivos utilizando Google Earth Engine, `geemap` e `ipywidgets`, enfocado en la extracción de múltiples zonas de interés de forma acumulativa.

## Características Principales
* **Cálculo de Índices Espectrales:** Soporte para NDVI, NDWI, MNDWI, EVI, NDSI, BSI, SAVI y NDMI usando Sentinel-2 Harmonized.
* **Muestreo Multi-zona:** Acumula múltiples figuras dibujadas en el mapa para procesarlas en conjunto.
* **Formato Horizontal e IDs Independientes:** Exporta los resultados tabulares en un formato horizontal donde cada zona tiene su propio conjunto de columnas (Pixel ID, Longitud, Latitud y Valor del Índice).
* **Auditoría Local de Píxeles:** Genera un archivo CSV secundario de auditoría (`auditoria_limite_pixeles_...csv`) que compara los píxeles reales existentes en Earth Engine frente a los descargados localmente, reportando posibles recortes por límites de píxeles.


