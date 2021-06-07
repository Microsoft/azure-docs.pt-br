---
title: Serviços de Machine Learning na Instância Gerenciada de SQL do Azure
description: Este artigo fornece uma visão geral ou Serviços de Machine Learning no SQL Instância Gerenciada do Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599554"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Serviços de Machine Learning na Instância Gerenciada de SQL do Azure

Serviços de Machine Learning é um recurso do Azure SQL Instância Gerenciada que fornece aprendizado de máquina no banco de dados, dando suporte a scripts Python e R. O recurso inclui pacotes python e R da Microsoft para análise preditiva de alto desempenho e aprendizado de máquina. Os dados relacionais podem ser usados em scripts por meio de procedimentos armazenados, script T-SQL que contém instruções Python ou R ou código Python ou R que contém o T-SQL.

## <a name="what-is-machine-learning-services"></a>O que são os Serviços de Machine Learning?

Serviços de Machine Learning no Azure SQL Instância Gerenciada permite executar scripts do Python e do R no banco de dados. Você pode usá-lo para preparar e limpar dados, fazer engenharia de recursos e treinar, avaliar e implantar modelos de machine learning em um banco de dados. O recurso executa seus scripts onde os dados residem e elimina a transferência dos dados pela rede para outro servidor.

Use Serviços de Machine Learning com suporte de R/Python no Azure SQL Instância Gerenciada para:

- **Execute scripts de r e Python para realizar a preparação de dados e o processamento de dados de uso geral** -agora você pode trazer seus scripts de r/Python para o Azure SQL instância gerenciada em que seus dados residem, em vez de ter que mover dados para outro servidor para executar scripts de R e Python. Você pode eliminar a necessidade de movimentação de dados e problemas associados relacionados à latência, segurança e conformidade.

- **Treinar modelos de aprendizado de máquina no banco de dados** – você pode treinar modelos usando qualquer algoritmo de software livre. Você pode dimensionar facilmente seu treinamento para todo o conjunto de dados, em vez de depender de conjuntos de exemplos de bancos de dados extraídos do banco de dados.

- **Implante seus modelos e scripts em produção em procedimentos armazenados** -os scripts e modelos treinados podem ser operados simplesmente inserindo-os em procedimentos armazenados do T-SQL. Os aplicativos que se conectam ao Azure SQL Instância Gerenciada podem se beneficiar de previsões e inteligência nesses modelos chamando apenas um procedimento armazenado. Você também pode usar a função de previsão T-SQL nativa para colocar em operação os modelos para Pontuação rápida em cenários de Pontuação altamente simultâneas em tempo real.

As distribuições base do Python e do R estão incluídas nos Serviços de Machine Learning. Você pode instalar e usar estruturas e pacotes de software livre, como PyTorch, TensorFlow e Scikit-learn, além dos pacotes da Microsoft [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) e [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) para Python e [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml), [olapR](/sql/machine-learning/r/ref-r-olapr) e [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) para R.

## <a name="how-to-enable-machine-learning-services"></a>Como habilitar Serviços de Machine Learning

Você pode habilitar Serviços de Machine Learning no SQL do Azure Instância Gerenciada habilitando a extensibilidade com os seguintes comandos SQL (o SQL Instância Gerenciada será reiniciado e estará indisponível por alguns segundos):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Para obter detalhes sobre como esse comando afeta os recursos do SQL Instância Gerenciada, consulte [governança de recursos](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Habilitar Serviços de Machine Learning em um grupo de failover

Em um [grupo de failover](failover-group-add-instance-tutorial.md), os bancos de dados do sistema não são replicados para a instância secundária (consulte [limitações de grupos de failover](../database/auto-failover-group-overview.md#limitations-of-failover-groups) para obter mais informações).

Se o Instância Gerenciada que você está usando fizer parte de um grupo de failover, faça o seguinte:

- Execute os `sp_configure` `RECONFIGURE` comandos e em cada instância do grupo de failover para habilitar serviços de Machine Learning.

- Instale as bibliotecas de R/Python em um banco de dados de usuário em vez de no banco de dados mestre.

## <a name="next-steps"></a>Próximas etapas

- Consulte as [principais diferenças de SQL Server serviços de Machine Learning](machine-learning-services-differences.md).
- Para saber como usar o Python no Serviços de Machine Learning, consulte [executar scripts Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para saber como usar o R no Serviços de Machine Learning, consulte [executar scripts R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para obter mais informações sobre o aprendizado de máquina em outras plataformas do SQL, consulte a [documentação do SQL Machine Learning](/sql/machine-learning/index).
