---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/01/2021
ms.author: tamram
ms.openlocfilehash: 16da73fe453760e2dc84e7d683c3a16c12b8a06f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218593"
---
| Recurso | Destino |
|-|-|
| Tamanho máximo de contêiner de blob único | Igual à capacidade máxima da conta de armazenamento |
| Número máximo de blocos em um blob de blocos ou de acréscimo | 50.000 blocos |
| Tamanho máximo de um bloco em um blob de blocos | 4000 MiB |
| Tamanho máximo de um blob de blocos | 50.000 X 4000 MiB (aproximadamente 190,7 TiB) |
| Tamanho máximo de um bloco em um blob de acréscimo | 4 MiB |
| Tamanho máximo de um blob de acréscimo | 50.000 X 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de um blob de páginas | 8 TiB<sup>2</sup> |
| Número máximo de políticas de acesso armazenadas por contêiner de blob | 5 |
| Taxa de solicitação de destino para um blob único | Até 500 solicitações por segundo |
| Taxa de transferência de destino para um blob de páginas único | Até 60 MiB por segundo<sup>2</sup> |
| Taxa de transferência de destino para um blob de blocos único | Até os limites de entrada/saída da conta de armazenamento<sup>1</sup> |

<sup>1</sup> A taxa de transferência para um blob único depende de vários fatores, incluindo, entre outros: simultaneidade, tamanho da solicitação, nível de desempenho, velocidade da origem para uploads e de destino para downloads. Para aproveitar as melhorias de desempenho dos [blobs de blocos com alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), carregue blobs ou blocos maiores. Especificamente, chame a operação [Colocar Blob](/rest/api/storageservices/put-blob) ou [Colocar Bloco](/rest/api/storageservices/put-block) com um tamanho de bloco ou de blob superior a 4 MiB para contas de armazenamento padrão. Para o blob de blocos Premium ou para contas de armazenamento Data Lake Storage Gen2, use um tamanho de bloco ou de blob superior a 256 KiB.

<sup>2</sup> Os blobs de páginas ainda não são compatíveis em contas que tenham o **Namespace hierárquico** nelas.

A tabela a seguir descreve os tamanhos máximos de bloco e de blob permitidos pela versão do serviço.

| Versão do serviço | Tamanho máximo do bloco (via Colocar Bloco) | Tamanho máximo do blob (via Colocar Lista de Bloco) | Tamanho máximo do blob via operação de gravação única (via Colocar Blob) |
|-|-|-|-|
| Versão 12/12/2019 e posterior | 4000 MiB | Aproximadamente 190,7 TiB (4000 MiB X 50.000 blocos) | 5000 MiB (versão prévia) |
| Versão 31/05/2016 até a versão 07/07/2019 | 100 MiB | Aproximadamente 4,75 TiB (100 MiB X 50.000 blocos) | 256 MiB |
| Versões anteriores a 31/05/2016 | 4 MiB | Aproximadamente 195 GiB (4 MiB X 50.000 blocos) (versão prévia) | 64 MiB |
