const http = require('http');
const port = 3000;

const server = http.createServer((request, response) => {

  const { headers, method, url } = request;
  console.log('headers: ', headers);
  console.log('method: ', method);
  console.log('url: ', url);
  let body = [];
  
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {

    body = Buffer.concat(body).toString();
    console.log('body: ', body);
    response.statusCode = 200;
    response.setHeader('Content-Type', 'text/plain');
    response.end('Hola Mundo');

  });

})

server.listen(port, () => {

  console.log('Servidor ejecutándose...');
  console.log('Abrir en un navegador http://localhost:3000');

});
