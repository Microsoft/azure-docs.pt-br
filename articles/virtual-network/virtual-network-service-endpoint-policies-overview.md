---
title: Políticas de ponto de extremidade de serviço de rede virtual do Azure | Microsoft Docs
description: Aprenda a filtrar o tráfego de Rede Virtual para recursos de serviço do Azure usando políticas de Ponto de Extremidade de Serviço
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumi
ms.openlocfilehash: 86726eefb53638036a4e9207c648bf5ffe6c866e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595378"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Políticas de ponto de extremidade de serviço de rede virtual (versão prévia)

Políticas de ponto de extremidade de serviço de VNet (Rede Virtual) permitem filtrar o tráfego de rede virtual para serviços do Azure, permitindo apenas recursos de serviço do Azure específico, em pontos de extremidade de serviço. As políticas de ponto de extremidade fornecem controle de acesso granular para tráfego de rede virtual aos serviços do Azure.

Este recurso está disponível em __versão prévia__ para os seguintes serviços do Azure e regiões:

__Armazenamento do Azure__: WestCentralUS, WestUS2, NorthCentralUS, SouthCentralUS, CentralUS, EastUS2.

Para obter notificações mais recentes sobre a versão prévia, confira a página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Durante a versão prévia, as políticas de ponto de extremidade de serviço de rede virtual não podem ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão em versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Principais benefícios

Políticas de ponto de extremidade de serviço de rede virtual fornecem os seguintes benefícios:

- __Segurança aprimorada para o tráfego de Rede Virtual para os Serviços do Azure__

  [Marcas de serviço do Azure para grupos de segurança de rede](https://aka.ms/servicetags) permitem que você restrinja o tráfego de saída de rede virtual para serviços específicos do Azure. No entanto, isso permite o tráfego para todos os recursos daquele serviço do Azure. 
  
  Com as políticas de ponto de extremidade, agora você pode restringir o acesso de saída de rede virtual a apenas os recursos específicos do Azure. Isso oferece um controle de segurança muito mais granular para proteger os dados acessados em sua rede virtual. 

- __Políticas escalonáveis altamente disponíveis para filtrar o tráfego de serviço do Azure__

   Políticas de ponto de extremidade fornecem uma solução altamente disponível e escalonável horizontalmente para filtrar o tráfego de serviço do Azure de redes virtuais em pontos de extremidade de serviço. Nenhuma sobrecarga adicional é necessária para manter os dispositivos de rede central para esse tráfego em suas redes virtuais.

## <a name="configuration"></a>Configuração

- Você pode configurar as políticas de ponto de extremidade para restringir o tráfego de rede virtual aos recursos específicos de serviço do Azure. Para a versão prévia, oferecemos suporte a políticas de ponto de extremidade para o Armazenamento do Azure. 
- A política de ponto de extremidade é configurada em uma sub-rede em uma rede virtual. Pontos de extremidade de serviço devem ser habilitados na sub-rede para aplicar a política para todos os serviços do Azure listados na política.
- A política de ponto de extremidade permite que você coloque na lista de permissões recursos específicos de serviço do Azure usando o formato resourceID. Você pode restringir o acesso a todos os recursos em um grupo de recursos ou em uma assinatura. Você também pode restringir o acesso a recursos específicos. 
- Por padrão, se nenhuma política tiver sido anexada a uma sub-rede com pontos de extremidade, você poderá acessar todos os recursos no serviço. Depois que uma política é configurada na sub-rede, somente os recursos especificados na política poderão ser acessados de instâncias de computação naquela sub-rede. O acesso a todos os outros recursos para o serviço específico será negado. 
- Você pode filtrar o tráfego para os recursos de serviço do Azure nas regiões em que o ponto de extremidade de serviço está configurado. O acesso aos recursos de serviço em outras regiões será permitido por padrão. 

  > [!NOTE]  
  > Para bloquear completamente o acesso de saída de rede virtual para recursos do Azure em regiões de ponto de extremidade de serviço, você também precisa de regras de grupo de segurança de rede configuradas para permitir o tráfego somente para as regiões de ponto de extremidade de serviço usando [marcas de serviço](security-overview.md#service-tags).

- Você pode aplicar várias políticas a uma sub-rede. Quando várias políticas estão associadas à sub-rede, para o mesmo serviço, o tráfego de rede virtual para recursos especificados em qualquer uma dessas políticas será permitido. O acesso a todos os outros recursos de serviço não especificado em nenhuma das políticas será negado. 

  > [!NOTE]  
  > A política só permite acesso aos recursos de serviço listado de uma rede virtual. Todos os outros tráfegos para o serviço serão negados automaticamente, quando você adicionar recursos específicos à política. Verifique se todas as dependências de recurso de serviço para seus aplicativos podem ser identificadas e listadas na política.

  > [!WARNING]  
  > Serviços do Azure implantados em sua rede virtual, como Azure HDInsight, acessam outros serviços do Azure, como Armazenamento do Azure, para requisitos de infraestrutura. Restringir a política de ponto de extremidade a recursos específicos pode interromper o acesso a esses recursos de infraestrutura para serviços implantados em sua rede virtual. Para serviços específicos, veja [Limitações](#limitations) durante a versão prévia, não há suporte para políticas de ponto de extremidade de serviço para nenhum serviço gerenciado do Azure implantado em sua rede virtual.

- Para o Armazenamento do Azure: 
  -  Você pode restringir o acesso listando a *resourceId* do Azure Resource Manager da conta de armazenamento. Isso abrange o tráfego para blobs, tabelas, filas, arquivos e armazenamento do Azure Data Lake Storage Gen2.

     Por exemplo, contas de Armazenamento do Azure poderiam ser listadas na definição de política de ponto de extremidade como mostrado abaixo:
    
     Para permitir conta de armazenamento específica:         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Para permitir todas as contas em um grupo de recursos e assinatura: `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Para permitir todas as contas em uma assinatura: `/subscriptions/subscriptionId`
    
- Somente contas de armazenamento usando o Modelo de Recurso do Azure podem ser especificadas na política de ponto de extremidade.  

  > [!NOTE]  
  > O acesso às contas de armazenamento clássico é bloqueado com as políticas de ponto de extremidade.

- O local primário para a conta listada deve ser nas regiões do par de área geográfica do ponto de extremidade de serviço para a sub-rede. 

  > [!NOTE]  
  > As políticas permitem que recursos de serviço de outras regiões sejam especificados. O acesso à rede virtual para os serviços do Azure só é filtrado para as regiões do par de área geográfica. Se os grupos de segurança de rede não estiverem restritos a regiões do par de área geográfica para o Armazenamento do Azure, a rede virtual poderá acessar todas as contas de armazenamento fora das regiões do par de área geográfica.

- Acesso de RA-GRS secundário será permitido automaticamente se a conta primária estiver listada. 
- Contas de armazenamento podem estar em uma assinatura ou Locatário do Azure Active Directory igual ou diferente daquele da rede virtual. 

## <a name="limitations"></a>Limitações

- Você só pode implantar políticas de ponto de extremidade de serviço em redes virtuais implantadas por meio do modelo de implantação do Azure Resource Manager.
- Redes virtuais devem estar na mesma região que a política de ponto de extremidade de serviço.
- Só será possível aplicar política de ponto de extremidade de serviço em uma sub-rede se os pontos de extremidade de serviço estiverem configurados para os serviços do Azure listados na política.
- Você não pode usar políticas de ponto de extremidade de serviço para o tráfego da sua rede local para serviços do Azure.
- Políticas de ponto de extremidade não devem ser aplicadas a sub-redes com serviços gerenciados do Azure com dependência de serviços do Azure para requisitos de infraestrutura. 

  > [!WARNING]  
  > Serviços do Azure implantados em sua rede virtual, como Azure HDInsight, acessam outros serviços do Azure, como Armazenamento do Azure, para requisitos de infraestrutura. Restringir a política de ponto de extremidade a recursos específicos pode interromper o acesso a esses recursos de infraestrutura para serviços do Azure implantados em sua rede virtual.
  
  - Alguns serviços do Azure podem ser implantados em sub-redes com outras instâncias de computação. Verifique se as políticas de ponto de extremidade não foram aplicadas à sub-rede caso os serviços gerenciados listados abaixo sejam implantados na sub-rede.
   
    - Azure HDInsight
    - Lote do Azure (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Gateway de Aplicativo do Azure (Azure Resource Manager)
    - Gateway de VPN do Azure (Azure Resource Manager)
    - Firewall do Azure

  - Alguns serviços do Azure são implantados em sub-redes dedicadas. Políticas de ponto de extremidade são bloqueadas em todos esses serviços, listados abaixo, durante a versão prévia. 

     - Ambiente de Serviço de Aplicativo do Azure
     - Cache Redis do Azure
     - Gerenciamento de API do Azure
     - Instância Gerenciada do Azure SQL
     - Azure Active Directory Domain Services
     - Gateway de Aplicativo do Azure (Clássico)
     - Gateway de VPN do Azure (Clássico)

- Armazenamento do Microsoft Azure: Não há suporte para contas de armazenamento clássicas em políticas de ponto de extremidade. As políticas negarão o acesso a todas as contas de armazenamento clássicas por padrão. Se seu aplicativo precisar acessar o Azure Resource Manager e contas de armazenamento clássico, políticas de ponto de extremidade não deverão ser usadas para esse tráfego. 

## <a name="nsgs-with-service-endpoint-policies"></a>NSGs com Políticas de Ponto de Extremidade de Serviço
- Por padrão, NSGs permitem tráfego na Internet de saída, incluindo o tráfego de rede virtual para serviços do Azure.
- Se você quiser negar todo o tráfego na Internet de saída e permitir somente o tráfego para recursos específicos de serviço do Azure: 

  Etapa 1: Configurar NSGs para permitir o tráfego de saída somente para serviços do Azure em regiões de ponto de extremidade usando *marcas de serviço do Azure*. Para saber mais, confira [marcas do serviço para NSGs](https://aka.ms/servicetags)
      
  Por exemplo, regras de grupo de segurança de rede que restringem o acesso a apenas regiões ponto de extremidade se parecem com o exemplo a seguir:

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Etapa 2: Aplicar a política de ponto de extremidade de serviço com acesso apenas a recursos específicos do serviço do Azure.

  > [!WARNING]  
  > Se o grupo de segurança de rede não estiver configurado para limitar o acesso de serviço do Azure da rede virtual a regiões de ponto de extremidade, você poderá acessar recursos do serviço em outras regiões, mesmo que a política de ponto de extremidade de serviço esteja aplicada.

## <a name="scenarios"></a>Cenários

- **Redes virtuais emparelhadas, conectadas ou múltiplas**: Para filtrar o tráfego em redes virtuais emparelhadas, as políticas de ponto de extremidade devem ser aplicadas individualmente a elas.
- **Filtrando o tráfego da Internet com Dispositivos de Rede ou o Firewall do Azure**: Filtre o tráfego do serviço do Azure com políticas, em pontos de extremidade, e filtre o restante do tráfego da Internet ou do Azure por meio de dispositivos ou do Firewall do Azure. 
- **Filtrando o tráfego nos serviços do Azure implantados em Redes Virtuais**: Durante a versão prévia, não há suporte para políticas de ponto de extremidade de serviço em nenhum serviço gerenciado do Azure implantado na rede virtual. 
 Para serviços específicos, veja as [limitações.](#limitations)
- **Filtrando o tráfego para serviços do Azure do local**: As políticas de ponto de extremidade de serviço se aplicam somente ao tráfego de sub-redes associadas às políticas. Para permitir o acesso a recursos de Serviço do Azure específicos do local, o tráfego deve ser filtrado usando firewalls ou dispositivos virtuais de rede ou.

## <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas
Nenhum registro em log centralizado está disponível para políticas de ponto de extremidade de serviço. Para logs de diagnóstico de serviço, veja [Registro em log de pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Cenários de solução de problemas
- Acesso permitido a contas de armazenamento não listadas nas políticas de ponto de extremidade
  - Grupos de segurança de rede podem estar permitindo acesso às contas de Armazenamento do Azure ou da Internet em outras regiões.
  - Grupos de segurança de rede devem ser configurados para negar todo o tráfego na Internet de saída e permitir somente o tráfego para regiões específicas do Armazenamento do Azure. Para obter detalhes, confira Grupos de Segurança de Rede.
- O acesso é negado para contas listadas nas políticas de ponto de extremidade
  - Grupos de segurança de rede ou a filtragem de firewall podem estar bloqueando o acesso
  - Se remover/reaplicar a política resultar em perda de conectividade:
    - Valide se o serviço do Azure está configurado para permitir o acesso da rede virtual, por pontos de extremidade, ou se a política padrão para o recurso está definida como *Permitir Tudo*.
      > [!NOTE]      
      > Recursos de serviço não precisam ser protegidos para as redes virtuais obterem acesso por políticas de ponto de extremidade. No entanto, como uma melhor prática de segurança, é recomendável que os recursos de serviço sejam protegidos para redes confiáveis, como redes virtuais do Azure, por meio de pontos de extremidade de serviço e, localmente, por meio de um firewall de IP.
  
    - Valide que o diagnóstico de serviço mostra o tráfego nos pontos de extremidade.
    - Verifique se os logs de fluxo do grupo de segurança de rede mostram o acesso e se os logs de armazenamento mostram o acesso, conforme esperado, pelos pontos de extremidade de serviço.
    - Contate o suporte do Azure.
- O acesso é negado para contas não listadas nas políticas de ponto de extremidade de serviço
  - Grupos de segurança de rede ou a filtragem de firewall podem estar bloqueando o acesso. Verifique se a marca de serviço *Armazenamento do Azure* é permitida para as regiões de ponto de extremidade. Para restrições de política, veja as [limitações](#limitations).
  Por exemplo, contas de armazenamento clássicas têm acesso negado se uma política é aplicada.
  - Valide se o serviço do Azure está configurado para permitir o acesso da rede virtual, por pontos de extremidade, ou se a política padrão para o recurso está definida como *Permitir Tudo*.

## <a name="provisioning"></a>Provisionamento

Políticas de ponto de extremidade de serviço podem ser configuradas em sub-redes por um usuário com acesso de gravação a uma rede virtual. Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Redes virtuais e recursos de serviço do Azure podem estar em assinaturas ou locatários do Azure Active Directory iguais ou diferentes. 

## <a name="pricing-and-limits"></a>Preços e limites

Não há custo adicional para usar políticas de ponto de extremidade de serviço. O modelo de preço atual para serviços do Azure (como o Armazenamento do Azure) se aplica é hoje, em pontos de extremidade de serviço.

Os limites a seguir são impostos em políticas de ponto de extremidade de serviço: 

 |Recurso | Limite padrão |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar políticas de ponto de extremidade de serviço de rede virtual](virtual-network-service-endpoint-policies-portal.md)
- Saiba mais sobre [Pontos de Extremidade de Serviço de rede virtual](virtual-network-service-endpoints-overview.md)

