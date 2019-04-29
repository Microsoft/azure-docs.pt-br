---
title: Roteamento de tráfego de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como o Azure roteia o tráfego de rede virtual e como você pode personalizar o roteamento do Azure.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2017
ms.author: malop;kumud
ms.openlocfilehash: ad35d440904c7b65e27b4ead75cec00daa20f8ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596310"
---
# <a name="virtual-network-traffic-routing"></a>Roteamento de tráfego de rede virtual

Saiba mais sobre como o Azure roteia o tráfego entre o Azure, locais e recursos da Internet. O Azure cria automaticamente uma tabela de rotas para cada sub-rede dentro de uma rede virtual do Azure e adiciona as rotas de sistema padrão à tabela. Para saber mais sobre as redes virtuais e sub-redes, confira [Visão geral da rede virtual](virtual-networks-overview.md). É possível substituir algumas das rotas de sistema do Azure por [rotas personalizadas](#custom-routes) e adicionar outras rotas personalizadas às tabelas de rotas. O Azure roteia o tráfego de saída de uma sub-rede com base nas rotas em uma tabela de rotas da sub-rede.

## <a name="system-routes"></a>Rotas do sistema

O Azure cria rotas de sistema automaticamente e as atribui a cada sub-rede em uma rede virtual. Não é possível criar nem remover rotas de sistema, mas é possível substituir algumas rotas de sistema por [rotas personalizadas](#custom-routes). O Azure cria rotas de sistema padrão para cada sub-rede e adiciona [rotas padrão opcionais](#optional-default-routes) a sub-redes específicas ou a todas as sub-redes quando você usa os recursos específicos do Azure.

### <a name="default"></a>Padrão

Cada rota contém um prefixo de endereço e o tipo do próximo salto. Quando um tráfego saindo de uma sub-rede é enviado a um endereço IP dentro do prefixo de endereço de uma rota, a rota que contém o prefixo é a que o Azure usa. Saiba mais sobre [como o Azure seleciona uma rota](#how-azure-selects-a-route) quando várias rotas contêm os mesmos prefixos ou prefixos sobrepostos. Sempre que uma rede virtual é criada, o Azure cria automaticamente as rotas de sistema padrão a seguir para cada sub-rede na rede virtual:


|Fonte |Prefixos do endereço                                        |Tipo do próximo salto  |
|-------|---------                                               |---------      |
|Padrão|Exclusivo para a rede virtual                           |Rede virtual|
|Padrão|0.0.0.0/0                                               |Internet       |
|Padrão|10.0.0.0/8                                              |Nenhum           |
|Padrão|172.16.0.0/12                                           |Nenhum           |
|Padrão|192.168.0.0/16                                          |Nenhum           |
|Padrão|100.64.0.0/10                                           |Nenhum           |

Os tipos do próximo salto listados na tabela anterior representam como o Azure roteia o tráfego destinado ao prefixo de endereço listado. As explicações para os tipos do próximo salto seguem:

- **Rede virtual**: encaminha o tráfego entre intervalos de endereços no [espaço de endereço](manage-virtual-network.md#add-or-remove-an-address-range) de uma rede virtual. O Azure cria uma rota com um prefixo de endereço que corresponde a cada intervalo de endereços definido no espaço de endereço de uma rede virtual. Se o espaço de endereço da rede virtual tiver vários intervalos de endereços definidos, o Azure cria uma rota individual para cada intervalo de endereços. O Azure roteia automaticamente o tráfego entre as sub-redes usando as rotas criadas para cada intervalo de endereços. Não é preciso definir gateways para o Azure para rotear o tráfego entre sub-redes. Embora uma rede virtual contenha sub-redes e cada sub-rede tenha um intervalo de endereços definido, o Azure *não* cria rotas padrão para intervalos de endereço da sub-rede porque cada um desses intervalos está dentro de um intervalo de endereços do espaço de endereço de uma rede virtual.

- **Internet**: encaminha o tráfego especificado pelo prefixo do endereço para a Internet. A rota padrão de sistema especifica o prefixo de endereço 0.0.0.0/0. Se você não substituir as rotas padrão do Azure, o Azure roteia o tráfego para qualquer endereço não especificado por um intervalo de endereços em uma rede virtual para a Internet, com uma exceção. Se o endereço de destino for para um dos serviços do Azure, o Azure roteia o tráfego diretamente ao serviço da sua rede de backbone em vez de rotear o tráfego para a Internet. O tráfego entre os serviços do Azure não percorre a Internet, independentemente de em qual região do Azure a rede virtual existe ou em qual região do Azure uma instância do serviço do Azure está implantada. Você pode substituir a rota do sistema padrão do Azure para o prefixo de endereço 0.0.0.0/0 com uma [rota personalizada](#custom-routes).

- **Nenhum**: o tráfego encaminhado para tipo do próximo salto **Nenhum** é descartado em vez de ser roteado para fora da sub-rede. O Azure cria automaticamente as rotas padrão para os seguintes prefixos de endereço:
  - **10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16**: reservados para uso particular no RFC 1918.
  - **100.64.0.0/10**: reservado no RFC 6598.

    Se você atribuir algum dos intervalos de endereços anteriores no espaço de endereço de uma rede virtual, o Azure altera automaticamente o tipo do próximo salto da rota de **Nenhum** para **Rede virtual**. Se você atribuir um intervalo de endereços para o espaço de endereço de uma rede virtual que inclui, mas não é a mesma que um dos quatro prefixos de endereço reservados, o Azure remove a rota do prefixo e adiciona uma rota para o prefixo de endereço que você adicionou, com a **Rede virtual** como o tipo do próximo salto.

### <a name="optional-default-routes"></a>Rotas padrão opcionais

O Azure adiciona outras rotas de sistema padrão para diferentes recursos do Azure, mas somente se você habilitar os recursos. Dependendo do recurso, o Azure adiciona rotas padrão opcionais para sub-redes específicas na rede virtual ou para todas as sub-redes em uma rede virtual. As rotas de sistema adicionais e os tipos do próximo salto que o Azure pode adicionar quando você habilita recursos diferentes são:

|Fonte                 |Prefixos do endereço                       |Tipo do próximo salto|A sub-rede na rede virtual que roteia é adicionada a|
|-----                  |----                                   |---------                    |--------|
|Padrão                |Exclusivo para a rede virtual, por exemplo: 10.1.0.0/16|Emparelhamento VNet                 |Todos|
|Gateway de rede virtual|Prefixos anunciados do local via BGP ou configurada no gateway de rede local     |Gateway de rede virtual      |Todos|
|Padrão                |Vários                               |VirtualNetworkServiceEndpoint|Somente a sub-rede para a qual um ponto de extremidade de serviço está habilitado.|

- **Emparelhamento de rede virtual (VNet)**: quando você cria um emparelhamento de rede virtual entre duas redes virtuais, uma rota é adicionada a cada intervalo de endereços dentro do espaço de endereço de cada rede virtual para a qual um emparelhamento é criado. Saiba mais sobre [emparelhamento de rede virtual](virtual-network-peering-overview.md).  
- **Gateway de rede virtual**: uma ou mais rotas com *Gateway de rede virtual* listado como o tipo do próximo salto são adicionadas quando um gateway de rede virtual é adicionado a uma rede virtual. A fonte também é *gateway de rede virtual* porque o gateway adiciona as rotas à sub-rede. Se o gateway de rede local troca as rotas do protocolo [BGP](#border-gateway-protocol) (Border Gateway Protocol) com um gateway de rede virtual do Azure, uma rota é adicionada para cada rota propagada do gateway de rede virtual local. Recomenda-se resumir rotas locais para os maiores intervalos de endereços possíveis para que o menor número de rotas seja propagado para um gateway de rede virtual do Azure. Há limites para o número de rotas que podem ser propagadas para um gateway de rede virtual do Azure. Para obter detalhes, confira [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).
- **VirtualNetworkServiceEndpoint**: os endereços IP públicos para determinados serviços são adicionados à tabela de rotas pelo Azure quando você habilita um ponto de extremidade de serviço para o serviço. Pontos de extremidade de serviço são habilitados para sub-redes individuais em uma rede virtual, de modo que a rota só seja adicionada à tabela de rotas de uma sub-rede para a qual está habilitado um ponto de extremidade de serviço. Os endereços IP públicos de serviços do Azure mudam periodicamente. O Azure gerencia os endereços na tabela de rotas automaticamente quando os endereços mudam. Saiba mais sobre [pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md) e os serviços para os quais é possível criar pontos de extremidade de serviço. 

> [!NOTE]
> Os tipos do próximo salto **emparelhamento VNet** e **VirtualNetworkServiceEndpoint** só são adicionados a tabelas de rota de sub-redes em redes virtuais criadas por meio do modelo de implantação do Azure Resource Manager. Os tipos do próximo salto não são adicionados a tabelas de rotas que estão associadas a sub-redes de rede virtual criadas por meio do modelo de implantação clássico. Saiba mais sobre os [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure.

## <a name="custom-routes"></a>Rotas personalizadas

É possível criar rotas personalizadas criando rotas [definidas pelo usuário](#user-defined) ou trocando rotas de [protocolo BGP](#border-gateway-protocol) entre seu local de gateway de rede e um gateway de rede virtual do Azure. 
 
### <a name="user-defined"></a>Definido pelo usuário

É possível criar rotas personalizadas ou definidas pelo usuário no Azure para substituir as rotas de sistema padrão do Azure ou adicionar outras rotas a uma tabela de rotas da sub-rede. No Azure, você cria uma tabela de rotas e depois a associa para zero ou mais sub-redes de rede virtual. Cada sub-rede pode ter zero ou uma tabela de rotas associada a ela. Para saber mais sobre o número máximo de rotas que podem ser adicionadas a uma tabela de rotas e o número máximo de tabelas de rotas definidas pelo usuário que podem ser criadas por assinatura do Azure, confira os [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Se você criar uma tabela de rotas e associá-la a uma sub-rede, as rotas dentro dela substituem ou são combinadas às rotas padrão que o Azure adiciona a uma sub-rede por padrão.

Você pode especificar os seguintes tipos do próximo salto ao criar uma rota definida pelo usuário:

- **Solução de virtualização**: uma solução de virtualização é uma máquina virtual que normalmente executa um aplicativo de rede, como um firewall. Para saber mais sobre uma variedade de soluções de virtualização de rede pré-configuradas que podem ser implantadas em uma rede virtual, confira o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Quando você cria uma rota com o tipo de salto **solução de virtualização**, também especifica o endereço IP de um próximo salto. O endereço IP pode ser:

  - O [endereço IP privado](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) de uma interface de rede anexada a uma máquina virtual. Qualquer interface de rede anexada a uma máquina virtual que encaminhe tráfego de rede para um endereço diferente do seu próprio deve ter a opção *Habilitar encaminhamento de IP* do Azure habilitada para isso. A configuração desabilita a verificação do Azure sobre a origem e o destino de uma interface de rede. Saiba mais sobre como [habilitar o encaminhamento de IP de uma interface de rede](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Embora *Habilitar encaminhamento de IP* seja uma configuração do Azure, talvez também seja preciso habilitar o encaminhamento de IP no sistema operacional da máquina virtual para que o dispositivo encaminhe o tráfego entre os endereços IP para as interfaces de rede do Azure. Se o dispositivo tiver que rotear o tráfego para um endereço IP público, ele deverá fazer o proxy do tráfego ou o endereço de rede deverá traduzir o endereço IP privado do endereço IP privado da origem para seu próprio endereço IP privado, que o endereço de rede do Azure traduzirá em um endereço IP público, antes de enviar o tráfego para a Internet. Para determinar as configurações necessárias na máquina virtual, confira a documentação para seu sistema operacional ou aplicativo de rede. Para entender as conexões de saída no Azure, consulte [Entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    > [!NOTE]
    > Implante uma solução de virtualização em uma sub-rede diferente daquela em que estão implantados os recursos que roteiam por meio da solução de virtualização. Ao implantar a solução de virtualização à mesma sub-rede e depois aplicar uma tabela de rotas para a sub-rede que roteia o tráfego por meio da solução de virtualização, é possível que isso resulte em loops de roteamento em que o tráfego nunca saia da sub-rede.

  - O endereço IP privado de um [balanceador de carga interno](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Um balanceador de carga geralmente é usado como parte de uma [estratégia de alta disponibilidade para soluções de virtualização de rede](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    É possível definir uma rota com 0.0.0.0/0 como o prefixo de endereço e um tipo do próximo salto da solução de virtualização, permitindo que a solução inspecione o tráfego e determine encaminhar ou descartar o tráfego. Se você pretende criar uma rota definida pelo usuário que contenha o prefixo de endereço 0.0.0.0/0, primeiro confira [Prefixo de endereço 0.0.0.0/0](#default-route).

- **Gateway de rede virtual**: especifique quando você deseja que o tráfego destinado para prefixos de endereço específicos seja encaminhado para um gateway de rede virtual. O gateway de rede virtual deve ser criado com o tipo **VPN**. Você não pode especificar um gateway de rede virtual criado como tipo **ExpressRoute** em uma rota definida pelo usuário porque, com o ExpressRoute, você deve usar BGP para rotas personalizadas. Você pode definir uma rota que direciona o tráfego destinado para o prefixo de endereço 0.0.0.0/0 para um gateway de rede virtual [baseado em rota](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). Em seus locais, você pode ter um dispositivo que inspecione o tráfego e determine encaminhar ou descartar o tráfego. Se você pretende criar uma rota definida pelo usuário para o prefixo de endereço 0.0.0.0/0, primeiro confira [Prefixo de endereço 0.0.0.0/0](#default-route). Em vez de configurar uma rota definida pelo usuário para o prefixo de endereço 0.0.0.0/0, é possível anunciar uma rota com o prefixo 0.0.0.0/0 por meio do BGP, caso tenha [habilitado o BGP para um gateway de rede virtual de VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Nenhum**: especifique quando você deseja remover o tráfego para um prefixo de endereço em vez de encaminhar o tráfego para um destino. Se você ainda não configurou totalmente um recurso, o Azure pode listar *Nenhum* para algumas das rotas de sistema opcionais. Por exemplo, se você vir *Nenhum* listado como o **Endereço IP do próximo salto** com um **Tipo do próximo salto** de *Gateway de rede virtual* ou *Solução de virtualização*, isso pode ocorrer porque o dispositivo não está em execução ou não está totalmente configurado. O Azure cria [rotas padrão](#default) de sistema para prefixos de endereço reservados com **Nenhum** como o tipo do próximo salto.
- **Rede virtual**: especifique quando você deseja substituir o roteamento padrão em uma rede virtual. Confira [Exemplo de roteamento](#routing-example) para obter um exemplo de por que você pode criar uma rota com o tipo de salto **Rede virtual**.
- **Internet**: especifique quando você deseja encaminhar explicitamente o tráfego destinado a um prefixo de endereço para a Internet ou se desejar que o tráfego destinado para serviços do Azure com endereços IP públicos seja mantido dentro da rede de backbone do Azure.

Não é possível especificar **Emparelhamento VNet** ou **VirtualNetworkServiceEndpoint** como o tipo do próximo salto em rotas definidas pelo usuário. Rotas com os tipos do próximo salto **Emparelhamento VNet** ou **VirtualNetworkServiceEndpoint** são criadas somente pelo Azure, quando você configurar uma emparelhamento de rede virtual ou um ponto de extremidade de serviço.

**Tipos do próximo salto nas ferramentas do Azure**

O nome exibido e referenciado para tipos do próximo salto é diferente entre o portal do Azure e as ferramentas de linha de comando, e entre o Azure Resource Manager e modelos clássicos de implantação. A tabela a seguir lista os nomes usados para se referir a cada tipo do próximo salto com as diferentes ferramentas e [modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Tipo do próximo salto                   |CLI do Azure e o PowerShell (Resource Manager) |CLI clássica do Azure e o PowerShell (clássico)|
|-------------                   |---------                                       |-----|
|Gateway de rede virtual         |VirtualNetworkGateway                           |VPNGateway|
|Rede virtual                 |VNetLocal                                       |VNETLocal (não disponível no CLI clássico no modo asm)|
|Internet                        |Internet                                        |Internet (não disponível no CLI clássico no modo asm)|
|Solução de virtualização               |VirtualAppliance                                |VirtualAppliance|
|Nenhum                            |Nenhum                                            |NULL (não disponível no CLI clássico no modo asm)|
|Emparelhamento de rede virtual         |Emparelhamento VNet                                    |Não aplicável|
|Ponto de extremidade de serviço de rede virtual|VirtualNetworkServiceEndpoint                   |Não aplicável|

### <a name="border-gateway-protocol"></a>Protocolo BGP

Um gateway de rede local pode trocar rotas com um gateway de rede virtual do Azure usando o protocolo BGP (Border Gateway Protocol). O uso do BGP com um gateway de rede virtual do Azure depende do tipo que você selecionou ao criar o gateway. Se o tipo selecionado foi:

- **ExpressRoute**: você deve usar o BGP para anunciar rotas locais para o roteador de borda da Microsoft. Não é possível criar rotas definidas pelo usuário para forçar o tráfego ao gateway de rede virtual do ExpressRoute caso você implante um gateway de rede virtual implantado como tipo: ExpressRoute. Você pode usar as rotas definidas pelo usuário para forçar o tráfego do ExpressRoute para, por exemplo, uma Solução de Virtualização de Rede.
- **VPN**: você pode, opcionalmente, usar o BGP. Para obter detalhes, confira [BGP com conexões VPN site a site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ao trocar de rotas com o Azure usando o BGP, uma rota separada é adicionada à tabela de rotas de todas as sub-redes em uma rede virtual para cada prefixo anunciado. A rota é adicionada com o *Gateway de rede virtual* listado como a fonte e o tipo do próximo salto. 

A propagação de rotas de Gateway de VPN e ER pode ser desabilitada em uma sub-rede usando uma propriedade em uma tabela de rotas. Quando você troca rotas com o Azure usando o BGP, as rotas não são adicionadas à tabela de rotas das sub-redes com propagação BGP desabilitada. A conectividade com conexões VPN é feita usando [rotas personalizadas](#custom-routes) com um tipo de *gateway de rede virtual* de próximo salto. Para obter detalhes, consulte [Como desabilitar a propagação de rotas BGP](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Como o Azure seleciona uma rota

Quando o tráfego de saída é enviado de uma sub-rede, o Azure seleciona uma rota com base no endereço IP de destino usando o algoritmo de correspondência com o prefixo mais longo. Por exemplo, uma tabela de rotas tem duas rotas: uma rota especifica o prefixo de endereço 10.0.0.0/24, enquanto a outra especifica o prefixo de endereço 10.0.0.0/16. O Azure roteia o tráfego destinado a 10.0.0.5 para o tipo do próximo salto especificado na rota com o prefixo de endereço 10.0.0.0/24 porque 10.0.0.0/24 é um prefixo mais longo que 10.0.0.0/16, mesmo que 10.0.0.5 esteja dentro de ambos os prefixos de endereço. O Azure roteia o tráfego destinado a 10.0.1.5 para o tipo do próximo salto especificado na rota com o prefixo de endereço 10.0.0.0/16 porque 10.0.1.5 não está incluído no prefixo de endereço 10.0.0.0/24, portanto, a rota com o prefixo de endereço 10.0.0.0/16 é o prefixo correspondente mais longo.

Se várias rotas contêm o mesmo prefixo de endereço, o Azure seleciona o tipo de rota com base na seguinte ordem de prioridade:

1. Rota definida pelo usuário
2. Rota BGP
3. Rota de sistema

> [!NOTE]
> As rotas de sistema para o tráfego relativas à rede virtual, aos emparelhamentos de rede virtual ou aos pontos de extremidade de serviço de rede virtual são rotas preferenciais, mesmo que as rotas BGP sejam mais específicas.

Por exemplo, uma tabela de rotas contém as seguintes rotas:


|Fonte   |Prefixos do endereço  |Tipo do próximo salto           |
|---------|---------         |-------                 |
|Padrão  | 0.0.0.0/0        |Internet                |
|Usuário     | 0.0.0.0/0        |Gateway de rede virtual |

Quando o tráfego é destinado a um endereço IP fora dos prefixos de endereço de qualquer outra rota na tabela de rotas, o Azure seleciona a rota com a fonte de **Usuário** porque rotas definidas pelo usuário têm prioridade maior do que as rotas de sistema padrão.

Confira [Exemplo de roteamento](#routing-example) para ver uma tabela de roteamento abrangente com explicações sobre as rotas na tabela.

## <a name="default-route"></a>Prefixo de endereço 0.0.0.0/0

Uma rota com o prefixo de endereço 0.0.0.0/0 instrui o Azure sobre como rotear o tráfego destinado a um endereço IP que não está dentro do prefixo de endereço de nenhuma outra rota na tabela de rotas da sub-rede. Quando uma sub-rede é criada, o Azure cria uma rota [padrão](#default) para o prefixo de endereço 0.0.0.0/0, com o tipo do próximo salto **Internet**. Se você não substituir essa rota, o Azure roteia para a Internet todo o tráfego destinado para endereços IP não incluídos no prefixo de endereço de nenhuma outra rota. A exceção é que o tráfego para os endereços IP públicos de serviços do Azure permanece na rede de backbone do Azure e não é roteado para a Internet. Se você substituir essa rota por uma rota [personalizada](#custom-routes), o tráfego destinado a endereços que não estão dentro de prefixos de endereço de nenhuma outra rota na tabela de rotas é enviado a uma solução de virtualização de rede ou a um gateway de rede virtual, dependendo de qual você especificar em uma rota personalizada.

Quando você substitui o prefixo de endereço 0.0.0.0/0, além do tráfego de saída da sub-rede fluindo pelo gateway de rede virtual ou pela solução de virtualização, ocorrem as seguintes alterações com o roteamento padrão do Azure: 

- O Azure envia todo o tráfego para o tipo do próximo salto especificado na rota para incluir o tráfego destinado a endereços IP públicos de serviços do Azure. Quando o tipo do próximo salto para a rota com o prefixo de endereço 0.0.0.0/0 é **Internet**, o tráfego da sub-rede destinado a endereços IP públicos de serviços do Azure nunca deixa a rede de backbone do Azure, independentemente da região do Azure na qual existe a rede virtual ou o recurso de serviço do Azure. Porém, quando você cria uma rota definida pelo usuário ou BGP com um tipo do próximo salto **Gateway de rede virtual** ou **solução de virtualização**, todo o tráfego, incluindo o tráfego enviado aos endereços IP públicos dos serviços do Azure para os quais você ainda não habilitou os [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md), é enviado para o tipo do próximo salto especificado na rota. Caso tenha habilitado um ponto de extremidade de serviço para um serviço, o tráfego para o serviço não é roteado para o tipo do próximo salto em uma rota com o prefixo de endereço 0.0.0.0/0 porque os prefixos de endereço para o serviço são especificados na rota que o Azure cria quando você habilita o ponto de extremidade de serviço, e os prefixos de endereço para o serviço são maiores que 0.0.0.0/0.
- Você já não consegue acessar diretamente os recursos na sub-rede pela Internet. É possível acessar indiretamente recursos na sub-rede pela Internet caso o tráfego de entrada passe pelo dispositivo especificado pelo tipo do próximo salto para uma rota com o prefixo de endereço 0.0.0.0/0 antes de alcançar o recurso na rede virtual. Se a rota contiver os seguintes valores para o tipo do próximo salto:
    - **Solução de virtualização**: A solução deve:
        - Ser acessíveis pela Internet
        - Ter um endereço IP público atribuído a ela
        - Não ter uma regra de grupo de segurança de rede associada a ela que impeça a comunicação com o dispositivo
        - Não negar a comunicação
        - Ser capaz de converter o endereço de rede e encaminhar ou ser proxy do tráfego para o recurso de destino na sub-rede e retornar o tráfego de volta para a Internet. 
    - **Gateway de rede virtual**: se o gateway for um gateway de rede virtual ExpressRoute, um dispositivo local conectado à Internet poderá converter o endereço de rede e encaminhar ou ser proxy do tráfego para o recurso de destino na sub-rede por meio do [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering) do ExpressRoute. 

Se sua rede virtual está conectada a um gateway de VPN do Azure, não associe a uma tabela de rota para o [sub-rede de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que inclui uma rota com um destino de 0.0.0.0/0. Isso pode impedir que o gateway funcione corretamente. Para obter detalhes, consulte a pergunta *Por que determinadas portas estão abertas no meu gateway de VPN?* em [Perguntas frequentes sobre o Gateway de VPN](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports).

Confira [Rede de perímetro entre o Azure e seu datacenter local](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Rede de perímetro entre Azure e a Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes de implementação ao usar gateways de rede virtual e soluções de virtualização entre a Internet e o Azure.

## <a name="routing-example"></a>Exemplo de roteamento

Para ilustrar os conceitos neste artigo, as seções a seguir descrevem:
- Um cenário, com requisitos
- As rotas personalizadas necessárias para atender aos requisitos
- A tabela de rotas que existe para uma sub-rede que inclui as rotas padrão e personalizadas necessárias para atender aos requisitos

> [!NOTE]
> Este exemplo não tem a pretensão de ser uma implementação recomendada ou de melhor prática. Em vez disso, é fornecido apenas para ilustrar conceitos neste artigo.

### <a name="requirements"></a>Requisitos

1. Implementar duas redes virtuais na mesma região do Azure e habilitar recursos para se comunicar entre as redes virtuais.
2. Habilitar uma rede local para se comunicar de modo seguro com ambas as redes virtuais por meio de um túnel VPN pela Internet. *Como alternativa, pode ser usada uma conexão ExpressRoute, mas uma conexão VPN é usada neste exemplo.*
3. Para uma sub-rede em uma rede virtual:
 
    - Forçar todo o tráfego de saída da sub-rede, exceto para o Armazenamento do Azure e dentro da sub-rede, a fluir por meio de uma solução de virtualização de rede para inspeção e registro em log.
    - Não inspecionar o tráfego entre os endereços IP privados na sub-rede; permitir que o tráfego flua diretamente entre todos os recursos. 
    - Descartar qualquer tráfego de saída destinado para outra rede virtual.
    - Habilitar o tráfego de saída no armazenamento do Azure a fluir diretamente ao armazenamento, sem forçá-lo por meio de uma solução de virtualização de rede.

4. Permitir todo o tráfego entre todas as outras sub-redes e redes virtuais.

### <a name="implementation"></a>Implementação

A figura a seguir mostra uma implementação por meio do Modelo de implantação do Azure Resource Manager que atende aos requisitos anteriores:

![Diagrama de rede](./media/virtual-networks-udr-overview/routing-example.png)

As setas mostram o fluxo de tráfego. 

### <a name="route-tables"></a>Tabelas de rotas

#### <a name="subnet1"></a>Subnet1

A tabela de rotas da *Subnet1* na imagem contém as seguintes rotas:

|ID  |Fonte |Estado  |Prefixos do endereço    |Tipo do próximo salto          |Endereço IP do próximo salto|Nome da rota definida pelo usuário| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Padrão|Inválido|10.0.0.0/16         |Rede virtual        |                   |              |
|2   |Usuário   |Ativo |10.0.0.0/16         |Solução de virtualização      |10.0.100.4         |Within-VNet1  |
|3   |Usuário   |Ativo |10.0.0.0/24         |Rede virtual        |                   |Within-Subnet1|
|4   |Padrão|Inválido|10.1.0.0/16         |Emparelhamento VNet           |                   |              |
|5   |Padrão|Inválido|10.2.0.0/16         |Emparelhamento VNet           |                   |              |
|6   |Usuário   |Ativo |10.1.0.0/16         |Nenhum                   |                   |ToVNet2-1-Drop|
|7   |Usuário   |Ativo |10.2.0.0/16         |Nenhum                   |                   |ToVNet2-2-Drop|
|8   |Padrão|Inválido|10.10.0.0/16        |Gateway de rede virtual|[X.X.X.X]          |              |
|9   |Usuário   |Ativo |10.10.0.0/16        |Solução de virtualização      |10.0.100.4         |To-On-Prem    |
|10  |Padrão|Ativo |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Padrão|Inválido|0.0.0.0/0           |Internet               |                   |              |
|12  |Usuário   |Ativo |0.0.0.0/0           |Solução de virtualização      |10.0.100.4         |Default-NVA   |

Segue uma explicação de cada ID de rota:

1. O Azure adicionada automaticamente essa rota para todas as sub-redes dentro da *Virtual-network-1*, pois 10.0.0.0/16 é o único intervalo de endereços definido no espaço de endereço da rede virtual. Se a rota definida pelo usuário na rota ID2 não foi criada, o tráfego enviado para qualquer endereço entre 10.0.0.1 e 10.0.255.254 será roteado dentro da rede virtual porque o prefixo é maior do que 0.0.0.0/0, e não dentro dos prefixos de endereço de qualquer das outras rotas. O Azure alterou automaticamente o estado de *Ativo* para *Inválido* quando ID2, uma rota definida pelo usuário, foi adicionada, uma vez que ela tem o mesmo prefixo que a rota padrão, e rotas definidas pelo usuário substituem rotas padrão. O estado dessa rota ainda é *Ativo* para a *Subnet2*, pois a tabela de rotas na qual está a rota definida pelo usuário ID2 não está associada à *Subnet2*.
2. O Azure adicionou essa rota quando uma rota definida pelo usuário para o prefixo de endereço 10.0.0.0/16 estava associada à sub-rede *Subnet1* na rede virtual *Virtual-network-1*. A rota definida pelo usuário especifica 10.0.100.4 como o endereço IP da solução de virtualização porque o endereço é o endereço IP atribuído à máquina virtual da solução de virtualização. A tabela de rotas na qual essa rota existe não está associada à *Subnet2*, assim, ela não aparece na tabela de rotas da *Subnet2*. Essa rota substitui a rota padrão do prefixo 10.0.0.0/16 (ID1), que roteou automaticamente o tráfego endereçado a 10.0.0.1 e 10.0.255.254 dentro da rede virtual por meio do tipo do próximo salto da rede virtual. Essa rota existe para atender ao [requisito](#requirements) 3, para forçar todo o tráfego de saída por meio de uma solução de virtualização.
3. O Azure adicionou essa rota quando uma rota definida pelo usuário para o prefixo de endereço 10.0.0.0/24 estava associada à sub-rede *Subnet1*. O tráfego destinado a endereços entre 10.0.0.1 e 10.0.0.254 permanece dentro da sub-rede em vez de ser roteado para a solução de virtualização especificada na regra anterior (ID2) porque ela tem um prefixo mais longo que a rota ID2. Essa rota não estava associada à *Subnet2*, assim, a rota não aparece na tabela de rotas da *Subnet2*. Essa rota substitui efetivamente a rota ID2 para o tráfego dentro da *Subnet1*. Essa rota existe para atender ao [requisito](#requirements) 3.
4. O Azure adicionou automaticamente as rotas nas IDs 4 e 5 para todas as sub-redes dentro da *Virtual-network-1* quando a rede virtual foi emparelhada com a *Virtual-network-2.* *Virtual-network-2* tem dois intervalos de endereços em seu espaço de endereço: 10.1.0.0/16 e 10.2.0.0/16, portanto, o Azure adicionou uma rota para cada intervalo. Se as rotas definidas pelo usuário nas IDs de rota 6 e 7 não foram criadas, o tráfego enviado para qualquer endereço entre 10.1.0.1-10.1.255.254 e 10.2.0.1-10.2.255.254 será roteado para a rede virtual emparelhada porque o prefixo é maior do que 0.0.0.0/0, e não dentro dos prefixos de endereço de nenhuma das outras rotas. O Azure alterou automaticamente o estado de *Ativo* para *Inválido* quando as rotas nas IDs 6 e 7 foram adicionadas, uma vez que elas têm os mesmos prefixos que as rotas nas IDs 4 e 5, e rotas definidas pelo usuário substituem rotas padrão. O estado das rotas nas IDs 4 e 5 ainda é *Ativo* para a *Subnet2*, pois a tabela de rotas na qual estão as rotas definidas pelo usuário nas IDs 4 e 5 não está associada à *Subnet2*. Um emparelhamento de rede virtual foi criado para atender ao [requisito](#requirements) 1.
5. Mesma explicação da ID4.
6. O Azure adicionou essa rota e a rota na ID7 quando rotas definidas pelo usuário para os prefixos de endereço 10.1.0.0/16 e 10.2.0.0/16 foram associados à sub-rede *Subnet1*. O tráfego destinado a endereços entre 10.1.0.1-10.1.255.254 e 10.2.0.1-10.2.255.254 é descartado do Azure em vez de ser roteado para a rede virtual emparelhada porque rotas definidas pelo usuário substituem rotas padrão. As rotas não estão associadas à *Subnet2*, assim, as rotas não aparecem na tabela de rotas da *Subnet2*. As rotas substituem as rotas ID4 e ID5 para o tráfego deixando a *Subnet1*. As rotas ID6 e ID7 existem para atender ao [requisito](#requirements) 3 para descartar o tráfego destinado à outra rede virtual.
7. Mesma explicação da ID6.
8. O Azure adicionou automaticamente essa rota para todas as sub-redes *Virtual-network-1* quando um gateway de rede virtual do tipo VPN foi criado dentro da rede virtual. O Azure adicionou o endereço IP público do gateway de rede virtual à tabela de rotas. O tráfego enviado para qualquer endereço entre 10.10.0.1 e 10.10.255.254 é roteado para o gateway de rede virtual. O prefixo é maior do que 0.0.0.0/0 e não está dentro dos prefixos de endereço de nenhuma das outras rotas. Um gateway de rede virtual foi criado para atender ao [requisito](#requirements) 2.
9. O Azure adicionou essa rota quando uma rota definida pelo usuário para o prefixo de endereço 10.10.0.0/16 foi adicionada à tabela de rotas associada à *Subnet1*. Essa rota substitui a ID8. A rota envia todo o tráfego destinado para a rede local para uma NVA para inspeção em vez de rotear o tráfego diretamente no local. Essa rota foi criada para atender ao [requisito](#requirements) 3.
10. O Azure adicionou automaticamente essa rota à sub-rede quando um ponto de extremidade de serviço para um serviço do Azure foi habilitado para a sub-rede. O Azure roteia o tráfego da sub-rede para um endereço IP público do serviço pela rede de infraestrutura do Azure. O prefixo é maior do que 0.0.0.0/0 e não está dentro dos prefixos de endereço de nenhuma das outras rotas. Um ponto de extremidade de serviço foi criado para atender ao [requisito](#requirements) 3 para permitir que o tráfego destinado ao Armazenamento do Azure flua diretamente a ele.
11. O Azure adicionou automaticamente essa rota à tabela de rotas de todas as sub-redes dentro da *Virtual-network-1* e *Virtual-network-2.* O prefixo de endereço 0.0.0.0/0 é o prefixo mais curto. Qualquer tráfego enviado para endereços dentro de um prefixo de endereço mais longo é roteado com base em outras rotas. Por padrão, o Azure roteia todo o tráfego destinado para endereços que não sejam os endereços especificados em uma das outras rotas para a Internet. O Azure alterou automaticamente o estado de *Ativo* para *Inválido* para a sub-rede *Subnet1* quando uma rota definida pelo usuário para o prefixo de endereço 0.0.0.0/0 (ID12) estava associada à sub-rede. O estado dessa rota ainda é *Ativo* para todas as outras sub-redes dentro de ambas as redes virtuais porque a rota não está associada a nenhuma outra sub-rede em nenhuma outra rede virtual.
12. O Azure adicionou essa rota quando uma rota definida pelo usuário para o prefixo de endereço 0.0.0.0/0 estava associada à sub-rede *Subnet1*. A rota definida pelo usuário especifica 10.0.100.4 como o endereço IP da solução de virtualização. Essa rota não está associada à *Subnet2*, assim, a rota não aparece na tabela de rotas da *Subnet2*. Todo o tráfego para qualquer endereço não incluído nos prefixos de endereço de nenhuma das outras rotas é enviado para a solução de virtualização. A adição dessa rota alterou o estado da rota padrão para o prefixo de endereço 0.0.0.0/0 (ID11) de *Ativo* para *Inválido* da *Subnet1*, pois uma rota definida pelo usuário substitui uma rota padrão. Essa rota existe para atender ao [requisito](#requirements) 3.

#### <a name="subnet2"></a>Subnet2

A tabela de rotas da *Subnet2* na imagem contém as seguintes rotas:

|Fonte  |Estado  |Prefixos do endereço    |Tipo do próximo salto             |Endereço IP do próximo salto|
|------- |-------|------              |-------                   |--------           
|Padrão |Ativo |10.0.0.0/16         |Rede virtual           |                   |
|Padrão |Ativo |10.1.0.0/16         |Emparelhamento VNet              |                   |
|Padrão |Ativo |10.2.0.0/16         |Emparelhamento VNet              |                   |
|Padrão |Ativo |10.10.0.0/16        |Gateway de rede virtual   |[X.X.X.X]          |
|Padrão |Ativo |0.0.0.0/0           |Internet                  |                   |
|Padrão |Ativo |10.0.0.0/8          |Nenhum                      |                   |
|Padrão |Ativo |100.64.0.0/10       |Nenhum                      |                   |
|Padrão |Ativo |172.16.0.0/12       |Nenhum                      |                   |
|Padrão |Ativo |192.168.0.0/16      |Nenhum                      |                   |

A tabela de rotas para *Subnet2* contém todas as rotas padrão criadas pelo Azure, além do emparelhamento opcional VNet e rotas opcionais de gateway de rede virtual. O Azure adicionou as rotas opcionais para todas as sub-redes na rede virtual quando o gateway e o emparelhamento foram adicionados à rede virtual. O Azure removeu as rotas para os prefixos de endereço 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 e 100.64.0.0/10 da tabela de rotas da *Subnet1* quando a rota definida pelo usuário para o prefixo de endereço 0.0.0.0/0 foi adicionada à *Subnet1*.  

## <a name="next-steps"></a>Próximas etapas

- [Criar uma tabela de rotas definidas pelo usuário com rotas e uma solução de virtualização de rede](tutorial-create-route-table-portal.md)
- [Configurar o BGP para um Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Usar o BGP com ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)
- [Exibir todas as rotas de uma sub-rede](diagnose-network-routing-problem.md). Uma tabela de rotas definidas pelo usuário só mostra as rotas definidas pelo usuário, não as rotas padrão e BGP de uma sub-rede. Exibir todas as rotas mostra as rotas padrão, BGP e definidas pelo usuário da sub-rede na qual está uma interface de rede.
- [Determinar o tipo do próximo salto](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) entre uma máquina virtual e um endereço IP de destino. O recurso do salto seguinte do Observador de Rede do Azure permite que você determine se o tráfego está deixando uma sub-rede e sendo roteado para onde você acha que ele devem estar.
