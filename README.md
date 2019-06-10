# express-typescript
####Dependencias

    yarn init
    yarn add typescript express @types/express
    yarn add ts-node-dev --dev

####Scripts package.json

	"scripts": { 
      "dev": "ts-node-dev --respawn --transpileOnly ./src/server.ts",
      "build": "tsc ./dist/server.js",
	  "start": "node ./dist/server.js"
	  },

####Configuracion tsconfig.json

	{
		"compilerOptions": {
			"module": "commonjs",
			"esModuleInterop": false,
			"target": "es6",
			"noImplicitAny": true,
			"moduleResolution": "node",
			"sourceMap": true,
			"outDir": "dist",
			"baseUrl": ".",
			"paths": {
				"*": [
					"node_modules/",
					"src/types/*"
				]
			}
		},
		"include": [
			"src/**/*"
		]
	}
