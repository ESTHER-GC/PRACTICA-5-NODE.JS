const http = require('http');
const querystring = require('querystring');
const port = 3000;
const MongoClient = require('mongodb').MongoClient;
const url = 'mongodb://localhost:27017';
const dbName = 'nodejs-mongo';
const client = new MongoClient(url, { useNewUrlParser: true });

const server = http.createServer((req, res) => {

  const { headers, method, url } = req;
  console.log('headers: ', headers);
  console.log('method: ', method);
  console.log('url: ', url);
  let datos_recibidos = [];

  req.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    datos_recibidos.push(chunk);
  }).on('end', () => {

    datos_recibidos = Buffer.concat(datos_recibidos).toString();
    console.log('datos_recibidos: ', datos_recibidos);

    if(req.method == 'GET'){

      client.connect().then(async () => {
    
        console.log("Conectado con éxito al servidor");
        const db = client.db(dbName);
        const collection = db.collection('usuarios');     
        const findResult = await collection.find({}).toArray();
        console.log("Documentos inicio: ", findResult);
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/html');
 
        for(let i = 0; i < findResult.length; i++){
          res.write(findResult[i].name+ ' ' + findResult[i].phone + "<br/>");        
        }
 
        res.end();
    
      }).catch((error) => {
    
        console.log("Se produjo algún error en las operaciones con la base de datos: " + error);
        client.close();
  
      });

    }

    else if (req.method == 'POST'){

      client.connect().then(async () => {
  
        console.log("Conectado con éxito al servidor");
        const db = client.db(dbName);
        const collection = db.collection('usuarios');
        const insertResult = await collection.insertOne(querystring.parse(datos_recibidos));
        console.log("Resultado de la inserción: ", insertResult.result);
        res.statusCode = 201;
        res.setHeader('Content-Type', 'text/html');
        res.write('Entrada creada:' + "<br/>");
        res.write('Hemos recibido el siguiente nombre: ' + querystring.parse(datos_recibidos).name + '');
        res.write(', con el siguiente telefono: ' + querystring.parse(datos_recibidos).phone);
        res.end();
    
      }).catch((error) => {
 
        res.statusCode = 400;
        console.log("Se produjo algún error en las operaciones con la base de datos: " + error);
        client.close();
  
      });
  
    }

    else {
        
      res.statusCode = 404;
      res.end();

    }
  
  })

});

server.listen(port, () => {

  console.log('Servidor ejecutándose...');
  console.log('Abrir en un navegador http://localhost:3000');

});
