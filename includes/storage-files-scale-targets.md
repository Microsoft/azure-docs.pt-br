---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449921"
---
| Resource | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos do Premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | Nenhum mínimo; pago conforme o uso | 100 giB; provisionado |
| Tamanho máximo de um compartilhamento de arquivos | 5 TiB (GA), 100 TiB (visualização) | 100 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| Máximo de IOPS por compartilhamento | 1\.000 IOPS (GA), 10.000 IOPS (visualização) | 100.000 IOPS |
| Número máximo de políticas de acesso armazenadas por arquivo compartilhar | 5 | 5 |
| Taxa de transferência de destino para um compartilhamento de arquivo único | Até 60 MiB/s (GA), até 300 MiB/s (visualização) | Ver os valores de entrada e saída de compartilhamento de arquivo do premium|
| Egresso máximo para um compartilhamento de arquivo único | Consulte a taxa de transferência de destino de compartilhamento de arquivo padrão | Até 6,204 MiB/s |
| Máximo de entrada para um compartilhamento de arquivo único | Consulte a taxa de transferência de destino de compartilhamento de arquivo padrão | Até 4.136 MiB/s |
| Máximo de identificadores abertos por arquivo | 2\.000 identificadores abertos | 2\.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2\.048 caracteres | 2\.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |