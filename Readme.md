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

 - Sitio web (realizado con el framework laravel), para lo cual necesitaremos una VM con Linux en el que instalaremos un servidor web junto con el framework
 - Base de datos Google SQL
 - Crawlers para sacar las url de la competencias y la API de la tienda origen(functions de Google)
 - Scrapping para sacar los precios(functions de google)
 - Scheduler  para forzar el scrapping cada 24 horas

## DAaaS Operating Model Design

Para leer la api del ecommerce, usaremos un crawler de Python(function). Esto nos dará todos los productos de la tienda y en la propia web seleccionaremos aquellos productos de los que queramos hacer el seguimiento y los guardaremos en una base de datos relacional Google SQL.

El siguiente paso es sacar la url de cada producto en cada una de las páginas de la competencia. Para ellos, usaremos otro crawler de Python al que pasaremos la referencia del producto y nos devolverá la URL en cada web de la competencia. De esta manera tendremos siempre la url para poder solicitar los precios de manera más eficiente.

Por último, una vez al día, cada scrapper asociado a cada tienda de la competencia recibirá las urls de cada producto en la competencia y devolverá el precio, guardándolo en la base de datos, asociado a la fecha y al producto original.