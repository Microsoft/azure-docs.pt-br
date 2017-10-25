---
title: Adicionar a entrada a um aplicativo Web do Node.js para o Azure B2C | Microsoft Docs
description: "Como criar um aplicativo Web do Node.js que conecta os usuários usando um locatário do B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: c85b8f8434d1e837ac96ac63b9b37f990677ed6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: adicionar entrada a um aplicativo Web do Node.js

**Passport** é middleware de autenticação para o Node.js. Extremamente flexível e modular, o Passport pode ser instalado sem impedimento em qualquer aplicativo Web baseado em Express ou Restify. Um conjunto abrangente de estratégias que dão suporte à autenticação usando um nome de usuário e uma senha, o Facebook, o Twitter e muito mais.

Desenvolvemos uma estratégia para o Azure AD (Active Directory do Azure). Você instalará esse módulo e, em seguida, adicionará o plug-in `passport-azure-ad` do Azure AD.

Para fazer isso, você precisa:

1. Registre um aplicativo usando o Azure AD.
2. Configure seu aplicativo para usar o plug-in `passport-azure-ad`.
3. Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprima dados de usuário.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Para acompanhá-lo, você pode [baixar o esqueleto do aplicativo como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). Também é possível clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

O aplicativo completo é fornecido no fim deste tutorial.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário.  Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo cliente e a API Web serão representados por uma única **ID do Aplicativo**, pois eles abrangem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Incluir um **aplicativo Web**/**api Web** no aplicativo.
- Digite `http://localhost:3000/auth/openid/return` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
- Crie um **Segredo de aplicativo** para seu aplicativo e copie-o. Você precisará dela mais tarde. Observe que esse valor precisa ser [seguido por caracteres de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de ser usado.
- Copie a **ID de aplicativo** atribuída ao aplicativo. Você também precisará dela mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas

No Azure AD B2C, toda experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Esse aplicativo contém três experiências de identidade: inscrição, entrada e entrada usando o Facebook. Você precisa criar esta política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Ao criar as três políticas, não deixe de:

- Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
- Escolha as declarações de aplicativo **Nome de exibição** e **ID do Objeto** em todas as políticas. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política depois de criá-la. Ele deve ter o prefixo `b2c_1_`.  Você precisará esses nomes de política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre o funcionamento das políticas no Azure AD B2C, comece com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Adicionar pré-requisitos a seu diretório

Na linha de comando, altere os diretórios para a pasta raiz, se você não ainda estiver lá. Execute os seguintes comandos:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Além disso, usamos `passport-azure-ad` para a visualização no esqueleto do Início Rápido.

- `npm install passport-azure-ad`

Isso instalará as bibliotecas das quais o `passport-azure-ad` depende.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Configurar seu aplicativo para usar a estratégia do Passport-Node.js
Configure o middleware Express para usar o protocolo de autenticação OpenID Connect. O Passport será usado para emitir solicitações de entrada e saída, gerenciar sessões de usuário e obter informações sobre usuários, entre outras ações.

Abra o arquivo `config.js` na raiz do projeto e insira os valores de configuração do aplicativo na seção `exports.creds`.
- `clientID`: a **ID do Aplicativo** atribuída a seu aplicativo no portal de registro.
- `returnURL`: o **URI de Redirecionamento** que você inseriu no portal.
- `tenantName`: o nome do locatário de seu aplicativo; por exemplo, **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Abra o arquivo `app.js` na raiz do projeto. Adicione a chamada a seguir para invocar a estratégia `OIDCStrategy` que vem com o `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Use a estratégia referenciada para manipular solicitações de entrada.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
O Passport usa um padrão semelhante para todas as suas estratégias (incluindo o Twitter e o Facebook). Todos os gravadores de estratégia seguem esse padrão. Ao examinar a estratégia, você pode ver que passa para ela um `function()` que tem um token e um `done` como parâmetros. A estratégia volta para você após fazer seu trabalho. Armazene o usuário e guarde o token para que não precise pedi-lo novamente.

> [!IMPORTANT]
O código anterior usa todos os usuários que o servidor autentica. Esse é o registro automático. Ao usar servidores de produção, não convém permitir a inclusão de usuários, a menos que eles passem por um processo de registro configurado por você. Frequentemente, você pode ver esse padrão em aplicativos de consumidor. Eles permitem que você se registre usando o Facebook, mas, em seguida, pedem que você preencha informações adicionais. Se o aplicativo não fosse um exemplo, poderíamos extrair um endereço de email do objeto de token que é retornado e, em seguida, pedir ao usuário que preenchesse informações adicionais. Como esse é um servidor de teste, basta adicionar usuários ao banco de dados na memória.

Adicione os métodos que lhe permitem acompanhar os usuários que se inscreveram, conforme solicitado pelo Passport. Isso inclui a serialização e a desserialização de informações do usuário:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Adicione o código para carregar o mecanismo Express. A seguir, você pode ver que usamos o padrão `/views` e `/routes` padrão fornecidos pelo Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Adicione as rotas `POST` que entregarão as solicitações de logon reais ao mecanismo `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Usar o Passport para emitir solicitações de entrada e saída ao AD do Azure

Seu aplicativo agora está configurado corretamente para se comunicar com o ponto de extremidade v2.0 usando o protocolo de autenticação OpenID Connect. O `passport-azure-ad` cuidou de todos os detalhes da criação de mensagens de autenticação, validação de tokens do Azure AD e manutenção da sessão do usuário. Tudo o que resta é fornecer aos usuários uma maneira de entrar e sair e obter informações adicionais sobre os usuários que entraram.

Primeiro, adicione a conta de entrada padrão e métodos de saída ao arquivo `app.js`:

```JavaScript

//Routes (Section 4)

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

Para examinar esses métodos em detalhes:
- A rota `/` é redirecionada para o modo de exibição `index.ejs` passando o usuário na solicitação (se houver).
- A rota `/account` verifica primeiro se você está autenticado (a implementação para isso está abaixo). Em seguida, ela passa o usuário na solicitação para que você possa obter informações adicionais sobre o usuário.
- A rota `/login` chama o autenticador `azuread-openidconnect` de `passport-azure-ad`. Se não tiver êxito, a rota redirecionará o usuário de volta para `/login`.
- `/logout` simplesmente chama `logout.ejs` (e sua rota). Isso limpa os cookies e, em seguida, retorna o usuário para `index.ejs`.


Para a última parte de `app.js`, adicione o método `EnsureAuthenticated` que é usado na rota `/account`.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Por fim, crie o próprio servidor em `app.js`.

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Crie esses modos de exibição e rotas no Express para chamar as políticas

O `app.js` já foi concluído. Basta adicionar as rotas e os modos de exibição que lhe permitem chamar as políticas de entrada e de inscrição. Elas também lidam com as rotas `/logout` e `/login` que você criou.

Criar a rota `/routes/index.js` no diretório raiz.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Criar a rota `/routes/user.js` no diretório raiz.

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Essas rotas simples passam solicitações para seus modos de exibição. Elas incluem o usuário, caso esteja presente.

Crie o modo de exibição `/views/index.ejs` no diretório raiz. Essa é uma página simples que chama as políticas de entrada e saída. Você também pode usá-la para obter informações sobre a conta. Observe que você pode usar o `if (!user)` condicional enquanto o usuário é passado na solicitação para comprovar que o usuário está conectado.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Crie o modo de exibição `/views/account.ejs` no diretório raiz para que você possa exibir informações adicionais que `passport-azure-ad` colocou na solicitação do usuário.

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
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Agora você pode compilar e executar o aplicativo.

Execute `node app.js` e navegue até `http://localhost:3000`


Registre-se ou entre no aplicativo usando o email ou o Facebook. Saia e entre novamente como outro usuário.

##<a name="next-steps"></a>Próximas etapas

Para referência, o exemplo completo (sem seus valores de configuração) é [fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Agora você pode passar para tópicos mais avançados. Você pode experimentar:

[Proteger uma API Web usando o modelo B2C no Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
