---
title: Solucionar problemas do gateway de VPN do Azure usando logs de diagnóstico
description: Solucionar problemas do gateway de VPN do Azure usando logs de diagnóstico
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771985"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Solucionar problemas do gateway de VPN do Azure usando logs de diagnóstico

Este artigo ajuda a entender os diferentes logs disponíveis para diagnóstico de gateway de VPN e como usá-los para solucionar problemas de gateway de VPN com eficiência.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Os seguintes logs estão disponíveis no Azure:

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Contém logs de diagnóstico para eventos de configuração do gateway, alterações principais e eventos de manutenção. |
|**TunnelDiagnosticLog** | Contém eventos de alteração de estado de túnel. Eventos Connect/Disconnect de túnel têm um motivo resumido para a alteração de estado, se aplicável. |
|**RouteDiagnosticLog** | Registra as alterações em rotas estáticas e eventos BGP que ocorrem no gateway. |
|**IKEDiagnosticLog** | Registra mensagens de controle IKE e eventos no gateway. |
|**P2SDiagnosticLog** | Registra mensagens de controle de ponto a site e eventos no gateway. |

Observe que há várias colunas disponíveis nessas tabelas. Neste artigo, estamos apenas apresentando as mais relevantes para um consumo de log mais fácil.

## <a name="set-up-logging"></a><a name="setup"></a>Configurar registro em log

Para saber como configurar eventos de log de diagnóstico do gateway de VPN do Azure usando o Azure Log Analytics, consulte [configurar alertas em eventos de log de diagnóstico do gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

As alterações de configuração são auditadas na tabela **GatewayDiagnosticLog** . Pode levar alguns minutos antes que as alterações executadas sejam refletidas nos logs.

Aqui você tem uma consulta de exemplo como referência.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

Essa consulta no **GatewayDiagnosticLog** mostrará várias colunas.

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o carimbo de data/hora de cada evento, no fuso horário UTC.|
|**OperationName** |o evento que aconteceu. Pode ser *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove,* ValidateGatewayConfiguration.|
|**Mensagem** | o detalhe de qual operação está acontecendo e lista os resultados bem-sucedidos/com falha.|

O exemplo a seguir mostra a atividade registrada quando uma nova configuração foi aplicada:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Exemplo de uma operação set gateway vista em GatewayDiagnosticLog.":::


Observe que um SetGatewayConfiguration será registrado sempre que alguma configuração for modificada em um gateway de VPN ou em um gateway de rede local.
A referência cruzada dos resultados da tabela **GatewayDiagnosticLog** com os da tabela **TunnelDiagnosticLog** pode nos ajudar a determinar se uma falha de conectividade de túnel foi iniciada ao mesmo tempo em que uma configuração foi alterada ou se uma manutenção ocorreu. Nesse caso, temos um excelente ponteiro para a possível causa raiz.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

A tabela **TunnelDiagnosticLog** é muito útil para inspecionar os status de conectividade histórica do túnel.

Aqui você tem uma consulta de exemplo como referência.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

Essa consulta no **TunnelDiagnosticLog** mostrará várias colunas.


|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o carimbo de data/hora de cada evento, no fuso horário UTC.|
|**OperationName** | o evento que aconteceu. Pode ser *TunnelConnected* ou *TunnelDisconnected*.|
| **S de instância \_** | a instância da função de gateway que disparou o evento. Pode ser GatewayTenantWorker \_ em \_ 0 ou GatewayTenantWorker \_ em \_ 1, que são os nomes das duas instâncias do gateway.|
| **Recurso** | indica o nome do gateway de VPN. |
| **ResourceGroup** | indica o grupo de recursos onde está o gateway.|


Saída de exemplo:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Exemplo de um evento conectado de túnel visto em TunnelDiagnosticLog.":::


O **TunnelDiagnosticLog** é muito útil para solucionar os eventos anteriores sobre DESconexões VPN inesperadas. Sua natureza leve oferece a possibilidade de analisar intervalos de tempo grandes em vários dias com pouco esforço.
Somente depois de identificar o carimbo de data/hora de uma desconexão, você pode alternar para a análise mais detalhada da tabela **IKEdiagnosticLog** para se aprofundar no raciocínio das desconexões que serão relacionadas ao IPsec.


Algumas dicas de Solução de problemas:
- Se você vir um evento de desconexão em uma instância de gateway, seguido por um evento de conexão na instância de gateway **diferente** em alguns segundos, você verá um failover de gateway. Isso geralmente é um comportamento esperado devido à manutenção em uma instância de gateway. Para saber mais sobre esse comportamento, consulte [sobre a redundância de gateway de VPN do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- O mesmo comportamento será observado se você executar intencionalmente uma redefinição de gateway no lado do Azure, o que causa uma reinicialização da instância de gateway ativa. Para saber mais sobre esse comportamento, consulte [redefinir um gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic).
- Se você vir um evento de desconexão em uma instância de gateway, seguido por um evento de conexão na **mesma** instância de gateway em alguns segundos, você pode estar olhando uma falha de rede causando um tempo limite de DPD ou uma desconexão enviada erroneamente pelo dispositivo local.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

A tabela **RouteDiagnosticLog** rastreia a atividade de rotas ou rotas modificadas estaticamente, recebidas via BGP.

Aqui você tem uma consulta de exemplo como referência.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Essa consulta no **RouteDiagnosticLog** mostrará várias colunas.

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o carimbo de data/hora de cada evento, no fuso horário UTC.|
|**OperationName** | o evento que aconteceu. Pode ser *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*.|
| **Mensagem** | o detalhe de qual operação está acontecendo.|

A saída mostrará informações úteis sobre pares de BGP conectados/desconectados e rotas trocadas.

Exemplo:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Exemplo de atividade de troca de rota BGP vista em RouteDiagnosticLog.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

A tabela **IKEDiagnosticLog** oferece log de depuração detalhado para IKE/IPSec. Isso é muito útil para revisar ao solucionar problemas de desconexões ou falha ao conectar cenários de VPN.

Aqui você tem uma consulta de exemplo como referência.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

Essa consulta no **IKEDiagnosticLog** mostrará várias colunas.


|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o carimbo de data/hora de cada evento, no fuso horário UTC.|
| **RemoteIP** | o endereço IP do dispositivo VPN local. Em cenários do mundo real, é útil filtrar pelo endereço IP do dispositivo local relevante deve haver mais de um. |
|**LocalIP** | o endereço IP do gateway de VPN que estamos Solucionando problemas. Em cenários do mundo real, é útil filtrar pelo endereço IP do gateway de VPN relevante deve haver mais de um em sua assinatura. |
|**Evento** | contém uma mensagem de diagnóstico útil para solução de problemas. Normalmente, eles começam com uma palavra-chave e se referem às ações executadas pelo gateway do Azure: **\[ Send \]** indica um evento causado por um pacote IPsec enviado pelo gateway do Azure.  **\[ Recebido \]** indica um evento em consequência de um pacote recebido do dispositivo local.  **\[ Local \]** indica uma ação realizada localmente pelo gateway do Azure. |


Observe como as colunas RemoteIP, LocalIP e Event não estão presentes na lista de colunas original no banco de dados AzureDiagnostics, mas são adicionadas à consulta analisando a saída da coluna "Message" para simplificar sua análise.

Dicas de solução de problemas:

- Para identificar o início de uma negociação IPSec, você precisa encontrar a mensagem inicial SA \_ init. Essa mensagem pode ser enviada por qualquer lado do túnel. Quem envia o primeiro pacote é chamado de "iniciador" na terminologia IPsec, enquanto o outro lado torna-se o "Respondente". A primeira \_ mensagem de inicialização SA é sempre aquela em que rCookie = 0.

- Se o túnel IPsec falhar ao estabelecer, o Azure continuará tentando novamente a cada poucos segundos. Por esse motivo, a solução de problemas de "VPN inoperante" é muito conveniente em IKEdiagnosticLog porque você não precisa esperar por um tempo específico para reproduzir o problema. Além disso, a teoria sempre será a mesma toda vez que tentarmos, de modo que você possa ampliar uma negociação com falha de "amostra" a qualquer momento.

- A \_ inicialização SA contém os parâmetros IPSec que o par deseja usar para essa negociação IPSec. O documento oficial   
Os [parâmetros padrão de IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) lista os parâmetros IPSec com suporte pelo gateway do Azure com as configurações padrão.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

A última tabela disponível para diagnóstico de VPN é **P2SDiagnosticLog**. Esta tabela rastreia a atividade de ponto a site.

Aqui você tem uma consulta de exemplo como referência.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Essa consulta no **P2SDiagnosticLog** mostrará várias colunas.

|***Nome** _ | _ *_Descrição_** |
|---        | ---               |
|**TimeGenerated** | o carimbo de data/hora de cada evento, no fuso horário UTC.|
|**OperationName** | o evento que aconteceu. Será *P2SLogEvent*.|
| **Mensagem** | o detalhe de qual operação está acontecendo.|

A saída mostrará todas as configurações de ponto a site que o gateway aplicou, bem como as diretivas IPsec em vigor.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Exemplo de conexão ponto a site vista em P2SDiagnosticLog.":::

Além disso, sempre que um cliente se conectar via IKEv2 ou OpenVPN ponto a site, a tabela registrará a atividade de pacote, as conversas de EAP/RADIUS e os resultados de êxito/falha pelo usuário.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Exemplo de autenticação EAP vista em P2SDiagnosticLog.":::

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em logs de recursos de túnel, consulte [configurar alertas em logs de recursos do gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

