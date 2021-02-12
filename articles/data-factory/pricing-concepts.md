---
title: Noções básicas sobre preços Azure Data Factory por meio de exemplos
description: Este artigo explica e demonstra o modelo de preços do Azure Data Factory com exemplos detalhados
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: cea8ae07585f09c644f0ef6e1e6142998ddc7f08
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497493"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Noções básicas de preços do Data Factory por meio de exemplos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explica e demonstra o modelo de preços do Azure Data Factory com exemplos detalhados.

> [!NOTE]
> Os preços usados nestes exemplos abaixo são hipotéticos e não têm o objetivo de sugerir o preço real.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Copiar dados do AWS S3 para o Armazenamento de Blobs do Azure por hora

Neste cenário, é ideal copiar dados do AWS S3 para o Armazenamento de Blobs do Azure em um agendamento por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados do AWS S3.

2. Um conjunto de dados de saída para os dados no Armazenamento do Azure.

3. Um gatilho de agenda para executar o pipeline a cada hora.

   ![O diagrama mostra um pipeline com um gatilho de agendamento. No pipeline, a atividade de cópia flui para um conjunto de dados de entrada, que flui para um serviço vinculado S3 S e A atividade de cópia também flui para um conjunto de dados de saída, que flui para um serviço vinculado do armazenamento do Azure.](media/pricing-concepts/scenario1.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 2 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 2 Execuções de atividade (1 para execução do gatilho, 1 para execução da atividade) |
| Suposição ao copiar dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime(configuração DIU padrão = 4) Para obter mais informações sobre unidades de integração de dados e otimização de desempenho de cópia, consulte [este artigo](copy-activity-performance.md) |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 2 registros de execução de monitoramento recuperados (1 para execução de pipeline, 1 para execução de atividade) |

**Total de preços do cenário: US$ 0,16811**

- Operações do Data Factory = **US$ 0,0001**
  - Leitura/gravação = 10\*00001 = US$ 0,0001 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 2\*000005 = US$ 0,00001 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução da orquestração &amp; de pipeline = **US$ 0,168**
  - Execuções de atividade = 001\*2 = 0,002 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de movimentação de dados = US$ 0,166 (proporcional a 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Copiar dados e transformar com o Azure Databricks por hora

Neste cenário, você deseja copiar dados do AWS S3 para o Armazenamento de Blobs do Azure e transformar os dados com o Azure Databricks em um agendamento por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados do AWS S3 e um conjunto de dados de saída para os dados no Armazenamento do Azure.
2. Uma atividade do Azure Databricks para a transformação de dados.
3. Um gatilho de agenda para executar o pipeline a cada hora.

![O diagrama mostra um pipeline com um gatilho de agendamento. No pipeline, a atividade de cópia flui para um conjunto de dados de entrada, um conjunto de dados de saída e uma atividade do databricks, que é executada em Azure Databricks. O conjunto de dados de entrada flui para um serviço vinculado S3 S. O conjunto de resultados de saída flui para um serviço vinculado do armazenamento do Azure.](media/pricing-concepts/scenario2.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 3 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 3 Execuções de atividade (1 para execução do gatilho, 2 para execução da atividade) |
| Suposição ao copiar dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime(configuração DIU padrão = 4) Para obter mais informações sobre unidades de integração de dados e otimização de desempenho de cópia, consulte [este artigo](copy-activity-performance.md) |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 3 registros de execução de monitoramento recuperados (1 para execução de pipeline, 2 para execução de atividade) |
| Suposição da atividade Executar Databricks: tempo de execução = 10 min | 10 minutos de execução de atividade do pipeline externo |

**Total de preços do cenário: US$ 0,16916**

- Operações do Data Factory = **US$ 0,00012**
  - Leitura/gravação = 11\*00001 = US$ 0,00011 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 3\*000005 = US$ 0,00001 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução da orquestração &amp; de pipeline = **US$ 0,16904**
  - Execuções de atividade = 001\*3 = 0,003 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de movimentação de dados = US$ 0,166 (proporcional a 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)
  - Atividade de pipeline externa = US$ 0,000041 (proporcional a 10 minutos de tempo de execução. US$ 0,00025/hora no Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Copiar dados e transformar com parâmetros dinâmicos por hora

Nesse cenário, você deseja copiar dados do AWS S3 para o Armazenamento de Blobs do Azure e transformá-los com o Azure Databricks (com parâmetros dinâmicos no script) em um agendamento por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de cópia com um conjunto de dados de entrada para os dados a serem copiados do AWS S3, um conjunto de dados de saída para os dados no Armazenamento do Azure.
2. Uma atividade de pesquisa para passar parâmetros dinamicamente para o script de transformação.
3. Uma atividade do Azure Databricks para a transformação de dados.
4. Um gatilho de agenda para executar o pipeline a cada hora.

![O diagrama mostra um pipeline com um gatilho de agendamento. No pipeline, a atividade de cópia flui para um conjunto de dados de entrada, um conjunto de dados de saída e uma atividade de pesquisa que flui para uma atividade do databricks, que é executada em Azure Databricks. O conjunto de dados de entrada flui para um serviço vinculado S3 S. O conjunto de resultados de saída flui para um serviço vinculado do armazenamento do Azure.](media/pricing-concepts/scenario3.png)

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 3 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 4 Execuções de atividade (1 para execução do gatilho, 3 para execução da atividade) |
| Suposição ao copiar dados: tempo de execução = 10 min | 10 \* 4 Azure Integration Runtime(configuração DIU padrão = 4) Para obter mais informações sobre unidades de integração de dados e otimização de desempenho de cópia, consulte [este artigo](copy-activity-performance.md) |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 4 monitoramento de registros de execução recuperados (1 para execução de pipeline, 3 para execução de atividade) |
| Suposição da atividade de pesquisa: tempo de execução = 1 min | 1 minuto de execução da atividade do pipeline |
| Suposição da atividade Executar Databricks: tempo de execução = 10 min | 10 minutos de execução de atividade do pipeline externo |

**Total de preços do cenário: US$ 0,17020**

- Operações do Data Factory = **US$ 0,00013**
  - Leitura/gravação = 11\*00001 = US$ 0,00011 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 4\*000005 = US$ 0,00002 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução da orquestração &amp; de pipeline = **US$ 0,17007**
  - Execuções de atividade = 001\*4 = 0,004 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de movimentação de dados = US$ 0,166 (proporcional a 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)
  - Atividade de pipeline = US$ 0,00003 (proporcional a 1 minuto de tempo de execução. US$ 0,002/hora no Azure Integration Runtime)
  - Atividade de pipeline externa = US$ 0,000041 (proporcional a 10 minutos de tempo de execução. US$ 0,00025/hora no Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Usando a depuração de fluxo de dados de mapeamento para um workday normal

Como engenheiro de dados, o Sam é responsável por criar, criar e testar fluxos de dados de mapeamento todos os dias. O Sam faz logon na interface do usuário do ADF na manhã e habilita o modo de depuração para fluxos de dados. O TTL padrão para sessões de depuração é de 60 minutos. O Sam funciona ao longo do dia por 8 horas, portanto, a sessão de depuração nunca expira. Portanto, os encargos de Sam para o dia serão:

**8 (horas) x 8 (núcleos com otimização de computação) x $0.193 = $12.35**

Ao mesmo tempo, Chris, outro engenheiro de dados, também faz logon na interface do usuário do navegador ADF para o trabalho de design de ETL e criação de perfil de dados. Chris não funciona no ADF todo dia como o Sam. Chris só precisa usar o depurador de fluxo de dados por 1 hora durante o mesmo período e no mesmo dia que o Sam acima. Esses são os encargos que Chris incorre para uso de depuração:

**1 (hora) x 8 (núcleos de uso geral) x $0.274 = $2.19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Transformar dados no repositório de blob com fluxos de dados de mapeamento

Nesse cenário, você deseja transformar os dados no repositório de blob visualmente nos fluxos de dados de mapeamento do ADF em uma agenda por hora.

Para executar o cenário, você precisará criar um pipeline com os seguintes itens:

1. Uma atividade de fluxo de dados com a lógica de transformação.

2. Um DataSet de entrada para os dados no armazenamento do Azure.

3. Um conjunto de dados de saída para os dados no Armazenamento do Azure.

4. Um gatilho de agenda para executar o pipeline a cada hora.

| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 2 entidades de leitura/gravação  |
| Criar conjuntos de dados | 4 entidades de leitura/gravação (2 para criação do conjunto de dados, 2 para referências do serviço vinculado) |
| Criar pipeline | 3 entidades de leitura/gravação (1 para criação de pipeline, 2 para referências do conjunto de dados) |
| Obter pipeline | 1 entidade de leitura/gravação |
| Executar pipeline | 2 Execuções de atividade (1 para execução do gatilho, 1 para execução da atividade) |
| Pressuposições de fluxo de dados: tempo de execução = 10 min + 10 min TTL | 10 \* núcleos de computação geral com TTL de 10 |
| Suposição de monitoramento do pipeline: ocorreu apenas 1 execução | 2 registros de execução de monitoramento recuperados (1 para execução de pipeline, 1 para execução de atividade) |

**Preço do cenário total: $1.4631**

- Operações do Data Factory = **US$ 0,0001**
  - Leitura/gravação = 10\*00001 = US$ 0,0001 [1 L/G = US$ 0,50/50000 = 0,00001]
  - Monitoramento = 2\*000005 = US$ 0,00001 [1 monitoramento = US$ 0,25/50000 = 0,000005]
- Execução de orquestração de pipeline &amp; = **$1.463**
  - Execuções de atividade = 001\*2 = 0,002 [1 execução = US$ 1/1000 = 0,001]
  - Atividades de fluxo de dados = $1.461 rateado por 20 minutos (tempo de execução de 10 min + 10 minutos TTL). $0.274/hora em Azure Integration Runtime com 16 núcleos de computação geral

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Integração de dados na VNET gerenciada Azure Data Factory
Nesse cenário, você deseja excluir os arquivos originais no armazenamento de BLOBs do Azure e copiar dados do banco de dados SQL do Azure para o armazenamento de BLOBs do Azure. Você fará essa execução duas vezes em pipelines diferentes. O tempo de execução desses dois pipelines se sobrepõe.
![Scenario4 ](media/pricing-concepts/scenario-4.png) para realizar o cenário, você precisa criar dois pipelines com os seguintes itens:
  - Uma atividade de pipeline – excluir atividade.
  - Uma atividade de cópia com um conjunto de dados de entrada que será copiado do armazenamento de BLOBs do Azure.
  - Um DataSet de saída para os dados no banco de dado SQL do Azure.
  - Um agendamento de gatilhos para executar o pipeline.


| **Operações** | **Tipos e unidades** |
| --- | --- |
| Criar serviço vinculado | 4 entidade de leitura/gravação |
| Criar conjuntos de dados | 8 entidades de leitura/gravação (4 para criação de conjuntos de conjunto, 4 para referências de serviço vinculadas) |
| Criar pipeline | 6 entidades de leitura/gravação (2 para criação de pipeline, 4 para referências de DataSet) |
| Obter pipeline | 2 entidades de leitura/gravação |
| Executar pipeline | 6 execuções de atividade (2 para execução de gatilho, 4 para execuções de atividade) |
| Executar atividade de exclusão: cada tempo de execução = 5 min. A execução da atividade de exclusão no primeiro pipeline é de 10:00 AM UTC para 10:05 AM UTC. A execução da atividade de exclusão no segundo pipeline é de 10:02 AM UTC para 10:07 AM UTC.|Total de 7 minutos de execução da atividade de pipeline na VNET gerenciada. A atividade de pipeline dá suporte a até 50 de simultaneidade na VNET gerenciada. |
| Pressuposição de Copiar Dados: cada tempo de execução = 10 min. A execução da cópia no primeiro pipeline é de 10:06 AM UTC para 10:15 AM UTC. A execução da atividade de exclusão no segundo pipeline é de 10:08 AM UTC para 10:17 AM UTC. | 10 * 4 Azure Integration Runtime (configuração padrão de DIU = 4) para obter mais informações sobre unidades de integração de dados e otimizar o desempenho de cópia, consulte [Este artigo](copy-activity-performance.md) |
| Monitorar a suposição de pipeline: apenas 2 execuções ocorreram | 6 registros de execução de monitoramento recuperados (2 para execução de pipeline, 4 para execução de atividade) |


**Preço do cenário total: $0.45523**

- Operações de Data Factory = $0.23
  - Leitura/gravação = 20 * 00001 = $0.02 [1 R/W = US $0,50/50000 = 0, 1]
  - Monitoramento = 6 * 000005 = $0.03 [1 monitoramento = $0,25/50000 = 0, 5]
- Execução de & de orquestração de pipeline = $0.455
  - Execuções de atividade = 0,001 * 6 = 0, 6 [1 Run = $1/1000 = 0, 1]
  - Atividades de movimentação de dados = $0.333 (rateado por 10 minutos de tempo de execução. US$ 0,25 no Azure Integration Runtime)
  - Atividade de pipeline = $0.116 (rateado por 7 minutos de tempo de execução. US $1/hora no Azure Integration Runtime)

> [!NOTE]
> Esses preços são apenas para fins de exemplo.

**perguntas frequentes**

P: se eu quiser executar mais de 50 atividades de pipeline, essas atividades poderão ser executadas simultaneamente?

R: as atividades de pipeline simultâneas máximas 50 serão permitidas.  A atividade de pipeline 51th será enfileirada até que um "slot livre" seja aberto. Mesmo para atividade externa. O máximo de 800 atividades externas simultâneas serão permitidas.

## <a name="next-steps"></a>Próximas etapas

Agora que você entendeu o preço do Azure Data Factory, pode começar a usá-lo!

- [Criar um data factory usando a interface do usuário do Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Introdução ao Azure Data Factory](introduction.md)

- [Criação visual no Azure Data Factory](author-visually.md)
