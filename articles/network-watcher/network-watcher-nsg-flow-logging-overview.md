---
title: "Introdução ao log de fluxo dos Grupos de Segurança da Rede com o Observador de Rede do Azure | Microsoft Docs"
description: "Esta página explica como usar o recurso dos logs de fluxo NSG do Observador de Rede do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: be29b993592e494053353aac1067bfb7eff90ed7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao log de fluxo dos Grupos de Segurança da Rede

Os logs de fluxo do Grupo de Segurança da Rede são um recurso do Observador de Rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança da Rede. Esses logs de fluxo são escritos no formato json e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, informações com cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

![visão geral dos logs de fluxo][1]

Embora os logs de fluxo sejam destinados aos Grupos de Segurança da Rede, eles não são exibidos como os outros logs. Os logs de fluxo são armazenados apenas em uma conta de armazenamento e seguem o caminho do log como mostrado no exemplo a seguir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

As mesmas políticas de retenção, como visto em outros logs, aplicam-se aos logs de fluxo. Os logs têm uma política de retenção que pode ser definida desde 1 dia até 365 dias. Se uma política de retenção não for definida, os logs serão mantidos para sempre.

## <a name="log-file"></a>Arquivo de log

Os logs de fluxo têm várias propriedades. A lista a seguir é uma listagem das propriedades retornadas no log de fluxo NSG:

* **time** - a hora em que o evento foi registrado
* **systemId** - a ID do recurso Grupo de Segurança da Rede.
* **category** - a categoria do evento; sempre será NetworkSecurityGroupFlowEvent
* **resourceid** - a ID do recurso do NSG
* **operationName** - é sempre NetworkSecurityGroupFlowEvents
* **properties** - uma coleção de propriedades do fluxo
    * **Version** - o número de versão do esquema do evento Log de Fluxo
    * **flows** - uma coleção de fluxos. Essa propriedade tem várias entradas para diferentes regras
        * **rule** - a regra para a qual os fluxos são listados
            * **flows** - uma coleção de fluxos
                * **mac** - o endereço MAC da NIC para a VM na qual o fluxo foi coletado
                * **flowTuples** - uma cadeia de caracteres que contém várias propriedades para a tupla de fluxo no formato separado por vírgulas
                    * **Time Stamp** - esse valor é o carimbo de hora de quando ocorreu o fluxo no formato UNIX EPOCH
                    * **Source IP** - o IP de origem
                    * **Destination IP** - o IP de destino
                    * **Source Port** - a porta de origem
                    * **Destination Port** - a porta de destino
                    * **Protocol** - o protocolo do fluxo. Os valores válidos são **T** para TCP e **U** para UDP
                    * **Traffic Flow** - a direção do fluxo do tráfego. Os valores válidos são **I** para entrada e **O** para saída.
                    * **Traffic** - se o tráfego foi permitido ou negado. Os valores válidos são **A** para permitido e **D** para negado.


Segue um exemplo de um Log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior. 

> [!NOTE]
> Os valores na propriedade flowTuples são uma lista separada por vírgulas.
 
```json
{
    "records": 
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>Próximas etapas

Saiba como habilitar os Logs de fluxo visitando [Habilitar o Log de fluxo](network-watcher-nsg-flow-logging-portal.md).

Saiba mais sobre o log de NSG visitando [Log Analytics para os grupos de segurança da rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md).

Descubra se o tráfego é permitido ou negado em uma VM visitando [Examinar o tráfego com a verificação de fluxo de IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png

