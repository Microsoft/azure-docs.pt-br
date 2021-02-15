---
title: Reparar uma conta do Azure automanaged interrompida
description: Se você moveu recentemente uma assinatura que contém uma conta de autogerenciamento para um novo locatário, precisará reconfigurá-la. Neste artigo, você aprenderá como.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183695"
---
# <a name="repair-an-automanage-account"></a>Reparar uma conta de autogerenciamento
Sua [conta de gerenciamento automático do Azure](./automanage-virtual-machines.md#automanage-account) é o contexto de segurança ou a identidade sob a qual as operações automatizadas ocorrem. Se você moveu recentemente uma assinatura que contém uma conta de autogerenciamento para um novo locatário, precisará reconfigurar a conta. Para reconfigurá-lo, você precisa redefinir o tipo de identidade e atribuir as funções apropriadas para a conta.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Etapa 1: redefinir o tipo de identidade da conta de autogerenciamento
Redefina o tipo de identidade de conta de autogerenci usando o seguinte modelo de Azure Resource Manager (ARM). Salve o arquivo localmente como armdeploy.jsem ou em um nome semelhante. Anote o nome e o local da sua conta de autogerenciamento porque eles são parâmetros obrigatórios no modelo ARM.

1. Crie uma implantação do Resource Manager usando o modelo a seguir. Use `identityType = None`.
    * Você pode criar a implantação no CLI do Azure usando o `az deployment sub create` . Para obter mais informações, consulte [AZ Deployment sub](/cli/azure/deployment/sub).
    * Você pode criar a implantação no PowerShell usando o `New-AzDeployment` módulo. Para obter mais informações, consulte [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Execute o mesmo modelo de ARM novamente com `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Etapa 2: atribuir funções apropriadas para a conta de autogerenciamento
A conta de autogerenciamento requer as funções de colaborador de política de recurso e colaborador na assinatura que contém as VMs que o autogerenci gerencia. Você pode atribuir essas funções usando o portal do Azure, os modelos ARM ou o CLI do Azure.

Se você estiver usando um modelo ARM ou o CLI do Azure, precisará da ID da entidade de segurança (também conhecida como a ID de objeto) da sua conta de autogerenciamento. (Você não precisará da ID se estiver usando o portal do Azure.) Você pode encontrar essa ID usando estes métodos:

- [CLI do Azure](/cli/azure/ad/sp): Use o comando `az ad sp list --display-name <name of your Automanage Account>` .

- Portal do Azure: Vá para **Azure Active Directory** e procure sua conta de autogerenciamento por nome. Em **aplicativos empresariais**, selecione o nome da conta de autogerenciamento quando ele for exibido.

### <a name="azure-portal"></a>Portal do Azure
1. Em **assinaturas**, vá para a assinatura que contém suas VMs autogerenciadas.
1. Vá para **controle de acesso (iam)**.
1. Selecione **Adicionar atribuições de função**.
1. Selecione a função **colaborador** e insira o nome da sua conta de autogerenciamento.
1. Selecione **Salvar**.
1. Repita as etapas de 3 a 5, desta vez com a função **colaborador de política de recurso** .

### <a name="arm-template"></a>Modelo de ARM
Execute o modelo ARM a seguir. Você precisará da ID da entidade de segurança da sua conta de autogerenciamento. As etapas para obtê-lo estão no início desta seção. Insira a ID quando você for solicitado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>CLI do Azure
Execute estes comandos:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o autogerenciamento do Azure](./automanage-virtual-machines.md)