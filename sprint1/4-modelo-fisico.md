# 4. Crear modelo físico con columnas y clave primaria (acorde al alcance definido por el Product Owner).
A continuación se presenta el modelo físico de nuestra base de datos junto con el script en postgreSQL

![Diagrama del Modelo Lógico](/sprint1/img/modelo_fisico.png)

```sql
CREATE TABLE clientes (
	id_cliente BIGSERIAL PRIMARY KEY,
	nombre VARCHAR(150) NOT NULL,
	correo_contacto VARCHAR(150) UNIQUE,
imagen_perfil VARCHAR(500),
	creado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
	actualizado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
	descripcion TEXT
);

CREATE TABLE usuarios (
	id_usuario BIGSERIAL PRIMARY KEY,
	correo VARCHAR(150) NOT NULL UNIQUE,
	contrasena VARCHAR(255) NOT NULL,
estado VARCHAR(30) NOT NULL CHECK (estado IN ('ACTIVO', 'INACTIVO', 'PENDIENTE', 'BLOQUEADO')),
creado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
actualizado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
id_cliente BIGINT UNIQUE,
CONSTRAINT fk_usuario_cliente FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente)
);

CREATE TABLE codigos_verificacion (
	id_codigo BIGSERIAL PRIMARY KEY,
	codigo VARCHAR(10) NOT NULL,
	tipo VARCHAR(30) NOT NULL CHECK (tipo IN ('VERIFICACION_CORREO', 'RECUPERACION_CONTRASENA', 'DOS_FACTORES')),
	expira_en TIMESTAMPTZ NOT NULL,
	creado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
	usado BOOLEAN DEFAULT FALSE,
	id_usuario BIGINT NOT NULL,
	CONSTRAINT fk_codigo_usuario FOREIGN KEY (id_usuario) REFERENCES usuarios(id_usuario)
);

CREATE TABLE categorias (
	id_categoria BIGSERIAL PRIMARY KEY,
	nombre VARCHAR(50) NOT NULL,
	icono VARCHAR(500) NOT NULL,
	tipo VARCHAR(10) NOT NULL CHECK (tipo IN ('INGRESO', 'GASTO')),
creado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
actualizado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
id_cliente BIGINT NOT NULL,
CONSTRAINT fk_categoria_cliente FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente)
);

CREATE TABLE transacciones (
id_transaccion BIGSERIAL PRIMARY KEY,
nombre VARCHAR(150) NOT NULL,
monto NUMERIC(15,2) NOT NULL CHECK (monto > 0),
movimiento_en TIMESTAMPTZ NOT NULL,
creado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
actualizado_en TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
tipo VARCHAR(10) NOT NULL CHECK (tipo IN ('INGRESO', 'GASTO')),
id_cliente BIGINT NOT NULL,
id_categoria BIGINT NOT NULL,
CONSTRAINT fk_transaccion_cliente FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente),
CONSTRAINT fk_transaccion_categoria FOREIGN KEY (id_categoria) REFERENCES categorias(id_categoria)
);
```
