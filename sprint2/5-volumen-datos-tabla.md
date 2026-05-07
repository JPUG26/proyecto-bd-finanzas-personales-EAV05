# 5. Definir volumen de datos por tabla aproximado.

## Volumen de datos de la tabla Usuarios
* ### Campos variables: 
correo, contrasena, estado
* ### Campos fijos: 
  * id_usuario (es de tipo BIGSERIAL de tamaño 8 Bytes), 
  * id_cliente (es de tipo BIGINT de tamaño 8 Bytes), 
  * creado_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes), 
  * actualizado_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes), 
  * correo_verificado (es de tipo BOOLEAN de tamaño 1 byte)
* ### Mapa bits
Como el campo id_cliente (llave foránea) permite valores nulos inicialmente se utiliza 1 byte para gestionar valores nulos.
* ### Tamaño estimado usado de campos variables
  * Campo correo tamaño estimado: 40 bytes
  * Campo contraseña tamaño estimado: 60 bytes (definición por la longitud de un hash)
  * Campo estado tamaño estimado: 22 bytes (longitud de máximo valor aceptado 'PENDIENTE_VERIFICACION')
 
### Calculo de la longitud estimada del registro
L = (4x3) + (8+8+8+8+1) + 1 + (40 + 60 + 22) = 168 bytes

## * Volumen de datos de la tabla Clientes
## * Volumen de datos de la tabla Códigos de Verificación
## * Volumen de datos de la tabla Categorías
## * Volumen de datos de la tabla transacciones
