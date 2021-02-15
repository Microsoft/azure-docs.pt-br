---
title: Início rápido da biblioteca de clientes do Node.js de Leitura Avançada
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você cria um aplicativo Web do zero e adiciona a funcionalidade da API de Leitura Avançada.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: c34ed97339076a4412f8b6bf36ddf342205c88be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376555"
---
A [Leitura Avançada](https://www.onenote.com/learningtools) é uma ferramenta inclusivamente desenvolvida que implementa técnicas comprovadas para melhorar a compreensão de leitura para novos leitores, aprendizes da idioma e pessoas com diferenças de aprendizado, como dislexia. Você pode usar a Leitura Avançada em seus aplicativos para isolar texto e aprimorar o foco, exibir imagens para palavras usadas com frequência, realçar partes de fala, ler o texto selecionado em voz alta, traduzir palavras e frases em tempo real e muito mais.

Neste guia de início rápido, você cria um aplicativo Web do zero e integra a Leitura Avançada usando a biblioteca de clientes de Leitura Avançada. Uma amostra funcional completa deste guia de início rápido está disponível [no GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Node.js](https://nodejs.org/) e [Yarn](https://yarnpkg.com)
* Um IDE como o [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Criar um aplicativo Web Node.js com o Express

Crie um aplicativo Web Node.js com a ferramenta `express-generator`.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Instale as dependências do YARN e adicione as dependências `request` e `dotenv`, que serão usadas posteriormente no início rápido.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>Configurar a autenticação

### <a name="configure-authentication-values"></a>Configurar os valores de autenticação

Crie um arquivo chamado _.env_ na raiz de seu projeto. Cole o código a seguir nele, fornecendo os valores fornecidos quando você criou o recurso de Leitura Avançada.
Não inclua aspas ou os caracteres "{" e "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de não confirmar esse arquivo no controle do código-fonte, uma vez que ele contém segredos que não devem ser tornados públicos.

Em seguida, abra _app.js_ e adicione o seguinte à parte superior do arquivo. Isso carrega as propriedades definidas no arquivo .env como variáveis de ambiente no Node.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Atualizar o roteador para adquirir o token
Abra o arquivo _routes\index.js_ e substitua o código gerado automaticamente pelo código a seguir.

Este código cria um ponto de extremidade de API, que adquire um token de autenticação do Azure AD usando a senha de entidade de serviço. Ele também recupera o subdomínio. Em seguida, ele retorna um objeto que contém o token e o subdomínio.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            },
            url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: process.env.CLIENT_ID,
                client_secret: process.env.CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

O ponto de extremidade da API **GetTokenAndSubdomain** deve ser protegido por alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço de Leitura Avançada e cobrança; esse trabalho está além do escopo deste início rápido.

## <a name="add-sample-content"></a>Adicionar o conteúdo de exemplo

Agora, vamos adicionar conteúdo de exemplo a este aplicativo Web. Abra _views\index.pug_ e substitua o código gerado automaticamente por este exemplo:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


Observe que todo o texto tem um atributo **lang**, que descreve os idiomas do texto. Esse atributo ajuda a Leitura Avançada a fornecer recursos relevantes de idioma e gramática.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Nosso aplicativo Web está pronto. Inicie o aplicativo executando:

```bash
npm start
```

Abra o navegador da Web e navegue até _http://localhost:3000_ . Você deve ver o seguinte:

![Aplicativo de exemplo – Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Iniciar a Leitura Avançada

Ao clicar no botão "Leitura Avançada", você verá a Leitura Avançada iniciada com o conteúdo na página.

![Leitura Avançada – Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](../../reference.md)