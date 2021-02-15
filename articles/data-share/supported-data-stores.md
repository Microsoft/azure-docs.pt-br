---
title: Armazenamentos de dados com suporte no Azure Data Share
description: Saiba mais sobre os armazenamentos de dados com suporte para uso no compartilhamento de dados do Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963671"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamentos de dados com suporte no Azure Data Share

O compartilhamento de dados do Azure fornece compartilhamento de dados aberto e flexível, incluindo a capacidade de compartilhar de e para armazenamentos de dados diferentes. Os provedores de dados podem compartilhar dados de um tipo de armazenamento de dados, e os consumidores de dados podem escolher um armazenamento de dados para receber os dados. 

Neste artigo, você aprenderá sobre o rico conjunto de armazenamentos de dados do Azure que o compartilhamento de dados do Azure dá suporte. Você também aprenderá como os provedores de dados e os consumidores de dados podem combinar diferentes armazenamentos de dados. 

## <a name="supported-data-stores"></a>Armazenamento de dados com suporte 

A tabela a seguir explica os armazenamentos de dados aos quais o compartilhamento de dados do Azure dá suporte. 

| Armazenamento de dados | Compartilhamento com base em instantâneos completos | Compartilhamento com base em instantâneos incrementais | Compartilhamento no local 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento do Blobs do Azure |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Banco de Dados SQL do Azure |✓ | | |
| Azure Synapse Analytics (antigo SQL Data Warehouse do Azure) |✓ | | |
| Pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Matriz de suporte do armazenamento de dados

O compartilhamento de dados do Azure permite que os consumidores de dados escolham um armazenamento de dados para aceitar dados. Por exemplo, os dados que são compartilhados do Azure SQL Database podem ser recebidos em Azure Data Lake Storage Gen2, banco de dados SQL do Azure ou Azure Synapse Analytics. Quando os clientes configuram um compartilhamento de dados de recebimento, eles podem escolher o formato para receber os dados. 

A tabela a seguir explica as combinações e opções que os consumidores de dados podem escolher quando aceitam e configuram um compartilhamento de dados. Para obter mais informações, consulte [configurar um mapeamento de conjunto de](how-to-configure-mapping.md)dados.

| Armazenamento de dados | Armazenamento de Blobs | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Banco de Dados SQL | Synapse Analytics (anteriormente SQL Data Warehouse) | Pool de SQL dedicado do Synapse Analytics (espaço de trabalho) | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Armazenamento de Blobs | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| Banco de Dados SQL | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (anteriormente SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Pool de SQL dedicado do Synapse Analytics (espaço de trabalho) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Compartilhamento por meio de uma conta de armazenamento
O compartilhamento de dados do Azure dá suporte ao compartilhamento de arquivos, pastas e sistemas de arquivos de Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Ele também dá suporte ao compartilhamento de BLOBs, pastas e contêineres do armazenamento de BLOBs do Azure. No momento, somente há suporte para BLOBs de blocos. 

Quando os sistemas de arquivos, os contêineres ou as pastas são compartilhados no compartilhamento baseado em instantâneo, os consumidores de dados podem optar por fazer uma cópia completa dos dados compartilhados. Ou podem usar o recurso de instantâneo incremental para copiar somente arquivos novos ou arquivos atualizados. 

Um instantâneo incremental é baseado na hora da última modificação dos arquivos. Os arquivos existentes que têm o mesmo nome que os arquivos nos dados recebidos são substituídos em um instantâneo. Os arquivos que são excluídos da origem não são excluídos no destino. 

Para obter mais informações, consulte [compartilhar e receber dados do armazenamento de BLOBs do Azure e Azure data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Compartilhamento por meio de uma fonte baseada em SQL
O compartilhamento de dados do Azure dá suporte ao compartilhamento de tabelas e modos de exibição do Azure SQL Database e do Azure Synapse Analytics (anteriormente SQL Data Warehouse do Azure). Ele dá suporte ao compartilhamento de tabelas do pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho). O compartilhamento do pool SQL sem servidor do Azure Synapse Analytics (espaço de trabalho) não tem suporte no momento. 

Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Storage Gen2 ou no armazenamento de BLOBs do Azure como um arquivo CSV ou arquivo parquet. Eles também podem aceitar os dados como tabelas no Azure SQL Database e no Azure Synapse Analytics.

Quando os consumidores aceitam dados no Azure Data Lake Storage Gen2 ou no armazenamento de BLOBs do Azure, os instantâneos completos substituem o conteúdo do arquivo de destino se o arquivo já existir. Quando os dados são recebidos em uma tabela e a tabela de destino ainda não existe, o compartilhamento de dados do Azure cria uma tabela SQL usando o esquema de origem. Se uma tabela de destino já existir e tiver o mesmo nome, ela será descartada e substituída pelo instantâneo completo mais recente. Atualmente, não há suporte para instantâneos incrementais.

Para obter mais informações, consulte [compartilhar e receber dados do banco de dados SQL do Azure e do Azure Synapse Analytics](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Compartilhar de Data Explorer
O compartilhamento de dados do Azure dá suporte à capacidade de compartilhar bancos de dados no local a partir de clusters do Azure Data Explorer. Um provedor de dados do pode compartilhar no nível do banco de dados ou do cluster. 

Quando os dados são compartilhados no nível do banco de dados, os clientes do podem acessar somente os bancos de dados compartilhados pelo provedor. Quando um provedor compartilha dados no nível do cluster, os consumidores de dados podem acessar todos os bancos de dado do cluster do provedor, incluindo quaisquer bancos de dados futuros criados pelo provedor de data.

Para acessar bancos de dados compartilhados, os consumidores de clientes precisam de seu próprio cluster de Data Explorer do Azure. Seu cluster deve estar no mesmo datacenter do Azure que o cluster de Data Explorer do Azure do provedor de dados. 

Quando uma relação de compartilhamento é estabelecida, o compartilhamento de dados do Azure cria um vínculo simbólico entre o cluster do provedor e o cluster do consumidor. Os dados que são ingeridos no cluster de origem usando o modo de lote são exibidos no cluster de destino em alguns minutos.

Para obter mais informações, consulte [compartilhar e receber dados do Azure data Explorer](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Próximas etapas

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .
