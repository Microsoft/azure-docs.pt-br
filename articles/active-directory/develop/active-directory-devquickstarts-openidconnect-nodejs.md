---
title: "Introdução à entrada e saída no Azure AD usando Node.js | Microsoft Docs"
description: Saiba como criar um aplicativo Web MVC Node.js Express que se integra ao Azure AD para entrar.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 13317b016f9ff3955f376b858645c42668b0de42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="nodejs-web-app-sign-in-and-sign-out-with-azure-ad"></a>Entrada e saída do aplicativo Web Node.js com o Azure AD
Aqui usaremos o Passport para:

* Conectar o usuário para o aplicativo com o Azure Active Directory (AD do Azure).
* Exibir informações sobre o usuário.
* Desconectar o usuário do aplicativo.

O Passport é um middleware de autenticação para o Node.js. Flexível e modular, o Passport pode ser colocado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias dão suporte à autenticação que usa um nome de usuário e senha, Facebook, Twitter e mais. Desenvolvemos uma estratégia para o Active Directory do Microsoft Azure. Instalamos esse módulo e então adicionamos o plug-in `passport-azure-ad` do Microsoft Azure Active Directory.

Para fazer isso, execute as seguintes etapas:

1. Registrar um aplicativo
2. Configure seu aplicativo para usar a estratégia `passport-azure-ad`.
3. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprima dados sobre o usuário.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um arquivo .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

O aplicativo completo também é fornecido no final deste tutorial.

## <a name="step-1-register-an-app"></a>Etapa 1: Registrar um aplicativo
1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu na parte superior da página, selecione sua conta. Sob o **diretório** , escolha onde você deseja registrar seu aplicativo de locatário do Active Directory.

3. Selecione **Mais Serviços** no menu no lado esquerdo da tela e selecione **Azure Active Directory**.

4. Selecione **Registros do aplicativo**e, em seguida, selecione **Adicionar**.

5. Siga os prompts para criar um **Aplicativo Web** e/ou uma **API Web**.
  * O **nome** do aplicativo descreve o aplicativo aos usuários.

  * A **URL de logon** é a URL base do seu aplicativo.  O padrão do esqueleto é `http://localhost:3000/auth/openid/return``.

6. Depois de registrar, AD do Azure atribui seu aplicativo uma ID de aplicativo único. Você precisará desse valor nas seções a seguir, então copie-o da página do aplicativo.
7. Na página **Configurações** -> **Propriedades** do aplicativo, atualize o URI da ID do Aplicativo. O **URI da ID do aplicativo** é um identificador exclusivo para seu aplicativo. A convenção é usar o formato `https://<tenant-domain>/<app-name>`, por exemplo: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Etapa 2: Adicionar pré-requisitos a seu diretório
1. Na linha de comando, altere o diretório para a pasta raiz se você ainda não estiver lá e execute os seguintes comandos:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Além disso, você precisa `passport-azure-ad`:
    * `npm install passport-azure-ad`

Isso instala as bibliotecas que `passport-azure-ad` depende.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Etapa 3: Configurar seu aplicativo para usar a estratégia passport-node-js
Aqui, configuramos o Express para usar o protocolo de autenticação OpenID Connect.  O Passport é usado para fazer várias coisas, incluindo emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário.

1. Para começar, abra o arquivo `config.js` na raiz do projeto e insira os valores de configuração do aplicativo na seção `exports.creds`.

  * `clientID` é a **ID do Aplicativo** atribuída ao seu aplicativo no portal de registro.

  * `returnURL` é o **Uri de Redirecionamento** inserido no portal.

  * O `clientSecret` é o segredo gerado no portal.

2. Em seguida, abra o arquivo `app.js` na raiz do projeto. Em seguida, adicione a chamada a seguir para invocar a estratégia `OIDCStrategy` que vem com o `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Depois disso, use a estratégia que acabamos de referenciar para manipular nossas solicitações de entrada.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
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
O Passport usa um padrão semelhante para todas as estratégias (Twitter, Facebook e assim por diante) que todos os gravadores de estratégia seguem. Observando a estratégia, você verá que passamos a ela uma função que tem um token e um done como parâmetros. A estratégia de volta para nós depois faz seu trabalho. Em seguida, queremos armazenar o usuário e acrescentar o token, para que não precisemos pedi-lo novamente.

> [!IMPORTANT]
O código anterior usa qualquer usuário que tente se autenticar em nosso servidor. Isso é conhecido como registro automático. Recomendamos que você não permita que ninguém se autentique em um servidor de produção sem primeiro se registrar por meio de um processo escolhido por você. Geralmente, esse é o padrão visto em aplicativos de consumidor, que permitem se registrar com o Facebook, mas depois solicitam o fornecimento de informações adicionais. Se esse não fosse um aplicativo de exemplo, poderíamos ter extraído o endereço de email do usuário do objeto de token retornado e, em seguida, solicitado a ele que preenchesse informações adicionais. Como esse é um servidor de teste, nós o adicionamos ao banco de dados em memória.


4. Em seguida, vamos adicionar os métodos que permitem controlar os usuários autenticados conforme exigido pelo Passport. Esses métodos incluem a serialização e a desserialização de informações do usuário.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
            done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
            findByEmail(id, function (err, user) {
                done(err, user);
            });
            });

            // array to hold signed-in users
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

5.  Em seguida, adicionaremos o código para carregar o mecanismo Express. Aqui você vê que usamos o padrão /views e /routes que o Express fornece.

    ```JavaScript

        // configure Express (section 2)

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

6. Por fim, adicionaremos as rotas que entregarão as solicitações de logon reais ao mecanismo `passport-azure-ad`:


       ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

            // GET /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.get('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });

            // POST /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.post('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });
       ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Etapa 4: usar o Passport para emitir solicitações de entrada e saída ao Azure AD
Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade usando o protocolo de autenticação OpenID Connect.  O `passport-azure-ad` cuidou de todos os detalhes da criação de mensagens de autenticação, validação de tokens do Azure AD e manutenção das sessões do usuário. Tudo o que resta é fornecer aos usuários uma maneira de entrar e sair e obter informações adicionais sobre os usuários que entraram.

1. Primeiro, adicionaremos a conta de entrada padrão e os métodos de saída ao nosso arquivo `app.js`:

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
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  Vamos examiná-los detalhadamente:

  * O roteiro `/` é redirecionada para a exibição de index.ejs passando o usuário na solicitação (se houver).
  * O roteiro `/account` primeiro *garante que sejamos autenticados* (implementamos no exemplo abaixo) e passa o usuário na solicitação para que possamos obter informações adicionais sobre ele.
  * O roteiro `/login` chama nosso autenticador azuread-openidconnect de `passport-azuread`. Caso não obtenha êxito, ele redireciona o usuário para /login.
  * O roteiro `/logout` simplesmente chamará logout.ejs (e o roteiro), o que limpa os cookies e, em seguida, retorna o usuário para index.ejs.

3. Para a última parte do `app.js`, vamos adicionar o método **EnsureAuthenticated** que é usado em `/account`, como mostrado anteriormente.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Por fim, vamos criar o próprio servidor em `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Etapa 5: Para exibir nosso usuário no site, crie as exibições e os roteiros no Express
Agora `app.js` for concluída. Nós simplesmente precisamos adicionar os roteiros e as exibições que mostram as informações de que precisamos para o usuário e lidar com os roteiros `/logout` e `/login` criados.

1. Criar a rota `/routes/index.js` no diretório raiz.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Criar a rota `/routes/user.js` no diretório raiz.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 Eles passam a solicitação para nossos modos de exibição, incluindo o usuário se presente.

3. Crie o modo de exibição `/views/index.ejs` no diretório raiz. Essa é uma página simples que chama os nossos métodos de logon e logoff e nos permite obter dados da conta. Observe que podemos usar o `if (!user)` condicional, pois o usuário que está sendo passado na solicitação é uma prova de que temos um usuário conectado.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Crie a exibição `/views/account.ejs` no diretório raiz para que possamos exibir as informações adicionais que `passport-azuread` colocou na solicitação do usuário.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Vamos fazer essa análise boa adicionando um layout. Crie a exibição '/views/layout.ejs' sob o diretório raiz.

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
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>Próximas etapas
Por fim, compile e execute seu aplicativo. Executar `node app.js`e, em seguida, vá para `http://localhost:3000`.

Entre com uma conta pessoal da Microsoft ou uma conta corporativa ou de estudante e observe como a identidade do usuário é exibida na lista /account. Agora você tem um aplicativo Web protegido por protocolos padrão do setor, que podem autenticar usuários com as respectivas contas pessoais e corporativas ou de estudante.

Para referência, o exemplo completo (sem seus valores de configuração) é [fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Como alternativa, você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Agora você pode ir para tópicos mais avançados. Você pode desejar experimentar:

[Proteger uma API Web com o Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
