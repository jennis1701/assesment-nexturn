db.customers.insertMany([
    { name: "John Doe", email: "johndoe@example.com", address: { street: "123 Main St", city: "Springfield", zipcode: "12345" }, phone: "555-1234", registered_on: new Date("2023-01-01T12:00:00Z") },
    { name: "Jane Smith", email: "janesmith@example.com", address: { street: "456 Elm St", city: "Shelbyville", zipcode: "54321" }, phone: "555-5678", registered_on: new Date("2023-02-15T12:00:00Z") },
    { name: "Alice Johnson", email: "alicej@example.com", address: { street: "789 Oak St", city: "Springfield", zipcode: "12345" }, phone: "555-8765", registered_on: new Date("2023-03-10T12:00:00Z") },
    { name: "Bob Brown", email: "bobbrown@example.com", address: { street: "321 Pine St", city: "Shelbyville", zipcode: "54321" }, phone: "555-4321", registered_on: new Date("2023-04-20T12:00:00Z") },
    { name: "Carol White", email: "carolwhite@example.com", address: { street: "654 Maple St", city: "Springfield", zipcode: "12345" }, phone: "555-6789", registered_on: new Date("2023-05-05T12:00:00Z") }
]);

db.customers.findOne({ name: "John Doe" })._id;
db.customers.findOne({ name: "Jane Smith" })._id;
db.customers.findOne({ name: "Alice Johnson" })._id;
db.customers.findOne({ name: "Bob Brown" })._id;
db.customers.findOne({ name: "Carol White" })._id;

db.orders.insertMany([
    {
        _id: ObjectId(),
        order_code: "ODR001",
        client_id: ObjectId('67320c549ec744648a0d8190'),
        placed_on: "2023-05-15",
        current_status: "shipped",
        products: [
            { item: "Laptop", quantity: 1, unit_price: 1500 },
            { item: "Mouse", quantity: 2, unit_price: 25 }
        ],
        total_amount: 1550
    },
    {
        _id: ObjectId(),
        order_code: "ODR002",
        client_id: ObjectId('67320c549ec744648a0d8191'),
        placed_on: "2023-06-01",
        current_status: "pending",
        products: [
            { item: "Tablet", quantity: 1, unit_price: 300 }
        ],
        total_amount: 300
    },
    {
        _id: ObjectId(),
        order_code: "ODR003",
        client_id: ObjectId('67320c549ec744648a0d8192'),
        placed_on: "2023-06-10",
        current_status: "delivered",
        products: [
            { item: "Keyboard", quantity: 1, unit_price: 100 },
            { item: "Monitor", quantity: 1, unit_price: 200 }
        ],
        total_amount: 300
    },
    {
        _id: ObjectId(),
        order_code: "ODR004",
        client_id: ObjectId('67320c549ec744648a0d8193'),
        placed_on: "2023-06-20",
        current_status: "shipped",
        products: [
            { item: "Smartphone", quantity: 1, unit_price: 800 }
        ],
        total_amount: 800
    },
    {
        _id: ObjectId(),
        order_code: "ODR005",
        client_id: ObjectId('67320c549ec744648a0d8194'),
        current_status: "processing",
        products: [
            { item: "Headphones", quantity: 1, unit_price: 50 },
            { item: "Charger", quantity: 1, unit_price: 20 }
        ],
        total_amount: 70
    }
]);

db.orders.find({ client_id: ObjectId('67320c549ec744648a0d8190') });

db.customers.findOne({ _id: ObjectId('67320c549ec744648a0d8190') });

db.orders.updateOne(
    { order_code: "ODR001" },
    { $set: { current_status: "delivered" } }
);

db.orders.deleteOne({ order_code: "ODR001" });

db.orders.aggregate([
    { $group: { _id: "$client_id", total_spent: { $sum: "$total_amount" } } },
    { $lookup: { from: "customers", localField: "_id", foreignField: "_id", as: "client_info" } },
    { $unwind: "$client_info" },
    { $project: { name: "$client_info.name", total_spent: 1 } }
]);

db.orders.aggregate([
    { $group: { _id: "$current_status", order_count: { $sum: 1 } } }
]);

db.orders.aggregate([
    { $sort: { placed_on: -1 } },
    { $group: { _id: "$client_id", latest_order: { $first: "$$ROOT" } } },
    { $lookup: { from: "customers", localField: "_id", foreignField: "_id", as: "client_info" } },
    { $unwind: "$client_info" },
    { $project: { name: "$client_info.name", email: "$client_info.email", order_code: "$latest_order.order_code", total_amount: "$latest_order.total_amount" } }
]);

db.orders.aggregate([
    { $sort: { total_amount: -1 } },
    { $group: { _id: "$client_id", top_order: { $first: "$$ROOT" } } },
    { $lookup: { from: "customers", localField: "_id", foreignField: "_id", as: "client_info" } },
    { $unwind: "$client_info" },
    { $project: { name: "$client_info.name", order_code: "$top_order.order_code", total_amount: "$top_order.total_amount" } }
]);

db.orders.aggregate([
    { $match: { placed_on: { $gte: new Date(new Date().setDate(new Date().getDate() - 30)) } } },
    { $group: { _id: "$client_id", latest_order: { $first: "$$ROOT" } } },
    { $lookup: { from: "customers", localField: "_id", foreignField: "_id", as: "client_info" } },
    { $unwind: "$client_info" },
    { $project: { name: "$client_info.name", email: "$client_info.email", order_date: "$latest_order.placed_on" } }
]);

db.orders.aggregate([
    { $match: { client_id: ObjectId('67320c549ec744648a0d8190') } },
    { $unwind: "$products" },
    { $group: { _id: "$products.item", quantity_ordered: { $sum: "$products.quantity" } } }
]);

db.orders.aggregate([
    { $group: { _id: "$client_id", total_spent: { $sum: "$total_amount" } } },
    { $sort: { total_spent: -1 } },
    { $limit: 3 },
    { $lookup: { from: "customers", localField: "_id", foreignField: "_id", as: "client_info" } },
    { $unwind: "$client_info" },
    { $project: { name: "$client_info.name", total_spent: 1 } }
]);

db.orders.insertOne({
    order_code: "ODR006",
    client_id: ObjectId("67320c549ec744648a0d8191"),
    placed_on: new Date("2023-08-01T14:00:00Z"),
    current_status: "pending",
    products: [
        { item: "Smartphone", quantity: 1, unit_price: 700 },
        { item: "Headphones", quantity: 2, unit_price: 50 }
    ],
    total_amount: 800
});

db.customers.aggregate([
    { $lookup: { from: "orders", localField: "_id", foreignField: "client_id", as: "order_history" } },
    { $match: { "order_history": { $size: 0 } } },
    { $project: { name: 1, email: 1 } }
]);

db.orders.aggregate([
    { $unwind: "$products" },
    { $group: { _id: null, avg_items: { $avg: "$products.quantity" } } },
    { $project: { avg_items: 1, _id: 0 } }
]);

db.orders.aggregate([
    { $lookup: { from: "customers", localField: "client_id", foreignField: "_id", as: "client_info" } },
    { $unwind: "$client_info" },
    { $match: { "client_info.city": "Springfield" } },
    { $group: { _id: null, avg_order_total: { $avg: "$total_amount" } } }
]);
