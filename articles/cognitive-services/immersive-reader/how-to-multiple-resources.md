---
title: Integrar vários recursos da Leitura Avançada
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Node.js que inicia a Leitura Avançada usando vários recursos da Leitura Avançada.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619983"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>Integrar vários recursos da Leitura Avançada

Na [visão geral](./overview.md), você aprendeu sobre o que é a Leitura Avançada e como ela implementa técnicas comprovadas para melhorar a compreensão de leitura para aprendizes do idioma, leitores emergentes e estudantes com diferenças de aprendizado. No [início rápido](./quickstarts/client-libraries.md), você aprendeu a usar o leitor de imersão com um único recurso. Este tutorial aborda como integrar vários recursos da Leitura Avançada no mesmo aplicativo. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar vários recursos da Leitura Avançada em um grupo de recursos existente
> * Iniciar a Leitura Avançada usando vários recursos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Siga o [início rápido](./quickstarts/client-libraries.md?pivots=programming-language-nodejs) para criar um aplicativo Web que inicia a Leitura Avançada com Node.js. Nesse início rápido, você configura apenas um recurso da Leitura Avançada. Nós vamos nos basear nele neste tutorial.

## <a name="create-the-immersive-reader-resources"></a>Criar os recursos da Leitura Avançada

Siga [estas instruções](./how-to-create-immersive-reader.md) para criar cada recurso da Leitura Avançada. O script **Create-ImmersiveReaderResource** tem `ResourceName` , `ResourceSubdomain` e `ResourceLocation` como parâmetros. Eles devem ser exclusivos para cada recurso que está sendo criado. Os parâmetros restantes devem ser iguais aos que você usou ao configurar seu primeiro recurso da Leitura Avançada. Dessa forma, cada recurso pode ser vinculado ao mesmo grupo de recursos do Azure e ao aplicativo do Azure AD.

O exemplo a seguir mostra como criar dois recursos, um na região WestUS e outro na região EastUS. Observe os valores exclusivos para `ResourceName`, `ResourceSubdomain` e `ResourceLocation`.

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Adicionar recursos à configuração do ambiente

No guia de início rápido, você criou um arquivo de configuração de ambiente que contém os parâmetros `TenantId`, `ClientId`, `ClientSecret` e `Subdomain`. Como todos os seus recursos usam o mesmo aplicativo do Azure AD, podemos usar os mesmos valores para `TenantId`, `ClientId` e `ClientSecret`. A única alteração que precisa ser feita é listar cada subdomínio de cada recurso.

O novo arquivo __.env__ agora deve ser semelhante ao seguinte:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Certifique-se de não confirmar esse arquivo no controle do código-fonte, uma vez que ele contém segredos que não devem ser tornados públicos.

Em seguida, vamos modificar o arquivo _routes\index.js_ que criamos para dar suporte a nossos vários recursos. Substitua o conteúdo dele pelo código a seguir.

Assim como antes, este código cria um ponto de extremidade de API, que adquire um token de autenticação do Azure AD usando a senha de entidade de serviço. Desta vez, ele permite que o usuário especifique uma localização para o recurso e o passe como um parâmetro de consulta. Em seguida, ele retorna um objeto que contém o token e o subdomínio correspondente.

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

O ponto de extremidade da API do **getimmersivereaderlaunchparams** deve ser protegido por alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço e cobrança de Leitura Avançada; esse trabalho está além do escopo deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

1. Abra _views\index.pug_ e substitua o conteúdo pelo código a seguir. Esse código preenche a página com conteúdo de exemplo e adiciona dois botões que iniciam a Leitura Avançada. Um para iniciar a Leitura Avançada para o recurso na região EastUS e outro para o recurso na região WestUS.

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. Nosso aplicativo Web está pronto. Inicie o aplicativo executando:

    ```bash
    npm start
    ```

4. Abra o navegador e navegue até `http://localhost:3000`. Veja o conteúdo acima na página. Clique no botão da **Leitura Avançada no EastUS** ou no botão da **Leitura Avançada no WestUS** para iniciar a Leitura Avançada usando esses recursos respectivos.

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)
* Visualize exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)