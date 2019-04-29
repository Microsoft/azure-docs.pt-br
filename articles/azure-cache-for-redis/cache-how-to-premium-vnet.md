---
title: Configurar uma Rede Virtual para um Cache do Azure Premium para Redis | Microsoft Docs
description: Saiba como criar e gerenciar suporte de Rede Virtual para o Cache do Azure na camada Premium para instâncias do Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: d4b8fd6ccb3fc7cb2627d4bd3e103239181e4d9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831023"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Como configurar suporte de Rede Virtual para um Cache do Azure Premium para Redis
O Cache do Azure para Redis apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos de cache, incluindo recursos da camada Premium como clustering, persistência e suporte de rede virtual. Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache do Azure para Redis é configurada com uma rede virtual, ela não é endereçável publicamente e somente pode ser acessada de máquinas virtuais e aplicativos dentro da rede virtual. Este artigo descreve como configurar suporte de rede virtual para uma instância do Cache do Azure Premium para Redis.

> [!NOTE]
> O Cache do Azure para Redis dá suporte tanto para VNets do Resource Manager como Clássicas.
> 
> 

Para obter informações sobre outros recursos de Cache Premium, consulte [Introdução à camada Premium do Cache do Azure para Redis](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Por que a VNet?
A implantação de[ VNet (Rede Virtual do Microsoft Azure)](https://azure.microsoft.com/services/virtual-network/) fornece segurança e isolamento aprimorados para o Cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O suporte da VNet (Rede Virtual) é configurado na folha **Novo Cache do Azure para Redis** durante a criação do cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de selecionar um tipo de preço Premium, você pode configurar a integração VNet do Redis selecionando uma VNet que está na mesma assinatura e localização que seu cache. Para usar uma nova VNet, crie-a primeiro seguindo as etapas em [Criar uma Rede Virtual usando o portal do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [Criar uma rede virtual (clássica) usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e, em seguida, retorne para a folha **Novo Cache do Azure para redis** para criar e configurar o cache Premium.

Para configurar a VNet para o novo cache, clique em **Rede Virtual** na folha **Novo Cache do Azure para Redis** e selecione a VNet desejada na lista suspensa.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada da lista suspensa **Sub-rede** e especifique o **Endereço IP estático** desejado. Se você estiver usando uma VNet clássica, o campo **Endereço IP estático** será opcional e, se não for especificado nenhum, será escolhido um da sub-rede selecionada.

> [!IMPORTANT]
> Ao implantar um Cache do Azure para Redis em uma VNet do Resource Manager, o cache deve estar em uma sub-rede dedicada que não contém nenhum outro recurso, exceto o Cache do Azure para Redis. Se uma tentativa de implantar um Cache do Azure para Redis for realizada em uma VNet do Resource Manager para uma sub-rede contendo outros recursos, a implantação falhará.
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

Para conectar a instância de Cache do Azure para Redis usando uma VNet, especifique o nome do host do cache na cadeia de conexão, conforme mostrado no exemplo a seguir:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Perguntas frequentes sobre o Cache do Azure para Redis
A lista a seguir contém respostas para perguntas frequentes sobre o dimensionamento do Cache do Azure para Redis.

* Quais são alguns problemas comuns de configuração incorreta com o Cache do Azure para Redis e VNets?
* [Como verificar se o cache está funcionando em uma VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Ao tentar conectar o Cache do Azure para Redis em uma VNET, por que estou recebendo um erro informando que o certificado remoto é inválido?
* [Posso usar VNets com um cache básico ou Standard?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Por que a criação de um Cache do Azure para Redis falha em algumas sub-redes, mas não em outras?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quais são alguns problemas comuns de configuração incorreta com o Cache do Azure para Redis e VNets?
Quando o Cache do Azure para Redis for hospedado em uma VNet, serão usadas as portas nas tabelas a seguir. 

>[!IMPORTANT]
>Se as portas nas tabelas a seguir estiverem bloqueadas, é possível que o cache não funcione corretamente. Ter uma ou mais dessas portas bloqueadas é o problema mais comum de configuração incorreta ao usar o Cache do Azure para Redis em uma rede virtual.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há sete requisitos de porta de saída.

- Todas as conexões de saída à internet podem ser feitas por meio de um cliente do dispositivo de auditoria local.
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
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |


#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Há oito requisitos de intervalo de portas de entrada. As solicitações de entrada nesses intervalos são recebidas de outros serviços hospedados na mesma VNET ou internas para as comunicações de sub-rede Redis.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | IP local | IP Remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com o Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) | (sub-rede Redis) Rede virtual, Azure Load Balancer |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 10221-10231 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redus), Azure Load Balancer |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) |Rede virtual, Azure Load Balancer |
| 15000-15999 |Entrada |TCP |Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) |Rede virtual, Azure Load Balancer |
| 16001 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) |Azure Load Balancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

#### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos de conectividade de rede VNET adicionais

Há requisitos de conectividade de rede para o Cache do Azure para Redis que podem não ser atendidos inicialmente em uma rede virtual. O Cache do Azure para Redis requer que todos os itens a seguir funcionem corretamente quando usados em uma rede virtual.

* Conectividade de rede de saída para pontos de extremidade do Armazenamento do Azure em todo o mundo. Isso inclui pontos de extremidade localizados na mesma região que a instância do Cache do Azure para Redis, bem como pontos de extremidade de armazenamento localizados em **outras** regiões do Azure. Os pontos de extremidade do Armazenamento do Azure são resolvidos nos seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*. 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Essa conectividade é necessária para dar suporte à funcionalidade SSL.
* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores. Esses requisitos de DNS podem ser atendidos, garantindo que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual.
* Conectividade de rede de saída para os seguintes pontos de extremidade do Azure Monitoring resolvidos nos domínios DNS a seguir: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como verificar se o cache está funcionando em uma VNET?

>[!IMPORTANT]
>Ao conectar uma instância do Cache do Azure para Redis que está hospedada em uma VNET, seus clientes de cache deverão estar na mesma VNET ou em uma VNET com emparelhamento VNET habilitado. Isso inclui todos os aplicativos de teste ou ferramentas de diagnóstico do ping. Seja qual for o local em que o aplicativo cliente está hospedado, os Grupos de segurança de rede devem ser configurados de modo que o tráfego de rede do cliente tenha permissão para acessar a instância do Redis.
>
>

Uma vez configurados os requisitos de porta conforme descrito na seção anterior, você pode verificar se o cache está funcionando executando as etapas a seguir.

- [Reinicialize](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não podem ser acessadas (conforme documentado em [Requisitos de porta de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [Requisitos de porta de saída](cache-how-to-premium-vnet.md#outbound-port-requirements)), não será possível reiniciar o cache com êxito.
- Uma vez que os nós de cache tenham reinicializado (conforme relatado pelo status de cache no portal do Azure), você poderá executar os testes a seguir:
  - Execute o ping no ponto de extremidade do cache (usando a porta 6380) de um computador que esteja na mesma VNET que o cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo: 
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a ferramenta `tcping` relata que a porta está aberta, o cache está disponível para a conexão de clientes na VNET.

  - Outra maneira de testar é criar um cliente de cache de teste (que pode ser um simples aplicativo de console usando StackExchange.Redis) que se conecta ao cache e adiciona e recupera alguns itens do cache. Instale o aplicativo de cliente de exemplo em uma VM que está na mesma VNET que o cache e execute-o para verificar a conectividade com o cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Ao tentar conectar o Cache do Azure para Redis em uma VNET, por que estou recebendo um erro informando que o certificado remoto é inválido?

Ao tentar conectar um Cache do Azure para Redis em uma VNET, você verá um erro de validação de certificado como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que você está se conectando ao host pelo endereço IP. É recomendável usar o nome do host. Em outras palavras, use o seguinte:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite usar o endereço IP semelhante à seguinte cadeia de caracteres de conexão:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não for possível resolver o nome DNS, algumas bibliotecas de cliente incluem opções como `sslHost` que é fornecido pelo cliente StackExchange.Redis. Isso permite que você substitua o nome do host usado para validação de certificado. Por exemplo: 

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Posso usar VNets com um cache básico ou Standard?
As VNets podem ser usadas apenas com os caches Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Por que a criação de um Cache do Azure para Redis falha em algumas sub-redes, mas não em outras?
Se você estiver implantando um Cache do Azure para Redis em uma VNet do Resource Manager, o cache deverá estar em uma sub-rede dedicada que não contenha nenhum outro tipo de recurso. Se uma tentativa de implantar um Cache do Azure para Redis for realizada em uma sub-rede de VNet do Resource Manager que contenha outros recursos, a implantação falhará. Será necessário excluir os recursos existentes na sub-rede antes de criar um novo Cache do Azure para Redis.

Você pode implantar vários tipos de recursos para uma VNet clássica, contanto que tenha endereços IP suficientes disponíveis.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura do Azure VNET, cada instância de Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Considera-se que um cache não clusterizado tem um fragmento.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?
Quando o seu cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como resultado, os recursos de gerenciamento de cache a seguir não funcionarão neste momento.

* Console do Redis – Como o Console do Redis é executado em seu navegador local, que está fora da VNET, ele não poderá se conectar ao seu cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usar ExpressRoute com Cache do Azure para Redis

Os clientes podem conectar um circuito da [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à infraestrutura de rede virtual, estendendo, assim, a rede local até o Azure. 

Por padrão, um circuito ExpressRoute recém-criado não executa túnel forçado (anúncio de uma rota padrão, 0.0.0.0/0) em uma VNET. Como resultado, a conectividade da Internet de saída é permitida diretamente da VNET e os aplicativos clientes podem conectar outros pontos de extremidade do Azure, incluindo o Cache do Azure para Redis.

No entanto, uma configuração de cliente comum é usar túnel forçado (anunciar uma rota padrão), o que força o tráfego de saída da Internet a, em vez disso, ter fluxo local. Esse fluxo de tráfego interromperá a conectividade com o Cache do Azure para Redis se o tráfego de saída for bloqueado no local, de modo que a instância do Cache do Azure para Redis não consiga comunicar-se com suas dependências.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede contendo o Cache do Azure para Redis. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

* A configuração de ExpressRoute anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
* A UDR aplicada à sub-rede contendo o Cache do Azure para Redis define 0.0.0.0/0 com uma rota de trabalho para tráfego TCP/IP para a Internet pública, por exemplo, configurando o próximo tipo salto para 'Internet'.

O efeito combinado dessas etapas é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute, garantindo o acesso à Internet de saída do Cache do Azure para Redis.

Conectar uma instância do Cache do Azure para Redis de um aplicativo local usando o ExpressRoute não é um cenário de uso típico devido a motivos de desempenho (para melhor desempenho, os clientes do Cache do Azure para Redis devem estar na mesma região do Cache do Azure para Redis).

>[!IMPORTANT] 
>As rotas definidas em uma UDR **devem** ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo a seguir usa o intervalo de endereços 0.0.0.0/0 amplo, e potencialmente pode ser substituído por acidente pelos anúncios de rota usando intervalos de endereços mais específicos.

>[!WARNING]  
>O Cache do Azure para redis não tem suporte com configurações do ExpressRoute que **incorretamente cruzam anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**. As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereço forem anunciados incorretamente de modo cruzado no caminho de emparelhamento privado, o resultado será que todos os pacotes de rede de saída da sub-rede da instância do Cache do Azure para Redis serão incorretamente encapsulados para a infraestrutura de rede local do cliente. Esse fluxo de rede interrompe o Cache do Azure para Redis. A solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.


Informações básicas sobre as rotas definidas pelo usuário estão disponíveis nesta [visão geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, consulte [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Próximas etapas
Aprenda a usar mais recursos de cache premium.

* [Introdução à camada Premium do Cache Redis do Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

