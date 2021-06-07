---
title: Resiliência & alta disponibilidade
titleSuffix: Azure Machine Learning
description: Saiba como tornar seus Azure Machine Learning recursos mais resilientes a interrupções usando uma configuração de alta disponibilidade.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93325485"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Aumentar a resiliência de Azure Machine Learning



Neste artigo, você aprenderá a tornar seus Microsoft Azure Machine Learning recursos mais resilientes usando configurações de alta disponibilidade. Você pode configurar os serviços do Azure que Azure Machine Learning depende para alta disponibilidade. Este artigo identifica os serviços que você pode configurar para alta disponibilidade e links para informações adicionais sobre como configurar esses recursos.

> [!NOTE]
> Azure Machine Learning em si não oferece uma opção de recuperação de desastre.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Entender os serviços do Azure para Azure Machine Learning

Azure Machine Learning depende de vários serviços do Azure e tem várias camadas. Alguns desses serviços são provisionados em sua assinatura do (cliente). Você é responsável pela configuração de alta disponibilidade desses serviços. Outros serviços são criados em uma assinatura da Microsoft e gerenciados pela Microsoft. 

Os serviços do Azure incluem:

* **Infraestrutura de Azure Machine Learning**: um ambiente gerenciado pela Microsoft para o espaço de trabalho Azure Machine Learning.

* **Recursos associados**: recursos provisionados em sua assinatura durante a criação de Azure Machine Learning espaço de trabalho. Esses recursos incluem armazenamento do Azure, Azure Key Vault, registro de contêiner do Azure e Application Insights. Você é responsável por definir configurações de alta disponibilidade para esses recursos.
  * O armazenamento padrão tem dados, como modelo, dados de log de treinamento e DataSet.
  * Key Vault tem credenciais para armazenamento do Azure, registro de contêiner e armazenamentos de dados.
  * O registro de contêiner tem uma imagem do Docker para ambientes de treinamento e inferência.
  * Application Insights é para Azure Machine Learning de monitoramento.

* **Recursos de computação**: recursos que você cria após a implantação do espaço de trabalho. Por exemplo, você pode criar uma instância de computação ou um cluster de cálculo para treinar um modelo de Machine Learning.
  * Cluster de computação e instância de computação: ambientes de desenvolvimento de modelo gerenciados pela Microsoft.
  * Outros recursos: recursos de computação da Microsoft que você pode anexar a Azure Machine Learning, como o AKS (serviço kubernetes do Azure), Azure Databricks, instâncias de contêiner do Azure e Azure HDInsight. Você é responsável por definir configurações de alta disponibilidade para esses recursos.

* **Armazenamentos de dados adicionais**: Azure Machine Learning pode montar armazenamentos de dados adicionais, como o armazenamento do Azure, o Azure data Lake Storage e o banco de dados SQL do Azure para o treinamento.  Esses armazenamentos de dados são provisionados em sua assinatura. Você é responsável por definir suas configurações de alta disponibilidade.

A tabela a seguir mostra quais serviços do Azure são gerenciados pela Microsoft, que são gerenciados por você e que são altamente disponíveis por padrão.

| Serviço | Gerenciado por | Alta disponibilidade por padrão |
| ----- | ----- | ----- |
| **Infraestrutura de Azure Machine Learning** | Microsoft | |
| **Recursos associados** |
| Armazenamento do Azure | Você | |
| Key Vault | Você | ✓ |
| Registro de Contêiner | Você | |
| Application Insights | Você | NA |
| **Recursos de computação** |
| Instância de computação | Microsoft |  |
| Cluster de computação | Microsoft |  |
| Outros recursos de computação, como AKS, <br>Azure Databricks, instâncias de contêiner, HDInsight | Você |  |
| Armazenamentos de **dados adicionais** , como o armazenamento do Azure, o SQL Database,<br> Banco de dados do Azure para PostgreSQL, banco de dados do Azure para MySQL, <br>Sistema de arquivos Azure Databricks | Você | |

O restante deste artigo descreve as ações que você precisa tomar para tornar cada um desses serviços altamente disponível.

## <a name="associated-resources"></a>Recursos associados

> [!IMPORTANT]
> O Azure Machine Learning não dá suporte ao failover de conta de armazenamento padrão usando GRS (armazenamento com redundância geográfica), GZRS (armazenamento com redundância de zona geográfica), armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou armazenamento com redundância de acesso de leitura (RA-GZRS).

Certifique-se de definir as configurações de alta disponibilidade de cada recurso fazendo referência à seguinte documentação:

* **Armazenamento do Azure**: para definir configurações de alta disponibilidade, consulte [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md).
* **Key Vault**: o Key Vault fornece alta disponibilidade por padrão e não requer nenhuma ação do usuário.  Consulte [Azure Key Vault disponibilidade e redundância](../key-vault/general/disaster-recovery-guidance.md).
* **Registro de contêiner**: escolha a opção de registro Premium para replicação geográfica. Consulte [replicação geográfica no registro de contêiner do Azure](../container-registry/container-registry-geo-replication.md).
* **Application insights**: o Application insights não fornece configurações de alta disponibilidade. Para ajustar o período de retenção de dados e os detalhes, consulte [coleta de dados, retenção e armazenamento em Application insights](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Recursos de computação

Certifique-se de definir as configurações de alta disponibilidade de cada recurso fazendo referência à seguinte documentação:

* **Serviço kubernetes do Azure**: Veja [as práticas recomendadas para continuidade dos negócios e recuperação de desastre no AKs (serviço kubernetes do Azure](../aks/operator-best-practices-multi-region.md) ) e [crie um cluster AKs (serviço kubernetes do Azure) que usa zonas de disponibilidade](../aks/availability-zones.md). Se o cluster AKS foi criado usando o Azure Machine Learning Studio, o SDK ou a CLI, não há suporte para alta disponibilidade entre regiões.
* **Azure Databricks**: consulte [recuperação de desastre regional para Azure Databricks clusters](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Instâncias de contêiner**: um orquestrador é responsável pelo failover. Consulte [instâncias de contêiner do Azure e orquestradores de contêiner](../container-instances/container-instances-orchestrator-relationship.md).
* **HDInsight**: consulte [serviços de alta disponibilidade com suporte do Azure HDInsight](../hdinsight/hdinsight-high-availability-components.md).

## <a name="additional-data-stores"></a>Armazenamentos de dados adicionais

Certifique-se de definir as configurações de alta disponibilidade de cada recurso fazendo referência à seguinte documentação:

* **Contêiner de blob do Azure/arquivos/data Lake Storage Gen2 do Azure**: igual ao armazenamento padrão.
* **Data Lake Storage Gen1**: consulte as [diretrizes de alta disponibilidade e recuperação de desastres para data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **Banco de dados SQL**: consulte [alta disponibilidade para o banco de dados SQL do Azure e o SQL instância gerenciada](../azure-sql/database/high-availability-sla.md).
* **Banco de dados do Azure para PostgreSQL**: consulte [conceitos de alta disponibilidade no banco de dados do Azure para PostgreSQL-servidor único](../postgresql/concepts-high-availability.md).
* **Banco de dados do Azure para MySQL**: consulte [entender a continuidade dos negócios no banco de dados do Azure para MySQL](../mysql/concepts-business-continuity.md).
* **Azure Databricks sistema de arquivos**: consulte [recuperação de desastre regional para Azure Databricks clusters](/azure/databricks/scenarios/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Se você fornecer sua própria chave gerenciada pelo cliente para implantar um espaço de trabalho Azure Machine Learning, Azure Cosmos DB também será provisionado em sua assinatura. Nesse caso, você é responsável por definir suas configurações de alta disponibilidade. Confira [Alta disponibilidade com o Azure Cosmos DB](../cosmos-db/high-availability.md).

## <a name="next-steps"></a>Próximas etapas

Para implantar Azure Machine Learning com recursos associados com suas configurações de alta disponibilidade, use um [modelo de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).