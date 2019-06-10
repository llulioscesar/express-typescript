# express-typescript
### #Dependencias

    yarn init
    yarn add typescript express @types/express morgan @types/morgan http-errors @types/http-errors
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
#### src/app.ts
	import * as createError from 'http-errors'
	import * as express from 'express'
	import * as logger from 'morgan'
	import indexRuta from './rutas'

	class App {
	    public app:express.Application = express();

	    constructor(){
		this.configuracion();

		this.app.use('/', indexRuta);

	    }

	    private configuracion():void{
		this.app.use(logger('dev'));
		this.app.use(express.json());
		this.app.use(express.urlencoded({extended:false}));
		// catch 404 and forward to error handler
		this.app.use((req:any, res:any, next:any) => {
		    next(createError(404));
		});
		// error handler
		this.app.use((error:any, req:any, res:any, next:any):void => {
		    res.locals.message = error.message;
		    res.locals.error = req.app.get('env') === 'development' ? error : {};

		    // render the error page
		    res.status(error.status || 500);
		    res.render('error');
		})
	    }
	}

	export default new App().app;
#### src/rutas/index.ts
	import {Request, Response, Router} from 'express'
	var router = Router();

	router.get('/', (req:Request, res:Response) => {
	    res.send("Express TypeScript")
	});

	export default router;
