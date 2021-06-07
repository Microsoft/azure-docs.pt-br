---
title: Endereços IP no AzureEndereços IP no Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre endereços IP públicos no Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: ffdd673cc8a357a7156fb3b3e932c524c831db15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418055"
---
# <a name="public-ip-addresses"></a>Endereços IP públicos

Endereços IP públicos permitem recursos de Internet para comunicar a entrada para recursos do Azure. Os endereços IP públicos permitem que os recursos do Azure se comuniquem à Internet e aos serviços do Azure voltados para o público. O endereço é dedicado ao recurso, até que ele não seja atribuído por você. Um recurso sem um IP público atribuído pode comunicar a saída. O Azure atribui dinamicamente um endereço IP disponível que não é dedicado ao recurso. Para obter mais informações sobre conexões de saída no Azure, consulte [Entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Gerenciador de recursos do Azure, um endereço [IP público](virtual-network-public-ip-address.md) é um recurso com as próprias propriedades. Alguns dos recursos aos quais você pode associar um recurso de endereço IP público:

* Interfaces de rede de máquina virtual
* Balanceadores de carga para Internet
* Gateways VPN
* Gateways de aplicativo
* Firewall do Azure

## <a name="ip-address-version"></a>Versão do endereço IP

Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. 

## <a name="sku"></a>SKU

Para saber mais sobre a atualização do SKU, consulte [atualização de IP público](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Os endereços IP públicos são criados com um dos seguintes SKUs:

>[!IMPORTANT]
> Os SKUs correspondentes são necessários para os recursos de balanceador de carga e IP público. Não é possível ter uma combinação de recursos de SKU Básico e SKU Standard. Você não pode anexar as máquinas virtuais autônomas, máquinas virtuais em um recurso de conjunto de disponibilidade, ou recursos de conjunto de dimensionamento de máquina virtual para os SKUs simultaneamente.  Os novos designs devem considerar o uso de recursos de SKU Standard.  Examine [Balanceador de Carga Standard](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes.

### <a name="standard"></a>Standard

Endereços IP públicos de SKU padrão:

- Sempre use o método de alocação estática.
- Ter um fluxo originado de entrada ajustável tempo limite de ociosidade de fluxo originado de entrada ajustável de 4 a 30 minutos, com um padrão de 4 minutos, e um tempo limite de ociosidade de fluxo originado de saída fixo de 4 minutos.
- Seguro por padrão e fechado para tráfego de entrada. Permitir listar o tráfego de entrada com um [grupo de segurança de rede](./network-security-groups-overview.md#network-security-groups).
- Atribuído a interfaces de rede, balanceadores de carga públicos padrão ou gateways de aplicativo. Para obter mais informações sobre o balanceador de carga padrão, consulte [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pode ser com redundância de zona (anunciada de todas as 3 zonas), zonal (garantida em uma zona de disponibilidade pré-selecionada específica) ou sem zona (não associada a uma zona de disponibilidade pré-selecionada específica). Para saber mais sobre as zonas de disponibilidade, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Balanceador de carga Standard e zonas de disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **IPs com redundância de zona só podem ser criados em [regiões em que três zonas de disponibilidade](../availability-zones/az-region.md) estão ativas.** Os IPs criados antes de as zonas serem ativas não terão redundância de zona.
- Pode ser usado como IPs de front-end anycast para [balanceadores de carga entre regiões](../load-balancer/cross-region-overview.md) (funcionalidade de visualização).
 
> [!NOTE]
> A comunicação de entrada com o recurso de SKU Standard falha até que você crie e associe um [grupo de segurança de rede](./network-security-groups-overview.md#network-security-groups) e permita explicitamente o tráfego de entrada desejado.

> [!NOTE]
> Somente endereços IP públicos com SKU básico estão disponíveis ao usar o [serviço de metadados de instância IMDS](../virtual-machines/windows/instance-metadata-service.md). Não há suporte para o SKU Standard.

> [!NOTE]
> As configurações de diagnóstico não aparecem na folha de recursos ao usar um endereço IP público de SKU padrão. Para habilitar o registro em log em seu recurso de endereço IP público padrão, navegue até configurações de diagnóstico na folha Azure Monitor e selecione o recurso de endereço IP.

### <a name="basic"></a>Basic

Todos os endereços IP públicos criados antes da introdução dos SKUs são endereços IP públicos do SKU Básico. 

Com a introdução de SKUs, especifique qual SKU você gostaria que o endereço IP público fosse. 

Endereços básicos do SKU:

- Atribuídos com o método de alocação estática ou dinâmica.
- Ter um fluxo originado de entrada ajustável tempo limite de ociosidade de fluxo originado de entrada ajustável de 4 a 30 minutos, com um padrão de 4 minutos, e um tempo limite de ociosidade de fluxo originado de saída fixo de 4 minutos.
- Estão abertos por padrão.  Os grupos de segurança de rede são recomendados, mas opcionais, para restringir o tráfego de entrada ou saída.
- Atribuído a qualquer recurso do Azure que pode ser atribuído a um endereço IP público, como:
    * Adaptadores de rede
    * Gateways VPN
    * Gateways do Aplicativo
    * Balanceadores de carga públicos
- Não há suporte para cenários de zona de disponibilidade. Use o IP público do SKU padrão para cenários de zona de disponibilidade. Para saber mais sobre as zonas de disponibilidade, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Balanceador de carga Standard e zonas de disponibilidade](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Método de alocação

Os IPs públicos básicos e Standard dão suporte à atribuição **estática** .  O recurso é atribuído a um endereço IP no momento em que é criado. O endereço IP é liberado quando o recurso é excluído.

Os endereços IP públicos de SKU básica dão suporte a uma atribuição **dinâmica** . Dinâmico é o método de atribuição padrão. O endereço IP **não é** fornecido ao recurso no momento da criação ao selecionar dinâmico.

O IP é atribuído quando você associa o recurso de endereço IP público a:

* Máquina virtual 
* A primeira máquina virtual é associada ao pool de back-end de um balanceador de carga.

O endereço IP é liberado quando você para (ou exclui) o recurso.  

Por exemplo, um recurso de IP público é liberado de um recurso chamado **recurso a**. O **recurso A** receberá um IP diferente na inicialização se o recurso de IP público for reatribuído.

O endereço IP é liberado quando o método de alocação é alterado de **estático** para **dinâmico**. Para garantir que o endereço IP do recurso associado permaneça o mesmo, defina o método de alocação explicitamente como **estático**. Um endereço IP estático é atribuído imediatamente.

> [!NOTE]
> Mesmo quando você define o método de alocação para **estático**, não é possível especificar o endereço IP real atribuído ao recurso de endereço IP público. O Azure atribui o endereço IP de um pool de endereços IP disponíveis no local do Azure onde o recurso é criado.
>

Os endereços IP públicos estáticos são comumente usados nas seguintes situações:

* Quando você precisa atualizar regras de firewall para se comunicar com os recursos do Azure.
* Resolução de nome DNS, em que uma alteração no endereço IP exigiria a atualização de registros A.
* Seus recursos do Azure comunicam-se com outros aplicativos ou serviços que usam um endereço IP baseado em um modelo de segurança.
* Você usa certificados TLS/SSL vinculados a um endereço IP.

> [!NOTE]
> O Azure aloca endereços IP públicos de um intervalo exclusivo para cada região em cada nuvem do Azure. Você pode baixar a lista de intervalos (prefixos) para as nuvens [pública](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062) e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.
>

## <a name="dns-hostname-resolution"></a>Resolução de nome de host DNS

Selecione a opção para especificar um rótulo de nome de domínio DNS para um recurso IP público. 

Essa seleção cria um mapeamento para **domainnamelabel**. **Location**. cloudapp.Azure.com para o IP público no DNS gerenciado pelo Azure. 

Por exemplo, a criação de um IP público com:

* **contoso** como um **domainnamelabel**
* **Oeste dos EUA** **Local** do Azure

O FQDN (nome de domínio totalmente qualificado) **contoso.westus.cloudapp.Azure.com** resolve para o endereço IP público do recurso.

> [!IMPORTANT]
> Cada rótulo de nome do domínio criado deve ser exclusivo dentro de seu local do Azure.  
>

## <a name="dns-recommendations"></a>Recomendações de DNS

Se uma movimentação de região for necessária, você não poderá migrar o FQDN do seu IP público. Use o FQDN para criar um registro CNAME personalizado apontando para o endereço IP público. 

Se uma mudança para um IP público diferente for necessária, atualize o registro CNAME em vez de atualizar o FQDN.

Você pode usar o [DNS do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) ou um provedor DNS externo para seu registro DNS. 

## <a name="virtual-machines"></a>Máquinas virtuais

Você pode associar um endereço IP público a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuindo-o ao seu **adaptador de rede**. 

Escolha **dinâmico** ou **estático** para o endereço IP público. Saiba mais lendo o artigo [Atribuindo endereços IP a adaptadores de rede](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Balanceadores de carga para Internet

Você pode associar um endereço IP público de um [SKU](#sku) a um [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração de **front-end** do balanceador de carga. O IP público serve como um IP com balanceamento de carga. 

Você pode atribuir um endereço IP público estático ou dinâmico a um front-end de balanceador de carga. Você pode atribuir vários endereços IP públicos a um front-end de balanceador de carga. Essa configuração permite cenários de [vários VIPs](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , como um ambiente multilocatário com sites baseados em TLS. 

Para saber mais sobre os SKUs do balanceador de carga do Azure, confira [SKU padrão do balanceador de carga do Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="vpn-gateways"></a>Gateways VPN

O [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) conecta uma rede virtual do Azure a:

* Redes virtuais do Azure
* Rede (s) local (is). 

Um endereço IP público é atribuído ao gateway de VPN para habilitar a comunicação com a rede remota. 

* Atribua um IP público **dinâmico** básico a uma configuração de front-end de SKU VPNGw 1-5.
* Atribua um endereço IP público padrão **estático** a uma configuração de front-end de SKU VPNGwAZ 1-5.

## <a name="application-gateways"></a>Gateways de aplicativo

Você pode associar um endereço IP público do Azure [Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), atribuindo-o à configuração de **front-end** do gateway. 

* Atribua um IP público **dinâmico** básico a uma configuração de front-end do gateway de aplicativo v1. 
* Atribua um endereço IP público padrão **estático** a uma configuração de front-end v2.

## <a name="azure-firewall"></a>Firewall do Azure

O [Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) permite criar, impor e registrar políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

Você só pode associar endereços IP públicos padrão **estáticos** a um firewall. Isso permite que os firewalls externos identifiquem o tráfego originado de sua rede virtual. 


## <a name="at-a-glance"></a>Imediato

A tabela a seguir mostra a propriedade por meio da qual um IP público pode ser associado a um recurso de nível superior e os métodos de alocação possíveis.

| Recurso de nível superior | Associação de Endereço IP | Dinâmico | Estático |
| --- | --- | --- | --- |
| Máquina virtual |Adaptador de rede |Sim |Sim |
| Balanceador de carga voltado para a Internet |Configuração de front-end |Sim |Sim |
| gateway de VPN |Configuração de IP do gateway |Sim |Não |
| Gateway de Aplicativo |Configuração de front-end |Sim (apenas V1) |Sim (apenas V2) |
| Firewall do Azure | Configuração de front-end | Não | Sim|

## <a name="limits"></a>Limites

Os limites para o endereçamento IP são listados no conjunto completo de [limites para rede](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) no Azure. 

Os limites são por região e por assinatura. [Entre em contato](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o suporte para aumentar os limites padrão até os limites máximos com base em suas necessidades de negócios.

## <a name="pricing"></a>Preços

Endereços IP públicos podem ter um custo nominal. Para saber mais sobre preços de endereços IP no Azure, confira a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [endereços IP privados no Azure](private-ip-addresses.md)
* [Implantar uma VM com um IP público estático usando o Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
