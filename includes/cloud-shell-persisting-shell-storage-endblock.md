---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172259"
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