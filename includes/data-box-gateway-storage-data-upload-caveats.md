---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581465"
---
As limitações a seguir se aplicam aos dados conforme são movidos para o Azure.

- Sugerimos que mais de um dispositivo não deve gravar no mesmo contêiner.
- Se você tiver um objeto do Azure existente (como um BLOB ou um arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o dispositivo substituirá o arquivo na nuvem.
- Uma hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas de compartilhamento não é carregada nos contêineres de blob.
- Você pode copiar os dados usando arrastar e soltar com o explorador de arquivos ou por meio da linha de comando. Se o tamanho agregado dos arquivos que estão sendo copiados for maior que 10 GB, recomendamos que você use um programa de cópia em massa, como `Robocopy` ou `rsync` . As ferramentas de cópia em massa tentam novamente a operação de cópia para erros intermitentes e fornecem resiliência adicional.
- Se o compartilhamento associado ao contêiner de armazenamento do Azure carregar blobs que correspondam ao tipo de blob definido para o compartilhamento no momento da criação, esses blobs não serão atualizados. Por exemplo, se você criar um compartilhamento de blob de blocos no dispositivo, associe o compartilhamento a um contêiner de nuvem existente que tem blobs de páginas, atualize esse compartilhamento para baixar os arquivos e, em seguida, modifique alguns dos arquivos atualizados que já estão armazenados como BLOBs de páginas na nuvem. você verá falhas de carregamento.
- Depois que um arquivo é criado nos compartilhamentos, não há suporte para a renomeação do arquivo.
- A exclusão de um arquivo de um compartilhamento não exclui a entrada na conta de armazenamento.
- Se estiver usando `rsync` para copiar dados, a opção `rsync -a` não é compatível.