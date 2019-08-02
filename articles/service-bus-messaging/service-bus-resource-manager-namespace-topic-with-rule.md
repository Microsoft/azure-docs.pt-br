---
title: Criar uma regra e assinatura de tópicos do Barramento de Serviço do Azure usando o modelo do Azure Resource Manager | Microsoft Docs
description: Criar um namespace do Barramento de Serviço com tópico, assinatura e regra usando um modelo do Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 8be34a85cb1a1863ee8a78c7b97af66627612fea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444754"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Criar um namespace do Barramento de Serviço com tópico, assinatura e regra usando um modelo do Azure Resource Manager

Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace do Barramento de Serviço com tópico, assinatura e regra (filtro). O artigo explica como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para saber mais sobre a criação de modelos, consulte [Criação de modelos do Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter mais informações sobre as práticas e padrões em convenções de nomenclatura de recursos do Azure, consulte [convenções de nomenclatura para recursos do Azure recomendadas][Recommended naming conventions for Azure resources].

Para ver o modelo completo, veja o [Modelo de namespace do Barramento de Serviço com tópico, assinatura e regra][Service Bus namespace with topic, subscription, and rule].

> [!NOTE]
> Os modelos do Azure Resource Manager a seguir estão disponíveis para download e implantação.
> 
> * [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
> * [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
> * [Criar um namespace do Barramento de Serviço](service-bus-resource-manager-namespace.md)
> * [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
> 
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e procure por Barramento de Serviço.
> 
> 

## <a name="what-do-you-deploy"></a>O que você implanta?

Com este modelo, você implantará um namespace de Barramento de Serviço com tópico, assinatura e regra (filtro).

Os [tópicos e as assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fornecem uma forma de comunicação de um para muitos, em um padrão de *publicação/assinatura*. Ao usar tópicos e assinaturas, componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, mas trocam mensagens por meio de tópico que age como um intermediário. Uma assinatura para um tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas ao tópico. Um filtro na assinatura permite que você especifique quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em muitos cenários, as mensagens com características específicas precisam ser processadas de maneiras diferentes. Para habilitar isso, você pode configurar assinaturas para localizar as mensagens com as propriedades específicas e, em seguida, realizar determinadas modificações nessas propriedades. Embora as assinaturas do Barramento de Serviço vejam todas as mensagens enviadas para o tópico, você só poderá copiar um subconjunto dessas mensagens para a fila de assinatura virtual. Isso é feito usando filtros de assinatura. Para saber mais sobre regras (filtros), consulte [Regras e ações](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>parâmetros

Com o Azure Resource Manager, defina parâmetros para os valores que você deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Defina um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os seguintes parâmetros:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
O nome do namespace do Barramento de Serviço a ser criado.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
O nome do tópico criado no namespace do Barramento de Serviço.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
O nome da assinatura criada no namespace do Barramento de Serviço.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
O nome da regra (filtro) criada no namespace do Barramento de Serviço.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
A versão da API do Barramento de Serviço do modelo.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Recursos a implantar
Cria um namespace de Barramento de Serviço padrão do tipo **Mensagens**, com tópico, assinatura e regras.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Para saber mais sobre a sintaxe e as propriedades de JSON, consulte [namespaces](/azure/templates/microsoft.servicebus/namespaces), [topics](/azure/templates/microsoft.servicebus/namespaces/topics), [subscriptions](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions) e [rules](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>CLI do Azure
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Próximas etapas
Consulte o tópico a seguir que mostra como criar um filtro de assinatura: [Criar um namespace do barramento de serviço com tópico, assinatura e regra usando um modelo do Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)

Saiba como gerenciar esses recursos consultando estes artigos:

* [Gerenciar o Barramento de Serviço do Azure](service-bus-management-libraries.md)
* [Gerenciar o Barramento de Serviço com o PowerShell](service-bus-manage-with-ps.md)
* [Gerenciar recursos do Barramento de Serviço com o Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

