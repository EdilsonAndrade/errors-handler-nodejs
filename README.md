# Errors-handler-nodejs

Para poder monitorar os erros das aplicaçoes em produão, existe uma ferramenta o qual pode ser configurada no nodejs que monitora qualquer tipo de erro e demonstra as issues no site para serem tratadas

O site é https://sentry.io

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

Instale o Youch para trazer no retorno da requisição também os dados completos de erro em formato JSON
<pre>
yarn add youch
</pre>
E utilize como a classe acima.

O express por default não consegue captar os erros com métodos async, para isto instale
<pre>
yarn add express-async-errors
</pre>

