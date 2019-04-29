---
title: Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com o .NET | Microsoft Docs
description: Este tópico mostra como usar a autenticação do Azure AD (Azure Active Directory) para acessar a API do AMS (Serviços de Mídia do Azure) com o .NET.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 15b986d4e7567be48c582e4a39b727ab110de2be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230940"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com o .NET

A partir do windowsazure.mediaservices 4.0.0.4, os Serviços de Mídia do Azure dão suporte à autenticação baseada no Azure AD (Azure Active Directory). Este tópico mostra como usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com o Microsoft .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter detalhes, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).
- O último pacote [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices).
- Familiaridade com o tópico [Acessando API de serviços de mídia do Azure a visão geral de autenticação do Azure Active Directory](media-services-use-aad-auth-to-access-ams-api.md). 

Ao usar a autenticação do Azure AD com os Serviços de Mídia do Azure, você pode fazer a autenticação usando uma destas duas maneiras:

- A **autenticação de usuário** autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia do Azure. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 
- A **autenticação de entidade de serviço** autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados, como aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou microsserviços.

>[!IMPORTANT]
>Atualmente, os Serviços de Mídia do Azure dão suporte a um modelo de autenticação do Serviço de Controle de Acesso do Azure. No entanto, a autorização de Controle de Acesso será preterida em 22 de junho de 2018. Recomendamos que você migre para o modelo de autenticação do Azure Active Directory assim que possível.

## <a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD

Para se conectar à API dos Serviços de Mídia do Azure com a autenticação do Azure AD, o aplicativo cliente precisa solicitar um token de acesso do Azure AD. Quando você usa o SDK de cliente do .NET dos Serviços de Mídia, vários detalhes sobre como adquirir um token de acesso do Azure AD são encapsulados e simplificados para você nas classes [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) e [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs). 

Por exemplo, você não precisa fornecer a autoridade do Azure AD, o URI de recurso dos Serviços de Mídia ou os detalhes do aplicativo nativo do Azure AD. Esses são valores conhecidos que já são configurados pela classe de provedor do token de acesso do Azure AD. 

Se você não estiver usando o SDK do .NET dos Serviços de Mídia do Azure, recomendamos que você use a [Biblioteca de Autenticação do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md). Para obter valores para os parâmetros que você precisa usar com a Biblioteca de Autenticação do Azure AD, consulte [Usar o portal do Azure para acessar as configurações de autenticação do Azure AD](media-services-portal-get-started-with-aad.md).

Você também tem a opção de substituir a implementação padrão do **AzureAdTokenProvider** por sua própria implementação.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalar e configurar o SDK do .NET dos Serviços de Mídia do Azure

>[!NOTE] 
>Para usar a autenticação do Azure AD com o SDK do .NET dos Serviços de Mídia, você precisa ter a última versão do pacote [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices). Além disso, adicione uma referência ao assembly **Microsoft.IdentityModel.Clients.ActiveDirectory**. Se estiver usando um aplicativo existente, inclua o assembly **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll**. 

1. No Visual Studio, crie um novo aplicativo de console C#.
2. Use o pacote NuGet [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) para instalar o **SDK do .NET dos Serviços de Mídia do Azure**. 

    Para adicionar referências usando o NuGet, realize as seguintes etapas: no **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e, depois, selecione **Gerenciar pacotes NuGet**. Em seguida, pesquise **windowsazure.mediaservices** e selecione **Instalar**.
    
    -ou-

    Execute o comando a seguir no **Console do Gerenciador de Pacotes** do Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adicione **using** ao código-fonte.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Usar a autenticação de usuário

Para se conectar à API dos Serviços de Mídia do Azure com a opção de autenticação de usuário, o aplicativo cliente precisa solicitar um token do Azure AD usando os seguintes parâmetros:  

- Ponto de extremidade do locatário do Azure AD. As informações do locatário podem ser recuperadas no portal do Azure. Focalize o usuário conectado no canto superior direito.
- URI de recurso dos Serviços de Mídia.
- ID do cliente do aplicativo (nativo) dos Serviços de Mídia. 
- URI de redirecionamento do aplicativo (nativo) dos Serviços de Mídia. 

Os valores desses parâmetros podem ser encontrados em **AzureEnvironments.AzureCloudEnvironment**. A constante **AzureEnvironments.AzureCloudEnvironment** é um auxiliar no SDK do .NET para obtenção das configurações de variável de ambiente corretas de um Data Center público do Azure. 

Ela contém configurações de ambiente predefinidas para acessar os Serviços de Mídia apenas nos data centers públicos. Para regiões de nuvem soberana ou governamental, é possível usar **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** ou **AzureGermanCloudEnvironment**, respectivamente.

O seguinte exemplo de código cria um token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Para começar a programar nos Serviços de Mídia, você precisa criar uma instância **CloudMediaContext** que representa o contexto de servidor. O **CloudMediaContext** inclui referências para coleções importantes incluindo trabalhos, ativos, arquivos, políticas de acesso e localizadores. 

Você também precisa passar o **URI de recurso dos Serviços de Mídia REST** para o construtor **CloudMediaContext**. Para obter o URI de recurso dos Serviços REST de Mídia, entre no portal do Azure, selecione sua conta dos Serviços de Mídia do Azure, selecione **Acesso à API** e, em seguida, selecione **Conectar aos Serviços de Mídia do Azure com a autenticação de usuário**. 

O seguinte exemplo de código cria uma instância **CloudMediaContext**:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

O seguinte exemplo mostra como criar o token do Azure AD e o contexto:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Se você receber uma exceção informando que "O servidor remoto retornou um erro: (401) Não autorizado", confira a seção [Controle de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) em Visão geral sobre o acesso à API dos Serviços de Mídia do Microsoft Azure com a autenticação do Azure Active Directory.

## <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço
    
Para se conectar à API dos Serviços de Mídia do Azure com a opção de entidade de serviço, o aplicativo de camada intermediária (API Web ou aplicativo Web) precisa solicitar um token do Azure AD com os seguintes parâmetros:  

- Ponto de extremidade do locatário do Azure AD. As informações do locatário podem ser recuperadas no portal do Azure. Focalize o usuário conectado no canto superior direito.
- URI de recurso dos Serviços de Mídia.
- Valores do aplicativo do Azure AD: a **ID do Cliente** e o **Segredo do cliente**.

Os valores dos parâmetros **ID do Cliente** e **Segredo do cliente** podem ser encontrados no portal do Azure. Para obter mais informações, consulte [Introdução à autenticação do Azure AD usando o portal do Azure](media-services-portal-get-started-with-aad.md).

O seguinte exemplo de código cria um token usando o construtor **AzureAdTokenCredentials** que usa **AzureAdClientSymmetricKey** como parâmetro: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Você também pode especificar o construtor **AzureAdTokenCredentials** que usa **AzureAdClientCertificate** como parâmetro. 

Para obter instruções sobre como criar e configurar um certificado em um formato que pode ser usado pelo Azure AD, consulte [Autenticando no Azure AD em aplicativos daemon com certificados – etapas de configuração manual](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Para começar a programar nos Serviços de Mídia, você precisa criar uma instância **CloudMediaContext** que representa o contexto de servidor. Você também precisa passar o **URI de recurso dos Serviços de Mídia REST** para o construtor **CloudMediaContext**. Também obtenha o valor do **URI de recurso dos Serviços REST de Mídia** no portal do Azure.

O seguinte exemplo de código cria uma instância **CloudMediaContext**:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
O seguinte exemplo mostra como criar o token do Azure AD e o contexto:

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-dotnet-upload-files.md).
