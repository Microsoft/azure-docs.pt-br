---
title: Solucionando problemas de importação e exportação na importação/exportação do Azure | Microsoft Docs
description: Saiba como lidar com problemas comuns ao usar a importação/exportação do Azure.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706170"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Solucionar problemas na importação/exportação do Azure
Este artigo descreve como solucionar problemas comuns ao importar e exportar dados na importação/exportação do Azure.

## <a name="a-copy-session-failed-what-i-should-do"></a>Falha em uma sessão de cópia. O que devo fazer?  

Quando uma sessão de cópia falha, você tem duas opções:  
* Se o erro puder ser repetido – por exemplo, se o compartilhamento de rede estava offline por um curto período e agora está online novamente, você pode retomar a sessão de cópia.
* Se o erro não puder ser repetido-por exemplo, se você especificou o diretório de arquivo de origem incorreto nos parâmetros de linha de comando, será necessário anular a sessão de cópia.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Não consigo retomar nem anular uma sessão de cópia.

Se a sessão de cópia for a primeira sessão de cópia de uma unidade, a mensagem de erro deverá indicar: “A primeira sessão de cópia não pode ser continuada nem anulada”. Nesse caso, é possível excluir o arquivo de diário antigo e executar o comando novamente.  

Se uma sessão de cópia não for a primeira para uma unidade, a sessão sempre poderá ser retomada ou anulada.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Perdi o arquivo de diário. Ainda posso criar o trabalho?

O arquivo de diário de uma unidade contém informações completas da sessão da cópia de dados. Quando você adiciona arquivos à unidade, o arquivo de diário é usado para criar um trabalho de importação. Se você perder o arquivo de diário, precisará refazer todas as sessões de cópia da unidade.

## <a name="next-steps"></a>Próximas etapas

* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)
* [Preparar discos rígidos para um trabalho de importação](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Examinar o status do trabalho com arquivos de log de cópia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparar um trabalho de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparar um trabalho de exportação](storage-import-export-tool-repairing-an-export-job-v1.md)