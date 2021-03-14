---
title: Conectar-se à API dos serviços de mídia do Azure v3-.NET
description: Este artigo demonstra como se conectar à API dos serviços de mídia v3 com .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 8946f6e94dd26db45622bc7609fb2375d59bb57e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455375"
---
# <a name="connect-to-media-services-v3-api---net"></a>Conectar-se à API dos serviços de mídia v3-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como se conectar ao SDK do .NET dos serviços de mídia do Azure v3 usando o método de logon da entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](./create-account-howto.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia
- Instale uma ferramenta que você gostaria de usar para o desenvolvimento do .NET. As etapas neste artigo mostram como usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Você pode usar Visual Studio Code, consulte [trabalhando com C#](https://code.visualstudio.com/docs/languages/csharp). Ou, você pode usar um editor de código diferente.

> [!IMPORTANT]
> Examine [Convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Criar um aplicativo de console

1. Inicie o Visual Studio. 
1. No menu **arquivo** , clique em **novo**  >  **projeto**. 
1. Crie um aplicativo de console do **.NET Core** .

O aplicativo de exemplo neste tópico, targets `netcoreapp2.0` . O código usa ' async Main ', que está disponível a partir do C# 7,1. Consulte este [blog](/archive/blogs/benwilli/async-main-is-available-but-hidden) para obter mais detalhes.

## <a name="add-required-nuget-packagesassemblies"></a>Adicionar pacotes/assemblies NuGet necessários

1. No Visual Studio, selecione **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador do NuGet**.
2. Na janela do **console do Gerenciador de pacotes** , use `Install-Package` o comando para adicionar os seguintes pacotes NuGet. Por exemplo, `Install-Package Microsoft.Azure.Management.Media`.

|Pacote|Descrição|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK dos serviços de mídia do Azure. <br/>Para verificar se você está usando o pacote mais recente dos serviços de mídia do Azure, verifique [Microsoft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|

### <a name="other-required-assemblies"></a>Outros assemblies necessários

- Azure. Storage. BLOBs
- Microsoft.Extensions.Configuração
- Microsoft.Extensions.Configuração. EnvironmentVariables
- Microsoft.Extensions.Configuration.Jsem
- Microsoft.Rest.ClientRuntime.Azure.Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Criar e configurar o arquivo de configurações do aplicativo

### <a name="create-appsettingsjson"></a>Criar appsettings.jsem

1. Ir para o  >  **arquivo de texto** geral.
1. Nomeie-o como "appsettings.js".
1. Defina a propriedade "copiar para o diretório de saída" do arquivo. JSON como "copiar se for mais recente" (para que o aplicativo possa acessá-lo quando publicado).

### <a name="set-values-in-appsettingsjson"></a>Definir valores em appsettings.jsem

Execute o `az ams account sp create` comando conforme descrito em [APIs de acesso](./access-api-howto.md). O comando retorna JSON que você deve copiar para "appsettings.json".
 
## <a name="add-configuration-file"></a>Adicionar arquivo de configuração

Para sua conveniência, adicione um arquivo de configuração que seja responsável por ler valores de "appsettings.jsem".

1. Adicione uma nova classe. cs ao seu projeto. Nomeie-o como `ConfigWrapper`. 
1. Cole o código a seguir neste arquivo (Este exemplo pressupõe que você tem o namespace `ConsoleApp1` ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Conectar-se ao cliente .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código a seguir, a função GetCredentialsAsync cria o objeto createclientcredentials com base nas credenciais fornecidas no arquivo de configuração local.

1. Abra o `Program.cs`.
1. Cole o seguinte código:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: carregar, codificar e transmitir vídeos-.NET](stream-files-tutorial-with-api.md) 
- [Tutorial: transmitir ao vivo com os serviços de mídia v3-.NET](stream-live-tutorial-with-api.md)
- [Tutorial: analisar vídeos com os serviços de mídia v3-.NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada de trabalho com base em um arquivo local – .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada de trabalho com base em uma URL HTTPS – .NET](job-input-from-http-how-to.md)
- [Codificação com uma transformação personalizada – .NET](customize-encoder-presets-how-to.md)
- [Usar a criptografia dinâmica AES-128 e o serviço de entrega de chaves – .NET](protect-with-aes128.md)
- [Usar a criptografia dinâmica DRM e o serviço de entrega de licenças – .NET](protect-with-drm.md)
- [Obter uma chave de assinatura da política existente – .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com os Serviços de Mídia – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplos de vídeos sob demanda avançados do Azure Functions v2 com Serviços de Mídia v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Confira também

* [Referência do .NET](/dotnet/api/overview/azure/mediaservices/management)
* Para obter mais exemplos de código, consulte o repositório de [exemplos do SDK do .net](https://github.com/Azure-Samples/media-services-v3-dotnet) .
