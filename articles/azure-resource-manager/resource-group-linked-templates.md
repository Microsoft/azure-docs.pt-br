---
title: "Vincular modelos para implantação do Azure | Microsoft Docs"
description: "Descreve como usar modelos vinculados em um modelo do Gerenciador de Recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especificar um arquivo de parâmetro e URLs criadas dinamicamente."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Usando modelos vinculados ao implantar os recursos do Azure
Em um modelo do Azure Resource Manager, você pode vincular a outro modelo, o que permite decompor sua implantação em um conjunto de modelos orientados para fins específicos. Assim como com a decomposição de um aplicativo em várias classes de código, a decomposição oferece benefícios em termos de teste, reutilização e legibilidade.  

Você pode passar parâmetros de um modelo principal a um modelo vinculado e esses parâmetros podem corresponder diretamente aos parâmetros ou variáveis expostos pelo modelo de chamada. O modelo vinculado também pode passar uma variável de saída de volta para o modelo de origem, permitindo uma troca de dados bidirecional entre os modelos.

## <a name="linking-to-a-template"></a>Vinculando a um modelo
Para criar um vínculo entre dois modelos, adicione um recurso de implantação no modelo principal que aponta para o modelo vinculado. Defina a propriedade **templateLink** como o URI do modelo vinculado. Você pode fornecer valores de parâmetro para o modelo vinculado diretamente em seu modelo ou em um arquivo de parâmetro. O exemplo a seguir usa a propriedade **parameters** para especificar um valor de parâmetro diretamente.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Assim como outros tipos de recurso, você pode definir dependências entre o modelo vinculado e outros recursos. Portanto, quando outros recursos exigirem um valor de saída do modelo vinculado, você poderá ter certeza de que o modelo vinculado foi implantado antes deles. Ou, quando o modelo vinculado depender de outros recursos, você poderá verificar se outros recursos foram implantados antes do modelo vinculado. Você pode recuperar um valor de um modelo vinculado com a seguinte sintaxe:

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

O serviço do Resource Manager deve ser capaz de acessar o modelo vinculado. Você não pode especificar um arquivo local ou um arquivo que esteja disponível apenas em sua rede local para o modelo vinculado. Você só pode fornecer um valor de URI que inclua **http** ou **https**. Uma opção é colocar o modelo vinculado em uma conta de armazenamento e usar o URI do item, conforme mostra o exemplo a seguir:

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Embora o modelo vinculado precise estar disponível externamente, ele não precisa estar disponível para o público. Você pode adicionar o modelo a uma conta de armazenamento privada que pode ser acessada somente pelo proprietário da conta de armazenamento. Em seguida, você cria um token SAS (assinatura de acesso compartilhado) para permitir o acesso durante a implantação. Você pode adicionar esse token SAS ao URI para o modelo vinculado. Para ver as etapas para configurar um modelo em uma conta de armazenamento e gerar um token SAS, consulte [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md). 

O exemplo a seguir mostra um modelo pai vinculado a outro modelo. O modelo vinculado é acessado com um token SAS que é transmitido como um parâmetro.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Embora o token seja transmitido como uma cadeia de caracteres segura, o URI do modelo vinculado, incluindo o token SAS, é registrado nas operações de implantação. Para limitar a exposição, defina uma expiração para o token.

O Resource Manager trata cada modelo vinculado como uma implantação separada. No histórico de implantações do grupo de recursos, você verá implantações separadas para o modelo pai e os modelos aninhados.

![histórico de implantações](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Vinculando a um arquivo de parâmetro
O exemplo a seguir usa a propriedade **parametersLink** para vincular a um arquivo de parâmetro.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

O valor do URI para o arquivo de parâmetro vinculado não pode ser um arquivo local e deve incluir **http** ou **https**. O arquivo de parâmetro também pode ter o acesso limitado por meio de um token SAS.

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

Você também pode usar [deployment()](resource-group-template-functions-deployment.md#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Essa abordagem será útil se o local do modelo é alterado (talvez devido a controle de versão) ou para evitar embutir URLs no arquivo de modelo. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Exemplo completo
Os exemplos de modelos a seguir mostram uma disposição simplificada de modelos vinculados para ilustrar vários conceitos neste artigo. Ela pressupõe que os modelos tenham sido adicionados ao mesmo contêiner em uma conta de armazenamento com acesso público desativado. O modelo vinculado transmite um valor de volta para o modelo principal na seção **outputs** .

O arquivo **parent.json** consiste em:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

O arquivo **helloworld.json** consiste em:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

No PowerShell, você obtém um token para o contêiner e implanta os modelos com:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Na CLI do Azure 2.0, você obtém um token para o contêiner e implanta os modelos com o código a seguir:

```azurecli
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

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como definir a ordem de implantação para seus recursos, confira [Definição de dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md)
* Para saber como definir um recurso, mas criando várias instâncias dele, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md)

