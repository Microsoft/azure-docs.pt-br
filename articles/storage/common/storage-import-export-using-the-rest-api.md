---
title: "Usando a API REST do serviço de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba ais sobre como encontrar recursos para usar a API REST do serviço de Importação/Exportação do Azure, incluindo instruções e material de referência."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: b780385ad0af34bcb15639683d1aa5d689b38b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Usando a API REST do serviço de Importação/Exportação do Azure

O serviço de Importação/Exportação do Microsoft Azure expõe uma API REST para habilitar o controle programático de trabalhos de importação/exportação. É possível usar a API REST para executar todas as operações de importação/exportação que podem ser executadas com o [portal do Azure](https://portal.azure.com/). Além disso, é possível usar a API REST para executar determinadas operações granulares, como consulta do percentual de conclusão de um trabalho, que não estão disponíveis atualmente no Portal Clássico do Azure.

Consulte [Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o Armazenamento de Blobs](../storage-import-export-service.md) para obter uma visão geral do serviço de Importação/Exportação e um tutorial que demonstra como usar o portal clássico para criar e gerenciar trabalhos de importação e exportação.

## <a name="service-endpoints"></a>Pontos de extremidade de serviço

O serviço de Importação/Exportação do Azure é um provedor de recursos para o Azure Resource Manager e fornece um conjunto de APIs REST no seguinte ponto de extremidade HTTPS para o gerenciamento de trabalhos de importação/exportação:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Controle de versão

As solicitações para o serviço de Importação/Exportação devem especificar o parâmetro `api-version` e definir seu valor como `2016-11-01`.

## <a name="importexport-service-operations"></a>Operações do serviço de Importação/Exportação

[Criação de um trabalho de importação](../storage-import-export-creating-an-import-job.md)

[Criação de um trabalho de exportação](../storage-import-export-creating-an-export-job.md)

[Recuperação de informações de estado para um trabalho](storage-import-export-retrieving-state-info-for-a-job.md)

[Enumeração de trabalhos](../storage-import-export-enumerating-jobs.md)

[Cancelamento e exclusão de trabalhos](storage-import-export-cancelling-and-deleting-jobs.md)

[Backup de manifestos da unidade](../storage-import-export-backing-up-drive-manifests.md)

[Diagnóstico e recuperação de erro para trabalhos de Importação/Exportação](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Próximas etapas

* [Referência de REST de Importação/Exportação do Armazenamento](/rest/api/storageimportexport)
