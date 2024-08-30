# inits
## Table of Contents
- [Node / Express / TypeScript](#node--express--typescript)
- [Mongoose](#mongoose)
-  [Bycript](#bycript)
-  [JsonWebToken](#jsonwebtoken)
-  [Redux Toolkit](#redux-toolkit)

## Node / Express / Typescript 
- ### 1st install all the dependencies
  - ``` npm i express dotenv typescript ``` 
  
- ### 2nd install all the dependencies ( DO NOT forget @types/YourDependencies)
  - ``` npm i ts-node-dev @types/express @types/node rimraf --save-dev ```  (ts-node-dev allows you to run typescript without compiling every single time and rimraf delete dist in every build)

- ### 3rd init TSC
  - ``` npx tsc --init --outDir dist/ --rootDir src ```
  - In the tsconfig.json file and on top outside of ``` "compilerOptions" ``` Add "exclude" and "include":
```javascript
"exclude": ["node_modules, dist"],
"include": ["src"],
```
- ### 4th Add scripts
  - create a src folder and a ts file called app.ts
  - in package.json locate script and add
```javascript
"dev": "tsnd --respawn --clear src/server.ts",
"build": "rimraf ./dist && tsc",
"start": "node dist/server.js"
```
 And just like that you have your Node / Express / Typescript project ready, Happy coding!

## Mongoose

- Start by creating a .env variable called ```MONGODB_IRL = your-database-IRL```

- To connect MongoDB using Mongoose

```javascript
const MONGODB_IRL: any = process.env.MONGODB_IRL;

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

* Remember that User in ```const User = mongoose.model("User", userSchema);``` will be plural in the DB(Users) no need to type it in plural.

___

## Bycript

### Bcrypt is used here to securely hash the user's password before storing it in the database, protecting it from being exposed if the database is compromised.
#### Register Phase
- Let's get the password from ```const {password = req.body}```
- Need to generate a Salt ```javascript const salt = await bcrypt.genSalt(10);```
  - What it does: This line generates a salt, which is a random value added to the password before hashing.
  - Why it's needed: The salt ensures that even if two users have the same password, their hashed passwords will be different because of the unique salt.
  - Parameter: The 10 represents the cost factor, which controls the computational complexity of the hashing process. A higher number means more security but requires more computing power.
```javascript
 // Hash the password
    const salt = await bcrypt.genSalt(10);
    const hashedPassword = await bcrypt.hash(password, salt);
```

#### Login Phase
- When login Bcrypt uses ```bcrypt.compare(password, user.password)``` to take the plain-text password and the hashed password, hashes the plain-text password with the same salt used in the hashed password, and checks if the result matches the stored hash.

```javascript
authRouter.post("/login", async (req: Request, res: Response) => {
  const { email, password } = req.body;

  try {
    // ///
    // rest of the code ///
   // ///
    const user = await User.findOne({ email });
    if (!user) return res.status(400).json({ msg: "Invalid credentials" });

    // Compare the provided password with the hashed password
    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) return res.status(400).send({ msg: "Invalid credentials" });
    // ///
    //rest of the code ///
    // //// 
  } catch (error: any) {
    res.status(500).send(error.message);
  }
});
```

___

## JsonWebToken

- Start by creating a .env variable called ```JWT_SECRET = secret-name```
  - The JWT secret key is like a special password used to lock and unlock a JWT (a kind of digital ID card).
  - When the JWT is created (jwt.sign), the secret key helps lock it up securely so nobody can change it without being noticed.
  - When someone presents the JWT later (jwt.verify), the secret key is used to unlock and check if it’s still valid and hasn’t been tampered with.

### Components of jwt.sign

- #### Payload:
  - The first argument to jwt.sign is the payload. The data you want to include in the token. It is usually an object containing user information or claims (e.g., { id: user._id }).
  - The payload can include any data you want, but avoid including sensitive information since it is encoded but not encrypted.

- #### Secret Key:
  - The second argument is the secret key (or private key) used to sign the token. This key is crucial for ensuring the token’s authenticity. The same key will be needed to verify the token later.
 
- #### Options:
  - Settings to customize the token, such as expiration time.

```javascript
authRouter.post("/login", async (req: Request, res: Response) => {
  const { email, password } = req.body;

  try {
    // ///
    //rest of the code ///
    // //// 
    // Generate a token       //Payload//    //Secre Key//   
    const token = jwt.sign({ id: user._id }, JWT_SECRET, { expiresIn: "1h" });
    res.json({ token });

  } catch (error: any) {
    res.status(500).send(error.message);
  }
});
```

### Components of jwt.verify

- #### token:
  - The JWT that you want to validate.

- #### Secret Key:
  - The key used to verify the token's signature.
  - Ensures that the token has not been altered and was signed with the expected key.
 
- #### Options (Optional):
   - Additional settings you can use to customize the verification process.
   - You can specify settings like token expiration, audience, issuer, etc.
   - Example: { algorithms: ['HS256'] } - This specifies the algorithm used for signing.
 

___

## Redux Toolkit

**Redux Toolkit** is a library designed to simplify working with Redux, a state management tool for JavaScript applications. 
It provides tools and best practices to make working with Redux easier and more efficient.

### Key Concepts

- **Store:** This is where your application’s state lives. It holds the entire state of your app in one central place.
- **Actions:** These are plain objects that describe an event or change in the application. For example, an action might be { type: 'ADD_TODO', payload: 'Buy groceries' }.
- **Reducers:** Functions that take the current state and an action and return a new state. They specify how the state changes in response to actions.
- **Slices:** Redux Toolkit introduces the concept of “slices.” A slice is a collection of reducers and actions for a specific part of your state. It helps organize and manage related pieces of state together.
- **createSlice:** This is a function provided by Redux Toolkit to create slices easily. It automatically generates action creators and action types based on the reducers you define.
- **configureStore:** This function sets up the Redux store with recommended defaults. It simplifies store configuration by automatically applying middleware for handling asynchronous logic and other best practices.
- **createAsyncThunk:** A utility to handle asynchronous actions. It simplifies the process of dispatching async operations and automatically dispatches corresponding lifecycle actions (pending, fulfilled, rejected).

1. **Create a Slice:** A slice represents a portion of the Redux state and the reducers to handle changes to that state.

```javascript
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
  },
});

export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;
```

2. **Configure the Store:** Set up the store using configureStore, and include the slices you created.

```javascript
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});

export default store;
```

3. **Provide the Store to Your Application:** Wrap your application with the Provider component from react-redux to make the Redux store available to all components.

```javascript
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import App from './App';
import store from './store'; // Import your store

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);

```

4. **Use the Store in Components:** Connect your React components to the Redux store using useSelector to read state and useDispatch to dispatch actions.

```javascript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './counterSlice';

const Counter = () => {
  const {value} = useSelector((state) => state.counter);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {value}</p>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
};

export default Counter;
```



Redux Toolkit streamlines the process of managing state in a Redux application by providing a more user-friendly API and sensible defaults.

___
