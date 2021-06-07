---
title: Criar & excluir uma identidade gerenciada atribuída pelo usuário usando Azure Resource Manager
description: Instruções passo a passo sobre como criar e excluir identidades gerenciadas atribuídas pelo usuário usando Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98184867"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando Azure Resource Manager


Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Neste artigo, você cria uma identidade gerenciada atribuída ao usuário usando um Azure Resource Manager.

Não é possível listar e excluir uma identidade gerenciada atribuída ao usuário usando um modelo do Azure Resource Manager.  Consulte os artigos a seguir para criar e listar uma identidade gerenciada atribuída ao usuário:

- [Listar identidade gerenciada atribuída ao usuário](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Excluir identidade gerenciada atribuída ao usuário](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="template-creation-and-editing"></a>Criação e edição de modelo

Assim como com o portal do Azure e o script, os modelos do Azure Resource Manager permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

- Usar um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou usar como base um modelo comum existente ou um [modelo de início rápido](https://azure.microsoft.com/documentation/templates/).
- Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) ou do [estado atual da implantação](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), depois carregar e implantar usando o PowerShell ou a CLI.
- Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do Visual Studio para criar e implantar um modelo. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função [Administrador de identidade gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para criar uma identidade gerenciada atribuída ao usuário, use o modelo a seguir. Substitua o valor `<USER ASSIGNED IDENTITY NAME>` por seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como atribuir uma identidade gerenciada atribuída ao usuário a uma VM do Azure usando um modelo do Azure Resource Manager, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos](qs-configure-template-windows-vm.md).


