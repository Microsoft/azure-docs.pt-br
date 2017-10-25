---
title: "Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo"
description: "Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um ambiente de serviço de aplicativo."
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: 
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo
## <a name="overview"></a>Visão geral
Um Ambiente de Serviço de Aplicativo pode ser criado **tanto** em uma rede virtual do Azure Resource Manager, **quanto** em uma [rede virtual][virtualnetwork] de modelo de implantação clássico.  Uma nova rede virtual e uma nova sub-rede podem ser definidas no momento em que um Ambiente de Serviço de Aplicativo é criado.  Como alternativa, um Ambiente de Serviço de Aplicativo pode ser criado em uma rede virtual e uma sub-rede existentes.  Com uma alteração recente feita em junho de 2016, os ASEs agora podem ser implantados nas redes virtuais que usam os intervalos de endereço público ou espaços de endereço RFC1918 (ou seja, endereços privados).  Para saber mais sobre a criação de um Ambiente de Serviço de Aplicativo, veja [Como criar um ambiente de serviço de aplicativo][HowToCreateAnAppServiceEnvironment].

Um ambiente de serviço de aplicativo sempre deve ser criado em uma sub-rede, porque uma sub-rede fornece um limite de rede que pode ser usado para bloquear o tráfego de entrada por trás de dispositivos e serviços de upstream, de modo que o tráfego HTTP e HTTPS é aceito apenas de endereços IP upstream específicos.

O tráfego de rede de entrada e saída em uma sub-rede é controlado usando um [grupo de segurança de rede][NetworkSecurityGroups]. Controlar o tráfego de entrada requer a criação de regras de segurança de rede em um grupo de segurança de rede, seguida da atribuição do grupo de segurança de rede à sub-rede que contém o ambiente de serviço de aplicativo.

Quando um grupo de segurança de rede é atribuído a uma sub-rede, o tráfego de entrada para aplicativos no ambiente de serviço de aplicativo é permitido/bloqueado com base em regras de permissão e bloqueio definidas no grupo de segurança de rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de entrada de rede usadas em um Ambiente de Serviço de Aplicativo
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede requeridas e opcionais usadas por um ambiente de serviço de aplicativo.  Fechar acidentalmente o tráfego para algumas portas pode resultar em perda de funcionalidade em um ambiente de serviço de aplicativo.

A seguir está uma lista de portas usadas por um Ambiente de Serviço de Aplicativo. Todas as portas são **TCP**, a menos que indicado o claramente contrário:

* 454: **Porta requerida** usada pela infraestrutura do Azure para gerenciamento e manutenção de Ambientes de Serviço de Aplicativo via SSL.  Não bloqueie o tráfego para essa porta.  Essa porta é sempre associada ao VIP público de um ASE.
* 455: **Porta requerida** usada pela infraestrutura do Azure para gerenciamento e manutenção de Ambientes de Serviço de Aplicativo via SSL.  Não bloqueie o tráfego para essa porta.  Essa porta é sempre associada ao VIP público de um ASE.
* 80: porta padrão para tráfego HTTP de entrada para aplicativos executados em Planos de Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 443: porta padrão para tráfego SSL de entrada para aplicativos executados em Planos de Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 21: canal de controle para FTP.  Essa porta pode ser bloqueada com segurança se o FTP não está sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB de um ASE.
* 990: canal de controle para FTPS.  Essa porta poderá ser bloqueada com segurança se o FTPS não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB de um ASE.
* 10001-10020: canais de dados para FTP.  Assim como acontece com o canal de controle, essas portas podem ser bloqueadas com segurança se o FTP não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB do ASE.
* 4016: usado para depuração remota com o Visual Studio 2012.  Essa porta pode ser bloqueada com segurança se o recurso não está sendo usado.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 4018: usado para depuração remota com o Visual Studio 2013.  Essa porta pode ser bloqueada com segurança se o recurso não está sendo usado.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 4020: usado para depuração remota com o Visual Studio 2015.  Essa porta pode ser bloqueada com segurança se o recurso não está sendo usado.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e conectividade de saída
Para que um Ambiente de Serviço de Aplicativo funcione corretamente, ele requer acesso de saída a vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usado por um ASE está na seção "Conectividade de rede necessária" do artigo [Configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Ambientes de Serviço de Aplicativo exigem uma infraestrutura DNS válida configurada para a rede virtual.  Se por algum motivo, a configuração do DNS for alterada após ter sido criado um Ambiente do Serviço de Aplicativo, os desenvolvedores podem forçar um Ambiente do Serviço de Aplicativo para captar a nova configuração de DNS.  Acionar uma reinicialização do ambiente sem interrupção usando o ícone "Reiniciar" localizado na parte superior da folha de gerenciamento do Ambiente de Serviço de Aplicativo no [Portal do Azure][NewPortal] fará com que o ambiente capture a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na rede virtual sejam configurados com antecedência antes da criação de um ambiente do Serviço de Aplicativo.  Se a configuração DNS de uma rede virtual for alterada enquanto um Ambiente de Serviço de Aplicativo estiver sendo criado, isso resultará em falha do processo de criação do Ambiente de Serviço de Aplicativo.  Do mesmo modo, se um servidor DNS personalizado existir na outra extremidade de um gateway de VPN e estiver inacessível ou indisponível, o processo de criação do Ambiente do Serviço de Aplicativo também falhará.

## <a name="creating-a-network-security-group"></a>Criando um grupo de segurança de rede
Para saber mais sobre como funcionam os grupos de segurança de rede, veja as seguintes [informações][NetworkSecurityGroups].  O exemplo de Gerenciamento do Serviço do Azure abaixo mostra alguns destaques sobre os grupos de segurança de rede, concentrando-se em como configurar e aplicar um grupo de segurança de rede a uma sub-rede que contém um Ambiente de Serviço de Aplicativo.

**Observação:** os grupos de segurança de rede podem ser configurados graficamente usando o [Portal do Azure](https://portal.azure.com) ou por meio do Azure PowerShell.

Grupos de segurança de rede são criados pela primeira vez como uma entidade autônoma associada a uma assinatura. Como os grupos de segurança de rede são criados em uma região do Azure, certifique-se de que o grupo de segurança de rede é criado na mesma região que o ambiente de serviço de aplicativo.

A seguir, demonstra-se como criar um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Depois de um grupo de segurança de rede ter sido criado, uma ou mais regras de segurança de rede são adicionadas a ele.  Já que o conjunto de regras pode ser alterado ao longo do tempo, recomenda-se espaçar o esquema de numeração usado para as prioridades de regra, para tornar mais fácil inserir regras adicionais ao longo do tempo.

O exemplo a seguir mostra uma regra que concede explicitamente o acesso às portas de gerenciamento requeridas pela infraestrutura do Azure para gerenciar e manter um ambiente de serviço de aplicativo.  Observe que todo o tráfego de gerenciamento passa por SSL e é protegido por certificados de cliente, portanto, mesmo que as portas estejam abertas elas são inacessíveis por qualquer entidade que não seja a infraestrutura de gerenciamento do Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Ao bloquear o acesso às portas 80 e 443 para “ocultar” um ambiente de serviço de aplicativo por trás de serviços ou dispositivos upstream, você precisará saber o endereço IP upstream.  Por exemplo, se você estiver usando um firewall de aplicativo Web (WAF), o WAF terá seu próprio endereço (ou endereços) IP, que ele utilizará ao usar proxy de tráfego para um ambiente de serviço de aplicativo downstream.  Você precisará usar esse endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um determinado endereço IP upstream é explicitamente permitido.  O endereço *1.2.3.4* é usado como um espaço reservado para o endereço IP de um WAF upstream.  Altere o valor para coincidir com o endereço usado pelo serviço ou dispositivo upstream.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Se desejar suporte a FTP, as regras a seguir podem usadas como um modelo para conceder acesso à porta de controle de FTP e às portas de canal de dados.  Como o FTP é um protocolo com monitoramento de estado, você não poderá rotear o tráfego FTP por meio de um dispositivo de proxy ou firewall HTTP/HTTPS tradicional.  Nesse caso, será necessário definir o *SourceAddressPrefix* para um valor diferente - por exemplo, o intervalo de endereços IP de computadores de desenvolvedor ou de implantação, nos quais clientes FTP estão em execução. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Observação:** o intervalo de portas do canal de dados pode mudar durante o período de visualização.)

Se a depuração remota com o Visual Studio é usada, as regras a seguir demonstram como conceder acesso.  Há uma regra separada para cada versão do Visual Studio para a qual há suporte, já que cada versão usa uma porta diferente para a depuração remota.  Assim como acontece com acesso ao FTP, o tráfego de depuração remota pode não fluir corretamente por meio de um dispositivo de proxy ou WAF tradicional.  O *SourceAddressPrefix* pode ser definido, em vez disso, como o intervalo de endereços IP dos computadores de desenvolvedor executando o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuir um grupo de segurança de rede a uma sub-rede
Um grupo de segurança de rede tem uma regra de segurança padrão que nega o acesso a todo o tráfego externo.  O resultado da combinação entre as regras de segurança de rede descritas acima e a regra de segurança padrão bloqueando o tráfego de entrada é que somente o tráfego de intervalos de endereços de origem associado a uma ação *Permitir* poderá enviar tráfego a aplicativos em execução em um ambiente de serviço de aplicativo.

Depois que um grupo de segurança de rede é preenchido com regras de segurança, ele precisa ser atribuído à sub-rede que contém o ambiente de serviço de aplicativo.  O comando de atribuição referencia tanto o nome da rede virtual onde reside o ambiente de serviço de aplicativo como o nome da sub-rede onde o ambiente de serviço de aplicativo foi criado.  

O exemplo a seguir mostra um grupo de segurança de rede que está sendo atribuído a uma sub-rede e rede virtual:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Depois que a atribuição de grupo de segurança de rede for bem-sucedida (a atribuição é uma operação demorada e pode levar alguns minutos para ser concluída), somente tráfego de entrada correspondendo às regras *Permitir* alcançará com êxito os aplicativos no ambiente de serviço de aplicativo.

Para fins de exatidão, o exemplo a seguir mostra como remover e, portanto, dissociar o grupo de segurança de rede da sub-rede:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para IP-SSL explícito
Se um aplicativo for configurado com um endereço IP-SSL explícito (aplicável *somente* a ASEs que têm um VIP público), ao invés de usar o endereço IP padrão do Ambiente do Serviço de Aplicativo, o tráfego HTTP e HTTPS fluirá para a sub-rede por um conjunto de portas diferente das portas 80 e 443.

O par individual de portas usadas por cada endereço IP-SSL pode ser encontrado na interface do usuário pela folha UX de detalhes do Ambiente de Serviço de Aplicativo.  Selecione "Todas as configurações"--> "Endereços IP".  A folha "Endereços IP" mostra uma tabela de todos os endereços IP-SSL configurados explicitamente para o Ambiente de Serviço de Aplicativo, juntamente com o par de portas especial que é usado para rotear o tráfego HTTP e HTTPS associado a cada endereço IP-SSL.  É esse par de portas que precisa ser usado para os parâmetros DestinationPortRange ao configurar regras em um grupo de segurança de rede.

Quando um aplicativo em um ASE é configurado para usar IP-SSL, os clientes externos não verão e não precisarão se preocupar com o mapeamento de par de portas especial.  O tráfego para os aplicativos fluirá normalmente para o endereço IP-SSL configurado.  A conversão para o par de portas especial ocorrerá internamente de forma automática durante o segmento final do roteamento do tráfego para a sub-rede que contém o ASE. 

## <a name="getting-started"></a>Introdução
Para começar a usar Ambientes de Serviço de Aplicativo, veja [Introdução ao ambiente de Serviço de Aplicativo][IntroToAppServiceEnvironment]

Para saber mais sobre aplicativos que se conectam com segurança a recursos do back-end a partir de um Ambiente de Serviço de Aplicativo, veja [Conexão segura a recursos de back-end a partir de um ambiente do Serviço de Aplicativo][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

