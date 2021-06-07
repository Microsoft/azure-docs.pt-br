---
title: Introdução ao log de fluxo para NSGs
titleSuffix: Azure Network Watcher
description: Este artigo explica como usar o recurso dos logs de fluxo NSG do Observador de Rede do Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: bc085163b4f738d022ab9771794ec85293de5ed8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521672"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introdução ao log de fluxo dos grupos de segurança da rede

## <a name="introduction"></a>Introdução

Os logs de fluxo do NSG ( [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) ) são um recurso do observador de rede do Azure que permite registrar informações sobre o tráfego IP que flui através de um NSG. Os dados de fluxo são enviados para contas de armazenamento do Azure de onde você pode acessá-los, bem como exportá-los para qualquer ferramenta de visualização, SIEM ou IDS de sua escolha.

![visão geral dos logs de fluxo](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Por que usar logs de fluxo?

É essencial monitorar, gerenciar e conhecer sua própria rede para um desempenho, conformidade e segurança incomparáveis. Conhecer o seu próprio ambiente é de suma importância para protegê-lo e otimizá-lo. Geralmente é necessário saber o estado atual da rede, quem está se conectando, de onde eles estão se conectando, quais portas estão abertas para a Internet, o comportamento esperado da rede, o comportamento irregular da rede e os aumentos repentinos no tráfego.

Os logs de fluxo são a fonte de verdade para todas as atividades de rede em seu ambiente de nuvem. Quer você seja uma próxima inicialização tentando otimizar recursos ou grandes empresas tentando detectar invasões, os logs de fluxo são sua melhor aposta. Você pode usá-lo para otimizar os fluxos de rede, monitorar a taxa de transferência, verificar a conformidade, detectar invasões e muito mais.

## <a name="common-use-cases"></a>Casos de uso comuns

**Monitoramento de rede**: identificar tráfego desconhecido ou indesejado. Monitore os níveis de tráfego e o consumo de largura de banda. Filtrar logs de fluxo por IP e porta para entender o comportamento do aplicativo. Exportar logs de fluxo para ferramentas de análise e visualização de sua escolha para configurar painéis de monitoramento.

**Monitoramento e otimização de uso:** Identifique os principais palestrantes em sua rede. Combine com os dados do GeoIP para identificar o tráfego entre regiões. Entenda o crescimento do tráfego para a previsão de capacidade. Use dados para remover regras de tráfego overtly restritivas.

**Conformidade**: Use os dados de fluxo para verificar o isolamento de rede e a conformidade com as regras de acesso corporativo

**Análise de segurança & de perícia de rede**: analise fluxos de rede de IPS e interfaces de rede comprometidos. Exporte os logs de fluxo para qualquer ferramenta SIEM ou IDS de sua escolha.

## <a name="how-logging-works"></a>Como o log funciona

**Propriedades da chave**

- Os logs de fluxo operam na [camada 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) e registram todos os fluxos de IP que entram e saem de um NSG
- Os logs são coletados em um **intervalo de 1 minuto** por meio da plataforma Azure e não afetam os recursos do cliente ou o desempenho da rede de alguma forma.
- Os logs são gravados no formato JSON e mostram os fluxos de entrada e saída em uma base regra por NSG.
- Cada registro de log contém a NIC (interface de rede) que o fluxo aplica a informações de 5 tuplas, a decisão de tráfego & (versão 2 somente) informações de taxa de transferência. Veja o _formato de log_ abaixo para obter detalhes completos.
- Os logs de fluxo têm um recurso de retenção que permite excluir automaticamente os logs de até um ano após a criação. 

> [!NOTE]
> A retenção estará disponível somente se você usar [contas de armazenamento v2 de uso geral (GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts). 

**Conceitos fundamentais**

- Redes definidas por software são organizadas em redes virtuais (VNETs) e sub-redes. A segurança dessas VNets e sub-redes pode ser gerenciada usando um NSG.
- Um NSG (grupo de segurança de rede) contém uma lista de _regras de segurança_ que permitem ou negam o tráfego de rede em recursos aos quais ele está conectado. NSGs pode ser associado a sub-redes, VMs individuais ou adaptadores de rede (NIC) individuais conectados às VMs (Gerenciador de recursos). Para obter mais informações, confira [Visão geral do Grupo de Segurança de Rede](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Todos os fluxos de tráfego em sua rede são avaliados usando as regras no NSG aplicável.
- O resultado dessas avaliações são os logs de fluxo NSG. Os logs de fluxo são coletados por meio da plataforma Azure e não exigem nenhuma alteração nos recursos do cliente.
- Observação: as regras são de dois tipos – terminando & não terminando, cada um com diferentes comportamentos de log.
- - As regras de negação NSG estão sendo encerradas. O NSG que nega o tráfego o registrará em logs de fluxo e o processamento, nesse caso, será interrompido depois que qualquer NSG negar tráfego. 
- - As regras de NSG permitem que não sejam encerradas, o que significa que, mesmo se uma NSG permitir, o processamento continuará para o próximo NSG. O último NSG que permite o tráfego registrará o tráfego em logs de fluxo.
- Os logs de fluxo NSG são gravados em contas de armazenamento de onde podem ser acessados.
- Você pode exportar, processar, analisar e Visualizar logs de fluxo usando ferramentas como TA, Splunk, Grafana, Stealthwatch, etc.

## <a name="log-format"></a>Formato de log

Logs de fluxo incluem as seguintes propriedades:

* **time** - a hora em que o evento foi registrado
* **SystemId** -ID do sistema do grupo de segurança de rede.
* **category** - a categoria do evento. A categoria é sempre **NetworkSecurityGroupFlowEvent**
* **ResourceId** -a ID de recurso do NSG
* **operationName** - é sempre NetworkSecurityGroupFlowEvents
* **properties** - uma coleção de propriedades do fluxo
    * **Version** - o número de versão do esquema do evento Log de Fluxo
    * **fluxos** – uma coleção de fluxos. Essa propriedade tem várias entradas para diferentes regras
        * **rule** - a regra para a qual os fluxos são listados
            * **flows** - uma coleção de fluxos
                * **mac** - o endereço MAC da NIC para a VM na qual o fluxo foi coletado
                * **flowTuples** - uma cadeia de caracteres que contém várias propriedades para a tupla de fluxo no formato separado por vírgulas
                    * **Carimbo de data/hora** -esse valor é o carimbo de data/hora de quando o fluxo ocorreu no formato de época do UNIX
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


**Logs de fluxo NSG versão 2 (vs versão 1)** 

A versão 2 dos logs apresenta o conceito de estado de fluxo. Você pode configurar qual versão de logs de fluxo receber.

O estado de fluxo _B_ é registrado quando um fluxo é iniciado. Estado de fluxo _C_ e o estado de fluxo _E_ são os estados que marcam a continuação de um fluxo e encerramento de fluxo, respectivamente. Os estados _C_ e _E_ contêm informações de largura de banda de tráfego.

### <a name="sample-log-records"></a>Registros de log de exemplo

O texto que segue é um exemplo de um log de fluxo. Como você pode ver, há vários registros que seguem a lista de propriedades descrita na seção anterior.

> [!NOTE]
> Os valores na propriedade *flowTuples* são uma lista separada por vírgulas.
 
**Amostra de formato de log do fluxo de NSG Versão 1**
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
        
        
```
**Amostra de formato de log do fluxo de NSG Versão 2**
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
        }
        
```
**Tupla de log explicada**

![tupla de logs de fluxo](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Cálculo de largura de banda de exemplo**

Fluxo de tuplas de uma conversa TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Para os estados de fluxo de continuação _C_ e final _E_, as contagens de bytes e pacotes são contagens agregadas do tempo do registro da tupla de fluxo anterior. Referenciando a conversação de exemplo anterior, o número total de pacotes transferidos é 1021 + 52 + 8005 + 47 = 9125. O número total de bytes transferidos é 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Como habilitar logs de fluxo de NSG

Use o link relevante abaixo para obter guias sobre como habilitar logs de fluxo.

- [Azure portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Atualizando parâmetros

**Azure portal**

Na portal do Azure, navegue até a seção de logs de fluxo NSG no observador de rede. Em seguida, clique no nome do NSG. Isso abrirá o painel configurações para o log de fluxo. Altere os parâmetros desejados e clique em **salvar** para implantar as alterações.

**PS/CLI/REST/ARM**

Para atualizar parâmetros por meio de ferramentas de linha de comando, use o mesmo comando usado para habilitar logs de fluxo (acima), mas com parâmetros atualizados que você deseja alterar.

## <a name="working-with-flow-logs"></a>Trabalhando com logs de fluxo

*Ler e exportar logs de fluxo*

- [Baixar &amp; logs de fluxo de exibição do portal](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Ler logs de fluxo usando funções do PowerShell](./network-watcher-read-nsg-flow-logs.md)
- [Exportar logs de fluxo NSG para Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Embora os logs de fluxo sejam destinados aos NSGs, eles não são exibidos como os outros logs. Os logs de fluxo são armazenados apenas em uma conta de armazenamento e seguem o caminho do log mostrado no exemplo a seguir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualizar logs de fluxo*

- A [análise de tráfego do Azure](./traffic-analytics.md) é um serviço nativo do Azure para processar logs de fluxo, extrai informações e visualiza logs de fluxo. 
- [Destina Visualizar logs de fluxo NSG com Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Destina Visualizar logs de fluxo NSG com pilha elástica](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Destina Gerenciar e analisar logs de fluxo de NSG usando o Grafana](./network-watcher-nsg-grafana.md)
- [Destina Gerenciar e analisar logs de fluxo de NSG usando o Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>Considerações de log de fluxo NSG

**Considerações sobre a conta de armazenamento**: 

- Local: a conta de armazenamento usada deve estar na mesma região que o NSG.
- Nível de desempenho: atualmente, há suporte apenas para contas de armazenamento de camada Standard.
- Rotação de chaves de autogerenciamento: se você alterar/girar as chaves de acesso para sua conta de armazenamento, os logs de fluxo do NSG deixarão de funcionar. Para corrigir esse problema, você deve desabilitar e, em seguida, reabilitar os logs de fluxo do NSG.

**Custos de log de fluxo**: o log de fluxo de NSG é cobrado no volume de logs produzidos. Um alto volume de tráfego pode resultar em um volume grande de log de fluxo e nos custos associados. Os preços do log de fluxo de NSG não incluem os custos de armazenamento subjacentes. Usar o recurso de política de retenção com log de fluxo NSG significa incorrer em custos de armazenamento separados por longos períodos de tempo. Se você quiser manter os dados para sempre e não quiser aplicar nenhuma política de retenção, defina a retenção (dias) como 0. Para obter mais informações, consulte [preços do observador de rede](https://azure.microsoft.com/pricing/details/network-watcher/) e preços do [armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter detalhes adicionais.

**Problemas com regras TCP de entrada definidas pelo usuário**: [NSGs (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md) são implementados como um [Firewall com estado](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). No entanto, devido às limitações atuais da plataforma, as regras definidas pelo usuário que afetam os fluxos TCP de entrada são implementadas de forma sem monitoração de estado. Devido a isso, os fluxos afetados por regras de entrada definidas pelo usuário se tornam não conclusivos. Além disso, as contagens de byte e pacote não são registradas para esses fluxos. Consequentemente, o número de bytes e pacotes relatados nos logs de fluxo NSG (e Análise de Tráfego) podem ser diferentes dos números reais. Um sinalizador de aceitação que corrige esses problemas está programado para ser disponibilizado por março de 2021 mais recente. No ínterim, os clientes que enfrentam problemas graves devido a esse comportamento podem solicitar a recusa por meio do suporte, gerar uma solicitação de suporte no observador de rede > logs de fluxo do NSG.  

**Fluxos de entrada registrados de IPS de Internet para VMs sem IPS públicos**: VMs que não têm um endereço IP público atribuído por meio de um endereço IP público associado à NIC como um IP público em nível de instância, ou que fazem parte de um pool de back-end do Load Balancer básico, usam [SNAT padrão](../load-balancer/load-balancer-outbound-connections.md) e têm um endereço IP atribuído pelo Azure para facilitar a conectividade de saída. Como resultado, você poderá ver entradas de log de fluxo de fluxos de endereços IP da Internet, se o fluxo for destinado a uma porta no intervalo de portas atribuídas para SNAT. Embora o Azure não permita esses fluxos para a VM, a tentativa é registrada e aparece no log de fluxo do NSG do observador de rede por design. Recomendamos que o tráfego de Internet de entrada indesejado seja explicitamente bloqueado com NSG.

**Problema com a sub-rede NSG do gateway de aplicativo v2**: o log de fluxo na sub-rede v2 do gateway de aplicativo NSG [não tem suporte](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) no momento. Esse problema não afeta o Application Gateway v1.

**Serviços incompatíveis**: devido às limitações da plataforma atual, um pequeno conjunto de serviços do Azure não tem suporte dos logs de fluxo do NSG. A lista atual de serviços incompatíveis é
- [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/)
- [Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Práticas recomendadas

**Habilitar em VNETs/sub-redes críticas**: os logs de fluxo devem ser habilitados em todas as VNETs/sub-redes críticas em sua assinatura como uma prática recomendada de auditoria e de segurança. 

**Habilitar o log de fluxo de NSG em todos os NSGs anexados a um recurso**: o log de fluxo no Azure está configurado no recurso NSG. Um fluxo só será associado a uma regra de NSG. Em cenários em que vários NSGs são utilizados, recomendamos habilitar os logs de fluxo do NSG em todos os NSGs aplicados à sub-rede do recurso ou à interface de rede para garantir que todo o tráfego seja registrado. Para obter mais informações, consulte [como o tráfego é avaliado](../virtual-network/network-security-group-how-it-works.md) em grupos de segurança de rede. 

Alguns cenários comuns:
1. **Várias NICs em uma VM**: caso várias NICs estejam conectadas a uma máquina virtual, o log de fluxo deve ser habilitado em todas elas
1. **Ter NSG no nível de NIC e de sub-rede**: no caso de NSG ser configurado na NIC, bem como no nível de sub-rede, o registro em log de fluxo deve ser habilitado em ambos os NSGs. 

**Provisionamento de armazenamento**: o armazenamento deve ser provisionado em sintonia com o volume de log de fluxo esperado.

**Nome: o** nome do NSG deve ter até 80 caracteres e os nomes da regra NSG até 65 caracteres. Se os nomes excederem o limite de caracteres, ele poderá ficar truncado durante o log.

## <a name="troubleshooting-common-issues"></a>Solução de problemas comuns

**Não consegui habilitar os logs de fluxo do NSG**

- O provedor de recursos **Microsoft. insights** não está registrado

Se você recebeu um erro _AuthorizationFailed_ ou _GatewayAuthenticationFailed_, talvez não tenha habilitado o provedor de recursos do Microsoft Insights em sua assinatura. [Siga as instruções](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) para habilitar o provedor do Microsoft insights.

**Habilitei os logs de fluxo do NSG, mas não vejo nenhum dado em minha conta de armazenamento**

- **Hora da configuração**

Os logs de fluxo do NSG podem levar até 5 minutos para aparecer em sua conta de armazenamento (se configurados corretamente). Um PT1H.json será exibido, que pode ser acessado [conforme descrito aqui](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Nenhum tráfego em seus NSGs**

Às vezes, você não verá logs porque suas VMs não estão ativas ou há filtros upstream em um Gateway de Aplicativo ou outros dispositivos que estão bloqueando o tráfego para seus NSGs.

**Quero automatizar os logs de fluxo do NSG**

O suporte para automação por meio de modelos do Resource Manager não está disponível atualmente para logs de fluxo NSG. Leia o [anúncio do recurso](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) para obter mais informações.

## <a name="faq"></a>Perguntas frequentes

**O que os logs de fluxo do NSG fazem?**

Os recursos de rede do Azure podem ser combinados e gerenciados por meio [de NSGs (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md). Os logs de fluxo do NSG permitem que você registre informações de fluxo de 5 tuplas sobre todo o tráfego por meio de seu NSGs. Os logs de fluxo brutos são gravados em uma conta de armazenamento do Azure de onde eles podem ser processados, analisados, consultados ou exportados conforme necessário.

**O uso de logs de fluxo afeta a latência ou o desempenho da rede?**

Os dados de logs de fluxo são coletados fora do caminho do tráfego de rede e, portanto, não afetam a taxa de transferência ou latência da rede. Você pode criar ou excluir logs de fluxo sem nenhum risco de impacto no desempenho da rede.

**Como fazer usar logs de fluxo NSG com uma conta de armazenamento por trás de um firewall?**

Para usar uma conta de armazenamento por trás de um firewall, você precisa fornecer uma exceção para que os serviços confiáveis da Microsoft acessem sua conta de armazenamento:

- Navegue até a conta de armazenamento digitando o nome da conta de armazenamento na pesquisa global no portal ou na [página contas de armazenamento](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Na seção  **configurações**  , selecione  **firewalls e redes virtuais**
- Em **permitir acesso de**, selecione  **redes selecionadas**. Em  **exceções**, marque a caixa ao lado de * * * * permitir que serviços confiáveis da Microsoft acessem esta conta de armazenamento * * * *
- Se já estiver selecionada, nenhuma alteração será necessária.
- Localize o NSG de destino na [página de visão geral dos logs de fluxo do NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) e habilite os logs de fluxo do NSG com a conta de armazenamento acima selecionada.

Você pode verificar os logs de armazenamento após alguns minutos, você deve ver um carimbo de data/hora atualizado ou um novo arquivo JSON criado.

**Como fazer usar logs de fluxo NSG com uma conta de armazenamento por trás de um ponto de extremidade de serviço?**

Os logs de fluxo NSG são compatíveis com pontos de extremidade de serviço sem a necessidade de nenhuma configuração extra. Consulte o [tutorial sobre como habilitar pontos de extremidade de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) em sua rede virtual.

**Qual é a diferença entre as versões 1 & 2 dos logs de fluxo?**

Os logs de fluxo versão 2 apresentam o conceito de _estado de fluxo_ & armazena informações sobre bytes e pacotes transmitidos. [Leia mais](#log-format)

## <a name="pricing"></a>Preços

Os logs de fluxo NSG são cobrados por GB de logs coletados e vêm com uma camada gratuita de 5 GB/mês por assinatura. Para obter os preços atuais em sua região, consulte a [página de preços do observador de rede](https://azure.microsoft.com/pricing/details/network-watcher/).

O armazenamento de logs é cobrado separadamente, consulte a [página de preços do blob de blocos de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) para obter os preços relevantes.