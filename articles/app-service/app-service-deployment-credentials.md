---
title: "Credenciais de implantação do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como usar as credenciais de implantação do Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 2f8691b0aadbd97e87996c2683849a6db3b74618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar as credenciais de implantação do Serviço de Aplicativo do Azure
O [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) oferece suporte a dois tipos de credenciais para a [implantação local do Git](app-service-deploy-local-git.md) e a [implantação de FTP/S](app-service-deploy-ftp.md). Elas não são as mesmas credenciais do Azure Active Directory.

* **Credenciais de nível de usuário**: um conjunto de credenciais para toda a conta do Azure. Ele pode ser usado para implantar no Serviço de Aplicativo para qualquer aplicativo e em qualquer assinatura que a conta do Azure tem permissão para acessar. Esses são os conjuntos de credenciais padrão que você configura nos **Serviços de Aplicativos** > **&lt;nome_do_aplicativo>** > **Credenciais de implantação**. Esse também é o conjunto padrão exibido no portal da interface gráfica do usuário (como **Visão geral** e **Propriedades** da [folha de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) do seu aplicativo).

    > [!NOTE]
    > Ao delegar acesso a recursos do Azure por meio de RBAC (Controle de Acesso Baseado em Função) ou permissões de coadministrador, cada usuário do Azure que está recebendo acesso a um aplicativo pode usar suas próprias credenciais no escopo do usuário até que o acesso seja revogado. Essas credenciais de implantação não devem ser compartilhadas com outros usuários do Azure.
    >
    >

* **Credenciais de nível de aplicativo**: um conjunto de credenciais para cada aplicativo. Podem ser usadas para implantar nesse aplicativo somente. As credenciais para cada aplicativo são geradas automaticamente na criação do aplicativo e são encontradas no aplicativo de publicação do perfil. Não é possível configurar manualmente as credenciais, mas é possível redefini-las para um aplicativo a qualquer momento.

    > [!NOTE]
    > Para conceder a alguém o acesso a essas credenciais por meio do RBAC (Controle de Acesso Baseado em Função), você precisa torná-lo colaborador ou conceder a ele uma função superior no Aplicativo Web. Os leitores não têm permissão para publicar e, portanto, não podem acessar essas credenciais.
    >
    >

## <a name="userscope"></a>Definir e redefinir credenciais de usuário

Você pode configurar as credenciais de usuário na [folha de recursos](../azure-resource-manager/resource-group-portal.md#manage-resources) de qualquer aplicativo. Independentemente de para qual aplicativo você configura essas credenciais, elas se aplicam a todos os aplicativos e a todas as assinaturas na conta do Azure. 

Para configurar as credenciais de usuário:

1. No [Portal do Azure](https://portal.azure.com), clique em Serviço de Aplicativo > **&lt;qualquer_aplicativo>** > **Credenciais de implantação**.

    > [!NOTE]
    > No portal, você deve ter pelo menos um aplicativo antes de poder acessar a folha de credenciais de implantação. No entanto, com a [CLI do Azure](/cli/azure/webapp/deployment/user#set), é possível configurar credenciais de usuário sem um aplicativo existente.

2. Configure o nome de usuário e a senha e, em seguida, clique em **Salvar**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Depois que você tiver configurado suas credenciais de implantação, é possível encontrar o nome de usuário de implantação do *Git* na **Visão geral** de seu aplicativo,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

e o nome de usuário de implantação de *FTP* nas **Propriedades** de seu aplicativo.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> O Azure não mostra sua senha de implantação de usuário. Se você esquecer a senha, não é possível recuperá-la. No entanto, você pode redefinir suas credenciais seguindo as etapas nesta seção.
>
>  

## <a name="appscope"></a>Definir e redefinir credenciais de aplicativo
Para cada aplicativo no Serviço de Aplicativo, suas credenciais de aplicativo são armazenadas no perfil de publicação XML.

Para definir e redefinir credenciais de aplicativo:

1. No [Portal do Azure](https://portal.azure.com), clique em Serviço de Aplicativo > **&lt;qualquer_aplicativo>** > **Visão geral**.

2. Clique em **...Mais** > **Obter perfil de publicação** e inicie o download de um arquivo .PublishSettings.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Abra o arquivo .PublishSettings e localize a marca `<publishProfile>` com o atributo `publishMethod="FTP"`. Em seguida, obtenha seus atributos `userName` e `password`.
Essas são as credenciais de aplicativo.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Assim como as credenciais de usuário, o nome de usuário de implantação de FTP está no formato `<app_name>\<username>` e o nome de usuário de implantação do Git é apenas o `<username>` sem o `<app_name>\` precedente.

Para redefinir as credenciais de aplicativo:

1. No [Portal do Azure](https://portal.azure.com), clique em Serviço de Aplicativo > **&lt;qualquer_aplicativo>** > **Visão geral**.

2. Clique em **...Mais** > **Redefinir perfil de publicação**. Clique em **Sim** para confirmar a redefinição.

    A ação de redefinição invalida quaisquer arquivos .PublishSettings baixados anteriormente.

## <a name="next-steps"></a>Próximas etapas

Saiba como usar essas credenciais para implantar seu aplicativo do [Git local](app-service-deploy-local-git.md) ou usando [FTP/S](app-service-deploy-ftp.md).
