---
title: Criar um cofre de chaves do Azure e uma política de acesso de cofre usando o modelo ARM
description: Este artigo mostra como criar cofres de chaves do Azure e políticas de acesso do cofre usando um modelo de Azure Resource Manager.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e489efd335d2a46a0e55d4ceb9bdee809ae6c39d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595236"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Como criar um cofre de chaves do Azure e uma política de acesso do cofre usando um modelo do Resource Manager

[Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece um repositório seguro para segredos como chaves, senhas e certificados. Este artigo descreve o processo de implantação de um modelo de Azure Resource Manager (modelo ARM) para criar um cofre de chaves.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar as etapas neste artigo:

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-a-key-vault-resource-manager-template"></a>Criar um modelo do Key Vault Resource Manager

O modelo a seguir mostra uma maneira básica de criar um cofre de chaves. Alguns valores são especificados no modelo.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Para obter mais informações sobre Key Vault configurações de modelo, consulte [referência de modelo do ARM Key Vault](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Se um modelo for reimplantado, todas as políticas de acesso existentes no cofre de chaves serão substituídas. Recomendamos que você preencha a `accessPolicies` propriedade com as políticas de acesso existentes para evitar perder o acesso ao cofre de chaves. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Adicionar uma política de acesso a um modelo do Key Vault Resource Manager

Você pode implantar políticas de acesso a um cofre de chaves existente sem reimplantar o modelo inteiro do cofre de chaves. O modelo a seguir mostra uma maneira básica de criar políticas de acesso:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Para obter mais informações sobre Key Vault configurações de modelo, consulte [referência de modelo do ARM Key Vault](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Mais modelos do Key Vault Resource Manager

Há outros modelos do Resource Manager disponíveis para objetos de Key Vault:

| Segredos | simétricas | Certificados |
|--|--|--|
|<ul><li>[Início rápido](../secrets/quick-create-template.md)<li>[Referência](/azure/templates/microsoft.keyvault/vaults/secrets)|N/D|N/D|

Você pode encontrar mais modelos de Key Vault aqui: [Key Vault referência do Resource Manager](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Implantar os modelos

Você pode usar o portal do Azure para implantar os modelos anteriores usando a opção **criar seu próprio modelo no editor** , conforme descrito aqui: [implantar recursos de um modelo personalizado](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Você também pode salvar os modelos anteriores em arquivos e usar estes comandos:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) e [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar com os tutoriais e guias de início rápido subsequentes, poderá deixar esses recursos em vigor. Quando você não precisar mais dos recursos, exclua o grupo de recursos. Se você excluir o grupo, o cofre de chaves e os recursos relacionados também serão excluídos. Para excluir o grupo de recursos usando o CLI do Azure ou Azure PowerShell, conclua estas etapas:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Recursos

- Leia uma [visão geral do Azure Key Vault](../general/overview.md).
- Saiba mais sobre [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Examine a [Visão geral de segurança do Azure Key Vault](security-overview.md)

## <a name="next-steps"></a>Próximas etapas

- [Proteger o acesso a um cofre de chaves](secure-your-key-vault.md)
- [Autenticar em um cofre de chaves](authentication.md)
- [Guia do desenvolvedor do Cofre da Chave do Azure](developers-guide.md)
