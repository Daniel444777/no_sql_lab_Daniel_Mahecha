# mongo_commands.md

## Conexión (mongosh o MongoDB Atlas)
# Local:
# mongosh "mongodb://localhost:27017/tienda_online"

## Crear base de datos
use tienda_online

## Crear colecciones (automático al insertar)
# products, users, orders, inventories, audits

## Insertar documentos (ejemplos)

db.products.insertOne({
  sku: "P1001",
  name: "Camiseta básica",
  price: 29.99,
  stock: 120,
  categories: ["ropa","hombre"],
  details: { material: "algodón", sizes: ["S","M","L","XL"] },
  created_at: new Date()
})

db.users.insertOne({
  username: "kmartinez",
  email: "kevin@example.com",
  full_name: "Kevin Martinez",
  address: { city: "Bogotá", country: "Colombia" },
  created_at: new Date()
})

db.orders.insertOne({
  order_number: "O10001",
  user_id: ObjectId(), // reemplazar por _id real del usuario
  items: [
    { sku: "P1001", qty: 2, price: 29.99 }
  ],
  total: 59.98,
  status: "pending",
  created_at: new Date()
})

## Consultas básicas

# Obtener producto por SKU
db.products.findOne({ sku: "P1001" })

# Buscar productos por categoría con proyección
db.products.find({ categories: "ropa" }, { name: 1, price: 1 })

# Consultas con rango de precio
db.products.find({ price: { $gte: 20, $lte: 50 } })

## Actualizar documentos
# Actualizar stock
db.products.updateOne({ sku: "P1001" }, { $inc: { stock: -1 } })

# Añadir campo nuevo a todos los productos
db.products.updateMany({}, { $set: { currency: "USD" } })

## Operaciones avanzadas: aggregation
# Top productos por ventas (suponiendo una colección de line_items o datos de orders)
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: { _id: "$items.sku", totalSold: { $sum: "$items.qty" } } },
  { $sort: { totalSold: -1 } },
  { $limit: 10 }
])

## Índices
# Índice por SKU
db.products.createIndex({ sku: 1 }, { unique: true })

# Índice compuesto para búsquedas por categoría y precio
db.products.createIndex({ categories: 1, price: 1 })

## Validación de esquema (ejemplo simple)
db.createCollection("products", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["sku","name","price"],
      properties: {
        sku: { bsonType: "string" },
        name: { bsonType: "string" },
        price: { bsonType: "double" }
      }
    }
  }
})

## Eliminaciones
# Borrar producto
db.products.deleteOne({ sku: "P1001" })

# Borrar coleccion
db.orders.drop()
