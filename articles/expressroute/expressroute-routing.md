---
title: 'Azure ExpressRoute: requisitos de roteamento'
description: Esta página fornece requisitos detalhados para a configuração e gerenciamento de roteamento para circuitos do ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: duau
ms.openlocfilehash: 0dc2b48d02eb8a69afc947891c263ef1510257a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721830"
---
# <a name="expressroute-routing-requirements"></a>Requisitos de roteamento da Rota Expressa
Para se conectar aos serviços de nuvem da Microsoft usando o ExpressRoute, você precisará configurar e gerenciar o roteamento. Alguns provedores de conectividade oferecem a configuração e o gerenciamento de roteamento como um serviço gerenciado. Verifique se o seu provedor de conectividade oferece esse serviço. Se não oferecer, você deverá atender aos requisitos a seguir:

Consulte o artigo [Circuitos e domínios de roteamento](expressroute-circuit-peerings.md) para ver uma descrição das sessões de roteamento que precisam ser configuradas para facilitar a conectividade.

> [!NOTE]
> A Microsoft não dá suporte aos protocolos de redundância do roteador (por exemplo, HSRP e VRRP) para as configurações de alta disponibilidade. Contamos com um par redundante de sessões BGP por emparelhamento para alta disponibilidade.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Endereços IP usados para emparelhamentos
Você precisa reservar alguns blocos de endereços IP para configurar o roteamento entre a rede e os roteadores de borda (MSEEs) corporativos da Microsoft. Esta seção fornece uma lista de requisitos e descreve as regras para a maneira como esses endereços IP devem ser adquiridos e usados.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Endereços IP usados para o emparelhamento privado do Azure
Você pode usar endereços IP privados ou endereços IP públicos para configurar os emparelhamentos. O intervalo de endereços usado para configurar rotas não deve se sobrepor aos intervalos de endereços usados para criar redes virtuais no Azure. 

* IPv4:
    * Você deve reservar uma sub-rede /29 ou duas sub-redes /30 para interfaces de roteamento.
    * As sub-redes usadas para roteamento podem ser endereços IP privados ou endereços IP públicos.
    * As sub-redes não devem entrar em conflito com o intervalo reservado pelo cliente para uso na nuvem da Microsoft.
    * Se uma sub-rede /29 for usada, ela será dividida em duas sub-redes /30. 
      * A primeira sub-rede /30 é usada para o link principal e a segunda sub-rede /30 é usada para o link secundário.
      * Para cada uma das sub-redes /30, é necessário usar o primeiro endereço IP da sub-rede /30 em seu roteador. A Microsoft usa o segundo endereço IP da sub-rede /30 para configurar uma sessão BGP.
      * Você deve configurar ambas as sessões BGP para que nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.
* IPv6:
    * Você deve reservar uma sub-rede/125 ou duas sub-redes/126 para interfaces de roteamento.
    * As sub-redes usadas para roteamento podem ser endereços IP privados ou endereços IP públicos.
    * As sub-redes não devem entrar em conflito com o intervalo reservado pelo cliente para uso na nuvem da Microsoft.
    * Se uma sub-rede /125 for usada, será dividida em duas sub-redes /126. 
      * A primeira sub-rede/126 é usada para o link primário e a segunda sub-rede/30 é usada para o link secundário.
      * Para cada uma das sub-redes /126, é necessário usar o primeiro endereço IP da sub-rede /126 em seu roteador. A Microsoft usa o segundo endereço IP da sub-rede /126 para configurar uma sessão BGP.
      * Você deve configurar ambas as sessões BGP para que nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

#### <a name="example-for-private-peering"></a>Exemplo para emparelhamento privado
Se você optar por usar a.b.c.d/29 para configurar o emparelhamento, ela será dividida em duas sub-redes /30. No exemplo a seguir, observe como a sub-rede a.b.c.d / 29 é usada:

* a.b.c.d/29 será dividida em a.b.c.d/30 e a.b.c.d+4/30 e passada à Microsoft por meio das APIs de provisionamento.
  * Você usará a.b.c.d+1 como o IP VRF para o PE Primário, e a Microsoft consumirá a.b.c.d+2 como o IP VRF para o MSEE primário.
  * Você usará a.b.c.d+5 como o IP VRF para o PE secundário, e a Microsoft usará a.b.c.d+6 como o IP VRF para o MSEE secundário.

Considere um caso em que você seleciona 192.168.100.128/29 para configurar o emparelhamento privado. 192.168.100.128/29 inclui os endereços de 192.168.100.128 a 192.168.100.135, entre os quais:

* 192.168.100.128/30 será atribuído ao link1, com o provedor usando 192.168.100.129 e a Microsoft usando 192.168.100.130.
* 192.168.100.132/30 será atribuído ao link2, com o provedor usando 192.168.100.133 e a Microsoft usando 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Endereços IP usados para emparelhamento da Microsoft
Você deve usar endereços IP públicos que possui para configurar as sessões BGP. A Microsoft deve ser capaz de verificar a propriedade dos endereços IP por meio de Registros da Internet de Roteamento e Registros de Roteamento da Internet.

* Os IPs listados no portal para prefixos públicos anunciados para o Emparelhamento da Microsoft criarão ACLs para os roteadores de núcleo da Microsoft para permitir o tráfego de entrada desses IPs. 
* Você deve usar uma sub-rede /29 (IPv4) ou /125 (IPv6) ou duas sub-redes /30 (IPv4) ou /126 (IPv6) para configurar o emparelhamento BGP para cada emparelhamento por circuito ExpressRoute(se houver mais de um).
* Se uma sub-rede /29 for usada, ela será dividida em duas sub-redes /30.
* A primeira sub-rede /30 é usada para o link principal e a segunda sub-rede /30 será usada para o link secundário.
* Para cada uma das sub-redes /30, é necessário usar o primeiro endereço IP da sub-rede /30 em seu roteador. A Microsoft usa o segundo endereço IP da sub-rede /30 para configurar uma sessão BGP.
* Se uma sub-rede /125 for usada, será dividida em duas sub-redes /126.
* A primeira sub-rede /126 será usada para o link principal e a segunda sub-rede /126 é usada para o link secundário.
* Para cada uma das sub-redes /126, é necessário usar o primeiro endereço IP da sub-rede /126 em seu roteador. A Microsoft usa o segundo endereço IP da sub-rede /126 para configurar uma sessão BGP.
* Você deve configurar ambas as sessões BGP para que nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Endereços IP usados para o emparelhamento público do Azure

> [!NOTE]
> Não está disponível para novos circuitos do emparelhamento público do Azure.
> 

Você deve usar endereços IP públicos que possui para configurar as sessões BGP. A Microsoft deve ser capaz de verificar a propriedade dos endereços IP por meio de Registros da Internet de Roteamento e Registros de Roteamento da Internet. 

* Você deve usar uma sub-rede /29 exclusiva ou duas sub-redes /30 para configurar o emparelhamento BGP para cada emparelhamento por circuito da Rota Expressa (se houver mais de um). 
* Se uma sub-rede /29 for usada, ela será dividida em duas sub-redes /30. 
  * A primeira sub-rede /30 é usada para o link principal e a segunda sub-rede /30 é usada para o link secundário.
  * Para cada uma das sub-redes /30, é necessário usar o primeiro endereço IP da sub-rede /30 em seu roteador. A Microsoft usa o segundo endereço IP da sub-rede /30 para configurar uma sessão BGP.
  * Você deve configurar ambas as sessões BGP para que nosso [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/) seja válido.

## <a name="public-ip-address-requirement"></a>Requisito do endereço IP público

### <a name="private-peering"></a>Emparelhamento privado
Você pode optar por usar os endereços IPv4 públicos ou privados para o emparelhamento privado. Podemos fornecer um isolamento de ponta a ponta do tráfego para que a sobreposição dos endereços com outros clientes não seja possível no caso do emparelhamento privado. Esses endereços não são divulgados na Internet. 

### <a name="microsoft-peering"></a>Emparelhamento da Microsoft
O caminho de emparelhamento da Microsoft permite conectar os serviços em nuvem da Microsoft. A lista de serviços inclui serviços Microsoft 365s, como o Exchange Online, o SharePoint Online, o Skype for Business e o Microsoft Teams. A Microsoft dá suporte à conectividade bidirecional no emparelhamento da Microsoft. O tráfego destinado aos serviços de nuvem da Microsoft deve usar os endereços IPv4 públicos válidos antes de entrar na rede da Microsoft.

Verifique se o endereço IP e o número de AS estão registrados em um dos registros abaixo:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Se seus prefixos e número AS não forem atribuídos a você nos registros anteriores, será necessário abrir um caso de suporte para validação manual dos prefixos e ASN. O suporte exigirá a documentação, como uma Carta de Autorização, que prove que você pode usar os recursos.

Um Número de AS Privado é permitido com o emparelhamento da Microsoft, mas também exigirá a validação manual. Além disso, removemos números de AS privados no CAMINHO AS para os prefixos recebidos. Como resultado, não é possível anexar números de AS públicos no CAMINHO AS para [influenciar o roteamento para o Microsoft Peering](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Não anuncie a mesma rota IP pública à Internet pública e pelo ExpressRoute. Para reduzir o risco de configuração incorreta provocar roteamento assimétrico, recomendamos fortemente que os [endereços IP NAT](expressroute-nat.md) anunciados para a Microsoft via ExpressRoute estejam em um intervalo não anunciado para a Internet de modo algum. Caso isso não seja possível, é essencial garantir que você anuncie um intervalo mais específico por meio do ExpressRoute que aquele na conexão de Internet. Além da rota pública para NAT, você também pode anunciar por ExpressRoute os endereços IP públicos usados pelos servidores em sua rede local que se comunicam com os pontos de extremidade Microsoft 365 na Microsoft. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Emparelhamento público (preterido - não disponível para novos circuitos)
O caminho do emparelhamento público do Azure permite que você se conecte a todos os serviços hospedados no Azure por meio de seus endereços IP públicos. Isso inclui os serviços listados nas [Perguntas Frequentes sobre a Rota Expressa](expressroute-faqs.md) e quaisquer serviços hospedados por ISVs no Microsoft Azure. A conectividade com os serviços do Microsoft Azure no emparelhamento público é sempre iniciada de sua rede para a rede da Microsoft. Você deve usar os endereços IP Públicos para o tráfego destinado à rede da Microsoft.

> [!IMPORTANT]
> Todos os serviços de PaaS do Azure são acessíveis por meio do emparelhamento da Microsoft.
>   

Um número de AS Privado é permitido com emparelhamento público.

## <a name="dynamic-route-exchange"></a>Intercâmbio de roteamento dinâmico
O intercâmbio de roteamento será por meio do protocolo eBGP. As sessões EBGP são estabelecidas entre os MSEEs e os roteadores. A autenticação de sessões BGP não é um requisito. Se necessário, um hash MD5 pode ser configurado. Consulte [Configurar roteamento](how-to-routefilter-portal.md) e [luxos de trabalho de provisionamento e estados do circuito](expressroute-workflows.md) para obter informações sobre como configurar as sessões BGP.

## <a name="autonomous-system-numbers"></a>Números de sistema autônomos
A Microsoft usa AS 12076 para o emparelhamento público do Azure, o emparelhamento privado do Azure e o emparelhamento da Microsoft. Reservamos os ASNs de 65515 a 65520 para o uso interno. Há suporte para números AS de 16 e 32 bits.

Não há requisitos de simetria de transferência de dados. Os caminhos de encaminhamento e retorno podem atravessar pares de roteadores diferentes. Rotas idênticas devem ser anunciadas de qualquer um dos lados entre vários pares de circuitos que pertencem a você. As métricas de rotas não precisam ser idênticas.

## <a name="route-aggregation-and-prefix-limits"></a>Agregação de rotas e limites de prefixo
Damos suporte a até 4000 prefixos IPv4 e 100 prefixos IPv6 anunciados para nós por meio do emparelhamento privado do Azure. Isso pode ser aumentado até 10.000 prefixos IPv4 se o complemento Premium do ExpressRoute estiver habilitado. Aceitamos até 200 prefixos por sessão BGP para o emparelhamento público do Azure e o emparelhamento da Microsoft. 

A sessão BGP é descartada se o número de prefixos exceder o limite. Aceitaremos rotas padrão somente no link de emparelhamento privado. O provedor deve filtrar a rota padrão e endereços IP privados (RFC 1918) dos caminhos de emparelhamento público do Azure e emparelhamento da Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Roteamento de tráfego e de roteamento entre regiões
A Rota Expressa não pode ser configurada como roteadores de tráfego. Você precisará contar com seu provedor de conectividade para serviços de roteamento de tráfego.

## <a name="advertising-default-routes"></a>Anunciando rotas padrão
As rotas padrão são permitidas apenas em sessões de emparelhamento privado do Azure. Nesse caso, rotearemos todo o tráfego das redes virtuais associadas para sua rede. Anunciar rotas padrão para emparelhamento privado fará com que o caminho da Internet do Azure seja bloqueado. Você deve contar com sua borda corporativa para rotear o tráfego de e para a Internet para serviços hospedados no Azure. 

 Para habilitar a conectividade com outros serviços do Azure e serviços de infraestrutura, verifique se um dos seguintes itens está em vigor:

* O emparelhamento público do Azure está habilitado para rotear o tráfego para pontos de extremidade públicos.
* Você usa o roteamento definido pelo usuário para permitir a conectividade da Internet para cada sub-rede que requer conectividade com a Internet.

> [!NOTE]
> Anunciar rotas padrão irá interromper o Windows e outra ativação de licença da VM. Siga as instruções [aqui](/archive/blogs/mast/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling) para solucionar esse problema.
> 
> 

## <a name="support-for-bgp-communities"></a><a name="bgp"></a>Suporte a comunidades BGP
Esta seção fornece uma visão geral de como as comunidades BGP serão usadas com o ExpressRoute. A Microsoft anunciará rotas nos caminhos de emparelhamento público e da Microsoft com rotas marcadas com valores de comunidade apropriados. A lógica para fazer isso e os detalhes de valores de comunidade são descritos abaixo. No entanto, a Microsoft não adotará valores de comunidade marcados para rotas anunciadas à Microsoft.

Se estiver se conectando à Microsoft por meio da Rota Expressa em qualquer local de emparelhamento dentro de uma região geopolítica, você terá acesso a todos os serviços de nuvem da Microsoft em todas as regiões dentro dos limites geopolíticos. 

Por exemplo, se você estiver conectado à Microsoft em Amsterdã por meio da Rota Expressa, terá acesso a todos os serviços de nuvem da Microsoft hospedados no Norte da Europa e na Europa Ocidental. 

Consulte a página [Locais de emparelhamento e parceiros do ExpressRoute](expressroute-locations.md) para obter uma lista detalhada das regiões geopolíticas, regiões associadas do Azure e locais de emparelhamento correspondentes do ExpressRoute.

Você pode adquirir mais de um circuito da Rota Expressa por região geopolítica. Ter várias conexões oferece vantagens significativas para a alta disponibilidade devido à redundância geográfica. Nos casos em que há vários circuitos do ExpressRoute, você receberá o mesmo conjunto de prefixos divulgados pela Microsoft nos caminhos de emparelhamento público e emparelhamento da Microsoft. Isso significa que você terá vários caminhos de sua rede até a Microsoft. Potencialmente, isso pode fazer com que decisões de roteamento não ideais sejam tomadas em sua rede. Como resultado, você pode ter experiências de conectividade não ideal para diferentes serviços. Você pode contar com os valores de comunidade para tomar decisões de roteamento apropriadas e oferecer o [roteamento ideal aos clientes](expressroute-optimize-routing.md).

| **Região do Microsoft Azure** | **Comunidade de BGP regional** | **Comunidade de BGP de armazenamento** | **Comunidade BGP do SQL** | **Cosmos DB comunidade BGP** | **Comunidade BGP de backup** |
| --- | --- | --- | --- | --- | --- |
| **América do Norte** | |
| Leste dos EUA | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 | 12076:55004 |
| Leste dos EUA 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 | 12076:55005 |
| Oeste dos EUA | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 | 12076:55006 |
| Oeste dos EUA 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 | 12076:55026 |
| Centro-Oeste dos EUA | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 | 12076:55027 |
| Centro-Norte dos EUA | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 | 12076:55007 |
| Centro-Sul dos Estados Unidos | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 | 12076:55008 |
| Centro dos EUA | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 | 12076:55009 |
| Canadá Central | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 | 12076:55020 |
| Leste do Canadá | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 | 12076:55021 |
| **América do Sul** | |
| Sul do Brasil | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 | 12076:55014 |
| **Europa** | |
| Norte da Europa | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 | 12076:55003 |
| Europa Ocidental | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 | 12076:55002 |
| Sul do Reino Unido | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 | 12076:55024 |
| Oeste do Reino Unido | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 | 12076:55025 |
| França Central | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 | 12076:55030 |
| Sul da França | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 | 12076:55031 |
| Norte da Suíça | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 12076:55038 |
| Oeste da Suíça | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 12076:55039 | 
| Norte da Alemanha | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 12076:55040 | 
| Centro-Oeste da Alemanha | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 12076:55041 | 
| Leste da Noruega | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 12076:55042 | 
| Oeste da Noruega | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 12076:55043 | 
| **Pacífico Asiático** | |
| Leste da Ásia | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 | 12076:55010 |
| Sudeste Asiático | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 | 12076:55011 |
| **Japão** | |
| Japan East | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 | 12076:55012 |
| Oeste do Japão | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 | 12076:55013 |
| **Austrália** | |
| Leste da Austrália | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 | 12076:55015 |
| Australia Southeast | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 | 12076:55016 |
| **Governo da Austrália** | |
| Austrália Central | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 | 12076:55032 |
| Austrália Central 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 | 12076:55033 |
| **Índia** | |
| Sul da Índia | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 | 12076:55019 |
| Oeste da Índia | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 | 12076:55018 |
| Centro da Índia | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 | 12076:55017 |
| **Coreia do Sul** | |
| Sul da Coreia | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 | 12076:55028 |
| Coreia Central | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 | 12076:55029 |
| **África do Sul**| |
| Norte da África do Sul | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 | 12076:55034 |
| Oeste da África do Sul | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 | 12076:55035 |
| **EAU**| |
| Norte dos EAU | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 | 12076:55036 |
| EAU Central | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 | 12076:55037 |


Todas as rotas anunciadas pela Microsoft serão marcadas com o valor de comunidade apropriado. 

> [!IMPORTANT]
> Os prefixos globais são marcados com um valor de comunidade apropriado.
> 
> 

### <a name="service-to-bgp-community-value"></a>Valor da comunidade de serviço para BGP
Além disso, a Microsoft também marcará prefixos com base no serviço ao qual eles pertencem. Isso se aplica somente ao emparelhamento da Microsoft. A tabela a seguir fornece um mapeamento de serviço para o valor de comunidade BGP. Você pode executar o cmdlet ' Get-AzBgpServiceCommunity ' para obter uma lista completa dos valores mais recentes.

| **Serviço** | **Valor de comunidade BGP** |
| --- | --- |
| Exchange Online\*\* | 12076:5010 |
| SharePoint Online\*\* | 12076:5020 |
| Skype for Business Online\*\*/\*\*\* | 12076:5030 |
| CRM Online\*\*\*\* |12076:5040 |
| Serviços globais do Azure\* | 12076:5050 |
| Azure Active Directory |12076:5060 |
| Azure Resource Manager |12076:5070 |
| Outros serviços online do Office 365 * * | 12076:5100 |

\* Os serviços globais do Azure incluem apenas DevOps do Azure no momento.

\*\* Autorização necessária da Microsoft, consulte [Configurar filtros de rota para o emparelhamento da Microsoft](how-to-routefilter-portal.md)

\*\*\* Essa comunidade também publica as rotas necessárias para os serviços do Microsoft Teams.

\*\*\*\* O CRM Online dá suporte ao Dynamics v 8.2 e inferior. Para versões superiores, selecione a comunidade regional para suas implantações do Dynamics.

> [!NOTE]
> A Microsoft não atende a valores de comunidade BGP definidos por você nas rotas anunciadas para a Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Suporte à Comunidade de BGP no National Clouds

| **Região do Azure de Nuvens Nacionais**| **Valor de comunidade BGP** |
| --- | --- |
| **Governo dos EUA** |  |
| Governo dos EUA do Arizona | 12076:51106 |
| US Gov Iowa | 12076:51109 |
| Gov. dos EUA – Virgínia | 12076:51105 |
| Governo dos EUA do Texas | 12076:51108 |
| DoD Central dos EUA | 12076:51209 |
| DoD do Leste dos EUA | 12076:51205 |


| **Serviço nas Nuvens Nacionais** | **Valor de comunidade BGP** |
| --- | --- |
| **Governo dos EUA** |  |
| Exchange Online |12076:5110 |
| SharePoint online |12076:5120 |
| Skype for Business Online |12076:5130 |
| Azure Active Directory |12076:5160 |
| Outros serviços Online do Office 365 |12076:5200 |

## <a name="next-steps"></a>Próximas etapas
* Configurar sua conexão do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
