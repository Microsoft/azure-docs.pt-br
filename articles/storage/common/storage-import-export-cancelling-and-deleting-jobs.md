---
title: "Cancelar e excluir um trabalho de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba como cancelar e excluir trabalhos do serviço de Importação/Exportação do Microsoft Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1e989c72fc03697bf6d2e515ff53003703665d1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Cancelando e excluindo trabalhos de Importação/Exportação do Azure

 Para solicitar que um trabalho seja cancelado antes que ele entre no estado `Packaging`, chame a operação [Atualizar Propriedades do Trabalho](/rest/api/storageimportexport/jobs#Jobs_Update) e defina o elemento `CancelRequested` como `true`. O trabalho é cancelado com os melhores esforços. Se as unidades estiverem transferindo dados, os dados poderão continuar sendo transferidos mesmo depois que o cancelamento tiver sido solicitado.

 Um trabalho cancelado é movido para o estado `Completed` e é mantido por 90 dias, ponto em que é excluído.

 Para excluir um trabalho, chame a operação [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) antes que o trabalho seja enviado (ou seja, enquanto o trabalho está no estado `Creating`). Também é possível excluir um trabalho quando ele está no estado `Completed`. Após a exclusão de um trabalho, suas informações e seu status não são mais acessíveis por meio da API REST ou do portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
