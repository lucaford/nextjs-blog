---
title: "Crear DApp en Algorand con Javascript: generar transacciones, NFTs y Tokens."
date: "2022-04-04"
description: "Funciones básicas para introducirse en el mundo de las DApps con la blockchain de Algorand. Generar cuentas, transacciones, NFT's y tokens con javascript."
---

En los últimos meses [$ALGO](https://coinmarketcap.com/es/currencies/algorand/) tomo mucha fuerza como cryptomoneda debido a la especulación de los traders y más allá de que no podemos saber a ciencia cierta como resultará no podemos negar que Algorand es una infraestructura blockchain sobre la que se están construyendo proyectos de gran impacto como [ePioneers](https://epioneer.io/). Algorand se jacta de ser diferente a las demás debido a que busca resolver el [trilemma blockchain](https://www.ledger.com/academy/what-is-the-blockchain-trilemma) a través de su protocolo de consenso [**PPoS (Pure Proof of Stake).**](https://www.algorand.com/technology/pure-proof-of-stake) A diferencia de blockchains conocidas como Ethereum o Bitcoin que todavía utilizan el protocolo PoW, con PPoS no se requiere resolver puzzles complejos para lograr minar por ende el gasto de computo es muy reducido y en consecuencia se puede decir que es una solución sustentable en comparación con las ya existentes.
El equipo de Algorand logró construir una solución built-in muy simple para que los desarrolladores puedan integrar diversos métodos como enviar transacciones o crear NFT's sin tener que crear smart contracts. Esto nos va a permitir crear soluciones rápidas y así solo preocuparnos por solucionar el problema que queremos resolver. Solo vamos a necesitar algunos requisitos previos antes de comenzar a desarrollar. En el siguiente [repositorio de github](https://github.com/lucaford/algorand-javascript-sdk-playground) pueden encontrar un pequeño CLI ya armado donde vamos a poder probar cada una de estas funciones.
Vale destacar que todo lo que veremos a continuación esta muy bien detallado (y mejor de lo que yo podría describirla) en la [documentación del SDK de Javascript de Algorand](https://developer.algorand.org/docs/sdks/javascript/)

# Entorno de desarrollo

Los requisitos para comenzar a jugar con algorand varían según el ambiente que utilices y el lenguaje al que quieras integrar sus métodos. Necesitaremos **instanciar un nodo** utilizando la red de test de algorand (testnet). Esto nos permitirá jugar con todos los métodos que nos brinda el SDK sobre una red de testing sin tener que gastar $ALGOs o ejecutar acciones en producción. Para eso vamos a ir por la forma más sencilla y utilizar el sandbox que nos brinda el equipo de desarrollo. Para instalarlo haremos todo lo siguiente:

## Node

Nosotros nos enfocaremos puramente en el SDK de JavaScript (AlgoSDK). Necesitaremos instalar el entorno de node para crear nuestra aplicación. No necesitamos una versión especifica así que podemos ir directamente por la [LTS](https://nodejs.org/es/).

## Docker

El [sandbox](https://github.com/algorand/sandbox) viene preparado dentro de un container para que no tengamos que preocuparnos por el versionado de nuestros programas o por alguna incompatibilidad con nuestro OS, así que primero instalaremos docker desde [**su página oficial**](https://www.docker.com/get-started/) que es el software que nos permitirá levantar el container. Para verificar que se haya instalado correctamente es recomendable crear la [app de pruebas](https://docs.docker.com/get-started/02_our_app/).

## Sandbox

Por último necesitaremos levantar el [sandbox de algorand](https://github.com/algorand/sandbox.git). Clonaremos el proyecto e iniciaremos el setup automático. La primera vez va a tardar unos minutos así que al final del comando pueden agregar el parámetro "-v" para visualizar que procesos están corriendo (y así estar menos ansiosos)

```js

git clone https://github.com/algorand/sandbox.git
cd sandbox
./sandbox up testnet -v

```

Y listo! Ya tienen el **entorno de desarrollo de algorand** para hacer todo lo que necesiten. [!] Hay que acordarse de siempre tener corriendo el sandbox si queremos realizar pruebas con nuestra app.

# Funciones Algorand

Comenzaremos con un setup muy simple donde veremos que cada función cumpla lo requerido y luego al final terminaremos utilizando cada una de ellas. Empecemos.

![](https://media.giphy.com/media/BpGWitbFZflfSUYuZ9/giphy.gif)

Primero crearemos una carpeta y dentro de ella inicializaremos el package correspondiente.

```

mkdir algorand-javascript-sdk
cd algorand-javascript-sdk
npm init -y

```

Dentro crearemos un archivo index.js

`touch index.js`

Luego instalaremos algoSDK y listo, nuestra aplicación ya esta lista para interactuar con la blockchain de algorand.

`npm install algosdk`

## Crear cuenta

Antes que nada debemos crear una cuenta, y luego fondearla para poder interactuar con la blockchain de algorand. Importaremos algosdk en nuestro index.js, crearemos una función llamada createAccount() y luego la exportaremos con module.exports = { createAccount } para poder llamarla desde nuestra consola. Index.js te debería quedar algo así

```

const algosdk = require('algosdk');
const createAccount = () => {};
module.exports = {
createAccount
}

```

Dentro vamos a utilizar la función **generateAccount()** de algosdk, la cual creara una cuenta en la blockchain y nos devolverá un objeto que contiene los datos de esta misma. Haremos un console.log() del objeto en cuestión para poder ver toda los datos que nos arroja.

```

const createAccount = () => {
const account = algosdk.generateAccount();
console.log("Account: ", account)
};

```

Allí vamos a poder ver un montón de datos pero de momento los que mas nos interesan son la Address y la Secret Key. Pero ojo! Porqué nosotros necesitamos convertir el hash de la Secret Key en una frase mnemotécnica para poder anotarla. Esto lo haremos con el método que nos provee algosdk llamado secretKeyToMnemonic(secretKey) que nos devolverá una frase de 24 palabras. También lo mostraremos por consola ya que lo necesitaremos luego para realizar las transacciones de prueba.

```

const createAccount = () => {
const account = algosdk.generateAccount();
console.log("Account: ", account)
let mnemonic = algosdk.secretKeyToMnemonic(account.sk);
console.log("Mnemonic: ", mnemonic);
};

```

Ahora vamos a probar el programa. En la consola arrojaremos el siguiente comando de node que nos permitirá triggerear la función que estamos exportando

`node -e "require('index').createAccount()"`

Ya con la cuenta creada veremos que en la consola se muestran todos los datos que necesitamos. **Copia el Address y la mnemonic!** Si se pierde se puede crear una cuenta nueva.
Ahora tendremos que fondear nuestra cuenta. Para eso iremos a la siguiente página, ingresaremos nuestra address en el input y le daremos a "DISPENSE". Al cabo de unos segundos ya tendremos depositados 5 ALGOS en nuestra cuenta de test.
Todas las transacciones que se realizan en todas las blockchains son publicas y esta no es la excepción. Todo lo que hagamos lo podremos ver en el [explorer de la red de test](https://testnet.algoexplorer.io/). Si ingresamos nuestra address vamos a poder observar el detalle de la transacción de 5 ALGOS que nos acaban de hacer desde la address que utiliza la testnet de algorand para fondear cuentas.
Y listo! Ya tenemos una cuenta con 5 ALGOs en la red de test de algorand.

![](https://media.giphy.com/media/b229GDfmrp98Q8cfgS/giphy.gif)

## Información de la cuenta

A la hora de realizar una transacción, comprar un asset, vender, o realizar cualquier interacción entre una address y otra va a ser importante que nosotros podamos obtener información de esa cuenta. Para eso Algorand nos provee la función llamada **accountInformation(address)** que nos devuelve la información pública de esa address especifica. A diferencia de la creación de cuenta vamos a tener que conectarnos al cliente de Algorand y desde ahí llamar a la función. A partir de ahora vamos a utilizar este cliente para todas las funciones relacionadas a obtener información de una address, crear una transacción, etc … Debido a que estamos usando una testnet los valores que debemos ingresar son los locales.

```

const algodToken = "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa";
const algodServer = "http://localhost";
const algodPort = 4001;
const algodClient = new algosdk.Algodv2(algodToken, algodServer, algodPort);

```

De esta forma vamos a tener instanciado el cliente en el objeto **algodClient** ya disponible para usar las funciones que necesitemos. Ahora que ya tenemos el cliente lo único que nos queda hacer es llamar a la función.

```

const accountInfo = await algodClient.accountInformation(addr).do();
console.log("Account information: ", accountInfo)

```

Y listo! Tenemos la información necesaria para efectuar todas las acciones que queramos realizar de ahora en adelante.

## Primera transacción

Ahora vamos a crear una transacción en la blockchain de Algorand. La haremos desde la address de la cuenta que creamos hacía otra address de la testnet que nos proveen los desarrolladores así devolvemos los ALGOs que nos prestaron para realizar las pruebas.
La transacción puede ser modificada utilizando diferente parámetros. Podemos hasta definir un fee que no sea el **default de algorand (0.001 ALGOs)** o hasta agregar una nota para el receiver. De momento nosotros utilizaremos los valores por default y cualquier cosa desde [la documentación](https://developer.algorand.org/docs/sdks/javascript/#submit-the-transaction) se pueden ver los adicionales. Un dato importante a tener en cuenta es que el monto a enviar esta determinado en **MicroAlgos** y no Algos. Así que enviaremos **1000000 MicroAlgos que equivalen a 1 ALGO.** (1000 MicroAlgos === 0.001 Algo).
Primero obtendremos los parámetros sugeridos por el SDK utilizando el cliente que ya generamos anteriormente, luego agregamos los parámetros custom necesarios para realizarla y al final construiremos el objeto transacción utilizando el método **makePaymentTxnWithSuggestedParamsFromObject().**

```

let transactionParams = await algodClient.getTransactionParams().do();
const receiver = "HZ57J3K46JIJXILONBBZOHX6BKPXEM2VVXNRFSUED6DKFD5ZD24PMJ3MVA";
const enc = new TextEncoder();
const note = enc.encode("Nota al receiver");
let amount = 1000000; // equals 1 ALGO
let sender = myAccount.addr;
let txn = algosdk.makePaymentTxnWithSuggestedParamsFromObject({
from: sender,
to: receiver,
amount: amount,
note: note,
suggestedParams: transactionParams
});

```

Ahora debemos **firmar** la transacción utilizando nuestra private key. En este caso necesitaremos la Secret Key y es probable que ya tengamos el mnemonic de nuestra cuenta. En este caso podemos utilizar la función **mnemonicToSecretKey(mnemonic)** del objeto algoSdk (no el cliente) y nos devolverá la SK de la cuenta.

```

const SK = algoSdk.mnemonicToSecretKey(mnemonic)
let signedTxn = txn.signTxn(myAccount.sk);
let txId = txn.txID().toString();

```

Ahora que ya firmamos la transacción solo nos queda enviarla a la blockchain! Primero vamos a enviarla con la función **sendRawTransaction()** y luego aguardaremos por la confirmación con **waitForConfirmation()**. Una vez hecho esto haremos un console.log() de la transacción confirmada.

```

await algodClient.sendRawTransaction(signedTxn).do();
let confirmedTxn = await algosdk.waitForConfirmation(algodClient, txId, 4);
console.log("Transaction " + txId + " confirmed in round " + confirmedTxn["confirmed-round"]);

```

Y listo! Ya realizamos nuestra primera transacción en la blockchain de Algorand. Todo utilizando las funciones built-in, sin tener que crear ningún smart contract. Para ver más información de la transacción en particular podemos ver directamente [la documentación](https://developer.algorand.org/docs/sdks/javascript/#submit-the-transaction).

## Crear NFT

En algorand los NFT's se crean usando **Algorand Standar Assets (ASAs)**. En otras blockchains es necesario generar un smart contract para representar un NFT pero en Algorand lo creamos con una solución ya construida dentro del protocolo y termina siendo una transacción más pero con ciertos parámetros específicos que son los que determinan si es una transacción normal, un NFT o un Token. Es un poco confuso pero en esta [documentación](https://developer.algorand.org/docs/get-started/tokenization/nft/) tienen una explicación detallada de que es un NFT y cómo se construyen en la blockchain. Antes de comenzar a desarrollar también recomiendo continuar la lectura hacía qué es un [Functional Token (FT)](https://developer.algorand.org/docs/get-started/tokenization/ft/) ya que nos va a quedar mucho más en claro como funcionan este tipo de transacciones en Algorand (y en general). So far so good.

![](https://media.giphy.com/media/pzTDF5eeYBVUO6Sgyf/giphy.gif)

Primero vamos a definir ciertos parámetros que van a determinar qué NFT crearemos. En este caso los más importantes son el address del creador, el nombre de la colección, el nombre del asset (NFT), la URL de la metadata del NFT en formato JSON [(ejemplo)](https://replit.com/@Algorand/CreateNFTJavaScript#NFT/metadata.json) y el total de NFT's que vamos a emitir. En este caso el total lo vamos a querer tener en exactamente 1 así solo generamos un original de este NFT. Y cómo no queremos fraccionar el asset (concepto un poco más avanzado de minting) vamos a dejar ese parámetro en 0. En este caso estamos usando la **convención estándar de Algorand para crear assets [ARC3](https://github.com/algorandfoundation/ARCs/blob/main/ARCs/arc-0003.md)**. Parámetros como managerAddress, reserve, freeze y clawback nos sirven para darle permisos a ciertas direcciones sobre nuestro asset como por ejemplo para destruirlo. Por este propósito lo dejaremos en undefined.

```

const creator = myAccount.address;
const defaultFrozen = false;
const unitName = "CRYPTOARTE";
const assetName = "Mi NFT@arc3";
const url = "https://path/to/my/nft/asset/metadata.json";
const managerAddr = undefined;
const reserveAddr = undefined;
const freezeAddr = undefined;
const clawbackAddr = undefined;
const total = 1;
const decimals = 0;

```

Una vez definido estos parámetros vamos a instanciar la transacción como lo hicimos la primera vez. Al ser un tipo de transacción vamos a necesitar utilizar lo que vimos anteriormente. Primero obtendremos los parámetros por default de Algorand que obtuvimos con el método **getTransactionParams()**, luego firmarla, enviarla y esperar su confirmación.

```

let transactionParams = await algodClient.getTransactionParams().do();
const txn = algosdk.makeAssetCreateTxnWithSuggestedParamsFromObject({
from:creator,
total,
decimals,
assetName,
unitName,
assetURL: url,
assetMetadataHash: metadata,
defaultFrozen,
freeze: freezeAddr,
manager: managerAddr,
clawback: clawbackAddr,
reserve: reserveAddr,
suggestedParams: transactionParams,
});
const SK = algoSdk.mnemonicToSecretKey(mnemonic)
let signedTxn = txn.signTxn(myAccount.sk);
let txId = txn.txID().toString();
await algodClient.sendRawTransaction(signedTxn).do();
let confirmedTxn = await algosdk.waitForConfirmation(algodClient, txId, 4);
console.log("Transaction " + txId + " confirmed in round " + confirmedTxn["confirmed-round"]);

```

Y listo! creamos nuestro primer NFT en la blockchain de Algorand.

## Crear Token

Ahora vamos a crear un token (Fungible Token - TK) en la red de Algorand. En este caso debemos seguir exactamente los mismos pasos que al crear un Non-Fungible Token solo que vamos a darle como valor de la cantidad a crear mayor a 1 y vamos a agregar 2 decimales… De esta forma nuestra transacción de tipo NFT pasará a ser un FT ya que existen más de uno(viéndolo de forma muy simple). Así de sencillo es crear un Token con configuraciones básicas! Debemos seguir los mismos pasos de firmar la transacción y confirmarla al igual que con el NFT.

```

const creator = myAccount.address;
const defaultFrozen = false;
const unitName = "COIN";
const assetName = "My Coin@arc3";
const url = "https://path/to/my/fungible/asset/metadata.json";
const managerAddr = undefined;
const reserveAddr = undefined;
const freezeAddr = undefined;
const clawbackAddr = undefined;
const total = 10000;
const decimals = 2;
let transactionParams = await algodClient.getTransactionParams().do();
const txn = algosdk.makeAssetCreateTxnWithSuggestedParamsFromObject({
creator,
total,
decimals,
assetName,
unitName,
assetURL: url,
assetMetadataHash: metadata,
defaultFrozen,
freeze: freezeAddr,
manager: managerAddr,
clawback: clawbackAddr,
reserve: reserveAddr,
suggestedParams: transactionParams
});
const SK = algoSdk.mnemonicToSecretKey(mnemonic)
let signedTxn = txn.signTxn(myAccount.sk);
let txId = txn.txID().toString();
await algodClient.sendRawTransaction(signedTxn).do();
let confirmedTxn = await algosdk.waitForConfirmation(algodClient, txId, 4);
console.log("Transaction " + txId + " confirmed in round " + confirmedTxn["confirmed-round"]);

```

[!] Es importante destacar que, al igual que con los NFT's, el propósito del código es probarlo en la testnet. En caso de querer lanzar a producción un token es NECESARIO entender en profundidad qué es lo que estamos haciendo y que significa cada parámetro que estamos configurando ya que una vez que la transacción se ejecuta en la blockchain NO se puede modificar tan fácilmente.

---

Estos métodos no serán suficientes para crear una wallet o lanzar un marketplace de NFT's a producción pero los protocolos cada vez se acercan más a que eso sea posible sin tener que enroscarnos en crear smart contracts, contratar auditorias, etc. Existe una basta cantidad de blockchains, no solo algorand, con soluciones built-in dependiendo lo que queramos hacer. Hasta soluciones no-code como los productos que stripe esta lanzando al mercado. Solo es cuestión de investigar y perderse en un rabbit-hole de información hasta que encontremos lo que queramos. Creo que hacer es la mejor forma de aprender y entender cómo funcionan todos estos procesos que están lejos de ser sencillos. Por suerte hay gente mucho más inteligente que yo, como Silvio Macali, que su trabajo logra que nosotros solo tengamos que enfocarnos en la problemática que queremos resolver.

---

¿Dudas? **hablemos** por twitter -  https://twitter.com/lfordev

```

```

```

```
