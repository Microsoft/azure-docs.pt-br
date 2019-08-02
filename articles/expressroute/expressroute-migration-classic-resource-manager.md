---
title: 'Migrar redes virtuais associadas do clássico para o Resource Manager - ExpressRoute: Azure: PowerShell | Microsoft Docs'
description: Esta página descreve como migrar redes virtuais associadas ao ExpressRoute para o Resource Manager depois de mudar o circuito.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f73ab7b68e56f5303e84d86a2711b53ffc894276
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718178"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrar redes virtuais associadas ao ExpressRoute do clássico para o Resource Manager

Este artigo explica como migrar redes virtuais associadas ao ExpressRoute do modelo implantação clássico para o modelo de implantação do Azure Resource Manager após mudar o circuito do ExpressRoute. 

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Verifique se você tem as versões mais recentes dos módulos do PowerShell do Azure. Para obter mais informações, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para instalar o módulo de gerenciamento de serviços do PowerShell (que é necessário para o modelo de implantação clássico), consulte [instalando o módulo de gerenciamento de serviço do Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Leia os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Examine as informações fornecidas em [Como mover um circuito de ExpressRoute do clássico para o Resource Manager](expressroute-move.md). Certifique-se de entender completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo de implantação clássico.
* Verifique se você tem um grupo de recursos que foi criado no modelo de implantação do Gerenciador de Recursos.
* Examine a documentação de migração de recurso a seguir:

    * [Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Perguntas frequentes: Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Confira os erros mais comuns de migração e as mitigações](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Cenários com e sem suporte

* Um circuito do ExpressRoute pode ser migrado do ambiente clássico para o Resource Manager sem nenhum tempo de inatividade. É possível mover um circuito de ExpressRoute do ambiente clássico para o do Resource Manager sem nenhum tempo de inatividade. Siga as instruções em [Movendo circuitos do ExpressRoute do clássico para o modelo de implantação do Resource Manager usando o PowerShell](expressroute-howto-move-arm.md). Este é um pré-requisito para mover recursos conectados à rede virtual.
* Redes virtuais, gateways e implantações associadas dentro da rede virtual que estão anexadas a um circuito de ExpressRoute na mesma assinatura podem ser migradas para o ambiente do Resource Manager sem nenhum tempo de inatividade. Você pode seguir as etapas descritas posteriormente para migrar recursos como redes virtuais, gateways e máquinas virtuais implantadas na rede virtual. Certifique-se de que as redes virtuais estão configuradas corretamente antes de elas serem migradas. 
* Redes virtuais, gateways e implantações associadas dentro da rede virtual que não estão na mesma assinatura que o circuito de ExpressRoute requerem algum tempo de inatividade para que a migração seja concluída. A última seção do documento descreve as etapas a serem seguidas para migrar os recursos.
* Uma rede virtual com o Gateway de ExpressRoute e Gateway VPN não pode ser migrada.
* Não há suporte para a migração entre assinaturas do circuito do ExpressRoute. Para obter mais informações, consulte [Network mover suporte](../azure-resource-manager/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Mover um circuito de ExpressRoute de clássico para Resource Manager
Você deve mover um circuito de ExpressRoute do ambiente clássico para o do Resource Manager antes de tentar migrar os recursos que estão anexados ao circuito de ExpressRoute. Para realizar essa tarefa, consulte os artigos a seguir:

* Examine as informações fornecidas em [Como mover um circuito de ExpressRoute do clássico para o Resource Manager](expressroute-move.md).
* [Migrar um circuito de clássico para o Resource Manager usando o Azure PowerShell](expressroute-howto-move-arm.md).
* Use o Portal de Gerenciamento de Serviços do Azure. Você pode seguir o fluxo de trabalho para [criar um novo circuito de ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e selecionar a opção de importação. 

Esta operação não envolve tempo de inatividade. Você pode continuar a transferir dados entre seu local e a Microsoft enquanto a migração está em andamento.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrar redes virtuais, gateways e implantações associadas

As etapas a serem seguidas para a migração dependem se os recursos estão na mesma assinatura, em assinaturas diferentes ou ambos.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrar redes virtuais, gateways e implantações associadas na mesma assinatura que o circuito de ExpressRoute
Esta seção descreve as etapas a serem seguidas para migrar de uma rede virtual, gateway e implantações associadas na mesma assinatura que o circuito de ExpressRoute. Nenhum tempo de inatividade é associado a essa migração. Você pode continuar a usar todos os recursos durante o processo de migração. O plano de gerenciamento é bloqueado enquanto a migração está em andamento. 

1. Certifique-se de que o circuito de ExpressRoute tenha sido migrado do ambiente clássico para o do Resource Manager.
2. Certifique-se de que a rede virtual foi preparada adequadamente para a migração.
3. Registre sua assinatura para a migração de recursos. Para registrar sua assinatura para a migração de recursos, use o seguinte snippet do PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Valide, prepare e migre. Para mover a rede virtual, use o seguinte snippet do PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Você também pode anular a migração executando o seguinte cmdlet do PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Próximas etapas
* [Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Perguntas frequentes: Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Confira os erros mais comuns de migração e as mitigações](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
