---
title: 'Azure ExpressRoute: mover circuitos clássicos para o Gerenciador de recursos'
description: Esta página descreve como mover um circuito clássico para o modelo de implantação do Resource Manager usando o PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 460ea446fc6dfc43e81a1a57bbba032a61f3a72d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90532538"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Mover os circuitos do ExpressRoute do modelo de implantação clássico para o do Resource Manager usando o PowerShell

Para usar um circuito do ExpressRoute para os modelos de implantação clássico e do Resource Manager, você deve mover o circuito para o modelo de implantação do Resource Manager. As seções a seguir lhe ajudarão a mover seu circuito usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

* Verifique se você instalou os módulos clássico e AZ Azure PowerShell localmente no seu computador. Para obter mais informações, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/).
* Leia os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Examine as informações fornecidas em [Como mover um circuito de ExpressRoute do clássico para o Resource Manager](expressroute-move.md). Certifique-se de entender completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo de implantação clássico.
* Verifique se você tem um grupo de recursos que foi criado no modelo de implantação do Gerenciador de Recursos.

## <a name="move-an-expressroute-circuit"></a>Mover um circuito de ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Etapa 1: Coletar detalhes do circuito do modelo de implantação clássico

Entre no ambiente clássico do Azure e obtenha a chave de serviço.

1. Entre em sua conta do Azure.

   ```powershell
   Add-AzureAccount
   ```

2. Selecione a assinatura do Azure apropriada.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importar os módulos do PowerShell para Azure e ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Use o cmdlet abaixo para obter as chaves de serviço para todos os seus circuitos do ExpressRoute. Após a recuperação das chaves, copie a **chave de serviço** do circuito que você deseja mover para o modelo de implantação do Resource Manager.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Etapa 2: Entrar e criar um grupo de recursos

Entre no ambiente do Resource Manager e crie um novo grupo de recursos.

1. Entre no ambiente do Azure Resource Manager.

   ```powershell
   Connect-AzAccount
   ```

2. Selecione a assinatura do Azure apropriada.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Modifique o snippet a seguir para criar um novo grupo de recursos se você ainda não tiver um grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Etapa 3: Mover o circuito de ExpressRoute para o modelo de implantação do Gerenciador de Recursos

Agora você está pronto para mover o circuito de ExpressRoute do modelo de implantação clássico para o do Resource Manager. Antes de prosseguir, examine as informações fornecidas em [Como mover circuitos de ExpressRoute do modelo de implantação clássico para o Resource Manager](expressroute-move.md).

Para mover o circuito, modifique e execute o snippet a seguir:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

No modo clássico, um circuito ExpressRoute não tem o conceito de estar ligado a uma região. No Gerenciador de Recursos, todo recurso precisa ser mapeado para uma região do Azure. A região especificada no cmdlet Move-AzExpressRouteCircuit pode ser tecnicamente qualquer região. Para propósitos organizacionais, você pode desejar escolher uma região que represente de perto seu local de emparelhamento.

> [!NOTE]
> * Depois de mover o circuito do ExpressRoute clássico para o modelo de implantação do Gerenciador de recursos, ele terá acesso aos modelos de implantação clássico e do Resource Manager por padrão.
> * O novo nome listado no cmdlet anterior será usado para endereçar o recurso. O circuito essencialmente será renomeado.

## <a name="modify-circuit-access"></a>Modificar o acesso de circuito

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para habilitar o acesso ao circuito de ExpressRoute para ambos os modelos de implantação

Você pode habilitar o acesso ao modelo de implantação clássico para circuitos do ExpressRoute que foram criados no modelo de implantação do Gerenciador de recursos. Execute os cmdlets a seguir para habilitar o acesso a ambos os modelos de implantação:

1. Obtenha os detalhes do circuito.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Defina "Permitir operações clássicas" como TRUE.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Atualize o circuito. Depois que a operação for concluída com êxito, você poderá exibir o circuito no modelo de implantação clássico.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Execute o cmdlet a seguir para obter os detalhes do circuito de ExpressRoute. Você deve ser capaz de ver a chave de serviço relacionada.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Agora você pode gerenciar links para o circuito do ExpressRoute usando os comandos do modelo de implantação clássico para redes virtuais clássicas e os comandos do Gerenciador de recursos para VNets do Gerenciador de recursos. Os artigos a seguir lhe ajudam a gerenciar links para o circuito de ExpressRoute:

    * [Vincule sua rede virtual ao circuito de ExpressRoute no modelo de implantação do Gerenciador de Recursos](expressroute-howto-linkvnet-arm.md)
    * [Vincule sua rede virtual ao circuito de ExpressRoute no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desabilitar o circuito de ExpressRoute para o modelo de implantação clássico

Execute os cmdlets a seguir para desabilitar o acesso ao modelo de implantação clássico.

1. Obtenha detalhes do circuito de ExpressRoute.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Defina "Permitir operações clássicas" como FALSE.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Atualize o circuito. Depois que a operação for concluída com êxito, você não poderá exibir o circuito no modelo de implantação clássico.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Próximas etapas

* [Criar e modificar o roteamento do circuito do ExpressRoute](expressroute-howto-routing-arm.md)
* [Vincular a rede virtual ao circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
