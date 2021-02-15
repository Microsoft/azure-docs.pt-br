---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026171"
---
## <a name="what-is-queue-storage"></a>O que é o armazenamento de filas?

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. O armazenamento de filas é geralmente usado para criar uma pendência de trabalho para processar de forma assíncrona.

## <a name="queue-service-concepts"></a>Conceitos do serviço Fila

O serviço Fila do Azure contém os seguintes componentes:

![Componentes do serviço Fila do Azure](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Conta de armazenamento:** Todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Para saber mais sobre as contas de armazenamento, confira [Visão geral da conta de armazenamento](../articles/storage/common/storage-account-overview.md).
* **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens devem estar em uma fila. Observe que o nome da fila deve estar em letras minúsculas. Para saber mais sobre filas de nomenclatura, confira [Nomenclatura de filas e metadados](/rest/api/storageservices/Naming-Queues-and-Metadata).
* **Mensagem:** uma mensagem, em qualquer formato, de até 64 KB. O tempo máximo que uma mensagem pode ficar na fila é de sete dias. Para a versão 2017-07-29 ou posterior, a vida útil máxima pode ser qualquer número positivo ou -1, indicando que a mensagem não expira. Se esse parâmetro for omitido, a vida útil padrão será de sete dias.
* **Formato da URL:** As filas são endereçáveis usando o seguinte formato de URL: http:// `<storage account>` . Queue.Core.Windows.net/`<queue>`

    A URL a seguir endereça um fila no diagrama:

    `http://myaccount.queue.core.windows.net/incoming-orders`