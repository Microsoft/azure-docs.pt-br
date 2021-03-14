---
title: Substituição de sub-rede do Gerenciador de tráfego do Azure usando Azure PowerShell | Microsoft Docs
description: Este artigo o ajudará a entender como a substituição de sub-rede do Gerenciador de tráfego é usada para substituir o método de roteamento de um perfil do Gerenciador de tráfego para direcionar o tráfego para um ponto de extremidade baseado no endereço IP do usuário final por meio do intervalo de IP predefinido para mapeamentos de ponto de extremidade usando Azure PowerShell.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 7dd7f43044a9643eb7e9d5296dfb209e425d5fb6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504765"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Substituição de sub-rede do Gerenciador de tráfego usando o Azure PowerShell

A substituição de sub-rede do Gerenciador de tráfego permite alterar o método de roteamento de um perfil.  A adição de uma substituição direcionará o tráfego com base no endereço IP do usuário final com um intervalo de IP predefinido para o mapeamento de ponto de extremidade. 

## <a name="how-subnet-override-works"></a>Como a substituição de sub-rede funciona

Quando as substituições de sub-rede são adicionadas a um perfil do Gerenciador de tráfego, o Gerenciador de tráfego primeiro verificará se há uma substituição de sub-rede para o endereço IP do usuário final. Se um for encontrado, a consulta DNS do usuário será direcionada para o ponto de extremidade correspondente.  Se um mapeamento não for encontrado, o Gerenciador de tráfego fará o fallback para o método de roteamento original do perfil. 

Os intervalos de endereços IP podem ser especificados como intervalos CIDR (por exemplo, 1.2.3.0/24) ou como intervalos de endereços (por exemplo, 1.2.3.4-5.6.7.8). Os intervalos de IP associados a cada ponto de extremidade devem ser exclusivos para esse ponto de extremidade. Qualquer sobreposição de intervalos de IP entre diferentes pontos de extremidade fará com que o perfil seja rejeitado pelo Gerenciador de tráfego.

Há dois tipos de perfis de roteamento que dão suporte a substituições de sub-rede:

* **Geográfico** – se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele roteará a consulta para o ponto de extremidade, independentemente da integridade do ponto de extremidade.
* **Desempenho** – se o Gerenciador de tráfego encontrar uma substituição de sub-rede para o endereço IP da consulta DNS, ele roteará apenas o tráfego para o ponto de extremidade se ele estiver íntegro.  O Gerenciador de tráfego fará o fallback para o heurística de roteamento de desempenho se o ponto de extremidade de substituição de sub-rede não estiver íntegro.

## <a name="create-a-traffic-manager-subnet-override"></a>Criar uma substituição de sub-rede do Gerenciador de tráfego

Para criar uma substituição de sub-rede do Gerenciador de tráfego, você pode usar Azure PowerShell para adicionar as sub-redes para a substituição para o ponto de extremidade do Gerenciador de tráfego.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell pelo computador, será necessário ter o módulo do Azure PowerShell do Azure, 1.0.0 ou posterior. É possível executar `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se estiver executando o PowerShell localmente, também será necessário executar `Login-AzAccount` entrar no Azure.


1. **Recuperar o ponto de extremidade do Gerenciador de tráfego:**

    Para habilitar a substituição de sub-rede, recupere o ponto de extremidade para o qual você deseja adicionar a substituição e armazene-o em uma variável usando [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint).

    Substitua o nome, ProfileName e ResourceGroupName pelos valores do ponto de extremidade que você está alterando.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Adicione o intervalo de endereços IP ao ponto de extremidade:**
    
    Para adicionar o intervalo de endereços IP ao ponto de extremidade, você usará [Add-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange) para adicionar o intervalo.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Depois que os intervalos forem adicionados, use [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) para atualizar o ponto de extremidade.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
A remoção do intervalo de endereços IP pode ser concluída usando [Remove-AzTrafficManagerIpAddressRange](/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange).

1.  **Recuperar o ponto de extremidade do Gerenciador de tráfego:**

    Para habilitar a substituição de sub-rede, recupere o ponto de extremidade para o qual você deseja adicionar a substituição e armazene-o em uma variável usando [Get-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint).

    Substitua o nome, ProfileName e ResourceGroupName pelos valores do ponto de extremidade que você está alterando.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Remova o intervalo de endereços IP do ponto de extremidade:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Depois que os intervalos forem removidos, use [set-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint) para atualizar o ponto de extremidade.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

Saiba mais sobre o [método de roteamento de tráfego de sub-rede](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)