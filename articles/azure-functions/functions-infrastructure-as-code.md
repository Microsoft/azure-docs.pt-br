---
title: Automatizar a implantação de recursos do aplicativo de funções no Azure
description: Aprenda a criar um modelo do Azure Resource Manager que implanta o aplicativo de funções.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9df4c62a65fd133c6ea8dc84e33d7c7b02d94cbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99494032"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizar a implantação de recursos para seu aplicativo de funções do Azure Functions

Você pode usar um modelo do Azure Resource Manager para implantar um aplicativo de funções. Este artigo descreve os recursos e os parâmetros necessários para fazer isso. Talvez seja necessário implantar recursos adicionais, dependendo dos [gatilhos e associações](functions-triggers-bindings.md) em seu aplicativo de funções.

Para saber mais sobre a criação de modelos, consulte [Criação de modelos do Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Para modelos de exemplo, consulte:
- [Aplicativo de funções no Plano de Consumo]
- [Aplicativo de funções no Plano do Serviço de Aplicativo do Azure]

## <a name="required-resources"></a>Recursos necessários

Uma implantação Azure Functions normalmente consiste nesses recursos:

| Recurso                                                                           | Requisito | Referência de sintaxe e propriedades                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Um aplicativo de funções                                                                     | Obrigatório    | [Microsoft. Web/sites](/azure/templates/microsoft.web/sites)                             |
| Uma conta de [armazenamento do Azure](../storage/index.yml)                                   | Obrigatório    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Um componente [Application insights](../azure-monitor/app/app-insights-overview.md) | Opcional    | [Microsoft. insights/Components](/azure/templates/microsoft.insights/components)         |
| Um [plano de hospedagem](./functions-scale.md)                                             | Opcional<sup>1</sup>    | [Microsoft. Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Um plano de hospedagem só é necessário quando você opta por executar seu aplicativo de funções em um [plano Premium](./functions-premium-plan.md) ou em um [plano do serviço de aplicativo](../app-service/overview-hosting-plans.md).

> [!TIP]
> Embora não seja necessário, é altamente recomendável que você configure Application Insights para seu aplicativo.

<a name="storage"></a>
### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para um aplicativo de funções. Você precisa de uma conta de finalidade geral que dá suporte a blobs, tabelas, consultas e arquivos. Para saber mais, confira [Requisitos da conta de armazenamento do Azure Functions](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Além disso, a propriedade `AzureWebJobsStorage` deve ser especificada como uma configuração de aplicativo na configuração do site. Se o aplicativo de função não usar o Application Insights para monitoramento, também deverá especificar `AzureWebJobsDashboard` como uma configuração de aplicativo.

O Azure Functions runtime usa a cadeia de conexão `AzureWebJobsStorage` para criar filas internas.  Quando o Application Insights não estiver habilitado, o runtime usará a cadeia de conexão `AzureWebJobsDashboard` para fazer logon no armazenamento de Tabela do Azure e capacitar a guia **Monitor** no portal.

Essas propriedades são especificadas na coleção `appSettings` no objeto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights é recomendado para monitorar seus aplicativos de funções. O recurso Application Insights é definido com o tipo **Microsoft. insights/Components** e o tipo **Web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Além disso, a chave de instrumentação precisa ser fornecida ao aplicativo de funções usando a `APPINSIGHTS_INSTRUMENTATIONKEY` configuração de aplicativo. Essa propriedade é especificada na `appSettings` coleção no `siteConfig` objeto:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plano de hospedagem

A definição do plano de hospedagem varia e pode ser uma das seguintes:
* [Plano de consumo](#consumption) (padrão)
* [Plano Premium](#premium)
* [Plano do Serviço de Aplicativo](#app-service-plan)

### <a name="function-app"></a>Aplicativo de funções

O recurso de aplicativo de funções é definido usando um recurso do tipo **Microsoft. Web/sites e o** tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Se você estiver definindo explicitamente um plano de hospedagem, um item adicional será necessário na matriz de dependências: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Um aplicativo de funções deve incluir estas configurações de aplicativo:

| Nome da configuração                 | Descrição                                                                               | Valores de exemplo                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Uma cadeia de conexão para uma conta de armazenamento que o tempo de execução do Functions usa para a fila interna | Consulte a [conta de armazenamento](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | A versão do Azure Functions Runtime                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A pilha de idiomas a ser usada para funções neste aplicativo                                   | `dotnet`, `node`, `java`, `python` ou `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Necessário apenas se estiver usando a `node` pilha de idiomas, especifica a versão a ser usada              | `10.14.1`                             |

Essas propriedades são especificadas na `appSettings` coleção na `siteConfig` Propriedade:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Implantar no plano de consumo

O plano de consumo automaticamente aloca poder de computação quando seu código está em execução, dimensiona horizontalmente conforme necessário para lidar com a carga e, em seguida, é dimensionado quando o código não está em execução. Você não precisa pagar por VMs ociosas e não precisa reservar a capacidade com antecedência. Para saber mais, confira [Escala e hospedagem do Azure Functions](consumption-plan.md).

Para um exemplo de modelo do Azure Resource Manager, consulte [Aplicativo de funções no Plano de Consumo].

### <a name="create-a-consumption-plan"></a>Criar um Plano de Consumo

Um plano de consumo não precisa ser definido. Uma será criada automaticamente ou selecionada em uma base por região quando você criar o recurso do aplicativo de funções em si.

O plano de consumo é um tipo especial de recurso "ServerFarm". Para o Windows, você pode especificá-lo usando o `Dynamic` valor para as `computeMode` `sku` Propriedades e:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> O plano de consumo não pode ser explicitamente definido para Linux. Ele será criado automaticamente.

Se você definir explicitamente seu plano de consumo, será necessário definir a `serverFarmId` propriedade no aplicativo para que ele aponte para a ID de recurso do plano. Você deve garantir que o aplicativo de funções também tenha uma `dependsOn` configuração para o plano.

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

As configurações exigidas por um aplicativo de funções em execução no plano de consumo são adiadas entre o Windows e o Linux. 

#### <a name="windows"></a>Windows

No Windows, um plano de consumo requer uma configuração adicional na configuração do site: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Essa propriedade configura a conta de armazenamento onde o código do aplicativo de funções e a configuração são armazenados.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Não defina a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) configuração conforme ela é gerada quando o site é criado pela primeira vez.  

#### <a name="linux"></a>Linux

No Linux, o aplicativo de funções deve ter seu `kind` definido como `functionapp,linux` e deve ter a `reserved` propriedade definida como `true` . 

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

As [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) configurações e não têm suporte no Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Implantar no plano Premium

O plano Premium oferece o mesmo dimensionamento do plano de consumo, mas inclui recursos dedicados e recursos adicionais. Para saber mais, confira [Azure Functions plano Premium](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Criar um plano Premium

Um plano Premium é um tipo especial de recurso "ServerFarm". Você pode especificá-lo usando `EP1` `EP2` ou, ou `EP3` para o `Name` valor da propriedade no `sku` [objeto Description](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

Um aplicativo de funções em um plano Premium deve ter a `serverFarmId` propriedade definida como a ID de recurso do plano criado anteriormente. Além disso, um plano Premium requer uma configuração adicional na configuração do site: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Essa propriedade configura a conta de armazenamento onde o código do aplicativo de funções e a configuração são armazenados.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Não defina a [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) configuração conforme ela é gerada quando o site é criado pela primeira vez.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Implantar no plano do serviço de aplicativo

No Plano do Serviço de Aplicativo, seus aplicativos de funções são executados em VMs dedicadas, em SKUs Basic, Standard e Premium, assim como os aplicativos Web. Para obter detalhes sobre como o plano do Serviço de Aplicativo funciona, consulte [Visão geral detalhada de planos de Serviço de Aplicativo do Azure](../app-service/overview-hosting-plans.md).

Para um exemplo de modelo do Azure Resource Manager, consulte [Aplicativo de funções no Plano do Serviço de Aplicativo do Azure].

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

Um plano do serviço de aplicativo é definido por um recurso "ServerFarm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Para executar seu aplicativo no Linux, você também deve definir o `kind` para `Linux` :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Criar um aplicativo de funções

Um aplicativo de funções em um plano do serviço de aplicativo deve ter a `serverFarmId` propriedade definida como a ID de recurso do plano criado anteriormente.

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

Os aplicativos do Linux também devem incluir uma `linuxFxVersion` propriedade em `siteConfig` . Se você estiver apenas implantando o código, o valor para isso será determinado pela pilha de tempo de execução desejada no formato de ```runtime|runtimeVersion``` :

| Pilha            | Valor de exemplo                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Se você estiver [implantando uma imagem de contêiner personalizada](./functions-create-function-linux-custom-image.md), deverá especificá-la com `linuxFxVersion` e incluir a configuração que permite que a imagem seja puxada, como no [aplicativo Web para contêineres](../app-service/index.yml). Além disso, defina `WEBSITES_ENABLE_APP_SERVICE_STORAGE` como `false` , pois o conteúdo do aplicativo é fornecido no próprio contêiner:

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Personalizando uma implantação

Um aplicativo de funções tem muitos recursos filho que podem ser usados na sua implantação, incluindo configurações do aplicativo e opções de controle do código-fonte. Você também pode optar por remover o recurso filho **sourcecontrols** e usar uma opção de [implantação](functions-continuous-deployment.md) diferente.

> [!IMPORTANT]
> Para implantar seu aplicativo com êxito usando o Azure Resource Manager, é importante entender como os recursos são implantados no Azure. No exemplo a seguir, as configurações de nível superior são aplicadas usando **siteConfig**. É importante definir essas configurações em um nível superior porque transmitem informações para o mecanismo de implantação e de runtime do Functions. Informações de nível superior são necessárias antes do recurso filho **sourcecontrols/web** ser aplicado. Embora seja possível definir essas configurações no nível de recurso filho **config/appSettings**, em alguns casos, o aplicativo de funções deve ser implantado *antes de* **config/appSettings** ser aplicado. Por exemplo, quando você está usado funções com [aplicativos lógicos](../logic-apps/index.yml), as funções são uma dependência de outro recurso.

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
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
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
> Este modelo usa o valor configurações do aplicativo do [projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) , que define o diretório base no qual o mecanismo de implantação de funções (kudu) procura o código implantável. Em nosso repositório, nossas funções estão em uma subpasta da pasta **src**. Assim, no exemplo anterior, definimos o valor de configurações do aplicativo para `src`. Se as funções estão na raiz do seu repositório, ou se não está implantando do controle de origem, você pode remover esse valor de configurações do aplicativo.

## <a name="deploy-your-template"></a>Implantar o modelo

Você pode usar qualquer uma das seguintes maneiras para implantar o modelo:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [CLI do Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão Implantar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada de URL](https://www.bing.com/search?q=url+encode) do caminho bruto de seu `azuredeploy.json` arquivo no GitHub.

Este é um exemplo que usa markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Este é um exemplo que usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Implantar usando o PowerShell

Os comandos do PowerShell a seguir criam um grupo de recursos e implantam um modelo que cria um aplicativo de funções com seus recursos necessários. Para executar localmente, você deve ter o [Azure PowerShell](/powershell/azure/install-az-ps) instalado. Execute [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) para entrar.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Para testar essa implantação, você pode usar um [modelo como este](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) que cria um aplicativo de funções no Windows em um plano de consumo. Substitua `<function-app-name>` por um nome exclusivo para seu aplicativo de funções.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como desenvolver e configurar o Azure Functions.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Como definir as configurações do aplicativo de funções do Azure](functions-how-to-use-azure-function-app-settings.md)
* [Como criar a sua primeira função do Azure](./functions-get-started.md)

<!-- LINKS -->

[Aplicativo de funções no Plano de Consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplicativo de funções no Plano do Serviço de Aplicativo do Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
