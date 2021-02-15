---
title: Autenticação de uma identidade gerenciada com o Azure Active Directory
description: Este artigo fornece informações sobre como autenticar uma identidade gerenciada com Azure Active Directory para acessar o serviço de Signaler do Azure
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 46d66451bb8f2cd6c5d4448131b5f4842a728fd0
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797455"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Autenticar uma identidade gerenciada com Azure Active Directory para acessar os recursos do Signalr do Azure
O serviço de Signaler do Azure dá suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autorizar o acesso aos recursos do serviço de Signaler do Azure usando as credenciais do Azure AD de aplicativos em execução em VMs (máquinas virtuais) do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com seus aplicativos que são executados na nuvem.

Este artigo mostra como autorizar o acesso a um serviço de Signaler do Azure usando uma identidade gerenciada de uma VM do Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de usar identidades gerenciadas para recursos do Azure para autorizar recursos do serviço de SIgnaler do Azure de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell do Azure](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço de Signalr do Azure por meio de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações de RBAC (controle de acesso baseado em função) para essa identidade gerenciada. O serviço de sinalizador do Azure define funções RBAC que abrangem permissões para adquirir `AccessKey` ou `ClientToken` . Quando a função RBAC é atribuída a uma identidade gerenciada, a identidade gerenciada recebe acesso ao serviço de Signaler do Azure no escopo apropriado.

Para obter mais informações sobre como atribuir funções RBAC, consulte [autenticar com Azure Active Directory para acessar os recursos do serviço de signaler do Azure](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Conectar-se ao serviço de Signaler do Azure com identidades gerenciadas
Para se conectar ao serviço de Signaler do Azure com identidades gerenciadas, você precisa atribuir a identidade a função e o escopo apropriado. O procedimento nesta seção usa um aplicativo simples que é executado em uma identidade gerenciada e acessa os recursos do serviço Signaler do Azure.

Aqui, estamos usando um recurso de máquina virtual do Azure de exemplo.

1. Vá para **configurações** e selecione **identidade**. 
1. Selecione o **status** a ser **ativado**. 
1. Clique em **Salvar** para salvar a configuração. 

    ![Identidade gerenciada para uma máquina virtual](./media/authenticate/identity-virtual-machine.png)

Depois de habilitar essa configuração, uma nova identidade de serviço será criada em seu Azure Active Directory (AD do Azure) e configurada no host do serviço de aplicativo.

Agora, atribua essa identidade de serviço a uma função no escopo necessário em seus recursos do serviço de Signaler do Azure.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Atribuir funções de RBAC usando o portal do Azure  
Para saber mais sobre como gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure, consulte [Este artigo](..//role-based-access-control/role-assignments-portal.md). 

Depois de determinar o escopo apropriado para uma atribuição de função, navegue até esse recurso na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

1. Na [portal do Azure](https://portal.azure.com/), navegue até o recurso de sinalização.
1. Selecione **controle de acesso (iam)** para exibir as configurações de controle de acesso para o Signalr do Azure. 
1. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais. Selecione o botão **Adicionar** na barra de ferramentas e, em seguida, selecione **Adicionar atribuição de função**. 

    ![Botão Adicionar na barra de ferramentas](./media/authenticate/role-assignments-add-button.png)

1. Na página **Adicionar atribuição de função** , execute as seguintes etapas:
    1. Selecione a **função de signalr do Azure** que você deseja atribuir. 
    1. Pesquise para localizar a **entidade de segurança** (usuário, grupo, entidade de serviço) à qual você deseja atribuir a função.
    1. Selecione **salvar** para salvar a atribuição de função. 

        ![Atribuir função a um aplicativo](./media/authenticate/assign-role-to-application.png)

    1. A identidade à qual você atribuiu a função aparece listada sob essa função. Por exemplo, a imagem a seguir mostra esse aplicativo `signalr-dev` e `signalr-service` está na função de servidor de aplicativo signalr. 
        
        ![Lista de atribuição de função](./media/authenticate/role-assignment-list.png)

Você pode seguir etapas semelhantes para atribuir uma função com escopo ao grupo de recursos ou assinatura. Depois de definir a função e seu escopo, você pode testar esse comportamento com exemplos [neste local do GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="samples-code-while-configuring-your-app-server"></a>Exemplo de código ao configurar seu servidor de aplicativos

Adicione as seguintes opções quando `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre o RBAC, confira [o que é o Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md)?
- Para saber como atribuir e gerenciar atribuições de função do Azure com Azure PowerShell, CLI do Azure ou a API REST, consulte estes artigos:
    - [Gerenciar o controle de acesso baseado em função (RBAC) com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a CLI do Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gerenciar o controle de acesso baseado em função (RBAC) com a API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gerenciar RBAC (controle de acesso baseado em função) com modelos de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Consulte os seguintes artigos relacionados:
- [Autenticar um aplicativo com Azure Active Directory para acessar o serviço de Signaler do Azure](authenticate-application.md)
- [Autorizar o acesso ao serviço de Signaler do Azure usando o Azure Active Directory](authorize-access-azure-active-directory.md)