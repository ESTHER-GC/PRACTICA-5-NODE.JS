const MongoClient = require('mongodb').MongoClient;
const url = 'mongodb://localhost:27017';
const dbName = 'nodejs-mongo';
const client = new MongoClient(url, {useNewUrlParser: true});

client.connect(function (err) {
  if (err) {
  console.log ("Error al conectar al servidor: ", err);
  return;
}

console.log("Conectado con éxito al servidor");
const db = client.db(dbName);
client.close();

});
