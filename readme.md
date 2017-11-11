# Apunte QlikView

Franz Wompner Risso

10 de Noviembre de 2017

## Tabla de contenidos
*  Introducción
*  Descargando el software
   *  Windows
   *  Otras plataformas
*  Iniciando el software
   *  Carga de datos
      *  Header
      *  Fuentes de datos
   *  Visualización del modelo
*  Análisis y visualización de datos
   *  Filtros, *Select Fields*
   *  Objetos, *Sheet Objects*
      *  Statistics Box
      *  Chart
         *  Expresiones
         *  Agregación y ordenamiento
         *  Obtención de datos
         *  Filtro por valores
      *  Pestañas

## Introducción

[QlikView](http://www.qlik.com/us/)
es un software de Business Intelligence (BI) -- permite realizar el proceso de extracción, transformación y carga (ETL), análisis de datos, visualización, dashboards, etc.

Sus ventajas radican en que es bastante simple de usar, y que es de uso personal gratuito, esencial en el contexto de nuestro ramo -- mientras que su principal desventaja es que es un software relativamente simple y limitado comparado con otras ofertas en BI.

Para la tarea y actividad de este curso, utilizaremos el software QlikView Desktop.
El objetivo de esta elección es que usted usar este software para "soltar la mano" y tener experiencia usando una herramienta de BI libre de pago, aunque en el futuro puedan terminar usando otra herramienta en un contexto laboral.

## Descargando el software

### Windows

Puede descargar el software de la [página oficial](https://www.qlik.com/us/try-or-buy/download-qlikview), la cual requiere registro; o bien, usando estos links directos:

* [Descarga 32 bits (x86)](http://www.buildingthefuture.biz/gamma/QlikViewDesktop_x86Setup.exe)
* [Descarga 64 bits (x64)](https://drive.google.com/file/d/0Bz7uSwRh_clwbmZUa2I2T096enc/view?usp=sharing)

### Otras plataformas

QlikView Desktop sólo se encuentra disponible para Windows.
Por lo tanto, su mejor opción en otra plataforma consiste en usar una máquina virtual para ejecutar Windows.

Si no cuenta con una licencia de Windows, en el sitio de [Microsoft Edge Development](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/) puede descargar máquinas virtuales de Windows gratuitas con expiración dentro de 90 días. Recomiendo seleccionar "IE8 on Win7 (x86)" sobre VirtualBox. Posteriormente debe descomprimir el archivo, abrir el .ova para instalar la máquina virtual, y proceder con las instrucciones para instalación en Windows.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/0.png" width="600px"/>

## Iniciando el software

Al iniciar el software por primera vez recomiendo crear un archivo nuevo, y evitar el *wizard* seleccionando la opción "Cancel".

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/1.png" width="300px"/>

### Carga de datos

Para la carga de datos a QlikView debe ir a la sección "Edit Script":

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/2.png" width="300px"/>

El script de carga de datos consta de un *header* y uno o más descriptores de archivos o fuentes de datos a cargar.

#### Header
Algunos de los campos que pueden incluirse en el *header* son:

    SET ThousandSep='.';
    SET DecimalSep=',';
    SET MoneyThousandSep='.';
    SET MoneyDecimalSep=',';
    SET MoneyFormat='$ #.##0,00;-$ #.##0,00';
    SET TimeFormat='h:mm:ss';
    SET DateFormat='DD-MM-YYYY';
    SET TimestampFormat='DD-MM-YYYY h:mm:ss[.fff]';
    SET MonthNames='ene;feb;mar;abr;may;jun;jul;ago;sep;oct;nov;dic';
    SET DayNames='lun;mar;miÈ;jue;vie;s·b;dom';

#### Fuentes de datos

Para cargar datos de un archivo, usted debe indicar la ubicación del archivo en el sistema de archivos, el nombre que dará a la tabla, y los distintos atributos a cargar.

Una opción para cargar un archivo de datos es utilizar la opción del menú *Insert > Load Statement > Load from File*; el software insertará el código necesario para cargar el archivo seleccionado.

Alternativamente puede copiar a QlikView el siguiente código según el formato del archivo que se carga, cambiando los parámetros que sean necesarios:

XLSX:

    NombreTabla:
    LOAD Atributo1,
         Atributo2,
         Atributo3,
         [Atributo con espacios],
         [Llave foránea] as nombrellave
    FROM
    [C:\path\al\archivo.xlsx]
    (ooxml, embedded labels, table is Sheet1);

CSV:

    NombreTabla:
    LOAD Atributo1,
         Atributo2,
         Atributo3,
         [Atributo con espacios],
         [Llave foránea] as nombrellave
    FROM
    [C:\path\al\archivo.csv]
    (txt, codepage is 1252, embedded labels, delimiter is ';', msq);

QVD:

    NombreTabla:
    LOAD Atributo1,
         Atributo2,
         Atributo3,
         [Atributo con espacios],
         [Llave foránea] as nombrellave
    FROM
    [C:\path\al\archivo.qvd]
    (qvd);

Considere lo siguiente:

* Para vincular tablas según una llave foránea, QlikView asume que atributos con el mismo nombre están vinculados. Para vincularlos usted puede indicar el nombre con el que se cargará una llave foránea:

         [Llave foránea] as nombrellave

* Usted puede separar una fecha en día, mes y año cargándola de la siguiente manera:

         day(Fecha) as dia,
         month(Fecha) as mes,
         year(Fecha) as anio

La interpretación de la fecha se realizará según los parámetros definidos en el *header*.

* Usted puede separar un atributo en distintos tramos de la siguiente manera:

         if(Edad < 20,'0-20',
         if(Edad >= 20 and Edad < 40,'20-40',
         '40+') ) as Rango_Edades

Tras modificar el script de carga de datos, primero **guarde** el archivo, pues si hay un error se perderán todos sus avances no guardados. Luego, presione *Reload* en el editor de scripts.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/3.png" width="300px"/>

### Visualización del modelo

Usted puede visualizar su modelo, y verificar si se ha cargado correctamente, seleccionando la opción "Table Viewer" en la ventana de "Edit Script".

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/4.png" width="600px"/>

## Análisis y visualización de datos

El esquema de análisis y visualización de datos se divide en los siguientes conceptos: usted define **filtros** a los atributos que definen sobre qué datos va a trabajar (por ejemplo, sólo datos del año 2012, o dentro de Chile, o incluyendo clientes sólo con cierta id), y luego define **objetos** que realizan cálculos sobre estos datos y muestran gráficamente sus resultados, como pueden ser gráficos, tablas de resumen, etc.

### Filtros, *Select Fields*

Para agregar un campo de filtrado, haga click derecho y seleccione la opción *Select Fields*. Aquí puede seleccionar los atributos sobre los que filtrará.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/5.png" width="300px"/>

Una vez creados los objetos para filtrar, usted puede seleccionar más de un elemento usando las teclas Control y Shift.

Todos los objetos trabajarán en base a todos los filtros, de manera global, por lo que debe recordar eliminar filtros que no esté usando. Esto puede hacerse en el diálogo "Current Selections" haciendo click derecho y seleccionado la opción *Clear*.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/5a.png" width="300px"/>

### Objetos, *Sheet Objects*

Los objetos permiten realizar análisis y visualización de los datos sobre los que hemos filtrado.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/6.png" width="300px"/>

#### Statistics Box

El *Statistics Box* permite obtener estadísticas simples sobre los datos filtrados, como el máximo, mínimo, promedio, etc. Debe indicar el *campo o atributo* a estudiar, así como las *estadísticas* que desea mostrar.

#### Chart

El chart es la herramienta más poderosa que utilizará en QlikView. Éste requiere que usted defina **dimensiones**, correspondiente al eje X del gráfico y **expresiones**, correspondiente al eje Y.

##### Expresiones

Para construir las expresiones QlikView le ofrece un lenguaje simple, con algunas funciones. Algunos ejemplos de expresiones y funciones:

* `Sum(Atributo)`: entregará la suma de todos los valores del atributo asociados con la dimensión elegida. Por ejemplo, si decido sumar todas las ventas de una sucursal, la dimensión sería la id de la sucursal y la expresión sería `Sum(Ventas)`.
* `Count(Atributo)`: cuenta el número de veces que este atributo existe. Siguiendo el mismo ejemplo, sería el número de ventas. Puede recibir el parámetro DISTINCT (i.e. `Count(DISTINCT Atributo)`) que no cuenta los valores repetidos.
* `Min(Atributo)` y `Max(Atributo)`: encuentra el menor y mayor valor respectivamente.
* `Avg(Atributo)` y `Median(Atributo)`: encuentran el promedio y mediana del atributo respectivamente.

Puede explorar todas las funciones disponibles en la pestaña *Functions* de la ventana de expresiones.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/7.png" width="600px"/>

##### Agregación y ordenamiento

En la sección de expresiones puede elegir que su gráfico esté *agregado*, es decir que cada expresión incluya la suma de las anteriores. Siempre se utilizará la opción "full aggregate", sumando todos los valores anteriores.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/8.png" width="600px"/>

Si selecciona "Next", en la siguiente sección puede elegir que su gráfico esté ordenado según el valor del eje Y.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/9.png" width="600px"/>

Si combina la expresión `Sum(Atributo)/Sum(TOTAL Atributo)`, agregación y orden descendiente, puede construir *un gráfico de probabilidad acumulada*, viendo por ejemplo cuántas de las ventas totales son realizadas por los tres vendedores más efectivos. Por ejemplo, en este gráfico se ve que el primer vendedor aporta el 20% de las ventas totales, y que los primeros tres cubren más del 50% de las ventas.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/10.png" width="600px"/>

##### Obtención de datos

Si mueve el cursor sobre cierto punto del gráfico, podrá obtener los valores X e Y de ese punto, lo cual puede ser necesario si usted necesita saber la cifra exacta.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/11.png" width="300px"/>

##### Filtro por valores

Si arrastra y selecciona algunos de los elementos del eje X del gráfico (dimensiones), esto le permitirá filtrar según esos elementos. De este modo podemos obtener estadísticas de qué características tienen los vendedores más efectivos y los menos efectivos, por ejemplo.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/12.png" width="600px"/>
<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/13.png" width="600px"/>

### Pestañas

Para ordenar su trabajo, QlikView le permite crear diferentes pestañas en las que ingresar sus objetos. Recomiendo contestar cada pregunta en una pestaña nueva eligiendo la opción "Add Sheet". Recuerde que los filtros son **globales**, por lo que los definidos en una pestaña afectarán los resultados de las otras -- recuerde eliminar filtros que no esté usando.

<img src="https://raw.githubusercontent.com/franzwr/apunte_bi/master/imgs/14.png" width="300px"/>

Finalmente, recuerde guardar con frecuencia su trabajo en caso de cualquier problema.
