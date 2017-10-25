---
title: Criar o primeiro modelo do Azure Resource Manager | Microsoft Docs
description: "Um guia passo a passo para criar seu primeiro modelo do Azure Resource Manager. Ele mostra como usar a referência de modelo para uma conta de armazenamento para criar o modelo."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/02/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 7d20469aaf2dfdd7a5f3650983b59152de837837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Criar e implantar seu primeiro modelo do Azure Resource Manager
Este tópico explica as etapas de criação de seu primeiro modelo do Azure Resource Manager. Os modelos do Resource Manager são arquivos JSON que definem os recursos necessários para implantar sua solução. Para entender os conceitos associados à implantação e ao gerenciamento de soluções do Azure, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md). Se você já tiver recursos e quiser obter um modelo para esses recursos, consulte [Exportar um modelo do Azure Resource Manager de recursos existentes](resource-manager-export-template.md).

Para criar e revisar os modelos, você precisa de um editor de JSON. [Visual Studio Code](https://code.visualstudio.com/) é um editor de código entre plataformas aberto e leve. Recomendamos usar o Visual Studio Code para criar modelos do Resource Manager. Este artigo pressupõe que você está usando o VS Code. Se você tiver outro editor de JSON (como o Visual Studio), poderá usá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio Code. Se for necessário, instale-o pelo site [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-template"></a>Criar modelo

Vamos começar com um modelo simples que implanta uma conta de armazenamento na sua assinatura.

1. Selecione **Arquivo** > **Novo Arquivo**. 

   ![Novo arquivo](./media/resource-manager-create-first-template/new-file.png)

2. Copie e cole a seguinte sintaxe JSON em seu arquivo:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Os nomes de conta de armazenamento têm várias restrições que os tornam difíceis de definir. O nome deve ter entre três e 24 caracteres de comprimento e usar somente números e letras minúsculas, além de ser exclusivo. O modelo anterior usa a função [uniqueString](resource-group-template-functions-string.md#uniquestring) para gerar um valor de hash. Para dar mais significado a esse valor de hash, ele adiciona o prefixo *storage*. 

3. Salve esse arquivo como **azuredeploy.json** em uma pasta local.

   ![Salvar modelo](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Implantar modelo

Você está pronto para implantar o modelo. Use o PowerShell ou a CLI do Azure para criar um grupo de recursos. Em seguida, implante uma conta de armazenamento para esse grupo de recursos.

* No caso do PowerShell, use os seguintes comandos na pasta que contém o modelo:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* No caso de uma instalação local da CLI do Azure, use os seguintes comandos na pasta que contém o modelo:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Quando a implantação é concluída, sua conta de armazenamento passa a existir no grupo de recursos.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Para a CLI do Azure, use os seguintes comandos:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
```

Atualmente, o PowerShell está disponível no Cloud Shell como uma versão prévia. Para o PowerShell, use os seguintes comandos:

```powershell
New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile $home\CloudDrive\templates\azuredeploy.json
```

Quando a implantação é concluída, sua conta de armazenamento passa a existir no grupo de recursos.

## <a name="customize-the-template"></a>Personalizar o modelo

O modelo funciona bem, mas não é flexível. Ele sempre implanta um armazenamento com redundância local no Centro-Sul dos EUA. O nome é sempre *armazenamento*, seguido de um valor de hash. Para habilitar o uso do modelo em cenários diferentes, adicione parâmetros a ele.

O exemplo a seguir mostra a seção de parâmetros com dois parâmetros. O primeiro parâmetro, `storageSKU`, permite que você especifique o tipo de redundância. Ele restringe os valores que você pode transmitir a valores que são válidos para uma conta de armazenamento. Ela também especifica um valor padrão. O segundo parâmetro, `storageNamePrefix`, está definido para permitir um máximo de 11 caracteres. Ele especifica um valor padrão.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

Na seção de variáveis, adicione uma variável chamada `storageName`. Ela combina o valor de prefixo dos parâmetros e um valor de hash a partir da função [uniqueString](resource-group-template-functions-string.md#uniquestring). Ela usa a função [toLower](resource-group-template-functions-string.md#tolower) para converter todos os caracteres em minúsculas.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Para usar esses novos valores na sua conta de armazenamento, altere a definição do recurso:

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Observe que o nome da conta de armazenamento agora está definido como a variável que você adicionou. O nome da SKU é definido como o valor do parâmetro. O local é definido como o mesmo do grupo de recursos.

Salve o arquivo. 

O modelo agora se parece com:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Reimplantar o modelo

Reimplante o modelo com valores diferentes.

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Para a CLI do Azure, use:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Para o Cloud Shell, carregue o modelo alterado no compartilhamento de arquivos. Substitua o arquivo existente. Em seguida, use o seguinte comando:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>Usar o preenchimento automático

Até agora, seu trabalho no modelo consistiu somente em copiar e colar JSON deste artigo. No entanto, ao desenvolver seus próprios modelos, você deve localizar e especificar as propriedades e os valores que estão disponíveis para o tipo de recurso. O VS Code lê o esquema do tipo de recurso e sugere propriedades e valores. Para ver o recurso de preenchimento automático, acesse o elemento propriedades do modelo e adicione uma nova linha. Digite um sinal de aspas e observe que o VS Code sugere imediatamente nomes que estão disponíveis dentro do elemento propriedades.

![Mostrar propriedades disponíveis](./media/resource-manager-create-first-template/show-properties.png)

Selecione **criptografia**. Digite dois-pontos (:) e o VS Code sugere adicionando um novo objeto.

![Adicionar objeto](./media/resource-manager-create-first-template/add-object.png)

Pressione Tab ou Enter para adicionar o objeto.

Novamente, digite um sinal de aspas e veja se o VS Code sugere agora propriedades que estão disponíveis para criptografia.

![Mostrar propriedades de criptografia](./media/resource-manager-create-first-template/show-encryption-properties.png)

Selecione **serviços** e continue a adicionar valores com base em extensões do VS Code até que você tenha:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

Habilitado a criptografia de blob na conta de armazenamento. No entanto, o VS Code identificou um problema. Observe que a criptografia tem um aviso.

![Aviso de criptografia](./media/resource-manager-create-first-template/encryption-warning.png)

Para ver o aviso, passe o mouse sobre a linha verde.

![Propriedades ausentes](./media/resource-manager-create-first-template/missing-property.png)

Você verá que o elemento criptografia requer uma propriedade keySource. Adicione uma vírgula após o objeto serviços e adicione a propriedade keySource. O VS Code sugere **"Microsoft.Storage"** como um valor válido. Quando terminar, o elemento de propriedades será:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

O modelo final é:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>Implantar armazenamento criptografado

Novamente, implante o modelo e forneça um novo nome de conta de armazenamento.

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Para a CLI do Azure, use:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

Para o Cloud Shell, carregue o modelo alterado no compartilhamento de arquivos. Substitua o arquivo existente. Em seguida, use o seguinte comando:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos.

Para o PowerShell, use:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Para a CLI do Azure, use:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Próximas etapas
* Para obter mais assistência para o desenvolvimento de modelos, você pode instalar uma extensão do VS Code. Para saber mais, confira [Usar a extensão do Visual Studio Code para criar modelos do Azure Resource Manager](resource-manager-vscode-extension.md)
* Para saber mais sobre a estrutura de um modelo, confira [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md).
* Para saber mais sobre as propriedades de uma conta de armazenamento, confira [Referência do modelo de contas de armazenamento](/azure/templates/microsoft.storage/storageaccounts).
* Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
