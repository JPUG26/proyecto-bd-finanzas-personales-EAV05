# Definición de Entidades para la Aplicación de Gestión Financiera

## Entidad #1: Usuario
**Definición y necesidad de creación de la entidad:** es la entidad encargada de gestionar el correo de inicio de sesión, la contraseña encriptada y el estado de activación de la cuenta. 
Su creación es indispensable para autenticar a los usuarios de forma segura,  permite que el sistema de seguridad sea independiente de los datos personales, protegiendo las credenciales de posibles filtraciones. 

## Entidad #2: Cliente
**Definición y necesidad de creación de la entidad:** esta entidad almacena la información que identifica a la persona dentro de la aplicación, contiene información descriptiva, 
el nombre para la generación de reportes y el correo de contacto (que puede ser distinto al de inicio de sesión). Es necesaria para almacenar la información descriptiva y de contacto de la persona que se 
registra en la aplicación.

## Entidad #3: Código de Verificación
**Definición y necesidad de creación de la entidad:** esta entidad permite gestionar y almacenar de forma segura los códigos numéricos temporales de un solo uso generados por el sistema. 
Facilita la validación de la identidad del usuario en procesos críticos (como la verificación de correo o la recuperación de contraseñas), garantizando que cada código tenga una ventana de tiempo de validez 
estricta y controlando que no pueda ser reutilizado una vez consumido. Es importante crear esta entidad para desacoplar la lógica de validación temporal de la entidad principal de seguridad que es la entidad usuarios. 
Su creación responde a la necesidad de gestionar múltiples flujos de autorización (como validación de correo, recuperación de contraseña o autenticación de dos factores) sin alterar la estructura de la cuenta del usuario. 

## Entidad #4: Categoría
**Definición y necesidad de creación de la entidad:** esta entidad permite la creación de etiquetas o agrupaciones temáticas (ej. Alimentación, Transporte, Salario, Entretenimiento) que sirven para clasificar el origen 
de un ingreso o el destino de un gasto del cliente. Dentro de las reglas del negocio se exige la clasificación de transacciones por categorías, por lo que esta entidad es vital para poder agrupar, filtrar y mostrar 
al cliente cuales son los tipos de ingresos y gastos en donde se encuentra su dinero.

## Entidad #5: Transacción
**Definición y necesidad de creación de la entidad:** esta entidad está encargada de registrar de forma cronológica cada movimiento financiero sea gasto o ingreso realizado por el cliente. 
Permite vincular un monto monetario con una categoría de clasificación al cliente que realiza el registro del movimiento en su cuenta. La creación de esta entidad responde directamente a la regla de negocio 
“Registro de ingresos y gastos personales”, esta entidad es indispensable para la generación de reportes relacionados con gastos, ingresos y presupuesto del cliente y permite junto con la entidad Categoría 
definir reportes personalizados por cada uno de los conceptos creados por el cliente asociados al registro de sus movimientos. Es importante considerar el tipo de transacciones que puede registrar el cliente, 
estas pueden ser ingresos (por ejemplo: salario mensual, honorarios por trabajos extra, rendimientos de inversiones, regalos, etc) y/o gastos (por ejemplo: pago de arriendo, mercado, servicios públicos, entretenimiento, 
donación, etc).

## DICCIONARIO DE DATOS

## Definición de abreviaturas para las restricciones manejadas en el diccionario de datos

* **PK:** Primary key (Clave primaria)
* **FK:** Foreign key (Clave foránea)
* **NN:** Not null (No nulo)
* **UQ:** Unique (Único)
* **AI:** Auto increment (Autoincremental)
* **DF:** Default (Por defecto)

### Entidad Usuario

| Campo | Tipo de dato | Restricciones | Comentario |
| :--- | :--- | :--- | :--- |
| id_usuario | BIGSERIAL | PK, AI, NN | identificador único autoincrementable. |
| correo | VARCHAR(150) | UQ, NN | Correo de inicio de sesión. Es la principal credencial de acceso. |
| contrasena | VARCHAR(255) | NN | Almacena la contraseña del usuario encriptada. |
| estado | VARCHAR(30) | NN, CHECK | valores que se aceptan: 'PENDIENTE', 'ACTIVO', 'INACTIVO', 'BLOQUEADO'. |
| creado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha de registro. No cambia tras la inserción. |
| actualizado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha de último cambio del perfil del usuario o de su contraseña. |
| id_cliente | BIGINT | FK, UQ | Llave foránea que asocia las credenciales de seguridad (entidad Usuario) con los datos de perfil de la persona (entidad Cliente). Inicialmente puede ser NULL. |

### Entidad Cliente

| Campo | Tipo de dato | Restricciones | Comentario |
| :--- | :--- | :--- | :--- |
| id_cliente | BIGSERIAL | PK, AI, NN | Identificador único autoincrementable |
| nombre | VARCHAR(150) | NN | Nombre completo del cliente |
| correo_contacto | VARCHAR(150) | UQ | El sistema no debe permitir que dos clientes compartan el mismo correo de contacto. La función de este correo es de notificación y contacto, no de autenticación. |
| imagen_perfil | VARCHAR(500) | | El campo no almacena la imagen sino la URL donde estará alojada la imagen |
| creado_en | TIMESTAMPTZ | NN, DF (NOW()) | Fecha de creación del cliente. No cambia tras la inserción. |
| actualizado_en | TIMESTAMPTZ | NN, DF (NOW()) | Fecha de último cambio del perfil del cliente. |
| descripcion | TEXT | | Este campo es útil para que el cliente describa su perfil financiero o sus objetivos con el uso de la aplicación. |

### Entidad Código de Verificación

| Campo | Tipo de dato | Restricciones | Comentario |
| :--- | :--- | :--- | :--- |
| id_codigo | BIGSERIAL | PK, AI, NN | Identificador único autoincrementable. |
| codigo | VARCHAR(10) | NN | Se almacena como texto para soportar ceros a la izquierda |
| tipo | VARCHAR(30) | NN, CHECK | Valores que se aceptan: 'Verificacion correo', 'Recuperacion_contrasena', 'Dos_factores' |
| expira_en | TIMESTAMPTZ | NN | Momento exacto de caducidad. |
| creado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha para saber cuando se envío el correo. |
| usado | BOOLEAN | DF(false) | Estado del código. Cambia a true tras validación exitosa. |
| id_usuario | BIGINT | FK, NN | Relación con la cuenta que solicitó el código. |

### Entidad Categoría

| Campo | Tipo de dato | Restricciones | Comentario |
| :--- | :--- | :--- | :--- |
| id_categoria | BIGSERIAL | PK, AI, NN | Identificador único autoincrementable. |
| nombre | VARCHAR(50) | NN | Nombre de la categoría (ej: alimentación, salud, salario, deporte, impuestos, transporte, tecnología, mascotas, viajes, entretenimiento o cualquiera que considere el cliente) |
| icono | VARCHAR(500) | NN | El campo no almacena la imagen sino la URL donde estará alojado el icono o el código del emoji. |
| tipo | VARCHAR(10) | NN, CHECK | Valores que se aceptan: "ingreso" o "gasto". |
| creado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha de registro. No cambia tras la inserción. |
| actualizado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha de último cambio de la categoría creada por el cliente. |
| id_cliente | BIGINT | FK, NN | Relación con la tabla cliente, indica a qué cuenta pertenece esta categoría personalizada, ya que cada cliente puede crear categorías diferentes. |

### Entidad Transacción

| Campo | Tipo de dato | Restricciones | Comentario |
| :--- | :--- | :--- | :--- |
| id_transaccion | BIGSERIAL | PK, NN, AI | Identificador único autoincrementable |
| nombre | VARCHAR(150) | NN | Concepto de la transacción (ej: compra de mercado, compra de medicamentos, recarga de tarjeta cívica, compra de boleta para el cine) |
| monto | NUMERIC(15, 2) | NN, CHECK | Valor monetario exacto. CHECK (monto > 0). Teniendo en cuenta que el campo tipo define si suma o resta. |
| movimiento_en | TIMESTAMPTZ | NN | Fecha en la que ocurrió la compra o el ingreso. Puede ser ingresada directamente por el usuario. |
| creado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha de registro en la base de datos. |
| actualizado_en | TIMESTAMPTZ | NN, DF(NOW()) | Fecha de cuando se modificó por última vez el registro. |
| tipo | VARCHAR(10) | NN, CHECK | Valores que se aceptan: "ingreso" o "gasto". |
| id_categoria | BIGINT | FK, NN | Relación con la categoría que clasifica este movimiento. |
| id_cliente | BIGINT | FK, NN | Relación con el cliente dueño de esta transacción. |
