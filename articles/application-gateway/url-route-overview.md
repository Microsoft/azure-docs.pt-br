---
title: Visão geral do roteamento de conteúdo baseado na URL do Gateway de Aplicativo do Azure
description: Este artigo fornece uma visão geral do roteamento de conteúdo baseado em URL do Gateway de Aplicativo, da configuração de UrlPathMap e da regra de PathBasedRouting.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 04/23/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: ee0267146140d095487b293331a7de493ba151c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61361936"
---
# <a name="azure-application-gateway-url-path-based-routing-overview"></a>Visão geral do roteamento baseado do caminho da URL de Gateway de Aplicativo do Azure

O Roteamento Baseado em Caminho de URL permite rotear o tráfego para pools do servidor de back-end com base nos Caminhos de URL da solicitação. 

Um dos cenários possíveis é rotear as solicitações de tipos de conteúdo diferentes para pools de servidores de back-end diferentes.

No exemplo a seguir, o Gateway de Aplicativo está fornecendo tráfego para contoso.com de três pools de servidor de back-end, como por exemplo: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/url-route-overview/figure1.png)

As solicitações para <http://contoso.com/video/*> são roteadas para VideoServerPool, e <http://contoso.com/images/*> são roteadas para ImageServerPool. O DefaultServerPool será selecionado se nenhum dos padrões de caminho forem compatíveis.

> [!IMPORTANT]
> As regras são processadas na ordem em que elas são listadas no portal. É altamente recomendável configurar primeiro os ouvintes de vários locais para configurar um ouvinte básico.  Isso garante que o tráfego seja roteado para o back-end correto. Se um ouvinte básico for listado primeiro e corresponder a uma solicitação de entrada, ele é processado por esse ouvinte.

## <a name="urlpathmap-configuration-element"></a>Elemento de configuração UrlPathMap

O elemento urlPathMap é usado para especificar padrões de Caminho para mapeamentos de pool do servidor de back-end. O seguinte é o snippet do elemento urlPathMap do arquivo de modelo.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> PathPattern: Essa configuração é uma lista de padrões de caminho para correspondência. Cada um deve começar com / e o único lugar onde um "*" é permitido é no final após um "/". A cadeia de caracteres inserida no correspondente de caminho não inclui nenhum texto após o primeiro ? ou #, e esses caracteres não são permitidos aqui.

Você pode conferir um [modelo do Resource Manager usando o roteamento baseado em URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) para obter mais informações.

## <a name="pathbasedrouting-rule"></a>Regra de PathBasedRouting

RequestRoutingRule do tipo PathBasedRouting é usada para associar um ouvinte a um urlPathMap. Todas as solicitações recebidas por este ouvinte são roteadas com base na política especificada no urlPathMap.
snippet de código da regra de PathBasedRouting:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer o roteamento de conteúdo baseado em URL, acesse [criar um application gateway usando o roteamento baseado em URL](tutorial-url-route-powershell.md) para criar um application gateway com as regras de roteamento de URL.

<!-- Update_Description: update metedata properties -->