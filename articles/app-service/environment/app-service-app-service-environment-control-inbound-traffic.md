---
title: Controlar o tráfego de entrada v1
description: Saiba como controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962053"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicativo
## <a name="overview"></a>Visão geral
Um Ambiente de Serviço de Aplicativo pode ser criado **tanto** em uma rede virtual do Azure Resource Manager, **quanto** em uma [rede virtual][virtualnetwork] de modelo de implantação clássico.  Uma nova rede virtual e uma nova sub-rede podem ser definidas no momento em que um Ambiente de Serviço de Aplicativo é criado. Em vez disso, um Ambiente do Serviço de Aplicativo pode ser criado em uma rede virtual pré-existente e uma sub-rede já existente.  A partir de junho de 2016, o ASEs também pode ser implantado em redes virtuais que usam intervalos de endereços públicos ou espaços de endereço RFC1918 (endereços privados).  Para obter mais informações, consulte [como criar um ambiente do serviço de aplicativo][HowToCreateAnAppServiceEnvironment].

Sempre crie um Ambiente do Serviço de Aplicativo dentro de uma sub-rede. Uma sub-rede fornece um limite de rede que pode ser usado para bloquear o tráfego de entrada por trás de dispositivos e serviços de upstream. Essa configuração permite que apenas endereços IP de upstream específicos aceitem o tráfego HTTP e HTTPS.

O tráfego de rede de entrada e saída em uma sub-rede é controlado usando um [grupo de segurança de rede][NetworkSecurityGroups]. Para controlar o tráfego de entrada, crie regras de segurança de rede em um grupo de segurança de rede. Em seguida, atribua o grupo de segurança de rede à sub-rede que contém o Ambiente do Serviço de Aplicativo.

Depois de atribuir um grupo de segurança de rede a uma sub-rede, o tráfego de entrada para os aplicativos na Ambiente do Serviço de Aplicativo é permitido ou bloqueado com base nas regras de permissão e negação definidas no grupo de segurança de rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de entrada de rede usadas em um Ambiente de Serviço de Aplicativo
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, conheça o conjunto de portas de rede necessárias e opcionais usadas por um Ambiente do Serviço de Aplicativo.  Fechar acidentalmente o tráfego para algumas portas pode resultar em perda de funcionalidade em um ambiente de serviço de aplicativo.

A lista a seguir contém as portas usadas por um Ambiente do Serviço de Aplicativo. Todas as portas são **TCP**, a menos que indicado o claramente contrário:

* 454:  **porta necessária** usada pela infraestrutura do Azure para gerenciar e manter ambientes de serviço de aplicativo via TLS.  Não bloqueie o tráfego para esta porta.  Essa porta é sempre associada ao VIP público de um ASE.
* 455:  **porta necessária** usada pela infraestrutura do Azure para gerenciar e manter ambientes de serviço de aplicativo via TLS.  Não bloqueie o tráfego para esta porta.  Essa porta é sempre associada ao VIP público de um ASE.
* 80: porta padrão para tráfego HTTP de entrada para aplicativos executados em Planos de Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 443: porta padrão para tráfego TLS de entrada para aplicativos que são executados nos planos do serviço de aplicativo em um Ambiente do Serviço de Aplicativo.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 21: canal de controle para FTP.  Essa porta pode ser bloqueada com segurança se o FTP não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB de um ASE.
* 990: canal de controle para FTPS.  Essa porta pode ser bloqueada com segurança se a FTPS não estiver sendo usada.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB de um ASE.
* 10001-10020: canais de dados para FTP.  Assim como no canal de controle, essas portas podem ser bloqueadas com segurança se o FTP não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB do ASE.
* 4016: usado para depuração remota com o Visual Studio 2012.  Essa porta pode ser bloqueada com segurança se o recurso não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 4018: usado para depuração remota com o Visual Studio 2013.  Essa porta pode ser bloqueada com segurança se o recurso não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.
* 4020: usado para depuração remota com o Visual Studio 2015.  Essa porta pode ser bloqueada com segurança se o recurso não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta é associada ao endereço ILB do ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e conectividade de saída
Para que um Ambiente de Serviço de Aplicativo funcione corretamente, ele requer acesso de saída a vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usado por um ASE está na seção "Conectividade de rede necessária" do artigo [Configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Ambientes de Serviço de Aplicativo exigem uma infraestrutura DNS válida configurada para a rede virtual.  Se a configuração de DNS for alterada após a criação de um Ambiente do Serviço de Aplicativo, os desenvolvedores poderão forçar um Ambiente do Serviço de Aplicativo a escolher a nova configuração de DNS.  Se você disparar uma reinicialização do ambiente sem interrupção usando o ícone de **reinicialização** , o ambiente selecionará a nova configuração de DNS. (O ícone de **reinicialização** está localizado na parte superior da folha gerenciamento de ambiente do serviço de aplicativo, na [portal do Azure][NewPortal].)

Também é recomendável que todos os servidores DNS personalizados na vnet sejam configurados antecipadamente antes da criação de um Ambiente do Serviço de Aplicativo.  Se a configuração de DNS de uma rede virtual for alterada durante a criação de um Ambiente do Serviço de Aplicativo, o processo de criação de Ambiente do Serviço de Aplicativo falhará.  Da mesma forma, se houver um servidor DNS personalizado inacessível ou não disponível na outra extremidade de um gateway de VPN, o processo de criação de Ambiente do Serviço de Aplicativo também falhará.

## <a name="creating-a-network-security-group"></a>Criando um grupo de segurança de rede
Para saber mais sobre como funcionam os grupos de segurança de rede, veja as seguintes [informações][NetworkSecurityGroups].  O exemplo de gerenciamento de serviços do Azure abaixo aborda os destaques dos grupos de segurança de rede. O exemplo configura e aplica um grupo de segurança de rede a uma sub-rede que contém um Ambiente do Serviço de Aplicativo.

**Observação:** Os grupos de segurança de rede podem ser configurados graficamente usando o [portal do Azure](https://portal.azure.com) ou por meio de Azure PowerShell.

Grupos de segurança de rede são criados pela primeira vez como uma entidade autônoma associada a uma assinatura. Como os grupos de segurança de rede são criados em uma região do Azure, crie o grupo de segurança de rede na mesma região que o Ambiente do Serviço de Aplicativo.

O comando a seguir demonstra como criar um grupo de segurança de rede:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Depois de um grupo de segurança de rede ter sido criado, uma ou mais regras de segurança de rede são adicionadas a ele.  Como o conjunto de regras pode mudar ao longo do tempo, você deve espaçar o esquema de numeração usado para as prioridades de regra. Essa prática facilita a inserção de regras adicionais ao longo do tempo.

No exemplo a seguir, uma regra concede explicitamente acesso às portas de gerenciamento necessárias à infraestrutura do Azure para gerenciar e manter um Ambiente do Serviço de Aplicativo.  Todo o tráfego de gerenciamento flui por TLS e é protegido por certificados de cliente. Embora as portas sejam abertas, elas são inacessíveis por qualquer entidade que não seja a infraestrutura de gerenciamento do Azure.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Quando você bloqueia o acesso à porta 80 e 443 para "Ocultar" um Ambiente do Serviço de Aplicativo por trás de dispositivos ou serviços upstream, lembre-se do endereço IP upstream.  Por exemplo, se você estiver usando um WAF (firewall do aplicativo Web), o WAF terá seu próprio endereço IP ou endereços. O WAF os usa ao fazer o proxy do tráfego para um Ambiente do Serviço de Aplicativo downstream.  Você precisará usar esse endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um determinado endereço IP upstream é explicitamente permitido.  O endereço *1.2.3.4* é usado como um espaço reservado para o endereço IP de um WAF upstream.  Altere o valor para coincidir com o endereço usado pelo serviço ou dispositivo upstream.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Se o suporte a FTP for desejado, use as regras a seguir como um modelo para conceder acesso à porta de controle de FTP e às portas de canal de dados.  Como o FTP é um protocolo com estado, talvez não seja possível rotear o tráfego de FTP por meio de um firewall HTTP/HTTPS tradicional ou um dispositivo proxy.  Nesse caso, você precisará definir o *SourceAddressPrefix* para um valor diferente, como o intervalo de endereços IP de computadores de desenvolvedor ou de implantação nos quais os clientes FTP estão em execução. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**Observação:**  o intervalo de portas do canal de dados pode ser alterado durante o período de visualização.)

Se a depuração remota com o Visual Studio é usada, as regras a seguir demonstram como conceder acesso.  Há uma regra separada para cada versão com suporte do Visual Studio, já que cada versão usa uma porta diferente para a depuração remota.  Assim como acontece com acesso ao FTP, o tráfego de depuração remota pode não fluir corretamente por meio de um dispositivo de proxy ou WAF tradicional.  O *SourceAddressPrefix* pode ser definido, em vez disso, como o intervalo de endereços IP dos computadores de desenvolvedor executando o Visual Studio.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuir um grupo de segurança de rede a uma sub-rede
Um grupo de segurança de rede tem uma regra de segurança padrão que nega o acesso a todo o tráfego externo. Quando você combina essa regra com as regras de segurança de rede acima, somente o tráfego dos intervalos de endereços de origem associados a uma ação *permitir* poderá enviar tráfego para aplicativos executados em um ambiente do serviço de aplicativo.

Depois que um grupo de segurança de rede é preenchido com regras de segurança, atribua-o à sub-rede que contém o Ambiente do Serviço de Aplicativo.  O comando de atribuição faz referência a dois nomes: o nome da rede virtual em que o Ambiente do Serviço de Aplicativo é e o nome da sub-rede em que o Ambiente do Serviço de Aplicativo foi criado.  

O exemplo a seguir mostra um grupo de segurança de rede que está sendo atribuído a uma sub-rede e rede virtual:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

A atribuição é uma operação de execução longa e pode levar alguns minutos para ser concluída. Depois que a atribuição do grupo de segurança de rede for bem-sucedida, somente o tráfego de entrada que corresponde às regras de *permissão* alcançará com êxito os aplicativos no ambiente do serviço de aplicativo.

Para fins de integridade, o exemplo a seguir mostra como remover e desassociar o grupo de segurança de rede da sub-rede:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para IP-SSL explícito
Se um aplicativo estiver configurado com um endereço IP-SSL explícito (aplicável *somente* a ases que têm um VIP público), em vez de usar o endereço IP padrão do ambiente do serviço de aplicativo, o tráfego HTTP e HTTPS fluirá para a sub-rede por portas diferentes das portas 80 e 443.

Para localizar o par individual de portas que é usado por cada endereço IP-SSL, acesse o portal e exiba a folha UX de detalhes do Ambiente do Serviço de Aplicativo.  Selecione **todas as configurações**  >  **endereços IP**.  A folha **endereços IP** mostra uma tabela de todos os endereços IP-SSL configurados explicitamente para o ambiente do serviço de aplicativo. A folha também mostra o par de portas especiais usado para rotear o tráfego HTTP e HTTPS associado a cada endereço IP-SSL.  Use este par de portas para os parâmetros DestinationPortRange ao configurar regras em um grupo de segurança de rede.

Quando um aplicativo em um ASE é configurado para usar IP-SSL, os clientes externos não veem ou precisam se preocupar com o mapeamento de pares de portas especiais.  O tráfego para os aplicativos fluirá normalmente para o endereço IP-SSL configurado.  A conversão para o par de portas especiais ocorre automaticamente internamente, durante o trecho final do tráfego de roteamento na sub-rede que contém o ASE. 

## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment].

Para obter mais informações, consulte [conectando-se com segurança a recursos de back-end de um ambiente do serviço de aplicativo][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->