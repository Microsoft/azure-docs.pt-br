---
title: Esquema da análise de tráfego do Azure | Microsoft Docs
description: Compreenda o esquema da análise de tráfego para analisar logs de fluxo do grupo de segurança de rede do Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 9a02a56df85c5c6aa9fd177ad42a2f9bfb303e44
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491947"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregação de esquema e os dados na análise de tráfego

Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo nas redes em nuvem. Análise de Tráfego analisa os logs de fluxo de grupo de segurança da rede (NSG) do Observador de Rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Com a Análise de Tráfego, você pode:

- Visualizar a atividade de rede nas assinaturas do Azure e identificar pontos de acesso.
- Identificar ameaças à segurança e proteger sua rede com informações como portas abertas, aplicativos tentando acesso à internet e máquinas virtuais (VM) conectando-se a redes não autorizadas.
- Compreender os padrões de fluxo de tráfego entre regiões do Azure e a internet para otimizar a implantação de rede para desempenho e capacidade.
- Identificar problemas de configuração de rede originando conexões com falha em sua rede.
- Sabe o uso de rede em bytes, pacotes ou fluxos.

### <a name="data-aggregation"></a>Agregação de dados

1. Todos os logs de fluxo em um NSG entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" são capturados em intervalos de um minuto na conta de armazenamento como blobs antes de serem processados pela análise de tráfego.
2. Intervalo de processamento de análise de tráfego padrão é 60 minutos. Isso significa que cada 60 minutos em que análise de tráfego escolhe blobs de armazenamento para a agregação.
3. Fluxos que tenham o mesmo IP de origem, IP de destino, porta de destino, nome do NSG, regra NSG, direção do fluxo e transporte de protocolo (TCP ou UDP) camada (Observação: Porta de origem é excluída para agregação) estabelecido em um único fluxo pela análise de tráfego
4. Esse registro único é decorados (detalhes na seção abaixo) e ingeridos no Log Analytics pelo tráfego Analytics.This processo poderá levar até 1 hora máx.
5. Campo FlowStartTime_t indica a primeira ocorrência de tal um fluxo agregado (mesmo quatro tuplas) no log de fluxo de processamento intervalo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Para qualquer recurso no TA, os fluxos de indicado na interface do usuário são o total vistos pelo NSG de fluxos, mas do Log Analytics usuário verá somente o registro único, reduzido. Para ver todos os fluxos, use o campo de blob_id, que pode ser referenciado de armazenamento. O fluxo total contar para que o registro corresponderá os fluxos individuais vistos no blob.

A consulta abaixo ajuda você examina todos os logs do local nos últimos 30 dias de fluxo.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Para exibir o caminho do blob para os fluxos na consulta mencionada acima, use a consulta a seguir:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

A consulta acima constrói uma URL para acessar o blob diretamente. A URL com os espaços reservados está abaixo:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campos usados no esquema de análise de tráfego

Análise de tráfego se baseia no Log Analytics, para que você possa executar consultas personalizadas nos dados decorado pela análise de tráfego e definir alertas no mesmo.

Abaixo estão os campos no esquema e o que eles significam

| Campo | Formatar | Comentários |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela de dados de tráfego Anlaytics
| SubType_s | FlowLog | Subtipo para os logs de fluxo. Usar apenas "FlowLog", outros valores de SubType_s são para o funcionamento interno do produto |
| FASchemaVersion_s |   1   | Versão do esquema. Não reflete a versão do Log de fluxo de NSG |
| TimeProcessed_t   | Data e hora em UTC  | Hora em que a análise de tráfego processada os logs de fluxo bruto da conta de armazenamento |
| FlowIntervalStartTime_t | Data e hora em UTC |  Hora de início do intervalo de processamento de log de fluxo. Isso é o tempo do qual o intervalo de fluxo é medido |
| FlowIntervalEndTime_t | Data e hora em UTC | Hora final do intervalo de processamento de log de fluxo |
| FlowStartTime_t | Data e hora em UTC |  Primeira ocorrência do fluxo (que será obter agregado) no intervalo de processamento de log de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Esse fluxo é agregado com base na lógica de agregação |
| FlowEndTime_t | Data e hora em UTC | Última ocorrência do fluxo (que será obter agregado) no intervalo de processamento de log de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Em termos de v2 de log de fluxo, esse campo contém a hora em que o último fluxo com o mesmo quatro tuplas iniciado (marcado como "B" no registro de fluxo bruto) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Privado desconhecido <br> * Desconhecido | Definição nas notas para a tabela a seguir |
| SrcIP_s | Endereço IP de origem | Estará em branco no caso de AzurePublic e ExternalPublic fluxos |
| DestIP_s | Endereço IP de destino | Estará em branco no caso de AzurePublic e ExternalPublic fluxos |
| VMIP_s | IP da VM | Usado para fluxos AzurePublic e ExternalPublic |
| PublicIP_s | Endereços IP públicos | Usado para fluxos AzurePublic e ExternalPublic |
| DestPort_d | Porta de destino | Porta na qual o tráfego é de entrada |
| L4Protocol_s  | * T <br> * U  | Protocolo de transporte. T = TCP <br> U = UDP |
| L7Protocol_s  | Nome do protocolo | Derivado de porta de destino |
| FlowDirection_s | * Eu = de entrada<br> * S = saída | Direção do fluxo no/fora do NSG, de acordo com o log de fluxo |
| FlowStatus_s  | * A = permitido pela regra NSG <br> * 1!d = negados pela regra NSG  | Status do fluxo permitido/nblocked por NSG, de acordo com o log de fluxo |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Grupo de segurança de rede (NSG) associado com o fluxo |
| NSGRules_s | \<O valor 0 de índice) >< NSG_RULENAME >\<direção de fluxo >\<Status do fluxo >\<FlowCount ProcessedByRule > |  Regra de NSG que é permitido ou negado a este fluxo |
| NSGRuleType_s | * Definidas pelo usuário * padrão |   O tipo de regra de NSG usados pelo fluxo |
| MACAddress_s | Endereço MAC | Endereço MAC da NIC em que o fluxo foi capturado |
| Subscription_s | Assinatura da rede virtual do Azure / interface de rede / máquina virtual é preenchida neste campo | Aplicável somente para FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate tipos de fluxo (tipos de fluxo em que apenas um lado é o azure) |
| Subscription1_s | ID da assinatura | ID da assinatura da rede virtual / interface de rede / máquina virtual ao qual o IP de origem no fluxo de pertence |
| Subscription2_s | ID da assinatura | ID da assinatura da rede virtual / interface de rede / máquina virtual ao qual o IP de destino no fluxo de pertence |
| Region_s | Região do Azure da rede virtual / interface de rede / máquina virtual ao qual o IP no fluxo de pertence | Aplicável somente para FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate tipos de fluxo (tipos de fluxo em que apenas um lado é o azure) |
| Region1_s | Região do Azure | Região do Azure da rede virtual / interface de rede / máquina virtual ao qual o IP de origem no fluxo de pertence |
| Region2_s | Região do Azure | Região do Azure da rede virtual ao qual o IP de destino no fluxo de pertence |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Associado à VM enviar ou receber o tráfego NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associada com o IP de origem no fluxo |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Placa de rede associada com o IP de destino no fluxo |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Máquina virtual associada a interface de rede NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina virtual associada com o IP de origem no fluxo |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina virtual associada com o IP de destino no fluxo |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associada a NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Subrede associado com o IP de origem no fluxo |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Subrede associado com o IP de destino no fluxo |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicativo associado com o IP de origem no fluxo |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicativo associado com o IP de destino no fluxo |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balanceador de carga associada com o IP de origem no fluxo |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balanceador de carga associado com o IP de destino no fluxo |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associada com o IP de origem no fluxo |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado com o IP de destino no fluxo |
| ConnectionType_s | Os valores possíveis são VNetPeering, VpnGateway e ExpressRoute |    Tipo de Conexão |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nome da Conexão |
| ConnectingVNets_s | Lista separada por espaço de nomes de rede virtual | No caso de topologia de hub e spoke, redes virtuais de hub serão preenchidas aqui |
| Country_s | Duas letras do código do país (ISO 3166-1 alpha-2) | Populado para o tipo de fluxo ExternalPublic. Todos os endereços IP no campo PublicIPs_s compartilharão o mesmo código de país |
| AzureRegion_s | Locais de região do Azure | Populado para o tipo de fluxo AzurePublic. Todos os endereços IP no campo PublicIPs_s compartilharão a região do Azure |
| AllowedInFlows_d | | Contagem de fluxos de entrada que eram permitidas. Isso representa o número de fluxos que compartilhou a mesma quatro tuplas de entrada para a interface netweork em que o fluxo foi capturado |
| DeniedInFlows_d |  | Contagem de fluxos de entrada que foram negadas. (De entrada para o adaptador de rede em que o fluxo foi capturado) |
| AllowedOutFlows_d | | Contagem de fluxos de saída que eram permitidas (saída para o adaptador de rede em que o fluxo foi capturado) |
| DeniedOutFlows_d  | | Contagem de fluxos de saída que foram negados (saída para o adaptador de rede em que o fluxo foi capturado) |
| FlowCount_d | Preterido. Total de fluxos que corresponderam a mesma quatro tuplas. No caso de tipos de fluxo ExternalPublic e AzurePublic, a contagem incluirá os fluxos de vários endereços de PublicIP.
| InboundPackets_d | Pacotes recebidos como capturados na interface de rede em que a regra NSG foi aplicada | Esse campo é preenchido somente para o esquema de log de fluxo da versão 2 do NSG |
| OutboundPackets_d  | Pacotes enviados como capturados na interface de rede em que a regra NSG foi aplicada | Esse campo é preenchido somente para o esquema de log de fluxo da versão 2 do NSG |
| InboundBytes_d |  Bytes recebidos como capturados na interface de rede em que a regra NSG foi aplicada | Esse campo é preenchido somente para o esquema de log de fluxo da versão 2 do NSG |
| OutboundBytes_d | Bytes enviados como capturados na interface de rede em que a regra NSG foi aplicada | Esse campo é preenchido somente para o esquema de log de fluxo da versão 2 do NSG |
| CompletedFlows_d  |  | Esse campo é preenchido com um valor diferente de zero somente para o esquema de log de fluxo da versão 2 do NSG |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separados por barras |

### <a name="notes"></a>Observações

1. No caso de fluxos AzurePublic e ExternalPublic, o cliente já possuía que IP da VM do Azure é preenchida no campo de VMIP_s, enquanto os endereços IP públicos estão sendo preenchidos no campo PublicIPs_s. Para esses tipos de fluxo de dois, devemos usar VMIP_s e PublicIPs_s em vez de campos SrcIP_s e DestIP_s. Para endereços AzurePublic e ExternalPublicIP, podemos agregar Além disso, para que o número de registros ingeridos ao espaço de trabalho do cliente log analytics é mínimo. (Esse campo será preterido em breve e devemos usar SrcIP_ e DestIP_s dependendo se a VM do azure foi a origem ou o destino no fluxo de)
1. Detalhes de tipos de fluxo: Com base nos endereços IP envolvidos no fluxo, podemos categorizar os fluxos para os seguintes tipos de fluxo:
1. IntraVNet – os endereços IP no fluxo de residam na mesma rede Virtual do Azure.
1. InterVNet - endereços IP no fluxo de residem em diferentes duas redes virtuais do Azure.
1. S2S – (Site a Site) de um dos endereços IP pertence a rede Virtual do Azure, enquanto o outro endereço IP pertence à rede de cliente (Site) conectado à rede Virtual do Azure por meio do gateway VPN ou rota expressa.
1. P2S - (ponto a Site) um dos endereços IP pertence à rede Virtual do Azure, enquanto o outro endereço IP pertence à rede de cliente (Site) conectado à rede Virtual do Azure por meio do gateway VPN.
1. AzurePublic - um dos endereços IP pertence a rede Virtual do Azure enquanto o outro endereço IP pertence a endereços IP públicos interno do Azure de propriedade da Microsoft. Endereços IP públicos não fará parte desse tipo de fluxo de propriedade do cliente. Por exemplo, qualquer cliente pertence a VM enviando tráfego para um serviço do Azure (ponto de extremidade de armazenamento) seria categorizado nesse tipo de fluxo.
1. ExternalPublic - um dos endereços IP pertence à rede Virtual do Azure enquanto o outro endereço IP é um IP público que não está no Azure, não será relatado como mal-intencionado pelos feeds ASC consome de análise de tráfego para o intervalo de processamento entre " FlowIntervalStartTime_t"e"FlowIntervalEndTime_t".
1. MaliciousFlow - um dos endereços IP pertencem a rede virtual do azure enquanto o outro endereço IP é um IP público que não está no Azure e é relatado como mal-intencionado pelos feeds ASC consome de análise de tráfego para o intervalo de processamento entre" FlowIntervalStartTime_t"e"FlowIntervalEndTime_t".
1. UnknownPrivate - um dos endereços IP pertencer a rede Virtual do Azure enquanto o outro endereço IP pertence ao intervalo de IP privado, conforme definido na RFC 1918 e não pôde ser mapeado pela análise de tráfego para um site ou rede Virtual do Azure de propriedade do cliente.
1. Desconhecido – não é possível mapear a qualquer um do IP endereços nos fluxos com a topologia de cliente no Azure, bem como local (site).
1. Alguns nomes de campo são acrescentados com s ou _d. Eles não significam a origem e destino.

### <a name="next-steps"></a>Próximas etapas
Para obter respostas para perguntas frequentes, consulte [perguntas frequentes sobre análise de tráfego](traffic-analytics-faq.md) para ver detalhes sobre a funcionalidade, consulte [documentação da análise de tráfego](traffic-analytics.md)
