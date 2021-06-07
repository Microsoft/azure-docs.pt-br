---
title: Diferenças do Azure Data Factory
description: Saiba como os recursos de integração de dados do Azure Synapse Analytics diferem dos Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 9cd3fc9353c684ec6617761616b958477ca83ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183634"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integração de dados no Azure Synapse Analytics versus Azure Data Factory

No Azure Synapse Analytics, os recursos de integração de dados, como pipelines Synapse e fluxos de dados, são baseados nos Azure Data Factory. Para obter mais informações, consulte [o que é Azure data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Recursos disponíveis no ADF & análise de Synapse do Azure

Verifique a tabela abaixo para obter a disponibilidade dos recursos:

| Categoria                 | Recurso    |  Fábrica de dados do Azure  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Usando o SSIS e o SSIS Integration Runtime | ✓ | ✗ |
|                          | Suporte para Integration Runtime entre regiões (fluxos de dados) | ✓ | ✗ |
|                          | Compartilhamento de Integration Runtime | ✓<br><small>*Pode ser compartilhado entre diferentes fábricas de dados* | ✗ |
|                          | Vida útil | ✓ | ✗ |
| **Atividades de pipelines** | Atividade de pacote SSIS | ✓ | ✗ |
|                          | Suporte para Power Query atividade | ✓ | ✓ |
| **Galeria de modelos e centro de conhecimento** | Modelos de solução | ✓<br><small>*Galeria de modelos do Azure Data Factory* | ✓<br><small>*Centro de conhecimento do Synapse Workspace* |
| **Integração de repositório GIT** | Integração do GIT | ✓ | ✓ |
| **Monitoring**           | Monitoramento de trabalhos do Spark para fluxo de dados | ✗ | ✓<br><small>*Aproveite os pools do Spark Synapse* |
|                          | Integração com o Azure Monitor | ✓ | ✗ |
| **Linhagem** | Dá suporte à publicação de dados de linhagem de pipeline para alcance  | ✓ | ✗ |  

> [!Note]
> A **vida** útil é uma configuração Azure Integration Runtime que permite que o cluster do Spark *permaneça quente* por um período de tempo após uma execução do fluxo de dados.
>


## <a name="next-steps"></a>Próximas etapas

Comece a usar a integração de dados em seu espaço de trabalho do Synapse aprendendo a [incluir dados em uma conta do Azure data Lake Storage Gen2](data-integration-data-lake.md).
