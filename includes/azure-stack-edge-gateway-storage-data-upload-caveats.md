---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466021"
---
As limitações a seguir se aplicam aos dados conforme são movidos para o Azure.

- Sugerimos que mais de um dispositivo não deve gravar no mesmo contêiner.
- Se você tiver um objeto existente do Azure (como um blob ou arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o dispositivo substituirá o arquivo na nuvem.
- Uma hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas de compartilhamento não é carregada nos contêineres de blob.
- Você pode copiar os dados usando arrastar e soltar com o Explorador de Arquivos ou por linha de comando. Se o tamanho agregado dos arquivos que estão sendo copiados for maior que 10 GB, recomendamos que você use um programa de cópia em massa, como Robocopy ou rsync. As ferramentas de cópia em massa tentam novamente a operação de cópia para erros intermitentes e fornecem resiliência adicional. Se estiver usando o armazenamento de BLOBs via REST, AzCopy ou Gerenciador de Armazenamento do Azure poderá ser usado.
- Se o compartilhamento associado ao contêiner de armazenamento do Azure carregar blobs que correspondam ao tipo de blob definido para o compartilhamento no momento da criação, esses blobs não serão atualizados. Por exemplo, crie um compartilhamento de blob de blocos no dispositivo. Associe o compartilhamento de um contêiner de nuvem existente que tenha os blobs de páginas. Atualize esse compartilhamento para baixar os arquivos. Modifique alguns dos arquivos atualizados que já estão armazenados como blobs de páginas na nuvem. Você verá falhas de carregamento.
- Depois que um arquivo é criado nos compartilhamentos, não há suporte para a renomeação do arquivo.
- A exclusão de um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.
- Se estiver usando rsync para copiar dados, a `rsync -a` opção não terá suporte.
