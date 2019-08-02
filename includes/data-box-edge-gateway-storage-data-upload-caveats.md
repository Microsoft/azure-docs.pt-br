---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172377"
---
As limitações a seguir se aplicam aos dados conforme são movidos para o Azure.

- Sugerimos que mais de um dispositivo não deve gravar no mesmo contêiner.
- Se você tiver um objeto existente do Azure (como um blob ou arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o dispositivo substituirá o arquivo na nuvem.
- Uma hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas de compartilhamento não é carregada nos contêineres de blob.
- Você pode copiar os dados usando arrastar e soltar com o Explorador de arquivos ou por meio da linha de comando. Se o tamanho agregado de arquivos que está sendo copiado for maior que 10 GB, recomendamos que você use um programa de cópia em massa, como o Robocopy ou rsync. As ferramentas de cópia em massa Repita a operação de cópia para erros intermitentes e oferecem resiliência adicional.
- Se o compartilhamento associado ao contêiner de armazenamento do Azure carregar blobs que correspondam ao tipo de blob definido para o compartilhamento no momento da criação, esses blobs não serão atualizados. Por exemplo, crie um compartilhamento de blob de blocos no dispositivo. Associe o compartilhamento de um contêiner de nuvem existente que tenha os blobs de páginas. Atualize esse compartilhamento para baixar os arquivos. Modifique alguns dos arquivos atualizados que já estão armazenados como blobs de páginas na nuvem. Você verá falhas de carregamento.
