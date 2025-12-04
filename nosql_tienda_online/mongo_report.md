# mongo_report.md

## Tienda Online - Diseño en MongoDB

### Caso de uso
Tienda online que gestiona productos, usuarios y pedidos. MongoDB se usa como sistema de persistencia principal.

### Colecciones principales
- **products**: sku, name, price, stock, categories, details, created_at
- **users**: username, email, full_name, address, created_at
- **orders**: order_number, user_id, items, total, status, created_at
- **audits**: registros de cambios (opcional)

### Ejemplo de documento (products)
{
  "sku": "P1002",
  "name": "Pantalón jean",
  "price": 59.99,
  "stock": 45,
  "categories": ["ropa","hombre"],
  "details": { "material": "denim", "sizes": ["M","L","XL"] },
  "created_at": "2025-02-01T12:00:00Z"
}

### Resultados de consultas y comandos ejecutados
- Inserción: `db.products.insertOne({...})`
- Consulta: `db.products.find({ categories: "ropa" })`
- Actualización: `db.products.updateOne({ sku: "P1002" }, { $inc: { stock: -2 } })`
- Agregación: top productos por ventas (ver `mongo_commands.md`)

*(En la versión PDF final agrega capturas de `mongosh` o `MongoDB Compass` mostrando la salida real.)*
