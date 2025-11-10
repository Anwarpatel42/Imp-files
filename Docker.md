Sure 👍 Here’s the **complete Markdown (`README.md`) code** version of your Docker + MongoDB + Node.js setup — fully formatted for GitHub or documentation use.

---

````markdown
# 🐳 Docker + MongoDB + Node.js Setup Guide

This documentation explains how to install dependencies, manage Docker containers, connect MongoDB with Node.js, and run a simple Express server.

---

## 🧩 1. Basic Linux Setup

Before using Docker, ensure you have essential packages installed:

```bash
sudo apt update
sudo apt install nano -y
sudo apt install gcc -y
````

---

## 🐋 2. Docker Basic Commands

### 🔹 List Docker Images

```bash
docker images
```

👉 Shows all downloaded images on your system.

### 🔹 List Running Containers

```bash
docker ps
```

👉 Displays only **active (running)** containers.

### 🔹 List All Containers (Active + Stopped)

```bash
docker ps -a
```

### 🔹 Stop a Running Container

```bash
docker stop <container_id_or_name>
```

### 🔹 Remove (Delete) a Container

```bash
docker rm <container_id_or_name>
```

👉 Must stop it before removing.

### 🔹 Remove (Delete) an Image

```bash
docker rmi <image_id>
```

👉 You need to delete containers **created from that image first**.

---

## 🧱 3. Docker Network Commands

### 🔹 List Networks

```bash
docker network ls
```

### 🔹 Create a Custom Network

```bash
docker network create network name --example(mongo-network)
```
```
docker network inspect mongo-network (container composed in that network)
```

---

## 🐘 4. Running MySQL (Example)

```bash
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:latest
```

* `--name some-mysql` → custom name for the container
* `-e KEY=value` → set environment variables
* `-d` → run in detached mode (background)

---

## 🍃 5. Running MongoDB and Mongo Express

### 🟢 MongoDB Container

```bash
docker run -d -p 27017:27017 --name mongo --network mongo-network \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password mongo
```

### 🟣 Mongo Express Container

```bash
docker run -d --name mongo-express --network mongo-network -p 8081:8081 \
  -e ME_CONFIG_MONGODB_URL="mongodb://admin:password@mongo:27017/" \
  -e ME_CONFIG_BASICAUTH_ENABLED=true \
  -e ME_CONFIG_BASICAUTH_USERNAME=admin \
  -e ME_CONFIG_BASICAUTH_PASSWORD=password mongo-express
```

* MongoDB Admin URL: **[http://localhost:8081](http://localhost:8081)**
* MongoDB Connection String:

  ```
  mongodb://admin:password@localhost:27017/docker-mongodb?authSource=admin
  ```

---

## ⚙️ 6. Node.js + Express + Mongoose Example

### 📁 server.js

```js
const express = require("express");
const mongoose = require("mongoose");

const app = express();
const PORT = process.env.PORT || 3000;
const MONGO_URI =
  process.env.MONGO_URI ||
  "mongodb://admin:password@localhost:27017/docker-mongodb?authSource=admin";

// Middleware
app.use(express.json());

// User Schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number },
});

const User = mongoose.model("User", userSchema);

// Dummy Data
const dummyUsers = [
  { name: "Anwar Patel", email: "anwar@example.com", age: 22 },
  { name: "Patel", email: "patel@example.com", age: 25 },
  { name: "Sameer", email: "sameer@example.com", age: 20 },
];

// Connect to MongoDB
mongoose
  .connect(MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(async () => {
    console.log("Connected to MongoDB");

    const count = await User.countDocuments();
    if (count === 0) {
      await User.insertMany(dummyUsers);
      console.log("Dummy users inserted");
    }
  })
  .catch((err) => console.error("MongoDB connection error:", err));

// Routes
app.get("/", (req, res) => res.send("Hello from Docker MongoDB Server!"));

app.get("/users", async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.post("/users", async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});

// Start Server
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

---

## 🧪 7. Run the App

```bash
node server.js
```

**Output:**

```
Server running on port 3000
Connected to MongoDB
```

---

## ✅ 8. Test the API

### ➤ Home Route

**GET**

```
http://localhost:3000/
```

**Response:**

```
Hello from Docker MongoDB Server!
```

### ➤ Get All Users

**GET**

```
http://localhost:3000/users
```

### ➤ Add a New User

**POST**

```
http://localhost:3000/users
```

**Body (JSON):**

```json
{
  "name": "New User",
  "email": "newuser@example.com",
  "age": 30
}
```

---

## 🔍 9. Common Troubleshooting

| Error                             | Cause                                    | Fix                                                                             |
| --------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------- |
| `Command requires authentication` | Wrong MongoDB URL or missing credentials | Use correct URI: `mongodb://admin:password@localhost:27017/db?authSource=admin` |
| `EADDRINUSE 3000`                 | Port 3000 already in use                 | Change port in `server.js`                                                      |
| Mongo Express login fails         | Wrong BASIC AUTH credentials             | Check `-e ME_CONFIG_BASICAUTH_*` values                                         |
| Container not found               | Wrong name or network                    | Run `docker ps -a` to verify                                                    |

---

## 🧹 10. Cleanup Commands

```bash
docker stop mongo mongo-express
docker rm mongo mongo-express
docker rmi mongo mongo-express
docker network rm mongo-network
```

---

## 🎯 Summary

✅ Install dependencies
✅ Run MongoDB and Mongo Express containers
✅ Connect Node.js app using Mongoose
✅ Access and manage data through REST API

---

**Author:** 🧑‍💻 *Anwar Patel*
**Project:** *Docker MongoDB Node.js Integration*
**Date:** *2025*

```

---

Would you like me to add a **table of contents with clickable links** at the top (for GitHub navigation)? It makes the README much easier to browse.
```
