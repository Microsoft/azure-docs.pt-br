---
title: Conectar ao back-end v1
description: Saiba como realizar conexão segura a recursos de back-end a partir de um ambiente do Serviço de Aplicativo. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961798"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Conectar-se com segurança a recursos de back-end em um Ambiente do Serviço de Aplicativo
Como um Ambiente do Serviço de Aplicativo sempre é criado **ou** em uma rede virtual do Azure Resource Manager, **ou** em uma [rede virtual][virtualnetwork] do modelo de implantação clássico, as conexões de saída de um Ambiente do Serviço de Aplicativo com outros recursos de back-end podem fluir exclusivamente pela rede virtual. A partir de junho de 2016, o ASEs também pode ser implantado em redes virtuais que usam intervalos de endereços públicos ou espaços de endereço RFC1918 (endereços privados).  

Por exemplo, pode haver um SQL Server em execução em um cluster de máquinas virtuais com a porta 1433 bloqueada.  O ponto de extremidade pode ser ACLd, para permitir apenas acesso de outros recursos na mesma rede virtual.  

Como outro exemplo, pontos de extremidade confidenciais podem ser executados localmente e conectados ao Azure via conexões [Site a Site][SiteToSite] ou do [Azure ExpressRoute][ExpressRoute].  Como resultado, somente os recursos nas redes virtuais conectadas aos túneis site a site ou ExpressRoute podem acessar pontos de extremidade locais.

Para todos esses cenários, os aplicativos em execução em um Ambiente do Serviço de Aplicativo podem se conectar com segurança aos vários servidores e recursos. Se o tráfego de saída de aplicativos for executado em um Ambiente do Serviço de Aplicativo para pontos de extremidade privados na mesma rede virtual ou conectado à mesma rede virtual, ele fluirá apenas pela rede virtual.  O tráfego de saída para pontos de extremidade privados não fluirá pela Internet pública.

Um problema se aplica ao tráfego de saída de um Ambiente do Serviço de Aplicativo para pontos de extremidade em uma rede virtual. Ambientes de serviço de aplicativo não podem alcançar pontos de extremidade de máquinas virtuais que estão localizados na **mesma** sub-rede que o ambiente do serviço de aplicativo. Essa limitação normalmente não deve ser um problema, se os ambientes do serviço de aplicativo forem implantados em uma sub-rede reservada para uso exclusivo pelo Ambiente do Serviço de Aplicativo.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e conectividade de saída
Para um Ambiente de Serviço de Aplicativo funcionar corretamente, ele requer acesso de saída a vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usado por um ASE está na seção "Conectividade de rede necessária" do artigo [Configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Ambientes de Serviço de Aplicativo exigem uma infraestrutura DNS válida configurada para a rede virtual.  Se a configuração de DNS for alterada após a criação de um Ambiente do Serviço de Aplicativo, os desenvolvedores poderão forçar um Ambiente do Serviço de Aplicativo a escolher a nova configuração de DNS. Na parte superior da folha gerenciamento de Ambiente do Serviço de Aplicativo no portal, selecione o ícone de **reinicialização** para disparar uma reinicialização do ambiente sem interrupção, o que faz com que o ambiente pegue a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet sejam configurados antecipadamente antes da criação de um Ambiente do Serviço de Aplicativo.  Se a configuração de DNS de uma rede virtual for alterada durante a criação de um Ambiente do Serviço de Aplicativo, isso resultará na falha do processo de criação de Ambiente do Serviço de Aplicativo. Na outra extremidade de um gateway de VPN, se houver um servidor DNS personalizado inacessível ou indisponível, o processo de criação de Ambiente do Serviço de Aplicativo também falhará.

## <a name="connecting-to-a-sql-server"></a>Conectando-se a um SQL Server
Uma configuração comum do SQL Server tem um ponto de extremidade escutando na porta 1433:

![Ponto de extremidade do SQL Server][SqlServerEndpoint]

Há duas abordagens para restringir o tráfego para esse ponto de extremidade:

* [Listas de controle de acesso a redes][NetworkAccessControlLists] (ACLs de rede)
* [Grupos de segurança de rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringindo o acesso com uma ACL de rede
A porta 1433 pode ser protegida usando uma lista de controle de acesso de rede.  O exemplo a seguir adiciona às permissões de atribuição os endereços de cliente que se originam de dentro de uma rede virtual e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controle de acesso de rede][NetworkAccessControlListExample]

Todos os aplicativos que são executados no Ambiente do Serviço de Aplicativo, na mesma rede virtual que o SQL Server, podem se conectar à instância de SQL Server. Use o endereço IP **interno da VNet** para a máquina virtual SQL Server.  

A cadeia de conexão de exemplo a seguir faz referência ao SQL Server usando seu endereço IP privado.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Embora a máquina virtual também tenha um ponto de extremidade público, as tentativas de conexão para usar o endereço IP público serão rejeitadas por causa da ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringindo o acesso com um grupo de segurança de rede
Uma abordagem alternativa para proteger o acesso é com um grupo de segurança de rede.  Grupos de segurança de rede podem ser aplicados a máquinas virtuais individuais ou a uma sub-rede que contenha as máquinas virtuais.

Primeiro, você precisará criar um grupo de segurança de rede:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Restringir o acesso apenas ao tráfego interno de VNet é simples com um grupo de segurança de rede.  As regras padrão em um grupo de segurança de rede somente permitem o acesso a partir de outros clientes de rede na mesma rede virtual.

Como resultado, o bloqueio do acesso ao SQL Server é simples. Basta aplicar um grupo de segurança de rede com suas regras padrão para as máquinas virtuais em execução SQL Server ou a sub-rede que contém as máquinas virtuais.

O exemplo a seguir aplica a um grupo de segurança de rede à sub-rede que o contém:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

O resultado final é um conjunto de regras de segurança que bloqueiam o acesso externo, permitindo acesso interno à VNet:

![Regras de segurança de rede padrão][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes sobre como controlar o tráfego de entrada para seu Ambiente do Serviço de Aplicativo, confira [Como controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png