---
title: Pré-requisitos de API | Microsoft Docs
description: Pré-requisitos para usar as APIs do Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0d743d89c344ce9cb6eec116ed9c3e0cbe56f86e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720841"
---
<a name="api-prerequisites"></a>Pré-requisitos de API
================

Há dois ativos programático necessários para usar as APIs do Portal do Cloud Partner: uma entidade de serviço e um token de acesso do Azure AD (Azure Active Directory).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Criar uma entidade de serviço em seu locatário do Azure Active Directory
----------------------------------------------------------------

Primeiro, você precisa criar uma entidade de serviço no locatário do Azure AD. Este locatário será atribuído a seu próprio conjunto de permissões no Portal do Cloud Partner. O código chamará APIs usando esse locatário em vez de usar suas credenciais pessoais.  Para obter uma explicação completa sobre a criação de uma entidade de serviço, confira [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que podem acessar recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Adicionar a entidade de serviço à sua conta
-----------------------------------------

Agora que você criou a entidade de serviço em seu locatário, você poderá adicioná-la como um usuário à sua conta do Portal do Cloud Partner. Assim como um usuário, a entidade de serviço pode ser um proprietário ou um colaborador no portal.

Siga as etapas a seguir para adicionar a entidade de serviço:

1. Faça logon no Portal do Cloud Partner. 
2. Clique em **Usuários** na barra de menus à esquerda e escolha **Adicionar Usuário**.

   ![Adicionar um usuário ao portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Na lista suspensa **Tipo**, selecione **Entidade de Serviço** e adicione os seguintes detalhes:

-   Um **Nome Amigável** para a entidade de serviço, por exemplo `spAccount`.
-   A **ID do Aplicativo**. Para localizar esse identificador, acesse o [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, escolha **Registros de aplicativo** e clique em seu aplicativo.
-   A **ID do locatário**, também conhecida como a **ID do diretório**, do seu locatário do Azure AD. Você pode encontrar esse identificador na página do Azure Active Directory no [portal do Azure](https://portal.azure.com), em **Propriedades**.
-   A **ID do Objeto** do objeto de entidade de serviço. Você pode obter esse identificador no portal do Azure. Acesse **Azure Active Directory**, escolha **Registros de aplicativo**, clique em seu aplicativo e clique no nome do aplicativo em **Aplicativo gerenciado no diretório local**. Em seguida, acesse a página **Propriedades**, para localizar a ID do objeto. Não pegue a ID do objeto inicial que está no aplicativo, mas sim a ID do objeto que está no aplicativo gerenciado.
-   A **função** associada à conta, que será usada para o RBAC.

     ![Adicionar um aplicativo gerenciado ao portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Clique em **Adicionar** para adicionar a entidade de serviço à sua conta.

   ![Adicionar uma entidade de serviço](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD
----------------------------

As APIs do Portal do Microsoft Cloud Partner usam os seguintes protocolos e ativos durante a autenticação:

- Um token de portador JWT (Token Web JSON) para solicitar acesso a recursos
- O protocolo [OIDC](https://openid.net/connect/) (OpenID Connect) para verificar a identidade
- O [Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) como a autoridade de identidade

Há duas abordagens de princípio para adquirir um token JWT de forma programática:

- Usar a biblioteca de autenticação da Microsoft para .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Essa abordagem de nível superior é recomendada para os desenvolvedores do .NET. 
- Fazer uma solicitação **HTTP POST** para o ponto de extremidade do **token** OAuth do Azure AD, que usa o formato:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Agora, você pode passar esse token como parte do cabeçalho de autorização para solicitações de API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas as APIs nesta referência, o cabeçalho de autorização é sempre considerado como passado, portanto, ele não foi mencionado explicitamente.

Se você encontrar erros de autenticação em sua solicitação, confira [Solução de erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
