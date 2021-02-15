---
title: Usar identidades gerenciadas do Azure para criar ambientes no DevTest Labs | Microsoft Docs
description: Saiba como usar identidades gerenciadas no Azure para implantar ambientes em um laboratório no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185777"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Usar identidades gerenciadas do Azure para implantar ambientes em um laboratório 

Como proprietário de um laboratório, você pode usar uma identidade gerenciada para implantar ambientes em um laboratório. Esse recurso é útil em cenários em que o ambiente contém ou tem referências a recursos do Azure, como cofres de chaves, galerias de imagens compartilhadas e redes que são externas ao grupo de recursos do ambiente. Ele permite a criação de ambientes de área restrita que não são limitados ao grupo de recursos desse ambiente.

> [!NOTE]
> Atualmente, há suporte para uma única identidade atribuída pelo usuário por laboratório. 

## <a name="prerequisites"></a>Pré-requisitos

- [Crie, liste, exclua ou atribua uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Verifique se sua identidade gerenciada foi criada na mesma região e assinatura que o seu laboratório. A identidade gerenciada não precisa estar no mesmo grupo de recursos.

## <a name="use-azure-portal"></a>Usar o portal do Azure

Nesta seção, você, como proprietário do laboratório, usa o portal do Azure para adicionar uma identidade gerenciada pelo usuário ao laboratório. 

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure **DevTest Labs**.
1. Na lista de laboratórios, selecione o laboratório desejado.
1. Selecione a **configuração e a identidade das políticas**  ->  **(versão prévia)**. 
1. Para adicionar uma identidade atribuída ao usuário, selecione a guia **atribuído pelo usuário** .
1. Pressione **Adicionar** .
1. Selecione um usuário existente que você criou e/ou que tem acesso no menu suspenso.
 
    ![Adicionar identidade gerenciada pelo usuário](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Pressione **salvar** na parte superior da página.

    Depois de salvo, o laboratório usará essa identidade durante a implantação de todos os ambientes de laboratório. Você também pode acessar o recurso de identidade no Azure selecionando a identidade na lista. 

O proprietário do laboratório não precisa fazer nada especial durante a implantação de um ambiente, contanto que a identidade adicionada ao laboratório tenha permissões para os recursos externos que o ambiente precisa acessar. 

Para alterar a identidade gerenciada pelo usuário atribuída ao laboratório, remova a identidade anexada ao laboratório primeiro e, em seguida, adicione outra ao laboratório. Para remover uma identidade anexada ao laboratório, selecione **... (reticências)** e clique em **remover**. 

## <a name="use-api"></a>Usar API

1. Depois de criar uma identidade, observe a ID de recurso dessa identidade. Ele deve ser semelhante ao exemplo a seguir: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Execute um método https PUT para adicionar um novo `ServiceRunner` recurso ao laboratório semelhante ao exemplo a seguir. O recurso do executor de serviço é um recurso de proxy para gerenciar e controlar identidades gerenciadas no DevTest Labs. O nome do executor de serviço pode ser qualquer nome válido, mas recomendamos que você use o nome do recurso de identidade gerenciada. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Veja um exemplo: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Depois que a identidade atribuída pelo usuário for adicionada ao laboratório, o serviço de Azure DevTest Labs o usará durante a implantação de ambientes de Azure Resource Manager. Por exemplo, se você precisar que seu modelo do Resource Manager acesse uma imagem da Galeria de imagens compartilhada externa, certifique-se de que a identidade adicionada ao laboratório tenha as permissões mínimas necessárias para o recurso da Galeria de imagens compartilhadas. 
