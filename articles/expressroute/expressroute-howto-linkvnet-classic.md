---
title: 'Azure ExpressRoute: vincular uma VNet a um circuito: clássico'
description: Este documento fornece uma visão geral de como vincular as redes virtuais (VNets) aos circuitos de ExpressRoute usando o modelo de implantação clássico e o PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e8a1d48934d73f1035c6f2c4c76e56d9a0e4052
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206963"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Conectar uma rede virtual a um circuito do ExpressRoute usando o PowerShell (clássico)
> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [CLI do Azure](howto-linkvnet-cli.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-linkvnet-classic.md)
>

Este artigo ajudará você a vincular redes virtuais (VNets) a circuitos do Azure ExpressRoute usando o PowerShell. Uma única rede virtual pode ser vinculada a até quatro circuitos de ExpressRoute. Use as etapas neste artigo para criar um novo link para cada circuito do ExpressRoute ao qual você está se conectando. Os circuitos de ExpressRoute podem estar na mesma assinatura, assinaturas diferentes ou uma mistura de ambos. Este artigo se aplica a redes virtuais criadas usando o modelo de implantação clássico.

Você pode vincular até 10 redes virtuais a um circuito de ExpressRoute. Todas as redes virtuais devem estar na mesma região geopolítica. Você pode vincular um número maior de redes virtuais ao seu circuito da Rota Expressa ou vincular redes virtuais que estão em outras regiões geopolíticas se você ativar o complemento premium da Rota Expressa. Verifique as [perguntas frequentes sobre](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

* Analise os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo.
   * Siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e para que o provedor de conectividade habilite o circuito.
   * Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Consulte o artigo [Configurar roteamento](expressroute-howto-routing-classic.md) para obter instruções de roteamento.
   * Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.
   * É necessário ter uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para [configurar uma rede virtual para o ExpressRoute](./expressroute-howto-add-gateway-portal-resource-manager.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Baixe os cmdlets mais recentes do PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conectar uma rede virtual na mesma assinatura a um circuito
Você pode vincular uma rede virtual a um circuito do ExpressRoute usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o cmdlet.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Remover um link de rede virtual para um circuito
Você pode remover um link de rede virtual para um circuito do ExpressRoute usando o cmdlet a seguir. Verifique se a assinatura atual está selecionada para a rede virtual especificada. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conectar uma rede virtual em uma assinatura diferente a um circuito
Você pode compartilhar um circuito do ExpressRoute entre várias assinaturas. A figura a seguir mostra um esquema simples de como funciona o compartilhamento de circuitos do ExpressRoute entre várias assinaturas.

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas os departamentos podem compartilhar um único circuito do ExpressRoute para se conectar de volta à respectiva rede local. Um único departamento (neste exemplo: TI) pode ter o circuito do ExpressRoute. Outras assinaturas dentro da organização podem usar o circuito de ExpressRoute.

> [!NOTE]
> As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito do ExpressRoute. Todas as redes virtuais compartilham a mesma largura de banda.
> 
> 

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administração
O *proprietário do circuito* é o administrador/coadministrador da assinatura na qual o circuito do ExpressRoute foi criado. O proprietário do circuito pode autorizar administradores/coadministradores de outras assinaturas, conhecidos como *usuários do circuito*, a usar o circuito dedicado que eles possuem. Os usuários do circuito autorizados a usar o circuito do ExpressRoute da organização podem vincular a rede virtual em sua assinatura ao circuito do ExpressRoute depois que são autorizados.

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização fará com que todos os links sejam excluídos da assinatura cujo acesso foi revogado.

### <a name="circuit-owner-operations"></a>Operações do proprietário do circuito

**Criando uma autorização**

O proprietário do circuito autoriza os administradores de outras assinaturas a usar o circuito especificado. No exemplo a seguir, o administrador do circuito (TI da Contoso) habilita o administrador de outra assinatura (Desenvolvimento/Teste) a vincular até duas redes virtuais ao circuito. O administrador de TI da Contoso habilita isso especificando a ID da Microsoft de Desenvolvimento/Teste. O cmdlet não envia um email para a ID da Microsoft especificada. O proprietário do circuito precisa notificar explicitamente o outro proprietário da assinatura de que a autorização foi concluída.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Retorne:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Examinando autorizações**

O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o seguinte cmdlet:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Retorne:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Atualizando autorizações**

O proprietário do circuito pode modificar autorizações usando o seguinte cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Retorne:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Excluindo autorizações**

O proprietário do circuito pode revogar/excluir autorizações usando o seguinte cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operações do usuário do circuito

**Examinando autorizações**

O usuário do circuito pode examinar autorizações usando o seguinte cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Retorne:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Resgatando autorizações de link**

O usuário de circuito pode executar o seguinte cmdlet para resgatar uma autorização de vínculo:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Retorne:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Execute este comando na assinatura recentemente vinculada para a rede virtual:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte [Perguntas Frequentes sobre ExpressRoute](expressroute-faqs.md).