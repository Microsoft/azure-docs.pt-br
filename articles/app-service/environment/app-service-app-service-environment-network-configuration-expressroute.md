---
title: "Detalhes da configuração de rede para trabalhar com a Rota Expressa"
description: "Detalhes da configuração de rede para executar Ambientes do Serviço de Aplicativo em uma rede virtual conectada a um circuito do ExpressRoute."
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: bb3e283e8a9327a9c66c8d8ded037cee5195ffc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Detalhes da configuração de rede para Ambientes de Serviço de Aplicativo com o ExpressRoute
## <a name="overview"></a>Visão geral
Os clientes podem conectar um circuito da [Azure ExpressRoute][ExpressRoute] à infraestrutura de rede virtual, estendendo, assim, a rede local até o Azure.  Um Ambiente de Serviço de Aplicativo pode ser criado em uma sub-rede dessa infraestrutura de [rede virtual][virtualnetwork].  Os aplicativos em execução no Ambiente de Serviço de Aplicativo podem então estabelecer conexões seguras com recursos de back-end acessíveis apenas pela conexão de ExpressRoute.  

Um Ambiente de Serviço de Aplicativo pode ser criado **tanto** em uma rede virtual do Azure Resource Manager **quanto** em uma rede virtual de modelo de implantação clássico.  Com uma alteração recente feita em junho de 2016, os ASEs agora também podem ser implantados nas redes virtuais que usam os intervalos de endereço público ou espaços de endereço RFC1918 (ou seja, endereços privados). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária
Há requisitos de conectividade de rede para Ambientes do Serviço de Aplicativo que não podem ser inicialmente atendidos em uma rede virtual conectada a um ExpressRoute.  Os Ambientes do Serviço de Aplicativo exigem tudo o que se segue para que funcionem corretamente:

* Conectividade de rede de saída para pontos de extremidade do Armazenamento do Azure em todo o mundo nas portas 80 e 443.  Isso inclui os pontos de extremidade localizados na mesma região que o Ambiente do Serviço de Aplicativo, bem como pontos de extremidade de armazenamento localizados em **outras** regiões do Azure.  Os pontos de extremidade do Armazenamento do Azure são resolvidos nos seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.  
* Conectividade de rede de saída para o serviço de arquivos do Azure na porta 445.
* Conectividade de rede de saída para pontos de extremidade de Banco de Dados SQL localizados na mesma região que o Ambiente de Serviço de Aplicativo.  Os pontos de extremidade do banco de dados SQL são resolvidos neste domínio: *database.windows.net*.  Isso exige a abertura de acesso às portas 1433, 11000 a 11999 e 14000 a 14999.  Para obter mais detalhes, consulte [este artigo sobre o uso de portas do banco de dados SQL V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
* Conectividade de rede de saída para os pontos de extremidade do plano de gerenciamento do Azure (pontos de extremidade ASM e ARM).  Inclui conectividade de saída para *management.core.windows.net* e *management.azure.com*. 
* Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*.  Necessária para dar suporte à funcionalidade SSL.
* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores.  Se esses pontos de extremidade não puderem ser resolvidos, tentativas de criação do Ambiente de Serviço de Aplicativo irão falhar, e Ambientes de Serviços de Aplicativo existentes serão marcados como não íntegros.
* O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS.
* Se houver um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível a partir da sub-rede contendo o Ambiente de Serviço de Aplicativo. 
* O caminho da rede de saída não pode passar por proxies corporativos internos, nem pode ser encapsulado à força em locais.  Isso altera o endereço NAT eficiente de tráfego de rede de saída do Ambiente de Serviço de Aplicativo.  Alterar o endereço NAT do tráfego de rede de saída de um Ambiente do Serviço de Aplicativo causará falhas de conectividade em muitos dos pontos de extremidade listados acima.  Isso resulta em tentativas de criação de Ambiente de Serviço de Aplicativo, e faz com que Ambientes de Serviços de Aplicativo anteriormente íntegros sejam marcados como não íntegros.  
* O acesso de rede de entrada a portas obrigatórias para os Ambientes do Serviço de Aplicativo deve ser permitido, como descrito neste [artigo][requiredports].

Os requisitos de DNS podem ser atendidos, garantindo que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual.  Se por algum motivo, a configuração do DNS for alterada após ter sido criado um Ambiente do Serviço de Aplicativo, os desenvolvedores podem forçar um Ambiente do Serviço de Aplicativo para captar a nova configuração de DNS.  Acionar uma reinicialização do ambiente sem interrupção usando o ícone "Reiniciar" localizado na parte superior da folha de gerenciamento do Ambiente de Serviço de Aplicativo no [Portal do Azure][NewPortal] fará com que o ambiente capture a nova configuração de DNS.

Os requisitos de acesso de rede de entrada podem ser atendidos ao configurar um [grupo de segurança de rede][NetworkSecurityGroups] na sub-rede do Ambiente do Serviço de Aplicativo para permitir o acesso obrigatório, como descrito neste [artigo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Habilitando a conectividade de rede de saída para um Ambiente do Serviço de Aplicativo
Por padrão, um circuito do ExpressRoute recentemente criado anuncia uma rota padrão que permite conectividade de saída da Internet.  Com essa configuração, um Ambiente do Serviço de Aplicativo será capaz de se conectar a outros pontos de extremidade do Azure.

No entanto, uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0), que força o tráfego de saída da Internet para o fluxo local.  Esse fluxo de tráfego invariavelmente interrompe os Ambientes do Serviço de Aplicativo porque o tráfego de saída é bloqueado no local ou seu endereço de rede é convertido em um conjunto irreconhecível de endereços que não funcionam mais com vários pontos de extremidade do Azure.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contém o Ambiente do Serviço de Aplicativo.  Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

* A configuração de ExpressRoute anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
* O UDR aplicado à sub-rede que contém o Ambiente do Serviço de Aplicativo define 0.0.0.0/0 com um tipo do próximo salto da Internet (veja um exemplo disso no final deste artigo).

O efeito combinado dessas etapas é que o nível de sub-rede UDR terá precedência sobre o encapsulamento forçado do ExpressRoute, garantindo acesso de Internet de saída do Ambiente do Serviço de Aplicativo.

> [!IMPORTANT]
> As rotas definidas em uma UDR **devem** ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute.  O exemplo a seguir usa o intervalo de endereço 0.0.0.0/0 amplo, e potencialmente pode ser substituído por acidente pelos anúncios de rota usando intervalos de endereços mais específicos.
> 
> Não há suporte para Ambientes de Serviço de Aplicativo com configurações do ExpressRoute que **cruzem anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**.  As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure.  Se esses intervalos de endereços forem anunciados de modo cruzado no caminho de emparelhamento particular, o resultado final será que todos os pacotes de saída de rede da sub-rede do Ambiente do Serviço de Aplicativo serão encapsulados à força em uma infraestrutura de rede local do cliente.  Esse fluxo de rede não tem suporte no momento com os Ambientes de Serviço de Aplicativo.  Uma solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.
> 
> 

Informações básicas sobre as rotas definidas pelo usuário estão disponíveis nesta [visão geral][UDROverview].  

Os detalhes sobre como criar e configurar rotas definidas pelo usuário estão disponíveis neste [Guia de instruções][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Exemplo de configuração da UDR para um Ambiente do Serviço de Aplicativo
**Pré-requisitos**

1. Instale o Azure Powershell na [página Downloads do Azure][AzureDownloads] (de junho de 2015 em diante).  Em "Ferramentas de linha de comando", há um link "Instalar" em "Windows Powershell" que instalará os cmdlets mais recentes do PowerShell.
2. É recomendável que uma sub-rede exclusiva seja criada para uso exclusivo por um Ambiente do Serviço de Aplicativo.  Isso garante que as UDRs aplicadas à sub-rede abrirão apenas tráfego de saída para o Ambiente do Serviço de Aplicativo.
3. **Importante**: não implante o Ambiente do Serviço de Aplicativo **até que** as etapas de configuração a seguir sejam seguidas.  Isso garante que a conectividade de rede de saída esteja disponível antes da tentativa de implantar um Ambiente do Serviço de Aplicativo.

**Etapa 1: Criar uma tabela de rotas nomeada**

O trecho a seguir cria uma tabela de rota chamada "DirectInternetRouteTable" na região oeste dos EUA no Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Etapa 2: Criar uma ou mais rotas na tabela de rotas**

Você precisará adicionar uma ou mais rotas à tabela de rotas para permitir acesso de saída da Internet.  

A abordagem recomendada para configurar o acesso de saída à Internet é definir uma rota para 0.0.0.0/0, como mostrado abaixo.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Lembre-se de que 0.0.0.0/0 é um intervalo de endereços amplo e assim será substituído por intervalos de endereços mais específicos anunciados pelo ExpressRoute.  Para reiterar a recomendação anterior, um UDR com uma rota 0.0.0.0/0 deve ser usado em conjunto com uma configuração de Rota Expressa que só anuncia 0.0.0.0/0 também. 

Como alternativa, você pode baixar uma lista abrangente e atualizada de intervalos CIDR em uso pelo Azure.  O arquivo Xml que contém todos os intervalos de endereços IP do Azure está disponível no [Centro de Download da Microsoft][DownloadCenterAddressRanges].  

No entanto, observe que esses intervalos se alteram ao longo do tempo, necessitando de atualizações manuais periódicas para manter as rotas definidas por usuários em sincronia.  Além disso, uma vez que há um limite superior padrão de 100 rotas em um único UDR, você precisará "resumir" os intervalos de endereços IP do Azure para caberem no limite de 100 rotas, tendo em mente que rotas definidas por UDR precisam ser mais específicas que as rotas anunciadas pelo ExpressRoute.  

**Etapa 3: Associar a tabela de rotas à sub-rede que contém o Ambiente do Serviço de Aplicativo**

A última etapa da configuração é associar a tabela de rotas à sub-rede em que o Ambiente do Serviço de Aplicativo será implantado.  O comando a seguir associa "DirectInternetRouteTable" a "ASESubnet" que, no fim, conterá um Ambiente do Serviço de Aplicativo.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Etapa 4: Etapas finais**

Depois que a tabela de rotas estiver associada à sub-rede, é recomendável primeiro testar e confirmar o efeito desejado.  Por exemplo, implante uma máquina virtual na sub-rede e verifique se:

* O tráfego de saída para pontos de extremidade do Azure e que não sejam do Azure mencionados anteriormente neste artigo **não** está fluindo pelo circuito do ExpressRoute.  É muito importante verificar esse comportamento, uma vez que o tráfego de saída da sub-rede ainda está sendo encapsulado à força no local, sempre haverá falha na criação do Ambiente do Serviço de Aplicativo. 
* Todas as pesquisas de DNS para os pontos de extremidade mencionados anteriormente estão sendo resolvidas corretamente. 

Depois que as etapas acima forem confirmadas, você precisará excluir a máquina virtual porque a sub-rede precisa estar "vazia" no momento em que o Ambiente do Serviço de Aplicativo é criado.

Prossiga então com a criação de um Ambiente do Serviço de Aplicativo.

## <a name="getting-started"></a>Introdução
Para começar a usar Ambientes de Serviço de Aplicativo, veja [Introdução ao ambiente de Serviço de Aplicativo][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
