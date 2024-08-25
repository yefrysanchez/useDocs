# inits

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

## And just like that you have your Node / Express / Typescript project ready, Happy coding!
