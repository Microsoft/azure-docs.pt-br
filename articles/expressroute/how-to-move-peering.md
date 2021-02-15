---
title: 'Azure ExpressRoute: mover um emparelhamento público para o emparelhamento da Microsoft'
description: Este artigo mostra as etapas para mover o emparelhamento público para o emparelhamento da Microsoft no ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2019
ms.author: duau
ms.openlocfilehash: 608d6c87442821e904fde16c6b75841fe792e3f0
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206281"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um emparelhamento público para o emparelhamento da Microsoft

Este artigo ajuda você a mover uma configuração de emparelhamento público para o emparelhamento da Microsoft sem tempo de inatividade. O ExpressRoute dá suporte a serviços de PaaS do Azure, como o armazenamento do Azure e o Banco de Dados SQL do Azure, usando o emparelhamento da Microsoft com filtros de rota. Agora, você precisa apenas de um domínio de roteamento para acessar os serviços de PaaS e SaaS da Microsoft. Aproveite os filtros de rota para anunciar seletivamente os prefixos do serviço de PaaS para as regiões do Azure que você deseja consumir.

O emparelhamento público do Azure tem 1 endereço IP NAT associado a cada sessão BGP. O emparelhamento da Microsoft permite que você configure suas próprias alocações NAT, bem como Use filtros de rota para anúncios de prefixo seletivo. O emparelhamento público é um serviço unidirecional que usa a conectividade sempre iniciada da sua WAN para Microsoft Azure serviços. Os serviços do Microsoft Azure não poderão iniciar conexões a sua rede por meio desse domínio de roteamento.

Após o emparelhamento público ser habilitado, você poderá se conectar a todos os serviços do Azure. Não permitimos que você escolha seletivamente os serviços para os quais podemos anunciar rotas. Enquanto o emparelhamento da Microsoft é uma conectividade bidirecional em que a conexão pode ser iniciada a partir do serviço Microsoft Azure junto com sua WAN. Para obter mais informações sobre os domínios de roteamento e o emparelhamento, consulte [circuitos de ExpressRoute e domínios de roteamento](expressroute-circuit-peerings.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Para se conectar ao emparelhamento da Microsoft, você precisa configurar e gerenciar o NAT. Seu provedor de conectividade pode configurar e gerenciar o NAT como um serviço gerenciado. Caso você pretenda acessar os serviços de PaaS e SaaS do Azure no emparelhamento do Microsoft, é importante dimensionar o pool IP do NAT corretamente. Para obter mais informações sobre o NAT para o ExpressRoute, consulte os [Requisitos do NAT para o emparelhamento da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando você se conecta à Microsoft por meio do Azure ExpressRoute (emparelhamento da Microsoft), você tem vários links para a Microsoft. Um link é sua conexão com a Internet existente e o outro é via ExpressRoute. Parte do tráfego para a Microsoft pode passar pela Internet, mas voltar por meio do ExpressRoute ou vice-versa.

![Conectividade bidirecional](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> O pool de IPs de NAT anunciado à Microsoft não deve ser anunciado na Internet. Isso interromperá a conectividade com outros serviços da Microsoft.

Consulte [Roteamento Assimétrico com vários caminhos de rede](./expressroute-asymmetric-routing.md) para obter as advertências de roteamento assimétrico antes de configurar o emparelhamento da Microsoft.

* Se você estiver usando o emparelhamento público e tiver regras de rede IP para endereços IP públicos que são usados para acessar o [armazenamento do Azure](../storage/common/storage-network-security.md) ou o [banco de dados SQL do Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md), você precisará certificar-se de que o pool de IP de NAT configurado com o emparelhamento da Microsoft esteja incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou conta SQL do Azure.<br>
* Para fazer uma movimentação para o emparelhamento da Microsoft sem tempo de inatividade, use as etapas deste artigo na ordem em que são apresentadas.

## <a name="1-create-microsoft-peering"></a><a name="create"></a>1. criar emparelhamento da Microsoft

Se um emparelhamento da Microsoft não tiver sido criado, use um dos artigos a seguir para criar um emparelhamento da Microsoft. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, solicite a ele a habilitação do emparelhamento da Microsoft para seu circuito.

Se a camada 3 for gerenciada por você, as seguintes informações serão necessárias antes de prosseguir:

* Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.<br>
* Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.<br>
* Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.<br>
* Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.<br>
* Prefixos anunciados: forneça uma lista com todos os prefixos que você pretende anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.<br>
* Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.

* **Opcional** -ASN do cliente: se você estiver anunciando prefixos que não estão registrados para o número as de emparelhamento, você pode especificar o número as para o qual eles estão registrados.<br>
* **Opcional** -um hash MD5 se você optar por usar um.

Instruções detalhadas para habilitar o emparelhamento da Microsoft podem ser encontradas nos seguintes artigos:

* [Criar um emparelhamento da Microsoft usando o portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Criar um emparelhamento da Microsoft usando o Azure PowerShell](expressroute-howto-routing-arm.md#msft)<br>
* [Criar um emparelhamento da Microsoft usando a CLI do Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a><a name="validate"></a>2. validar o emparelhamento da Microsoft está habilitado

Verifique se o emparelhamento da Microsoft está habilitado e se os prefixos públicos anunciados estão no estado configurado.

* [Azure portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [PowerShell do Azure](expressroute-howto-routing-arm.md#getmsft)<br>
* [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a><a name="routefilter"></a>3. configurar e anexar um filtro de rota ao circuito

Por padrão, o novo emparelhamento da Microsoft não anuncia nenhum prefixo até que um filtro de rota seja anexado ao circuito. Ao criar uma regra de filtro de rota, você pode especificar a lista de comunidades de serviço para regiões do Azure que você deseja consumir para os serviços de PaaS do Azure. Isso fornece a flexibilidade para filtrar as rotas de acordo com seu requisito, conforme mostrado na seguinte captura de tela:

![Mesclar um emparelhamento público](./media/how-to-move-peering/routefilter.jpg)

Use um dos artigos a seguir para configurar filtros de rota:

* [Configurar filtros de rota para o emparelhamento da Microsoft usando o portal do Azure](how-to-routefilter-portal.md)<br>
* [Configurar filtros de rota para o emparelhamento da Microsoft usando o Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurar filtros de rota para o emparelhamento da Microsoft usando a CLI do Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a><a name="delete"></a>4. excluir o emparelhamento público

Depois de verificar se o emparelhamento da Microsoft está configurado e se os prefixos que você deseja consumir estão corretamente anunciados no emparelhamento da Microsoft, você poderá excluir o emparelhamento público. Para excluir o emparelhamento público, use um dos seguintes artigos:

* [Excluir o emparelhamento público do Azure usando o Azure PowerShell](about-public-peering.md#powershell)
* [Excluir o emparelhamento público do Azure usando a CLI](about-public-peering.md#cli)
  
## <a name="5-view-peerings"></a><a name="view"></a>5. exibir emparelhamentos
  
Você pode ver uma lista de todos os circuitos ExpressRoute e emparelhamentos no portal do Azure. Para obter mais informações, consulte [detalhes de emparelhamento do modo de exibição da Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).