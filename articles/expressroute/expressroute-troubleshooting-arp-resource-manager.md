---
title: 'Azure ExpressRoute: tabelas ARP – solução de problemas'
description: Esta página fornece instruções sobre como obter as tabelas ARP (Address Resolution Protocol) para um circuito do ExpressRoute
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97561572"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Obtenção de tabelas ARP no modelo de implantação do Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - clássico](expressroute-troubleshooting-arp-classic.md)
> 
> 

Este artigo fornece uma orientação pelas etapas de aprendizado sobre as tabelas ARP para seu circuito do ExpressRoute.

> [!IMPORTANT]
> Este documento tem como intenção ajudar você a diagnosticar e corrigir problemas simples. Ela não deve ser usado como uma substituição ao suporte da Microsoft. Você deve abrir um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se não conseguir resolver o problema usando a orientação abaixo.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP (Protocolo de resolução de endereço) e tabelas ARP
ARP (Protocolo de resolução de endereço) é um protocolo de camada 2 definido no [RFC 826](https://tools.ietf.org/html/rfc826). ARP é usado para mapear o endereço de Ethernet (endereço MAC) com um endereço IP.

A tabela ARP fornece as seguintes informações para as interfaces primária e secundária para cada tipo de emparelhamento:

1. Mapeamento do endereço IP da interface do roteador local para o endereço MAC
2. Mapeamento do endereço IP da interface do roteador de ExpressRoute para o endereço MAC
3. Idade do mapeamento

As tabelas ARP podem ajudar a validar a configuração da camada 2 e a solucionar problemas básicos de conectividade da camada 2. 

Exemplo de tabela ARP: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


A seção a seguir fornece informações sobre como você pode exibir as tabelas de ARP vistas pelos roteadores de borda de ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Pré-requisitos para o aprendizado de tabelas ARP
Verifique se as informações abaixo são verdadeiras antes de progredir ainda mais:

* Um circuito de ExpressRoute válido configurado com pelo menos um emparelhamento. O circuito deve ser totalmente configurado pelo provedor de conectividade. Você ou seu provedor de conectividade devem ter configurado pelo menos o emparelhamento privado do Azure, público ou Microsoft Azure nesse circuito.
* Intervalos de endereços IP usados para configurar os emparelhamentos. Examine os exemplos de atribuição de endereço IP na [página de requisitos de roteamento do ExpressRoute](expressroute-routing.md) para entender como os endereços IP são mapeados para interfaces. Saiba mais sobre a configuração de emparelhamento conferindo a [página de configuração de emparelhamento do ExpressRoute](expressroute-howto-routing-arm.md).
* Informações de sua equipe de rede/provedor de conectividade sobre os endereços MAC de interfaces usadas com esses endereços IP.
* Você deve ter o módulo mais recente do PowerShell do Azure (versão 1.50 ou mais recente).

> [!NOTE]
> Se a camada 3 for fornecida pelo provedor de serviços e as tabelas ARP estiverem em branco no portal/saída abaixo, atualize a configuração de circuito usando o botão Atualizar no Portal. Essa operação aplicará a configuração de roteamento correta em seu circuito. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Como obter as tabelas ARP para o circuito de ExpressRoute
Esta seção fornece instruções sobre como você pode exibir as tabelas ARP por emparelhamento usando o PowerShell. Você, ou seu provedor de conectividade, deve ter configurado o emparelhamento antes de prosseguir. Cada circuito tem dois caminhos (primário e secundário). Você pode verificar a tabela ARP para cada caminho de forma independente.

### <a name="arp-tables-for-azure-private-peering"></a>Tabelas ARP para emparelhamento privado do Azure
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento privado do Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Veja abaixo um exemplo de saída para um dos caminhos

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>Tabelas ARP para emparelhamento público do Azure
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento público do Azure

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Veja abaixo um exemplo de saída para um dos caminhos

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>Tabelas ARP para emparelhamento da Microsoft
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento da Microsoft

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Veja abaixo um exemplo de saída para um dos caminhos

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Como usar essas informações
A tabela ARP de um emparelhamento pode ser usada para determinar a validade da configuração e da conectividade da camada 2. Esta seção fornece uma visão geral da aparência das tabelas ARP em cenários diferentes.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Tabela ARP quando um circuito está no estado operacional (estado esperado)
* A tabela ARP terá uma entrada para o lado local com um endereço IP e endereço MAC válidos. O mesmo pode ser visto para o lado da Microsoft. 
* O último octeto do endereço IP local sempre será um número ímpar.
* O último octeto do endereço IP da Microsoft sempre será um número par.
* O mesmo endereço MAC será exibido no lado da Microsoft para todos os três emparelhamentos (primário/secundário). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Tabela de ARP quando o lado do provedor de conectividade/local tiver problemas
Se ocorrer um problema com o provedor local ou de conectividade, a tabela ARP mostrará uma das duas coisas. Você verá o endereço MAC local mostrar incompleto ou só verá a entrada da Microsoft na tabela ARP.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
ou
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Abra uma solicitação de suporte com seu provedor de conectividade para depurar esses problemas. Se a tabela ARP não tiver endereços IP das interfaces mapeados para endereços MAC, examine as seguintes informações:
> 
> 1. Se o primeiro endereço IP da sub-rede /30 atribuído para o link entre o MSEE-PR e MSEE é usado na interface do MSEE-PR. O Azure sempre usa o segundo endereço IP para MSEEs.
> 2. Verifique se as marcações de VLAN do cliente (C-Tag) e de serviços (S-Tag) correspondem às duas no par MSEE-PR e MSEE.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>Tabela ARP quando o lado da Microsoft apresentar problemas
* Você não verá uma tabela ARP mostrada para um emparelhamento se houver problemas no lado da Microsoft. 
* Abra um tíquete com o suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que você tem um problema de conectividade de camada 2. 

## <a name="next-steps"></a>Próximas etapas
* Valide as configurações de camada 3 para o circuito do ExpressRoute.
  * Obtenha o resumo da rota para determinar o estado das sessões BGP.
  * Obter tabela de rotas para determinar quais prefixos são anunciados no ExpressRoute.
* Valide a transferência de dados revisando bytes de entrada/saída.
* Abra um tíquete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.

