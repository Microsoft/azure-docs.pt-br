---
title: Key Vault segredo com o modelo
description: Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 05749fe2e9179051c3183ea2e592cf7190ddb347
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889851"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação

Em vez de colocar um valor seguro (como uma senha) diretamente no seu arquivo de modelo ou parâmetro, você pode recuperar o valor de um [Azure Key Vault](../../key-vault/general/overview.md) durante uma implantação. Você recupera o valor fazendo referência ao cofre de chaves e ao segredo no arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. O cofre de chaves pode existir em uma assinatura diferente do grupo de recursos no qual você está implantando.

Este artigo se concentra no cenário de passar um valor confidencial no como um parâmetro de modelo. Ele não aborda o cenário de configuração de uma propriedade de máquina virtual para a URL de um certificado em um Key Vault. Para obter um modelo de início rápido desse cenário, consulte [instalar um certificado de Azure Key Vault em uma máquina virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Implantar Key Vaults e segredos

Para acessar um cofre de chaves durante a implantação do modelo, defina `enabledForTemplateDeployment` no cofre de chaves como `true` .

Se você já tiver um Key Vault, verifique se ele permite implantações de modelo.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Para criar um novo Key Vault e adicionar um segredo, use:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Como o proprietário do cofre de chaves, você terá acesso automaticamente à criação de segredos. Se o usuário que está trabalhando com segredos não for o proprietário do cofre de chaves, conceda acesso com:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Para obter mais informações sobre como criar cofres de chaves e adicionar segredos, consulte:

- [Definir e recuperar um segredo usando a CLI](../../key-vault/secrets/quick-create-cli.md)
- [Definir e recuperar um segredo usando o PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Definir e recuperar um segredo usando o portal](../../key-vault/secrets/quick-create-portal.md)
- [Definir e recuperar um segredo usando o .NET](../../key-vault/secrets/quick-create-net.md)
- [Definir e recuperar um segredo usando o Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Permitir acesso aos segredos

O usuário que implanta o modelo deve ter a `Microsoft.KeyVault/vaults/deploy/action` permissão para o escopo do grupo de recursos e do cofre de chaves. Ambas as funções [Proprietário](../../role-based-access-control/built-in-roles.md#owner) e [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) concedem esse acesso. Se você criou o cofre de chaves, você é o proprietário para que tenha a permissão.

O procedimento a seguir mostra como criar uma função com a permissão mínima e como atribuir o usuário

1. Crie um arquivo JSON de definição de função personalizada:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Substitua "00000000-0000-0000-0000-000000000000" pela ID da assinatura.

2. Crie a nova função usando o arquivo JSON:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Os exemplos atribuem a função personalizada ao usuário no nível do grupo de recursos.

Ao usar um Key Vault com o modelo para um [Aplicativo Gerenciado](../managed-applications/overview.md), você precisa conceder acesso à entidade de serviço **Provedor de Recursos do Dispositivo**. Para obter mais informações, confira [Segredo do Azure Key Vault durante a implantação de Aplicativos Gerenciados do Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Fazer referência segredos com ID estática

Com esta abordagem, você faz referência ao Key Vault no arquivo de parâmetro, não no modelo. A imagem a seguir mostra como o arquivo de parâmetro faz referência ao segredo e passa o valor para o modelo.

![Diagrama de ID estática de integração do Key Vault do Resource Manager](./media/key-vault-parameter/statickeyvault.png)

[Tutorial: integrar Azure Key Vault no Gerenciador de recursos implantação de modelo](./template-tutorial-use-key-vault.md) usa esse método.

O modelo a seguir implanta um SQL Server que inclui uma senha de administrador. O parâmetro de senha é definido como uma cadeia de caracteres segura. No entanto, o modelo não especifica de onde vem esse valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Agora, crie um arquivo de parâmetro para o modelo anterior. No arquivo de parâmetro, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, referenciar o segredo do cofre de chave. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chaves e o nome do segredo:

No arquivo de parâmetro a seguir, o segredo do cofre de chaves já deve existir e você fornece um valor estático para sua ID de recurso.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

Se você precisar usar uma versão do segredo diferente da versão atual, use a propriedade `secretVersion`.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implante o modelo e passe o arquivo de parâmetro:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Fazer referência a segredos com ID dinâmica

A seção anterior mostrou como passar uma ID do recurso estático para o segredo do cofre de chaves do parâmetro. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Ou, você pode passar valores de parâmetros para o modelo em vez de criar um parâmetro de referência no arquivo de parâmetros. Em ambos os casos, é possível gerar dinamicamente a ID do recurso para um segredo do cofre de chaves usando um modelo vinculado.

Não é possível gerar dinamicamente a ID do recurso no arquivo de parâmetros porque as expressões de modelo não são permitidas no arquivo de parâmetros.

No modelo pai, você adiciona o modelo aninhado e passa um parâmetro que contém a ID de recurso gerada dinamicamente. A imagem a seguir mostra como um parâmetro no modelo vinculado faz referência ao segredo.

![ID dinâmica](./media/key-vault-parameter/dynamickeyvault.png)

O seguinte modelo cria dinamicamente a ID do cofre de chaves e a passa como um parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre os cofres de chaves, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/general/overview.md).
- Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- Para um módulo Microsoft Learn que cobre a passagem de um valor seguro de um cofre de chaves, consulte [gerenciar implantações de nuvem complexas usando recursos avançados de modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
