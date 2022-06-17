# Censo 2017 (Paquete Python)

<!-- badges: start -->

![Python Logo](https://www.python.org/static/community_logos/python-logo.png "Sample inline image")
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4277761.svg)](https://doi.org/10.5281/zenodo.4277761)
[![Buy Me a
Coffee](https://img.shields.io/badge/buymeacoffee-pacha-yellow)](https://www.buymeacoffee.com/pacha?via=github)
<!-- badges: end -->

# Acerca de

Provee un acceso conveniente a mas de 17 millones de registros de la
base de datos del Censo 2017. Los datos fueron importados desde el DVD
oficial del INE usando el [Convertidor
REDATAM](https://github.com/discontinuos/redatam-converter/) creado por
Pablo De Grande y ademas se proporcionan los mapas que acompanian a
estos datos. Estos mismos datos en DVD posteriormente quedaron
disponibles en las [Bases de Datos del
INE](https://www.ine.cl/estadisticas/sociales/censos-de-poblacion-y-vivienda/poblacion-y-vivienda).

Despues de la primera llamada a `library(censo2017)` se le pedira al
usuario que descargue la base usando `censo_descargar_base()` y se puede
modificar la ruta de descarga con la variable de entorno
`CENSO_BBDD_DIR`.

Este software es una adaptacion del paquete original de R censo2017. Para hacer el port me base en [Python Sample Project](https://github.com/pypa/sampleproject).

# Publico objetivo

Estudiantes, academicos e investigadores que necesiten un acceso
conveniente a datos censales directamente en Python o Jupyer Notebook.

# Requerimientos de instalacion

Este paquete necesita 3.5 GB libres para la crear la base de datos
localmente.

# Instalacion

## Pre-requisitos

Desde la linea de comandos se debe instalar primero

```
pip install duckdb
pip install wget
```

Luego se puede hacer lo siguiente desde la linea de comandos

```
git clone git@github.com:pachadotdev/censo2017_python.git
cd censo2017_python
python setup.py install --user
```

# Valor agregado sobre los archivos SHP y REDATAM del INE

Esta version de la base de datos del Censo 2017 presenta algunas
diferencias respecto de la original que se obtiene en DVD y corresponde
a una version DuckDB derivada a partir de los Microdatos del Censo 2017
en formato DVD.

La modificacion sobre los archivos originales, que incluyen geometrias
detalladas disponibles en [Cartografias
Censo2017](https://github.com/ropensci/censo2017-cartografias),
consistio en unir todos los archivos SHP regionales en una unica tabla
por nivel (e.g en lugar de proveer `R01_mapa_comunas`, …,
`R15_mapa_comunas` combine las 15 regiones en una unica tabla
`mapa_comunas`).

Los cambios concretos respecto de la base original son los siguientes:

-   Nombres de columna en formato “tidy” (e.g. `comuna_ref_id` en lugar
    de `COMUNA_REF_ID`).
-   Agregue los nombres de las unidades geograficas (e.g. se incluye
    `nom_comuna` en la tabla `comunas` para facilitar los filtros).
-   Aniadi la variable `geocodigo` a la tabla de `zonas`. Esto facilita
    mucho las uniones con las tablas de mapas en SQL.
-   Tambien inclui las observaciones 16054 to 16060 en la variable
    `zonaloc_ref_id`. Esto se debio a que era necesario para crear una
    llave foranea desde la tabla `mapa_zonas` (ver repositorio
    [Cartografias
    Censo2017](https://github.com/ropensci/censo2017-cartografias)) y
    vincular el `geocodigo` (no todas las zonas del mapa estan presentes
    en los datos del Censo).

Ademas de los datos del Censo, inclui la descripcion de las variables en
formato tabla (y no en XML como se obtiene del DVD). La ventaja de esto
es poder consultar rapidamente lo que significan los codigos de
variables y su etiquetado, por ejemplo:

``` r
# con la bbdd instalada
library(censo2017)
library(dplyr)

censo_tabla("variables") %>% 
  filter(variable == "p01")
#> # A tibble: 1 x 5
#>   tabla     variable descripcion      tipo    rango 
#>   <chr>     <chr>    <chr>            <chr>   <chr> 
#> 1 viviendas p01      Tipo de Vivienda integer 1 - 10

censo_tabla("variables_codificacion") %>% 
  filter(variable == "p01")
#> # A tibble: 12 x 4
#>    tabla     variable valor descripcion                                         
#>    <chr>     <chr>    <int> <chr>                                               
#>  1 viviendas p01          1 Casa                                                
#>  2 viviendas p01          2 Departamento en Edificio                            
#>  3 viviendas p01          3 Vivienda Tradicional Indígena (Ruka, Pae Pae u Otra…
#>  4 viviendas p01          4 Pieza en Casa Antigua o en Conventillo              
#>  5 viviendas p01          5 Mediagua, Mejora, Rancho o Choza                    
#>  6 viviendas p01          6 Móvil (Carpa, Casa Rodante o Similar)               
#>  7 viviendas p01          7 Otro Tipo de Vivienda Particular                    
#>  8 viviendas p01          8 Vivienda Colectiva                                  
#>  9 viviendas p01          9 Operativo Personas en Tránsito (No Es Vivienda)     
#> 10 viviendas p01         10 Operativo Calle (No Es Vivienda)                    
#> 11 viviendas p01         11 Valor Perdido                                       
#> 12 viviendas p01          0 No Aplica
```

# Relacion de Censo 2017 con Chilemapas

Todos los datos de estos repositorios contemplan 15 regiones pues los
archivos del Censo se entregan de esta forma y este paquete esta 100%
orientado a facilitar el acceso a datos.

Por su parte, [chilemapas](https://docs.ropensci.org/censo2017) se
centra unicamente en los mapas y tambien usa las cartografias del DVD
del Censo para entregar mapas simplificados (de menor detalle y mas
livianos). Chilemapas cuenta con una transformacion de codigos para dar
cuenta de la creacion de la Region de Niuble.

En resumen, censo2017 permite construir estadisticas demograficas y
chilemapas ayuda a mostrarlas en un mapa usando ggplot2 (u otro paquete
como tmap).

# Cita este trabajo

Si usas `censo2017` en trabajos academicos u otro tipo de publicacion
por favor usa la siguiente cita:

    Mauricio Vargas (2020). censo2017: Base de Datos de Facil Acceso del Censo
      2017 de Chile (2017 Chilean Census Easy Access Database). R package version
      0.1. https://docs.ropensci.org/censo2017/

Entrada para BibTeX:

    @Manual{,
      title = {censo2017: Base de Datos de F\'acil Acceso del Censo 2017 de Chile
    (2017 Chilean Census Easy Access Database)},
      author = {Mauricio Vargas},
      year = {2020},
      note = {R package version 0.1},
      url = {https://docs.ropensci.org/censo2017/},
      doi = {10.5281/zenodo.4277761}
    }

# Contribuciones

Para contribuir a este proyecto debes estar de acuerdo con el [Codigo de
Conducta de rOpenSci](https://ropensci.org/code-of-conduct/). Me es util
contar con mas ejemplos, mejoras a las funciones y todo lo que ayude a
la comunidad. Si tienes algo que aportar me puedes dejar un issue o pull
request.

# Agradecimientos

Muchas gracias a Juan Correa por su asesoria como geografo experto.

# Aportes

Si quieres donar para aportar al desarrollo de este y mas paquetes Open
Source, puedes hacerlo en [Buy Me a
Coffee](https://www.buymeacoffee.com/pacha/).
