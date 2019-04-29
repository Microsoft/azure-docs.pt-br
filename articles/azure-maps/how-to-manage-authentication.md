---
title: Gerenciar autenticação no Azure Mapas | Microsoft Docs
description: Você pode usar o portal do Azure para gerenciar a autenticação no Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693936"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta de mapas do Azure, uma ID de cliente e as chaves são criadas para dar suporte a Azure Active Directory (Azure AD) ou autenticação de chave compartilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Você pode exibir os detalhes de autenticação no portal do Azure. Vá para sua conta e selecione **autenticação** sobre o **configurações** menu.

![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

 Para obter mais informações, consulte [autenticação com o Azure mapas](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configurar o registro de aplicativo do Azure AD

Depois de criar uma conta de mapas do Azure, você precisa estabelecer um vínculo entre um locatário do Azure AD e o recurso de mapas do Azure.

1. Vá para a folha Azure AD e crie um registro de aplicativo. Forneça um nome para o registro. No **URL de logon** caixa, forneça a home page do aplicativo web / API (por exemplo, https:\//localhost/). Se você já tiver um aplicativo registrado, vá para a etapa 2.

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-registration.png)

    ![Detalhes do registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

2. Para atribuir permissões de API para mapas do Azure, vá para o aplicativo sob **registros de aplicativo**e, em seguida, selecione **configurações**.  Selecione **permissões necessárias**e, em seguida, selecione **Add**. Pesquise e selecione **mapas do Azure** sob **selecionar uma API**e, em seguida, selecione o **selecione** botão.

    ![Permissões de aplicativo de API](./media/how-to-manage-authentication/app-permissions.png)

3. Sob **selecionar permissões**, selecione **acesso do Azure mapas**e, em seguida, selecione o **selecione** botão.

    ![Selecione as permissões do aplicativo de API](./media/how-to-manage-authentication/select-app-permissions.png)

4. Conclua a etapa um ou b, dependendo de seu método de autenticação.

    1. Se seu aplicativo usa a autenticação de token de usuário com o SDK da Web de mapas do Azure, habilitar `oauthEnableImplicitFlow` definindo-o como verdadeiro na seção de manifesto da página de detalhes do registro do aplicativo.
    
       ![Manifesto de aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    2. Se seu aplicativo usa a autenticação do servidor/aplicativo, vá para o **chaves** folha no registro do aplicativo e criar uma senha ou carregar um certificado de chave pública para o registro do aplicativo. Se você criar uma senha, depois de selecionar **salvar**, copie a senha para uso posterior e armazená-lo com segurança. Você utilizará esta senha para adquirir tokens do AD do Azure.

       ![Chaves do aplicativo](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Conceder RBAC ao Azure Mapas

Depois de associar uma conta de mapas do Azure com seu locatário do Azure AD, você pode conceder controle de acesso, atribuindo um usuário ou aplicativo para uma ou mais funções de controle de acesso de mapas do Azure.

1. Vá para **controle de acesso (IAM)**, selecione **as atribuições de função**e, em seguida, selecione **Adicionar atribuição de função**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. No **Adicionar atribuição de função** janela, em **função**, selecione **Azure mapas data Reader (versão prévia)**. Em **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**. Sob **selecionar**, selecione o usuário ou aplicativo. Clique em **Salvar**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções disponíveis de RBAC do Azure Mapas

Para exibir as funções RBAC (controle) de acesso baseado em função que estão disponíveis para mapas do Azure, vá para **controle de acesso (IAM)**, selecione **funções**, e, em seguida, pesquise funções começando com **mapas do Azure**. Essas são as funções que você pode conceder acesso a.

![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Exibir mapas do Azure RBAC

O RBAC fornece controle de acesso granular.

Para exibir usuários e aplicativos que receberam permissão RBAC para mapas do Azure, vá para **controle de acesso (IAM)**, selecione **as atribuições de função**e, em seguida, filtrar por **mapas do Azure**.

![Exibir usuários e aplicativos concedida RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Depois de registrar seu aplicativo e associá-lo a mapas do Azure, você pode solicitar tokens de acesso.

* Se seu aplicativo usa a autenticação de token de usuário com o SDK da Web de mapas do Azure, você precisará configurar sua página HTML com a ID de cliente de mapas do Azure e a ID do aplicativo do Azure AD.

* Se seu aplicativo usa a autenticação do servidor/aplicativo, você precisará solicitar um token do ponto de extremidade de logon do Azure AD `https://login.microsoftonline.com` com a ID de recurso do Azure AD `https://atlas.microsoft.com/`, a ID do cliente de mapas do Azure, a ID do aplicativo do Azure AD e o registro do aplicativo do Azure AD a senha ou certificado.

Para obter mais informações sobre como solicitar tokens de acesso do AD do Azure para usuários e entidades de serviço, consulte [cenários de autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a autenticação do Azure AD e o SDK da Web do Azure Mapas, consulte [Azure AD e o SDK da Web do Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).