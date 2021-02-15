---
title: Comparar opções de armazenamento para uso com clusters do Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como eles funcionam com o Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945859"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparar opções de armazenamento para uso com clusters do Azure HDInsight

Você pode escolher entre alguns serviços de armazenamento do Azure diferentes ao criar clusters HDInsight:

* [Armazenamento de BLOBs do Azure com o HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 com o HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 com o HDInsight](./overview-data-lake-storage-gen1.md)

Este artigo fornece uma visão geral desses tipos de armazenamento e de seus recursos exclusivos.

## <a name="storage-types-and-features"></a>Tipos de armazenamento e recursos

A tabela a seguir resume os serviços de armazenamento do Azure que têm suporte com versões diferentes do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de namespace | Serviços com suporte | Níveis de desempenho compatíveis | Camadas de acesso compatíveis | Versão do HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Uso geral V2 | Hierárquico (Filesystem) | Blob | Standard | Frequente, Esporádico, Arquivos | 3.6+ | Todos, exceto Spark 2,1 e 2,2|
|Armazenamento do Azure| Uso geral V2 | Objeto | Blob | Standard | Frequente, Esporádico, Arquivos | 3.6+ | Todos |
|Armazenamento do Azure| Uso geral V1 | Objeto | Blob | Standard | N/D | Todos | Todos |
|Armazenamento do Azure| Armazenamento de BLOBs * * | Objeto | Blob de blocos | Standard | Frequente, Esporádico, Arquivos | Todos | Todos |
|Azure Data Lake Storage Gen1| N/D | Hierárquico (Filesystem) | N/D | N/D | N/D | apenas 3,6 | Todos, exceto HBase |
|Armazenamento do Azure| Blob de blocos| Objeto | Blob de blocos | Premium | N/D| 3.6+ | Somente HBase com gravações aceleradas|
|Azure Data Lake Storage Gen2| Blob de blocos| Hierárquico (Filesystem) | Blob de blocos | Premium | N/D| 3.6+ | Somente HBase com gravações aceleradas|

* * Para clusters HDInsight, somente contas de armazenamento secundárias podem ser do tipo BlobStorage e o blob de página não é uma opção de armazenamento com suporte.

Para obter mais informações sobre os tipos de conta de armazenamento do Azure, consulte [visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre as camadas de acesso de armazenamento do Azure, consulte [armazenamento de BLOBs do Azure: camadas Premium (visualização), quente, fria e arquivo morto](../storage/blobs/storage-blob-storage-tiers.md)

Você pode criar clusters usando combinações de serviços para armazenamento primário e secundário opcional. A tabela a seguir resume as configurações de armazenamento de cluster que atualmente têm suporte no HDInsight:

| Versão do HDInsight | Armazenamento primário | Armazenamento secundário | Com suporte |
|---|---|---|---|
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Data Lake Storage Gen2 | Não |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Sim |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Não |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Sim |
| 3,6 | Data Lake Storage Gen1 | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Não |
| 4,0 | Data Lake Storage Gen1 | Qualquer | Não |
| 4,0 | Uso Geral v1, Uso Geral v2 | Data Lake Storage Gen1 | Não |

* = Isso pode ser um ou vários Data Lake Storage Gen2, desde que todas as configurações usem a mesma identidade gerenciada para acesso ao cluster.

> [!NOTE]
> Não há suporte para o armazenamento primário Data Lake Storage Gen2 para clusters Spark 2,1 ou 2,2.

## <a name="data-replication"></a>Replicação de dados

O Azure HDInsight não armazena dados do cliente. O principal meio de armazenamento para um cluster são suas contas de armazenamento associadas. Você pode anexar o cluster a uma conta de armazenamento existente ou criar uma nova conta de armazenamento durante o processo de criação do cluster. Se uma nova conta for criada, ela será criada como uma conta de LRS (armazenamento com redundância local) e atenderá aos requisitos de residência de dados em região, incluindo aqueles especificados na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net).

Você pode validar se o HDInsight está configurado corretamente para armazenar dados em uma única região, garantindo que a conta de armazenamento associada ao HDInsight seja LRS ou outra opção de armazenamento mencionada na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Próximas etapas

* [Visão geral do armazenamento do Azure no HDInsight](./overview-azure-storage.md)
* [Visão geral do Azure Data Lake Storage Gen1 no HDInsight](./overview-data-lake-storage-gen1.md)
* [Visão geral do Azure Data Lake Storage Gen2 no HDInsight](./overview-data-lake-storage-gen2.md)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
