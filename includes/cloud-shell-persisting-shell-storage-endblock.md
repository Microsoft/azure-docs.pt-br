---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200115"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Transferir arquivos locais para o Cloud Shell
O diretório `clouddrive` é sincronizado com a folha de armazenamento do portal do Azure. Use essa folha para transferir arquivos locais de ou para o compartilhamento de arquivos. A atualização dos arquivos a partir do Cloud Shell é refletida na GUI do armazenamento de arquivos quando você atualiza a folha.

### <a name="download-files"></a>Baixar arquivos

![Lista de arquivos locais](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. No portal do Azure, vá para o compartilhamento de arquivos montado.
2. Selecione o arquivo de destino.
3. Selecione o botão **Baixar** .

### <a name="upload-files"></a>Carregar arquivos

![Arquivos locais a serem carregados](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Vá para o compartilhamento de arquivos montado.
2. Selecione o botão **Carregar**.
3. Selecione os arquivos que você deseja carregar.
4. Confirme o upload.

Agora você deve ver os arquivos que estão acessíveis em seu diretório do `clouddrive` no Cloud Shell.