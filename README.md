# TecnoStore Colombia - Inventario E-commerce

Proyecto de base de datos para una tienda en línea de tecnología. Maneja inventario, pedidos y proveedores usando MongoDB.

## Información del curso

**Universidad:** Universidad Nacional Abierta y a Distancia (UNAD)  
**Vicerrectoría:** Académica y de Investigación  
**Curso:** Big Data  
**Código:** 202016911  
**Actividad:** Tarea 4 - Almacenamiento y Consultas de Datos en Big Data

## Qué tiene

### productos
Los productos de la tienda (celulares, portátiles, tablets, etc)
- codigo, nombre, marca, categoria
- precio, stock, proveedor
- estado (disponible/agotado)

### pedidos
Los pedidos que hacen los clientes
- numero de pedido, cliente, telefono
- ciudad, direccion
- producto, cantidad, total
- metodo de pago, estado

### proveedores
Empresas que nos venden los productos
- nombre, telefono, ciudad, email

## Cómo usar

Abrir el documento word y copiar las consultar a la terminal de mongoDB.


El código tiene ejemplos de:
- Crear, leer, actualizar y borrar productos
- Buscar por precio, marca, categoría
- Ver qué productos tienen poco stock
- Contar cuántos productos hay por categoría
- Calcular el valor total del inventario
- Ver estadísticas de ventas por ciudad
- Saber cuáles son los clientes que más compran