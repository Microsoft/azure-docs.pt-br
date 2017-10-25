---
title: "Automatizar a implantação de recursos para um aplicativo de funções do Azure Functions | Microsoft Docs"
description: "Aprenda a criar um modelo do Azure Resource Manager que implanta o aplicativo de funções."
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, arquitetura sem servidores, infraestrutura como código, azure resource manager"
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2fee04ee11210b9081fc7edb64da1f0210f1bd06
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implantação de recursos para seu aplicativo de funções do Azure Functions

Você pode usar um modelo do Azure Resource Manager para implantar um aplicativo de funções. Este artigo descreve os recursos e os parâmetros necessários para fazer isso. Talvez seja necessário implantar recursos adicionais, dependendo dos [gatilhos e associações](functions-triggers-bindings.md) em seu aplicativo de funções.

Para saber mais sobre a criação de modelos, consulte [Criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para modelos de exemplo, consulte:
- [Aplicativo de funções no Plano de Consumo]
- [Aplicativo de funções no Plano do Serviço de Aplicativo do Azure]

## <a name="required-resources"></a>Recursos necessários

Um aplicativo de funções requer estes recursos:

* Uma conta de [Armazenamento do Azure](../storage/index.yml)
* Um plano de hospedagem (Plano de Consumo ou Plano do Serviço de Aplicativo)
* Um aplicativo de funções 

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para um aplicativo de funções. Você precisa de uma conta de finalidade geral que dá suporte a blobs, tabelas, consultas e arquivos. Para saber mais, confira [Requisitos da conta de armazenamento do Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Além disso, as propriedades `AzureWebJobsStorage` e `AzureWebJobsDashboard` devem ser especificadas como configurações de aplicativo na configuração do site. O tempo de execução do Azure Functions usa a cadeia de conexão `AzureWebJobsStorage` para criar filas internas. A cadeia de conexão `AzureWebJobsDashboard` é usada para fazer logon no armazenamento de Tabelas do Azure e alimentar a guia **Monitorar** no portal.

Essas propriedades são especificadas na coleção `appSettings` no objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Plano de hospedagem

A definição do plano de hospedagem varia, dependendo se você usa um Plano do Serviço de Aplicativo ou de Consumo. Consulte [Implantar um aplicativo de funções no Plano de Consumo](#consumption) e [Implantar um aplicativo de funções no Plano do Serviço de Aplicativo](#app-service-plan).

### <a name="function-app"></a>Aplicativo de funções

O recurso do aplicativo de funções é definido usando um recurso do tipo **Microsoft.Web/Site** e variante **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Implantar um aplicativo de funções no Plano de Consumo

Você pode executar um aplicativo de funções em dois modos diferentes: o Plano de Consumo e o Plano do Serviço de Aplicativo. O Plano de Consumo automaticamente aloca potência de computação quando seu código está em execução, escala horizontalmente conforme a necessidade para tratar do carregamento e reduz verticalmente quando o código não está em execução. Assim, você não precisa pagar por VMs ociosas e não precisa reservar a capacidade com antecedência. Para saber mais sobre os planos de hospedagem, consulte [Consumo do Azure Functions e Planos de Serviço de Aplicativo](functions-scale.md).

Para um exemplo de modelo do Azure Resource Manager, consulte [Aplicativo de funções no Plano de Consumo].

### <a name="create-a-consumption-plan"></a>Criar um Plano de Consumo

Um Plano de Consumo é um tipo especial de recurso "serverfarm". Você o especifica usando o valor `Dynamic` para as propriedades `computeMode` e `sku`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

Além disso, um Plano de Consumo requer duas configurações adicionais na configuração do site: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Essas propriedades configuram a conta de armazenamento e o caminho do arquivo em que o código e as configurações do aplicativo de funções estão armazenados.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Implantar um aplicativo de funções no Plano do Serviço de Aplicativo

No Plano do Serviço de Aplicativo, seus aplicativos de funções são executados em VMs dedicadas, em SKUs Basic, Standard e Premium, assim como os aplicativos Web. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Para um exemplo de modelo do Azure Resource Manager, consulte [Aplicativo de funções no Plano do Serviço de Aplicativo do Azure].

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Criar um aplicativo de funções 

Depois de selecionar uma opção de dimensionamento, crie um aplicativo de funções. O aplicativo é o contêiner que mantém todas as suas funções.

Um aplicativo de funções tem muitos recursos filho que podem ser usados na sua implantação, incluindo configurações do aplicativo e opções de controle do código-fonte. Você também pode optar por remover o recurso filho **sourcecontrols** e usar uma outra [opção de implantação](functions-continuous-deployment.md).

> [!IMPORTANT]
> Para implantar seu aplicativo com êxito usando o Azure Resource Manager, é importante entender como os recursos são implantados no Azure. No exemplo a seguir, as configurações de nível superior são aplicadas usando **siteConfig**. É importante definir essas configurações em um nível superior porque transmitem informações para o mecanismo de implantação e de tempo de execução do Functions. Informações de nível superior são necessárias antes do recurso filho **sourcecontrols/web** ser aplicado. Embora seja possível definir essas configurações no nível de recurso filho **config/appSettings**, em alguns casos, o aplicativo de funções deve ser implantado *antes* de **config/appSettings** ser aplicado. Por exemplo, quando você está usado funções com [aplicativos lógicos](../logic-apps/index.md), as funções são uma dependência de outro recurso.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Este modelo usa o valor das configurações do aplicativo [Projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), que determina o diretório-base cujo mecanismo de implantação de funções (Kudu) procura o código implantável. Em nosso repositório, nossas funções estão em uma subpasta da pasta **src**. Assim, no exemplo anterior, definimos o valor de configurações do aplicativo para `src`. Se as funções estão na raiz do seu repositório, ou se não está implantando do controle de origem, você pode remover esse valor de configurações do aplicativo.

## <a name="deploy-your-template"></a>Implantar o modelo

Você pode usar qualquer uma das seguintes maneiras para implantar o modelo:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão Implantar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada de URL](https://www.bing.com/search?q=url+encode) do caminho bruto de seu `azuredeploy.json` arquivo no GitHub.

Este é um exemplo que usa markdown:

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Este é um exemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como desenvolver e configurar o Azure Functions.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Como definir configurações do aplicativo de funções do Azure](functions-how-to-use-azure-function-app-settings.md)
* [Como criar a sua primeira função do Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplicativo de funções no Plano de Consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplicativo de funções no Plano do Serviço de Aplicativo do Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

