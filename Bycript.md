# Bycript

 **Bcrypt is used here to securely hash the user's password before storing it in the database, protecting it from being exposed if the database is compromised**.
**Register Phase**
- Let's get the password from ```const {password} = req.body```
- Need to generate a Salt ```javascript const salt = await bcrypt.genSalt(10);```
  - What it does: This line generates a salt, which is a random value added to the password before hashing.
  - Why it's needed: The salt ensures that even if two users have the same password, their hashed passwords will be different because of the unique salt.
  - Parameter: The 10 represents the cost factor, which controls the computational complexity of the hashing process. A higher number means more security but requires more computing power.
```javascript
 // Hash the password
    const salt = await bcrypt.genSalt(10);
    const hashedPassword = await bcrypt.hash(password, salt);
```

**Login Phase**
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
