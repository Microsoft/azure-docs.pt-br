---
title: Tipos de endereço IP no Azure (Clássico)
titlesuffix: Azure Virtual Network
description: Saiba mais sobre endereços IP (clássicos) públicos e privados no Azure.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 9e7a5772dd1e10abf43eddf0548833d625ecfb24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742103"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Tipos de endereço IP e métodos de alocação (clássico) no Azure
Você pode atribuir endereços IP aos recursos do Azure para se comunicar com outros recursos do Azure, sua rede local e a Internet. Há dois tipos de endereços IP que você pode usar no Azure: público e privado.

Endereços IP públicos são usados para comunicação com a Internet, incluindo serviços públicos do Azure.

Endereços IP privados são usados para comunicação em uma VNet (rede virtual) do Azure, um serviço de nuvem e na sua rede local quando você usa um gateway de VPN ou circuito de ExpressRoute para estender sua rede para o Azure.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../resource-manager-deployment-model.md).  Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o Gerenciador de Recursos. Saiba mais sobre endereços IP no Gerenciador de Recursos lendo o artigo [endereços IP](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Endereços IP públicos
Endereços IP públicos permitem que os recursos do Azure comuniquem-se com a internet e com os serviços públicos do Azure como o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/), [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [Banco de dados SQL](../sql-database/sql-database-technical-overview.md), e [Armazenamento do Azure](../storage/common/storage-introduction.md).

Um endereço IP público é associado aos seguintes tipos de recursos:

* Serviços de Nuvem
* VMs (Máquinas Virtuais) de IaaS
* Instâncias de função de PaaS
* Gateways VPN
* Application gateways

### <a name="allocation-method"></a>Método de alocação
Quando precisa de um endereço IP público precisa ser atribuído a um recurso do Azure, ele é *dinamicamente* alocado de um pool de endereço IP público disponível no local em que o recurso é criado. Esse endereço IP é liberado quando o recurso é interrompido. Com um serviço de nuvem, isso acontece quando todas as instâncias de função são interrompidas, o que pode ser evitado usando um endereço IP *estático* (reservado) (consulte [Serviços de Nuvem](#cloud-services)).

> [!NOTE]
> A lista de intervalos IP do qual os endereços IP públicos são alocados a recursos do Azure é publicada em [intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Resolução de nome de host DNS
Quando você cria um serviço de nuvem ou em uma VM de IaaS, precisa fornecer um nome DNS do serviço de nuvem que seja exclusivo entre todos os recursos no Azure. Isso cria um mapeamento nos servidores DNS gerenciados do Azure para *dnsname*. cloudapp.net para o endereço IP público do recurso. Por exemplo, ao criar um serviço de nuvem com um nome DNS do serviço de nuvem de **contoso**, o FQDN (nome de domínio totalmente qualificado) **contoso.cloudapp.net** será resolvido para um endereço VIP (IP público) do serviço de nuvem. Você pode usar o FQDN para criar um registro CNAME do domínio personalizado apontando para o endereço IP público no Azure.

### <a name="cloud-services"></a>Serviços de Nuvem
Um serviço de nuvem sempre tem um endereço IP público, conhecido como VIP (endereço IP virtual). Você pode criar pontos de extremidade em um serviço de nuvem para associar portas diferentes no VIP para portas internas em VMs e instâncias de função no serviço de nuvem. 

Um serviço de nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, todos expostos por meio do mesmo VIP de serviço de nuvem. Você também pode atribuir [vários VIPs a um serviço de nuvem](../load-balancer/load-balancer-multivip.md), o que permite cenários multiVIP como um ambiente multilocatário com sites baseados em SSL.

Você pode garantir que o endereço IP público de um serviço de nuvem permaneça igual, mesmo quando todas as instâncias de função são interrompidas, usando um endereço IP público *estático* , conhecido como [IP Reservado](virtual-networks-reserved-public-ip.md). Você pode criar um recurso IP (reservado) estático em um local específico e atribuí-lo a qualquer serviço de nuvem naquele local. Não é possível especificar o endereço IP real para o IP reservado, ele é alocado do pool de endereços IP disponíveis no local em que é criado. Esse endereço IP não é liberado até você explicitamente excluí-lo.

Endereços IP públicos (reservados) estáticos são usados nos cenários em que um serviço de nuvem:

* requer regras de firewall para ser configurado por usuários finais.
* depende da resolução de nome DNS externa, e um IP dinâmico exigiria atualizar registros A.
* consome serviços Web externos que usam o modelo de segurança com base em IP.
* usa certificados SSL vinculados a um endereço IP.

> [!NOTE]
> Quando você cria uma VM clássica, um contêiner *serviço de nuvem* é criado pelo Azure, que tem um endereço IP virtual (VIP). Quando a criação é feita por meio do portal, um padrão de *ponto de extremidade* RDP ou SSH é configurado pelo portal para conectar-se à VM por meio do VIP de serviço de nuvem. Este VIP de serviço de nuvem pode ser reservado, o que efetivamente fornece um endereço IP reservado para se conectar à VM. Você pode abrir portas adicionais configurando mais pontos de extremidade.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>VMs de IaaS e instâncias de função PaaS
É possível atribuir um endereço IP público diretamente a uma [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) IaaS ou a uma instância de função PaaS em um serviço de nuvem. Isso é conhecido como[ILPIP](virtual-networks-instance-level-public-ip.md)(endereço IP público em nível de instância). Esse endereço IP público pode ser apenas dinâmico.

> [!NOTE]
> Isso é diferente do VIP do serviço em nuvem, que é um contêiner para VMs de instâncias de função IaaS ou PaaS, como um serviço de nuvem pode conter várias VMs de IaaS ou instâncias de função de PaaS, expostos por meio do mesmo VIP de serviço de nuvem.
> 
> 

### <a name="vpn-gateways"></a>Gateways VPN
Um [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) pode ser usado para conectar uma VNet do Azure a outra VNet do Azure ou a redes locais. Um gateway de VPN recebe um endereço IP público *dinamicamente*, que permite a comunicação com a rede remota.

### <a name="application-gateways"></a>Application gateways
O [Application Gateway](../application-gateway/application-gateway-introduction.md) do Azure pode ser usado para balanceamento de carga Layer7 para rotear tráfego de rede com base em HTTP. O Application Gateway recebe um endereço IP público *dinamicamente*, que serve como o VIP com balanceamento de carga.

### <a name="at-a-glance"></a>Visão rápida
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e capacidade de atribuir vários endereços IP públicos.

| Resource | Dinâmico | estático | Vários endereços IP |
| --- | --- | --- | --- |
| serviço de nuvem |Sim |sim |Sim |
| Instância de função de PaaS ou VM de IaaS |Sim |Não |Não  |
| gateway de VPN |Sim |Não |Não  |
| Gateway de Aplicativo |Sim |Não |Não  |

## <a name="private-ip-addresses"></a>Endereços IP privados
Endereços IP privados permitem que os recursos do Azure comuniquem-se com outros recursos em um serviço de nuvem ou em uma VNet ( [rede virtual](virtual-networks-overview.md)), ou na rede local (por meio de um gateway de VPN ou circuito de ExpressRoute), sem usar um endereço IP acessível pela Internet.

No modelo de implantação clássico do Azure, um endereço IP privado pode ser atribuído aos seguintes recursos do Azure:

* VMs de IaaS e instâncias de função PaaS
* Balanceador de carga interno
* Gateway de Aplicativo

### <a name="iaas-vms-and-paas-role-instances"></a>VMs de IaaS e instâncias de função PaaS
As VMs (máquinas virtuais) criadas com o modelo de implantação clássico sempre são colocadas em um serviço de nuvem, semelhante às instâncias de função de PaaS. O comportamento de endereços IP privados, portanto, é semelhante para esses recursos.

É importante observar que um serviço de nuvem pode ser implantado de duas maneiras:

* Como um serviço de nuvem *autônomo* , em que ele não está dentro de uma rede virtual.
* Como parte de uma rede virtual.

#### <a name="allocation-method"></a>Método de alocação
No caso de um serviço de nuvem *autônomo*, os recursos recebem um endereço IP privado alocado *dinamicamente* do intervalo de endereços IP privados do datacenter do Azure. Ele pode ser usado apenas para comunicação com outras VMs no mesmo serviço de nuvem. Esse endereço IP pode mudar quando o recurso for interrompido e iniciado.

No caso de um serviço de nuvem implantado em uma rede virtual, os recursos recebem endereços IP privados do intervalo de endereços das sub-redes associadas (conforme especificado na configuração de rede). Esses endereços IP privados podem ser usados para comunicação entre todas as VMs na VNet.

Além disso, no caso de serviços de nuvem em uma rede virtual, um endereço IP privado é alocado *dinamicamente* (usando DHCP) por padrão. Ele pode mudar quando o recurso for interrompido e iniciado. Para garantir que o endereço IP permaneça o mesmo, defina o método de alocação para *estático*e forneça um endereço IP válido dentro do intervalo de endereços correspondente.

Os endereços IP privados estáticos costumam ser usados para:

* VMs que atuam como controladores de domínio ou servidores DNS.
* VMs que exigem regras de firewall usando endereços IP.
* VMs executando serviços acessados por outros aplicativos por meio de um endereço IP.

#### <a name="internal-dns-hostname-resolution"></a>Resolução do nome do host DNS interno
Todas as VMs do Azure e instâncias de função de PaaS são configuradas com [servidores DNS gerenciados pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você explicitamente configure servidores DNS personalizados. Esses servidores DNS fornecem resolução de nomes interna para VMs e instâncias de função que residem no mesmo serviço de nuvem ou rede virtual.

Quando você cria uma VM, um mapeamento para o nome de host para seu endereço IP privado é adicionado aos servidores DNS gerenciados pelo Azure. No caso de uma VM com várias NICs, o nome do host é mapeado para o endereço IP privado da NIC principal. No entanto, essa informação de mapeamento é restrita a recursos dentro do mesmo serviço de nuvem ou rede virtual.

No caso de um serviço de nuvem *autônomo* , você será capaz de resolver nomes de host de todas as instâncias de VMs/função dentro do mesmo serviço de nuvem apenas. No caso de um serviço de nuvem em uma rede virtual, você será capaz de resolver nomes de host de todas as instâncias de função/VMs na rede virtual.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILB) e gateways de aplicativo
Você pode atribuir um endereço IP privado para a configuração de **front end** de um [balanceador de carga interno do Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) ou um [Gateway de Aplicativo do Azure](../application-gateway/application-gateway-introduction.md). Esse endereço IP privado serve como ponto de extremidade interno, acessível somente aos recursos dentro da sua rede virtual (VNet) e de redes remotas conectadas à VNet. Você pode atribuir a um endereço IP privado dinâmico ou estático à configuração de front-end. Você também pode atribuir vários endereços IP privados para habilitar cenários de multi-vip.

### <a name="at-a-glance"></a>Visão rápida
A tabela a seguir mostra cada tipo de recurso com os métodos de alocação possíveis (dinâmico/estático) e capacidade de atribuir vários endereços IP privados.

| Resource | Dinâmico | estático | Vários endereços IP |
| --- | --- | --- | --- |
| VM (em uma VNet ou um serviço de nuvem *autônomo*) |Sim |sim |Sim |
| Instância de função de PaaS (em uma VNet ou um serviço de nuvem *autônomo*) |Sim |Não |Não  |
| Front-end do balanceador de carga interno |Sim |sim |Sim |
| Front-end do Application Gateway |Sim |sim |Sim |

## <a name="limits"></a>limites
A tabela abaixo mostra os limites impostos ao endereçamento IP no Azure por assinatura. Você pode [entrar em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites padrão até os limites máximos com base nas necessidades de sua empresa.

|  | Limite padrão | Limite máximo |
| --- | --- | --- |
| Endereços IP públicos (dinâmicos) |5 |entrar em contato com o suporte |
| Endereços IP públicos reservados |20 |entrar em contato com o suporte |
| VIP público por implantação (serviço de nuvem) |5 |entrar em contato com o suporte |
| VIP privado (ILB) por implantação (serviço de nuvem) |1 |1 |

Leia o conjunto completo de [limites de rede](../azure-subscription-service-limits.md#networking-limits) do Azure.

## <a name="pricing"></a>Preços
Na maioria dos casos, endereços IP públicos são gratuitos. Há um custo nominal para usar endereços IP públicos adicionais e/ou estáticos. Certifique-se de compreender a [estrutura de preços para IPs públicos](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Diferenças entre as implantações do Gerenciador de recursos e clássica
A seguir está uma comparação dos recursos de endereçamento IP no Gerenciador de Recursos com o modelo de implantação clássico.

|  | Resource | Clássico | Gerenciador de Recursos |
| --- | --- | --- | --- |
| **Endereço IP público** |***VM*** |Conhecido como um ILPIP (somente dinâmico) |Conhecido como um IP público (dinâmico ou estático) |
|  ||Atribuído a uma VM IaaS ou a uma instância de função de PaaS |Associado à NIC da VM |
|  |***Balanceador de carga para a Internet*** |Conhecido como VIP (dinâmico) ou IP Reservado (estático) |Conhecido como um IP público (dinâmico ou estático) |
|  ||Atribuído a um serviço de nuvem |Associado à configuração de front-end do Balanceador de Carga |
|  | | | |
| **Endereço IP privado** |***VM*** |Conhecido como um DIP |Conhecido como um endereço IP privado |
|  ||Atribuído a uma VM IaaS ou a uma instância de função de PaaS |Atribuído à NIC da VM |
|  |***Balanceador de carga interno (ILB)*** |Atribuído ao ILB (dinâmico ou estático) |Atribuído à configuração de front-end do ILB (dinâmico ou estático) |

## <a name="next-steps"></a>Próximas etapas
* [Implantar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-classic-pportal.md) usando o portal do Azure.

