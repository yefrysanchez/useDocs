# Node / Express / Typescript 

- ## 1st Create a package.json
  - ```
    npm init
    ```
- ## 2st Install all the dependencies
  - ```
    npm i express dotenv typescript
    ``` 
  
- ## 3nd Install all the dependencies ( DO NOT forget @types/YourDependencies)
  - ```
    npm i ts-node-dev @types/express @types/node rimraf --save-dev
    ```
  - (ts-node-dev allows you to run typescript without compiling every single time and rimraf delete dist in every build)

- ## 4rd Init TSC
  - ```
    npx tsc --init --outDir dist/ --rootDir src
    ```
  - In the tsconfig.json file and on top outside of ``` "compilerOptions" ``` Add "exclude" and "include":
   ```javascript
    "exclude": ["node_modules, dist"],
    "include": ["src"],
    ```
- ## 5th Add scripts
  - create a src folder and a ts file called app.ts inside.
  - in package.json locate script and add
  ```javascript
  "dev": "tsnd --respawn --clear src/app.ts",
  "build": "rimraf ./dist && tsc",
  "start": "node dist/server.js"
  ```
 And just like that you have your Node / Express / Typescript project ready, Happy coding!
