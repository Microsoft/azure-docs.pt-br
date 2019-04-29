---
title: Regras de firewall dos Hubs de Eventos do Azure| Microsoft Docs
description: Use as Regras de firewall para permitir conexões de endereços IP específicos com os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: ccb2fa7b0805b332957513c52c0c1051d068d2cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821670"
---
# <a name="use-firewall-rules"></a>Usar regras de firewall

Para cenários em que os Hubs de Eventos do Azure devem ser acessíveis apenas de determinados sites bem conhecidos, as regras de firewall permitem que você configure regras para aceitar tráfego originado de endereços IPv4 específicos. Por exemplo, esses endereços podem ser aqueles de um gateway NAT corporativo.

## <a name="when-to-use"></a>Quando usar

Se você deseja configurar o seu namespace dos Hubs de Eventos para que receba tráfego apenas de um intervalo específico de endereços IP e rejeite todo o resto, você pode aproveitar uma *Regra de firewall* para bloquear os pontos de extremidade do Hub de Eventos de outros endereços IP. Por exemplo, se você usa os Hubs de Eventos com o [Azure ExpressRoute][express-route], você pode criar uma *Regra de firewall* para restringir o tráfego de seus endereços IP da infraestrutura local.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível do namespace dos Hubs de Eventos. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra IP permitida no namespace dos Hubs de Eventos será rejeitada como não autorizada. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** no portal para Hubs de Eventos fica vazia. Essa configuração padrão significa que o hub de eventos aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

>[!WARNING]
> Implementar as regras de firewall pode impedir que outros serviços do Azure interajam com os Hubs de Eventos.
>
> Não há suporte para serviços confiáveis da Microsoft para quando a Filtragem de IP (Firewalls) é implementada; isto será disponibilizado em breve.
>
> Cenários comuns do Azure que não funcionam com a Filtragem de IP (observe que a lista **NÃO** é exaustiva):
> - Azure Monitor
> - Stream Analytics do Azure
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
> - Azure Data Explorer
>
> Os serviços da Microsoft abaixo devem estar em uma rede virtual
> - Aplicativos Web do Azure 
> - Funções do Azure

### <a name="creating-a-firewall-rule-with-azure-resource-manager-templates"></a>Como criar uma regra de firewall com modelos do Azure Resource Manager

> [!IMPORTANT]
> As regras de firewall têm suporte nas camadas **Standard** e **Dedicada** dos Hubs de Eventos. Não tem suporte na camada básica.

O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de Hubs de Eventos existente.

Parâmetros de modelo:

- A **ipMask** é um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"**, que não restringe as conexões.
> Ao criar as regras de rede virtual ou de firewalls, devemos alterar a ***"defaultAction"***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximas etapas

Para restringir o acesso a Hubs de Eventos para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de rede virtual para Hubs de Eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md
