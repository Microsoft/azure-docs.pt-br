---
title: 'Configurar o emparelhamento para um circuito – ExpressRoute: Azure: clássico | Microsoft Docs'
description: Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento do emparelhamento público, privado e da Microsoft de um circuito de ExpressRoute. Este artigo também mostra como verificar o status, atualizar ou excluir emparelhamentos de seu circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: a57681cc9f44593ceea6b2c1795274c1b16d3a94
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64726203"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Criar e modificar o emparelhamento de um circuito de ExpressRoute (clássico)
> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Este artigo fornece uma orientação pelas etapas de criação e de gerenciamento da configuração de emparelhamento/roteamento de um circuito do ExpressRoute usando o PowerShell e o modelo de implantação clássico. As etapas a seguir também mostrarão a você como verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Você pode configurar um, dois ou todos os três emparelhamentos (privado e público do Azure e da Microsoft) para um circuito da ExpressRoute. Você pode configurar emparelhamentos em qualquer ordem escolhida. No entanto, você deve concluir a configuração de um emparelhamento por vez. 

Estas instruções aplicam-se apenas a circuitos criados com provedores de serviços que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

* Verifique se você leu a página de [pré-requisitos](expressroute-prerequisites.md), a página de [requisitos do roteamento](expressroute-routing.md) e a página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito do ExpressRoute deve estar em um estado provisionado e habilitado e para que você possa executar os cmdlets descritos abaixo.

### <a name="download-the-latest-powershell-cmdlets"></a>Baixe os cmdlets mais recentes do PowerShell

Instale as versões mais recentes dos módulos PowerShell do Gerenciamento de Serviços do Azure (SM) e do módulo ExpressRoute. Ao usar o exemplo a seguir, observe que o número da versão (neste exemplo, 5.1.1) será alterado conforme versões mais recentes dos cmdlets forem liberadas.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Para obter mais informações, consulte [Introdução aos cmdlets do Azure PowerShell](/powershell/azure/overview) para obter orientações passo a passo sobre como configurar seu computador para usar os módulos do Azure PowerShell.

### <a name="sign-in"></a>Entrar

Para entrar na sua conta do Azure, use os seguintes exemplos:

1. Abra o console do PowerShell com direitos elevados e conecte-se à sua conta.

   ```powershell
   Connect-AzAccount
   ```
2. Verificar as assinaturas da conta.

   ```powershell
   Get-AzSubscription
   ```
3. Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Use o cmdlet a seguir para adicionar sua assinatura do Azure ao PowerShell para o modelo de implantação clássico.

   ```powershell
   Add-AzureAccount
   ```

## <a name="azure-private-peering"></a>Emparelhamento privado do Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. **Criar um circuito do ExpressRoute.**

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, solicite a ele a habilitação do emparelhamento privado do Azure. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, siga as instruções abaixo.
2. **Verifique o circuito do ExpressRoute para garantir que ele seja provisionado.**
   
   Verifique se o circuito da Rota Expressa é Provisionado e também Ativado.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Retorne:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verifique se o circuito aparece como Provisionado e Habilitado. Se não for, trabalhe com seu provedor de conectividade para obter o circuito para o estado e o status necessários.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configure o emparelhamento privado do Azure para o circuito.**

   Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:
   
   * Uma sub-rede /30 para o link principal. Ela não deve fazer parte de qualquer espaço de endereçamento reservado para redes virtuais.
   * Uma sub-rede /30 para o link secundário. Ela não deve fazer parte de qualquer espaço de endereçamento reservado para redes virtuais.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa o mesmo ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento. Verifique se que você não use 65515.
   * Um Hash MD5, se você optar por usar um. **Opcional**.
     
   Você pode usar o exemplo a seguir para configurar o emparelhamento privado do Azure para o seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Se você quiser usar um hash MD5, use o exemplo a seguir para configurar o emparelhamento privado para o seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Verifique se que você especifique o número como um ASN de emparelhamento, não um ASN de cliente.
   > 

### <a name="to-view-azure-private-peering-details"></a>Para exibir detalhes sobre o emparelhamento privado do Azure

Você pode exibir detalhes de configuração usando o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Retorne:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir. No exemplo a seguir, o ID da VLAN do circuito está sendo atualizado de 100 para 500.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet. Você deve certificar-se de que todas as redes virtuais estejam desvinculadas do circuito da Rota Expressa antes de executar esse cmdlet.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Emparelhamento público do Azure

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito do ExpressRoute.

> [!NOTE]
> Emparelhamento público do Azure foi preterido para circuitos de novo.
>

### <a name="to-create-azure-public-peering"></a>Criar o emparelhamento público do Azure

1. **Criar um circuito do ExpressRoute**

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, solicite a ele a habilitação do emparelhamento público do Azure. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, siga as instruções abaixo.
2. **Verifique o circuito do ExpressRoute para verificar se ele está provisionado**

   Primeiro, verifique se o circuito do ExpressRoute está Provisionado e Habilitado.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Retorne:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verifique se o circuito está exibido como Provisionado e Ativado. Se não for, trabalhe com seu provedor de conectividade para obter o circuito para o estado e o status necessários.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Configure o emparelhamento público do Azure para o circuito**
   
   Verifique se você tem as informações a seguir antes de prosseguir:
   
   * Uma sub-rede /30 para o link principal. Precisa ser um prefixo IPv4 público válido.
   * Uma sub-rede /30 para o link secundário. Precisa ser um prefixo IPv4 público válido.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa o mesmo ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Um Hash MD5, se você optar por usar um. **Opcional**.

   > [!IMPORTANT]
   > Certifique-se de especificar seu número AS como emparelhamento no ASN e não no ASN do cliente.
   >  
     
   Você pode usar o exemplo a seguir para configurar o emparelhamento público do Azure para o seu circuito:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Se você quiser usar um hash MD5, use o seguinte exemplo para configurar seu circuito:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Para exibir detalhes sobre o emparelhamento público do Azure

Para exibir detalhes de configuração, use o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Retorne:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o cmdlet a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 200 para 600.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Verifique se o circuito está exibido como Provisionado e Ativado. 
### <a name="to-delete-azure-public-peering"></a>Excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Emparelhamento da Microsoft

Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. **Criar um circuito do ExpressRoute**
  
   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-classic.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, solicite a ele a habilitação do emparelhamento privado do Azure. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, siga as instruções abaixo.
2. **Verifique o circuito do ExpressRoute para verificar se ele está provisionado**

   Verifique se o circuito está exibido como Provisionado e Ativado. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Retorne:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Verifique se o circuito está exibido como Provisionado e Ativado. Se não for, trabalhe com seu provedor de conectividade para obter o circuito para o estado e o status necessários.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Configurar o emparelhamento da Microsoft para o circuito**
   
    Você precisa ter as seguintes informações antes de continuar:
   
   * Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
   * Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa o mesmo ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: você precisa fornecer uma lista com todos os prefixos que planeja anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Você pode enviar uma lista separada por vírgula se planeja enviar um conjunto de prefixos. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * ASN do cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados. **Opcional**.
   * Nome do registro de roteamento: é possível especificar o RIR/IRR no qual o número AS e os prefixos estão registrados.
   * Um hash MD5, se você optar por usar um. **Opcional**
     
   Execute o seguinte cmdlet para configurar o Microsoft emparelhamento para o seu circuito:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Para exibir detalhes de emparelhamento da Microsoft

Você pode exibir detalhes de configuração usando o seguinte cmdlet:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Retorne:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração usando o seguinte cmdlet:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, [Vincular uma Rede Virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Para obter mais informações sobre fluxos de trabalho, veja [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
