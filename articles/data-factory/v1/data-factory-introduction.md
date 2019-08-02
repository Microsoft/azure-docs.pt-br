---
title: Introdução ao Data Factory, um serviço de integração de dados | Microsoft Docs
description: 'Saiba o que é o Azure Data Factory: Um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: aed073e7d65f193c4123106ba1ae3f4f442965e1
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836305"
---
# <a name="introduction-to-azure-data-factory"></a>Introdução ao Azure Data Factory 
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-introduction.md)
> * [Versão 2 (versão atual)](../introduction.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço Data Factory, veja [Introdução ao Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure?
No mundo de grandes volumes de dados, como os dados existentes são usados nos negócios? É possível enriquecer os dados gerados na nuvem usando dados de referência de fontes de dados locais ou de outras fontes de dados diferentes? 

Por exemplo, uma empresa de jogos coleta logs que são produzidos por jogos na nuvem. Ela analisa esses logs para saber as preferências dos clientes, a faixa demográfica, o comportamento de uso, e assim por diante. A empresa também deseja identificar oportunidades de venda adicional e venda cruzada, desenvolver recursos novos e cativantes para estimular o crescimento do negócio, e fornecer uma melhor experiência para os clientes. 

Para analisar esses logs, a empresa precisa usar dados de referência como informações do cliente, informações do jogo e informações de campanhas de marketing que estejam disponíveis em repositórios de dados locais. Portanto, a empresa quer ingerir dados de log do armazenamento de dados de nuvem e dados de referência do repositório de dados local. 

Em seguida, ela processa esses dados usando o Hadoop na nuvem (Azure HDInsight). A empresa então pode publicar os dados resultantes em um data warehouse na nuvem como o SQL Data Warehouse do Azure ou um repositório de dados local como o SQL Server. Ela deseja executar esse fluxo de trabalho uma vez por semana. 

A empresa precisa de uma plataforma onde possa criar um fluxo de trabalho capaz de ingerir dados provenientes de repositórios de dados locais e na nuvem. Ela também precisa ser capaz de transformar ou processar dados usado os serviços de computação existentes, como o Hadoop, e publicar os resultados em um repositório de dados local ou na nuvem para que eles sejam consumidos por aplicativos de BI. 

![Visão geral do Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

O Azure Data Factory é a plataforma para esses tipos de cenários. Ele é um *serviço de integração de dados baseado na nuvem que permite a criação de fluxos de trabalho orientados a dados na nuvem que fazem a orquestração e automação da transformação e movimentação dos dados*. Usando o Azure Data Factory, você pode realizar as seguintes tarefas: 

- Criar e agendar fluxos de trabalhos orientados a dados (chamados pipelines) que podem ingerir dados provenientes de repositórios de dados diferentes.

- Processar ou transformar os dados usando serviços de computação como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning.

-  Publicar dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para que estes sejam consumidos por aplicativos de business intelligence (BI).  

Ele é mais uma plataforma para extrair e carregar (EL) e transformar e carregar (TL) do que uma plataforma tradicional para extrair, transformar e carregar (ETL). As transformações processam os dados usando serviços de computação em vez de adicionar colunas derivadas, contar o número de linhas, classificar os dados, e assim por diante. 

Atualmente, no Azure Data Factory, os dados que os fluxos de trabalho consomem e produzem são *dados de frações de tempo* (por hora, diariamente, semanalmente, e assim por diante). Por exemplo, um pipeline pode ler dados de entrada, processar dados e produzir dados de saída uma vez por dia. Você também pode executar um fluxo de trabalho apenas uma vez.  
  

## <a name="how-does-it-work"></a>Como ele funciona? 
Os pipelines (fluxos de trabalho orientados a dados) no Azure Data Factory normalmente executam as três etapas a seguir:

![As três etapas do Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Conectar e coletar
As empresas possuem dados de vários tipos que estão localizados em diferentes fontes. A primeira etapa ao criar um sistema de geração de informações é se conectar a todas as diferentes fontes de dados e processamento necessárias. Essas fontes incluem serviços de SaaS, compartilhamentos de arquivos, FTP e serviços Web. Em seguida, mova os dados conforme necessário para um local central para que eles sejam processados posteriormente.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. É caro e difícil integrar e manter esses sistemas. Eles também , muitas vezes, não possuem o monitoramento, os alertas e os controles de nível empresarial oferecidos por um serviço totalmente gerenciado.

Com o Data Factory, você pode usar a Atividade de Cópia em um pipeline de dados para mover dados de repositórios de dados locais e na nuvem para um repositório de dados centralizado na nuvem para análise posterior. 

Por exemplo, é possível coletar dados no Azure Data Lake Store e transformar os dados posteriormente usando um serviço de computação do Azure Data Lake Analytics. Ou, coletar dados no armazenamento de blobs do Azure e transformá-los usando um cluster do Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Após os dados estarem presentes no repositório de dados central na nuvem, processe-os ou transfira-os usando serviços de computação como o HDInsight Hadoop, Spark, Data Lake Analytics ou Machine Learning. Você quer produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis. 

### <a name="publish"></a>Publicar 
Entregue os dados transformados das fontes da nuvem para fontes locais como o SQL Server. Alternativamente, é possível guardá-los nas suas fontes de armazenamento na nuvem para consumo por ferramentas de BI e de análise e outros aplicativos.

## <a name="key-components"></a>Principais componentes
Uma assinatura do Azure pode ter uma ou mais instâncias (ou data factories) do Azure Data Factory. O Azure Data Factory é composto de quatro componentes principais. Esses componentes trabalham juntos para oferecer a plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para mover e transformar dados. 

### <a name="pipeline"></a>Pipeline
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um grupo de atividades. Juntas, as atividades em um pipeline executam uma tarefa. 

Por exemplo, um pipeline pode conter um grupo de atividades que ingere dados provenientes de um blob do Azure e, em seguida, executa uma consulta Hive em um cluster HDInsight para particionar os dados. A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. Por exemplo, é possível implantar e agendar o pipeline, em vez de agendar atividades independentes. 

### <a name="activity"></a>Atividade
Um pipeline pode ter uma ou mais atividades. As Atividades definem as ações a serem realizadas em seus dados. Por exemplo, é possível usar uma atividade de cópia para copiar dados de um repositório de dados para outro repositório de dados. Da mesma forma, é possível usar uma atividade Hive. Uma atividade Hive executa uma consulta Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a dois tipos de atividades: atividades de movimentação de dados e atividades de transformação de dados.

### <a name="data-movement-activities"></a>Atividades de movimentação de dados
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. Os dados de qualquer origem podem ser gravados em qualquer coletor. Selecione um repositório de dados para saber como copiar dados de e para este repositório. O Data Factory é compatível com os seguintes repositórios de dados:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais informações, confira [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para saber mais informações, confira [Mover dados usando a Atividade de Cópia](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades personalizadas do .NET
Crie uma atividade personalizada do .NET se for necessário mover dados de ou para um repositório de dados com o qual a Atividade de Cópia não seja compatível ou se for necessário transformar dados usando a sua própria lógica. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Conjunto de dados
Uma atividade aceita zero ou mais conjuntos de dados como entrada e produz um ou mais conjuntos de dados como saídas. Os conjuntos de dados representam estruturas de dados dentro dos repositórios de dados. Essas estruturas apontam para ou referenciam os dados que você deseja usar nas suas atividades (tais como entradas ou saídas). 

Por exemplo, um conjuntos de dados de blob do Azure especifica o contêiner e a pasta do blob no armazenamento de blobs do Azure de onde o pipeline deve ler os dados. Ou um conjuntos de dados de tabela de SQL do Azure especifica a tabela na qual os dados de saída são gravados pela atividade. 

### <a name="linked-services"></a>Serviços vinculados
Os serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para que o Data Factory se conecte aos recursos externos. Pense dessa maneira: um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. 

Por exemplo, um serviço vinculado do Armazenamento do Azure especifica uma cadeia de conexão para conectar-se à conta de Armazenamento do Azure. Um conjunto de dados de blob do Azure especifica o contêiner de blob e a pasta que contém os dados.   

Os serviços vinculados são usados para duas finalidades no Data Factory:

* Para representar uma *fonte de dados* que inclui, mas não está limitada a, um banco de dados do SQL Server local, um banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de blobs do Azure. Confira a seção [Atividades de movimentação de dados](#data-movement-activities) para obter uma lista de repositórios de dados com suporte.

* Para representar um *recurso de computação* que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Confira a seção [Atividades de transformação de dados](#data-transformation-activities) para obter uma lista dos ambientes de computação compatíveis.

### <a name="relationship-between-data-factory-entities"></a>Relação entre entidades de Data Factory

![Diagrama: Data Factory, um serviço de integração de dados de nuvem – principais conceitos](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Regiões com suporte
Atualmente, é possível criar data factories nas regiões Oeste dos EUA, Leste dos EUA e Europa Setentrional. No entanto, um data factory pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre os repositórios de dados ou processar dados usando os serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite criar fluxos de trabalho orientados a dados para orquestrar o movimento de dados entre os [repositórios de dados compatíveis](#data-movement-activities). Ele também permite processar dados usando os [serviços de computação](#data-transformation-activities) em outras regiões ou em um ambiente local. Também permite [monitorar e gerenciar fluxos de trabalho](data-factory-monitor-manage-pipelines.md) usando mecanismos programáticos e de IU.

O Data Factory está disponível somente nas regiões Oeste dos EUA, Leste dos EUA e Europa Setentrional. No entanto, o serviço que possibilita a movimentação de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Se um repositório de dados estiver atrás de um firewall, então um [Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado em seu ambiente local moverá os dados.

Por exemplo, vamos supor que seus ambientes de computação, como o cluster do Azure HDInsight e o Azure Machine Learning, estão localizados na região da Europa Ocidental. É possível criar e usar uma instância do Azure Data Factory na Europa Setentrional. Em seguida, você pode usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Demora alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

## <a name="get-started-with-creating-a-pipeline"></a>Introdução à criação de um pipeline
Você pode usar uma dessas ferramentas ou APIs para criar pipelines de dados no Azure Data Factory: 

- Visual Studio
- PowerShell
- API do .NET
- API REST
- Modelo do Azure Resource Manager

Para saber como criar data factories com pipelines de dados, siga as instruções passo a passo dos tutoriais a seguir:

| Tutorial | DESCRIÇÃO |
| --- | --- |
| [Mover dados entre dois armazenamentos de dados em nuvem](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Criar um data factory com um pipeline que move dados do armazenamento de blobs para um banco de dados SQL. |
| [Transformar dados usando o cluster Haddop](data-factory-build-your-first-pipeline.md) |Criar o seu primeiro data factory do Azure com um pipeline de dados que processa dados executando um script Hive em um cluster do Azure HDInsight (Hadoop). |
| [Mover os dados entre um repositório de dados local e um repositório de dados na nuvem usando o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) |Criar um data factory com um pipeline que move os dados de um banco de dados SQL Server local para um blob do Azure. Como parte do passo a passo, você instala e configura o Gateway de Gerenciamento de Dados em seu computador. |
