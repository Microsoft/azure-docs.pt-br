---
title: Segredo do Cofre de Chaves com o modelo do Azure Resource Manager | Microsoft Docs
description: Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: de52dbb10d515a2255b5886df5bf0a0faa454f6b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672750"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação

Em vez de colocar um valor seguro (como uma senha) diretamente em seu arquivo de modelo ou parâmetro, você pode recuperar o valor de uma [Azure Key Vault](../key-vault/key-vault-whatis.md) durante a implantação. Você recupera o valor fazendo referência ao cofre de chaves e ao segredo no arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. O Cofre de chaves pode existir em uma assinatura diferente que o grupo de recursos que você está implantando.

## <a name="deploy-key-vaults-and-secrets"></a>Implantar Key Vaults e segredos

Para acessar um cofre de chaves durante a implantação de modelo, defina `enabledForTemplateDeployment` no cofre de chaves para `true`.

Os exemplos de CLI do Azure e o Azure PowerShell a seguir mostram como criar o Cofre de chaves e adicionar um segredo.

```azurecli
az group create --name $resourceGroupName --location $location
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $keyVaultName --name "ExamplePassword" --value "hVFkk965BuUv"
```

```azurepowershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName $keyVaultName -Name 'ExamplePassword' -SecretValue $secretvalue
```

Como o proprietário do Cofre de chaves, você automaticamente tem acesso à criação de segredos. Se o usuário trabalhar com os segredos não for o proprietário do Cofre de chaves, conceda acesso com:

```azurecli
az keyvault set-policy \
  --upn $userPrincipalName \
  --name $keyVaultName \
  --secret-permissions set delete get list
```

```azurepowershell
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName $keyVaultName `
  -UserPrincipalName $userPrincipalName `
  -PermissionsToSecrets set,delete,get,list
```

Para obter mais informações sobre como criar cofres de chaves e segredos de adição, consulte:

- [Definir e recuperar um segredo usando a CLI](../key-vault/quick-create-cli.md)
- [Definir e recuperar um segredo usando o PowerShell](../key-vault/quick-create-powershell.md)
- [Definir e recuperar um segredo usando o portal](../key-vault/quick-create-portal.md)
- [Definir e recuperar um segredo usando o .NET](../key-vault/quick-create-net.md)
- [Definir e recuperar um segredo usando o Node.js](../key-vault/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Permitir acesso aos segredos

O usuário que implanta o modelo deve ter o `Microsoft.KeyVault/vaults/deploy/action` permissão para o escopo do grupo de recursos e Cofre de chaves. Ambas as funções [Proprietário](../role-based-access-control/built-in-roles.md#owner) e [Colaborador](../role-based-access-control/built-in-roles.md#contributor) concedem esse acesso. Se você criou o Cofre de chaves, você é o proprietário para que você tenha a permissão.

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
    Substitua "00000000-0000-0000-0000-000000000000" com a ID da assinatura.

2. Crie a nova função usando o arquivo JSON:

    ```azurecli
    az role definition create --role-definition "<PathToRoleFile>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee $userPrincipalName \
      --resource-group $resourceGroupName
    ```

    ```azurepowershell
    New-AzRoleDefinition -InputFile "<PathToRoleFile>" 
    New-AzRoleAssignment `
      -ResourceGroupName $resourceGroupName `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName $userPrincipalName
    ```

    Os exemplos de atribuir a função personalizada para o usuário no nível do grupo de recursos.  

Ao usar um Key Vault com o modelo para um [Aplicativo Gerenciado](../managed-applications/overview.md), você precisa conceder acesso à entidade de serviço **Provedor de Recursos do Dispositivo**. Para obter mais informações, confira [Segredo do Azure Key Vault durante a implantação de Aplicativos Gerenciados do Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Fazer referência segredos com ID estática

Com esta abordagem, você faz referência ao Key Vault no arquivo de parâmetro, não no modelo. A imagem a seguir mostra como o arquivo de parâmetro faz referência ao segredo e passa o valor para o modelo.

![Diagrama de ID estática de integração do Key Vault do Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Tutorial: Integrar o Azure Key Vault à implantação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md) usa este método.

O modelo a seguir implanta um SQL server que inclui uma senha de administrador. O parâmetro de senha é definido como uma cadeia de caracteres segura. Porém, o modelo não especifica de onde vem esse valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
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

No arquivo de parâmetro a seguir, o segredo do Cofre de chaves já deve existir e você fornecer um valor estático para sua ID de recurso.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Para a CLI do Azure, use:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Para o PowerShell, use:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Fazer referência a segredos com ID dinâmica

A seção anterior mostrou como passar uma ID do recurso estático para o segredo do cofre de chaves do parâmetro. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Ou, você pode passar valores de parâmetros para o modelo em vez de criar um parâmetro de referência no arquivo de parâmetros. Em ambos os casos, é possível gerar dinamicamente a ID do recurso para um segredo do cofre de chaves usando um modelo vinculado.

Não é possível gerar dinamicamente a ID do recurso no arquivo de parâmetros porque as expressões de modelo não são permitidas no arquivo de parâmetros. 

No modelo pai, adicione o modelo vinculado e passe um parâmetro que contém a ID de recurso gerada dinamicamente. A imagem a seguir mostra como um parâmetro no modelo vinculado faz referência ao segredo.

![ID dinâmica](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

O [seguinte modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) cria dinamicamente a ID do cofre de chaves e a passa como um parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
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
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Implante o modelo anterior e forneça valores para os parâmetros. É possível usar o modelo de exemplo do GitHub, mas é necessário fornecer os valores de parâmetro para o seu ambiente.

Para a CLI do Azure, use:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Para o PowerShell, use:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre os cofres de chaves, consulte [O que é o Cofre de Chaves do Azure?](../key-vault/key-vault-overview.md).
- Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
