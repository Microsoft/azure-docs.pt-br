---
title: Aceleração de consulta Azure Data Lake Storage
description: A aceleração de consulta permite que aplicativos e estruturas de análise otimizem drasticamente o processamento de dados recuperando apenas os dados necessários para uma operação de processamento.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: dc1d217dba64c36aa219abbd4d2220a494347689
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912749"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Aceleração de consulta Azure Data Lake Storage

A aceleração de consulta permite que aplicativos e estruturas de análise otimizem drasticamente o processamento de dados recuperando apenas os dados necessários para executar uma determinada operação. Isso reduz o tempo e a capacidade de processamento necessários para obter informações críticas sobre os dados armazenados.

## <a name="overview"></a>Visão geral

A aceleração de consulta aceita *predicados* de filtragem e *projeções de coluna* que permitem que os aplicativos filtrem linhas e colunas no momento em que os dados são lidos do disco. Somente os dados que atendem às condições de um predicado são transferidos pela rede para o aplicativo. Isso reduz o custo de computação e a latência da rede.  

Você pode usar o SQL para especificar os predicados de filtro de linha e as projeções de coluna em uma solicitação de aceleração de consulta. Uma solicitação processa apenas um arquivo. Portanto, os recursos relacionais avançados do SQL, como junções e agrupamento por agregações, não têm suporte. A aceleração de consulta dá suporte a CSV e a dados formatados em JSON como entrada para cada solicitação.

O recurso de aceleração de consulta não está limitado a Data Lake Storage (contas de armazenamento que têm o namespace hierárquico habilitado neles). A aceleração de consulta é totalmente compatível com os BLOBs nas contas de armazenamento que **não** têm um namespace hierárquico habilitado neles. Isso significa que você pode obter a mesma redução na latência da rede e nos custos de computação ao processar dados que você já armazenou como BLOBs nas contas de armazenamento.

Para obter um exemplo de como usar a aceleração de consulta em um aplicativo cliente, consulte [filtrar dados usando a aceleração de consulta Azure data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Fluxo de dados

O diagrama a seguir ilustra como um aplicativo típico usa a aceleração de consulta para processar dados.

> [!div class="mx-imgBorder"]
> ![Visão geral da aceleração de consulta](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. O aplicativo cliente solicita dados de arquivo especificando predicados e projeções de coluna.

2. A aceleração de consulta analisa a consulta SQL especificada e distribui o trabalho para analisar e filtrar dados.

3. Os processadores lêem os dados do disco, analisa os dados usando o formato apropriado e filtra os dados aplicando os predicados especificados e as projeções de coluna.

4. A aceleração de consulta combina os fragmentos de resposta para transmitir de volta para o aplicativo cliente.

5. O aplicativo cliente recebe e analisa a resposta transmitida. O aplicativo não precisa filtrar dados adicionais e pode aplicar o cálculo ou a transformação desejada diretamente.

## <a name="better-performance-at-a-lower-cost"></a>Melhor desempenho a um custo menor

A aceleração de consulta otimiza o desempenho, reduzindo a quantidade de dados que são transferidos e processados pelo seu aplicativo.

Para calcular um valor agregado, os aplicativos normalmente recuperam **todos** os dados de um arquivo e, em seguida, processam e filtram os dados localmente. Uma análise dos padrões de entrada/saída para cargas de trabalho de análise revela que os aplicativos normalmente exigem apenas 20% dos dados que eles lêem para executar qualquer cálculo específico. Essa estatística é verdadeira mesmo após a aplicação de técnicas, como a [remoção de partição](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md#hive-partitioning). Isso significa que 80% desses dados são desnecessariamente transferidos pela rede, analisados e filtrados por aplicativos. Esse padrão, essencialmente projetado para remover dados desnecessários, incorre em um custo de computação significativo.  

Embora o Azure retenha uma rede líder do setor, em termos de taxa de transferência e latência, a transferência desnecessária de dados pela rede ainda é dispendiosa para o desempenho do aplicativo. Ao filtrar os dados indesejados durante a solicitação de armazenamento, a aceleração de consulta elimina esse custo.

Além disso, a carga de CPU necessária para analisar e filtrar dados desnecessários exige que seu aplicativo provisione um número maior e VMs maiores para fazer seu trabalho. Ao transferir essa carga de computação para aceleração de consulta, os aplicativos podem obter uma economia de custo significativa.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplicativos que podem se beneficiar da aceleração de consulta

A aceleração de consulta foi projetada para estruturas de análise distribuídas e aplicativos de processamento de dados. 

Estruturas de análise distribuída, como Apache Spark e Apache Hive, incluem uma camada de abstração de armazenamento dentro da estrutura. Esses mecanismos também incluem otimizadores de consulta que podem incorporar o conhecimento dos recursos de serviço de e/s subjacentes ao determinar um plano de consulta ideal para consultas de usuário. Essas estruturas estão começando a integrar a aceleração de consulta. Como resultado, os usuários dessas estruturas verão latência de consulta aprimorada e um menor custo total de propriedade sem precisar fazer nenhuma alteração nas consultas. 

A aceleração de consulta também foi projetada para aplicativos de processamento de dados. Esses tipos de aplicativos normalmente executam transformações de dados em grande escala que podem não levar diretamente a insights de análise para que nem sempre usem estruturas de análise distribuídas estabelecidas. Esses aplicativos geralmente têm uma relação mais direta com o serviço de armazenamento subjacente para que possam se beneficiar diretamente de recursos como aceleração de consulta. 

Para obter um exemplo de como um aplicativo pode integrar a aceleração de consulta, consulte [filtrar dados usando Azure data Lake Storage aceleração de consulta](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Preços

Devido à maior carga de computação dentro do serviço de Azure Data Lake Storage, o modelo de preços para usar a aceleração de consulta difere do modelo de transação de Azure Data Lake Storage normal. A aceleração de consulta cobra um custo para a quantidade de dados verificados, bem como um custo para a quantidade de dados retornados ao chamador. Para obter mais informações, consulte [preços de Azure data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Apesar da alteração no modelo de cobrança, o modelo de preços da aceleração de consulta foi projetado para reduzir o custo total de propriedade de uma carga de trabalho, considerando a redução dos custos de VM muito mais caros.

## <a name="next-steps"></a>Próximas etapas

- [Filtrar dados usando a aceleração de consulta Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md)
- [Referência da linguagem SQL de aceleração de consulta](query-acceleration-sql-reference.md)