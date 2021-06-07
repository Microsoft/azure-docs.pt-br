---
title: Cache do Azure para opções de isolamento de rede Redis
description: Neste artigo, você aprenderá a determinar a melhor solução de isolamento de rede para suas necessidades. Veremos os conceitos básicos do link privado do Azure, a injeção da VNet (rede virtual do Azure) e as regras de firewall do Azure com suas vantagens e limitações.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621511"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Cache do Azure para opções de isolamento de rede Redis 
Neste artigo, você aprenderá a determinar a melhor solução de isolamento de rede para suas necessidades. Veremos os conceitos básicos do link privado do Azure, a injeção da VNet (rede virtual do Azure) e as regras de firewall do Azure com suas vantagens e limitações.  

## <a name="azure-private-link-public-preview"></a>Link privado do Azure (visualização pública) 
O link privado do Azure fornece conectividade privada de uma rede virtual para os serviços de PaaS do Azure. Ele simplifica a arquitetura de rede e protege a conexão entre os pontos de extremidade no Azure, eliminando a exposição de dados para a Internet pública. 

### <a name="advantages"></a>Vantagens
* Com suporte em cache básico, Standard e Premium do Azure para instâncias de Redis. 
* Usando o [link privado do Azure](../private-link/private-link-overview.md), você pode se conectar a uma instância de cache do Azure de sua rede virtual por meio de um ponto de extremidade privado, que é atribuído a um endereço IP privado em uma sub-rede dentro da rede virtual. Com isso, as instâncias de cache estão disponíveis tanto na VNet quanto publicamente.  
* Depois que um ponto de extremidade privado é criado, o acesso à rede pública pode ser restringido por meio do `publicNetworkAccess` sinalizador. Esse sinalizador é definido como `Disabled` por padrão, o que permitirá somente o acesso de link privado. Você pode definir o valor para `Enabled` ou `Disabled` com uma solicitação de patch. Para obter mais informações, consulte [cache do Azure para Redis com o link privado do Azure (versão prévia)](cache-private-link.md). 
* Todas as dependências de cache externo não afetarão as regras de NSG da VNet.

### <a name="limitations"></a>Limitações 
* Os NSG (grupos de segurança de rede) estão desabilitados para pontos de extremidade privados. No entanto, se houver outros recursos na sub-rede, a imposição de NSG será aplicada a esses recursos.
* Replicação geográfica, regras de firewall, suporte do console do portal, vários pontos de extremidade por cache clusterizado, persistência para firewall e caches injetados de VNet ainda não têm suporte. 
* Para se conectar a um cache clusterizado, é `publicNetworkAccess` necessário definir como `Disabled` e só pode haver uma conexão de ponto de extremidade privada.

> [!NOTE]
> Ao adicionar um ponto de extremidade privado a uma instância de cache, todo o tráfego Redis será movido para o ponto de extremidade privado por causa do DNS.
> Verifique se as regras de firewall anteriores estão ajustadas antes.  
>
>

## <a name="azure-virtual-network-injection"></a>Injeção de rede virtual do Azure 
VNet é o bloco de construção fundamental para sua rede privada no Azure. A VNet permite que muitos recursos do Azure se comuniquem com segurança entre si, Internet e redes locais. A VNet é como uma rede tradicional que você operaria em sua própria data center, mas com os benefícios da infraestrutura, escala, disponibilidade e isolamento do Azure. 

### <a name="advantages"></a>Vantagens
* Quando um cache do Azure para instância Redis é configurado com uma VNet, ele não é publicamente endereçável e só pode ser acessado de máquinas virtuais e aplicativos na VNet.  
* Quando a VNet é combinada com políticas NSG restritas, ela ajuda a reduzir o risco de vazamento de dados. 
* A implantação de VNet fornece segurança e isolamento aprimorados para o cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. 
* Há suporte para replicação geográfica. 

### <a name="limitations"></a>Limitações
* Os caches injetados VNet só estão disponíveis para o cache Premium do Azure para Redis. 
* Ao usar um cache injetado VNet, você precisará abrir sua VNet para armazenar em cache as dependências como CRLs/PKI, AKV, armazenamento do Azure, Azure Monitor e muito mais.  


## <a name="azure-firewall-rules"></a>Regras de firewall do Azure
O [Firewall do Azure](../firewall/overview.md) é um serviço de segurança de rede gerenciado baseado em nuvem que protege seus recursos de VNet do Azure. Trata-se de um firewall totalmente com estado como um serviço com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.  

### <a name="advantages"></a>Vantagens
* Quando regras de firewall são configuradas, apenas as conexões de cliente de intervalos de endereços IP especificados podem se conectar ao cache. Conexões dos sistemas de monitoramento do Cache do Azure para Redis serão sempre permitidas, mesmo se regras de firewall forem configuradas. As regras de NSG que você define também são permitidas.  

### <a name="limitations"></a>Limitações
* As regras de firewall podem ser usadas em conjunto com caches injetados VNet, mas não pontos de extremidade privados no momento. 


## <a name="next-steps"></a>Próximas etapas
* Saiba como configurar um [cache injetado de VNet para um cache do Azure Premium para instância Redis](cache-how-to-premium-vnet.md).  
* Saiba como configurar [regras de firewall para todo o cache do Azure para camadas Redis](cache-configure.md#firewall). 
* Saiba como [Configurar pontos de extremidade privados para todo o cache do Azure para camadas Redis](cache-private-link.md).