---
title: Suporte a WebSocket no gateway Aplicativo Azure
description: O Gateway de Aplicativo fornece suporte nativo a WebSocket em todos os tamanhos de gateway. Não há configurações definidas pelo usuário.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362688"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Visão geral do suporte para WebSocket no Gateway de Aplicativo

O Gateway de Aplicativo fornece suporte nativo a WebSocket em todos os tamanhos de gateway. Não há nenhuma configuração configurada pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. 

O protocolo WebSocket padronizado na [RFC6455](https://tools.ietf.org/html/rfc6455) permite uma comunicação full-duplex entre um servidor e um cliente em uma conexão TCP de execução longa. Esse recurso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem a necessidade de sondagem, necessária nas implementações baseadas em HTTP. O WebSocket tem baixa sobrecarga, ao contrário do HTTP e pode reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização mais eficiente de recursos. Os protocolos WebSocket são projetados para funcionar em portas HTTP tradicionais de 80 e 443.

Você pode continuar usando um ouvinte HTTP padrão na porta 80 ou 443 para receber o tráfego do WebSocket. O tráfego WebSocket será direcionado para o servidor de back-end habilitado para WebSocket usando o pool de back-end apropriado conforme especificado nas regras do gateway de aplicativo. O servidor back-end deve responder às investigações do gateway de aplicativo, descritas na seção [Visão geral da investigação de integridade](application-gateway-probe-overview.md). As investigações de integridade do gateway de aplicativo se destinam apenas a HTTP/HTTPS. Cada servidor back-end deve responder às investigações de HTTP do gateway de aplicativo para encaminhar o tráfego do WebSocket para o servidor.

Ele é usado em aplicativos que se beneficiam de comunicação rápida e em tempo real, como chat, painel e aplicativos de jogos.

## <a name="how-does-websocket-work"></a>Como funciona o WebSocket

Para estabelecer uma conexão WebSocket, um handshake baseado em HTTP específico é trocado entre o cliente e o servidor. Em caso de êxito, o protocolo de camada de aplicativo é "atualizado" de HTTP para WebSockets usando a conexão TCP estabelecida anteriormente. Assim que isso ocorre, o HTTP fica totalmente fora de cogitação. Os dados podem ser enviados ou recebidos usando o protocolo WebSocket por ambos os pontos de extremidade até o encerramento da conexão com o WebSocket. 

![O diagrama compara um cliente que interage com um servidor Web, conectando duas vezes para obter duas respostas, com uma interação WebSocket, em que um cliente se conecta a um servidor uma vez para obter várias respostas.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Elemento de configuração do ouvinte

Um ouvinte HTTP existente pode ser usado para dar suporte ao tráfego do WebSocket. A seguir, veja um snippet de um elemento httpListeners de um arquivo de modelo de exemplo. Você precisaria de ouvintes HTTP e HTTPS para oferecer suporte a tráfego WebSocket e WebSocket seguro. Da mesma forma, você pode usar o portal ou Azure PowerShell para criar um gateway de aplicativo com ouvintes na porta 80/443 para dar suporte ao tráfego de WebSocket.

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

Um BackendAddressPool é usado para definir um pool de back-end com servidores habilitados para WebSocket. A backendHttpSetting é definida com uma porta de back-end 80 e 443. O valor de tempo limite da solicitação em configurações HTTP também se aplica à sessão WebSocket. Não há nenhuma alteração necessária na regra de roteamento, que é usada para vincular o ouvinte apropriado ao pool de endereços de back-end correspondente. 

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
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Outro motivo é que a investigação de integridade de back-end do gateway de aplicativo dá suporte apenas aos protocolos HTTP e HTTPS. Se o servidor back-end não responder às investigações de HTTP ou HTTPS, ele será retirado do pool de back-end.

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre o suporte ao WebSocket, vá para [criar um gateway de aplicativo](quick-create-powershell.md) para começar a usar um aplicativo Web habilitado para WebSocket.