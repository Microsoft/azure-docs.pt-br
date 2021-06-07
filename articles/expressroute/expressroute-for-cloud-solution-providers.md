---
title: ExpressRoute dos Provedores de Solução na Nuvem - Azure | Microsoft Docs
description: Este artigo fornece informações para Provedores de Soluções na Nuvem que desejam incorporar serviços do Azure e o ExpressRoute a suas ofertas.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 09fee610ccc15874481ecfd4693e4b89379caa7a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330029"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute para Provedores de Soluções na Nuvem (CSP)
A Microsoft fornece serviços de hiperescala para revendedores e distribuidores tradicionais (CSP) poderem provisionar rapidamente novos serviços e soluções para seus clientes sem a necessidade de investir no desenvolvimento desses novos serviços. Para permitir que o Provedor de Soluções na Nuvem (CSP) tenha a capacidade de gerenciar esses novos serviços diretamente, a Microsoft oferece programas e APIs que permitem que o CSP gerencie recursos do Microsoft Azure em nome de seus clientes. Um desses recursos é o ExpressRoute. o ExpressRoute permite que o CSP conecte os recursos do cliente aos serviços do Azure. O ExpressRoute é um link de comunicação privada de alta velocidade para serviços no Azure. 

O ExpressRoute é composto por um par de circuitos para alta disponibilidade que são anexados a assinaturas de um único cliente e não podem ser compartilhados por vários clientes. Cada circuito deve ser encerrado em um roteador diferente para manter a alta disponibilidade.

> [!NOTE]
> Há limites para a largura de banda e o número de conexões possíveis em cada circuito do ExpressRoute. Se as necessidades de um único cliente excederem esses limites, elas precisarão de vários circuitos do ExpressRoute para a implementação da rede híbrida.
> 
> 

O Microsoft Azure fornece um número crescente de serviços que você pode oferecer aos seus clientes. O ExpressRoute ajuda você e seus clientes a aproveitar esses serviços, fornecendo acesso de baixa latência de alta velocidade ao ambiente de Microsoft Azure.

## <a name="microsoft-azure-management"></a>Gerenciamento do Microsoft Azure
A Microsoft fornece CSPs com APIs para gerenciar as assinaturas de clientes do Azure, permitindo a integração programática com seus próprios sistemas de gerenciamento de serviços. Os recursos de gerenciamento com suporte podem ser encontrados [aqui](/previous-versions/windows/mt844538(v=win.10)).

## <a name="microsoft-azure-resource-management"></a>Gerenciamento de recursos do Microsoft Azure
O contrato que você tem com o cliente determinará como a assinatura será gerenciada. O CSP pode gerenciar diretamente a criação e a manutenção de recursos ou o cliente pode manter o controle da assinatura do Microsoft Azure e criar recursos necessário do Azure. Se o cliente gerenciar a criação de recursos em sua assinatura Microsoft Azure, ele usará um dos dois modelos: "*conectar-se*" ou modelo "*direto para*". Esses modelos serão descritos em detalhes nas seções a seguir.  

### <a name="connect-through-model"></a>Modelo Connect-Through
![Diagrama que mostra o modelo "Connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

No modelo Connect-Through, o CSP cria uma conexão direta entre seu datacenter e a assinatura do Azure do seu cliente. A conexão direta é feita usando o ExpressRoute, conectando a sua rede ao Azure. Em seguida, seu cliente se conecta à sua rede. Esse cenário requer que o cliente passe pela rede CSP para acessar os serviços do Azure. 

Se o seu cliente tiver outras assinaturas do Azure não gerenciadas por você, elas usarão a Internet pública ou sua própria conexão privada para se conectar a esses serviços provisionados na assinatura não CSP. 

Para o CSP gerenciar os serviços do Azure, presume-se que o CSP tenha um armazenamento de identidade do cliente estabelecido anteriormente, que seria então replicado em Azure Active Directory para o gerenciamento de sua assinatura do CSP por meio de AOBO (administração em nome de). Os principais drivers para esse cenário incluem onde um determinado parceiro ou provedor de serviços tem uma relação estabelecida com o cliente, o cliente está consumindo serviços de provedor no momento ou o parceiro tem a intenção de fornecer uma combinação de soluções hospedadas pelo provedor e pelo Azure para fornecer flexibilidade e solucionar os desafios dos clientes que não podem ser satisfeitos apenas pelo CSP. Esse modelo é ilustrado na **Figura** abaixo.

![Diagrama que mostra um cenário detalhado para o modelo "Connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Modelo Connect-To
![Diagrama que mostra o modelo "Connect-to".](./media/expressroute-for-cloud-solution-providers/connect-to.png)

No modelo Connect-To, o provedor de serviço cria uma conexão direta entre o datacenter do cliente e a assinatura do Azure provisionada pelo CSP usando o ExpressRoute na rede do cliente.

> [!NOTE]
> Para o ExpressRoute, o cliente precisaria criar e manter o circuito do ExpressRoute.  
> 
> 

Esse cenário de conectividade requer que o cliente se conecte diretamente por meio de uma rede de cliente para acessar a assinatura do Azure gerenciada pelo CSP, usando uma conexão de rede direta que é criada, de propriedade e gerenciada totalmente ou em parte pelo cliente. Para esses clientes, supõe-se que o provedor atualmente não tem um armazenamento de identidade do cliente estabelecido, e o provedor auxiliaria o cliente a replicar seu armazenamento de identificação atual em Azure Active Directory para o gerenciamento de sua assinatura por meio do AOBO. Os fatores determinantes para este cenário incluem o local onde um determinado parceiro ou provedor de serviços tem um relacionamento estabelecido com o cliente, se o cliente está consumindo serviços de provedor no momento ou se o parceiro tem um desejo fornecer serviços baseados somente em soluções hospedadas no Azure sem a necessidade de um datacenter ou de infraestrutura do provedor existente.

![Diagrama que mostra um cenário detalhado para o modelo "Connect-to".](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

As opções entre essas duas opções se baseiam nas necessidades do cliente e na necessidade atual de fornecer serviços do Azure. Os detalhes desses modelos e dos padrões de design do controle de acesso baseado em função, de rede e de identidade serão abordados em detalhes nos links a seguir:

* **Controle de acesso baseado em função do Azure (RBAC do Azure)** – o RBAC do Azure é baseado em Azure Active Directory.  Para obter mais informações sobre o RBAC do Azure, consulte [aqui](../role-based-access-control/role-assignments-portal.md).
* **Rede** – aborda vários tópicos da rede no Microsoft Azure.
* **Azure Active Directory (AD do Azure)** – o AD do Azure fornece gerenciamento de identidade para Microsoft Azure e aplicativos SaaS de terceiros. Para obter mais informações sobre o AD do Azure, consulte [aqui](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Velocidades de rede
O ExpressRoute dá suporte a velocidades de rede de 50 MB/s a 10 GB/s. Isso permite que os clientes comprem a quantidade de largura de banda de rede necessária para seu ambiente exclusivo.

> [!NOTE]
> A largura de banda de rede pode ser aumentada, conforme necessário, sem a interrupção das comunicações, mas para reduzir a velocidade da rede, é necessária a divisão do circuito e sua recriação na velocidade de rede menor.  
> 
> 

O ExpressRoute oferece suporte à conexão de várias Redes Virtuais a um único circuito de ExpressRoute para a melhor utilização das conexões de alta velocidade. Um único circuito de ExpressRoute pode ser compartilhado entre várias assinaturas do Azure pertencentes ao mesmo cliente.

## <a name="configuring-expressroute"></a>Configuração do ExpressRoute
O ExpressRoute pode ser configurado para oferecer suporte a três tipos de tráfego ([domínios de roteamento](#expressroute-routing-domains)) em um único circuito de ExpressRoute. Esse tráfego é segregado em emparelhamento privado, emparelhamento da Microsoft e emparelhamento público (preterido). Você pode escolher o envio de um ou de todos os tipos de tráfego em um único circuito de ExpressRoute ou usar vários circuitos de ExpressRoute, dependendo do tamanho do circuito de ExpressRoute e do isolamento exigidos pelo cliente. A postura de segurança do cliente pode não permitir o tráfego público e o tráfego privado no mesmo circuito.

### <a name="connect-through-model"></a>Modelo Connect-Through
Em uma configuração de conexão, você será responsável por todas as bases de rede para conectar os recursos do datacenter do cliente às assinaturas hospedadas no Azure. Cada um de seus clientes que desejam usar os recursos do Azure precisará de sua própria conexão de ExpressRoute, que será gerenciada por você. Você usará os mesmos métodos que o cliente usaria para adquirir o circuito do ExpressRoute. Você seguirá as mesmas etapas descritas no artigo fluxos de [trabalho do ExpressRoute](expressroute-workflows.md) para provisionamento de circuito e Estados de circuito. Em seguida, você irá configurar as rotas de Border Gateway Protocol (BGP) para controlar o fluxo de tráfego entre a rede local e a vNet do Azure.

### <a name="connect-to-model"></a>Modelo Connect-To
Em uma configuração de conexão, seu cliente já tem uma conexão existente com o Azure ou iniciará uma conexão com o provedor de serviços de Internet vinculando o ExpressRoute de seu próprio datacenter diretamente ao Azure, em vez de seu datacenter. Para iniciar o processo de provisionamento, o cliente seguirá as etapas como descrito no modelo Connect-Through, acima. Depois que o circuito tiver sido estabelecido, o cliente precisará configurar os roteadores locais para poder acessar a rede e o Azure vNets.

Você pode auxiliar na configuração da conexão e na configuração das rotas para permitir que os recursos do seu datacenter também se comuniquem com os recursos do cliente em seu datacenter, ou com os recursos hospedados no Azure.

## <a name="expressroute-routing-domains"></a>Domínios de roteamento do ExpressRoute
O ExpressRoute oferece dois domínios de roteamento para novos circuitos: emparelhamento privado e emparelhamento da Microsoft. Cada um dos domínios de roteamento é configurado com roteadores idênticos na configuração ativa-ativa para alta disponibilidade. Para obter mais detalhes sobre os domínios de roteamento do ExpressRoute, entre [aqui](expressroute-circuit-peerings.md).

Você pode definir filtros de rotas personalizados para permitir apenas as rotas necessárias. Para saber mais ou para ver como fazer essas alterações, confira o artigo: [Criar e modificar o roteamento de um circuito de ExpressRoute usando o PowerShell](expressroute-howto-routing-classic.md) para obter mais detalhes sobre os filtros de roteamento.

> [!NOTE]
> Para o emparelhamento da Microsoft, a conectividade deve ser apesar de um endereço IP público de Propriedade do cliente ou CSP e deve aderir a todas as regras definidas. Para obter mais informações, consulte a página [pré-requisitos do ExpressRoute](expressroute-prerequisites.md) .  
> 
> 

## <a name="routing"></a>Roteamento
o ExpressRoute se conecta às redes do Azure por meio do Gateway de Rede Virtual do Azure. Os gateways de rede fornecem roteamento para as redes virtuais do Azure.

A criação de redes virtuais do Azure também cria uma tabela de roteamento padrão para a Rede Virtual direcionar o tráfego de/para sub-redes da Rede Virtual. Se a tabela de rotas padrão for insuficiente para a solução, as rotas personalizadas poderão ser criadas para rotear o tráfego de saída para dispositivos personalizados ou para bloquear rotas para sub-redes específicas ou redes externas.

### <a name="default-routing"></a>Roteamento padrão
A tabela de rotas padrão inclui as seguintes rotas:

* Roteamento em uma sub-rede
* Sub-rede para sub-rede na rede virtual
* Para a Internet
* Rede virtual para rede virtual usando o gateway de VPN
* Rede virtual para rede local usando um gateway de VPN ou de ExpressRoute

![Diagrama que mostra as opções de roteamento padrão.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Roteamento definido pelo usuário (UDR)
As rotas definidas pelo usuário permitem o controle do tráfego de saída da sub-rede atribuída para outras sub-redes na rede virtual ou em um dos outros gateways predefinidos (ExpressRoute, Internet ou VPN). A tabela padrão de roteamento do sistema pode ser substituída por uma tabela de roteamento definida pelo usuário que substitua a tabela de roteamento padrão com rotas personalizadas. Com o roteamento definido pelo usuário, os clientes podem criar rotas específicas para dispositivos como firewalls ou dispositivos de detecção de intrusão, além de bloquear o acesso a sub-redes específicas da sub-rede que hospeda a rota definida pelo usuário. Para obter uma visão geral de User-Defined rotas, veja [aqui](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Segurança
Dependendo do modelo em uso, Connect-To ou Connect-Through, seu cliente definirá as políticas de segurança na Rede Virtual ou fornecer os requisitos da política de segurança ao CSP para que ele defina nas Redes Virtuais. Os critérios de segurança a seguir podem ser definidos:

1. **Isolamento do cliente** — a plataforma do Azure fornece isolamento de cliente ao armazenar a ID do Cliente e as informações da Rede Virtual em um banco de dados seguro, que é usado para encapsular o tráfego de cada cliente em um túnel GRE.
2. **NSG (Grupo de Segurança de Rede)** definem o tráfego de entrada e de saída permitido para as sub-redes em Redes Virtuais no Azure. Por padrão, o NSG contém regras de bloco para bloquear o tráfego da Internet para a vNet e permitir regras de tráfego em uma vNet. Para obter mais informações sobre grupos de segurança de rede, veja [aqui](https://azure.microsoft.com/blog/network-security-groups/).
3. **Criação de túneis à força** — essa é uma opção para redirecionar o tráfego limitado à Internet originado no Azure na conexão de ExpressRoute para o datacenter local. Para saber mais sobre a criação de túneis à força, entre [aqui](expressroute-routing.md#advertising-default-routes).  
4. **Criptografia** — mesmo se os circuitos de ExpressRoute estiverem dedicados a um cliente específico, há a possibilidade de que o provedor de rede possa ser violado, permitindo que um invasor examine o tráfego de pacotes. Para reduzir essa possibilidade, um cliente ou um CSP pode criptografar o tráfego pela conexão ao definir políticas de modo de túnel IPSec para todo o tráfego que fluir entre os recursos locais e os recursos do Azure (consulte o modo de túnel IPSec opcional para o Cliente 1 na Figura 5: Segurança do ExpressRoute, acima). A segunda opção seria usar um dispositivo de firewall em cada ponto de extremidade do circuito do ExpressRoute. Isso exigirá que VMs/dispositivos de firewall de terceiros adicionais sejam instalados em ambas as extremidades para criptografar o tráfego pelo circuito do ExpressRoute.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Próximas etapas
O serviço Provedor de Soluções de Nuvem oferece uma maneira de aumentar seu valor para seus clientes sem a necessidade de comprar recursos e uma infraestrutura caros, mantendo sua posição como o provedor terceirizado principal. A integração direta com o Microsoft Azure pode ser feita por meio da API do CSP, permitindo que você integre o gerenciamento do Microsoft Azure às suas estruturas de gerenciamento existentes.  

Encontre mais informações nestes links:

[Azure no programa Cloud Solution Provider](/azure/cloud-solution-provider).  
[Prepare-se para a transação como um Provedor de Soluções da Nuvem](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch).  
[Recursos do Provedor de Soluções da Nuvem da Microsoft](https://partner.microsoft.com/solutions/cloud-reseller-resources).