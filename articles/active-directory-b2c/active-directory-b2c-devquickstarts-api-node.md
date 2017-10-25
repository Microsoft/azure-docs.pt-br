---
title: 'Azure AD B2C: proteger uma API Web usando o Node.js | Microsoft Docs'
description: "Como criar uma API Web do Node.js que aceita tokens de um locatário do B2C"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: xerners
ms.openlocfilehash: 2315f5d0ebb142561c65224cd9d06299466a7ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: proteger uma API Web usando o Node .js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Active Directory B2C do Azure (AD do Azure), você pode proteger uma API Web usando tokens de acesso do OAuth 2.0. Esses tokens permitem que os aplicativos cliente que usem o AD B2C do Azure se autentiquem na API. Este artigo mostra como criar uma API de "lista de tarefas" que permite aos usuários adicionar e listar tarefas. A API Web é protegida usando o Azure AD B2C e permite que apenas usuários autenticados gerenciem sua lista de tarefas pendentes.

> [!NOTE]
> Este exemplo foi escrito para que você se conecte a ele usando nosso [aplicativo de exemplo B2C para iOS](active-directory-b2c-devquickstarts-ios.md). Primeiramente, explore este passo a passo e, depois, acompanhe usando esse exemplo.
>
>

**Passport** é middleware de autenticação para o Node.js. Flexível e modular, o Passport pode ser instalado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias que dão suporte à autenticação usando um nome de usuário e uma senha, o Facebook, o Twitter e muito mais. Desenvolvemos uma estratégia para o Azure AD (Active Directory do Azure). Você instala esse módulo e, em seguida, adiciona o plug-in `passport-azure-ad` do Azure AD.

Para realizar este exemplo, você precisa:

1. Registrar um aplicativo com o Azure AD.
2. Configurar seu aplicativo para usar o plug-in `passport-azure-ad` do Passport.
3. Configurar um aplicativo cliente para chamar a API Web de "lista de tarefas pendentes".

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário.  Um diretório é um contêiner para todos os usuários, aplicativos, grupos e muito mais.  Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo no diretório B2C, que dá ao Azure AD algumas informações de que ele precisa para se comunicar de forma segura com o aplicativo. Nesse caso, o aplicativo cliente e a API Web são representados por uma única **ID de aplicativo**, pois eles abrangem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

* Incluir um **aplicativo Web/api Web** no aplicativo
* Digite `http://localhost/TodoListService` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
* Crie um **Segredo de aplicativo** para seu aplicativo e copie-o. Você precisa destes dados mais tarde. Observe que esse valor precisa ser [seguido por caracteres de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de ser usado.
* Copie a **ID de aplicativo** atribuída ao aplicativo. Você precisa destes dados mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
No AD B2C do Azure, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). O aplicativo contém duas experiências de identidade: inscrever-se e entrar. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  Ao criar as três políticas, não deixe de:

* Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
* Escolha as declarações de aplicativo **Nome de exibição** e **ID do Objeto** em todas as políticas.  Você pode escolher outras declarações também.
* Copie o **Nome** de cada política após criá-la. Ele deve ter o prefixo `b2c_1_`.  Mais tarde, você precisará desses nomes de política.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Para saber mais sobre o funcionamento das políticas no Azure AD B2C, comece com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Baixar o código
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Para compilar o exemplo à medida que avançar, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

## <a name="download-nodejs-for-your-platform"></a>Baixar o Node.js para sua plataforma
Para usar este exemplo com êxito, você precisa de uma instalação do Node.js em funcionamento.

Instale o Node.js de [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalar o MongoDB para sua plataforma
Para usar este exemplo com êxito, você deve ter uma instalação do MongoDB funcionando corretamente. Usamos o MongoDB para tornar a API REST persistente entre instâncias de servidor.

Instale o MongoDB de [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Este passo a passo presume que você use os pontos de extremidade de servidor e de instalação padrão para MongoDB, que, no momento da redação deste artigo, são `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalar os módulos Restify na API Web
Usamos o Restify para criar a API REST. O Restify é uma estrutura de aplicativo do Node.js mínima e flexível, derivada do Express. Tem um conjunto robusto de recursos para a criação de APIs REST sobre o Connect.

### <a name="install-restify"></a>Instalar Restify
Na linha de comando, altere o diretório para `azuread`. Se o diretório `azuread` não existir, crie-o.

`cd azuread` ou `mkdir azuread;`

Digite o seguinte comando:

`npm install restify`

Este comando instala o Restify.

#### <a name="did-you-get-an-error"></a>Você obteve um erro?
Em alguns sistemas operacionais, ao usar `npm`, você pode receber o erro `Error: EPERM, chmod '/usr/local/bin/..'` e uma solicitação para executar a conta como administrador. Se o problema ocorrer, use o comando `sudo` para executar `npm` com um nível de privilégio mais elevado.

#### <a name="did-you-get-a-dtrace-error"></a>Você recebeu um erro DTrace?
Você pode ver algo como este texto ao instalar o Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
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

O Restify fornece um mecanismo poderoso para rastreamento de chamadas REST usando o DTrace. No entanto, muitos sistemas operacionais não têm o DTrace disponível. Você pode ignorar com segurança esses erros.

A saída do comando deve ser semelhante ao seguinte texto:

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
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalar o Passport.js na API Web
Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá.

Instale o Passport usando o seguinte comando:

`npm install passport`

A saída do comando deve ser semelhante ao seguinte texto:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Adicionar passport azuread à API Web
Em seguida, adicione a estratégia OAuth usando `passport-azuread`, um pacote de estratégias que conectam o Azure AD ao Passport. Use essa estratégia para tokens de portador no exemplo de API REST.

> [!NOTE]
> Embora o OAuth2 forneça uma estrutura na qual qualquer tipo de token conhecido pode ser emitido, somente determinados tipos de token passaram a ser amplamente usados. Os tokens para proteger pontos de extremidade são tokens de portador. Estes tipos de tokens são mais amplamente emitidos em OAuth2. Muitas implementações presumem que os tokens de portador sejam o único tipo de token emitido.
>
>

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá.

Instale o módulo `passport-azure-ad` do Passport usando o seguinte comando:

`npm install passport-azure-ad`

A saída do comando deve ser semelhante ao seguinte texto:

``
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
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Adicionar os módulos do MongoDB à API Web
Este exemplo usa o MongoDB como repositório de dados. Para isso, instale o Mongoose, um plug-in amplamente utilizado para gerenciar modelos e esquemas.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalar módulos adicionais
Em seguida, instale os módulos necessários restantes.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Instale os módulos no diretório `node_modules` :

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Criar um arquivo server.js com suas dependências
O arquivo `server.js` fornece a maior parte da funcionalidade para o servidor de API Web.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Crie um arquivo `server.js` em um editor. Adicione as seguintes informações:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Salve o arquivo. Você voltará a ele mais tarde.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Criar um arquivo config.js para armazenar as configurações do Azure AD
Esse arquivo de código passa os parâmetros de configuração do Portal do Azure AD para o arquivo `Passport.js` . Você criou esses valores de configuração ao adicionar a API Web ao portal na primeira parte do passo a passo. Explicaremos o que deve ser inserido nos valores desses parâmetros depois que você copiar o código.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Crie um arquivo `config.js` em um editor. Adicione as seguintes informações:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valores necessários
`clientID`: a ID do cliente do aplicativo de API da Web.

`IdentityMetadata`: esse é o local onde `passport-azure-ad` procura os dados de configuração para o provedor de identidade. Ele também procura as chaves para validar os tokens Web JSON.

`audience`: o URI (uniform resource identifier) do portal que identifica o aplicativo de chamada.

`tenantName`: o nome do locatário (por exemplo, **contoso.onmicrosoft.com**).

`policyName`: a política que você deseja que valide os tokens que chegam ao servidor. Essa política deve ser a mesma política que você usou no aplicativo cliente para entrar.

> [!NOTE]
> Por ora, use as mesmas políticas em instalações de cliente e de servidor. Se já tiver concluído um passo a passo e criado essas políticas, você não precisará fazer isso novamente. Como concluiu o passo a passo, você não deve precisar configurar novas políticas para um passo a passo de cliente no site.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Adicionar configuração ao arquivo server.js
Para ler os valores do arquivo `config.js` criado, adicione o arquivo `.config` como um recurso necessário em seu aplicativo e, em seguida, defina as variáveis globais como aquelas no documento `config.js`.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abra o arquivo `server.js` em um editor. Adicione as seguintes informações:

```Javascript
var config = require('./config');
```
Adicione uma nova seção a `server.js` que inclua o seguinte código:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Em seguida, vamos adicionar alguns espaços reservados para os usuários que recebemos dos aplicativos de chamada.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Vamos prosseguir e criar nosso logger também.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Adicionar as informações de esquema e modelo do MongoDB usando Mongoose
A preparação anterior rende frutos quando você reune esses três arquivos em um serviço de API REST.

Para este passo a passo, use o MongoDB para armazenar suas tarefas, como discutido anteriormente.

No arquivo `config.js` , você chamou o banco de dados **tasklist**. Esse nome também foi o que você colocou no fim da URL de conexão `mongoose_auth_local` . Você não precisa criar esse banco de dados com antecedência no MongoDB. Ele cria o banco de dados para você na primeira execução do aplicativo de servidor.

Após informar ao servidor qual banco de dados do MongoDB deve ser usado, você precisa escrever código adicional para criar o modelo e o esquema para suas tarefas de servidor.

### <a name="expand-the-model"></a>Expandir o modelo
Esse modelo de esquema é simples. Você pode expandi-lo conforme necessário.

`owner`: quem está atribuído à tarefa. Este objeto é uma **cadeia de caracteres**.  

`Text`: a própria tarefa. Este objeto é uma **cadeia de caracteres**.

`date`: a data em que a tarefa deverá ser concluída. Este objeto é um **datetime**.

`completed`: se a tarefa for concluída. Este objeto é um **Booliano**.

### <a name="create-the-schema-in-the-code"></a>Criar o esquema no código
Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abra o arquivo `server.js` em um editor. Adicione as seguintes informações abaixo da entrada de configuração:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Primeiro você cria o esquema e, em seguida, cria um objeto de modelo que usa para armazenar os dados em todo o código ao definir suas **rotas**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Adicionar rotas para o servidor de tarefa da API REST
Agora que você tem um modelo de banco de dados com o qual trabalhar, adicione as rotas que usa para o servidor de API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas no Restify
As rotas funcionam no Restify da mesma forma como funcionam ao usar a pilha Express. Você define rotas usando o URI que espera que os aplicativos de cliente chamem.

Um padrão típico para uma rota do Restify é:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

O Restify e o Express fornecem funcionalidade muito mais aprofundada, como a definição de tipos de aplicativos e o roteamento complexo entre diferentes pontos de extremidade. Para este tutorial, mantemos essas rotas simples.

#### <a name="add-default-routes-to-your-server"></a>Adicionar rotas padrão a seu servidor
Agora você adiciona rotas CRUD básicas de **create** e **list** para a API REST. Outras rotas podem ser encontradas na ramificação `complete` do exemplo.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

Abra o arquivo `server.js` em um editor. Abaixo das entradas de banco de dados que você criou acima, adicione as seguintes informações:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

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
            log.warn(err, "There is no tasks in the database. Add one!");
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


#### <a name="add-error-handling-for-the-routes"></a>Adicionar tratamento de erro para as rotas
Adicione tratamento de erros, para que possa comunicar os problemas encontrados ao cliente de forma que ele possa entender.

Adicione os códigos a seguir:

```Javascript
///--- Errors for communicating something interesting back to the client
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


## <a name="create-your-server"></a>Criar seu servidor
Agora você definiu o banco de dados e incluiu as rotas. A última coisa a fazer é adicionar a instância do servidor que gerencia suas chamadas.

O Restify e Express fornecem personalização aprofundada para um servidor de API REST, mas usamos a configuração mais básica aqui.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Adicionar as rotas ao servidor (sem autenticação)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Adicionar autenticação a seu servidor de API REST
Agora que tem um servidor de API REST em execução, você pode torná-lo útil no Azure AD.

Na linha de comando, altere o diretório para `azuread`, se ainda não estiver lá:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Use o OIDCBearerStrategy que está incluído no passport-azure-ad
> [!TIP]
> Ao escrever APIs, você deve sempre vincular os dados a algo exclusivo do token que o usuário não possa falsificar. Quando o servidor armazena itens de ToDo, ele faz isso com base na **oid** do usuário no token (chamado por meio de token.oid) que é incluído no campo "proprietário". Esse valor garante que somente o usuário possa acessar seus próprios itens de Tarefas Pendentes. Não há exposição na API do "proprietário". Portanto, um usuário externo pode solicitar os itens ToDo de outras pessoas, mesmo que estejam autenticados.
>
>

Em seguida, use a estratégia de portador que vem com o `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

O Passport usa o mesmo padrão para todas as suas estratégias. Você o passa como um `function()` que tem `token` e `done` como parâmetros. A estratégia volta para você após fazer seu trabalho. Em seguida, você deve armazenar o usuário e salvar o token para que não precise pedi-lo novamente.

> [!IMPORTANT]
> O código acima usa qualquer usuário que se autentique em seu servidor. Este processo é conhecido como registro automático. Em servidores de produção, não permitem que qualquer usuário acesse a API sem primeiro passar por um processo de registro. Esse processo geralmente é o padrão em aplicativos de consumidor que lhe permitem registrar-se usando o Facebook, mas depois pedem que você preencha informações adicionais. Se esse programa não fosse um programa de linha de comando, poderíamos ter extraído o email do objeto de token que é retornado e pedido que os usuários preenchessem informações adicionais. Como este é um exemplo, eles são adicionados a um banco de dados na memória.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Executar o aplicativo de servidor e verificar se ele o rejeita
Você pode usar `curl` para ver se agora tem a proteção de OAuth2 para os pontos de extremidade. Os cabeçalhos retornados devem ser suficientes para indicar que você está no caminho certo.

Verifique se a instância do MongoDB está em execução.

    $sudo mongodb

Altere para o diretório e execute o servidor:

    $ cd azuread
    $ node server.js

Em uma nova janela de terminal, execute `curl`

Tente uma POSTAGEM básica:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Um erro 401 é a resposta desejada. Isso indica que a camada do Passport está tentando se redirecionar para o ponto de extremidade de autorização.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Agora você tem um serviço de API REST que usa OAuth2
Você implementou uma API REST usando Restify e OAuth! Agora você tem código suficiente para continuar a desenvolver o serviço e ampliar este exemplo. Você  fez tudo o que podia com esse servidor sem usar um cliente compatível com OAuth2. Para a próxima etapa, use um passo a passo adicional, como [Conectar-se a uma API da Web por meio do iOS com B2C](active-directory-b2c-devquickstarts-ios.md) .

## <a name="next-steps"></a>Próximas etapas
Agora você pode ir para tópicos mais avançados, como:

[Conectar-se a uma API Web usando o iOS com o B2C](active-directory-b2c-devquickstarts-ios.md)
