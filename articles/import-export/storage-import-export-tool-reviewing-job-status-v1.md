---
title: Examinando o status do trabalho de Importação/Exportação do Azure — v1 | Microsoft Docs
description: Saiba como usar os arquivos de log criados pelo trabalho de importação ou exportação para ver o status do trabalho.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: f634ceb60ae78d4d825c73bd2e98da2fb951b374
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706174"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Examinando o Status do trabalho de Importação/Exportação do Azure com cópias de arquivos de log
Quando o serviço de Importação/Exportação do Microsoft Azure processa as unidades associadas a um trabalho de importação ou exportação, ele grava os arquivos de log de cópia na conta de armazenamento usada para importar ou exportar BLOBs. O arquivo de log contém o status detalhado sobre cada arquivo importado ou exportado. O serviço retorna a URL para cada arquivo de log de cópia quando você consulta o status de um trabalho concluído. Para obter mais informações, consulte [obter trabalho](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>URLs de exemplo

Estas são URLs de exemplo para arquivos de log de cópia de um trabalho de importação com duas unidades:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Confira [Formato de arquivo de log de serviço de Importação/Exportação](/previous-versions/azure/storage/common/storage-import-export-file-format-log) para obter o formato dos logs de cópia e a lista completa de códigos de status.  

## <a name="next-steps"></a>Próximas etapas

 * [Configurando a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparando discos rígidos para um trabalho de importação](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
 * [Reparação de um trabalho de importação](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de um trabalho de exportação](./storage-import-export-tool-repairing-an-export-job-v1.md)