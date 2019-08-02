---
title: Perguntas frequentes do Firewall do Azure
description: Perguntas frequentes para Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 6/21/2019
ms.author: victorh
ms.openlocfilehash: 933b4167f25db5a01cf1160f5e781a1fe31afc6b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304590"
---
# <a name="azure-firewall-faq"></a>Perguntas frequentes do Firewall do Azure

## <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quais recursos têm suporte no Firewall do Azure?

* Firewall como serviço com estado
* Alta disponibilidade interna com escalabilidade de nuvem irrestrita
* Filtragem de FQDN
* Marcas de FQDN
* Regras de filtragem de tráfego de rede
* Suporte a SNAT de saída
* Suporte a DNAT de entrada
* É possível criar, impor e registrar em log centralmente políticas de conectividade de rede e de aplicativo em VNETs e assinaturas do Azure
* Totalmente integrado ao Azure Monitor para registro em log e análise

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

É possível implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente o implantam em uma rede virtual central e emparelham outras redes virtuais a ele em um modelo de hub e spoke. Você pode definir a rota padrão de redes virtuais emparelhadas para apontar a essa rede virtual de firewall central. O emparelhamento VNet global tem suporte, mas ele não é recomendado devido a possíveis problemas de desempenho e latência entre regiões. Para obter melhor desempenho, implante um firewall por região.

A vantagem desse modelo é a capacidade de exercer controle central sobre várias VNETs spoke entre assinaturas diferentes. Também há economia de custo, pois você não precisa implantar um firewall em cada VNET separadamente. A economia de custo deve ser medida em comparação com o custo de emparelhamento associado com base nos padrões de tráfego do cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>Como eu instalo o Firewall do Azure?

O Firewall do Azure pode ser configurado usando o portal do Azure, PowerShell, API REST ou usando modelos. Consulte [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

## <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos do Firewall do Azure?

O Firewall do Azure é compatível com regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e a mesma prioridade. Coleções de regras são executadas na ordem de prioridade. Coleções de regras de rede têm prioridade maior do que as coleções de regras de aplicativo, sendo que todas as regras são de terminação.

Há três tipos de coleções de regras:

* *Regras de aplicativo*: Configure nomes de domínio totalmente qualificados (FQDNs) que podem ser acessados de uma sub-rede.
* *Regras de rede*: Configure as regras que contêm endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras de NAT*: Configure regras DNAT para permitir conexões de entrada.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

O Firewall do Azure dá suporte à filtragem de entrada e saída. A proteção de entrada é para protocolos não HTTP/S. Por exemplo, protocolos RDP, SSH e FTP.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quais serviços de registro em log e análise têm suporte do Firewall do Azure?

O Firewall do Azure é integrado ao Azure Monitor para exibir e analisar logs de firewall. Os logs podem ser enviados para o Log Analytics, Armazenamento do Azure ou para Hubs de Eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas, como Excel e Power BI. Para obter mais informações, consulte [Tutorial: Monitorar os logs do Firewall do Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona diferentemente dos serviços existentes, como NVAs no marketplace?

O Firewall do Azure é um serviço de firewall básico que pode atender a determinados cenários de cliente. Espera-se que você terá uma mistura de NVAs de terceiros e o Firewall do Azure. Trabalhar melhor juntos é uma prioridade principal.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do Gateway de Aplicativo e o Firewall do Azure?

O WAF (Firewall de Aplicativo Web) é um recurso do Gateway de Aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O Firewall do Azure fornece proteção de entrada para protocolos não HTTP / S (por exemplo, RDP, SSH, FTP), proteção de nível de rede de saída para todas as portas e protocolos e proteção no nível do aplicativo para HTTP / S de saída.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre NSGs (Grupos de Segurança de Rede) e o Firewall do Azure?

O serviço de Firewall do Azure complementa a funcionalidade de grupo de segurança de rede. Juntos, eles fornecem uma melhor segurança de rede de "defesa em profundidade". Os grupo de segurança de rede fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego para recursos dentro de redes virtuais em cada assinatura. O Firewall do Azure é um firewall como serviço de rede centralizado totalmente com estado, que fornece proteção no nível de rede e de aplicativo em diferentes assinaturas e redes virtuais.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Há suporte para grupos de segurança de rede (NSGs) na sub-rede de Firewall do Azure?

Firewall do Azure é um serviço gerenciado com várias camadas de proteção, incluindo a proteção de plataforma com NSGs de nível de NIC (não visíveis).  Nível de sub-rede NSGs não são necessários na sub-rede de Firewall do Azure e estão desabilitados para garantir que nenhuma interrupção do serviço.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos ponto de extremidade de serviço. Você pode optar por habilitar pontos de extremidade de serviço na sub-rede de Firewall do Azure e desabilitá-los nas redes virtuais spoke conectadas. Dessa forma você aproveita ambos os recursos – segurança de ponto de extremidade de serviço e o registro em log central para todo o tráfego.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço para o Firewall do Azure?

Ver [Firewall do Azure preços](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Como fazer para parar e iniciar o Firewall do Azure?

Use os métodos *deallocate* e *allocate* do Azure PowerShell.

Por exemplo:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Você deve realocar um firewall e o IP público ao grupo de recursos e à assinatura originais.

## <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

Para limites de serviço de Firewall do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Firewall do Azure em uma rede virtual de hub pode encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke?

Sim, você pode usar o Firewall do Azure em uma rede virtual de hub para encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke. As sub-redes em cada uma das redes virtuais de spoke devem ter uma UDR apontando para o Firewall do Azure como um gateway padrão para que este cenário funcione corretamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Firewall do Azure pode encaminhar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou em redes virtuais emparelhadas?

Sim. No entanto, configurar as UDRs para redirecionar o tráfego entre sub-redes na mesma VNET requer atenção adicional. Embora o uso do intervalo de endereços de VNET como um prefixo de destino para a UDR seja suficiente, isso também roteia todo o tráfego de um computador para outro na mesma sub-rede por meio da instância do Firewall do Azure. Para evitar isso, inclua uma rota para a sub-rede na UDR com um tipo **VNET** de próximo salto. O gerenciamento dessas rotas pode ser complicado e passível de erros. O método recomendado para segmentação de rede interna é usar Grupos de Segurança de Rede, o que não exige UDRs.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>É o SNAT de saída do Firewall do Azure entre redes privadas?

Firewall do Azure não SNAT quando o endereço IP de destino é um intervalo IP privados por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se sua organização usa um intervalo de endereços IP públicos para redes privadas, Azure Firewall SNATs o tráfego para um de IP privado firewall endereços no AzureFirewallSubnet.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>É forçado túnel/encadeamento para uma solução de virtualização de rede com suporte?

O túnel forçado não é suportado por padrão, mas ele pode ser habilitado com a Ajuda de suporte.

O Firewall do Azure deve ter conectividade direta com a Internet. Se seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet. Por padrão, o Firewall do Azure não dá suporte ao túnel forçado para uma rede local.

No entanto, se sua configuração exigir um túnel forçado para uma rede local, a Microsoft dará suporte de acordo com cada caso. Entre em contato com o suporte para que possamos analisar seu caso. Se aceita, vamos permitir que a assinatura e verifique se que a conectividade de Internet de firewall necessárias é mantida.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Há qualquer firewall restrições no grupo de recursos?

Sim. O firewall, a sub-rede, a rede virtual e o endereço IP público devem estar todos no mesmo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar DNAT para tráfego entrada de rede, também preciso configurar uma regra de rede correspondente para permitir esse tráfego?

Não. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Para saber mais sobre a lógica de processamento de regra do Firewall do Azure, confira [Lógica de processamento de regra do Firewall no Azure](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Como os curingas funcionam em um FQDN de destino da regra de aplicativo?

Se você configurar * **. contoso.com**, ele permite *QualquerValor*. contoso.com, mas não contoso.com (o ápice de domínio). Se você quiser permitir o apex de domínio, você deve configurá-lo explicitamente como um FQDN de destino.

## <a name="what-does-provisioning-state-failed-mean"></a>O que faz *estado de provisionamento: Falha ao* significam?

Sempre que uma alteração de configuração for aplicada, o Firewall do Azure tenta atualizar todas as suas instâncias de back-end subjacente. Em casos raros, uma dessas instâncias de back-end pode impedir a atualização com a nova configuração e o processo de atualização será interrompido com um estado de provisionamento com falha. O Firewall do Azure ainda está em operação, mas a configuração aplicada pode estar em um estado inconsistente, em que algumas instâncias têm a configuração anterior onde outras pessoas tenham a regra atualizada definido. Se isso acontecer, tente atualizar a configuração mais uma vez até que a operação for bem-sucedida e o Firewall está em um *bem-sucedido* estado de provisionamento.
