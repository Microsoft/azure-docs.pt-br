---
title: 'Início Rápido: Criar um aplicativo Web que inicia a Leitura Avançada com C#'
titlesuffix: Azure Cognitive Services
description: Neste início rápido, você cria um aplicativo Web do zero e adiciona a funcionalidade da API de Leitura Avançada.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 978b56f5c38a154c79544c7deb5d49396b1deff9
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688857"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Início Rápido: Criar um aplicativo Web que inicia a Leitura Avançada (C#)

A [Leitura Avançada](https://www.onenote.com/learningtools) é uma ferramenta projetada de forma inclusiva que implementa técnicas comprovadas para melhorar a compreensão da leitura.

Neste Início Rápido, você cria um aplicativo Web do zero e integra a Leitura Avançada usando o SDK de Leitura Avançada. Um exemplo de funcionamento completo deste Início Rápido está disponível [aqui](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Um recurso de Leitura Avançada configurado para autenticação do Azure AD (Azure Active Directory). Siga [estas instruções](./azure-active-directory-authentication.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de exemplo. Salve a saída da sessão em um arquivo de texto para referência futura.

## <a name="create-a-web-app-project"></a>Criar um projeto do aplicativo Web

Crie um novo projeto no Visual Studio, usando o modelo de aplicativo Web ASP.NET Core com Model-View-Controller interno.

![Novo Projeto](./media/vswebapp.png)

![Novo aplicativo Web ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Você precisará de alguns valores da etapa de pré-requisito de configuração da autenticação do Azure AD acima para esta parte. Veja novamente o arquivo de texto que você salvou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Clique com botão direito do mouse no projeto no _Gerenciador de Soluções_ e escolha **Gerenciar Segredos do Usuário**. Isso abrirá um arquivo chamado _secrets.json_. Substitua o conteúdo desse arquivo pelo mostrado a seguir, fornecendo os valores de propriedade personalizados acima.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Abra _Controllers\HomeController.cs_ e substitua o arquivo pelo código a seguir.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Adicione o pacote NuGet Microsoft.IdentityModel.Clients.ActiveDirectory

O código acima usa objetos do pacote NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**; portanto, você precisará adicionar uma referência a esse pacote no projeto.

Abra o Console do Gerenciador de Pacotes NuGet em **Ferramentas -> Gerenciador de Pacotes NuGet-> Console do Gerenciador de Pacotes** e digite o seguinte:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Adicionar o conteúdo de exemplo

Agora, vamos adicionar um conteúdo de exemplo a este aplicativo Web. Abra _Views\Home\Index.cshtml_ e substitua o código gerado automaticamente por este exemplo:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Na barra de menus, selecione **Depurar > Iniciar Depuração** ou pressione **F5** para iniciar o aplicativo.

Em seu navegador, você deverá ver:

![Aplicativo de exemplo](./media/quickstart-result.png)

Ao clicar no botão "Leitura Avançada", você verá a Leitura Avançada iniciada com o conteúdo na página.

![Leitura Avançada](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Próximas etapas

* Confira o [tutorial](./tutorial.md) para ver o que mais você pode fazer com o SDK da Leitura Avançada
* Explore o [SDK da Leitura Avançada](https://github.com/microsoft/immersive-reader-sdk) e a [Referência de SDK da Leitura Avançada](./reference.md)
