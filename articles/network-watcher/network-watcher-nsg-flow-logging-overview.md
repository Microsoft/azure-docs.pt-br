---
title: Introdução ao log de fluxo dos grupos de segurança da rede com o Observador de Rede do Azure | Microsoft Docs
description: Este artigo explica como usar o recurso dos logs de fluxo NSG do Observador de Rede do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 6e15149dec9fdbb7413745d36b3f6a158113b586
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684533"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao log de fluxo dos grupos de segurança da rede

Logs de fluxo do grupo de segurança de rede (NSG) são um recurso do Observador de Rede permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um NSG. Os logs de fluxo são escritos no formato JSON e mostram os fluxos de entrada e de saída por regra, a interface de rede (NIC) à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, e protocolo) e se o tráfego foi permitido ou negado e na Versão 2, informação de taxa de transferência (Bytes e Pacotes).


![visão geral dos logs de fluxo](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Embora os logs de fluxo sejam destinados aos NSGs, eles não são exibidos como os outros logs. Os logs de fluxo são armazenados apenas em uma conta de armazenamento e seguem o caminho do log mostrado no exemplo a seguir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Você pode analisar logs de fluxo e obter percepções sobre seu tráfego de rede usando a [análise de tráfego](traffic-analytics.md).

As mesmas políticas de retenção vistas para outros logs aplicam-se aos logs de fluxo. Você pode definir a política de retenção de log de 1 dia, 2147483647 dias. Se uma política de retenção não for definida, os logs serão mantidos para sempre.

> [!NOTE] 
> O uso do recurso de política de retenção com o log de fluxo do NSG pode resultar em um alto volume de operações de armazenamento e os custos associados. Se você não precisa do recurso de política de retenção, é recomendável que você defina esse valor como 0.


## <a name="log-file"></a>Arquivo de log

Logs de fluxo incluem as seguintes propriedades:

* **time** - a hora em que o evento foi registrado
* **systemId** - a ID do recurso Grupo de Segurança da Rede.
* **category** - a categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
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
                    * **Decisão de tráfego** - se o tráfego foi permitido ou negado. Os valores válidos são **A** para permitido e **D** para negado.
                    * **Estado de fluxo - versão 2 somente** - captura o estado do fluxo. Os possíveis estados são **B**: Iniciar, quando um fluxo é criado. As estatísticas não são fornecidas. **C**: Continuando um fluxo contínuo. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: Encerrar, quando um fluxo é encerrado. Estatísticas são fornecidas.
                    * **Pacotes - Fonte para destino - Versão 2 Apenas** A quantidade total de pacotes TCP ou UDP enviados da origem ao destino desde a última atualização.
                    * **Bytes enviados - Fonte para destino - Versão 2 Apenas** A quantidade total de bytes de pacotes TCP ou UDP enviados da origem ao destino desde a última atualização. O número total de bytes de pacote TCP e UDP enviados da origem para o destino desde os últimos bytes updatePacket inclui o cabeçalho e a carga útil do pacote.
                    * **Pacotes - Destino para fonte - Versão 2 Apenas** A quantidade total de pacotes TCP ou UDP enviados do destino para a fonte desde a última atualização.
                    * **Bytes enviados - Destino para fonte - Versão 2 Apenas** A quantidade total de bytes de pacotes TCP ou UDP enviados do destino para a fonte desde a última atualização. Os bytes de pacote incluem cabeçalho de pacote e carga útil.

## <a name="nsg-flow-logs-version-2"></a>Versão 2 de logs do fluxo NSG

Versão 2 dos logs apresenta o estado de fluxo. Você pode configurar qual versão de logs de fluxo receber. Para saber como habilitar os logs de fluxo, consulte [Habilitar o log de fluxo NSG](network-watcher-nsg-flow-logging-portal.md).

O estado de fluxo *B* é registrado quando um fluxo é iniciado. Estado de fluxo *C* e o estado de fluxo *E* são os estados que marcam a continuação de um fluxo e encerramento de fluxo, respectivamente. Os estados *C* e *E* contêm informações de largura de banda de tráfego.

Para os estados de fluxo de continuação *C* e final *E*, as contagens de bytes e pacotes são contagens agregadas do tempo do registro da tupla de fluxo anterior. Referenciando a conversação de exemplo anterior, o número total de pacotes transferidos é 1021 + 52 + 8005 + 47 = 9125. O número total de bytes transferidos é 588096 + 29952 + 4610880 + 27072 = 5256000.

**Exemplo**: Fluxo de tuplas de uma conversa TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Para os estados de fluxo de continuação *C* e final *E*, as contagens de bytes e pacotes são contagens agregadas do tempo do registro da tupla de fluxo anterior. Referenciando a conversação de exemplo anterior, o número total de pacotes transferidos é 1021 + 52 + 8005 + 47 = 9125. O número total de bytes transferidos é 588096 + 29952 + 4610880 + 27072 = 5256000.

O texto que segue é um exemplo de um log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior.

## <a name="nsg-flow-logging-considerations"></a>Considerações registro em log do fluxo NSG

**Habilite o fluxo de registro em log de fluxo NSG em todos os NSGs anexados a um recurso**: O registro em log de fluxo no Azure é configurado no recurso NSG. Um fluxo só será associado a uma regra de NSG. Em cenários em que vários NSGs são utilizados, é recomendável que o registro em log de fluxo do NSG esteja habilitado em todos os NSGs aplicados a uma sub-rede ou adaptador de rede de um recurso para garantir que todo o tráfego seja registrado. Para obter mais informações sobre os Grupos de Segurança de Rede, confira [como o tráfego é avaliado](../virtual-network/security-overview.md#how-traffic-is-evaluated). 

**Os custos de registro em log de fluxo**: O registro em log de fluxo de NSG é cobrado segundo o volume de log produzido. Um alto volume de tráfego pode resultar em um volume grande de log de fluxo e nos custos associados. Os preços do log de fluxo de NSG não incluem os custos de armazenamento subjacentes. O uso do recurso de política de retenção com o log de fluxo do NSG pode resultar em um alto volume de operações de armazenamento e os custos associados. Se você não precisa do recurso de política de retenção, é recomendável que você defina esse valor como 0. Veja [Preços do Observador de Rede](https://azure.microsoft.com/en-us/pricing/details/network-watcher/) e [Preços do Armazenamento do Azure](https://azure.microsoft.com/en-us/pricing/details/storage/) para obter mais detalhes.

**Conectado da internet IPs a VMs sem IPs públicos de fluxos de entrada**: As VMs que não têm um endereço IP público atribuído por meio de um endereço IP público associado à NIC como um IP público em nível de instância, ou que fazem parte de um pool de back-end do balanceador de carga básico, uso [padrão SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) e tem um endereço IP atribuído pelo Azure para facilitar a conectividade de saída. Como resultado, você poderá ver entradas de log de fluxo para fluxos de internet endereços IP, se o fluxo é destinado a uma porta no intervalo de portas atribuídas para SNAT. Embora o Azure não permitirá que esses fluxos para a máquina virtual, a tentativa é registrada e aparece no log de fluxo NSG do observador de rede por design. É recomendável que o tráfego de internet de entrada indesejado ser explicitamente bloqueada com NSG.

## <a name="sample-log-records"></a>Registros de log de exemplo

O texto que segue é um exemplo de um log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior.


> [!NOTE]
> Os valores na propriedade **flowTuples* são uma lista separada por vírgulas.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Amostra de formato de log do fluxo de NSG Versão 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Amostra de formato de log do fluxo de NSG Versão 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Próximas etapas

- Para saber como habilitar os logs de fluxo, consulte [Habilitar o log de fluxo NSG](network-watcher-nsg-flow-logging-portal.md).
- Para saber como ler os logs de fluxo, confira [Ler logs de fluxo do NSG](network-watcher-read-nsg-flow-logs.md).
- Para saber mais sobre o log de NSG, consulte [do Azure Monitor registra em log para grupos de segurança de rede (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Para determinar se o tráfego é permitido ou negado para ou de uma VM, consulte [Diagnosticar um problema de filtro de tráfego de rede VM](diagnose-vm-network-traffic-filtering-problem.md)
