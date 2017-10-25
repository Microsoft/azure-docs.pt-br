---
title: Suporte para WebSocket no Gateway de Aplicativo do Azure | Microsoft Docs
description: "Esta página oferece uma visão geral do suporte ao WebSocket do Gateway de Aplicativo."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Visão geral do suporte para WebSocket no Gateway de Aplicativo

O Gateway de Aplicativo fornece suporte nativo a WebSocket em todos os tamanhos de gateway. Não há nenhuma configuração configurada pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. 

O protocolo WebSocket padronizado na [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação full-duplex entre um servidor e um cliente em uma conexão TCP de execução longa. Esse recurso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem a necessidade de sondagem, necessária nas implementações baseadas em HTTP. O WebSocket tem baixa sobrecarga, ao contrário do HTTP e pode reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização mais eficiente de recursos. Os protocolos WebSocket são projetados para funcionar em portas HTTP tradicionais de 80 e 443.

Você pode continuar usando um ouvinte HTTP padrão na porta 80 ou 443 para receber o tráfego do WebSocket. O tráfego WebSocket será direcionado para o servidor de back-end habilitado para WebSocket usando o pool de back-end apropriado conforme especificado nas regras do gateway de aplicativo. O servidor back-end deve responder às investigações do gateway de aplicativo, descritas na seção [Visão geral da investigação de integridade](application-gateway-probe-overview.md). As investigações de integridade do gateway de aplicativo se destinam apenas a HTTP/HTTPS. Cada servidor back-end deve responder às investigações de HTTP do gateway de aplicativo para encaminhar o tráfego do WebSocket para o servidor.

## <a name="listener-configuration-element"></a>Elemento de configuração do ouvinte

Um ouvinte HTTP existente pode ser usado para dar suporte ao tráfego do WebSocket. A seguir, veja um trecho de um elemento httpListeners de um arquivo de modelo de exemplo. Você precisaria de ouvintes HTTP e HTTPS para oferecer suporte a tráfego WebSocket e WebSocket seguro. Da mesma forma, você pode usar o [portal](application-gateway-create-gateway-portal.md) ou o [PowerShell](application-gateway-create-gateway-arm.md) para criar um gateway de aplicativo com ouvintes na porta 80/443 para dar suporte ao tráfego WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuração de regra BackendAddressPool, BackendHttpSetting e Routing

Um BackendAddressPool é usado para definir um pool de back-end com servidores habilitados para WebSocket. A backendHttpSetting é definida com uma porta de back-end 80 e 443. As propriedades de afinidade baseada em cookie e requestTimeouts não são relevantes para o tráfego do WebSocket. Nenhuma alteração é necessária na regra de roteamento; “Básico” é usado para vincular o ouvinte apropriado ao pool de endereços de back-end correspondente. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end habilitado para WebSocket

O back-end deve ter um servidor Web HTTP/HTTPS em execução na porta configurada (normalmente 80/443) para que o WebSocket funcione. Esse requisito existe porque o protocolo WebSocket exige que o handshake inicial seja HTTP com atualização para o protocolo WebSocket como um campo de cabeçalho. Segue um exemplo de um cabeçalho:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Outro motivo é que a investigação de integridade de back-end do gateway de aplicativo dá suporte apenas aos protocolos HTTP e HTTPS. Se o servidor back-end não responder às investigações de HTTP ou HTTPS, ele será retirado do pool de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre o suporte ao WebSocket, vá para [criar um gateway de aplicativo](application-gateway-create-gateway.md) para começar a usar um aplicativo Web habilitado para WebSocket.

