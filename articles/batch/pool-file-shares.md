---
title: Compartilhamento de arquivo do Azure para pools do Lote do Azure
description: Como montar um compartilhamento de Arquivos do Azure de nós de computação em um pool de Linux ou Windows no Lote do Azure.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735487"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Usar um compartilhamento de arquivos do Azure com um pool do Lote

Os [Arquivos do Azure](../storage/files/storage-files-introduction.md) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo SMB (Server Message Block) padrão no setor. Este artigo fornece informações e exemplos de código para montar e usar um compartilhamento de arquivos do Azure em nós de computação do pool.

## <a name="considerations-for-use-with-batch"></a>Considerações para uso com o Lote

* Considere usar um compartilhamento de arquivos do Azure quando houver pools que executam um número relativamente baixo de tarefas paralelas se usarem arquivos não Premium do Azure. Examine as [metas de desempenho e escalabilidade](../storage/files/storage-files-scale-targets.md) para determinar se os Arquivos do Azure (que usam uma conta do Armazenamento do Azure) devem ser usados, dado o tamanho esperado do pool e o número de arquivos de ativo. 

* Os compartilhamentos de arquivos do Azure são [econômicos](https://azure.microsoft.com/pricing/details/storage/files/) e podem ser configurados com replicação de dados para outra região, então são redundantes globalmente. 

* Você pode montar um compartilhamento de arquivos do Azure simultaneamente em um computador local. No entanto, certifique-se de entender as [implicações de simultaneidade](../storage/blobs/concurrency-manage.md) especialmente ao usar APIs REST.

* Veja também as [considerações de planejamento](../storage/files/storage-files-planning.md) gerais para compartilhamentos de arquivos do Azure.


## <a name="create-a-file-share"></a>Criar um compartilhamento de arquivos

[Crie um compartilhamento de arquivos](../storage/files/storage-how-to-create-file-share.md) em uma conta de armazenamento que esteja vinculada à sua conta do Lote ou em uma conta de armazenamento separada.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Montar um compartilhamento de arquivos do Azure em um pool do lote

Veja a documentação sobre como [montar um sistema de arquivos virtual em um pool do lote](virtual-file-mount.md).

## <a name="next-steps"></a>Próximas etapas

* Para outras opções de leitura e gravação de dados no Lote, confira [Manter a saída de trabalho e tarefa](batch-task-output.md).
* Veja também o kit de ferramentas [Shipyard do Lote](https://github.com/Azure/batch-shipyard), que inclui [receitas Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes) para implantar sistemas de arquivos para cargas de trabalho de contêiner do Lote.