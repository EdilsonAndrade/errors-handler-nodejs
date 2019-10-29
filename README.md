# Errors-handler-nodejs

Para poder monitorar os erros das aplicaçoes em produão, existe uma ferramenta o qual pode ser configurada no nodejs que monitora qualquer tipo de erro e demonstra as issues no site para serem tratadas

O site é https://sentry.io

Crie um projeto no site do tipo express / nodejs.

<img src="https://github.com/EdilsonAndrade/errors-handler-nodejs/blob/master/createprojectsentry.png" >
Crie uma conta gratuita, e após a criação da mesma é possível configurar conforme a seguir: 

<pre>
# Using yarn
$ yarn add @sentry/node@5.7.1
</pre>

Na classe que contém a inicialização do servidor express, inicialização da rota configure como a seguir
```javascript
import express from 'express';
import 'express-async-errors';
import mongoose from 'mongoose';
import path from 'path';
import Youch from 'youch';
import * as Sentry from '@sentry/node';
import sentryConfig from './config/sentry';

import routes from './routes';
import './database';

class App {
  constructor() {
    this.server = express();
    Sentry.init(sentryConfig);
    this.middlewares();
    this.routes();
    this.mongoose();
    this.exceptionHandlers();
  }

  middlewares() {
    this.server.use(Sentry.Handlers.requestHandler());
    this.server.use(express.json());
    this.server.use(
      '/files',
      express.static(path.resolve(__dirname, '..', 'tmp', 'uploads'))
    );
  }

  routes() {
    this.server.use(routes);
    this.server.use(Sentry.Handlers.errorHandler());
  }

  mongoose() {
    mongoose.connect('mongodb://localhost:27017', {
      useNewUrlParser: true,
      useFindAndModify: true,
      useUnifiedTopology: true,
    });
  }

  exceptionHandlers() {
    this.server.use(async (err, req, res, next) => {
      const errors = await new Youch(err, req).toJSON();
      return res.status(500).json(errors);
    });
  }
}

export default new App().server;

```

# Os erros são demonstrados conforme as imagens abaixo
<img src="https://github.com/EdilsonAndrade/errors-handler-nodejs/blob/master/sentryexample.png" >
<img src="https://github.com/EdilsonAndrade/errors-handler-nodejs/blob/master/sentry2.png" >

Instale o Youch para trazer no retorno da requisição também os dados completos de erro em formato JSON
<pre>
yarn add youch
</pre>
E utilize como a classe acima.

O express por default não consegue captar os erros com métodos async, para isto instale
<pre>
yarn add express-async-errors
</pre>

# Com o youch, o retorno dos erros em formato json ficam conforme a seguir:
```json
{
  "error": {
    "message": "_Appointment2.default.finddAll is not a function",
    "name": "TypeError",
    "frames": [
      {
        "file": "src/app/controllers/AppointmentController.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/src/app/controllers/AppointmentController.js",
        "method": "index",
        "line": 15,
        "column": 54,
        "context": {
          "start": 10,
          "pre": "\nclass AppointmentController {\n  async index(req, res) {\n    const { page, limit } = req.query;\n",
          "line": "    const appointments = await Appointment.finddAll({",
          "post": "      where: {\n        canceled_at: null,\n        user_id: req.userId,\n      },\n      limit,"
        },
        "isModule": false,
        "isNative": false,
        "isApp": true
      },
      {
        "file": "node_modules/express-async-errors/index.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express-async-errors/index.js",
        "method": "newFn",
        "line": 16,
        "column": 20,
        "context": {
          "start": 11,
          "pre": "  return newFn;\n}\n\nfunction wrap(fn) {\n  const newFn = function newFn(...args) {",
          "line": "    const ret = fn.apply(this, args);",
          "post": "    const next = (args.length === 5 ? args[2] : last(args)) || noop;\n    if (ret && ret.catch) ret.catch(err => next(err));\n    return ret;\n  };\n  Object.defineProperty(newFn, 'length', {"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/layer.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/layer.js",
        "method": "Layer.handle [as handle_request]",
        "line": 95,
        "column": 5,
        "context": {
          "start": 90,
          "pre": "    // not a standard request handler\n    return next();\n  }\n\n  try {",
          "line": "    fn(req, res, next);",
          "post": "  } catch (err) {\n    next(err);\n  }\n};\n"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/route.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/route.js",
        "method": "next",
        "line": 137,
        "column": 13,
        "context": {
          "start": 132,
          "pre": "    }\n\n    if (err) {\n      layer.handle_error(err, req, res, next);\n    } else {",
          "line": "      layer.handle_request(req, res, next);",
          "post": "    }\n  }\n};\n\n/**"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/route.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/route.js",
        "method": "Route.dispatch",
        "line": 112,
        "column": 3,
        "context": {
          "start": 107,
          "pre": "    method = 'get';\n  }\n\n  req.route = this;\n",
          "line": "  next();",
          "post": "\n  function next(err) {\n    // signal to exit route\n    if (err && err === 'route') {\n      return done();"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express-async-errors/index.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express-async-errors/index.js",
        "method": "newFn",
        "line": 16,
        "column": 20,
        "context": {
          "start": 11,
          "pre": "  return newFn;\n}\n\nfunction wrap(fn) {\n  const newFn = function newFn(...args) {",
          "line": "    const ret = fn.apply(this, args);",
          "post": "    const next = (args.length === 5 ? args[2] : last(args)) || noop;\n    if (ret && ret.catch) ret.catch(err => next(err));\n    return ret;\n  };\n  Object.defineProperty(newFn, 'length', {"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/layer.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/layer.js",
        "method": "Layer.handle [as handle_request]",
        "line": 95,
        "column": 5,
        "context": {
          "start": 90,
          "pre": "    // not a standard request handler\n    return next();\n  }\n\n  try {",
          "line": "    fn(req, res, next);",
          "post": "  } catch (err) {\n    next(err);\n  }\n};\n"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/index.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/index.js",
        "method": null,
        "line": 281,
        "column": 22,
        "context": {
          "start": 276,
          "pre": "      if (err) {\n        return next(layerError || err);\n      }\n\n      if (route) {",
          "line": "        return layer.handle_request(req, res, next);",
          "post": "      }\n\n      trim_prefix(layer, layerError, layerPath, path);\n    });\n  }"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/index.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/index.js",
        "method": "Function.process_params",
        "line": 335,
        "column": 12,
        "context": {
          "start": 330,
          "pre": "  // captured parameters from the layer, keys and values\n  var keys = layer.keys;\n\n  // fast track\n  if (!keys || keys.length === 0) {",
          "line": "    return done();",
          "post": "  }\n\n  var i = 0;\n  var name;\n  var paramIndex = 0;"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "node_modules/express/lib/router/index.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/node_modules/express/lib/router/index.js",
        "method": "next",
        "line": 275,
        "column": 10,
        "context": {
          "start": 270,
          "pre": "      ? mergeParams(layer.params, parentParams)\n      : layer.params;\n    var layerPath = layer.path;\n\n    // this should be done for the layer",
          "line": "    self.process_params(layer, paramcalled, req, res, function (err) {",
          "post": "      if (err) {\n        return next(layerError || err);\n      }\n\n      if (route) {"
        },
        "isModule": true,
        "isNative": false,
        "isApp": false
      },
      {
        "file": "src/app/middlewares/auth.js",
        "filePath": "/home/edilson/projetos/bootcamp9/gobarber-backend/src/app/middlewares/auth.js",
        "method": "exports.default",
        "line": 18,
        "column": 12,
        "context": {
          "start": 13,
          "pre": "    const decoder = await promisify(jwt.verify)(token, auth.secret);\n    if (!decoder) {\n      return res.status(401).json({ message: 'User not verified' });\n    }\n    req.userId = decoder.id;",
          "line": "    return next();",
          "post": "  } catch (error) {\n    return res.status(500).json({ error: `Error occured ${error}` });\n  }\n};\n"
        },
        "isModule": false,
        "isNative": false,
        "isApp": true
      },
      {
        "file": "internal/process/task_queues.js",
        "filePath": "internal/process/task_queues.js",
        "method": "processTicksAndRejections",
        "line": 93,
        "column": 5,
        "context": {},
        "isModule": false,
        "isNative": true,
        "isApp": false
      }
    ]
  }
}
```

Divirta-se e tenha controle de erros em sua mão :-)
