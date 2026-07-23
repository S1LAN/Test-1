# Dashboard de Indicadores de Seguridad — CGES Jalisco
**Estado del proyecto al 23 de julio de 2026**

Este documento resume todo lo revisado, corregido y agregado hasta ahora, para retomar el trabajo sin perder contexto.

---

## 1. Archivo principal
`index.html` — Dashboard completo y autocontenido (no requiere servidor, no requiere internet para funcionar, excepto para las gráficas que usan Chart.js vía CDN). Ábrelo con doble clic en cualquier navegador.

## 2. Origen del problema (resumen del diagnóstico)
Se auditó una entrega previa (hecha por otra IA) contra:
- El PPTX/PDF fuente (`Indicadores_CGES.pdf`, Plan Estatal de Desarrollo 2024–2030).
- Las 30 fichas oficiales de indicador descargadas de mide.jalisco.gob.mx.

Se encontraron errores serios:
- **13 de 30 indicadores** tenían el ID cruzado con el nombre/valores de otro indicador.
- **1 indicador faltante** (JTP2-010 "Personas Vinculadas") y **1 ID duplicado** (JTP2-006).
- Valores de 2026 **inventados** (copiados de 2025 en vez de usar el dato real o marcar N/D), violando la regla de "cero alucinaciones" del prompt original.
- Confusión entre "Meta 2026" (oficial MIDE) y "Meta 2027" (Plan Estatal) — se trataban como si fueran el mismo campo.
- No existía distinción de Nivel 1/Nivel 2 ni de Eje/Categoría en los datos.
- Logo roto (referenciaba un archivo que no venía incluido en el paquete).

## 3. Correcciones aplicadas a los datos
Se reconstruyeron los 30 registros cruzando fichas oficiales + PPTX. Cada indicador ahora tiene:
`id, nombre, concepto, nivel, categoria, reportado_por, frecuencia, unidad, tendencia_deseable, valores_historicos {2024, 2025, 2026_preliminar}, meta_2026_oficial_MIDE, meta_plan_estatal {anio, valor}, semaforo, corte_informacion, metodologia, formula`

El JSON completo y limpio está en `datos/indicadores_completos_CORREGIDO.json` (por si se necesita reutilizar fuera del HTML; dentro del HTML ya viene embebido).

## 4. Correcciones al dashboard (mismo diseño visual original)
- Clasificación de semáforo corregida: MEJORA=verde, AUMENTA/DISMINUYE=rojo, SIN MEJORA/SE MANTIENE=amarillo (antes AUMENTA se contaba mal como amarillo).
- Filtro de semáforo ahora incluye las 5 categorías reales (antes faltaban AUMENTA y SIN MEJORA).
- Resumen superior (tarjetas) corregido para contar bien las 3 clases de color.
- Badge de "Nivel 1/2" agregado en cada tarjeta.
- Modal de detalle: ya no muestra "Meta 2027" fija y mal etiquetada; muestra Meta 2026 oficial y, cuando aplica, Meta Plan Estatal con su año real.
- Bug corregido: el botón "Informe" de la tarjeta no sabía a qué indicador se refería si no habías abierto antes el modal.

## 5. Logo
Se incrustó el logo real (el PNG que proporcionaste) directamente en el HTML como base64 — no depende de ningún archivo externo. Aparece en:
- Header del dashboard (esquina superior izquierda).
- Portada de ambos informes PDF.
- Encabezado interno de cada sección del informe.

El fondo del header se ajustó al azul exacto del logo (`#152595`) con un difuminado en los bordes de la imagen para que no se vea una "caja" recortada.

## 6. Informes en PDF (función nueva)
Botones activos: **"📄 Informe"** (por indicador) y **"📊 Generar Informe General"** (los 30).
- Al hacer clic, se abre el diálogo de impresión nativo del navegador → el usuario elige "Guardar como PDF".
- Sin librerías externas para esto (funciona offline); solo las gráficas usan Chart.js por CDN.
- Informe individual: portada + ficha completa (tabla de evolución, metodología, fuente) + gráfica de línea (idéntica a la del modal "Expandir").
- Informe general: portada + resumen ejecutivo (tarjetas + gráfica de dona de semáforos) + tabla resumen de 30 + detalle de cada indicador con su gráfica, agrupado por categoría.

Ejemplos ya generados (con datos reales, pero con un simulador de gráfica porque el entorno de pruebas no tiene internet) están en `ejemplos_pdf/`.

## 7. Pendientes / cosas por decidir cuando se retome
- [ ] **Confirmar visualmente** las gráficas reales de Chart.js (los ejemplos en PDF usan un simulador; falta que el usuario las genere desde su navegador con internet y confirme que se ven bien).
- [ ] **Decidir cómo se va a compartir la URL pública** del dashboard: opciones discutidas fueron GitHub Pages (control total, requiere que el usuario suba el repo) o "Publish" de Claude.ai (rápido pero público en internet, no recomendado para datos de seguridad institucionales sin evaluar el riesgo).
- [ ] Verificar si se requiere el manual de marca/colores oficial de la Coordinación (el prompt original lo mencionaba como insumo pero nunca se proporcionó; se usó la paleta azul ya existente en el diseño).
- [ ] Confirmar si se necesita el filtro adicional por "Nivel" en la barra de filtros (el prompt original lo pedía; por ahora solo se agregó el badge visual en la tarjeta, no un filtro funcional, para no alterar el diseño existente).
- [ ] Revisar si el difuminado del header quedó al gusto o se ajusta más/menos.

## 8. Archivos de esta entrega
Estructura **plana** (sin subcarpetas) — todos los archivos en la misma carpeta, lista para publicar tal cual en GitHub Pages, Netlify u otro hosting estático:
```
index.html                              # Dashboard completo (abrir este archivo, funciona solo)
indicadores_completos_CORREGIDO.json    # Los 30 registros corregidos, de referencia
                                         # (NO es requerido por index.html: los datos ya
                                         #  vienen embebidos dentro del propio HTML)
Ejemplo_Informe_Individual.pdf
Ejemplo_Informe_General.pdf
ESTADO_DEL_PROYECTO.md                  # Este archivo
```
El único recurso externo que usa `index.html` es la librería Chart.js vía CDN (`cdnjs.cloudflare.com`) para las gráficas — requiere internet solo para eso; todo lo demás (datos, logo) está incrustado en el propio archivo.
