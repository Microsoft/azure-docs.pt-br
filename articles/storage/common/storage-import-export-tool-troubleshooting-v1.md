---
title: "Solucionando problemas da Ferramenta de Importação/Exportação do Azure | Microsoft Docs"
description: "Saiba mais sobre alguns dos problemas comuns enfrentados ao usar a Ferramenta de Importação/Exportação do Azure e como lidar com eles."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Solução de problemas da Ferramenta de Importação/Exportação do Azure
A Ferramenta de Importação/Exportação do Microsoft Azure retorna mensagens de erro em caso de problemas. Este tópico lista alguns problemas comuns que os usuários poderão enfrentar.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Quando uma sessão de cópia falha o que devo fazer?  
 Quando uma sessão de cópia falha, há duas opções:  
  
 Se o erro for com nova tentativa, por exemplo, se o compartilhamento de rede ficou offline por um curto período e agora está online novamente, será possível retomar a sessão de cópia. Se o erro for sem nova tentativa, por exemplo, se você especificou o diretório do arquivo de origem incorreto nos parâmetros de linha de comando, você precisará anular a sessão de cópia. Consulte [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md) (Preparando os discos rígidos para um trabalho de importação) para obter mais informações sobre como continuar e anular sessões de cópia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Não consigo retomar nem anular uma sessão de cópia.  
 Se a sessão de cópia for a primeira sessão de cópia de uma unidade, a mensagem de erro deverá indicar: “A primeira sessão de cópia não pode ser continuada nem anulada”. Nesse caso, é possível excluir o arquivo de diário antigo e executar o comando novamente.  
  
 Se uma sessão de cópia não for a primeira de uma unidade, ela poderá sempre ser continuada ou anulada.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Perdi o arquivo de diário. Ainda posso criar o trabalho?  
 O arquivo de diário de uma unidade contém as informações completas da cópia de dados para essa unidade. Ele é necessário para adicionar mais arquivos à unidade e será usado para criar um trabalho de importação. Em caso de perda do arquivo de diário, você precisará refazer todas as sessões de cópia da unidade.  
  
## <a name="next-steps"></a>Próximas etapas
 
* [Configurando a Ferramenta de importação/exportação do Azure](../storage-import-export-tool-setup-v1.md)   
* [Preparação de discos rígidos para um trabalho de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do status do trabalho com arquivos de log de cópia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de um trabalho de importação](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparação de um trabalho de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)
