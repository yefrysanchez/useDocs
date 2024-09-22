# Mongoose

- Start by creating a .env variable called ```MONGODB_IRL = your-database-IRL```

- To connect MongoDB using Mongoose

```javascript
const MONGODB_IRL = process.env.MONGODB_IRL;

mongoose
  .connect(MONGODB_IRL)
  .then(() => console.log("MongoDB connected"))
  .catch((err) => console.log(err));
```

- Example of how to create a Schema in Mongoose
```javascript
const mongoose = require("mongoose") ;

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    require: true,
    unique: true,
    trim: true,
  },
  email: {
    type: String,
    required: true,
    unique: true,
    trim: true,
    lowercase: true,
  },
  password: {
    type: String,
    required: true,
  },
});

const User = mongoose.model("User", userSchema);

module.exports = User;

```

* Remember that "User" in ```const User = mongoose.model("User", userSchema);``` will be plural in the DB("Users") no need to type it in plural.
