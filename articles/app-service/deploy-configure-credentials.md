---
title: Configurar credenciais de implantação – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar as credenciais de implantação do Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/10/2019
ms.author: cephalin
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 65e5d6bacc67c64fa21268a853dc9c9d9b447da7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617178"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar as credenciais de implantação do Serviço de Aplicativo do Azure
O [Serviço de Aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714) oferece suporte a dois tipos de credenciais para a [implantação local do Git](deploy-local-git.md) e a [implantação de FTP/S](deploy-ftp.md). Essas credenciais não são as mesmas credenciais do Active Directory do Azure.

* **Credenciais de nível de usuário**: um conjunto de credenciais para toda a conta do Azure. Ele pode ser usado para implantar no Serviço de Aplicativo para qualquer aplicativo e em qualquer assinatura que a conta do Azure tem permissão para acessar. É o conjunto padrão que aparece na GUI do portal (como a **Visão geral** e **Propriedades** da página de recursos [do aplicativo](../azure-resource-manager/manage-resources-portal.md#manage-resources)). Quando um usuário é concedido acesso ao aplicativo por meio do controle de acesso baseado em função (RBAC) ou permissões de coadministrador, o que o usuário pode usar suas próprias credenciais de usuário até que o acesso seja revogado. Não compartilhe essas credenciais com outros usuários do Azure.

* **Credenciais de nível de aplicativo**: um conjunto de credenciais para cada aplicativo. Podem ser usadas para implantar nesse aplicativo somente. As credenciais de cada aplicativo são geradas automaticamente na criação do aplicativo. Eles não podem ser configurados manualmente, mas podem ser redefinidos a qualquer momento. Para que um usuário tenha acesso às credenciais no nível do aplicativo via (RBAC), esse usuário deve ser colaborador ou superior no aplicativo. Os leitores não têm permissão para publicar e não pode acessar essas credenciais.

## <a name="userscope"></a>Definir e redefinir credenciais de usuário

Você pode configurar as credenciais de usuário na [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources) de qualquer aplicativo. Independentemente de para qual aplicativo você configura essas credenciais, elas se aplicam a todos os aplicativos e a todas as assinaturas na conta do Azure. 

Para configurar as credenciais de usuário:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, clique em **serviços de aplicativos** >  **&lt;qualquer_aplicativo >**  > **implantação Centro** > **credenciais de implantação**.

    No portal, você deve ter pelo menos um aplicativo antes de poder acessar a página de credenciais de implantação. No entanto, com a [CLI do Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), é possível configurar credenciais de usuário sem um aplicativo existente.

2. Clique em **credenciais de usuário**, configure o nome de usuário e senha e, em seguida, clique em **credenciais de salvamento**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Depois que você tiver configurado suas credenciais de implantação, é possível encontrar o nome de usuário de implantação do *Git* na **Visão geral** de seu aplicativo,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

e o nome de usuário de implantação de *FTP* nas **Propriedades** de seu aplicativo.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> O Azure não mostra sua senha de implantação de usuário. Se você esquecer a senha, poderá redefinir suas credenciais seguindo as etapas desta seção.
>
>  

## <a name="use-user-level-credentials-with-ftpftps"></a>Usar credenciais de usuário com o FTP/FTPS

Fazer a autenticação em um ponto de extremidade FTP/FTPS usando um nome de usuário de requirers de credenciais de usuário no seguinte formato: `<app-name>\<user-name>`

Uma vez que as credenciais de usuário são vinculadas ao usuário e não um recurso específico, o nome de usuário deve estar no seguinte formato para direcionar a ação de sinal para o ponto de extremidade do aplicativo certo.

## <a name="appscope"></a>Definir e redefinir credenciais de aplicativo
Para definir e redefinir credenciais de aplicativo:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, clique em **serviços de aplicativos** >  **&lt;qualquer_aplicativo >**  > **implantação Centro** > **credenciais de implantação**.

2. Clique em **credenciais de aplicativo**e clique no **cópia** link para copiar o nome de usuário ou senha.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Para redefinir as credenciais de nível de aplicativo, clique em **Redefinir credenciais** na mesma caixa de diálogo.

## <a name="next-steps"></a>Próximas etapas

Saiba como usar essas credenciais para implantar seu aplicativo do [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
