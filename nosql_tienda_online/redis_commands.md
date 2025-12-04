# redis_commands.md

# Conexión (redis-cli o RedisInsight)
# redis-cli -h 127.0.0.1 -p 6379

## 1. Strings - Uso básico (sessions, flags, counters)
SET session:userid:1234 "{\"username\":\"kmartinez\",\"cart_count\":2}" EX 3600
GET session:userid:1234
INCR product:views:P1001

## 2. Hashes - almacenar atributos asociados a una entidad
HSET inventory:P1001 stock 120 reserved 0
HGETALL inventory:P1001
HINCRBY inventory:P1001 reserved 1
HINCRBY inventory:P1001 stock -1

## 3. Lists - cola de órdenes
LPUSH order_queue "{"order_number":"O10002","user_id":"u100"}"
BRPOP order_queue 0  # worker que procesa la cola

## 4. Sorted Sets - ranking de productos por vistas o ventas
ZADD product_views 10 "P1001"
ZINCRBY product_views 1 "P1001"
ZRANGE product_views 0 -1 WITHSCORES

## 5. Sets - usuarios online
SADD online_users user:123 user:456
SMEMBERS online_users
SREM online_users user:123

## 6. TTL y caducidad
SET temp_token:abc123 "..." EX 300
TTL temp_token:abc123

## 7. Streams (ejemplo simple para eventos)
XADD order_events * order_id O10001 status "created" user kmartinez

## 8. Operaciones de ejemplo en un flujo real
# Al crear una vista de producto en la app:
INCR product:views:P1001
ZINCRBY product_views 1 "P1001"

# Al crear una orden (worker):
LPUSH order_queue "{"order_number":"O10003"}"
