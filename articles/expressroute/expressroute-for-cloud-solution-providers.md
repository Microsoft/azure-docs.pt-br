---
title: ExpressRoute dos Provedores de Solução na Nuvem - Azure | Microsoft Docs
description: Este artigo fornece informações para Provedores de Soluções na Nuvem que desejam incorporar serviços do Azure e o ExpressRoute a suas ofertas.
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: ricarlse
ms.custom: seodec18
ms.openlocfilehash: a3bd48f32dfcee1a666ff842cfcab2384a5459ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849256"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute para Provedores de Soluções na Nuvem (CSP)
A Microsoft fornece serviços de hiperescala para revendedores e distribuidores tradicionais (CSP) poderem provisionar rapidamente novos serviços e soluções para seus clientes sem a necessidade de investir no desenvolvimento desses novos serviços. Para permitir que o Provedor de Soluções na Nuvem (CSP) tenha a capacidade de gerenciar esses novos serviços diretamente, a Microsoft oferece programas e APIs que permitem que o CSP gerencie recursos do Microsoft Azure em nome de seus clientes. Um desses recursos é o ExpressRoute. o ExpressRoute permite que o CSP conecte os recursos do cliente aos serviços do Azure. o ExpressRoute é um link de comunicações privado de alta velocidade para serviços do Azure. 

O ExpressRoute é composto de um par de circuitos para alta disponibilidade conectados a uma única assinatura de cliente e não pode ser compartilhada por vários clientes. Cada circuito deve ser encerrado em um roteador diferente para manter a alta disponibilidade.

> [!NOTE]
> Existem limites de largura de banda e de conexão no ExpressRoute, o que significa que as implementações grandes/complexas exigirão vários circuitos de ExpressRoute para um único cliente.
> 
> 

O Microsoft Azure fornece um número crescente de serviços que você pode oferecer aos seus clientes. O ExpressRoute ajuda você e seus clientes a tirar proveito desses serviços, fornecendo acesso de baixa latência de alta velocidade ao ambiente do Microsoft Azure.

## <a name="microsoft-azure-management"></a>Gerenciamento do Microsoft Azure
A Microsoft fornece CSPs com APIs para gerenciar as assinaturas de cliente do Azure, permitindo a integração programática com seus próprios sistemas de gerenciamento de serviço. Os recursos de gerenciamento com suporte podem ser encontrados [aqui](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Gerenciamento de recursos do Microsoft Azure
O contrato que você tem com o cliente determinará como a assinatura será gerenciada. O CSP pode gerenciar diretamente a criação e a manutenção de recursos ou o cliente pode manter o controle da assinatura do Microsoft Azure e criar recursos necessário do Azure. Se o cliente gerenciar a criação de recursos na sua assinatura do Microsoft Azure, ele usará um destes dois modelos: modelo "*Connect-Through*" ou modelo "*Direct-To*". Esses modelos serão descritos em detalhes nas seções a seguir.  

### <a name="connect-through-model"></a>Modelo Connect-Through
![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

No modelo Connect-Through, o CSP cria uma conexão direta entre seu datacenter e a assinatura do Azure do seu cliente. A conexão direta é feita usando o ExpressRoute, conectando a sua rede ao Azure. Em seguida, seu cliente se conecta à sua rede. Esse cenário requer que o cliente passe pela rede CSP para acessar os serviços do Azure. 

Se seu cliente tiver outras assinaturas do Azure não gerenciadas por você, deverá a conexão pública com a Internet ou sua própria conexão privada para se conectar aos serviços provisionadas sob a assinatura não CSP. 

Para que o CSP gerencie serviços do Azure, supõe-se que o CSP tenha um armazenamento de identidades do cliente previamente estabelecido que, então, seria replicado para o Azure Active Directory para o gerenciamento de suas assinaturas do CSP por meio de AOBO (Administrar em Nome de). Os fatores determinantes para este cenário incluem o local onde um determinado parceiro ou provedor de serviços tem um relacionamento estabelecido com o cliente, se o cliente está consumindo serviços de provedor no momento ou se o parceiro tem um desejo fornecer uma combinação de soluções hospedadas no provedor e hospedadas no Azure para fornecer flexibilidade e para enfrentar os desafios do cliente que não podem ser atendidos somente pelo CSP. Esse modelo é ilustrado na **Figura**abaixo.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Modelo Connect-To
![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-to.png)

No modelo Connect-To, o provedor de serviço cria uma conexão direta entre o datacenter do cliente e a assinatura do Azure provisionada pelo CSP usando o ExpressRoute na rede do cliente.

> [!NOTE]
> Para o ExpressRoute, o cliente precisaria criar e manter o circuito do ExpressRoute.  
> 
> 

Esse cenário de conectividade requer que o cliente se conecte diretamente por meio de uma rede de cliente para acessar a assinatura do Azure gerenciada pelo CSP usando uma conexão de rede direta criada, pertencente e gerenciada no todo ou em parte pelo cliente. Presume-se que atualmente o provedor não tenha um armazenamento de identidades de cliente estabelecida para esses clientes, e que o provedor auxiliaria o cliente na replicação do armazenamento de identidades atual para o Azure Active Directory para o gerenciamento da assinatura por meio de AOBO. Os fatores determinantes para este cenário incluem o local onde um determinado parceiro ou provedor de serviços tem um relacionamento estabelecido com o cliente, se o cliente está consumindo serviços de provedor no momento ou se o parceiro tem um desejo fornecer serviços baseados somente em soluções hospedadas no Azure sem a necessidade de um datacenter ou de infraestrutura do provedor existente.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

A escolha entre essas duas opções se baseia nas necessidades do cliente e em sua necessidade atual para fornecer os serviços do Azure. Os detalhes desses modelos e dos padrões de design do controle de acesso baseado em função, de rede e de identidade serão abordados em detalhes nos links a seguir:

* **RBAC (Controle de Acesso Baseado em Função)** – o RBAC baseia-se no Azure Active Directory.  Para saber mais sobre o RBAC do Azure, entre [aqui](../role-based-access-control/role-assignments-portal.md).
* **Rede** – aborda vários tópicos da rede no Microsoft Azure.
* **Azure AD (Azure Active Directory)** – o Azure AD fornece gerenciamento de identidades para o Microsoft Azure e para aplicativos SaaS de terceiros. Para saber mais sobre o AD do Azure, entre [aqui](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Velocidades de rede
o ExpressRoute dá suporte a velocidades de rede de 50 Mb/s a 10Gb/s. Isso permite que os clientes comprem a quantidade de largura de banda de rede necessária para seu ambiente exclusivo.

> [!NOTE]
> A largura de banda de rede pode ser aumentada, conforme necessário, sem a interrupção das comunicações, mas para reduzir a velocidade da rede, é necessária a divisão do circuito e sua recriação na velocidade de rede menor.  
> 
> 

O ExpressRoute oferece suporte à conexão de várias Redes Virtuais a um único circuito de ExpressRoute para a melhor utilização das conexões de alta velocidade. Um único circuito de ExpressRoute pode ser compartilhado entre várias assinaturas do Azure pertencentes ao mesmo cliente.

## <a name="configuring-expressroute"></a>Configuração do ExpressRoute
O ExpressRoute pode ser configurado para oferecer suporte a três tipos de tráfego ([domínios de roteamento](#expressroute-routing-domains)) em um único circuito de ExpressRoute. Esse tráfego é dividido em emparelhamento da Microsoft, emparelhamento público e emparelhamento privado do Azure. Você pode escolher o envio de um ou de todos os tipos de tráfego em um único circuito de ExpressRoute ou usar vários circuitos de ExpressRoute, dependendo do tamanho do circuito de ExpressRoute e do isolamento exigidos pelo cliente. A postura de segurança do cliente pode não permitir o tráfego público e o tráfego privado no mesmo circuito.

### <a name="connect-through-model"></a>Modelo Connect-Through
Em uma configuração Connect-Through, você será responsável por todas as bases de rede conectadas a seus recursos de datacenter de clientes para as assinaturas hospedadas no Azure. Cada um dos seus clientes que quiser usar recursos do Azure terá sua própria conexão de ExpressRoute, que será gerenciada por você. Você usará os mesmos métodos que o cliente usaria para adquirir o circuito de ExpressRoute. Em seguida, você seguirá as mesmas etapas descritas no artigo [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md) para o provisionamento de circuitos e os estados de circuito. Em seguida, você irá configurar as rotas BGP (Border Gateway Protocol) para controlar o tráfego que flui entre a rede local e a Rede Virtual do Azure.

### <a name="connect-to-model"></a>Modelo Connect-To
Em uma configuração Connect-To, ou seu cliente já tem uma conexão existente com o Azure ou iniciará uma conexão com o provedor de serviços de Internet ao vincular o ExpressRoute do próprio datacenter do seu cliente diretamente ao Azure, em vez de ao seu datacenter. Para iniciar o processo de provisionamento, o cliente seguirá as etapas como descrito no modelo Connect-Through, acima. Depois que o circuito tiver sido estabelecido, seu cliente precisará configurar os roteadores locais para que eles possam acessar sua rede e as Redes Virtuais do Azure.

Você pode auxiliar na configuração da conexão e na configuração das rotas para permitir que os recursos do seu datacenter também se comuniquem com os recursos do cliente em seu datacenter, ou com os recursos hospedados no Azure.

## <a name="expressroute-routing-domains"></a>Domínios de roteamento do ExpressRoute
o ExpressRoute oferece três domínios de roteamento: público, privado e emparelhamento da Microsoft. Cada um dos domínios de roteamento é configurado com roteadores idênticos na configuração ativo-ativo para obtenção de alta disponibilidade. Para obter mais detalhes sobre os domínios de roteamento do ExpressRoute, entre [aqui](expressroute-circuit-peerings.md).

Você pode definir filtros de rotas personalizados para permitir apenas as rotas necessárias. Para obter mais informações ou para ver como fazer essas alterações consulte o artigo: [Criar e alterar o roteamento de um circuito de ExpressRoute usando o PowerShell](expressroute-howto-routing-classic.md) para obter mais detalhes sobre os filtros de roteamento.

> [!NOTE]
> Para o emparelhamento da Microsoft e público, a conectividade deve passar por um endereço IP público de propriedade do cliente ou do CSP e deve aderir a todas as regras definidas. Para saber mais, veja a página [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md) .  
> 
> 

## <a name="routing"></a>Roteamento
o ExpressRoute se conecta às redes do Azure por meio do Gateway de Rede Virtual do Azure. Os gateways de rede fornecem roteamento para as redes virtuais do Azure.

A criação de redes virtuais do Azure também cria uma tabela de roteamento padrão para a Rede Virtual direcionar o tráfego de/para sub-redes da Rede Virtual. Se a tabela de rotas padrão for insuficiente para a solução, você poderá criar rotas personalizadas para o tráfego de saída para dispositivos personalizados ou para bloquear rotas para sub-redes ou redes externas específicas.

### <a name="default-routing"></a>Roteamento padrão
A tabela de rotas padrão inclui as seguintes rotas:

* Roteamento em uma sub-rede
* Sub-rede para sub-rede na rede virtual
* Para a Internet
* Rede virtual para rede virtual usando o gateway de VPN
* Rede virtual para rede local usando um gateway de VPN ou de ExpressRoute

![texto alternativo](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Roteamento definido pelo usuário (UDR)
As rotas definidas pelo usuário permitem o controle do tráfego de saída da sub-rede atribuída para outras sub-redes na rede virtual ou em um dos outros gateways predefinidos (ExpressRoute, Internet ou VPN). A tabela padrão de roteamento do sistema pode ser substituída por uma tabela de roteamento definida pelo usuário que substitua a tabela de roteamento padrão com rotas personalizadas. Com o roteamento definido pelo usuário, os clientes podem criar rotas específicas para dispositivos como firewalls ou dispositivos de detecção de intrusão, além de bloquear o acesso a sub-redes específicas da sub-rede que hospeda a rota definida pelo usuário. Para obter uma visão geral das Rotas Definidas pelo Usuário, entre [aqui](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Segurança
Dependendo do modelo em uso, Connect-To ou Connect-Through, seu cliente definirá as políticas de segurança na Rede Virtual ou fornecer os requisitos da política de segurança ao CSP para que ele defina nas Redes Virtuais. Os critérios de segurança a seguir podem ser definidos:

1. **Isolamento do cliente** — a plataforma do Azure fornece isolamento de cliente ao armazenar a ID do Cliente e as informações da Rede Virtual em um banco de dados seguro, que é usado para encapsular o tráfego de cada cliente em um túnel GRE.
2. **NSG (Grupo de Segurança de Rede)** definem o tráfego de entrada e de saída permitido para as sub-redes em Redes Virtuais no Azure. Por padrão, o NSG contém regras de Bloqueio para bloquear o tráfego da Internet para a Rede Virtual e as regras de Permissão para o tráfego em uma Rede Virtual. Para saber mais sobre os Grupos de Segurança de Rede, entre [aqui](https://azure.microsoft.com/blog/network-security-groups/).
3. **Criação de túneis à força** — essa é uma opção para redirecionar o tráfego limitado à Internet originado no Azure na conexão de ExpressRoute para o datacenter local. Para saber mais sobre a criação de túneis à força, entre [aqui](expressroute-routing.md#advertising-default-routes).  
4. **Criptografia** — mesmo se os circuitos de ExpressRoute estiverem dedicados a um cliente específico, há a possibilidade de que o provedor de rede possa ser violado, permitindo que um invasor examine o tráfego de pacotes. Para reduzir essa possibilidade, um cliente ou um CSP pode criptografar o tráfego pela conexão ao definir políticas de modo de túnel IPSec para todo o tráfego que fluir entre os recursos locais e os recursos do Azure (consulte o modo de túnel IPSec opcional para o Cliente 1 na Figura 5: Segurança do ExpressRoute, acima). A segunda opção seria usar um dispositivo de firewall em cada ponto de extremidade do circuito do ExpressRoute. Isso exigirá a instalação de VMs/dispositivos de firewall de terceiros adicionais em ambas as extremidades para criptografar o tráfego no circuito de ExpressRoute.

![texto alternativo](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Próximas etapas
O serviço Provedor de Soluções de Nuvem oferece uma maneira de aumentar seu valor para seus clientes sem a necessidade de comprar recursos e uma infraestrutura caros, mantendo sua posição como o provedor terceirizado principal. A integração direta com o Microsoft Azure pode ser feita por meio da API do CSP, permitindo que você integre o gerenciamento do Microsoft Azure às suas estruturas de gerenciamento existentes.  

Encontre mais informações nestes links:

[Azure no programa Cloud Solution Provider](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Prepare-se para a transação como um Provedor de Soluções da Nuvem](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Recursos do Provedor de Soluções da Nuvem da Microsoft](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).
