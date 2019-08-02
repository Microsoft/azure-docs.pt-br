---
title: 'Tutorial: Iniciar a Leitura Avançada usando Node.js'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Node.js que inicia a Leitura Avançada.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ea9728ac54ae357925bd666ffecf270172137f77
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688297"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Tutorial: Iniciar a Leitura Avançada (Node.js)

Na [visão geral](./overview.md), você aprendeu sobre o que é a Leitura Avançada e como ela implementa técnicas comprovadas para melhorar a compreensão de leitura para aprendizes do idioma, leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial aborda como criar um aplicativo Web Node.js que inicia a Leitura Avançada. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo Web Node.js com o Express
> * Adquirir um token de acesso
> * Iniciar a Leitura Avançada com o conteúdo de exemplo
> * Especificar o idioma do conteúdo
> * Especifique o idioma da interface de Leitura Avançada
> * Iniciar a Leitura Avançada com o conteúdo de matemática

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso de Leitura Avançada configurado para autenticação do Azure AD (Azure Active Directory). Siga [estas instruções](./azure-active-directory-authentication.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Node.js](https://nodejs.org/) e [Yarn](https://yarnpkg.com)
* Um IDE como o [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Criar um aplicativo Web Node.js com o Express

Crie um aplicativo Web Node.js com a ferramenta `express-generator`.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Instale as dependências do yarn e adicione as dependências `request` e `dotenv`, o que será usado posteriormente no tutorial.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Em seguida, escreva uma API de back-end para recuperar um token de autenticação do Azure AD.

Você precisará de alguns valores da etapa de pré-requisito de configuração da autenticação do Azure AD acima para esta parte. Veja novamente o arquivo de texto que você salvou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Quando você tiver esses valores, crie um arquivo chamado _.env_ e cole o código a seguir nele, fornecendo os valores de propriedade personalizada acima.

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

Abra o arquivo _routes\index.js_ e o seguinte será importado na parte superior do arquivo:

```javascript
var request = require('request');
```

Em seguida, adicione o código a seguir diretamente abaixo dessa linha. Este código cria um ponto de extremidade de API, que adquire um token de autenticação do Azure AD usando a senha de entidade de serviço e depois retorna esse token. Também há um segundo ponto de extremidade para recuperar o subdomínio.

```javascript
router.get('/getimmersivereadertoken', function(req, res) {
  request.post ({
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
      function(err, resp, token) {
          if (err) {
              return res.status(500).send('CogSvcs IssueToken error');
          }

          return res.send(JSON.parse(token).access_token);
      }
  );
});

router.get('/subdomain', function (req, res) {
    return res.send(process.env.SUBDOMAIN);
});
```

O ponto de extremidade da API do **getimmersivereadertoken** deve ser protegido por alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço de Leitura Avançada e cobrança; esse trabalho está além do escopo deste tutorial.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar a Leitura Avançada com o conteúdo de exemplo

1. Abra _views\layout.pug_ e adicione o seguinte código sob a marca `head`, antes da marca `body`. Essas marcas `script` carregam o [SDK de Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Abra _views\index.pug_ e substitua o conteúdo pelo código a seguir. Esse código preenche a página com conteúdo de exemplo e adiciona um botão que inicia a Leitura Avançada.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.

        function getImmersiveReaderTokenAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/getimmersivereadertoken',
                    type: 'GET',
                    success: token => {
                        resolve(token);
                    },
                    error: err => {
                        console.log('Error in getting token!', err);
                        reject(err);
                    }
                });
            });
        }

        function getSubdomainAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/subdomain',
                    type: 'GET',
                    success: subdomain => { resolve(subdomain); },
                    error: err => { reject(err); }
                });
            });
        }

        async function launchImmersiveReader() {
            const content = {
                title: document.getElementById('title').innerText,
                chunks: [{
                    content: document.getElementById('content').innerText + '\n\n',
                    lang: 'en'
                }]
            };

            const token = await getImmersiveReaderTokenAsync();
            const subdomain = await getSubdomainAsync();

            ImmersiveReader.launchAsync(token, subdomain, content);
        }
    ```

3. Nosso aplicativo Web está pronto. Inicie o aplicativo executando:

    ```bash
    npm start
    ```

4. Abra o navegador da Web e navegue até _http://localhost:3000_ . Veja o conteúdo acima na página. Clique no botão **Leitura Avançada** para iniciar a Leitura Avançada com seu conteúdo.

## <a name="specify-the-language-of-your-content"></a>Especificar o idioma do conteúdo

A Leitura Avançada oferece suporte para vários idiomas diferentes. Você pode especificar o idioma do seu conteúdo, seguindo as etapas abaixo.

1. Abra _views\index.pug_ e adicione o seguinte código abaixo da marca `p(id=content)` que você adicionou na etapa anterior. Esse código adiciona algum conteúdo em espanhol à sua página.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. No código JavaScript, adicione o seguinte acima da chamada para `ImmersiveReader.launchAsync`. Esse código transmite o conteúdo em espanhol para a Leitura Avançada.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navegue até _http://localhost:3000_ novamente. Você verá o texto em espanhol na página e, ao clicar na **Leitura Avançada**, ele também será exibido na Leitura Avançada.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Especifique o idioma da interface de Leitura Avançada

Por padrão, o idioma da interface da Leitura Avançada corresponde à configurações de idioma do navegador. Você também pode especificar o idioma da interface da Leitura Avançada com o código a seguir.

1. Em _views\index.pug_, substitua a chamada para `ImmersiveReader.launchAsync(token, content)` pelo código abaixo.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Navegue até _http://localhost:3000_ . Quando você inicia o leitor imersivo, a interface é mostrada em francês.

## <a name="launch-the-immersive-reader-with-math-content"></a>Iniciar a Leitura Avançada com o conteúdo de matemática

Você pode incluir o conteúdo de matemática na Leitura Avançada usando o [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modifique _views\index.pug_ para incluir o seguinte código acima da chamada para `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navegue até _http://localhost:3000_ . Ao iniciar a Leitura Avançada e rolar para baixo, você verá a fórmula matemática.

## <a name="next-steps"></a>Próximas etapas

* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)
* Visualize exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
