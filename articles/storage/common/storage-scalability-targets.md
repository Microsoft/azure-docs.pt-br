---
title: Escalabilidade e metas de desempenho do armazenamento do Azure-contas de armazenamento
description: Saiba mais sobre as metas de escalabilidade e desempenho, incluindo capacidade, taxa de solicitação e largura de banda de entrada e saída, para contas de armazenamento do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326969"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Escalabilidade e metas de desempenho do armazenamento do Azure para contas de armazenamento

Este artigo fornece detalhes sobre as metas de desempenho e escalabilidade para contas de armazenamento do Azure. As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo.

Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.

Quando seu aplicativo atinge o limite de processamento de uma partição para sua carga de trabalho, o Armazenamento do Azure começa a retornar respostas com o código de erro 503 (Servidor Ocupado) ou 500 (Tempo Limite da Operação). Se 503 erros estiverem ocorrendo, considere modificar seu aplicativo para usar uma política de backoff exponencial para novas tentativas. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.

## <a name="storage-account-scale-limits"></a>Limites de escala da conta de armazenamento

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limites de escala da conta de armazenamento de desempenho premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limites de escala de provedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Destinos de escala de Armazenamento de Blobs do Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Destinos de escala de Arquivos do Azure

Para saber mais sobre as metas de escala e desempenho para Arquivos do Azure e da Sincronização de Arquivos do Azure, consulte [Metas de escala e de desempenho de Arquivos do Azure](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Os limites da conta de armazenamento são aplicados a todos os compartilhamentos. A expansão para o máximo de contas de armazenamento só será atingível se houver apenas um compartilhamento por conta de armazenamento.
>
> Compartilhamentos de arquivos padrão maiores que 5 TiB estão em visualização e têm certas limitações.
> Para obter uma lista de limitações e integrar a visualização desses tamanhos maiores de compartilhamento de arquivos, consulte a seção compartilhamentos de arquivos [padrão](../files/storage-files-planning.md#standard-file-shares) do guia de planejamento de arquivos do Azure.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Destinos de escala de arquivos Premium

Há três categorias de limitações a serem consideradas para arquivos Premium: contas de armazenamento, compartilhamentos e arquivos.

Por exemplo: Um único compartilhamento pode alcançar 100.000 IOPS e um único arquivo pode ser dimensionado para até 5.000 IOPS. Portanto, por exemplo, se você tiver três arquivos em um único compartilhamento, o máximo de IOPs que você pode obter desse compartilhamento é 15.000.

#### <a name="premium-file-share-limits"></a>Limites de compartilhamento de arquivos Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure

A sincronização de Arquivos do Azure foi projetado com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Destinos de escala de Armazenamento de Fila do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Destinos de escala de Armazenamento de Tabela do Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Consulte também

- [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
- [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md)
- [Replicação de armazenamento do Azure](../storage-redundancy.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../storage-performance-checklist.md)
