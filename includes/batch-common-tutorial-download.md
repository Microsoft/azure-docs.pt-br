---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172272"
---
### <a name="retrieve-output-files"></a>Recuperar arquivos de saída

Você pode usar o Portal do Azure para baixar os arquivos MP3 de saída gerados pelas tarefas de ffmpeg. 

1. Clique em **Todos os serviços** > **Contas de armazenamento** e, depois, clique no nome da sua conta de armazenamento.
2. Clique em **Blobs** > *saída*.
3. Clique com o botão direito em um dos arquivos MP3 de saída e, em seguida, clique em **Baixar**. Siga as instruções em seu navegador para abrir ou salvar o arquivo.

![Baixar o arquivo de saída](./media/batch-common-tutorial-download/download.png)

Embora não seja mostrado neste exemplo, você também pode baixar os arquivos de forma programática a partir dos nós de computação ou do contêiner de armazenamento.
