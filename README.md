Swagger Koa Validator
=========================


`swagger-koa-validator` is a lightweight middleware for request/response validation based on
[OpenAPI v2.0](http://swagger.io/specification/) (aka swagger) specification.

The main difference of this package to alternatives like
[swagger-tools](https://github.com/apigee-127/swagger-tools) is that this package is very
configurable and only concentrates on validation against provided schema. You can choose the
behavior of invalid validation like returning a 500 or just logging an error to your logger.

## Requirements
- koa ^2.0.0
- body-parser ^1.0.0

## Features
* Configurable validation behavior
* [Fastest](https://github.com/ebdrup/json-schema-benchmark) available JSON schema validation based on [ajv](https://github.com/epoberezkin/ajv) library
* Optional validations for either request parameters or response values
* Independent from express application structure. This is a simple drop-in middleware without additional
 alterations to your swagger definitions or application routing.
* Support for nullable field validation though `x-nullable` attribute

## Installation
Start using this library with `npm install swagger-koa-validator --save`

## Sample use
To set up simple validation for your requests and responses:
```javascript
const util = require('util');
const koa = require('koa');
const bodyParser = require('body-parser');
const validator = require('swagger-koa-validator');
const schema = require('./api-schema.json');

const server = koa();
server.use(bodyParser.json());

const opts = {
  schema, // Swagger schema
  preserveResponseContentType: false, // Do not override responses for validation errors to always be JSON, default is true
  returnRequestErrors: true, // Include list of request validation errors with response, default is false
  returnResponseErrors: true, // Include list of response validation errors with response, default is false
  validateRequest: true,
  validateResponse: true,
  requestValidationFn: (req, data, errors) => {
    console.log(`failed request validation: ${req.method} ${req.originalUrl}\n ${util.inspect(errors)}`)
  },
  responseValidationFn: (req, data, errors) => {
    console.log(`failed response validation: ${req.method} ${req.originalUrl}\n ${util.inspect(errors)}`)
  },
};
server.use(validator(opts));

server.use('/status', (req, res) => {
  res.json({
    status: 'OK',
  })
});
server.use((err, req, res, next) => {
  res.status(500);
  res.json(err);
});

return server.listen(3000);

```


## Contributors
- [Nazar Gargol](https://github.com/gargol)
- [Isaac Stefanek](https://github.com/iadknet)
- [James Gregory](https://github.com/jagregory)
- [Igor Savin](https://github.com/kibertoad)
- [Danila Plee](https://github.com/danilaplee)

## Licence
[MIT](https://github.com/gargol/swagger-koa-validator/blob/master/LICENSE)

