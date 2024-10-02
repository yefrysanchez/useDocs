# Prisma

Prisma is a tool that helps developers easily connect their applications to a database. It lets you define how your data is structured and provides a simple way to read and write data. 
Think of it as a bridge that makes it easier to work with databases without having to write complicated SQL queries. Here are some key concepts:

## Key Concepts of Prisma

- **Prisma Client**: A tool that helps you interact with your database using easy-to-read code.

- **Prisma Schema**: A file where you define how your data looks and how it connects in the database.

- **Data Model**: The blueprint for your data. Each model represents a table in your database.

- **Migrations**: Changes you make to your database structure, like adding or changing tables. Prisma helps manage these changes.

- **Prisma Migrate**: A tool that helps you create and apply these changes to your database.

- **Prisma Studio**: A web app that lets you view and edit your database data in a user-friendly way.

- **Relationships**: How different pieces of data are connected, like linking users to their posts.

- **Middleware**: Extra code you can add to run custom actions (like logging) whenever you access your database.

- **Introspection**: A way to automatically generate the data model from an existing database.

- **Deployment**: How you can set up Prisma to work with your app in different environments, like on the cloud or a local server.

### Initialize Prisma

```bash
npx prisma init
```
This will create a prisma directory with a schema.prisma file.

### Define Your Prisma Schema

Edit the ```prisma/schema.prisma``` file to define your data model:

```prisma
datasource db {
  provider = "sqlite" // Use SQLite for simplicity
  url      = "file:./dev.db"
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
  posts Post[]
}

model Post {
  id        Int    @id @default(autoincrement())
  title     String
  content   String
  userId    Int
  user      User   @relation(fields: [userId], references: [id])
}
```

### Migrate the Database

Now, run the following commands to create your SQLite database and apply the migration:
```bash
npx prisma migrate dev --name init
```

### Create an Express Server
Create an ```index.js``` file in the root directory:
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const { PrismaClient } = require('@prisma/client');

const app = express();
const prisma = new PrismaClient();

app.use(bodyParser.json());

// CRUD for Users

// Create a User
app.post('/users', async (req, res) => {
  const { name, email } = req.body;
  const user = await prisma.user.create({
    data: { name, email },
  });
  res.json(user);
});

// Read all Users
app.get('/users', async (req, res) => {
  const users = await prisma.user.findMany({
    include: { posts: true },
  });
  res.json(users);
});

// Update a User
app.put('/users/:id', async (req, res) => {
  const { id } = req.params;
  const { name, email } = req.body;
  const user = await prisma.user.update({
    where: { id: parseInt(id) },
    data: { name, email },
  });
  res.json(user);
});

// Delete a User
app.delete('/users/:id', async (req, res) => {
  const { id } = req.params;
  await prisma.user.delete({
    where: { id: parseInt(id) },
  });
  res.sendStatus(204);
});

// CRUD for Posts

// Create a Post
app.post('/posts', async (req, res) => {
  const { title, content, userId } = req.body;
  const post = await prisma.post.create({
    data: { title, content, userId },
  });
  res.json(post);
});

// Read all Posts
app.get('/posts', async (req, res) => {
  const posts = await prisma.post.findMany({
    include: { user: true },
  });
  res.json(posts);
});

// Update a Post
app.put('/posts/:id', async (req, res) => {
  const { id } = req.params;
  const { title, content } = req.body;
  const post = await prisma.post.update({
    where: { id: parseInt(id) },
    data: { title, content },
  });
  res.json(post);
});

// Delete a Post
app.delete('/posts/:id', async (req, res) => {
  const { id } = req.params;
  await prisma.post.delete({
    where: { id: parseInt(id) },
  });
  res.sendStatus(204);
});

// Start the server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```
