---
title: Esquema de análise de tráfego do Azure | Microsoft Docs
description: Entenda o esquema de Análise de Tráfego para analisar os logs de fluxo do grupo de segurança de rede do Azure.
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
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 765a2728c5de49787a64fff723625e53e100e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98017775"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregação de esquema e dados no Análise de Tráfego

Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo nas redes em nuvem. Análise de Tráfego analisa os logs de fluxo de grupo de segurança da rede (NSG) do Observador de Rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Com a Análise de Tráfego, você pode:

- Visualizar a atividade de rede nas assinaturas do Azure e identificar pontos de acesso.
- Identificar ameaças à segurança e proteger sua rede com informações como portas abertas, aplicativos tentando acesso à internet e máquinas virtuais (VM) conectando-se a redes não autorizadas.
- Compreender os padrões de fluxo de tráfego entre regiões do Azure e a internet para otimizar a implantação de rede para desempenho e capacidade.
- Identificar problemas de configuração de rede originando conexões com falha em sua rede.
- Conheça o uso da rede em bytes, pacotes ou fluxos.

### <a name="data-aggregation"></a>Agregação de dados

1. Todos os logs de fluxo em um NSG entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" são capturados em intervalos de um minuto na conta de armazenamento como BLOBs antes de serem processados pelo Análise de Tráfego.
2. O intervalo de processamento padrão de Análise de Tráfego é de 60 minutos. Isso significa que a cada 60 minutos Análise de Tráfego escolhe os BLOBs do armazenamento para agregação. Se o intervalo de processamento escolhido for 10 minutos, Análise de Tráfego escolherá os BLOBs da conta de armazenamento após cada 10 minutos.
3. Os fluxos que têm o mesmo IP de origem, IP de destino, porta de destino, nome NSG, regra NSG, direção do fluxo e protocolo TCP ou UDP (Observação: a porta de origem é excluído para agregação) são Clubbed em um único fluxo por Análise de Tráfego
4. Este registro único é decorado (detalhes na seção abaixo) e ingerido em Log Analytics por Análise de Tráfego. esse processo pode levar até 1 hora no máximo.
5. FlowStartTime_t campo indica a primeira ocorrência desse fluxo agregado (mesmo quatro tupla) no intervalo de processamento do log de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Para qualquer recurso em TA, os fluxos indicados na interface do usuário são fluxos totais vistos pelo NSG, mas no Log Analytics usuário verá apenas o registro único e reduzido. Para ver todos os fluxos, use o campo blob_id, que pode ser referenciado do armazenamento. A contagem total de fluxo para esse registro corresponderá aos fluxos individuais vistos no BLOB.

A consulta abaixo ajuda a examinar todas as sub-redes que interagem com IPs públicos não Azure nos últimos 30 dias.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
Para exibir o caminho do blob para os fluxos na consulta mencionada acima, use a consulta abaixo:

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

A consulta acima constrói uma URL para acessar o blob diretamente. A URL com espaço em espera está abaixo de:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campos usados no esquema de Análise de Tráfego
  > [!IMPORTANT]
  > O esquema de Análise de Tráfego foi atualizado em 22 de agosto de 2019. O novo esquema fornece IPs de origem e de destino, removendo separadamente a necessidade de analisar o campo FlowDirection fazendo consultas mais simples. </br>
  > FASchemaVersion_s atualizado de 1 para 2. </br>
  > Campos preteridos: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Novos campos: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Os campos preteridos estarão disponíveis até 22 de novembro de 2019.

Análise de Tráfego é criado sobre Log Analytics, para que você possa executar consultas personalizadas em dados decorados por Análise de Tráfego e definir alertas no mesmo.

Listados abaixo estão os campos no esquema e o que eles significam

| Campo | Formatar | Comentários |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela para dados de Análise de Tráfego
| SubType_s | Log | Subtipo para os logs de fluxo. Use apenas "log", outros valores de SubType_s são para o funcionamento interno do produto |
| FASchemaVersion_s |   2   | Versão do esquema. Não reflete a versão do log de fluxo NSG |
| TimeProcessed_t   | Data e hora em UTC  | Hora em que o Análise de Tráfego processou os logs de fluxo brutos da conta de armazenamento |
| FlowIntervalStartTime_t | Data e hora em UTC |  Hora de início do intervalo de processamento do log de fluxo. Essa é a hora em que o intervalo de fluxo é medido |
| FlowIntervalEndTime_t | Data e hora em UTC | Hora de término do intervalo de processamento do log de fluxo |
| FlowStartTime_t | Data e hora em UTC |  Primeira ocorrência do fluxo (que será agregada) no intervalo de processamento do log de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Esse fluxo é agregado com base na lógica de agregação |
| FlowEndTime_t | Data e hora em UTC | Última ocorrência do fluxo (que será agregada) no intervalo de processamento do log de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Em termos de log de fluxo v2, esse campo contém a hora em que o último fluxo com a mesma quatro tupla foi iniciado (marcado como "B" no registro de fluxo bruto) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Particular desconhecido <br> * Desconhecido | Definição nas observações abaixo da tabela |
| SrcIP_s | Endereço IP de origem | Ficará em branco no caso de fluxos AzurePublic e ExternalPublic |
| DestIP_s | Endereço IP de destino | Ficará em branco no caso de fluxos AzurePublic e ExternalPublic |
| VMIP_s | IP da VM | Usado para fluxos AzurePublic e ExternalPublic |
| PublicIP_s | Endereços IP públicos | Usado para fluxos AzurePublic e ExternalPublic |
| DestPort_d | Porta de destino | Porta na qual o tráfego é recebido |
| L4Protocol_s  | * T <br> * U  | Protocolo de transporte. T = TCP <br> U = UDP |
| L7Protocol_s  | Nome do protocolo | Derivado da porta de destino |
| FlowDirection_s | * I = entrada<br> * O = saída | Direção do fluxo de entrada/saída de NSG como por log de fluxo |
| FlowStatus_s  | * A = permitido pela regra NSG <br> * D = negado pela regra NSG  | Status do fluxo permitido/nblocked por NSG como por log de fluxo |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | NSG (grupo de segurança de rede) associado ao fluxo |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  Regra NSG que permitia ou negou este fluxo |
| NSGRule_s | NSG_RULENAME |  Regra NSG que permitia ou negou este fluxo |
| NSGRuleType_s | * Padrão definido pelo usuário * |   O tipo de regra NSG usada pelo fluxo |
| MACAddress_s | Endereço MAC | Endereço MAC da NIC na qual o fluxo foi capturado |
| Subscription_s | A assinatura da rede virtual/interface de rede/máquina virtual do Azure é populada neste campo | Aplicável somente para Flowtype = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e tipos de fluxo de UnknownPrivate (tipos de fluxo em que apenas um lado é o Azure) |
| Subscription1_s | ID da assinatura | ID de assinatura da rede virtual/interface de rede/máquina virtual à qual o IP de origem no fluxo pertence |
| Subscription2_s | ID da assinatura | ID de assinatura da rede virtual/interface de rede/máquina virtual à qual o IP de destino no fluxo pertence |
| Region_s | Região do Azure da rede virtual/interface de rede/máquina virtual à qual o IP no fluxo pertence | Aplicável somente para Flowtype = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e tipos de fluxo de UnknownPrivate (tipos de fluxo em que apenas um lado é o Azure) |
| Region1_s | Região do Azure | Região do Azure da rede virtual/interface de rede/máquina virtual à qual o IP de origem no fluxo pertence |
| Region2_s | Região do Azure | Região do Azure da rede virtual à qual o IP de destino no fluxo pertence |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  NIC associada à VM que envia ou recebe o tráfego |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associada ao IP de origem no fluxo |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC associada ao IP de destino no fluxo |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Máquina virtual associada à interface de rede NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina virtual associada ao IP de origem no fluxo |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Máquina virtual associada ao IP de destino no fluxo |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associada ao NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sub-rede associada ao IP de origem no fluxo |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Sub-rede associada ao IP de destino no fluxo |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicativo associado ao IP de origem no fluxo |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Gateway de aplicativo associado ao IP de destino no fluxo |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balanceador de carga associado ao IP de origem no fluxo |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Balanceador de carga associado ao IP de destino no fluxo |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao IP de origem no fluxo |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao IP de destino no fluxo |
| ConnectionType_s | Os valores possíveis são VNetPeering, VpnGateway e ExpressRoute |    Tipo de Conexão |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nome da conexão. Para flowtype P2S, ele será formatado como <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | Lista separada por espaços de nomes de rede virtual | No caso de topologia hub e spoke, as redes virtuais de Hub serão preenchidas aqui |
| Country_s | Código do país de duas letras (ISO 3166-1 Alpha-2) | Preenchido para o tipo de fluxo ExternalPublic. Todos os endereços IP no campo PublicIPs_s compartilharão o mesmo código de país |
| AzureRegion_s | Locais de região do Azure | Preenchido para o tipo de fluxo AzurePublic. Todos os endereços IP no campo PublicIPs_s compartilharão a região do Azure |
| AllowedInFlows_d | | Contagem de fluxos de entrada que foram permitidos. Isso representa o número de fluxos que compartilharam a mesma entrada de quatro tuplas para a interface de rede na qual o fluxo foi capturado |
| DeniedInFlows_d |  | Contagem de fluxos de entrada que foram negados. (Entrada para a interface de rede na qual o fluxo foi capturado) |
| AllowedOutFlows_d | | Contagem de fluxos de saída que foram permitidos (saída para a interface de rede na qual o fluxo foi capturado) |
| DeniedOutFlows_d  | | Contagem de fluxos de saída que foram negados (saída para a interface de rede na qual o fluxo foi capturado) |
| FlowCount_d | Preterido. Total de fluxos que corresponderam à mesma quatro tupla. No caso de tipos de fluxo ExternalPublic e AzurePublic, a contagem incluirá os fluxos de vários endereços de PublicIP também.
| InboundPackets_d | Pacotes recebidos como capturados na interface de rede em que a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo do NSG |
| OutboundPackets_d  | Pacotes enviados como capturados na interface de rede em que a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo do NSG |
| InboundBytes_d |  Bytes recebidos como capturados na interface de rede em que a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo do NSG |
| OutboundBytes_d | Bytes enviados conforme capturados na interface de rede em que a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo do NSG |
| CompletedFlows_d  |  | Isso é preenchido com valor diferente de zero somente para a versão 2 do esquema de log de fluxo NSG |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separadas por barras |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separadas por barras |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Entradas separadas por barras |

### <a name="notes"></a>Observações

1. No caso de fluxos AzurePublic e ExternalPublic, o IP de VM do Azure pertencente ao cliente é preenchido no campo VMIP_s, enquanto os endereços IP públicos estão sendo preenchidos no campo PublicIPs_s. Para esses dois tipos de fluxo, devemos usar VMIP_s e PublicIPs_s em vez dos campos SrcIP_s e DestIP_s. Para endereços AzurePublic e ExternalPublicIP, agregamos mais, para que o número de registros ingeridos no espaço de trabalho do log Analytics do cliente seja mínimo. (Esse campo será preterido em breve e deve estar usando SrcIP_ e DestIP_s dependendo se a VM do Azure era a origem ou o destino no fluxo)
1. Detalhes para tipos de fluxo: com base nos endereços IP envolvidos no fluxo, categorizamos os fluxos em para os seguintes tipos de fluxo:
1. IntraVNet – ambos os endereços IP no fluxo residem na mesma rede virtual do Azure.
1. InterVNet-os endereços IP no fluxo residem nas duas redes virtuais do Azure diferentes.
1. S2S – (site a site) um dos endereços IP pertence à rede virtual do Azure enquanto o outro endereço IP pertence à rede do cliente (site) conectada à rede virtual do Azure por meio do gateway de VPN ou da rota expressa.
1. P2S-(ponto a site) um dos endereços IP pertence à rede virtual do Azure enquanto o outro endereço IP pertence à rede do cliente (site) conectada à rede virtual do Azure por meio do gateway de VPN.
1. AzurePublic-um dos endereços IP pertence à rede virtual do Azure, enquanto o outro endereço IP pertence aos endereços IP públicos internos do Azure de propriedade da Microsoft. Os endereços IP públicos de Propriedade do cliente não fazem parte desse tipo de fluxo. Por exemplo, qualquer VM de Propriedade do cliente enviando tráfego para um serviço do Azure (ponto de extremidade de armazenamento) seria categorizada sob esse tipo de fluxo.
1. ExternalPublic-um dos endereços IP pertence à rede virtual do Azure, enquanto o outro endereço IP é um IP público que não está no Azure, não é relatado como mal-intencionado nos feeds ASC que Análise de Tráfego consome para o intervalo de processamento entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. MaliciousFlow-um dos endereços IP pertence à rede virtual do Azure, enquanto o outro endereço IP é um IP público que não está no Azure e é relatado como mal-intencionado nos feeds ASC que Análise de Tráfego consome para o intervalo de processamento entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. UnknownPrivate-um dos endereços IP pertence à rede virtual do Azure enquanto o outro endereço IP pertence ao intervalo de IP privado, conforme definido no RFC 1918 e não pôde ser mapeado por Análise de Tráfego para um site de Propriedade do cliente ou para uma rede virtual do Azure.
1. Desconhecido – não é possível mapear um dos endereços IP nos fluxos com a topologia do cliente no Azure, bem como no local (site).
1. Alguns nomes de campo são anexados com \_ s ou \_ d. Elas não significam origem e destino, mas indicam a cadeia de caracteres de tipos de dados e decimal respectivamente.

### <a name="next-steps"></a>Próximas etapas
Para obter respostas para perguntas frequentes, consulte [perguntas frequentes sobre análise de tráfego](traffic-analytics-faq.md) para ver detalhes sobre a funcionalidade, consulte [documentação da análise de tráfego](traffic-analytics.md)
