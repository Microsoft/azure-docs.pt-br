---
title: 'Registrar uma conexão do Serviço de Emparelhamento – Azure PowerShell '
description: Neste tutorial, saiba como registrar uma conexão do Serviço de Emparelhamento com o PowerShell.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: ed4f628f6c3b888a640d61048c39f945a81901d7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060832"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Tutorial: Registrar uma conexão do Serviço de Emparelhamento usando o Azure PowerShell

Neste tutorial, você aprenderá a registrar o Serviço de Emparelhamento usando o Azure PowerShell.

O Serviço de Emparelhamento do Azure é um serviço de rede que aprimora a conectividade do cliente com os serviços em nuvem da Microsoft, como o Microsoft 365, o Dynamics 365, os serviços de SaaS (software como serviço), o Azure ou qualquer serviço da Microsoft acessível por meio da Internet pública. Neste artigo, você aprenderá a registrar uma conexão do Serviço de Emparelhamento usando o Azure PowerShell.

Caso não tenha uma assinatura do Azure, crie uma [conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) agora.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar o PowerShell localmente, este início rápido exigirá que você use o módulo do Azure PowerShell versão 1.0.0 ou posterior. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Para obter informações de instalação e atualização, confira o [módulo Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

Por fim, se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount`. Esse comando cria uma conexão com o Azure.

Use o módulo Azure PowerShell para registrar e gerenciar o Serviço de Emparelhamento. Você pode registrar ou gerenciar o Serviço de Emparelhamento na linha de comando do PowerShell ou em scripts.


## <a name="prerequisites"></a>Pré-requisitos  
Você deve ter o seguinte:

### <a name="azure-account"></a>Conta do Azure

Você deve ter uma conta válida e ativa do Microsoft Azure. Essa conta é necessária para configurar a conexão do Serviço de Emparelhamento. O Serviço de Emparelhamento é um recurso dentro das assinaturas do Azure.

### <a name="connectivity-provider"></a>Provedor de conectividade

Você pode trabalhar com um provedor de serviços de Internet ou um parceiro do Exchange da Internet para obter o Serviço de Emparelhamento para conectar sua rede com a rede da Microsoft.

Verifique se os provedores de conectividade são parceiros da Microsoft.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Registrar uma assinatura com o provedor de recursos e o sinalizador de recurso

Antes de prosseguir para as etapas de registro do Serviço de Emparelhamento, registre sua assinatura com o provedor de recursos e o sinalizador de recurso usando o Azure PowerShell. Os comandos do Azure PowerShell são especificados aqui:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Buscar o local e o provedor de serviços 

Execute os seguintes comandos no Azure PowerShell para adquirir o local e o provedor de serviços para os quais o Serviço de Emparelhamento deve ser habilitado. 

Obter locais do Serviço de Emparelhamento:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Obter provedores do Serviço de Emparelhamento:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Registrar a conexão do Serviço de Emparelhamento

Registre a conexão do serviço de emparelhamento usando o seguinte conjunto de comandos por meio do Azure PowerShell. Este exemplo registra o Serviço de Emparelhamento chamado myPeeringService.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Registrar o prefixo do Serviço de Emparelhamento

Registre o prefixo fornecido pelo provedor de conectividade executando os comandos a seguir por meio do Azure PowerShell. Este exemplo registra o prefixo chamado myPrefix.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Listar todas as conexões dos Serviços de Emparelhamento

Para exibir a lista de todos os Serviços de Emparelhamento, execute o seguinte comando:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Listar todos os prefixos de Serviço de Emparelhamento

Para exibir a lista de todos os prefixos do Serviço de Emparelhamento, execute o seguinte comando:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Remover o prefixo do Serviço de Emparelhamento

Para remover o prefixo do Serviço de Emparelhamento, execute o seguinte comando:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a conexão do Serviço de Emparelhamento, confira [Conexão do Serviço de Emparelhamento](connection.md).
- Para saber mais sobre telemetria de conexão do Serviço de Emparelhamento, confira [Telemetria de conexão do Serviço de Emparelhamento](connection-telemetry.md).
- Para registrar uma conexão do Serviço de Emparelhamento usando o portal do Azure, confira [Registrar uma conexão do Serviço de Emparelhamento – portal do Azure](azure-portal.md).
- Para registrar uma conexão do Serviço de Emparelhamento usando a CLI do Azure, confira [Registrar uma conexão do Serviço de Emparelhamento – CLI do Azure](cli.md).
