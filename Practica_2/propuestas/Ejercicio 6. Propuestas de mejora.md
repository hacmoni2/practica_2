# Ejercicio 6. Propuestas de mejora

## Propuesta 1: Guardar filtros de búsqueda personalizados
1. **Título y necesidad:** *Guardar filtros de búsqueda personalizados*. Actualmente el usuario tiene que buscar y rellenar los mismos filtros cada vez que entra; el problema es la falta de personalización para consultas frecuentes.
2. **Descripción de la funcionalidad:** Como usuario común, podrás guardar tus filtros favoritos (por ejemplo: "Sismos mayores a 4.0 en mi estado durante el último mes") con un botón, para que la próxima vez que entres al sistema veas tus resultados de inmediato sin volver a configurar todo.
3. **Cambios en el modelo de datos:** 
   * Una entidad nueva llamada `FILTRO_USUARIO` (con atributos como `id_filtro`, `magnitud_minima`, `rango_dias`).
   * Una relación N:1 simple entre `FILTRO_USUARIO` y la `UBICACION_GEOGRAFICA`.
4. **En qué se apoya:** Necesidad identificada por el equipo para mejorar la experiencia de usuario y agilizar las consultas repetitivas en la plataforma.
5. **Dificultad estimada:** Baja, porque solo requiere una tablita extra en la base de datos y un botón básico en la interfaz de PHP.

---

## Propuesta 2: Gráfica rápida de población expuesta por sismo
1. **Título y necesidad:** *Gráfica rápida de población expuesta por sismo*. El artículo menciona que se cruzan datos del INEGI, pero a simple vista en los mapas no se ve un resumen claro de cuántas personas vivían en el municipio donde tembló.
2. **Descripción de la funcionalidad:** Como analista o estudiante, al hacer clic en un sismo del mapa, se abrirá un recuadro sencillo que te muestre directamente la población total de ese municipio (según el INEGI) para darte una idea inmediata de cuánta gente pudo sentirlo.
3. **Cambios en el modelo de datos:**
   * No se necesitan tablas nuevas. Solo se le agrega un atributo calculado o directo llamado `poblacion_total` a la entidad existente `INDICADOR_INEGI`.
   * Se ajusta la relación existente entre `SISMO` y `UBICACION_GEOGRAFICA`.
4. **En qué se apoya:** Se apoya en la línea de trabajo futuro del artículo de aprovechar mejor las variables demográficas del INEGI para entender el contexto social de los sismos.
5. **Dificultad estimada:** Baja, ya que los datos de población ya deberían estar cargados y solo es mostrarlos juntos en una consulta.

---

## Propuesta 3: Clasificación simple de la magnitud del sismo usando Machine Learning (IA)
1. **Título y necesidad:** *Categorizador automático del nivel de alerta del sismo*. Hace falta una etiqueta automática que le diga al usuario si el sismo es "Leve", "Moderado" o "Fuerte" sin que tenga que interpretar los números de la magnitud por sí mismo.
2. **Descripción de la funcionalidad:** Como usuario de la plataforma, cuando el sistema cargue un nuevo sismo, un modelo de Machine Learning muy sencillo (como un árbol de decisión básico o reglas de clasificación) calculará y le asignará una etiqueta automática de severidad a cada evento basándose en su magnitud y profundidad.
3. **Cambios en el modelo de datos:**
   * Modificar la entidad `SISMO` agregándole un nuevo atributo: `categoria_severidad_ia` (que guardará el resultado de la clasificación: Leve, Moderado, Severo).
   * No requiere crear tablas complejas de IA, solo guardar el resultado en el mismo registro del sismo.
4. **En qué se apoya:** Se alinea con los apartados de análisis y procesamiento inteligente de datos que sugieren los capítulos de referencia del proyecto para hacer la información más comprensible.
5. **Dificultad estimada:** Media-Baja, porque se puede resolver con un script sencillo en Python con reglas fijas o un modelo súper básico de clasificación sin meternos en redes neuronales difíciles.