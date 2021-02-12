---
title: Camadas de desempenho de armazenamento de blobs de blocos — armazenamento do Azure
description: Discute a diferença entre as camadas de desempenho premium e Standard para o armazenamento de blobs de blocos do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: cf0b5d29e0dc375a07fe024ef0763c8200275055
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880701"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Níveis de desempenho para armazenamento de blobs de blocos

À medida que as empresas implantam aplicativos nativos de nuvem sensíveis ao desempenho, é importante ter opções de armazenamento de dados econômico em diferentes níveis de desempenho.

O armazenamento de blobs de blocos do Azure oferece dois níveis de desempenho diferentes:

- **Premium**: otimizado para altas taxas de transação e latência de armazenamento consistente de um dígito
- **Standard**: otimizado para alta capacidade e alta taxa de transferência

As seguintes considerações se aplicam a diferentes níveis de desempenho:

| Área |Desempenho padrão  |Desempenho premium  |
|---------|---------|---------|
|Disponibilidade de região     |   Todas as regiões      | Em [selecionar regiões](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|[Tipos de conta de armazenamento](../common/storage-account-overview.md#types-of-storage-accounts) com suporte     |     Uso geral v2, BlobStorage, uso geral v1    |    BlockBlobStorage     |
|Dá suporte a [blobs de blocos de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Sim, em mais de 4 tamanhos de PutBlock ou PutBlob de MiB     |    Sim, em mais de 256 KiB PutBlock ou tamanhos de PutBlob    |
|Redundância     |     Consulte [tipos de contas de armazenamento](../common/storage-account-overview.md#types-of-storage-accounts)   |  Atualmente dá suporte apenas ao LRS (armazenamento com redundância local) e ao ZRS (armazenamento de Redudant de zona)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> O ZRS (armazenamento com redundância de zona) está disponível em regiões selecionadas para contas de armazenamento de blob de bloco de desempenho premium.</div>

Em relação ao custo, o desempenho premium fornece preços otimizados para aplicativos com altas taxas de transações para ajudar a [reduzir o custo total de armazenamento](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) para essas cargas de trabalho.

## <a name="premium-performance"></a>Desempenho premium

O armazenamento de blobs de blocos de desempenho premium disponibiliza os dados por meio de hardware de alto desempenho. Os dados são armazenados em SSDs (unidades de estado sólido) que são otimizados para baixa latência. O SSDs fornece maior taxa de transferência em comparação com os discos rígidos tradicionais.

O armazenamento de desempenho Premium é ideal para cargas de trabalho que exigem tempos de resposta rápidos e consistentes. É melhor para cargas de trabalho que executam muitas transações pequenas. As cargas de trabalho de exemplo incluem:

- **Cargas de trabalho interativas**. Essas cargas de trabalho exigem atualizações instantâneas e comentários do usuário, como aplicativos de comércio eletrônico e de mapeamento. Por exemplo, em um aplicativo de comércio eletrônico, os itens exibidos com menos frequência provavelmente não são armazenados em cache. No entanto, eles devem ser exibidos instantaneamente para o cliente sob demanda.

- **Análise**. Em um cenário de IoT, muitas operações de gravação menores podem ser enviadas para a nuvem a cada segundo. Grandes quantidades de dados podem ser executadas, agregadas para fins de análise e, em seguida, excluídas quase imediatamente. Os altos recursos de ingestão do armazenamento de blobs de blocos Premium o tornam eficiente para esse tipo de carga de trabalho.

- **Inteligência artificial/aprendizado de máquina (ia/ml)**. IA/ML lida com o consumo e o processamento de diferentes tipos de dados, como visuais, fala e texto. Esse tipo de computação de alto desempenho de carga de trabalho lida com grandes quantidades de dados que exigem resposta rápida e tempos de ingestão eficientes para análise de dados.

- **Transformação de dados**. Processos que exigem edição constante, modificação e conversão de dados exigem atualizações instantâneas. Para uma representação de dados precisa, os consumidores desses dados devem ver essas alterações refletidas imediatamente.

## <a name="standard-performance"></a>Desempenho padrão

O desempenho padrão dá suporte a diferentes [níveis de acesso](storage-blob-storage-tiers.md) para armazenar dados da maneira mais econômica. Ele é otimizado para alta capacidade e alta taxa de transferência em conjuntos de dados grandes.

- **Conjuntos de backup e recuperação de desastres**. O armazenamento de desempenho padrão oferece camadas econômicas, tornando-o um caso de uso perfeito para conjuntos de dados de recuperação de desastres de curto e longo prazo, backups secundários e arquivamento de data de conformidade.

- **Conteúdo de mídia**. Imagens e vídeos geralmente são acessados com frequência quando criados e armazenados pela primeira vez, mas esse tipo de conteúdo é usado com menos frequência, pois ele é mais antigo. O armazenamento de desempenho padrão oferece camadas adequadas para as necessidades de conteúdo de mídia. 

- **Processamento de dados em massa**. Esses tipos de cargas de trabalho são adequados para o armazenamento Standard porque exigem armazenamento econômico de alta taxa de transferência em vez de baixa latência consistente. Conjuntos de grandes volumes de valores brutos são preparados para processamento e eventualmente migram para camadas mais friadas.

## <a name="migrate-from-standard-to-premium"></a>Migrar do Standard para o Premium

Não é possível converter uma conta de armazenamento de desempenho padrão existente em uma conta de armazenamento de blobs de blocos com desempenho premium. Para migrar para uma conta de armazenamento de desempenho premium, você deve criar uma conta do BlockBlobStorage e migrar os dados para a nova conta. Para obter mais informações, consulte [criar uma conta do BlockBlobStorage](storage-blob-create-account-block-blob.md).

Para copiar BLOBs entre contas de armazenamento, você pode usar a versão mais recente da ferramenta de linha de comando [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) . Outras ferramentas, como Azure Data Factory, também estão disponíveis para movimentação e transformação de dados.

## <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob

O gerenciamento do ciclo de vida do armazenamento de BLOBs oferece uma política avançada baseada em regras:

- **Premium**: expirar dados no final de seu ciclo de vida.
- **Standard**: dados de transição para a melhor camada de acesso e expiram dados no final de seu ciclo de vida.

Para saber mais, consulte [gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md).

Não é possível mover dados armazenados em uma conta de armazenamento de blob de blocos Premium entre camadas frequentes, esporádicas e de arquivo morto. No entanto, você pode copiar blobs de uma conta de armazenamento de blobs de blocos para a camada de acesso quente em uma conta *diferente* . Para copiar dados para uma conta diferente, use o [bloco Put da API de URL](/rest/api/storageservices/put-block-from-url) ou [AzCopy V10](../common/storage-use-azcopy-v10.md). O **bloco Put da API de URL copia de** forma síncrona os dados no servidor. A chamada é concluída somente depois que todos os dados são movidos do local do servidor original para o local de destino.

## <a name="next-steps"></a>Próximas etapas

Avalie o Hot, o frio e o arquivo em contas de armazenamento de BLOBs e GPv2.

- [Saiba mais sobre os dados de blob reidratar da camada de arquivo](storage-blob-rehydration.md)
- [Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](./monitor-blob-storage.md)
- [Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
