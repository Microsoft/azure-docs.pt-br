---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/01/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e878ca23b9187fe3175ad0af1b4f27e59e1deef6
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509803"
---
### <a name="premium-performance-block-blob-storage"></a>Armazenamento de blob de bloco de desempenho Premium

Uma conta de armazenamento de blob de bloco do premium desempenho é otimizada para aplicativos que usam o intervalo de kilobyte, objetos menores. Ele é ideal para aplicativos que exigem armazenamento de baixa latência consistente ou altas taxas de transação. O armazenamento de blob de bloco de desempenho Premium foi projetado para dimensionar com seus aplicativos. Se você planeja implantar os aplicativos que exigem a centenas de milhares de solicitações por segundo ou petabytes de capacidade de armazenamento, entre em contato conosco enviando uma solicitação de suporte nas [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage"></a>Desempenho Premium FileStorage

[!INCLUDE [azure-storage-limits-filestorage](azure-storage-limits-filestorage.md)]

 Para o arquivo premium compartilham destinos de escala, consulte a [arquivos Premium dimensionar destinos](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) seção.

### <a name="premium-performance-page-blob-storage"></a>Armazenamento de blob de página de desempenho Premium

Desempenho Premium, uso geral v1 ou v2 contas de armazenamento têm as seguintes metas de escalabilidade:

| Capacidade total da conta                            | Largura de banda total para uma conta de armazenamento com redundância local                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Até 50 gigabits para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> Todos os dados (solicitações) que são enviados para uma conta de armazenamento

<sup>2</sup> Todos os dados (respostas) que são recebidos de uma conta de armazenamento

Se você estiver usando contas de armazenamento de desempenho premium para discos não gerenciados e seu aplicativo ultrapassa as metas de escalabilidade de uma única conta de armazenamento, você talvez queira migrar para discos gerenciados. Se você não deseja migrar discos gerenciados, crie seu aplicativo para usar várias contas de armazenamento. Em seguida, particione os dados nessas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 TB em várias VMs, separe-as entre duas contas de armazenamento. O limite para uma conta de armazenamento único premium é de 35 TB. Certifique-se de que uma conta de armazenamento único premium desempenho nunca tem mais de 35 TB de discos provisionados.