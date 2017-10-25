---
title: "Introdução ao Node.js do Azure AD | Microsoft Docs"
description: "Como compilar uma API Web REST do Node.js que se integre ao Azure AD para autenticação."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 4f58177f540c14172d7ece8b4bc8c8a2b9787f8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-web-apis-for-nodejs"></a>Introdução às APIs Web para o Node.js
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* é middleware de autenticação para o Node.js. Flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias dão suporte à autenticação usando um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Microsoft Azure Active Directory (Azure AD). Instalamos esse módulo e então adicionamos o plug-in `passport-azure-ad` do Microsoft Azure Active Directory.

Para fazer isso, você precisa:

1. Registrar um aplicativo com o Azure AD.
2. Configurar seu aplicativo para usar o plug-in `passport-azure-ad` do Passport.
3. Configurar um aplicativo cliente para chamar a API da Web da "lista de tarefas pendentes".

O código para este tutorial é mantido [no GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado.  Recomendamos que você comece lendo o [documento Como integrar com o Azure Active Directory](active-directory-how-to-integrate.md) para saber mais sobre os conceitos básicos do Azure Active Directory.
>
>

Já lançamos todo o código-fonte para este exemplo em execução no GitHub sob uma licença do MIT, então fique à vontade para copiar (ou melhor ainda, divulgar), fazer comentários e solicitações pull.

## <a name="about-nodejs-modules"></a>Sobre os módulos do Node.js
Usaremos módulos do Node.js neste passo a passo. Os módulos são pacotes carregáveis do JavaScript que fornecem funcionalidade específica para seu aplicativo. Você geralmente instala módulos usando uma ferramenta de linha de comando NPM do Node.js no diretório de instalação do NPM. No entanto, alguns módulos, como o módulo HTTP, estão inclusos no pacote do Node.js do núcleo.

Os módulos instalados são salvos no diretório **node_modules**, na raiz do diretório de instalação do seu Node.js. Cada módulo dentro do diretório **node_modules** mantém seu próprio diretório **node_modules** que contém todos os módulos dos quais ele depende. Além disso, cada módulo requerido tem um diretório **node_modules**. Essa estrutura de diretório recursiva representa a cadeia de dependências.

Essa estrutura de cadeia de dependência resulta em um espaço de aplicativo maior. Mas ela também garante que todas as dependências sejam atendidas e que a versão dos módulos usada no desenvolvimento também seja usada na produção. Isso torna o comportamento do aplicativo de produção mais previsível e evita os problemas de controle de versão que podem afetar os usuários.

## <a name="step-1-register-an-azure-ad-tenant"></a>Etapa 1: registrar um locatário do Azure AD
Para usar esse exemplo, você precisará de um locatário do Azure Active Directory. Se você não tem certeza do que é um locatário ou como obter um, consulte [Como obter um locatário do Azure AD](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>Etapa 2: criar um aplicativo
Em seguida, crie um aplicativo no seu diretório que dê ao Azure AD algumas informações de que ele precisa para se comunicar de forma segura com o seu aplicativo.  O aplicativo cliente e a API da Web são representados por uma única **ID de aplicativo** nesse caso, pois eles abrangem um aplicativo lógico.  Para criar um aplicativo, [siga estas instruções](active-directory-how-applications-are-added.md). Se você estiver criando um aplicativo de linha de negócios, [estas instruções adicionais podem ser úteis](../active-directory-applications-guiding-developers-for-lob-applications.md).

Para criar um aplicativo:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu superior, selecione a sua conta. Na lista **Diretório**, escolha o locatário do Active Directory onde você deseja registrar seu aplicativo.

3. No menu do lado esquerdo da tela, selecione **Mais Serviços**, depois selecione **Azure Active Directory**.

4. Selecione **Registros do aplicativo**e, em seguida, selecione **Adicionar**.

5. Siga os prompts para criar um **Aplicativo Web e/ou uma WebAPI**.

      * O **nome** do aplicativo descreve o seu aplicativo aos usuários finais.

      * A **URL de logon** é a URL base do seu aplicativo.  A URL padrão do código de exemplo é `https://localhost:8080`.

6. Depois de se registrar, o Azure AD atribui uma ID de aplicativo única ao seu aplicativo. Você precisará desse valor nas próximas seções, então copie-o da página do aplicativo.

7. Na página **Configurações** -> **Propriedades** do aplicativo, atualize o URI da ID do Aplicativo. O **URI da ID do aplicativo** é um identificador exclusivo para seu aplicativo. A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Crie uma **Chave** para o seu aplicativo na página **Configurações** e copie-a em algum lugar. Você precisará dela em breve.

## <a name="step-3-download-nodejs-for-your-platform"></a>Etapa 3: baixar o Node.js para a sua plataforma
Para usar este exemplo com êxito, você deve ter uma instalação do Node.js em funcionamento.

Instale o Node.js a partir de [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>Etapa 4: instalar o MongoDB na sua plataforma
Para usar este exemplo com êxito, você deve ter uma instalação do MongoDB funcionando corretamente. Use o MongoDB para tornar a API REST persistente entre as instâncias do servidor.

Instalar o MongoDB a partir de [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Este passo a passo presume que você usa os pontos de extremidade de servidor e de instalação padrão para o MongoDB, que, no momento da redação deste artigo, é: mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>Etapa 5: instalar os módulos Restify na sua API da Web
Usaremos o Resitfy para criar nossa API REST. O Restify é uma estrutura de aplicativo do Node.js mínima e flexível, derivada do Express. Tem um conjunto robusto de recursos para a criação de APIs REST sobre o Connect.

### <a name="install-restify"></a>Instalar Restify
1. Na linha de comando, altere os diretórios para o diretório **azuread**. Se o diretório **azuread** não existir, crie-o.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Digite o seguinte comando:

    `npm install restify`

    Este comando instala o Restify.

#### <a name="did-you-get-an-error"></a>Você obteve um erro?
Ao usar o NPM em alguns sistemas operacionais, você poderá receber uma mensagem de erro que diz: **Erro: EPERM, chmod '/usr/local/bin/..'** e uma solicitação para tentar executar a conta como administrador. Se isso ocorrer, use o comando sudo para executar o NPM com um nível de privilégio mais elevado.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Ocorreu um erro com relação ao DTRACE?
Você poderá ver um erro assim ao instalar o Restify:

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
O Restify fornece um mecanismo poderoso para rastreamento de chamadas REST usando o DTrace. No entanto, muitos sistemas operacionais não possuem o DTrace. Você pode ignorar com segurança esses erros.

A saída desse comando deve ser semelhante ao seguinte:

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


## <a name="step-6-install-passportjs-in-your-web-api"></a>Etapa 6: instalar o Passport.js na sua API da Web
[Passport](http://passportjs.org/) é middleware de autenticação para o Node.js. Flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias dão suporte à autenticação usando um nome de usuário e senha, Facebook, Twitter e mais.

Desenvolvemos uma estratégia para o Active Directory do Azure. Instalaremos esse módulo e, em seguida, adicionaremos o plug-in de estratégia do Azure Active Directory.

1. Na linha de comando, altere os diretórios para o diretório **azuread**.

2. Insira o comando a seguir para instalar o passport.js:

    `npm install passport`

    A saída do comando deve ser semelhante ao seguinte:

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>Etapa 7: adicionar o Passport-Azure-AD à sua API da Web
Em seguida, adicionamos a estratégia OAuth usando `passport-azure-ad`, um pacote de estratégias que conectam o Azure Active Directory ao Passport. Usaremos essa estratégia para tokens de portador neste exemplo de API Rest.

> [!NOTE]
> Embora o OAuth2 forneça uma estrutura na qual qualquer tipo de token conhecido possa ser emitido, somente determinados tipos de token são comumente usados. Os tokens de portador são os tokens mais usados para proteger os pontos de extremidade. Esses são o tipo mais amplamente emitido de token no OAuth2. Muitas implementações presumem que os tokens de portador sejam o único tipo de token emitido.
>
>

Na linha de comando, altere os diretórios para o diretório **azuread**.

Insira o comando a seguir para instalar o Passport.js`passport-azure-ad module`:

`npm install passport-azure-ad`

A saída do comando deve ser semelhante ao seguinte:


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



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>Etapa 8: adicionar módulos do MongoDB à sua API da Web
Usamos o MongoDB como nosso repositório de dados. Por esse motivo, é preciso instalar o plug-in amplamente utilizado chamado Mongoose para gerenciar modelos e esquemas. Também é necessário instalar o driver do banco de dados para o MongoDB (que também é chamado de MongoDB).

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>Etapa 9: instalar módulos adicionais
Em seguida, instalaremos os módulos necessários restantes.

1. Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela.

    `cd azuread`

2. Digite os comandos a seguir para instalar esses módulos no seu diretório **node_modules**:

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>Etapa 10: criar um server.js com as suas dependências
O arquivo server.js fornece a maior parte da funcionalidade para o nosso servidor de API da Web. Adicionamos a maior parte do nosso código a esse arquivo. Para fins de produção, recomendamos que você refatore a funcionalidade em arquivos menores, como rotas separadas e controladores. Nesta demonstração, usaremos o server.js para essa funcionalidade.

1. Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela.

    `cd azuread`

2. Crie um arquivo `server.js` no seu editor favorito e adicione as informações a seguir:

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. Salve o arquivo. Voltaremos a ele em breve.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>Etapa 11: criar um arquivo de configuração para armazenar as configurações do Active Directory do Azure
Esse arquivo de código passa os parâmetros de configuração do seu Portal do Azure Active Directory ao Passport.js. Você criou esses valores de configuração quando adicionou a API da Web ao portal na primeira parte do passo a passo. Explicaremos o que deve ser inserido nos valores desses parâmetros depois que você copiar o código.

1. Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela.

    `cd azuread`

2. Crie um arquivo `config.js` no seu editor favorito e adicione as informações a seguir:

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. Salve o arquivo.

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>Etapa 12: adicione valores de configuração ao seu arquivo server.js
Precisamos ler esses valores do arquivo .config que você acabou de criar no nosso aplicativo. Para fazer isso, adicionamos o arquivo .config como um recurso necessário em nosso aplicativo. Em seguida, definimos as variáveis globais para corresponder às variáveis no documento config. js.

1. Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela.

    `cd azuread`

2. Abra o arquivo `server.js` no seu editor favorito e adicione as informações a seguir:

    ```Javascript
    var config = require('./config');
    ```
3. Em seguida, adicione uma nova seção a `server.js` com o código a seguir:

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Salve o arquivo.

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Etapa 13: adicionar as informações de esquema e modelo do MongoDB usando o Mongoose
Agora toda essa preparação passará a compensar, enquanto combinamos esses três arquivos em um serviço de API REST.

Para este passo a passo, usaremos o MongoDB para armazenar nossas tarefas, conforme discutido na Etapa 4.

No arquivo `config.js` criado na Etapa 11, chamamos nosso banco de dados de `tasklist`, já que foi isso que colocamos no final da nossa URL de conexão **mogoose_auth_local**. Você não precisa criar esse banco de dados com antecedência no MongoDB. Em vez disso, o MongoDB o criará para nós na primeira execução do nosso aplicativo de servidor (supondo que o banco de dados ainda não existe).

Agora que dissemos ao servidor qual banco de dados MongoDB gostaríamos de usar, precisamos escrever algum código adicional para criar o modelo e o esquema para as tarefas do nosso servidor.

### <a name="discussion-of-the-model"></a>Discussão do modelo
Nosso modelo de esquema é simples. Você pode expandi-lo conforme necessário.

NAME: o nome da pessoa a quem é atribuída a tarefa. Uma **cadeia de caracteres**.

TASK: a própria tarefa. Uma **cadeia de caracteres**.

DATE: a data em que a tarefa deverá ser concluída. UM VALOR **DATETIME**.

COMPLETED: se a tarefa foi concluída ou não. UM VALOR **BOOLEANO**.

### <a name="creating-the-schema-in-the-code"></a>Criando o esquema no código
1. Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela.

    `cd azuread`

2. Abra o arquivo `server.js` no seu editor favorito e adicione as seguintes informações abaixo da entrada de configuração:

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
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
Como você pode ver no código, criamos nosso esquema primeiro. Em seguida, criamos um objeto de modelo que usamos para armazenar nossos dados por todo o código quando definimos nossas **Rotas**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>Etapa 14: adicionar nossas rotas a nosso servidor de API REST da tarefa
Agora que temos um modelo de banco de dados com o qual trabalhar, vamos adicionar as rotas que usaremos para o nosso servidor de API REST.

### <a name="about-routes-in-restify"></a>Sobre rotas no Restify
As rotas funcionam no Restify exatamente da mesma maneira que funcionam usando a pilha Express. Você define rotas usando o URI que espera que os aplicativos de cliente chamem. Normalmente, você define suas rotas em um arquivo separado. Para nossos propósitos, colocaremos as nossas rotas no arquivo server.js. Recomendamos que você as coloque no seu próprio arquivo para uso em produção.

Um padrão típico para uma rota do Restify é:

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Esse é o padrão no nível mais básico. O Restify (e o Express) fornece uma funcionalidade muito mais aprofundada, como a definição de tipos de aplicativos e o roteamento complexo entre diferentes pontos de extremidade. Para nossos propósitos, estamos mantendo essas rotas simples.

### <a name="add-default-routes-to-our-server"></a>Adicionar rotas padrão ao nosso servidor
Agora adicionamos as rotas CRUD básicas de Create (criar), Retrieve (extrair), Update (atualizar) e Delete (excluir).

1. Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela:

    `cd azuread`

2. Abra o arquivo `server.js` no nosso editor favorito e adicione as seguintes informações abaixo das entradas anteriores do banco de dados que você criou:

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
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

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
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

### <a name="add-error-handling-in-our-apis"></a>Adicionar tratamento de erros em nossas APIs
```

///--- Errors for communicating something interesting back to the client.

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


## <a name="step-15-create-your-server"></a>Etapa 15: criar seu servidor
Definimos nosso banco de dados e nossas rotas estão em vigor. A última coisa a fazer é adicionar a instância do servidor que gerencia nossas chamadas.

No Restify (e no Express) é possível fazer várias personalizações em um servidor de API REST, mas, novamente, usaremos a configuração mais básica para atender aos nossos objetivos.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>Etapa 16: adicionar as rotas ao servidor (sem autenticação, por enquanto)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
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
// Register a default '/' handler.
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

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>Etapa 17: executar o servidor (antes de adicionar o suporte ao OAuth)
Teste o seu servidor antes de adicionarmos a autenticação.

A maneira mais fácil de testar o seu servidor é usando ondulação em uma linha de comando. Antes de fazermos isso, precisamos de um utilitário que nos permita analisar a saída como JSON.

1. Instale a seguinte ferramenta JSON (todos os exemplos a seguir usam essa ferramenta):

    `$npm install -g jsontool`

    Isso instala a ferramenta JSON globalmente. Agora que você conseguiu fazer isso, vamos mexer com o servidor:

2. Primeiro, verifique se a instância do MongoDB está em execução:

    `$sudo mongod`

3. Em seguida, vá até o diretório e inicie a ondulação:

    `$ cd azuread` `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
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

4. Então, podemos adicionar uma tarefa deste modo:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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
    E podemos pode listar tarefas para Brandon deste modo:

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Se tudo isso funcionar, estamos prontos para adicionar o OAuth ao servidor de API REST.

Você tem um servidor de API REST com o MongoDB!

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>Etapa 18: adicionar autenticação ao nosso servidor de API REST
Agora que temos uma API REST em execução, vamos começar ativando-a com o Azure AD.

Na linha de comando, altere os diretórios para a pasta **azuread**, se você ainda não estiver nela.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Use o OIDCBearerStrategy que está incluído no passport-azure-ad
Até agora, criamos um servidor típico TODO do REST sem nenhum tipo de autorização. Aqui é onde começamos a juntar as peças.

1. Primeiro, precisamos indicar que queremos usar o Passport. Coloque isso logo após a configuração do servidor:

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > Ao escrever APIs, recomendamos que você sempre vincule os dados a algo exclusivo do token que o usuário não possa falsificar. Quando esse servidor armazena itens TODO, ele faz isso com base na ID de objeto do usuário no token que colocamos no campo "proprietário" (chamado por meio de token.oid). Isso garante que somente o usuário possa acessar seus TODOs. Não há exposição na API do "proprietário”, portanto, um usuário externo pode solicitar os TODOs de outras pessoas, mesmo que estejam autenticados.                    

2. Em seguida, vamos usar a estratégia de portador que vem com o `passport-azure-ad`. Por enquanto, apenas olhe o código, e explicaremos o restante daqui a pouco. Coloque isto depois do que você colou acima:

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
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


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

O Passport usa um padrão semelhante para todas as estratégias (Twitter, Facebook e assim por diante) que todos os gravadores de estratégia seguem. Observando a estratégia, você verá que passamos a ela uma função que tem um token e um done como parâmetros. A estratégia de volta para nós depois faz seu trabalho. Depois disso, armazenamos o usuário e guardamos o token, para que não precisemos pedi-lo novamente.

> [!IMPORTANT]
> O código anterior usa qualquer usuário que tente se autenticar em nosso servidor. Isso é conhecido como registro automático. Em servidores de produção, não convém permitir que qualquer pessoa entre sem primeiro passar por um processo de registro em que você decida. Esse geralmente é o padrão que você vê em aplicativos de consumidor que permitem que você se registre com o Facebook, mas depois pedem que preencha informações adicionais. Se esse não fosse um programa de linha de comando, poderíamos ter extraído o e-mail do objeto de token que é retornado e pedido que o usuário preenchesse informações adicionais. Como esse é um servidor de teste, basta adicioná-los ao banco de dados na memória.
>
>

### <a name="protect-some-endpoints"></a>Proteger alguns pontos de extremidade
Proteja os pontos de extremidade ao especificar a chamada `passport.authenticate()` com o protocolo que deseja usar.

Para que o nosso código de servidor faça algo mais interessante, vamos editar a rota.

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>Etapa 19: executar o aplicativo de servidor novamente e certificar-se de que rejeitará você
Vamos usar `curl` novamente para ver se agora temos proteção OAuth2 para nossos pontos de extremidade. Fazemos isso antes de executar qualquer um dos nossos SDKs de cliente para esse ponto de extremidade. Os cabeçalhos retornados devem ser suficientes para nos indicar que estamos no caminho certo.

1. Primeiro, verifique se a instância do MongoDB está em execução:

    `$sudo mongod`

2. Em seguida, vá até o diretório e inicie a ondulação.

      `$ cd azuread` `$ node server.js`

3. Tente uma POSTAGEM básica.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Um 401 é a resposta que você está procurando. Essa resposta indica que a camada do Passport está tentando redirecionar-se para o ponto de extremidade autorizado, que é exatamente o que você deseja.

## <a name="next-steps"></a>Próximas etapas
Você já fez tudo o que podia com esse servidor sem usar um cliente compatível com o OAuth2. Você precisará passar por um passo a passo adicional.

Agora, você aprendeu como implementar uma API REST usando o Restify e o OAuth2. Você também tem código mais do que suficiente para continuar desenvolvendo o seu serviço e aprender como compilar esse exemplo.

Se você estiver interessado nas próximas etapas de sua jornada ADAL, aqui estão alguns clientes ADAL suportados com os quais recomendamos que você continue trabalhando.

Faça a clonagem para a sua máquina de desenvolvedor e configure conforme indicado no passo a passo.

[ADAL para iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
