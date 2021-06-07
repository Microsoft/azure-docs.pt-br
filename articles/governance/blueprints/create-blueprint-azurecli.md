---
title: 'Início Rápido: criar um blueprint com a CLI do Azure'
description: Neste início rápido, você usa o Azure Blueprints para criar, definir e implantar artefatos usando a CLI do Azure.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: fbe5c12f1c94d4b59dbdc2a97b6a4cb9af5a2328
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563660"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Início Rápido: definir e atribuir um Blueprint do Azure com a CLI do Azure

Aprender a criar e atribuir blueprints permite definir padrões comuns para desenvolver configurações reutilizáveis e que podem ser implantadas rapidamente com base em modelos do ARM (modelos do Azure Resource Manager), políticas, segurança e muito mais. Neste tutorial, você aprenderá a usar o Blueprint do Azure para executar algumas das tarefas comuns relacionadas à criação, publicação e atribuição de um blueprint dentro de sua organização, como:

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.
- Se você ainda não usou o Azure Blueprints, registre o provedor de recursos por meio de CLI do Azure com `az provider register --namespace Microsoft.Blueprint`.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Adicionar a extensão de Blueprint

Para permitir que a CLI do Azure gerencie definições e atribuições de blueprint, a extensão deve ser adicionada.
Essa extensão funcionará sempre que a CLI do Azure puder ser usada, incluindo no [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (tanto autônomo quanto dentro do portal), na [imagem do Docker da CLI do Azure](https://hub.docker.com/_/microsoft-azure-cli) ou instalada localmente.

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.0.76**). Se ainda não estiver instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows).

1. Em seu ambiente da CLI do Azure preferido, importe-a com o seguinte comando:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Verifique se a extensão foi instalada e se é a versão esperada (**0.1.0** ou posterior):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Criar um plano gráfico

A primeira etapa na definição de um modelo padrão para conformidade é compor um blueprint a partir dos recursos disponíveis. Vamos criar um blueprint chamado 'MyBlueprint' para configurar as atribuições de função e política para a assinatura. Em seguida, vamos adicionar um grupo de recursos, um modelo do ARM e uma atribuição de função ao grupo de recursos.

> [!NOTE]
> Ao usar a CLI do Azure, o objeto _blueprint_ será criado primeiro. Para cada _artefato_ com parâmetros a ser adicionado, os parâmetros precisam ser definidos com antecedência no _blueprint_ inicial.

1. Crie o objeto _blueprint_ original. O parâmetro **parameters** usa um arquivo JSON que inclui todos os parâmetros do nível do blueprint. Os parâmetros são definidos durante a atribuição e usados pelos artefatos adicionados nas etapas posteriores.

   - Arquivo JSON – blueprintparms.json

     ```json
     {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_GRS",
                "Standard_ZRS",
                "Premium_LRS"
            ],
            "metadata": {
                "displayName": "storage account type.",
                "description": null
            }
        },
        "tagName": {
            "type": "string",
            "metadata": {
                "displayName": "The name of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "tagValue": {
            "type": "string",
            "metadata": {
                "displayName": "The value of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "contributors": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Comando da CLI do Azure

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Use o nome de arquivo _blueprint.json_ ao importar suas definições de blueprint.
     > Esse nome de arquivo é usado ao chamar a [importação de blueprint az](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import).

     Por padrão, o objeto blueprint é criado na assinatura padrão. Para especificar o grupo de gerenciamento, use o parâmetro **managementgroup**. Para especificar a assinatura, use o parâmetro **subscription**.

1. Adicione o grupo de recursos dos artefatos de armazenamento à definição.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Adicione a atribuição de função na assinatura. No exemplo a seguir, as identidades de entidade de segurança concedidas à função especificada são configuradas para um parâmetro que é definido durante a atribuição do blueprint. Este exemplo usa a função interna _Colaborador_ com um GUID igual a `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Adicione a atribuição de política na assinatura. Este exemplo usa a política interna _Aplicar a marca e seu valor padrão a grupos de recursos_ com um GUID igual a `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Arquivo JSON – artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Comando da CLI do Azure

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

     > [!NOTE]
     > Ao usar `az blueprint` em um Mac, substitua `\` por `/` para os valores de parâmetro que incluem o caminho. Nesse caso, o valor de **parameters** passa a ser `artifacts/policyTags.json`.

1. Adicione outra atribuição de política para a marca Armazenamento (reutilizando o parâmetro _storageAccountType_) na assinatura. Este artefato de atribuição de política adicional demonstra que um parâmetro definido no blueprint pode ser usado por mais de um artefato. No exemplo, o **storageAccountType** é usado para definir uma marca no grupo de recursos. Esse valor fornece informações sobre a conta de armazenamento que será criada na próxima etapa. Este exemplo usa a política interna _Aplicar a marca e seu valor padrão a grupos de recursos_ com um GUID igual a `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - Arquivo JSON – artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Comando da CLI do Azure

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

     > [!NOTE]
     > Ao usar `az blueprint` em um Mac, substitua `\` por `/` para os valores de parâmetro que incluem o caminho. Nesse caso, o valor de **parameters** passa a ser `artifacts/policyStorageTags.json`.

1. Adicione o modelo sob o grupo de recursos. O parâmetro **template** de um modelo do ARM inclui os componentes JSON normais do modelo. O modelo também reutiliza os parâmetros de blueprint **storageAccountType**, **tagName** e **tagValue** transmitindo-os para o modelo. Os parâmetros do blueprint são disponibilizados ao modelo usando o parâmetro **parameters** e, dentro do JSON do modelo, o par chave-valor é usado para injetar o valor. Os nomes dos parâmetros de blueprint e de modelo podem ser iguais.

   - Arquivo de modelo do ARM JSON – artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Arquivo de parâmetro de modelo de ARM JSON – artifacts\templateStorageParams.json

     ```json
     {
        "storageAccountTypeFromBP": {
           "value": "[parameters('storageAccountType')]"
        },
        "tagNameFromBP": {
           "value": "[parameters('tagName')]"
        },
        "tagValueFromBP": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Comando da CLI do Azure

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

     > [!NOTE]
     > Ao usar `az blueprint` em um Mac, substitua `\` por `/` para os valores de parâmetro que incluem o caminho. Nesse caso, o valor de **template** passa a ser `artifacts/templateStorage.json` e o valor de **parameters** passa a ser `artifacts/templateStorageParams.json`.

1. Adicione atribuição de função sob o grupo de recursos. Semelhante à entrada de atribuição de função anterior, o exemplo abaixo usa o identificador da definição para a função **Proprietário** e fornece a ela um parâmetro diferente do blueprint. Este exemplo usa a função interna _Proprietário_ com um GUID igual a `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Publicar um modelo

Agora que os artefatos foram adicionados ao blueprint, é hora de publicá-lo. A publicação disponibiliza a atribuição a uma assinatura.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

O valor de `{BlueprintVersion}` é uma cadeia de caracteres de letras, números e hifens (sem espaços ou outros caracteres especiais) com um comprimento máximo de 20 caracteres. Use algo exclusivo e informativo, como **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Atribuir um modelo

Depois que um blueprint é publicado usando a CLI do Azure, ele pode ser atribuído a uma assinatura. Atribua o blueprint que você criou a uma das assinaturas em sua hierarquia do grupo de gerenciamento. Se o blueprint for salvo em uma assinatura, ele só poderá ser atribuído a essa assinatura. O parâmetro **blueprint-name** especifica o blueprint a ser atribuído. Para fornecer os parâmetros nome, localização, identidade, bloqueio e blueprint, use os parâmetros correspondentes da CLI do Azure no comando `az blueprint assignment create` ou forneça-os no arquivo de **parâmetros** do JSON.

1. Execute a implantação do blueprint atribuindo-o uma assinatura. Como os parâmetros **contributors** e **owners** exigem que uma matriz de objectIds das entidades de segurança seja concedida à atribuição de função, use a [API do Graph do Azure Active Directory](/graph/migrate-azure-ad-graph-planning-checklist) para coletar as objectIds a serem usadas nos **parâmetros** de seus usuários, grupos ou entidades de serviço.

   - Arquivo JSON – blueprintAssignment.json

     ```json
     {
        "storageAccountType": {
            "value": "Standard_GRS"
        },
        "tagName": {
            "value": "CostCenter"
        },
        "tagValue": {
            "value": "ContosoIT"
        },
        "contributors": {
            "value": [
                "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                "38833b56-194d-420b-90ce-cff578296714"
            ]
        },
        "owners": {
            "value": [
                "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                "316deb5f-7187-4512-9dd4-21e7798b0ef9"
            ]
        }
     }
     ```

   - Comando da CLI do Azure

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Identidade gerenciada atribuída pelo usuário

     Uma atribuição de blueprint também pode usar uma [identidade gerenciada atribuída por usuário](../../active-directory/managed-identities-azure-resources/overview.md).
     Nesse caso, o parâmetro **identity-type** é definido como _UserAssigned_ e o parâmetro **user-assigned-identities** especifica a identidade. Substitua `{userIdentity}` pelo nome da identidade gerenciada atribuída por usuário.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     A **identidade gerenciada atribuída por usuário** pode estar em qualquer assinatura e grupo de recursos aos quais o usuário que atribui o blueprint tem acesso.

     > [!IMPORTANT]
     > O Azure Blueprints não gerencia a identidade gerenciada atribuída por usuário. Os usuários são responsáveis por atribuir funções e permissões suficientes ou a atribuição de blueprint falhará.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="unassign-a-blueprint"></a>Cancelar a atribuição de um blueprint

Você pode remover um blueprint de uma assinatura. A remoção geralmente é feita quando os recursos de artefato não são mais necessários. Quando um blueprint é removido, os artefatos atribuídos como parte desse blueprint são deixados para trás. Para remover uma atribuição de blueprint, use o comando `az blueprint assignment delete`:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou, atribuiu e removeu um blueprint com a CLI do Azure. Para saber mais sobre o Azure Blueprints, prossiga para o artigo de ciclo de vida do blueprint.

> [!div class="nextstepaction"]
> [Saiba mais sobre o ciclo de vida do blueprint](./concepts/lifecycle.md)