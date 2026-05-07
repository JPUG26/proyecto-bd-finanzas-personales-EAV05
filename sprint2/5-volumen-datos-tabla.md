# 5. Definir volumen de datos por tabla aproximado.

## Volumen de datos de la tabla Usuarios
* ### Campos variables: 
correo, contrasena, estado
* ### Campos fijos: 
  * id_usuario (es de tipo BIGSERIAL de tamaño 8 Bytes) 
  * id_cliente (es de tipo BIGINT de tamaño 8 Bytes)
  * creado_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes)
  * actualizado_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes) 
  * correo_verificado (es de tipo BOOLEAN de tamaño 1 byte)
* ### Mapa bits
Como el campo id_cliente (llave foránea) permite valores nulos inicialmente se utiliza 1 byte para gestionar valores nulos.
* ### Tamaño estimado usado de campos variables
  * Campo correo con tamaño estimado: 40 bytes
  * Campo contraseña con tamaño estimado: 60 bytes (definición por la longitud de un hash)
  * Campo estado con tamaño estimado: 22 bytes (longitud de máximo valor aceptado 'PENDIENTE_VERIFICACION')
 
### Calculo de la longitud estimada del registro
L = (4x3) + (8+8+8+8+1) + 1 + (40 + 60 + 22) = 168 bytes

## * Volumen de datos de la tabla Clientes
* ### Campos variables: 
nombre, correo_contacto, imagen_perfil, descripcion
* ### Campos fijos: 
  * id_cliente (es de tipo BIGINT de tamaño 8 Bytes) 
  * creado_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes)
* ### Mapa bits
Como los campos imagen_perfil, descripcion y correo_contacto permiten valores nulos se utiliza 1 byte para gestionar valores nulos.
* ### Tamaño estimado usado de campos variables
  * Campo nombre con tamaño estimado: 40 bytes
  * Campo correo con tamaño estimado: 40 bytes 
  * Campo imagen con tamaño estimado: 150 bytes (longitud estimada de la URL donde se almacenerá la imagen)
  * Campo descripcion con tamaño estimado: 200 bytes 
 
### Calculo de la longitud estimada del registro
L = (4x4) + (8+8) + 1 + (40 + 40 + 150 + 200) = 463 bytes

## * Volumen de datos de la tabla Códigos de Verificación
* ### Campos variables: 
codigo
* ### Campos fijos: 
  * id_codigo (es de tipo BIGSERIAL de tamaño 8 Bytes)
  * id_usuario (es de tipo BIGINT de tamaño 8 Bytes)
  * expira_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes)
  * creado_en (es de tipo TIMESTAMPTZ de tamaño 8 bytes)
  * boolean (es de tipo BOOLEAN de tamaño 1 byte)  
* ### Mapa bits
Como el campo usado permite valores nulos se utiliza 1 byte para gestionar valores nulos.
* ### Tamaño estimado usado de campos variables
  * Campo codigo tamaño estimado: 6 bytes
  * 
### Calculo de la longitud estimada del registro
L = (4x1) + (8+8+8+8+1) + 1 + 6 =  44 bytes

## * Volumen de datos de la tabla Categorías
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

## * Volumen de datos de la tabla transacciones
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
