---
title: Camada Premium para Azure Data Lake Storage | Microsoft Docs
description: Usar o nível de desempenho premium com Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324221"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Camada Premium para Azure Data Lake Storage

O Azure Data Lake Storage Gen2 agora dá suporte ao [nível de desempenho premium](storage-blob-performance-tiers.md#premium-performance). O nível de desempenho Premium é ideal para aplicativos de análise de Big Data e cargas de trabalho que exigem baixa latência consistente e têm um grande número de transações.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Cargas de trabalho que podem se beneficiar do nível de desempenho premium

As cargas de trabalho de exemplo incluem cargas de trabalho interativas, IoT, análise de streaming, inteligência artificial e aprendizado de máquina. 

**Cargas de trabalho interativas** 

Essas cargas de trabalho exigem atualizações instantâneas e comentários do usuário, como aplicativos de comércio eletrônico e de mapeamento, aplicativos de vídeo interativos, etc. Por exemplo, em um aplicativo de comércio eletrônico, os itens exibidos com menos frequência provavelmente não são armazenados em cache. No entanto, eles devem ser exibidos instantaneamente para o cliente sob demanda. Como outro exemplo, cientistas de dados, analistas e desenvolvedores podem derivar percepções sensíveis ao tempo ainda mais rápido executando consultas em dados armazenados em uma conta que usa o nível de desempenho premium. 

**Análise de IoT/streaming** 

Em um cenário de IoT, muitas operações de gravação menores podem ser enviadas para a nuvem a cada segundo. Grandes quantidades de dados podem ser ingeridas, agregadas para fins de análise e, em seguida, excluídas quase imediatamente. Os altos recursos de ingestão do nível de desempenho premium o tornam eficiente para esse tipo de carga de trabalho. 

**Inteligência artificial/aprendizado de máquina (ia/ML)** 

IA/ML lida com o consumo e o processamento de diferentes tipos de dados, como visuais, fala e texto. Esse tipo de computação de alto desempenho de carga de trabalho lida com grandes quantidades de dados que exigem resposta rápida e tempos de ingestão eficientes para análise de dados. 

## <a name="cost-effectiveness"></a>Custo-benefício

O nível de desempenho Premium tem um custo de armazenamento maior, mas um custo de transação menor em comparação com o nível de desempenho padrão. Se seus aplicativos e cargas de trabalho executarem um grande número de transações, o nível de desempenho premium poderá ser econômico.

A tabela a seguir demonstra o custo-benefício da camada Premium para Azure Data Lake Storage. Cada coluna representa o número de transações em um mês.  Cada linha representa a porcentagem de transações que são transações de leitura. Cada célula na tabela mostra a porcentagem de redução de custos associada a uma porcentagem de transação de leitura e o número de transações executadas. 

Por exemplo, supondo que sua conta esteja na região leste dos EUA 2, o número de transações com sua conta exceda próximos e 70% dessas transações são transações de leitura, o nível de desempenho Premium é mais econômico.

> [!div class="mx-imgBorder"]
> ![a imagem é exibida aqui](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Se preferir avaliar a eficiência de custo com base no número de transações por segundo para cada TB de dados, você poderá usar os títulos de coluna que aparecem na parte inferior da tabela.

Para obter mais informações sobre preços, consulte a página de [preços do Azure data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/) .

## <a name="feature-availability"></a>Disponibilidade de recursos 

Alguns recursos de armazenamento de BLOBs podem não estar disponíveis ou podem ter apenas suporte parcial com o nível de desempenho premium. Para obter uma lista completa, consulte [recursos de armazenamento de BLOBs disponíveis em Azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md). Em seguida, examine uma lista de [problemas conhecidos](data-lake-storage-known-issues.md) para avaliar qualquer lacuna na funcionalidade.

## <a name="enabling-the-premium-performance-tier"></a>Habilitando o nível de desempenho premium 

Você pode usar a camada Premium para Azure Data Lake Storage criando uma conta BlockBlobStorage com a configuração de **namespace hierárquico** **habilitada**. Para obter orientação completa, consulte [criar uma conta de conta do BlockBlobStorage](storage-blob-create-account-block-blob.md) .

Ao criar a conta, certifique-se de escolher a opção de desempenho **Premium** e o tipo de conta **BlockBlobStorage** .

> [!div class="mx-imgBorder"]
> ![Criar blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Habilite a configuração de **namespace hierárquico** na guia **avançado** da página **criar conta de armazenamento** . Você deve habilitar essa configuração ao criar a conta. Você não pode habilitá-lo posteriormente.

A imagem a seguir mostra essa configuração na página **criar conta de armazenamento** .

> [!div class="mx-imgBorder"]
> ![Configuração de namespace hierárquico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Disponibilidade regional

A camada Premium para Azure Data Lake Storage está disponível nas regiões a seguir.

|Região|Redundância|
|--|--|
|Leste dos EUA|LRS, ZRS|
|Leste dos EUA 2|LRS, ZRS|
|Centro dos EUA|LRS|
|Oeste dos EUA|LRS|
|Oeste dos EUA 2|LRS, ZRS|
|Centro-Oeste dos EUA|LRS|
|Centro-Sul dos Estados Unidos|LRS|
|Canadá Central|LRS|
|Leste do Canadá|LRS|
|Norte da Europa|LRS, ZRS|
|Europa Ocidental|LRS, ZRS|
|Sul do Reino Unido|LRS|
|Oeste do Reino Unido|LRS|
|França Central|LRS|
|Leste da Ásia|LRS|
|Coreia Central|LRS|
|Sul da Coreia|LRS|
|Índia Central|LRS|
|Oeste da Índia|LRS|
|Norte dos EAU|LRS|
|Leste do Japão|LRS|
|Oeste do Japão|LRS|
|Sudeste da Ásia|LRS, ZRS|
|Leste da Austrália|LRS, ZRS|
|Sudeste da Austrália|LRS|
|Sul do Brasil|LRS|

## <a name="next-steps"></a>Próximas etapas

Use a camada Premium para Azure Data Lake Storage com seu serviço de análise favorito, como Azure Databricks, Azure HDInsight e Azure Synapse Analytics. 

- [Tutorial: Azure Data Lake Storage Gen2, Azure Databricks e Spark](data-lake-storage-use-databricks-spark.md) 
- [Usar Azure data Lake Storage Gen2 com clusters do Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) Atualmente, o HDInsight dá suporte a uma conta que usa o nível de desempenho premium junto com um cluster HBase com gravações aceleradas habilitadas.
- [Início Rápido: Criar um workspace do Synapse](../../synapse-analytics/quickstart-create-workspace.md)

