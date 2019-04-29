---
title: Cancelar e excluir um trabalho de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como cancelar e excluir trabalhos do serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483138"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Cancelando e excluindo trabalhos de Importação/Exportação do Azure

 Para solicitar que um trabalho seja cancelado antes que ele entre no estado `Packaging`, chame a operação [Atualizar Propriedades do Trabalho](/rest/api/storageimportexport/jobs) e defina o elemento `CancelRequested` como `true`. O trabalho é cancelado com os melhores esforços. Se as unidades estiverem transferindo dados, os dados poderão continuar sendo transferidos mesmo depois que o cancelamento tiver sido solicitado.

 Um trabalho cancelado é movido para o estado `Completed` e é mantido por 90 dias, ponto em que é excluído.

 Para excluir um trabalho, chame a operação [Delete Job](/rest/api/storageimportexport/jobs) antes que o trabalho seja enviado (ou seja, enquanto o trabalho está no estado `Creating`). Também é possível excluir um trabalho quando ele está no estado `Completed`. Após a exclusão de um trabalho, suas informações e seu status não são mais acessíveis por meio da API REST ou do portal do Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
