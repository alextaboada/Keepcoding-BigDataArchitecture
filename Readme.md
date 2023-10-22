# Práctica Arquitectura de datos
## Definición DAaaS
Crear un sitio web monetizable como SaaS.

Esta web permitirá a los clientes añadir los productos de su propia tienda a una dashboard y hacer seguimiento de sus productos en varias webs de la competencia.

Cada cliente, al registrarse, podrá conectarse con su propia tienda online a través de la propia API que posee su CMS (Prestashop, Woocommerce, Agento, Shopify…) y seleccionar los productos que quiere comparar. Esta selección de productos tienes 2 motivos:

 1. En toda tienda existen productos que no tiene sentido hacer seguimiento(productos únicos, muy baratos, etc…)
 2. Posibilidad de hacer varios planes de precios, aumentando el número de productos según la tarifa que se pague

Una vez seleccionados los productos, el cliente seleccionará con que webs de la competencia quiere comparar sus productos. Para ello, crearemos los diferentes crawlers y scrappings, uno por cada web de la competencia. En el caso de que ya estén creados, se podrán seleccionar de un desplegable. Si no están creados, se crearán para que posteriormente puedan ser usados por más clientes. Estos crawlers y scrappers que tendrán dos funciones:

 1. Sacar las url de cada producto en la competencia (en caso de que la web de la competencia no muestre referencias o tenga referencias propias, se podrán introducir manualmente)
 2. Sacar el precio de cada producto en cada web de la competencia, de manera diaria

La salida de todo este proceso serán dos páginas en la propia web:

 - Para una visión general, una tabla donde se verá una fila por cada producto, en la que se podrá ver el nombre del producto, la referencia y el precio (todo esto de la propia web del cliente) y los precios de la competencia.
 - Para una visión más particular de cada producto, una pantalla donde se vea el nombre, referencia y precio (todo esto de la propia web del cliente), los precios actuales de cada competencia para ese producto y una gráfica de línea con el histórico de precios de la competencia

## Arquitectura DAaas

 - Fuente de datos: Ecommerce del cliente, que estará construido con un CMS específico para ventas(Prestashop, Woocommerce, Magento, Shopify). En el caso de 
 - Sitio web (realizado con el framework laravel), para lo cual necesitaremos una VM con Linux en el que instalaremos un servidor web junto con el framework
 - Base de datos Google SQL
 - Crawlers para sacar las url de la competencias y la API de la tienda origen(functions de Google)
   - Librería Requests para leer de la API
   - Librería psycopg2 para guardar los datos en la base de datos
 - Scrapping para sacar los precios(functions de google)
   - Librería Scrappy para sacar los datos de las diferentes webs
   - Librería psycopg2 para guardar datos en la base de datos
 - Scheduler  para forzar el scrapping cada 24 horas

## DAaaS Operating Model Design

Para leer la API del ecommerce, usaremos un crawler de Python(function). Esto nos dará todos los productos de la tienda y en la propia web el cliente podrá seleccionar todos los productos para los cuales queremos hacer un seguimiento y los guardaremos en una base de datos relacional Google SQL.

El siguiente paso es sacar la url de cada producto en cada una de las páginas de la competencia. Para ellos, usaremos otro crawler de Python al que pasaremos la referencia del producto y nos devolverá la URL en cada web de la competencia. De esta manera tendremos siempre la url para poder solicitar los precios de manera más eficiente. En caso de que no sea posible conseguir la url del producto (existen webs que no muestran la referencia o bien tienen referencias internas), el cliente podrá introducir esos valores manualmente.

Una vez al día, el schedueler forzará a cada scrapper asociado a cada tienda de la competencia a recuperar los productos de esa tienda junto con su producto principal asociado, buscar el precio para ese día para cada producto y guardar los valores de precio de esa competencia, producto original y fecha en la base de datos.

Por último, el cliente, en el momento que lo desee, podrá consultar los productos en su dashboard, de dos maneras:
- Por un lado, a través de una tabla donde podrá ver todos los productos con el precio en su propia tienda, junto con los precios en las tiendas de la competencia seleccionadas y un % de diferencia de precio entre su precio y el de la competencia. 
![Ver imagen de reporte de tabla](https://github.com/alextaboada/Keepcoding-BigDataArchitecture/blob/main/salida_tabla.png)

- Si desea una visión histórica de los precios, al entrar en cada uno de los productos, verá una página con los precios actuales, tanto de su tienda como de la competencia junto con un gráfico de líneas con el valor del precio de la competencia a lo largo del tiempo.
![Ver imagen de reporte detallado](https://github.com/alextaboada/Keepcoding-BigDataArchitecture/blob/main/salida_tabla.png)

## Esquema DAaas

[Esquema en formato draw.io](https://github.com/alextaboada/Keepcoding-BigDataArchitecture/blob/main/esquema.drawio)

![Esquema](https://github.com/alextaboada/Keepcoding-BigDataArchitecture/blob/main/esquema.png) 