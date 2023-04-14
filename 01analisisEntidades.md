# Analisis de las entidades.

Esta fase del analisis tiene identificar los atributos de las entidades, así como nombrar las relaciones entre ellas, con el fin de obtener el diagrama ER.
Procederemos a analizar cada entidad y sus relaciones.

El analisis de entidad debe seguir el siguiente formato
## Nombre entidad
Descripcion entidad
### Atributos
---
#### nombre atributo
Descripcion atributo
###
### Relaciones
---
#### Nombre relacion
Descripcion relacion




## SUJETO
Por `SUJETO` se refiere a cualquier entidad que tenga un nombre, un lugar, un telefono, un correo y una dirección. En este caso tenemos a `EMPLEADO`, `CLIENTE`, `LUGAR` y `PROVEEDOR`.
### Atributos
---
#### id
Dato serial con el objetivo de identificar los distintos sujetos.
#### telefono
Número de teléfono del sujeto. Este es un dato numérico de 10 dígitos.
#### correo
Correo electrónico del sujeto. Este es un dato alfanumérico.

Una dirección de correo electrónico válida debe cumplir las siguientes condiciones:

- Contener "@"
- La longitud de la parte local (antes del símbolo "@") debe estar comprendida entre 1 y 64 caracteres.
- La longitud de la parte de dominio (después del símbolo "@") debe estar comprendida entre 4 y 255 caracteres.
- La longitud total debe ser menor o igual a 256 caracteres.
- La parte local y la parte de dominio deben comenzar por una letra o dígito y no deben contener dos símbolos "." consecutivos
- La parte local y la parte de dominio pueden contener letras, números y los caracteres ".", "_" y "-".
- La parte del dominio debe terminar con un símbolo "." y entre dos y cuatro caracteres alfabéticos.

#### nombre
Nombre del sujeto. Este es un dato alfanumérico, el cual puede contener espacios, acentos y caracteres especiales, su longitud máxima es de 100 caracteres.
#### codigo postal
Código postal del sujeto. Este es un dato numérico de 5 dígitos.
#### pais
País del sujeto. Esta es una llave foránea que hace referencia a la tabla `PAIS`, es una cadena de texto de 2 caracteres acorde al ISO-3166
alpha2.
#### entidad_federativa
Entidad federativa del sujeto. Esta es una llave foránea que hace referencia a la tabla `ENTIDAD_FEDERATIVA`, es una cadena de texto de 3 caracteres acorde al ISO-3166-2
#### municipio
Municipio del sujeto. Es una cadena de texto de 100 caracteres acorde.
#### calle
Calle del sujeto. Es una cadena de texto de 100 caracteres acorde.
#### numero
Número del sujeto. Es un dato numérico de 5 dígitos.


### Lugar 
Por `LUGAR` se refiere a la sucursal y/o almacen (si se da el caso) en que se encuentran almacenados productos.
Por lugares hacemos referencia a las sucursales, almacenes y/u oficinas que posee la empresa, los dos primeros poseeran una capacidad máxima, en los dos primeros casos de productos y en el ultimo de personal.

Hereda de `SUJETO`.
#### Atributos
---
##### tipo
Tipo de lugar, puede ser `sucursal`, `almacen` u `oficina`.
##### responsable
Responsable del lugar, es una llave foránea que hace referencia a la tabla `EMPLEADO`.
##### cap_max
Capacidad máxima del lugar, es un dato numérico de 20 dígitos.

#### Relaciones
---
Esta entidad presenta relaciones con 8 entidades en total,  `EMPLEADO`, `ARTICULO`, `SERVICIO`, `GASTOS_LUGAR`, `REABASTECIMIENTO`, `TRASLADO`, `PERDIDA` y `VENTA`.
##### Lugar-Empleado (responsable)
Un lugar puede tener uno y solo responsable y un empleado puede ser responsable de uno y solo un lugar. Por lo que que la relación es de uno a uno, implementando para esto .

##### Lugar-Articulo
Un lugar puede almacenar muchos articulos y un articulo puede estar almacenado en muchos lugares. Por lo que que la relación es de muchos a muchos.

Dado que dicha relacion es ineficiente, se implementa una tabla intermedia `INVENTARIO` que relaciona a `LUGAR` y `ARTICULO` con los siguientes atributos:

Las demás relaciones se analizaran cuando la otra entidad sea analizada.

### Empleado
Por `EMPLEADO` se refiere a los trabajadores de la empresa, cada empleado puede pertenecer o a un departamento o a un lugar, pero no a ambos, puesto que pertenecer a un departamento implicaría que pertenece al lugar dónde se encuentra dicho departamento.

Hereda de `SUJETO`.



#### Atributos
---
##### nss
Número de seguridad social del empleado. Este es un dato numérico de 11 dígitos.

##### rfc
Registro federal de contribuyentes del empleado. Este es un dato alfanumérico de 13 caracteres.

##### fecha_nacimiento
Fecha de nacimiento del empleado. Este es un dato de tipo `DATE`.

##### fecha_ingreso
Fecha de ingreso del empleado. Este es un dato de tipo `DATE`.

##### contrato_activo
Contrato activo del empleado. Hace referencia al ultimo contrato que se le ha hecho al empleado, es una llave foránea que hace referencia a la tabla `CONTRATO`.

##### departamento
Departamento al que pertenece el empleado. Hace referencia al departamento al que pertenece el empleado, es una llave foránea que hace referencia a la tabla `DEPARTAMENTO` y es opcional.

##### lugar
Lugar al que pertenece el empleado. Hace referencia al lugar al que pertenece el empleado, es una llave foránea que hace referencia a la tabla `LUGAR` y es opcional.

##### indice_productividad
Indice de productividad del empleado. Este es un dato se encuentra entre el 0 y el 1.

#### Relaciones
---
Esta entidad presenta relaciones con 6 entidades en total, `CONTRATO`, `DEPARTAMENTO`, `LUGAR`, `GASTOS_LUGAR`, `PERDIDA` y `VENTA`.

##### Empleado-Contrato (contrato_activo)
Un empleado puede tener muchos contratos y un contrato puede ser de uno y solo un `EMPLEADO`. Por lo que que la relación es de muchos a uno.

##### Empleado-Departamento (departamento)
Un empleado puede pertenecer a uno y solo un departamento y un departamento puede tener muchos empleados. Por lo que que la relación es de uno a muchos.

##### Empleado-Lugar (lugar)



### Articulo
 Nos interesa identificarlo, conocer su nombre, descripción, precio en que se compra, precio a la venta, categoria a la que pertenece, en qué tiendas/almacenes se encuentra y la cantidad que hay en cada uno, un registro de cuando se reabastece y un registro de cada venta de dicho producto.

 Dado que el  departamento de facturacióntambien usará el sistema, el SAT (Secretaria de Administración Tributaria) requiere dentro de una factura algunos datos del articulo, entre ellos se encuentran
 - Clave de producto o servicio
 - Numero de identificación
 - Clave de unidad
 - Unidad
 - Descripción
 - Valor unitario
 - Si es objeto de impuestos o no

Se procede a analizar atributo por atributo para definir y normalizar cada uno.

#### Atributos
---

##### id
Este atributo tiene el fin de identificar a cada articulo, este es el mismo que el Numero de Identificacion requerido por el SAT, hay distintas formas de conformar el identificador, citando lo descrito por la *Secretaria de Administración Tributaria*
> En este campo se puede registrar el número de parte, identificador del producto o del servicio, la clave de producto o servicio, SKU (número de referencia) o equivalente, propia de la operación del contribuyente emisor del comprobante fiscal descrito en el presente concepto.
>- Opcionalmente se pueden utilizar claves del estándar GTIN (número global de artículo comercial).
>- Puede conformarse desde uno hasta 100 caracteres alfanuméricos.
> Ejemplo: NoIdentificacion= UT421510<sup>4</sup>

Considerando que estamos empleando la gestion del inventario mediante codigo de barras, y estas se obtienen utilzando el GTIN, este sera nuestro identificador, el cual varia de 8 a 14 digitos númericos.

##### nombre
Este atributo tiene cómo finalidad que el usuario final sepa a que articulo se está haciendo referencia, este será un dato de tipo alfanumerico de longitud de 70 caracteres.

##### descripción
Este atributo permite al usuario identificar la naturaleza del articulo, para fines de este proyecto tomaremos que es la descripcion mostrada por el SAT en su catalogo de productos y servicios, para lo que simplemente se hace referencia a la clave de producto o servicio, para una optima implementacion, se creará la entidad `CAT_PROD_SER`, la cual será poblada con la informacion proporcionada por el SAT.

La entidad `CAT_PROD_SER` se analizará más.

##### precio
Este atributo es una pieza clave en nuestra base de datos, este lo podemos considerar en dos partes, el precio a la compra, y el precio a la venta, dado que el precio a la compra no es propio del articulo, más bien, es propio del movimiento de reabastecimiento, este se ignorará de momento en esta entidad, mientras que el precio a la venta sí lo es, este sera referenciado solamente cómo precio, el cual es el valor unitario solicitado por el SAT.

##### categoria
Este atributo tiene un fin similar a la descripcion, con la unica diferencia de que su uso es agrupar a este producto con otros similares, para esto igualmente utilizaremos los datos proporcionados en el catalogo de productos y servicios, a diferencia que esta vez, el interes es unicamente la clave de la clase.

##### unidad
Este atributo es el que se utiliza para identificar la unidad de medida del articulo, para esto se hace referencia a la clave de unidad, la cual es proporcionada por el SAT, para una optima implementacion, se creará la entidad `CAT_UNIDAD`, la cual será poblada con la informacion proporcionada por el SAT.

##### obj_imp
Se utiliza simplemente para identificar si el articulo es objeto de impuesto o no, utilizado posteriormente en el analizis y calculo de impuestos, los posibles valores son:
|obj_imp|Definicion|
|--|--|
|01|No objeto de impuesto|
|02|Sí objeto de impuesto|
|03|Si objeto de impuesto y no obligado al desglose|
|04|Sí objeto del impuesto y no causa impuesto|

##### caracteristicas
Con el fin de analizar el comportamiento de los usuarios, y hacer un analizis más completo de las ventas, estos se guardaran en objetos javascript.


El resto de datos no son propios de un articulo, pues recaen en otras entidades, cómo lo es el precio de reabastecimiento, la cantidad que hay del articulo, o el lugar en el que se almacena, entre otros.

#### Relaciones
---
Esta entidad con las entidades `LUGAR`, `MOVIMIENTO`, `CAT_PROD_SER` y `CAT_UNIDAD`.

La relación con la entidad `LUGAR` ya fue analizada en la entidad `LUGAR`, por lo que no se volverá a analizar.

La relación con la entidad `MOVIMIENTO` se analizará en la entidad `MOVIMIENTO`, puesto que esta tiene entidades hijas que posiblemente no sigan todas la misma relación.

##### CAT_PROD_SER-ARTICULO
Esta relación es de uno a muchos, puesto que un articulo puede tener una sola descripcion, pero una descripcion puede ser utilizada por muchos articulos.

Esta relacion tambien se da en el atributo categoría, donde funciona de la misma manera.

##### CAT_UNIDAD-ARTICULO
Esta relación es de uno a muchos, puesto que un articulo puede tener una sola unidad, pero una unidad puede ser utilizada por muchos articulos.

### Movimiento
El `Movimiento` se refiere a cada entidad donde se requiera saber la cantidad de  articulos, el lugar donde se llevo el movimiento asi como la fecha y hora del movimiento; De esta entidad forman parte como hijas las entidades `TRANSLADO`, `VENTA`, `REBASTECIMIENTO` y `PERDIDA` las cuales son un tipo de movimiento.


### Atributos
---

#### id_movimiento
Cada movimiento debera ser identificado por un id para asi llevar un control de los translados, ventas, rebastecimiento y perdidas

#### cantidad_conceptos
Este atributo hace referencia a la cantidad de conceptos, es decir la cantidad total de articulos que tuvieron un movimiento

#### id_lugar
Cada movimiento sera realizado en un determinado lugar por lo que se necesitara el identificador unico de dicho lugar para tener establecido donde se realizo dicho movimiento

#### fecha
Cada movimiento ademas de ser realizado en un lugar determinado contara con una fecha especifica en la que se realizo, las fechas de estos movimientos empezaran desde la implementacion del sistema en la tienda.

#### hora
Ademas de la fecha tambien se contara con una hora en la cual se realizo el movimiento, esto para llevar un control mas exaustivo y preciso de cada movimiento.
 


Cuando se realiza una `VENTA` se realizará una `FACTURA`, ya sea a publico en general o a un `CLIENTE` en especifico, debemos realizar un registro de cada venta, esta operacion es de ingreso, si es en mostrador o a domicilio.

Un `TRASLADO` es llevado a cabo cuando por algún motivo es necesario mover uno o más articulos de una tienda a otra, o en su caso, de un almacen a otro, o de una almacen a una tienda, ya sea parcial o totalmente, aquí tendremos almenos un `EMPLEADO` encargado, esta operacion no representa ni un ingreso ni un egreso a la empresa.


Una `DEVOLUCION` se realiza, como ya se mencionó arriba, cuando un usuario decide regresar el producto, aquí se le regresa el dinero del costo del producto devuelto, este producto podra regresar al inventario o desecharse.

Una `PERDIDA` se da por dos motivos, que un producto se deba desechar, o, que un producto haya sido robado, esto representa perdidas para la empresa.
--- 
En esta entidad se almacenan los  

## Recursos humanos
## Finanzas
Precio base, porcentaje de ganancia, porcentaje de impuestos, porcentaje de gasstos de administracion, porcentaje de gastos de venta

Cada que se realiza una `venta` se actualiza la cantidad de productos en inventario, de cada `venta`, nos interesa conocer los articulos involucrados, así como la cantidad de cada uno, el total, la tienda en que se realizó la compra, la fecha, si el pago fue en efectivo, transferencia o con tarjeta, así como los datos del `cliente` y el `empleado` que la concretó.

Hay más momentos que pueden alterar al inventario, estos son `traslado` y la `reabastecimiento`, en el caso de `traslado` nos referimos cuando se deben redestribuir los articulos dentro de lugares propios de la empresa (almacen, tienda, etc), es decir cuando de la "tienda 1" se traslada "x" producto a la "tienda 2", en este movimiento nos interesa conocer los 

 obteniendo el siguiente diagrama conceptual:

### Normalizar

--- DIAGRAMA ER FINAL

### DISTRUBUCION DE LA BD
