---
title: Configurar identidades gerenciadas na VM do Azure usando um modelo – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure, usando um modelo do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4948e17d1e0e782a8fa18c3eb5a2185e816a459a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631397"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código.

Neste artigo, usando o modelo de implantação do Azure Resource Manager, você aprende como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com o uso do modelo de implantação do Azure Resource Manager, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Assim como com o Portal do Azure e o script, os modelos do [Azure Resource Manager](../../azure-resource-manager/management/overview.md) permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

   - Usar um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou usar como base um modelo comum existente ou um [modelo de início rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/templates/export-template-portal.md) ou do [estado atual da implantação](../../azure-resource-manager/templates/export-template-portal.md).
   - Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), depois carregar e implantar usando o PowerShell ou a CLI.
   - Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhido, a sintaxe do modelo será a mesma durante a implantação inicial e a reimplantação. Habilitar uma identidade gerenciada atribuída ao usuário ou ao sistema em uma VM nova ou existente é feita da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/templates/deployment-modes.md) para implantações.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você habilitará e desabilitará uma identidade gerenciada atribuída ao sistema usando um modelo do Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitar identidade gerenciada atribuída ao sistema durante a criação de uma VM do Azure ou em uma VM existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função [Colaboração da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual.

2. Para habilitar a identidade gerenciada atribuída ao sistema, carregue o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachines` de interesse dentro da seção `resources` e adicione a propriedade `"identity"` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachines"`. Use a seguinte sintaxe:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Quando você terminar, as seguintes seções deverão ser adicionadas à seção `resource` do modelo e ela será semelhante à seguinte:

   ```JSON
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Atribuir uma função à identidade gerenciada atribuída ao sistema da VM

Após habilitar a identidade atribuída ao sistema na VM, é recomendável conceder uma função a ela, como o acesso de **Leitor** ao grupo de recursos no qual foi criado.

Para atribuir uma função à identidade atribuída pelo sistema da sua VM, sua conta precisa da atribuição de função [Administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator).

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e adicione as informações a seguir para conceder o acesso de **Leitor** na VM ao grupo de recursos no qual ela foi criada.  A estrutura do modelo pode variar conforme o editor e o modelo de implantação escolhido.

   Na seção `parameters`, adicione o seguinte:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Na seção `variables`, adicione o seguinte:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Na seção `resources`, adicione o seguinte:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Desabilitar uma identidade gerenciada atribuída ao sistema de uma VM do Azure

Para remover a identidade gerenciada atribuída pelo sistema de uma VM, sua conta precisa da atribuição de função [Atribuída do Virtual Machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e localize o recurso `Microsoft.Compute/virtualMachines` de interesse na seção `resources`. Se você tiver uma VM que tenha apenas a identidade gerenciada atribuída ao sistema, poderá desabilitá-la alterando o tipo de identidade para `None`.  

   **Microsoft.Compute/virtualMachines API versão 2018-06-01**

   Se a VM tiver identidades gerenciadas atribuídas ao usuário e ao sistema, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` junto com os valores de dicionário `userAssignedIdentities`.

   **Microsoft.Compute/virtualMachines API versão 2018-06-01**

   Se `apiVersion` for `2017-12-01` e a VM tiver identidades gerenciadas ao sistema e ao usuário, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` junto com a matriz `identityIds` das identidades gerenciadas atribuídas ao usuário.  

O seguinte exemplo mostra como remover uma identidade gerenciada atribuída ao sistema de uma VM sem identidades gerenciadas atribuídas ao usuário:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você atribui uma identidade gerenciada atribuída ao usuário a uma VM do Azure usando o modelo do Azure Resource Manager.

> [!Note]
> Para criar uma identidade gerenciada atribuída ao usuário usando um modelo do Azure Resource Manager, consulte [Criar uma identidade gerenciada atribuída ao usuário](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Atribuir uma identidade gerenciada atribuída ao usuário a uma VM do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. No elemento `resources`, adicione a seguinte entrada para atribuir uma identidade gerenciada atribuída ao usuário à VM.  Certifique-se de substituir `<USERASSIGNEDIDENTITY>` pelo nome da identidade gerenciada atribuída ao usuário que você criou.

   **Microsoft.Compute/virtualMachines API versão 2018-06-01**

   Se `apiVersion` for `2018-06-01`, as identidades gerenciadas atribuídas ao usuário serão armazenadas no formato de dicionário `userAssignedIdentities` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do modelo.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines API versão 2017-12-01**

   Se `apiVersion` for `2017-12-01`, as identidades gerenciadas atribuídas ao usuário serão armazenadas na matriz `identityIds` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do modelo.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Quando você terminar, as seguintes seções deverão ser adicionadas à seção `resource` do modelo e ela será semelhante à seguinte:

   **Microsoft.Compute/virtualMachines API versão 2018-06-01**    

   ```JSON
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```
   **Microsoft.Compute/virtualMachines API versão 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída pelo usuário de uma VM, sua conta precisa da atribuição de função [Atribuído do Virtual Machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e localize o recurso `Microsoft.Compute/virtualMachines` de interesse na seção `resources`. Se você tiver uma VM que tenha apenas a identidade gerenciada atribuída ao usuário, será possível desabilitá-la alterando o tipo de identidade para `None`.

   O exemplo a seguir mostra como remover todas as identidades gerenciadas atribuídas ao usuário de uma VM sem identidades gerenciadas atribuídas ao sistema:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines API versão 2018-06-01**

   Para remover uma identidade gerenciada atribuída ao usuário único de uma VM, remova-a do dicionário `useraAssignedIdentities`.

   Caso você tenha uma identidade gerenciada atribuída ao sistema, mantenha a identidade com o valor `type` no valor `identity`.

   **Microsoft.Compute/virtualMachines API versão 2017-12-01**

   Para remover uma identidade gerenciada atribuída ao usuário único de uma VM, remova-a da matriz `identityIds`.

   Caso você tenha uma identidade gerenciada atribuída ao sistema, mantenha a identidade com o valor `type` no valor `identity`.

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral dos recursos do Azure](overview.md).
