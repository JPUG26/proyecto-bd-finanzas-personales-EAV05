# 2. Crear o refinar el script de creación de objetos en general con Trigger y procedimientos para las HU desarrolladas.
**Nota:** Los triggers y procedimientos almacenados no fueron implementados durante los Sprints 1 y 2. La lógica de negocio fue desarrollada íntegramente en la capa de aplicación (Spring Boot) siguiendo los principios de la Arquitectura Hexagonal. Este apartado documenta cómo podrían implementarse en futuras iteraciones del proyecto.

## Justificación de la decisión de diseño
En la Arquitectura Hexagonal el dominio y los casos de uso viven en el código Java, no en la base de datos. Esto garantiza que la lógica sea completamente testeable, portable entre motores de BD y fácil de depurar. Delegar lógica a triggers crearía dependencias ocultas que romperían este principio arquitectónico.
Sin embargo, los elementos descritos a continuación representan alternativas válidas para futuras iteraciones en las que se decida optimizar el rendimiento delegando parte del procesamiento al motor de PostgreSQL.

## 2.1 Trigger propuesto — Actualizar timestamp automáticamente
**Propósito:** mantener el campo actualizado_en siempre sincronizado sin depender de que la aplicación lo envíe. Aplica a las tablas: usuarios, clientes, categorias, transacciones y presupuestos.

```sql
CREATE OR REPLACE FUNCTION fn_actualizar_timestamp()
RETURNS TRIGGER AS $$
BEGIN
    NEW.actualizado_en = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_usuarios_actualizar_en
    BEFORE UPDATE ON usuarios
    FOR EACH ROW EXECUTE FUNCTION fn_actualizar_timestamp();

CREATE TRIGGER trg_clientes_actualizar_en
    BEFORE UPDATE ON clientes
    FOR EACH ROW EXECUTE FUNCTION fn_actualizar_timestamp();
```

## 2.2 Trigger propuesto — Invalidar códigos anteriores
**Propósito:** al insertar un nuevo código de verificación, marcar automáticamente como usados los anteriores del mismo tipo para el mismo usuario.

```sql
CREATE OR REPLACE FUNCTION fn_invalidar_codigos_anteriores()
RETURNS TRIGGER AS $$
BEGIN
    UPDATE codigos_verificacion
    SET usado = TRUE
    WHERE id_usuario = NEW.id_usuario
      AND tipo      = NEW.tipo
      AND usado     = FALSE
      AND id_codigo != NEW.id_codigo;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_invalidar_codigos
    AFTER INSERT ON codigos_verificacion
    FOR EACH ROW EXECUTE FUNCTION fn_invalidar_codigos_anteriores();
```

## 2.3 Procedimiento almacenado propuesto — Balance del cliente
**Propósito:** encapsular el cálculo del balance total para reutilizarlo en múltiples consultas sin repetir la lógica de suma.
```sql
CREATE OR REPLACE FUNCTION fn_balance_cliente(p_id_cliente BIGINT)
RETURNS NUMERIC(15,2) AS $$
DECLARE
    v_ingresos NUMERIC(15,2);
    v_gastos   NUMERIC(15,2);
BEGIN
    SELECT COALESCE(SUM(monto), 0) INTO v_ingresos
    FROM transacciones
    WHERE id_cliente = p_id_cliente AND tipo = 'INGRESO';

    SELECT COALESCE(SUM(monto), 0) INTO v_gastos
    FROM transacciones
    WHERE id_cliente = p_id_cliente AND tipo = 'GASTO';

    RETURN v_ingresos - v_gastos;
END;
$$ LANGUAGE plpgsql;
-- Uso: SELECT fn_balance_cliente(1);
```

## 2.4 Procedimiento almacenado propuesto — Resumen mensual
**Propósito:** generar un resumen de ingresos, gastos y balance para un mes y año específicos.
```sql
CREATE OR REPLACE FUNCTION fn_resumen_mensual(
    p_id_cliente BIGINT, p_anio INT, p_mes INT
)
RETURNS TABLE(
    total_ingresos NUMERIC(15,2),
    total_gastos   NUMERIC(15,2),
    balance        NUMERIC(15,2)
) AS $$
BEGIN
    RETURN QUERY
    SELECT
        COALESCE(SUM(CASE WHEN tipo='INGRESO' THEN monto ELSE 0 END), 0),
        COALESCE(SUM(CASE WHEN tipo='GASTO'   THEN monto ELSE 0 END), 0),
        COALESCE(SUM(CASE WHEN tipo='INGRESO' THEN monto ELSE -monto END), 0)
    FROM transacciones
    WHERE id_cliente = p_id_cliente
      AND EXTRACT(YEAR  FROM movimiento_en) = p_anio
      AND EXTRACT(MONTH FROM movimiento_en) = p_mes;
END;
$$ LANGUAGE plpgsql;
-- Uso: SELECT * FROM fn_resumen_mensual(1, 2026, 4);
```

## 2.5 Procedimiento almacenado propuesto — Verificar presupuesto
**Propósito:** verificar si el cliente superó el presupuesto definido para una categoría en el período activo. 
```sql
CREATE OR REPLACE FUNCTION fn_verificar_presupuesto(
    p_id_cliente BIGINT, p_id_categoria BIGINT
)
RETURNS TABLE(
    nombre_categoria VARCHAR, monto_limite NUMERIC(15,2),
    monto_gastado NUMERIC(15,2), porcentaje_uso NUMERIC(5,2),
    supera_limite BOOLEAN
) AS $$
BEGIN
    RETURN QUERY
    SELECT c.nombre, p.monto_limite,
        COALESCE(SUM(t.monto), 0),
        ROUND(COALESCE(SUM(t.monto),0) / p.monto_limite * 100, 2),
        COALESCE(SUM(t.monto), 0) > p.monto_limite
    FROM presupuestos p
    JOIN categorias c ON c.id_categoria = p.id_categoria
    LEFT JOIN transacciones t
           ON t.id_categoria = p.id_categoria
          AND t.id_cliente   = p.id_cliente
          AND t.tipo         = 'GASTO'
          AND t.movimiento_en BETWEEN p.periodo_inicio AND p.periodo_fin
    WHERE p.id_cliente   = p_id_cliente
      AND p.id_categoria = p_id_categoria
      AND NOW() BETWEEN p.periodo_inicio AND p.periodo_fin
    GROUP BY c.nombre, p.monto_limite;
END;
$$ LANGUAGE plpgsql;
-- Uso: SELECT * FROM fn_verificar_presupuesto(1, 3);
```
