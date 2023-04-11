# Carga de datos en la base de datos distribuida de una cadena de tiendas de abarrotes. (Documentación)

En este repositorio se concentraran todos los documentos de los distintos sistemas (unicamente documentacion, no codigo, la documentación puede incluir trozos de codigo indispensables para el entendimiento de esta).

## ¿De que va el proyecto?
Se realizará el diseño de un sistema de información ERP (Enterprise Resource Planning) de una cadena de tienda de abarrotes. Crear el modelo lógico y físico de bases de datos distribuidas.

## ¿Cómo funciona una cadena de tienda de abarrotes?

Las tiendas de abarrotes son pequeños o medianos negocios que ofrecen una gran variedad de productos de primera necesidad; desde alimentos hasta productos de limpieza, e incluso, hay algunas que tienen disponibilidad de productos de salchichonería y bebidas alcohólicas<sup>1</sup>.

Colocandonos en el contexto proporcionado por el proyecto 
>El sistema es un ERP de una empresa de tiendas de abarrotes la cual tiene 4 sucursalesen la ciudad (Sucursal Lerma, Zinacantepec, Toluca centro y Metepec). El desarrollo debe ser un sistema integral donde se incluyan todas las áreas y los procesos de negocio de la empresa (ventas, control de inventarios, entrega de productos, finanzas, facturación, recursos humanos). No necesariamente en alguna de las sucursales estarán las bases de datos <sup>2</sup>

obtenemos una guia para nuestra investigación, principalmente enfocado a entender cual es el flujo de trabajo de una tienda de abarrotes.

Las partes fundamentales del proceso de negocio son el control de inventario, recursos humanos y finanzas, las cuales se analizaran aisladamente para posteriormente unificarlas.

## Inventario

 El inventario es el conjunto de artículos o materiales que un negocio tiene la intención de vender a los clientes con fines lucrativos. 

La gestión de inventario, un elemento crítico de la cadena de suministro, es el seguimiento del inventario desde el momento de su fabricación hasta los almacenes, y desde estas instalaciones hasta el punto de venta. 

¿Cuáles son los tipos de gestión de inventario?
Gestión periódica de inventarios
El sistema periódico de inventarios es un método de valoración que se utiliza para los informes financieros en el que se realiza un recuento físico del inventario a intervalos específicos. Este método contable toma el inventario al comienzo de un período, agrega nuevas compras de inventario durante el período y deduce el inventario final para derivar el costo de los bienes vendidos (COGS).


Gestión de inventario de códigos de barras
Las empresas utilizan sistemas de gestión de inventario de códigos de barras para asignar un número a cada producto que venden. Pueden asociar varios puntos de datos al número, incluido el proveedor, las dimensiones del producto, el peso e incluso datos variables, como saber cuántos hay disponibles.


Gestión de inventario RFID
La identificación por radiofrecuencia o RFID es un sistema que transmite de forma inalámbrica la identidad de un producto en forma de un número de serie único para rastrear artículos y proporcionar información detallada del mismo. El sistema de gestión de almacenes basado en RFID puede mejorar la eficiencia, aumentar la visibilidad del inventario y garantizar un registro automático rápido para la recepción y la entrega.<sup>3</sup>

Dado que las ventas de la empresa son de productos previamente adquiridos, es decir no son producidos por esta misma, la mejor opcion es utilizar la gestión de inventario de código de barras.


El objetivo de la gestión de inventario es tener los productos correctos en el lugar adecuado y en el momento preciso. Esto requiere visibilidad de inventario: saber cuándo se debe hacer los pedidos, cuánto se debe pedir y dónde almacenar las existencias.

- Compra de inventario: los productos listos para la venta se compran y se entregan en el almacén o directamente en el punto de venta.
- Almacenamiento de inventario: el inventario se almacena hasta que se necesita. Los bienes o materiales se transfieren a través de su red, y hasta que estén listos para su envío.
- Beneficio del inventario: Se controla la cantidad de producto a la venta. Las mercancías acabadas son liberadas para tramitar los pedidos. Los productos son enviados a los clientes.<sup>3</sup>

En el inventario se encunetran distintas entidades relacionadas, la principal es el `articulo`, puesto que es la manipulación de este en que giran las acciones de la empresa.

Este `articulo` sera almacenado ya sea en alguna o en todas las tiendas u almacenes, este podra ser vendido, trasladado, reabastecido o perdido, estas acciones las identificaremos como `movimientos`.

Cuando se realiza una `venta` se realizará una `factura`, ya sea a publico en general o a un cliente en especifico, debemos realizar un registro de cada venta, esta operacion es de ingreso.

Un `traslado` es llevado a cabo cuando por algún motivo es necesario mover uno o más articulos de una tienda a otra, o en su caso, de un almacen a otro, o de una almacen a una tienda, ya sea parcial o totalmente, aquí tendremos almenos un `empleado` encargado, esta operacion no representa ni un ingreso ni un egreso a la empresa.

Un `reabastecimiento` es la compra de `articulos` a un `proveedor`, este se realizará cuando se acerque a un minimo de cantidades de dichos articulos, esta representa un egreso.

Una `devolución` se realiza, como ya se mencionó arriba, cuando un usuario decide regresar el producto, aquí se le regresa el dinero del costo del producto devuelto, este producto podra regresar al inventario o desecharse.

Una `perdida` se da por dos motivos, que un producto se deba desechar, o, que un producto haya sido robado, esto representa perdidas para la empresa.

```mermaid
graph TD;
    LUGAR---ARTICULO;    
    SUJETO---MOVIMIENTO;
    ARTICULO---MOVIMIENTO;
    LUGAR---MOVIMIENTO;
```
Diagrama de relaciones generales entre entidades.

Se procede a analizar individualmente cada entidad, con el fin de definir los atributos que cada una poseera, a ecepcion de los englobados en sujeto.

### Lugar 
Por `LUGAR` nos referimos a la sucursal y/o almacen (si se da el caso) en que se encuentra almacenado un producto.

---
### Articulo
 Nos interesa identificarlo, conocer su nombre, descripción, precio en que se compra, precio a la venta, palabras clave, categoria a la que pertenece, en qué tiendas/almacenes se encuentra y la cantidad que hay en cada uno, un registro de cuando se reabastece y un registro de cada venta de dicho producto.

 Dado que el  departamento de facturacióntambien usará el sistema, el SAT (Secretaria de Administración Tributaria) requiere dentro de una factura algunos datos del articulo, entre ellos se encuentran
 - Clave de producto o servicio
 - Numero de identificación
 - Clave de unidad
 - Unidad
 - Descripción
 - Valor unitario
 - Si es objeto de impuestos o no

Se procede a analizar atributo por atributo para definir y normalizar cada uno.

#### id
Este atributo tiene el fin de identificar a cada articulo, será unico y dependerá de la empresa, este es el mismo que el Numero de Identificacion requerido por el SAT, hay distintas formas de conformar el identificador, citando lo descrito por la *Secretaria de Administración Tributaria*
> En este campo se puede registrar el número de parte, identificador del producto o del servicio, la clave de producto o servicio, SKU (número de referencia) o equivalente, propia de la operación del contribuyente emisor del comprobante fiscal descrito en el presente concepto.
>- Opcionalmente se pueden utilizar claves del estándar GTIN (número global de artículo comercial).
>- Puede conformarse desde uno hasta 100 caracteres alfanuméricos.
> Ejemplo: NoIdentificacion= UT421510<sup>4</sup>

Considerando que estamos empleando la gestion del inventario mediante codigo de barras, y estas se obtienen utilzando el GTIN, este sera nuestro identificador.

El GTIN es el número global de articulo comercial, un modelo oficial para estandarizar los procesos de identificación de productos y gestión comercial.
Es utilizado para identificar de manera única a cualquier producto o ítem sobre el cual existe una necesidad de obtener una información específica y al cual se le debe asignar un precio. Esta definición incluye materias primas, productos terminados, insumos y servicios.<sup>5</sup>
Existe distintas formas del GTIN las cuales varian de 8 a 14 digitos númericos.

#### nombre
Este atributo tiene cómo finalidad que el usuario final sepa a que articulo se está haciendo referencia, este será un dato de tipo alfanumerico de longitud de 70 caracteres.

#### descripción
Este atributo permite al usuario identificar la naturaleza del articulo, para fines de este proyecto tomaremos que es la descripcion mostrada por el SAT en su catalogo de productos y servicios, para lo que simplemente se hace referencia a la clave de producto o servicio, para una optima implementacion, se creará la entidad `CAT_PROD_SER`, la cual será poblada con la informacion proporcionada por el SAT.

La entidad `CAT_PROD_SER` se analizará más adelante en la sección facturacipon.

#### precio
Este atributo es una pieza clave en nuestra base de datos, este lo podemos considerar en dos partes, el precio a la compra, y el precio a la venta, dado que el precio a la compra no es propio del articulo, más bien, es propio del movimiento de reabastecimiento, este se ignorará de momento en esta entidad, mientras que el precio a la venta sí lo es, este sera referenciado solamente cómo precio, el cual es el valor unitario solicitado por el SAT.

#### categoria
Este atributo tiene un fin similar a la descripcion, con la unica diferencia de que su uso es agrupar a este producto con otros similares, para esto igualmente utilizaremos los datos proporcionados en el catalogo de productos y servicios, a diferencia que esta vez, el interes es unicamente la clave de la clase.

#### unidad
Este atributo es requerido por el SAT, el fin es conocer la tangibilidad de los articulos(kg, cm, lts, etc), al igual que con la categoria y descripcion, nos proporciona un catalogo de unidades, por lo que se implementará la entidad `CAT_UNIDAD`.

#### obj_imp
Se utiliza simplemente para identificar si el articulo es objeto de impuesto o no, utilizado posteriormente en el analizis y calculo de impuestos

#### caracteristicas
Con el fin de analizar el comportamiento de los usuarios, y hacer un analizis más completo de las ventas, estos se guardaran en objetos javascript.


El resto de datos no son propios de un articulo, pues recaen en otras entidades, cómo lo es el precio de reabastecimiento, la cantidad que hay del articulo, o el lugar en el que se almacena, entre otros.

```mermaid
erDiagram
ARTICULO }|--|| CAT_PROD_SER :tiene
ARTICULO }|--|| CAT_UNIDAD :tiene
ARTICULO{
    Entero(14) id PK 
    CARACTER(70) nombre
    Entero(8) descripcion FK
    Entero precio
    Entero(8) categoria FK
    CARACTER(3) unidad FK
    ENUM obj_imp
    JSON caracteristicas
}
CAT_UNIDAD{
    Entero(8) clave PK
    CARACTER(200) tipo
    CARACTER(50) nombre
}
CAT_PROD_SER{
    Entero(8) clave PK
    CARACTER(200) descripcion
}
```
---
### Movimientos
--- 
En esta entidad se almacenan los  

## Recursos humanos
## Finanzas

Precio base, porcentaje de ganancia, porcentaje de impuestos, porcentaje de gasstos de administracion, porcentaje de gastos de venta





Cada que se realiza una `venta` se actualiza la cantidad de productos en inventario, de cada `venta`, nos interesa conocer los articulos involucrados, así como la cantidad de cada uno, el total, la tienda en que se realizó la compra, la fecha, si el pago fue en efectivo, transferencia o con tarjeta, así como los datos del `cliente` y el `empleado` que la concretó.

Hay más momentos que pueden alterar al inventario, estos son `traslado` y la `reabastecimiento`, en el caso de `traslado` nos referimos cuando se deben redestribuir los articulos dentro de lugares propios de la empresa (almacen, tienda, etc), es decir cuando de la "tienda 1" se traslada "x" producto a la "tienda 2", en este movimiento nos interesa conocer los 
 



# ...

 obteniendo el siguiente diagrama conceptual:




## Referencias
1. [Crehana | Blog | Negocios | ¿Cómo poner una tienda de abarrotes](https://www.crehana.com/blog/negocios/como-poner-tienda-abarrotes/)
2. [Pablo Salas Castillo | Evaluación Practica](https://alumnouaemex.sharepoint.com/sites/BasedeDatosIICO02Semestre2023a/Materiales%20de%20clase/Evaluacion%20practica/Evaluacion2023a_Co02.pdf?CT=1680902754832&OR=ItemsView)
3. [IBM | ¿Qué es la gestión de inventario?](https://www.ibm.com/mx-es/topics/inventory-management)
4. [SAT | Anexo 20 guia de los comprobantes fiscales digitales por internet](http://omawww.sat.gob.mx/tramitesyservicios/Paginas/documentos/Anexo_20_Guia_de_llenado_CFDI.pdf)
5. [GS1 Mexico | ¿Qué es el GTIN y para qué sirve?](https://blog.gs1mexico.org/que-es-codigo-gtin-para-que-sirve)