from fastapi import FastAPI
from pydantic import BaseModel
import sqlite3

app = FastAPI()

@app.post("/query")
async def query_data(request: BaseModel):
    q = request.user_query.lower()
    query = (
        f"SELECT * FROM products WHERE brand = '{q.split('brand ')[-1]}';" if "products under brand" in q else
        f"SELECT * FROM suppliers WHERE product_categories_offered LIKE '%{q.split('provide ')[-1]}%';" if "suppliers provide" in q else None
    )
    with sqlite3.connect("database.db") as conn:
        conn.row_factory = sqlite3.Row
        data = [dict(row) for row in conn.execute(query).fetchall()] if query else None
    return {"data": data} if data else {"error": "Query not supported"}

import React, { useState } from "react";
import axios from "axios";

function App() {
    const [query, setQuery] = useState("");
    const [response, setResponse] = useState(null);
    return (
        <div>
            <input value={query} onChange={(e) => setQuery(e.target.value)} />
            <button onClick={async () => setResponse((await axios.post("/query", { user_query: query })).data.data)}>Submit</button>
            <pre>{JSON.stringify(response, null, 2)}</pre>
        </div>
    );
}
export default App;

with sqlite3.connect("database.db") as conn:
    conn.executescript('''
        CREATE TABLE products (id INTEGER PRIMARY KEY, name TEXT, brand TEXT, price REAL, category TEXT, description TEXT, supplier_id INTEGER);
        CREATE TABLE suppliers (id INTEGER PRIMARY KEY, name TEXT, contact_info TEXT, product_categories_offered TEXT);
        INSERT INTO products VALUES (NULL, "Laptop", "BrandX", 1000.0, "Electronics", "High-end laptop", 1);
        INSERT INTO products VALUES (NULL, "Phone", "BrandY", 500.0, "Electronics", "Smartphone", 2);
        INSERT INTO suppliers VALUES (NULL, "Supplier1", "contact@supplier1.com", "Laptops, Phones");
        INSERT INTO suppliers VALUES (NULL, "Supplier2", "contact@supplier2.com", "Phones");
    ''')
    //Here is fully working code with different modules Develop a chatbot that allows users to query a product and supplier database using natural language. The chatbot should interact with an open-source LLM and utilize LangGraph framework for agent workflows to fetch relevant information from a MySQL/PostgreSQL database and summarize the data using LLM.
