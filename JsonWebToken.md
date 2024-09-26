# JsonWebToken

- Start by creating a .env variable called ```JWT_SECRET = secret-name```
  - The JWT secret key is like a special password used to lock and unlock a JWT (a kind of digital ID card).
  - When the JWT is created (jwt.sign), the secret key helps lock it up securely so nobody can change it without being noticed.
  - When someone presents the JWT later (jwt.verify), the secret key is used to unlock and check if it’s still valid and hasn’t been tampered with.

## Components of jwt.sign

-  **Payload**:
  - The first argument to jwt.sign is the payload. The data you want to include in the token. It is usually an object containing user information or claims (e.g., { id: user._id }).
  - The payload can include any data you want, but avoid including sensitive information since it is encoded but not encrypted.

- **Secret Key**:
  - The second argument is the secret key (or private key) used to sign the token. This key is crucial for ensuring the token’s authenticity. The same key will be needed to verify the token later.
 
- **Options**:
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

## Components of jwt.verify

- **token**:
  - The JWT that you want to validate.

- **Secret Key**:
  - The key used to verify the token's signature.
  - Ensures that the token has not been altered and was signed with the expected key.
 
- **Options (Optional)**:
   - Additional settings you can use to customize the verification process.
   - You can specify settings like token expiration, audience, issuer, etc.
   - Example: { algorithms: ['HS256'] } - This specifies the algorithm used for signing.

 ```javascript
// Middleware to verify token
authRouter.post("/verify-token", (req: Request, res: Response) => {
  const token = req.body.token; // Assuming the token is sent in the request body

  if (!token) {
    return res.status(403).send('Token is required.');
  }

  jwt.verify(token, JWT_SECRET, (err, decoded) => {
    if (err) {
      return res.status(401).send('Invalid token.');
    }

    // If token is valid, you can use decoded information
    res.json({ message: 'Token is valid', userId: decoded.id });
  });
});
```

**Explanation**:

- **Token Retrieval**: The token is expected to be sent in the request body.
- **Token Check**: If no token is provided, a 403 error is returned.
- **Verification**: The jwt.verify method checks the validity of the token.
- **Response Handling**: If the token is valid, a success message is sent back along with the user ID extracted from the token.
