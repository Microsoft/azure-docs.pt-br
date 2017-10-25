---
title: Como configurar o MSI em uma VM do Azure usando um modelo
description: "Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciado) em uma VM do Azure usando um modelo do Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 3a81ab859bea5bfe53c7f761e0b40ad481c4c41a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurar a Identidade de Serviço Gerenciado de um VM usando um modelo

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

A MSI (Identidade de Serviço Gerenciado) fornece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover o MSI de uma VM do Windows Azure usando um modelo de implantação do Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure ou em uma VM existente

Assim como com o portal do Azure e o script, os modelos do Azure Resource Manager permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

   - Use um [modelo personalizado do Azure Marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou use como base um modelo comum existente ou de [Início Rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) ou do [estado atual da implantação](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar um [editor JSON local (por exemplo, VS Code)](../azure-resource-manager/resource-manager-create-first-template.md), depois carregar e implantar usando o PowerShell ou a CLI.
   - Usar o [projeto do Grupo de Recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhido, a sintaxe do modelo será a mesma durante a implantação inicial e a reimplantação. Habilitar a MSI em uma VM nova ou existente é feito da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) para implantações:

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual. Verifique também se a sua conta pertence a uma função que lhe dá permissões de gravação na VM (por exemplo, a função de “Colaborador da Máquina Virtual”).

2. Depois de carregar o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachines` de interesse na seção `resources`. O seu pode parecer um pouco diferente do mostrado captura de tela a seguir, dependendo do editor usado e de se você está editando um modelo para uma implantação nova ou existente.

   >[!NOTE] 
   > Este exemplo supõe que variáveis como `vmName`, `storageAccountName`, e `nicName` foram definidas no modelo.
   >

   ![Captura de tela do modelo – localizar VM](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Adicione a propriedade `"identity"` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachines"`. Use a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Depois, adicione a extensão da MSI da VM como um elemento `resources`. Use a seguinte sintaxe:

   >[!NOTE] 
   > O exemplo a seguir pressupõe que uma extensão de VM do Windows (`ManagedIdentityExtensionForWindows`) está sendo implantada. Você também pode configurar para Linux usando `ManagedIdentityExtensionForLinux` em vez disso, para os elementos `"name"` e `"type"`.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. Quando terminar, seu modelo deverá ser semelhante ao seguinte:

   ![Captura de tela do modelo após a atualização](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma VM que não precise mais de uma MSI:

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual. Verifique também se a sua conta pertence a uma função que lhe dá permissões de gravação na VM (por exemplo, a função de “Colaborador da Máquina Virtual”).

2. Remova os dois elementos que foram adicionados na seção anterior: a propriedade da VM `"identity"` e o `"Microsoft.Compute/virtualMachines/extensions"` recurso.

## <a name="related-content"></a>Conteúdo relacionado

- Para obter uma perspectiva mais ampla sobre MSI, leia a [visão geral da Identidade de Serviço Gerenciado](msi-overview.md).

