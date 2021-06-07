---
title: Visão geral do roteamento de conteúdo baseado na URL do Gateway de Aplicativo do Azure
description: Este artigo fornece uma visão geral do roteamento de conteúdo baseado em URL Aplicativo Azure gateway, configuração de UrlPathMap e regra PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a9b2e8148586ec58ea6a7a033099e726920857b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84987939"
---
# <a name="url-path-based-routing-overview"></a>Visão geral do Roteamento Baseado em Caminho de URL

O Roteamento Baseado em Caminho de URL permite rotear o tráfego para pools do servidor de back-end com base nos Caminhos de URL da solicitação. 

Um dos cenários possíveis é rotear as solicitações de tipos de conteúdo diferentes para pools de servidores de back-end diferentes.

No exemplo a seguir, o Gateway de Aplicativo está fornecendo tráfego para contoso.com de três pools de servidor de back-end, como por exemplo: VideoServerPool, ImageServerPool e DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

As solicitações de http\://contoso.com/video/* são roteadas para VideoServerPool e as de http\://contoso.com/images/* são roteadas para ImageServerPool. O DefaultServerPool será selecionado se nenhum dos padrões de caminho forem compatíveis.

> [!IMPORTANT]
> Para a SKU v1, as regras são processadas na ordem em que estão listadas no Portal. Se um ouvinte básico for listado primeiro e corresponder a uma solicitação de entrada, ele é processado por esse ouvinte. Para a SKU v2, as correspondências exatas têm precedência mais alta. No entanto, é altamente recomendável configurar primeiro os ouvintes de vários sites antes de configurar um ouvinte básico. Isso garante que o tráfego seja roteado para o back-end correto.

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

### <a name="pathpattern"></a>PathPattern

PathPattern é uma a lista de padrões de caminho para correspondência. Cada um deve começar com / e o único lugar onde um "*" é permitido é no final após um "/". A cadeia alimentada para o correspondente de caminho não inclui nenhum texto após o primeiro? ou #, e esses caracteres não são permitidos aqui. Caso contrário, todos os caracteres permitidos em um URL serão permitidos no PathPattern.

Os padrões compatíveis dependem de se você implanta o Gateway de Aplicativo v1 ou v2:

#### <a name="v1"></a>v1

As regras de caminho não diferenciam maiúsculas de minúsculas.

|Padrão de caminho v1  |É compatível?  |
|---------|---------|
|`/images/*`     |sim|
|`/images*`     |sim|
|`/images/*.jpg`     |não|
|`/*.jpg`     |não|
|`/Repos/*/Comments/*`     |não|
|`/CurrentUser/Comments/*`     |sim|

#### <a name="v2"></a>v2

As regras de caminho não diferenciam maiúsculas de minúsculas.

|Padrão de caminho v2  |É compatível?  |
|---------|---------|
|`/images/*`     |sim|
|`/images*`     |sim|
|`/images/*.jpg`     |não|
|`/*.jpg`     |não|
|`/Repos/*/Comments/*`     |não|
|`/CurrentUser/Comments/*`     |sim|

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
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}"
    }

}
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Depois de conhecer o roteamento de conteúdo baseado em URL, acesse [criar um application gateway usando o roteamento baseado em URL](create-url-route-portal.md) para criar um application gateway com as regras de roteamento de URL.
