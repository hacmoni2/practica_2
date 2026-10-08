## 4.1 Requisitos ampliados

### Descripción del problema
En las instalaciones universitarias, el control tradicional de préstamos de recursos —tales como material deportivo, libros de consulta y equipos tecnológicos— suele gestionarse de manera manual o deficiente. Esto provoca un grave descontrol reflejado en la pérdida de artículos, retrasos crónicos en las devoluciones y la falta de disponibilidad para la comunidad estudiantil que sí cumple con la normativa. El sistema propuesto busca automatizar la gestión mediante la identificación escolar única de cada alumno, aplicando bloqueos automáticos por adeudos o sanciones e integrando una estructura formal que contemple la diversidad de recursos que ofrece la institución.

### Requisitos ampliados y restricciones estructurales
*   **Restricciones de cardinalidad estrictas:** Cada alumno puede realizar múltiples préstamos a lo largo del tiempo, pero un préstamo individual pertenece de forma exclusiva a un único alumno. Asimismo, un recurso del catálogo puede estar involucrado en múltiples transacciones de préstamo históricas, mientras que un registro de préstamo específico hace referencia a una unidad o recurso determinado del inventario.
*   **Entidades dependientes (débiles):** El sistema identifica que los detalles específicos de un préstamo o los registros de penalizaciones/historiales particulares no pueden subsistir de forma autónoma sin el encabezado del préstamo o el perfil del usuario al que pertenecen.
*   **Categorización y especialización de recursos y usuarios:** Para reflejar la realidad del entorno universitario, las entidades principales se dividen en tipologías específicas:
    *   Los **Usuarios** del sistema se especializan en dos perfiles con reglas operativas distintas: *Alumnos regulares* y *Personal docente*.
    *   Los **Recursos** del inventario se especializan en distintas naturalezas (ej. *Material Deportivo*, *Libros*, *Equipos Tecnológicos*), cada uno con atributos operativos propios.
*   **Relaciones complejas:** El sistema contempla relaciones ternarias o asociaciones donde intervienen de forma simultánea el usuario, el recurso solicitado y el área o departamento administrativo que autoriza la entrega del material dentro del campus.

---

## 4.2 Conceptos del modelo extendido

### 1. Relaciones con cardinalidad mínima y máxima justificadas por regla de negocio
*Formato: [Entidad 1] – [relación] – [Entidad 2] y (mín, máx) : (mín, máx)*

*   **[Alumno] – [Realiza] – [Prestamo]** y **(0, N) : (1, 1)**
    *   *Regla de negocio:* Un alumno registrado en el sistema puede realizar cero o múltiples préstamos a lo largo de su trayectoria académica `(0, N)`. Por el contrario, cada registro de préstamo debe estar asociado obligatoriamente y de manera exclusiva a un único alumno `(1, 1)`.
*   **[Prestamo] – [Incluye] – [Recurso]** y **(1, N) : (0, N)**
    *   *Regla de negocio:* Un préstamo registrado debe incluir al menos un recurso físico del inventario, pudiendo abarcar varios en una misma transacción global `(1, N)`. Asimismo, un recurso del inventario puede estar presente en cero o múltiples transacciones de préstamo a lo largo del tiempo `(0, N)`.
*   **[Prestamo] – [Genera] – [Sancion]** y **(1, 1) : (0, 1)**
    *   *Regla de negocio:* Todo registro de sanción o penalización proviene de exactamente un préstamo específico que incurrió en anomalías o retrasos `(1, 1)`. Por su parte, un préstamo determinado puede generar como máximo una sanción (si se devuelve a tiempo no genera registro, o genera uno en caso de incumplimiento) `(0, 1)`.

### 2. Entidades débiles y tipo de dependencia
*   **`DetallePrestamo` (Dependencia de Existencia):**
    *   *Justificación:* Esta entidad almacena las líneas específicas de los artículos solicitados en una transacción múltiple. No puede existir de forma independiente porque carece de sentido lógico o administrativo sin un `Prestamo` general al cual pertenecer; si el préstamo principal se elimina, sus detalles deben desaparecer en cascada.
*   **`HistorialSancion` (Dependencia de Identificación):**
    *   *Justificación:* Depende directamente de la entidad `Alumno`. Su clave primaria está compuesta parcialmente por la clave del alumno infractor, por lo que no puede identificarse ni existir por sí misma en el sistema sin estar vinculada al registro del usuario sancionado.

### 3. Jerarquía de generalización y especialización
*   **Supertipo:** `UsuarioUniversitario` (Atributos generales: `boleta_o_id`, `nombre_completo`, `correo_institucional`, `fecha_registro`).
*   **Subtipos:** 
    *   `Alumno` (Atributos propios: `carrera`, `semestre_actual`).
    *   `Docente` (Atributos propios: `departamento_academico`, `cubiculo`).
*   **Restricciones:**
    *   **Disyunción:** **Disjunta (d)**, ya que un usuario perteneciente a la comunidad universitaria o es alumno o es docente, pero no puede detentar ambos roles simultáneamente bajo el mismo registro de perfil.
    *   **Completitud:** **Parcial (p)**, debido a que pueden existir perfiles administrativos u operadores del almacén general que no pertenecen estrictamente ni a la categoría de alumno ni a la de docente.

### 4. Conceptos adicionales implementados
*   **Atributo multivaluado:** El atributo `telefonos_contacto` en la entidad de usuarios, permitiendo almacenar uno o varios números telefónicos de localización para el seguimiento de adeudos.
*   **Atributo compuesto:** El atributo `direccion` en los registros de usuarios, desglosado internamente en subcomponentes: `calle`, `numero`, `colonia` y `codigo_postal`.
*   **Agregación:** Utilizada para encapsular la relación entre `Prestamo` e `Inventario` cuando se requiere asociar una supervisión o auditoría externa por parte del departamento de intendencia o vigilancia del campus.

---

## 4.3 Dos notaciones de modelado

### 1. Herramienta seleccionada
*   **Herramienta recomendada:** **ERDPlus** (o en su defecto **draw.io** / **dbdiagram.io**).
*   **Justificación:** ERDPlus y dbdiagram.io permiten estructurar con absoluta precisión tanto los diagramas conceptuales avanzados en notación de Peter Chen (óvalos de atributos compuestos/multivaluados, líneas de doble estructura para entidades débiles y triángulos de especialización) como los modelos lógicos relacionales limpios en notación de pata de cuervo (*Crow’s Feet*), manteniendo la integridad estricta de las llaves primarias y foráneas sin requerir configuraciones complejas de diseño visual.

### 2. Descripción esquemática de las notaciones
*   **Notación de Peter Chen (Conceptual Avanzado):**
    *   Se representan los rectángulos principales para `UsuarioUniversitario`, `Recurso` y `Prestamo`.
    *   Se emplea un doble rectángulo para las entidades débiles como `DetallePrestamo`.
    *   Se utiliza un triángulo con la etiqueta **(d, p)** conectado mediante líneas de herencia desde el supertipo `UsuarioUniversitario` hacia los subtipos `Alumno` y `Docente`.
    *   Los atributos compuestos (`direccion`) se despliegan en óvalos secundarios, y los multivaluados (`telefonos_contacto`) se representan mediante óvalos concéntricos.
*   **Notación Crow’s Feet (Lógico Relacional):**
    *   **`Alumno` (Tabla Principal):** `boleta` (PK), `nombre_completo`, `carrera`, `estatus`.
    *   **`Recurso` (Tabla Principal):** `id_recurso` (PK), `categoria`, `titulo`, `stock_disponible`.
    *   **`Prestamo` (Tabla Transaccional):** `id_prestamo` (PK), `boleta` (FK), `fecha_prestamo`, `fecha_limite`.
    *   Las relaciones muestran el extremo de barra vertical (mínimo 1) y la pata de cuervo (máximo N) para enlazar las entidades respetando las reglas de negocio descritas.

---

## 4.4 Justificación técnica 
1.  **Por qué las entidades débiles no pueden existir de forma independiente:**  
    La entidad `DetallePrestamo` carece de un atributo identificador propio que tenga sentido fuera del contexto de una transacción activa; depende por completo de la existencia de la cabecera `Prestamo`. De igual forma, el `HistorialSancion` no posee razón de ser administrativa ni puede ser consultado de manera aislada si no está indisolublemente atado al perfil del usuario infractor que generó la falta.
2.  **Por qué se eligió ese tipo de especialización:**  
    La jerarquía entre `UsuarioUniversitario` como supertipo y `Alumno` / `Docente` como subtipos bajo una regla **disjunta (d)** responde a la lógica institucional de que un usuario posee privilegios y restricciones de préstamo específicas según su rol exacto (por ejemplo, los plazos de devolución de un libro difieren entre un alumno y un docente). Es **parcial (p)** porque la plataforma contempla la existencia de personal administrativo u operativo auxiliar que no encaja estrictamente en ninguna de las dos clasificaciones anteriores pero requiere acceso al sistema.
3.  **Cómo reflejan las cardinalidades las reglas del negocio:**  
    Las restricciones `(0, N)` y `(1, 1)` garantizan que ninguna transacción de préstamo quede "huérfana" de usuario responsable (evitando mermas sin identificar), al tiempo que permiten que un estudiante pueda realizar múltiples operaciones a lo largo del ciclo escolar sin saturar ni corromper la integridad referencial del motor de bases de datos.
4.  **Tres consultas avanzadas que el modelo extendido permite responder:**
    *   *Consulta 1 (Especialización de roles):* Obtener el listado analítico de penalizaciones acumuladas diferenciando estrictamente entre el impacto de los adeudos generados por alumnos frente a los generados por docentes, aprovechando la jerarquía de herencia.
    *   *Consulta 2 (Atributos compuestos y multivaluados):* Localizar de manera inmediata los números de teléfono alternativos y el desglose de código postal de aquellos usuarios que rebasen el límite de fecha de devolución de un recurso especial.
    *   *Consulta 3 (Entidades débiles y transacciones complejas):* Generar un reporte consolidado del costo y desglose exacto de los artículos involucrados en préstamos múltiples mediante el uso de la entidad débil de detalle, permitiendo auditar el stock en tiempo real de forma transaccional.