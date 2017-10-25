---
title: Configurar a Rede Virtual para um Cache Redis do Azure Premium | Microsoft Docs
description: "Saiba como criar e gerenciar o suporte de Rede Virtual para as instâncias da camada Premium do Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: sdanie
ms.openlocfilehash: 59d46990e02c0719d2b4df01e216a97fd649c509
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos de cache, incluindo recursos do nível Premium, como clustering, persistência e suporte à rede virtual. Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache Redis do Azure é configurada com uma Vnet, ela não é publicamente endereçável e pode ser acessada apenas de máquinas virtuais e aplicativos na VNet. Este artigo descreve como configurar o suporte de rede virtual para uma instância Premium do Cache Redis do Azure.

> [!NOTE]
> O Cache Redis do Azure dá suporte a VNets clássicas e do Resource Manager.
> 
> 

Para obter informações sobre outros recursos de cache premium, veja [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que a VNet?
A implantação da [VNet (Rede Virtual do Azure)](https://azure.microsoft.com/services/virtual-network/) fornece segurança e isolamento aprimorados para seu Cache Redis do Azure, além de todas as sub-redes, políticas de controle de acesso e outros recursos para obter acesso restrito adicional.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O suporte da VNet (Rede Virtual) é configurado na folha **Novo Cache Redis** durante a criação do cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de selecionar um tipo de preço Premium, você pode configurar a integração VNet do Redis selecionando uma VNet que está na mesma assinatura e localização que seu cache. Para usar uma nova VNet, crie-a primeiro seguindo as etapas em [Criar uma Rede Virtual usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [Criar uma rede virtual (clássica) usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e volte para a folha **Novo Cache Redis** para criar e configurar seu cache Premium.

Para configurar a VNet para seu novo cache, clique em **Rede Virtual** na folha **Novo Cache Redis** e selecione a VNet desejada na lista suspensa.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada da lista suspensa **Sub-rede** e especifique o **Endereço IP estático** desejado. Se você estiver usando uma VNet clássica, o campo **Endereço IP estático** será opcional e, se não for especificado nenhum, será escolhido um da sub-rede selecionada.

> [!IMPORTANT]
> Ao implantar um Cache Redis do Azure em uma VNet do Resource Manager, o cache deve estar em uma sub-rede dedicada que não contém nenhum outro recurso, exceto o Cache Redis do Azure. Se for feita uma tentativa de implantar um Cache Redis do Azure em uma VNet do Resource Manager para uma sub-rede contendo outros recursos, a implantação falhará.
> 
> 

![Rede virtual][redis-cache-vnet-ip]

> [!IMPORTANT]
> O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Além dos endereços IP usados pela infraestrutura do Azure VNET, cada instância de Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Considera-se que um cache não clusterizado tem um fragmento.
> 
> 

Após o cache ser criado, você pode exibir a configuração da VNet clicando em **Rede Virtual** no menu **Recursos**.

![Rede virtual][redis-cache-vnet-info]

Para se conectar à sua instância de Cache Redis do Azure usando uma VNet, especifique o nome do host do cache na cadeia de conexão, conforme mostrado no exemplo a seguir:

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-redis-cache-vnet-faq"></a>Perguntas frequentes sobre a Vnet do Cache Redis do Azure
A lista a seguir contém respostas para perguntas frequentes sobre a escala do Cache Redis do Azure.

* [Quais são alguns problemas comuns de configuração incorreta no Cache Redis do Azure e nas VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Como verificar se o cache está funcionando em uma VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [Posso usar VNets com um cache básico ou Standard?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Por que a criação de um cache Redis falha em algumas sub-redes, mas não em outras?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quais são alguns problemas comuns de configuração incorreta no Cache Redis do Azure e nas VNets?
Quando o Cache Redis do Azure estiver hospedado em uma VNet, as portas nas tabelas a seguir serão usadas. 

>[!IMPORTANT]
>Se as portas nas tabelas a seguir estiverem bloqueadas, é possível que o cache não funcione corretamente. A existência de uma ou mais dessas portas bloqueadas é o problema mais comum de configuração incorreta no uso do Cache Redis do Azure em uma VNet.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há sete requisitos de porta de saída.

- Se desejado, todas as conexões de saída à Internet podem ser feitas por meio de um dispositivo de auditoria local do cliente.
- Três das portas roteiam o tráfego para Pontos de Extremidade do Azure atendendo o Armazenamento do Azure e o DNS do Azure.
- Os intervalos de portas restantes e para comunicações internas de sub-rede Redis. Não é necessária nenhuma regra do NSG da sub-rede para as comunicações internas de sub-rede Redis.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | IP local | IP Remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências de Redis no Armazenamento do Azure/PKI (Internet) | (Sub-rede Redis) |* |
| 53 |Saída |TCP/UDP |Dependências Redis no DNS (Internet/Rede Virtual) | (Sub-rede Redis) |* |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |


### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Há oito requisitos de intervalo de portas de entrada. As solicitações de entrada nesses intervalos são recebidas de outros serviços hospedados na mesma VNET ou internas para as comunicações de sub-rede Redis.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | IP local | IP Remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com o Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) |Rede virtual, Azure Load Balancer |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redus), Azure Load Balancer |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) |Rede virtual, Azure Load Balancer |
| 15000-15999 |Entrada |TCP |Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) |Rede virtual, Azure Load Balancer |
| 16001 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos de conectividade de rede VNET adicionais

Há requisitos de conectividade de rede para o Cache Redis do Azure que podem não ser atendidos inicialmente em uma rede virtual. O Cache Redis do Azure requer todos os itens a seguir para funcionar corretamente quando usado em uma rede virtual.

* Conectividade de rede de saída para pontos de extremidade do Armazenamento do Azure em todo o mundo. Isso inclui os pontos de extremidade localizados na mesma região que a instância do Cache Redis do Azure, bem como os pontos de extremidade de armazenamento localizados em **outras** regiões do Azure. Os pontos de extremidade do Armazenamento do Azure são resolvidos nos seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*. 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Essa conectividade é necessária para dar suporte à funcionalidade SSL.
* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores. Esses requisitos de DNS podem ser atendidos, garantindo que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual.
* Conectividade de rede de saída para os seguintes pontos de extremidade do Azure Monitoring resolvidos nos domínios DNS a seguir: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como verificar se o cache está funcionando em uma VNET?

>[!IMPORTANT]
>Ao se conectar a uma instância de Cache Redis do Azure que é hospedada em uma VNET, os clientes de cache devem estar na mesma VNET, incluindo quaisquer aplicativos de teste ou ferramentas de diagnóstico do ping.
>
>

Uma vez configurados os requisitos de porta conforme descrito na seção anterior, você pode verificar se o cache está funcionando executando as etapas a seguir.

- [Reinicialize](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não podem ser acessadas (conforme documentado em [Requisitos de porta de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [Requisitos de porta de saída](cache-how-to-premium-vnet.md#outbound-port-requirements)), não será possível reiniciar o cache com êxito.
- Uma vez que os nós de cache tenham reinicializado (conforme relatado pelo status de cache no portal do Azure), você poderá executar os testes a seguir:
  - Execute o ping no ponto de extremidade do cache (usando a porta 6380) de um computador que esteja na mesma VNET que o cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a ferramenta `tcping` relata que a porta está aberta, o cache está disponível para a conexão de clientes na VNET.

  - Outra maneira de testar é criar um cliente de cache de teste (que pode ser um simples aplicativo de console usando StackExchange.Redis) que se conecta ao cache e adiciona e recupera alguns itens do cache. Instale o aplicativo de cliente de exemplo em uma VM que está na mesma VNET que o cache e execute-o para verificar a conectividade com o cache.


### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Posso usar VNets com um cache básico ou Standard?
As VNets podem ser usadas apenas com os caches Premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Por que a criação de um cache Redis falha em algumas sub-redes, mas não em outras?
Se você estiver implantando um Cache Redis do Azure em uma VNet do Resource Manager, o cache deverá estar em uma sub-rede dedicada que não contém nenhum outro tipo de recurso. Se for feita uma tentativa de implantar um Cache Redis do Azure em uma VNet do Resource Manager em uma sub-rede que contém outros recursos, a implantação falhará. É necessário excluir os recursos existentes na sub-rede antes de ser possível criar um novo Cache Redis.

Você pode implantar vários tipos de recursos para uma VNet clássica, contanto que tenha endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura do Azure VNET, cada instância de Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Considera-se que um cache não clusterizado tem um fragmento.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?
Quando o seu cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como resultado, os recursos de gerenciamento de cache a seguir não funcionarão neste momento.

* Console do Redis – Como o Console do Redis é executado em seu navegador local, que está fora da VNET, ele não poderá se conectar ao seu cache.

## <a name="use-expressroute-with-azure-redis-cache"></a>Usar o ExpressRoute com Cache Redis do Azure
Os clientes podem conectar um circuito do [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à infraestrutura de rede virtual, estendendo, assim, a rede local até o Azure. 

Por padrão, um circuito ExpressRoute recém-criado não executa túnel forçado (anúncio de uma rota padrão, 0.0.0.0/0) em uma VNET. Como resultado, a conectividade de Internet de saída é permitida diretamente da VNET e aplicativos cliente são capazes de se conectar a outros pontos de extremidade do Azure, incluindo o Cache Redis do Azure.

No entanto, uma configuração de cliente comum é usar túnel forçado (anunciar uma rota padrão), o que força o tráfego de saída da Internet a, em vez disso, ter fluxo local. Esse fluxo de tráfego interromperá a conectividade com o Cache Redis do Azure se o tráfego de saída for bloqueado localmente, de modo que a instância do Cache Redis do Azure não será capaz de se comunicar com suas dependências.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contenham o Cache Redis do Azure. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

* A configuração de ExpressRoute anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
* O UDR aplicado à sub-rede que contém o Cache Redis do Azure define 0.0.0.0/0 com uma rota de trabalho para o tráfego de TCP/IP à Internet pública. Por exemplo, definindo o tipo do próximo salto como “Internet”.

O efeito combinado dessas etapas é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute, garantindo acesso de Internet de saída do Cache Redis do Azure.

Conectar-se a uma instância de Cache Redis do Azure de um aplicativo local usando o ExpressRoute não é um cenário de uso típico por motivos de desempenho (para o melhor desempenho, os clientes do Cache Redis do Azure devem estar na mesma região que o Cache Redis do Azure).

>[!IMPORTANT] 
>As rotas definidas em uma UDR **devem** ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo a seguir usa o intervalo de endereços 0.0.0.0/0 amplo, e potencialmente pode ser substituído por acidente pelos anúncios de rota usando intervalos de endereços mais específicos.

>[!WARNING]  
>O Cache Redis do Azure não tem suporte com configurações do ExpressRoute que **incorretamente cruzam anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**. As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereços forem incorretamente anunciados de modo cruzado no caminho de emparelhamento privado, o resultado final será que todos os pacotes de saída de rede da sub-rede da instância do Cache Redis do Azure serão incorretamente encapsulados à força em uma infraestrutura de rede local do cliente. Esse fluxo de rede interrompe o Cache Redis do Azure. A solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.


Informações básicas sobre as rotas definidas pelo usuário estão disponíveis nesta [visão geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, consulte [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Próximas etapas
Aprenda a usar mais recursos de cache premium.

* [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

