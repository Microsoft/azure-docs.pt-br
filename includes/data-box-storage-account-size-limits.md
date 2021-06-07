---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225673"
---
Aqui estão os limites do tamanho dos dados que são copiados em uma conta de armazenamento. Verifique se os dados carregados estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, consulte [escalabilidade e metas de desempenho para armazenamento de BLOBs](../articles/storage/blobs/scalability-targets.md) e [metas de desempenho e escalabilidade de arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e blob de páginas                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a assinatura do Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) e inclui dados de todas as fontes, incluindo data box.   |
| Arquivos do Azure                                                          | O Data Box oferece suporte a compartilhamentos de arquivos grandes (100 TiB), se habilitado antes da criação da ordem de Data Box. <br> Se não estiver habilitado antes da criação do pedido, o tamanho máximo do compartilhamento de arquivos com suporte é 5 TiB. <br> O Data Box dá suporte a compartilhamentos de arquivos premium do Azure que permitem um total de 100 TiB para todos os compartilhamentos na conta de armazenamento. <br> A capacidade utilizável máxima é um pouco menor devido ao espaço que os logs de cópia e os logs de auditoria usam. Um mínimo de 100 GiB cada é reservado para o log de cópia e log de auditoria. Para obter mais informações, consulte [logs de auditoria para Azure Data Box Azure data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> Todas as pastas *StorageAccount_AzureFiles* precisam seguir esse limite. <br> Para obter mais informações, consulte [habilitar e criar compartilhamentos de arquivos grandes](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
