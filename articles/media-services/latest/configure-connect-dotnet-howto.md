---
title: Conectar-se à API de v3 de serviços de mídia do Azure - .NET
description: Saiba como se conectar a serviços de mídia v3 API com o .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: a256eb787d7e3dbd800ec2e630cac591b07ca0fc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444179"
---
# <a name="connect-to-media-services-v3-api---net"></a>Conectar-se à API de v3 de serviços de mídia - .NET

Este artigo mostra como se conectar ao SDK do .NET dos serviços de mídia do Azure v3 usando o método de logon de entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Certifique-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia
- Instale uma ferramenta que você deseja usar para desenvolvimento no .NET. As etapas neste artigo mostram como usar [Community Edition do Visual Studio 2019](https://www.visualstudio.com/downloads/). Você pode usar o Visual Studio Code, consulte [trabalhando com C# ](https://code.visualstudio.com/docs/languages/csharp). Ou, você pode usar um editor de código diferentes.

## <a name="create-a-console-application"></a>Criar um aplicativo de console

1. Inicie o Visual Studio. 
1. Dos **arquivo** menu, clique em **New** > **projeto**. 
1. Criar uma **.NET Core** aplicativo de console.

O aplicativo de exemplo neste tópico, tem como alvo `netcoreapp2.0`. O código usa 'async main', que está disponível a partir do C# 7.1. Consulte este [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) para obter mais detalhes.

## <a name="add-required-nuget-packages"></a>Adicionar pacotes NuGet necessários

1. No Visual Studio, selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **Console do Gerenciador de NuGet**.
2. No **Package Manager Console** janela, use `Install-Package` comando para adicionar os seguintes pacotes NuGet. Por exemplo: `Install-Package Microsoft.Azure.Management.Media`.

|Pacote|DESCRIÇÃO|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK de serviços de mídia do Azure. <br/>Para verificar se você estiver usando o pacote de serviços de mídia do Azure mais recente, verifique [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteca de autenticação de ADAL do SDK do Azure para NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Ler valores de configuração de variáveis de ambiente e os arquivos JSON locais|
|`Microsoft.Extensions.Configuration.Json`|Ler valores de configuração de variáveis de ambiente e os arquivos JSON locais
|`WindowsAzure.Storage`|SDK de armazenamento|

## <a name="create-and-configure-the-app-settings-file"></a>Criar e configurar o arquivo de configurações do aplicativo

### <a name="create-appsettingsjson"></a>Criar appSettings. JSON

1. Go vá **gerais** > **arquivo de texto**.
1. O nome "appSettings. JSON".
1. Defina a propriedade "Copiar para diretório de saída" do arquivo. JSON para "Copiar se mais recente" (para que o aplicativo seja capaz de acessá-lo quando publicado).

### <a name="set-values-in-appsettingsjson"></a>Valores definidos no appSettings. JSON

Execute o `az ams account sp create` comando conforme descrito em [acessar APIs](access-api-cli-how-to.md). O comando retorna o json que você deve copiar em seu "appSettings. JSON".
 
## <a name="add-configuration-file"></a>Adicionar arquivo de configuração

Para sua conveniência, adicione um arquivo de configuração que é responsável por ler os valores de "appSettings. JSON".

1. Adicione uma nova classe. cs ao seu projeto. Nomeie-o `ConfigWrapper`. 
1. Cole o código a seguir neste arquivo (Este exemplo pressupõe que você tenha o namespace é `ConsoleApp1`).

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

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Conectar-se para o cliente .NET

Para começar a usar a APIs de Serviços de Mídia do Azure com o .NET, é necessário criar um objeto **AzureMediaServicesClient**. Para criar o objeto, você precisa fornecer as credenciais necessárias para o cliente se conectar ao Azure usando o Microsoft Azure Active Directory. No código a seguir, a função GetCredentialsAsync cria o objeto de ServiceClientCredentials com base nas credenciais fornecidas no arquivo de configuração local.

1. Abra `Program.cs`.
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

- [Tutorial: Carregar, codificar e transmitir vídeos – .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Transmissão ao vivo com os Serviços de Mídia v3 – .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os Serviços de Mídia v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada de trabalho com base em um arquivo local – .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada de trabalho com base em uma URL HTTPS – .NET](job-input-from-http-how-to.md)
- [Codificação com uma transformação personalizada – .NET](customize-encoder-presets-how-to.md)
- [Usar a criptografia dinâmica AES-128 e o serviço de entrega de chaves – .NET](protect-with-aes128.md)
- [Usar a criptografia dinâmica DRM e o serviço de entrega de licenças – .NET](protect-with-drm.md)
- [Obter uma chave de assinatura da política existente – .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com os Serviços de Mídia – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplos de vídeos sob demanda avançados do Azure Functions v2 com Serviços de Mídia v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Consulte também

[Referência do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
