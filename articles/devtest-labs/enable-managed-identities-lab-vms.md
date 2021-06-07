---
title: Habilitar identidades gerenciadas em suas VMs de laboratório no Azure DevTest Labs
description: Este artigo mostra como um proprietário de laboratório pode habilitar identidades gerenciadas atribuídas pelo usuário em suas máquinas virtuais de laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88717618"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Habilitar identidades gerenciadas atribuídas pelo usuário em máquinas virtuais de laboratório no Azure DevTest Labs
Como proprietário de um laboratório, você pode habilitar identidades gerenciadas atribuídas pelo usuário em suas VMs (máquinas virtuais) de laboratório em Azure DevTest Labs.

Uma identidade gerenciada pode ser usada para autenticar em qualquer serviço que ofereça suporte à autenticação Azure Active Directory (AD), incluindo Key Vault, sem passar credenciais no código. Para obter mais informações sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Com esse recurso, os usuários do laboratório podem compartilhar recursos do Azure, como o banco de dados SQL do Azure, no contexto do laboratório. A autenticação para o recurso é levada em conta com a própria identidade. Uma vez configurado, todas as VMs de laboratório existentes/recém-criadas serão habilitadas com essa identidade. Os usuários do laboratório podem acessar recursos depois de conectados a seus computadores.

> [!NOTE]
> Você pode adicionar várias identidades gerenciadas atribuídas ao usuário para serem habilitadas em suas VMs de laboratório.

## <a name="use-azure-portal"></a>Usar o portal do Azure
Para adicionar uma identidade gerenciada atribuída ao usuário para VMs do laboratório, siga estas etapas:

1. [Criar uma identidade gerenciada atribuída pelo usuário em sua assinatura](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navegue até a página **configuração e políticas** do seu laboratório.
1. Selecione **identidade (versão prévia)** no menu à esquerda.
1. Selecione a guia **máquina virtual** .
1. Selecione **Adicionar** para selecionar uma identidade existente de uma lista suspensa preenchida previamente. 

    > [!div class="mx-imgBorder"]
    > ![Botão Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Selecione uma **identidade gerenciada pelo usuário** existente na lista suspensa e selecione **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar identidade](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Usar API

1.  Depois de criar uma identidade, observe a ID de recurso da identidade. Ele deve ser semelhante ao exemplo a seguir: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Execute um método HTTPS PUT para adicionar um novo recurso do **perrunner** ao laboratório, conforme mostrado no exemplo a seguir. 

    O recurso do executor de serviço é um recurso de proxy para gerenciar e controlar identidades gerenciadas no DevTest Labs. O nome do executor de serviço pode ser qualquer nome válido, mas recomendamos que você use o nome do recurso de identidade gerenciada.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre identidades gerenciadas, confira [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md).







