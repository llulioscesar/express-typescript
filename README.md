# express-typescript
### #Dependencias

    yarn init
    yarn add typescript express @types/express morgan @types/morgan http-errors @types/http-errors pug @types/pug
    yarn add ts-node-dev --dev

#### Scripts package.json

	"scripts": { 
		"dev": "ts-node-dev --respawn --transpileOnly ./src/servidor.ts",
		"build": "tsc",
		"start": "NODE_ENV=production node ./dist/servidor.js",
		"start_windows": "set NODE_ENV=production && node ./dist/servidor.js"
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
	import * as path from 'path'
	import * as express from 'express'
	import * as logger from 'morgan'
	import {Rutas} from './rutas'

	class App {
	    public app:express.Application = express();

	    constructor(){
		this.configuracion();
	    }

	    private configuracion():void{
		let ruta = __dirname.substring(0, __dirname.length - 4);
		this.app.set('views', path.join(ruta, 'vistas'));
		this.app.set('view engine', 'pug');
		this.app.use(logger('dev'));
		this.app.use(express.json());
		this.app.use(express.urlencoded({extended:false}));
		this.app.use(express.static(path.join(ruta, 'publico')));

		Rutas.map(this.app);

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
		});
	    }
	}

	export default new App().app;
#### src/rutas/index.ts
	import {PrincipalRuta} from './principal'

	export class Rutas {

	    public static map(app:any):void{
		new PrincipalRuta(app, '/').start();
	    }

	}
#### src/rutas/principal.ts
	import {Request, Response} from "express";
	import {Ruta} from './ruta'

	export class PrincipalRuta extends Ruta{
	    public start():void{
		this.app.route(this.endPoint)
		    .get((req:Request, res:Response) => {
			res.send('Express TypeScript')
		    })
	    }
	}
#### src/rutas/ruta.ts
	export abstract class Ruta {
	    protected app:any;
	    protected endPoint:string;
	    public constructor(app:any, endpoint:string) {
		this.app = app;
		this.endPoint = endpoint;
	    }
	}
#### src/vistas/layout.pug
	doctype html
	html
	    head
		title= title
		link(rel='stylesheet', href='/stylesheets/style.css')
	    body
		block content
#### src/vistas/error.pug
	extends layout

	block content
	    h1= message
	    h2= error.status
	    pre #{error.stack}
