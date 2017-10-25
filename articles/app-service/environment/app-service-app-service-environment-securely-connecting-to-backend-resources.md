---
title: "Conexão segura a recursos de back-end a partir de um ambiente do Serviço de Aplicativo"
description: "Saiba como realizar conexão segura a recursos de back-end a partir de um ambiente do Serviço de Aplicativo."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.openlocfilehash: a43d88d64710b95dd753c19f02582f22accac8b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Conexão segura a recursos de back-end a partir de um ambiente do Serviço de Aplicativo
## <a name="overview"></a>Visão geral
Como um Ambiente do Serviço de Aplicativo sempre é criado **ou** em uma rede virtual do Azure Resource Manager, **ou** em uma [rede virtual][virtualnetwork] do modelo de implantação clássico, as conexões de saída de um Ambiente do Serviço de Aplicativo com outros recursos de back-end podem fluir exclusivamente pela rede virtual.  Com uma alteração recente feita em junho de 2016, os ASEs agora podem ser implantados nas redes virtuais que usam os intervalos de endereço público ou espaços de endereço RFC1918 (ou seja, endereços privados).  

Por exemplo, pode haver um SQL Server em execução em um cluster de máquinas virtuais com a porta 1433 bloqueada.  O ponto de extremidade pode ser ACLd, para permitir apenas acesso de outros recursos na mesma rede virtual.  

Como outro exemplo, pontos de extremidade confidenciais podem ser executados localmente e conectados ao Azure via conexões [Site a Site][SiteToSite] ou do [Azure ExpressRoute][ExpressRoute].  Como resultado, apenas os recursos nas redes virtuais conectadas a túneis Site a Site ou de ExpressRoute poderão acessar pontos de extremidade locais.

Para todos esses cenários, aplicativos em execução em um ambiente do serviço de aplicativo serão capazes de se conectar com segurança aos diversos servidores e recursos.  Tráfego de saída de aplicativos que são executados em um ambiente do serviço de aplicativo para pontos de extremidade privados na mesma rede virtual (ou conectados à mesma rede virtual) passará apenas pela rede virtual.  O tráfego de saída para pontos de extremidade privados não passará pela Internet pública.

Uma limitação se aplica ao tráfego de saída de um ambiente de Serviço de Aplicativo para pontos de extremidade em uma rede virtual.  Ambientes do Serviço de Aplicativo não podem acessar pontos de extremidade de máquinas virtuais localizados na **mesma** sub-rede que o ambiente do Serviço de Aplicativo.  Isso não deve ser um problema, desde que os ambientes do Serviço de Aplicativo sejam implantados em uma sub-rede reservada para uso exclusivo pelo ambiente do Serviço de Aplicativo.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisitos de DNS e conectividade de saída
Para um Ambiente de Serviço de Aplicativo funcionar corretamente, ele requer acesso de saída a vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usado por um ASE está na seção "Conectividade de rede necessária" do artigo [Configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Ambientes de Serviço de Aplicativo exigem uma infraestrutura DNS válida configurada para a rede virtual.  Se por algum motivo, a configuração do DNS for alterada após ter sido criado um Ambiente do Serviço de Aplicativo, os desenvolvedores podem forçar um Ambiente do Serviço de Aplicativo para captar a nova configuração de DNS.  Disparar uma reinicialização do ambiente sem interrupção usando o ícone "Reiniciar" localizado na parte superior da folha de gerenciamento do Ambiente de Serviço de Aplicativo no portal fará com que o ambiente capture a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na rede virtual sejam configurados com antecedência antes da criação de um ambiente do Serviço de Aplicativo.  Se a configuração DNS de uma rede virtual for alterada enquanto um Ambiente de Serviço de Aplicativo estiver sendo criado, isso resultará em falha do processo de criação do Ambiente de Serviço de Aplicativo.  Do mesmo modo, se um servidor DNS personalizado existir na outra extremidade de um gateway de VPN e estiver inacessível ou indisponível, o processo de criação do Ambiente do Serviço de Aplicativo também falhará.

## <a name="connecting-to-a-sql-server"></a>Conectando-se a um SQL Server
Uma configuração comum do SQL Server tem um ponto de extremidade escutando na porta 1433:

![Ponto de extremidade do SQL Server][SqlServerEndpoint]

Há duas abordagens para restringir o tráfego para esse ponto de extremidade:

* [Listas de controle de acesso a redes][NetworkAccessControlLists] (ACLs de rede)
* [Grupos de segurança de rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringindo o acesso com uma ACL de rede
A porta 1433 pode ser protegida usando uma lista de controle de acesso de rede.  O exemplo abaixo coloca endereços de cliente originados dentro de uma rede virtual em lista branca e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controle de acesso de rede][NetworkAccessControlListExample]

Quaisquer aplicativos em execução no ambiente de serviço de aplicativo na mesma rede virtual que o SQL Server serão capazes de se conectarem à instância do SQL Server usando o endereço IP **VNet interno** para a máquina virtual do SQL Server.  

A cadeia de conexão de exemplo a seguir faz referência ao SQL Server usando seu endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Embora a máquina virtual também tenha um ponto de extremidade público, tentativas de conexão usando o endereço IP público serão rejeitadas devido à ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringindo o acesso com um grupo de segurança de rede
Uma abordagem alternativa para proteger o acesso é com um grupo de segurança de rede.  Grupos de segurança de rede podem ser aplicados a máquinas virtuais individuais ou a uma sub-rede que contenha as máquinas virtuais.

Primeiro é preciso criar um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso apenas ao tráfego VNet interno é muito simples com um grupo de segurança de rede.  As regras padrão em um grupo de segurança de rede somente permitem o acesso a partir de outros clientes de rede na mesma rede virtual.

Como resultado, bloquear o acesso ao SQL Server é tão simples quanto a aplicação de um grupo de segurança de rede com suas regras padrão às máquinas virtuais executando o SQL Server ou então à sub-rede que contém as máquinas virtuais.

O exemplo a seguir aplica a um grupo de segurança de rede à sub-rede que o contém:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

O resultado final é um conjunto de regras de segurança que bloqueiam o acesso externo, permitindo acesso interno VNet:

![Regras de segurança de rede padrão][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para começar a usar Ambientes de Serviço de Aplicativo, veja [Introdução ao ambiente de Serviço de Aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes sobre como controlar o tráfego de entrada para seu Ambiente do Serviço de Aplicativo, confira [Como controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
