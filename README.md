# ACT-mysql

-- 1. Tabla paÃ­ses
CREATE TABLE paises (
    id INT AUTO_INCREMENT PRIMARY KEY,
    pais_name VARCHAR(100)
);

-- 2. Tabla estados
CREATE TABLE estados (
    id INT AUTO_INCREMENT PRIMARY KEY,
    estado_name VARCHAR(100),
    pais_id INT,
    CONSTRAINT fk_estado_pais FOREIGN KEY (pais_id) REFERENCES paises(id)
);

-- 3. Tabla ciudades
CREATE TABLE ciudades (
    id INT AUTO_INCREMENT PRIMARY KEY,
    ciudad_name VARCHAR(100),
    codigo_postal VARCHAR(50),
    estado_id INT,
    CONSTRAINT fk_ciudad_estado FOREIGN KEY (estado_id) REFERENCES estados(id)
);

-- 4. Tabla ubicacion
CREATE TABLE ubicacion (
    id INT AUTO_INCREMENT PRIMARY KEY,
    calle_info VARCHAR(100),
    carrera VARCHAR(50),
    calle VARCHAR(50),
    description TEXT,
    id_ciudad INT,
    CONSTRAINT fk_ubicacion_ciudad FOREIGN KEY (id_ciudad) REFERENCES ciudades(id)
);

-- 5. Tabla clientes
CREATE TABLE clientes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    email VARCHAR(100) UNIQUE,
    ubicacion_id INT,
    CONSTRAINT fk_cliente_ubicacion FOREIGN KEY (ubicacion_id) REFERENCES ubicacion(id)
);

-- 6. Tabla telefonos
CREATE TABLE telefonos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    telefono VARCHAR(50) UNIQUE,
    descripcion TEXT
);

-- 7. Tabla clientes_telefonos
CREATE TABLE clientes_telefonos (
    clientes_id INT,
    telefonos_id INT,
    PRIMARY KEY (clientes_id, telefonos_id),
    CONSTRAINT fk_ct_cliente FOREIGN KEY (clientes_id) REFERENCES clientes(id),
    CONSTRAINT fk_ct_telefono FOREIGN KEY (telefonos_id) REFERENCES telefonos(id)
);

-- 8. Tabla tiposproducto
CREATE TABLE tiposproducto (
    id INT AUTO_INCREMENT PRIMARY KEY,
    tipo_nombre VARCHAR(100),
    descripcion TEXT
);

-- 9. Tabla productos
CREATE TABLE productos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255),
    precio DECIMAL(10, 2),
    tipo_id INT,
    CONSTRAINT fk_producto_tipo FOREIGN KEY (tipo_id) REFERENCES tiposproducto(id)
);

-- 10. Tabla proveedores
CREATE TABLE proveedores (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    telefono VARCHAR(20),
    direccion VARCHAR(255)
);

-- 11. Tabla productos_proveedores
CREATE TABLE productos_proveedores (
    proveedor_id INT,
    producto_id INT,
    PRIMARY KEY (proveedor_id, producto_id),
    CONSTRAINT fk_pp_proveedor FOREIGN KEY (proveedor_id) REFERENCES proveedores(id),
    CONSTRAINT fk_pp_producto FOREIGN KEY (producto_id) REFERENCES productos(id)
);

-- 12. Tabla tipo_puestos
CREATE TABLE tipo_puestos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    puesto VARCHAR(100),
    descripcion TEXT
);

-- 13. Tabla empleados
CREATE TABLE empleados (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100),
    puesto_id INT,
    salario DECIMAL(10, 2),
    fecha_contratacion DATE,
    CONSTRAINT fk_empleado_puesto FOREIGN KEY (puesto_id) REFERENCES tipo_puestos(id)
);

-- 14. Tabla proveedores_empleados
CREATE TABLE proveedores_empleados (
    proveedores_id INT,
    empleados_id INT,
    PRIMARY KEY (proveedores_id, empleados_id),
    CONSTRAINT fk_pe_proveedor FOREIGN KEY (proveedores_id) REFERENCES proveedores(id),
    CONSTRAINT fk_pe_empleado FOREIGN KEY (empleados_id) REFERENCES empleados(id)
);

-- 15. Tabla estado_pedidos
CREATE TABLE estado_pedidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    detalle VARCHAR(100)
);

-- 16. Tabla pedidos
CREATE TABLE pedidos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    cliente_id INT,
    producto_id INT,
    estado_id INT,
    precio DECIMAL(10,2),
    fecha DATE,
    CONSTRAINT fk_pedido_cliente FOREIGN KEY (cliente_id) REFERENCES clientes(id),
    CONSTRAINT fk_pedido_producto FOREIGN KEY (producto_id) REFERENCES productos(id),
    CONSTRAINT fk_pedido_estado FOREIGN KEY (estado_id) REFERENCES estado_pedidos(id)
);

-- 17. Tabla detalles_pedido
CREATE TABLE detalles_pedido (
    pedido_id INT,
    producto_id INT,
    cantidad INT,
    precio DECIMAL(10, 2),
    PRIMARY KEY (pedido_id, producto_id),
    CONSTRAINT fk_dp_pedido FOREIGN KEY (pedido_id) REFERENCES pedidos(id),
    CONSTRAINT fk_dp_producto FOREIGN KEY (producto_id) REFERENCES productos(id)
);

-- 18. Tabla historial
CREATE TABLE historial (
    id INT AUTO_INCREMENT PRIMARY KEY,
    pedido_id INT,
    cliente_id INT,
    fechapedido DATE,
    fechacierre DATE,
    estado_id INT,
    CONSTRAINT fk_historial_pedido FOREIGN KEY (pedido_id) REFERENCES pedidos(id),
    CONSTRAINT fk_historial_cliente FOREIGN KEY (cliente_id) REFERENCES clientes(id),
    CONSTRAINT fk_historial_estado FOREIGN KEY (estado_id) REFERENCES estado_pedidos(id)
);

-- 19. Tabla detalles_historial
CREATE TABLE detalles_historial (
    historial_id INT,
    producto_id INT,
    cantidad INT,
    precio DECIMAL(10, 2),
    PRIMARY KEY (historial_id, producto_id),
    CONSTRAINT fk_dh_historial FOREIGN KEY (historial_id) REFERENCES historial(id),
    CONSTRAINT fk_dh_producto FOREIGN KEY (producto_id) REFERENCES productos(id)
);


---------------------------------------------
## JOINS

--Obtener la lista de todos los pedidos con los nombres de clientes usando INNER JOIN .
SELECT pedidos.id, clientes.nombre
FROM pedidos 
INNER JOIN clientes ON pedidos.cliente_id = clientes.id
;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386219954839486474/image.png?ex=6859922f&is=685840af&hm=a41f3d2cc6198b06fae3f77ded46e3074a83582fb462af8a3ba23b5079bc53be&)

--Listar los productos y proveedores que los suministran con INNER JOIN .
SELECT productos.id, productos.nombre, proveedores.nombre
FROM productos 
INNER JOIN proveedores ON productos.proveedor_id = proveedores.id
;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386219954839486474/image.png?ex=6859922f&is=685840af&hm=a41f3d2cc6198b06fae3f77ded46e3074a83582fb462af8a3ba23b5079bc53be&=&format=webp&quality=lossless)

ALTER TABLE productos
ADD COLUMN proveedor_id INT AFTER precio;

ALTER TABLE productos
ADD CONSTRAINT fk_po_id FOREIGN KEY (proveedor_id) REFERENCES proveedores(id);

--Mostrar los pedidos y las ubicaciones de los clientes con LEFT JOIN .
SELECT pedidos.id, clientes.nombre, ubicacion.calle_info, ubicacion.carrera, ubicacion.calle, ubicacion.description
FROM pedidos
LEFT JOIN clientes ON pedidos.cliente_id = clientes.id
LEFT JOIN ubicacion ON clientes.ubicacion_id = ubicacion.id;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386222452014714900/image.png?ex=68599483&is=68584303&hm=22ee05dde2c3fb78de09e6e120fc182de49f356bfa9d71e16b24c3ad667e2217&=&format=webp&quality=lossless)

ALTER TABLE pedidos
ADD COLUMN empleado_id INT AFTER cliente_id;

ALTER TABLE pedidos
ADD CONSTRAINT fk_empleado_id FOREIGN KEY (empleado_id) REFERENCES empleados(id);

--Consultar los empleados que han registrado pedidos, incluyendo empleados sin pedidos
( LEFT JOIN ).
SELECT pedidos.id, pedidos.empleado_id, empleados.nombre
FROM pedidos
LEFT JOIN empleados ON pedidos.empleado_id = empleados.id;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386225903515730012/image.png?ex=685997ba&is=6858463a&hm=c7320848d5d338009c54639c3d033467f64027783530098c07b55680e648cfdb&=&format=webp&quality=lossless)

--Obtener el tipo de producto y los productos asociados con INNER JOIN .
SELECT productos.id, productos.nombre, tiposproducto.tipo_nombre
FROM productos 
INNER JOIN tiposproducto ON productos.tipo_id = tiposproducto.id;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386229449841840261/image.png?ex=68599b07&is=68584987&hm=d39ee8b631e087cbd69bddbaeb3682f3d1015cd3d5606ebf077895abf904226a&=&format=webp&quality=lossless)

INSERT INTO clientes (nombre, email, ubicacion_id) VALUES

INSERT INTO pedidos (cliente_id, empleado_id, producto_id, precio, fecha) VALUES

--Listar todos los clientes y el número de pedidos realizados con COUNT y GROUP BY .
SELECT nombre, COUNT(pedidos.id) AS pedidos_realizados 
FROM clientes 
LEFT JOIN pedidos ON pedidos.cliente_id = clientes.id 
GROUP BY nombre;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386253347119431751/image.png?ex=6859b149&is=68585fc9&hm=cb50d78e65b2356a98a971fb30232eecd3adada06ad247f0ee3957fab500d5b1&=&format=webp&quality=lossless)

--Combinar Pedidos y Empleados para mostrar qué empleados gestionaron pedidos
específicos.
SELECT pedidos.id, empleados.nombre
FROM pedidos
INNER JOIN empleados ON pedidos.empleado_id = empleados.id;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386257840808919060/image.png?ex=6859b578&is=685863f8&hm=f6c61d198213dafea3d73e9a5f6f28cf6f119793840505f5821856a9e5aa9f04&=&format=webp&quality=lossless)

--Mostrar productos que no han sido pedidos ( RIGHT JOIN ).
SELECT productos.id, productos.nombre, productos.precio
FROM detalles_pedido
RIGHT JOIN productos ON detalles_pedido.producto_id = productos.id
WHERE detalles_pedido.pedido_id IS NULL;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386260156270252122/image.png?ex=6859b7a0&is=68586620&hm=a7f8f0096d40863cac73cba66cf5917b06c1a207c6a038fbbb549fa2151b7361&=&format=webp&quality=lossless)

--Mostrar el total de pedidos y ubicación de clientes usando múltiples JOIN .
SELECT c.id AS cliente_id, c.nombre AS cliente_nombre, COUNT(p.id) AS total_pedidos, u.calle_info, u.carrera, u.calle, ci.ciudad_name, es.estado_name, pa.pais_name
FROM clientes c
LEFT JOIN pedidos p ON c.id = p.cliente_id
JOIN ubicacion u ON c.ubicacion_id = u.id
JOIN ciudades ci ON u.id_ciudad = ci.id
JOIN estados es ON ci.estado_id = es.id
JOIN paises pa ON es.pais_id = pa.id
GROUP BY c.id, c.nombre, u.calle_info, u.carrera, u.calle, ci.ciudad_name, es.estado_name, pa.pais_name;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386260989569138758/image.png?ex=6859b867&is=685866e7&hm=933a115dcf5a87d9c7850b1702ee90b4446bdb074b969b119e93b81be7857f7a&=&format=webp&quality=lossless&width=877&height=228)

--Unir Proveedores , Productos , y TiposProductos para un listado completo de inventario.
SELECT 
    productos.id AS producto_id,
    productos.nombre AS producto_nombre,
    productos.precio,
    tiposproducto.tipo_nombre AS tipo_producto,
    proveedores.nombre AS proveedor_nombre,
    proveedores.telefono,
    proveedores.direccion
FROM productos
JOIN proveedores ON productos.proveedor_id = proveedores.id
JOIN tiposproducto ON productos.tipo_id = tiposproducto.id
ORDER BY proveedores.nombre, productos.nombre;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386552284552368218/image.png?ex=685a1ef1&is=6858cd71&hm=5c80edcc3c42da29c9e221203d1dbe8cf8ba8b3615f8d6fef341104a1f85aaca&=&format=webp&quality=lossless&width=877&height=342)

----------------------------------------------------------
--CONSULTAS SIMPLES
--Seleccionar todos los productos con precio mayor a $50.
SELECT id, nombre, precio FROM productos WHERE precio < 50.00;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386553879965073558/image.png?ex=685a206d&is=6858ceed&hm=dfdd60d19d34ce6f5ee8e099dde667e53e4aefa95dfefb3c99dd851a2162eb32&=&format=webp&quality=lossless)

--Consultar clientes registrados en una ciudad específica.
SELECT clientes.id, clientes.nombre, ciudades.ciudad_name
FROM clientes
INNER JOIN ubicacion ON clientes.ubicacion_id = ubicacion.id
INNER JOIN ciudades ON ubicacion.id_ciudad = ciudades.id;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386579645591191675/image.png?ex=685a386c&is=6858e6ec&hm=eee1cba7d8a031adb5ab9dff6628e7340d74c2d7167c6fb00f3cfddb2668b299&=&format=webp&quality=lossless)

--Mostrar empleados contratados en los últimos 2 años.
SELECT id, nombre, fecha_contratacion FROM empleados WHERE fecha_contratacion > 2023-02-11

![image](https://media.discordapp.net/attachments/1337463162940817490/1386582024806203482/image.png?ex=685a3aa4&is=6858e924&hm=51a67dede8c82cd6fa80c67e5a7b85ab59636e70a48e1d43f50c888d8599d625&=&format=webp&quality=lossless&width=877&height=154)

--Seleccionar proveedores que suministran más de 5 productos.
INSERT INTO productos (nombre, precio, proveedor_id, tipo_id) VALUES ('Horno Eléctrico Oster', 450.00, 1, 1;

INSERT INTO productos (nombre, precio, proveedor_id, tipo_id) VALUES ('Aspiradora LG Compacta', 570.00, 1, 3), ('Zapatos Deportivos Nike', 320.00, 2, 2);
INSERT INTO productos (nombre, precio, proveedor_id, tipo_id) VALUES ('Silla de Oficina Ergonómica', 890.00, 1, 3), ('Audífonos Bluetooth JBL', 210.00, 1, 1);

SELECT pr.id AS proveedor_id, pr.nombre AS proveedor_nombre, COUNT(p.id) AS total_productos 
FROM proveedores pr 
JOIN productos p ON p.proveedor_id = pr.id 
GROUP BY pr.id, pr.nombre 
HAVING COUNT(p.id) > 5;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386586849480282253/image.png?ex=685a3f22&is=6858eda2&hm=9a845c04c0742ffcdb713b9d832d683fb9fdd387c086de5989f856fb89941c9d&=&format=webp&quality=lossless)

--Listar clientes que no tienen dirección registrada en UbicacionCliente.
INSERT INTO clientes (nombre, email) VALUES
('Carlos López', 'carlos.lopez@example.com'), ('Mariana Díaz', 'mariana.diaz@example.com'), ('Juan Ortega', 'juan.ortega@example.com'), ('Valentina Torres', 'valentina.torres@example.com');

SELECT id, nombre, email, ubicacion_id 
FROM clientes 
WHERE ubicacion_id IS NULL;

![image](https://media.discordapp.net/attachments/1337463162940817490/1386591329819164732/image.png?ex=685a434e&is=6858f1ce&hm=0ca5638cf0c41958b1dff6f0ae4910d2e8d52d5fa6c6ac327b3c3c35a868a794&=&format=webp&quality=lossless)

--Calcular el total de ventas por cada cliente.
SELECT c.id AS cliente_id, c.nombre AS cliente_nombre, SUM(p.precio) AS total_ventas
FROM clientes c
LEFT JOIN pedidos p ON c.id = p.cliente_id
GROUP BY c.id, c.nombre;

![image](https://github.com/user-attachments/assets/2d771fad-2914-4d0a-a1cf-73b8231583f9)


--Mostrar el salario promedio de los empleados.
SELECT AVG(salario) AS salario_promedio
FROM empleados
;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386601581440405657/image.png?ex=685a4cda&is=6858fb5a&hm=a92e255dab8c93e71cb6ee9c763e4fd7b754ff4a97604a0dd6d1f84213a524b5&)

--Consultar el tipo de productos disponibles en TiposProductos
SELECT id, tipo_nombre AS tipo FROM tiposproducto;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386602243188326461/image.png?ex=685a4d78&is=6858fbf8&hm=68da0e59a70d7595a4efaa6ce36c3e512437856bd4c806f53236b8a67ce68625&)

--Seleccionar los 3 productos más caros
SELECT id, nombre, precio
FROM productos
ORDER BY precio DESC
LIMIT 3;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386603956238680074/image.png?ex=685a4f11&is=6858fd91&hm=ec7d0e04f26a46251cbdbc5a88df5d317773c70c7415cea6040c47d87186b8d0&)

--------------------------------------------------------
## CONSULTAS MULTITABLA
--Listar todos los pedidos y el cliente asociado.
SELECT pedidos.id AS pedido_id, clientes.nombre AS cliente_nombre, pedidos.precio, pedidos.fecha AS fecha_pedido
FROM pedidos
JOIN clientes ON pedidos.cliente_id = clientes.id;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386610873065541662/image.png?ex=685a5582&is=68590402&hm=136a0bf905e1bf5fd926e3e0b6406892e3b0b49c0cf5bfbfce33e29711a4050f&)

--mostrar la ubicacion de cada cliente en sus pedidos
SELECT p.id AS pedido_id, c.nombre AS cliente_nombre, u.calle_info, u.carrera, u.calle, ci.ciudad_name, es.estado_name, pa.pais_name
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
JOIN ubicacion u ON c.ubicacion_id = u.id
JOIN ciudades ci ON u.id_ciudad = ci.id
JOIN estados es ON ci.estado_id = es.id
JOIN paises pa ON es.pais_id = pa.id;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386613121782644869/image.png?ex=685a579a&is=6859061a&hm=d5c92a4d8c8c3c370aed1c3096889aefdf023d0b869c743ac7b89a8e9ce8d74f&)

--Listar productos junto con el proveedor y tipo de producto
SELECT p.nombre AS producto, pr.nombre AS proveedor, tp.tipo_nombre AS tipo_producto, p.precio
FROM productos p
JOIN proveedores pr ON p.proveedor_id = pr.id
JOIN tiposproducto tp ON p.tipo_id = tp.id;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386617864894681289/image.png?ex=685a5c05&is=68590a85&hm=7ead87fac2df3569b5ec5a4d8ecc0dd3dff71d20976b834ff0a91254ebb04413&)

--Consultar todos los empleados que gestionan pedidos de clientes en una ciudad específica
SELECT DISTINCT e.id AS empleado_id, e.nombre AS empleado_nombre, ci.ciudad_name
FROM empleados e
JOIN pedidos p ON p.empleado_id = e.id
JOIN clientes c ON p.cliente_id = c.id
JOIN ubicacion u ON c.ubicacion_id = u.id
JOIN ciudades ci ON u.id_ciudad = ci.id
WHERE ci.ciudad_name = 'Sincelejo';

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386618001771724941/image.png?ex=685a5c25&is=68590aa5&hm=c2d47d7cc6fd5cb3488f620cfaf6e5b92805d7fd41905621208001a41431172f&)

--Consultar los 5 productos más vendidos
SELECT p.nombre AS producto, SUM(dp.cantidad) AS total_vendidos
FROM detalles_pedido dp
JOIN productos p ON dp.producto_id = p.id
GROUP BY p.id, p.nombre
ORDER BY total_vendidos DESC LIMIT 5;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386618148815638608/image.png?ex=685a5c48&is=68590ac8&hm=7679213138b8721a087650d82d9eb132da7561bb8061805e18bf6d3d764c6fec&)

--Obtener la cantidad total de pedidos por cliente y ciudad
SELECT c.nombre AS cliente, ci.ciudad_name, COUNT(p.id) AS total_pedidos
FROM pedidos p
JOIN clientes c ON p.cliente_id = c.id
JOIN ubicacion u ON c.ubicacion_id = u.id
JOIN ciudades ci ON u.id_ciudad = ci.id
GROUP BY c.id, c.nombre, ci.ciudad_name;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386618223784366142/image.png?ex=685a5c5a&is=68590ada&hm=ff568e5996e321b8ec09bdcd7afb0a42e98e126f7964c8a52b9c655d9543f634&)

--Listar clientes y proveedores en la misma ciudad

ALTER TABLE proveedores
DROP COLUMN direccion;

ALTER TABLE proveedores
ADD COLUMN ubicacion_id INT;

ALTER TABLE proveedores
ADD CONSTRAINT fk_ubi_id FOREIGN KEY (ubicacion_id) REFERENCES ubicacion(id);

UPDATE proveedores SET ubicacion_id = 1 WHERE id = 1;

UPDATE proveedores SET ubicacion_id = 2 WHERE id = 2;

SELECT c.nombre AS cliente, pr.nombre AS proveedor,  ci.ciudad_name
FROM clientes c
JOIN ubicacion uc ON c.ubicacion_id = uc.id
JOIN ciudades ci ON uc.id_ciudad = ci.id
JOIN ubicacion ub ON ub.id_ciudad = ci.id
JOIN proveedores pr ON pr.ubicacion_id = ub.id;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386618868977504316/image.png?ex=685a5cf4&is=68590b74&hm=073e4af1997d8186b688733c6dc90b044152756fe5a41317b6ce41ed97fa0478&)

--Mostrar el total de ventas agrupado por tipo de producto
SELECT tp.tipo_nombre, SUM(p.precio) AS total_ventas
FROM pedidos p
JOIN productos pr ON p.producto_id = pr.id
JOIN tiposproducto tp ON pr.tipo_id = tp.id
GROUP BY tp.tipo_nombre;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386620211876335688/image.png?ex=685a5e34&is=68590cb4&hm=f8e0d8e88169d3522f70c1b0ecb29e71c63cf11e0cc0d244186da0e837c78976&)

--Listar empleados que gestionan pedidos de productos de un proveedor específico
SELECT DISTINCT e.id AS empleado_id, e.nombre AS empleado_nombre, prv.nombre AS proveedor_nombre
FROM empleados e
JOIN pedidos p ON p.empleado_id = e.id
JOIN productos pr ON p.producto_id = pr.id
JOIN proveedores prv ON pr.proveedor_id = prv.id
WHERE prv.nombre = 'TechDistribuidora';

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386620929647317042/image.png?ex=685a5edf&is=68590d5f&hm=4dc4adbcdb1de406a45b87740ffc54ebedf8d7cce2859bcef812b4e27ccdf39c&)

--Obtener el ingreso total de cada proveedor a partir de los productos vendidos
SELECT prv.nombre AS proveedor, SUM(dp.precio * dp.cantidad) AS ingreso_total
FROM productos pr
JOIN proveedores prv ON pr.proveedor_id = prv.id
JOIN detalles_pedido dp ON pr.id = dp.producto_id
GROUP BY prv.id, prv.nombre;

![image](https://cdn.discordapp.com/attachments/1337463162940817490/1386621298624696360/image.png?ex=685a5f37&is=68590db7&hm=e8428a2431eadecbe6d0c68c68b423920362c1add258045bd8b1096c27998fb8&)


