---
title: configurar credenciais de implantação
description: Saiba quais são os tipos de credenciais de implantação no Serviço de Aplicativo do Azure e como configurá-las e usá-las.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c7d3c7c8b5da40a4e9ccd9085af5a850b9ebc3dd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052340"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar as credenciais de implantação do Serviço de Aplicativo do Azure
Para proteger a implantação de aplicativo de um computador local, [Azure app serviço](./overview.md) dá suporte a dois tipos de credenciais para implantação [local do git](deploy-local-git.md) e [implantação de FTP/S](deploy-ftp.md). Essas credenciais não são iguais às suas credenciais de assinatura do Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> A página do **centro de desenvolvimento (clássico)** na portal do Azure, que é a antiga experiência de implantação, será preterida em março de 2021. Essa alteração não afetará nenhuma configuração de implantação existente em seu aplicativo e você poderá continuar a gerenciar a implantação do aplicativo na página do **centro de implantação** .

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Configurar credenciais de escopo do usuário

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Execute o comando [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) . Substitua \<username> e \<password> pelo nome de usuário e a senha do usuário de implantação. 

- O nome de usuário deve ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@". 
- A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a senha como `null`.

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Não é possível configurar as credenciais de escopo do usuário com Azure PowerShell. Use um método diferente ou considere o [uso de credenciais de escopo de aplicativo](#appscope). 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Você pode configurar suas credenciais de escopo do usuário na página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)de qualquer aplicativo. Independentemente de qual aplicativo você configurar essas credenciais, ele se aplica a todos os aplicativos para todas as assinaturas em sua conta do Azure. 

No [portal do Azure](https://portal.azure.com), você deve ter pelo menos um aplicativo para poder acessar a página de credenciais de implantação. Para configurar suas credenciais de escopo do usuário:

1. No menu à esquerda do seu aplicativo, selecione >   >  **as credenciais de FTPS** da central de implantação ou **credenciais locais do git/FTPS**.

    ![Mostra como você pode selecionar o painel de FTP na central de implantação no Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Role para baixo até **escopo do usuário**, configure o **nome** de usuários e a **senha** e, em seguida, selecione **salvar**.

Depois que você tiver configurado suas credenciais de implantação, é possível encontrar o nome de usuário de implantação do *Git* na página **Visão geral** de seu aplicativo.

![Mostra como localizar o nome de usuário de implantação do git na página de visão geral do seu aplicativo.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implantação do Git estiver configurada, a página mostrará um **Nome de usuário da implantação/Git**; caso contrário, um **Nome de usuário da implantação/FTP**.

> [!NOTE]
> O Azure não mostra sua senha de implantação de escopo do usuário. Se você esquecer a senha, poderá redefinir suas credenciais seguindo as etapas desta seção.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Usar credenciais de escopo do usuário com FTP/FTPS

A autenticação em um ponto de extremidade de FTP/FTPS usando credenciais de escopo de usuário requer um nome de usuário no seguinte formato: `<app-name>\<user-name>`

Uma vez que as credenciais de escopo do usuário são vinculadas ao usuário e não a um recurso específico, o nome de usuário deve estar nesse formato para direcionar a ação de entrada para o ponto de extremidade do aplicativo correto.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Obter credenciais de escopo de aplicativo

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Obtenha as credenciais de escopo do aplicativo usando o comando [AZ webapp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Por exemplo: 

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Para a [implantação do git local](deploy-local-git.md), você também pode usar o comando [AZ webapp Deployment List-Publishing-Credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) para obter um URI remoto do git para seu aplicativo, com as credenciais de escopo do aplicativo já inseridas. Por exemplo: 

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Obtenha as credenciais de escopo do aplicativo usando o comando [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Por exemplo: 

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. No menu à esquerda do seu aplicativo, selecione a **central de implantação**  >  **credenciais de FTPS** ou **credenciais locais de git/FTPS**.

    ![Mostra como você pode selecionar o painel de FTP na central de implantação no Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Na seção **escopo do aplicativo** , selecione o link **copiar** para copiar o nome de usuário ou a senha.

-----

## <a name="reset-application-scope-credentials"></a>Redefinir credenciais de escopo de aplicativo

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Redefina as credenciais de escopo do aplicativo usando o comando [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Redefina as credenciais de escopo do aplicativo usando o comando [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) :

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. No menu à esquerda do seu aplicativo, selecione a **central de implantação**  >  **credenciais de FTPS** ou **credenciais locais de git/FTPS**.

    ![Mostra como você pode selecionar o painel de FTP na central de implantação no Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Na seção **escopo do aplicativo** , selecione **Redefinir**.

-----

## <a name="disable-basic-authentication"></a>Desabilitar a autenticação básica

Algumas organizações precisam atender aos requisitos de segurança e, em vez disso, desabilitar o acesso via FTP ou WebDeploy. Dessa forma, os membros da organização só podem acessar seus serviços de aplicativos por meio de APIs controladas pelo Azure Active Directory (AD do Azure).

### <a name="ftp"></a>FTP

Para desabilitar o acesso FTP ao site, execute o comando da CLI a seguir. Substitua os espaços reservados pelo seu grupo de recursos e nome do site. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Para confirmar se o acesso ao FTP está bloqueado, você pode tentar autenticar usando um cliente FTP, como o FileZilla. Para recuperar as credenciais de publicação, vá para a folha visão geral do seu site e clique em baixar perfil de publicação. Use o nome de host, o nome de usuário e a senha do FTP do arquivo para autenticar e você receberá uma resposta de erro 401, indicando que você não está autorizado.

### <a name="webdeploy-and-scm"></a>WebDeploy e SCM

Para desabilitar o acesso básico de autenticação para a porta WebDeploy e o site SCM, execute o comando da CLI a seguir. Substitua os espaços reservados pelo seu grupo de recursos e nome do site. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Para confirmar se as credenciais do perfil de publicação estão bloqueadas no WebDeploy, tente [publicar um aplicativo Web usando o Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Desabilitar o acesso à API

A API na seção anterior é apoiada no controle de acesso baseado em função do Azure (RBAC do Azure), o que significa que você pode [criar uma função personalizada](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) e atribuir usuários de priveldged menores à função para que eles não possam habilitar a autenticação básica em nenhum site. Para configurar a função personalizada, [siga estas instruções](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Você também pode usar [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) para auditar quaisquer solicitações de autenticação bem-sucedidas e usar [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) para impor essa configuração para todos os sites em sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Saiba como usar essas credenciais para implantar seu aplicativo do [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
