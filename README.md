# express-typescript
####Dependencias

    yarn init
    yarn add typescript express @types/express morgan @types/morgan
    yarn add ts-node-dev --dev

#### Scripts package.json

	"scripts": { 
      "dev": "ts-node-dev --respawn --transpileOnly ./src/servidor.ts",
      "build": "tsc",
	  "start": "node ./dist/servidor.js"
	  },

#### Configuracion tsconfig.json

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
#### src/servidor.ts
	import app from './app'
	import * as http from 'http'

	const normalizePort = (val:any) => {
	    let port = parseInt(val, 10);
	    if(isNaN(port)){
		return val
	    }
	    if (port >= 0){
		return port;
	    }
	    return false;
	};

	const onError = (error: any) => {
	    if(error.syscall !== 'listen'){
		throw error;
	    }
	    let bind = typeof PORT === 'string' ? 'Pipe ' + PORT : 'Port ' + PORT;

	    switch (error.code) {
		case 'EACCES':
		    console.error(bind + ' requires elevated privileges');
		    process.exit(1);
		    break;
		case 'EADDRINUSE':
		    console.error(bind + ' is already in use');
		    process.exit(1);
		    break;
		default:
		    throw error;
	    }
	};

	const onListening = () => {
	    let addr = server.address();
	    let bind = typeof addr === 'string'
		? 'pipe ' + addr
		: 'port ' + addr.port;
	    console.log('Listening on ' + bind);
	};

	const PORT = normalizePort(process.env.PORT || 3000);

	app.set('port', PORT);

	const server = http.createServer(app);
	server.listen(PORT);
	server.on('error', onError);
	server.on('listening', onListening);
