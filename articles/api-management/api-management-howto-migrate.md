---
title: Como migrar o gerenciamento de API do Azure entre regiões
description: Saiba como migrar uma instância de gerenciamento de API de uma região para outra.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250219"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Como migrar o gerenciamento de API do Azure entre regiões
Para migrar instâncias de gerenciamento de API de uma região do Azure para outra, você pode usar o recurso de [backup e restauração](api-management-howto-disaster-recovery-backup-restore.md) . Você deve escolher o mesmo tipo de preço de gerenciamento de API nas regiões de origem e de destino. 

> [!NOTE]
> O backup e a restauração não funcionarão durante a migração entre diferentes tipos de nuvem. Para isso, você precisará exportar o recurso [como um modelo](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Em seguida, adapte o modelo exportado para a região do Azure de destino e recrie o recurso. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opção 1: usar um nome de instância de gerenciamento de API diferente

1. Na região de destino, crie uma nova instância de gerenciamento de API com o mesmo tipo de preço que a instância de gerenciamento de API de origem. A nova instância deve ter um nome diferente. 
1. Faça backup da instância de gerenciamento de API existente para uma conta de armazenamento.
1. Restaure o backup criado na etapa 2 para a nova instância de gerenciamento de API criada na nova região na etapa 1.
1. Se você tiver um domínio personalizado apontando para a instância de gerenciamento de API de região de origem, atualize o CNAME de domínio personalizado para apontar para a nova instância de gerenciamento de API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opção 2: usar o mesmo nome de instância de gerenciamento de API

> [!NOTE]
> Essa opção resultará em tempo de inatividade durante a migração.

1. Faça backup da instância de gerenciamento de API na região de origem para uma conta de armazenamento.
1. Exclua o gerenciamento de API na região de origem. 
1. Crie uma nova instância de gerenciamento de API na região de destino com o mesmo nome que aquela na região de origem.
1. Restaure o backup criado na etapa 1 para a nova instância de gerenciamento de API na região de destino.  


## <a name="next-steps"></a><a name="next-steps"> </a>Próximas etapas
* Para obter mais informações sobre o recurso de backup e restauração, consulte [como implementar a recuperação de desastres](api-management-howto-disaster-recovery-backup-restore.md).
* Para obter informações sobre a migração de recursos do Azure, consulte [diretrizes de migração entre regiões do Azure](https://github.com/Azure/Azure-Migration-Guidance).
* [Otimize e economize em seus gastos com a nuvem](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
