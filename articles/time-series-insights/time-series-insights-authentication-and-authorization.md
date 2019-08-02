---
title: Autenticar e autorizar usando uma API no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543969"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este documento descreve como registrar um aplicativo no Azure Active Directory usando a nova folha Azure Active Directory. Aplicativos registrados no Azure Active Directory permitem que os usuários autenticar e ser autorizado a usar a API do Azure Time Series Insight associado com um ambiente do Time Series Insights.

## <a name="service-principal"></a>Entidade de serviço

As seções a seguir descrevem como configurar um aplicativo para acessar a API do Time Series Insights em nome de um aplicativo. O aplicativo pode, em seguida, consultar ou publicar dados de referência no ambiente do Time Series Insights usando suas próprias credenciais de aplicativo do Azure Active Directory.

## <a name="summary-and-best-practices"></a>Resumidas e práticas recomendadas

O fluxo de registro de aplicativo do Azure Active Directory envolve três etapas principais.

1. [Registrar um aplicativo](#azure-active-directory-app-registration) no Azure Active Directory.
1. Autorizar o aplicativo tenha [acesso a dados para o ambiente do Time Series Insights](#granting-data-access).
1. Use o **ID do aplicativo** e **segredo do cliente** para adquirir um token do `https://api.timeseries.azure.com/` no seu [aplicativo de cliente](#client-app-initialization). O token, em seguida, pode ser usado para chamar a API de análises de séries temporais.

Por **etapa 3**, separar o seu aplicativo e suas credenciais de usuário permite que você:

* Atribua permissões a identidade do aplicativo que são diferentes das suas próprias permissões. Normalmente, essas permissões são restritas a apenas o que o aplicativo exige. Por exemplo, você pode permitir que o aplicativo leia dados somente de um determinado ambiente do Time Series Insights.
* Isolar a segurança do aplicativo das credenciais de autenticação do usuário criando usando um **segredo do cliente** ou certificado de segurança. Como resultado, as credenciais do aplicativo não são dependentes de credenciais de um usuário específico. Se a função do usuário for alterada, o aplicativo não requer necessariamente novas credenciais ou configuração adicional. Se o usuário altera sua senha, todo o acesso ao aplicativo não requer novas credenciais ou chaves.
* Execute um script autônomo usando uma **segredo do cliente** ou segurança de certificado em vez de credenciais de um usuário específico (exigir que eles estejam presentes).
* Use um certificado de segurança em vez de uma senha para proteger o acesso à sua API do Azure Time Series Insights.

> [!IMPORTANT]
> Siga o princípio de **separação de preocupações** (descrito para esse cenário acima) ao configurar a política de segurança do Azure Time Series Insights.

> [!NOTE]
> * O artigo se concentra em um aplicativo de único locatário em que o aplicativo destina-se para ser executado em uma única organização.
> * Normalmente, você usará aplicativos de locatário único para aplicativos de linha de negócios que são executados em sua organização.

## <a name="detailed-setup"></a>Detalhadas de instalação

### <a name="azure-active-directory-app-registration"></a>Registro de aplicativo do Active Directory do Azure

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Conceder acesso a dados

1. Para o ambiente do Time Series Insights, selecione **políticas de acesso de dados** e selecione **Add**.

   [![Adicionar nova política de acesso de dados para o ambiente do Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. No **Selecionar usuário** diálogo caixa, cole-o a **nome do aplicativo** ou o **ID do aplicativo** da seção de registro de aplicativo do Azure Active Directory.

   [![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecione a função. Selecione **Reader** para consultar dados ou **Colaborador** para consultar dados e alterar dados de referência. Selecione **OK**.

   [![Escolher a leitor ou colaborador na caixa de diálogo Selecionar função de usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Salve a política selecionando **Okey**.

   > [!TIP]
   > Leia sobre [concedendo acesso a dados](./time-series-insights-data-access.md) ao ambiente do Time Series Insights no Azure Active Directory.

### <a name="client-app-initialization"></a>Inicialização do aplicativo cliente

1. Use o **ID do aplicativo** e **segredo do cliente** (chave de aplicativo) da seção de registro de aplicativo do Azure Active Directory para adquirir o token em nome do aplicativo.

    No C#, o código a seguir pode adquirir o token em nome do aplicativo. Para obter um exemplo completo, consulte [Consultar dados usando o C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. O token, em seguida, pode ser passado ao `Authorization` cabeçalho quando o aplicativo chama a API de análises de séries temporais.

## <a name="next-steps"></a>Próximas etapas

- Para exemplo de código que chama a API do GA Time Series Insights, consulte [consulta de dados usando C# ](./time-series-insights-query-data-csharp.md).

- Para exemplos de código da API do Time Series Insights da versão prévia, consulte [visualização de consulta de dados usando C# ](./time-series-insights-update-query-data-csharp.md).

- Para obter informações de referência da API, confira [Referência da API de Consulta](/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).