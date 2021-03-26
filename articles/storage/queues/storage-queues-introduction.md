---
title: Introdução ao Armazenamento de Filas do Azure – Armazenamento do Azure
description: Veja uma introdução ao Armazenamento de Filas do Azure, um serviço para armazenar um grande volume de mensagens. Um serviço Armazenamento de Filas contém um formato de URL, uma conta de armazenamento, uma fila e uma mensagem.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8c5c97fbb72934dd99ec784ccf8e08eec059c31b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590572"
---
# <a name="what-is-azure-queue-storage"></a>O que é o Armazenamento de Filas do Azure?

O armazenamento de Filas do Azure é um serviço usado para armazenar grandes quantidades de mensagens. Você acessa as mensagens em qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB. Uma fila pode conter milhões de mensagens, até o limite da capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processamento assíncrono.

## <a name="queue-storage-concepts"></a>Conceitos de Armazenamento de Filas

O Armazenamento de Filas contém os seguintes componentes:

![Diagrama mostrando a relação entre uma conta de armazenamento, as filas e as mensagens.](./media/storage-queues-introduction/queue1.png)

- **Formato da URL:** as filas são acessadas usando o seguinte formato de URL:

  `https://<storage account>.queue.core.windows.net/<queue>`

  A URL a seguir endereça um fila no diagrama:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Conta de armazenamento:** Todo o acesso ao Armazenamento do Azure ocorre por meio de uma conta de armazenamento. Para obter informações sobre a capacidade da conta de armazenamento, confira [Metas de escalabilidade e desempenho das contas de Armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Fila:** uma fila contém um conjunto de mensagens. O nome da fila **precisa** estar em letras minúsculas. Para saber mais sobre nomear filas, confira [Nomenclatura de filas e metadados](/rest/api/storageservices/naming-queues-and-metadata).

- **Mensagem:** uma mensagem, em qualquer formato, de até 64 KB. Antes da versão 2017-07-29, a vida útil máxima permitida era de sete dias. Para a versão 2017-07-29 ou posterior, a vida útil máxima pode ser qualquer número positivo ou -1, indicando que a mensagem não expira. Se esse parâmetro for omitido, a vida útil padrão será de sete dias.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma conta de armazenamento](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Introdução ao Armazenamento de Filas usando o .NET](storage-dotnet-how-to-use-queues.md)
- [Introdução ao Armazenamento de Filas usando Java](storage-java-how-to-use-queue-storage.md)
- [Introdução ao Armazenamento de Filas usando Python](storage-python-how-to-use-queue-storage.md)
- [Introdução ao Armazenamento de Filas usando Node.js](storage-nodejs-how-to-use-queues.md)
