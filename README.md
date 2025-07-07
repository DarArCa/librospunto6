# librospunto6


/*
Proyecto de Base de Datos: Sistema de Pedidos

Este proyecto contiene la estructura y un conjunto de consultas SQL desarrolladas 
para gestionar un sistema de pedidos entre clientes, productos y sus ubicaciones. 
La base de datos permite realizar análisis sobre ventas, clientes y relaciones entre tablas clave.

Estructura de Tablas:

El sistema está compuesto por las siguientes tablas principales:

- Clientes: Información de los clientes registrados.
- Ubicaciones: Direcciones asociadas a los clientes.
- Productos: Artículos disponibles para la venta.
- Pedidos: Órdenes realizadas por los clientes.
- DetallesPedido: Productos específicos incluidos en cada pedido.

Consultas SQL Incluidas:

A continuación se describen las consultas desarrolladas, junto con su objetivo y resultado esperado.
*/

/*
1. Obtener el producto más vendido (por cantidad total vendida)

SELECT Productos.nombre, SUM(DetallesPedido.cantidad) AS total_vendida
FROM DetallesPedido
JOIN Productos ON DetallesPedido.producto_id = Productos.id
GROUP BY Productos.id
ORDER BY total_vendida DESC
LIMIT 1;
*/

/*
2. Obtener el cliente que más ha gastado

SELECT Clientes.nombre, SUM(Pedidos.total) AS total_gastado
FROM Pedidos
JOIN Clientes ON Pedidos.cliente_id = Clientes.id
GROUP BY Clientes.id
ORDER BY total_gastado DESC
LIMIT 1;
*/

/*
3. Obtener la ciudad con más pedidos

SELECT Ubicaciones.ciudad, COUNT(Pedidos.id) AS total_pedidos
FROM Pedidos
JOIN Ubicaciones ON Pedidos.ubicacion_id = Ubicaciones.id
GROUP BY Ubicaciones.ciudad
ORDER BY total_pedidos DESC
LIMIT 1;
*/

/*
4. Obtener productos que nunca han sido vendidos

SELECT Productos.nombre
FROM Productos
LEFT JOIN DetallesPedido ON Productos.id = DetallesPedido.producto_id
WHERE DetallesPedido.id IS NULL;
*/

/*
5. Obtener el cliente con más pedidos realizados

SELECT Clientes.nombre, COUNT(Pedidos.id) AS total_pedidos
FROM Pedidos
JOIN Clientes ON Pedidos.cliente_id = Clientes.id
GROUP BY Clientes.id
ORDER BY total_pedidos DESC
LIMIT 1;
*/

/*
6. Obtener productos que más ingresos han generado

SELECT Productos.nombre, SUM(DetallesPedido.cantidad * DetallesPedido.precio_unitario) AS total_generado
FROM DetallesPedido
JOIN Productos ON DetallesPedido.producto_id = Productos.id
GROUP BY Productos.id
ORDER BY total_generado DESC;
*/

/*
7. Obtener clientes, ciudades asociadas y total de compras

SELECT clientes.id, clientes.nombre,
       GROUP_CONCAT(DISTINCT ubicaciones.ciudad) AS ciudades,
       SUM(pedidos.total) AS total_pedidos
FROM clientes
JOIN pedidos ON clientes.id = pedidos.cliente_id
JOIN ubicaciones ON pedidos.ubicacion_id = ubicaciones.id
GROUP BY clientes.id;
*/




CREATE DATABASE vtaszfs;
USE vtaszfs;


CREATE TABLE Clientes (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  email VARCHAR(100) UNIQUE
);

CREATE TABLE Empleados (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  puesto VARCHAR(50),
  salario DECIMAL(10, 2),
  fecha_contratacion DATE
);

CREATE TABLE Proveedores (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100)

);

CREATE TABLE TiposProductos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  tipo_nombre VARCHAR(100),
  descripcion TEXT,
  tipo_padre_id INT, 
  FOREIGN KEY (tipo_padre_id) REFERENCES TiposProductos(id)
);

CREATE TABLE Productos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  proveedor_id INT,
  tipo_id INT,
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id),
  FOREIGN KEY (tipo_id) REFERENCES TiposProductos(id)
);

CREATE TABLE Pedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  fecha DATE,
  total DECIMAL(10, 2),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);


CREATE TABLE HistorialPedidos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT,
  cliente_id INT,
  fecha_anterior DATE,
  fecha_nueva DATE,
  total_anterior DECIMAL(10,2),
  total_nuevo DECIMAL(10,2),
  fecha_modificacion TIMESTAMP,
  modificado_por VARCHAR(100),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
CREATE TABLE Ubicaciones (
  id INT PRIMARY KEY AUTO_INCREMENT,
  entidad_tipo VARCHAR(50), 
  entidad_id INT,
  direccion VARCHAR(255),
  ciudad VARCHAR(100),
  estado VARCHAR(50),
  codigo_postal VARCHAR(10),
  pais VARCHAR(50)
);
CREATE TABLE ContactoProveedores (
  id INT PRIMARY KEY AUTO_INCREMENT,
  proveedor_id INT,
  nombre_contacto VARCHAR(100),
  telefono VARCHAR(20),
  FOREIGN KEY (proveedor_id) REFERENCES Proveedores(id)
);
CREATE TABLE Telefonos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  cliente_id INT,
  numero_telefono VARCHAR(20),
  tipo VARCHAR(20), 
  FOREIGN KEY (cliente_id) REFERENCES Clientes(id)
);
CREATE TABLE Puestos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre_puesto VARCHAR(50) UNIQUE,
  salario DECIMAL(10, 2)
);

CREATE TABLE DatosEmpleados (
  id INT PRIMARY KEY AUTO_INCREMENT,
  nombre VARCHAR(100),
  puesto_id INT,
  fecha_contratacion DATE,
  FOREIGN KEY (puesto_id) REFERENCES Puestos(id)
);

DROP TABLE Empleados; 
CREATE TABLE PreciosProducto (
  id INT PRIMARY KEY AUTO_INCREMENT,
  producto_id INT,
  precio DECIMAL(10,2),
  fecha_inicio DATE,
  fecha_fin DATE,
  FOREIGN KEY (producto_id) REFERENCES Productos(id)
);

CREATE TABLE DetallesPedido (
  id INT PRIMARY KEY AUTO_INCREMENT,
  pedido_id INT,
  producto_id INT,
  precio_producto_id INT,
  cantidad INT,
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id),
  FOREIGN KEY (producto_id) REFERENCES Productos(id),
  FOREIGN KEY (precio_producto_id) REFERENCES PreciosProducto(id)
);
CREATE TABLE EmpleadosPedidos (
  empleado_id INT,
  pedido_id INT,
  PRIMARY KEY (empleado_id, pedido_id),
  FOREIGN KEY (empleado_id) REFERENCES DatosEmpleados(id),
  FOREIGN KEY (pedido_id) REFERENCES Pedidos(id)
);
INSERT INTO Clientes (nombre, email) VALUES
('Carlos Pérez', 'carlos.perez@email.com'),
('María Gómez', 'maria.gomez@email.com');

INSERT INTO Ubicaciones (entidad_tipo, entidad_id, direccion, ciudad, estado, codigo_postal, pais) VALUES
('cliente', 1, 'Calle 123', 'Caracas', 'Distrito Capital', '1010', 'Venezuela'),
('cliente', 2, 'Av. 456', 'Maracaibo', 'Zulia', '4001', 'Venezuela');

INSERT INTO Telefonos (cliente_id, numero_telefono, tipo) VALUES
(1, '0414-1112233', 'móvil'),
(1, '0212-5553344', 'casa'),
(2, '0424-9998877', 'trabajo');

INSERT INTO Proveedores (nombre) VALUES
('Distribuciones Andina'),
('Importadora Global');

INSERT INTO Ubicaciones (entidad_tipo, entidad_id, direccion, ciudad, estado, codigo_postal, pais) VALUES
('proveedor', 1, 'Zona Industrial 1', 'Caracas', 'Distrito Capital', '1012', 'Venezuela'),
('proveedor', 2, 'Av. Libertador', 'Valencia', 'Carabobo', '2001', 'Venezuela');

INSERT INTO ContactoProveedores (proveedor_id, nombre_contacto, telefono) VALUES
(1, 'Luis Salazar', '0212-1112233'),
(2, 'Ana Torres', '0416-8899221');

INSERT INTO TiposProductos (tipo_nombre, descripcion) VALUES
('Electrónica', 'Dispositivos electrónicos'),
('Alimentos', 'Comida y bebida');

INSERT INTO Productos (nombre, proveedor_id, tipo_id) VALUES
('Laptop Lenovo', 1, 1),
('Galletas Oreo', 2, 2);

INSERT INTO PreciosProducto (producto_id, precio, fecha_inicio, fecha_fin) VALUES
(1, 500.00, '2025-01-01', NULL),
(2, 2.50, '2025-01-01', NULL);

INSERT INTO Pedidos (cliente_id, fecha, total) VALUES
(1, '2025-06-20', 505.00),
(2, '2025-06-21', 5.00);

INSERT INTO DetallesPedido (pedido_id, producto_id, precio_producto_id, cantidad) VALUES
(1, 1, 1, 1),
(2, 2, 2, 2);

INSERT INTO Puestos (nombre_puesto, salario) VALUES
('Vendedor', 450.00),
('Gerente', 1200.00);

INSERT INTO DatosEmpleados (nombre, puesto_id, fecha_contratacion) VALUES
('Juan Rojas', 1, '2024-01-15'),
('Lucía Medina', 2, '2023-09-01');
INSERT INTO EmpleadosPedidos (empleado_id, pedido_id) VALUES
(1, 1),
(2, 2);


ALTER TABLE Pedidos
ADD COLUMN empleado_id INT,
ADD FOREIGN KEY (empleado_id) REFERENCES DatosEmpleados(id);


UPDATE Pedidos SET empleado_id = 1 WHERE id = 1;
UPDATE Pedidos SET empleado_id = 2 WHERE id = 2;


UPDATE PreciosProducto
SET precio = 500.00
WHERE producto_id = (SELECT id FROM Productos WHERE nombre = 'Laptop Lenovo');


UPDATE PreciosProducto
SET precio = 2.50
WHERE producto_id = (SELECT id FROM Productos WHERE nombre = 'Galletas Oreo');



SELECT clientes.nombre, ubicaciones.ciudad
FROM clientes
INNER JOIN ubicaciones 
  ON clientes.id = ubicaciones.entidad_id
WHERE ubicaciones.entidad_tipo = 'cliente'
  AND ubicaciones.ciudad = 'Caracas';

SELECT Pedidos.id AS pedido_id, Clientes.nombre AS cliente_nombre, Pedidos.fecha, Pedidos.total
FROM Pedidos
INNER JOIN Clientes ON Pedidos.cliente_id = Clientes.id;


SELECT Productos.nombre AS producto, Proveedores.nombre AS proveedor
FROM Productos
INNER JOIN Proveedores ON Productos.proveedor_id = Proveedores.id;


SELECT Pedidos.id AS pedido_id, Clientes.nombre, Ubicaciones.direccion, Ubicaciones.ciudad
FROM Pedidos
LEFT JOIN Clientes ON Pedidos.cliente_id = Clientes.id
LEFT JOIN Ubicaciones ON Ubicaciones.entidad_tipo = 'cliente' AND Ubicaciones.entidad_id = Clientes.id;



SELECT DatosEmpleados.nombre, Pedidos.id AS pedido_id, Pedidos.fecha
FROM DatosEmpleados
LEFT JOIN Pedidos ON DatosEmpleados.id = Pedidos.empleado_id;


SELECT TiposProductos.tipo_nombre, Productos.nombre AS producto
FROM Productos
INNER JOIN TiposProductos ON Productos.tipo_id = TiposProductos.id;

SELECT Pedidos.id AS pedido_id, DatosEmpleados.nombre AS empleado_nombre, Pedidos.fecha
FROM Pedidos
INNER JOIN DatosEmpleados ON Pedidos.empleado_id = DatosEmpleados.id;

SELECT Productos.nombre
FROM DetallesPedido
RIGHT JOIN Productos ON DetallesPedido.producto_id = Productos.id
WHERE DetallesPedido.id IS NULL;


SELECT Clientes.nombre, COUNT(Pedidos.id) AS total_pedidos, Ubicaciones.direccion, Ubicaciones.ciudad
FROM Clientes
LEFT JOIN Pedidos ON Clientes.id = Pedidos.cliente_id
LEFT JOIN Ubicaciones ON Ubicaciones.entidad_tipo = 'cliente' AND Ubicaciones.entidad_id = Clientes.id
GROUP BY Clientes.id, Clientes.nombre, Ubicaciones.direccion, Ubicaciones.ciudad;

SELECT pedidos.id AS pedido_id, clientes.nombre AS cliente, pedidos.fecha
FROM pedidos
INNER JOIN clientes ON pedidos.cliente_id = clientes.id;


SELECT productos.nombre AS producto, proveedores.nombre AS proveedor
FROM productos
INNER JOIN proveedores ON productos.proveedor_id = proveedores.id;


SELECT pedidos.id AS pedido_id, clientes.nombre, ubicaciones.ciudad
FROM pedidos
LEFT JOIN clientes ON pedidos.cliente_id = clientes.id
LEFT JOIN ubicaciones 
  ON clientes.id = ubicaciones.entidad_id AND ubicaciones.entidad_tipo = 'cliente';


SELECT datosempleados.nombre AS empleado, pedidos.id AS pedido_id
FROM datosempleados
LEFT JOIN empleadospedidos ON datosempleados.id = empleadospedidos.empleado_id
LEFT JOIN pedidos ON empleadospedidos.pedido_id = pedidos.id;


SELECT productos.nombre AS producto, tiposproductos.tipo_nombre
FROM productos
INNER JOIN tiposproductos ON productos.tipo_id = tiposproductos.id;


SELECT clientes.nombre, COUNT(pedidos.id) AS cantidad_pedidos
FROM clientes
LEFT JOIN pedidos ON clientes.id = pedidos.cliente_id
GROUP BY clientes.id;


SELECT pedidos.id AS pedido_id, datosempleados.nombre AS empleado
FROM pedidos
INNER JOIN empleadospedidos ON pedidos.id = empleadospedidos.pedido_id
INNER JOIN datosempleados ON empleadospedidos.empleado_id = datosempleados.id;

SELECT productos.nombre
FROM productos
LEFT JOIN detallespedido ON productos.id = detallespedido.producto_id
WHERE detallespedido.id IS NULL;


SELECT clientes.id, clientes.nombre, 
       GROUP_CONCAT(DISTINCT ubicaciones.ciudad) AS ciudades, 
       SUM(pedidos.total) AS total_pedidos
FROM clientes
LEFT JOIN ubicaciones
  ON clientes.id = ubicaciones.entidad_id AND ubicaciones.entidad_tipo = 'cliente'
LEFT JOIN pedidos ON clientes.id = pedidos.cliente_id
GROUP BY clientes.id, clientes.nombre;



SELECT productos.nombre AS producto,
       proveedores.nombre AS proveedor,
       tiposproductos.tipo_nombre AS tipo,
       preciosproducto.precio
FROM productos
INNER JOIN proveedores ON productos.proveedor_id = proveedores.id
INNER JOIN tiposproductos ON productos.tipo_id = tiposproductos.id
INNER JOIN preciosproducto ON productos.id = preciosproducto.producto_id;

SELECT pedidos.id AS pedido_id, pedidos.fecha, clientes.nombre AS cliente
FROM pedidos
INNER JOIN clientes ON pedidos.cliente_id = clientes.id;


SELECT pedidos.id AS pedido_id, clientes.nombre AS cliente, ubicaciones.ciudad, ubicaciones.direccion
FROM pedidos
INNER JOIN clientes ON pedidos.cliente_id = clientes.id
LEFT JOIN ubicaciones 
  ON ubicaciones.entidad_tipo = 'cliente' AND ubicaciones.entidad_id = clientes.id;


SELECT productos.nombre AS producto,
       proveedores.nombre AS proveedor,
       tiposproductos.tipo_nombre AS tipo
FROM productos
INNER JOIN proveedores ON productos.proveedor_id = proveedores.id
INNER JOIN tiposproductos ON productos.tipo_id = tiposproductos.id;


SELECT DISTINCT datosempleados.nombre AS empleado, clientes.nombre AS cliente, ubicaciones.ciudad
FROM datosempleados
INNER JOIN empleadospedidos ON datosempleados.id = empleadospedidos.empleado_id
INNER JOIN pedidos ON empleadospedidos.pedido_id = pedidos.id
INNER JOIN clientes ON pedidos.cliente_id = clientes.id
INNER JOIN ubicaciones 
  ON ubicaciones.entidad_tipo = 'cliente' AND ubicaciones.entidad_id = clientes.id
WHERE ubicaciones.ciudad = 'Caracas';


SELECT productos.nombre, SUM(detallespedido.cantidad) AS total_vendido
FROM detallespedido
INNER JOIN productos ON detallespedido.producto_id = productos.id
GROUP BY productos.id, productos.nombre
ORDER BY total_vendido DESC
LIMIT 5;

SELECT clientes.nombre, ubicaciones.ciudad, COUNT(pedidos.id) AS total_pedidos
FROM clientes
LEFT JOIN pedidos ON clientes.id = pedidos.cliente_id
LEFT JOIN ubicaciones 
  ON ubicaciones.entidad_tipo = 'cliente' AND ubicaciones.entidad_id = clientes.id
GROUP BY clientes.id, clientes.nombre, ubicaciones.ciudad;

SELECT DISTINCT c.nombre AS cliente, p.nombre AS proveedor, u1.ciudad
FROM clientes c
INNER JOIN ubicaciones u1 ON u1.entidad_tipo = 'cliente' AND u1.entidad_id = c.id
INNER JOIN ubicaciones u2 ON u2.entidad_tipo = 'proveedor' AND u1.ciudad = u2.ciudad
INNER JOIN proveedores p ON u2.entidad_id = p.id;


SELECT tiposproductos.tipo_nombre, SUM(detallespedido.cantidad * preciosproducto.precio) AS total_ventas
FROM detallespedido
INNER JOIN productos ON detallespedido.producto_id = productos.id
INNER JOIN tiposproductos ON productos.tipo_id = tiposproductos.id
INNER JOIN preciosproducto ON detallespedido.precio_producto_id = preciosproducto.id
GROUP BY tiposproductos.id, tiposproductos.tipo_nombre;


SELECT DISTINCT datosempleados.nombre AS empleado, proveedores.nombre AS proveedor
FROM empleadospedidos
INNER JOIN pedidos ON empleadospedidos.pedido_id = pedidos.id
INNER JOIN detallespedido ON pedidos.id = detallespedido.pedido_id
INNER JOIN productos ON detallespedido.producto_id = productos.id
INNER JOIN proveedores ON productos.proveedor_id = proveedores.id
INNER JOIN datosempleados ON empleadospedidos.empleado_id = datosempleados.id
WHERE proveedores.nombre = 'Distribuciones Andina';


SELECT proveedores.nombre AS proveedor, 
       SUM(detallespedido.cantidad * preciosproducto.precio) AS ingreso_total
FROM detallespedido
INNER JOIN productos ON detallespedido.producto_id = productos.id
INNER JOIN preciosproducto ON detallespedido.precio_producto_id = preciosproducto.id
INNER JOIN proveedores ON productos.proveedor_id = proveedores.id
GROUP BY proveedores.id, proveedores.nombre;



insert into preciosproducto(producto_id, precio, fecha_inicio) values (3, 300.00, '2025-04-03'), (4, 350.00, '2024-08-04'),(5,100.00,'2020-02-14');
/*
SELECT p.nombre AS nombre_producto, pp.precio AS precio, tp.tipo_nombre AS categoria, p.id AS producto_id 
FROM productos p
JOIN preciosproducto pp ON pp.producto_id = p.id
 JOIN tiposproductos tp ON p.tipo_id = tp.id;
 */
SELECT 
    p.id AS producto_id,
    p.nombre AS nombre_producto,
    tp.tipo_nombre AS categoria,
    pp.precio AS precio
FROM 
    productos p
JOIN 
    tiposproductos tp ON tp.id = p.tipo_id 
JOIN 
    preciosproducto pp ON pp.producto_id = p.id
WHERE 
    pp.precio = (
        SELECT MAX(pp2.precio)
        FROM productos p2
        JOIN preciosproducto pp2 ON pp2.producto_id = p2.id
        WHERE p2.tipo_id = p.tipo_id
    )
;

--cliente con mayor total en pedidos
SELECT 
    c.id AS cliente_id,
    c.nombre,
    SUM(p.total) AS total_gastado
FROM 
    clientes c
JOIN 
    pedidos p ON c.id = p.cliente_id
GROUP BY 
    c.id, c.nombre
ORDER BY 
    total_gastado DESC;
LIMIT 1;


select de.id AS emplado_id, de.nombre AS nombre_empleado, p.nombre_puesto AS puesto_empleado 
FROM datosempleados de 
JOIN puestos p ON de.puesto_id = p.id
WHERE 
    p.salario > (
        SELECT AVG(salario) FROM puestos
    );

/*
+---------------+---------------+------+-----+---------+----------------+
| Field         | Type          | Null | Key | Default | Extra          |
+---------------+---------------+------+-----+---------+----------------+
| id            | int           | NO   | PRI | NULL    | auto_increment |
| nombre_puesto | varchar(50)   | YES  | UNI | NULL    |                |
| salario       | decimal(10,2) | YES  |     | NULL    |                |
+---------------+---------------+------+-----+---------+----------------+

+--------------------+--------------+------+-----+---------+----------------+
| Field              | Type         | Null | Key | Default | Extra          |
+--------------------+--------------+------+-----+---------+----------------+
| id                 | int          | NO   | PRI | NULL    | auto_increment |
| nombre             | varchar(100) | YES  |     | NULL    |                |
| puesto_id          | int          | YES  | MUL | NULL    |                |
| fecha_contratacion | date         | YES  |     | NULL    |                |
+--------------------+--------------+------+-----+---------+----------------+
*/


SELECT de.id, de.nombre, p.nombre_puesto, p.salario
FROM datosempleados de
JOIN puestos p ON de.puesto_id = p.id
WHERE p.salario > (SELECT AVG(salario) FROM puestos);




SELECT p.id, p.nombre, SUM(dp.cantidad) AS total_pedida
FROM productos p
JOIN detallespedido dp ON p.id = dp.producto_id
GROUP BY p.id, p.nombre
HAVING total_pedida > 5;

SELECT id, cliente_id, fecha, total, empleado_id
FROM pedidos
WHERE total > (SELECT AVG(total) FROM pedidos);
SELECT pr.id, pr.nombre, COUNT(p.id) AS total_productos
FROM proveedores pr
JOIN productos p ON pr.id = p.proveedor_id
GROUP BY pr.id, pr.nombre
ORDER BY total_productos DESC
LIMIT 3;



SELECT p.id, p.nombre, tp.tipo_nombre, pp.precio
FROM productos p
JOIN tiposproductos tp ON p.tipo_id = tp.id
JOIN preciosproducto pp ON p.id = pp.producto_id
WHERE pp.precio > (
    SELECT AVG(pp2.precio)
    FROM productos p2
    JOIN preciosproducto pp2 ON p2.id = pp2.producto_id
    WHERE p2.tipo_id = p.tipo_id
);



SELECT c.id, c.nombre, COUNT(p.id) AS total_pedidos
FROM clientes c
JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre
HAVING total_pedidos > (
    SELECT AVG(total_pedidos) FROM (
        SELECT COUNT(*) AS total_pedidos
        FROM pedidos
        GROUP BY cliente_id
    ) AS sub
);


SELECT p.id, p.nombre, pp.precio
FROM productos p
JOIN preciosproducto pp ON p.id = pp.producto_id
WHERE pp.precio > (
    SELECT AVG(precio) FROM preciosproducto
);


SELECT de.id, de.nombre, p.nombre_puesto, p.salario
FROM datosempleados de
JOIN puestos p ON de.puesto_id = p.id
WHERE p.salario < (
    SELECT AVG(salario)
    FROM puestos
    WHERE id = p.id
);


/* la parte 6 del pdf */
DELIMITER $$
CREATE PROCEDURE actualizar_precio_por_proveedor(
    IN proveedor INT,
    IN nuevo_precio DECIMAL(10,2)
)
BEGIN
    UPDATE preciosproducto
    SET precio = nuevo_precio
    WHERE producto_id IN (
        SELECT id FROM productos WHERE proveedor_id = proveedor
    );
END$$
DELIMITER ;


DELIMITER $$
CREATE PROCEDURE obtener_direccion_cliente(IN cliente_id INT)
BEGIN
    SELECT direccion, ciudad, estado, codigo_postal, pais
    FROM ubicaciones
    WHERE entidad_tipo = 'cliente' AND entidad_id = cliente_id;
END$$
DELIMITER ;


DELIMITER $$
CREATE PROCEDURE registrar_pedido(
    IN cliente INT,
    IN fecha_pedido DATE,
    IN producto INT,
    IN precio_id INT,
    IN cantidad INT
)
BEGIN
    DECLARE pedido_id INT;
    DECLARE precio_unitario DECIMAL(10,2);

    SELECT precio INTO precio_unitario FROM preciosproducto WHERE id = precio_id;

    INSERT INTO pedidos(cliente_id, fecha, total) 
    VALUES (cliente, fecha_pedido, cantidad * precio_unitario);
    
    SET pedido_id = LAST_INSERT_ID();

    INSERT INTO detallespedido(pedido_id, producto_id, precio_producto_id, cantidad)
    VALUES (pedido_id, producto, precio_id, cantidad);
END$$
DELIMITER ;

DELIMITER $$
CREATE PROCEDURE total_ventas_cliente(IN cliente_id INT)
BEGIN
    SELECT cliente_id, SUM(total) AS total_ventas
    FROM pedidos
    WHERE cliente_id = cliente_id
    GROUP BY cliente_id;
END$$
DELIMITER ;



DELIMITER $$
CREATE PROCEDURE empleados_por_puesto(IN nombre_puesto VARCHAR(50))
BEGIN
    SELECT de.id, de.nombre
    FROM datosempleados de
    JOIN puestos p ON de.puesto_id = p.id
    WHERE p.nombre_puesto = nombre_puesto;
END$$
DELIMITER ;


DELIMITER $$
CREATE PROCEDURE actualizar_salario_por_puesto(
    IN nombre_puesto VARCHAR(50),
    IN nuevo_salario DECIMAL(10,2)
)
BEGIN
    UPDATE puestos
    SET salario = nuevo_salario
    WHERE nombre_puesto = nombre_puesto;
END$$
DELIMITER ;


DELIMITER $$
CREATE PROCEDURE pedidos_entre_fechas(IN fecha_ini DATE, IN fecha_fin DATE)
BEGIN
    SELECT id, cliente_id, fecha, total, empleado_id
    FROM pedidos
    WHERE fecha BETWEEN fecha_ini AND fecha_fin;
END$$
DELIMITER ;


DELIMITER $$
CREATE PROCEDURE aplicar_descuento_categoria(
    IN categoria_id INT,
    IN porcentaje_descuento DECIMAL(5,2)
)
BEGIN
    UPDATE preciosproducto
    SET precio = precio - (precio * (porcentaje_descuento / 100))
    WHERE producto_id IN (
        SELECT id FROM productos WHERE tipo_id = categoria_id
    );
END$$
DELIMITER ;


DELIMITER $$ 
CREATE PROCEDURE proveedores_por_tipo_producto(IN tipo INT)
BEGIN
    SELECT DISTINCT pr.id, pr.nombre
    FROM proveedores pr
    JOIN productos p ON pr.id = p.proveedor_id
    WHERE p.tipo_id = tipo;
END$$
DELIMITER ;

DELIMITER $$
CREATE PROCEDURE pedido_mayor_valor()
BEGIN
    SELECT id, cliente_id, fecha, total, empleado_id
    FROM pedidos
    ORDER BY total DESC
    LIMIT 1;
END$$
DELIMITER ;

