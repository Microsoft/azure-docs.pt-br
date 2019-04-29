---
title: Fazendo backup de manifestos de unidade de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como fazer backup automático dos manifestos da unidade do serviço de Importação/Exportação do Microsoft Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483121"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Fazendo backup de manifestos de unidade de trabalhos de Importação/Exportação do Azure

É possível fazer backup automático dos manifestos da unidade em blobs definindo a propriedade `BackupDriveManifest` como `true` nas operações de API REST [Put Job](/rest/api/storageimportexport/jobs) ou [Update Job Properties](/rest/api/storageimportexport/jobs). Por padrão, não é feito o backup dos manifestos da unidade. Os backups dos manifestos da unidade são armazenados como blobs de blocos em um contêiner na conta de armazenamento associada ao trabalho. Por padrão, o nome do contêiner é `waimportexport`, mas é possível especificar outro nome na propriedade `DiagnosticsPath` ao chamar as operações `Put Job` ou `Update Job Properties`. O blob do manifesto do backup é nomeado no seguinte formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 É possível recuperar o URI dos manifestos da unidade de backup de um trabalho chamando a operação [Get Job](/rest/api/storageimportexport/jobs). O URI do blob é retornado na propriedade `ManifestUri` de cada unidade.

## <a name="next-steps"></a>Próximas etapas

* [Usando a API REST do serviço de Importação/Exportação](storage-import-export-using-the-rest-api.md)
