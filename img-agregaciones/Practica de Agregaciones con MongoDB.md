# Practica Agregaciones en MongoDB 

##### Para hacer esta práctica vamos a cargar unos datos ficticios de empresas.
##### Tienes un fichero denominado “productos.json” en los recursos de esta actividad
##### Debes crear una base de datos y agregar los documentos de productos
##### Cada documento contiene información de productos, como el fabricante, unidades y similar

#### Consultas

##### 1_Cuenta los productos de tipo “medio”, usando un método básico
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        tipo: "medio",
      },
  },
  {
    $count:
      /**
       * Provide the field name for the count.
       */
      "total_productos_medio",
  },
]
```
<img src="/img-agregaciones/img1.png" alt="Agregación con Group-Max" width="auto" height="auto
" >

##### 2_Indicar con un distinct, las empresas (fabricantes) que hay en la colección
```mongo
[
  {
    $group: {
      _id: "$fabricante",
    },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 1,
      },
  },
]
```
<img src="/img-agregaciones/img2.png" alt="Agregación con Group-Max" width="auto" height="auto
" >

##### 3_Usando aggregate, visualizar los productos que tengan más de 80 unidades
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $gt: 80,
        },
      },
  },
]
```
<img src="/img-agregaciones/img3.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 4_Con $project visualizar solo el nombre, unidades y precio de los productos que tengan menos de 10 unidades
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        unidades: 1,
        precio: 1,
      },
  },
]
```
<img src="/img-agregaciones/img4.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 5_Con $project ponemos el fabricante, pero le cambiamos el nombre por “empresa”. Usamos el mismo comando anterior
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        empresa: "$fabricante",
        unidades: 1,
        precio: 1,
      },
  },
]
```
<img src="/img-agregaciones/img5.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 6_Añadir a la consulta anterior un campo calculado que se llame total y que multiplique precio por unidades.
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
      },
  },
]
```
<img src="/img-agregaciones/img6.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 7_Hacer que el nombre salga en mayúsculas con el operador $toUpper
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: {
          $toUpper: "$nombre",
        },
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
      },
  },
]
```
<img src="/img-agregaciones/img7.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 8_Añadir un campo calculado que ponga el nombre del producto y el tipo concatenado con el operador $concat. Le llamamos al campo “completo”
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
        completo: {
          $concat: ["$nombre", " - ", "$tipo"],
        },
      },
  },
]
```
<img src="/img-agregaciones/img8.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 9_Ordena el resultado por el campo “total”
```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        unidades: {
          $lt: 10,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        unidades: 1,
        precio: 1,
        empresa: "$fabricante",
        total: {
          $multiply: ["$precio", "$unidades"],
        },
        completo: {
          $concat: ["$nombre", " - ", "$tipo"],
        },
      },
  },
  {
    $sort:
      /**
       * Provide any number of field/order pairs.
       */
      {
        total: 1,
      },
  },
]
```
<img src="/img-agregaciones/img9.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 10_Haciendo una nueva consulta, averiguar el numero de productos por tipo de producto
```mongo
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$tipo",
        count: {
          $sum: 1,
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        tipo: "$_id",
        cantidad: "$count",
      },
  },
]
```
<img src="/img-agregaciones/img10.png" alt="Agregación con Group-Max" width="auto" height="auto
" >

##### 11_Añadir el valor mayor y el menor
```mongo
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$tipo",
        cantidad: {
          $sum: 1,
        },
        max_precio: {
          $max: "$precio",
        },
        min_precio: {
          $min: "$precio",
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        tipo: "$_id",
        cantidad: 1,
        max_precio: 1,
        min_precio: 1,
      },
  },
]
```
<img src="/img-agregaciones/img11.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 12_ Añade el total de unidades por cada tipo
```mongo
[
  {
    $group:
      /**
       * _id: The id of the group.
       * fieldN: The first field name.
       */
      {
        _id: "$tipo",
        cantidad: {
          $sum: 1,
        },
        total_unidades: {
          $sum: "$unidades",
        },
        max_precio: {
          $max: "$precio",
        },
        min_precio: {
          $min: "$precio",
        },
      },
  },
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        tipo: "$_id",
        cantidad: 1,
        total_unidades: 1,
        max_precio: 1,
        min_precio: 1,
      },
  },
]
```
<img src="/img-agregaciones/img12.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 13_Con el operador set y el operador “$substr” visualiza todos los datos del producto"Small Metal Tuna" y los primeros 5 caracteres del nombre

```mongo
[
  {
    $match:
      /**
       * query: The query in MQL.
       */
      {
        nombre: "Small Metal Tuna",
      },
  },
  {
    $set:
      /**
       * field: The field name
       * expression: The expression.
       */
      {
        nombre: {
          $substr: ["$nombre", 0, 5],
        },
      },
  },
]
```
<img src="/img-agregaciones/img13.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 14_Creamos una salida que tenga el nombre del articulo y el total (precio por unidades) y lo guardamos en una colección denominada productos2
```mongo
[
  {
    $project:
      /**
       * specifications: The fields to
       *   include or exclude.
       */
      {
        _id: 0,
        nombre: 1,
        total: {
          $multiply: ["$precio", "$unidades"],
        },
      },
  },
  {
    $out:
      /**
       * Provide the name of the output collection.
       */
      "productos2",
  },
]
```
<img src="/img-agregaciones/img14.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 15_Comprobamos que se ha creado

<img src="/img-agregaciones/img15.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
##### 17_Hacemos un find para comprobar el resultado
<img src="/img-agregaciones/img16.png" alt="Agregación con Group-Max" width="auto" height="auto
" >
