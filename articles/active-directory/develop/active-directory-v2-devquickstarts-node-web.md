---
title: "Conexão do aplicativo Web do Node.js no Azure Active Directory v2.0 | Microsoft Docs"
description: "Saiba como criar um aplicativo Web do Node.js que conecta um usuário usando uma conta pessoal da Microsoft e uma conta corporativa ou de estudante."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Adicionar conexão a um aplicativo Web do Node.js

> [!NOTE]
> Nem todos os cenários e recursos do Azure Active Directory funcionam com o ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0 ou v1.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 

Neste tutorial, usamos o Passport para realizar as seguintes tarefas:

* Em um aplicativo Web, conecte o usuário usando o Azure AD (Azure Active Directory) e o ponto de extremidade v2.0.
* Exibir informações sobre o usuário.
* Desconectar o usuário do aplicativo.

**Passport** é middleware de autenticação para o Node.js. Flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. No Passport, um conjunto abrangente de estratégias dão suporte à autenticação com o uso de um nome de usuário e uma senha, Facebook, Twitter e outras opções. Desenvolvemos uma estratégia para o Azure AD. Neste artigo, mostramos como instalar o módulo e, em seguida, adicione o plug-in `passport-azure-ad` do Azure AD.

## <a name="download"></a>Baixar
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Para seguir o tutorial, [baixe o esqueleto do aplicativo como um arquivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) ou clone o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Também obtenha o aplicativo concluído ao final deste tutorial.

## <a name="1-register-an-app"></a>1: registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga [estas etapas detalhadas](active-directory-v2-app-registration.md) para registrar um aplicativo. Verifique se você:

* Copie a **ID do Aplicativo** atribuída ao aplicativo. Ele é necessário para este tutorial.
* Adicionar a plataforma **Web** para seu aplicativo.
* Copiar o **URI de redirecionamento** do portal. Você deve usar o valor de URI padrão `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-add-prerequisities-to-your-directory"></a>2: Adicionar pré-requisitos ao diretório
No prompt de comando, altere os diretórios para acessar a pasta raiz, caso você ainda não esteja nela. Execute os seguintes comandos:

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

Além disso, usamos `passport-azure-ad` no esqueleto do início rápido:

* `npm install passport-azure-ad`

Isso instala as bibliotecas utilizadas pelo `passport-azure-ad`.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3: Configurar o aplicativo para usar a estratégia passport-node-js
Configure o middleware Express para usar o protocolo de autenticação OpenID Connect. O Passport é usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

1.  Na raiz do projeto, abra o arquivo Config.js. Na seção `exports.creds`, insira os valores de configuração do aplicativo.
  
  * `clientID`: a **ID do Aplicativo** atribuída ao aplicativo no portal do Azure.
  * `returnURL`: o **URI de Redirecionamento** inserido no portal.
  * `clientSecret`: o segredo gerado no portal.

2.  Na raiz do projeto, abra o arquivo App.js. Para invocar a estratégia OIDCStrategy, que acompanha o `passport-azure-ad`, adicione a seguinte chamada:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Para manipular solicitações de conexão, use a estratégia que você acabou de referenciar:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
      process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
          if (err) {
            return done(err);
          }
          if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
          }
          return done(null, user);
        });
      });
    }
  ));
  ```

O Passport usa um padrão semelhante para todas as suas estratégias (Twitter, Facebook e assim por diante). Todos os gravadores de estratégia seguem o padrão. Passe uma `function()` para a estratégia que usa um token e `done` como parâmetros. A estratégia é retornada depois de fazer todo o seu trabalho. Armazene o usuário e guarde o token para que você não precise solicitá-lo novamente.

  > [!IMPORTANT]
  > O código anterior usa qualquer usuário que pode se autenticar no servidor. Isso é conhecido como registro automático. Em um servidor de produção, você não desejará permitir que nenhuma pessoa entre sem primeiro passar por um processo de registro escolhido por você. Geralmente, esse é o padrão visto em aplicativos de consumidor. O aplicativo pode permitir que você se registre com o Facebook, mas, em seguida, solicita que você insira informações adicionais. Se você não estava usando um programa de linha de comando para este tutorial, você poderá extrair o email do objeto de token que é retornado. Em seguida, você poderá solicitar ao usuário que insira informações adicionais. Como esse é um servidor de teste, você adiciona o usuário diretamente ao banco de dados na memória.
  > 
  > 

4.  Adicione os métodos usados para acompanhar os usuários que estão conectados, conforme solicitado pelo Passport. Isso inclui a serialização e a desserialização de informações do usuário:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
  var users = [];

  var findByEmail = function(email, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
      var user = users[i];
      log.info('we are using user: ', user);
      if (user.email === email) {
        return fn(null, user);
      }
    }
    return fn(null, null);
  };
  ```

5.  Adicione o código que carrega o mecanismo Express. Use o padrão /views e /routes fornecido pelo Express:

  ```JavaScript

  // Set up Express (section 2)

  var app = express();

  app.configure(function() {
    app.set('views', __dirname + '/views');
    app.set('view engine', 'ejs');
    app.use(express.logger());
    app.use(express.methodOverride());
    app.use(cookieParser());
    app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
    app.use(bodyParser.urlencoded({ extended : true }));
    // Initialize Passport!  Also use passport.session() middleware, to support
    // persistent login sessions (recommended).
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(app.router);
    app.use(express.static(__dirname + '/../../public'));
  });

  ```

6.  Adicione as rotas POST que entregam as solicitações de conexão reais ao mecanismo `passport-azure-ad`:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4: Usar o Passport para emitir solicitações de entrada e saída ao Azure AD
Seu aplicativo agora está configurado para se comunicar com o ponto de extremidade v2.0 usando o protocolo de autenticação OpenID Connect. A estratégia `passport-azure-ad` cuida de todos os detalhes da criação de mensagens de autenticação, validação de tokens do Azure AD e manutenção da sessão do usuário. Tudo o que resta a fazer é fornecer aos usuários uma maneira de entrar e sair do serviço e coletar mais informações sobre o usuário que está conectado.

1.  Adicione os métodos **default**, **login**, **account** e **logout** ao arquivo App.js:

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Estes são os detalhes:
    
    * A rota `/` redireciona para a exibição index.ejs. Ela passa o usuário na solicitação (se houver).
    * A rota `/account` primeiro *garante que você está autenticado* (você implementa isso no código a seguir). Em seguida, ela passa o usuário na solicitação. Isso serve para obter mais informações sobre o usuário.
    * A rota `/login` chama o autenticador `azuread-openidconnect` por meio de `passport-azuread`. Caso não obtenha êxito, ele redirecionará o usuário novamente para `/login`.
    * A rota `/logout` chama a exibição logout.ejs (e a rota). Isso limpa os cookies e, em seguida, retorna o usuário para index.ejs.

2.  Adicione o método **EnsureAuthenticated** que você usou anteriormente em `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  No App.js, crie o servidor:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5: Criar as exibições e rotas no Express que são mostradas ao usuário no site
Adicione as rotas e exibições que mostram informações ao usuário. As rotas e exibições também manipulam as rotas `/logout` e `/login` que você criou.

1. No diretório raiz, crie a rota `/routes/index.js`.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  No diretório raiz, crie a rota `/routes/user.js`.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` e `/routes/user.js` são rotas simples que passam a solicitação para as exibições, incluindo o usuário, se ele estiver presente.

3.  No diretório raiz, crie a exibição `/views/index.ejs`. Essa página chama os métodos **login** e **logout**. Você também usa a exibição `/views/index.ejs` para capturar informações de conta. Use o `if (!user)` condicional como o usuário que está sendo passado na solicitação. Ele é uma prova de que você tem um usuário conectado.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  No diretório raiz, crie a exibição `/views/account.ejs`. A exibição `/views/account.ejs` permite exibir informações adicionais colocadas pelo `passport-azuread` na solicitação de usuário.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Adicione um layout. No diretório raiz, crie a exibição `/views/layout.ejs`.

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  Para compilar e executar seu aplicativo, execute `node app.js`. Em seguida, acesse `http://localhost:3000`.

7.  Entre com uma conta pessoal da Microsoft ou uma conta corporativa ou de estudante. Observe que a identidade do usuário é refletida na lista /account. 

Agora você tem um aplicativo Web que é protegido com o uso de protocolos padrão do setor. Você pode autenticar os usuários em seu aplicativo usando as contas pessoais e corporativas ou de estudante deles.

## <a name="next-steps"></a>Próximas etapas
Para referência, o exemplo completo (sem seus valores de configuração) é [fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Você também pode cloná-la no GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

A seguir, você pode passar para tópicos mais avançados. Você pode desejar experimentar:

[Proteger uma API Web do Node.js usando o ponto de extremidade v2.0](active-directory-v2-devquickstarts-node-api.md)

Estes são alguns recursos adicionais:

* [Guia do desenvolvedor do Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Marcação “azure-active-directory” do Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos
Incentivamos você a se inscrever para receber notificações quando ocorrerem incidentes de segurança. Na página [Notificações de Segurança Técnica da Microsoft](https://technet.microsoft.com/security/dd252948), assine Alertas de Avisos de Segurança.

