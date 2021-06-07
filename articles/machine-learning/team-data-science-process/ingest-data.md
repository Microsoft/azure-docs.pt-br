---
title: Carregar dados em ambientes de armazenamento do Azure-processo de ciência de dados de equipe
description: Saiba mais sobre como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452669"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>Carregar dados em ambientes de armazenamento para análise

O processo de ciência de dados de equipe exige que os dados sejam ingeridos ou carregados na maneira mais apropriada em cada estágio. Os destinos de dados podem incluir o armazenamento de BLOBs do Azure, bancos de SQL Azure, SQL Server na VM do Azure, HDInsight (Hadoop), Azure Synapse Analytics e Azure Machine Learning. 

Os artigos a seguir descrevem como ingerir dados em vários ambientes de destino nos quais os dados são armazenados e processados.

* Para/Do [Armazenamento de Blobs do Azure](move-azure-blob.md)
* Para [SQL Server na VM do Azure](move-sql-server-virtual-machine.md)
* Para o [banco de dados SQL do Azure](move-sql-azure.md)
* Para [Tabelas do Hive](move-hive-tables.md)
* Para [Tabelas particionadas SQL](parallel-load-sql-partitioned-tables.md)
* Do [SQL Server local](move-sql-azure-adf.md)

As necessidades técnicas e de negócios, bem como o local inicial, o formato e o tamanho dos dados, determinarão o melhor plano de ingestão de dados. Não é incomum que um plano melhor tenha várias etapas. Essa sequência de tarefas pode incluir, por exemplo, a exploração de dados, pré-processamento, limpar, redução da resolução e treinamento de modelo.  Azure Data Factory é um recurso recomendado do Azure para orquestrar a movimentação e a transformação de dados.
