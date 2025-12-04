# NoSQL Lab - Tienda Online (MongoDB + Redis)

**Caso de uso:** Tienda online con catálogo de productos, usuarios, pedidos y funcionalidades en tiempo real (ranking de vistas, stock en memoria, sesiones, colas de órdenes).

Fecha de generación: 2025-12-04

---

## 1. ¿Qué es una base de datos NoSQL y en qué se diferencia, a nivel conceptual, de una base de datos relacional (SQL)?
Una base de datos NoSQL es un sistema de almacenamiento diseñado para manejar datos con modelos no tabulares: documentos, grafos, columnas o pares clave–valor. Se orienta a la **flexibilidad**, la **alta disponibilidad** y la **escalabilidad horizontal**. A diferencia de una base de datos relacional (SQL), NoSQL no exige un esquema rígido, y está optimizado para cargas de trabajo distribuidas y volúmenes grandes de datos.

**Diferencias clave:**

- **Modelo de datos:** SQL usa tablas con filas y columnas fijas; NoSQL (por ejemplo MongoDB) usa documentos JSON/BSON que pueden variar entre sí.
- **Escalabilidad:** SQL tradicionalmente escala verticalmente (mejor hardware). NoSQL está diseñado para escalar horizontalmente (sharding) y replicación sencilla.

---

## 2. ¿Cuál es el rol de MongoDB dentro del ecosistema NoSQL?
MongoDB es una **base de datos orientada a documentos**. Almacena datos en documentos BSON (binario JSON), lo que lo hace ideal para aplicaciones web modernas donde los datos son naturalmente representados como objetos anidados. MongoDB ofrece consultas ricas, índices, agregaciones y opciones de replicación y sharding para escalabilidad.

---

## 3. ¿Qué son una “base de datos”, una “colección” y un “documento” en MongoDB, y cómo se relacionan entre sí?
- **Base de datos:** Contenedor que agrupa colecciones (ej. `tienda_online`).
- **Colección:** Conjunto de documentos (similar a una tabla, pero sin esquema rígido) (ej. `products`, `users`, `orders`).
- **Documento:** Unidad mínima de almacenamiento (JSON/BSON).

**Ejemplo de documento (`products`):**
```json
{
  "_id": { "$oid": "650000000000000000000001" },
  "sku": "P1001",
  "name": "Camiseta básica",
  "price": 29.99,
  "stock": 120,
  "categories": ["ropa", "hombre"],
  "details": {
    "material": "algodón",
    "sizes": ["S","M","L","XL"]
  },
  "created_at": { "$date": "2025-01-01T10:00:00Z" }
}
```

---

## 4. ¿Qué ventajas aporta el esquema flexible (schema flexible) de MongoDB en comparación con el esquema rígido de una base de datos relacional?
**Ventajas:**

1. **Evolución rápida:** Añadir campos o cambiar estructura sin migraciones pesadas.
2. **Modelado natural:** Documentos anidados permiten representar objetos complejos en una sola entidad.

**Riesgo:** Si no se controla, la falta de esquema puede producir documentos inconsistentes, dificultando validaciones y consultas complejas. Es recomendable usar validaciones (`$jsonSchema`) o una capa de validación en la aplicación.

---

## 5. ¿Qué es Redis y por qué se dice que es un “data structure server” y no solo un almacén clave–valor simple?
Redis es una base de datos en memoria que ofrece estructuras de datos avanzadas además de pares clave-valor. Almacena y opera sobre **strings, hashes, lists, sets, sorted sets, streams**, entre otros, con latencias muy bajas, por lo que es ideal para caching y operaciones en tiempo real.

**Tipos de datos (ejemplos):** Strings, Hashes, Lists, Sorted Sets.

---

## 6. Elegir dos tipos de datos de Redis y describir su uso y caso de uso.

### Lists
**Comandos básicos:** `LPUSH queue job`, `RPUSH queue job`, `LPOP queue`, `RPOP queue`, `LRANGE queue 0 -1`.
**Caso de uso:** Cola de procesamiento de órdenes (`order_queue`) donde los workers usan `BRPOP`/`BLPOP` para procesar órdenes en segundo plano.

### Sorted Sets
**Comandos básicos:** `ZADD ranking 10 product:1001`, `ZRANGE ranking 0 -1 WITHSCORES`, `ZINCRBY ranking 1 product:1001`.
**Caso de uso:** Ranking de productos por vistas o ventas (`product_views`) para mostrar los más populares en tiempo real.

---

## 7. En el laboratorio de turnos (MongoDB + Redis):

### ¿Qué información se almacena en MongoDB?
- Registros persistentes de turnos (historial): `turns` con datos del usuario, hora, servicio, estado final.

### ¿Qué información se maneja o acelera con Redis?
- Contadores de turnos, estado de la fila en memoria, turnos activos y cola en Redis para lecturas rápidas.

**Lógica de división:** MongoDB para persistencia y consultas históricas; Redis para los estados que cambian frecuentemente y para responder a lecturas/actualizaciones de alta velocidad.

---

## 8. En el laboratorio de tamagochi (MongoDB + Redis):

### ¿Qué guardar en MongoDB?
- Datos persistentes del tamagochi: nombre, especie, fecha creación, log histórico de eventos.

### ¿Qué guardar en Redis?
- Estado dinámico (hambre, energía, felicidad), contadores incrementales, timers y TTL para acciones con caducidad.

---

## 9. Criterio para decidir qué va en MongoDB y qué va en Redis
- **MongoDB:** Datos que deben ser persistentes, auditables y consultables (usuarios, pedidos, productos).
- **Redis:** Datos volátiles o de acceso intensivo en lectura/escritura (sessions, caches, contadores, rankings, colas).

**Ejemplo concreto:**

- **MongoDB:** Colección `orders` con el detalle completo del pedido y su historial.
- **Redis:** Sorted Set `product_views` para rankear productos por vistas en tiempo real, y Hash `inventory:<sku>` para el stock en memoria.

---

## 10. Reflexión final
**Usaría MongoDB como base principal para:** catálogos, usuarios, pedidos, historiales y cuando se necesita evitar migraciones complejas al evolucionar el esquema.

**Usaría Redis como apoyo para:** sesiones, caché, colas de tareas (envío de emails, procesamiento de pagos asíncrono), rankings y contadores en tiempo real. En nuestros laboratorios, Redis aceleró el acceso a estados volátiles como el contador de turnos y las métricas del tamagochi.
