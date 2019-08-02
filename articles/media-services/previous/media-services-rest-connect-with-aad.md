---
title: Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com REST | Microsoft Docs
description: Saiba como acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure Active Directory usando REST.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: 66c69552157df957e572a3af092131a3b7e560d5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871702"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia com REST

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Ao usar a autenticação do Azure AD com os Serviços de Mídia do Azure, você pode fazer a autenticação usando uma destas duas maneiras:

- A **autenticação de usuário** autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia do Azure. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 
- A **autenticação de entidade de serviço** autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados, como aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou microsserviços.

    Este tutorial mostra como usar a autenticação de **entidade de serviço** do Azure AD para acessar a API AMS com REST. 

    > [!NOTE]
    > A **entidade de serviço** é a prática recomendada para a maioria dos aplicativos que se conectam aos Serviços de Mídia do Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Obter informações de autenticação do portal do Azure
> * Obter o token de acesso usando o Postman
> * Testar a API de **Ativos** usando o token de acesso


> [!IMPORTANT]
> Atualmente, os Serviços de Mídia dão suporte ao modelo de autenticação de serviços do Controle de Acesso do Azure. No entanto, a autenticação de Controle de Acesso será preterida em 1º de junho de 2018. Recomendamos que você migre para o modelo de autenticação do Azure AD assim que possível.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Crie uma conta dos Serviços de Mídia do Azure usando o Portal do Azure](media-services-portal-create-account.md).
- Consulte o artigo [Acessando a API dos Serviços de Mídia do Azure com visão geral da autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).
- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as APIs REST mostradas neste artigo. 

    Neste tutorial, estamos usando o **Postman**, mas qualquer ferramenta REST seria adequada. As outras alternativas são: **Visual Studio Code** com o plug-in do REST ou **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Obter informações de autenticação do portal do Azure

### <a name="overview"></a>Visão geral

Para acessar a API dos Serviços de Mídia, você precisa coletar os seguintes pontos de dados.

|Configuração|Exemplo|DESCRIÇÃO|
|---|-------|-----|
|Domínio do locatário do Azure Active Directory|microsoft.onmicrosoft.com|O Azure AD enquanto ponto de extremidade do STS (Serviço de Token de Segurança) é criado usando o seguinte formato: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token>. O Azure AD emite um JWT para acessar recursos (um token de acesso).|
|Ponto de extremidade da API REST|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Este é o ponto de extremidade pelo qual todas as chamadas de API REST dos Serviços de Mídia em seu aplicativo são feitas.|
|ID do cliente (ID do aplicativo)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|ID do aplicativo (cliente) do Azure AD. A ID do cliente é necessária para obter o token de acesso. |
|Segredo do cliente|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Chaves de aplicativo do Azure AD (segredo do cliente). O segredo do cliente é necessário para obter o token de acesso.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Obter informações de autenticação do AAD no portal do Azure

Para obter as informações, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até sua instância do AMS.
3. Selecione **Acesso à API**.
4. Clique em **Conecte-se à API dos Serviços de Mídia do Azure com a entidade de serviço**.

    ![Acesso à API](./media/connect-with-rest/connect-with-rest01.png)

5. Selecione um **aplicativo do Azure AD** existente ou crie um novo (mostrado abaixo).

    > [!NOTE]
    > Para que a solicitação REST da Mídia do Azure seja bem-sucedida, o usuário chamador deve ter uma função de **Colaborador** ou **Proprietário** na conta dos Serviços de Mídia que ele está tentando acessar. Se você receber uma exceção informando que “O servidor remoto retornou um erro: (401) Não autorizado”, confira [Controle de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Se precisar criar um novo aplicativo do AD, siga estas etapas:
    
   1. Pressione **Criar Novo**.
   2. Insira um nome.
   3. Pressione **Criar Novo** novamente.
   4. Pressione **Salvar**.

      ![Acesso à API](./media/connect-with-rest/new-app.png)

      O novo aplicativo aparece na página.

6. Obtenha a **ID do Cliente** (ID do aplicativo).
    
   1. Selecione o aplicativo.
   2. Obtenha a **ID do Cliente** na janela à direita. 

      ![Acesso à API](./media/connect-with-rest/existing-client-id.png)

7. Obter a **Chave** do aplicativo (segredo do cliente). 

   1. Clique no botão **Gerenciar aplicativo** (observe que as informações de ID do cliente estão em **ID do Aplicativo**). 
   2. Pressione **Chaves**.
    
       ![Acesso à API](./media/connect-with-rest/manage-app.png)
   3. Gere a chave do aplicativo (segredo do cliente) preenchendo **DESCRIÇÃO** e **VENCIMENTO** e pressionando **Salvar**.
    
       Quando o botão **Salvar** é pressionado, o valor da chave é exibido. Copie o valor de chave antes de deixar a folha.

   ![Acesso à API](./media/connect-with-rest/connect-with-rest03.png)

Você pode adicionar valores para parâmetros de conexão do AD ao seu arquivo app.config ou web.config, para uso posterior em seu código.

> [!IMPORTANT]
> A **Chave do cliente** é um segredo importante e deve ser protegido adequadamente em um cofre de chaves ou criptografado na produção.

## <a name="get-the-access-token-using-postman"></a>Obter o token de acesso usando o Postman

Esta seção mostra como usar o **Postman** para executar uma API REST que retorna um Token de Portador JWT (token de acesso). Para chamar qualquer API REST dos Serviços de Mídia, você precisa adicionar o cabeçalho "Authorization" às e adicionar o valor de "Bearer *seu_token_de_acesso*" a cada chamada (conforme mostrado na próxima seção deste tutorial). 

1. Abra o **Postman**.
2. Selecione **POST**.
3. Digite a URL que inclui o nome do locatário usando o seguinte formato: o nome do locatário deve terminar com **.onmicrosoft.com** e a URL deve terminar com **oauth2/token**: 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. Selecione a guia **Cabeçalhos**.
5. Insira as informações de **Cabeçalhos** usando a grade de dados de "chave/valor". 

    ![Grade de dados](./media/connect-with-rest/headers-data-grid.png)

    Como alternativa, clique no link **Editar em Massa** à direita da janela do Postman e cole o código a seguir.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. Pressione a guia **Corpo**.
7. Insira as informações do corpo usando a grade de dados de "chave/valor" (substitua os valores de ID do cliente e segredo). 

    ![Grade de dados](./media/connect-with-rest/data-grid.png)

    Como alternativa, clique em **Editar em Massa** à direita da janela do Postman e cole o corpo a seguir (substitua os valores de ID do cliente e segredo):

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. Pressione **Enviar**.

    ![obter token](./media/connect-with-rest/connect-with-rest04.png)

A resposta retornada contém o **token de acesso** que você precisa usar para acessar as APIs do AMS.

## <a name="test-the-assets-api-using-the-access-token"></a>Testar a API de **Ativos** usando o token de acesso

Esta seção mostra como acessar a API de **Ativos** usando o **Postman**.

1. Abra o **Postman**.
2. Selecione **GET**.
3. Cole o ponto de extremidade da API REST (por exemplo, https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecione a guia **Autorização**. 
5. Selecione **Token de Portador**.
6. Cole o token que foi criado na seção anterior.

    ![obter token](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > A experiência do usuário do Postman pode ser diferente entre um Mac e um PC. Se a versão do Mac não tiver a opção "Token de Portador" no menu suspenso da seção de **Autenticação**, você deverá adicionar o cabeçalho de **Autorização** manualmente no cliente Mac.

   ![Cabeçalho de autenticação](./media/connect-with-rest/auth-header.png)

7. Selecione **Cabeçalhos**.
5. Clique no link **Editar em Massa** à direita da janela do Postman.
6. Cole os seguintes cabeçalhos:

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. Pressione **Enviar**.

A resposta retornada contém os ativos que estão em sua conta.

## <a name="next-steps"></a>Próximas etapas

* Teste este código de exemplo na [Autenticação do Azure AD para Acesso aos Serviços de Mídia do Azure: ambos via API REST](https://github.com/willzhan/WAMSRESTSoln)
* [Carregar arquivos com .NET](media-services-dotnet-upload-files.md)
