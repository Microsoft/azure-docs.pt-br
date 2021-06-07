---
title: Perguntas frequentes-Azure Load Balancer
description: Respostas para perguntas frequentes sobre o Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3752a36d22f879b95b02bd49436be78212fe56a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576034"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer perguntas frequentes

## <a name="what-types-of-load-balancer-exist"></a>Quais tipos de Load Balancer existem?
Balanceadores de carga internos que equilibram o tráfego em uma VNET e balanceadores de carga externos que balanceam o tráfego de e para um ponto de extremidade conectado à Internet. Para obter mais informações, consulte [tipos de Load Balancer](components.md#frontend-ip-configurations). 

Para ambos os tipos, o Azure oferece uma SKU básica e uma SKU standard que têm diferentes recursos funcionais, de desempenho, de segurança e de controle de integridade. Essas diferenças são explicadas em nosso artigo de [comparação de SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Como posso atualizar de um básico para um Standard Load Balancer?
Consulte o artigo [Atualizar do básico para o padrão](upgrade-basic-standard.md) para um script automatizado e orientação sobre como atualizar um SKU Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Quais são as diferentes opções de balanceamento de carga no Azure?
Consulte o [Guia de tecnologia do balanceador de carga](/azure/architecture/guide/technology-choices/load-balancing-overview)  para obter os serviços de balanceamento de carga disponíveis e os usos recomendados para cada um.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Onde posso encontrar Load Balancer modelos de ARM?
Consulte a [lista de modelos de início rápido de Azure Load Balancer](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) para modelos de ARM de implantações comuns.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Como as regras NAT de entrada são diferentes das regras de balanceamento de carga?
As regras de NAT são usadas para especificar um recurso de back-end para rotear o tráfego. Por exemplo, configurar uma porta de balanceador de carga específica para enviar o tráfego de RDP para uma VM específica. As regras de balanceamento de carga são usadas para especificar um pool de recursos de back-end para rotear o tráfego, equilibrando a carga em cada instância. Por exemplo, uma regra de balanceador de carga pode rotear pacotes TCP na porta 80 do balanceador de carga em um pool de servidores Web.

## <a name="what-is-ip-1686312916"></a>O que é o 168.63.129.16 de IP?
O endereço IP virtual para o host marcado como a infraestrutura do Azure Load Balancer onde as investigações de integridade do Azure se originam. Ao configurar instâncias de back-end, elas devem permitir o tráfego desse endereço IP para responder com êxito às investigações de integridade. Essa regra não interage com o acesso ao seu front-end Load Balancer. Se você não estiver usando o Azure Load Balancer, poderá substituir essa regra. Você pode aprender mais sobre marcas de serviço [aqui](../virtual-network/service-tags-overview.md#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Posso usar o emparelhamento VNet global com Load Balancer básica?
Não. O Load Balancer básico não dá suporte ao emparelhamento VNET global. Em vez disso, você pode usar um Standard Load Balancer. Consulte o artigo [Atualizar do básico para o padrão](upgrade-basic-standard.md) para atualização direta.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Como posso descobrir o IP público que uma VM do Azure usa?

Há várias maneiras de determinar o endereço IP público de uma conexão de saída. O OpenDNS fornece um serviço que pode mostrar o endereço IP público de sua VM.
Usando o comando nslookup, você pode enviar uma consulta DNS para o nome myip.opendns.com para o resolvedor do OpenDNS. O serviço retornará o endereço IP de origem que foi usado para enviar a consulta. Quando você executa a consulta a seguir de sua VM, a resposta será o IP público usado para essa VM:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Posso adicionar uma VM do mesmo conjunto de disponibilidade a diferentes pools de back-end de uma Load Balancer?
Não, isso não é possível.

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>Qual é a taxa de transferência máxima de dados que pode ser obtida por meio de um Azure Load Balancer?
Como o Azure LB é um balanceador de carga de rede de passagem, as limitações de taxa de transferência são ditadas pelo tipo de máquina virtual usada no pool de back-end. Para saber mais sobre outras informações relacionadas à taxa de transferência de rede, consulte [taxa de transferência de rede de máquina virtual](../virtual-network/virtual-machine-network-throughput.md).

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Como funcionam as conexões com o armazenamento do Azure na mesma região?
Não é necessário ter conectividade de saída por meio dos cenários acima para se conectar ao Armazenamento na mesma região que a VM. Se você não quiser isso, use NSGs (grupos de segurança de rede), conforme explicado acima. Para a conectividade com o armazenamento em outras regiões, a conectividade de saída é necessária. Observe que, ao se conectar ao Armazenamento de uma VM na mesma região, o endereço IP de origem nos logs de diagnóstico de Armazenamento será um endereço de provedor interno e não o endereço IP público da VM. Se você quiser restringir o acesso à sua conta de armazenamento a VMs em uma ou mais sub-redes de rede virtual na mesma região, use [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) (e não seu endereço IP público) ao configurar o firewall da conta de armazenamento. Depois que os pontos de extremidade de serviço forem configurados, você verá seu endereço IP privado da rede virtual nos logs de diagnóstico de armazenamento e não no endereço do provedor interno.

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>Azure Load Balancer dá suporte à terminação TLS/SSL?
Não, Azure Load Balancer atualmente não dá suporte à terminação, pois é uma passagem pelo balanceador de carga de rede. O gateway de aplicativo pode ser uma solução potencial se seu aplicativo exigir isso.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Quais são as melhores práticas em relação à conectividade de saída?
Standard Load Balancer e IP público padrão introduz recursos e comportamentos diferentes para a conectividade de saída. Eles não são o mesmo que SKUs Básicos. Se quiser ter conectividade de saída ao trabalhar com SKUs Standard, você precisará defini-la explicitamente com endereços IP públicos Standard ou com o Load Balancer Standard público. Isso inclui a criação da conectividade de saída ao usar um Standard Load Balancer interno. É recomendável que você sempre use regras de saída em um Load Balancer Standard público. Isso significa que, quando um Standard Load Balancer for usado, você precisará executar etapas para criar a conectividade de saída para as VMs no pool de back-end se a conectividade de saída for desejada. No contexto de conectividade de saída, uma única VM autônoma, todas as VMs em um conjunto de disponibilidade, todas as instâncias em um VMSS se comportam como um grupo. Isso significa que, se uma única VM em um conjunto de disponibilidade estiver associada a um SKU Standard, todas as instâncias de VM neste conjunto de disponibilidade agora se comportarão de acordo com as mesmas regras que se aplicariam se elas estivessem associados ao SKU Standard, mesmo se uma instância individual não estivesse diretamente associada a ele. Esse comportamento também é observado no caso de uma VM autônoma com várias placas adaptadoras de rede anexadas a um balanceador de carga. Se uma NIC for adicionada como autônoma, ela terá o mesmo comportamento. Revise atentamente todo este documento para entender os conceitos gerais, revise [Standard Load Balancer](./load-balancer-overview.md) para ver as diferenças entre as SKUs e revise [regras de saída](load-balancer-outbound-connections.md#outboundrules).
Usar regras de saída permite ter um controle refinado sobre todos os aspectos da conectividade de saída.
 
## <a name="next-steps"></a>Próximas etapas
Se sua pergunta não estiver listada acima, envie comentários sobre esta página com sua pergunta. Isso criará um problema do GitHub para a equipe de produto, a fim de garantir que todas as perguntas importantes do cliente sejam respondidas.
