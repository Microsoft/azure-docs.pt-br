---
title: Usar Key Vault ao implantar o aplicativo gerenciado
description: Mostra como usar segredos de acesso no Azure Key Vault ao implantar Aplicativos Gerenciados
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81458276"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Acessar segredo do Azure Key Vault durante a implantação de Aplicativos Gerenciados do Azure

Quando você precisa passar um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível recuperar o valor de um [Azure Key Vault](../../key-vault/general/overview.md). Para acessar o Key Vault ao implantar os Aplicativos Gerenciados, você precisa permitir acesso à entidade de serviço **Provedor de Recursos do Dispositivo**. O serviço de aplicativos gerenciados usa essa identidade para executar operações. Durante a implantação, a entidade de serviço deve ser capaz de acessar o Key Vault para recuperar um valor dele com êxito.

Este artigo descreve como configurar o Key Vault para trabalhar com os Aplicativos Gerenciados.

## <a name="enable-template-deployment"></a>Habilitar a implantação de modelo

1. No portal, selecione o Key Vault.

1. Selecione **Políticas de acesso**.   

   ![Selecionar as políticas de acesso](./media/key-vault-access/select-access-policies.png)

1. Selecione **Clique para exibir as políticas de acesso avançado**.

   ![Mostrar políticas de acesso avançado](./media/key-vault-access/advanced.png)

1. Selecione **Habilitar acesso ao Azure Resource Manager para implantação de modelos**. Em seguida, selecione **Salvar**.

   ![Habilitar a implantação de modelo](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Adicionar o serviço como colaborador

1. Selecione **IAM (Controle de acesso)** .

   ![Selecionar o controle de acesso](./media/key-vault-access/access-control.png)

1. Selecione **Adicionar atribuição de função**.

   ![Selecionar adicionar](./media/key-vault-access/add-access-control.png)

1. Selecione **Colaborador** para a função. Pesquise por **Provedor de Recursos do Dispositivo** e selecione-o entre as opções disponíveis.

   ![Pesquisar por provedor](./media/key-vault-access/search-provider.png)

1. Clique em **Salvar**.

## <a name="reference-key-vault-secret"></a>Referenciar segredo do Key Vault

Para passar um segredo de um Key Vault para um modelo em seu aplicativo gerenciado, você deve usar um [modelo vinculado ou aninhado](../templates/linked-templates.md) e referenciar o Key Vault nos parâmetros para o modelo vinculado ou aninhado. Forneça a ID de recurso do Key Vault e o nome do segredo.

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
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Você configurou seu Key Vault para ser acessível durante a implantação de um Aplicativo Gerenciado.

* Para obter informações sobre como passar um valor de um Key Vault como um parâmetro de modelo, veja [Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](../templates/key-vault-parameter.md).
* Para obter exemplos de aplicativo gerenciado, veja [Projetos de exemplo para aplicativos gerenciados do Azure](sample-projects.md).
* Para saber como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](create-uidefinition-overview.md).
