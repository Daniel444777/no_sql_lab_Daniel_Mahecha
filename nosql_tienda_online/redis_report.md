# redis_report.md

## Tienda Online - Uso de Redis

### Por qué Redis
Redis se utiliza para acelerar accesos frecuentes y gestionar datos volátiles: sesiones, colas, métricas en tiempo real y rankeos.

### Claves y tipos utilizados
- `session:userid:<id>` (String) - sesiones con TTL.
- `inventory:<sku>` (Hash) - stock en memoria y reservado.
- `order_queue` (List) - cola para procesar pedidos asíncronamente.
- `product_views` (Sorted Set) - ranking de vistas por producto.
- `online_users` (Set) - usuarios actualmente conectados.

### Ejemplos de comandos y resultados
- `HGETALL inventory:P1001` -> `{ stock: 120, reserved: 0 }`
- `ZINCRBY product_views 1 "P1001"` -> incrementa contador de vistas
- `LPUSH order_queue "{...}"` -> añade nueva orden a la cola

*(En la versión PDF final agrega capturas de `redis-cli` o RedisInsight mostrando la salida real.)*
