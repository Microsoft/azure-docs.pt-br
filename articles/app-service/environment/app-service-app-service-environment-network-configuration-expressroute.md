---
title: Configurar o Azure ExpressRoute v1
description: Configuração de rede para o Ambiente do Serviço de Aplicativo para o PowerApps com o Azure ExpressRoute. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 3fa2677f7c71b118ec3f7da717e29d5879ee982b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961815"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Detalhes da configuração de rede para o Ambiente do Serviço de Aplicativo para o PowerApps com o Azure ExpressRoute

Os clientes podem conectar um circuito do [Azure ExpressRoute][ExpressRoute] à infraestrutura de rede virtual para estender a rede local ao Azure. Um Ambiente do Serviço de Aplicativo é criado em uma sub-rede dessa infraestrutura de [rede virtual][virtualnetwork]. Aplicativos executados no Ambiente do Serviço de Aplicativo estabelecem conexões seguras com recursos de back-end acessíveis apenas pela conexão do ExpressRoute.  

O Ambiente do Serviço de Aplicativo pode ser criado nesses cenários:
- Redes virtuais do Azure Resource Manager.
- Redes virtuais do modelo de implantação clássico.
- Redes virtuais que usam intervalos de endereços públicos ou espaços de endereço RFC1918 (ou seja, endereços privados). 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>Conectividade de rede necessária

O Ambiente do Serviço de Aplicativo tem requisitos de conectividade de rede que podem não ser atendidos inicialmente em uma rede virtual conectada ao ExpressRoute.

O Ambiente do Serviço de Aplicativo requer as seguintes configurações de conectividade de rede para funcionar corretamente:

* A conectividade de rede de saída com pontos de extremidade do Armazenamento do Azure em todo o mundo nas portas 80 e 443. Esses pontos de extremidade estão localizados na mesma região que o Ambiente do Serviço de Aplicativo e também em outras regiões do Azure. Os pontos de extremidade do Armazenamento do Azure são resolvidos nos seguintes domínios DNS: table.core.windows.net, blob.core.windows.net, queue.core.windows.net e file.core.windows.net.  

* Conectividade de rede de saída para o serviço de arquivos do Azure na porta 445.

* Conectividade de rede de saída com pontos de extremidade do Banco de Dados SQL do Azure localizados na mesma região que o Ambiente do Serviço de Aplicativo. Os pontos de extremidade do Banco de Dados SQL são resolvidos no domínio database.windows.net, que exige acesso aberto às portas 1433, 11000-11999 e 14000-14999. Para obter detalhes sobre o uso da porta V12 do Banco de Dados SQL, confira [Portas além da 1433 para ADO.NET 4.5](../../azure-sql/database/adonet-v12-develop-direct-route-ports.md).

* Conectividade de rede de saída com os pontos de extremidade do plano de gerenciamento do Azure (modelo de implantação clássico do Azure e pontos de extremidade do Azure Resource Manager). A conectividade com esses pontos de extremidade inclui os domínios management.core.windows.net e management.azure.com. 

* Conectividade de rede de saída com os domínios ocsp.msocsp.com, mscrl.microsoft.com e crl.microsoft.com. A conectividade com esses domínios é necessária para compatibilidade com a funcionalidade TLS.

* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados neste artigo. Se os pontos de extremidade não puderem ser resolvidos, a criação do Ambiente do Serviço de Aplicativo falhará. Qualquer Ambiente do Serviço de Aplicativo existente está marcado como não íntegro.

* O acesso de saída na porta 53 é necessário para a comunicação com servidores DNS.

* Se houver um servidor DNS personalizado na outra extremidade de um gateway de VPN, o servidor DNS deverá estar acessível na sub-rede que contém o Ambiente do Serviço de Aplicativo. 

* O caminho da rede de saída não pode passar por proxies corporativos internos, nem pode ser encapsulado à força localmente. Essas ações alteram o endereço NAT em vigor do tráfego de rede de saída do Ambiente do Serviço de Aplicativo. Alterações no endereço NAT do tráfego de rede de saída do Ambiente do Serviço de Aplicativo causa falhas de conectividade em muitos pontos de extremidade. Falha na criação de um Ambiente do Serviço de Aplicativo. Qualquer Ambiente do Serviço de Aplicativo existente está marcado como não íntegro.

* O acesso de rede de entrada a portas obrigatórias para os Ambientes do Serviço de Aplicativo deve ser permitido. Para conhecer os detalhes, confira [Como controlar o tráfego de entrada do Ambiente do Serviço de Aplicativo][requiredports].

Para atender aos requisitos de DNS, certifique-se de que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual. Se a configuração do DNS for alterada após o Ambiente do Serviço de Aplicativo ser criado, os desenvolvedores poderão forçar o Ambiente do Serviço de Aplicativo a captar a nova configuração de DNS. Você pode disparar uma reinicialização do ambiente usando o ícone **Reiniciar** no gerenciamento do Ambiente do Serviço de Aplicativo no [portal do Azure][NewPortal]. A reinicialização faz com que o ambiente capture a nova configuração do DNS.

Para atender aos requisitos de acesso de rede de entrada, configure um [NSG (grupo de segurança de rede)][NetworkSecurityGroups] na sub-rede do Ambiente do Serviço de Aplicativo. O NSG permite que o acesso necessário [controle o tráfego de entrada para o Ambiente do Serviço de Aplicativo][requiredports].

## <a name="outbound-network-connectivity"></a>Conectividade de rede de saída

Por padrão, um circuito do ExpressRoute recentemente criado anuncia uma rota padrão que permite conectividade de saída da Internet. O Ambiente do Serviço de Aplicativo pode usar essa configuração para se conectar a outros Pontos de Extremidade do Azure.

Uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0), que força o tráfego de saída da Internet para o fluxo local. Esse fluxo de tráfego invariavelmente interrompe o Ambiente do Serviço de Aplicativo. O tráfego de saída é bloqueado localmente ou seu endereço de rede é convertido em um conjunto irreconhecível de endereços que não funcionam mais com vários Pontos de Extremidade do Azure.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contém o Ambiente do Serviço de Aplicativo. Uma UDR define rotas específicas de sub-rede que são consideradas no lugar da rota padrão.

Se for possível, use a seguinte configuração:

* A configuração do ExpressRoute anuncia 0.0.0.0/0. Por padrão, a configuração força túneis para todo o tráfego de saída local.
* A UDR aplicada à sub-rede que contém o Ambiente do Serviço de Aplicativo define 0.0.0.0/0 com um tipo do próximo salto de Internet. Um exemplo dessa configuração é descrito posteriormente neste artigo.

O efeito combinado dessa configuração é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute. O acesso de saída à Internet do Ambiente do Serviço de Aplicativo é garantido.

> [!IMPORTANT]
> As rotas definidas em uma UDR devem ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo descrito na próxima seção usa o intervalo de endereços amplo 0.0.0.0/0. Esse intervalo pode acidentalmente ser substituído pelos anúncios de rota que usam intervalos de endereços mais específicos.
> 
> O Ambiente do Serviço de Aplicativo não dá suporte a configurações do ExpressRoute que fazem anúncios de modo cruzado de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado. As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se os intervalos de endereços forem anunciados de modo cruzado no caminho de emparelhamento privado, o túnel forçado de todos os pacotes de rede de saída da sub-rede do Ambiente do Serviço de Aplicativo será realizado para uma infraestrutura de rede local do cliente. Esse fluxo de rede não tem suporte no momento para o Ambiente do Serviço de Aplicativo. Uma solução é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento privado.
> 
> 

Para detalhes sobre rotas definidas pelo usuário, confira [Roteamento de tráfego de rede virtual][UDROverview].  

Para saber como criar e configurar rotas definidas pelo usuário, confira [Encaminhar o tráfego de rede com uma tabela de rotas usando o PowerShell][UDRHowTo].

## <a name="udr-configuration"></a>Configuração da UDR

Esta seção mostra um exemplo de configuração da UDR para um Ambiente do Serviço de Aplicativo.

### <a name="prerequisites"></a>Pré-requisitos

* Instale o Azure PowerShell da [página Downloads do Azure][AzureDownloads]. Escolha um download com uma data de junho de 2015 ou posterior. Em **Ferramentas de linha de comando** > **Windows PowerShell**, selecione **Instalar** para instalar os cmdlets mais recentes do PowerShell.

* Crie uma sub-rede única para uso exclusivo pelo Ambiente do Serviço de Aplicativo. Essa rede exclusiva garante que as UDRs aplicadas à sub-rede abrirão apenas tráfego de saída para o Ambiente do Serviço de Aplicativo.

> [!IMPORTANT]
> Implante o Ambiente do Serviço de Aplicativo apenas depois de concluir as etapas de configuração. As etapas garantem que a conectividade de rede de saída esteja disponível antes da tentativa de implantar um Ambiente do Serviço de Aplicativo.

### <a name="step-1-create-a-route-table"></a>Etapa 1: Criar uma tabela de rotas

Crie uma tabela de rota chamada **DirectInternetRouteTable** na região Oeste dos EUA do Azure, conforme mostrado neste snippet:

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>Etapa 2: Criar rotas na tabela

Adicione rotas à tabela de rotas para habilitar o acesso de saída à Internet.  

Configurar o acesso de saída à Internet. Defina uma rota para 0.0.0.0/0, conforme mostrado neste snippet de código:

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 é um intervalo de endereços amplo. O intervalo é substituído por intervalos de endereços anunciados pelo ExpressRoute que são mais específicos. Uma UDR com uma rota 0.0.0.0/0 deve ser usado em conjunto com uma configuração do ExpressRoute que também só anuncia 0.0.0.0/0. 

Como alternativa, baixe uma lista abrangente e atualizada de intervalos CIDR em uso pelo Azure. O arquivo XML com todos os intervalos de endereços IP do Azure está disponível no [Centro de Download da Microsoft][DownloadCenterAddressRanges].  

> [!NOTE]
>
> Os intervalos de endereços IP do Azure mudam com o passar do tempo. Rotas definidas pelo usuário precisam de atualizações manuais periódicas para serem mantidas em sincronia.
>
> Uma única UDR tem um limite superior padrão de 100 rotas. Você precisará "resumir" os intervalos de endereços IP do Azure para que eles caibam no limite de 100 rotas. Rotas definidas pela UDR precisam ser mais específicas que rotas anunciadas pela sua conexão do ExpressRoute.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>Etapa 3: Associar a tabela à sub-rede

Associe a tabela de rotas à sub-rede em que você pretende implantar Ambiente do Serviço de Aplicativo. Este comando associa a tabela **DirectInternetRouteTable** à sub-rede **ASESubnet** que conterá um Ambiente do Serviço de Aplicativo.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>Etapa 4: Testar e confirmar a rota

Após a tabela de rotas ser associada à sub-rede, teste e confirme a rota.

Implante uma máquina virtual na sub-rede e confirme estas condições:

* O tráfego de saída para Pontos de Extremidade do Azure e que não são do Azure descritos neste artigo **não** flui pelo circuito do ExpressRoute. Se o tráfego de saída da sub-rede for forçado no túnel local, a criação do Ambiente do Serviço de Aplicativo sempre falhará.
* Todas as pesquisas DNS para os pontos de extremidade descritos neste artigo são resolvidas corretamente. 

Depois de concluir as etapas de configuração e confirmar a rota, exclua a máquina virtual. A sub-rede precisa estar "vazia" quando o Ambiente do Serviço de Aplicativo é criado.

Agora você está pronto para implantar o Ambiente do Serviço de Aplicativo!

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Ambiente do Serviço de Aplicativo para PowerApps, confira [Introdução ao Ambiente do Serviço de Aplicativo][IntroToAppServiceEnvironment].

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[UDROverview]: ../../virtual-network/virtual-networks-udr-overview.md
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: ../../virtual-network/tutorial-create-route-table-powershell.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->