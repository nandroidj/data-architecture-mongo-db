# data-architecture-mongo-db

Se utiliza el sistema de gestion de base de datos **MongoDB** para realizar la practica de operaciones en el entorno de las finanzas.

## Configuracion del entorno Atlas 

<details><summary><b>Pasos necesarios</b></summary><br>

1. En primer lugar se crea una cuenta [Atlas](https://www.mongodb.com/docs/atlas/tutorial/create-atlas-account/).

2. Luego, se procede a acceder al entorno [cloud de MongoDB](https://cloud.mongodb.com/) con la cuenta creada. 

![data base creation](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/01_db_deployment.png)

3. Para crear la base de datos se configuran las credenciales, el usuario y la contraseña, y se agrega la *IP* de la red que se esta utilizando actualmente.

4. Posteriormente, en la tab *Deployment* se procede a presionar el boton *Connect* a traves de la opcion *Connect using MongoDB Compass*.

![compass option](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/02_connect_mongodb_compass_option.png)

![compass option](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/03_mongodb_compass_option.png)

5. Una que se seleccionada la opcion, se selecciona la alternativa *I have MongoDB Compass* y se copia la *URI* que permite la conexion desde la aplicacion de escritorio, `mongodb+srv://<username>:<password>@cluster0.pzoqn.mongodb.net/test`.

6. Seguidamente, desde la version de escritorio *MongoDB Compass* se crea la base de datos *iot* y la coleccion *facturas* como se puede ver en las siguientes imagenes. 

![db creation](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/05_db_creation.png)

![iot db](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/06_iot_db.png)

![iot collection](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/07_iot_facturas_collection.png)

![iot collection detail](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/08_iot_facturas_detail.png)

7. Finalmente, se clickea en la zona inferior la terminal *MONGOSH*

![mongosh](https://github.com/nandroidj/data-architecture-mongo-db/blob/main/docs/imgs/09_mongosh.png)

</details>


## Operaciones

<details><summary><b>Operaciones propuestas</b></summary><br>


1. Consultar la cantidad de documentos insertados.

```
use iot
'switched to db iot'
db.facturas.find().count()

100
```

2. Obtener 1 sólo documento para ver el esquema y los nombres de los campos. Sin mostrar el *_id*.

```
db.facturas.find({}).sort({"_id":1}).limit(1).pretty()


{ _id: ObjectId("55e4a6fabfc68c676a041063"),
  cliente: 
   { apellido: 'Malinez',
     cuit: 2740488484,
     nombre: 'Marina',
     region: 'CENTRO' },
  condPago: 'CONTADO',
  fechaEmision: 2014-02-20T00:00:00.000Z,
  fechaVencimiento: 2014-02-20T00:00:00.000Z,
  item: 
   [ { cantidad: 11, precio: 18, producto: ' CORREA 12mm' },
     { cantidad: 1, precio: 490, producto: 'TALADRO 12mm' } ],
  nroFactura: 1000 }
```

3. Obtener las facturas con fecha de emisión posterior al 23/02/2014 y número menor a 1500. Ordenar por región y cuit del cliente.

```
db.facturas.find({ $and: [ { fechaEmision: {$gte: ISODate("2014-02-23T00:00:000Z") } }, { nroFactura: {$lt: 1500} } ] }).count()

56
```

```
db.facturas.find({$and:[{fechaEmision:{$gte: ISODate("2014-02-23T00:00:00Z")} }, {nroFactura:{$lt:1500}} ]}).sort({"cliente.region":1, "cliente.cuit":1})


{ _id: ObjectId("55e4a6fcbfc68c676a0410a5"),
  cliente: 
   { apellido: 'Manoni',
     cuit: 2029889382,
     nombre: 'Juan Manuel',
     region: 'NEA' },
  condPago: 'CONTADO',
  fechaEmision: 2014-02-24T00:00:00.000Z,
  fechaVencimiento: 2014-02-24T00:00:00.000Z,
  item: 
   [ { cantidad: 2, precio: 60, producto: 'TUERCA 2mm' },
     { cantidad: 1, precio: 490, producto: 'TALADRO 12mm' },
     { cantidad: 15, precio: 90, producto: 'TUERCA 5mm' } ],
  nroFactura: 1066 }
{ _id: ObjectId("55e4a6fcbfc68c676a0410a7"),
  cliente: 
   { apellido: 'Manoni',
     cuit: 2029889382,
     nombre: 'Juan Manuel',
     region: 'NEA' },
  condPago: '60 Ds FF',
  fechaEmision: 2014-02-25T00:00:00.000Z,
  fechaVencimiento: 2014-04-26T00:00:00.000Z,
  item: 
   [ { cantidad: 1, precio: 700, producto: 'SET HERRAMIENTAS' },
     { cantidad: 1, precio: 490, producto: 'TALADRO 12mm' } ],
  nroFactura: 1068 }
{ _id: ObjectId("55e4a6fcbfc68c676a0410a8"),
  cliente: 
   { apellido: 'Manoni',
     cuit: 2029889382,
     nombre: 'Juan Manuel',
     region: 'NEA' },
  condPago: 'CONTADO',
  fechaEmision: 2014-02-25T00:00:00.000Z,
  fechaVencimiento: 2014-02-25T00:00:00.000Z,
  item: [ { cantidad: 10, precio: 90, producto: 'TUERCA 5mm' } ],
  nroFactura: 1069 }

  ....
```

4. Obtener sólo los datos de cliente de las facturas donde se haya comprado “CORREA 10mm”. Ordenar por apellido del cliente.

```
db.facturas.find({"item.producto":"CORREA 10mm"}, {"cliente":1, _id:0}).sort({"cliente.apellido":1})

{ cliente: 
   { apellido: 'Zavasi',
     cuit: 2038373771,
     nombre: 'Martin',
     region: 'CABA' } }
{ cliente: 
   { apellido: 'Zavasi',
     cuit: 2038373771,
     nombre: 'Martin',
     region: 'CABA' } }
{ cliente: 
   { apellido: 'Zavasi',
     cuit: 2038373771,
     nombre: 'Martin',
     region: 'CABA' } }

  ....
```

5. Obtener sólo nombre y apellido de cliente, de las facturas con número entre 2500 y 3000.

```
db.facturas.find({nroFactura: {$gt: 2500, $lt:3000}}, {"cliente.apellido":1, "cliente.nombre":1, _id:0})

empty
```

El numero maximo de facturas es 1099 por lo que tiene sentido no obtener ningun resultado para facturas entre 2500 y 3000.

6. Obtener sólo la fecha de vencimiento de las facturas 5000, 6000, 7000 y 8000.

```
db.facturas.find( { $or: [ {nroFactura: 5000}, {nroFactura: 6000}, {nroFactura: 7000}, {nroFactura: 8000} ] }, {fechaVencimiento:1, _id:0} )

empty
```

Al igual que en el punto 5, el nroFacturaMax es 1099.


7. Obtener las facturas de los clientes cuyo apellido comience con Z. Ordenar por número de factura y devolver solo las primeras 5.

```
db.facturas.find( { "cliente.apellido": { $regex: /^Z/ } } ).sort( { nroFactura: 1 } ).limit(5)


{ _id: ObjectId("55e4a6fbbfc68c676a041064"),
  cliente: 
   { apellido: 'Zavasi',
     cuit: 2038373771,
     nombre: 'Martin',
     region: 'CABA' },
  condPago: '30 Ds FF',
  fechaEmision: 2014-02-20T00:00:00.000Z,
  fechaVencimiento: 2014-03-22T00:00:00.000Z,
  item: [ { cantidad: 2, precio: 134, producto: 'CORREA 10mm' } ],
  nroFactura: 1001 }
{ _id: ObjectId("55e4a6fbbfc68c676a041065"),
  cliente: 
   { apellido: 'Zavasi',
     cuit: 2038373771,
     nombre: 'Martin',
     region: 'CABA' },
  condPago: 'CONTADO',
  fechaEmision: 2014-02-20T00:00:00.000Z,
  fechaVencimiento: 2014-02-20T00:00:00.000Z,
  item: 
   [ { cantidad: 6, precio: 60, producto: 'TUERCA 2mm' },
     { cantidad: 12, precio: 134, producto: 'CORREA 10mm' } ],
  nroFactura: 1002 }

  ....
```

8. Obtener sólo los números de factura en las que la región sea “CENTRO” o la condición de pago sea “CONTADO”. Ordenar descendentemente por númer de factura y devolver de la 5 a la 10.

```
db.facturas.find( { $or: [ { region:'CENTRO' }, { condPago:'CONTADO' } ] }, {nroFactura:1, _id:0}).sort


{ nroFactura: 1000 }
{ nroFactura: 1087 }
{ nroFactura: 1086 }
{ nroFactura: 1084 }
{ nroFactura: 1083 }
```

9. Obtener las facturas de todos los clientes que no sean de apellido “Zavasi” ni “Malinez”.

```
db.facturas.find( { "cliente.apellido": { $nin: [ "Zavasi", "Malinez" ] } }, { nroFactura:1, _id:0 } )


{ nroFactura: 1066 }
{ nroFactura: 1067 }
{ nroFactura: 1068 }
{ nroFactura: 1069 }
{ nroFactura: 1003 }
{ nroFactura: 1004 }
{ nroFactura: 1005 }
{ nroFactura: 1006 }
{ nroFactura: 1010 }
{ nroFactura: 1011 }
{ nroFactura: 1012 }
{ nroFactura: 1013 }
{ nroFactura: 1017 }
{ nroFactura: 1018 }
{ nroFactura: 1019 }
{ nroFactura: 1020 }
{ nroFactura: 1024 }
{ nroFactura: 1025 }
{ nroFactura: 1026 }
{ nroFactura: 1027 }
```


10. Obtener sólo el nombre del producto de las facturas donde se haya comprado 15 unidades de dicho producto.

```
db.facturas.find( { "item.cantidad": 15 }, { "item.producto.$":1, _id:0 } )


{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
{ item: [ { producto: 'TUERCA 5mm' } ] }
```

11. Obtener sólo una factura del cliente de cuit 2038373771, condición de pago “30 Ds FF” y fecha de vencimiento entre el 20/03/2014 y 24/03/2014.

```
db.facturas.find({ $and: [ {"cliente.cuit": 2038373771}, { condPago:"30 Ds FF"}, {fechaVencimiento:{$gt: ISODate("2014-03-20T00:00:00Z")}}, {fechaVencimiento:{$: ISODate("2014-03-24T00:00:00Z")}} ] }, {nroFactura:1, _id:0}).limit(1)


{ nroFactura: 1004 }
```
</details>














