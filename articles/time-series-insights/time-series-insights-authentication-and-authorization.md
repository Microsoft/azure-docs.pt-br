---
title: Como autenticar e autorizar pela API no Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como configurar a autenticação e autorização para um aplicativo personalizado que chama a API do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: 66a3c40bf1e1e1dc6253520a555e19ebf011297c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761632"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autenticação e autorização para API do Azure Time Series Insights

Este artigo explica como configurar a autenticação e autorização usados em um aplicativo personalizado que chama a API do Azure Time Series Insights.

## <a name="service-principal"></a>Entidade de serviço

Esta seção explica como configurar um aplicativo para acessar a API de análises de séries temporais em nome do aplicativo. O aplicativo pode, em seguida, consultar dados ou publicar dados de referência no ambiente de Time Series Insights com as credenciais do aplicativo e não com as credenciais do usuário.

Quando você tiver um aplicativo que precisa acessar o Time Series Insights, você deve configurar um aplicativo do Azure Active Directory e atribuir políticas de acesso a dados no ambiente Time Series Insights. Esta abordagem é preferencial para executar o aplicativo com suas próprias credenciais porque:

* Você pode atribuir permissões para a identidade do aplicativo que são diferentes das suas próprias permissões. Normalmente, essas permissões são restritas a apenas o que o aplicativo exige. Por exemplo, você pode permitir que o aplicativo apenas leia dados em um ambiente de análises de séries temporais específico.
* Você não precisa alterar as credenciais do aplicativo se alterar suas responsabilidades.
* Você pode usar um certificado ou uma chave de aplicativo para automatizar a autenticação ao executar um script autônomo.

Este artigo mostra como executar essas etapas no portal do Azure. Ele se concentra em um aplicativo de locatário único que se destina a ser executado em uma única organização. Você normalmente usa os aplicativos com um único locatário para os aplicativos da linha de negócios executados em sua organização.

O fluxo de instalação consiste em três etapas de alto nível:

1. Criar um aplicativo no Azure Active Directory.
2. Autorizar esse aplicativo para acessar o ambiente de análises de séries temporais.
3. Use a ID do aplicativo e a chave para adquirir um token para o `"https://api.timeseries.azure.com/"` público ou recurso. O token, em seguida, pode ser usado para chamar a API de análises de séries temporais.

Aqui estão as etapas detalhadas:

1. No portal do Azure, selecione **Azure Active Directory** > **Registros do aplicativo** > **Novo registro de aplicativo**.

   ![Novo registro do aplicativo no Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Nomeie o aplicativo, selecione o tipo a ser assinar**aplicativo Web / API**, selecione qualquer URI válido para **URL de logon**e clique em **Criar**.

   ![Criar o aplicativo no Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Selecione seu aplicativo recém-criado e copie seu ID de aplicativo para o seu editor de texto favorito.

   ![Copie a ID do aplicativo](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Selecione **Chaves**, insira o nome da chave, selecione a expiração e clique em **Salvar**.

   ![Selecione as chaves do aplicativo](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Insira o nome da chave e a expiração e clique em Salvar](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Copie a chave para o seu editor de texto favorito.

   ![Copie a chave do aplicativo](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Para o ambiente de análises de séries temporais, selecione **Políticas de acesso de dados** e clique em **Adicionar**.

   ![Adicionar nova política de acesso de dados para o ambiente de análises de séries temporais](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. Na caixa de diálogo **Selecionar usuário**, cole o nome do aplicativo (da etapa 2) ou a ID do aplicativo (da etapa 3).

   ![Localizar um aplicativo na caixa de diálogo Selecionar usuário](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Selecione a função (**Leitor** para consultar dados, **Colaborador** para consultar dados e alterar dados de referência) e clique em **Ok**.

   ![Escolha o Leitor ou Colaborador na caixa de diálogo Selecionar função](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Salve a política clicando em **Ok**.

10. Use a ID do aplicativo (da etapa 3) e a chave de aplicativo (da etapa 5) para adquirir o token em nome do aplicativo. O token, em seguida, pode ser passado ao `Authorization` cabeçalho quando o aplicativo chama a API de análises de séries temporais.

    Se você estiver usando C#, você pode usar o código a seguir para adquirir o token em nome do aplicativo. Para obter um exemplo completo, consulte [Consultar dados usando o C#](time-series-insights-query-data-csharp.md).

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
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Use a ID do aplicativo e a chave em seu aplicativo para autenticar como Azure Time Series Insight. 

## <a name="next-steps"></a>Próximos passos

- Para código de exemplo que chama a API de análises de séries temporais, consulte [Consultar dados usando o C#](time-series-insights-query-data-csharp.md).

- Para obter informações de referência da API, confira [Referência da API de Consulta](/rest/api/time-series-insights/ga-query-api).

- Saiba como [criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md).