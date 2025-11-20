# SISTEMA DE INVENTARIO E-COMMERCE
## TecnoStore Colombia - Tienda en Línea de Tecnología

---

## 1. DISEÑO DEL ESQUEMA DE BASE DE DATOS

### 1.1 Colecciones Definidas

La base de datos `tecnostore_db` está compuesta por **3 colecciones principales**:

#### **Colección: productos**
Almacena el inventario completo de productos tecnológicos disponibles para la venta.

**Campos:**
- `codigo` (String): Identificador único del producto (Ej: "TECH-0001")
- `nombre` (String): Nombre descriptivo del producto
- `marca` (String): Marca del producto (Samsung, Apple, etc.)
- `categoria` (String): Categoría del producto (Celulares, Portátiles, etc.)
- `precio` (Number): Precio de venta en pesos colombianos
- `stock` (Number): Cantidad disponible en inventario
- `stockMinimo` (Number): Nivel mínimo de reorden (10 unidades)
- `proveedor` (String): Nombre del proveedor
- `garantia` (String): Tiempo de garantía del producto
- `estado` (String): Estado del producto (disponible/agotado)

**Ejemplo de documento:**
```json
{
  "codigo": "TECH-0001",
  "nombre": "Samsung Celular Pro 1",
  "marca": "Samsung",
  "categoria": "Celulares",
  "precio": 1250000,
  "stock": 45,
  "stockMinimo": 10,
  "proveedor": "Tech Import Colombia",
  "garantia": "12 meses",
  "estado": "disponible"
}
```

#### **Colección: pedidos**
Registra todas las órdenes de compra realizadas por los clientes.

**Campos:**
- `numeroPedido` (String): Número único del pedido (Ej: "ORD-2024-00001")
- `cliente` (String): Nombre del cliente
- `telefono` (String): Número de contacto
- `ciudad` (String): Ciudad de entrega
- `direccion` (String): Dirección de envío
- `codigoProducto` (String): Código del producto ordenado
- `cantidad` (Number): Cantidad de unidades
- `total` (Number): Valor total del pedido
- `metodoPago` (String): Forma de pago (Efectivo, Tarjeta, PSE)
- `estado` (String): Estado del pedido (pendiente, enviado, entregado, cancelado)
- `fechaPedido` (Date): Fecha de creación del pedido

**Ejemplo de documento:**
```json
{
  "numeroPedido": "ORD-2024-00001",
  "cliente": "Lina Magali Dagua",
  "telefono": "3001234567",
  "ciudad": "Bogotá",
  "direccion": "Calle 11 #1-10",
  "codigoProducto": "TECH-0002",
  "cantidad": 2,
  "total": 1500000,
  "metodoPago": "Tarjeta",
  "estado": "entregado",
  "fechaPedido": ISODate("2024-01-01")
}
```

#### **Colección: proveedores**
Contiene información de los proveedores de productos.

**Campos:**
- `nombre` (String): Nombre de la empresa proveedora
- `telefono` (String): Teléfono de contacto
- `ciudad` (String): Ciudad donde opera
- `email` (String): Correo electrónico

**Ejemplo de documento:**
```json
{
  "nombre": "Tech Import Colombia",
  "telefono": "3101234567",
  "ciudad": "Bogotá",
  "email": "ventas@techimport.co"
}
```

---

## 2. IMPLEMENTACIÓN EN MONGODB

### 2.1 Creación de la Base de Datos

```javascript
// Crear y conectar a la base de datos
db = db.getSiblingDB('tecnostore_db');

// Crear las colecciones
db.createCollection("productos");
db.createCollection("pedidos");
db.createCollection("proveedores");
```

### 2.2 Inserción de Datos de Prueba

Se insertaron más de 100 documentos distribuidos de la siguiente manera:
- **100 productos** en diferentes categorías de tecnología
- **50 pedidos** con diferentes estados y ciudades
- **5 proveedores** de distintas ciudades de Colombia

---

## 3. CONSULTAS BÁSICAS (CRUD)

### 3.1 INSERCIÓN (Create)

**Objetivo:** Agregar un nuevo producto al inventario.

```javascript
db.productos.insertOne({
  codigo: "TECH-0101",
  nombre: "iPhone 15 Pro Max",
  marca: "Apple",
  categoria: "Celulares",
  precio: 6500000,
  stock: 20,
  stockMinimo: 5,
  proveedor: "Tech Import Colombia",
  garantia: "12 meses",
  estado: "disponible"
});
```

**Resultado:** Se inserta un nuevo documento en la colección productos con toda la información del iPhone 15 Pro Max.

### 3.2 CONSULTA (Read)

**Objetivo:** Buscar productos de una categoría específica.

```javascript
db.productos.find(
  { categoria: "Celulares" },
  { nombre: 1, precio: 1, stock: 1, _id: 0 }
).limit(3);
```

**Resultado:** Retorna los primeros 3 celulares mostrando solo nombre, precio y stock.

### 3.3 ACTUALIZACIÓN (Update)

**Objetivo:** Incrementar el stock de un producto cuando llega nueva mercancía.

```javascript
db.productos.updateOne(
  { codigo: "TECH-0001" },
  { $inc: { stock: 20 } }
);
```

**Resultado:** Aumenta el stock del producto TECH-0001 en 20 unidades.

### 3.4 ELIMINACIÓN (Delete)

**Objetivo:** Eliminar productos agotados que ya no se van a reponer.

```javascript
db.productos.deleteMany({ 
  estado: "agotado",
  stock: 0
});
```

**Resultado:** Elimina todos los productos que estén agotados y con stock en cero.

---

## 4. CONSULTAS CON FILTROS Y OPERADORES

### 4.1 Operador de Comparación ($gt)

**Consulta:** Productos con precio mayor a $1.000.000

```javascript
db.productos.find(
  { precio: { $gt: 1000000 }, estado: "disponible" },
  { nombre: 1, precio: 1, _id: 0 }
).limit(5);
```

**Explicación:** El operador `$gt` (greater than) filtra productos cuyo precio sea superior a 1 millón de pesos.

### 4.2 Operador de Expresión ($expr)

**Consulta:** Productos con stock por debajo del mínimo

```javascript
db.productos.find(
  { $expr: { $lt: ["$stock", "$stockMinimo"] } },
  { nombre: 1, stock: 1, stockMinimo: 1, _id: 0 }
).limit(5);
```

**Explicación:** Compara dos campos del mismo documento para encontrar productos que necesitan reabastecimiento.

### 4.3 Operador de Inclusión ($in)

**Consulta:** Productos de marcas específicas

```javascript
db.productos.find(
  { marca: { $in: ["Samsung", "Apple"] } },
  { nombre: 1, marca: 1, precio: 1, _id: 0 }
).limit(5);
```

**Explicación:** El operador `$in` busca documentos donde el campo marca sea Samsung o Apple.

### 4.4 Operador de Rango ($gte, $lte)

**Consulta:** Productos en un rango de precios

```javascript
db.productos.find(
  { precio: { $gte: 500000, $lte: 1500000 } },
  { nombre: 1, precio: 1, _id: 0 }
).limit(5);
```

**Explicación:** Filtra productos cuyo precio esté entre $500.000 y $1.500.000 usando los operadores `$gte` (mayor o igual) y `$lte` (menor o igual).

### 4.5 Operador Lógico ($or)

**Consulta:** Pedidos pendientes o enviados

```javascript
db.pedidos.find(
  { $or: [{ estado: "pendiente" }, { estado: "enviado" }] },
  { numeroPedido: 1, cliente: 1, estado: 1, _id: 0 }
).limit(5);
```

**Explicación:** El operador `$or` retorna documentos que cumplan al menos una de las condiciones.

### 4.6 Operador de Expresión Regular ($regex)

**Consulta:** Buscar productos por nombre

```javascript
db.productos.find(
  { nombre: { $regex: "Pro", $options: "i" } },
  { nombre: 1, precio: 1, _id: 0 }
).limit(5);
```

**Explicación:** Busca productos cuyo nombre contenga "Pro", sin importar mayúsculas/minúsculas (opción "i").

---

## 5. CONSULTAS DE AGREGACIÓN

Las consultas de agregación permiten procesar múltiples documentos y retornar resultados calculados.

### 5.1 CONTAR - Productos por Categoría

**Objetivo:** Obtener el total de productos y stock por cada categoría.

```javascript
db.productos.aggregate([
  { $group: { 
    _id: "$categoria", 
    totalProductos: { $sum: 1 },
    stockTotal: { $sum: "$stock" }
  }},
  { $sort: { totalProductos: -1 } }
]);
```

**Explicación:**
- `$group`: Agrupa documentos por categoría
- `$sum: 1`: Cuenta un documento por cada producto
- `$sum: "$stock"`: Suma el stock de todos los productos
- `$sort`: Ordena de mayor a menor

**Resultado esperado:**
```json
[
  { "_id": "Celulares", "totalProductos": 15, "stockTotal": 450 },
  { "_id": "Portátiles", "totalProductos": 14, "stockTotal": 380 },
  ...
]
```

### 5.2 SUMAR - Valor Total del Inventario

**Objetivo:** Calcular el valor monetario total del inventario por categoría.

```javascript
db.productos.aggregate([
  { $match: { estado: "disponible" } },
  { $group: { 
    _id: "$categoria", 
    valorInventario: { $sum: { $multiply: ["$precio", "$stock"] } }
  }},
  { $sort: { valorInventario: -1 } }
]);
```

**Explicación:**
- `$match`: Filtra solo productos disponibles
- `$multiply`: Multiplica precio por stock para cada producto
- `$sum`: Suma todos los valores calculados por categoría

**Resultado esperado:**
```json
[
  { "_id": "Portátiles", "valorInventario": 125000000 },
  { "_id": "Celulares", "valorInventario": 98000000 },
  ...
]
```

### 5.3 PROMEDIAR - Precios por Marca

**Objetivo:** Calcular estadísticas de precios por cada marca.

```javascript
db.productos.aggregate([
  { $group: { 
    _id: "$marca", 
    precioPromedio: { $avg: "$precio" },
    precioMaximo: { $max: "$precio" },
    precioMinimo: { $min: "$precio" }
  }},
  { $sort: { precioPromedio: -1 } }
]);
```

**Explicación:**
- `$avg`: Calcula el promedio de precios
- `$max`: Encuentra el precio más alto
- `$min`: Encuentra el precio más bajo

**Resultado esperado:**
```json
[
  { "_id": "Apple", "precioPromedio": 4500000, "precioMaximo": 6500000, "precioMinimo": 2800000 },
  { "_id": "Samsung", "precioPromedio": 2100000, "precioMaximo": 4200000, "precioMinimo": 800000 },
  ...
]
```

### 5.4 Análisis de Ventas por Estado

**Objetivo:** Obtener estadísticas de pedidos según su estado.

```javascript
db.pedidos.aggregate([
  { $group: { 
    _id: "$estado", 
    cantidad: { $sum: 1 },
    ventasTotal: { $sum: "$total" }
  }},
  { $sort: { ventasTotal: -1 } }
]);
```

**Resultado esperado:**
```json
[
  { "_id": "entregado", "cantidad": 20, "ventasTotal": 45000000 },
  { "_id": "enviado", "cantidad": 15, "ventasTotal": 32000000 },
  ...
]
```

### 5.5 Ventas Mensuales

**Objetivo:** Calcular ventas totales agrupadas por mes.

```javascript
db.pedidos.aggregate([
  { $match: { estado: { $ne: "cancelado" } } },
  { $group: { 
    _id: { $month: "$fechaPedido" },
    totalVentas: { $sum: "$total" },
    cantidadPedidos: { $sum: 1 }
  }},
  { $sort: { _id: 1 } }
]);
```

**Explicación:**
- `$match`: Excluye pedidos cancelados
- `$month`: Extrae el número del mes de la fecha
- Agrupa y suma ventas por mes

### 5.6 Promedio de Ventas por Ciudad

**Objetivo:** Identificar qué ciudades tienen el mayor ticket promedio.

```javascript
db.pedidos.aggregate([
  { $group: { 
    _id: "$ciudad", 
    promedioVentas: { $avg: "$total" },
    totalPedidos: { $sum: 1 }
  }},
  { $sort: { promedioVentas: -1 } }
]);
```

**Resultado esperado:**
```json
[
  { "_id": "Bogotá", "promedioVentas": 1850000, "totalPedidos": 18 },
  { "_id": "Medellín", "promedioVentas": 1720000, "totalPedidos": 12 },
  ...
]
```

### 5.7 Producto Más Caro por Categoría

**Objetivo:** Encontrar el producto más costoso en cada categoría.

```javascript
db.productos.aggregate([
  { $sort: { precio: -1 } },
  { $group: { 
    _id: "$categoria",
    productoMasCaro: { $first: "$nombre" },
    precio: { $first: "$precio" }
  }}
]);
```

**Explicación:**
- Primero ordena por precio descendente
- Luego agrupa y toma el primero de cada grupo

### 5.8 Stock Total por Proveedor

**Objetivo:** Analizar el inventario suministrado por cada proveedor.

```javascript
db.productos.aggregate([
  { $group: { 
    _id: "$proveedor", 
    stockTotal: { $sum: "$stock" },
    totalProductos: { $sum: 1 },
    valorInventario: { $sum: { $multiply: ["$precio", "$stock"] } }
  }},
  { $sort: { valorInventario: -1 } }
]);
```

**Resultado esperado:**
```json
[
  { 
    "_id": "Tech Import Colombia", 
    "stockTotal": 850, 
    "totalProductos": 25,
    "valorInventario": 95000000 
  },
  ...
]
```

### 5.9 Ventas por Método de Pago

**Objetivo:** Conocer qué métodos de pago son más utilizados.

```javascript
db.pedidos.aggregate([
  { $match: { estado: "entregado" } },
  { $group: { 
    _id: "$metodoPago", 
    totalVentas: { $sum: "$total" },
    cantidad: { $sum: 1 }
  }}
]);
```

### 5.10 Top 5 Clientes

**Objetivo:** Identificar los mejores clientes por volumen de compras.

```javascript
db.pedidos.aggregate([
  { $group: { 
    _id: "$cliente", 
    totalCompras: { $sum: "$total" },
    cantidadPedidos: { $sum: 1 }
  }},
  { $sort: { totalCompras: -1 } },
  { $limit: 5 }
]);
```
