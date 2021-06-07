---
title: Exemplos de modelo do Resource Manager para Serviço de Aplicativo do Azure + Recursos do Application Insights
description: Modelos do Azure Resource Manager de exemplo para implantar um Serviço de Aplicativo do Azure com um recurso do Application Insights.
ms.topic: sample
ms.custom: devx-track-dotnet
author: lgayhardt
ms.author: lagayhar
ms.date: 08/06/2020
ms.openlocfilehash: 4e287d600e596539aec2763157036d2bb71b141c
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383314"
---
# <a name="resource-manager-template-samples-for-creating-azure-app-services-web-apps-with-application-insights-monitoring"></a>Exemplos de modelo do Resource Manager para criar aplicativos Web de Serviços de Aplicativos do Azure com monitoramento do Application Insights

Este artigo inclui exemplos de [modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) para implantar e configurar [recursos Application Insights clássicos](../app/create-new-resource.md) em conjunto com um aplicativo Web de Serviços de Aplicativos do Azure. Cada amostra inclui um arquivo de modelo e um arquivo de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="net-core-runtime"></a>runtime do .NET Core

O exemplo a seguir cria um aplicativo Web de Serviço de Aplicativo do Azure básico com o runtime do .NET Core e um [recurso do Application Insights clássico](../app/create-new-resource.md) com monitoramento habilitado. 

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "errorLink": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        },
                        {
                            "name": "ANCM_ADDITIONAL_ERROR_PAGE_LINK",
                            "value": "[parameters('errorLink')]"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "currentStack": {
      "value": "dotnetcore"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "errorLink": {
      "value": "https://web-app-name-01.scm.azurewebsites.net/detectors?type=tools&name=eventviewer"
    }
  }
}

```

## <a name="aspnet-runtime"></a>Runtime do ASP.NET

O exemplo a seguir cria um aplicativo Web de Serviço de Aplicativo do Azure básico com o runtime do ASP.NET e um [recursos do Application Insights clássico](../app/create-new-resource.md) com monitoramento habilitado. 

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "netFrameworkVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "netFrameworkVersion": "[parameters('netFrameworkVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameters-file"></a>Arquivo de parâmetros

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "currentStack": {
      "value": "dotnet"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "netFrameworkVersion": {
      "value": "v4.0"
    }
  }
}
```

## <a name="nodejs-runtime-linux"></a>Runtime do Node.js (Linux)

O exemplo a seguir cria um aplicativo Web do Linux do Serviço de Aplicativo do Azure básico com o runtime do Node.js e um [recursos do Application Insights clássico](../app/create-new-resource.md) com monitoramento habilitado. 

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "linuxFxVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "linuxFxVersion": "[parameters('linuxFxVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": false
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": null,
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "App-planTest01-916e"
    },
    "serverFarmResourceGroup": {
      "value": "app_resource_group_custom"
    },
    "alwaysOn": {
      "value": true
    },
    "linuxFxVersion": {
      "value": "NODE|12-lts"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Obtenha outros modelos de amostra do Azure Monitor](../resource-manager-samples.md).
* [Saiba mais sobre os recursos do Application Insights clássico](../app/create-new-resource.md).
