---
title: Mover máquinas virtuais do Azure para nova assinatura ou grupo de recursos | Microsoft Docs
description: Use o Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723539"
---
# <a name="move-guidance-for-virtual-machines"></a>Diretrizes de movimentação para máquinas virtuais

Este artigo descreve os cenários que atualmente não têm suporte e as etapas para mover máquinas virtuais com backup.

## <a name="scenarios-not-supported"></a>Cenários sem suporte

Ainda não há suporte para os cenários a seguir:

* Discos gerenciados em zonas de disponibilidade não podem ser movidos para uma assinatura diferente.
* Máquinas Virtuais com certificado armazenado no Key Vault podem ser movidas para um novo grupo de recursos na mesma assinatura, mas não entre assinaturas.
* Conjuntos de dimensionamento de máquina virtual com o balanceador de carga de SKU Standard ou IP público do SKU Standard não pode ser movidos.
* As máquinas virtuais criadas a partir dos recursos do Marketplace com os planos anexados não podem ser movidas entre grupos de recursos ou assinaturas. Desprovisione a máquina virtual na assinatura atual e implante-a novamente na nova assinatura.
* Máquinas virtuais em uma rede virtual existente, mas você não estiver movendo todos os recursos na rede virtual.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com o Backup do Azure

Para mover máquinas virtuais configuradas com o Backup do Azure, use a seguinte solução alternativa:

* Localize o local de sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se estiver no portal do Azure, marque "Mostrar tipos ocultos"
* Se estiver no PowerShell, use o cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Se estiver na CLI, use o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localize o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Exclua este recurso. Esta operação exclui somente os pontos de recuperação instantânea, não os dados de backup no cofre.
* Após a conclusão da exclusão, você pode mover o cofre e a máquina virtual para a assinatura de destino. Após a movimentação, você poderá continuar backups sem perda de dados.
* Para saber mais sobre como mover cofres do Serviço de Recuperação para o backup, veja [Limitações dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Próximas etapas

Para ver comandos para mover recursos, confira [Move resources to new resource group or subscription](../resource-group-move-resources.md) (Mover recursos para o novo grupo de recursos ou assinatura).
