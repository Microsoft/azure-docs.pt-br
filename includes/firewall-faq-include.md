---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b8842ab4bcaf16b7345b25fa9ac4998981d9c458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713585"
---
### <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quais recursos têm suporte no Firewall do Azure?

* Firewall como serviço com estado
* Alta disponibilidade interna com escalabilidade de nuvem irrestrita
* Filtragem de FQDN
* Marcas de FQDN
* Regras de filtragem de tráfego de rede
* Suporte a SNAT de saída
* Suporte a DNAT de entrada
* É possível criar, impor e registrar em log centralmente políticas de conectividade de rede e de aplicativo em VNETs e assinaturas do Azure
* Totalmente integrado ao Azure Monitor para registro em log e análise

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

É possível implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente o implantam em uma rede virtual central e emparelham outras redes virtuais a ele em um modelo de hub e spoke. Você pode definir a rota padrão de redes virtuais emparelhadas para apontar a essa rede virtual de firewall central. O emparelhamento VNet global tem suporte, mas não é recomendado devido a possíveis problemas de desempenho e latência entre regiões. Para obter melhor desempenho, implante um firewall por região.

A vantagem desse modelo é a capacidade de exercer controle central sobre várias VNETs spoke entre assinaturas diferentes. Também há economia de custo, pois você não precisa implantar um firewall em cada VNET separadamente. A economia de custo deve ser medida em comparação com o custo de emparelhamento associado com base nos padrões de tráfego do cliente.

### <a name="how-can-i-install-the-azure-firewall"></a>Como eu instalo o Firewall do Azure?

O Firewall do Azure pode ser configurado usando o portal do Azure, PowerShell, API REST ou usando modelos. Consulte [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](../articles/firewall/tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

### <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos do Firewall do Azure?

O Firewall do Azure é compatível com regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e a mesma prioridade. Coleções de regras são executadas na ordem de prioridade. Coleções de regras de rede têm prioridade maior do que as coleções de regras de aplicativo, sendo que todas as regras são de terminação.

Há três tipos de coleções de regras:

* *Regras de aplicativo*: Configure nomes de domínio totalmente qualificados (FQDNs) que podem ser acessados de uma sub-rede.
* *Regras de rede*: Configure as regras que contêm endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras de NAT*: Configure regras DNAT para permitir conexões de entrada.

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

O Firewall do Azure dá suporte à filtragem de entrada e saída. A proteção de entrada é para protocolos não HTTP/S. Por exemplo, protocolos RDP, SSH e FTP.

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quais serviços de registro em log e análise têm suporte do Firewall do Azure?

O Firewall do Azure é integrado ao Azure Monitor para exibir e analisar logs de firewall. Os logs podem ser enviados para o Log Analytics, Armazenamento do Azure ou para Hubs de Eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas, como Excel e Power BI. Para obter mais informações, consulte [Tutorial: Monitorar os logs do Firewall do Azure](../articles/firewall/tutorial-diagnostics.md).

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona diferentemente dos serviços existentes, como NVAs no marketplace?

O Firewall do Azure é um serviço de firewall básico que pode atender a determinados cenários de cliente. Espera-se que você terá uma mistura de NVAs de terceiros e o Firewall do Azure. Trabalhar melhor juntos é uma prioridade principal.

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do Gateway de Aplicativo e o Firewall do Azure?

O WAF (Firewall de Aplicativo Web) é um recurso do Gateway de Aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O Firewall do Azure fornece proteção de entrada para protocolos não HTTP / S (por exemplo, RDP, SSH, FTP), proteção de nível de rede de saída para todas as portas e protocolos e proteção no nível do aplicativo para HTTP / S de saída.

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre NSGs (Grupos de Segurança de Rede) e o Firewall do Azure?

O serviço de Firewall do Azure complementa a funcionalidade de grupo de segurança de rede. Juntos, eles fornecem uma melhor segurança de rede de "defesa em profundidade". Os grupo de segurança de rede fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego para recursos dentro de redes virtuais em cada assinatura. O Firewall do Azure é um firewall como serviço de rede centralizado totalmente com estado, que fornece proteção no nível de rede e de aplicativo em diferentes assinaturas e redes virtuais.

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos ponto de extremidade de serviço. Você pode optar por habilitar pontos de extremidade de serviço na sub-rede de Firewall do Azure e desabilitá-los nas redes virtuais spoke conectadas. Dessa forma você aproveita ambos os recursos – segurança de ponto de extremidade de serviço e o registro em log central para todo o tráfego.

### <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço para o Firewall do Azure?

O Firewall do Azure tem um custo fixo + um custo variável:

* Valor fixo: US$ 1,25/firewall/hora
* Valor variável: US$ 0,03/GB processado pelo firewall (entrada ou saída)

Não há custos para um firewall desalocado.

Para obter mais informações, consulte [Preços do Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Como fazer para parar e iniciar o Firewall do Azure?

Use os métodos *deallocate* e *allocate* do Azure PowerShell.

Por exemplo: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Você deve realocar um firewall e o IP público ao grupo de recursos e à assinatura originais.

### <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

Para limites de serviço do Firewall do Azure, consulte [Limites, cotas e restrições de assinatura e serviço do Azure](../articles/azure-subscription-service-limits.md#azure-firewall-limits)

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Firewall do Azure em uma rede virtual de hub pode encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke?

Sim, você pode usar o Firewall do Azure em uma rede virtual de hub para encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke. As sub-redes em cada uma das redes virtuais de spoke devem ter uma UDR apontando para o Firewall do Azure como um gateway padrão para que este cenário funcione corretamente.

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Firewall do Azure pode encaminhar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou em redes virtuais emparelhadas?

Sim. No entanto, configurar as UDRs para redirecionar o tráfego entre sub-redes na mesma VNET requer atenção adicional. Embora o uso do intervalo de endereços de VNET como um prefixo de destino para a UDR seja suficiente, isso também roteia todo o tráfego de um computador para outro na mesma sub-rede por meio da instância do Firewall do Azure. Para evitar isso, inclua uma rota para a sub-rede na UDR com um tipo **VNET** de próximo salto. O gerenciamento dessas rotas pode ser complicado e passível de erros. O método recomendado para segmentação de rede interna é usar Grupos de Segurança de Rede, o que não exige UDRs.

### <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>É forçado túnel/encadeamento para uma solução de virtualização de rede com suporte?

Sim.

Firewall do Azure deve ter conectividade direta com a Internet. Por padrão, AzureFirewallSubnet tem uma rota 0.0.0.0/0 com o valor de NextHopType definido como **Internet**.

Se você habilitar o túnel forçado para o local por meio do Gateway de VPN ou ExpressRoute, você precisa configurar uma rota definida pelo usuário de 0.0.0.0/0 (UDR) com o conjunto de valores de NextHopType como a Internet e associá-la ao seu AzureFirewallSubnet explicitamente. Isso substitui um gateway padrão de potencial anúncio de BGP para sua rede local. Se sua organização exige que o túnel forçado para o Firewall do Azure direcionar o tráfego de gateway padrão voltar por meio de sua rede local, contate o suporte. Podemos colocar sua assinatura para garantir que o firewall necessário conectividade com a Internet é mantida.

### <a name="are-there-any-firewall-resource-group-restrictions"></a>Há qualquer firewall restrições no grupo de recursos?

Sim. O firewall, a sub-rede, a rede virtual e o endereço IP público devem estar todos no mesmo grupo de recursos.

### <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar DNAT para tráfego entrada de rede, também preciso configurar uma regra de rede correspondente para permitir esse tráfego?

Não. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Para saber mais sobre a lógica de processamento de regra do Firewall do Azure, confira [Lógica de processamento de regra do Firewall no Azure](../articles/firewall/rule-processing.md).

### <a name="how-to-wildcards-work-in-an-application-rule-target-fqdn"></a>Como a curingas funcionam em um FQDN de destino da regra de aplicativo?

Se você configurar ***. contoso.com**, ele permite *QualquerValor*. contoso.com, mas não contoso.com (o ápice de domínio). Se você quiser permitir o apex de domínio, você deve configurá-lo explicitamente como um FQDN de destino.
