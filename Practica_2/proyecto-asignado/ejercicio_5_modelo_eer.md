# Ejercicio 5. Modelo EER del Proyecto: "Cuando México tiembla: la historia contada por los datos"

## 1. Definición del Modelo Conceptual del Dominio

Revisando de qué trata el proyecto, la idea principal es agarrar toda la información de sismos que suelta el Servicio Sismológico Nacional (SSN) y cruzarla con los datos de población, economía y geografía que nos da el INEGI para entender mejor cómo nos afectan los temblores en México.

Haciendo la ingeniería inversa y quitándole toda la parte técnica de las bases de datos de análisis, los objetos reales (entidades) y cómo se conectan en la vida real son estos:

* **SISMO:** Es el temblor tal cual ocurrió. Lo que nos importa de él son datos como su fecha, a qué hora fue, qué magnitud tuvo, la profundidad y por dónde cayó el epicentro.
* **UBICACION_GEOGRAFICA:** Es la zona o lugarcito de México donde se sintió o pasó el sismo (aquí entran cosas como el estado, el municipio o la clave del lugar).
* **INDICADOR_INEGI:** Son todos los datos estadísticos que nos dicen cuánta gente vive ahí, condiciones de las casas o temas económicos de esa región.
* **ESTACION_SISMO:** Son los aparatos o redes del SSN que detectan y miden cuando la tierra se mueve.

**Cómo se relacionan entre ellos:**
* Un **Sismo** pasa en una sola **Ubicación Geográfica** en específico, aunque en esa misma ubicación se puedan registrar varios sismos con el tiempo.
* Cada **Ubicación Geográfica** tiene un montón de **Indicadores INEGI** asociados (información demográfica y económica).
* Una **Estación Sísmica** puede capturar varios **Sismos**, y al mismo tiempo, un solo **Sismo** puede ser registrado por varias estaciones al mismo tiempo.

---

## 2. Representación del Modelo EER (Notación Textual)

Para armar el modelo de entidades y atributos con sus llaves principales ($\text{PK}$), queda así:

* **Entidad: SISMO**
  * Atributos: `id_sismo` ($\text{PK}$), `fecha_hora`, `magnitud`, `profundidad`, `latitud`, `longitud`.

* **Entidad: UBICACION_GEOGRAFICA**
  * Atributos: `id_ubicacion` ($\text{PK}$), `estado`, `municipio`, `clave_geoestadistica`.

* **Entidad: INDICADOR_INEGI**
  * Atributos: `id_indicador` ($\text{PK}$), `nombre_indicador`, `valor`, `anio`.

* **Entidad: ESTACION_SISMO**
  * Atributos: `id_estacion` ($\text{PK}$), `nombre_estacion`, `latitud_estacion`, `longitud_estacion`.

---

## 3. Identificación de Jerarquías y Entidades Débiles

* **¿Hay entidades débiles?**
  * Sí. Los **Indicadores INEGI** funcionan como una entidad débil que depende totalmente de la **Ubicación Geográfica**. Si no tenemos definido un municipio o un estado real, el dato estadístico o económico no sirve de nada porque no tendríamos a qué lugar pegarlo.

* **¿Hay jerarquías (especialización o generalización)?**
  * Sí. La **Ubicación Geográfica** se puede ver con una jerarquía, partiendo de una zona general que luego se divide en subtipos más específicos como `Estado` y `Municipio`, tal cual como está dividido políticamente el país.

---

## 4. Tabla de Correspondencia (Modelo Conceptual vs. Esquema en Estrella)

Aquí comparamos cómo se ve el modelo de la vida real frente a cómo terminaron armando las tablas en el repositorio para que la plataforma web funcione rápido:

| Entidad de nuestro Modelo Conceptual | Tabla en el Repositorio (Esquema en Estrella) | Qué información se agrega o se pierde en el cambio |
| :--- | :--- | :--- |
| **Sismo** | `fact_sismos` (Tabla de Hechos) | **Se agrega:** IDs técnicos (`sk_sismo`) y datos calculados para que las consultas de la página no tarden horas en cargar. |
| **Ubicación Geográfica** | `dim_ubicacion` o `dim_geografia` (Dimensión) | **Se pierde/agrega:** En la vida real la geografía tiene varios niveles de jerarquía, pero aquí se aplana todo en una sola tablota con columnas fijas para buscar más rápido. |
| **Indicador INEGI** | `dim_inegi` (Tablas dimensionales) | **Se agrega:** Resúmenes e históricos ya procesados que originalmente vienen de bases de datos separadas del INEGI. |
| **Estación Sísmica** | `dim_estacion` (Dimensión) | **Se pierde:** Las conexiones complejas que tienen las estaciones en la realidad se simplifican a una simple tabla de datos para mostrar la información en los mapas. |

---

## 5. Bibliografia

DOI: 10.24275/AZC2026E1004
Enlace: https://doi.org/10.24275/AZC2026E1004
