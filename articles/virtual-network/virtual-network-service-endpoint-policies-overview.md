---
title: Políticas de ponto de extremidade de serviço de rede virtual do Azure | Microsoft Docs
description: Aprenda a filtrar o tráfego de Rede Virtual para recursos de serviço do Azure usando políticas de Ponto de Extremidade de Serviço
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 633d0b9e454fe2906d98624ac2cdbcc9a8b6c7fa
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022255"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Políticas de ponto de extremidade de serviço de rede virtual para o Armazenamento do Azure

As políticas de ponto de extremidade de serviço de rede virtual (VNet) permitem filtrar o tráfego de rede virtual de egresso para contas de armazenamento do Azure sobre o ponto de extremidade de serviço e permitir que os dados vazamentom apenas contas específicas de armazenamento do Azure As políticas de ponto de extremidade fornecem controle de acesso granular para o tráfego de rede virtual para o armazenamento do Azure ao se conectar ao ponto de extremidade

![Protegendo o tráfego de saída da rede virtual para contas de armazenamento do Azure](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Esse recurso está geralmente disponível para o __armazenamento do Azure__ em __todas as regiões globais do Azure__.

## <a name="key-benefits"></a>Principais benefícios

Políticas de ponto de extremidade de serviço de rede virtual fornecem os seguintes benefícios:

- __Segurança aprimorada para o tráfego de rede virtual para o armazenamento do Azure__

  As [marcas de serviço do Azure para grupos de segurança de rede](./network-security-groups-overview.md) permitem restringir o tráfego de saída da rede virtual para regiões específicas do armazenamento do Azure. No entanto, isso permite o tráfego para qualquer conta na região de armazenamento do Azure selecionada.
  
  As políticas de ponto de extremidade permitem que você especifique as contas de armazenamento do Azure que têm permissão de acesso de saída de rede virtual e restringe o acesso a todas as outras contas de armazenamento. Isso fornece um controle de segurança muito mais granular para proteger os dados vazamento de sua rede virtual.

- __Políticas escalonáveis altamente disponíveis para filtrar o tráfego de serviço do Azure__

   Políticas de ponto de extremidade fornecem uma solução altamente disponível e escalonável horizontalmente para filtrar o tráfego de serviço do Azure de redes virtuais em pontos de extremidade de serviço. Nenhuma sobrecarga adicional é necessária para manter os dispositivos de rede central para esse tráfego em suas redes virtuais.

## <a name="json-object-for-service-endpoint-policies"></a>Objeto JSON para políticas de ponto de extremidade de serviço
Vamos dar uma olhada rápida no objeto de política de ponto de extremidade de serviço.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Configuração

-   Você pode configurar as políticas de ponto de extremidade para restringir o tráfego de rede virtual para contas específicas de armazenamento do Azure.
-   A política de ponto de extremidade é configurada em uma sub-rede em uma rede virtual. Os pontos de extremidade de serviço do armazenamento do Azure devem ser habilitados na sub-rede para aplicar a política.
-   A política de ponto de extremidade permite adicionar contas de armazenamento do Azure específicas à lista de permissões, usando o formato ResourceId. Você pode restringir o acesso a
    - todas as contas de armazenamento em uma assinatura<br>
      `E.g. /subscriptions/subscriptionId`

    - todas as contas de armazenamento em um grupo de recursos<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - uma conta de armazenamento individual listando o Azure Resource Manager ResourceId correspondente. Isso abrange o tráfego para blobs, tabelas, filas, arquivos e armazenamento do Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Por padrão, se nenhuma política estiver anexada a uma sub-rede com pontos de extremidade, você poderá acessar todas as contas de armazenamento no serviço. Depois que uma política é configurada na sub-rede, somente os recursos especificados na política poderão ser acessados de instâncias de computação naquela sub-rede. O acesso a todas as outras contas de armazenamento será negado.
-   Ao aplicar políticas de ponto de extremidade de serviço em uma sub-rede, o *escopo do ponto de extremidade do serviço* de armazenamento do Azure é atualizado de regional para **global**. Isso significa que todo o tráfego para o armazenamento do Azure é protegido pelo ponto de extremidade de serviço posteriormente. As políticas de ponto de extremidade de serviço também são aplicáveis globalmente, portanto, qualquer conta de armazenamento, que não seja explicitamente permitida, terá o acesso negado. 
-   Você pode aplicar várias políticas a uma sub-rede. Quando várias políticas são associadas à sub-rede, o tráfego de rede virtual para os recursos especificados em qualquer uma dessas políticas será permitido. O acesso a todos os outros recursos de serviço não especificado em nenhuma das políticas será negado.

    > [!NOTE]  
    > As políticas de ponto de extremidade de serviço são **políticas de permissão**, portanto, além dos recursos especificados, todos os outros recursos são restritos. Verifique se todas as dependências de recursos de serviço para seus aplicativos são identificadas e listadas na política.

- Somente contas de armazenamento usando o Modelo de Recurso do Azure podem ser especificadas na política de ponto de extremidade. Suas contas de armazenamento do Azure clássicas não dão suporte a políticas de ponto de extremidade de serviço do Azure
- Acesso de RA-GRS secundário será permitido automaticamente se a conta primária estiver listada.
- Contas de armazenamento podem estar em uma assinatura ou Locatário do Azure Active Directory igual ou diferente daquele da rede virtual.

## <a name="scenarios"></a>Cenários

- **Redes virtuais emparelhadas, conectadas ou múltiplas**: para filtrar o tráfego em redes virtuais emparelhadas, as políticas de ponto de extremidade devem ser aplicadas individualmente a elas.
- **Filtrando o tráfego de Internet com dispositivos de rede ou o Firewall do Azure**: filtre o tráfego de serviço do Azure com políticas, pontos de extremidade de serviço e filtre o restante da Internet ou o tráfego do Azure por meio de dispositivos ou firewall do Azure.
- **Filtragem de tráfego nos serviços do Azure implantados em redes virtuais**: neste momento, as políticas de ponto de extremidade de serviço do Azure não têm suporte para serviços gerenciados do Azure que são implantados em sua rede virtual. 
- **Filtragem de tráfego para serviços do Azure do local**: políticas de ponto de extremidade de serviço se aplicam somente ao tráfego de sub-redes associadas a políticas. Para permitir o acesso a recursos de Serviço do Azure específicos do local, o tráfego deve ser filtrado usando firewalls ou dispositivos virtuais de rede ou.

## <a name="logging-and-troubleshooting"></a>Registro em log e solução de problemas
Nenhum registro em log centralizado está disponível para políticas de ponto de extremidade de serviço. Para logs de recursos de serviço, consulte [log de pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Cenários de solução de problemas
- Acesso negado às contas de armazenamento que estavam funcionando na visualização (não em uma região emparelhada geograficamente)
  - Com a atualização do armazenamento do Azure para usar marcas de serviço globais, o escopo do ponto de extremidade de serviço e, portanto, as políticas de ponto de extremidade de serviço agora são Portanto, qualquer tráfego para o armazenamento do Azure é criptografado em pontos de extremidade de serviço e somente contas de armazenamento explicitamente listadas na política têm permissão de acesso.
  - Permitir explicitamente listar todas as contas de armazenamento necessárias para restaurar o acesso.  
  - Contate o suporte do Azure.
- O acesso é negado para contas listadas nas políticas de ponto de extremidade
  - Grupos de segurança de rede ou a filtragem de firewall podem estar bloqueando o acesso
  - Se remover/reaplicar a política resultar em perda de conectividade:
    - Valide se o serviço do Azure está configurado para permitir o acesso da rede virtual nos pontos de extremidade ou se a política padrão para o recurso está definida como *permitir todos*.
    - Valide que o diagnóstico de serviço mostra o tráfego nos pontos de extremidade.
    - Verifique se os logs de fluxo do grupo de segurança de rede mostram o acesso e se os logs de armazenamento mostram o acesso, conforme esperado, pelos pontos de extremidade de serviço.
    - Contate o suporte do Azure.
- O acesso é negado para contas não listadas nas políticas de ponto de extremidade de serviço
  - Valide se o armazenamento do Azure está configurado para permitir o acesso da rede virtual nos pontos de extremidade ou se a política padrão do recurso está definida para *permitir todos*.
  - Verifique se as contas não são **contas de armazenamento clássicas** com políticas de ponto de extremidade de serviço na sub-rede.
- Um serviço gerenciado do Azure parou de funcionar após a aplicação de uma política de ponto de extremidade de serviço pela sub-rede
  - Não há suporte para serviços gerenciados com políticas de ponto de extremidade de serviço no momento. *Assista a este espaço para obter atualizações*.
- O acesso a contas de armazenamento gerenciado parou de funcionar após a aplicação de uma política de ponto de extremidade de serviço pela sub-rede
  - Não há suporte para contas de armazenamento gerenciadas com políticas de ponto de extremidade de serviço. Se configurado, as políticas negarão o acesso a todas as contas de armazenamento gerenciadas, por padrão. Se seu aplicativo precisar de acesso a contas de armazenamento gerenciadas, as políticas de ponto de extremidade não deverão ser usadas para esse tráfego.

## <a name="provisioning"></a>Provisionamento

Políticas de ponto de extremidade de serviço podem ser configuradas em sub-redes por um usuário com acesso de gravação a uma rede virtual. Saiba mais sobre [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure e como atribuir permissões específicas a [funções personalizadas](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

As redes virtuais e as contas de armazenamento do Azure podem estar na mesma assinatura ou em assinaturas diferentes, ou Azure Active Directory locatários.

## <a name="limitations"></a>Limitações

- Você só pode implantar políticas de ponto de extremidade de serviço em redes virtuais implantadas por meio do modelo de implantação do Azure Resource Manager.
- Redes virtuais devem estar na mesma região que a política de ponto de extremidade de serviço.
- Só será possível aplicar política de ponto de extremidade de serviço em uma sub-rede se os pontos de extremidade de serviço estiverem configurados para os serviços do Azure listados na política.
- Você não pode usar políticas de ponto de extremidade de serviço para o tráfego da sua rede local para serviços do Azure.
- Atualmente, os serviços gerenciados do Azure não dão suporte a políticas de ponto de extremidade. Isso inclui serviços gerenciados implantados nas sub-redes compartilhadas (por exemplo, *lote do Azure, Azure ADDS, gateway de aplicativo Azure, gateway de VPN do Azure, firewall do Azure*) ou em sub-redes dedicadas (por exemplo *, ambiente do serviço de aplicativo do Azure, cache Redis do Azure, gerenciamento de API do Azure, mi SQL do Azure, serviços gerenciados clássicos*).

 > [!WARNING]
 > Serviços do Azure implantados em sua rede virtual, como Azure HDInsight, acessam outros serviços do Azure, como Armazenamento do Azure, para requisitos de infraestrutura. Restringir a política de ponto de extremidade a recursos específicos pode interromper o acesso a esses recursos de infraestrutura para serviços do Azure implantados em sua rede virtual.

- Não há suporte para contas de armazenamento clássicas em políticas de ponto de extremidade. As políticas negarão o acesso a todas as contas de armazenamento clássicas por padrão. Se seu aplicativo precisar acessar o Azure Resource Manager e contas de armazenamento clássico, políticas de ponto de extremidade não deverão ser usadas para esse tráfego.

## <a name="pricing-and-limits"></a>Preços e limites

Não há custo adicional para usar políticas de ponto de extremidade de serviço. O modelo de preço atual para serviços do Azure (como o Armazenamento do Azure) se aplica é hoje, em pontos de extremidade de serviço.

Os limites a seguir são impostos em políticas de ponto de extremidade de serviço: 

 |Recurso | Limite padrão |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Próximas etapas

- Saiba como [configurar políticas de ponto de extremidade de serviço de rede virtual](virtual-network-service-endpoint-policies-portal.md)
- Saiba mais sobre os [pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md)
