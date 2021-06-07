---
title: Introdução ao Azure Data Lake Storage Gen2
description: Leia uma introdução ao Azure Data Lake Storage Gen2. Conheça os principais recursos. Examine os recursos de armazenamento de Blobs compatíveis, as integrações de serviço do Azure e as plataformas.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879891"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introdução ao Azure Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 é um conjunto de recursos dedicados à análise de Big Data, criado sobre o [Armazenamento de Blobs do Azure](storage-blobs-introduction.md). 

O Data Lake Storage Gen2 converge as funcionalidades do [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) com o Armazenamento de Blobs do Azure. Por exemplo, o Data Lake Storage Gen2 fornece semântica do sistema de arquivos, segurança em nível de arquivo e escala. Como essas funcionalidades são criadas no Armazenamento de Blobs, você também obterá um armazenamento em camadas de baixo custo, com funcionalidades de alta disponibilidade/recuperação de desastre.

## <a name="designed-for-enterprise-big-data-analytics"></a>Projetado para análise de big data da empresa

O Data Lake Storage Gen2 torna o armazenamento do Azure a fundação para a criação de data lakes empresariais no Azure. Projetado desde o início para atender a vários petabytes de informações enquanto mantém centenas de gigabits de taxa de transferência, o Data Lake Storage Gen2 permite que você gerencie de uma maneira fácil grandes quantidades de dados.

Uma parte fundamental do Data Lake Storage Gen2 é a adição de um [namespace hierárquico](data-lake-storage-namespace.md) para armazenamento de Blobs. O namespace hierárquico organiza objetos/arquivos em uma hierarquia de diretórios para acesso eficiente a dados. Uma convenção de nomenclatura de armazenamento de objetos comum usa barras no nome para imitar uma estrutura de diretórios hierárquica. Essa estrutura se torna real com o Data Lake Storage Gen2. Operações como renomear ou excluir um diretório se tornam operações de metadados atômicas únicas no diretório. Não é necessário enumerar e processar todos os objetos que compartilham o prefixo de nome do diretório.

O Data Lake Storage Gen2 se baseia no Armazenamento de Blobs e aprimora o desempenho, o gerenciamento e a segurança das seguintes maneiras:

-   **O desempenho** é otimizado porque você não precisa copiar ou transformar dados como um pré-requisito para análise. Em comparação com o namespace simples do Armazenamento de Blobs, o namespace hierárquico aprimora muito o desempenho das operações de gerenciamento de diretório, o que aprimora o desempenho geral do trabalho.

-   **Gerenciamento** é mais fácil porque você pode organizar e manipular arquivos por meio de diretórios e subdiretórios.

-   **Segurança** é aplicável porque você pode definir as permissões POSIX em arquivos individuais ou diretórios.

Além disso, o Data Lake Storage Gen2 é muito econômico, pois se baseia no [Armazenamento de Blobs do Azure](storage-blobs-introduction.md) de baixo custo. Os recursos adicionais reduzem ainda mais o custo total de propriedade para executar a análise de big data no Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Principais recursos do Data Lake Storage Gen2

-   **Acesso compatível com Hadoop**: O Azure Data Lake Storage Gen2 permite gerenciar e acessar dados como você faria com um [Sistema de Arquivos Distribuído do Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). O novo [driver ABFS](data-lake-storage-abfs-driver.md) (usado para acessar dados) está disponível em todos os ambientes do Apache Hadoop. Entre esses ambientes estão o [Azure HDInsight](../../hdinsight/index.yml) *, o* [Azure Databricks](/azure/databricks/) e o [Azure Synapse Analytics](../../synapse-analytics/index.yml).

-   **Um superconjunto de permissões POSIX**: O modelo de segurança do Data Lake Gen2 dá suporte a permissões POSIX e ACL juntamente com alguma granularidade adicional específica para o Data Lake Storage Gen2. As configurações podem ser definidas por meio do Gerenciador de Armazenamento ou por meio de estruturas como Hive e Spark.

-   **Econômico**: O Data Lake Storage Gen2 oferece transações e capacidade de armazenamento de baixo custo. Recursos como o [ciclo de vida do Armazenamento de Blobs do Azure](storage-lifecycle-management-concepts.md) otimizam os custos conforme ocorrem as transições dos dados durante o ciclo de vida.

-   **Driver otimizado**: O driver ABFS é [otimizado especificamente](data-lake-storage-abfs-driver.md) para análise de Big Data. As APIs REST correspondentes são exibidas pelo ponto de extremidade, `dfs.core.windows.net`.

### <a name="scalability"></a>Escalabilidade

O Armazenamento do Azure é dimensionável por design, independentemente de você acessar via Data Lake Storage Gen2 ou interfaces de armazenamento do Blob. É capaz de armazenar e servir *muitos exabytes de dados*. Essa quantidade de armazenamento está disponível com taxa de transferência medida em gigabits por segundo (Gbps) em altos níveis de operações de entrada/saída por segundo (IOPS). O processamento é executado em latências por solicitação quase constantes que são medidas nos níveis de serviço, conta e arquivo.

### <a name="cost-effectiveness"></a>Custo-benefício

Como o Data Lake Storage Gen2 é baseado no Armazenamento de Blobs do Azure, a capacidade de armazenamento e os custos de transação são menores. Ao contrário de outros serviços de armazenamento em nuvem, você não precisa migrar nem transformar seus dados para analisá-los. Para obter mais informações sobre preços, confira [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage).

Além disso, recursos como o [namespace hierárquico](data-lake-storage-namespace.md) melhoram significativamente o desempenho geral de muitas tarefas de análise. Essa melhoria no desempenho significa que você precisa de menos poder computacional para processar a mesma quantidade de dados, resultando em um custo total de propriedade (TCO) mais baixo para o trabalho analítico de ponta a ponta.

### <a name="one-service-multiple-concepts"></a>Um único serviço, vários conceitos

Como o Data Lake Storage Gen2 é baseado no Armazenamento de Blobs do Azure, vários conceitos podem descrever as mesmas coisas compartilhadas.

A seguir estão as entidades equivalentes, conforme descrito pelos diferentes conceitos. Salvo especificado em contrário, essas entidades são sinônimos diretos:

| Conceito                                | Organização de nível superior | Organização de nível inferior                                            | Contêiner de dados |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobs – Armazenamento de objetos de uso geral | Contêiner              | Diretório virtual (SDK somente – não fornece a manipulação atômica) | Blob           |
| Azure Data Lake Storage Gen2 – Armazenamento de Análise          | Contêiner            | Diretório                                                           | Arquivo           |

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

Recursos do Armazenamento de Blobs, como [log de diagnósticos](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md) e [políticas de gerenciamento do ciclo de vida do Armazenamento de Blobs](storage-lifecycle-management-concepts.md), estão disponíveis para sua conta. 

Para obter uma lista de recursos do Armazenamento de Blobs com suporte, confira [Recursos do Armazenamento de Blobs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviço do Azure com suporte

O Data Lake Storage Gen2 dá suporte a vários serviços do Azure. Use-os para ingerir dados, realizar análises e criar representações visuais. Para obter uma lista de serviços do Azure com suporte, confira [Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre suportam o Data Lake Storage Gen2. Para obter uma lista completa, confira [Plataformas de software livre que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)