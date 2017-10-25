---
title: Proteger uma API Web do Azure Active Directory v2.0 usando o Node.js | Microsoft Docs
description: Aprenda a compilar uma API Web Node.js que aceita tokens de contas da Microsoft pessoais e de contas corporativas ou de estudante.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 94e945a52b9df7c495de1611baa08083357670c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Proteger uma API Web usando Node.js
> [!NOTE]
> Nem todos os cenários e recursos do Azure Active Directory funcionam com o ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0 ou v1.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

Quando você usa o ponto de extremidade do Azure AD (Azure Active Directory) v2.0, você pode usar os tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md) para proteger sua API Web. Com os tokens de acesso OAuth 2.0, os usuários que têm uma conta da Microsoft pessoal e contas corporativas ou de estudante podem acessar sua API Web com segurança.

*Passport* é middleware de autenticação para o Node.js. Flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. No Passport, um conjunto abrangente de estratégias dão suporte à autenticação com o uso de um nome de usuário e uma senha, Facebook, Twitter e outras opções. Desenvolvemos uma estratégia para o Azure AD. Neste artigo, mostramos como instalar o módulo e, em seguida, adicione o plug-in `passport-azure-ad` do Azure AD.

## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Para seguir o tutorial, [baixe o esqueleto do aplicativo como um arquivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) ou clone o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Também obtenha o aplicativo concluído ao final deste tutorial.

## <a name="1-register-an-app"></a>1: registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga [estas etapas detalhadas](active-directory-v2-app-registration.md) para registrar um aplicativo. Verifique se você:

* Copiou a **ID do Aplicativo** atribuída ao aplicativo. Ela será necessária para este tutorial.
* Adicione a plataforma **Móvel** de seu aplicativo.
* Copiar o **URI de redirecionamento** do portal. Você deve usar o valor de URI padrão `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: instalar o Node.js
Para usar o exemplo neste tutorial, você deve [instalar o Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: instalar o MongoDB
Para poder usar esse exemplo com êxito, você deve [instalar o MongoDB](http://www.mongodb.org). Nesse exemplo, você usa o MongoDB para tornar a API REST persistente entre instâncias de servidor.

> [!NOTE]
> Neste artigo, supomos que você usa a instalação e os pontos de extremidade de servidor padrão para o MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: instalar os módulos Restify na API Web
Usaremos Resitfy para criar nossa API REST. O Restify é uma estrutura de aplicativo do Node.js mínima e flexível, derivada do Express. O Restify tem um conjunto robusto de recursos que você pode usar para criar APIs REST no Connect.

### <a name="install-restify"></a>Instalar o Restify
1.  Em um prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

    Se o diretório **azuread** não existir, crie-o:

    `mkdir azuread`

2.  Instale o Restify:

    `npm install restify`

    A saída desse comando deve ter esta aparência:

    ```
    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Você obteve um erro?
Em alguns sistemas operacionais, quando você usar o comando `npm`, você verá esta mensagem: `Error: EPERM, chmod '/usr/local/bin/..'`. O erro é seguido de uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando `sudo` para executar `npm` com um nível de privilégio mais elevado.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Você obteve um erro com relação ao DTrace?
Quando você instala o restify, pode ver esta mensagem:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

O Restify tem um mecanismo eficiente para rastrear chamadas REST usando o DTrace. No entanto, o DTrace não está disponível em muitos sistemas operacionais. Você pode ignorar essa mensagem de erro com segurança.


## <a name="5-install-passportjs-in-your-web-api"></a>5: instalar o Passport.js na sua API Web
1.  Em um prompt de comando, altere o diretório para **azuread**.

2.  Instale o Passport.js:

    `npm install passport`

    A saída do comando deve ter esta aparência:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: adicionar o passport-azure-ad à sua API Web
Em seguida, adicione a estratégia de OAuth, usando passport-azure-ad. `passport-azuread` é um pacote de estratégias que conectam o Azure AD ao Passport. Usaremos essa estratégia para tokens de portador neste exemplo de API Rest.

> [!NOTE]
> Embora o OAuth 2.0 forneça uma estrutura na qual qualquer tipo de token conhecido possa ser emitido, determinados tipos de token são comumente usados. Os tokens de portador são usados para proteger os pontos de extremidade. Esses são o tipo mais amplamente emitido de token no OAuth 2.0. Muitas implementações do OAuth 2.0 presumem que os tokens de portador sejam o único tipo de token emitido.
> 
> 

1.  Em um prompt de comando, altere o diretório para **azuread**.

    `cd azuread`

2.  Instale o módulo `passport-azure-ad` do Passport.js:

    `npm install passport-azure-ad`

    A saída do comando deve ter esta aparência:

    ```
    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: adicionar módulos do MongoDB à sua API Web
Neste exemplo, usamos o MongoDB como nosso armazenamento de dados. 

1.  Instale o Mongoose, um plug-in amplamente utilizado para gerenciar modelos e esquemas: 

    `npm install mongoose`

2.  Instale o driver do banco de dados para o MongoDB, que também é chamado de MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: instalar módulos adicionais
Instale os módulos necessários restantes.

1.  Em um prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

2.  Digite os comandos a seguir. Os comandos instalam os módulos a seguir em seu diretório node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: criar um arquivo Server.js para suas dependências
Um arquivo Server.js retém a maior parte da funcionalidade para o servidor de API Web. Adicione a maior parte do nosso código a esse arquivo. Para fins de produção, você pode refatorar a funcionalidade em arquivos menores, assim como para controladores e rotas separadas. Neste artigo, usaremos Server.js para essa finalidade.

1.  Em um prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

2.  Usando um editor de sua escolha, crie um arquivo Server.js. Adicione as seguintes informações ao arquivo:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Salve o arquivo. Voltaremos a ele em breve.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: criar um arquivo de configuração para armazenar as configurações do AD do Azure
Esse arquivo de código passa os parâmetros de configuração do Portal do Azure AD para o arquivo Passport.js. Você criou esses valores de configuração quando adicionou a API Web ao portal no início do artigo. Após você copiar o código, explicaremos o que deve ser inserido nos valores desses parâmetros.

1.  Em um prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

2.  Em um editor, crie um arquivo Config.js. Adicione as seguintes informações:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Valores necessários

*   **IdentityMetadata**: é onde `passport-azure-ad` procura os dados de configuração para o IdP (provedor de identidade), bem como as chaves para validar os tokens JWT (Token Web JSON). Se você estiver usando o Azure AD, você provavelmente não desejará alterar isso.

*   **audience**: o URI de redirecionamento do portal.

> [!NOTE]
> Reverta suas chaves em intervalos frequentes. Verifique se você está extraindo sempre da URL "openid_keys" e de que o aplicativo pode acessar a Internet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: adicionar configuração ao arquivo Server.js
O aplicativo precisa ler esses valores do arquivo de configuração que você acabou de criar. Adicione o arquivo .config como um recurso necessário no aplicativo. Defina as variáveis globais para aqueles que estão em Config.js.

1.  No prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

2.  Em um editor, abra o Server.js. Adicione as seguintes informações:

    ```Javascript
    var config = require('./config');
    ```

3.  Adicione uma nova seção ao Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: Adicionar as informações de esquema e modelo do MongoDB usando Mongoose
Em seguida, conecte esses três arquivos em um serviço de API REST.

Neste artigo, usaremos o MongoDB para armazenar nossas tarefas. Abordaremos isso na *etapa 4*.

No arquivo Config.js criado na etapa 11, o banco de dados é chamado de *tasklist*. É isso que você colocou no final da URL de conexão mongoose_auth_local. Você não precisa criar esse banco de dados com antecedência no MongoDB. O banco de dados é criado na primeira execução do seu aplicativo para servidores (supondo que o banco de dados ainda não existe).

Você informou ao servidor qual banco de dados MongoDB usar. Em seguida, você precisa escrever algum código adicional para criar o modelo e o esquema para tarefas do servidor.

### <a name="the-model"></a>O modelo
O modelo de esquema é muito básico. Você pode expandi-lo se necessário. 

O modelo de esquema tem estes valores:

*   **NAME**. A pessoa atribuída à tarefa. Esse é um valor do tipo **string**.
*   **TASK**. O nome da tarefa. Esse é um valor do tipo **string**.
*   **DATE**. A data na qual a tarefa expira. Esse é um valor do tipo **datetime**.
*   **COMPLETED**. Se a tarefa está ou não concluída. Esse é um valor do tipo **Boolean**.

### <a name="create-the-schema-in-the-code"></a>Criar o esquema no código
1.  Em um prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

2.  No editor, abra o Server.js. Adicione as seguintes informações abaixo da entrada de configuração:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Esse código se conecta ao servidor MongoDB. Ele também retorna um objeto de esquema.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Usando o esquema, crie o modelo no código
Abaixo do código anterior, adicione o código a seguir:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Como você pode ver no código, você cria o esquema primeiro. Em seguida, você cria um objeto do modelo. Você usa o objeto do modelo para armazenar os dados em todo o código ao definir suas **rotas**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: adicionar as rotas ao servidor de API REST da tarefa
Agora que você tem um modelo de banco de dados com o qual trabalhar, adicione as rotas que você usará para o servidor de API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas no Restify
As rotas no Restify funcionam exatamente da mesma forma que quando você usar a pilha do Express. Você define rotas usando o URI que espera que os aplicativos de cliente chamem. Normalmente, você define suas rotas em um arquivo separado. Neste tutorial, colocamos nossa rotas no Server.js. Para uso em produção, recomendamos que você coloque as rotas no próprio arquivo delas.

Eis um padrão típico para uma rota de Restify:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Este é o padrão no nível mais básico. O Restify (e o Express) fornecem uma funcionalidade muito mais aprofundada, como a definição de tipos de aplicativos e o roteamento complexo entre diferentes pontos de extremidade.

#### <a name="add-default-routes-to-your-server"></a>Adicionar rotas padrão a seu servidor
Adicione as rotas CRUD básicas: **create** (criar), **retrieve** (extrair), **update** (atualizar) e **delete** (excluir).

1.  Em um prompt de comando, altere o diretório para **azuread**:

    `cd azuread`

2.  Em um editor, abra o Server.js. Abaixo das entradas de banco de dados que você criou anteriormente, adicione as seguintes informações:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();
    _task.save(function(err) {
    if (err) {
    req.log.warn(err, 'createTask: unable to save');
    next(err);
    } else {
    res.send(201, _task);
    }
    });
    return next();
    }
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
    }
    if (!owner) {
    log.warn(err, "You did not pass an owner when listing tasks.");
    } else {
    res.json(data);
    }
    });
    return next();
    }
    ```

### <a name="add-error-handling-for-the-routes"></a>Adicionar tratamento de erro para as rotas
Adicione algum tratamento de erro para que você possa se comunicar de volta com o cliente sobre o problema encontrado.

Adicione o código a seguir abaixo do código que você já escreveu:

```Javascript
///--- Errors for communicating something more information back to the client.
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="14-create-your-server"></a>14: criar seu servidor
A última coisa a fazer é adicionar a instância do servidor. A instância do servidor gerencia suas chamadas.

O Restify (e Express) têm personalização profunda que pode ser usada com um servidor de API REST. Neste tutorial, usamos a configuração mais básica.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: adicionar as rotas (sem autenticação por enquanto)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16: executar o servidor
É uma boa ideia testar o servidor antes de adicionar a autenticação.

A maneira mais fácil de testar o servidor é usando curl em um prompt de comando. Para fazer isso, você precisa de um utilitário simples que você pode usar para analisar a saída como JSON. 

1.  Instale a ferramenta JSON que usamos nos exemplos a seguir:

    `$npm install -g jsontool`

    Isso instala a ferramenta JSON globalmente.

2.  Verifique se a instância do MongoDB está em execução.

    `$sudo mongod`

3.  Altere o diretório para **azuread** e execute o curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Para adicionar uma tarefa:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    A resposta deve ser:

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Listar tarefas para Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se todos esses comandos são executados sem erros, você está pronto para adicionar OAuth ao servidor de API REST.

*Agora você tem um servidor de API REST com o MongoDB!*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: adicionar autenticação a seu servidor de API REST
Agora que você tem uma API REST em execução, configure-a para usá-la com o Azure AD.

Em um prompt de comando, altere o diretório para **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Usar o oidcbearerstrategy que está incluído no passport-azure-ad
Até agora, você criou um servidor típico TODO do REST sem nenhum tipo de autorização. Agora, adicione autenticação.

Primeiro, indique que você deseja usar o Passport. Coloque isso logo após a sua configuração anterior do servidor:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Ao escrever APIs, convém sempre vincular os dados a algo exclusivo do token que o usuário não possa falsificar. Quando esse servidor armazena itens TODO, armazena-os com base na ID da assinatura do usuário no token (chamado por meio de token.sub). Você pode colocar o token.sub no campo "proprietário". Isso garante que somente esse usuário possa acessar os TODOs do usuário. Ninguém pode acessar as TODOs que foram inseridas. Não há nenhuma exposição na API para o "proprietário". Um usuário externo pode solicitar TODOs de outros usuários, mesmo quando eles estão autenticados.
> 
> 

Em seguida, usaremos a estratégia de Open ID Connect Bearer que vem com `passport-azure-ad`. Coloque isto depois do que você colou anteriormente:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

O Passport usa um padrão semelhante para todas as suas estratégias (Twitter, Facebook e assim por diante). Todos os gravadores de estratégia seguem o padrão. Passe uma `function()` para a estratégia que usa um token e `done` como parâmetros. A estratégia é retornada depois de fazer todo o seu trabalho. Armazene o usuário e guarde o token para que você não precise solicitá-lo novamente.

> [!IMPORTANT]
> O código anterior usa qualquer usuário que pode se autenticar no servidor. Isso é conhecido como registro automático. Em um servidor de produção, você não desejará permitir que nenhuma pessoa entre sem primeiro passar por um processo de registro escolhido por você. Geralmente, esse é o padrão visto em aplicativos de consumidor. O aplicativo pode permitir que você se registre com o Facebook, mas, em seguida, solicita que você insira informações adicionais. Se você não estava usando um programa de linha de comando para este tutorial, você poderá extrair o email do objeto de token que é retornado. Em seguida, você poderá solicitar ao usuário que insira informações adicionais. Como esse é um servidor de teste, você adiciona o usuário diretamente ao banco de dados na memória.
> 
> 

### <a name="protect-endpoints"></a>Proteger pontos de extremidade
Você pode proteger pontos de extremidade, especificando a chamada **passport.authenticate()** com o protocolo que você deseja usar.

Você pode editar sua rota em seu código de servidor para uma opção mais avançada:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: executar o aplicativo para servidores novamente
Use o curl novamente para ver se você tem a proteção OAuth 2.0 para os pontos de extremidade. Faça isso antes de executar qualquer um dos seus SDKs de cliente para esse ponto de extremidade. Os cabeçalhos retornados devem lhe informar se a autenticação está funcionando corretamente.

1.  Verifique se a instância do MongoDB está em execução:

    `$sudo mongod`

2.  Altere para o diretório **azuread** e, em seguida, use o curl:

    `$ cd azuread`

    `$ node server.js`

3.  Tente uma POSTAGEM básica:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Uma resposta 401 indica que a camada do Passport está tentando redirecionar-se para o ponto de extremidade autorizado, que é exatamente o que você deseja.

*Agora você tem um serviço de API REST que usa OAuth 2.0!*

Você já fez tudo o que podia com esse servidor sem usar um cliente compatível com o OAuth 2.0. Para fazer isso, você precisará examinar um tutorial adicional.

## <a name="next-steps"></a>Próximas etapas
Para referência, o exemplo completo (sem seus valores de configuração) é [fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Você também pode cloná-la do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Agora, você pode passar para tópicos mais avançados. Talvez você queira experimentar [Proteger um aplicativo Web do Node.js usando o ponto de extremidade v2.0](active-directory-v2-devquickstarts-node-web.md).

Estes são alguns recursos adicionais:

* [Guia do desenvolvedor do Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Marcação “azure-active-directory” do Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos
Incentivamos você a se inscrever para receber notificações quando ocorrerem incidentes de segurança. Na página [Notificações de Segurança Técnica da Microsoft](https://technet.microsoft.com/security/dd252948), assine Alertas de Avisos de Segurança.

