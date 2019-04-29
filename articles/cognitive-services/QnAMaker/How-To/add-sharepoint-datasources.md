---
title: Arquivos do SharePoint – QnA Maker
titleSuffix: Azure Cognitive Services
description: Adicione fontes de dados protegidos do Sharepoint à sua base de dados de conhecimento para enriquecer a base de dados de conhecimento com perguntas e respostas que podem ser protegidas com o Active Directory.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: 671d76b6c0a5a2cdac5797668fb0e5651b5823e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379267"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Adicionar uma fonte de dados protegida do Sharepoint à sua base de dados de Conhecimento

Adicione fontes de dados protegidos do Sharepoint à sua base de dados de conhecimento para enriquecer a base de dados de conhecimento com perguntas e respostas que podem ser protegidas com o Active Directory. 

Quando você adiciona um documento Sharepoint protegido à sua base de dados de Conhecimento, como o Gerenciador do QnA Maker, você deve solicitar permissão do Active Directory para o QnA Maker. Depois que essa permissão é fornecida do Gerenciador do Active Directory para o QnA Maker para acesso ao Sharepoint, ele não precisa ser fornecido novamente. Cada adição de documento subsequente para a base de conhecimento não precisarão autorização se ele estiver no mesmo recurso do Sharepoint. 

Se o Gerenciador de dados de Conhecimento de QnA Maker não é o Gerenciador do Active Directory, você precisará para se comunicar com o Gerenciador do Active Directory para concluir esse processo.

## <a name="add-supported-file-types-to-knowledge-base"></a>Adicionar tipos de arquivo com suporte à base de Conhecimento

Você pode adicionar tudo com suporte do QnA Maker [tipos de arquivo](../Concepts/data-sources-supported.md) de um servidor do Sharepoint à sua base de dados de Conhecimento. Talvez você precise conceder [permissões](#permissions) se o recurso de arquivo estiver protegido.

1. Do servidor do Sharepoint, selecione o menu de reticências do arquivo, `...`.
1. Copie a URL do arquivo.

    ![Obter a URL do arquivo do Sharepoint selecionando o menu de reticências do arquivo, em seguida, copiando a URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. No portal do QnA Maker, sobre o **configurações** página, [adicionar a URL](edit-knowledge-base.md#add-datasource) à base de Conhecimento. 

## <a name="permissions"></a>Permissões

Concedendo permissões acontece quando um arquivo protegido de um servidor do Sharepoint é adicionado a uma base de dados de Conhecimento. Dependendo da configuração do Sharepoint up e as permissões da pessoa que está adicionando o arquivo, isso poderia exigir:

* Não há etapas adicionais – a pessoa adicionando o arquivo tem todas as permissões necessárias.
* as etapas pelas duas [Gerenciador de dados de Conhecimento](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) e [manager do Active Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Consulte as etapas listadas abaixo. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gerenciador de dados de Conhecimento: adicionar fonte de dados do Sharepoint no portal QnA Maker

Quando o **manager QnA Maker** adiciona um documento Sharepoint protegido a uma base de dados de Conhecimento, a base de Conhecimento manager inicia uma solicitação de permissão que o Gerenciador do Active Directory precisa para ser concluída.

A solicitação começa com um pop-up para autenticar uma conta do Active Directory. 

![Autenticar a conta de usuário](../media/add-sharepoint-datasources/authenticate-user-account.png)

Depois que o Gerenciador do QnA Maker seleciona a conta, o administrador do Active Directory receberá um aviso de que ele precisa permitir que o QnA Maker acesso ao aplicativo (não o Gerenciador do QnA Maker) para o recurso do Sharepoint. O Gerenciador do Active Directory será necessário fazer isso para todos os recursos do Sharepoint, mas não todos os documentos do recurso. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>O Gerenciador do Active Directory: conceder acesso de leitura de arquivo para o QnA Maker

O Gerenciador do Active Directory (não o Gerenciador do QnA Maker) precisa conceder acesso ao QnA Maker para acessar o recurso do Sharepoint, selecionando [esse link](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) para autorizar o aplicativo do enterprise do QnA Maker Portal Sharepoint ter de leitura de arquivo permissões. 

![O Gerenciador do Active Directory do Azure concede a permissão interativamente](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Conceder acesso de centro de administração do Active Directory do Azure

1. O Gerenciador do Active Directory entra no portal do Azure e abre  **[aplicativos empresariais](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**. 

1. Pesquise `QnAMakerPortalSharepoint` selecionar o aplicativo do QnA Maker. 

    [![Pesquisar QnAMakerPortalSharepoint na lista de aplicativos empresariais](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Sob **segurança**, acesse **permissões**. Selecione **conceder consentimento do administrador para a organização**. 

    [![Selecione o usuário autenticado para o administrador do Active Directory](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecione uma conta de logon com permissões para conceder permissões para o Active Directory. 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) or [updating a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Colaborar em sua base de dados de Conhecimento](collaborate-knowledge-base.md)