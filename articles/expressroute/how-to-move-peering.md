---
title: Mover um emparelhamento público no emparelhamento da Microsoft – Azure ExpressRoute| Microsoft Docs
description: Este artigo mostra as etapas para mover o emparelhamento público para o emparelhamento da Microsoft no ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592107"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Mover um emparelhamento público para o emparelhamento da Microsoft

Este artigo ajuda você a mover uma configuração de emparelhamento público para o emparelhamento da Microsoft sem tempo de inatividade. O ExpressRoute dá suporte a serviços de PaaS do Azure, como o armazenamento do Azure e o Banco de Dados SQL do Azure, usando o emparelhamento da Microsoft com filtros de rota. Agora, você precisa apenas de um domínio de roteamento para acessar os serviços de PaaS e SaaS da Microsoft. Aproveite os filtros de rota para anunciar seletivamente os prefixos do serviço de PaaS para as regiões do Azure que você deseja consumir.

Emparelhamento público do Azure tem 1 endereço IP de NAT associado a cada sessão BGP. Emparelhamento da Microsoft permite que você configurar seus próprio alocações de NAT, bem como usar filtros de rota para anúncios de prefixo seletivo. Emparelhamento público é um serviço unidirecional usando o que a conectividade é sempre iniciado de sua WAN para serviços do Microsoft Azure. Os serviços do Microsoft Azure não poderão iniciar conexões a sua rede por meio desse domínio de roteamento.

Após o emparelhamento público ser habilitado, você poderá se conectar a todos os serviços do Azure. Não permitimos que você escolha seletivamente os serviços para os quais podemos anunciar rotas. Enquanto o emparelhamento da Microsoft, há uma conectividade bidirecional em que a conexão pode ser iniciada do serviço do Microsoft Azure, juntamente com sua WAN. Para obter mais informações sobre domínios de roteamento e o emparelhamento, consulte [ExpressRoute circuitos e domínios de roteamento](expressroute-circuit-peerings.md).

## <a name="before"></a>Antes de começar

Para se conectar ao emparelhamento da Microsoft, você precisa configurar e gerenciar o NAT. Seu provedor de conectividade pode configurar e gerenciar o NAT como um serviço gerenciado. Caso você pretenda acessar os serviços de PaaS e SaaS do Azure no emparelhamento do Microsoft, é importante dimensionar o pool IP do NAT corretamente. Para obter mais informações sobre o NAT para o ExpressRoute, consulte os [Requisitos do NAT para o emparelhamento da Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quando você se conecta à Microsoft por meio do Azure ExpressRoute(Microsoft peering), você tem vários links para a Microsoft. Um link é sua conexão com a Internet existente e o outro é via ExpressRoute. Parte do tráfego para a Microsoft pode passar pela Internet, mas voltar por meio do ExpressRoute ou vice-versa.

![Conectividade bidirecional](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> O pool de IPs de NAT anunciado à Microsoft não deve ser anunciado na Internet. Isso interromperá a conectividade com outros serviços da Microsoft.

Consulte a [roteamento assimétrico com vários caminhos de rede](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) para conhecer as restrições de roteamento assimétrico antes de configurar o emparelhamento da Microsoft.

* Se você estiver usando o emparelhamento público e tiver regras de rede IP para endereços IP públicos que são usados para acessar [armazenamento do Azure](../storage/common/storage-network-security.md) ou [banco de dados SQL do Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), você precisa certificar-se de que o pool de IP de NAT configurado com o emparelhamento Microsoft está incluído na lista de endereços IP públicos para a conta de armazenamento do Azure ou a conta do SQL Azure.<br>
* Para fazer uma movimentação para o emparelhamento da Microsoft sem tempo de inatividade, use as etapas deste artigo na ordem em que são apresentadas.

## <a name="create"></a>1. Criar um emparelhamento da Microsoft

Se um emparelhamento da Microsoft não tiver sido criado, use um dos artigos a seguir para criar um emparelhamento da Microsoft. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, solicite a ele a habilitação do emparelhamento da Microsoft para seu circuito.

Se a camada 3 for gerenciada por você as informações a seguir serão necessárias antes de prosseguir:

* Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.<br>
* Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR. Dessa sub-rede, você atribuirá o primeiro endereço IP utilizável ao seu roteador já que a Microsoft usa o segundo IP utilizável para seu roteador.<br>
* Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN. Para os links Primário e Secundário, você deve usar a mesma ID de VLAN.<br>
* Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.<br>
* Prefixos anunciados: você precisa fornecer uma lista com todos os prefixos que planeja anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.<br>
* Nome do registro de roteamento: é possível especificar o RIR/IRR no qual o número AS e os prefixos estão registrados.

* **Opcional** -ASN de cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.<br>
* **Opcional** -hash MD5 de um se você optar por usar um.

Instruções detalhadas para habilitar o emparelhamento da Microsoft podem ser encontradas nos seguintes artigos:

* [Criar um emparelhamento da Microsoft usando o portal do Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Criar um emparelhamento da Microsoft usando o Azure PowerShell](expressroute-howto-routing-arm.md#msft)<br>
* [Criar um emparelhamento da Microsoft usando a CLI do Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Validar se o emparelhamento da Microsoft está habilitado

Verifique se o emparelhamento da Microsoft está habilitado e se os prefixos públicos anunciados estão no estado configurado.

* [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [PowerShell do Azure](expressroute-howto-routing-arm.md#getmsft)<br>
* [CLI do Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurar e anexar um filtro de rota ao circuito

Por padrão, novo emparelhamento da Microsoft não anunciam nenhum prefixo até que um filtro de rota seja anexado ao circuito. Quando você cria uma regra de filtro de rota, você pode especificar a lista de comunidades de serviço para as regiões do Azure que você deseja consumir serviços PaaS do Azure. Isso oferece a flexibilidade para filtrar as rotas de acordo com suas necessidades, conforme mostrado na seguinte captura de tela:

![Mesclar um emparelhamento público](./media/how-to-move-peering/routefilter.jpg)

Use um dos artigos a seguir para configurar filtros de rota:

* [Configurar filtros de rota para o emparelhamento da Microsoft usando o portal do Azure](how-to-routefilter-portal.md)<br>
* [Configurar filtros de rota para o emparelhamento da Microsoft usando o Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurar filtros de rota para o emparelhamento da Microsoft usando a CLI do Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Excluir o emparelhamento público

Depois de verificar se o emparelhamento da Microsoft está configurado e se os prefixos que você deseja consumir estão corretamente anunciados no emparelhamento da Microsoft, você poderá excluir o emparelhamento público. Para excluir o emparelhamento público, use um dos seguintes artigos:

* [Excluir o emparelhamento público do Azure usando o portal do Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Excluir o emparelhamento público do Azure usando o Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Excluir o emparelhamento público do Azure usando a CLI](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Emparelhamentos de exibição
  
Você pode ver uma lista de todos os circuitos ExpressRoute e emparelhamentos no portal do Azure. Para obter mais informações, consulte [detalhes de emparelhamento do modo de exibição da Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).
