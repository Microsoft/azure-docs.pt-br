---
title: "Examinando o status do trabalho de Importação/Exportação do Azure — v1 | Microsoft Docs"
description: "Saiba como usar os arquivos de log criados durante a execução do trabalho de importação ou exportação para ver o status do trabalho de Importação/Exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Examinando o Status do trabalho de Importação/Exportação do Azure com cópias de arquivos de log
Quando o serviço de Importação/Exportação do Microsoft Azure processa unidades associadas a um trabalho de importação ou exportação, ele grava arquivos de log de cópia na conta de armazenamento para a qual ou da qual os blobs estão sendo importados ou exportados. O arquivo de log contém o status detalhado sobre cada arquivo importado ou exportado. A URL para cada arquivo de log de cópia é retornada ao consultar o status de um trabalho concluído; consulte [Get Job](/rest/api/storageservices/Get-Job3) (Obter Trabalho) para obter mais informações.  

## <a name="example-urls"></a>URLs de exemplo

Estas são URLs de exemplo para arquivos de log de cópia de um trabalho de importação com duas unidades:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 Confira [Formato de arquivo de log de serviço de Importação/Exportação](../storage-import-export-file-format-log.md) para obter o formato dos logs de cópia e a lista completa de códigos de status.  
  
## <a name="next-steps"></a>Próximas etapas
 
 * [Configurando a Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-setup-v1.md)   
 * [Preparação de discos rígidos para um trabalho de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Reparação de um trabalho de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Reparação de um trabalho de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Solucionando problemas da Ferramenta de Importação/Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
