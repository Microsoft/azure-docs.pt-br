---
title: Configurar uma rede virtual-camada Premium cache do Azure para instância Redis
description: Saiba como criar e gerenciar o suporte de rede virtual para o cache do Azure de camada Premium para a instância Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375265"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Configurar o suporte de rede virtual para um cache do Azure Premium para a instância Redis

A implantação de [rede virtual do Azure](https://azure.microsoft.com/services/virtual-network/) fornece segurança e isolamento aprimorados juntamente com sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Quando um cache do Azure para instância Redis é configurado com uma rede virtual, ele não é publicamente endereçável e só pode ser acessado de máquinas virtuais e aplicativos na rede virtual. Este artigo descreve como configurar o suporte de rede virtual para um cache do Azure de camada Premium para instância Redis.

> [!NOTE]
> O cache do Azure para Redis dá suporte ao modelo de implantação clássico e Azure Resource Manager redes virtuais.
> 

## <a name="set-up-virtual-network-support"></a>Configurar o suporte de rede virtual

O suporte à rede virtual é configurado no painel **novo cache do Azure para Redis** durante a criação do cache.

1. Para criar um cache de camada Premium, entre no [portal do Azure](https://portal.azure.com) e selecione **criar um recurso**. Além de criar caches no portal do Azure, você também pode criá-los usando modelos do Resource Manager, o PowerShell ou o CLI do Azure. Para obter mais informações sobre como criar um cache do Azure para a instância do Redis, consulte [criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Captura de tela que mostra criar um recurso.":::
   
1. Na página **novo** , selecione **bancos de dados**. Em seguida, selecione **cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Captura de tela que mostra a seleção do cache do Azure para Redis.":::

1. Na página **novo cache Redis** , defina as configurações para seu novo cache de camada Premium.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome do cache deve ser uma cadeia entre 1 e 63 caracteres que contenham apenas números, letras ou hifens. O nome deve começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. |
   | **Assinatura** | Selecione sua assinatura na lista suspensa. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. |
   | **Grupo de recursos** | Selecione um grupo de recursos na lista suspensa ou selecione **criar novo** e insira um novo nome de grupo de recursos. | O nome do grupo de recursos no qual criar o cache e outros recursos. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. |
   | **Localidade** | Selecione uma localização na lista suspensa. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de cache** |Selecione um cache de camada Premium na lista suspensa para configurar os recursos da camada Premium. Para obter mais informações, confira [Preços do Cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, consulte [visão geral do cache do Azure para Redis](cache-overview.md). |

1. Selecione a guia **rede** ou selecione o botão **rede** na parte inferior da página.

1. Na guia **rede** , selecione **redes virtuais** como seu método de conectividade. Para usar uma nova rede virtual, crie-a primeiro seguindo as etapas em [criar uma rede virtual usando o portal do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [criar uma rede virtual (clássica) usando o portal do Azure](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Em seguida, retorne ao painel **novo cache do Azure para Redis** para criar e configurar o cache de camada Premium.

   > [!IMPORTANT]
   > Quando você implanta o cache do Azure para Redis em uma rede virtual do Resource Manager, o cache deve estar em uma sub-rede dedicada que não contém outros recursos, exceto para o cache do Azure para instâncias Redis. Se você tentar implantar um cache do Azure para instância Redis em uma sub-rede de rede virtual do Resource Manager que contenha outros recursos, a implantação falhará.
   > 
   > 

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Rede virtual** | Selecione sua rede virtual na lista suspensa. | Selecione uma rede virtual que esteja na mesma assinatura e local que o cache. |
   | **Sub-rede** | Selecione sua sub-rede na lista suspensa. | O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Ele deve estar contido no espaço de endereço da rede virtual. |
   | **Endereço IP estático** | Adicional Insira um endereço IP estático. | Se você não especificar um endereço IP estático, um endereço IP será escolhido automaticamente. |

   > [!IMPORTANT]
   > O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Além dos endereços IP usados pela infraestrutura de rede virtual do Azure, cada cache do Azure para instância Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Um cache não clusterizado é considerado como tendo um fragmento.
   > 

1. Selecione a **próxima guia Avançado:** ou selecione o botão **Avançar: avançado** na parte inferior da página.

1. Na guia **avançado** de uma instância de cache de camada Premium, defina as configurações para porta não TLS, clustering e persistência de dados.

1. Selecione a **próxima: guia marcas** ou selecione o botão **próximo: marcas** na parte inferior da página.

1. Opcionalmente, na guia **marcas** , digite o nome e o valor se desejar categorizar o recurso.

1. Selecione **Examinar + criar**. Você é levado para a guia **revisar + criar** , na qual o Azure valida sua configuração.

1. Depois que a mensagem de **validação verde aprovada** for exibida, selecione **criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso. Depois que o cache é criado, você pode exibir a configuração da rede virtual selecionando **rede virtual** no menu de **recursos** .

![Rede virtual][redis-cache-vnet-info]

Para se conectar ao cache do Azure para a instância do Redis ao usar uma rede virtual, especifique o nome do host do seu cache na cadeia de conexão, conforme mostrado no exemplo a seguir:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Perguntas frequentes sobre o cache do Azure para rede virtual Redis

A lista a seguir contém respostas para perguntas frequentes sobre o dimensionamento do Cache Redis do Azure.

* Quais são alguns problemas comuns de configuração incorreta com o cache do Azure para Redis e redes virtuais?
* [Como posso verificar se o cache está funcionando em uma rede virtual?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Quando tento me conectar ao meu cache do Azure para a instância Redis em uma rede virtual, por que obtenho um erro informando que o certificado remoto é inválido?
* [Posso usar redes virtuais com um cache Standard ou básico?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Por que a criação de um cache do Azure para a instância Redis falha em algumas sub-redes, mas não em outras?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todos os recursos de cache funcionam quando um cache é hospedado em uma rede virtual?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Quais são alguns problemas comuns de configuração incorreta com o cache do Azure para Redis e redes virtuais?

Quando o cache do Azure para Redis é hospedado em uma rede virtual, as portas nas tabelas a seguir são usadas.

>[!IMPORTANT]
>Se as portas nas tabelas a seguir estiverem bloqueadas, o cache poderá não funcionar corretamente. Ter uma ou mais dessas portas bloqueadas é o problema de configuração incorreta mais comum ao usar o cache do Azure para Redis em uma rede virtual.
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há nove requisitos de porta de saída. As solicitações de saída nesses intervalos são de saída para outros serviços necessários para que o cache funcione ou seja interno à sub-rede Redis para comunicação entre nós. Para a replicação geográfica, existem outros requisitos de saída para a comunicação entre as sub-redes do cache primário e de réplica.

| Portas | Direção | Protocolo de transporte | Finalidade | IP local | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências do Redis no armazenamento do Azure/PKI (Internet) | (Sub-rede Redis) |* <sup>quatro</sup> |
| 443 | Saída | TCP | Dependência Redis em Azure Key Vault e Azure Monitor | (Sub-rede Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Saída |TCP/UDP |Dependências do Redis no DNS (Internet/rede virtual) | (Sub-rede Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualquer servidor DNS personalizado para a sub-rede <sup>3</sup> |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis e replicação geográfica | (Sub-rede Redis) |(Sub-rede Redis) (Sub-rede par de réplica geográfica) |
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> você pode usar as marcas de serviço AzureKeyVault e AzureMonitor com grupos de segurança de rede (NSGs) do Resource Manager.

<sup>2</sup> esses endereços IP pertencentes à Microsoft são usados para tratar da VM do host que serve o DNS do Azure.

<sup>3</sup> essas informações não são necessárias para sub-redes sem servidor DNS personalizado ou caches Redis mais recentes que ignoram o DNS personalizado.

<sup>4</sup> para obter mais informações, consulte [requisitos adicionais de conectividade de rede virtual](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos de porta de pares de replicação geográfica

Se você estiver usando a replicação geográfica entre caches em redes virtuais do Azure, desbloqueie as portas 15000-15999 para toda a sub-rede nas direções de entrada *e* saída para ambos os caches. Com essa configuração, todos os componentes de réplica na sub-rede podem se comunicar diretamente entre si mesmo se houver um failover geográfico futuro.

#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Há oito requisitos de intervalo de portas de entrada. As solicitações de entrada nesses intervalos são de entrada de outros serviços hospedados na mesma rede virtual ou internas para as comunicações de sub-rede Redis.

| Portas | Direção | Protocolo de transporte | Finalidade | IP local | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com o Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede do cliente), AzureLoadBalancer <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) | AzureLoadBalancer |
| 10221-10231 |Entrada |TCP |Comunicação de cliente com clusters Redis, comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis), AzureLoadBalancer, (sub-rede do cliente) |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede do cliente), AzureLoadBalancer |
| 15000-15999 |Entrada |TCP |Comunicação de cliente com clusters Redis, balanceamento de carga do Azure e replicação geográfica | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede do cliente), AzureLoadBalancer, (sub-rede par de réplica geográfica) |
| 16001 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) | AzureLoadBalancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> você pode usar a marca de serviço AzureLoadBalancer para o Resource Manager ou AZURE_LOADBALANCER para o modelo de implantação clássico para criar as regras de NSG.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Requisitos adicionais de conectividade de rede virtual

Há requisitos de conectividade de rede para o cache do Azure para Redis que podem não ser atendidos inicialmente em uma rede virtual. O cache do Azure para Redis exige que todos os seguintes itens funcionem corretamente quando usados em uma rede virtual:

* Conectividade de rede de saída para pontos de extremidade do Armazenamento do Azure em todo o mundo. Os pontos de extremidade localizados na mesma região que o cache do Azure para a instância Redis e os pontos de extremidade de armazenamento localizados em *outras* regiões do Azure são incluídos. Os pontos de extremidade do armazenamento do Azure são resolvidos nos seguintes domínios DNS: *Table.Core.Windows.net*, *BLOB.Core.Windows.net*, *Queue.Core.Windows.net* e *File.Core.Windows.net*.
* Conectividade de rede de saída para *OCSP.DigiCert.com*, *crl4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* e *CRL.Microsoft.com*. Essa conectividade é necessária para dar suporte à funcionalidade TLS/SSL.
* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores. Esses requisitos de DNS podem ser atendidos, garantindo que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual.
* Conectividade de rede de saída para os seguintes Azure Monitor pontos de extremidade, que são resolvidos nos seguintes domínios DNS: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net*, *azglobal-Red.azglobal.Metrics.nsatc.net*, *shoebox3.Prod.microsoftmetrics.com*, *shoebox3-Red.Prod.microsoftmetrics.com* e *shoebox3-Black.Prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Como posso verificar se o cache está funcionando em uma rede virtual?

>[!IMPORTANT]
>Quando você se conecta a um cache do Azure para a instância Redis hospedada em uma rede virtual, os clientes de cache devem estar na mesma rede virtual ou em uma rede virtual com emparelhamento de rede virtual habilitado na mesma região do Azure. O emparelhamento de rede virtual global não tem suporte no momento. Esse requisito se aplica a quaisquer aplicativos de teste ou ferramentas de ping de diagnóstico. Independentemente de onde o aplicativo cliente está hospedado, NSGs ou outras camadas de rede devem ser configuradas de modo que o tráfego de rede do cliente tenha permissão para acessar o cache do Azure para a instância Redis.
>

Depois que os requisitos de porta são configurados conforme descrito na seção anterior, você pode verificar se o cache está funcionando seguindo estas etapas:

- [Reinicialize](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não puderem ser atingidas, conforme documentado em [requisitos de porta de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e requisitos de porta de [saída](cache-how-to-premium-vnet.md#outbound-port-requirements), o cache não poderá ser reiniciado com êxito.
- Depois que os nós de cache forem reiniciados, conforme relatado pelo status do cache no portal do Azure, você poderá fazer os seguintes testes:
  - Execute ping no ponto de extremidade do cache usando a porta 6380 de um computador que está dentro da mesma rede virtual que o cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a `tcping` ferramenta relatar que a porta está aberta, o cache estará disponível para conexão de clientes na rede virtual.

  - Outra maneira de testar é criar um cliente de cache de teste (que pode ser um simples aplicativo de console usando StackExchange.Redis) que se conecta ao cache e adiciona e recupera alguns itens do cache. Instale o aplicativo cliente de exemplo em uma VM que está na mesma rede virtual que o cache. Em seguida, execute-o para verificar a conectividade com o cache.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Quando tento me conectar ao meu cache do Azure para a instância Redis em uma rede virtual, por que obtenho um erro informando que o certificado remoto é inválido?

Ao tentar se conectar a um cache do Azure para a instância Redis em uma rede virtual, você verá um erro de validação de certificado, como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que você está se conectando ao host pelo endereço IP. Recomendamos que você use o nome do host. Em outras palavras, use a seguinte cadeia de caracteres:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite usar o endereço IP semelhante à seguinte cadeia de caracteres de conexão:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não for possível resolver o nome DNS, algumas bibliotecas de cliente incluem opções de configuração como `sslHost` , que são fornecidas pelo cliente stackexchange. Redis. Essa opção permite que você substitua o nome do host usado para validação de certificado. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Posso usar redes virtuais com um cache Standard ou básico?

As redes virtuais só podem ser usadas com caches de camada Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Por que a criação de um cache do Azure para a instância Redis falha em algumas sub-redes, mas não em outras?

Se você estiver implantando um cache do Azure para instância Redis em uma rede virtual, o cache deverá estar em uma sub-rede dedicada que não contém nenhum outro tipo de recurso. Se for feita uma tentativa de implantar um cache do Azure para instância Redis em uma sub-rede de rede virtual do Resource Manager que contenha outros recursos, como instâncias de gateway Aplicativo Azure e NAT de saída, a implantação normalmente falhará. Você deve excluir os recursos existentes de outros tipos antes de poder criar um novo cache do Azure para a instância do Redis.

Você também deve ter endereços IP suficientes disponíveis na sub-rede.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?

O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de rede virtual do Azure, cada cache do Azure para instância Redis na sub-rede usa dois endereços IP por fragmento de cluster, além de endereços IP adicionais para réplicas adicionais, se houver. Um endereço IP adicional é usado para o balanceador de carga. Um cache não clusterizado é considerado como tendo um fragmento.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Todos os recursos de cache funcionam quando um cache é hospedado em uma rede virtual?

Quando o cache faz parte de uma rede virtual, somente os clientes na rede virtual podem acessar o cache. Como resultado, os seguintes recursos de gerenciamento de cache não funcionam no momento:

* **Console do Redis**: como o console do Redis é executado no navegador local, que geralmente está em um computador de desenvolvedor que não está conectado à rede virtual, ele não pode se conectar ao seu cache.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usar ExpressRoute com Cache do Azure para Redis

Os clientes podem conectar um circuito [do Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à sua infraestrutura de rede virtual. Dessa forma, eles estendem sua rede local para o Azure.

Por padrão, um circuito ExpressRoute recém-criado não executa túnel forçado (anúncio de uma rota padrão, 0.0.0.0/0) em uma rede virtual. Como resultado, a conectividade de Internet de saída é permitida diretamente da rede virtual. Os aplicativos cliente podem se conectar a outros pontos de extremidade do Azure, o que inclui um cache do Azure para a instância Redis.

Uma configuração de cliente comum é usar o túnel forçado (anunciar uma rota padrão), que força o tráfego de Internet de saída para o fluxo local. Esse fluxo de tráfego interrompe a conectividade com o cache do Azure para Redis se o tráfego de saída é bloqueado localmente, de modo que o cache do Azure para a instância Redis não possa se comunicar com suas dependências.

A solução é definir uma ou mais UDRs (rotas definidas pelo usuário) na sub-rede que contém o cache do Azure para a instância Redis. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Se for possível, use a seguinte configuração:

* A configuração do ExpressRoute anuncia 0.0.0.0/0 e, por padrão, força o túneI a todo o tráfego de saída local.
* O UDR aplicado à sub-rede que contém o cache do Azure para a instância Redis define 0.0.0.0/0 com uma rota de trabalho para o tráfego TCP/IP para a Internet pública. Por exemplo, ele define o tipo do próximo salto como *Internet*.

O efeito combinado dessas etapas é que a UDR no nível da sub-rede tem precedência sobre o túnel forçado do ExpressRoute, que garante o acesso à Internet de saída do cache do Azure para a instância do Redis.

Conectar-se a um cache do Azure para instância Redis de um aplicativo local usando o ExpressRoute não é um cenário de uso típico devido a motivos de desempenho. Para obter um melhor desempenho, o cache do Azure para clientes Redis deve estar na mesma região que o cache do Azure para a instância Redis.

>[!IMPORTANT]
>As rotas definidas em um UDR *devem* ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo a seguir usa o intervalo de endereços 0.0.0.0/0 amplo e, como tal, pode potencialmente ser substituído acidentalmente por anúncios de rota que usam intervalos de endereços mais específicos.

>[!WARNING]
>O cache do Azure para Redis não tem suporte com configurações do ExpressRoute que *incorretamente anunciam rotas do caminho de emparelhamento público para o caminho de emparelhamento privado*. As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereço forem anunciados incorretamente de modo cruzado no caminho de emparelhamento privado, o resultado será que todos os pacotes de rede de saída da sub-rede da instância do Cache do Azure para Redis serão incorretamente encapsulados para a infraestrutura de rede local do cliente. Esse fluxo de rede interrompe o Cache do Azure para Redis. A solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.

As informações básicas sobre o UDRs estão disponíveis no [Roteamento de tráfego de rede virtual](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, consulte [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
