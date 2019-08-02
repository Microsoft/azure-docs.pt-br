---
title: Vincular modelos para implantação do Azure | Microsoft Docs
description: Descreve como usar modelos vinculados em um modelo do Gerenciador de Recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especificar um arquivo de parâmetro e URLs criadas dinamicamente.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: c79429d1a39e975c6bcc7fce191846a6205f9a86
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311699"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Usando modelos vinculados e aninhados ao implantar os recursos do Azure

Para implantar sua solução, você pode usar um único modelo, ou um modelo principal com muitos modelos relacionados. O modelo relacionado pode ser um arquivo separado que é vinculado a partir do modelo principal ou um modelo que está aninhado dentro do modelo principal.

Para pequenas e médias soluções, um único modelo é mais fácil de entender e manter. Você pode ver todos os recursos e valores em um único arquivo. Para cenários avançados, modelos vinculados permitem dividir a solução em componentes desejados e reutilizar modelos.

Ao usar modelos vinculados, você cria um modelo principal que recebe os valores de parâmetros durante a implantação. O modelo principal contém todos os modelos vinculados e passa valores para esses modelos, conforme necessário.

Para obter um tutorial, consulte [Tutorial: criar modelos vinculados do Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Para modelos vinculados ou aninhados, você só pode usar o modo de implantação [Incremental](deployment-modes.md).
>

## <a name="link-or-nest-a-template"></a>Vincular ou aninhar um modelo

Para vincular a outro modelo, adicione um recurso de **implantações** no seu modelo principal.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

As propriedades que você fornece para o recurso de implantação variam dependendo se você está vinculando a um modelo externo ou aninhando um modelo in-line no modelo principal.

### <a name="nested-template"></a>Modelo aninhado

Para aninhar o modelo no modelo principal, use a propriedade **modelo** e especifique a sintaxe do modelo.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Para modelos aninhados, você não pode usar parâmetros ou variáveis que são definidas no modelo aninhado. Você pode usar parâmetros e variáveis do modelo principal. No exemplo anterior, `[variables('storageName')]` recupera um valor de modelo principal, não o modelo aninhado. Essa restrição não se aplica a modelos externos.
>
> Para dois recursos definidos dentro de um modelo aninhado e um recurso depende do outro, o valor da dependência é simplesmente o nome do recurso dependente:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Você não pode usar `reference` a função na seção de saídas de um modelo aninhado para um recurso que você implantou no modelo aninhado. Para retornar os valores de um recurso implantado em um modelo aninhado, converta seu modelo aninhado em um modelo vinculado.

O modelo aninhado requer as [mesmas propriedades](resource-group-authoring-templates.md) como um modelo padrão.

### <a name="external-template-and-external-parameters"></a>Modelo externo e parâmetros externos

Para vincular a um arquivo de parâmetro e o modelo externo, use **templateLink** e **parametersLink**. Ao vincular a um modelo, o do Gerenciador de Recursos deve ser capaz de acessá-lo. Não é possível especificar um arquivo local ou um arquivo que esteja disponível apenas em sua rede local. Você só pode fornecer um valor de URI que inclua **http** ou **https**. Uma opção é colocar o modelo vinculado em uma conta de armazenamento e usar o URI do item.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
    }
    }
  }
]
```

Você não precisa fornecer a propriedade `contentVersion` para o modelo ou parâmetros. Se você não fornecer um valor de versão do conteúdo, a versão atual do modelo é implantada. Se você fornecer um valor para a versão do conteúdo, ele deve corresponder à versão do modelo vinculado; caso contrário, a implantação falhará com um erro.

### <a name="external-template-and-inline-parameters"></a>Modelo externo e parâmetros embutidos

Ou, você pode fornecer o parâmetro embutido. Você não pode usar os dois parâmetros inline e um link para um arquivo de parâmetros. A implementação falha com um erro quando `parametersLink` e `parameters` são especificados.

Para passar um valor do modelo principal para o modelo vinculado, use **parâmetros**.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-copy"></a>Usando a cópia

Para criar várias instâncias de um recurso com um modelo aninhado, adicione o elemento Copy no nível do recurso **Microsoft. Resources/** Implantations.

O modelo de exemplo a seguir mostra como usar Copy com um modelo aninhado.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy":{
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Usando variáveis para vincular modelos

Os exemplos anteriores mostraram valores codificados de URL para os vínculos de modelo. Essa abordagem pode funcionar para um modelo simples, mas não funciona bem quando ao trabalhar com um grande conjunto de modelos modulares. Em vez disso, você pode criar uma variável estática que armazena uma URL de base para o modelo principal e, em seguida, criar dinamicamente URLs para os modelos vinculados dessa URL de base. A vantagem dessa abordagem é mover ou bifurcar o modelo, pois você precisa alterar a variável estática no modelo principal. O modelo principal passa os URIs corretos em todo o modelo decomposto.

O exemplo a seguir mostra como usar uma URL base para criar duas URLs para modelos vinculados (**sharedTemplateUrl** e **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Você também pode usar [deployment()](resource-group-template-functions-deployment.md#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Essa abordagem será útil se o local do modelo é alterado ou para evitar embutir URLs no arquivo de modelo. A propriedade templateLink só será retornada ao vincular a um modelo remoto com uma URL. Se você estiver usando um modelo local, essa propriedade não estará disponível.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Obter valores de modelos vinculados

Para obter um valor de saída de um modelo vinculado, recupere o valor da propriedade com uma sintaxe semelhante a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Ao obter uma propriedade de saída de um modelo vinculado, o nome da propriedade não pode incluir um traço.

Os exemplos a seguir demonstram como fazer referência a um modelo vinculado e recuperar um valor de saída. O modelo vinculado retorna uma mensagem simples.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

O modelo principal implanta o modelo vinculado e obtém o valor retornado. Observe que ele faz referência aos recursos de implantação por nome, e ele usa o nome da propriedade retornada pelo modelo vinculado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Assim como outros tipos de recurso, você pode definir dependências entre o modelo vinculado e outros recursos. Portanto, quando outros recursos exigirem um valor de saída do modelo vinculado,certifique-se de que o modelo vinculado foi implantado antes deles. Ou, quando o modelo vinculado depender de outros recursos, certifique-se que outros recursos foram implantados antes do modelo vinculado.

O exemplo a seguir mostra um modelo que implanta um endereço IP público e retorna a ID do recurso:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Para usar o endereço IP público do modelo anterior ao implantar um balanceador de carga, vincule ao modelo e adicione uma dependência no recurso de implantação. O endereço IP público no balanceador de carga é definido como o valor de saída do modelo vinculado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Modelos vinculados e aninhados no histórico de implantação

O Gerenciador de Recursos trata cada modelo como uma implantação separada no histórico de implantações. Portanto, o modelo principal com três modelos vinculados ou aninhados aparece no histórico de implantação, como:

![Histórico de implantações](./media/resource-group-linked-templates/deployment-history.png)

Você pode usar essas entradas separadas no histórico para recuperar valores de saída após a implantação. O modelo a seguir cria um endereço IP público e gera o endereço IP:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Os seguintes links de modelo para o modelo anterior. Ele cria três endereços IP públicos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

Após a implantação, você pode recuperar os valores de saída com o seguinte script do PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Ou, script da CLI do Azure em um shell Bash:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Proteger um modelo externo

Embora o modelo vinculado precise estar disponível externamente, ele não precisa estar disponível para o público. Você pode adicionar o modelo a uma conta de armazenamento privada que pode ser acessada somente pelo proprietário da conta de armazenamento. Em seguida, você cria um token SAS (assinatura de acesso compartilhado) para permitir o acesso durante a implantação. Você pode adicionar esse token SAS ao URI para o modelo vinculado. Embora o token seja transmitido como uma cadeia de caracteres segura, o URI do modelo vinculado, incluindo o token SAS, é registrado nas operações de implantação. Para limitar a exposição, defina uma expiração para o token.

O arquivo de parâmetro também pode ter o acesso limitado por meio de um token SAS.

O exemplo a seguir mostra como passar um token SAS ao vincular a um modelo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

No PowerShell, você obtém um token para o contêiner e implanta os modelos com os comandos a seguir. Observe que o parâmetro **containerSasToken** está definido no modelo. Não é um parâmetro no comando **New-AzResourceGroupDeployment**.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Para a CLI do Azure em um shell Bash, você obtém um token para o contêiner e implanta os modelos com o seguinte código:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Modelos de exemplo

Os exemplos a seguir mostram os usos comuns dos modelos vinculados.

|Modelo principal  |Modelo vinculado |DESCRIÇÃO  |
|---------|---------| ---------|
|[Olá mundo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[modelo vinculado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Retorna a cadeia de caracteres do modelo vinculado. |
|[Azure Load Balancer com o endereço IP público](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[modelo vinculado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Retorna o endereço IP público do modelo vinculado e define esse valor no balanceador de carga. |
|[Vários endereços IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [modelo vinculado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Cria vários endereços IP públicos no modelo vinculado.  |

## <a name="next-steps"></a>Próximas etapas

* Para percorrer um tutorial, consulte [Tutorial: criar modelos vinculados do Azure Resource Manager](./resource-manager-tutorial-create-linked-templates.md).
* Para saber mais sobre como definir a ordem de implantação para seus recursos, consulte [Definição de dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md).
* Para saber como definir um recurso, mas criando várias instâncias dele, consulte [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md).
* Para ver as etapas para configurar um modelo em uma conta de armazenamento e gerar um token SAS, consulte [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md).
