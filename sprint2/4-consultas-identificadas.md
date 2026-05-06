# 4. Crear las consultas identificadas o acciones del módulo para las HU correspondientes al Sprint 1 y 2 vs modelo físico.
A continuación se presentan las diferentes consultas identificadas del módulo en cada uno de los sprint:

## HU: Resgistrar Usuario

* Crear el perfil del cliente
```sql
INSERT INTO clientes (nombre, correo_contacto, imagen_perfil, descripcion) 
VALUES ('Nombre del Usuario', 'usuario@email.com', 'https://ejemplo.com/fotos/perfil_usuario.png', 'Usuario que quiere tener mayor control de sus finanzas' )
```
* Crear el usuario vinculado al cliente
```sql
INSERT INTO usuarios (correo, contrasena, estado, id_cliente, correo_verificado) 
VALUES ('usuario@email.com', '$2a$10$enc_password_hash', 'PENDIENTE_VERIFICACION', 1, FALSE);
```

## HU: Iniciar Sesión
```sql
SELECT id_usuario, correo, contrasena, estado, correo_verificado, id_cliente 
FROM usuarios 
WHERE correo = 'usuario@email.com' 
AND estado != 'BLOQUEADO';
```

## HU: Registrar Ingreso
```sql
INSERT INTO transacciones (nombre, monto, movimiento_en, tipo, id_categoria, id_cliente) 
VALUES ('Salario', 2500000, CURRENT_TIMESTAMP, 'INGRESO', 1, 1);
```

## HU: Registrar Gasto
```sql
INSERT INTO transacciones (nombre, monto, movimiento_en, tipo, id_categoria, id_cliente) 
VALUES ('Compra de Almuerzo', 15000, CURRENT_TIMESTAMP, 'GASTO', 2, 1);
```

## HU: Visualizar Historial
```sql
SELECT t.id_transaccion, t.nombre, t.monto, t.movimiento_en, t.tipo, c.nombre AS categoria
FROM transacciones t
JOIN categorias c ON t.id_categoria = c.id_categoria
WHERE t.id_cliente = 1 
ORDER BY t.movimiento_en DESC
LIMIT 20; 
```
